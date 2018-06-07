---
title: Automatikus skálázási virtuálisgép-méretezési beállítja az Azure portálon |} Microsoft Docs
description: A virtuálisgép-méretezési automatikus skálázási szabályok létrehozása beállítja az Azure-portálon
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: c9386f7dd0ba390a5f089be058c7f3edd6e33cf9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652372"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Automatikus méretezése a virtuálisgép-méretezési beállítása az Azure-portálon
Méretezési csoport létrehozásakor meghatározza a futtatni kívánt virtuálisgép-példányok számát. Az alkalmazás igényeihez igazodva automatikusan növelheti vagy csökkentheti a virtuálisgép-példányok számát. Az automatikus méretezésnek köszönhetően lépést tarthat az ügyfeleik igényeivel és az alkalmazás teljes élettartama alatt reagálhat az alkalmazás teljesítményében bekövetkezett változásokra.

Ez a cikk bemutatja, hogyan automatikus skálázási szabályok létrehozását az Azure portálon, a méretezési csoportban lévő Virtuálisgép-példányok teljesítményének figyeléséhez. Ezek a szabályok automatikus skálázás növelje, vagy csökkentse a metrikák válaszul Virtuálisgép-példányok száma. Ezeket a lépéseket is végre tudja [Azure PowerShell](tutorial-autoscale-powershell.md) vagy a [Azure CLI 2.0](tutorial-autoscale-cli.md).


## <a name="prerequisites"></a>Előfeltételek
Automatikus skálázási szabályok létrehozásához szükséges egy meglévő virtuális gép méretezési készlet. Létrehozhat egy méretezési állítható be a [Azure-portálon](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md), vagy [Azure CLI 2.0](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Hozzon létre egy szabályt, amely automatikusan horizontális felskálázása
Az alkalmazás növekvő igényeivel párhuzamosan a méretezési csoportban lévő virtuálisgép-példányok terhelése is nő. Ha a megnövekedett terhelés állandó, nem csak pillanatnyi igény, akkor megadhatja, hogy az automatikus skálázási szabály növelje meg a virtuálisgép-példányok számát a méretezési csoportban. Ezen virtuálisgép-példányok létrehozását és az alkalmazások telepítését követően a méretezési csoport megkezdi a forgalom elosztását közöttük a terheléselosztón keresztül. Ön határozza meg, hogy milyen metrikákat kíván monitorozni – például a processzort vagy a lemezt, meddig kell az alkalmazás terhelésének elérnie egy megadott küszöbértéket, hány virtuálisgép-példányt kell hozzáadni a méretezési csoporthoz.

1. Nyissa meg az Azure portál és select **erőforráscsoportok** a bal oldalon az irányítópult-menüjéből.
2. Válassza ki az erőforráscsoportot, amely tartalmazza a méretezési csoportban, majd kattintson a méretezési készletben erőforrások közül.
3. Válasszon **méretezés** a bal oldalon a skála menüjéből beállítása ablakban. Kattintson a gombra, majd **engedélyezze az automatikus skálázás**:

    ![Az Azure portálon automatikus skálázás engedélyezése](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Írjon be egy nevet a beállítások, például *automatikus skálázás*, majd jelölje be a **szabály hozzáadása**.

5. Hozzon létre egy szabályt, amely növeli a terjedő skálán állítható be, ha a átlagos CPU-terhelést érték nagyobb, mint 70 % 10 perc alatt a Virtuálisgép-példányok számát. A szabály akkor váltja ki, ha Virtuálisgép-példányok száma 20 %-kal növekszik. A méretezési készlet egy Virtuálisgép-példányok kis mennyiségű, akkor állítsa a **művelet** való *számolva növelése* , majd adja meg *1* vagy *2* a a *száma példány*. Virtuálisgép-példányok számos, a 10 % vagy 20 %-os növekedést méretezési készlet Virtuálisgép-példányok lehet jobban megfelelő.

    Adja meg a szabály a következő beállításokat:
    
    | Paraméter              | Magyarázat                                                                                                         | Érték          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Idő összesítése*     | Meghatározza, hogy az összegyűjtött metrikák hogyan legyenek összesítve az elemzéshez.                                                | Átlag        |
    | *Metrika neve*          | A méretezési csoporthoz tartozó műveletek monitorozásának és alkalmazásának teljesítménymutatója.                                                   | Százalékos processzorhasználat |
    | *Idő felbontása statisztika* | Határozza meg, hogy minden időfelbontási a gyűjtött metrikák elemzések céljából összesíti kell-e.                             | Átlag        |
    | *Operátor*             | A metrikaadatok és a küszöbérték összehasonlításához használt operátor.                                                     | Nagyobb mint   |
    | *Küszöbérték*            | Az automatikus skálázási szabály elindítani egy műveletet kiváltó százalékos.                                                 | 70             |
    | *Időtartam*             | A mérőszám és a küszöbértékek összehasonlítása előtt monitorozott időtartam.                                   | 10 perc     |
    | *Művelet*            | Meghatározza, hogy a méretezési fel vagy le, ha a szabály vonatkozik, és milyen növekményű kell méretezni.                        | Százalék növelése a következővel: |
    | *A példányok száma*       | Megadja, hogy a virtuálisgép-példányok hány százalékát kell módosítani a szabály aktiválásakor.                                            | 20             |
    | *Cool le (perc)*  | Mennyi idő teljen el a szabály újbóli alkalmazása előtt, hogy az automatikus skálázási műveletek kifejthessék a hatásukat. | 5 perc      |

    A következő példák azt szemléltetik, ezek a beállítások megfelelő Azure-portálon létrehozott egy szabályt:

    ![Virtuálisgép-példányok számának növeléséhez automatikus skálázási szabály létrehozása](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. A szabály létrehozásához válassza **hozzáadása**


## <a name="create-a-rule-to-automatically-scale-in"></a>Hozzon létre egy szabályt, amely az automatikus méretezése
Az este vagy a hétvége folyamán az alkalmazás igényei csökkenhetnek. Ha a csökkent terhelés egy adott időtartam alatt állandó, akkor megadhatja, hogy az automatikus skálázási szabály csökkentse a virtuálisgép-példányok számát a méretezési csoportban. A horizontális leskálázási művelet csökkenti a méretezési csoport futtatásának költségeit, mivel csak az aktuális igényt kielégítő számú példányt futtat.

1. Válassza ki a **szabály hozzáadása** újra.
2. Hozzon létre egy szabályt, amely csökken a Virtuálisgép-példány terjedő skálán állítható be, ha a átlagos CPU-terhelést, majd alá süllyed 30 % 10 perc alatt. A szabály akkor váltja ki, ha a Virtuálisgép-példányok száma 20 %-kal csökken.

    Az azonos módszert használja, csakúgy, mint a korábbi szabály. A szabály a következő beállításokat módosíthatja:
    
    | Paraméter              | Magyarázat                                                                                                          | Érték          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operátor*             | A metrikaadatok és a küszöbérték összehasonlításához használt operátor.                                                      | Kisebb mint   |
    | *Küszöbérték*            | Az automatikus skálázási szabály elindítani egy műveletet kiváltó százalékos.                                                 | 30             |
    | *Művelet*            | Meghatározza, hogy a méretezési fel vagy le, ha a szabály vonatkozik, és milyen növekményű kell méretezni.                         | Százalék csökkentése a következővel: |
    | *A példányok száma*       | Megadja, hogy a virtuálisgép-példányok hány százalékát kell módosítani a szabály aktiválásakor.                                             | 20             |

3. A szabály létrehozásához válassza **hozzáadása**


## <a name="define-autoscale-instance-limits"></a>Adja meg az automatikus skálázás példánykorlátozásai
Az automatikus skálázási profil definiálnia kell egy minimum, maximum és Virtuálisgép-példányok alapértelmezett száma. Az automatikus skálázási szabályok vonatkoznak, ha a példány működés felső korlátjának győződjön meg arról, hogy azt ne terjessze ki a példányok, vagy meghaladja a példányok minimális skála maximális száma túl.

1. A következő példány vonatkozó korlátok beállítása:

    | Minimális | Maximum | Alapértelmezett|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Szeretné alkalmazni, az automatikus skálázási szabályok és példánykorlátozásai, **mentése**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Méretezési csoportban lévő példányok száma figyelése
A szám és a Virtuálisgép-példányok állapotának megtekintéséhez válassza ki a **példányok** a bal oldalon a skála menüjéből beállítása ablakban. Az állapot azt jelzi, hogy a Virtuálisgép-példány *létrehozása* a méretezési készletben automatikusan méretezi a kimenő, vagy van *törlése* , az automatikusan méretezi a skála.

![Virtuálisgép-példányok méretezési készlet listájának megtekintése](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Ütemezett automatikus skálázás
Az előző példákban automatikusan átméretezi a bejövő vagy kimenő meg például a CPU-használat alapvető gazdagép metrikák méretezési. Ütemezés alapján automatikus skálázás szabályokat is létrehozhat. Ütemezésalapú szabályok lehetővé teszik automatikusan horizontális felskálázás előre várható növekedése alkalmazás igény szerinti core időpontokat a munkaidőhöz, például Virtuálisgép-példányok száma, és majd automatikus méretezése a példányok száma, amelyek várhatóan kisebb egyszerre igény szerint, például a hétvégi.

1. Válasszon **méretezés** a bal oldalon a skála menüjéből beállítása ablakban. A fenti példákban létrehozott meglévő automatikus skálázás szabályokat törléséhez válassza a Kuka ikonra.

    ![Törölje a meglévő automatikus skálázási szabályok](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Válassza ki a **méretezési feltétel hozzáadása**. Válassza ki a szabály neve mellett a ceruza ikonra, és adjon meg egy nevet, mint *folyamán minden kiterjesztése*.

    ![Az alapértelmezett automatikus skálázási szabály átnevezése](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Válassza ki a választógomb bejelölésével **egy adott példányszám méret**.
4. Vertikális felskálázás-példányok száma, írja be a következőt *10* a példányok száma szerint.
5. Válasszon **ismételje meg az adott napokra** a a **ütemezés** típusa.
6. Válassza ki az összes a munkanapok, hétfőtől péntekig.
7. Válassza ki a megfelelő időzónára, majd adja meg a **kezdési időpont** a *09:00*.
8. Válassza ki a **méretezési feltétel hozzáadása** újra. Ismételje meg a folyamat nevű ütemezés létrehozása *során az este méretezése* , amely alkalmazkodnak *3* példányok ismétlődik minden hétköznap és kezdődik, *18:00*.
9. Az ütemezés alapján automatikus skálázási szabályok jelölje **mentése**.

    ![Ütemezés szerint méretezhető automatikus skálázási szabályok létrehozása](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Az automatikus skálázási szabályok alkalmazása megtekintéséhez válasszon **futtatási előzményei** tetején a **méretezés** ablak. A grafikon és események beállításakor az automatikus skálázási szabályok eseményindító és a skála a Virtuálisgép-példányok száma növekvő vagy csökkenő listában jeleníti meg.


## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan használható az automatikus skálázási szabályok horizontálisan méretezhető és növeléséhez vagy csökkentéséhez a *szám* állítsa be a skálázási Virtuálisgép-példánya. Is méretezheti függőleges növelheti vagy csökkentheti a Virtuálisgép-példány *mérete*. További információkért lásd: [virtuálisgép-méretezési csoportok függőleges skálázva](virtual-machine-scale-sets-vertical-scale-reprovision.md).

A Virtuálisgép-példányok kezeléséről további információért lásd: [kezelése virtuálisgép-méretezési beállítja az Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Az automatikus skálázási szabályok eseményindító riasztást generáljon, lásd: [automatikus skálázási műveletek segítségével e-mailek és a webhook riasztási értesítések küldése az Azure-figyelő](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Emellett [használata naplókat küldeni e-mailek és a webhook riasztási értesítések az Azure-figyelő](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
