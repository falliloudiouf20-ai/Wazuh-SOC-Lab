# 🛠️ Phase 1 : Déploiement de l'Infrastructure et des Machines Virtuelles

## 1. Contexte et Objectif

L'objectif de cette première étape était d'établir l'environnement de laboratoire virtualisé sous VirtualBox afin d'accueillir notre solution SIEM/SOC. L'architecture repose sur deux machines principales :

* **Kali Linux** : faisant office de serveur/manager Wazuh, du Dashboard d'administration et de machine d'attaque pour les futurs scénarios de détection.
* **Ubuntu 26.04 LTS** : la machine cible (endpoint) sur laquelle nous allons déployer l'agent Wazuh afin de collecter les journaux d'événements et surveiller les activités système.

---

## 2. Processus d'Installation et Configuration Réseau

### 2.1 Configuration des Cartes Réseau (Host-Only / Réseau Privé Hôte)

Pour garantir une isolation du lab par rapport au réseau domestique tout en permettant une communication entre les machines, une interface **Host-Only (`vboxnet0`)** a été configurée dans VirtualBox.

* **Sous-réseau attribué :** `192.168.56.0/24`
* **IP du Manager (Kali Linux) :** `192.168.56.1`
* **IP de l'Agent Cible (Ubuntu) :** `192.168.56.101`

Cette configuration permet aux deux machines de communiquer sur un réseau virtuel dédié.

### 2.2 Préparation de la VM Cible Ubuntu

La machine virtuelle Ubuntu a été créée avec :

* **2 vCPUs**
* **4 Go de RAM**
* Réseau configuré avec l'interface Host-Only

Après l'installation d'Ubuntu, le système a été mis à jour et les dépendances nécessaires ont été installées :

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install curl wget apt-transport-https gnupg lsb-release net-tools -y
```

---

## 3. Déploiement du Manager Wazuh sur Kali Linux

Pour le serveur central, un déploiement All-in-One de la suite Wazuh a été effectué directement sur Kali Linux.

### 3.1 Installation du Serveur

Le script automatisé Wazuh a été utilisé pour installer les composants centraux en version **4.12.0** :

```bash
curl -sO https://packages.wazuh.com/4.12/wazuh-install.sh
sudo bash ./wazuh-install.sh -a
```

L'installation permet de disposer des composants centraux nécessaires au fonctionnement du laboratoire :

* Wazuh Manager
* Wazuh Indexer
* Wazuh Dashboard

### 3.2 Vérification des Services Centralisés

Une fois l'installation terminée, les services ont été vérifiés avec `systemctl`.

Le **Wazuh Dashboard** a également été utilisé pour contrôler l'état du serveur et des futurs endpoints.

L'accès au Dashboard a été effectué via l'interface Web locale du serveur Wazuh.

---

## 4. Résultat de la Phase 1

À la fin de cette phase, l'infrastructure de base du laboratoire était opérationnelle :

```text
Kali Linux
├── Wazuh Manager
├── Wazuh Indexer
└── Wazuh Dashboard
        │
        │ Host-Only / vboxnet0
        │ 192.168.56.0/24
        │
        ▼
Ubuntu 26.04 LTS
└── Wazuh Agent
```

La phase suivante consiste à installer et enrôler l'agent Wazuh sur Ubuntu.
