---
title: "Védelem-Újrabeállítási módjáról a átadja a feladatokat az Azure virtuális gépek vissza az Azure-régiót |} Microsoft Docs"
description: "Az Azure-régió, egy másik virtuális gépek feladatátvétel után a Azure Site Recovery segítségével visszafelé gépek védelmére. A lépéseket a védelem-újrabeállítási újra a feladatátvétel előtt módjáról további információt."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: ruturajd
ms.openlocfilehash: 0004323fee44c1433cdd3c39fc1fa7dad965dbf7
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="reprotect-azure-vms-back-to-the-primary-region"></a>Azure virtuális gépek állítsa vissza az elsődleges régió



>[!NOTE]
>
> Az Azure virtuális gépek (VM) helyreállítási helyreplikálásának jelenleg előzetes verzió.


Ha Ön [feladatátvételt](../site-recovery-failover.md) virtuális gépeket az Azure-régió, egy másik, a feladatait átadó virtuális gépek vannak egy nem védett állapotban. Ha azt szeretné, hogy azokat vissza az elsődleges régióban, meg kell először indítsa el a virtuális gépek replikálása, és ezután átveheti újra. Nincs különbség egy irányt vagy egyéb feladatátvételi. Hasonlóképpen Miután engedélyezte a Virtuálisgép-replikációt, nincs ismételt védelem feladatátvételt követően, vagy a post-feladat-visszavétel közötti különbség.

Annak magyarázata ismételt védelem folyamat feltételezzük, hogy az elsődleges hely a védett virtuális gépek Kelet-Ázsia régió, valamint a helyreállítási helyen, Délkelet-Ázsia példaként. A feladatátvétel során a rendszer átadja a virtuális gépeket, Délkelet-Ázsia régió. Ahhoz, hogy feladat-visszavételt, kell replikálni a virtuális gépek a Délkelet-Ázsia, Kelet-Ázsia. Ez a cikk ismételt védelemmel ellátni azt a szükséges lépéseket ismerteti.

> [!WARNING]
> Ha áttelepítés befejeződött, és a virtuális gép áthelyezése a másik erőforráscsoportba (vagy törölték a virtuális gépet az Azure) nem utasíthat el vissza.

Miután ismételt védelem befejeződik, és a virtuális gépeket replikál, a virtuális gépeken, hogy azokat vissza a Kelet-Ázsia régió feladatátvevő is kezdeményezhető.

## <a name="prerequisites"></a>Előfeltételek
1. A virtuális gép kell véglegesítve lettek.
2. A célhelyre (Kelet-Ázsia) elérhetőnek kell lennie, és Ön hozzáférést/létrehozni az új erőforrásokat az adott régióban képesnek kell lennie.

## <a name="reprotect"></a>Lássa el újból védelemmel

A lépések végrehajtásával adja meg újra az alapértelmezett beállításokkal virtuális gép védelmét.

1. A **tároló** > **replikált elemek**, kattintson a jobb gombbal a virtuális Gépet, amely a feladatátvételt, és válassza **védelmének újbóli beállításához**. Is kattintson a gépre, és válassza ki **védelmének újbóli beállításához** a parancs gombok közül.

  ![Ismételt védelem](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Ellenőrizze, hogy a replikálás irányát **Délkelet-Ázsiában, Kelet-Ázsia való**, van kiválasztva.

  ![Lássa el újból védelemmel](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotectblade.png)

3. Tekintse át a **erőforráscsoport, hálózati, tárolási és rendelkezésre állási készletek** információkat, majd kattintson **OK**. Ha bármely (új) megjelölt erőforrások, azok ismételt védelem során jönnek létre.

Ez elindítja a magok a célhelyhez, a legújabb adatokkal, és miután ez befejeződött, replikálja az eltérések előtt nem sikerül vissza Délkelet-Ázsiában ismételt védelem feladatot.

### <a name="reprotect-customization"></a>Lássa el újból védelemmel testreszabása
Válassza ki azt a kivonatot tárfiók vagy a hálózati védelem-újrabeállítási során szeretné, ha az ismételt védelemmel ellátni azt a lapon található Testreszabás beállítás használatával végezhet.

![A beállítás testreszabása](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

Testre szabhatja, hogy a következő tulajdonságok a cél virtuális gép ismételt védelem alatt.

![Testreszabás](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Tulajdonság |Megjegyzések  |
|---------|---------|
|Cél-erőforráscsoport     | Ha szeretné, módosítsa a célként megadott erőforráscsoportja th virtuális géphez létre. Védelem-újrabeállítási részeként a cél virtuális gép törlődik, ezért választhat egy új erőforráscsoportot, amelyek alapján is létrehozhat a feladás egy vagy több virtuális gép feladatátvétele         |
|Virtuális hálózati cél     | Hálózati ismételt védelem alatt nem lehet megváltoztatni. Ha módosítani szeretné a hálózathoz, végezze el újra a hálózat leképezését.         |
|Célként megadott     | A storage-fiók, amelyhez a virtuális gép létrejön post feladatátvételi módosíthatja.         |
|Gyorsítótár-tároló     | A replikáció során használt gyorsítótár tárfiók is megadhat. Ha az alapértelmezett értékekkel, a gyorsítótár új tárfiók létrehozza, ha még nem létezik.         |
|Rendelkezésre állási csoport     |Kelet-Ázsia a virtuális gép rendelkezésre állási csoport része, ha választhat a rendelkezésre állási készlet Délkelet-Ázsiában található a cél virtuális gépen. Alapértelmezett megtalálja a meglévő tenger rendelkezésre állási csoport, és próbálja meg használni. Testreszabás adjon meg egy teljesen új AV-készlet.         |


### <a name="what-happens-during-reprotect"></a>Mi történik a védelem-újrabeállítási során?

Fentiekhez hasonló után az első engedélyezni a védelmet, az alábbiakban az összetevőket, get hozott létre, ha az alapértelmezett beállításokat használja.
1. Kelet-Ázsia régióban a gyorsítótár storage-fiók jön létre.
2. Ha nem létezik a céloldali tárfiók (az eredeti tárfiók a Délkelet-Ázsia virtuális gép), egy új jön létre. A név nem a "automatikus" utótaggal a Kelet-Ázsia virtuális gép tárfiók.
3. Ha a cél AV-készlet nem létezik, és az alapértelmezett beállításokat észleli, hogy hozzon létre egy új AV-készlet, akkor a rendszer automatikusan létrehozza az ismételt védelem feladat részeként. Ha testreszabott ismételt védelemmel ellátni azt, majd a kijelölt AV-készlet történik.


Az alábbiakban a lépéseket, amelyek fordulhat elő, ha az ismételt védelem feladatot indít listáját. Ez az abban az esetben a cél ügyféloldali virtuális gép is létezik.

1. A szükséges összetevők védelem-újrabeállítási részeként jönnek létre. Ha már léteznek, majd azokat újra felhasználja a rendszer.
2. A cél ügyféloldali (Délkelet-Ázsiában) virtuális gép először ki van kapcsolva, ha fut-e.
3. A cél ügyféloldali virtuális gép lemezét másolja a program Azure Site Recovery által egy tárolóba kezdőérték blob.
4. A cél ügyféloldali virtuális gép majd törlődik.
5. Az aktuális adatforrás ügyféloldali (Kelet-Ázsia) virtuális gép a kezdőérték blob replikálásához használt. Ez biztosítja, hogy csak eltérések replikálódnak.
6. A forrás lemez és a kezdőérték blob között jelentős változásokat szinkronizálja a rendszer. Ez eltarthat egy ideig.
7. Ha az ismételt védelem feladat befejeződik, a változások replikálása, amely létrehoz egy helyreállítási pontot a házirend szerint kezdődik.

> [!NOTE]
> A helyreállítási terv szinten nem védhetők. Akkor is csak lássa el újból védelemmel a virtuális gép szintenként.

Miután az ismételt védelem sikeres, a virtuális gép módba lép, egy védett állapotban.

## <a name="next-steps"></a>Következő lépések

Miután a virtuális gép védett állapotba került, feladatátvevő is kezdeményezhető. A feladatátvételi állítsa le a virtuális gépet, Kelet-Ázsia Azure régióban és majd létrehoz, és a Délkelet-Ázsia régió virtuális gép. Ezért van egy kis állásidő az alkalmazáshoz. A feladatátvételi idő Igen, akkor válassza, ha az alkalmazás működését egy állásidő. Javasoljuk, hogy először futtassa a virtuális gép feladatátvételi tesztet, győződjön meg arról, hogy az hamarosan megfelelően, a feladatátvétel kezdeményezése előtt.

-   [Lépések végrehajtásával elindíthatja a virtuális gép feladatátvételi teszt](../site-recovery-test-failover-to-azure.md)

-   [Lépések végrehajtásával elindíthatja a virtuális gép](../site-recovery-failover.md)
