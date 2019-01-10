---
title: Különbségek és szempontok a Managed Disks az Azure Stackben |} A Microsoft Docs
description: Különbségek és szempontok ismerje meg az Azure stack felügyelt lemezek használatakor.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: jiahan
ms.openlocfilehash: 3445974cf832b7ed594f704615482e1d9b0e351c
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159366"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Az Azure Stack felügyelt lemezek: különbségek és szempontok

Ez a cikk összefoglalja, ismert [Azure Stack-Managed Disks](azure-stack-manage-vm-disks.md) és [az Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md). Azure Stack és az Azure magas szintű különbségeit kapcsolatos további információkért tekintse meg a [szempontok kulcs](azure-stack-considerations.md) cikk.

A felügyelt lemezek leegyszerűsítik a Lemezkezelés IaaS virtuális gépekhez, mivel kezeli az [tárfiókok](../azure-stack-manage-storage-accounts.md) társított Virtuálisgép-lemezek.

> [!Note]  
> Felügyelt lemezek az Azure Stacken 1808 frissítés érhető el. Ez alapértelmezés szerint engedélyezve van az Azure Stack portálon 1811 Update használó virtuális gépek létrehozásakor.
  

## <a name="cheat-sheet-managed-disk-differences"></a>Hasznos tanácsok: Felügyelt lemez különbségek

| Szolgáltatás | Azure (globális) | Azure Stack |
| --- | --- | --- |
|Az inaktív adatok titkosítása |Az Azure Storage Service Encryption (SSE), az Azure Disk Encryption (ADE)     |A BitLocker 128 bites AES-titkosítás      |
|Kép          | Felügyelt egyéni lemezképek támogatása |Még nem támogatott|
|Biztonsági mentés beállításai |Az Azure Backup szolgáltatás támogatja |Még nem támogatott |
|Vész-helyreállítási lehetőségeket |Az Azure Site Recovery támogatja |Még nem támogatott|
|Lemeztípusok     |Prémium szintű SSD, a standard szintű SSD (előzetes verzió) és a Standard HDD |Prémium szintű SSD, Standard HDD |
|Prémium szintű lemezek  |Teljes mértékben támogatott. |Bővítheti, de nincs teljesítményszint vagy garancia  |
|Prémium szintű lemez iops-érték  |Függ a lemez mérete  |2300 iops-érték lemezenként |
|Prémium szintű lemezek átviteli sebesség |Függ a lemez mérete |Lemezenként 145 MB/s |
|Lemezméret  |Az Azure prémium szintű lemez: P4 (32 GiB) való P80 (32 TiB)<br>Az Azure Standard SSD-lemez: E10 (128 GiB) való E80 (32 TiB)<br>Az Azure standard szintű HDD lemez: S4 (32 GiB) való S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GiB<br>M15: 256 GiB<br>M20: 512 GiB<br>M30: 1024 GB |
|Lemezek pillanatkép-másolás|Pillanatkép készítése az Azure felügyelt támogatott futó virtuális Géphez csatolt lemezek|Még nem támogatott |
|Lemezek teljesítményét elemző |Összesített mérőszámok és a lemezenkénti metrikák támogatott |Még nem támogatott |
|Migrálás      |Adja meg az eszköz meglévő nem felügyelt Azure Resource Manager virtuális gépek nélkül hozza létre újra a virtuális gép áttelepítése  |Még nem támogatott |

> [!NOTE]  
> Felügyelt lemez IOPs és átviteli sebesség az Azure Stackben értéke helyett egy kiépített számot, amelyet az üzemállapotára negatív hatással a hardver- és az Azure Stackben futó számítási feladatok maximális szám.

## <a name="metrics"></a>Mérőszámok

A storage-mérőszámok különbségek is vannak:

- Az Azure Stack a tranzakciós adatokat a storage-mérőszámok nem tesznek különbséget a belső vagy külső hálózati sávszélességet.
- A storage-mérőszámok az Azure Stack tranzakciós adatokat nem tartalmazza a virtuális gép hozzá a csatlakoztatott lemezeket.

## <a name="api-versions"></a>API-verziók

Az Azure Stack felügyelt lemezek a következő API-verziókat támogatja:

- 2017-03-30

## <a name="known-issues"></a>Ismert problémák

1808 után a frissítések alkalmazása, után, a következő problémák léphetnek ha felügyelt lemezekkel rendelkező virtuális gépek üzembe helyezéséhez:

- Ha az előfizetés korábban jött létre a 1808 frissítése, a felügyelt lemezekkel rendelkező virtuális gép üzembe helyezése egy belső hiba miatt sikertelen lehet. A hiba elhárításához kövesse ezeket a lépéseket minden egyes előfizetés esetén:
   1. A bérlői portálon lépjen a **előfizetések** , és keresse meg az előfizetés. Kattintson a **erőforrás-szolgáltatók**, majd kattintson az **Microsoft.Compute**, és kattintson a **újraregisztrálni**.
   2. Lépjen az azonos előfizetéshez tartozó **hozzáférés-vezérlés (IAM)**, és ellenőrizze, hogy **Azure Stack – felügyelt lemez** szerepel a listán.
- Egy több-bérlős környezet van beállítva, ha a virtuális gépek üzembe helyezéséhez egy adott előfizetéshez tartozó és a Vendég címtár egy belső hiba miatt meghiúsulhat. A hiba elhárításához kövesse az alábbi lépéseket a [Ez a cikk](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) újrakonfigurálása a Vendég címtárak mindegyike.


## <a name="next-steps"></a>További lépések

- [Azure Stack virtual machines bemutatása](azure-stack-compute-overview.md)
