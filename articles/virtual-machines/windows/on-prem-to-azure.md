---
title: AWS-ről és más platformokra át az Azure Managed Disks |} A Microsoft Docs
description: Virtuális gép létrehozása az Azure-ban a VHD-k más felhőkben, mint az AWS vagy más virtualizációs platformokról feltöltött, és kihasználhatja az Azure Managed Disks.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05e687ab31b6c19193076033e1350952549d26e0
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56330749"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Az Amazon Web Services (AWS) és más platformokra át az Azure Managed Disks

Az Azure-bA hozhat létre virtuális gépeket, amelyek kihasználják a Managed Disks AWS vagy helyi virtualizációs megoldások a VHD-fájlokat tölthet fel. Az Azure Managed Disks feleslegessé teszi a storage-fiókok kezelése az Azure IaaS virtuális gépek. Csak adja meg a típusát (prémium vagy Standard) és a méretét kell lemez van szüksége, és az Azure létrehozza és felügyeli a lemezt Ön helyett. 

Általános, és speciális virtuális merevlemezek tölthet fel. 
- **Általános virtuális Merevlemezből** -volt-e minden személyes fiókadatot a Sysprep segítségével távolítja el. 
- **Specializált virtuális merevlemez** – a felhasználói fiókok, alkalmazások és más állapot adatait az eredeti virtuális gép kezeli. 

> [!IMPORTANT]
> Minden olyan VHD feltöltése az Azure-ba, mielőtt követendő [Windows VHD vagy VHDX feltöltése az Azure előkészítése](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Forgatókönyv                                                                                                                         | Dokumentáció                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Felügyelt lemezek használata Azure virtuális gépeket áttelepíteni kívánt meglévő AWS EC2-példányok rendelkezik                              | [Az Amazon Web Servicesből (AWS) virtuális gép áthelyezése az Azure-bA](aws-to-azure.md)                           |
| Rendelkezik egy virtuális Gépet, amely szeretné képként használatával több Azure virtuális gép létrehozása egy másik virtualizációs platformról. | [Általános VHD feltöltése és ezzel hozzon létre egy új virtuális Gépet az Azure-ban](upload-generalized-managed.md) |
| Rendelkezik egy egyedileg testre szabott virtuális Gépet, amelyet szeretne az Azure-ban hozza létre újra.                                                      | [Egyéni VHD feltöltése az Azure-ba, és a egy új virtuális gép létrehozása](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>A felügyelt lemezek áttekintése

Az Azure Managed Disks Virtuálisgép-kezelés felügyelnie tárfiókokat eltávolításával egyszerűbbé teszi. A Managed Disks emellett juttatás a rendelkezésre állási csoportban lévő virtuális gépek nagyobb megbízhatóságot. Ez biztosítja, hogy a rendelkezésre állási csoport különböző virtuális lemezek egy meghibásodási pont elkerülése érdekében hibapontok. A rendelkezésre állási csoport a különböző tárolási skálázási egységeket (stampek), amely korlátozza a egyetlen skálázási egységek hibáival oka a hardver és szoftver hibák különböző virtuális lemezek automatikusan helyezi.
Igényei alapján választhat a négy típusú tárolási lehetőségeket. A rendelkezésre álló lemeztípusok kapcsolatos további információkért tekintse meg ezt a cikket [válassza ki a lemez típusát](disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Tervezze meg a migrálás a Managed Disks szolgáltatásba

Ez a szakasz segít, hogy a legjobb döntést a virtuális gép és a lemez típusa.

Ha azt tervezi, nem felügyeltről felügyelt lemezessé szolgáltatásba, célszerű tisztában lennie a felhasználók által a [virtuális gépek Közreműködője](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkör nem tudják módosítani a Virtuálisgép-méret (mivel azok sikerült előzetes átalakítás). Ennek az az oka a felügyelt lemezekkel rendelkező virtuális gépek a felhasználót, hogy az operációsrendszer-lemezek Microsoft.Compute/disks/write engedélye szükséges.

### <a name="location"></a>Hely

Jelöljön ki egy helyet, ahol érhetők el az Azure Managed Disks. Ha az áttelepítés a Premium Managed Disks szolgáltatásba, is elérhető legyen a Premium storage a régióban, ahol kíván áttelepíteni. Lásd: [Azure-szolgáltatások régió szerint](https://azure.microsoft.com/regions/#services) naprakész információk az elérhető helyek.

### <a name="vm-sizes"></a>A virtuális gépek mérete

Ha az áttelepítés a Premium Managed Disks szolgáltatásba, akkor frissítse a virtuális gép méretét a Premium Storage képes a méret elérhető a régióban, ahol a virtuális gép is található. Tekintse át a Premium Storage képes a a Virtuálisgép-méretek. Az Azure virtuális gép mérete előírások felsorolt [virtuális gépek méretei](sizes.md).
Tekintse át a teljesítményt nyújt, amely a Premium Storage működnek, és válassza ki a leginkább megfelelő Virtuálisgép-méretet a számítási feladathoz leginkább megfelelő virtuális gépek. Győződjön meg arról, hogy van-e elegendő sávszélesség érhető el a meghajtó a lemez forgalmat a virtuális gép.

### <a name="disk-sizes"></a>Lemezméretek

**Premium Managed Disks**

Prémium szintű felügyelt lemezek, amelyek használhatók a virtuális gép hét típusa van, mindegyik adott IOPs és átviteli sebesség korlátok. Figyelembe ezeket a korlátokat a prémium szintű lemeztípus kiválasztása a virtuális gép kapacitását, teljesítmény, méretezhetőség tekintetében az alkalmazás igényeinek megfelelően, és csúcs tölti be.

| Prémium szintű lemezek típusa  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Lemezméret           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS-érték lemezenként       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Adattovábbítás lemezenként | 25 MB / s  | 50 MB / s  | 100 MB / s | 125 MB / s |150 MB / s | 200 MB / s | 250 MB / s | 250 MB / s |

**Standard szintű felügyelt lemezekre**

Hét típusa, a standard szintű felügyelt lemezek, amelyek használhatók a virtuális gép van. Azok a más kapacitásokhoz de azonos IOPS- és feldolgozásisebesség-korlátait. Válassza ki a standard szintű Managed disks kapacitást az alkalmazás igényeinek megfelelően.

| Standard lemez típusa  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Lemezméret           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| IOPS-érték lemezenként       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Adattovábbítás lemezenként | 60 MB / s | 60 MB / s | 60 MB / s | 60 MB / s |60 MB / s | 60 MB / s | 60 MB / s | 60 MB / s | 

### <a name="disk-caching-policy"></a>Lemez gyorsítótárazási házirend 

**Premium Managed Disks**

Alapértelmezés szerint a lemez gyorsítótárazási házirend a *csak olvasható* minden a prémium szintű adatlemezek esetén és *olvasási és írási* a prémium szintű operációsrendszer-lemez a virtuális Géphez csatlakoztatva. Ezt a konfigurációs beállítást az optimális teljesítmény érdekében az alkalmazás IOs-hez javasolt. Írási vagy csak írási adatlemezek (például az SQL Server-naplófájlok) tiltsa le a lemezek gyorsítótárazása, így jobb alkalmazásteljesítményt érhet el.

### <a name="pricing"></a>Díjszabás

Tekintse át a [a felügyelt lemezek díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/). Prémium szintű Managed Disks díjszabása megegyezik a prémium szintű Unmanaged Disks. Azonban a standard szintű Managed Disks díjszabása eltér attól a standard szintű nem felügyelt lemezek.


## <a name="next-steps"></a>További lépések

- Minden olyan VHD feltöltése az Azure-ba, mielőtt követendő [Windows VHD vagy VHDX feltöltése az Azure előkészítése](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
