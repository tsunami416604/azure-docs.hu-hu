---
title: Mezőleképezések az Automatikus indexelés indexelők – Azure Search használatával
description: Állítsa be az Azure Search indexelők mező-leképezések a mezőnevek és az adatok formátumban különbségek figyelembe.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 3546e342b535a122ea4ed3f844cd5e28a76d551a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147794"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>Mező-leképezések és átalakítás Azure Search-indexelők használatával

Azure Search-indexelők használata esetén néha találja, hogy a bemeneti adatok teljesen a célindex sémája nem egyezik. Ezekben az esetekben használható **mezőleképezéseivel** az indexelési folyamat során az adatok átalakításához.

Bizonyos helyzetekben, ahol Mezőleképezések hasznosak:

* Az adatforrás rendelkezik mezőnév `_id`, de az Azure Search nem engedélyezi a mezők nevei kezdődhet aláhúzásjellel. Mezőleképezés lehetővé teszi a hatékony nevezheti át a mezőt.
* Adja meg az indexben, az adatokat forrás ugyanazokat az adatokat több mezőkben szeretné. Például előfordulhat, hogy szeretné alkalmazni a különböző elemzők ezekhez a mezőkhöz.
* Egy index mezőt egynél több adatforrásból származó adatokkal feltölteni kívánt, és az adatforrások minden másik mező nevét használják.
* Van szüksége a Base64 kódolás, vagy az adatok dekódolása. Mezőleképezések támogatja a több **funkciók leképezési**, többek között funkciók Base64 a kódolási és dekódolási.

> [!NOTE]
> A mező hálózatleképezési funkcióját az Azure Search-indexelők datová Pole leképezés index mezőire, van néhány lehetőség az adatkonvertálás egyszerű módszert biztosít. Összetettebb adatokat lehet szükség, amely egyszerűen index űrlapon átalakításához, előzetesen feldolgozni.
>
> A Microsoft Azure Data Factory hatékony felhőalapú megoldás importálása és az adatok átalakítása is. Átalakítják a forrásadatokat indexelés kódot is írhat. Hitelesítésikód-példák, lásd: [relációs adatok modellezése](search-example-adventureworks-modeling.md) és [értékkorlátozással többszintű modellezésére](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Mezőleképezések beállítása

Mezőleképezés három részből áll:

1. A `sourceFieldName`, amely jelöli, hogy az adatforrás egy mezője. Ez a tulajdonság megadása kötelező.
2. Egy nem kötelező `targetFieldName`, amely egy mezőt a keresési index jelöli. Ha nincs megadva, ugyanazzal a névvel, mint az adatforrást használja.
3. Egy nem kötelező `mappingFunction`, amely alakíthatja át az adatokat az egyik több előre meghatározott függvényeket. A funkciók teljes listája [alább](#mappingFunctions).

Mező-leképezések kerülnek a `fieldMappings` az indexelő definíciója tömbje.

## <a name="map-fields-using-the-rest-api"></a>A mezők leképezése a REST API használatával

Mezőleképezések létrehozásakor egy új indexelő segítségével hozzáadhatja a [indexelő létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) API-kérelem. A mező-leképezések egy meglévő indexelő használata kezelheti a [frissítés indexelő](https://docs.microsoft.com/rest/api/searchservice/update-indexer) API-kérelem.

Például a következő mező leképezése egy célmezőt, más néven:

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

Több mező-leképezések beviteli mező lehet hivatkozni. Az alábbi példa bemutatja, hogyan "ágaztatnia" Másolás forrás ugyanahhoz a mezőhöz két különböző index mezőire mező:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Az Azure Search nem betűérzékeny összehasonlító használja a mező-leképezések mezőt és a függvény nevek feloldásához. Ez kényelmes (nem kell minden a kis-és kellhet), de az azt jelenti, hogy az adatforrás vagy az index nem rendelkezhet, amelyet csak az eset eltérő mezők.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>A .NET SDK használatával a mezők leképezése

Mezőleképezések az a .NET SDK használatával meghatározott a [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) osztály, amely a tulajdonságokkal rendelkezik `SourceFieldName` és `TargetFieldName`, és a egy nem kötelező `MappingFunction` hivatkozást.

Mezőleképezések az indexelő kapcsolatot létesítő beállításával, illetve később közvetlenül megadhatja a `Indexer.FieldMappings` tulajdonság.

A következő C# példa beállítja a mező-leképezések az indexelő létrehozásánál.

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

## <a name="field-mapping-functions"></a>A mező leképezés funkciók

Egy mező leképezési függvény alakítja át egy mező tartalmát, előtt az indexben. A következő hozzárendelés-függvényeket jelenleg támogatja:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode függvény

Hajt végre *URL-cím környezetben is biztonságos* Base64 kódolás a bemeneti karakterláncot. Feltételezi, hogy a bemeneti UTF-8 kódolású.

#### <a name="example---document-key-lookup"></a>Példa - dokumentum kulcskeresés

Csak URL-cím biztonságos karakterek szerepelhetnek egy Azure Search-dokumentum kulcsot (mivel ügyfeleknek kell lennie arra, hogy a dokumentum használata a [keresési API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Ha a forrás mező, a kulcs URL-címekben nem biztonságos karaktereket tartalmaz, akkor használhatja a `base64Encode` , indexelési idő átalakítása függvény.

Keresés időpontban lekérésekor a kódolt kulcs ezután használhatja a `base64Decode` függvény az eredeti kulcs értékét, és azt használja a dokumentum lekéréséhez.

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

Ha a parameters tulajdonság nem adja meg a leképezési függvény, a rendszer alapértelmezés szerint az érték `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Az Azure Search két különböző Base64 kódolást támogatja. Ha a kódolási és dekódolási ugyanahhoz a mezőhöz ugyanazokat a paramétereket használja. További információkért lásd: [base64 kódolási beállítást](#base64details) eldönteni, melyik használandó paramétereket.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode függvény

Végrehajtja a Base64 kódolású bemeneti karakterlánc dekódolása. A bemeneti adatforrásmérete egy *URL-cím környezetben is biztonságos* Base64-kódolású karakterlánc.

#### <a name="example---decode-blob-metadata-or-urls"></a>Példa – blob metaadatait, vagy URL-dekódolást.

A forrásadatok Base64-kódolású karakterláncok, például a blob metaadatainak karakterláncokat vagy webes URL-címek, egyszerű szövegként kereshetővé kívánt tartalmazhat. Használhatja a `base64Decode` függvény a search-index adatokkal való feltöltésekor vissza alakítsa rendszeres karakterláncok a kódolt adatait.

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

Ha nem adja meg a parameters tulajdonság, a rendszer alapértelmezés szerint az érték `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Az Azure Search két különböző Base64 kódolást támogatja. Ha a kódolási és dekódolási ugyanahhoz a mezőhöz ugyanazokat a paramétereket használja. További részletekért lásd: [base64 kódolási beállítást](#base64details) eldönteni, melyik használandó paramétereket.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Base64 kódolási beállítást

Az Azure Search két különböző Base64 kódolást támogatja: **HttpServerUtility URL-cím jogkivonat**, és **URL-cím környezetben is biztonságos Base64 kódolás nélkül kitöltés**. Egy karakterlánc, amely a base64-kódolású az indexelés során később dekódolható kódolási beállítások, vagy pedig az eredmény az eredeti nem egyeznek.

Ha a `useHttpServerUtilityUrlTokenEncode` vagy `useHttpServerUtilityUrlTokenDecode` kódolási és dekódolási rendre paraméterek vannak beállítva `true`, majd `base64Encode` viselkedik [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) és `base64Decode` viselkedik [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Ha nem használja a teljes .NET-keretrendszer (azaz .NET Core vagy használ egy másik keretrendszer) az Azure Search viselkedésének emulációjához a kulcs értékeit állítja elő, majd állítsa be `useHttpServerUtilityUrlTokenEncode` és `useHttpServerUtilityUrlTokenDecode` való `false`. Attól függően, használja a könyvtárban a base64 kódolás és a visszafejtés funkciók eltérőek lehetnek az Azure Search által használt állók közül.

Az alábbi táblázat összehasonlítja a különböző base64 kódolású karakterlánc `00>00?00`. Annak megállapításához, a szükséges további feldolgozás (ha van ilyen) a base64-függvények, a alkalmazni a szalagtár funkciót, a karakterlánc kódolása `00>00?00` és összehasonlítja a runbooknak milyen kimenetet `MDA-MDA_MDA`.

| Encoding | Base64 kódolás kimenete | További erőforrástár-kódolás után feldolgozása | További feldolgozásra, mielőtt az erőforrástár-dekódolás |
| --- | --- | --- | --- |
| A kitöltés Base64 | `MDA+MDA/MDA=` | URL-cím környezetben is biztonságos karaktereket használni, és távolítsa el a kitöltés | Standard Base64-kódolású karakterek, és adja hozzá a kitöltés |
| Base64 kitöltése nélkül | `MDA+MDA/MDA` | URL-cím környezetben is biztonságos karakterek használhatók | Standard Base64-kódolású karakterek használhatók |
| URL-cím környezetben is biztonságos base64-kitöltés | `MDA-MDA_MDA=` | Távolítsa el a kitöltés | Kitöltés hozzáadása |
| URL-cím környezetben is biztonságos base64 kitöltése nélkül | `MDA-MDA_MDA` | None | None |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition függvény

Felosztja a megadott elválasztó használatával karakterlánc típusú, és kiválasztja a jogkivonat az eredményül kapott felosztása a megadott helyen.

Ez a függvény paraméterei a következők:

* `delimiter`: egy karakterláncot megfeleltessen elválasztóként használhatja, ha a bemeneti sztring felosztásához.
* `position`: egy egész számolt helyzetét megadja a jogkivonat választja ki, miután a bemeneti karakterlánc van felosztva.

Például, ha a bemeneti `Jane Doe`, a `delimiter` van `" "`(terület) és a `position` 0, az eredmény `Jane`; Ha a `position` : 1, az eredmény `Doe`. Ha a pozíció hivatkozik egy jogkivonatot, amely nem létezik, hibát ad vissza.

#### <a name="example---extract-a-name"></a>Példa - kivonat nevét

Az adatforrás tartalmaz egy `PersonName` mező, és azt szeretné, két külön tárgymutatóhoz `FirstName` és `LastName` mezőket. Ez a függvény használatával az elválasztó karakter a szóköz karakter használata a bemeneti felosztása.

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

Egy karakterlánc formátumú JSON-tömböt, karakterláncok való feltöltéséhez használható karakterlánc-tömbben alakítja át egy `Collection(Edm.String)` mezőt az indexben.

Például, ha a bemeneti karakterlánc `["red", "white", "blue"]`, majd a célmező típusú `Collection(Edm.String)` tölti fel a három értékek `red`, `white`, és `blue`. A bemeneti értékek, amelyek nejde analyzovat jako JSON-karakterlánc tömbök hibát ad vissza.

#### <a name="example---populate-collection-from-relational-data"></a>Példa – töltse ki a relációs adatok gyűjtése

Az Azure SQL Database nem rendelkezik olyan beépített adattípust, természetesen leképező `Collection(Edm.String)` mezőket az Azure Search szolgáltatásban. Karakterlánc-gyűjtemény mezők feltöltésére, a forrásadatok JSON karakterlánctömbként előzetes feldolgozása és majd a `jsonArrayToStringCollection` leképezési függvény.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Egy részletes példa, amely relációs adatok alakítja a gyűjtemény indexmező: [relációs adatok modellezése](search-example-adventureworks-modeling.md).
