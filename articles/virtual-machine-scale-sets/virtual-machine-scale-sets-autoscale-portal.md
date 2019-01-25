---
title: Automatikus skálázási virtuálisgép-méretezési csoportok az Azure Portalon |} A Microsoft Docs
description: Az Azure Portalon beállítja a virtuálisgép-méretezési csoport automatikus skálázási szabályok létrehozása
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 648bc0295cd5435e9c3e44f33b7ae80522fa8e0e
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888533"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Az Azure portal virtuális gép méretezési csoport automatikus méretezése
Méretezési csoport létrehozásakor meghatározza a futtatni kívánt virtuálisgép-példányok számát. Az alkalmazás igényeihez igazodva automatikusan növelheti vagy csökkentheti a virtuálisgép-példányok számát. Az automatikus méretezésnek köszönhetően lépést tarthat az ügyfeleik igényeivel és az alkalmazás teljes élettartama alatt reagálhat az alkalmazás teljesítményében bekövetkezett változásokra.

Ez a cikk bemutatja, hogyan automatikus skálázási szabályok létrehozása az Azure Portalon, amely monitorozza a méretezési csoportban lévő Virtuálisgép-példányok teljesítményét. Ezek a szabályok automatikus skálázási növelheti vagy csökkentheti a teljesítménymetrikák Virtuálisgép-példányok számát. Akkor is elvégezhetik ezeket a lépéseket [Azure PowerShell-lel](tutorial-autoscale-powershell.md) vagy a [Azure CLI-vel](tutorial-autoscale-cli.md).


## <a name="prerequisites"></a>Előfeltételek
Az automatikus skálázási szabályok létrehozásához, egy meglévő virtuális gépet kell méretezési csoportot. Létrehozhat egy méretezési csoportot a [az Azure portal](quick-create-portal.md), [Azure PowerShell-lel](quick-create-powershell.md), vagy [Azure CLI-vel](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Automatikus horizontális felskálázási szabály létrehozása
Az alkalmazás növekvő igényeivel párhuzamosan a méretezési csoportban lévő virtuálisgép-példányok terhelése is nő. Ha a megnövekedett terhelés állandó, nem csak pillanatnyi igény, akkor megadhatja, hogy az automatikus skálázási szabály növelje meg a virtuálisgép-példányok számát a méretezési csoportban. Ezen virtuálisgép-példányok létrehozását és az alkalmazások telepítését követően a méretezési csoport megkezdi a forgalom elosztását közöttük a terheléselosztón keresztül. Ön határozza meg, hogy milyen metrikákat kíván monitorozni – például a processzort vagy a lemezt, meddig kell az alkalmazás terhelésének elérnie egy megadott küszöbértéket, hány virtuálisgép-példányt kell hozzáadni a méretezési csoporthoz.

1. Nyissa meg az Azure Portalon, és válassza **erőforráscsoportok** az irányítópult bal oldalán a menüből.
2. Válassza ki az erőforráscsoportot, amely tartalmazza a méretezési csoportot, majd válassza ki a méretezési csoportban az erőforrások listájából.
3. Válasszon **méretezés** időszak megadása a méretezési csoport a bal oldalon a menüben. Válassza ki a gombra kattintva **automatikus skálázás engedélyezése**:

    ![Az Azure Portalon automatikus skálázás engedélyezése](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Adjon meg egy nevet a beállítások, például *automatikus skálázási*, majd válassza a **egy szabály hozzáadásához**.

5. Hozzunk létre egy szabályt, amely növeli a egy méretezési csoportot, ha egy 10 perces időszakban az átlagos processzorterhelés nagyobb, mint a 70 %-os Virtuálisgép-példányok számát. A szabály aktiválásakor a Virtuálisgép-példányok száma 20 %-kal növekszik. Beállítása sikerült Virtuálisgép-példányok kis számú méretezési a **művelet** való *növelése a következővel* és adja meg *1* vagy *2* számára a *példányszám*. A méretezési csoportok és a egy Virtuálisgép-példányok nagy számú, a 10 % vagy 20 %-os növekedést Virtuálisgép-példányok megfelelőbb lehet.

    Adja meg a szabály a következő beállításokat:
    
    | Paraméter              | Magyarázat                                                                                                         | Érték          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Idő összesítése*     | Meghatározza, hogy az összegyűjtött metrikák hogyan legyenek összesítve az elemzéshez.                                                | Átlag        |
    | *Metrika neve*          | A méretezési csoporthoz tartozó műveletek monitorozásának és alkalmazásának teljesítménymutatója.                                                   | Százalékos processzorhasználat |
    | *Időfelbontási szint statisztikája* | Meghatározza, hogy az egyes időfelbontási szint az összegyűjtött metrikák hogyan legyenek összesítve az elemzés.                             | Átlag        |
    | *Operátor*             | A metrikaadatok és a küszöbérték összehasonlításához használt operátor.                                                     | Nagyobb, mint   |
    | *Küszöbérték*            | A százalékos aránya, amelyek hatására az automatikus skálázási szabály aktivál egy műveletet.                                                 | 70             |
    | *Időtartam*             | A mérőszám és a küszöbértékek összehasonlítása előtt monitorozott időtartam.                                   | 10 perc     |
    | *Művelet*            | Meghatározza, hogy a méretezési csoport felfelé vagy lefelé a szabály alkalmazásakor és milyen növekményű                        | Százalék növelése a következővel: |
    | *Példányok száma*       | Megadja, hogy a virtuálisgép-példányok hány százalékát kell módosítani a szabály aktiválásakor.                                            | 20             |
    | *Lehűlés (percben)*  | Mennyi idő teljen el a szabály újbóli alkalmazása előtt, hogy az automatikus skálázási műveletek kifejthessék a hatásukat. | 5 perc      |

    Az alábbi példák bemutatják egy szabályt, amely megfelel a ezeket a beállításokat az Azure Portalon hozza létre:

    ![Hozzon létre egy automatikus skálázási szabály növelje a Virtuálisgép-példányok számát](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. A szabály létrehozásához válassza **hozzáadása**


## <a name="create-a-rule-to-automatically-scale-in"></a>Hozzon létre egy szabályt, amely automatikus skálázása
Az este vagy a hétvége folyamán az alkalmazás igényei csökkenhetnek. Ha a csökkent terhelés egy adott időtartam alatt állandó, akkor megadhatja, hogy az automatikus skálázási szabály csökkentse a virtuálisgép-példányok számát a méretezési csoportban. A horizontális leskálázási művelet csökkenti a méretezési csoport futtatásának költségeit, mivel csak az aktuális igényt kielégítő számú példányt futtat.

1. Válassza ki a **egy szabály hozzáadásához** újra.
2. Hozzon létre egy szabályt, amely egy méretezési csoportot, ha az átlagos processzorterhelés ezután nem éri el 30 %-os egy 10 perces időtartamon belül lévő Virtuálisgép-példányok száma csökken. A szabály aktiválásakor a Virtuálisgép-példányok száma 20 %-kal csökken.

    Használja ugyanazt a megközelítést csakúgy, mint az előző szabályban. A szabály a következő beállításokat módosíthatja:
    
    | Paraméter              | Magyarázat                                                                                                          | Érték          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operátor*             | A metrikaadatok és a küszöbérték összehasonlításához használt operátor.                                                      | Kisebb mint   |
    | *Küszöbérték*            | A százalékos aránya, amelyek hatására az automatikus skálázási szabály aktivál egy műveletet.                                                 | 30             |
    | *Művelet*            | Meghatározza, hogy a méretezési csoport felfelé vagy lefelé a szabály alkalmazásakor és milyen növekményű                         | Százalék csökkentése a következővel: |
    | *Példányok száma*       | Megadja, hogy a virtuálisgép-példányok hány százalékát kell módosítani a szabály aktiválásakor.                                             | 20             |

3. A szabály létrehozásához válassza **hozzáadása**


## <a name="define-autoscale-instance-limits"></a>Példánykorlátok automatikus skálázási definiálása
Az automatikus skálázási profil meg kell határoznia egy minimális, maximális és a Virtuálisgép-példányok alapértelmezett száma. Az automatikus skálázási szabályok alkalmazásakor a ezek példánykorlátok győződjön meg arról, hogy nem horizontális felskálázása példányra, vagy túl a példányok minimális méret maximális száma túl.

1. Állítsa be a következő példánykorlátok:

    | Minimális | Maximum | Alapértelmezett|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Válassza ki a alkalmazni, az automatikus skálázási szabályok és példánykorlátok **mentése**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Egy méretezési csoportban lévő példányok figyelése
A szám és a Virtuálisgép-példányok állapotát, jelölje ki a **példányok** időszak megadása a méretezési csoport a bal oldalon a menüben. Az állapot azt jelzi, hogy a Virtuálisgép-példány *létrehozása* , a méretezési csoport automatikusan elvégzi a horizontális felskálázást, vagy nem *törlése* , a méretezési csoport Automatikus méretezéssel.

![Méretezési csoport Virtuálisgép-példányok listájának megtekintése](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Az automatikus méretezés az ütemezés alapján
Az előző példák automatikusan méretezett méretezési vagy alapvető gazdagépmetrikák, például a CPU-használat. Ütemezés alapján automatikus méretezési szabályokat is létrehozhat. Ezek a szabályok ütemezésalapú automatikusan méretezi a modern alkalmazás igényei szerint, például a munkahelyi üzemóra várható növekedéséhez Virtuálisgép-példányok számát, és automatikusan horizontálisan leskálázzák a példányok számát, amelyek várhatóan kisebb egyszerre engedélyezése igény szerint, például a hétvégi.

1. Válasszon **méretezés** időszak megadása a méretezési csoport a bal oldalon a menüben. A meglévő automatikus skálázási szabályok az előzőekben létrehozott törléséhez válassza a Kuka ikonra.

    ![A meglévő automatikus skálázási szabályok törlése](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Válassza ki a **skálázási feltétel hozzáadása**. Válassza ki a szabály neve melletti ceruza ikonra, és adjon meg egy nevet például *horizontális felskálázás során minden munkanap*.

    ![Az alapértelmezett automatikus skálázási szabály átnevezése](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Válassza ki a választógomb **skálázás adott példányszámra**.
4. Adja meg a példányok számának vertikális felskálázásához *10* , a példányok száma.
5. Válasszon **adott napokon ismétlődik** számára a **ütemezés** típusa.
6. Válassza ki az összes a munkanapok, hétfőtől péntekig.
7. Válassza ki a megfelelő időzónában, majd adjon meg egy **kezdési idő** , *09:00*.
8. Válassza ki a **skálázási feltétel hozzáadása** újra. Ismételje meg a folyamatot nevű ütemezés létrehozása *során esténként skálázása* , amely méretezhető *3* példányok, ismétlődik minden hétköznap, és legalább *18:00*.
9. Válassza ki a alkalmazni az ütemezésalapú automatikus skálázási szabályokat, **mentése**.

    ![Ütemezés szerint méretezhető automatikus skálázási szabályok létrehozása](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Az automatikus skálázási szabályok alkalmazása megtekintéséhez válasszon **futtatási előzmények** felső részén a **méretezés** ablak. A graph és az események listázása mutat be, ha az automatikus skálázási szabályok eseményindító és a méretezési csoportban lévő Virtuálisgép-példányok száma növekedések és csökkenések.


## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan automatikus skálázási szabályok használatával horizontális skálázásra és növelheti vagy csökkentheti a *szám* a méretezési csoportban lévő Virtuálisgép-példányok beállítása. Is skálázhatja vertikálisan növelése vagy csökkentése érdekében a Virtuálisgép-példány *mérete*. További információkért lásd: [vertikális automatikus méretezés a Virtual Machine Scale sets](virtual-machine-scale-sets-vertical-scale-reprovision.md).

A Virtuálisgép-példányok kezeléséről további információért lásd: [kezelése virtuális gép méretezési csoportok az Azure PowerShell-lel](virtual-machine-scale-sets-windows-manage.md).

Riasztást generál, ha az automatikus méretezési szabályok eseményindító kezelésével kapcsolatos információkért lásd: [küldése e-mailt és webhookot riasztási értesítéseket az Azure Monitor automatikus skálázási műveletek használatával](../azure-monitor/platform/autoscale-webhook-email.md). Emellett [használati naplók küldése e-mailt és webhookot riasztási értesítéseket az Azure monitorban](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
