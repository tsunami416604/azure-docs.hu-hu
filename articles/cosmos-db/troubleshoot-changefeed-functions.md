---
title: Problémák diagnosztizálása és hibaelhárítása Azure Functions trigger használatakor Cosmos DB
description: Gyakori problémák, megkerülő megoldások és diagnosztikai lépések a Azure Functions trigger használatakor Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.date: 07/17/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 17fa443c3b0113d80a020f2a43c7099cf5a832d2
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68772901"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Problémák diagnosztizálása és hibaelhárítása Azure Functions trigger használatakor Cosmos DB

Ez a cikk a gyakori problémákról, a megkerülő megoldásokról és a diagnosztikai lépésekről nyújt útmutatást, ha a [Cosmos DB Azure functions triggert](change-feed-functions.md)használja.

## <a name="dependencies"></a>Függőségek

A Cosmos DB Azure Functions-trigger és-kötések a Azure Functions futtatókörnyezeten alapuló kiterjesztési csomagoktól függenek. Mindig tartsa naprakészen ezeket a csomagokat, mert olyan javításokat és új funkciókat is tartalmazhatnak, amelyek az esetlegesen felmerülő esetleges problémák megoldására képesek:

* Azure Functions v2 esetén lásd: [Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* A Azure Functions v1 esetében lásd: [Microsoft. Azure. webjobs. Extensions. DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Ez a cikk mindig Azure Functions v2-re hivatkozik, ha a futtatókörnyezetet megemlítik, kivéve, ha explicit módon meg van adva.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Az Azure Cosmos DB SDK egymástól független felhasználása

A kiterjesztési csomag legfontosabb funkciója, hogy támogatást nyújtson a Azure Functions-triggerhez és a Cosmos DB-kötésekhez. Emellett tartalmazza a [Azure Cosmos db .net SDK](sql-api-sdk-dotnet-core.md)-t is, amely akkor hasznos, ha az trigger és a kötések használata nélkül szeretné interaktívan használni a Azure Cosmos db.

Ha az Azure Cosmos DB SDK-t szeretné használni, győződjön meg arról, hogy nem adja hozzá a projekthez egy másik NuGet-csomag hivatkozását. Ehelyett az **SDK-referenciát a Azure functions "kiterjesztési csomagján keresztül oldja**fel. Az Azure Cosmos DB SDK használata az triggertől és a kötéstől függetlenül

Emellett, ha manuálisan hozza létre az [Azure Cosmos db SDK-ügyfél](./sql-api-sdk-dotnet-core.md)saját példányát, kövesse az ügyfél csak egy példányának [használatát egy egyedi minta megközelítéssel](../azure-functions/manage-connections.md#documentclient-code-example-c). Ez a folyamat elkerüli a lehetséges szoftvercsatorna-problémákat a műveletekben.

## <a name="common-scenarios-and-workarounds"></a>Gyakori forgatókönyvek és megkerülő megoldások

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Az Azure-függvény meghiúsul, és a hibaüzenetek gyűjtése nem létezik

Az Azure-függvény a következő hibaüzenettel meghiúsul: "vagy" a forrás gyűjteménye "(adatbázis neve"), vagy a (z) "collection2-Name" címbérleti gyűjtemény (adatbázis: "Adatbázis2") nem létezik. Mindkét gyűjteménynek léteznie kell a figyelő elindítása előtt. A címbérleti gyűjtemény automatikus létrehozásához állítsa a "CreateLeaseCollectionIfNotExists" értéket "true" értékre.

Ez azt jelenti, hogy a trigger működéséhez szükséges Azure Cosmos-tárolók egyike vagy mindkettő nem létezik, vagy nem érhető el az Azure-függvény számára. **A hiba maga tájékoztatja arról, hogy mely Azure Cosmos-adatbázis és-tárolók a** konfiguráció alapján megkeresett triggerek.

1. Ellenőrizze az `ConnectionStringSetting` attribútumot, és hogy az **Azure-függvényalkalmazás található beállításokra hivatkozik-** e. Az attribútum értéke nem lehet maga a kapcsolatok karakterlánca, hanem a konfigurációs beállítás neve.
2. Győződjön meg arról `databaseName` , `collectionName` hogy a és létezik az Azure Cosmos-fiókjában. Ha automatikus érték-helyettesítést használ (mintázatok használatával `%settingName%` ), győződjön meg arról, hogy a beállítás neve létezik az Azure-függvényalkalmazás.
3. Ha nem ad meg értéket `LeaseCollectionName/leaseCollectionName`, az alapértelmezett érték a "bérletek". Ellenőrizze, hogy létezik-e ilyen tároló. Opcionálisan beállíthatja az `CreateLeaseCollectionIfNotExists` `true` triggerben lévő attribútumot az automatikus létrehozásához.
4. Ellenőrizze az [Azure Cosmos-fiókja tűzfal](how-to-configure-firewall.md) -konfigurációját, és győződjön meg róla, hogy nem blokkolja az Azure-függvényt.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Az Azure-függvény nem indul el "a megosztott átviteli sebességű gyűjteménynek rendelkeznie kell partíciós kulccsal"

A Azure Cosmos DB bővítmény korábbi verziói nem támogatják a [megosztott átviteli sebességű adatbázisban](./set-throughput.md#set-throughput-on-a-database)létrehozott bérletek tároló használatát. A probléma megoldásához frissítse a [Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) kiterjesztést a legújabb verzió beszerzéséhez.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Az Azure-függvény nem indul el a következővel: "a bérlet gyűjteménye, ha particionálva van, az azonosítóval megegyező partíciós kulcsnak kell lennie."

Ez a hiba azt jelenti, hogy az aktuális címbérleti tároló particionálva van, de a partíciós `/id`kulcs elérési útja nem. A probléma megoldásához újra létre kell hoznia a bérletek tárolót `/id` a partíciós kulccsal.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>"Az érték nem lehet null. Paraméter neve: o "a Azure Functions naplókban, amikor megpróbálja futtatni az triggert

Ez a probléma akkor jelenik meg, ha a Azure Portal használja, és az triggert használó Azure-függvény vizsgálatakor megpróbálja kiválasztani a **Futtatás** gombot a képernyőn. Az trigger nem igényli, hogy válassza a Futtatás az indításhoz lehetőséget, a rendszer automatikusan elindul az Azure-függvény telepítésekor. Ha szeretné ellenőrizni az Azure-függvény naplózási streamjét a Azure Portalon, csak nyissa meg a figyelt tárolót, és szúrjon be néhány új elemet, automatikusan megjelenik az indítást végrehajtó művelet.

### <a name="my-changes-take-too-long-be-received"></a>A módosítások túl sokáig tartanak

Ennek a forgatókönyvnek több oka is lehet, és mindegyiket ellenőrizni kell:

1. Az Azure-függvény üzembe helyezése ugyanabban a régióban történik, mint az Azure Cosmos-fiókja? Az optimális hálózati késés érdekében mind az Azure-függvénynek, mind az Azure Cosmos-fióknak ugyanabban az Azure-régióban kell elhelyezkednie.
2. A változások az Azure Cosmos-tárolóban folyamatosan vagy szórványosan történnek?
Ha ez utóbbi, lehet némi késés a tárolt változtatások és az Azure-függvény felszedése között. Ez azért van így, mert belsőleg, amikor a trigger ellenőrzi az Azure Cosmos-tárolóban bekövetkező változásokat, és a rendszer egy nem függőben lévő értéket keres, egy konfigurálható időt (alapértelmezés szerint 5 másodperc) fog alvó állapotba helyezni az új módosítások ellenőrzése előtt (a magas RU-használat elkerüléséhez). Ezt az alvó időt a trigger `FeedPollDelay/feedPollDelay` konfigurációjának beállításán keresztül [](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) állíthatja be (az érték várhatóan ezredmásodpercben).
3. Előfordulhat, hogy az Azure Cosmos [-tárolók száma korlátozott](./request-units.md).
4. A `PreferredLocations` triggerben található attribútum segítségével megadhatja az Azure-régiók vesszővel tagolt listáját az egyéni előnyben részesített kapcsolódási sorrend meghatározásához.

### <a name="some-changes-are-missing-in-my-trigger"></a>Néhány módosítás hiányzik a saját triggerben

Ha azt tapasztalja, hogy az Azure Cosmos-tárolóban történt módosítások közül néhányat nem az Azure-függvény vette fel, akkor a kezdeti vizsgálati lépésnek kell megtörténnie.

Amikor az Azure-függvény megkapja a módosításokat, gyakran dolgozza fel őket, és igény szerint elküldheti az eredményt egy másik célhelyre. Ha a hiányzó módosításokat vizsgálja, győződjön meg arról, hogy a betöltési **ponton milyen változások érkeznek** (az Azure-függvény indításakor), nem a célhelyen.

Ha néhány módosítás hiányzik a célhelyről, ez azt jelentheti, hogy hiba történt az Azure-függvény végrehajtása során a módosítások fogadása után.

Ebben az esetben a legjobb művelet az, ha `try/catch blocks` a kódot és azon belül a hurkokat, amelyek feldolgozzák a módosításokat, felderíti az elemek adott részhalmaza meghibásodását, és ennek megfelelően kezeli őket (küldje el egy másik tárolóba további elemzés vagy újrapróbálkozás). 

> [!NOTE]
> A Cosmos DB Azure Functions-triggere alapértelmezés szerint nem próbálkozik újra egy köteg változásával, ha nem kezelt kivétel történt a kód végrehajtása során. Ez azt jelenti, hogy a módosítások nem érkeznek meg a célhelyre, mert nem kell feldolgoznia azokat.

Ha azt tapasztalja, hogy a trigger nem fogadta el a módosításokat, a leggyakoribb forgatókönyv az, hogy **egy másik Azure-függvény fut**. Lehet, hogy egy másik Azure-függvény üzembe helyezése az Azure-ban vagy egy olyan Azure-függvény, amely helyileg fut egy fejlesztői gépen, amely **pontosan ugyanazokkal a konfigurációval** rendelkezik (ugyanaz a figyelt és a bérlet tároló), és ez az Azure-függvény ellopja a módosítások egy részhalmazát. várhatóan feldolgozza az Azure-függvényt.

Emellett a forgatókönyv érvényesíthető is, ha tudja, hány Azure függvényalkalmazás példány fut. Ha megvizsgálja a bérletek tárolóját, és megszámolja a címbérleti elemek számát, a bennük lévő `Owner` tulajdonság különböző értékeinek meg kell egyezniük a függvényalkalmazás példányainak számával. Ha több tulajdonos van, mint az ismert Azure függvényalkalmazás-példányok, az azt jelenti, hogy ezek a további tulajdonosok a módosítások ellopása.

A helyzet megkerülő megoldásának egyik egyszerű módja, ha `LeaseCollectionPrefix/leaseCollectionPrefix` a függvényt egy új/eltérő értékkel alkalmazza, vagy Alternatív megoldásként egy új bérletek tárolóval kell tesztelni.

### <a name="need-to-restart-and-re-process-all-the-items-in-my-container-from-the-beginning"></a>Újra kell indítania és újra fel kell dolgoznia a tárolóban lévő összes elemet az elejéről 
Egy tároló összes elemének újbóli feldolgozása az elejétől:
1. Ha éppen fut, állítsa le az Azure-függvényt. 
1. Törölje a címbérleti gyűjtemény dokumentumait (vagy törölje, majd hozza létre újra a címbérleti gyűjteményt, hogy az üres legyen)
1. Állítsa a függvény [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) CosmosDBTrigger attribútumát True (igaz) értékre. 
1. Indítsa újra az Azure-függvényt. Most már elolvashatja és feldolgozhatja az összes módosítást a kezdetektől fogva. 

A [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) True értékre állításával megtudhatja, hogy az Azure-függvény az aktuális idő helyett a gyűjtemény előzményeinek elejéről olvassa be a módosításokat. Ez csak akkor működik, ha nincsenek már létrehozott bérletek (azaz dokumentumok a bérletek gyűjteményében). Ha a tulajdonságot igaz értékre állítja, akkor a már létrehozott bérletek nem lépnek érvénybe. Ebben az esetben, ha egy függvény leáll és újraindul, a a bérletek gyűjteményében meghatározottak szerint megkezdi az utolsó ellenőrzőpont olvasását. Az elejétől a fenti lépéseket követve újra feldolgozhatja a 1-4-as lépést.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>A kötés csak a IReadOnlyList\<Document > vagy a JArray használatával végezhető el.

Ez a hiba akkor fordul elő, ha a Azure Functions projekt (vagy bármely hivatkozott projekt) manuális NuGet hivatkozást tartalmaz az Azure Cosmos DB SDK-ra, amely az [Azure Functions Cosmos db bővítmény](./troubleshoot-changefeed-functions.md#dependencies)által megadott verziótól eltérő verziójú.

Ennek a helyzetnek a megkerülő megoldásához távolítsa el a hozzáadott manuális NuGet-hivatkozást, és hagyja meg a Azure Cosmos DB SDK-referenciát a Azure Functions Cosmos DB kiterjesztési csomagon keresztül.

## <a name="next-steps"></a>További lépések

* [A Azure Functions figyelésének engedélyezése](../azure-functions/functions-monitoring.md)
* [A .NET SDK Azure Cosmos DB hibaelhárítása](./troubleshoot-dot-net-sdk.md)
