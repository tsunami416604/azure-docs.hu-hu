---
title: Hyper-V virtuálisgép-lemezek kizárása a vészhelyreállításból az Azure-ba az Azure-ban az Azure Site Recovery segítségével
description: Hogyan zárhatja ki a Hyper-V virtuálisgép-lemezeket az Azure Site Recovery használatával az Azure-ba történő replikációból.
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 50fb6da2905b2ae27547f25cce3d7a76ca7976b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498132"
---
# <a name="exclude-disks-from-replication"></a>Lemezek kizárása a replikációból

Ez a cikk ismerteti, hogyan zárhatja ki a lemezeket a Hyper-V virtuális gépek Azure-ba replikálásakor. A lemezeket több okból is ki szeretné zárni a replikációból:

- Győződjön meg arról, hogy a kizárt lemezen nem fontos adatok nem leszreplikálva.
- Optimalizálja a felhasznált replikációs sávszélességet vagy a céloldali erőforrásokat, kivéve azokat a lemezeket, amelyeket nem kell replikálnia.
- Mentse a tárolási és hálózati erőforrásokat, ha nem replikálja a szükségtelen adatokat.

Mielőtt kizárná a lemezeket a replikációból:

- [További információ](exclude-disks-replication.md) a lemezek kizárásáról.
- Tekintse át [a tipikus kizárási forgatókönyveket](exclude-disks-replication.md#typical-scenarios) és [példákat,](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) amelyek bemutatják, hogy a lemez kizárása hogyan befolyásolja a replikációt, a feladatátvételt és a feladatátvételt.

## <a name="before-you-start"></a>Előkészületek

A kezdés előtt vegye figyelembe az alábbiakat:

- **Replikáció**: Alapértelmezés szerint a rendszer a számítógép összes lemezét replikálja.
- **Lemez típusa**:
    - Az alaplemezek kizárhatók a replikációból.
    - Nem zárhatja ki az operációsrendszer-lemezeket.
    - Azt javasoljuk, hogy ne zárja ki a dinamikus lemezeket. A Site Recovery nem tudja azonosítani, hogy melyik virtuális merevlemez alapvető vagy dinamikus a vendég virtuális gépben.  Ha nem zárja ki az összes függő dinamikus kötetlemezek, a védett dinamikus lemez lesz egy meghibásodott lemez egy feladatátvételi rendszer, és az adatok a lemezen nem érhető el.
- **Lemezek hozzáadása/eltávolítása/kizárása:** A replikáció engedélyezése után nem adhat hozzá/távolíthat el/zárhat ki lemezeket a replikációhoz. Ha lemezt szeretne hozzáadni vagy eltávolítani vagy kizárni, le kell tiltania a virtuális gép védelmét, majd újra engedélyeznie kell azt.
- **Feladatátvétel:** Feladatátvétel után, ha a feladatátvétel i alkalmazások ki kell zárni a lemezeket annak érdekében, hogy működjön, manuálisan kell létrehoznia ezeket a lemezeket. Azt is megteheti, hogy integrálja az Azure-automatizálást egy helyreállítási tervbe, hogy a lemezt a gép feladatátvétele során hozza létre.
- **Feladat-visszavétel:** Ha feladatátvétel után visszaadja a feladatátvételt a helyszíni helyre, az Azure-ban manuálisan létrehozott lemezek nem lesznek visszavételek. Ha például három lemezt sikertelenül, és két lemezt hoz létre közvetlenül egy Azure virtuális gépen, csak három lemez, amely feladatátvételt követően a feladatátvétel után. Nem vehet fel olyan lemezeket, amelyeket manuálisan hoztak létre a feladat-visszavételkor vagy a virtuális gépek fordított replikációjában.

## <a name="exclude-disks"></a>Lemezek kizárása

1. Ha ki szeretné zárni a lemezeket, amikor engedélyezi a [replikációt](site-recovery-hyper-v-site-to-azure.md) egy Hyper-V virtuális géphez, miután kiválasztotta a replikálni kívánt virtuális gépeket, a **Replikációs** > **tulajdonságok** > **konfigurálása lapban** tekintse át a **Lemezek replikáláshoz** oszlopot. Alapértelmezés szerint minden lemez ki van jelölve a replikációhoz.
2. Ha nem szeretne replikálni egy adott lemezt, a **Lemezek ben a kizárni** kívánt lemezek kijelölésének törlése. 

    ![Lemezek kizárása a replikációból](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>További lépések
Ha sikerült beállítania és elindítani az üzemelő példányt, [ismerkedjen meg részletesebben](failover-failback-overview.md) a feladatátvételi különféle típusaival.
