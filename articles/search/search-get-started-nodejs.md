---
title: 'Rövid útmutató: Keresési index létrehozása a Node.js-ben a REST API-k használatával'
titleSuffix: Azure Cognitive Search
description: Ebben a Node.js rövid útmutatóban megtudhatja, hogyan hozhat létre indexet, tölthet be adatokat, és futtathat lekérdezéseket az Azure Cognitive Search-en JavaScript és a REST API-k használatával.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.devlang: nodejs
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/25/2020
ms.openlocfilehash: cbef6029b93f134f95ee54aa87ce0dd65bcdf50d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77623997"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-nodejs-using-rest-apis"></a>Rövid útmutató: Hozzon létre egy Azure Cognitive Search indexet a Node.js-ben a REST API-k használatával
> [!div class="op_single_selector"]
> * [Javascript](search-get-started-nodejs.md)
> * [C #](search-get-started-dotnet.md)
> * [Portál](search-get-started-portal.md)
> * [Powershell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Hozzon létre egy Node.js alkalmazást, amely létrehoz, betölt és lekérdezi az Azure Cognitive Search indexét. Ez a cikk bemutatja, hogyan hozhat létre az alkalmazást lépésről-lépésre. Másik lehetőségként [letöltheti a forráskódot és az adatokat,](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/) és futtathatja az alkalmazást a parancssorból.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A következő szoftvereket és szolgáltatásokat használtuk a rövid útmutató létrehozásához és teszteléséhez:

+ [Node.js](https://nodejs.org)

+ [Az NPM-et](https://www.npmjs.com) a Node.js-nek kell telepítenie

+ A cikk ben vagy a [tártár **rövid útmutatókönyvtárából** mintaindex-struktúra és egyező dokumentumok találhatók.](https://github.com/Azure-Samples/azure-search-javascript-samples/)

+ [Hozzon létre egy Azure Cognitive Search szolgáltatást,](search-create-service-portal.md) vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. A rövid útmutatóhoz ingyenes szolgáltatást használhat.

Ajánlott:

* [Visual Studio kód](https://code.visualstudio.com)

* [Szebb](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) és [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) kiterjesztések a VSCode-hoz.

<a name="get-service-info"></a>

## <a name="get-keys-and-urls"></a>Kulcsok és URL-címek beszerezni

A szolgáltatás hívásai minden kéréshez url-végpontot és hozzáférési kulcsot igényelnek. A keresési szolgáltatás mindkettővel jön létre, így ha hozzáadta az Azure Cognitive Search-et az előfizetéséhez, kövesse az alábbi lépéseket a szükséges információk beszerezéséhez:

1. [Jelentkezzen be az Azure Portalra](https://portal.azure.com/), és a keresési szolgáltatás **áttekintése** lapon a keresési szolgáltatás nevét. A szolgáltatás nevét a végpont URL-címének áttekintésével ellenőrizheti. Ha a végpont `https://mydemo.search.windows.net`URL-címe lenne, `mydemo`a szolgáltatás neve a .

2. A **Beállítások** > **kulcsok**párbeszédpanelen szerezzen be egy rendszergazdai kulcsot a szolgáltatás teljes jogához. Két cserélhető rendszergazdai kulcs van, amelyek az üzletmenet folytonosságát biztosítják arra az esetre, ha át kell görgetnie egyet. Az elsődleges vagy másodlagos kulcsot objektumok hozzáadására, módosítására és törlésére irányuló kérelmeken használhatja.

    A lekérdezési kulcs beszerezése is. Ajánlott csak olvasási hozzáféréssel rendelkező lekérdezési kérelmeket kiadni.

![A szolgáltatás névének, a rendszergazdai és lekérdezési kulcsoknak a beszereznie](media/search-get-started-nodejs/service-name-and-keys.png)

Minden kérelemhez api-kulcs szükséges a szolgáltatásnak küldött minden kérés fejlécében. Az érvényes kulcs kérésenként megbízhatóságot hoz létre a kérelmet küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="set-up-your-environment"></a>A környezet kialakítása

Először nyisson meg egy Powershell-konzolt vagy más olyan környezetet, amelyben telepítette a Node.js-t.

1. Hozzon létre egy fejlesztési `quickstart` könyvtárat, amely a következő nevet adja neki:

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. Üres projekt inicializálása `npm init`az NPM programmal a futtással. Fogadja el az alapértelmezett értékeket, kivéve a Licencet, amelyet "MIT" értékre kell állítania. 

1. Add csomagok, amelyek függ a kód és a támogatás a fejlesztés:

    ```powershell
    npm install nconf node-fetch
    npm install --save-dev eslint eslint-config-prettier eslint-config-airbnb-base eslint-plugin-import prettier
    ```

4. Ellenőrizze, hogy konfigurálta-e a projekteket és annak függőségeit, ellenőrizve, hogy a **package.json** fájl hasonló-e a következőhöz:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Cognitive Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Azure_Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "nconf": "^0.10.0",
        "node-fetch": "^2.6.0"
      },
      "devDependencies": {
        "eslint": "^6.1.0",
        "eslint-config-airbnb-base": "^13.2.0",
        "eslint-config-prettier": "^6.0.0",
        "eslint-plugin-import": "^2.18.2",
        "prettier": "^1.18.2"
      }
    }
    ```

5. Hozzon létre egy fájlt **azure_search_config.json** fájllal a keresési szolgáltatás adatainak tárolására:

    ```json
    {
        "serviceName" : "[SEARCH_SERVICE_NAME]",
        "adminKey" : "[ADMIN_KEY]",
        "queryKey" : "[QUERY_KEY]",
        "indexName" : "hotels-quickstart"
    }
    ```

Cserélje `[SERVICE_NAME]` le az értéket a keresési szolgáltatás nevére. Cserélje `[ADMIN_KEY]` `[QUERY_KEY]` le és helyezze vissza a korábban rögzített kulcsértékeket. 

## <a name="1---create-index"></a>1 - Index létrehozása 

Fájl létrehozása **hotels_quickstart_index.json fájlhoz.**  Ez a fájl határozza meg, hogyan működik az Azure Cognitive Search a következő lépésben betöltendő dokumentumokkal. Minden mezőt egy `name` azonosít, és `type`rendelkezik egy megadott . Minden mező indexattribútumok sorozatával is rendelkezik, amelyek meghatározzák, hogy az Azure Cognitive Search kereshet-e, szűrhet, rendezhet és a mezőt. A legtöbb mező egyszerű adattípus, de `AddressType` néhány, például összetett típusok, amelyek lehetővé teszik, hogy gazdag adatstruktúrákat hozzon létre az indexben.  A [támogatott adattípusokról](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) és [indexattribútumokról](https://docs.microsoft.com/azure/search/search-what-is-an-index#index-attributes)olvashat bővebben. 

Adja hozzá a következőket **a hotels_quickstart_index.json** fájlhoz, vagy [töltse le a fájlt.](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels_quickstart_index.json) 

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```
    

Célszerű elkülöníteni egy adott forgatókönyv sajátosságait a kódtól, amely tágan alkalmazható lesz. Az `AzureSearchClient` **AzureSearchClient.js** fájlban definiált osztály tudni fogja, hogyan hozhat létre kérési URL-címeket, hogyan kérhet kérelmet a Fetch API használatával, és reagál a válasz állapotkódjára.

Kezdje nőbb az **AzureSearchClient.js** fájlon a **csomópontlehívási** csomag importálásával és egy egyszerű osztály létrehozásával. Az osztály cserélhető részeinek elkülönítése a `AzureSearchClient` konstruktornak átadva a különböző konfigurációs értékeket:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    constructor(searchServiceName, adminKey, queryKey, indexName) {
        this.searchServiceName = searchServiceName;
        this.adminKey = adminKey;
        // The query key is used for read-only requests and so can be distributed with less risk of abuse.
        this.queryKey = queryKey;
        this.indexName = indexName;
        this.apiVersion = '2019-05-06';
    }

    // All methods go inside class body here!
}

module.exports = AzureSearchClient;
```

Az osztály első feladata, hogy tudja, hogyan kell létrehozni URL-eket, amelyekre a különböző kéréseket küldeni lehet. Ezeket az URL-címeket olyan példánymetódusokkal hozhatja létre, amelyek az osztálykonstruktornak átadott konfigurációs adatokat használják. Figyelje meg, hogy az általuk készített URL-cím egy API-verzióra `2019-05-06`jellemző, és rendelkeznie kell egy argumentumkal, amely megadja az adott verziót (ebben az alkalmazásban). 

Az első ilyen módszer adja vissza az index URL-címét is. Adja hozzá a következő módszert az osztálytörzsbe:

```javascript
getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }

```

A következő `AzureSearchClient` feladata, hogy egy aszinkron kérelmet a Fetch API-t. Az aszinkron statikus metódus `request` egy URL-t, egy HTTP metódust meghatározó karakterláncot ("GET", "PUT", "POST", "DELETE"), a kérelemben használandó kulcsot és egy választható JSON-objektumot vesz fel. A `headers` változó `queryKey` leképezi a (akár a felügyeleti kulcs, akár az írásvédett lekérdezési kulcs) az "api-key" HTTP-kérelem fejlécéhez. A kérelem beállításai `method` mindig tartalmazzák `headers`a használandó és a . Ha `bodyJson` `null`nem, akkor a HTTP-kérelem törzse a `bodyJson`karakterlánc-ábrázolásra van állítva. A `request` metódus visszaadja a Fetch API a HTTP-kérelem végrehajtására vonatkozó ígéretét.

```javascript
static async request(url, method, apiKey, bodyJson = null) {
    // Uncomment the following for request details:
    /*
    console.log(`\n${method} ${url}`);
    console.log(`\nKey ${apiKey}`);
    if (bodyJson !== null) {
        console.log(`\ncontent: ${JSON.stringify(bodyJson, null, 4)}`);
    }
    */

    const headers = {
        'content-type' : 'application/json',
        'api-key' : apiKey
    };
    const init = bodyJson === null ?
        { 
            method, 
            headers
        }
        : 
        {
            method, 
            headers,
            body : JSON.stringify(bodyJson)
        };
    return fetch(url, init);
}
```

A demo célokra, csak dobjon kivételt, ha a HTTP-kérelem nem sikeres. Egy valós alkalmazás, akkor valószínűleg néhány naplózási és diagnosztikai `response` a HTTP-állapotkód a keresési szolgáltatás kérelmet. 
    
```javascript
static throwOnHttpError(response) {
    const statusCode = response.status;
    if (statusCode >= 300){
        console.log(`Request failed: ${JSON.stringify(response, null, 4)}`);
        throw new Error(`Failure in request. HTTP Status was ${statusCode}`);
    }
}
```

Végül adja hozzá az Azure Cognitive Search index észlelési, törlési és létrehozási módszereit. Ezek a módszerek mind ugyanazt a struktúrát:

* A végpont bekérése, amelyre a kérelem kerül sor.
* A kérelem létrehozása a megfelelő végpont, HTTP-művelet, API-kulcs, és adott esetben egy JSON-törzs. `indexExistsAsync()`és `deleteIndexAsync()` nincs JSON szervezet, `createIndexAsync(definition)` de nem.
* `await`a kérésre adott választ.  
* Cselekedjen a válasz állapotkódján.
* Visszaad egy megfelelő értékű ígéretet `this`(logikai értéket vagy a lekérdezés eredményét). 

```javascript
async indexExistsAsync() { 
    console.log("\n Checking if index exists...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "GET", this.adminKey);
    // Success has a few likely status codes: 200 or 204 (No Content), but accept all in 200 range...
    const exists = response.status >= 200 && response.status < 300;
    return exists;
}

async deleteIndexAsync() {
    console.log("\n Deleting existing index...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "DELETE", this.adminKey);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}

async createIndexAsync(definition) {
    console.log("\n Creating index...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "PUT", this.adminKey, definition);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

Ellenőrizze, hogy a metódusok az osztályon belül vannak-e, és hogy exportálja-e az osztályt. Az **AzureSearchClient.js** legkülső hatókörének a következőnek kell lennie:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    // ... code here ...
}

module.exports = AzureSearchClient;
```

Az objektumorientált osztály jó választás volt a potenciálisan újrafelhasználható **AzureSearchClient.js** modulhoz, de nem szükséges a fő programhoz, amelyet **egy index.js**nevű fájlba kell helyezni. 

Hozzon létre **index.js,** és kezdjük azzal, hogy az:

* Az **nconf** csomag, amely rugalmasságot biztosít a JSON, a környezeti változók vagy a parancssori argumentumok konfigurációjának megadásához.
* A **hotels_quickstart_index.json** fájlból származó adatok.
* Az `AzureSearchClient` modult.

```javascript
const nconf = require('nconf');

const indexDefinition = require('./hotels_quickstart_index.json');
const AzureSearchClient = require('./AzureSearchClient.js');
```

Az [ **nconf** csomag](https://github.com/indexzero/nconf) lehetővé teszi a konfigurációs adatok megadását különböző formátumokban, például környezeti változókban vagy a parancssorban. Ez a minta az **nconf-ot** alapmódon használja a **azure_search_config.json** fájl olvasásához és a fájl tartalmának szótárként való visszaküldéséhez. Az **nconf** `get(key)` függvény használatával gyorsan ellenőrizheti, hogy a konfigurációs adatok megfelelően testre lettek-e szabva. Végül a függvény a konfigurációt adja vissza:

```javascript
function getAzureConfiguration() {
    const config = nconf.file({ file: 'azure_search_config.json' });
    if (config.get('serviceName') === '[SEARCH_SERVICE_NAME]' ) {
        throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
    }
    return config;
}
```

A `sleep` függvény `Promise` létrehoz egy olyan függvényt, amely egy megadott idő elteltével feloldódik. Ezzel a funkcióval az alkalmazás szüneteltetheti az aszinkron indexműveletek befejezését és elérhetővé válását. Ilyen késleltetés hozzáadása általában csak demók, tesztek és mintaalkalmazások esetén szükséges.

```javascript
function sleep(ms) {
    return(
        new Promise(function(resolve, reject) {
            setTimeout(function() { resolve(); }, ms);
        })
    );
}
```

Végül adja meg és hívja meg `run` a fő aszinkron függvényt. Ez a függvény sorrendben hívja meg a `Promise`többi függvényt, és az s feloldásához szükséges ideig vár.

* A korábban `getAzureConfiguration()` írt konfiguráció beolvasása
* Új `AzureSearchClient` példány létrehozása, értékek átadása a konfigurációból
* Ellenőrizze, hogy az index létezik-e, és ha igen, törölje
* Index létrehozása a `indexDefinition` **hotels_quickstart_index.json-ból** betöltött használatával

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
    } catch (x) {
        console.log(x);
    }
}

run();
```

Ne felejtsd el az `run()`utolsó hívást! Ez a program bejárata, amikor `node index.js` a következő lépésben futsz.

Figyelje `AzureSearchClient.indexExistsAsync()` `AzureSearchClient.deleteIndexAsync()` meg ezt, és ne vegyen figyelembe paramétereket. Ezek a `AzureSearchClient.request()` függvények argumentum nélkül `bodyJson` hívnak. A `AzureSearchClient.request()`, `bodyJson === null` `true`mivel `init` a szerkezet csak a HTTP-ige ("GET" `indexExistsAsync()` és `deleteIndexAsync()`"DELETE" ) és a fejlécek, amelyek meghatározzák a kérelemkulcsot.  

Ezzel szemben `AzureSearchClient.createIndexAsync(indexDefinition)` a módszer _nem_ vesz egy paramétert. A `run` függvény `index.js`a alkalmazásban átadja a fájl tartalmát **hotels_quickstart_index.json** fájlnak a `AzureSearchClient.createIndexAsync(indexDefinition)` metódusnak. A `createIndexAsync()` metódus átadja ezt `AzureSearchClient.request()`a definíciót a alkalmazásnak. A `AzureSearchClient.request()` `bodyJson === null` rendszerben, `false`mivel `init` jelenleg is, a struktúra nem csak a HTTP-igét `body` ("PUT") és a fejléceket tartalmazza, hanem az indexdefiníciós adatokat is beállítja.

### <a name="prepare-and-run-the-sample"></a>A minta előkészítése és futtatása

A következő parancsokhoz használjon terminálablakot.

1. Nyissa meg a **package.json** fájlt tartalmazó mappát és a kód többi részét.
1. Telepítse a minta csomagjait a segítségével. `npm install`  Ez a parancs letölti azokat a csomagokat, amelyektől a kód függ.
1. Futtassa `node index.js`a programot a segítségével.

A program által végrehajtott műveleteket leíró üzenetek sorozatának kell megtekintenie. Ha a kérelmek részletesebb enciklijének megtekintését szeretné látni, `AzureSearchClient.request()` azhttps://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/AzureSearchClient.js#L21-L27) **AzureSearchClient.js**alkalmazásban a [metódus elején lévő sorokat] megjegyzésnélkül ítheti. 

Nyissa meg a keresési szolgáltatás **áttekintése** az Azure Portalon. Válassza az **Indexek** lapot. A következőhez hasonlót kell látnia:

![Képernyőkép az Azure Portalról, keresési szolgáltatás áttekintése, Indexek lap](media/search-get-started-nodejs/create-index-no-data.png)

A következő lépésben adatokat fog hozzáadni az indexhez. 

## <a name="2---load-documents"></a>2 - Dokumentumok betöltése 

Az Azure Cognitive Search, dokumentumok olyan adatstruktúrák, amelyek mind bemenetek indexelés és a lekérdezések kimenetek. Az ilyen adatokat közzé kell tennie az indexben. Ez az előző lépésben végzett műveleteknél eltérő végpontot használ. Nyissa meg **az AzureSearchClient.js-t,** és adja hozzá a következő `getIndexUrl()`módszert:

```javascript
 getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
```

Mint `AzureSearchClient.createIndexAsync(definition)`, szüksége van `AzureSearchClient.request()` egy funkció, amely felhívja és átadja a szállodai adatokat, hogy a szervezet. Az **AzureSearchClient.js** add `postDataAsync(hotelsData)` után: `createIndexAsync(definition)`

```javascript
async postDataAsync(hotelsData) {
    console.log("\n Adding hotel data...");
    const endpoint = this.getPostDataUrl();
    const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

 A dokumentumbemenetek lehetnek egy adatbázis, a Blob storage-ban lévő blobok, vagy mint ebben a példában, json-dokumentumok a lemezen. Letöltheti [a hotels.json fájlt,](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels.json) vagy létrehozhat saját **hotels.json** fájlt a következő tartalommal:

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}

```

Ha ezeket az adatokat be szeretné tölteni a programba, módosítsa az **index.js-t** a felső `hotelData` oldal közelében lévő sor hozzáadásával:

```javascript
const nconf = require('nconf');

const hotelData = require('./hotels.json');
const indexDefinition = require('./hotels_quickstart_index.json');
```

Most módosítsa `run()` a függvényt **index.js**. Az index elérhetővé válása néhány másodpercet is igénybe vehet, `AzureSearchClient.postDataAsync(hotelData)`ezért a hívás előtt adjon hozzá egy 2 másodperces szünetet:

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName"));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
    } catch (x) {
        console.log(x);
    }
}
```

Futtassa újra `node index.js`a programot a programmal Az 1. Ez alkalommal az index _létezik,_ és meg kell jelennie üzenetet törlése, mielőtt az alkalmazás létrehozza az új index et, és közzéteszi az adatokat. 

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Térjen vissza az **Indexek** lapra az Azure Portalon a keresési szolgáltatás **áttekintése** lapon. Az index most már négy dokumentumot tartalmaz, és bizonyos mennyiségű tárhelyet használ fel (eltarthat néhány percig, amíg a felhasználói felület megfelelően tükrözi az index mögöttes állapotát). Kattintson az index nevére, hogy a **Search Explorer**. Ezen a lapon kísérletezhet az adatlekérdezésekkel. Próbáljon meg rákeresni `*&$count=true` egy lekérdezési karakterláncra, és vissza kell kapnia az összes dokumentumot és az eredmények számát. Próbálja ki a `historic&highlight=Description&$filter=Rating gt 4` lekérdezési karakterláncot, és vissza kell kapnia `<em></em>` egy dokumentumot, a "történelmi" szó címkékbe csomagolva. További információ [a lekérdezések összeállításáról az Azure Cognitive Search szolgáltatásban.](https://docs.microsoft.com/azure/search/search-query-overview) 

Reprodukálja ezeket a lekérdezéseket kódolva az **index.js** megnyitásával és a kód tetejének közelében történő hozzáadásával:

```javascript
const queries = [
    "*&$count=true",
    "historic&highlight=Description&$filter=Rating gt 4&"
];
```

Ugyanebben az **index.js** fájlban írja be az `doQueriesAsync()` alábbi függvényt. Ez a `AzureSearchClient` függvény egy `AzureSearchClient.queryAsync` objektumot vesz fel, `queries` és a metódust alkalmazza a tömb minden értékére. A függvény `Promise.all()` segítségével egyetlen, `Promise` amely csak akkor oldja fel, ha az összes lekérdezés megoldódott. A lekérdezés `JSON.stringify(body, null, 4)` eredményének formázása olvashatóbb.

```javascript
async function doQueriesAsync(client) {
    return Promise.all(
        queries.map( async query => {
            const result = await client.queryAsync(query);
            const body = await result.json();
            const str = JSON.stringify( body, null, 4);
            console.log(`Query: ${query} \n ${str}`);
        })
    );
}
```

Módosítsa `run()` a függvényt úgy, hogy az indexelő működjön, majd hívja meg a `doQueriesAsync(client)` függvényt:

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName"));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
        // Data availability can take a few seconds
        await sleep(5000);
        await doQueriesAsync(client);
    } catch (x) {
        console.log(x);
    }
}
```

A `AzureSearchClient.queryAsync(query)`megvalósításhoz szerkesztse az **AzureSearchClient.js**fájlt. A kereséshez más végpontra van szükség, és a keresési `getSearchUrl(searchTerm)` kifejezések `getIndexUrl()` URL-argumentumokká válnak, ezért adja hozzá a függvényt a már megírt és `getPostDataUrl()` metódusok mellé.

```javascript
getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
 ```

A `queryAsync(searchTerm)` függvény az **AzureSearchClient.js-ben is megjelenik,** és ugyanazt a struktúrát követi, mint `postDataAsync(data)` a többi lekérdezési függvény: 

```javascript
async queryAsync(searchTerm) {
    console.log("\n Querying...")
    const endpoint = this.getSearchUrl(searchTerm);
    const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
    AzureSearchClient.throwOnHttpError(response);
    return response;
}
```

A keresés a "GET" igével történik, és nincs törzse, mivel a keresési kifejezés az URL része. Figyelje `queryAsync(searchTerm)` `this.queryKey`meg, hogy a , ellentétben a többi funkciót, amely a rendszergazdai kulcsot használta. A lekérdezési kulcsok, ahogy a neve is mutatja, csak az index lekérdezésére használhatók, és semmilyen módon nem használhatók az index módosítására. A lekérdezési kulcsok ezért biztonságosabban terjeszthetők az ügyfélalkalmazások között.

Futtassa `node index.js`a programot a programmal Most, amellett, hogy az előző lépéseket, a lekérdezések lesznek elküldve, és az eredményeket írt a konzolra.

### <a name="about-the-sample"></a>A mintáról

A minta egy kis mennyiségű szállodai adatokat használ, amelyek elegendőek az Azure Cognitive Search-index létrehozásának és lekérdezésének alapjainak bemutatásához.

Az **AzureSearchClient** osztály magában foglalja a konfigurációt, az URL-címeket és a keresési szolgáltatás alapvető HTTP-kéréseit. Az **index.js** fájl betölti az Azure Cognitive Search szolgáltatás konfigurációs adatait, az indexeléshez feltöltendő szállodai adatokat, és a `run` funkciójában lerendeli és végrehajtja a különböző műveleteket.

A függvény általános `run` viselkedése az Azure Cognitive Search index törlése, ha létezik, az index létrehozása, néhány adat hozzáadása és bizonyos lekérdezések végrehajtása.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

Az erőforrásokat a portálon keresheti meg és kezelheti a bal oldali navigációs ablak **Minden erőforrás** vagy **Erőforráscsoport** hivatkozásával.

Ha ingyenes szolgáltatást használ, ne feledje, hogy három indexelésre, indexelőre és adatforrásra van korlátozva. Törölheti az egyes elemeket a portálon, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

Ebben a Node.js rövid útmutatóban számos feladatot végzett egy index létrehozásához, a dokumentumokba való betöltéséhez és lekérdezések futtatásához. Bizonyos lépéseket tettünk, például a konfiguráció olvasását és a lekérdezések meghatározását a lehető legegyszerűbb módon. Egy valós alkalmazásban ezeket az aggályokat külön modulokba szeretné helyezni, amelyek rugalmasságot és beágyazást biztosítanak. 
 
Ha már rendelkezik némi háttérrel az Azure Cognitive Search, használhatja ezt a mintát ugródeszkaként a javaslatjavasolók (típus-előre vagy automatikus kiegészítés lekérdezések), szűrők és a sokoldalú navigáció. Ha most ismeri az Azure Cognitive Search szolgáltatást, javasoljuk, hogy próbáljon ki más oktatóanyagokat is, hogy jobban megértse, mit hozhat létre. További forrásokat a [dokumentációs oldalunkon](https://azure.microsoft.com/documentation/services/search/) talál. 

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search hívása weblapról Javascript használatával](https://github.com/liamca/azure-search-javascript-samples)
