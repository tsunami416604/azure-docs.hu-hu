---
title: Mezőleképezések az indexelőkben Azure Search
description: Azure Search indexelő mező-leképezések a mezőnevek és az adatok formátumban különbségeit fiók konfigurálása
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 08/30/2017
ms.author: eugenesh
ms.openlocfilehash: 51fa689030c4a8ce4e900ecd600cdd0524aa13d9
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2018
ms.locfileid: "42059470"
---
# <a name="field-mappings-in-azure-search-indexers"></a>Mezőleképezések az indexelőkben Azure Search
Azure Search-indexelők használatakor az időnként találhatja meg saját maga helyzetekben, ahol a bemeneti adatok nem teljesen felel meg a célindex sémája. Ezekben az esetekben használható **mezőleképezéseivel** átalakítja az adatokat a kívánt formázásához.

Bizonyos helyzetekben, ahol Mezőleképezések hasznosak:

* Az adatforrás tartozik, a mező `_id`, de nem engedélyezi az Azure Search aláhúzással kezdődő mezőnevek. Mezőleképezés "nevezze át" mező teszi lehetővé.
* Fel kívánja tölteni a több mező adatok forrása az adatokról, például az index, mert ezekhez a mezőkhöz más elemzők alkalmazni szeretné. Mező-leképezések lehetővé teszik egy adatforrásmezőnek "ágaztatnia".
* Van szüksége a Base64 kódolás, vagy az adatok dekódolása. Mezőleképezések támogatja a több **funkciók leképezési**, többek között funkciók Base64 a kódolási és dekódolási.   

## <a name="setting-up-field-mappings"></a>Mezőleképezések beállítása
Mezőleképezések létrehozásakor egy új indexelő segítségével hozzáadhatja a [indexelő létrehozása](https://msdn.microsoft.com/library/azure/dn946899.aspx) API-t. Mezőleképezések az indexelési indexelő használatával kezelheti a [frissítés indexelő](https://msdn.microsoft.com/library/azure/dn946892.aspx) API-t.

Mezőleképezés három részből áll:

1. A `sourceFieldName`, amely jelöli, hogy az adatforrás egy mezője. Ez a tulajdonság megadása kötelező.
2. Egy nem kötelező `targetFieldName`, amely egy mezőt a keresési index jelöli. Ha nincs megadva, ugyanazzal a névvel, mint az adatforrást használja.
3. Egy nem kötelező `mappingFunction`, amely alakíthatja át az adatokat az egyik több előre meghatározott függvényeket. A funkciók teljes listája [alább](#mappingFunctions).

Mező-leképezések kerülnek a `fieldMappings` az indexelő definíciója a tömb.

Például a következő hogyan mezőnevek különbségek képes kezelni:

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

Az indexelő rendelkezhet több mező-leképezések. Például az alábbiakban hogyan meg lehet "ágaztatnia" mező:

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

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>A mező leképezés funkciók
Ezek a függvények jelenleg támogatja:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

## <a name="base64encode"></a>base64Encode
Hajt végre *URL-cím környezetben is biztonságos* Base64 kódolás a bemeneti karakterláncot. Feltételezi, hogy a bemeneti UTF-8 kódolású.

### <a name="sample-use-case---document-key-lookup"></a>Minta használatieset - dokumentum kulcskeresés
Csak URL-cím biztonságos karakterek szerepelhetnek egy Azure Search-dokumentum kulcsot (mivel ügyfeleknek kell lennie arra, hogy a dokumentum használata a [keresési API](https://docs.microsoft.com/rest/api/searchservice/lookup-document), például). Ha az adatok URL-címekben nem biztonságos karaktereket tartalmaz, és a egy kulcsmező a search-index feltöltéséhez használandó, a függvény használható. Miután a kulcs van kódolva, az eredeti értéket dekódolandó base64. További információkért lásd: a [base64 kódolási és dekódolási](#base64details) szakaszban.

#### <a name="example"></a>Példa
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode" }
  }]
```

### <a name="sample-use-case---retrieve-original-key"></a>Példa használati eset – az eredeti kulcs lekérése
Rendelkezik egy blob indexelőjével, amely a blob elérési útja metaadatokkal a dokumentum kulcsaként blobok indexeket. A kódolt dokumentum kulcs beolvasása, után szeretné dekódolni az elérési utat, és a blob letöltéséhez.

#### <a name="example"></a>Példa
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode", "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false } }
  }]
 ```

Ha nem kell a kulcsok dokumentumok keresése, és szintén nem kell való dekódolandó a kódolt tartalom, csak hagyhatja `parameters` a leképezési függvény, amely alapértelmezés szerint `useHttpServerUtilityUrlTokenEncode` való `true`. Egyéb esetben [base64 részletek](#base64details) szakasz dönthet arról, hogy milyen beállításokat kíván használni.

<a name="base64DecodeFunction"></a>

## <a name="base64decode"></a>base64Decode
Végrehajtja a Base64 kódolású bemeneti karakterlánc dekódolása. A bemeneti feltételezhető, hogy egy *URL-cím környezetben is biztonságos* Base64-kódolású karakterlánc.

### <a name="sample-use-case"></a>Példa használati esetekhez
BLOB egyéni metaadatértékeket ASCII-kódolású kell lennie. Base64 kódolást segítségével egyéni blobmetaadatainak tetszőleges UTF-8 karakterláncokat jelölik. Azonban ahhoz, hogy a keresési jelentéssel bíró, használhatja ezt a funkciót a search-index adatokkal való feltöltésekor vissza alakítsa "normál" karakterláncok a kódolt adatait.

#### <a name="example"></a>Példa
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode", "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false } }
  }]
```

Ha nem ad meg bármelyik `parameters`, majd az alapértelmezett érték a `useHttpServerUtilityUrlTokenDecode` van `true`. Lásd: [base64 részletek](#base64details) szakasz dönthet arról, hogy milyen beállításokat kíván használni.

<a name="base64details"></a>

### <a name="details-of-base64-encoding-and-decoding"></a>Kódolási és dekódolási base64 részletei
Az Azure Search két base64 kódolást támogatja: HttpServerUtility URL-cím token és URL-cím környezetben is biztonságos base64 kódolást kitöltése nélkül. Ugyanolyan kódolású adatokként a hozzárendelés-függvényeket, ha azt szeretné, tekintse meg a dokumentum kulcsát kódolása mentése, az indexelő dekódolandó értéket kódolni vagy egy mezőt az indexelő kódolású dekódolni kell.

Ha `useHttpServerUtilityUrlTokenEncode` vagy `useHttpServerUtilityUrlTokenDecode` kódolási és dekódolási rendre paraméterek vannak beállítva `true`, majd `base64Encode` viselkedik [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) és `base64Decode` viselkedik [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Ha nem használja a teljes .NET-keretrendszer (azaz a .NET Core vagy használ más programozási környezetet) az Azure Search viselkedésének emulációjához a kulcs értékeit állítja elő, majd állítsa be `useHttpServerUtilityUrlTokenEncode` és `useHttpServerUtilityUrlTokenDecode` való `false`. Attól függően, használja a könyvtárban, a base64 kódolása és dekódolása segédprogram funkciók az Azure Search eltérő lehet.

Az alábbi táblázat összehasonlítja a különböző base64 kódolású karakterlánc `00>00?00`. Annak megállapításához, a szükséges további feldolgozás (ha van ilyen) a base64-függvények, a alkalmazni a szalagtár funkciót, a karakterlánc kódolása `00>00?00` és összehasonlítja a runbooknak milyen kimenetet `MDA-MDA_MDA`.

| Encoding | Base64 kódolás kimenete | További erőforrástár-kódolás után feldolgozása | További feldolgozásra, mielőtt az erőforrástár-dekódolás |
| --- | --- | --- | --- |
| A kitöltés Base64 | `MDA+MDA/MDA=` | URL-cím környezetben is biztonságos karaktereket használni, és távolítsa el a kitöltés | Standard Base64-kódolású karakterek, és adja hozzá a kitöltés |
| Base64 kitöltése nélkül | `MDA+MDA/MDA` | URL-cím környezetben is biztonságos karakterek használhatók | Standard Base64-kódolású karakterek használhatók |
| URL-cím környezetben is biztonságos base64-kitöltés | `MDA-MDA_MDA=` | Távolítsa el a kitöltés | Kitöltés hozzáadása |
| URL-cím környezetben is biztonságos base64 kitöltése nélkül | `MDA-MDA_MDA` | None | None |

<a name="extractTokenAtPositionFunction"></a>

## <a name="extracttokenatposition"></a>extractTokenAtPosition
Felosztja a megadott elválasztó használatával karakterlánc típusú, és kiválasztja a jogkivonat az eredményül kapott felosztása a megadott helyen.

Például, ha a bemeneti `Jane Doe`, a `delimiter` van `" "`(terület) és a `position` 0, az eredmény `Jane`; Ha a `position` : 1, az eredmény `Doe`. Ha a pozíció hivatkozik egy jogkivonatot, amely nem létezik, hibát ad vissza.

### <a name="sample-use-case"></a>Példa használati esetekhez
Az adatforrás tartalmaz egy `PersonName` mező, és azt szeretné, két külön tárgymutatóhoz `FirstName` és `LastName` mezőket. Ez a függvény használatával az elválasztó karakter a szóköz karakter használata a bemeneti felosztása.

### <a name="parameters"></a>Paraméterek
* `delimiter`: egy karakterláncot megfeleltessen elválasztóként használhatja, ha a bemeneti sztring felosztásához.
* `position`: egy egész számolt helyzetét megadja a jogkivonat választja ki, miután a bemeneti karakterlánc van felosztva.    

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
Egy karakterlánc formátumú JSON-tömböt, karakterláncok való feltöltéséhez használható karakterlánc-tömbben alakítja át egy `Collection(Edm.String)` mezőt az indexben.

Például, ha a bemeneti karakterlánc `["red", "white", "blue"]`, majd a célmező típusú `Collection(Edm.String)` tölti fel a három értékek `red`, `white`, és `blue`. A bemeneti értékek, amelyek nejde analyzovat jako JSON-karakterlánc tömbök hibát ad vissza.

### <a name="sample-use-case"></a>Példa használati esetekhez
Az Azure SQL database nem rendelkezik olyan beépített adattípust, természetesen leképező `Collection(Edm.String)` mezőket az Azure Search szolgáltatásban. Karakterlánc-gyűjtemény mezők feltöltése, a forrásadatok karakterlánctömbként JSON formátumban, és e funkció használatához.

### <a name="example"></a>Példa
```JSON

"fieldMappings" : [
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
]
```


## <a name="help-us-make-azure-search-better"></a>Segítsen jobbá Azure Search
Ha funkcióra vonatkozó javaslata vagy ötlete van javításai, lépjen kapcsolatba velünk a kapcsolatot a a [UserVoice webhelyén](https://feedback.azure.com/forums/263029-azure-search/).
