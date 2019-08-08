---
title: Az indexelő segítségével automatizált indexeléshez használható mező-hozzárendelések – Azure Search
description: Azure Search indexelő mező-hozzárendelések konfigurálása a mezőnevek és az adatábrázolások közötti különbségek figyelembevételéhez.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 771a6e413cd08a338da41c09cd6a0da35e28e5e4
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840654"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>Mező-hozzárendelések és átalakítások Azure Search indexelő használatával

Azure Search indexelő használatakor előfordulhat, hogy a bemeneti adatok nem felelnek meg a célként megadott index sémájának. Ezekben az esetekben a **mező** -hozzárendelések segítségével átalakíthatja az adatait az indexelési folyamat során.

Bizonyos helyzetekben hasznosak lehetnek a mezők leképezése:

* Az adatforrás egy nevű `_id`mezővel rendelkezik, de a Azure Search nem engedélyezi a mezőneveket, amely aláhúzással kezdődik. A mezők leképezése lehetővé teszi egy mező tényleges átnevezését.
* Az indexben több mezőt is fel szeretne tölteni ugyanabból az adatforrásból származó adatokból. Előfordulhat például, hogy különböző elemzőket szeretne alkalmazni ezekre a mezőkre.
* Egynél több adatforrásból származó adatokkal szeretné feltölteni az index mezőt, és az egyes adatforrások különböző mezőneveket használnak.
* Az adatai Base64 kódolása vagy dekódolása szükséges. A mező-hozzárendelések több **leképezési funkciót**is támogatnak, beleértve a Base64 kódoláshoz és a dekódoláshoz használható függvényeket.

> [!NOTE]
> Azure Search indexek mező-hozzárendelési funkciója egyszerű módszert biztosít az adatmezők index mezőkhöz való leképezésére, néhány lehetőséggel az adatátalakításra. Az összetettebb adatfeldolgozáshoz szükség lehet az előzetes feldolgozásra, hogy egy könnyen indexelhető űrlapra alakítsa át azokat.
>
> A Microsoft Azure Data Factory egy hatékony felhőalapú megoldás az adatimportáláshoz és átalakításhoz. Az indexelés előtt kódot is írhat a forrásadatok átalakításához. A példákat a következő témakörben tekintheti meg: a [modellhez kapcsolódó kapcsolatok](search-example-adventureworks-modeling.md) és [modellek](search-example-adventureworks-multilevel-faceting.md)többszintű dimenziója.
>

## <a name="set-up-field-mappings"></a>Mezők leképezésének beállítása

A mezők leképezése három részből áll:

1. A `sourceFieldName`, amely az adatforrás egy mezőjét jelöli. Ezt a tulajdonságot kötelező megadni.
2. Egy opcionális `targetFieldName`, amely a keresési index egyik mezőjét jelöli. Ha nincs megadva, a rendszer ugyanazt a nevet használja, mint az adatforrásban.
3. Opcionális `mappingFunction`, amely az adatok átalakítását számos előre definiált függvény használatával végezheti el. A függvények teljes listája [alább látható](#mappingFunctions).

A mező-hozzárendelések az indexelő definíciójának `fieldMappings` tömbje számára lesznek hozzáadva.

## <a name="map-fields-using-the-rest-api"></a>Mezők leképezése a REST API használatával

Az indexelő API-kérelem használatával új indexelő létrehozásakor mező- [](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) hozzárendeléseket adhat hozzá. Az indexelő API-kérelem használatával kezelheti egy meglévő indexelő mező [](https://docs.microsoft.com/rest/api/searchservice/update-indexer) -hozzárendeléseit.

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
> A Azure Search a kis-és nagybetűk megkülönböztetésének összehasonlításával oldja fel a mezők és függvények nevét a mezők leképezésében. Ez kényelmes (nem kell beolvasnia az összes burkolatot), de az azt jelenti, hogy az adatforrás vagy az index nem rendelkezhet olyan mezőkkel, amelyek csak esettől eltérőek lehetnek.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mezők leképezése a .NET SDK használatával

A [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) osztály segítségével definiálhatja a mezők leképezéseit a .net SDK-ban, amely tartalmazza `SourceFieldName` a `TargetFieldName`tulajdonságokat, valamint a `MappingFunction` választható hivatkozást.

Az indexelő létrehozásakor vagy később a `Indexer.FieldMappings` tulajdonság közvetlen beállításával megadhatja a mezők leképezését.

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

Csak URL-alapú biztonságos karakterek szerepelhetnek egy Azure Search dokumentum kulcsában (mivel az ügyfeleknek a [keresési API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) használatával tudniuk kell kezelni a dokumentumot). Ha a kulcs forrás mezőjében URL-nem biztonságos karakterek szerepelnek, akkor a függvény használatával `base64Encode` átalakíthatja az indexelési időt.

Ha a beolvasott kulcsot keresési időpontban kéri le, a `base64Decode` függvény használatával beolvashatja az eredeti kulcs értékét, és használhatja azt a forrásbizonylat lekéréséhez.

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

Ha nem tartalmaz Parameters tulajdonságot a leképezési függvényhez, az alapértelmezés szerint az értéket `{"useHttpServerUtilityUrlTokenEncode" : true}`adja meg.

A Azure Search két különböző Base64-kódolást támogat. Ugyanezeket a paramétereket kell használnia a kódoláshoz és a dekódoláshoz ugyanezen a mezőben. További információ: [Base64 kódolási beállítások](#base64details) a használandó paraméterek eldöntéséhez.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode függvény

A bemeneti karakterlánc Base64-dekódolását végzi. A bemenet a következő *URL-cím – biztonságos* Base64 kódolású karakterlánc lehet.

#### <a name="example---decode-blob-metadata-or-urls"></a>Példa – blob metaadatainak vagy URL-címek dekódolása

A forrásadatok olyan Base64 kódolású karakterláncokat tartalmazhatnak, mint például a blob metaadat-karakterláncok vagy a webes URL-címek, amelyeket egyszerű szövegként szeretne keresni. A `base64Decode` függvény használatával a kódolt adatokat visszaállíthatja normál karakterláncokra a keresési index feltöltésekor.

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

Ha nem tartalmaz Parameters tulajdonságot, a rendszer alapértelmezés szerint az értéket `{"useHttpServerUtilityUrlTokenEncode" : true}`adja meg.

A Azure Search két különböző Base64-kódolást támogat. Ugyanezeket a paramétereket kell használnia a kódoláshoz és a dekódoláshoz ugyanezen a mezőben. További részletekért lásd a [Base64-kódolási beállításokat](#base64details) a használandó paraméterek eldöntéséhez.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Base64 kódolási beállítások

Azure Search két különböző Base64-kódolást támogat: **HTTPSERVERUTILITY URL-token**és **URL-cím – biztonságos Base64-kódolás kitöltés nélkül**. Az indexelés során Base64 kódolású karakterláncot később dekódolni kell ugyanazzal a kódolási lehetőséggel, különben az eredmény nem egyezik meg az eredetivel.

Ha a `useHttpServerUtilityUrlTokenEncode` kódoláshoz és a `true`dekódoláshoz szükséges `useHttpServerUtilityUrlTokenDecode` paraméterek a (z) `base64Encode` értékre vannak beállítva, akkor a következőképpen `base64Decode` viselkedik: [HttpServerUtility. UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) , és úgy viselkedik, mint [ HttpServerUtility. UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Ha nem a teljes .NET-keretrendszert használja (azaz .net Core-t vagy más keretrendszert használ), hogy a kulcs értékeit a Azure Search viselkedésének emulálása céljából hozza létre, akkor a `useHttpServerUtilityUrlTokenEncode` és `useHttpServerUtilityUrlTokenDecode` a `false`értéket kell megadnia. A használt könyvtártól függően a Base64-kódolás és a dekódolási függvények eltérhetnek az Azure Search által használttól.

A következő táblázat a karakterlánc `00>00?00`különböző Base64 kódolásait hasonlítja össze. Ha meg szeretné határozni a Base64-függvények szükséges további feldolgozását (ha van ilyen), alkalmazza a függvénytár-kódolás `00>00?00` függvényt a karakterláncon, és hasonlítsa össze a kimenetet a várt kimenettel `MDA-MDA_MDA`.

| Encoding | Base64 kódolású kimenet | További feldolgozás a függvénytár-kódolás után | További feldolgozás a könyvtár dekódolása előtt |
| --- | --- | --- | --- |
| Base64 kitöltéssel | `MDA+MDA/MDA=` | URL-alapú biztonságos karakterek használata és kitöltés eltávolítása | Szabványos Base64-karakterek használata és kitöltés hozzáadása |
| Base64 kitöltés nélkül | `MDA+MDA/MDA` | URL-alapú biztonságos karakterek használata | Szabványos Base64-karakterek használata |
| URL – biztonságos Base64 kitöltéssel | `MDA-MDA_MDA=` | Kitöltés eltávolítása | Kitöltés hozzáadása |
| URL – biztonságos Base64 kitöltés nélkül | `MDA-MDA_MDA` | Nincsenek | Nincsenek |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition függvény

A megadott elválasztó karakterrel feldarabol egy sztring mezőt, és az eredményül kapott felosztásban kiválasztja a tokent a megadott pozícióban.

Ez a függvény a következő paramétereket használja:

* `delimiter`: a bemeneti karakterlánc felosztásakor elválasztóként használandó karakterlánc.
* `position`: a bemeneti sztring felosztása után kiválasztható token egészének nulla alapú pozíciója.

Ha `Jane Doe`például a bemenet, `" "`a `delimiter` (szóköz) `Jane`és az `position` értéke 0, az `Doe`eredmény a következő:. Ha a `position` értéke 1, az eredmény. Ha a pozíció olyan tokenre hivatkozik, amely nem létezik, hibaüzenetet ad vissza.

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

A sztringek JSON-tömbként formázott karakterláncának átalakítása egy karakterlánc-tömbbe, amely az index `Collection(Edm.String)` mezőinek feltöltésére használható.

Ha `["red", "white", "blue"]`például a bemeneti karakterlánc, akkor a típusú cél mező a következő `Collection(Edm.String)` három értékkel `red` `white`lesz feltöltve:, és `blue`. A nem értelmezhető JSON-karakterlánc-tömbökkel rendelkező bemeneti értékek esetén hibaüzenetet ad vissza.

#### <a name="example---populate-collection-from-relational-data"></a>Példa – a gyűjtemények feltöltése a kapcsolódó adatokból

A Azure SQL Database nem rendelkezik olyan beépített adattípussal, amely természetesen a `Collection(Edm.String)` Azure Search mezőihez van társítva. A karakterlánc-gyűjtési mezők feltöltéséhez JSON-karakterlánc-tömbként előre feldolgozhatja a forrásadatokat, majd használhatja a `jsonArrayToStringCollection` leképezési függvényt.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

A viszonyítási adat index-gyűjtési mezőkbe való átalakításával kapcsolatos részletes példákért [](search-example-adventureworks-modeling.md)lásd: a rokoni adatmodellek modellezése.

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode függvény

Ez a függvény egy karakterlánc kódolására használható, hogy az "URL-biztonság" legyen. Ha olyan karakterláncot használ, amely nem engedélyezett karaktereket tartalmaz egy URL-címben, akkor ez a függvény a "nem biztonságos" karaktereket átalakítja a karakter-entitások megfelelőbe. Ez a függvény az UTF-8 kódolási formátumot használja.

#### <a name="example---document-key-lookup"></a>Példa – dokumentum kulcsának keresése

`urlEncode`a függvény a függvény alternatívájaként is használható, ha `base64Encode` csak a nem biztonságos URL-címeket szeretné átalakítani, miközben más karaktereket is tart.

Tegyük fel, hogy a `<hello>` bemeneti karakterlánc – a típusú cél mező `(Edm.String)` a következő értékkel lesz feltöltve:`%3chello%3e`

Ha a beolvasott kulcsot keresési időpontban kéri le, a `urlDecode` függvény használatával beolvashatja az eredeti kulcs értékét, és használhatja azt a forrásbizonylat lekéréséhez.

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

 Egyes Azure Storage-ügyfelek automatikusan URL-címet kódolnak a blob metaadatainak kódolásához, ha nem ASCII-karaktereket tartalmaznak. Ha azonban szeretné, hogy az ilyen metaadatok kereshetők legyenek (egyszerű szövegként), a `urlDecode` függvény használatával a kódolt adatokat visszaállíthatja normál karakterláncokra a keresési index feltöltésekor.

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