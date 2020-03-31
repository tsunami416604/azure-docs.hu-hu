---
title: Az SAP HANA operációs rendszer biztonsági mentése és visszaállítása az Azure-beli (nagy példányok) II típusú ska-kban| Microsoft dokumentumok
description: Az sap HANA operációs rendszer biztonsági mentésének és visszaállításának végrehajtása az Azure-ban (nagy példányok) II típusú ska-kban
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 100e1b974e54d8c0065194bc7beb18f458011434
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616865"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>A 3-as verzió bélyegzőinek II típusú suk-jainak operációs rendszerének biztonsági mentése és visszaállítása

Ez a dokumentum ismerteti az operációs rendszer fájlszintű biztonsági mentésének és visszaállításának lépéseit **a** 3. 

>[!Important]
> **Ez a cikk nem vonatkozik a 4-es verzió hana nagy példánybélyegzőii iI típusú termékváltozatokra.** A 4-es verzióban a HANA nagypéldány-bélyegzőkben üzembe helyezett II típusú rendszerindító főazonosító egységekről tárolási pillanatképek készíthetők, mivel ez a helyzet a 3.


>[!NOTE]
>Az operációs rendszer biztonsági mentési parancsfájljai a rear szoftvert használja, amely előre telepítve van a kiszolgálón.  

Miután a Microsoft `Service Management` csapata befejezte a kiépítést, a kiszolgáló alapértelmezés szerint két biztonsági mentési ütemezéssel rendelkezik, hogy biztonsági másolatot készítsen a fájlrendszer szintjéről az operációs rendszer hátterében. A biztonsági mentési feladatok ütemezését a következő paranccsal ellenőrizheti:
```
#crontab –l
```
A biztonsági mentés ütemezését bármikor módosíthatja a következő paranccsal:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Hogyan kell kézi biztonsági mentést készíteni?

Az operációs rendszer fájlrendszerének biztonsági mentése már egy **cron feladat** használatával van ütemezve. Az operációs rendszer fájlszintjének biztonsági mentését azonban manuálisan is elvégezheti. Manuális biztonsági mentés végrehajtásához futtassa a következő parancsot:

```
#rear -v mkbackup
```
A következő képernyőn látható a minta kézi biztonsági mentés:

![Hogyan](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Hogyan lehet visszaállítani a biztonsági mentést?

A biztonsági másolatból visszaállíthatja a teljes biztonsági mentést vagy az egyes fájlokat. A visszaállításhoz használja a következő parancsot:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
A visszaállítás után a fájl visszakerül az aktuális munkakönyvtárba.

A következő parancs egy *fájl /etc/fstab* visszaállítását mutatja a *backup.tar.gz* biztonsági másolatfájlból
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>A biztonsági másolatból való visszaállítás után a fájlt a kívánt helyre kell másolnia.

A következő képernyőkép a teljes biztonsági mentés visszaállítását mutatja be:

![ÚtmutatóVisszaállításvisszaállítás.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Hogyan kell telepíteni a ReaR eszközt, és változtassa meg a konfigurációt? 

A Relax-and-Recover (ReaR) csomagok **előre telepítve** vannak a HANA nagy példányok **II típusú termékkészletében,** és nincs szükség műveletre. Közvetlenül elkezdheti használni a ReaR-t az operációs rendszer biztonsági mentéséhez.
Azonban az adott körülmények között, amikor telepítenie kell a csomagokat a saját, akkor kövesse a felsorolt lépéseket telepíteni és konfigurálni a ReaR eszköz.

A **ReaR** biztonsági másolat csomagok telepítéséhez használja a következő parancsokat:

**SLES** operációs rendszer esetén használja a következő parancsot:
```
#zypper install <rear rpm package>
```
**RHEL** operációs rendszer esetén használja a következő parancsot: 
```
#yum install rear -y
```
A ReaR eszköz konfigurálásához frissítenie kell **a paramétereket, OUTPUT_URL** és **BACKUP_URL** az */etc/rear/local.conf fájlban.*
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

A következő képernyőkép a teljes ![biztonsági mentés visszaállítását mutatja: RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
