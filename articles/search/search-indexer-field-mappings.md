---
title: Mező-hozzárendelések az indexelő használatával történő automatikus indexeléshez
titleSuffix: Azure Cognitive Search
description: Az indexelő mező-hozzárendelések konfigurálása a mezőnevek és az adatábrázolások közötti különbségek kiszámításához.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc863ee3dc7f2dc8049fcd22189acac94a855352
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786977"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Mező-hozzárendelések és átalakítások az Azure Cognitive Search indexelő használatával

Az Azure Cognitive Search indexelő használatakor előfordulhat, hogy a bemeneti adatok nem egyeznek meg a célként megadott index sémájával. Ezekben az esetekben a **mező-hozzárendelések** segítségével átalakíthatja az adatait az indexelési folyamat során.

Bizonyos helyzetekben hasznosak lehetnek a mezők leképezése:

* Az adatforrás `_id`nevű mezővel rendelkezik, az Azure Cognitive Search azonban nem engedélyezi az aláhúzással kezdődő mezőnevek használatát. A mezők leképezése lehetővé teszi egy mező tényleges átnevezését.
* Az indexben több mezőt is fel szeretne tölteni ugyanabból az adatforrásból származó adatokból. Előfordulhat például, hogy különböző elemzőket szeretne alkalmazni ezekre a mezőkre.
* Egynél több adatforrásból származó adatokkal szeretné feltölteni az index mezőt, és az egyes adatforrások különböző mezőneveket használnak.
* Az adatai Base64 kódolása vagy dekódolása szükséges. A mező-hozzárendelések több **leképezési funkciót**is támogatnak, beleértve a Base64 kódoláshoz és a dekódoláshoz használható függvényeket.

> [!NOTE]
> Az Azure Cognitive Search indexek mező-hozzárendelési funkciója egyszerű módszert kínál az adatmezők indexelési mezőkhöz való leképezésére, néhány lehetőséggel az adatátalakításra. Az összetettebb adatfeldolgozáshoz szükség lehet az előzetes feldolgozásra, hogy egy könnyen indexelhető űrlapra alakítsa át azokat.
>
> A Microsoft Azure Data Factory egy hatékony felhőalapú megoldás az adatimportáláshoz és átalakításhoz. Az indexelés előtt kódot is írhat a forrásadatok átalakításához. A példákat a következő témakörben tekintheti meg: a [modellhez kapcsolódó kapcsolatok](search-example-adventureworks-modeling.md) és [modellek többszintű dimenziója](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Mezők leképezésének beállítása

A mezők leképezése három részből áll:

1. Egy `sourceFieldName`, amely az adatforrás egy mezőjét jelöli. Ezt a tulajdonságot kötelező megadni.
2. Egy opcionális `targetFieldName`, amely a keresési indexben lévő mezőt jelöli. Ha nincs megadva, a rendszer ugyanazt a nevet használja, mint az adatforrásban.
3. Opcionális `mappingFunction`, amely az adatok átalakítását számos előre definiált függvény használatával végezheti el. A függvények teljes listája [alább látható](#mappingFunctions).

A mező-hozzárendelések az indexelő definíciójának `fieldMappings` sorába kerülnek.

## <a name="map-fields-using-the-rest-api"></a>Mezők leképezése a REST API használatával

Az [Indexelő](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) API-kérelem használatával új indexelő létrehozásakor mező-hozzárendeléseket adhat hozzá. Az [Indexelő](https://docs.microsoft.com/rest/api/searchservice/update-indexer) API-kérelem használatával kezelheti egy meglévő indexelő mező-hozzárendeléseit.

Például a következő módon képezhető le egy forrás mezőt egy célként megadott mezőre egy másik névvel:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

A forrás mező több mező-hozzárendelésre is hivatkozhat. Az alábbi példa azt szemlélteti, hogyan lehet "elágazást" lemásolni egy mezőből, és ugyanabból a forrásból két különböző index-mezőre másolni:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Az Azure Cognitive Search a kis-és nagybetűk megkülönböztetésének összehasonlításával oldja fel a mezők és függvények nevét a mezők leképezésében. Ez kényelmes (nem kell beolvasnia az összes burkolatot), de az azt jelenti, hogy az adatforrás vagy az index nem rendelkezhet olyan mezőkkel, amelyek csak esettől eltérőek lehetnek.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mezők leképezése a .NET SDK használatával

A [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) osztály segítségével definiálhatja a mező-hozzárendeléseket a .net SDK-ban, amely a tulajdonságok `SourceFieldName` és a `TargetFieldName`, valamint egy opcionális `MappingFunction`-hivatkozás.

Az indexelő létrehozásakor, vagy később a `Indexer.FieldMappings` tulajdonság közvetlen beállításával megadhatja a mezők leképezését.

Az alábbi C# példa egy indexelő létrehozásakor beállítja a mező-hozzárendeléseket.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Mező-hozzárendelési függvények

Egy mező-hozzárendelési függvény átalakítja egy mező tartalmát, mielőtt az indexben tárolja őket. A következő leképezési függvények jelenleg támogatottak:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode függvény

*URL-cím – biztonságos* Base64 kódolást végez a bemeneti karakterláncban. Feltételezi, hogy a bemenet UTF-8 kódolású.

#### <a name="example---document-key-lookup"></a>Példa – dokumentum kulcsának keresése

Csak az URL-alapú biztonságos karakterek szerepelhetnek az Azure Cognitive Search-dokumentum kulcsában (mivel az ügyfeleknek a [keresési API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) használatával tudniuk kell kezelni a dokumentumot). Ha a kulcs forrás mezőjében URL-nem biztonságos karakterek szerepelnek, akkor a `base64Encode` függvény használatával átalakíthatja az indexelési időt.

Amikor a beolvasott kulcsot keresési időpontban kéri le, a `base64Decode` függvénnyel beolvashatja az eredeti kulcs értékét, és ezzel a forrás dokumentum lekéréséhez használhatja azt.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

Ha nem tartalmaz Parameters tulajdonságot a leképezési függvényhez, az alapértelmezés szerint a `{"useHttpServerUtilityUrlTokenEncode" : true}`értéket adja meg.

Az Azure Cognitive Search két különböző Base64-kódolást támogat. Ugyanezeket a paramétereket kell használnia a kódoláshoz és a dekódoláshoz ugyanezen a mezőben. További információ: [Base64 kódolási beállítások](#base64details) a használandó paraméterek eldöntéséhez.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode függvény

A bemeneti karakterlánc Base64-dekódolását végzi. A bemenet a következő *URL-cím – biztonságos* Base64 kódolású karakterlánc lehet.

#### <a name="example---decode-blob-metadata-or-urls"></a>Példa – blob metaadatainak vagy URL-címek dekódolása

A forrásadatok olyan Base64 kódolású karakterláncokat tartalmazhatnak, mint például a blob metaadat-karakterláncok vagy a webes URL-címek, amelyeket egyszerű szövegként szeretne keresni. A `base64Decode` függvénnyel visszakapcsolhatja a kódolt adatokat a keresési index feltöltésekor a normál karakterláncba.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Ha nem tartalmaz Parameters tulajdonságot, a rendszer alapértelmezés szerint a `{"useHttpServerUtilityUrlTokenEncode" : true}`értéket adja meg.

Az Azure Cognitive Search két különböző Base64-kódolást támogat. Ugyanezeket a paramétereket kell használnia a kódoláshoz és a dekódoláshoz ugyanezen a mezőben. További részletekért lásd a [Base64-kódolási beállításokat](#base64details) a használandó paraméterek eldöntéséhez.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Base64 kódolási beállítások

Az Azure Cognitive Search két különböző Base64-kódolást támogat: a **HTTPSERVERUTILITY URL-tokent**és az **URL-alapú biztonságos Base64 kódolást a kitöltés nélkül**. Az indexelés során Base64 kódolású karakterláncot később dekódolni kell ugyanazzal a kódolási lehetőséggel, különben az eredmény nem egyezik meg az eredetivel.

Ha a kódoláshoz és a dekódoláshoz `useHttpServerUtilityUrlTokenEncode` vagy `useHttpServerUtilityUrlTokenDecode` paraméterek `true`értékre vannak beállítva, akkor `base64Encode` úgy viselkedik, mint a [HttpServerUtility. UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) , és a `base64Decode` úgy viselkedik, mint a [HttpServerUtility. UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Ha nem a teljes .NET-keretrendszert használja (azaz .NET Core-t vagy más keretrendszert használ) az Azure Cognitive Search viselkedését emuláló kulcs értékének előállításához, akkor `useHttpServerUtilityUrlTokenEncode` és `useHttpServerUtilityUrlTokenDecode` kell beállítania `false`. A használt könyvtártól függően a Base64-kódolás és a dekódolási függvények eltérhetnek az Azure Cognitive Search által használttól.

A következő táblázat összehasonlítja a karakterlánc különböző Base64 kódolásait `00>00?00`. Ha meg szeretné határozni a Base64-függvények szükséges további feldolgozását (ha van ilyen), alkalmazza a függvénytár-kódolás függvényt a karakterláncra `00>00?00` és hasonlítsa össze a kimenetet a várt kimeneti `MDA-MDA_MDA`ával.

| Encoding | Base64 kódolású kimenet | További feldolgozás a függvénytár-kódolás után | További feldolgozás a könyvtár dekódolása előtt |
| --- | --- | --- | --- |
| Base64 kitöltéssel | `MDA+MDA/MDA=` | URL-alapú biztonságos karakterek használata és kitöltés eltávolítása | Szabványos Base64-karakterek használata és kitöltés hozzáadása |
| Base64 kitöltés nélkül | `MDA+MDA/MDA` | URL-alapú biztonságos karakterek használata | Szabványos Base64-karakterek használata |
| URL – biztonságos Base64 kitöltéssel | `MDA-MDA_MDA=` | Kitöltés eltávolítása | Kitöltés hozzáadása |
| URL – biztonságos Base64 kitöltés nélkül | `MDA-MDA_MDA` | None | None |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition függvény

A megadott elválasztó karakterrel feldarabol egy sztring mezőt, és az eredményül kapott felosztásban kiválasztja a tokent a megadott pozícióban.

Ez a függvény a következő paramétereket használja:

* `delimiter`: a bemeneti karakterlánc felosztásakor elválasztóként használandó karakterlánc.
* `position`: a bemeneti sztring felosztása után kiválasztható token egészének nulla alapú pozíciója.

Ha például a bemenet `Jane Doe`, akkor a `delimiter` `" "`(szóköz), a `position` pedig 0, az eredmény `Jane`; Ha a `position` 1, az eredmény `Doe`. Ha a pozíció olyan tokenre hivatkozik, amely nem létezik, hibaüzenetet ad vissza.

#### <a name="example---extract-a-name"></a>Példa – név kinyerése

Az adatforrás tartalmaz egy `PersonName` mezőt, és két különálló `FirstName` és `LastName` mezőként szeretné indexelni. Ezt a függvényt használhatja a bemenet elválasztóként való felosztásához.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection függvény

A sztringek JSON-tömbként formázott karakterláncának átalakítása egy karakterlánc-tömbbe, amely az index `Collection(Edm.String)` mezőjének feltöltésére használható.

Ha például a bemeneti karakterlánc `["red", "white", "blue"]`, akkor a `Collection(Edm.String)` típusú cél mező a három értékkel lesz feltöltve `red`, `white`és `blue`. A nem értelmezhető JSON-karakterlánc-tömbökkel rendelkező bemeneti értékek esetén hibaüzenetet ad vissza.

#### <a name="example---populate-collection-from-relational-data"></a>Példa – a gyűjtemények feltöltése a kapcsolódó adatokból

Azure SQL Database nem rendelkezik olyan beépített adattípussal, amely természetes módon leképezi az Azure Cognitive Search `Collection(Edm.String)` mezőit. A karakterlánc-gyűjtési mezők feltöltéséhez JSON-karakterlánc-tömbként előre feldolgozhatja a forrásadatokat, majd használhatja a `jsonArrayToStringCollection` leképezési függvényt.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

A viszonyítási adat index-gyűjtési mezőkbe való átalakításával kapcsolatos részletes Példákért lásd: a [rokoni adatmodellek modellezése](search-example-adventureworks-modeling.md).

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode függvény

Ez a függvény egy karakterlánc kódolására használható, hogy az "URL-biztonság" legyen. Ha olyan karakterláncot használ, amely nem engedélyezett karaktereket tartalmaz egy URL-címben, akkor ez a függvény a "nem biztonságos" karaktereket átalakítja a karakter-entitások megfelelőbe. Ez a függvény az UTF-8 kódolási formátumot használja.

#### <a name="example---document-key-lookup"></a>Példa – dokumentum kulcsának keresése

a `urlEncode` függvény a `base64Encode` függvény alternatívájaként is használható, ha csak a nem biztonságos URL-címeket szeretné átalakítani, miközben más karaktereket is megtarthat.

Tegyük fel, hogy a bemeneti karakterlánc `<hello>` – a `(Edm.String)` típusú cél mező a következő értékkel lesz feltöltve `%3chello%3e`

Amikor a beolvasott kulcsot keresési időpontban kéri le, a `urlDecode` függvénnyel beolvashatja az eredeti kulcs értékét, és ezzel a forrás dokumentum lekéréséhez használhatja azt.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>urlDecode függvény

 Ez a függvény UTF-8 kódolási formátumban konvertál egy URL-kódolású karakterláncot egy dekódolt karakterlánccá.

 ### <a name="example---decode-blob-metadata"></a>Példa – blob metaadatainak dekódolása

 Egyes Azure Storage-ügyfelek automatikusan URL-címet kódolnak a blob metaadatainak kódolásához, ha nem ASCII-karaktereket tartalmaznak. Ha azonban szeretné, hogy az ilyen metaadatok kereshetők legyenek (egyszerű szövegként), a `urlDecode` függvénnyel visszaállíthatja a kódolt adatokat normál karakterláncokra a keresési index feltöltésekor.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```