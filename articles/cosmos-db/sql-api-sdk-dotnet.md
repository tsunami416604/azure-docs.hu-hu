---
title: 'Az Azure Cosmos DB: SQL .NET API, SDK és -erőforrások |} A Microsoft Docs'
description: Mindent megtudhat a SQL .NET API és az SDK, beleértve a kiadási dátum, kivezetési dátum és az Azure Cosmos DB .NET SDK minden verziója között végrehajtott módosítások.
services: cosmos-db
author: rnagpal
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: de0203a06e71a90223b864ab3ba059ad1ac9b5e3
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052280"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Az Azure Cosmos DB .NET SDK, az SQL API-hoz: Töltse le és kibocsátási megjegyzések
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítási hírcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor – Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**SDK letöltése**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>

<tr><td>**API-dokumentáció**</td><td>[.NET API dokumentációja](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Minták**</td><td>[.NET platformra írt kódmintái](sql-api-dotnet-samples.md)</td></tr>

<tr><td>**Első lépések**</td><td>[Az Azure Cosmos DB .NET SDK használatának első lépései](sql-api-get-started.md)</td></tr>

<tr><td>**Alapú webappokról szóló oktatóanyagunkat**</td><td>[Webalkalmazás-fejlesztés az Azure Cosmos DB használatával](sql-api-dotnet-application.md)</td></tr>

<tr><td>**Aktuális támogatott keretrendszer**</td><td>[Microsoft .NET-keretrendszer 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Kibocsátási megjegyzések
### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Új kérelem visszavonás támogatása.
* A hozzáadott SetCurrentLocation való ConnectionPolicy, amely automatikusan feltölti az elsődleges helyek a régióhoz igazodik.
* Kijavítva a hiba a partíció-lekérdezésekben Adatbázisközi minimális/maximális és a egy szűrőt, amely megegyezik az egyetlen dokumentum egyes partíción.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview

* DocumentClient módszerek IDocumentClient paritás most már rendelkezik.
* Frissített közvetlen TCP átviteli stack létesített kapcsolatok számának csökkentése érdekében.
* Támogatás hozzáadva a közvetlen mód TCP nem Windows-ügyfelek számára.

### <a name="a-name12201220"></a><a name="1.22.0"/>1.22.0

* FeedOptions hozzáadott ConsistencyLevel tulajdonság.
* A hozzáadott JsonSerializerSettings RequestOptions és FeedOptions.
* A hozzáadott EnableReadRequestsFallback ConnectionPolicy való.

### <a name="a-name12111211"></a><a name="1.21.1"/>1.21.1

* Rögzített KeyNotFoundException a partíció rendelés közötti esetekben a lekérdezések által.
* Kijavítva a hiba, a select záradékban a LINQ-lekérdezések JsonProperty attribútum lett nem folyamatban figyelembe véve.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* Kijavítva a hiba, amely bizonyos fajta feltételek elérte az eredmények az időszakos "Microsoft.Azure.Documents.NotFoundException: az olvasási munkamenet nem érhető el a bemeneti munkamenet-jogkivonat" hibák munkamenet konzisztenciaszint használatakor.

### <a name="a-name12011201"></a><a name="1.20.1"/>1.20.1

* Rögzített regressziós ahol FeedOptions.MaxItemCount = -1-System.ArithmeticException okozott: oldalméret értéke negatív.
* QueryMetrics hozzá egy új ToString() függvényét.
* Közzétett partíció statisztikai gyűjtemények olvasása.
* ChangeFeedOptions hozzáadott PartitionKey tulajdonság.
* Kisebb hibajavítások.

### <a name="a-name11911191"></a><a name="1.19.1"/>1.19.1

* Hozzáadja a DocumentCollection UniqueKeyPolicy tulajdonság használatával a dokumentumok egyedi indexek megadásának lehetőségét.
* Kijavítva a hiba, amelyben az egyéni JsonSerializer beállítások lettek nem folyamatban figyelembe véve az egyes lekérdezések és a tárolt eljárás végrehajtása.

### <a name="a-name11901190"></a><a name="1.19.0"/>1.19.0

* Azure Cosmos DB API-referencia az Azure documentdb módosítás márkajelzési, dokumentáció, szerelvényekben metaadat-információkat és a NuGet-csomagot. 
* Tegye elérhetővé a diagnosztikai adatok és a késés közvetlen kapcsolódás móddal küldött kérelmeket a válaszból. A tulajdonságnevek megkülönböztetik a RequestDiagnosticsString és RequestLatency ResourceResponse osztályhoz.
* Az SDK-verziója letölthető Azure Cosmos DB Emulatort elérhető legújabb verziója szükséges https://aka.ms/cosmosdb-emulator. 

### <a name="a-name11811181"></a><a name="1.18.1"/>1.18.1 

* A Microsoft barátok szerelvények belső módosítása.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* Több megbízhatóság javításai és továbbfejlesztései hozzá.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* Egy lekérdezés eredményeit a kulcs-egy adott partíció tartományértéke sorolására FeedOption, PartitionKeyRangeId támogatása. 
* Támogatás hozzáadva a StartTime, hogy elkezdhessük megvizsgálni a módosítások az időnek az elteltével a ChangeFeedOption.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Kijavítva a JsonSerializable osztály, amely a stack overflow kivételt okozhat.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   Javítva lett egy probléma, amely szükséges az alkalmazás JsonSerializerSettings bevezetése miatt a DocumentClient konstruktor nem kötelező paraméterként újrafordítás.
* A DocumentClient konstruktor elavultként megjelölve az, hogy az alapértelmezett értékek ConnectionPolicy utolsó paraméterként és ConsistencyLevel paraméterek a szükséges JsonSerializerSettings JsonSerializerSettings paraméterrel történő átadásakor.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Támogatás hozzáadva az egyéni JsonSerializerSettings megadása közben hárítható el [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Kijavítva, hogy x64 érintett gépek, amelyek nem támogatják a SSE4 utasítást, és egy SEHException throw, amikor az Azure Cosmos DB SQL-lekérdezések futtatásához.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Egy új konzisztenciaszint támogatása az új ConsistentPrefix nevezik.
*   Az egyes partíciók lekérdezési metrikák támogatása.
*   Támogatás hozzáadva a lekérdezések a folytatási méretét korlátozó.
*   Sikertelen kérelmek nyomkövetésének részletesebb támogatása.
*   Bizonyos teljesítménnyel kapcsolatos fejlesztések készült SDK-ban.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Funkcionális ugyanaz, mint a 1.13.3. Néhány belső módosításokat.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Funkcionális ugyanaz, mint a 1.13.2. Néhány belső módosításokat.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Kijavítva, hogy a rendszer figyelmen kívül hagyja a PartitionKey értéket FeedOptions megadott összesítő lekérdezésekhez.
* Javítva lett egy probléma transzparens kezelését a partíció felügyeleti közepes repülési a partíciók közti Order By záradék a lekérdezés végrehajtása során.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Rögzített holtpontok az API-k, az ASP.NET-környezetben való használat async néhány okozó problémát.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Javít, hogy rugalmasabb SDK bizonyos körülmények között az automatikus feladatátvételre.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Egy WebException alkalmanként okozó probléma: A távoli név nem oldható fel.
* A beírt dokumentumok közvetlenül olvasásakor ReadDocumentAsync API új hozzáadásával támogatása hozzáadva.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* LINQ támogatása összesítési lekérdezések (száma, MIN, MAX, SUM és átlagos).
* Javítás a memóriavesztés az oka eseménykezelő ConnectionPolicy objektum számára.
* Javít egy problémát UpsertAttachmentAsync viselkedésmintáit nem dolgozott ETag használatakor.
* Javítsa ki egy problémát a viselkedésmintáit közötti partíció az order by lekérdezések folytatása nem dolgozott rendezésekor karakterlánc típusú.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Összesítés lekérdezések (száma, MIN, MAX, SUM és átlagos) támogatása. Lásd: [összesítési támogatási](sql-api-sql-query.md#Aggregates).
* Süllyesztett minimálisan 2500 RU/s 10,100 RU/s a particionált gyűjtemények átviteli sebességet.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Javít egy problémát viselkedésmintáit a partícióra kiterjedő lekérdezések némelyike nem került sor a gazdagép 32 bites folyamatban.
* Javít egy problémát viselkedésmintáit a munkamenet-tároló nem frissítése a jogkivonattal a sikertelen kérelmek átjáró módban.
* Bizonyos esetekben javít egy problémát viselkedésmintáit egy lekérdezést az UDF-leképezés hívások volt sikertelen.
* Ügyféloldali teljesítményt javítását, növelve az olvasási és írási kérelmeket.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Javít egy problémát viselkedésmintáit a munkamenet-tároló nem frissítése a jogkivonattal a sikertelen kérelmek.
* Az SDK-t egy 32-bit-es gazdagép folyamatban lévő munka támogatása. Vegye figyelembe, hogy közötti partíciós lekérdezések használatakor, 64 bites gazdagép feldolgozása ajánlott jobb teljesítmény érdekében.
* Javított teljesítménye nagy számú partíciós kulcs értékeit az IN kifejezés lekérdezések forgatókönyveket.
* A dokumentum egy dokumentumgyűjteményben olvasási kérelmek, ha PopulateQuotaInfo kérelem beállítás értéke a ResourceResponse statisztikái, különböző erőforrás kvóta feltöltve.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* Kisebb teljesítményt javítás 1.11.0 rendszerben bevezetett Createdocumentcollectionasync API-hoz.
* Teljesítmény javítása forgatókönyvekről, egyidejű kérelmek magas fokú az SDK-ban.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Új osztályok és módszerek feldolgozni támogatása a [módosításcsatornáját](change-feed.md) dokumentumok egy gyűjteményen belül.
* Partícióra kiterjedő lekérdezések folytatása és néhány teljesítmény a partícióra kiterjedő lekérdezések támogatása.
* Createdatabaseasync és Createdocumentcollectionasync metódusok hozzáadásával.
* LINQ rendszer funkciók támogatása: IsDefined, IsNull és IsPrimitive.
* Javítsa ki az automatikus binplacing Microsoft.Azure.Documents.ServiceInterop.dll és DocumentDB.Spatial.Sql.dll sestavení alkalmazás bin mappát, vannak olyan eszközök project.json projektek a Nuget-csomag használata esetén.
* Támogatja az ügyfél oldalán ETW-nyomkövetések hibakeresés közben forgatókönyvek hasznosak lehetnek, amelyek kibocsátó.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* A hozzáadott közvetlen kapcsolat támogatja a particionált gyűjtemények.
* A korlátozott frissesség konzisztenciaszint javított teljesítménye.
* A hozzáadott sokszög és LineString adattípusok közben adja meg a gyűjtemény indexelési szabályzat a geokerítés-térinformatikai lekérdezéseket.
* LINQ támogatása StringEnumConverter, IsoDateTimeConverter és UnixDateTimeConverter predikátumok részére történő lefordításakor.
* Különböző SDK-t hibajavításokat tartalmaz.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* A következő NotFoundException okozó probléma kijavítva: az olvasási munkamenet a bemeneti munkamenet-jogkivonat nem érhető el. A kivétel történt bizonyos esetekben egy földrajzilag elosztott fiók olvasási-régió lekérdezésekor.
* A responseStream kimeneti tulajdonság a ResourceResponse osztály, amely lehetővé teszi a közvetlen hozzáférés az alapul szolgáló Stream választ el.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* A megfelelő nyilvános felület megvalósítása, hogy azok tesztelési üzembe helyezés (TDD) alapuló kell mocked ResourceResponse, FeedResponse, StoredProcedureResponse és MediaResponse osztályok módosítani.
* Rögzített egyéni JsonSerializerSettings objektumot használ a szerializálási adatok helytelenül formázott partíció key fejléc kiváltó problémát.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Kijavítva a hiba hosszabb ideig futó lekérdezések okozó: engedélyezési token nem érvényes az aktuális időpontban.
* Kijavítva az eredeti SqlParameterCollection partíció felső /-rendezés lekérdezések közötti eltávolítva.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Particionált gyűjteményeknél végezzen párhuzamos lekérdezések támogatása.
* Támogatás hozzáadva a partíció ORDER BY és a LEGGYAKORIBB lekérdezések a particionált gyűjtemények közötti.
* A hiányzó hivatkozás DocumentDB.Spatial.Sql.dll és Microsoft.Azure.Documents.ServiceInterop.dll meg lesz szükség, ha egy Azure Cosmos DB-projekt hivatkozik egy hivatkozást az Azure Cosmos DB Nuget-csomagot a rögzített.
* Rögzített használhatja a paraméterek különböző típusú LINQ a felhasználó által definiált függvények használata esetén. 
* Globálisan replikált fiókok hibája kijavítva, ahol Upsert-hívások olvasási helyek helyett írási helyek is irányítja.
* Új módszerek a IDocumentClient felületre, amelyek nem szerepeltek: 
  * UpsertAttachmentAsync metódus túlterhelését, amely mediaStream és a paraméterek beállításai
  * CreateAttachmentAsync metódus túlterhelését, amely beállítások paraméterként
  * CreateOfferQuery metódus túlterhelését, amely querySpec paraméterként.
* Lezáratlan nyilvános osztályokat is, amelyeket a IDocumentClient felületén érhetők el.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Többrégiós adatbázisfiókhoz támogatása.
* Támogatás hozzáadva a próbálkozást az szabályozott kérelmeinek száma.  Felhasználó szabhatja testre az újrapróbálkozások számának és a maximális várakozási idő a ConnectionPolicy.RetryOptions tulajdonság beállításával.
* Hozzáadott új IDocumentClient adaptert, amely meghatározza az összes DocumenClient tulajdonságok és metódusok az aláírások.  Ez a változás részeként is módosította, amely IQueryable és IOrderedQueryable létrehozása a metódusokhoz a DocumentClient osztály maga a bővítő metódusokat.
* A hozzáadott konfigurációs beállítást állítsa be a ServicePoint.ConnectionLimit egy adott Azure Cosmos DB végpont URI-t.  ConnectionPolicy.MaxConnectionLimit segítségével módosíthatja az alapértelmezett érték, amely 50 értékre van állítva.
* Elavult IPartitionResolver és a megvalósítás.  IPartitionResolver támogatása elavult. Magasabb tárolási és átviteli sebesség particionált gyűjtemények használata ajánlott.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Hozzáadott URI túlterhelés alapú ExecuteStoredProcedureAsync metódus túlterhelését, amely RequestOptions paramétert.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Dokumentumok élő (TTL) támogatási hozzáadott ideje.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Kijavítva a hiba a .NET SDK Nuget csomagolási becsomagolásához egy Azure Cloud Service megoldás részeként.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Megvalósított [particionált gyűjtemények](partition-data.md) és [felhasználó által definiált teljesítményszintek](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Rögzített]**  Lekérdezése az Azure Cosmos DB végpont jelez: "System.Net.Http.HttpRequestException: Hiba történt a tartalom másolása adatfolyamba".

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* Kibontott LINQ támogatja a lapozófájl, a feltételes kifejezések új operátorokat, és a tartomány összehasonlítása.
  * Válassza ki a felső engedélyezni kívánja a LINQ operátor igénybe
  * Ahhoz, hogy a karakterlánc-összehasonlítások tartomány compareto metódus végrehajtása operátor
  * Feltételes (?), és egyesítse a operátorok (?)
* **[Rögzített]**  : ArgumentOutOfRangeException modell leképezése összevonásakor helyét a LINQ-lekérdezésekre. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Rögzített]**  Ha válassza nem az utolsó kifejezés a LINQ-szolgáltató nincs leképezés feltételezi, és előállított VÁLASSZA * nem megfelelően.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Megvalósított Upsert, hozzáadott UpsertXXXAsync módszerek
* Összes kérelem a teljesítménnyel kapcsolatos fejlesztések
* LINQ szolgáltató támogatása feltételes, coalesce, és karakterláncok módszerek compareto metódus végrehajtása
* **[Rögzített]**  LINQ-szolgáltató--> metódus megvalósítása tartalmaz a listán az azonos SQL létrehozni az IEnumerable illesztőfelületet, illetve tömb
* **[Rögzített]**  BackoffRetryUtility használja ugyanazt a HttpRequestMessage újra egy új újrapróbálkozáskor létrehozása helyett
* **[Elavult]**  UriFactory.CreateCollection--> UriFactory.CreateDocumentCollection használja

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Rögzített]**  Honosítási problémák használatakor nem en kulturális környezet adatai, például nl-NL, és így tovább. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Hozzáadott azonosítóalapú Útválasztás
  * Új UriFactory segítő, amelyek segítik a hozhat létre, azonosító-alapú erőforrás-hivatkozások
  * A DocumentClient lépése URI új túlterheléssel
* Hozzáadott IsValid() és a LINQ a térinformatikai IsValidDetailed()
* Továbbfejlesztett LINQ szolgáltató támogatása:
  * **Matematikai** -Abs, Acos, Asin, Atan, felső határa, Cos, az Exp, emelet, Log, Log10, Pow, ciklikus, bejelentkezési, Sin, Sqrt, Tan, Truncate
  * **Karakterlánc** -Concat, tartalmaz, EndsWith, IndexOf, Count, ToLower, TrimStart, cserélje le, fordított TrimEnd, StartsWith, SubString, ToUpper
  * **Tömb** -Concat, tartalmaz, száma
  * **India** operátor

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Támogatás hozzáadva az indexelési szabályzatok módosításához.
  * A DocumentClient új ReplaceDocumentCollectionAsync metódus
  * Új IndexTransformationProgress tulajdonság ResourceResponse<T> index az irányelvek változásai készültségi állapotának nyomon követéséhez
  * Most már ezekre kapott válaszokon DocumentCollection.IndexingPolicy
* Támogatás hozzáadva a térbeli indexelést és lekérdezést.
  * A térbeli típusok szerializálása vagy deszerializálása új Microsoft.Azure.Documents.Spatial névtéren, például pont és sokszög
  * Új SpatialIndex osztályt GeoJSON, Cosmos DB-ben tárolt adatok indexelése
* **[Rögzített]**  Helytelen SQL-lekérdezés a LINQ kifejezés által létrehozott [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Függőség hozzáadása a Newtonsoft.Json v5.0.7.
* Az Order By támogatásához módosításokat:
  
  * LINQ által támogatott szolgáltatók OrderBy() vagy OrderByDescending()
  * Az Order By támogatásához IndexingPolicy 
    
    **Lehetséges használhatatlanná tévő változást** 
    
    Ha meglévő kódot, hogy rendelkezések gyűjteményeket, amelyek egyéni indexelési házirendet, majd a meglévő kód frissíteni kell az új IndexingPolicy osztály támogatásához. Ha nincs egyéni indexelési házirendet, majd a változás nem befolyásolja.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Támogatás hozzáadva az adatok particionálása az új HashPartitionResolver és RangePartitionResolver osztályok és a IPartitionResolver használatával.
* A hozzáadott DataContract szerializálási.
* Új GUID Azonosítót a LINQ-szolgáltató támogatja.
* Új UDF LINQ támogatja.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release--retirement-dates"></a>Állapot tárolá & kivezetési dátum
A Microsoft biztosít értesítési legalább **12 hónapig** kivonása egy SDK-t kiegyenlítse az a és újabb támogatott verzióra váltás előtt.

Új szolgáltatások és funkciók és optimalizálási lehetőségek csak hozzá az aktuális SDK-hoz, ezért javasoljuk, hogy mindig a legújabb SDK verzióra frissít leghamarabb lehető. 

Az Azure Cosmos DB egy kivont SDK használatával bármilyen kérelmeket a szolgáltatás által a rendszer elutasítja.

<br/>

| Verzió | Kiadás dátuma | Visszavonás dátuma |
| --- | --- | --- |
| [2.0.0-preview2](#2.0.0-preview2) |2018. július 26. |--- |
| [2.0.0-Preview](#2.0.0-preview) |2018. május 11. |--- |
| [1.22.0](#1.22.0) |2018. április 19. |--- |
| [1.21.1](#1.20.1) |2018. március 09. |--- |
| [1.20.2](#1.20.1) |2018. február 21. |--- |
| [1.20.1](#1.20.1) |2018. február 05 |--- |
| [1.19.1](#1.19.1) |2017. november 16. |--- |
| [1.19.0](#1.19.0) |2017. november 10. |--- |
| [1.18.1](#1.18.1) |2017. november 07. |--- |
| [1.18.0](#1.18.0) |2017. október 17. |--- |
| [1.17.0](#1.17.0) |2017. augusztus 10. |--- |
| [1.16.1](#1.16.1) |2017. augusztus 07. |--- |
| [1.16.0](#1.16.0) |2017. augusztus 02 |--- |
| [1.15.0](#1.15.0) |2017. június 30. |--- |
| [1.14.1](#1.14.1) |2017. május 23. |--- |
| [1.14.0](#1.14.0) |2017. május 10. |--- |
| [1.13.4](#1.13.4) |2017. május 09. |--- |
| [1.13.3](#1.13.3) |2017. május 06. |--- |
| [1.13.2](#1.13.2) |2017. április 19. |--- |
| [1.13.1](#1.13.1) |2017. március 29. |--- |
| [1.13.0](#1.13.0) |2017. március 24. |--- |
| [1.12.2](#1.12.2) |2017. március 20. |--- |
| [1.12.1](#1.12.1) |2017. március 14. |--- |
| [1.12.0](#1.12.0) |2017. február 15-én |--- |
| [1.11.4](#1.11.4) |2017. február 06. |--- |
| [1.11.3](#1.11.3) |2017. január 26. |--- |
| [1.11.1](#1.11.1) |2016. december 21-én |--- |
| [1.11.0](#1.11.0) |2016. december 08 |--- |
| [1.10.0](#1.10.0) |2016. szeptember 27. |--- |
| [1.9.5](#1.9.5) |2016. szeptember 01. |--- |
| [1.9.4](#1.9.4) |2016. augusztus 24-én |--- |
| [1.9.3](#1.9.3) |2016. augusztus 15-én |--- |
| [1.9.2](#1.9.2) |2016. július 23. |--- |
| [1.8.0](#1.8.0) |2016. június 14-én |--- |
| [1.7.1](#1.7.1) |2016. május 06. |--- |
| [1.7.0](#1.7.0) |2016. április 26. |--- |
| [1.6.3](#1.6.3) |2016. április 08 |--- |
| [1.6.2](#1.6.2) |2016. március 29-én |--- |
| [1.5.3](#1.5.3) |2016. február 19-én |--- |
| [1.5.2](#1.5.2) |14, 2015. december |--- |
| [1.5.1](#1.5.1) |2015. november 23. |--- |
| [1.5.0](#1.5.0) |2015. október 05 |--- |
| [1.4.1](#1.4.1) |2015. augusztus 25-én |--- |
| [1.4.0](#1.4.0) |13, 2015. augusztus |--- |
| [1.3.0](#1.3.0) |2015. augusztus 05 |--- |
| [1.2.0](#1.2.0) |2015. július 06. |--- |
| [1.1.0](#1.1.0) |2015. április 30. |--- |
| [1.0.0](#1.0.0) |2015. április 08. |--- |


## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
Cosmos DB kapcsolatos további információkért lásd: [a Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapján. 

