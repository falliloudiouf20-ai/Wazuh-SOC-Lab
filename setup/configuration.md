# ⚙️ Phase 3 : Configuration Fine de l'Agent et Audit du Fichier `ossec.conf`

## 1. Structure du Fichier de Configuration de l'Agent

La configuration principale de l'agent Wazuh repose sur le fichier :

```text
/var/ossec/etc/ossec.conf
```

Afin de stabiliser la communication entre l'agent Ubuntu et le Manager Kali Linux, la section `<client>` a été configurée comme suit :

```xml
<ossec_config>
  <client>
    <server>
      <address>192.168.56.1</address>
      <port>1514</port>
      <protocol>tcp</protocol>
    </server>
    <config-profile>ubuntu, ubuntu26, ubuntu26.04</config-profile>
    <notify_time>10</notify_time>
    <time-reconnect>60</time-reconnect>
    <auto_restart>yes</auto_restart>
    <crypto_method>aes</crypto_method>
    <enrollment>
      <enabled>yes</enabled>
      <agent_name>Ubuntu-Agent-01</agent_name>
    </enrollment>
  </client>
</ossec_config>
```

---

## 2. Analyse des Composants Clés de la Configuration

### Adresse du Manager

```xml
<address>192.168.56.1</address>
```

Cette adresse correspond à l'interface Host-Only du Manager Kali Linux.

Elle permet à l'agent Ubuntu de communiquer avec le serveur Wazuh sur le réseau privé du laboratoire.

### Port et protocole

```xml
<port>1514</port>
<protocol>tcp</protocol>
```

Le port `1514/TCP` est utilisé pour la communication principale entre l'agent et le Manager.

### Profil de configuration

```xml
<config-profile>ubuntu, ubuntu26, ubuntu26.04</config-profile>
```

Cette configuration permet d'indiquer que l'endpoint utilise Ubuntu et d'appliquer les paramètres correspondant à cet environnement.

### Reconnexion

```xml
<notify_time>10</notify_time>
<time-reconnect>60</time-reconnect>
```

Ces paramètres participent à la gestion des communications et aux tentatives de reconnexion de l'agent.

### Redémarrage automatique

```xml
<auto_restart>yes</auto_restart>
```

Cette option permet de maintenir automatiquement le fonctionnement de l'agent lorsque cela est nécessaire.

### Chiffrement

```xml
<crypto_method>aes</crypto_method>
```

Le mécanisme cryptographique configuré utilise AES pour sécuriser les communications de l'agent.

### Enrôlement

```xml
<enrollment>
  <enabled>yes</enabled>
  <agent_name>Ubuntu-Agent-01</agent_name>
</enrollment>
```

L'enrôlement est activé et l'agent utilise le nom :

```text
Ubuntu-Agent-01
```

La balise optionnelle `<authorization_pass_path>` n'a pas été utilisée, ce qui permettait de réaliser l'enrôlement sans mot de passe d'autorisation configuré sur `authd`.

---

## 3. Nettoyage de l'Environnement SIEM

À la suite des différentes tentatives d'enrôlement, deux agents apparaissaient dans le Dashboard :

* **Agent `001`** : ancienne entrée, déconnectée.
* **Agent `002`** : agent Ubuntu fonctionnel, actif.

Afin de conserver une configuration propre, l'ancien agent `001` a été supprimé depuis Kali Linux :

```bash
sudo /var/ossec/bin/manage_agents -r 001
```

Après actualisation du Dashboard, seul l'agent fonctionnel `002 - Ubuntu-Agent-01` reste présent.

---

## 4. État Final du Laboratoire

L'architecture finale obtenue est la suivante :

```text
┌───────────────────────────────────────────┐
│              Kali Linux                   │
│                                           │
│  Wazuh Manager                            │
│  Wazuh Indexer                            │
│  Wazuh Dashboard                          │
│                                           │
│  192.168.56.1                             │
└──────────────────┬────────────────────────┘
                   │
                   │ Host-Only
                   │ 192.168.56.0/24
                   │
                   │ TCP 1514
                   │ TCP 1515
                   │
┌──────────────────▼────────────────────────┐
│              Ubuntu 26.04 LTS             │
│                                           │
│  Wazuh Agent                              │
│  Ubuntu-Agent-01                          │
│                                           │
│  192.168.56.101                           │
│                                           │
│  Status : Active 🟢                       │
└───────────────────────────────────────────┘
```

---

## 5. Bilan de la Phase de Setup

Cette phase de déploiement a permis de développer des compétences solides dans plusieurs domaines :

### Réseau & Sécurité

* Configuration d'un réseau Host-Only VirtualBox.
* Isolation du laboratoire.
* Analyse des ports avec `ss`.
* Configuration du pare-feu avec `ufw`.
* Communication TCP entre l'agent et le Manager.

### Administration Système Linux

* Installation de paquets `.deb`.
* Gestion des services avec `systemctl`.
* Analyse des journaux avec `tail` et `grep`.
* Diagnostic des processus et démons Wazuh.
* Gestion des configurations XML.

### Architecture SIEM

* Compréhension du modèle **Agent → Manager**.
* Enrôlement et authentification des endpoints.
* Gestion des clés d'agent.
* Collecte des journaux et événements.
* Surveillance depuis le Dashboard Wazuh.
* Gestion des agents et nettoyage des anciennes entrées.

---

## 6. Conclusion

La phase de setup est terminée avec succès.

Le laboratoire dispose désormais d'un **Wazuh Manager fonctionnel sur Kali Linux** et d'un **endpoint Ubuntu 26.04 LTS correctement enrôlé**.

L'agent :

```text
Ubuntu-Agent-01
```

est identifié par :

```text
Agent ID : 002
IP       : 192.168.56.101
Status   : Active 🟢
```

L'infrastructure est donc prête pour la suite du projet : génération de scénarios d'attaque, collecte d'événements, détection et analyse des alertes SOC.
