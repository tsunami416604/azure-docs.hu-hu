---
title: Mezőleképezések az indexelőkben
titleSuffix: Azure Cognitive Search
description: Állítsa be az indexelő mezőleképezéseit a mezőnevek és adatábrázolások közötti különbségek figyelembevételére.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3e09741e841897032b8146dee67b79e0c26ea5cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275152"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Mezőleképezések és -átalakítások az Azure Cognitive Search indexelőivel

Az Azure Cognitive Search indexelők használatakor néha úgy találja, hogy a bemeneti adatok nem egészen egyeznek meg a célindex sémájával. Ezekben az esetekben **mezőleképezések** segítségével átformálhatja az adatokat az indexelési folyamat során.

Néhány olyan helyzet, amikor a mezőleképezések hasznosak:

* Az adatforrás egy mező `_id`nevű, de az Azure Cognitive Search nem engedélyezi a mezőneveket, amelyek egy aláhúzással kezdődnek. A mezőleképezés lehetővé teszi a mezők hatékony átnevezését.
* Az index több mezőjét ugyanabból az adatforrás-adatból szeretné felnagyítani. Előfordulhat például, hogy ezekre a mezőkre különböző elemzőket szeretne alkalmazni.
* Egy indexmezőt több adatforrás ból származó adatokkal szeretne felnagyítani, és az adatforrások mindegyike különböző mezőneveket használ.
* A Base64-nek meg kell kódolnia vagy dekódolnia kell az adatokat. A mezőleképezések számos **leképezési funkciót támogatnak,** beleértve a Base64 kódolásés dekódolás funkcióit is.

> [!NOTE]
> Az Azure Cognitive Search indexelők mezőleképezési szolgáltatása egyszerű módot kínál az adatmezők indexmezőkre való leképezésére, néhány adatkonverziós lehetőséggel. Összetettebb adatok előzetes feldolgozásra lehet szükség, hogy átalakítsa azt egy űrlapot, amely könnyen indexelhető.
>
> A Microsoft Azure Data Factory egy hatékony felhőalapú megoldás az adatok importálásához és átalakításához. A forrásadatok indexelés előtti átalakításához kódot is írhat. A kódpéldákért olvassa el [a Relációs adatok modellezése](search-example-adventureworks-modeling.md) és [a Modell többszintű dimenziók at.](search-example-adventureworks-multilevel-faceting.md)
>

## <a name="set-up-field-mappings"></a>Mezőleképezések beállítása

A mezőleképezés három részből áll:

1. A `sourceFieldName`) mezőt jelöl az adatforrásban. Ez a tulajdonság kötelező.
2. Nem `targetFieldName`kötelező , amely egy mezőt jelöl a keresési indexben. Ha nincs megadva, a rendszer ugyanazt a nevet használja, mint az adatforrásban.
3. Egy `mappingFunction`opcionális , amely az adatokat a számos előre definiált függvény egyikével alakíthatja át. A funkciók teljes listája az [alábbiakban](#mappingFunctions)található.

A mezőleképezések `fieldMappings` hozzáadódnak az indexelő definíciójának tömbjéhez.

## <a name="map-fields-using-the-rest-api"></a>Mezők leképezése a REST API használatával

Az [Indexelő](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) létrehozása API-kérelem melusz használatával új indexelő létrehozásakor mezőleképezéseket adhat hozzá. Egy meglévő indexelő mezőleképezéseit az [Indexelő API-frissítése](https://docs.microsoft.com/rest/api/searchservice/update-indexer) kéréssel kezelheti.

Így rendelhet hozzá például egy forrásmezőt egy másik nevű célmezőhöz:

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

A forrásmezőkre több mezőleképezésben is lehet hivatkozni. A következő példa bemutatja, hogyan kell "elforlni" egy mezőt, és ugyanazt a forrásmezőt két különböző indexmezőbe másolni:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Az Azure Cognitive Search a kis- és nagybetűk megkülönböztetése nem megfelelő összehasonlítást használja a mező- és függvénynevek megoldásához a mezőleképezésekben. Ez kényelmes (nem kell az összes burkolatot jobbra beszereznie), de ez azt jelenti, hogy az adatforrás vagy az index nem rendelkezhet olyan mezőkkel, amelyek csak eseti legkülönbözőbben különböznek.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mezők leképezése a .NET SDK használatával

A . [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) `SourceFieldName` `TargetFieldName` `MappingFunction`

Az indexelő létrehozásakor vagy később a `Indexer.FieldMappings` tulajdonság közvetlen beállításával mezőleképezéseket adhat meg.

A következő C# példa beállítja a mezőleképezések létrehozásakor egy indexelő.

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

## <a name="field-mapping-functions"></a>Mezőleképezési függvények

A mezőleképezési függvény átalakítja a mező tartalmát, mielőtt az indexben tárolná. A következő leképezési funkciók jelenleg támogatottak:

* [base64Encode](#base64EncodeFunction)
* [base64Dekód](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringGyűjtemény](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode függvény

A bemeneti karakterlánc *URL-mentes* Base64 kódolását hajtja végre. Feltételezi, hogy a bemenet UTF-8 kódolású.

#### <a name="example---document-key-lookup"></a>Példa – dokumentumkulcs-keresmény

Csak url-mentes karakterek jelenhetnek meg az Azure Cognitive Search dokumentumkulcsában (mivel az ügyfeleknek képesnek kell lenniük a dokumentum [címzésére](https://docs.microsoft.com/rest/api/searchservice/lookup-document) a Keresési API használatával). Ha a kulcs forrásmezője URL-ben nem biztonságos `base64Encode` karaktereket tartalmaz, a funkció segítségével indexeléskor konvertálhatja azt. A dokumentumkulcs (átalakítás előtt és után is) nem lehet hosszabb 1024 karakternél.

Amikor a keresés idején beolvassa a kódolt kulcsot, `base64Decode` a függvény segítségével lekérheti az eredeti kulcsértéket, és ezzel bekeresheti a forrásdokumentumot.

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

Ha nem ad meg paramétertulajdonságot a leképezési függvényhez, akkor az alapértelmezett érték a `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Az Azure Cognitive Search két különböző Base64 kódolást támogat. Ugyanazokat a paramétereket kell használnia ugyanazon mező kódolásaés dekódolása során. További információt a [base64 kódolási beállítások](#base64details) ban talál, amelyek közül választhatja ki a használni használandó paramétereket.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode függvény

A bemeneti karakterlánc Base64 dekódolását hajtja végre. A bemenet egy *URL-biztos* Base64-kódolású karakterlánc.

#### <a name="example---decode-blob-metadata-or-urls"></a>Példa – blob metaadatainak vagy URL-címének dekódolása

A forrásadatok tartalmazhatnak Base64 kódolású karakterláncokat, például blob metaadat-karakterláncokat vagy webes URL-címeket, amelyeket egyszerű szövegként szeretne keresni. A `base64Decode` függvény segítségével a kódolt adatokat normál karakterláncokká alakíthatja a keresési index feltöltésekor.

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

Ha nem ad meg paramétertulajdonságot, akkor az `{"useHttpServerUtilityUrlTokenEncode" : true}`alapértelmezett érték a .

Az Azure Cognitive Search két különböző Base64 kódolást támogat. Ugyanazokat a paramétereket kell használnia ugyanazon mező kódolásaés dekódolása során. További részletekért lásd: [base64 kódolási beállítások](#base64details) at annak eldöntéséhez, hogy mely paramétereket kell használni.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>base64 kódolási beállítások

Az Azure Cognitive Search támogatja az URL-biztos base64 kódolást és a normál base64 kódolást. Az indexelés során kódolt base64 karakterláncot később ugyanazzal a kódolási beállításokkal kell dekódolni, különben az eredmény nem egyezik meg az eredetivel.

Ha `useHttpServerUtilityUrlTokenEncode` a `useHttpServerUtilityUrlTokenDecode` kódoláshoz és a dekódoláshoz tartozó `true`paraméterek `base64Encode` vagy paraméterek a beállítás , majd `base64Decode` a [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) néven viselkednek, és a [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx)néven viselkednek.

> [!WARNING]
> Ha `base64Encode` kulcsértékek előállítására `useHttpServerUtilityUrlTokenEncode` szolgál, akkor igaz értékre kell állítani. A kulcsértékekhez csak URL-mentes base64 kódolás használható. Tekintse meg [az Elnevezési szabályok &#40;Az Azure Cognitive Search&#41;](https://docs.microsoft.com/rest/api/searchservice/naming-rules) a kulcsértékekben lévő karakterekre vonatkozó korlátozások teljes készletét.

Az Azure Cognitive Search . A `useHttpServerUtilityUrlTokenEncode` `useHttpServerUtilityUrlTokenDecode` és a lehetőségek ezt a beépített funkciót használják. Ha a .NET Core vagy más keretrendszert használja, javasoljuk, hogy adja meg ezeket a `false` beállításokat, és hívja meg közvetlenül a keretrendszer kódolási és dekódolási funkcióit.

Az alábbi táblázat a karakterlánc `00>00?00`különböző base64 kódolásait hasonlítja össze. A base64 függvények szükséges további feldolgozásának meghatározásához alkalmazza a könyvtárkódolási `00>00?00` függvényt a karakterláncon, és hasonlítsa össze a kimenetet a várt kimenettel. `MDA-MDA_MDA`

| Encoding | Base64 kódolja a kimenetet | További feldolgozás a tárkódolás után | További feldolgozás a függvénykód kezelés előtt |
| --- | --- | --- | --- |
| Base64 kitöltéssel | `MDA+MDA/MDA=` | URL-biztos karakterek használata és kitöltés eltávolítása | Szabványos base64 karakterek használata és kitöltés hozzáadása |
| Base64 kitöltés nélkül | `MDA+MDA/MDA` | URL-biztos karakterek használata | Szabványos alap64 karakter használata |
| URL-biztos base64 kitöltéssel | `MDA-MDA_MDA=` | Kitöltés eltávolítása | Kitöltés hozzáadása |
| URL-biztos base64 kitöltés nélkül | `MDA-MDA_MDA` | None | None |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition függvény

Feloszt egy karakterláncmezőt a megadott határolójel használatával, és a tokent az eredményül kapott felosztásban megadott helyen választja ki.

Ez a függvény a következő paramétereket használja:

* `delimiter`: a bemeneti karakterlánc felosztásakor elválasztóként használandó karakterlánc.
* `position`: a bemeneti karakterlánc felosztása után a kivételezendő token nulla-alapú pozíciója.

Ha például a `Jane Doe`bemeneta `delimiter` `" "`, a (szóköz) és `position` `Jane`a 0, akkor az eredmény a ; ha `position` az 1, az `Doe`eredmény . Ha a beosztás olyan jogkivonatra hivatkozik, amely nem létezik, a rendszer hibát ad vissza.

#### <a name="example---extract-a-name"></a>Példa - név kinyerése

Az adatforrás tartalmaz `PersonName` egy mezőt, amelyet két `FirstName` `LastName` külön és mezőként szeretne indexelni. Ezzel a függvénnyel feloszthatja a bemenetet a szóköz karaktert határolójelként.

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

A karakterláncok JSON-tömbjeként formázott karakterláncokat olyan karakterlánctömbté alakítja, `Collection(Edm.String)` amely az index egy mezőjének feltöltésére használható.

Ha például a bemeneti `["red", "white", "blue"]`karakterlánc a , `Collection(Edm.String)` akkor a rendszer `red`a három `blue`értékkel tölti fel a típust. `white` A JSON-karakterlánctömbökként nem elemezhető bemeneti értékek esetén a függvény hibát ad vissza.

#### <a name="example---populate-collection-from-relational-data"></a>Példa – a relációs adatokból való gyűjtés feltöltése

Az Azure SQL Database nem rendelkezik beépített adattípussal, amely természetesen leképezi az Azure Cognitive Search mezőihez. `Collection(Edm.String)` A karakterlánc-gyűjteménymezők feltöltéséhez a forrásadatokat json karakterlánctömbként előre `jsonArrayToStringCollection` feldolgozhatja, majd használhatja a leképezési függvényt.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

A relációs adatokat indexgyűjtő mezőkké alakító részletes példát a [Relációs adatok modellezése](search-example-adventureworks-modeling.md)című témakörben találja.

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode függvény

Ez a funkció egy karakterlánc kódolására használható, hogy az "URL-ben biztonságos" legyen. Ha olyan karakterlánccal használja, amely nem engedélyezett az URL-ben, ez a függvény ezeket a "nem biztonságos" karaktereket karakter-entitás megfelelőkké alakítja. Ez a függvény az UTF-8 kódolási formátumot használja.

#### <a name="example---document-key-lookup"></a>Példa – dokumentumkulcs-keresmény

`urlEncode`függvény a függvény alternatívájaként `base64Encode` használható, ha csak az URL nem biztonságos karaktereket szeretné konvertálni, miközben más karaktereket is úgy kell megtartani.

Mondja, a bemeneti karakterlánc `<hello>` - akkor `(Edm.String)` a célmező típusú lesz feltöltve az értékkel`%3chello%3e`

Amikor a keresés idején beolvassa a kódolt kulcsot, `urlDecode` a függvény segítségével lekérheti az eredeti kulcsértéket, és ezzel bekeresheti a forrásdokumentumot.

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

 Ez a függvény UTF-8 kódolási formátumban konvertálja az URL-kódolású karakterláncot dekódolt karakterláncgá.

 ### <a name="example---decode-blob-metadata"></a>Példa – blob metaadatainak dekódolása

 Egyes Azure storage-ügyfelek automatikusan url-cím blob metaadatokat, ha nem ASCII karaktereket tartalmaz. Ha azonban az ilyen metaadatokat kereshetővé szeretné tenni (egyszerű szövegként), a `urlDecode` funkció valamekkora hatázza vissza a kódolt adatokat normál karakterláncokká a keresési index feltöltésekor.

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
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>fixedLengthEncode függvény
 
 Ez a függvény bármilyen hosszúságú karakterláncot rögzített hosszúságú karakterláncgá alakít át.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>Példa - túl hosszú dokumentumkulcsok leképezése
 
Ha 1024 karakternél hosszabb dokumentumkulcsra panaszkodó hibákkal szembesül, ez a funkció alkalmazható a dokumentumkulcs hosszának csökkentésére.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```
