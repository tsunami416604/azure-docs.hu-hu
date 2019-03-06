---
title: Operációs rendszer biztonsági mentéséhez és visszaállításához az SAP HANA az Azure-ban (nagyméretű példányok) írja be a II termékváltozatok |} A Microsoft Docs
description: Hajtsa végre az operációs rendszer biztonsági mentés és visszaállítás Azure-ban (nagyméretű példányok) típusú II SKU-k az SAP Hana
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
ms.openlocfilehash: c82c5c74fe13bad99528486be69089df5f477457
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436340"
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>Az operációs rendszer biztonsági mentési és visszaállítási II. típusú termékváltozatokhoz

Ez a dokumentum ismerteti, hogyan hajtsa végre az operációs rendszer fájlszintű biztonsági mentés, és visszaállíthatja a **típus II termékváltozatok** , a nagyméretű HANA-példányokhoz. 

>[!NOTE]
>Az operációs rendszer biztonsági mentés parancsprogramjai használ a hátsó szoftvert, amely a kiszolgáló előzetesen már telepítve van.  

A kiépítés befejezése után a Microsoft szolgáltatás-kezelési csapatunk, alapértelmezés szerint a kiszolgáló biztonsági mentése a fájlrendszer két biztonsági mentést ütemezés van konfigurálva az operációs rendszer szintű biztonsági mentése. Az ütemezés a biztonsági mentési feladat a következő paranccsal ellenőrizheti:
```
#crontab –l
```
A biztonsági mentési ütemezés a következő paranccsal bármikor módosíthatja:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Manuális biztonsági mentés készítése hogyan?

Az operációs rendszer biztonsági másolat a helyrendszerről használatával ütemezhető egy **cron feladat** már. Azonban az operációs rendszer fájlszintű biztonsági mentés manuálisan is elvégezheti. Manuális biztonsági mentés végrehajtásához futtassa a következő parancsot:

```
#rear -v mkbackup
```
A következő képernyő show látható a minta manuális biztonsági mentés:

![Hogyan](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>A biztonsági másolat visszaállítási módját?

A biztonsági mentésből visszaállíthatja egy teljes biztonsági mentést, illetve egy adott fájl. Szeretne visszaállítani, használja a következő parancsot:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
A visszaállítás után helyreállított a fájl az aktuális munkakönyvtár.

A következő parancs bemutatja a visszaállítást egy fájl */etc/fstabfrom* a biztonságimásolat-fájl *backup.tar.gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Másolja a fájlt a kívánt helyre a biztonsági másolatból történt visszaállítása után kell.

Az alábbi képernyőképen látható egy teljes biztonsági másolat visszaállítása:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Telepítse a hátsó eszközt, és a konfiguráció módosítása hogyan? 

A Relax és-helyreállítás (hátsó) csomagok **előre telepített** a a **típus II termékváltozatok** nagyméretű HANA-példányokhoz, és nincs további teendője. A hátsó használatával az operációs rendszer biztonsági mentés közvetlenül megkezdése.
A körülmények között kell telepítenie a csomagokat a saját, követheti a felsorolt lépéseket annak telepítése és konfigurálása a hátsó eszköz.

Telepítése a **hátsó** csomagok biztonsági mentése, használja a következő parancsokat:

A **SLES** operációs rendszer, használja a következő parancsot:
```
#zypper install <rear rpm package>
```
A **RHEL** operációs rendszer, használja a következő parancsot: 
```
#yum install rear -y
```
A hátsó eszköz konfigurálásához frissítenie paraméterek **OUTPUT_URL** és **BACKUP_URL** a a */etc/rear/local.conf fájl*.
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

Az alábbi képernyőképen látható egy teljes biztonsági másolat visszaállítása: ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
