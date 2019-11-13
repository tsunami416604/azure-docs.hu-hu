---
title: Az Azure-beli virtuális gépek ismételt védetté Azure Site Recoveryával az elsődleges régióban | Microsoft Docs
description: Ismerteti, hogyan lehet az Azure-beli virtuális gépeket a feladatátvétel után, a másodlagosról az elsődleges régióba Azure Site Recovery használatával újra védelemmel ellátni.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 955e1b84f897a5eb877033e0a58b8d661f143a14
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954191"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Az Azure-beli virtuális gépek ismételt védetté váltása az elsődleges régióba


Ha az Azure-beli virtuális gépeket az egyik régióból a másikba [Azure site Recovery](site-recovery-overview.md)használatával hajtja végre, a virtuális gépek [nem](site-recovery-failover.md) védett állapotban indulnak el a másodlagos régióban. Ha a virtuális gépeket az elsődleges régiónak adja vissza, a következőket kell tennie:

- Állítsa be újra a virtuális gépeket a másodlagos régióban, hogy azok az elsődleges régióba replikálódnak.
- Miután az ismételt védelem befejeződik, és a virtuális gépek replikálódnak, a másodlagosról az elsődleges régióba végezheti a feladatátvételt.

## <a name="prerequisites"></a>Előfeltételek
1. A virtuális gép feladatátvételét az elsődlegesről a másodlagos régióba kell véglegesíteni.
2. Az elsődleges célhelynek elérhetőnek kell lennie, és képesnek kell lennie az adott régióban lévő erőforrások elérésére vagy létrehozására.

## <a name="reprotect-a-vm"></a>Virtuális gép újravédése

1. A tárolóban > **replikált elemek**területen kattintson a jobb gombbal a feladatátvételen átadott virtuális gépre, majd **válassza az** **ismételt védelem**lehetőséget. Az ismételt védelem irányának másodlagosról elsődlegesre kell mutatnia.

   ![Ismételt védelme](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Tekintse át az erőforráscsoportot, a hálózatot, a tárterületet és a rendelkezésre állási készleteket. Ezután kattintson az **OK** gombra. Ha van olyan erőforrás, amely újként van megjelölve, akkor az ismételt védelmi folyamat részeként jönnek létre.
3. A védelmi feladatsor a legfrissebb adatokkal magot a célhelyre. A befejezést követően a különbözeti replikáció zajlik. Ezután visszatérhet az elsődleges helyre. Kiválaszthatja a Storage-fiókot vagy azt a hálózatot, amelyet az ismételt védelem során használni szeretne, a Testreszabás lehetőség használatával.

   ![Testreszabási lehetőség](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Az ismételt védelem beállításainak testreszabása

Az ismételt védelem során a cél VMe következő tulajdonságait szabhatja testre.

![Testreszabás](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Tulajdonság |Megjegyzések  |
|---------|---------|
|Cél erőforráscsoport     | Módosítsa a célként megadott erőforráscsoportot, amelyben a virtuális gép létrejött. Az ismételt védelem részeként a cél virtuális gép törlődik. Kiválaszthat egy új erőforráscsoportot, amely alatt a virtuális gépet a feladatátvételt követően hozza létre.        |
|Célként megadott virtuális hálózat     | A célként megadott hálózat nem módosítható az újravédelemi feladatokban. A hálózat módosításához ismételje meg a hálózati leképezést.         |
|Cél tárterület (a másodlagos virtuális gép nem használ felügyelt lemezeket)     | Módosíthatja azt a Storage-fiókot, amelyet a virtuális gép a feladatátvételt követően használ.         |
|Replika által felügyelt lemezek (a másodlagos virtuális gép felügyelt lemezeket használ)    | A Site Recovery replika felügyelt lemezeket hoz létre az elsődleges régióban, hogy tükrözze a másodlagos virtuális gép felügyelt lemezeit.         |
|Cache Storage     | Megadhat egy gyorsítótárbeli Storage-fiókot, amelyet a replikáció során használni szeretne. Alapértelmezés szerint a rendszer új gyorsítótárbeli Storage-fiókot hoz létre, ha az nem létezik.         |
|Rendelkezésre állási csoport     |Ha a másodlagos régióban található virtuális gép egy rendelkezésre állási csoport része, kiválaszthatja a célként megadott virtuális gép rendelkezésre állási csoportját az elsődleges régióban. Alapértelmezés szerint a Site Recovery megpróbálja megkeresni a meglévő rendelkezésre állási készletet az elsődleges régióban, és használja azt. A Testreszabás során megadhat egy új rendelkezésre állási készletet.         |


### <a name="what-happens-during-reprotection"></a>Mi történik az ismételt védelem során?

Alapértelmezés szerint a következők történnek:

1. A rendszer létrehoz egy gyorsítótárbeli Storage-fiókot abban a régióban, ahol a feladatátvételen átesett virtuális gép fut.
2. Ha a célként megadott Storage-fiók (az elsődleges régióban található eredeti Storage-fiók) nem létezik, a rendszer létrehoz egy újat. A hozzárendelt Storage-fiók neve a másodlagos virtuális gép által használt Storage-fiók neve, az "ASR" utótaggal.
3. Ha a virtuális gép felügyelt lemezeket használ, a replika felügyelt lemezeket az elsődleges régióban hozza létre a rendszer a másodlagos virtuális gép lemezéről replikált adatok tárolásához.
4. Ha a cél rendelkezésre állási csoport nem létezik, akkor a rendszer egy újat hoz létre az újravédelemi feladatok részeként, ha szükséges. Ha testreszabta az ismételt védelmi beállításokat, a rendszer a kiválasztott készletet használja.

Ha aktivál egy ismételt védelmi feladatot, és a cél virtuális gép létezik, a következők történnek:

1. A cél oldali virtuális gép ki van kapcsolva, ha fut.
2. Ha a virtuális gép felügyelt lemezeket használ, az eredeti lemezek másolata "-ASRReplica" utótaggal jön létre. Az eredeti lemezek törlődnek. A rendszer a "-ASRReplica" másolatokat használja a replikáláshoz.
3. Ha a virtuális gép nem felügyelt lemezeket használ, a célként megadott virtuális gép adatlemezei le vannak választva, és a replikációhoz használják azokat. A rendszer létrehozza és csatolja az operációsrendszer-lemez másolatát a virtuális gépen. Az eredeti operációsrendszer-lemez le van választva, és a replikációhoz használatos.
4. A rendszer csak a forrásoldali lemez és a céllemez közötti változásokat szinkronizálja. A különbözeteket a lemezek összevetésével számítjuk ki, majd átvittük. Az alábbi becsült idő ellenőrzésének megkereséséhez.
5. A szinkronizálás befejeződése után a különbözeti replikáció megkezdődik, és egy helyreállítási pontot hoz létre a replikációs házirenddel összhangban.

Ha aktivál egy ismételt védelmi feladatot, és a cél virtuális gép és a lemezek nem léteznek, a következők történnek:
1. Ha a virtuális gép felügyelt lemezeket használ, a replika lemezek a "-ASRReplica" utótaggal jönnek létre. A rendszer a "-ASRReplica" másolatokat használja a replikáláshoz.
2. Ha a virtuális gép nem felügyelt lemezeket használ, a replika lemezeket a cél Storage-fiókban hozza létre a rendszer.
3. A teljes lemezeket a rendszer átmásolja a feladatátvételi régióból az új célként megadott régióba.
4. A szinkronizálás befejeződése után a különbözeti replikáció megkezdődik, és egy helyreállítási pontot hoz létre a replikációs házirenddel összhangban.

#### <a name="estimated-time--to-do-the-reprotection"></a>Az ismételt védelem várható ideje 

A legtöbb esetben a Azure Site Recovery nem replikálja a teljes adatforrást a forrásoldali régióba. Az alábbi feltételek határozzák meg az adatreplikációk mennyiségét:

1.  Ha a forrásként szolgáló virtuális gép által tárolt összes fájl törölve lett, sérült vagy nem érhető el valamilyen ok miatt, például az erőforráscsoport módosítása/törlése után, akkor az ismételt védelem során a teljes integrációs modul nem érhető el.
2.  Ha a forrásként szolgáló virtuális gép elérhetővé válik, akkor a rendszer csak a különbségeket számítja ki a lemezek és a továbbított adatok összehasonlításával. A becsült idő beszerzéséhez tekintse meg az alábbi táblázatot 

|\* * Példa a helyzetre * * | \* * Az ismételt védelemhez szükséges idő * * |
|--- | --- |
|A forrástartomány 1 TB-os standard lemezzel rendelkezik<br/>– Csak 127 GB-nyi adat használatos, és a lemez többi része üres<br/>– A lemez típusa standard, 60 MiB/S átviteli sebességgel<br/>– A feladatátvételt követően nem módosult az adatváltozás| Hozzávetőleges idő 45 perc – 1,5 óra<br/> – Az ismételt védelem során Site Recovery feltölti a teljes adat ellenőrzőösszegét, amely 127 GB/45 MBs ~ 45 percet vesz igénybe<br/>– Az automatikus méretezés 20-30 percnél hosszabb időre van szükség a Site Recovery számára<br/>-Nincsenek kimenő költségek |
|A forrástartomány 1 TB-os standard lemezzel rendelkezik<br/>– Csak 127 GB-nyi adat használatos, és a lemez többi része üres<br/>– A lemez típusa standard, 60 MiB/S átviteli sebességgel<br/>-45 GB adatváltozás a feladatátvétel után| Hozzávetőleges idő 1 óra – 2 óra<br/>– Az ismételt védelem során Site Recovery feltölti a teljes adat ellenőrzőösszegét, amely 127 GB/45 MBs ~ 45 percet vesz igénybe<br/>– Átviteli idő a 45 GB-os módosítások alkalmazásához, amely 45 GB/45 MBps ~ 17 perc<br/>-A kimenő forgalomra vonatkozó díjak csak az ellenőrzőösszeghez nem tartozó 45 GB-os adatokra vonatkoznak|
 



## <a name="next-steps"></a>Következő lépések

A virtuális gép védelme után kezdeményezheti a feladatátvételt. A feladatátvétel leállítja a virtuális gépet a másodlagos régióban, és a virtuális gépet az elsődleges régióban hozza létre és indítja el, kis állásidővel. Javasoljuk, hogy adjon meg egy időpontot, és futtasson feladatátvételi tesztet, mielőtt teljes feladatátvételt kezdeményez az elsődleges helyre. [További](site-recovery-failover.md) információ a feladatátvételről.
