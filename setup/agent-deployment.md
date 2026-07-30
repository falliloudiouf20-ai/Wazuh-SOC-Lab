# 🚀 Phase 2 : Déploiement de l'Agent Wazuh et Résolution de Problèmes

## 1. Méthodologie d'Installation de l'Agent Ubuntu

Après avoir initialisé le Manager sur Kali Linux, l'étape suivante consistait à intégrer la machine Ubuntu au SIEM en y déployant l'agent Wazuh.

### 1.1 Téléchargement et Tentative d'Installation

Sur la machine Ubuntu, le paquet Debian officiel de l'agent Wazuh **v4.12.0** a été téléchargé :

```bash
wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.12.0-1_amd64.deb
```

L'installation a initialement été tentée avec l'injection des variables d'environnement réseau :

```bash
sudo WAZUH_MANAGER='192.168.56.1' WAZUH_AGENT_NAME='Ubuntu-Wazuh-Agent' dpkg -i ./wazuh-agent_4.12.0-1_amd64.deb
```

---

## 2. Obstacles Rencontrés et Résolution de Problèmes

Cette phase a présenté plusieurs problèmes techniques nécessitant une analyse progressive du fonctionnement de l'agent et de la communication réseau.

### 🛠️ Problème n°1 : Absence de logs de connexion `wazuh-agentd`

En analysant les logs de l'agent dans :

```text
/var/ossec/logs/ossec.log
```

la commande suivante ne retournait aucun résultat :

```bash
sudo tail -n 20 /var/ossec/logs/ossec.log | grep -i "connected"
```

L'analyse a montré que plusieurs modules locaux fonctionnaient, notamment :

* Syscheckd
* SCA
* Logcollector

Cependant, le démon de communication réseau **`wazuh-agentd`** ne s'initialisait pas correctement.

Lors d'une réinstallation par-dessus un paquet existant, le fichier de configuration de l'agent ne conservait pas les directives nécessaires à la communication avec le Manager.

---

### 🛠️ Problème n°2 : Blocage du port 1515/TCP

Pour forcer l'enregistrement de l'agent auprès du serveur, un enrôlement manuel a été tenté depuis Ubuntu :

```bash
sudo /var/ossec/bin/agent-auth -m 192.168.56.1 -A Ubuntu-Wazuh-Agent
```

La commande restait bloquée sur :

```text
Requesting a key from server: 192.168.56.1
```

L'état des ports d'écoute sur Kali Linux a alors été vérifié :

```bash
sudo ss -tulpn | grep -E '1514|1515'
```

Les services Wazuh concernés étaient bien en écoute :

* `wazuh-remoted` sur le port **1514**
* `wazuh-authd` sur le port **1515**

Le problème venait du pare-feu local **UFW** sur Kali Linux, qui bloquait le trafic entrant depuis la VM Ubuntu.

Les règles suivantes ont donc été ajoutées :

```bash
sudo ufw allow 1514/tcp
sudo ufw allow 1515/tcp
sudo ufw allow 1514/udp
```

---

### 🛠️ Problème n°3 : Agent en doublon

Après avoir débloqué la communication, une nouvelle tentative d'enrôlement a retourné :

```text
ERROR: Duplicate agent name: Ubuntu-Wazuh-Agent (from manager)
```

Le Manager avait conservé l'entrée correspondant à la première tentative d'enrôlement.

Pour éviter le conflit, un nouvel identifiant unique a été utilisé :

```bash
sudo /var/ossec/bin/agent-auth -m 192.168.56.1 -A Ubuntu-Agent-01
```

Cette fois, l'enrôlement a réussi avec le message :

```text
INFO: Valid key received
```

---

## 3. Succès du Déploiement et Confirmation

Après réception de la clé d'authentification, le service Wazuh Agent a été redémarré sur Ubuntu :

```bash
sudo systemctl restart wazuh-agent
```

Les logs ont ensuite confirmé le démarrage des différents modules :

```text
2026/07/27 19:18:20 wazuh-modulesd:agent-upgrade: INFO: Module Agent Upgrade started.
2026/07/27 19:18:20 sca: INFO: Starting Security Configuration Assessment scan.
2026/07/27 19:18:20 wazuh-modulesd:syscollector: INFO: Module started.
2026/07/27 19:18:21 wazuh-logcollector: INFO: (9203): Monitoring journal entries.
2026/07/27 19:18:24 wazuh-syscheckd: INFO: FIM sync module started.
```

La communication avec le Manager a ensuite été confirmée par le message :

```text
Connected to the server at 192.168.56.1:1514
```

---

## 4. Validation dans le Dashboard

Sur le Dashboard Wazuh, l'agent est finalement apparu dans **Endpoints Summary** avec :

* **Agent ID :** `002`
* **Nom :** `Ubuntu-Agent-01`
* **Adresse IP :** `192.168.56.101`
* **Statut :** **Active 🟢**

Cette étape confirme que la communication entre Ubuntu et le Wazuh Manager sur Kali Linux est opérationnelle.

---

## 5. Bilan

Les principales difficultés rencontrées durant cette phase ont permis de mettre en pratique :

* le diagnostic des services Linux ;
* l'analyse des logs Wazuh ;
* la vérification des ports réseau ;
* la configuration d'un pare-feu avec UFW ;
* l'enrôlement manuel d'un agent ;
* la gestion des doublons d'agents ;
* la validation d'une connexion Agent → Manager depuis le Dashboard.

La phase suivante consiste à finaliser la configuration de l'agent et à nettoyer les anciennes entrées du Manager.
