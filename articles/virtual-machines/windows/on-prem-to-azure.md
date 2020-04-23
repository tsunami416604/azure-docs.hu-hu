---
title: Áttelepítés az AWS-ről és más platformokról felügyelt lemezekre az Azure-ban
description: Hozzon létre virtuális gépeket az Azure-ban más felhőkből, például az AWS-ből vagy más virtualizációs platformokról feltöltött virtuális merevlemezek használatával, és használja ki az Azure felügyelt lemezeinek előnyeit.
author: roygara
manager: twooley
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dc283d1845926b79fb541d8ccb011fe853f50484
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870286"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Áttelepítés az Amazon Web Services (AWS) szolgáltatásból és más platformokról felügyelt lemezekre az Azure-ban

VHD-fájlokat tölthet fel az AWS vagy a helyszíni virtualizációs megoldások az Azure-ba, hogy hozzon létre virtuális gépek, amelyek kihasználják a felügyelt lemezek. Az Azure Managed Disks szükségtelenné teszi az Azure IaaS virtuális gépek tárfiókjainak kezelését. Csak a szükséges lemez típusát (Premium vagy Standard) és méretét kell megadnia, és az Azure létrehozza és kezeli a lemezt. 

Általános és speciális Virtuális gépeket is feltölthet. 
- **Általános virtuális merevlemez** – a Sysprep programmal eltávolította az összes személyes fiókadatot. 
- **Speciális virtuális merevlemez** – az eredeti virtuális gép felhasználói fiókjait, alkalmazásait és egyéb állapotadatait tartja karban. 

> [!IMPORTANT]
> Mielőtt bármilyen virtuális merevlemezt feltöltene az Azure-ba, kövesse [a Windows VHD vagy VHDX előkészítése az Azure-ba való feltöltéshez.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Forgatókönyv                                                                                                                         | Dokumentáció                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Meglévő AWS EC2-példányokkal rendelkezik, amelyeket felügyelt lemezek használatával szeretne áttelepíteni az Azure virtuális gépeire                              | [Virtuális gép áthelyezése az Amazon Web Services (AWS) szolgáltatásból az Azure-ba](aws-to-azure.md)                           |
| Egy másik virtualizálási platformról származó virtuális géppel rendelkezik, amelyet lemezképként szeretne használni több Azure-beli virtuális gép létrehozásához. | [Töltsön fel egy általános virtuális merevlemezt, és használja egy új virtuális gép létrehozásához az Azure-ban](upload-generalized-managed.md) |
| Van egy egyedileg testre szabott virtuális gép, amely szeretné újra létrehozni az Azure-ban.                                                      | [Speciális virtuális merevlemez feltöltése az Azure-ba, és hozzon létre egy új virtuális gépet](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>A felügyelt lemezek áttekintése

Az Azure felügyelt lemezek leegyszerűsíti a virtuális gép kezelését azáltal, hogy megszünteti a tárfiókok kezelésének szükségességét. A felügyelt lemezek is kihasználják a virtuális gépek nagyobb megbízhatóságát egy rendelkezésre állási csoportban. Biztosítja, hogy a rendelkezésre állási csoportban lévő különböző virtuális gépek lemezei eléggé elkülönüljenek egymástól, hogy elkerüljék a meghibásodás egyetlen pontját. A különböző virtuális gépek lemezeit automatikusan elhelyezi egy rendelkezésre állási csoportban a különböző tárolási méretezési egységekben (bélyegzőkben), amelyek korlátozzák a hardver- és szoftverhibák miatt okozott egyetlen tárolási méretezési egység hibáinak hatását.
Az igényeinek megfelelően négyféle tárolási lehetőség közül választhat. A rendelkezésre álló lemeztípusokról a [Lemeztípus kiválasztása](disks-types.md)című cikkben olvashat.

## <a name="plan-for-the-migration-to-managed-disks"></a>A felügyelt lemezekre való áttelepítés megtervezése

Ez a szakasz segít a virtuális gép és a lemeztípusok legjobb döntésének meghozatalában.

Ha azt tervezi, hogy a nem felügyelt lemezekről felügyelt lemezekről való áttelepítést tervezi, vegye figyelembe, hogy a [Virtuálisgép közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkörrel rendelkező felhasználók nem módosíthatják a virtuális gép méretét (mivel azok átalakítás előtt is). Ennek az az oka, hogy a felügyelt lemezekkel rendelkező virtuális gépekhez a microsoft.Compute/disks/write engedéllyel kell rendelkeznie az operációs rendszer lemezein.

### <a name="location"></a>Hely

Válasszon egy helyet, ahol az Azure felügyelt lemezek érhetők el. Ha prémium szintű felügyelt lemezekre telepít át, győződjön meg arról is, hogy a prémium szintű tárhely elérhető abban a régióban, ahová át szeretne térni. Az elérhető helyekről az [Azure-szolgáltatások régiónkénti](https://azure.microsoft.com/regions/#services) naprakész információiért tekintse meg az Azure-szolgáltatások at.

### <a name="vm-sizes"></a>A virtuális gépek mérete

Ha prémium szintű felügyelt lemezekre telepíti át, frissítenie kell a virtuális gép méretét prémium szintű storage képes méretű elérhető a régióban, ahol a virtuális gép található. Tekintse át a virtuális gép méreteit, amelyek prémium szintű storage képes. Az Azure virtuális gép méretspecifikációi a [virtuális gépek méretei](sizes.md)ben vannak felsorolva.
Tekintse át a prémium szintű storage-tal működő virtuális gépek teljesítményjellemzőit, és válassza ki a számítási feladatoknak leginkább megfelelő virtuális gépméretet. Győződjön meg arról, hogy elegendő sávszélesség áll rendelkezésre a virtuális gép a lemezforgalom növeléséhez.

### <a name="disk-sizes"></a>Lemezméretek

**Prémium szintű felügyelt lemezek**

Hét típusú prémium szintű felügyelt lemezek, amelyek a virtuális gép, és mindegyik rendelkezik adott IP-k és átviteli korlátok. Vegye figyelembe ezeket a korlátokat, amikor kiválasztja a prémium szintű lemez típusát a virtuális gép hez az alkalmazás kapacitás, teljesítmény, méretezhetőség és csúcsterhelések alapján.

| Prémium szintű lemezek típusa  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Lemezméret           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS-érték lemezenként       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Adattovábbítás lemezenként | 25 MB másodpercenként  | 50 MB másodpercenként  | 100 MB másodpercenként | 125 MB másodpercenként |150 MB másodpercenként | 200 MB másodpercenként | 250 MB másodpercenként | 250 MB másodpercenként |

**Szabványos felügyelt lemezek**

Hét típusú szabványos felügyelt lemezek, amelyek a virtuális gép használható. Mindegyik különböző kapacitással rendelkezik, de azonos IOPS-és átviteli korlátokkal rendelkeznek. Válassza ki a standard felügyelt lemezek típusát az alkalmazás kapacitásigényei alapján.

| Standard lemez típusa  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Lemezméret           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| IOPS-érték lemezenként       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Adattovábbítás lemezenként | 60 MB másodpercenként | 60 MB másodpercenként | 60 MB másodpercenként | 60 MB másodpercenként |60 MB másodpercenként | 60 MB másodpercenként | 60 MB másodpercenként | 60 MB másodpercenként | 

### <a name="disk-caching-policy"></a>Lemezgyorsítótárazási házirend 

**Prémium szintű felügyelt lemezek**

Alapértelmezés szerint a lemezgyorsítótárazási házirend *írásvédett* az összes prémium szintű adatlemezhez, és a virtuális géphez csatlakoztatott prémium szintű *operációsrendszer-lemez írás-olvasás.* Ez a konfigurációs beállítás ajánlott az alkalmazás IOs optimális teljesítményének eléréséhez. Írási vagy csak írásra alkalmas adatlemezek (például SQL Server naplófájlok) esetén tiltsa le a lemezgyorsítótárazást, hogy jobb alkalmazásteljesítményt érjen el.

### <a name="pricing"></a>Díjszabás

Tekintse át a [felügyelt lemezek díjszabását.](https://azure.microsoft.com/pricing/details/managed-disks/) A prémium szintű felügyelt lemezek díjszabása megegyezik a prémium szintű nem felügyelt lemezek árával. A standard felügyelt lemezek díjszabása azonban eltér a szabványos nem felügyelt lemezekéétól.


## <a name="next-steps"></a>Következő lépések

- Mielőtt bármilyen virtuális merevlemezt feltöltene az Azure-ba, kövesse [a Windows VHD vagy VHDX előkészítése az Azure-ba való feltöltéshez.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
