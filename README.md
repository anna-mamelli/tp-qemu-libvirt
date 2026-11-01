# TP QEMU / KVM / libvirt — Mini-lab offensif

## Objectif
Mise en place d'un environnement de virtualisation complet (QEMU, KVM, libvirt, virt-manager) et d'un mini-lab offensif isolé (VM attaquant Kali / VM victime Metasploitable2).

## Architecture
- Hôte physique : Windows
- Hyperviseur : VM Debian (VMware), hébergeant QEMU/KVM/libvirt
- Réseau isolé libvirt : `lab-offensif` (192.168.100.0/24, sans accès internet)
- VM attaquant : Kali Linux (192.168.100.25)
- VM victime : Metasploitable2 (192.168.100.12)

## Scénario réalisé
1. Scan réseau (nmap) de Metasploitable2
2. Identification de la backdoor vsftpd 2.3.4
3. Exploitation via Metasploit (`exploit/unix/ftp/vsftpd_234_backdoor`) → shell root
4. Snapshot libvirt de l'état compromis (`etat-compromis`)
5. Remédiation : désactivation du service vsftpd via xinetd (`/etc/xinetd.d/vsftpd`)
6. Vérification : l'exploit échoue après remédiation
7. Snapshot libvirt de l'état remédié (`etat-remedie`)

## Fichiers
- `lab-offensif-network.xml` : définition du réseau isolé
- `kali-attaquant.xml` : définition de la VM Kali
- `metasploitable2.xml` : définition de la VM Metasploitable2
- `snapshot-etat-compromis.xml` / `snapshot-etat-remedie.xml` : snapshots documentés

## Difficultés rencontrées et résolues
- Virtualisation imbriquée bloquée par la sécurité basée sur la virtualisation (VBS) de Windows
- Erreur libvirt "Operation not permitted" due à une connexion par défaut en mode session (`qemu:///session`) au lieu du mode système (`qemu:///system`)
- Partition système trop petite, redimensionnée à chaud avec `fdisk`/`resize2fs`
