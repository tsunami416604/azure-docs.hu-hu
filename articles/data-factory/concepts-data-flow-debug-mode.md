---
title: Azure Data Factory leképezési adatfolyam hibakeresési módja
description: Interaktív hibakeresési munkamenet elindítása az adatfolyamatok létrehozásakor
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: c65ed14abb86b3b434e46fbe857487b06e217aad
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387375"
---
# <a name="mapping-data-flow-debug-mode"></a>Adatfolyam-hibakeresési mód leképezése



## <a name="overview"></a>Áttekintés

Azure Data Factory a leképezési adatfolyam hibakeresési módja lehetővé teszi, hogy interaktív módon figyelje az adatalakzatok átalakítóját az adatfolyamatok létrehozásakor és hibakeresése során. A hibakeresési munkamenet egyaránt használható az adatfolyam-tervezési munkamenetekben, valamint az adatfolyamatok hibakeresési folyamatának végrehajtása során. A hibakeresési mód bekapcsolásához használja a tervezési felület tetején található "adatfolyam-hibakeresés" gombot.

![Hibakeresési csúszka](media/data-flow/debugbutton.png "Hibakeresési csúszka")

A csúszka bekapcsolását követően meg kell adnia, hogy melyik Integration Runtime-konfigurációt kívánja használni. Ha a AutoResolveIntegrationRuntime van kiválasztva, akkor a rendszer egy, a 60 perces élettartammal rendelkező, nyolc maggal rendelkező fürtöt hoz létre. Az adatfolyam-integrációs modulokkal kapcsolatos további információkért lásd: [adatáramlási teljesítmény](concepts-data-flow-performance.md#increasing-compute-size-in-azure-integration-runtime).

![IR-kijelölés hibakeresése](media/data-flow/debugbutton2.png "IR-kijelölés hibakeresése")

Ha a hibakeresési mód be van kapcsolva, interaktív módon felépítheti az adatfolyamot egy aktív Spark-fürttel. A munkamenet akkor zárul le, ha a Azure Data Factoryban bekapcsolja a hibakeresést. Tisztában kell lennie azzal, hogy a hibakeresési munkamenet bekapcsolásának ideje alatt a Azure Databricks óradíjat számol fel.

A legtöbb esetben célszerű hibakeresési módban felépíteni az adatfolyamatokat, hogy ellenőrizni tudja az üzleti logikát, és megtekintheti az adatátalakításokat, mielőtt közzéteszi a munkáját a Azure Data Factoryban. Az adatfolyamatok folyamaton belüli teszteléséhez használja a folyamat panel "hibakeresés" gombját.

## <a name="cluster-status"></a>Fürt állapota

A fürt állapotjelzője a tervezési felület tetején zöldre vált, amikor a fürt készen áll a hibakeresésre. Ha a fürt már meleg, akkor a zöld kijelző szinte azonnal megjelenik. Ha a fürt nem fut a hibakeresési mód megadásakor, akkor 5-7 percet várnia kell, amíg a fürt fel nem kerül. A kijelző addig forog, amíg készen nem áll.

Ha befejezte a hibakeresést, kapcsolja ki a hibakeresési kapcsolót, hogy a Azure Databricks-fürt leálljon, és a továbbiakban nem lesz számlázva a hibakeresési tevékenységhez.

## <a name="debug-settings"></a>Hibakeresési beállítások

A hibakeresési beállítások szerkesztéséhez kattintson a "hibakeresési beállítások" lehetőségre az adatfolyam-vászon eszköztáron. Itt kiválaszthatja az egyes forrás-átalakításokhoz használandó sor korlátját vagy forrását. Az ebben a beállításban szereplő sorokra vonatkozó korlátok csak az aktuális hibakeresési munkamenet esetében érvényesek. Kiválaszthatja az SQL DW-forráshoz használandó átmeneti társított szolgáltatást is. 

![Hibakeresési beállítások](media/data-flow/debug-settings.png "Hibakeresési beállítások")

Ha az adatfolyamban vagy valamelyik hivatkozott adatkészletben paraméterek vannak megadva, megadhatja, hogy a hibakeresés során milyen értékeket kell használni a **Parameters (paraméterek** ) lapon.

![Hibakeresési beállítások paraméterei](media/data-flow/debug-settings2.png "Hibakeresési beállítások paraméterei")

## <a name="data-preview"></a>Adatelőnézet

Ha a hibakeresés be van kapcsolva, az adatelőnézet lap az alsó panelen jelenik meg. A hibakeresési mód nélkül az adatfolyam csak az egyes átalakítások aktuális metaadatait jeleníti meg a vizsgálat lapon. Az adatelőnézet csak a hibakeresési beállításokban beállított sorok számát kérdezi le. Az adatelőnézet beolvasásához kattintson a **frissítés** gombra.

![Adatelőnézet](media/data-flow/datapreview.png "Adatelőnézet")

> [!NOTE]
> A fájlok forrása csak a megjelenő sorokra korlátozza a beolvasott sorokat. A nagyon nagy adatkészletek esetében ajánlott a fájl egy kis részének elvégzése, és a teszteléshez használni. Egy ideiglenes fájlt is kijelölhet a hibakeresési beállításokban minden olyan forrás esetében, amely egy fájl-adatkészlet típusú.

Ha hibakeresési módban fut az adatfolyamban, az adatai nem lesznek beírva a fogadó átalakítóba. A hibakeresési munkamenetek célja, hogy tesztelési hámként szolgáljon az átalakításokhoz. A rendszer nem igényel mosogatót a hibakeresés során, és figyelmen kívül hagyja az adatfolyamban. Ha szeretné tesztelni a fogadóban lévő adatok írását, hajtsa végre az adatfolyamatot egy Azure Data Factory folyamatból, és használja egy folyamat hibakeresési végrehajtását.

### <a name="testing-join-conditions"></a>Csatlakozási feltételek tesztelése

Ha a mértékegység-tesztelés összekapcsolását, létezését vagy keresési átalakítását végzi, ügyeljen arra, hogy a teszthez egy kis mennyiségű ismert adat legyen használatban. A fenti hibakeresési beállítások lehetőséggel megadhat egy ideiglenes fájlt, amelyet a teszteléshez használhat. Erre azért van szükség, mert egy nagyméretű adatkészletből származó sorok korlátozása vagy mintavételezése esetén nem lehet megjósolni, hogy mely sorok és milyen kulcsok lesznek beolvasva a folyamatba a teszteléshez. Az eredmény nem determinisztikus, ami azt jelenti, hogy az illesztési feltételek sikertelenek lehetnek.

### <a name="quick-actions"></a>Gyors műveletek

Ha az adatelőnézet megjelenik, gyorsan átalakíthatja a typecast, eltávolíthatja vagy végrehajthatja az oszlop módosítását. Kattintson az oszlop fejlécére, majd válassza ki az egyik lehetőséget az adatok előnézetének eszköztárán.

![Gyors műveletek](media/data-flow/quick-actions1.png "Gyors műveletek")

Miután kiválasztott egy módosítást, az adatelőnézet azonnal frissülni fog. Új átalakítás létrehozásához kattintson a jobb felső sarokban található **megerősítés** gombra.

![Gyors műveletek](media/data-flow/quick-actions2.png "Gyors műveletek")

A **Typecast** és a **módosítás** egy származtatott oszlop átalakítását eredményezi, és az **Eltávolítás** egy kiválasztott átalakítást fog eredményezni.

![Gyors műveletek](media/data-flow/quick-actions3.png "Gyors műveletek")

> [!NOTE]
> Ha szerkeszti az adatfolyamatot, a gyors átalakítás hozzáadása előtt újra be kell olvasnia az adatelőnézetet.

### <a name="data-profiling"></a>Adatprofilkészítés

Ha kijelöl egy oszlopot az adatelőnézet lapon, és a **statisztikák** elemre kattint az adatelőnézet eszköztáron, az adatrács jobb oldalán megjelenik egy diagram, amely az egyes mezők részletes statisztikáit jeleníti meg. A Azure Data Factory az adatmintavétel alapján határozza meg, hogy milyen típusú diagramot kell megjeleníteni. A legfelső szintű mezők alapértelmezett értéke NULL/nem NULL értékű, míg a kategorikus és a numerikus adat, amely alacsony fokú, az adatértékek gyakoriságát ábrázoló sáv diagramokat jeleníti meg. A sztring mezők, a minimális/maximális érték numerikus mezőkben, a standard fejlesztés, a percentilis, a Count és az átlag mezőben is látható.

![Oszlop statisztikái](media/data-flow/stats.png "Oszlop statisztikái")

## <a name="next-steps"></a>Következő lépések

* Miután befejezte az adatfolyamok létrehozását és hibakeresését, [hajtsa végre a folyamatot egy folyamatból.](control-flow-execute-data-flow-activity.md)
* Amikor adatfolyamként teszteli a folyamatot, használja a folyamat [hibakeresési futtatásának futtatása beállítást.](iterative-development-debugging.md)
