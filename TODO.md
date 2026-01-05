# TODO - Améliorations du script installer_serveur_web_debian13.sh

## Objectif
Script d'installation et de configuration sécurisée d'un serveur web Debian 13 en un clic.

---

## Sécurité (Priorité Haute)

- [ ] **Backup automatique**
  - Sauvegarde quotidienne : MariaDB + /var/www + /etc
  - Rotation : 7 jours, 4 semaines, 12 mois
  - Sync distant : rsync/rclone vers S3 ou serveur de backup
  - Script de restauration
  - Complexité : ⭐⭐

- [x] **AIDE (Advanced Intrusion Detection Environment)** ✅ FAIT
  - Détection de modifications de fichiers système
  - Alerte email si fichier critique modifié
  - Vérification quotidienne (4h00)
  - Complexité : ⭐⭐

- [x] **rkhunter** ✅ FAIT
  - Scan hebdomadaire anti-rootkit (dimanche 3h00)
  - Email si menace détectée
  - Mise à jour automatique des signatures
  - Complexité : ⭐

- [x] **Logwatch** ✅ FAIT
  - Résumé quotidien des logs par email
  - Analyse : SSH, Apache, Fail2ban, système
  - Complexité : ⭐

- [x] **SSH login alert** ✅ FAIT
  - Email à chaque connexion SSH réussie
  - Inclut : IP, user, date/heure, géolocalisation (via ipinfo.io)
  - Via script dans /etc/profile.d/
  - Complexité : ⭐

- [x] **Secure /tmp** ✅ FAIT
  - Monter /tmp avec noexec, nosuid, nodev
  - Prévient l'exécution de malwares depuis /tmp
  - Complexité : ⭐

- [x] **ModSecurity OWASP CRS** ✅ FAIT
  - Core Rule Set : règles WAF professionnelles
  - Protection : SQLi, XSS, LFI, RFI, etc.
  - Mode DetectionOnly par défaut, blocage activable
  - Complexité : ⭐⭐

- [ ] **Hardening kernel supplémentaire**
  - Désactiver les modules kernel inutiles
  - Restreindre dmesg aux root
  - Désactiver SysRq
  - Complexité : ⭐

---

## Monitoring

- [ ] **Netdata**
  - Dashboard temps réel (CPU, RAM, réseau, disque)
  - Accessible via https://domain/netdata (protégé)
  - Alertes intégrées
  - Complexité : ⭐

- [ ] **Alertes seuils critiques**
  - Email si disque > 80%
  - Email si RAM > 90%
  - Email si load > nb CPU
  - Script cron toutes les heures
  - Complexité : ⭐

- [ ] **Monitoring uptime externe**
  - Intégration UptimeRobot / Hetrix / StatusCake
  - Ou script curl depuis autre serveur
  - Alerte si site down
  - Complexité : ⭐⭐

- [ ] **Monitoring certificats SSL**
  - Alerte 14 jours avant expiration
  - Vérifier que le renouvellement auto fonctionne
  - Complexité : ⭐

---

## Backup & Restauration

- [ ] **Backup MariaDB**
  - mysqldump quotidien avec compression
  - Rotation automatique
  - Option : backup incrémental avec mariabackup
  - Complexité : ⭐

- [ ] **Backup fichiers web**
  - Archive /var/www quotidienne
  - Exclure : node_modules, vendor, cache
  - Complexité : ⭐

- [ ] **Backup configurations**
  - /etc/apache2, /etc/php, /etc/postfix, /etc/opendkim
  - /etc/fail2ban, /etc/ufw
  - Complexité : ⭐

- [ ] **Sync distant**
  - rclone vers S3/Backblaze/Google Drive
  - Ou rsync vers serveur de backup
  - Chiffrement des backups
  - Complexité : ⭐⭐

- [ ] **Script de restauration**
  - Menu interactif pour choisir quoi restaurer
  - Restauration DB, fichiers, ou complète
  - Test de restauration automatique mensuel
  - Complexité : ⭐⭐

---

## Performance

- [ ] **Redis**
  - Cache sessions PHP
  - Cache applicatif
  - Configuration sécurisée (bind localhost, password)
  - Complexité : ⭐

- [ ] **OPcache tuning**
  - memory_consumption optimisé
  - max_accelerated_files adapté
  - revalidate_freq pour prod
  - Complexité : ⭐

- [ ] **Compression Brotli**
  - Meilleure compression que gzip
  - mod_brotli pour Apache
  - Complexité : ⭐

- [ ] **PageSpeed (optionnel)**
  - mod_pagespeed pour optimisation auto
  - Minification, lazy load, etc.
  - Complexité : ⭐⭐

---

## Confort & Outils

- [ ] **Docker (optionnel)**
  - Installation Docker + Docker Compose
  - Configuration daemon sécurisée
  - Lazy Docker (TUI)
  - Complexité : ⭐⭐

- [ ] **Recap persistant**
  - Sauvegarder le récap dans /root/server-info.txt
  - Mise à jour à chaque audit
  - Complexité : ⭐

- [ ] **Mode --uninstall**
  - Désinstallation propre des composants
  - Restauration des configs originales
  - Complexité : ⭐⭐⭐

- [ ] **Mode --update**
  - Mise à jour des composants installés
  - Sans tout réinstaller
  - Complexité : ⭐⭐

- [ ] **Support multi-sites**
  - Création automatique de vhosts
  - Un certificat par domaine
  - Isolation des sites
  - Complexité : ⭐⭐

---

## Documentation

- [ ] **Génération doc serveur**
  - Markdown avec toutes les infos du serveur
  - Ports, services, chemins, credentials
  - Commandes utiles personnalisées
  - Complexité : ⭐

- [ ] **Changelog intégré**
  - Log des modifications effectuées
  - Date + description de chaque run
  - Complexité : ⭐

---

## Priorités recommandées

### Phase 1 - Essentiel ✅ TERMINÉ (sauf backup)
1. [ ] Backup automatique (DB + fichiers + rotation)
2. [x] SSH login alert ✅
3. [x] Logwatch ✅
4. [x] rkhunter ✅

### Phase 2 - Monitoring
5. [ ] Netdata
6. [ ] Alertes seuils critiques
7. [ ] Monitoring certificats SSL

### Phase 3 - Hardening ✅ TERMINÉ
8. [x] AIDE ✅
9. [x] ModSecurity OWASP CRS ✅
10. [x] Secure /tmp ✅

### Phase 4 - Performance
11. [ ] Redis
12. [ ] OPcache tuning
13. [ ] Brotli

### Phase 5 - Confort
14. [ ] Docker (optionnel)
15. [ ] Recap persistant
16. [ ] Support multi-sites

---

## Notes

- Chaque amélioration doit être optionnelle (question y/n)
- Ajouter les checks correspondants dans la section vérifications
- Ajouter les infos dans le récap final
- Mettre à jour le rapport d'audit HTML

---

*Dernière mise à jour : 2026-01-05*
