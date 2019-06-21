---
title: Data Flow hibakeresési módban leképezése az Azure Data Factory
description: Egy interaktív hibakeresési munkamenetből, amikor áttérhettem folyamatok elindítása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: d86725718217caf7fd1d9dd6d5d67362e5de7270
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147363"
---
# <a name="mapping-data-flow-debug-mode"></a>Data Flow hibakeresési módban leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Az Azure Data Factory leképezési adatfolyam a hibakeresési mód be kell kapcsolni a tervezőfelületére tetején a "Data Flow Debug" gomb. Amikor adatfolyam-gyűjteményre, a hibakeresési mód bekapcsolásával kialakítása lehetővé teszi, hogy interaktív módon figyelje meg, hogy az adatok formázása átalakító hozhat létre és az adatfolyam-gyűjteményre hibakeresésekor. A hibakeresési munkamenet is használható, mind az adatfolyam-munkamenetekben tervezési, valamint folyamat hibakeresési adatfolyamok végrehajtása során.

![Hibakeresési gomb](media/data-flow/debugbutton.png "hibakeresési gomb")

## <a name="overview"></a>Áttekintés
Ha a hibakeresési módban, a Spark-fürt aktív adatfolyamait, interaktív módon fog létrehozni. A munkamenet bezárul kikapcsolása után hibakeresése az Azure Data Factoryban. A óránkénti díjat az időszakban, a hibakeresési munkamenet engedélyezve van az Azure Databricks felmerült tisztában kell lennie.

A legtöbb esetben tanácsos készítése, az adatok elkezdenek beérkezni hibakeresési módban, hogy az üzleti logika érvényesítéséhez, és a adatátalakítások megtekintése az Azure Data Factoryban a munkahelyi közzététele előtt. A folyamat panelen a "Debug" gomb segítségével belül egy folyamatot az adatok folyamat teszteléséhez.

> [!NOTE]
> Bár a hibakeresési mód világos az adat-előállító eszköztáron zöld, az elszámolás áfatartalma az adatfolyam hibakeresési 8 mag/óra általános számítási 60 perc a time-to-live 

> [!NOTE]
>Az adatfolyam hibakeresési módban történő futtatásakor az adatok nem ír a fogadó átalakítása. A hibakeresési munkamenet alkalmas szolgál egy teszt kihasználhatja az átalakításokat. Fogadóként hibakeresés során nem szükségesek, és a data folyamat figyelmen kívül hagyja. Ha szeretné tesztelni az adatok írása a fogadó a, hajtsa végre az adatfolyam egy Azure Data Factory-folyamatot, és használja a hibakeresési végrehajtása egy folyamatból.

## <a name="debug-settings"></a>Hibakeresési beállításokhoz
Hibakeresési beállításai "Hibakeresési beállításai" gombra kattintva módosíthatók az adatfolyam vászonalapú eszköztáron. Kiválaszthatja a korlátok és/vagy az egyes itt a forrás-átalakítások használata forrásfájlt. A sor korlátokat az ezt a beállítást csak az aktuális hibakeresési munkamenet vonatkoznak. Az átmeneti tárolási társított szolgáltatás használható az SQL DW forrást is kiválaszthatja. 

![Hibakeresési beállításokhoz](media/data-flow/debug-settings.png "hibakeresési beállításokhoz")

## <a name="cluster-status"></a>Fürt állapota
A Tervező felület, amely zöldre, amikor a fürt készen áll a hibakeresési tetején lévő fürt állapotjelző van. Ha a fürt már meleg, majd a zöld jelző jelenik meg szinte azonnal. Ha a fürt nem volt már fut a megadott hibakeresési módban, majd meg kell várnia a fürt üzembe helyezése 5 – 7 perc. A kijelző fog indíthat el, amíg a kész.

Amikor végzett a a hibakeresést, kikapcsolhatja a hibakeresési kapcsolót, hogy az Azure Databricks-fürt leállíthatja, és többé nem kell fizetnie a hibakeresési tevékenység.

## <a name="data-preview"></a>Adatelőnézet
A hibakereséssel az adatok előzetes lapon fog világos felfelé az alsó panel. Hibakeresési módot adatfolyam jeleníti meg, csak az aktuális metaadatokat kicsinyítheti az átalakításokat mindegyike a vizsgálat lapon. Az adatok előzetes csak lekérdezi a korlátot, állított be a hibakeresési beállításokat tartalmazó sorok száma. Kattintson a "Adatlehívást" szükség lehet az adatok előzetes frissítéséhez.

![Adatelőnézet](media/data-flow/datapreview.png "adatelőnézet")

## <a name="data-profiles"></a>Adatok profilok
Válassza ki az egyes oszlopok az adatok előzetes lap jelenik meg a jobb szélső, az adatrácsban az egyes mezők részletes statisztikáit a diagramot. Az Azure Data Factory fogja elérhetővé tenni a meghatározásához, hogy milyen típusú diagram az adat-mintavételezés alapján megjelenítéséhez. Nagy-számosságú mezők alapértelmezett /nem null értékű diagramok kategorikus és numerikus adatok, amelyek alacsony Számosság jelenik meg a sávdiagramok adatok érték gyakoriságának megjelenítése közben. Azt is láthatja a karakterlánc típusú, numerikus mezők, szabványos fejlesztési, percentilisei, számát és átlagos minimális/maximális értékei maximális/len hosszát. 

![Oszlopstatisztika](media/data-flow/stats.png "oszlopstatisztika")

## <a name="next-steps"></a>További lépések

Miután végzett létrehozásához és hibakereséséhez az adatfolyama [hajtsa végre egy folyamat.](control-flow-execute-data-flow-activity.md)

Ha teszteli a folyamatot egy data flow-val, a folyamat használata [hibakeresési Futtatás végrehajtási beállítását.](iterative-development-debugging.md)
