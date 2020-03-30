---
title: Adatfolyam-hibakeresési mód leképezése
description: Interaktív hibakeresési munkamenet indítása adatfolyamok létrehozásakor
ms.author: makromer
author: kromerm
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/06/2019
ms.openlocfilehash: 18848695327a374f12fbe5a34d03366b050d8b65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74928363"
---
# <a name="mapping-data-flow-debug-mode"></a>Adatfolyam-hibakeresési mód leképezése

## <a name="overview"></a>Áttekintés

Az Azure Data Factory leképezése adatfolyam hibakeresési mód lehetővé teszi, hogy interaktívan nézni az adatalakzat átalakítása, miközben az adatfolyamok létrehozása és hibakeresése. A hibakeresési munkamenet használható mind az adatfolyam-tervezési munkamenetekben, mind az adatfolyamok folyamathiba-hibakeresése során. A hibakeresési mód bekapcsolásához használja az "Adatáramlás-hibakeresési" gombot a tervezési felület tetején.

![Hibakeresési csúszka](media/data-flow/debugbutton.png "Hibakeresési csúszka")

Miután bekapcsolta a csúszkát, a rendszer megkérdezi, hogy melyik integrációs futásidejű konfigurációt szeretné használni. Ha az AutoResolveIntegrationRuntime van kiválasztva, egy fürt nyolc mag általános számítási 60 perces élő idő lesz fonva fel. Az adatfolyam-integráció futásidejéről az [Adatfolyam-teljesítmény](concepts-data-flow-performance.md#increasing-compute-size-in-azure-integration-runtime)című témakörben talál további információt.

![Hibakeresési infravörös kijelölés](media/data-flow/debugbutton2.png "Hibakeresési infravörös kijelölés")

Ha a Debug mód be van kapcsolva, interaktív módon hozhatja létre az adatfolyamot egy aktív Spark-fürttel. A munkamenet lezárul, amint kikapcsolja a hibakeresést az Azure Data Factoryban. Tisztában kell lennie az Azure Databricks által a hibakeresési munkamenet bekapcsolása során felmerülő óránkénti díjakkal.

A legtöbb esetben célszerű az adatfolyamokat hibakeresési módban felépíteni, hogy ellenőrizhesse az üzleti logikáját, és megtekintheti az adatátalakításokat, mielőtt közzétenné munkáját az Azure Data Factoryban. A folyamatpanel "Debug" gombjával tesztelheti az adatfolyamot egy folyamatban.

## <a name="cluster-status"></a>Fürt állapota

A tervezési felület tetején lévő fürtállapot-jelző zöldre vált, amikor a fürt készen áll a hibakeresésre. Ha a fürt már meleg, akkor a zöld jelző szinte azonnal megjelenik. Ha a fürt még nem futott, amikor hibakeresési módba lépett, akkor 5–7 percet kell várnia a fürt felpörgetésére. A kijelző addig forog, amíg el nem készül.

Ha befejezte a hibakeresést, kapcsolja ki a hibakeresési kikapcsolást, hogy az Azure Databricks-fürt leállíthassa, és a továbbiakban nem kell fizetnie a hibakeresési tevékenységért.

## <a name="debug-settings"></a>Hibakeresési beállítások

A hibakeresési beállítások az Adatfolyam vászon eszköztárán a "Hibakeresési beállítások" gombra kattintva szerkeszthetők. Itt kiválaszthatja az egyes forrásátalakításokhoz használandó sorkorlátot vagy fájlforrást. A beállításban szereplő sorkorlátok csak az aktuális hibakeresési munkamenetre vonatkoznak. Az SQL DW-forráshoz használandó átmeneti csatolt szolgáltatást is kiválaszthatja. 

![Hibakeresési beállítások](media/data-flow/debug-settings.png "Hibakeresési beállítások")

Ha az adatfolyamban vagy annak bármely hivatkozott adatkészletében vannak paraméterek, a **Paraméterek** lap kiválasztásával megadhatja, hogy milyen értékeket használjon a hibakeresés során.

![Hibakeresési beállítások paraméterei](media/data-flow/debug-settings2.png "Hibakeresési beállítások paraméterei")

## <a name="data-preview"></a>Adatelőnézet

Ha be van kapcsolva a hibakeresés, az Adatelőnézet lap világít az alsó panelen. Hibakeresési mód nélkül az Adatfolyam csak az aktuális metaadatokat jeleníti meg az egyes átalakításokon kívül a Vizsgálat lapon. Az adatelőnézet csak azt kérdezi le, hogy hány sort állított be korlátként a hibakeresési beállításokban. Az adatelőnézet beolvasásához kattintson a **Frissítés** gombra.

![Adatok előnézete](media/data-flow/datapreview.png "Adatelőnézet")

> [!NOTE]
> A fájlforrások csak a megjelenő sorokat korlátozzák, az olvasott sorokat nem. Nagyon nagy adatkészletek esetén ajánlott a fájl egy kis részét használni a teszteléshez. A Hibakeresési beállítások ban kijelölhet egy ideiglenes fájlt minden olyan forráshoz, amely fájladatkészlet-típus.

Ha az adatfolyamban hibakeresési módban fut, az adatok nem lesznek a fogadó átalakításába írva. A Debug munkamenet célja, hogy az átalakítások teszthámjaként szolgáljon. A hibakeresés során nincs szükség fogadókra, és a rendszer figyelmen kívül hagyja az adatfolyamot. Ha szeretné tesztelni az adatok írása a fogadóban, hajtsa végre az adatfolyamot egy Azure Data Factory Pipeline és használja a debug végrehajtás egy folyamatból.

### <a name="testing-join-conditions"></a>A csatlakozási feltételek tesztelése

Az egyesítések, a létezésűek vagy a kereseredmények során győződjön meg arról, hogy a teszthez ismert adatok kis készletét használja. A fenti Hibakeresési beállítások beállítással ideiglenes fájlt állíthat be a teszteléshez. Erre azért van szükség, mert amikor egy nagy adatkészlet sorait korlátozza vagy mintavételezi, nem tudja előre jelezni, hogy mely sorok és mely kulcsok kerülnek be a folyamatba tesztelésre. Az eredmény nem determinisztikus, ami azt jelenti, hogy az illesztési feltételek sikertelenek lehetnek.

### <a name="quick-actions"></a>Gyorsműveletek

Miután megjelenik az adatok előnézete, gyors átalakítást hozhat létre a typecast, eltávolítás vagy módosítás elvégzéséhez egy oszlopon. Kattintson az oszlopfejlécre, majd válasszon az adatelőnézeti eszköztár egyik beállításához.

![Gyorsműveletek](media/data-flow/quick-actions1.png "Gyorsműveletek")

A módosítás kiválasztása után az adatelőnézet azonnal frissül. Új átalakítás létrehozásához kattintson a jobb felső sarokban a **Megerősítés** gombra.

![Gyorsműveletek](media/data-flow/quick-actions2.png "Gyorsműveletek")

**A Typecast** és **a Modify** származtatott oszlop-átalakítást generál, **az Eltávolítás** pedig Select transzformációt.

![Gyorsműveletek](media/data-flow/quick-actions3.png "Gyorsműveletek")

> [!NOTE]
> Ha módosítja az adatfolyamot, a gyors átalakítás hozzáadása előtt újra be kell olvasnia az adatelőnézetet.

### <a name="data-profiling"></a>Adatprofilkészítés

Ha kijelöl egy oszlopot az adatelőnézeti lapon, és az adatelőnézeti **eszköztáron** a Statisztika gombra kattint, megjelenik egy diagram az adatrács jobb szélén, amely részletes statisztikákat tartalmaz az egyes mezőkről. Az Azure Data Factory a diagram megjelenítési típusának adatmintavétele alapján határozza meg a meghatározást. A nagy számosságú mezők alapértelmezés szerint NULL/NOT NULL diagramok lesznek, míg az alacsony számosságú kategorikus és numerikus adatok az adatérték gyakoriságát mutató sávdiagramokat jelenítik meg. A karakterláncmezők maximális/len hossza, a numerikus mezők ben lévő min/max értékek, a szabványos fejlesztési, percentiles, a darabszámok és az átlag is megjelenik.

![Oszlopstatisztika](media/data-flow/stats.png "Oszlopstatisztika")

## <a name="next-steps"></a>További lépések

* Miután befejezte az adatfolyam létrehozásának és hibakeresésének, [hajtsa végre egy folyamatból.](control-flow-execute-data-flow-activity.md)
* Ha a folyamatot adatfolyammal teszteli, használja a [Debug futtatási végrehajtási beállítást.](iterative-development-debugging.md)
