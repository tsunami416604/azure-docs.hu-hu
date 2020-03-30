---
title: Az Azure site-helyreállítási telepítéstervező verzióelőzményei
description: Ismert különböző Site Recovery Deployment Planner verziók javítások és ismert korlátozások mellett a kiadási dátumokat.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 10/16/2019
ms.author: dapatil
ms.openlocfilehash: bf32809f426f3bfcabd08ec3bd95e76202aa8f84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72433410"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Az Azure site-helyreállítási telepítéstervező verzióelőzményei

Ez a cikk az Azure Site Recovery Deployment Planner összes verziójának előzményeit, valamint a javításokat, az egyes és a kiadási dátumok ismert korlátait ismerteti.

## <a name="version-251"></a>2.51-es verzió

**Megjelenési dátum: August 22, 2019**

**Javítások:**

- A Deployment Planner 2.5-ös verziójával kapcsolatos költségajánlási probléma kijavítva

## <a name="version-25"></a>2.5-ös verzió

**Megjelenési dátum: July 29, 2019**

**Javítások:**

- VMware virtuális gépek és fizikai gépek esetén a javaslat a felügyelt lemezekre való replikáción alapul.
- A Windows 10 (x64), a Windows 8.1 (x64), a Windows 8 (x64), a Windows 7 (x64) SP1 vagy újabb verzióinak támogatása

## <a name="version-24"></a>2.4-es verzió

**Megjelenési dátum: April 17, 2019**

**Javítások:**

- Továbbfejlesztett operációsrendszer-kompatibilitás, különösen a lokalizáción alapuló hibák kezelésekor.
- Virtuális gépek hozzáadása legfeljebb 20 Mbps adatváltozási sebességgel (lemorzsolódás) a kompatibilitási ellenőrzőlistához.
- Továbbfejlesztett hibaüzenetek
- Hozzáadott támogatás vCenter 6.7.
- A Windows Server 2019 és a Red Hat Enterprise Linux (RHEL) munkaállomás támogatása hozzáadva.



## <a name="version-23"></a>2.3-as verzió

**Megjelenési dátum: December 3, 2018**

**Javítások:**

- Kijavítottunk egy problémát, amely megakadályozta, hogy a Központi telepítési tervező jelentést készítsen a megadott célhelyről és előfizetésről.

## <a name="version-22"></a>2.2-es verzió 

**Megjelenési dátum: April 25, 2018**

**Javítások:**

- GetVMList műveletek:
  - Kijavítottuk azt a problémát, amely miatt a GetVMList meghibásodott, ha a megadott mappa nem létezik. Most vagy létrehozza az alapértelmezett könyvtárat, vagy létrehozza a kimeneti fájl paraméterben megadott könyvtárat.
  - A GetVMList részletesebb hibaok hozzáadása.
- Virtuálisgép-típusadatok hozzáadása oszlopként a Központi telepítéstervező jelentés kompatibilis virtuális gépeinek lapjában.
- Hyper-V az Azure vész-helyreállítási:
  - Kizárt virtuális gépek megosztott Virtuális merevlemezekkel és PassThrough lemezekkel a profilkészítésből. A Startprofiling művelet a konzolon lévő kizárt virtuális gépek listáját jeleníti meg.
  - 64 lemezzel rendelkező virtuális gépek hozzáadása az inkompatibilis virtuális gépek listájához.
  - Frissítve a kezdeti replikációs (IR) és a különbözeti replikációs (DR) tömörítési tényező.
  - Az SMB-tárhely korlátozott támogatása hozzáadva.

## <a name="version-21"></a>2.1-es verzió

**Megjelenési dátum: January 3, 2018**

**Javítások:**

- Az Excel-jelentés frissítve.
- Javítottuk a GetThroughput művelet hibáit.
- Hozzáadott lehetőség, hogy korlátozza a virtuális gépek száma a profil vagy a jelentés létrehozása. Az alapértelmezett korlát 1000 virtuális gép.
- VMware az Azure vész-helyreállítási:
  - Kijavítottuk a Windows Server 2016 virtuális gép nem kompatibilis táblába kerülésének egyik adását. 
  - Frissített kompatibilitási üzenetek az EFI Windows virtuális gépekhez.
- Frissítette a VMware az Azure-ra és a Hyper-V-az Azure-ba, virtuális gép adatváltozás korlátját virtuális gépenként. 
- A virtuális gépek listájának fájlelemzésének megbízhatósága.

## <a name="version-201"></a>2.0.1-es verzió

**Megjelenési dátum: December 7, 2017**

**Javítások:**

- Hozzáadva a hálózati sávszélesség optimalizálására vonatkozó javaslat.

## <a name="version-20"></a>2.0-s verzió

**Megjelenési dátum: November 28, 2017**

**Javítások:**

- A Hyper-V támogatása az Azure vész-helyreállítási.
- Hozzáadott költség kalkulátor.
- Hozzáadott operációs rendszer verzióellenőrzése a VMware az Azure vész-helyreállítási annak megállapítására, hogy a virtuális gép kompatibilis vagy nem kompatibilis a védelem. Az eszköz az operációs rendszer verziókarakterláncát használja, amelyet a vCenter-kiszolgáló avirtuális géphez visszaad. Ez a vendég operációs rendszer verziója, amelyet a felhasználó kiválasztott a virtuális gép létrehozásakor a VMware-ben.

**Ismert korlátozások:**

- A Hyper-V és az Azure vész-helyreállítási, virtuális `,`gép `"` `[`nevét `]`tartalmazó ``` ` ``` karakterek, mint: , , , , és nem támogatott. Profilkezelés esetén a jelentés létrehozása sikertelen lesz, vagy hibás eredményt ad.
- A VMware az Azure vész-helyreállítási, vesszőt tartalmazó nevű virtuális gép nem támogatott. Profilkezelés esetén a jelentés létrehozása sikertelen, vagy hibás eredményt ad.

## <a name="version-131"></a>1.3.1-es verzió

**Megjelenési dátum: July 19, 2017** 

**Javítások:**

- A jelentés létrehozásában a nagy lemezek (> 1 TB) támogatása hozzáadva. Mostmár a Deployment Planner segítségével megtervezheti az 1 TB-nál (legfeljebb 4095 GB) lemezmérettel (1 TB-nál nagyobb lemezmérettel) működő virtuális gépek replikációját.
További információ a [Nagyméretű lemezek támogatásáról az Azure Site Recoveryben](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>1.3-as verzió

**Megjelenési dátum: May 9, 2017**

**Javítások:**

- A felügyelt lemez támogatása hozzáadva a jelentés létrehozásához. Az egy tárfiókba helyezhető virtuális gépek számát a rendszer abból akarom függőbe venni, hogy a felügyelt lemez ki van-e jelölve a feladatátvételi/tesztfeladat-átvételi konfigurációhoz.

## <a name="version-12"></a>1.2-es verzió

**Megjelenési dátum: April 7, 2017**

**Javítások:**

- Hozzáadott rendszerindítási típus (BIOS vagy EFI) ellenőrzi az egyes virtuális gépek annak megállapítására, hogy a virtuális gép kompatibilis vagy nem kompatibilis a védelem.
- A kompatibilis virtuális gépek és a nem kompatibilis virtuális gépek munkalapjaiban lévő egyes virtuális gépek operációsrendszer-típusadataihozzáadása.
- A GetThroughput művelet támogatása az Egyesült Államok kormánya és Kína Microsoft Azure régióiban.
- A rendszer most már néhány új előfeltételt is ellenőriz a vCenter- és az ESXi-kiszolgálók esetében.
- Kijavítottuk azt a problémát, amely miatt helytelen jelentés jött létre, amikor a területi beállítások nem angol nyelvűek.

## <a name="version-11"></a>1.1-es verzió

**Megjelenési dátum: March 9, 2017**

**Javítások:**

- Kijavítottuk azt a problémát, amely megakadályozta a virtuális gépek profilkészítését, ha két vagy több azonos nevű vagy IP-címmel rendelkező virtuális gép van a különböző vCenter ESXi-állomásokon.
- Kijavítottuk azt a problémát, amely miatt a kompatibilis virtuális gépek és az inkompatibilis virtuális gépek munkalapjainak másolása és keresése le lett tiltva.

## <a name="version-10"></a>1.0-s verzió

**Megjelenési dátum: Február 23, 2017**

**Ismert korlátozások:**

- Csak a VMware az Azure vész-helyreállítási forgatókönyvek támogatja. A Hyper-V és az Azure vész-helyreállítási forgatókönyvek, használja a [Hyper-V kapacitástervező eszköz.](./site-recovery-capacity-planning-for-hyper-v-replication.md)
- Nem támogatja a GetThroughput műveletet az Amerikai Egyesült Államok kormánya és Kína Microsoft Azure-régióiban.
- Az eszköz nem profil virtuális gépek, ha a vCenter-kiszolgáló két vagy több virtuális gép azonos nevű vagy IP-címet különböző ESXi-állomások.
Ebben a verzióban az eszköz kihagyja a profilkészítést abban az esetben, ha a VMListFile fájlban ismétlődik egy virtuális gép neve vagy IP-címe. Ez áthidalható úgy, hogy a vCenter-kiszolgáló helyett az ESXi-gazdagéppel készíti el a virtuális gépek profilját. Győződjön meg arról, hogy minden ESXi-állomáshoz egy példányt futtat.
