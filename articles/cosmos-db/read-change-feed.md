---
title: Azure Cosmos DB-változáscsatorna olvasása
description: Ez a cikk különböző lehetőségeket ismertet a Azure Cosmos DBban az olvasási és a hozzáférés-módosítási adatcsatorna számára.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.reviewer: sngun
ms.openlocfilehash: d7408f3b3e955d397ba4a54d07323f80dd72c3d3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83697349"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Azure Cosmos DB-változáscsatorna olvasása

A Azure Cosmos DB változási csatornát leküldéses modell vagy lekéréses modell használatával is használhatja. A leküldéses modellben az ügyfél egy kiszolgálóról kér munkát, és üzleti logikával dolgozza fel a változást. Azonban az utolsó feldolgozott változtatások állapotának ellenőrzésének és tárolásának összetettségét a kiszolgáló kezeli.

A lekéréses modell esetében a kiszolgáló a munkát kéri, és gyakran egy központi munkavárólistából kér rá. Az ügyfél ebben az esetben nem csupán az üzleti logikával rendelkezik a változások feldolgozásához, hanem az utolsó feldolgozott változás állapotának tárolásához is, több ügyfél terheléselosztását párhuzamosan dolgozza fel, és kezeli a hibákat.

A Azure Cosmos DB változási hírcsatornából való olvasáskor általában ajánlott leküldéses modellt használni, mert nem kell aggódnia:

- A módosítási hírcsatorna lekérdezése későbbi változásokhoz.
- Az utolsó feldolgozott módosítás állapotának tárolása. A változási hírcsatornából való olvasáskor a rendszer automatikusan egy [bérlet tárolóban](change-feed-processor.md#components-of-the-change-feed-processor)tárolja.
- Terheléselosztás több ügyfél között, amelyek felhasználják a módosításokat. Ha például egy ügyfél nem tud lépést tartani a feldolgozás változásaival, és egy másik rendelkezik a rendelkezésre álló kapacitással.
- [Hibák kezelésére](change-feed-processor.md#error-handling). Például automatikusan újrapróbálkozhat a sikertelen módosítások, amelyeket a kód vagy átmeneti hálózati hiba miatt nem kezelt kivétel után nem sikerült feldolgozni.

A Azure Cosmos DB változási hírcsatornát használó forgatókönyvek többsége a leküldéses modell beállításainak egyikét fogja használni. Vannak azonban olyan helyzetek, amikor szükség lehet a lekéréses modell további alacsony szintű felügyeletére. Ezek a következők:

- Egy adott partíciós kulcs változásainak olvasása
- Annak szabályozása, hogy az ügyfél milyen tempóban kapja meg a feldolgozás változásait
- A változási hírcsatornában található meglévő információk egyszeri olvasása (például az adatáttelepítés elvégzéséhez)

## <a name="reading-change-feed-with-a-push-model"></a>Változási hírcsatorna olvasása leküldéses modellel

Leküldéses modell használata a legegyszerűbb módszer a változási hírcsatornából való olvasáshoz. A változási csatornát kétféleképpen lehet leküldéses modellel olvasni: [Azure Functions Cosmos db eseményindítók](change-feed-functions.md) és a [hírcsatorna-feldolgozó függvénytár módosítása](change-feed-processor.md). A Azure Functions a háttérben a változási csatornát használja, így ezek nagyon hasonló módon olvashatók be a változási csatornán. Úgy gondolja, hogy Azure Functions egyszerűen egy üzemeltetési platformként a Change feed processzorhoz, nem egészen más módon a változási csatorna olvasásához.

### <a name="azure-functions"></a>Azure Functions

A Azure Functions a legegyszerűbb lehetőség, ha csak most kezdi meg a változási hírcsatorna használatát. Az egyszerűsége miatt az ajánlott lehetőség a legtöbb módosítási hírcsatorna-használati esetre is. Ha Azure Cosmos DBhoz hoz létre Azure Functions-triggert, válassza ki a csatlakoztatni kívánt tárolót, és az Azure-függvény akkor aktiválódik, amikor változás van a tárolóban. Mivel Azure Functions a háttérben a változási csatornát használja, az automatikusan parallelizes a tároló [partícióinak](partition-data.md)feldolgozását.

Az Azure Functions-val való fejlesztés egyszerű, és gyorsabb lehet, mint a módosítási hírcsatorna-feldolgozó üzembe helyezése. Az eseményindítók a Azure Functions portál használatával vagy programozott módon hozhatók létre az SDK-k használatával. A Visual Studio és a VS Code támogatást nyújt a Azure Functions írásához, és az Azure Functions CLI-t is használhatja a platformok közötti fejlesztéshez. A kódot megírhatja és hibakeresést végezhet az asztalon, majd egyetlen kattintással telepítheti a függvényt. További információkért tekintse meg a [kiszolgáló nélküli adatbázis-számítási feladatokat a Azure functions használatával](serverless-computing-database.md) és a [változási hírcsatorna használata Azure functions](change-feed-functions.md) cikkekkel című témakört.

### <a name="change-feed-processor-library"></a>A hírcsatorna-feldolgozó függvénytárának módosítása

A módosítási hírcsatorna processzora nagyobb mértékben szabályozza a változási csatornát, és továbbra is elrejti a legtöbb összetettséget. A változási hírcsatorna-feldolgozó függvénytár a megfigyelő mintát követi, ahol a feldolgozó függvényt a könyvtár hívja meg. A változás a hírcsatorna-feldolgozó kódtár esetében automatikusan megkeresi a módosításokat, és ha a módosítások megtalálhatók, a rendszer leküldi őket az ügyfélnek. Ha nagy átviteli sebességű változási csatornával rendelkezik, több ügyfelet is létrehozhat, hogy beolvassa a változási csatornát. A módosítási hírcsatorna-feldolgozó kódtár automatikusan elosztja a terhelést a különböző ügyfelek között. A bérlet állapotának fenntartása érdekében nem kell semmilyen logikát megvalósítani a terheléselosztáshoz több ügyfélen vagy bármilyen logikán keresztül.

A Change feed Processor Library az összes módosítás "legalább egyszeri" szállítását garantálja. Más szóval, ha az adatmódosítási folyamat függvénytárának módosítása funkciót használja, a Processing függvényt a rendszer a változási csatorna minden eleme esetében sikeresen meghívja. Ha a Processing függvényben nem kezelt kivétel található az üzleti logikában, a rendszer a sikertelen módosításokat újrapróbálkozik, amíg a feldolgozás nem sikerült. Ha meg szeretné akadályozni, hogy a Change feed processzora "beragadt" állapotba lépjen, és ne próbálkozzon ugyanazokkal a változásokkal, adja hozzá a logikát a feldolgozási függvényhez, hogy kivétel esetén a kézbesítetlen levelek várólistára írjon dokumentumokat. További információ a [hibakezelés](change-feed-processor.md#error-handling).

Azure Functions a hibák kezelésére vonatkozó javaslat ugyanaz. Továbbra is hozzá kell adnia a meghatalmazott kódjában lévő logikát, hogy a rendszer kivétel esetén a kézbesítetlen levelek várólistáján jegyezze fel a dokumentumokat. Ha azonban az Azure-függvény nem kezelt kivételt észlel, a rendszer nem fogja automatikusan újrapróbálkozni a kivételt létrehozó módosítással. Ha az üzleti logikában nem kezelt kivétel található, az Azure-függvény a következő módosítás feldolgozására fog lépni. Az Azure-függvény nem próbálkozik újra ugyanazzal a sikertelen módosítással.

A Azure Functionshoz hasonlóan a Change feed Processor Library egyszerűen fejleszthető. Azonban egy vagy több gazdagépet kell telepítenie a Change feed processzorhoz. A gazdagép egy olyan alkalmazás-példány, amely a változási hírcsatorna-feldolgozó használatával figyeli a módosításokat. Habár a Azure Functions rendelkezik az automatikus skálázáshoz szükséges képességekkel, a gazdagépek skálázása felelős. További információkért lásd: [a hírcsatorna-kezelő processzor használata](change-feed-processor.md#dynamic-scaling). Az adatváltozási processzor könyvtára a [Azure Cosmos db SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3)részét képezi.

## <a name="reading-change-feed-with-a-pull-model"></a>Módosítási hírcsatorna olvasása lekéréses modellel

A [hírcsatorna-lekéréses modell módosítása](change-feed-pull-model.md) lehetővé teszi, hogy a változási csatornát a saját tempójában használja fel. Az ügyfélnek meg kell adnia a módosításokat, és nincs automatikus lekérdezés a változásokhoz. Ha az utolsó feldolgozott módosítást (a leküldéses modell címbérleti tárolójához hasonlóan) véglegesen "könyvjelző" értékre szeretné állítani, a [folytatási tokent kell mentenie](change-feed-pull-model.md#saving-continuation-tokens).

A Change feed lekérési modell használata esetén a változási hírcsatorna ennél alacsonyabb szintű vezérlést kap. A módosítási hírcsatorna a lekéréses modellel való olvasásakor három lehetőség közül választhat:

- Teljes tároló változásainak olvasása
- Adott [FeedRange](change-feed-pull-model.md#using-feedrange-for-parallelization) változásainak olvasása
- Egy adott partíciós kulcs értékének módosítása

A integrálással több ügyfélen is feldolgozhatja, ugyanúgy, mint a Change feed processzorral. A lekéréses modell azonban nem kezeli automatikusan az ügyfelek közötti terheléselosztást. Ha a lekéréses modellt használja a integrálással feldolgozásához, először a FeedRanges listáját fogja beszerezni. A FeedRange a partíciós kulcs értékeinek egy tartományát öleli fel. Rendelkeznie kell egy Orchestrator-folyamattal, amely beolvassa a FeedRanges, és elosztja azokat a gépek között. Ezekkel a FeedRanges több géppel is elolvashatja a változási csatornát párhuzamosan.

A lekéréses modellel nem érhető el "legalább egyszeri" kézbesítési garancia. A lekéréses modell alacsony szintű vezérlést biztosít annak eldöntéséhez, hogy hogyan szeretné kezelni a hibákat.

> [!NOTE]
> A hírcsatorna-váltási lekérési modell jelenleg csak előzetes verzióban érhető [el a Azure Cosmos db .net SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) -ban. Az előzetes verzió még nem érhető el más SDK-verziókhoz.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Adatcsatorna módosítása a Cassandra és a MongoDB API-khoz

A MongoDB API-ban és a Cassandra API-ben predikátummal végzett lekérdezésben a változási csatorna funkciói a változási adatfolyamként jelennek meg. Ha többet szeretne megtudni a MongoDB API megvalósítási részleteiről, tekintse meg a [streamek módosítása Azure Cosmos db a MONGODB API-ban](mongodb-change-streams.md)című témakört.

A natív Apache Cassandra lehetővé teszi az adatváltozások rögzítését (CDC), egy olyan mechanizmust, amely az archiváláshoz adott táblákat jelöl, valamint elutasítja az írásokat az adott táblákba, amint a CDC-naplóhoz konfigurálható méretű lemez van elérve. A Cassandra Azure Cosmos DB API-ban az adatcsatorna módosítása funkció javítja a CQL-on keresztüli predikátumok lekérdezésének lehetőségét. További információ a megvalósítás részleteiről: [a Azure Cosmos db API használata a Cassandra-ben](cassandra-change-feed.md)című témakörben.

## <a name="next-steps"></a>További lépések

A következő cikkekben továbbra is megismerheti a hírcsatornák változását:

* [A hírcsatorna változásának áttekintése](change-feed.md)
* [A Change feed használata a Azure Functions](change-feed-functions.md)
* [A hírcsatorna-feldolgozó függvénytárának módosítása](change-feed-processor.md)
