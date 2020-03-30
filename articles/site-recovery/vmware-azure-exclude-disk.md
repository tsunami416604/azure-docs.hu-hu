---
title: VMware Virtuálisgép-lemezek kizárása a vészhelyreállításból az Azure-ba az Azure-ban az Azure Site Recovery segítségével
description: Vmware VM-lemezek kizárása az Azure Site Recovery használatával az Azure Site Recovery replikációjából.
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: cd54da5ee01206e576157435135065189bfb8035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495367"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Lemezek kizárása a VMware VM-replikációból az Azure-ba

Ez a cikk ismerteti, hogyan zárhatja ki a lemezeket, amikor a VMware virtuális gépek replikálása az Azure-ba vész-helyreállítási. A lemezeket több okból is ki szeretné zárni a replikációból:

- Győződjön meg arról, hogy a kizárt lemezen nem fontos adatok nem leszreplikálva.
- Optimalizálja a felhasznált replikációs sávszélességet vagy a céloldali erőforrásokat, kivéve azokat a lemezeket, amelyeket nem kell replikálnia.
- Mentse a tárolási és hálózati erőforrásokat, ha nem replikálja a szükségtelen adatokat.

Mielőtt kizárná a lemezeket a replikációból:

- [További információ](exclude-disks-replication.md) a lemezek kizárásáról.
- Tekintse át [a tipikus kizárási forgatókönyveket](exclude-disks-replication.md#typical-scenarios) és [példákat,](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) amelyek bemutatják, hogy a lemez kizárása hogyan befolyásolja a replikációt, a feladatátvételt és a feladatátvételt.

## <a name="before-you-start"></a>Előkészületek

 A kezdés előtt vegye figyelembe az alábbiakat:

- **Replikáció**: Alapértelmezés szerint a rendszer a számítógép összes lemezét replikálja.
- **Lemeztípusa**: Csak az alaplemezek zárhatók ki a replikációból. Nem zárhatja ki az operációsrendszer-lemezeket és a dinamikus lemezeket.
- **Mobilitási szolgáltatás**: A lemez replikációból való kizárásához a replikáció engedélyezése előtt manuálisan kell telepítenie a Mobilitás szolgáltatást a számítógépre. A leküldéses telepítés nem használható, mivel ez a módszer csak a replikáció engedélyezése után telepíti a Mobilitás szolgáltatást egy virtuális gépre.  
- **Lemezek hozzáadása/eltávolítása/kizárása:** A replikáció engedélyezése után nem adhat hozzá/távolíthat el/zárhat ki lemezeket a replikációhoz. Ha lemezeket szeretne hozzáadni vagy eltávolítani vagy kizárni, le kell tiltania a számítógép védelmét, majd újra engedélyeznie kell azt.
- **Feladatátvétel:** Feladatátvétel után, ha a feladatátvétel i alkalmazások kirekesztett lemezeket kell dolgozni, létre kell hoznia ezeket a lemezeket manuálisan. Azt is megteheti, hogy integrálja az Azure-automatizálást egy helyreállítási tervbe, hogy a lemezt a gép feladatátvétele során hozza létre.
- **Feladat-visszavétel-Windows:** Ha feladatátvétel után visszaadja a feladatátvételt, az Azure-ban manuálisan létrehozott Windows-lemezek nem lesznek visszavételek. Ha például három lemezt sikertelenül, és két lemezt hoz létre közvetlenül az Azure virtuális gépeken, csak a három lemez, amely feladatátvételre kerül, lesz feladatátvétel.
- **Feladat-visszavétel-Linux:** Linux-gépek feladat-visszavétele esetén az Azure-ban manuálisan létrehozott lemezek et a rendszer nem adja vissza. Ha például három lemezt sikertelenül, és hozzon létre két lemezt közvetlenül az Azure virtuális gépeken, mind az öt sikertelen lesz vissza. Nem zárhatja ki azokat a lemezeket, amelyeket manuálisan hoztak létre a feladat-visszavétel, vagy a virtuális gépek újbóli védelmében.



## <a name="exclude-disks-from-replication"></a>Lemezek kizárása a replikációból

1. Ha engedélyezi a [replikációt](site-recovery-hyper-v-site-to-azure.md) egy VMware virtuális gép, miután kiválasztja a replikáni kívánt virtuális gépek, a **Replikációs** > **tulajdonságok** > **konfigurálása tulajdonságainak** engedélyezése lap, tekintse át a **lemezek replikálása** oszlopban. Alapértelmezés szerint minden lemez ki van jelölve a replikációhoz.
2. Ha nem szeretne replikálni egy adott lemezt, a **Lemezek ben a kizárni** kívánt lemezek kijelölésének törlése. 

    ![Lemezek kizárása a replikációból](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>További lépések
Ha sikerült beállítania és elindítani az üzemelő példányt, [ismerkedjen meg részletesebben](failover-failback-overview.md) a feladatátvételi különféle típusaival.
