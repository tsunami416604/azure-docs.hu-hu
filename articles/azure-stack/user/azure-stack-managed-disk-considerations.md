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
ms.date: 09/05/2018
ms.author: sethm
ms.reviewer: jiahan
ms.openlocfilehash: 4bd36744cc417e85f49e58f9a08d2b9006da9fe4
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284029"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Az Azure Stack felügyelt lemezek: Különbségek és szempontok
Ez a cikk az Azure Stack-Managed Disks és az Azure Managed Disks ismert különbségeit foglalja össze. Azure Stack és az Azure magas szintű különbségeit kapcsolatos további információkért tekintse meg a [szempontok kulcs](azure-stack-considerations.md) cikk.

A felügyelt lemezek leegyszerűsítik a Lemezkezelés IaaS virtuális gépekhez, mivel kezeli az [tárfiókok](/azure/azure-stack/azure-stack-manage-storage-accounts) társított Virtuálisgép-lemezek.
  

## <a name="cheat-sheet-managed-disk-differences"></a>Hasznos tanácsok: felügyelt lemez különbségek

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
|Lemezméret  |Az Azure prémium szintű lemezes: P4 (32 GiB) való P80 (32 TiB)<br>Az Azure Standard SSD-lemez: E10 (128 GiB) való E80 (32 TiB)<br>Az Azure standard szintű HDD lemez: S4 (32 GiB) való S80 (32 TiB) |M4: 32 GIB-ra<br>M6: 64 GiB<br>M10: 128 GIB-ra<br>M15: 256 GiB<br>M20: 512 GIB-ra<br>M30: 1024 GiB |
|Lemezek teljesítményét elemző |Összesített mérőszámok és a lemezenkénti metrikák támogatott |Még nem támogatott |
|Migrálás      |Adja meg az eszköz meglévő nem felügyelt Azure Resource Manager virtuális gépek nélkül hozza létre újra a virtuális gép áttelepítése  |Még nem támogatott |


## <a name="metrics"></a>Mérőszámok
A storage-mérőszámok különbségek is vannak:
- Az Azure Stack a tranzakciós adatokat a storage-mérőszámok nem tesz különbséget a belső vagy külső hálózati sávszélességet.
- A storage-mérőszámok az Azure Stack tranzakciós adatokat a csatlakoztatott lemezeket virtuális gép hozzá nem tartalmazza.


## <a name="api-versions"></a>API-verziók
Az Azure Stack felügyelt lemezek a következő API-verziókat támogatja:
- 2017-03-30


## <a name="next-steps"></a>További lépések
[Azure Stack virtual machines bemutatása](azure-stack-compute-overview.md)
