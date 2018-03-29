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
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 984b16dae26fb6d9d33ef68ac3e8c8b658e82e08
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Automatikus méretezése a virtuálisgép-méretezési beállítása az Azure-portálon
A méretezési csoport létrehozásakor megadhatja a futtatni kívánt Virtuálisgép-példányok száma. Az alkalmazás igény szerinti változásával automatikusan növeli vagy csökkenti a Virtuálisgép-példányok számát. Automatikus skálázás teszi lehetővé teszi keresletének tartani, vagy az alkalmazás életciklusa során alkalmazás teljesítmény változásait.

Ez a cikk bemutatja, hogyan automatikus skálázási szabályok létrehozását az Azure portálon, a méretezési csoportban lévő Virtuálisgép-példányok teljesítményének figyeléséhez. Ezek a szabályok automatikus skálázás növelje, vagy csökkentse a metrikák válaszul Virtuálisgép-példányok száma. Ezeket a lépéseket is végre tudja [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) vagy a [Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md).


## <a name="prerequisites"></a>Előfeltételek
Automatikus skálázási szabályok létrehozásához szükséges egy meglévő virtuális gép méretezési készlet. Létrehozhat egy méretezési állítható be a [Azure-portálon](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md), vagy [Azure CLI 2.0](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Hozzon létre egy szabályt, amely automatikusan horizontális felskálázása
Az alkalmazás igény szerinti egyenes arányban növekszik, ha a Virtuálisgép-példány a skála terhelését növeli állítsa be. Ha ez a megnövekedett terhelés egységes, ahelyett, hogy csak egy rövid igény szerinti, konfigurálhatja az automatikus skálázási szabályok a méretezési csoportban lévő Virtuálisgép-példányok számának növeléséhez. Ezek a Virtuálisgép-példányok jönnek létre, és az alkalmazások vannak telepítve, a méretezési hozzákezd terjesztése azokra a terheléselosztó forgalmát. Milyen metrikák figyeléséhez, mint a CPU vagy lemez, mennyi ideig alkalmazásterhelés meg kell felelnie a megadott küszöbértéket, és hány Virtuálisgép-példányok a skála hozzáadása set szabályozhatja.

1. Nyissa meg az Azure portál és select **erőforráscsoportok** a bal oldalon az irányítópult-menüjéből.
2. Válassza ki az erőforráscsoportot, amely tartalmazza a méretezési csoportban, majd kattintson a méretezési készletben erőforrások közül.
3. Válasszon **méretezés** a bal oldalon a skála menüjéből beállítása ablakban. Kattintson a gombra, majd **engedélyezze az automatikus skálázás**:

    ![Az Azure portálon automatikus skálázás engedélyezése](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Írjon be egy nevet a beállítások, például *automatikus skálázás*, majd jelölje be a **szabály hozzáadása**.

5. Hozzon létre egy szabályt, amely növeli a terjedő skálán állítható be, ha a átlagos CPU-terhelést érték nagyobb, mint 70 % 10 perc alatt a Virtuálisgép-példányok számát. A szabály akkor váltja ki, ha Virtuálisgép-példányok száma 20 %-kal növekszik. A méretezési készlet egy Virtuálisgép-példányok kis mennyiségű, akkor állítsa a **művelet** való *számolva növelése* , majd adja meg *1* vagy *2* a a *száma példány*. Virtuálisgép-példányok számos, a 10 % vagy 20 %-os növekedést méretezési készlet Virtuálisgép-példányok lehet jobban megfelelő.

    Adja meg a szabály a következő beállításokat:
    
    | Paraméter              | Magyarázat                                                                                                         | Érték          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Idő összesítése*     | Határozza meg, hogy az összegyűjtött metrikák elemzések céljából összesíti kell-e.                                                | Átlag        |
    | *Metrika neve*          | A teljesítmény metrika figyelésére és a skála alkalmazni az alábbi műveletek beállítani.                                                   | Százalékos processzorhasználat |
    | *Idő felbontása statisztika* | Határozza meg, hogy minden időfelbontási a gyűjtött metrikák elemzések céljából összesíti kell-e.                             | Átlag        |
    | *Operátor*             | Hasonlítsa össze a metrikaadatokat szemben a küszöbérték operátor.                                                     | Nagyobb mint   |
    | *Küszöbérték*            | Az automatikus skálázási szabály elindítani egy műveletet kiváltó százalékos.                                                 | 70             |
    | *Időtartam*             | Az időtartam a metrika és a küszöbértéket az összehasonlítás előtt figyeli.                                   | 10 perc     |
    | *Művelet*            | Meghatározza, hogy a méretezési fel vagy le, ha a szabály vonatkozik, és milyen növekményű kell méretezni.                        | Százalék növelése a következővel: |
    | *A példányok száma*       | Virtuálisgép-példányok hány százalékát módosítani kell, amikor a szabály gondoskodik.                                            | 20             |
    | *Cool le (perc)*  | Mennyi ideig várjon a szabály alkalmazza újra, így az automatikus skálázási műveletek érvénybe lépéséhez idő kell. | 5 perc      |

    A következő példák azt szemléltetik, ezek a beállítások megfelelő Azure-portálon létrehozott egy szabályt:    

    ![Virtuálisgép-példányok számának növeléséhez automatikus skálázási szabály létrehozása](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. A szabály létrehozásához válassza **hozzáadása**


## <a name="create-a-rule-to-automatically-scale-in"></a>Hozzon létre egy szabályt, amely az automatikus méretezése
Egy este vagy hétvégi az alkalmazás igény szerinti csökkenhet. Ha egy meghatározott időtartamra vonatkozóan ez csökkentheti a betöltés egységes, konfigurálhatja az automatikus skálázási szabályok segítségével csökkentheti a méretezési csoportban lévő Virtuálisgép-példányok számát. A skálázási művelet csökkenti a költségeket a méretezési készletben a futtatásakor csak az aktuális igény kielégítésére szükséges példányok futtatásához.

1. Válassza ki a **szabály hozzáadása** újra.
2. Hozzon létre egy szabályt, amely csökken a Virtuálisgép-példány terjedő skálán állítható be, ha a átlagos CPU-terhelést, majd alá süllyed 30 % 10 perc alatt. A szabály akkor váltja ki, ha a Virtuálisgép-példányok száma 20 %-kal csökken.

    Az azonos módszert használja, csakúgy, mint a korábbi szabály. A szabály a következő beállításokat módosíthatja:
    
    | Paraméter              | Magyarázat                                                                                                          | Érték          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operátor*             | Hasonlítsa össze a metrikaadatokat szemben a küszöbérték operátor.                                                      | Kisebb mint   |
    | *Küszöbérték*            | Az automatikus skálázási szabály elindítani egy műveletet kiváltó százalékos.                                                 | 30             |
    | *Művelet*            | Meghatározza, hogy a méretezési fel vagy le, ha a szabály vonatkozik, és milyen növekményű kell méretezni.                         | Százalék csökkentése a következővel: |
    | *A példányok száma*       | Virtuálisgép-példányok hány százalékát módosítani kell, amikor a szabály gondoskodik.                                             | 20             |

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
