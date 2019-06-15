---
title: Az Azure Site Recovery Deployment Planner verzióelőzményeinek
description: Másik hely Recovery Deployment Planner verziók javításokat és ismert korlátozások mellett a kiadás dátumok ismert.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 04/24/2019
ms.author: dapatil
ms.openlocfilehash: 2edf7ce3be1402a497ceab5b826a89ee43c5c39b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64927372"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Az Azure Site Recovery Deployment Planner verzióelőzményeinek

Ez a cikk ismerteti az Azure Site Recovery Deployment Planner a javítások, ismert korlátozások minden és a kiadási dátum együtt minden verziójára előzményeit.

## <a name="version-24"></a>2\.4-es verzió

**Kiadás dátuma: 2019. április 17.**

**Javításokat tartalmaz:**

- Továbbfejlesztett operációs rendszerek kompatibilitása, különösen, ha a honosítási-alapú hibakezelést.
- A hozzáadott virtuális gépek az adatok legfeljebb 20 MB/s sebesség (forgalom) módosítsa a kompatibilitási ellenőrzőlista.
- Továbbfejlesztett hibaüzenetek
  - Támogatás hozzáadva a vCenter 6.7.
  - Támogatás hozzáadva a Windows Server 2019 és a Red Hat Enterprise Linux (RHEL) munkaállomás.



## <a name="version-23"></a>2\.3-as verzió

**Kiadás dátuma: 2018. december 3.**

**Javításokat tartalmaz:**

- Kijavítva, amely ebben az esetben a Deployment Planner a megadott cél helye és előfizetése a jelentés elkészítése.

## <a name="version-22"></a>2\.2-es verzió 

**Kiadás dátuma: 2018. április 25.**

**Javításokat tartalmaz:**

- GetVMList művelet:
  - Kijavítva a meghiúsuljon, ha a megadott mappa nem létezik a GetVMList kiváltó problémát. Azt most hozza létre az alapértelmezett könyvtárat vagy a outputfile paraméterben megadott könyvtárat hoz létre.
  - Hozzáadott részletesebb GetVMList hiba okairól.
- A hozzáadott virtuális gép típussal kapcsolatos információk a Deployment Planner-jelentés a kompatibilis virtuális gépek munkalapon oszlopként.
- Hyper-V – Azure vészhelyreállítási:
  - Kizárt virtuális gépek megosztott virtuális merevlemezek és a csatlakoztatott lemezek, a profilkészítés. A Startprofiling művelet kizárt virtuális gépek listáját jeleníti meg a konzolon.
  - A hozzáadott virtuális gépek legfeljebb 64 lemezt nem kompatibilis virtuális gépek listáját.
  - A kezdeti replikáció (IR) és a különbözeti replikációt (DR) tömörítési tényező frissítése.
  - A hozzáadott korlátozottan támogatja az SMB-tárolót.

## <a name="version-21"></a>2\.1-es verzió

**Kiadás dátuma: 2018. január 3.**

**Javításokat tartalmaz:**

- Az Excel-jelentés frissítése.
- A GetThroughput művelet rögzített hibákról.
- Hozzáadva a beállítással korlátozhatja a virtuális gépek profilját, vagy a jelentés létrehozásához. Az alapértelmezett korlát a rendszer 1000 virtuális gépet.
- VMware-ből az Azure-beli vészhelyreállításához:
  - Kijavítva a Windows Server 2016 virtuális gép a nem kompatibilis táblába. 
  - Frissített kompatibilitási üzenetek Extensible Firmware Interface (EFI) Windows virtuális gépek számára.
- Frissítve a VMware Azure és Hyper-V – Azure virtuális gép az adatváltozás virtuális gépenként korlátot. 
- A virtuális gép listáját tartalmazó fájl elemzésekor megbízhatósága.

## <a name="version-201"></a>2\.0.1-es verziója

**Kiadás dátuma: 2017. december 7.**

**Javításokat tartalmaz:**

- Az ajánlás hozzáadott a hálózati sávszélesség-optimalizálását.

## <a name="version-20"></a>2\.0-s verzió

**Kiadás dátuma: 2017. november 28.**

**Javításokat tartalmaz:**

- Hyper-V támogatása az Azure-beli vészhelyreállításához.
- A hozzáadott költségkalkulátor.
- Új operációs rendszer verziójának ellenőrzése: VMware-ről az Azure-beli vészhelyreállításához annak megállapításához, hogy a virtuális gép kompatibilis vagy nem kompatibilis a védelemre. Az eszköz a virtuális gép a vCenter-kiszolgáló által visszaadott operációs rendszer verzió-karakterlánca használja. A vendég operációs rendszer verziója, hogy a felhasználó ki van választva, VMware-ben a virtuális gép létrehozása során.

**Ismert korlátozások:**

- A Hyper-V – Azure vészhelyreállítási, a virtuális gép nevét, amely tartalmazza a karaktereket, például: `,`, `"`, `[`, `]`, és ``` ` ``` nem támogatottak. Ha a profilkészítés során létrehozott, jelentéskészítés sikertelen lesz, vagy helytelen eredményt fog rendelkezni.
- VMware – Azure vészhelyreállítási a virtuális gép nevét tartalmazó vesszővel tagolt nem támogatott. Ha a profilkészítés során létrehozott, jelentés generálása sikertelen, vagy helytelen eredményt fog rendelkezni.

## <a name="version-131"></a>1\.3.1 verzió

**Kiadás dátuma: 2017. július 19.** 

**Javításokat tartalmaz:**

- A jelentés létrehozásakor a nagyméretű lemezek (> 1 TB) támogatása. Most már használhatja a Deployment Planner, amelyek lemezmérete 1 TB-nál nagyobb virtuális gépek replikációjának megtervezéséhez (legfeljebb 4095 GB-ig).
További információ a [Nagyméretű lemezek támogatásáról az Azure Site Recoveryben](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>1\.3-as verzió

**Kiadás dátuma: 2017. május 9.**

**Javításokat tartalmaz:**

- Támogatás hozzáadva a felügyelt lemezt a jelentés létrehozásakor. Egyetlen tárfiókba helyezhető virtuális gépek száma alapján számítják, ha a felügyelt lemez van kijelölve feladatátvételre/feladatátvételi tesztre.

## <a name="version-12"></a>1\.2-es verziója

**Kiadás dátuma: 2017. április 7.**

**Javításokat tartalmaz:**

- Hozzáadott rendszerindító típus (BIOS vagy EFI) ellenőrzi, hogy a virtuális gép kompatibilis vagy nem kompatibilis a védelmet a virtuális gépek.
- Hozzáadott operációs rendszer a kompatibilis virtuális gépek és a nem kompatibilis virtuális gépek munkalapok írja be mindegyik virtuális gép adatait.
- Támogatás hozzáadva a GetThroughput műveletet a US Government és kínai Microsoft Azure-régiókhoz.
- A rendszer most már néhány új előfeltételt is ellenőriz a vCenter- és az ESXi-kiszolgálók esetében.
- Kijavítva az első jönnek létre, ha a területi beállítása nem angol nyelvű hibás jelentéseket.

## <a name="version-11"></a>1\.1-es verzió

**Kiadás dátuma: 2017. március 9.**

**Javításokat tartalmaz:**

- Kijavítva, amely ebben az esetben a virtuális gépek profiljának készítéséhez, ha két vagy több azonos nevű vagy IP-címet a virtuális gépek különböző vCenter ESXi-gazdagépek között.
- Másolás és a keresés le kell tiltani a kompatibilis virtuális gépek és a nem kompatibilis virtuális gépek munkalapok okozó probléma kijavítva.

## <a name="version-10"></a>1\.0-s verzió

**Kiadás dátuma: 2017. február 23.**

**Ismert korlátozások:**

- Csak a VMware – Azure vészhelyreállítási forgatókönyvekben támogatja. A Hyper-V – Azure vészhelyreállítási forgatókönyvekben, használja a [Hyper-V capacity planner eszközt](./site-recovery-capacity-planning-for-hyper-v-replication.md).
- A US Government és kínai Microsoft Azure-régiókhoz a GetThroughput művelet nem támogatja.
- Az eszköz nem profil virtuális gépeket, ha a vCenter-kiszolgáló azonos nevű vagy IP-cím két vagy több virtuális gép több különböző ESXi-gazdagépek.
Ebben a verzióban az eszköz kihagyja a profilkészítést abban az esetben, ha a VMListFile fájlban ismétlődik egy virtuális gép neve vagy IP-címe. Ez áthidalható úgy, hogy a vCenter-kiszolgáló helyett az ESXi-gazdagéppel készíti el a virtuális gépek profilját. Győződjön meg arról, egyetlen példány futtatásához minden ESXi-gazdagép esetében.
