---
title: Operációs rendszer biztonsági mentéséhez és visszaállításához SAP HANA Azure (nagy példányok) írja be a II termékváltozatok |} Microsoft Docs
description: Hajtsa végre az Azure (nagy példányok) típusú II termékváltozatok SAP HANA Operatign rendszer biztonsági mentése és visszaállítása
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f01a32612b335003856a372ece15ef300b9d93db
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063274"
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>Az operációs rendszer biztonsági mentéséhez és visszaállításához a típus II termékváltozatok

Ez a dokumentum ismerteti, hogyan hajtsa végre az operációs rendszer szintű biztonsági mentés, és visszaállíthatja a **típus II termékváltozatok** nagy HANA-példánya. 

>[!NOTE]
>Az operációs rendszer biztonsági mentési parancsfájlokat használ a hátsó a kiszolgálón telepített szoftver.  

A Microsoft Service Management csapat, alapértelmezés szerint a létesítési befejeződése után a kiszolgálót két biztonsági mentést ütemezéssel biztonsági mentése a fájlrendszer szintjén, készítsen biztonsági másolatot az operációs rendszer. Az ütemezés a biztonsági mentési feladat a következő paranccsal ellenőrizheti:
```
#crontab –l
```
A biztonsági mentés ütemezése a következő paranccsal bármikor módosíthatja:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Manuális biztonsági mentés készítése hogyan?

Az operációs rendszer fájlrendszer biztonsági mentése van ütemezve, használja a **cron feladat** már. Azonban az operációs rendszer szintű biztonsági mentés manuálisan is végezheti el. Manuális biztonsági mentés, futtassa a következő parancsot:

```
#rear -v mkbackup
```
A következő képernyő megjelenítése a minta manuális biztonsági mentés jeleníti meg:

![hogyan](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>A biztonsági másolat visszaállításával hogyan?

A biztonsági mentésből visszaállíthatja egy teljes biztonsági mentés vagy a fájlhoz. Visszaállításához használja a következő parancsot:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
A visszaállítás után a fájl az aktuális munkakönyvtárban a helyre lett állítva.

A következő parancs megjeleníti fájl visszaállítása */etc/fstabfrom* a biztonságimásolat-fájl *backup.tar.gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>A Másolás a kívánt helyre a biztonsági másolatból történt visszaállítása után kell.

Az alábbi képernyőfelvételen látható egy teljes biztonsági másolat visszaállítása:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Hogyan telepítse a hátsó eszközt, és módosítsa a konfigurációt? 

A Relax és-helyreállítás (hátsó) csomagok **előtelepített** a a **típus II termékváltozatok** HANA nagy példányokat, és nincs szükség művelet. Közvetlenül kezdheti hátsó az operációs rendszer biztonsági mentésre.
Ha telepítenie kell a csomagok saját körülmények között követheti telepítése és konfigurálása a hátsó eszköz a felsorolt lépéseket.

A telepítendő a **hátsó** csomagok biztonsági mentése, az alábbi parancsokat használja:

A **SLES** operációs rendszer, a következő paranccsal:
```
#zypper install <rear rpm package>
```
A **RHEL** operációs rendszer, a következő paranccsal: 
```
#yum install rear -y
```
A hátsó eszköz konfigurálásához, frissítenie kell paraméterek **OUTPUT_URL** és **BACKUP_URL** a a */etc/rear/local.conf fájl*.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

Az alábbi képernyőfelvételen látható egy teljes biztonsági másolat visszaállítása: ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
