---
title: "Azure Cosmos DB: SQL .NET API-t, az SDK és erőforrások |} Microsoft Docs"
description: "Tudnivalók az SQL .NET API és SDK, beleértve a kiadási dátum, használatból való kivonást dátumok és az Azure Cosmos DB .NET SDK verziói között végrehajtott módosításokat."
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 8e239217-9085-49f5-b0a7-58d6e6b61949
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/09/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5dd2fb353246120f58840914e8a4e5527d7a4486
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Az Azure Cosmos DB .NET SDK API-SQL: Töltse le és a kibocsátási megjegyzések
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítás adatcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

<table>

<tr><td>**SDK letöltése**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>

<tr><td>**API-JÁNAK dokumentációja**</td><td>[.NET API-referenciadokumentáció](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Minták**</td><td>[.NET-Kódminták](sql-api-dotnet-samples.md)</td></tr>

<tr><td>**Első lépések**</td><td>[Ismerkedés az Azure Cosmos DB .NET SDK-val](sql-api-get-started.md)</td></tr>

<tr><td>**Webes alkalmazás oktatóanyag**</td><td>[Webalkalmazás fejlesztése a Azure Cosmos DB](sql-api-dotnet-application.md)</td></tr>

<tr><td>**Aktuális támogatott keretrendszer**</td><td>[Microsoft .NET-keretrendszer 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Kibocsátási megjegyzések
### <a name="a-name12111211"></a><a name="1.21.1"/>1.21.1

* A KeyNotFoundException közötti partíció rendelés javította esetekben lévő lekérdezések.
* Rögzített hiba, ha a select záradékban a LINQ-lekérdezések JsonPropery attribútum lett nem alatt figyelembe véve.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* Rögzített, hibát talált az egyes versenyhelyzetek, amely időszakos eredményez "Microsoft.Azure.Documents.NotFoundException: az olvasási munkamenet nem érhető el a bemeneti munkameneti jogkivonat" hibák munkamenet konzisztenciaszint használatakor.

### <a name="a-name12011201"></a><a name="1.20.1"/>1.20.1

* Rögzített regressziós ahol FeedOptions.MaxItemCount = -1 egy System.ArithmeticException kivételt okozott: méretet a rendszer negatív.
* Egy új ToString() függvényét QueryMetrics hozzá.
* A gyűjtemények olvasása a partíció statisztikai adatainak kitéve.
* ChangeFeedOptions hozzáadott PartitionKey tulajdonság.
* Kisebb hibajavítások.

### <a name="a-name11911191"></a><a name="1.19.1"/>1.19.1

* Hozzáadja a meghatározhatja, hogy a dokumentumok egyedi indexet a DocumentCollection UniqueKeyPolicy tulajdonság használatával.
* Rögzített, amelyben az egyéni JsonSerializer beállítások volt nem alatt figyelembe véve az egyes lekérdezések és a tárolt eljárás végrehajtása hiba.

### <a name="a-name11901190"></a><a name="1.19.0"/>1.19.0

* Márkajelzési Azure Cosmos Adatbázist az API-referencia az Azure DocumentDB változásait, dokumentáció, szerelvényekben metaadat-információkat és a NuGet-csomagot. 
* Diagnosztikai adatok és a késleltetés közvetlen kapcsolatot módban küldött kérelmek a válaszban szereplő tenni. A tulajdonságnevek megkülönböztetik RequestDiagnosticsString és RequestLatency ResourceResponse osztályban.
* Az SDK-verzió Azure Cosmos DB emulátorának elérhetősége a legújabb verziója szükséges a letölthető https://aka.ms/cosmosdb-emulator. 

### <a name="a-name11811181"></a><a name="1.18.1"/>1.18.1 

* Belső módosításainak Microsoft ismerősök szerelvényeket.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* Több megbízhatóság javításokat és fejlesztések hozzá.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* Mint a lekérdezési eredmények kulcs adott partíció tartományértéke hatókörének egy FeedOption PartitionKeyRangeId támogatása. 
* StartTime, keressen a módosítások idő lejárta után egy ChangeFeedOption támogatása.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Rögzített problémát okozhat a verem túlcsordulásos kivétel JsonSerializable osztályból.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   Megtörtént egy probléma javítása, az alkalmazás, egy nem kötelező paraméter a DocumentClient konstruktorban JsonSerializerSettings bevezetése miatt újrafordítás szükségesek.
* A DocumentClient konstruktor elavultként megjelölve, hogy az utolsó paraméter alapértelmezett értéke ConnectionPolicy lehetővé és ConsistencyLevel paraméterek szükséges JsonSerializerSettings történő átadásakor JsonSerializerSettings paraméterben.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Támogatása az egyéni JsonSerializerSettings megadása közben példányának [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Rögzített x64 érintő problémát gépek, amelyek nem támogatják a SSE4 utasítást, és egy SEHException throw Azure Cosmos adatbázis SQL-lekérdezések futtatásakor.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Egy új konzisztenciaszint támogatása ConsistentPrefix nevezik.
*   Az egyes partíciók lekérdezési metrikák támogatása.
*   Támogatja a lekérdezések a folytatási kód mérete korlátozza.
*   Sikertelen kérelmek nyomkövetése részletesebb támogatása.
*   Az SDK-val végzett néhány teljesítménynövekedést.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Gyakorlatilag ugyanaz, mint a 1.13.3. Néhány belső módosítja.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Gyakorlatilag ugyanaz, mint a 1.13.2. Néhány belső módosítja.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Megtörtént egy probléma javítása, amely figyelmen kívül hagyta a megadott FeedOptions összesített lekérdezések PartitionKey érték.
* Megtörtént egy probléma javítása átlátszó kezelésében, partíció felügyeleti közepes repülési kereszt-partíció Order By lekérdezés végrehajtása közben.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Rögzített egyes az async API-k használatakor az ASP.NET környezet belül holtpont okozó problémát.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Kijavítja a SDK rugalmasabb, így az Automatikus feladatátvétel bizonyos körülmények között.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Javítsa ki a WebException alkalmanként okozó hibát: A távoli név nem sikerült feloldani.
* Támogatása a típusos dokumentumok közvetlenül olvasásakor ReadDocumentAsync API új túlterhelések hozzáadásával.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* LINQ támogatása (COUNT, MIN, MAX, SUM és átlagos) összesítési lekérdezések.
* Javítsa ki a problémát az az oka eseménykezelő ConnectionPolicy objektum.
* Javítsa ki a hibát, amelynek UpsertAttachmentAsync lett nem működik ETag használatakor.
* Javítsa ki a hibát, amelynek több partíciót az order by lekérdezés folytatási nem dolgozott rendezéskor karakterláncmező.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Összesítési lekérdezéseket (COUNT, MIN, MAX, SUM és átlagos) támogatása. Lásd: [összesítési támogatási](sql-api-sql-query.md#Aggregates).
* Minimális átviteli sebességet 2500 RU/mp 10,100 RU/mp a particionált gyűjtemények szintűre csökkent.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Javítsa ki a hibát, amelynek a kereszt-partíció lekérdezések némelyike nem került sor a 32 bites állomás folyamatban.
* Javítsa ki a hibát, amelynek a munkamenet-tároló nem frissítése a tokenhez tartozó átjáró módban.
* Javítsa ki a hibát, amelynek UDF hívások leképezése a lekérdezés nem működött, bizonyos esetekben.
* Ügyféloldali teljesítményt javítások növelésében az olvasási és írási kérelmeket.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Javítsa ki a hibát, amelynek a munkamenet-tároló nem frissítése a sikertelen kérelmek lexikális eleme.
* Az SDK 32 bites gazdafolyamat működni támogatása. Vegye figyelembe, hogy több partíció lekérdezések használatakor, 64 bites állomás feldolgozása ajánlott javítja a teljesítményt.
* Javítja a teljesítményt nagy számú partíciós kulcs értékét IN kifejezésben lekérdezések érintő forgatókönyvek.
* Az olvasási kérésekre, amikor PopulateQuotaInfo kérelem beállítás dokumentumgyűjteményt ResourceResponse a különböző erőforrás kvótájának statisztikák feltöltve.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* Kis teljesítmény javítása a 1.11.0 bevezetett CreateDocumentCollectionIfNotExistsAsync API.
* Teljesítmény javítás az SDK az egyidejű kérelmek magas fokú forgatókönyveit.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Támogatja az új osztályok és feldolgozni módszereket a [adatcsatorna módosítása](change-feed.md) dokumentumok egy gyűjteményen belül.
* Kereszt-partíciólekérdezés folytatási és néhány teljesítmény fejleszthetjük tovább a kereszt-partíció lekérdezések támogatása.
* CreateDatabaseIfNotExistsAsync és CreateDocumentCollectionIfNotExistsAsync metódusok hozzáadásával.
* LINQ rendszer funkciók támogatása: IsDefined, IsNull és IsPrimitive.
* Javítsa ki az automatikus binplacing Microsoft.Azure.Documents.ServiceInterop.dll és DocumentDB.Spatial.Sql.dll szerelvények az alkalmazás bin mappát, amelyek project.json tooling projektek a Nuget-csomag használata esetén.
* Támogatja az ügyfél oldali ETW nyomkövetési adatokat a hibakeresés forgatókönyvek hasznosak lehetnek, amelyek kibocsátó.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* A particionált gyűjtemények hozzáadott közvetlen kapcsolat támogatása.
* A kötött elavulási konzisztencia szint javítja a teljesítményt.
* A hozzáadott sokszög és LineString adattípusok indexelő házirend geokerítések térbeli lekérdezéseket a gyűjtemény meg.
* LINQ támogatása StringEnumConverter, IsoDateTimeConverter és UnixDateTimeConverter predikátumok részére történő lefordításakor.
* Különböző SDK hibajavításokat tartalmaz.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Megtörtént egy probléma javítása, ami miatt a következő NotFoundException: az olvasási munkamenet nem érhető el a bemeneti munkameneti jogkivonat. A kivétel történt bizonyos esetekben egy földrajzilag elosztott fiók olvasás-régió lekérdezésekor.
* A ResponseStream tulajdonság a ResourceResponse osztály, amely lehetővé teszi a közvetlen hozzáférés az alapul szolgáló adatfolyamba választ kitéve.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* A megfelelő nyilvános felület megvalósítása, így azokat a központi telepítés (TDD) alapú teszteléséhez kell mocked ResourceResponse, FeedResponse, StoredProcedureResponse és MediaResponse osztályok módosítani.
* Rögzített egyéni JsonSerializerSettings objektumot a szerializálási adatok használata esetén egy nem megfelelően formázott partíció kulcs fejléc okozó problémát.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Megtörtént egy probléma javítása, amelyek hosszú ideig futó lekérdezések sikertelen a következő hiba miatt: engedélyezési lexikális elem nem érvényes az aktuális időpontban.
* Megtörtént egy probléma javítása, az eredeti SqlParameterCollection közötti partíció felső/szerint lekérdezések megszűnt.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Támogatja a particionált gyűjtemények párhuzamos lekérdezések.
* Támogatja a particionált gyűjtemények ORDER BY és a felső lekérdezések partíció közötti.
* A hiányzó hivatkozások DocumentDB.Spatial.Sql.dll és Microsoft.Azure.Documents.ServiceInterop.dll által igényelt való hivatkozáskor az Azure Cosmos DB-projektek az Azure Cosmos DB Nuget-csomag hivatkozást rögzített.
* Rögzített különböző típusú paramétereket használhatja, amikor a felhasználó által definiált függvények LINQ használatával. 
* Rögzített globálisan replikált fiókok programhiba, ahol Upsert hívások olvassa el a helyek helyett írási helyek volt irányítja.
* A hozzáadott módszerek a IDocumentClient felületre hiányzik: 
  * UpsertAttachmentAsync metódus azon definícióváltozatát, mediaStream és paraméterekként beállítások
  * CreateAttachmentAsync metódus azon definícióváltozatát, beállítások, mint egy paraméter
  * CreateOfferQuery metódus, amely querySpec paramétert fogad.
* Lezáratlan nyilvános osztályok, a IDocumentClient felületen elérhetővé tett tárolókra.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Több területi adatbázis fiókok támogatása.
* Próbálkozzon újra a szabályozottan halmozott kérelmek támogatása.  Felhasználó a ConnectionPolicy.RetryOptions tulajdonság megadásával testre szabhatja az újrapróbálkozások száma és a maximális várakozási idő.
* Egy új IDocumentClient felületet, amely meghatározza az összes DocumenClient tulajdonságai és metódusai aláírásai hozzá.  Ez a változás részeként, hogy az IQueryable és IOrderedQueryable létesíthet a DocumentClient osztály metódusai kiterjesztésmetódusok megváltozik.
* A hozzáadott konfigurációs beállítást egy adott Azure Cosmos DB-végpont Uri ServicePoint.ConnectionLimit beállításához.  ConnectionPolicy.MaxConnectionLimit segítségével módosíthatja az alapértelmezett érték 50 értékre van állítva.
* Elavult IPartitionResolver és a megvalósítása.  IPartitionResolver támogatása elavult. Magasabb tárolási és átviteli particionált gyűjtemények használata ajánlott.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Hozzáadott URI túlterhelés alapú ExecuteStoredProcedureAsync metódus azon definícióváltozatát, RequestOptions paramétert.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* A hozzáadott idő live (TTL)-támogatás a dokumentumok.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* A .NET SDK Nuget csomagban programhiba rögzíteni csomagolás egy Azure Cloud Service megoldás részeként.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Megvalósított [particionált gyűjtemények](partition-data.md) és [felhasználói teljesítményszintet](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Rögzített]**  Lekérdezése Azure Cosmos DB végpont jelez: "System.Net.Http.HttpRequestException: Hiba történt egy adatfolyamba történő tartalommásoláskor".

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* Bővített LINQ támogatja, beleértve az új operátorok lapozási, a feltételes kifejezések és összehasonlító között.
  * Engedélyezni a felső válassza ki a "LINQ" operátor igénybe
  * CompareTo operátor karakterlánc tartomány összehasonlítása
  * Feltételes (?) és a coalesce operátorok (?)
* **[Rögzített]**  ArgumentOutOfRangeException modell leképezése egyesítésekor Where-a LINQ lekérdezés. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Rögzített]**  Ha válassza nincs utolsó kifejezés a LINQ szolgáltatónál feltételezve, hogy nincs leképezés, és válasszon előállított * nem megfelelő.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Megvalósított Upsert, UpsertXXXAsync hozzáadott metódusok
* Minden olyan kérelem esetében a teljesítménnyel kapcsolatos fejlesztések
* LINQ szolgáltatónál támogatása feltételes, coalesce, és karakterláncokat CompareTo módszerei
* **[Rögzített]**  LINQ-szolgáltató--> metódus megvalósítása tartalmaz listán az azonos SQL létrehozásához az IEnumerable és tömb
* **[Rögzített]**  BackoffRetryUtility használja az ugyanazon HttpRequestMessage újra egy új újrapróbálkozáskor létrehozása helyett
* **[Elavult]**  UriFactory.CreateCollection--> UriFactory.CreateDocumentCollection használja

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Rögzített]**  Honosítási ad ki, ha nem en kulturális környezet adatokkal például nl-NL stb. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Azonosító-alapú útválasztási hozzáadva
  * Új UriFactory segítő segít hozhat létre, azonosító-alapú erőforrás-hivatkozások
  * Az URI érvénybe DocumentClient be új túlterhelések
* A hozzáadott IsValid() és a földrajzi LINQ IsValidDetailed()
* Fokozott LINQ szolgáltatónál támogatják:
  * **Matematikai** -Abs, ARCCOS, ARCSIN, Atan Cos Exp, emelet, napló, Log10, Pow, ciklikus, bejelentkezési, EG, Sqrt, Tan, felső határának Truncate
  * **Karakterlánc** -összefűzés, tartalmazza, megadott módon végződő, IndexOf, Count, ToLower, TrimStart, cserélje le, TrimEnd, a megadott módon kezdődő, a SubString, ToUpper megfordításához
  * **A tömb** -összefűzés, tartalmazza, száma
  * **IN** operátor

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Támogatása az indexelő házirendek módosítása.
  * A DocumentClient új ReplaceDocumentCollectionAsync módszer
  * Új IndexTransformationProgress tulajdonság ResourceResponse<T> index házirend módosításai készültségi állapotának nyomon követése
  * Mostantól változtatható DocumentCollection.IndexingPolicy
* Támogatja a térbeli indexelő és lekérdezés.
  * A térbeli típusok szerializálása/deszerializálása névterét új Microsoft.Azure.Documents.Spatial, például pont és a sokszög
  * A Cosmos DB GeoJSON adataihoz az indexelés új SpatialIndex osztály
* **[Rögzített]**  Helytelen SQL-lekérdezést jön létre egy LINQ kifejezésből [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Függőség hozzáadta a Newtonsoft.Json v5.0.7.
* Végrehajtott módosítások Order By támogatásához:
  
  * Támogatott a LINQ szolgáltatók OrderBy() vagy OrderByDescending()
  * Order By támogatásához IndexingPolicy 
    
    **Lehetséges használhatatlanná tévő változást** 
    
    Ha a meglévő kódot, hogy rendelkezések gyűjteményeket, amelyek egyéni indexelési házirendet, majd a meglévő kódot frissíteni kell az új IndexingPolicy osztály támogatásához. Ha még nincs egyéni indexelési házirendet, majd a módosítás nem hatása a felhasználókra.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Támogatja a particionálás adatokat az új HashPartitionResolver és RangePartitionResolver osztályok és az IPartitionResolver használatával.
* A hozzáadott DataContract szerializálása.
* A hozzáadott GUID támogatja a LINQ szolgáltatónál.
* A hozzáadott UDF LINQ támogatja.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release--retirement-dates"></a>Kiadás & használatból való kivonást dátumok
A Microsoft biztosít értesítési legalább **12 hónapon keresztül** SDK eltávolítása érdekében vagy újabb támogatott verzióra való áttérés előtt.

Új szolgáltatásait és funkcióit és optimalizálás csak hozzá az aktuális SDK, így javasoljuk, hogy mindig a legújabb SDK verzióra frissít legkorábban lehető. 

A szolgáltatás bármely Azure Cosmos DB kivont SDK használatával kérelmeket visszautasítja.

<br/>

| Verzió | Kiadás dátuma | Kivezetési dátum |
| --- | --- | --- |
| [1.21.1](#1.20.1) |2018. március 09. |--- |
| [1.20.2](#1.20.1) |2018. február 21. |--- |
| [1.20.1](#1.20.1) |2018. február 05. |--- |
| [1.19.1](#1.19.1) |2017. november 16. |--- |
| [1.19.0](#1.19.0) |2017. november 10. |--- |
| [1.18.1](#1.18.1) |2017. november 07. |--- |
| [1.18.0](#1.18.0) |2017. október 17. |--- |
| [1.17.0](#1.17.0) |2017. augusztus 10. |--- |
| [1.16.1](#1.16.1) |2017. augusztus 07. |--- |
| [1.16.0](#1.16.0) |2017. augusztus 02. |--- |
| [1.15.0](#1.15.0) |2017. június 30. |--- |
| [1.14.1](#1.14.1) |2017. május 23. |--- |
| [1.14.0](#1.14.0) |2017. május 10. |--- |
| [1.13.4](#1.13.4) |2017. Előfordulhat, hogy 09. |--- |
| [1.13.3](#1.13.3) |2017. Előfordulhat, hogy 06. |--- |
| [1.13.2](#1.13.2) |2017. április 19. |--- |
| [1.13.1](#1.13.1) |2017. március 29. |--- |
| [1.13.0](#1.13.0) |2017. március 24. |--- |
| [1.12.2](#1.12.2) |2017. március 20. |--- |
| [1.12.1](#1.12.1) |2017. március 14. |--- |
| [1.12.0](#1.12.0) |2017. február 15. |--- |
| [1.11.4](#1.11.4) |2017. február 06. |--- |
| [1.11.3](#1.11.3) |2017. január 26. |--- |
| [1.11.1](#1.11.1) |2016. december 21. |--- |
| [1.11.0](#1.11.0) |2016. december 08. |--- |
| [1.10.0](#1.10.0) |2016. Szeptembertől 27. |--- |
| [1.9.5](#1.9.5) |2016 szeptemberétől 01 |--- |
| [1.9.4](#1.9.4) |2016 augusztusától 24 |--- |
| [1.9.3](#1.9.3) |2016 augusztusától 15 |--- |
| [1.9.2](#1.9.2) |2016. július 23. |--- |
| [1.8.0](#1.8.0) |2016. június 14. |--- |
| [1.7.1](#1.7.1) |2016. május 06. |--- |
| [1.7.0](#1.7.0) |2016. április 26. |--- |
| [1.6.3](#1.6.3) |2016. április 08. |--- |
| [1.6.2](#1.6.2) |2016. március 29. |--- |
| [1.5.3](#1.5.3) |2016. február 19. |--- |
| [1.5.2](#1.5.2) |2015. december 14. |--- |
| [1.5.1](#1.5.1) |2015. november 23. |--- |
| [1.5.0](#1.5.0) |2015. október 05. |--- |
| [1.4.1](#1.4.1) |2015. augusztus 25. |--- |
| [1.4.0](#1.4.0) |2015. augusztus 13. |--- |
| [1.3.0](#1.3.0) |2015. augusztus 05. |--- |
| [1.2.0](#1.2.0) |2015. július 06. |--- |
| [1.1.0](#1.1.0) |2015. április 30. |--- |
| [1.0.0](#1.0.0) |2015. áprilisi 08 |--- |


## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
A Cosmos DB kapcsolatos további információkért lásd: [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapján. 

