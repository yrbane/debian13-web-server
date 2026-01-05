# Debian 13 Web Server Installer

Script d'installation et de configuration sécurisée d'un serveur web Debian 13 (Trixie) en un clic.

## Fonctionnalités

### Stack Web
- **Apache 2.4** avec headers de sécurité, mod_security, mod_headers
- **PHP 8.x** avec configuration sécurisée (expose_php off, disable_functions optionnel)
- **MariaDB** avec hardening automatique
- **phpMyAdmin** avec URL aléatoire sécurisée
- **Certbot** (Let's Encrypt) avec renouvellement automatique

### Sécurité
- **SSH** : port custom, clé uniquement, root désactivé, AllowUsers
- **UFW** : politique deny in/allow out
- **Fail2ban** : protection SSH + filtres Apache
- **ModSecurity** : WAF avec OWASP Core Rule Set
- **Sysctl** : hardening kernel (TCP SYN cookies, ICMP, etc.)
- **/tmp sécurisé** : monté avec noexec, nosuid, nodev

### Détection d'intrusions
- **ClamAV** : antivirus avec scan quotidien (2h00)
- **rkhunter** : détection rootkits hebdomadaire (dimanche 3h00)
- **AIDE** : intégrité fichiers quotidienne (4h00)
- **SSH Alert** : email à chaque connexion avec géolocalisation
- **Logwatch** : résumé quotidien des logs par email

### Email
- **Postfix** : envoi local uniquement (loopback)
- **OpenDKIM** : signature DKIM automatique
- Configuration SPF/DKIM/DMARC guidée

### Outils de développement (optionnels)
- **Python 3** avec pip, venv, pipx, virtualenv
- **Node.js** via NVM
- **Rust** via rustup
- **Composer** (PHP)
- **Git**, curl, wget, htop, etc.

### Monitoring & Audit
- **Mode --audit** : rapport de sécurité hebdomadaire par email (HTML)
- Vérifications complètes : services, certificats SSL, bases de menaces, DNS, ressources

## Prérequis

- Debian 13 (Trixie) fraîchement installé
- Accès root
- Connexion internet
- Clé SSH publique pour l'utilisateur admin

## Installation

```bash
# Télécharger le script
wget https://raw.githubusercontent.com/VOTRE_USER/debian13-web-server/main/install.sh
chmod +x install.sh

# Lancer l'installation (en root)
sudo ./install.sh
```

Le script pose des questions interactives pour personnaliser l'installation.

## Usage

### Installation complète
```bash
sudo ./install.sh
```

### Mode audit (vérifications uniquement)
```bash
sudo ./install.sh --audit
```

Le mode audit :
- Vérifie tous les services et configurations
- Contrôle la fraîcheur des bases de menaces (ClamAV, rkhunter, AIDE)
- Vérifie l'expiration des certificats SSL
- Envoie un rapport HTML par email
- Programmé automatiquement chaque lundi à 7h00

## Configuration persistante

Le script sauvegarde la configuration dans `/root/.bootstrap.conf` pour :
- Réexécuter le script sans re-répondre aux questions
- Permettre le mode --audit de fonctionner correctement

## Post-installation

### DNS à configurer chez votre registrar

| Type | Nom | Valeur |
|------|-----|--------|
| A | @ | IP_SERVEUR |
| A | www | IP_SERVEUR |
| MX | @ | mail.votredomaine.fr |
| TXT | @ | v=spf1 a mx ~all |
| TXT | dkim._domainkey | (voir /etc/opendkim/keys/) |
| TXT | _dmarc | v=DMARC1; p=quarantine; rua=mailto:admin@votredomaine.fr |

### Certificat SSL
```bash
certbot --apache -d votredomaine.fr -d www.votredomaine.fr
```

### Vérifier DKIM
```bash
opendkim-testkey -d votredomaine.fr -s dkim -x /etc/opendkim.conf
```

## Structure des fichiers créés

```
/root/
├── .bootstrap.conf          # Configuration sauvegardée
├── .phpmyadmin_alias        # URL secrète phpMyAdmin
└── scripts/
    ├── clamav_scan.sh       # Scan antivirus quotidien
    ├── check-updates.sh     # Rapport mises à jour hebdo
    ├── rkhunter_scan.sh     # Scan rootkits
    └── aide_check.sh        # Vérification intégrité

/etc/
├── ssh/sshd_config          # SSH durci
├── sysctl.d/99-hardening.conf
├── fail2ban/
├── apache2/
│   └── conf-available/security-headers.conf
├── modsecurity/
└── opendkim/
```

## Crons installés

| Heure | Tâche |
|-------|-------|
| 2h00 quotidien | Scan ClamAV |
| 3h00 dimanche | Scan rkhunter |
| 4h00 quotidien | Vérification AIDE |
| 7h00 lundi | Check updates + Audit sécurité |

## Sécurité

### Fonctions PHP dangereuses
Par défaut, le script propose de désactiver les fonctions shell (exec, system, passthru, etc.).
Vous pouvez les garder actives si votre application en a besoin.

### ModSecurity
Installé en mode **DetectionOnly** par défaut. Pour activer le blocage :
```bash
sed -i 's/SecRuleEngine DetectionOnly/SecRuleEngine On/' /etc/modsecurity/modsecurity.conf
systemctl restart apache2
```

### Vérification des logs ModSecurity
```bash
tail -f /var/log/apache2/modsec_audit.log
```

## Dépannage

### Emails non reçus
```bash
# Voir les derniers envois
grep -E 'status=(sent|deferred|bounced)' /var/log/mail.log | tail -20

# File d'attente
mailq

# Forcer le renvoi
postqueue -f
```

### Services
```bash
systemctl status apache2 mariadb postfix fail2ban clamav-daemon
```

### Certificat SSL
```bash
# Tester le renouvellement
certbot renew --dry-run

# Forcer le renouvellement
certbot renew --force-renewal
```

## Roadmap

Voir [TODO.md](TODO.md) pour les améliorations prévues :
- Backup automatique (MariaDB + fichiers + rotation)
- Netdata (monitoring temps réel)
- Redis (cache PHP)
- Support multi-sites
- Mode --uninstall

## Licence

MIT License - Libre d'utilisation et de modification.

## Auteur

Script développé pour le déploiement rapide et sécurisé de serveurs web Debian 13.
