---
title: 'Az Azure Cosmos DB: SQL .NET API, SDK & erőforrások'
description: Ismerkedjen meg az SQL .NET API-val és az SDK-val, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat és a Azure Cosmos DB .NET SDK egyes verzióiban végrehajtott módosításokat.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: sngun
ms.openlocfilehash: 8017f02e694f5c9e2cd677c7b1f28c5de973d077
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932573"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET SDK az SQL API-hoz: Megjegyzések letöltése és kibocsátási megjegyzései
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET](sql-api-sdk-dotnet-standard.md)
> * [.NET-módosítási hírcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK letöltése**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**API-dokumentáció**|[.NET API dokumentációja](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Példák**|[.NET platformra írt kódmintái](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples)|
|**Első lépések**|[Ismerkedés a Azure Cosmos DB .NET SDK-val](sql-api-get-started.md)|
|**Alapú webappokról szóló oktatóanyagunkat**|[Webalkalmazás-fejlesztés az Azure Cosmos DB használatával](sql-api-dotnet-application.md)|
|**Aktuális támogatott keretrendszer**|[Microsoft .NET-keretrendszer 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

> [!NOTE]
> Ha a .NET-keretrendszert használja, tekintse meg a .net-szabványnak megfelelő .net [SDK](sql-api-sdk-dotnet-standard.md)legújabb 3. x verzióját. 

### <a name="a-name260260"></a><a name="2.6.0"/>2.6.0

* PortReusePolicy hozzáadva a ConnectionPolicy-hoz
* Rögzített ntdll! RtlGetVersion TypeLoadException probléma, ha az SDK-t egy UWP-alkalmazásban használják

### <a name="a-name251251"></a><a name="2.5.1"/>2.5.1

* Az SDK System .net. http verziója mostantól megegyezik a NuGet csomagban definiált értékkel.
* Egy másik régióba történő írási kérelmek engedélyezése, ha az eredeti hiba meghiúsul.
* Munkamenet-újrapróbálkozási szabályzat hozzáadása írási kérelemhez.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1

* Kijavítja az üres lapokat okozó lekérdezések nyomkövetési versenyhelyzet-feltételeit

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0

* Nagyobb decimális pontossági méret a LINQ-lekérdezéseknél.
* Új osztályok lettek hozzáadva a CompositePath, a CompositePathSortOrder, a SpatialSpec, a SpatialType és a PartitionKeyDefinitionVersion
* TimeToLivePropertyPath hozzáadva a DocumentCollection-hoz
* CompositeIndexes és SpatialIndexes hozzáadva a IndexPolicy-hez
* PartitionKeyDefinition-verzió hozzáadva
* Nincs hozzáadva a PartitionKey

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0

 * A IdleTcpConnectionTimeout, a OpenTcpConnectionTimeout, a MaxRequestsPerTcpConnection és a MaxTcpConnectionsPerEndpoint hozzáadva a ConnectionPolicy.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3

* Diagnosztika fejlesztése

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2.

* Környezeti változó hozzáadva a "POCOSerializationOnly" beállításhoz.

* A DocumentDB. térbeli. SQL. dll fájl el lett távolítva, és már megtalálható a Microsoft. Azure. Documents. ServiceInterop. dll fájlban

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* Az újrapróbálkozási logika fejlesztése a feladatátvétel során a StoredProcedure végrehajtásához.

* A DocumentClientEventSource egyszer készült. 

* Javítsa ki a GatewayAddressCache időtúllépését, és ne tartsa tiszteletben a ConnectionPolicy-RequestTimeout.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* A Direct/TCP Transport Diagnostics szolgáltatáshoz hozzáadott TransportException, az SDK belső kivételének típusa. Ha a kivételben lévő üzenetekben szerepel, ez a típus további információkat jelenít meg az ügyfelek kapcsolódási problémáinak elhárításához.

* Új konstruktor-túlterhelést adott hozzá, amely egy HttpMessageHandler-t, egy HTTP-kezelői veremet használ a HttpClient-kérelmek küldéséhez (például HttpClientHandler).

* Javítsa ki a hibát, hogy a null értékű fejlécek ne legyenek megfelelően kezelve.

* Továbbfejlesztett gyűjtemények gyorsítótárának ellenőrzése.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3

* Frissített System.Net.Security 4.3.2 való.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* A diagnosztikai nyomkövetés fejlesztése

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1

* Többrégiós kérés átmeneti hibák hozzá további rugalmasságának biztosításával.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* A hozzáadott többrégiós támogatási írni.
* Adatbázisközi partíció lekérdezési teljesítménnyel kapcsolatos fejlesztések a TOP és MaxBufferedItemCount.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Új kérelem visszavonás támogatása.
* A hozzáadott SetCurrentLocation való ConnectionPolicy, amely automatikusan feltölti az elsődleges helyek a régióhoz igazodik.
* Kijavítva a hiba a partíció-lekérdezésekben Adatbázisközi minimális/maximális és a egy szűrőt, amely megegyezik az egyetlen dokumentum egyes partíción.
* DocumentClient módszerek IDocumentClient paritás most már rendelkezik.
* Frissített közvetlen TCP átviteli stack létesített kapcsolatok számának csökkentése érdekében.
* Támogatás hozzáadva a közvetlen mód TCP nem Windows-ügyfelek számára.

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
* Kijavítva a hiba, ahol a JsonProperty attribútum a Select záradékban a LINQ-lekérdezésekhez nem lett betartva.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* Bizonyos versenyfeltételek miatt megjelenő rögzített hiba, amely a "Microsoft. Azure. Documents. NotFoundException" időszakot eredményezi: A munkamenet-konzisztencia szintjének használata esetén az olvasási munkamenet nem érhető el a bemeneti munkamenet tokenje számára.

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

* A Microsoft barátok szerelvények belső módosításai.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* Több megbízhatóság javításai és továbbfejlesztései hozzá.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* Egy lekérdezés eredményeit a kulcs-egy adott partíció tartományértéke sorolására FeedOption, PartitionKeyRangeId támogatása. 
* Támogatás hozzáadva a StartTime, hogy elkezdhessük megvizsgálni a módosítások az időnek az elteltével a ChangeFeedOption.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Kijavítva a JsonSerializable osztály, amely a stack overflow kivételt okozhat.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   Kijavítva egy olyan problémát, amely szükséges az alkalmazás újrafordításához a JsonSerializerSettings a DocumentClient konstruktorban választható paramétereként való bevezetése miatt.
* A DocumentClient konstruktor elavultként jelölte meg, hogy a kötelező JsonSerializerSettings az utolsó paraméterként, amely lehetővé teszi a ConnectionPolicy és a ConsistencyLevel paraméterek alapértelmezett értékeit a JsonSerializerSettings paraméterben való továbbításkor.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Az egyéni JsonSerializerSettings megadásának támogatása a [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet)példányának használata közben.

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Kijavított egy olyan problémát, amely a SSE4 utasítást nem támogató x64-es gépeket érintette, és Azure Cosmos DB SQL-lekérdezések futtatásakor dob egy SEHException.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Egy új konzisztenciaszint támogatása az új ConsistentPrefix nevezik.
*   Az egyes partíciók lekérdezési metrikák támogatása.
*   Támogatás hozzáadva a lekérdezések a folytatási méretét korlátozó.
*   Sikertelen kérelmek nyomkövetésének részletesebb támogatása.
*   Bizonyos teljesítménnyel kapcsolatos fejlesztések készült SDK-ban.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Ugyanúgy működik, mint a 1.13.3. Némi belső módosítás történt.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Ugyanúgy működik, mint a 1.13.2. Némi belső módosítás történt.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Kijavítva, hogy a rendszer figyelmen kívül hagyja a PartitionKey értéket FeedOptions megadott összesítő lekérdezésekhez.
* Javítva lett egy probléma transzparens kezelését a partíció felügyeleti közepes repülési a partíciók közti Order By záradék a lekérdezés végrehajtása során.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Rögzített holtpontok az API-k, az ASP.NET-környezetben való használat async néhány okozó problémát.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Javít, hogy rugalmasabb SDK bizonyos körülmények között az automatikus feladatátvételre.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Javítsa ki a problémát, amely esetenként egy webszolgáltatást okoz: A távoli név nem oldható fel.
* A beírt dokumentumok közvetlenül olvasásakor ReadDocumentAsync API új hozzáadásával támogatása hozzáadva.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* LINQ támogatása összesítési lekérdezések (száma, MIN, MAX, SUM és átlagos).
* Javítás a memóriavesztés az oka eseménykezelő ConnectionPolicy objektum számára.
* Javít egy problémát UpsertAttachmentAsync viselkedésmintáit nem dolgozott ETag használatakor.
* Javítsa ki egy problémát a viselkedésmintáit közötti partíció az order by lekérdezések folytatása nem dolgozott rendezésekor karakterlánc típusú.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Összesítés lekérdezések (száma, MIN, MAX, SUM és átlagos) támogatása. Lásd: [összesítési támogatási](sql-query-aggregates.md).
* Süllyesztett minimálisan 2500 RU/s 10,100 RU/s a particionált gyűjtemények átviteli sebességet.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Javítsa ki azt a problémát, amelyben a több partíciós lekérdezés végrehajtása sikertelen volt a 32 bites gazdagép-folyamat során.
* Javítsa ki azt a problémát, amelyben a munkamenet-tároló nem frissült az átjáró módban sikertelen kérések jogkivonatával.
* Javítsa ki azt a problémát, amelyben a kivetítésben UDF-hívásokat tartalmazó lekérdezés bizonyos esetekben sikertelen volt.
* Az ügyféloldali teljesítmény megjavítja a kérelmek olvasási és írási sebességének növelését.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Javítsa ki azt a problémát, amelyben a munkamenet-tároló nem frissült a sikertelen kérelmek jogkivonatával.
* Az SDK támogatása egy 32 bites gazdagép-folyamaton való működéshez. Vegye figyelembe, hogy ha több partíciós lekérdezést használ, a jobb teljesítmény érdekében ajánlott a 64 bites gazdagép-feldolgozás.
* Jobb teljesítmény olyan forgatókönyvek esetében, amelyek nagy számú partíciós kulccsal rendelkező lekérdezéseket tartalmaznak egy IN kifejezésben.
* Különböző erőforrás-kvóták statisztikáinak feltöltése a ResourceResponse az olvasási kérelmek esetében, ha be van állítva a PopulateQuotaInfo kérése beállítás.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* A 1.11.0-ben bevezetett Createdocumentcollectionifnotexistsasync metódusával API kisebb teljesítményű javítása.
* A teljesítmény javítása az SDK-ban olyan forgatókönyvek esetében, amelyek nagy mértékben egyidejű kérelmeket foglalnak magukban.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Új osztályok és metódusok támogatása a dokumentumok egy [](change-feed.md) gyűjteményen belüli változási csatornájának feldolgozásához.
* A több partíciós lekérdezések folytatásának támogatása és a több partíciós lekérdezések teljesítményének fejlesztése.
* Createdatabaseifnotexistasync metódusának és Createdocumentcollectionifnotexistsasync metódusával metódusok hozzáadása.
* A System functions LINQ-támogatása: IsDefined, IsNull és IsPrimitive.
* Javítsa ki a Microsoft. Azure. Documents. ServiceInterop. dll és a DocumentDB. térbeli. SQL. dll-szerelvények automatikus binplacing az alkalmazás bin mappájába, amikor a Nuget-csomagot a Project. JSON eszközzel rendelkező projektekkel használja.
* Ügyféloldali ETW-Nyomkövetések kibocsátásának támogatása, amelyek hasznosak lehetnek a hibakeresési helyzetekben.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Közvetlen kapcsolódási támogatás hozzáadva a particionált gyűjtemények számára.
* Javított teljesítmény a megkötött elavult konzisztencia-szintnél.
* A hozzáadott sokszög és LineString adattípusok közben adja meg a gyűjtemény indexelési szabályzat a geokerítés-térinformatikai lekérdezéseket.
* LINQ-támogatás hozzáadva a StringEnumConverter, a IsoDateTimeConverter és a UnixDateTimeConverterhoz a predikátumok lefordítása során.
* Különböző SDK-hibajavítások.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Kijavított egy problémát, amely a következő NotFoundException okozta: Az olvasási munkamenet nem érhető el a bemeneti munkamenet tokenje számára. Ez a kivétel bizonyos esetekben egy földrajzilag elosztott fiók olvasási régiójának lekérdezésekor fordul elő.
* A ResourceResponse osztályban elérhetővé teszi a ResponseStream tulajdonságot, amely közvetlen hozzáférést biztosít az alapul szolgáló adatfolyamhoz a válaszból.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Módosította a ResourceResponse, a FeedResponse, a StoredProcedureResponse és a MediaResponse osztályt a megfelelő nyilvános felület megvalósításához, hogy a tesztelésen alapuló üzembe helyezéshez (TDD) legyenek kialakítva.
* Kijavított egy olyan problémát, amely egy helytelenül formázott partíciós fejlécet okozott az adatszerializáláshoz használt egyéni JsonSerializerSettings-objektum használatakor.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Kijavított egy hibát, amely miatt a hosszú ideig futó lekérdezések sikertelenek voltak: Az engedélyezési jogkivonat az aktuális időpontban nem érvényes.
* Kijavított egy problémát, amely eltávolította az eredeti SqlParameterCollection a több partíciós Top/Order-by lekérdezésekből.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Párhuzamos lekérdezések támogatása a particionált gyűjteményekhez.
* A több partíciós megrendelés és a particionált gyűjtemények leggyakoribb lekérdezésének támogatása.
* Rögzítette a hiányzó hivatkozásokat a DocumentDB. térbeli. SQL. dll és a Microsoft. Azure. Documents. ServiceInterop. dll fájlra, amelyekre szükség van, amikor egy Azure Cosmos DB-projektre hivatkozik a Azure Cosmos DB Nuget-csomagra mutató hivatkozással.
* Kijavítottuk a különböző típusú paraméterek paramétereinek használatát, amikor felhasználó által definiált függvényeket használ a LINQ-ben. 
* Kijavítva egy hiba a globálisan replikált fiókok esetében, ahol a Upsert-hívások írási hely helyett olvasási helyekre vannak irányítva.
* A IDocumentClient felülethez hozzáadott metódusok lettek megadva: 
  * UpsertAttachmentAsync metódus, amely mediaStream és beállításokat fogad paraméterként
  * CreateAttachmentAsync metódus, amely a paramétereket paraméterként veszi igénybe
  * CreateOfferQuery metódus, amely paraméterként a querySpec.
* Lezáratlan nyilvános osztályok, amelyek ki vannak téve a IDocumentClient felületén.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Többrégiós adatbázisfiókhoz támogatása.
* Az újrapróbálkozások támogatása a szabályozott kérelmek esetében.  A felhasználó testreszabhatja az újrapróbálkozások számát és a maximális várakozási időt a ConnectionPolicy. RetryOptions tulajdonság konfigurálásával.
* Új IDocumentClient-felület lett hozzáadva, amely meghatározza az összes DocumentClient-tulajdonság és-metódus aláírását.  Ennek a változásnak a részeként olyan bővítmény-metódusok is módosultak, amelyek IQueryable és IOrderedQueryable hoznak létre a DocumentClient osztály metódusai alapján.
* Konfigurációs beállítás hozzáadva a ServicePoint. ConnectionLimit egy adott Azure Cosmos DB végponti URI azonosítóhoz.  Az ConnectionPolicy. MaxConnectionLimit használatával módosítsa az alapértelmezett értéket, amely 50 értékre van beállítva.
* Elavult IPartitionResolver és annak implementálása.  A IPartitionResolver támogatása már elavult. Azt javasoljuk, hogy particionált gyűjtemények használatával nagyobb tárterületet és átviteli sebességet használjon.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Túlterhelést adott az URI-alapú ExecuteStoredProcedureAsync metódushoz, amely paraméterként a RequestOptions-t veszi igénybe.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Dokumentumok élő (TTL) támogatási hozzáadott ideje.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Egy Azure Cloud Service-megoldás részeként a .NET SDK Nuget csomagolása hibát rögzített.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Megvalósított [particionált gyűjtemények](partition-data.md) és [felhasználó által definiált teljesítményszintek](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Kijavítva]** Azure Cosmos DB végpontok lekérdezése: 'System.Net.Http.HttpRequestException: Hiba történt a tartalom adatfolyamba való másolása közben.

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* Bővített LINQ-támogatás, beleértve az új operátorokat a lapozáshoz, a feltételes kifejezésekhez és a tartomány-összehasonlításhoz.
  * Válassza ki az operátort a legjobb viselkedés kiválasztásának engedélyezéséhez a LINQ-ben
  * Compareto metódus végrehajtása operátor a karakterlánc-tartomány összehasonlításának engedélyezéséhez
  * Feltételes (?) és egyesítő operátorok (??)
* **[Kijavítva]** ArgumentOutOfRangeException a modell kivetítésének kombinálása egy LINQ-lekérdezésben. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Kijavítva]** Ha a Select * nem az utolsó kifejezés, akkor a LINQ-szolgáltató nem feltételezi, hogy nincs kivetítés és nem megfelelő.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Megvalósított Upsert, hozzáadott UpsertXXXAsync metódusok
* Az összes kérelem teljesítményének fejlesztése
* A LINQ Provider támogatja a karakterláncok feltételes, egyesítési és Compareto metódus végrehajtása módszereit
* **[Kijavítva]** LINQ Provider – > megvalósítás metódust tartalmaz a listáról, hogy ugyanazt az SQL-t hozza létre, mint a IEnumerable és a Array
* **[Kijavítva]** A BackoffRetryUtility ugyanazt a HttpRequestMessage használja újra, ahelyett, hogy újat hozna létre újra
* **[Elavult]** UriFactory. CreateCollection – > a UriFactory. CreateDocumentCollection használatát kell használnia

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Kijavítva]** Honosítási problémák a nem en kulturális adatok, például az nl-NL használata esetén. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* AZONOSÍTÓ-alapú útválasztás hozzáadva
  * Új UriFactory segítő, amely segítséget nyújt az azonosító-alapú erőforrás-hivatkozások létrehozásában
  * Új túlterhelések a DocumentClient az URI-n való elvégzéséhez
* Hozzáadott IsValid () és IsValidDetailed () a "LINQ for térinformatika" esetében
* A LINQ Provider támogatása Továbbfejlesztve:
  * **Math** -ABS, ACO, ASIN, Atan, felső, cos, exp, floor, log, Log10, Pow, Round, Sign, Sin, SQRT, Tan, csonkítás
  * **String** -concat, tartalmaz, EndsWith, IndexOf, Count, ToLower, TrimStart, replace, reverse, TrimEnd, StartsWith, alstring, ToUpper
  * **Tömb** – Összefűzés, tartalmazza, darabszám
  * **Az** operátorban

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Az indexelési házirendek módosításának támogatása.
  * Új ReplaceDocumentCollectionAsync metódus a DocumentClient-ben
  * A ResourceResponse\<> T új IndexTransformationProgress tulajdonsága, amely nyomon követi az index-házirendek változásainak százalékos előrehaladását
  * A DocumentCollection. IndexingPolicy mostantól változékony
* További támogatás a térbeli indexeléshez és a lekérdezéshez.
  * Új Microsoft. Azure. Documents. térbeli névtér a térbeli típusok, például a pont és a sokszög szerializálásához/deszerializálásához
  * Új SpatialIndex-osztály a Cosmos DBban tárolt GeoJSON-adatértékek indexeléséhez
* **[Kijavítva]** Nem megfelelő SQL-lekérdezés lett létrehozva egy LINQ kifejezésből [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Függőség hozzáadva a Newtonsoft. JSON v 5.0.7.
* A következő módosításokat hajtotta végre a támogatási sorrend szerint:
  
  * LINQ Provider-támogatás a OrderBy () vagy a OrderByDescending () szolgáltatáshoz
  * IndexingPolicy az Order by 
    
    **Lehetséges megszakítási változás** 
    
    Ha meglévő kóddal rendelkezik, amely egyéni indexelési házirenddel rendelkező gyűjteményeket tartalmaz, akkor a meglévő kódot frissíteni kell az új IndexingPolicy osztály támogatásához. Ha nem rendelkezik egyéni indexelési házirenddel, akkor ez a változás nem érinti Önt.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Az új HashPartitionResolver és RangePartitionResolver osztályok és a IPartitionResolver használatával az adatparticionálás támogatása is támogatott.
* DataContract szerializálás hozzáadva.
* GUID-támogatás hozzáadva a LINQ providerben.
* UDF-támogatás hozzáadva a LINQ-ben.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release--retirement-dates"></a>Kiadási & nyugdíjazási dátumok
A Microsoft legalább **12 hónappal** korábban értesítést küld az SDK kivonásáról, hogy zökkenőmentes legyen az áttérés egy újabb/támogatott verzióra.

Az új funkciók és funkciók és optimalizálás csak a jelenlegi SDK-hoz adódik hozzá, ezért azt javasoljuk, hogy a lehető leghamarabb frissítsen a legújabb SDK-verzióra. 

A szolgáltatás elutasítja a kivont SDK használatával Azure Cosmos DB kérelmeket.

> [!WARNING]
> Az SQL API-hoz készült .NET SDK összes verziójának **1. x** verziója a **2020-es augusztus 30-ig**megszűnik.
> 
>
<br/>

| Version | Kiadás dátuma | Visszavonás dátuma |
| --- | --- | --- |
| [2.6.0](#2.6.0) |2019. augusztus 30-ig |--- |
| [2.5.1](#2.5.1) |Július 02, 2019 |--- |
| [2.4.1](#2.4.1) |2019. június 20. |--- |
| [2.4.0](#2.4.0) |Május 05., 2019 |--- |
| [2.3.0](#2.3.0) |Április 04., 2019 |--- |
| [2.2.3](#2.2.3) |Február 11., 2019 |--- |
| [2.2.2](#2.2.2) |Február 06, 2019 |--- |
| [2.2.1](#2.2.1) |December 24., 2018 |--- |
| [2.2.0](#2.2.0) |December 07, 2018 |--- |
| [2.1.3](#2.1.3) |2018. október 15. |--- |
| [2.1.2](#2.1.2) |2018. október 04. |--- |
| [2.1.1](#2.1.1) |2018. szeptember 27. |--- |
| [2.1.0](#2.1.0) |2018. szeptember 21. |--- |
| [2.0.0](#2.0.0) |2018. szeptember 07. |--- |
| [1.22.0](#1.22.0) |2018. április 19. | 2020. augusztus 30-ig |
| [1.21.1](#1.20.1) |2018. március 09. |2020. augusztus 30-ig |
| [1.20.2](#1.20.1) |2018. február 21. |2020. augusztus 30-ig |
| [1.20.1](#1.20.1) |2018. február 05 |2020. augusztus 30-ig |
| [1.19.1](#1.19.1) |2017. november 16. |2020. augusztus 30-ig |
| [1.19.0](#1.19.0) |2017. november 10. |2020. augusztus 30-ig |
| [1.18.1](#1.18.1) |November 07., 2017 |2020. augusztus 30-ig |
| [1.18.0](#1.18.0) |2017. október 17. |2020. augusztus 30-ig |
| [1.17.0](#1.17.0) |2017. augusztus 10. |2020. augusztus 30-ig |
| [1.16.1](#1.16.1) |2017. augusztus 07. |2020. augusztus 30-ig |
| [1.16.0](#1.16.0) |2017. augusztus 02 |2020. augusztus 30-ig |
| [1.15.0](#1.15.0) |Június 30., 2017 |2020. augusztus 30-ig |
| [1.14.1](#1.14.1) |2017. május 23. |2020. augusztus 30-ig |
| [1.14.0](#1.14.0) |2017. május 10. |2020. augusztus 30-ig |
| [1.13.4](#1.13.4) |Május 09., 2017 |2020. augusztus 30-ig |
| [1.13.3](#1.13.3) |Május 06., 2017 |2020. augusztus 30-ig |
| [1.13.2](#1.13.2) |2017. április 19. |2020. augusztus 30-ig |
| [1.13.1](#1.13.1) |2017. március 29. |2020. augusztus 30-ig |
| [1.13.0](#1.13.0) |Március 24., 2017 |2020. augusztus 30-ig |
| [1.12.2](#1.12.2) |2017. március 20. |2020. augusztus 30-ig |
| [1.12.1](#1.12.1) |2017. március 14. |2020. augusztus 30-ig |
| [1.12.0](#1.12.0) |Február 15., 2017 |2020. augusztus 30-ig |
| [1.11.4](#1.11.4) |Február 06, 2017 |2020. augusztus 30-ig |
| [1.11.3](#1.11.3) |2017. január 26. |2020. augusztus 30-ig |
| [1.11.1](#1.11.1) |2016. december 21-én |2020. augusztus 30-ig |
| [1.11.0](#1.11.0) |December 08., 2016 |2020. augusztus 30-ig |
| [1.10.0](#1.10.0) |Szeptember 27., 2016 |2020. augusztus 30-ig |
| [1.9.5](#1.9.5) |Szeptember 01., 2016 |2020. augusztus 30-ig |
| [1.9.4](#1.9.4) |Augusztus 24, 2016 |2020. augusztus 30-ig |
| [1.9.3](#1.9.3) |Augusztus 15., 2016 |2020. augusztus 30-ig |
| [1.9.2](#1.9.2) |2016. július 23. |2020. augusztus 30-ig |
| [1.8.0](#1.8.0) |2016. június 14-én |2020. augusztus 30-ig |
| [1.7.1](#1.7.1) |Május 06., 2016 |2020. augusztus 30-ig |
| [1.7.0](#1.7.0) |Április 26., 2016 |2020. augusztus 30-ig |
| [1.6.3](#1.6.3) |2016. április 08. |2020. augusztus 30-ig |
| [1.6.2](#1.6.2) |2016. március 29-én |2020. augusztus 30-ig |
| [1.5.3](#1.5.3) |Február 19., 2016 |2020. augusztus 30-ig |
| [1.5.2](#1.5.2) |December 14., 2015 |2020. augusztus 30-ig |
| [1.5.1](#1.5.1) |November 23., 2015 |2020. augusztus 30-ig |
| [1.5.0](#1.5.0) |2015. október 05 |2020. augusztus 30-ig |
| [1.4.1](#1.4.1) |Augusztus 25., 2015 |2020. augusztus 30-ig |
| [1.4.0](#1.4.0) |Augusztus 13., 2015 |2020. augusztus 30-ig |
| [1.3.0](#1.3.0) |2015. augusztus 05 |2020. augusztus 30-ig |
| [1.2.0](#1.2.0) |Július 06., 2015 |2020. augusztus 30-ig |
| [1.1.0](#1.1.0) |Április 30., 2015 |2020. augusztus 30-ig |
| [1.0.0](#1.0.0) |2015. április 08. | 2020. augusztus 30-ig |


## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
Cosmos DB kapcsolatos további információkért lásd: [a Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapján. 

