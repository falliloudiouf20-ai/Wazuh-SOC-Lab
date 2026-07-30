# 🏗️ Architecture du Lab SOC Wazuh

## 1. Présentation générale

Ce projet consiste à concevoir, déployer et valider un laboratoire SOC (Security Operations Center) basé sur la solution Open Source **Wazuh v4.12.0**. L'objectif est de reproduire le fonctionnement d'un véritable centre opérationnel de sécurité capable de collecter des journaux système, détecter des comportements malveillants, générer des alertes en temps réel et assister un analyste SOC dans l'investigation des incidents.

Contrairement à une démonstration théorique, ce laboratoire repose sur des tests réellement exécutés dans un environnement virtualisé. Les attaques ont été simulées depuis une machine Kali Linux contre un endpoint Ubuntu surveillé par Wazuh.

Les principaux scénarios réalisés sont :

- Attaque SSH et authentifications suspectes
- Surveillance de l'intégrité des fichiers (FIM)
- Création d'un compte privilégié
- Détection des commandes administrateur (sudo)
- Connexion SSH réussie avec un compte nouvellement créé
- Analyse des événements selon MITRE ATT&CK

L'objectif pédagogique est de démontrer la chaîne complète :

**Prévention → Détection → Investigation → Réponse → Remédiation**

---

# 2. Architecture réseau

L'environnement est composé de deux machines virtuelles VirtualBox reliées via un réseau Host-Only.

```
             Réseau VirtualBox Host-Only
                  192.168.56.0/24


        ┌──────────────────────────────┐
        │                              │
        │          Kali Linux          │
        │                              │
        │ IP : 192.168.56.1            │
        │                              │
        │ Wazuh Manager                │
        │ Wazuh Dashboard              │
        │ SIEM / SOC                   │
        │                              │
        └──────────────┬───────────────┘
                       │
                       │  Collecte des logs
                       │  Alertes
                       │
        ┌──────────────▼───────────────┐
        │                              │
        │      Ubuntu 26.04 LTS        │
        │                              │
        │ IP : 192.168.56.101          │
        │                              │
        │ Wazuh Agent                  │
        │ OpenSSH Server               │
        │ Endpoint Linux               │
        │                              │
        └──────────────────────────────┘
```

---

# 3. Composants utilisés

## Kali Linux

### Rôle

- Machine d'administration
- Wazuh Manager
- Dashboard Wazuh
- Machine d'attaque
- Poste d'analyse SOC

### Technologies

- Kali Linux Rolling
- Wazuh Manager 4.12.0
- SSH Client
- Linux
- Bash
- JSON
- VirtualBox

---

## Ubuntu

### Rôle

Endpoint Linux surveillé.

### Configuration

- Ubuntu 26.04 LTS
- IP : 192.168.56.101
- Wazuh Agent 4.12.0
- OpenSSH Server

Services surveillés :

- SSH
- Authentification PAM
- sudo
- Journald
- auth.log
- Syscheck (FIM)

---

# 4. Flux de fonctionnement du SOC

## Étape 1 — Collecte

Le Wazuh Agent collecte notamment :

- /var/log/auth.log
- journald
- sudo
- SSH
- File Integrity Monitoring
- événements système

---

## Étape 2 — Transmission

Les événements sont transmis automatiquement au Manager Wazuh installé sur Kali Linux.

---

## Étape 3 — Analyse

Le Manager applique :

- Décoders
- Règles natives Wazuh
- Mapping MITRE ATT&CK
- Corrélation des événements

---

## Étape 4 — Détection

Les événements deviennent des alertes consultables dans le Dashboard.

Exemples obtenus durant le projet :

- Failed SSH Authentication
- Successful SSH Authentication
- Integrity checksum changed
- New user added
- Password changed
- Successful sudo execution

---

# 5. Scénarios réalisés

| Scénario | Résultat |
|-----------|----------|
| Installation du Wazuh Agent | ✅ |
| Connexion Agent → Manager | ✅ |
| Installation OpenSSH | ✅ |
| Échec authentification SSH | ✅ |
| Connexion SSH réussie | ✅ |
| Détection FIM | ✅ |
| Détection sudo | ✅ |
| Création utilisateur | ✅ |
| Modification permissions | ✅ |

---

# 6. Mapping MITRE ATT&CK

| Technique | Description | Statut |
|------------|-------------|--------|
| T1110 | Brute Force | ✅ |
| T1078 | Valid Accounts | ✅ |
| T1021 | Remote Services | ✅ |
| T1136 | Create Account | ✅ |
| T1548.003 | Sudo | ✅ |
| T1565.001 | Stored Data Manipulation | ✅ |

---

# 7. Difficultés rencontrées

Ce projet n'a pas été un simple déploiement. Plusieurs problèmes ont dû être résolus.

## Problème n°1

### SSH absent

Erreur rencontrée :

```
Unit ssh.service could not be found
```

Diagnostic :

Le serveur OpenSSH n'était pas installé sur Ubuntu.

Résolution :

```bash
sudo apt install openssh-server -y
```

Activation :

```bash
sudo systemctl enable --now ssh
```

Validation :

```bash
sudo systemctl status ssh
```

Résultat :

```
Active: active (running)
```

---

## Problème n°2

Le port 22 n'était pas ouvert.

Commande utilisée :

```bash
sudo ss -tulpn | grep :22
```

Après installation de SSH :

```
LISTEN 0.0.0.0:22
```

---

## Problème n°3

Connexion SSH impossible.

Tests réalisés :

```bash
nc -zv 192.168.56.101 22
```

Puis :

```bash
ssh vboxuser@192.168.56.101
```

Résultat :

Connexion réussie.

---

## Problème n°4

Les événements SSH n'apparaissaient pas dans Wazuh.

Diagnostic :

Le fichier `/var/log/auth.log` n'était pas surveillé.

Ajout dans `ossec.conf` :

```xml
<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/auth.log</location>
</localfile>
```

Redémarrage :

```bash
sudo systemctl restart wazuh-agent
```

Validation :

```
Analyzing file:
/var/log/auth.log
```

---

## Problème n°5

Le dossier sensible n'était pas surveillé par FIM.

Ajout :

```xml
<directories realtime="yes" report_changes="yes">
/var/www/sensitive
</directories>
```

Résultat :

Les modifications sont remontées immédiatement.

---

# 8. Résultats obtenus

Les événements suivants ont été détectés par Wazuh.

## SSH

- Failed password
- Authentication failure
- Authentication success
- Ouverture de session PAM

---

## FIM

Détection :

```
Integrity checksum changed
```

Avec :

- MD5
- SHA1
- SHA256
- Diff des lignes
- Permissions
- Taille
- Date

---

## sudo

Détection :

```
Successful sudo to ROOT executed
```

---

## Création utilisateur

Détection :

```
New user added
```

Compte créé :

```
soc_test
```

---

## Changement de mot de passe

Détection :

```
password changed for soc_test
```

---

## Connexion SSH réussie

Détection :

```
Accepted password for soc_test from 192.168.56.1
```

---

# 9. Compétences démontrées

Au travers de ce laboratoire, les compétences suivantes ont été mises en œuvre :

- Administration Linux
- VirtualBox
- Réseau
- Déploiement Wazuh
- Gestion des agents
- Analyse SOC
- Analyse des logs
- Investigation
- MITRE ATT&CK
- Détection d'incidents
- SSH
- File Integrity Monitoring
- Gestion des utilisateurs Linux
- Bash

---

# 10. Captures d'écran associées

À ajouter dans le dossier `screenshots/` :

- wazuh-dashboard-initial.png
- agent-connected.png
- ssh-alert.png
- file-integrity-alert.png
- privilege-alert.png
- mitre-attck.png

---

# Conclusion

Ce laboratoire reproduit un environnement SOC réaliste basé sur Wazuh permettant de surveiller un endpoint Linux, détecter des comportements malveillants, analyser les alertes générées et documenter les incidents.

Toutes les démonstrations présentées dans ce projet ont été réalisées dans un environnement de test personnel à l'aide de Kali Linux, Ubuntu 26.04 et Wazuh 4.12.0.

Le projet démontre une maîtrise pratique de la mise en place d'un SIEM, de la collecte de journaux, de l'analyse d'événements de sécurité, du mapping MITRE ATT&CK ainsi que de la réponse aux incidents dans un contexte SOC.
