---
title: Hozzon létre indexelőt (az Azure Search szolgáltatás REST api-version = 2017-11-11 – előzetes verzió)
description: Az előnézetben API indexelők vannak terjeszteni outputFieldMapping paraméterek dúsító kimeneti leképezhető egy mező az Azure Search-index.
services: search
manager: pablocas
author: luiscabrer
ms.author: luisca
ms.date: 05/01/2018
ms.prod: azure
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.openlocfilehash: d47b14342caf0312e052584d20f1a9c86ca29cad
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>Hozzon létre indexelőt (az Azure Search szolgáltatás REST api-version = 2017-11-11 – előzetes verzió)

Az API-referencia a dokumentációban kognitív keresési indexelő fejlesztései kiterjedő preview-specifikus verziója is.

Például a [általánosan elérhető](https://docs.microsoft.com/rest/api/searchservice/create-indexer) verzió, létrehozhat egy új indexelő belül az Azure Search szolgáltatást használja a HTTP POST-kérelmet. 

```http
POST https://[service name].search.windows.net/indexers?api-version=[api-version]  
    Content-Type: application/json  
    api-key: [admin key]  
```  
A **api-kulcs** egy adminisztrációs kulcsot (szemben a lekérdezési kulcs) kell lennie. A hitelesítési részében [az Azure Search biztonsági](search-security-overview.md) kulcsok tájékozódhat. [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) a szolgáltatás URL-címe és a kérésben használt tulajdonságait ismerteti.

Azt is megteheti PUT használja, és adja meg az adatforrás neve URI-n. Ha az adatforrás nem létezik, a rendszer létrehozza.  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
A **api-version** szükséges. A jelenlegi verzió: `2016-09-01`. Lásd: [az Azure Search API-verziók](search-api-versions.md) részleteiről.

Adatok-platform-specifikus indexelők létrehozásával útmutatásért kezdődnie [indexelők áttekintése](search-indexer-overview.md), mely tartalmazza a teljes listáját [kapcsolódó cikkekben](search-indexer-overview.md#next-steps).

> [!NOTE]  
>  IP-címek függ az indexelők engedélyezett maximális számát. Az ingyenes szolgáltatás lehetővé teszi, hogy a 3 indexelők. Standard szolgáltatás lehetővé teszi, hogy 50 indexelők. Lásd: [szolgáltatásra vonatkozó korlátozások](search-limits-quotas-capacity.md) részleteiről.    

## <a name="request"></a>Kérés  
 A kérelem törzsében egy indexelő definíciót, amely meghatározza az adatforrás és a cél indexe indexelő, valamint a nem kötelező indexelési ütemezés és a paraméterek.  

 A szerkezetének kialakítása a kérelem hasznos szintaxisa a következő. Egy minta kérelem valósul meg a témakör későbbi részében.  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "schedule" : { Optional. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```

### <a name="indexer-schedule"></a>Az indexelő ütemezése  
Az indexelő opcionálisan megadható ütemezés szerint. Ha ütemezés szerint meg adva, az indexelő rendszeres ütemezés szerint futtatja. Az ütemező rendszer beépített; egy külső ütemező nem használható. **Ütemezés** az alábbi attribútumok tartoznak: 

-   **időköz**: kötelező. Egy időtartamértéket, amely megadja az időközt vagy az indexelő időszak fut. A legkisebb megengedett intervallum értéke 5 perc; a leghosszabb egy nap. Az XSD "daytimeduration típusú" értéknek kell formázni (korlátozott részhalmaza egy [ISO 8601 időtartama](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) érték). A minta: `"P[nD][T[nH][nM]]".` példák: `PT15M` a 15 percenként `PT2H` minden 2 órán át.  

-   **startTime**: nem kötelező. Az indexelő kell elindultak, amikor egy UTC datetime.  

### <a name="indexer-parameters"></a>Az indexelő paraméterek  
 Az indexelő Megadja annak viselkedését befolyásoló több paramétert. Az alább felsorolt paraméterek opcionálisak.  

-   **maxFailedItems**: az elemek száma, amelyek egy futó indexelő hiba indexelése sikertelen lehet. Alapértelmezett érték 0. Sikertelen elemek információt ad vissza a [indexelő állapotának beolvasása &#40;Azure Search szolgáltatás REST API&#41; ](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) műveletet.  

-   **maxFailedItemsPerBatch**: az elemek száma, amelyek az egyes kötegekben indexelése egy futó indexelő hibát sikertelen lehet. Alapértelmezett érték 0.  

-   **batchSize:** beolvasása az adatforrásból és indexelt elemek számát adja meg egy kötegben teljesítményének javítása érdekében. Az alapértelmezett függ az adatforrás típusa: Azure SQL és az Azure Cosmos DB 1000, és az Azure Blob Storage 10.

### <a name="field-mapping-parameters"></a>A mező leképezési paraméterek

Az indexelő definíciók mező társítások mező leképezése egy mezőt az Azure Search-index tartalmaz. Attól függően, hogy a tartalom átvitele követi közvetlen vagy bővített elérési társítások két típusa van:

+ **fieldMappings** nem kötelező, ha a forrás-cél mező nevei nem egyeznek, vagy ha szeretne megadni egy függvényt alkalmazza.
+ **outputFieldMappings** szükség, ha az Ön által létrehozott [egy dúsító adatcsatorna](cognitive-search-concept-intro.md). Egy dúsító sorban a kimeneti mező egy olyan konstrukció, a dúsító során meghatározott. A kimeneti mező Előfordulhat például, a dokumentum két külön mezői alapján dúsító során beépített összetett struktúrában. 

#### <a name="fieldmappings"></a>fieldMappings

A következő példának, vegye fontolóra a forrástábla mezőt `_id`. Az Azure Search nem engedélyezi a mező kell átnevezni, aláhúzásjelet, kezdve mező nevét. Ehhez használja a `fieldMappings` tulajdonsága az indexelő az alábbiak szerint:

```json
"fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
```

Több mező leképezéseket adhat meg:

```json
"fieldMappings" : [
    { "sourceFieldName" : "_id", "targetFieldName" : "id" },
    { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" }
]
```

A forrás- és cél mezőnevek nem különböztetik meg.

Ahol mező hozzárendelések hasznosak forgatókönyvekkel kapcsolatos további tudnivalókért lásd: [keresési indexelő mező hozzárendelések](https://docs.microsoft.com/azure/search/search-indexer-field-mappings).

#### <a name="outputfieldmappings"></a>outputFieldMappings

A kognitív keresési forgatókönyvek, amelyben egy skillset az indexelő van kötve, hozzá kell adnia `outputFieldMappings` rendelje hozzá a kimenetet egy dúsító lépés, amely az index kereshető mező tartalmát.

```json
  "outputFieldMappings" : [
        {
          "sourceFieldName" : "/document/organizations", 
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*", 
          "targetFieldName" : "keyphrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "language",
            "mappingFunction": null
        }      
   ],
```

<a name="FieldMappingFunctions"></a>

#### <a name="field-mapping-functions"></a>A mező leképezése

Mező hozzárendelések is használható forrás mező értékét átalakítására *leképezési funkciók mezőben*. Például egy tetszőleges karakterlánc lehet base64-kódolású így egy dokumentum kulcsmező feltöltéséhez használható.

Mikor és hogyan mező leképezési funkciók kapcsolatos további információkért lásd: [mező leképezési funkciók](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions).

### <a name="request-body-examples"></a>Példák a szervezet  
 Az alábbi példakód létrehozza indexelőt, amely által hivatkozott tábla adatait átmásolja a `ordersds` a forrás a `orders` index ütemezés szerint 2015 jan. 1 UTC elindul, és óránként futtat. Minden indexelő meghívás sikeres, ha 5-nél több elemek indexelése meghiúsulhat, ha az egyes kötegekben, és legfeljebb 10 elemet indexelése meghiúsulhat, ha összesen.  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

## <a name="response"></a>Válasz  
 A kérelem sikeres 201 létrehozva.  

## <a name="see-also"></a>Lásd még

+ [Kognitív keresési áttekintése](cognitive-search-concept-intro.md)
+ [Gyors üzembe helyezés: Próbálja kognitív keresése](cognitive-search-quickstart-blob.md)
+ [Hogyan mezők](cognitive-search-output-field-mapping.md)
