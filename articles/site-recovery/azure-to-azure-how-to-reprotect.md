---
title: "Védelem-újrabeállítási átadja a Azure virtuális gépek vissza az elsődleges Azure-régió az Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "Azure virtuális gépek egy másodlagos régióban, egy elsődleges régióban, az Azure Site Recovery segítségével a feladatátvételt követően állítsa ismerteti."
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: rajanaki
ms.openlocfilehash: 47056c85c6cb66a7fa28d623a4472b827d970dab
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Védelem-újrabeállítási átadja Azure virtuális gépeket az elsődleges régióban


>[!NOTE]
>
> Az Azure-beli virtuális gépek Site Recovery-replikációja jelenleg előzetes verzióban érhető el.



Ha Ön [feladatátvételt](site-recovery-failover.md) Azure virtuális gépek egy régióban egy másik használatával [Azure Site Recovery](site-recovery-overview.md), a virtuális gépek rendszerindítás másodlagos régióban, nem védett állapotban. Ha sikertelen a virtuális gépek biztonsági az elsődleges régióban, meg kell tegye a következőket:

- Állítsa a virtuális gépeket, a másodlagos régióban, hogy elindítja az elsődleges régióban replikálni. 
- Miután ismételt védelem befejeződik, és a virtuális gépeket replikál, átveheti azokat a másodlagos kiszolgálóról elsődleges régióban.

> [!WARNING]
> Ha megismerte [át](migrate-overview.md#what-do-we-mean-by-migration) a másodlagos régióban, az elsődleges gépek a virtuális gép áthelyezése a másik erőforráscsoportba, vagy törölték az Azure virtuális Gépen, hogy nem adja meg újra a virtuális gép védelmét, vagy feladat-visszavételt azt.


## <a name="prerequisites"></a>Előfeltételek
1. Másodlagos régióba az elsődleges virtuális gép feladatátvétele véglegesíteni kell lennie.
2. A cél elsődleges helyének elérhetőnek kell lennie, és meg kell megnyitni vagy létrehozni erőforrások az adott régióban.

## <a name="reprotect-a-vm"></a>Állítsa a virtuális gépek

1. A **tároló** > **replikált elemek**, kattintson a jobb gombbal a virtuális gép keresztül, és válassza ki **védelmének újbóli beállításához**. Az ismételt védelem irányba kell jelenjenek meg a másodlagos az elsődleges. 

  ![Lássa el újból védelemmel](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Tekintse át a erőforrás csoport, hálózati, tárolási és rendelkezésre állási készletek. Ezután kattintson az **OK** gombra. Ha vannak megjelölve az új erőforrásokat, létrehozásuk a ismételt védelem folyamat részeként.
3. A feladatátvételen feladat magok a célhelyhez, a legújabb adatokkal. Amely befejezése után a változásreplikálás kerül sor. Ezután átveheti vissza egy elsődleges hely. Kiválaszthatja, hogy a tárfiók, vagy során használni kívánt hálózati lássa el újból védelemmel, a Testreszabás beállítás használatával.

  ![A beállítás testreszabása](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Védelem-újrabeállítási beállítások testreszabása

Testre szabhatja, hogy a következő tulajdonságok a cél VMe ismételt védelem alatt.

![Testreszabás](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Tulajdonság |Megjegyzések  |
|---------|---------|
|Cél-erőforráscsoport     | Módosítsa a célként megadott erőforráscsoportja, amelyben a virtuális gép létrehozása. Ismételt védelem részeként a cél virtuális gép törlődik. Választhat egy új erőforráscsoportot, a feladatátvétel után a virtuális gép létrehozásához.        |
|Virtuális hálózati cél     | A célhálózat nem módosítható a védelem-újrabeállítási feladat során. Ha módosítani szeretné a hálózathoz, végezze el újra a hálózat leképezését.         |
|Célként megadott     | A storage-fiók, amely a virtuális gépről a feladatátvétel után módosíthatja.         |
|Cache Storage     | A replikáció során használandó gyorsítótár tárfiók is megadhat. Alapértelmezés szerint egy új gyorsítótár tárfiók van hozható létre, ha még nem létezik.         |
|Rendelkezésre állási csoport     |Ha a másodlagos régióban a virtuális gép rendelkezésre állási csoport része, választhat a rendelkezésre állási készlet a cél virtuális gép az elsődleges régióban. Alapértelmezés szerint a Site Recovery próbál meg megtalálni a meglévő rendelkezésre állási az elsődleges régióban csoportot, és használja. Testreszabás adjon meg egy új rendelkezésre állási csoportot.         |


### <a name="what-happens-during-reprotection"></a>Mi történik a ismételt védelem során?

Alapértelmezés szerint a következő történik:

1. A gyorsítótár tárfiók létrehozása az elsődleges régióban
2. Ha nem létezik a céloldali tárfiók (az eredeti tárfiók elsődleges régióban), egy új jön létre. A hozzárendelt tárfiókneve a másodlagos virtuális gép, a "automatikus" utótaggal használja a tárfiók nevét.
3. Ha a célként megadott rendelkezésre állási csoport nem létezik, szükség esetén egy új létrejön a védelem-újrabeállítási feladat részeként. Ha testreszabott az ismételt védelem beállításokat, majd a kijelölt használatos.

Amikor a védelem-újrabeállítási feladat, és a cél virtuális gép létezik indít el, az alábbiak történnek:

1. A szükséges összetevők védelem-újrabeállítási részeként jönnek létre. Ha már léteznek, azokat újra felhasználja a rendszer.
2. A virtuális gép kikapcsolt if cél oldalon fut.
3. A cél virtuális gép lemezgyorsítótár másolja a program a Site Recovery által egy tárolóba kezdőérték blob.
4. A cél virtuális gép ügyféloldali majd törlődik.
5. A jelenlegi forrást használja a kezdőérték blob oldalán található (másodlagos) virtuális Gépet replikálni. Ez biztosítja, hogy csak eltérések replikálódnak.
6. A forrás lemez és a kezdőérték blob között jelentős változásokat szinkronizálja a rendszer. Ez eltarthat egy ideig.
7. Miután a védelem-újrabeállítási feladat befejeződik, a változások replikálása kezdődik, és létrehoz egy helyreállítási pontot a replikációs házirend megjelenik.
8. Miután a védelem-újrabeállítási feladat sikeres, a virtuális gép védett állapotba kerül.

## <a name="next-steps"></a>További lépések

Miután a virtuális gép védett, feladatátvétel is kezdeményezhető. A feladatátvételi másodlagos régióban, a virtuális gép leáll, és hoz létre, és virtuális gép elindul az elsődleges régióban, néhány kis állásidővel. Azt javasoljuk, hogy ennek megfelelően válassza ki a időpontot, és a feladatátvételi tesztet futtatni, de egy teljes feladatátvétel az elsődleges helyhez kezdeményezése. [További](site-recovery-failover.md) feladatátvétellel kapcsolatos.

