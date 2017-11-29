---
title: "Védelem-Újrabeállítási módjáról a átadja a feladatokat az Azure virtuális gépek vissza az Azure-régiót |} Microsoft Docs"
description: "Az Azure-régió, egy másik virtuális gépek feladatátvétel után a Azure Site Recovery segítségével visszafelé gépek védelmére. A lépéseket a védelem-újrabeállítási újra a feladatátvétel előtt módjáról további információt."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: rajanaki
ms.openlocfilehash: 3e614b6c3c8358585f3b502f301cc659d2088e2f
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="reprotect-from-failed-over-azure-region-back-to-primary-region"></a>A védelem-újrabeállítási átadja a feladatokat az Azure-régió vissza az elsődleges régió



>[!NOTE]
>
> Az Azure virtuális gépek helyreállítási helyreplikálásának jelenleg előzetes verzió.


## <a name="overview"></a>Áttekintés
Ha Ön [feladatátvételi](site-recovery-failover.md) a virtuális gépek Azure-régió, egy másik, a virtuális gépek egy nem védett állapotban van. Ha azt szeretné, hogy azokat vissza az elsődleges régióban, meg kell először a virtuális gépeket, és a feladatátvételi be újra a védelmét. Hogyan között nincs különbség egy irányt vagy egyéb feladatátvételi. Ehhez hasonlóan az engedélyezése a virtuális gép védelmét, post, a védelem-újrabeállítási post feladatátvétel, vagy a feladás egy vagy több feladat-visszavétel között nincs különbség.
Ismertetik a munkafolyamatok a védelem-újrabeállítási, és így nem keverhetők össze, és hivatkozik, az elsődleges hely a védett gépek, Kelet-Ázsia régió, Délkelet-Ázsia régió gépet a helyreállítási helyen. A feladatátvétel során a virtuális gépek Délkelet-Ázsia régióban fog elindulni. Hogy a feladat-visszavétel előtt kell állítsa vissza a Kelet-Ázsia a virtuális gépek a Délkelet-Ázsia. Ez a cikk lássa el újból védelemmel történő lépéseit ismerteti.

> [!WARNING]
> Ha rendelkezik [áttelepítésével kész](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), a virtuális gép áthelyezése a másik erőforráscsoportba vagy törölni az Azure virtuális gép nem állítsa vagy a feladat-visszavételt a virtuális gép.

Miután a védelem-újrabeállítási befejeződik, és a védett virtuális gépeket replikál, vetheti vissza a Kelet-Ázsia régió virtuális gépeken a feladatátvételi is kezdeményezhető.

Megjegyzéseit vagy kérdéseit a cikk vagy a végén utáni a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Előfeltételek
1. A virtuális gépek kell véglegesítve lettek.
2. A célhely - ebben az esetben a Kelet-Ázsia Azure azon régióját elérhetőnek kell lennie, és hozzáférést/létrehozni az új erőforrásokat az adott régióban képesnek kell lennie.

## <a name="steps-to-reprotect"></a>Lássa el újból védelemmel lépései

Az alábbiakban lássa el újból védelemmel az alapértelmezett érték a virtuális gépek lépéseit.

1. A **tároló** > **replikált elemek**, kattintson a jobb gombbal a virtuális gépet, a feladatátvétel megtörtént, és válassza **védelmének újbóli beállításához**. Is kattintson a gépre, és válassza ki **védelmének újbóli beállításához** a parancs gombok közül.

![A jobb gombbal kattintva lássa el újból védelemmel](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. A panelen, figyelje meg, hogy a védelem irányát **Délkelet-Ázsiában, Kelet-Ázsia való**, már be van jelölve.

![Lássa el újból védelemmel panel](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotectblade.png)

3. Tekintse át a **erőforráscsoport, hálózati, tárolási és rendelkezésre állási készletek** információkat, és kattintson az OK gombra. Ha bármely (új) megjelölt erőforrások, akkor a védelem-újrabeállítási részeként létrejön.

A védelem-újrabeállítási feladat első rendezendő a célhelyre (ebben az esetben SZE) a legújabb adatokkal, és miután ez befejeződött, replikálja az eltérések, feladatátvétel előtt vissza Délkelet-Ázsia.

### <a name="reprotect-customization"></a>Lássa el újból védelemmel testreszabása
Ha meg kívánja a kivonatot tárfiók vagy a hálózati védelem-újrabeállítási során, a védelem-újrabeállítási panelen megadott testreszabás beállítás használatával végezhet.

![A beállítás testreszabása](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

Testre szabhatja, hogy a következő tulajdonságok he cél virtuális gép védelem-újrabeállítási során.

![Testre szabhatja a panelen](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Tulajdonság |Megjegyzések  |
|---------|---------|
|Cél-erőforráscsoport     | Ha szeretné, módosítsa a célként megadott erőforráscsoportja th virtuális géphez létre. Védelem-újrabeállítási részeként a cél virtuális gép törlődik, ezért választhat egy új erőforráscsoportot, amelyek alapján is létrehozhat a feladás egy vagy több virtuális gép feladatátvétele         |
|Virtuális hálózati cél     | Hálózati nem módosítható a védelem-újrabeállítási során jb. Ha módosítani szeretné a hálózathoz, végezze el újra a hálózat leképezését.         |
|Célként megadott     | A storage-fiók, amelyhez a virtuális gép létrejön post feladatátvételi módosíthatja.         |
|Gyorsítótár-tároló     | A replikáció során használt gyorsítótár tárfiók is megadhat. Ha az alapértelmezett értékekkel, a gyorsítótár új tárfiók létrehozza, ha még nem létezik.         |
|Rendelkezésre állási csoport     |Kelet-Ázsia a virtuális gép rendelkezésre állási csoport része, ha választhat a rendelkezésre állási készlet Délkelet-Ázsiában található a cél virtuális gépen. Alapértelmezett megtalálja a meglévő tenger rendelkezésre állási csoport, és próbálja meg használni. Testreszabás adjon meg egy teljesen új AV-készlet.         |


### <a name="what-happens-during-reprotect"></a>Mi történik a védelem-újrabeállítási során?

Fentiekhez hasonló után az első engedélyezni a védelmet, az alábbiakban az összetevőket, get hozott létre, ha az alapértelmezett beállításokat használja.
1. Kelet-Ázsia régióban a gyorsítótár storage-fiók jön létre.
2. Ha nem létezik a céloldali tárfiók (az eredeti tárfiók a Délkelet-Ázsia virtuális gép), egy új jön létre. A név nem a "automatikus" utótaggal a Kelet-Ázsia virtuális gép tárfiók.
3. Ha a cél AV-készlet nem létezik, és az alapértelmezett beállításokat észleli, hogy hozzon létre egy új AV-készlet, akkor a rendszer automatikusan létrehozza a védelem-újrabeállítási feladat részeként. Ha a védelem-újrabeállítási testreszabott, majd a kijelölt AV-készlet lesz.
4.

Az alábbiakban a lépéseket, amelyek fordulhat elő, amikor a védelem-újrabeállítási feladatot indít listáját. Ez az abban az esetben a cél ügyféloldali virtuális gép is létezik.

1. A szükséges összetevők védelem-újrabeállítási részeként jönnek létre. Ha már léteznek, majd azokat újra felhasználja a rendszer.
2. A cél ügyféloldali (Délkelet-Ázsiában) virtuális gép először ki van kapcsolva, ha fut-e.
3. A cél ügyféloldali virtuális gép lemezét másolja a program Azure Site Recovery által egy tárolóba kezdőérték blob.
4. A cél ügyféloldali virtuális gép majd törlődik.
5. Az aktuális adatforrás ügyféloldali (Kelet-Ázsia) virtuális gép a kezdőérték blob replikálásához használt. Ez biztosítja, hogy csak eltérések replikálódnak.
6. A forrás lemez és a kezdőérték blob között jelentős változásokat szinkronizálja a rendszer. Ez eltarthat egy ideig.
7. Miután a védelem-újrabeállítási feladat befejeződött, a változások replikálása, amely létrehoz egy helyreállítási pontot a házirend szerint kezdődik.

> [!NOTE]
> A helyreállítási terv szinten nem védhetők. Akkor is csak lássa el újból védelemmel a virtuális gép szintenként.

Miután a védelem-újrabeállítási feladat sikeres, a virtuális gép módba lép, a védett állapotban.

## <a name="next-steps"></a>Következő lépések

Miután a virtuális gép védett állapotba került, feladatátvevő is kezdeményezhető. A feladatátvételi állítsa le a virtuális gépet, Kelet-Ázsia Azure régióban és majd létrehoz, és a Délkelet-Ázsia régió virtuális gép. Ezért van egy kis állásidő az alkalmazáshoz. A feladatátvételi idő Igen, akkor válassza, ha az alkalmazás működését egy állásidő. Először győződjön meg arról, hogy az hamarosan megfelelően, a feladatátvétel kezdeményezése előtt a virtuális gép feladatátvételi teszt ajánlott.

-   [Lépések végrehajtásával elindíthatja a virtuális gép feladatátvételi teszt](site-recovery-test-failover-to-azure.md)

-   [Lépések végrehajtásával elindíthatja a virtuális gép](site-recovery-failover.md)
