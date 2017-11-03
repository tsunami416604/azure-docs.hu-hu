---
title: "Az Azure Search indexelők mező hozzárendelések"
description: "Konfigurálja a fiókot használja a mezőnevek és az adatok felelősséget Azure keresési indexelő mező leképezései"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 0325a4de-0190-4dd5-a64d-4e56601d973b
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/30/2017
ms.author: eugenesh
ms.openlocfilehash: 3f2ead208ea1525489a40d1fb637da47cd8a9b24
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="field-mappings-in-azure-search-indexers"></a>Az Azure Search indexelők mező hozzárendelések
Azure keresési indexelő használatához alkalmanként található saját kezűleg olyan esetekben, ahol a bemeneti adatok nem teljesen felel meg a sémának a cél index. Ezekben az esetekben használható **hozzárendelések mezőben** az adatok átalakítására be a kívánt alakot.

Bizonyos esetekben, ahol mező hozzárendelések hasznosak:

* Az adatforrás rendelkezik egy mező `_id`, de Azure Search aláhúzásjellel kezdődő mezőnevek nem engedélyezi. A mező leképezéseket "átnevezése" mező teszi lehetővé.
* Az azonos adatok forrás adatokhoz, például több mezőt feltöltéséhez, mert különböző elemzőkkel mezőkhöz alkalmazni kívánt szeretné. Mező hozzárendelések lehetővé teszik, hogy a "ágaztassa" egy adatforrás mezője.
* Meg kell Base64 kódolása vagy az adatok dekódolására. Mező hozzárendelések támogatja több **funkciók leképezési**, többek között a következőket funkciók a Base64 kódolási és dekódolási.   

## <a name="setting-up-field-mappings"></a>Mezők leképezésének beállítása
Egy új indexelő használatával létrehozásakor mező leképezéseket adhat hozzá a [létrehozása indexelő](https://msdn.microsoft.com/library/azure/dn946899.aspx) API. A mező leképezése egy indexelő indexelő használatával kezelheti a [frissítés indexelő](https://msdn.microsoft.com/library/azure/dn946892.aspx) API.

A mező leképezéseket három részből áll:

1. A `sourceFieldName`, amely jelzi, hogy egy mező az adatforrásban. E tulajdonság megadása kötelező.
2. Egy nem kötelező `targetFieldName`, amely jelöli az search-index mező. Ha nincs megadva, ahogy az adatforrás neve szolgál.
3. Egy nem kötelező `mappingFunction`, amely alakíthatja át az adatokat több egyikének használatával, előre definiált funkciók. A funkciók teljes listája [alatt](#mappingFunctions).

Mezők hozzárendelések hozzáadódnak a `fieldMappings` tömb az indexelő-definícióban.

Például ez hogyan úgy tud megfelelni mezőnevek különbségek:

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

Az indexelő rendelkezhet több mező leképezést. Ha például az alábbiakban hogyan akkor is "oszthatja ketté" mező:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" },
]
```

> [!NOTE]
> Az Azure Search nem betűérzékeny összehasonlító mező leképezései mező és függvény nevek feloldására használ. Ez kényelmes (nincs beolvasandó minden a kis-és jobb oldali), de azt jelenti, hogy az adatforrás vagy az index nem térnek el egymástól csak eset mezőket.  
>
>

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>A mező leképezési funkciók
Ezek a függvények jelenleg támogatottak:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

## <a name="base64encode"></a>base64Encode
Hajt végre *URL-cím szálbiztos* Base64 kódolást a bemeneti karakterlánc. Feltételezi, hogy a bemeneti UTF-8 kódolású.

### <a name="sample-use-case---document-key-lookup"></a>Példa használati eset - dokumentum kulcskeresési
Egy Azure Search dokumentum kulcs csak biztonságos URL-cím karakterek jelenhetnek meg (mivel az ügyfelek kell lennie arra, hogy a dokumentum segítségével a [keresési API](https://docs.microsoft.com/rest/api/searchservice/lookup-document), például). Ha az adatok URL-cím nem biztonságos karaktereket tartalmaz, és töltse fel adatokkal a keresési index kulcsmező használandó, a függvény használható. Ha a kulcs van kódolva, base64 dekódolása az eredeti érték beolvasása. További információkért lásd: a [base64 kódolási és dekódolási](#base64details) szakasz.

#### <a name="example"></a>Példa
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode" }
  }]
```

### <a name="sample-use-case---retrieve-original-key"></a>Példa használati eset - eredeti kulcs lekérése
Egy blob indexelő, hogy a blob elérési út metaadatokkal dokumentumkulcsként blobok indexek rendelkezik. A kódolt dokumentum kulcs beolvasása, után szeretne dekódolni a elérési utat, és töltse le a blob.

#### <a name="example"></a>Példa
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode", "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false } }
  }]
```

Ha nem kell kulcsok dokumentumok kereshet és is nem kell dekódolni a kódolt tartalom, csak hagyhatja `parameters` a leképezési függvény, amely alapértelmezés szerint használt érték `useHttpServerUtilityUrlTokenEncode` való `true`. Egyéb esetben lásd: [base64 részletek](#base64details) használja a beállítások a szakaszban található.

<a name="base64DecodeFunction"></a>

## <a name="base64decode"></a>base64Decode
Elvégzi a Base64 dekódolás a bemeneti karakterlánc. A bemeneti feltételezett, hogy egy *URL-cím szálbiztos* Base64 kódolású karakterlánc.

### <a name="sample-use-case"></a>Példa használati eset
A BLOB egyéni metaadat értékeknek kell lenniük ASCII-kódolású. Base64 kódolás használatával blob egyéni metaadat-tetszőleges UTF-8 karakterláncok képviseli. Azonban ahhoz, hogy a keresés kifejező, a funkció használata kapcsolja be a kódolt adatok vissza "rendszeres" karakterláncok azokat a keresési index való feltöltésekor.

#### <a name="example"></a>Példa
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode", "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false } }
  }]
```

Ha nem adja meg a `parameters`, majd az alapértelmezett érték `useHttpServerUtilityUrlTokenDecode` van `true`. Lásd: [base64 részletek](#base64details) használja a beállítások a szakaszban található.

<a name="base64details"></a>

### <a name="details-of-base64-encoding-and-decoding"></a>Kódolási és dekódolási base64 részleteit
Az Azure Search két base64 kódolást támogatja: HttpServerUtility URL-cím token és a biztonságos URL-cím base64 kódolás nélkül térközt. Az azonos kódolás a leképezési funkciók használható, ha szeretne kódolni tekintse meg a dokumentum kulcs mentése, fejteni, az indexelő értéket kódolni vagy egy mezőt az indexelő által kódolt dekódolása kell.

Ha használja a .NET-keretrendszer, akkor megadhatja `useHttpServerUtilityUrlTokenEncode` és `useHttpServerUtilityUrlTokenDecode` való `true`, a kódolási és dekódolási kulcsattribútumokkal. Majd `base64Encode` viselkedik [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) és `base64Decode` viselkedik [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Ha nem használja a .NET-keretrendszer, akkor célszerű `useHttpServerUtilityUrlTokenEncode` és `useHttpServerUtilityUrlTokenDecode` való `false`. Attól függően, hogy a könyvtárban, akkor használja, a base64 kódolás és segédprogram dekódolása funkciók Azure Search eltérő lehet.

Az alábbi táblázat összehasonlítja a különböző base64 kódolású karakterlánc `00>00?00`. Határozza meg a szükséges további feldolgozás (ha van ilyen) a base64 függvények, alkalmazza a szalagtár funkciót, a karakterlánc kódolása `00>00?00` és hasonlítsa össze a kimenet a várt kimeneti `MDA-MDA_MDA`.

| Encoding | Base64 kódolás kimenete | További könyvtár kódolás után feldolgozása | További feldolgozása előtt könyvtár dekódolása |
| --- | --- | --- | --- |
| A kitöltési Base64 | `MDA+MDA/MDA=` | Használjon biztonságos URL-cím karaktereket, és távolítsa el a szövegtávolság | Standard base64 karaktereket használja, és adja hozzá a szövegtávolság |
| A Base64 kitöltés nélkül | `MDA+MDA/MDA` | URL-cím szálbiztos karakterek használata | Standard base64 karakterek használata |
| Kitöltés a biztonságos URL-cím base64 | `MDA-MDA_MDA=` | Távolítsa el a szövegtávolság | Kitöltés hozzáadása |
| URL-cím szálbiztos base64 kitöltés nélkül | `MDA-MDA_MDA` | None | None |

<a name="extractTokenAtPositionFunction"></a>

## <a name="extracttokenatposition"></a>extractTokenAtPosition
Felosztja a mezőnek a megadott elválasztó használatával, és a token szerzi az eredményül kapott felosztása a megadott helyen.

Például, ha a bemeneti `Jane Doe`, a `delimiter` van `" "`(hely) és a `position` 0, az eredmény `Jane`; Ha a `position` 1, az eredmény `Doe`. Ha a pozíció hivatkozik egy jogkivonatot, amely nem létezik, hibát ad vissza.

### <a name="sample-use-case"></a>Példa használati eset
Az adatforrás tartalmaz egy `PersonName` mező, és szeretné index, két külön `FirstName` és `LastName` mezőket. Ez a funkció segítségével ossza fel a bemeneti az elválasztó karakter a szóköz karakter használatával.

### <a name="parameters"></a>Paraméterek
* `delimiter`: a felosztásának eredménye a bemeneti karakterlánc az elválasztóelemként használt karakterlánc.
* `position`: az egész nulláról indulva számolt helyzetét a jogkivonat után a bemeneti karakterlánc van szétosztva kiválasztásához.    

### <a name="example"></a>Példa
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

## <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection
Átalakítja az formázott JSON-tömb karakterláncok való feltöltéséhez használt karakterlánc-tömbben karakterlánc egy `Collection(Edm.String)` mező mellett az index.

Például, ha a bemeneti karakterlánc `["red", "white", "blue"]`, majd a cél mezőben típusú `Collection(Edm.String)` tölti fel a következő három érték `red`, `white`, és `blue`. A bemeneti értékek, amelyek a JSON-tömbök karakterlánc nem értelmezhető hibát ad vissza.

### <a name="sample-use-case"></a>Példa használati eset
Az Azure SQL-adatbázis nem rendelkezik olyan beépített adattípusú, amely leképezhető természetes `Collection(Edm.String)` az Azure Search mezőket. Gyűjtemény karakterláncmezőket feltöltése, a forrásadatok JSON karakterlánc tömbként formázása, és ezzel a funkcióval.

### <a name="example"></a>Példa
```JSON

"fieldMappings" : [
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
]
```


## <a name="help-us-make-azure-search-better"></a>Segítsen az Azure Search továbbfejlesztésében
Ha a szolgáltatás-kérelmek vagy ötleteket javításai, lépjen kapcsolatba velünk a a [UserVoice webhelyén](https://feedback.azure.com/forums/263029-azure-search/).
