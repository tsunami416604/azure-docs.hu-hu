---
title: Virtuálisgép-méretezési csoportok automatikus skálázása az Azure Portalon
description: Automatikus skálázási szabályok létrehozása a virtuálisgép-méretezési csoportokhoz az Azure Portalon
author: ju-shim
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: jushiman
ms.openlocfilehash: 1915b144aec5a5447504c70d18dbf420d255a08e
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010290"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Virtuálisgép-méretezési csoport automatikus méretezése az Azure Portalon
Méretezési csoport létrehozásakor meghatározza a futtatni kívánt virtuálisgép-példányok számát. Az alkalmazás igényeihez igazodva automatikusan növelheti vagy csökkentheti a virtuálisgép-példányok számát. Az automatikus méretezésnek köszönhetően lépést tarthat az ügyfeleik igényeivel és az alkalmazás teljes élettartama alatt reagálhat az alkalmazás teljesítményében bekövetkezett változásokra.

Ez a cikk bemutatja, hogyan hozhat létre automatikus skálázási szabályokat az Azure Portalon, amely figyeli a virtuálisgép-példányok teljesítményét a méretezési csoportban. Ezek az automatikus skálázási szabályok növelik vagy csökkentik a virtuálisgép-példányok számát ezekre a teljesítménymutatókra válaszul. Ezeket a lépéseket az [Azure PowerShell](tutorial-autoscale-powershell.md) vagy az [Azure CLI](tutorial-autoscale-cli.md)segítségével is végrehajthatja.


## <a name="prerequisites"></a>Előfeltételek
Automatikus skálázási szabályok létrehozásához szükség van egy meglévő virtuálisgép-méretezési csoport. Az [Azure Portalon,](quick-create-portal.md)az [Azure PowerShellben](quick-create-powershell.md)vagy az [Azure CLI-ben](quick-create-cli.md)scale-készletet hozhat létre.


## <a name="create-a-rule-to-automatically-scale-out"></a>Automatikus horizontális felskálázásra szabható szabály létrehozása
Az alkalmazás növekvő igényeivel párhuzamosan a méretezési csoportban lévő virtuálisgép-példányok terhelése is nő. Ha a megnövekedett terhelés állandó, nem csak pillanatnyi igény, akkor megadhatja, hogy az automatikus skálázási szabály növelje meg a virtuálisgép-példányok számát a méretezési csoportban. Ezen virtuálisgép-példányok létrehozását és az alkalmazások telepítését követően a méretezési csoport megkezdi a forgalom elosztását közöttük a terheléselosztón keresztül. Ön határozza meg, hogy milyen metrikákat kíván monitorozni – például a processzort vagy a lemezt, meddig kell az alkalmazás terhelésének elérnie egy megadott küszöbértéket, hány virtuálisgép-példányt kell hozzáadni a méretezési csoporthoz.

1. Nyissa meg az Azure Portalon, és válassza ki az **erőforráscsoportokat** az irányítópult bal oldalán található menüből.
2. Válassza ki a méretezési csoportot tartalmazó erőforráscsoportot, majd válassza ki a méretezési csoportot az erőforrások listájából.
3. Válassza a **méretezési** készlet ablakának bal oldalán található menü Méretezés parancsát. Az automatikus **skálázás engedélyezéséhez**válassza az automatikus skálázás engedélyezéséhez a gombot:

    ![Automatikus skálázás engedélyezése az Azure Portalon](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Adja meg a beállítások nevét, például *az automatikus skálázást,* majd válassza a **szabály hozzáadása**lehetőséget.

5. Hozzon létre egy szabályt, amely növeli a virtuálisgép-példányok számát egy méretezési csoportban, ha az átlagos CPU-terhelés nagyobb, mint 70%, egy 10 perces időszak alatt. Amikor a szabály aktiválódik, a virtuálisgép-példányok száma 20%-kal nő. Kis számú virtuálisgép-példányt azonosító méretezési készletekben beállíthatja, hogy a **művelet** növelje a *számlálót,* majd adjon meg *1* vagy *2* *példányszámot.* Nagyszámú virtuálisgép-példányok méretezési csoportokban 10%, illetve 20%-os virtuálisgép-példányok növekedése megfelelőbb lehet.

    Adja meg a szabály következő beállításait:
    
    | Paraméter              | Magyarázat                                                                                                         | Érték          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Idő összesítése*     | Meghatározza, hogy az összegyűjtött metrikák hogyan legyenek összesítve az elemzéshez.                                                | Átlag        |
    | *Metrikus neve*          | A méretezési csoporthoz tartozó műveletek monitorozásának és alkalmazásának teljesítménymutatója.                                                   | Százalékos processzorhasználat |
    | *Időfelbontási szint statisztikája* | Azt határozza meg, hogy az összegyűjtött mutatókat az egyes alkalommal szemcsémeket elemzésre kell összesíteni.                             | Átlag        |
    | *Művelet*             | A metrikaadatok és a küszöbérték összehasonlításához használt operátor.                                                     | Nagyobb, mint   |
    | *Küszöb*            | Az a százalék, amely az automatikus skálázási szabályt művelet et indít el.                                                 | 70             |
    | *Időtartam*             | A mérőszám és a küszöbértékek összehasonlítása előtt monitorozott időtartam.                                   | 10 perc     |
    | *Művelet*            | Azt határozza meg, hogy a méretezési készlet fel- vagy leskálázása a szabály alkalmazásakor és a mérethez                        | Százalékos növekedés |
    | *Példányok száma*       | Megadja, hogy a virtuálisgép-példányok hány százalékát kell módosítani a szabály aktiválásakor.                                            | 20             |
    | *Lehűlés (perc)*  | Mennyi idő teljen el a szabály újbóli alkalmazása előtt, hogy az automatikus skálázási műveletek kifejthessék a hatásukat. | 5 perc      |

    A következő példák az Azure Portalon létrehozott, az alábbi beállításoknak megfelelő szabályt mutatják be:

    ![Automatikus skálázási szabály létrehozása a virtuálisgép-példányok számának növeléséhez](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. A szabály létrehozásához válassza a **Hozzáadás gombot.**


## <a name="create-a-rule-to-automatically-scale-in"></a>Automatikus méretezéshez hozzon létre egy szabályt
Az este vagy a hétvége folyamán az alkalmazás igényei csökkenhetnek. Ha a csökkent terhelés egy adott időtartam alatt állandó, akkor megadhatja, hogy az automatikus skálázási szabály csökkentse a virtuálisgép-példányok számát a méretezési csoportban. A horizontális leskálázási művelet csökkenti a méretezési csoport futtatásának költségeit, mivel csak az aktuális igényt kielégítő számú példányt futtat.

1. Válassza a **szabály ismételt hozzáadását.**
2. Hozzon létre egy szabályt, amely csökkenti a virtuálisgép-példányok számát egy méretezési csoportban, amikor az átlagos CPU-terhelés, majd egy 10 perces időszak alatt 30% alá csökken. Amikor a szabály aktiválódik, a virtuálisgép-példányok száma 20%-kal csökken.

    Használja ugyanazt a megközelítést, mint az előző szabály. A szabály következő beállításainak módosítása:
    
    | Paraméter              | Magyarázat                                                                                                          | Érték          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Művelet*             | A metrikaadatok és a küszöbérték összehasonlításához használt operátor.                                                      | Kisebb mint   |
    | *Küszöb*            | Az a százalék, amely az automatikus skálázási szabályt művelet et indít el.                                                 | 30             |
    | *Művelet*            | Azt határozza meg, hogy a méretezési készlet fel- vagy leskálázása a szabály alkalmazásakor és a mérethez                         | Százalékcsökkentés |
    | *Példányok száma*       | Megadja, hogy a virtuálisgép-példányok hány százalékát kell módosítani a szabály aktiválásakor.                                             | 20             |

3. A szabály létrehozásához válassza a **Hozzáadás gombot.**


## <a name="define-autoscale-instance-limits"></a>Automatikus skálázási példánykorlátok definiálása
Az automatikus skálázási profilnak meg kell határoznia a virtuálisgép-példányok minimális, maximális és alapértelmezett számát. Az automatikus skálázási szabályok alkalmazásakor ezek a példánykorlátok győződjön meg arról, hogy nem horizontális felskálázás a példányok maximális száma túl, vagy a méretezés túl a minimális példányok.

1. Állítsa be a következő példánykorlátokat:

    | Minimális | Maximum | Alapértelmezett|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Az automatikus skálázási szabályok és a példánykorlátok alkalmazásához válassza a **Mentés gombot.**


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Méretezési csoport példányainak számának figyelése
A virtuálisgép-példányok számát és állapotát a méretezési csoport ablakának bal oldalán található menü **példányok** parancsával láthatja. Az állapot azt jelzi, ha a virtuális gép példány *létrehozása,* mint a méretezési csoport automatikusan horizontális felskálázza, vagy *a törlés,* mint a skála automatikusan skálázódik.

![Méretezési csoport virtuálisgép-példányok listájának megtekintése](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Automatikus méretezés ütemezés alapján
Az előző példák automatikusan skálázták a méretezési készlet, vagy ki az alapvető gazdagép metrikák, például a CPU-használat. Ütemezések alapján automatikus skálázási szabályokat is létrehozhat. Ezek az ütemezésalapú szabályok lehetővé teszik, hogy automatikusan horizontális felskálázása a virtuálisgép-példányok száma előtt várható növekedése az alkalmazás igénye, például az alapvető munkaidő, és automatikusan skálázhatja a példányok száma egy időben, hogy a kereslet várható, például a hétvégén.

1. Válassza a **méretezési** készlet ablakának bal oldalán található menü Méretezés parancsát. Az előző példákban létrehozott meglévő automatikus skálázási szabályok törléséhez válassza a kuka ikont.

    ![A meglévő automatikus skálázási szabályok törlése](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Válassza a **Léptékfeltétel hozzáadását.** Jelölje ki a szabály neve melletti ceruza ikont, és adjon meg egy nevet, például a *Lekicsinylés minden munkanapon.*

    ![Az alapértelmezett automatikus skálázási szabály átnevezése](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Válassza a választógombot **a Méretezés adott példányszámra**.
4. A példányok számának nagymértékű méretezéséhez írja be a *10* példányszámát.
5. Válassza az Ütemezés típus **adott napjainak** **ismétlése** lehetőséget.
6. Válassza ki az összes munkanapot hétfőtől péntekig.
7. Válassza ki a megfelelő időzónát, majd adja meg a *09:00* **kezdési időpontot.**
8. Válassza a **Méretezési feltétel ismételt hozzáadását.** Ismételje meg a folyamatot, hogy hozzon létre egy ütemezésnevű *Méretezés az este folyamán,* hogy a mérleg *3* példányban, ismétlődik minden hétköznap, és kezdődik *18:00*.
9. Az ütemezésalapú automatikus skálázási szabályok alkalmazásához válassza a **Mentés gombot.**

    ![Ütemezés szerinti méretezésre vonatkozó automatikus skálázási szabályok létrehozása](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Az automatikus skálázási szabályok alkalmazásának megtekintéséhez válassza az **Előzmények futtatása** a **Méretezés** ablak tetején lehetőséget. A grafikon és az események listája azt mutatja, ha az automatikus skálázási szabályok eseményindító és a virtuálisgép-példányok száma a méretezési csoportban növekszik vagy csökken.


## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan használhatja az automatikus skálázási szabályok horizontális méretezése és a virtuálisgép-példányok *számának* növelése vagy csökkentése a méretezési csoportban. Függőlegesen is méretezheti a virtuálisgép-példány *méretét.* További információ: [Vertical autoscale with Virtual Machine Scale sets](virtual-machine-scale-sets-vertical-scale-reprovision.md).

A virtuálisgép-példányok kezeléséről a [Virtuálisgép-méretezési csoportok kezelése az Azure PowerShell használatával című](virtual-machine-scale-sets-windows-manage.md)témakörben talál további információt.

Ha meg szeretné tudni, hogyan hozhat létre riasztásokat, amikor az automatikus skálázási szabályok aktiválódnak, olvassa [el az Automatikus skálázási műveletek használata e-mail- és webhook-riasztási értesítések küldéséhez az Azure Monitorban.](../azure-monitor/platform/autoscale-webhook-email.md) A [naplónaplók segítségével e-maileket és webhook-riasztásokat is küldhet az Azure Monitorban.](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
