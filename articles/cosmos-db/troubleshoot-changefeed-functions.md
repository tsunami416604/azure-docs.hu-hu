---
title: Az Azure Functions trigger cosmos DB használatával kapcsolatos problémák elhárítása
description: Gyakori problémák, kerülő megoldások és diagnosztikai lépések az Azure Functions eseményindító használatakor a Cosmos DB-hez
author: ealsur
ms.service: cosmos-db
ms.date: 03/13/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7bf7d418e3f2680b32f61e42cffc76c921068508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365508"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Az Azure Functions trigger használatakor a Cosmos DB-hoz használt problémák diagnosztizálása és elhárítása

Ez a cikk ismerteti a gyakori problémákat, kerülő megoldásokat és diagnosztikai lépéseket, amikor az Azure Functions eseményindítót használja a [Cosmos DB-hez.](change-feed-functions.md)

## <a name="dependencies"></a>Függőségek

Az Azure Functions eseményindító és kötések cosmos DB függ a bővítmény csomagok az alap Azure Functions futásidejű. Mindig tartsa naprakészen ezeket a csomagokat, mivel ezek olyan javításokat és új funkciókat tartalmazhatnak, amelyek orvosolhatják az esetleges problémákat:

* Az Azure Functions V2-ről a [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Az Azure Functions V1 fájlról a [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Ez a cikk mindig az Azure Functions V2-re hivatkozik, ha a futásidejű t említi, kivéve, ha kifejezetten meg van adva.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Az Azure Cosmos DB SDK független felhasználása

A bővítménycsomag legfontosabb funkciója az Azure Functions eseményindító és a Cosmos DB kötéseinek támogatása. Az Azure [Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md)is tartalmazza, amely akkor hasznos, ha az Azure Cosmos DB programozott módon, az eseményindító és a kötések használata nélkül szeretne együttműködni.

Ha az Azure Cosmos DB SDK-t szeretné használni, győződjön meg arról, hogy nem ad hozzá a projekthez egy másik NuGet-csomaghivatkozást. Ehelyett **hagyja, hogy az SDK-hivatkozás az Azure Functions bővítménycsomagon keresztül oldódja fel.** Az Azure Cosmos DB SDK-t az eseményindítótól és a kötésektől elkülönítve használja fel

Emellett ha manuálisan hozza létre az [Azure Cosmos DB SDK-ügyfél](./sql-api-sdk-dotnet-core.md)saját példányát, kövesse azt a mintát, hogy az ügyfélnek csak egy példánya [van singleton mintamegközelítést használva.](../azure-functions/manage-connections.md#documentclient-code-example-c) Ez a folyamat elkerüli a műveletek potenciális szoftvercsatorna-problémáit.

## <a name="common-scenarios-and-workarounds"></a>Gyakori forgatókönyvek és kerülő megoldások

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Az Azure-függvény sikertelen, mivel a hibaüzenetek gyűjteménye nem létezik

Az Azure Függvény sikertelen a következő hibaüzenettel: "A forrásgyűjtemény "gyűjteményneve" (az adatbázis "adatbázis-név") vagy a "collection2-name" címbérlet-gyűjtemény (az adatbázis "database "database2-name") nem létezik. Mindkét gyűjteménynek léteznie kell a figyelő indítása előtt. A címbérlet-gyűjtemény automatikus létrehozásához állítsa a "CreateLeaseCollectionIfNotExists" értéket "true"-ra" beállításra."

Ez azt jelenti, hogy az egyik vagy mindkét, az Azure Cosmos-tárolók működéséhez szükséges az eseményindító nem létezik, vagy nem érhető el az Azure Függvény. **Maga a hiba megmondja, hogy melyik Azure Cosmos-adatbázis és -tároló a** konfiguráció alapján keresett eseményindító.

1. Ellenőrizze `ConnectionStringSetting` az attribútumot, és hogy **hivatkozik-e az Azure Function Appban található beállításra.** Az attribútum értéke nem magának a kapcsolati karakterláncnak kell lennie, hanem a konfigurációs beállítás neve.
2. Ellenőrizze, `databaseName` hogy `collectionName` a és létezik-e az Azure Cosmos-fiókban. Ha automatikus értékcsere (minták `%settingName%` használatával), győződjön meg arról, hogy a beállítás neve létezik az Azure Function App.
3. Ha nem ad `LeaseCollectionName/leaseCollectionName`meg egy, az alapértelmezett a "bérletek". Ellenőrizze, hogy létezik-e ilyen tároló. Tetszés szerint `CreateLeaseCollectionIfNotExists` beállíthatja, hogy `true` az eseményindító attribútuma automatikusan létrehozza azt.
4. Ellenőrizze az [Azure Cosmos-fiók tűzfal-konfigurációját,](how-to-configure-firewall.md) és tekintse meg, hogy nem blokkolja az Azure-függvényt.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Az Azure Függvény nem indul el a "Megosztott átviteli rendszergyűjtemény kell egy partíciókulcs"

Az Azure Cosmos DB Extension korábbi verziói nem támogatták a [megosztott átviteli-adatbázisban](./set-throughput.md#set-throughput-on-a-database)létrehozott bérlettároló használatát. A probléma megoldásához frissítse a [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) bővítményt a legújabb verzió lehívásához.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Az Azure Függvény nem indul el a "PartitionKey kell megadni ehhez a művelethez."

Ez a hiba azt jelenti, hogy jelenleg egy particionált címbérlet-gyűjteményt használ egy régi [bővítményfüggőségtel.](#dependencies) Frissítsen a legújabb elérhető verzióra. Ha jelenleg fut az Azure Functions V1, frissítenie kell az Azure Functions V2.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Az Azure Függvény nem indul el a "A címbérlet-gyűjtemény, ha particionált, kell partíciókulcs egyenlő id."

Ez a hiba azt jelenti, hogy az aktuális bérletek `/id`tárolója particionálva van, de a partíciókulcs elérési útja nem. A probléma megoldásához újra létre kell hoznia `/id` a címbérlet-tárolót partíciókulcsként.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Az "Érték nem lehet null értékű. Paraméternév: o" az Azure Functions naplóiban, amikor megpróbálja futtatni az eseményindítót

Ez a probléma akkor jelenik meg, ha az Azure Portalon, és megpróbálja kiválasztani a **Futtatás** gombot a képernyőn, amikor az eseményindítót használó Azure-függvényt vizsgál. Az eseményindító nem igényel a Futtatás indításhoz lehetőséget, automatikusan elindul, amikor az Azure-függvény telepítve van. Ha azt szeretné, hogy ellenőrizze az Azure-függvény naplóadatfolyamaz Azure Portalon, csak menj a figyelt tárolóba, és helyezzen be néhány új elemeket, automatikusan megjelenik az eseményindító végrehajtása.

### <a name="my-changes-take-too-long-to-be-received"></a>A módosítások fogadása túl sokáig tart

Ennek a forgatókönyvnek több oka is lehet, és mindegyiket ellenőrizni kell:

1. Azure-függvénye ugyanabban a régióban található, mint az Azure Cosmos-fiókja? Az optimális késéshez az Azure-függvénynek és az Azure Cosmos-fióknak ugyanabban az Azure-régióban kell lennie.
2. A változások folyamatosan vagy szórványosan történnek az Azure Cosmos-tárolójában?
Ha ez utóbbi, akkor késés fordulhat elő a módosítások tárolása és azok az Azure-függvény általi felvétele között. Ez azért van így, mert belsőleg, amikor az eseményindító ellenőrzi az Azure Cosmos-tároló változásait, és nem talál függőben lévő elemet, egy személyre szabható ideig (alapértelmezés szerint 5 másodpercig) alvó módban lesz a következő ellenőrzésig (a magas RU-használat elkerülése végett). Ezt a szüneteltetési időt az eseményindító [konfigurációjának](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)`FeedPollDelay/feedPollDelay` beállításában adhatja meg (ezredmásodpercben).
3. Előfordulhat, hogy az Azure Cosmos-tároló [korlátozott.](./request-units.md)
4. Az eseményindítóban található `PreferredLocations` attribútum használatával megadhat egy vesszővel tagolt Azure-régiók listáját egy egyéni előnyben részesített kapcsolati sorrend meghatározásához.

### <a name="some-changes-are-repeated-in-my-trigger"></a>Néhány módosítás megismétlődik az eseményindítóban

A "változás" fogalma egy dokumentumon végzett művelet. A leggyakoribb forgatókönyvek, ahol az azonos dokumentum eseményei érkeznek:
* A fiók a végleges konzisztenciát használja. A módosítási hírcsatorna végső konzisztenciaszinten történő bevitele közben ismétlődő események lehetnek a későbbi változáscsatorna olvasási műveletek között (az egyik olvasási művelet utolsó eseménye jelenik meg a következő közül az elsőként).
* A dokumentum frissítése folyamatban van. A módosítási hírcsatorna több műveletet is tartalmazhat ugyanazon dokumentumokhoz, ha a dokumentum frissítéseket kap, több eseményt is felvehet (minden frissítéshez egyet). Az egyik egyszerű módja annak, hogy megkülönböztesse `_lsn` a különböző műveletek et az azonos dokumentum, hogy nyomon kövesse a [tulajdonság minden változás.](change-feed.md#change-feed-and-_etag-_lsn-or-_ts) Ha nem egyeznek, ezek különböző módosítások ugyanazon a dokumentumon.
* Ha csak a `id`, a dokumentumok azonosítása kor, ne `id` feledje, hogy a dokumentum egyedi `id` azonosítója a partíciókulcs (két dokumentum lehet ugyanazzal, de eltérő partíciókulccsal).

### <a name="some-changes-are-missing-in-my-trigger"></a>Néhány módosítás hiányzik az eseményindítómból

Ha úgy találja, hogy az Azure Cosmos-tárolóban történt módosítások némelyikét nem veszi fel az Azure-függvény, van egy kezdeti vizsgálati lépés, amelyet meg kell történnie.

Amikor az Azure-függvény megkapja a módosításokat, gyakran feldolgozza őket, és szükség esetén az eredményt egy másik célhelyre is elküldheti. A hiányzó módosítások vizsgálatakor győződjön meg arról, hogy a betöltési ponton (amikor az Azure Függvény elindul) milyen módosításokat fogad, ne a célhelyen mérje meg, hogy **mely módosítások érkeznek.**

Ha néhány módosítás hiányzik a cél, ez azt jelentheti, hogy valamilyen hiba történik az Azure Függvény végrehajtása során a módosítások beérkezése után.

Ebben a forgatókönyvben a legjobb megoldás `try/catch` az, hogy adja hozzá a blokkokat a kódot, és a hurkok, amelyek feldolgozzák a változásokat, észlelni a hiba egy adott részhalmaza az elemek, és kezelni őket ennek megfelelően (küldje el őket egy másik tárolótovábbi elemzés vagy újra). 

> [!NOTE]
> Az Azure Functions Cosmos DB-eseményindítója alapértelmezés szerint nem próbálkozik újra a módosításköteggel, ha a kódvégrehajtás során nem kezelt kivétel lépett fel. Ez azt jelenti, hogy a módosítások azért nem érkeztek meg a célállomásra, mert nem tudja feldolgozni őket.

Ha úgy találja, hogy néhány változtatást egyáltalán nem kapott meg az eseményindító, a leggyakoribb forgatókönyv az, hogy egy **másik Azure-függvény fut.** Lehet, hogy egy másik Azure-függvény az Azure-ban, vagy egy Azure-függvény helyileg futó fejlesztői gép, amely **pontosan ugyanazt a konfigurációt** (azonos figyelt és lízingtárolók), és ez az Azure-funkció ellopja a módosítások egy részét, amit elvár az Azure-függvény feldolgozása.

Emellett a forgatókönyv érvényesíthető, ha tudja, hogy hány Azure Function App-példány fut. Ha megvizsgálja a bérleti tárolót, és megszámolja a bérletelemek számát, a bennük lévő `Owner` tulajdonság különböző értékeinek meg kell egyeznie a Függvényalkalmazás példányainak számával. Ha több tulajdonos van, mint az ismert Azure Function App-példányok, ez azt jelenti, hogy ezek az extra tulajdonosok azok, akik "ellopják" a módosításokat.

Egy egyszerű módja annak, hogy megkerülje `LeaseCollectionPrefix/leaseCollectionPrefix` ezt a helyzetet, hogy alkalmazza a függvény egy új / más értéket, vagy, alternatívaként, teszt egy új bérleti tároló.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>Újra kell indítani, és újra feldolgozni az összes elemet a tárolóban az elejétől 
A tárolóban lévő összes elem újrafeldolgozása az elejétől kezdve:
1. Állítsa le az Azure-függvényt, ha az éppen fut. 
1. Törölje a címbérlet-gyűjteményben lévő dokumentumokat (vagy törölje és hozza létre újra a címbérlet-gyűjteményt, hogy az üres legyen)
1. Állítsa a [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger attribútumot a függvényben true értékre. 
1. Indítsa újra az Azure függvényt. Ez most olvasni és feldolgozni az összes változtatást az elejétől. 

[A StartFromBeginning beállítása igazra](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) állításával az Azure-függvény a gyűjtemény előzményeinek elejétől kezdje el olvasni a módosításokat az aktuális idő helyett. Ez csak akkor működik, ha nincsenek már létrehozott bérletek (azaz a címbérletgyűjteményben lévő dokumentumok). Ha ezt a tulajdonságot igaz értékre állítja, ha már vannak létrehozott bérletek, annak nincs hatása; ebben a forgatókönyvben, ha egy függvény levan állítva, és újraindul, akkor kezdődik olvasás az utolsó ellenőrzőpont, a bérletek gyűjteményében meghatározott. Az elejétől való újrafeldolgozáshoz kövesse a fenti lépéseket 1-4.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>A kötés csak iReadOnlyList\<document> vagy JArray

Ez a hiba akkor fordul elő, ha az Azure Functions projekt (vagy bármely hivatkozott projekt) tartalmaz egy manuális NuGet hivatkozást az Azure Cosmos DB SDK-ra, amely nem az [Azure Functions Cosmos DB Extension](./troubleshoot-changefeed-functions.md#dependencies)által biztosított verzióval rendelkezik.

A helyzet megoldásához távolítsa el a manuális NuGet-hivatkozást, amely et hozzáadott, és hagyja, hogy az Azure Cosmos DB SDK-hivatkozás feloldása az Azure Functions Cosmos DB Extension csomagon keresztül.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Az Azure Függvény lekérdezési időközének módosítása a változások észleléséhez

Amint azt korábban a [My változások túl sokáig tart, hogy megkapja,](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)az Azure-függvény alvó állapotban egy konfigurálható ideig (5 másodperc, alapértelmezés szerint) ellenőrzése előtt az új változások (a magas RU-fogyasztás elkerülése érdekében). Ezt a szüneteltetési időt az eseményindító [konfigurációjának](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)`FeedPollDelay/feedPollDelay` beállításában adhatja meg (ezredmásodpercben).

## <a name="next-steps"></a>További lépések

* [Figyelés engedélyezése az Azure-funkciókhoz](../azure-functions/functions-monitoring.md)
* [Az Azure Cosmos DB .NET SDK hibaelhárítása](./troubleshoot-dot-net-sdk.md)
