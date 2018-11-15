---
title: Azure Cosmos DB változáscsatorna az Azure Functions használatával
description: Az Azure Cosmos DB módosításához használja a hírcsatorna az Azure Functions használatával
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.openlocfilehash: 943cd79237290cd91d56cc1c51f5d773cdb16634
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636390"
---
# <a name="how-to-use-azure-cosmos-db-change-feed-with-azure-functions"></a>Azure Cosmos DB változáscsatorna az Azure Functions használatával

Az Azure Functions használata esetén a legegyszerűbb módja a módosítási hírcsatornáról csatlakozni-e hozzáadása egy [Azure Cosmos DB-eseményindító](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger) az Azure Functions-alkalmazás. Amikor létrehoz egy Cosmos DB-eseményindító egy Azure Functions-alkalmazás, választja, a Cosmos-tárolóhoz való csatlakozáshoz, és a függvény aktiválódik, amikor módosítja a tároló valami.

Eseményindítók hozható létre az Azure Functions portálon vagy az Azure Cosmos DB Portalon vagy programozott módon. További információkért lásd: [Azure Cosmos DB és az Azure Functions használatával kiszolgáló nélküli adatbázis-használat](serverless-computing-database.md).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region"></a>Hogyan konfigurálható az Azure functions egy adott régió olvasni?

Lehetséges meghatározásához a [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations) az Azure Cosmos DB-eseményindító használatakor, hogy a régiók listáját adja meg. Legyen azonos a ConnectionPolicy, hogy az eseményindító, olvassa el az előnyben részesített régiókból származó testreszabása. Ideális esetben szeretné olvasni a legközelebbi régió, ahol az Azure Functions üzemel.

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Mi az az alapértelmezett méret az Azure Functions kötegek?

Alapértelmezett érték az Azure Functions minden egyes elindításaihoz 100 elemet. Azonban ez a szám nem konfigurálható a function.json fájlban. Itt van a teljes [konfigurációs listájának](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Ha helyileg fejleszt, frissítse az alkalmazás beállításait a local.settings.json fájljában.

### <a name="i-am-monitoring-a-container-and-reading-its-change-feed-however-i-dont-get-all-the-inserted-documents-some-items-are-missing"></a>Általam figyelt egy tárolót, és a módosítási hírcsatorna olvasását, azonban nem jelenik meg a beszúrt dokumentumokat, bizonyos elemek nem találhatók?

Győződjön meg arról, hogy nincs más Azure-függvény olvasása ugyanazt a tárolót a bérlet ugyanazt a tárolót. A hiányzó dokumentumok dolgozza fel a többi Azure Functions is használ ugyanabban a bérlet.

Ezért több Azure Functions olvasni, ugyanaz a módosítási hírcsatornáról hoz létre, ha azok kell különböző címbérlet-tárolók használata vagy a "leasePrefix" konfiguráció használata ugyanabban a tárolóban. Azonban a változáscsatorna feldolgozói könyvtárával használatakor az Azure-függvény több példányát is elindítható, és az SDK fogja osztani a dokumentumok automatikusan az Ön számára különböző példányai között.

### <a name="azure-cosmos-item-is-updated-every-second-and-i-dont-get-all-the-changes-in-azure-functions-listening-to-change-feed"></a>Az Azure Cosmos-elem másodpercenként frissül, és nem jelenik meg minden módosítást az Azure Functions módosításcsatornáját figyelő?

Az Azure Functions a változáscsatorna módosítások folyamatosan, az alapértelmezett maximális 5 másodperc késéssel kérdezi le. Ha nem változtak függőben lévő kell olvasni, vagy ha nincsenek függőben lévő módosításokat, az eseményindító alkalmazása után, a függvény azonnal olvasási fogja őket. Azonban ha nem változtak függőben lévő, a függvény fog várjon 5 másodperc és lekérdezi a további módosításokat.

A dokumentum több módosítást, amely az új módosítások lekérdezésének eseményindító tartott ugyanolyan időköz kap, ha a dokumentumot, és nem a köztes legújabb verziójának kaphat.

Ha szeretne lekérdezni az 5 másodpercnél rövidebb csatorna módosítása, például minden második, meg is konfigurálja a lekérdezési idő "feedPollDelay", lásd: [a teljes konfiguráció](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations). Az alapértelmezett 5000-es érték ezredmásodpercben van definiálva. Ciklikus lekérdezés esetében lehetséges kevesebb mint 1 másodperc, de nem javasolt, mert több Processzor-memória indul.

### <a name="can-multiple-azure-functions-read-one-containers-change-feed"></a>Olvashatja a több Azure Functions egy tároló módosítási hírcsatorna?

Igen. Több Azure Functions olvashatja a módosítási hírcsatorna ugyanazt a tárolót. Azonban az Azure Functions rendelkeznie kell egy külön "leaseCollectionPrefix" definiálva.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-in-a-batch-of-10-documents-and-i-get-an-error-at-seventh-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-document-ie-seventh-document-in-my-next-feed"></a>Ha Azure Functions használatával 10 dokumentumok kötegelt feldolgozási változáscsatorna vagyok, és hibaüzenetet kapok, hetedik dokumentum. Ebben az esetben az utolsó három dokumentumok feldolgozása nem Hogyan tudok indíthatja feldolgozási a sikertelen dokumentum (azaz, hetedik dokumentum) a következő hírcsatornában?

Kezelni a hibát, a javasolt egyik burkolása try-catch blokkon a kódot, és meg vannak léptetés listában egy dokumentumot, ha minden egyes ismétléskor burkolhatja a saját try-catch blokkban. A hiba tényleges és helyezze a dokumentum egy üzenetsorban (kézbesíthetetlen levelek), és megadhatja, logikai dokumentumokat állítja elő a hiba kezelésére. Ezzel a módszerrel van egy 200-dokumentum batch, és nem sikerült, csak egyetlen dokumentumot nem rendelkezik, az egész batch dobja.

Abban az esetben, ha a hiba, meg kell nem Visszatekerés az ellenőrzőpont vissza a kezdő más meg fog is továbbra is megkapja ezeket a dokumentumokat a módosítási hírcsatorna. Ne feledje, hogy a dokumentum az előző pillanatképet elveszítheti a módosítási hírcsatorna tartja a dokumentumokat, ezt, mert az utolsó végső beépülő képernyőképe. módosítási hírcsatorna tartja a dokumentum csak egy előző verzióját, és a köztes más folyamatok származnak, és módosítani a dokumentumot.

A kód tartsa kijavítása, hamarosan található dokumentumok a kézbesítetlen levelek várólistájára vonatkozik. Módosítási hírcsatorna rendszer automatikusan hívja meg az Azure Functions és a check point belsőleg munkaterheléseire Azure-függvény. Ha azt szeretné, állítsa vissza a a check point és minden szempontját, érdemes lehet processzor SDK használatával módosítási hírcsatorna.

### <a name="are-there-any-extra-costs-for-using-the-azure-cosmos-db-trigger"></a>Vannak-e bármilyen további költségek az Azure Cosmos DB-eseményindító használatának?

Az Azure Cosmos DB-eseményindító belsőleg kihasználja a Változáscsatorna feldolgozói kódtárával. Emiatt a bérletek gyűjteménye, állapota és a részleges ellenőrzőpontok nevű egy extra gyűjteményben van szükség. Ez az állapot-felügyeleti dinamikusan méretezhető, és abban az esetben állítsa le az Azure Functions, és folytatja a műveletet egy későbbi időpontban szeretné továbbra is szükséges. További tudnivalókért lásd: [hogyan használható a módosítási hírcsatorna feldolgozói kódtára](change-feed-processor.md).

## <a name="next-steps"></a>További lépések

Folytassa további információ a változáscsatorna az alábbi cikkeket:

* [Módosítási hírcsatorna áttekintése](change-feed.md)
* [A módosítási hírcsatornáról olvasási módjai](read-change-feed.md)
* [Feldolgozói kódtára használatával módosítási hírcsatorna](change-feed-processor.md)
* [Hogyan használható a módosítási hírcsatorna feldolgozói kódtára](change-feed-processor.md)
* [Azure Cosmos DB és az Azure Functions használatával kiszolgáló nélküli adatbázis-használat](serverless-computing-database.md)
