---
title: Data Flow hibakeresési módban leképezése az Azure Data Factory
description: Egy interaktív hibakeresési munkamenetből, amikor áttérhettem folyamatok elindítása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 6bde6e74279fc16237c0c0436296e2d21c3a57c7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736617"
---
# <a name="mapping-data-flow-debug-mode"></a>Data Flow hibakeresési módban leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A hibakeresési mód, amely a hibakeresési gomb a felső részén a tervezőfelületére bekapcsolható az Azure Data Factory leképezési adatfolyam rendelkezik. Amikor adatfolyam-gyűjteményre, a hibakeresési mód beállítása a kialakítása lehetővé teszi, hogy interaktív módon figyelje meg, hogy az adatok formázása átalakító hozhat létre és az adatfolyam-gyűjteményre hibakeresésekor.

<img src="media/data-flow/debugbutton.png" width="400">

## <a name="overview"></a>Áttekintés
Ha a hibakeresési módban, és egy futó interaktív Azure Databricks-fürt az adatfolyam interaktív módon fog létrehozni. A munkamenet bezárul kikapcsolása után hibakeresése az Azure Data Factoryban. A óránkénti díjat az időszakban, a hibakeresési munkamenet engedélyezve van az Azure Databricks felmerült tisztában kell lennie.

A legtöbb esetben tanácsos készítése, az adatok elkezdenek beérkezni hibakeresési módban, hogy az üzleti logika érvényesítéséhez, és a adatátalakítások megtekintése az Azure Data Factoryban a munkahelyi közzététele előtt.

## <a name="debug-mode-on"></a>Hibakeresési mód a
Amikor a hibakeresési módot, egy oldalpanel űrlapot, amely kérni fogja, hogy az interaktív Azure Databricks-fürt mutasson, és jelölje be a forrás mintavételi kéri. Az Azure Databricks egy interaktív fürtöt használ, és a forrás átalakítások válasszon mintavételi méret az egyes, vagy válasszon ki egy szövegfájlt a Tesztadatok használandó.

<img src="media/data-flow/upload.png" width="400">

> [!NOTE]
>Az adatfolyam hibakeresési módban történő futtatásakor az adatok nem ír a fogadó átalakítása. A hibakeresési munkamenet célja egy teszt egyikükön > kihasználhatja az átalakításokat. Fogadóként hibakeresés során nem szükségesek, és a data folyamat figyelmen kívül hagyja. Ha az adatok írása tesztelni kívánt > a fogadó hajtsa végre az adatfolyam egy Azure Data Factory-folyamatot, és használja a hibakeresési végrehajtása egy folyamatból.

## <a name="debug-settings"></a>Hibakeresési beállításokhoz
Hibakeresési beállításokhoz az adatfolyam minden forrás az oldal panel fog megjelenni, és "adatforrás beállításaiban" adatfolyam Tervező eszköztáron kiválasztásával is szerkeszthető. Kiválaszthatja a korlátok és/vagy itt a forrás-átalakítást az egyes használata forrásfájlt. Is kiválaszthatja, melyik hibakeresési használni kívánt Databricks-fürt.

## <a name="cluster-status"></a>Fürt állapota
A Tervező felület, amely zöldre, amikor a fürt készen áll a hibakeresési tetején lévő fürt állapotjelző van. Ha a fürt már meleg, majd a zöld jelző jelenik meg szinte azonnal. Ha a fürt nem volt már fut, ha a megadott hibakeresési módban, majd akkor várjon 5 – 7 perc, a fürt üzembe helyezése. A jelzőfény sárga lesz, amíg készen áll. Miután a fürt készen áll a hibakeresési adatfolyam, jelzőfény zöldre.

Ha elkészült a hibakeresés, kapcsolja be a hibakeresési kapcsolja ki, hogy az Azure Databricks-fürt leállíthatja a.

<img src="media/data-flow/datapreview.png" width="400">

## <a name="data-preview"></a>Adatelőnézet
A hibakereséssel az adatok előzetes lapon fog világos felfelé az alsó panel. Hibakeresési módot adatfolyam jeleníti meg, csak az aktuális metaadatokat kicsinyítheti az átalakításokat mindegyike a vizsgálat lapon. Az adatok előzetes csak lekérdezi, hogy beállította a korlátot, az adatforrás-beállítások a sorok számát. Kattintson a "Adatlehívást" szükség lehet az adatok előzetes frissítéséhez.

<img src="media/data-flow/stats.png" width="400">

## <a name="data-profiles"></a>Adatok profilok
Az adatok előzetes lapon kiválasztja egyes oszlopai fog előugró ablak a jobb szélső az adatrácsban az egyes mezők részletes statisztikáit, a diagram. Az Azure Data Factory fogja elérhetővé tenni a meghatározásához, hogy milyen típusú diagram az adat-mintavételezés alapján megjelenítéséhez. Magas – számosságú mezők alapértelmezett NULL / NOT NULL diagramok, amíg a kategorikus és numerikus adatok, amelyek alacsony Számosság adatok érték gyakoriságának megjelenítő sávdiagramok jeleníti meg. Látni fogja emellett maximális / karakterlánc-mezők len hossza minimális / maximális numerikus mezők, szabványos fejlesztési, percentilisei, számát és átlagos értékek. 

<img src="media/data-flow/chart.png" width="400">
