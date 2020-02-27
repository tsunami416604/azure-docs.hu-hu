---
title: 'Gyors útmutató: keresési index létrehozása a Node. js-ben REST API-k használatával'
titleSuffix: Azure Cognitive Search
description: Ebben a Node. js-útmutatóban megtudhatja, hogyan hozhat létre indexet, tölthet be és hogyan futtathat lekérdezéseket az Azure Cognitive Search a JavaScript és a REST API-k használatával.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.devlang: nodejs
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/25/2020
ms.openlocfilehash: cbef6029b93f134f95ee54aa87ce0dd65bcdf50d
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623997"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-nodejs-using-rest-apis"></a>Gyors útmutató: Azure Cognitive Search index létrehozása a Node. js-ben REST API-k használatával
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Hozzon létre egy Node. js-alkalmazást, amely egy Azure Cognitive Search indexet hoz létre, tölt be és kérdez le. Ez a cikk bemutatja, hogyan hozhatja létre az alkalmazást lépésről lépésre. Azt is megteheti, hogy [letölti a forráskódot és](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/) az adatforrást, és futtatja az alkalmazást a parancssorból.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató összeállításához és teszteléséhez a következő szoftvereket és szolgáltatásokat használtuk:

+ [Node.js](https://nodejs.org)

+ A [NPM](https://www.npmjs.com) a Node. js-ben kell telepíteni

+ Ebben a cikkben a minta index struktúrája és a megfelelő dokumentumok szerepelnek, vagy a tárház [ **Gyorsindítás** könyvtárából](https://github.com/Azure-Samples/azure-search-javascript-samples/)

+ [Hozzon létre egy Azure Cognitive Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat.

Ajánlott:

* [Visual Studio Code](https://code.visualstudio.com)

* A VSCode [szebb](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) és [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) bővítményei.

<a name="get-service-info"></a>

## <a name="get-keys-and-urls"></a>Kulcsok és URL-címek lekérése

A szolgáltatás felé irányuló hívások URL-végpontot és hozzáférési kulcsot igényelnek minden kérelemben. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **áttekintése** lapon szerezze be a keresési szolgáltatás nevét. A szolgáltatás nevét a végpont URL-címének áttekintésével ellenőrizheti. Ha a végpont URL-címe `https://mydemo.search.windows.net`volt, a szolgáltatás neve `mydemo`.

2. A **beállítások** > **kulcsok**területen kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

    Kérje le a lekérdezési kulcsot is. Ajánlott a lekérdezési kérelmeket csak olvasási hozzáféréssel kibocsátani.

![A szolgáltatás nevének és a rendszergazda és a lekérdezési kulcsok beszerzése](media/search-get-started-nodejs/service-name-and-keys.png)

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kérelem fejlécében. Egy érvényes kulcs a kérést küldő alkalmazás és az azt kezelő szolgáltatás közötti megbízhatósági kapcsolatot hoz létre a kérelmek alapján.

## <a name="set-up-your-environment"></a>A környezet kialakítása

Először nyisson meg egy PowerShell-konzolt vagy más környezetet, amelyben telepítette a Node. js-t.

1. Hozzon létre egy fejlesztői könyvtárat, amely a `quickstart` nevet adja meg:

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. `npm init`futtatásával inicializáljon egy üres projektet a NPM. Fogadja el az alapértelmezett értékeket, kivéve a licencet, amelyet a "MIT" értékre kell beállítani. 

1. Adja hozzá azokat a csomagokat, amelyeket a kód és a fejlesztéshez nyújtott támogatás is függ:

    ```powershell
    npm install nconf node-fetch
    npm install --save-dev eslint eslint-config-prettier eslint-config-airbnb-base eslint-plugin-import prettier
    ```

4. Győződjön meg arról, hogy konfigurálta a projekteket és annak függőségeit, és ellenőrizze, hogy a **Package. JSON** fájl a következőhöz hasonlóan néz ki:

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

5. Hozzon létre egy **azure_search_config. JSON** fájlt a keresési szolgáltatás adatai tárolásához:

    ```json
    {
        "serviceName" : "[SEARCH_SERVICE_NAME]",
        "adminKey" : "[ADMIN_KEY]",
        "queryKey" : "[QUERY_KEY]",
        "indexName" : "hotels-quickstart"
    }
    ```

Cserélje le a `[SERVICE_NAME]` értéket a keresési szolgáltatás nevére. Cserélje le a `[ADMIN_KEY]` és a `[QUERY_KEY]` értéket a korábban feljegyzett kulcsokra. 

## <a name="1---create-index"></a>1 – index létrehozása 

Hozzon létre egy fájlt **hotels_quickstart_index. JSON**fájlban.  Ez a fájl határozza meg, hogyan működik az Azure Cognitive Search a következő lépésben betöltés alatt álló dokumentumokkal. Minden mezőt egy `name` azonosít, és egy megadott `type`rendelkezik. Az egyes mezőkben index attribútumok is megadhatók, amelyek meghatározzák, hogy az Azure Cognitive Search kereshet, szűrhető, rendezhető és kategorizálható a mező alapján. A mezők többsége egyszerű adattípusú, néhány például a `AddressType` összetett típus, amely lehetővé teszi, hogy az indexben gazdag adatstruktúrákat hozzon létre.  További információt a [támogatott adattípusokról](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) és az [index attribútumairól](https://docs.microsoft.com/azure/search/search-what-is-an-index#index-attributes)itt olvashat. 

Adja hozzá a következőt a **hotels_quickstart_index. JSON** [fájlhoz, vagy töltse le a fájlt](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels_quickstart_index.json). 

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
    

Célszerű elkülöníteni egy adott forgatókönyv sajátosságait a széles körben alkalmazandó kódból. A **AzureSearchClient. js** fájlban definiált `AzureSearchClient` osztály tudni fogja, hogyan kell létrehozni a kérelem URL-címeit, a beolvasás API használatával kérelmet készíteni, és reagálni a válasz állapotkódot.

Kezdjen el dolgozni a **AzureSearchClient. js** -ben a **Node-Fetch** csomag importálásával és egy egyszerű osztály létrehozásával. Különítse el a `AzureSearchClient` osztály cserélhető részeit úgy, hogy átadja a konstruktornak a különböző konfigurációs értékeket:

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

Az osztály első feladata, hogy megtudja, hogyan hozhat létre olyan URL-címeket, amelyekhez a különböző kéréseket küldeni szeretné. Ezeket az URL-címeket olyan példány-metódusokkal hozza létre, amelyek az osztály konstruktorának átadott konfigurációs adathalmazt használják. Figyelje meg, hogy az általuk létrehozott URL-cím egy API-verzióra vonatkozik, és rendelkeznie kell egy olyan argumentummal, amely megadja az adott verziót (ebben az alkalmazásban `2019-05-06`). 

Az első ilyen módszer az index URL-címét fogja visszaadni. Adja hozzá a következő metódust az osztály törzsében:

```javascript
getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }

```

`AzureSearchClient` következő feladata aszinkron kérelmet készít a beolvasási API-val. Az aszinkron statikus metódus `request` egy URL-címet, egy karakterláncot, amely megadja a HTTP-metódust ("GET", "PUT", "POST", "DELETE"), a kérelemben használandó kulcsot, valamint egy opcionális JSON-objektumot. A `headers` változó leképezi a `queryKey` (akár a rendszergazdai kulcsot, akár az írásvédett lekérdezési kulcsot) az "API-Key" HTTP-kérelem fejlécére. A kérések beállításai mindig tartalmazzák a használni kívánt `method` és a `headers`. Ha `bodyJson` nem `null`, a HTTP-kérelem törzse `bodyJson`karakterlánc-ábrázolására van beállítva. A `request` metódus a beolvasás API meghívását adja vissza a HTTP-kérelem végrehajtásához.

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

Bemutató céljára csak akkor dobjon kivételt, ha a HTTP-kérelem nem sikeres. Egy valós alkalmazásban valószínűleg elvégezheti a HTTP-állapotkód naplózását és diagnosztizálását a `response` a keresési szolgáltatási kérelemben. 
    
```javascript
static throwOnHttpError(response) {
    const statusCode = response.status;
    if (statusCode >= 300){
        console.log(`Request failed: ${JSON.stringify(response, null, 4)}`);
        throw new Error(`Failure in request. HTTP Status was ${statusCode}`);
    }
}
```

Végül adja hozzá a metódusokat az Azure Cognitive Search index észleléséhez, törléséhez és létrehozásához. Ezek a metódusok mind ugyanazzal a struktúrával rendelkeznek:

* Szerezze be azt a végpontot, amelyhez a kérést el szeretné végezni.
* A kérést a megfelelő végponttal, HTTP-művelettel, API-kulccsal, és ha szükséges, egy JSON-törzstel kell előállítani. `indexExistsAsync()` és `deleteIndexAsync()` nem rendelkezik JSON-törzstel, de `createIndexAsync(definition)`.
* `await` a kérelemre adott válasz.  
* A válasz állapotkód alapján járjon el.
* Egy megfelelő érték (logikai, `this`vagy lekérdezési eredmények) ígéretének visszaadása. 

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

Győződjön meg arról, hogy a metódusok az osztályban belül vannak, és hogy az osztályt exportálja. A **AzureSearchClient. js** legkülső hatókörének a következőket kell tartalmaznia:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    // ... code here ...
}

module.exports = AzureSearchClient;
```

Egy objektumorientált osztály jó választás volt az esetlegesen újrafelhasználható **AzureSearchClient. js** -modulhoz, de a főprogramhoz nem szükséges, amelyet az **index. js**nevű fájlba kell helyezni. 

Hozza létre az **index. js fájlt** , és kezdje a következőket:

* A **NConf** csomag, amely rugalmasságot biztosít a JSON-, környezeti változók vagy parancssori argumentumok konfigurációjának megadásához.
* A **hotels_quickstart_index. JSON** fájlból származó adatok.
* Az `AzureSearchClient` modult.

```javascript
const nconf = require('nconf');

const indexDefinition = require('./hotels_quickstart_index.json');
const AzureSearchClient = require('./AzureSearchClient.js');
```

A [ **NConf** csomag](https://github.com/indexzero/nconf) lehetővé teszi, hogy különböző formátumokban (például környezeti változókban vagy a parancssorban) megadhatja a konfigurációs adatmennyiséget. Ez a példa alapszintű **NConf** használ a **azure_search_config. JSON** fájl olvasásához és a fájl tartalmának szótárként való visszaküldéséhez. A **nconf**`get(key)` funkciójának használatával gyorsan megtekintheti, hogy a konfigurációs adatok megfelelően vannak-e testreszabva. Végül a függvény a konfigurációt adja vissza:

```javascript
function getAzureConfiguration() {
    const config = nconf.file({ file: 'azure_search_config.json' });
    if (config.get('serviceName') === '[SEARCH_SERVICE_NAME]' ) {
        throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
    }
    return config;
}
```

A `sleep` függvény egy olyan `Promise` hoz létre, amely a megadott idő után oldódik fel. A függvény használata lehetővé teszi, hogy az alkalmazás szüneteltethető legyen, miközben várakozik az aszinkron indexelési műveletek befejezésére, és elérhetővé válik. Ilyen késleltetés hozzáadására általában csak a demók, tesztek és példák esetében van szükség.

```javascript
function sleep(ms) {
    return(
        new Promise(function(resolve, reject) {
            setTimeout(function() { resolve(); }, ms);
        })
    );
}
```

Végül a fő aszinkron `run` függvényt kell megadnia és meghívnia. Ez a függvény sorrendben hívja meg a többi függvényt, és a `Promise`s feloldásához szükséges.

* A konfiguráció beolvasása a korábban írt `getAzureConfiguration()`
* Új `AzureSearchClient`-példány létrehozása a konfigurációban szereplő értékek átadásával
* Ellenőrizze, hogy az index létezik-e, és ha igen, törölje
* Index létrehozása a **hotels_quickstart_index. JSON** fájlból betöltött `indexDefinition` használatával

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

Ne feledkezzen meg arról, hogy a végső hívás `run()`! A következő lépésben `node index.js` futtatásakor a program beléptetési pontja.

Figyelje meg, hogy `AzureSearchClient.indexExistsAsync()` és `AzureSearchClient.deleteIndexAsync()` nem végez paramétereket. Ezek a függvények `bodyJson` argumentum nélkül hívja meg a `AzureSearchClient.request()`t. `AzureSearchClient.request()`belül, mivel `bodyJson === null` `true`, a `init` struktúra úgy van beállítva, hogy csak a HTTP-művelet ("GET" a `indexExistsAsync()` és a "Törlés" a `deleteIndexAsync()`) és a fejlécek, amelyek meghatározzák a kérés kulcsát.  

Ezzel szemben a _`AzureSearchClient.createIndexAsync(indexDefinition)` metódus egy_ paramétert hajt végre. A `index.js``run` függvénye átadja a **hotels_quickstart_index. JSON** fájl tartalmát a `AzureSearchClient.createIndexAsync(indexDefinition)` metódusnak. A `createIndexAsync()` metódus átadja ezt a definíciót `AzureSearchClient.request()`nak. `AzureSearchClient.request()`, mivel a `bodyJson === null` mostantól `false`, a `init` struktúra nem csak a HTTP-műveletet ("PUT") és a fejléceket tartalmazza, de a `body` az index definíciós adatra állítja be.

### <a name="prepare-and-run-the-sample"></a>A minta előkészítése és futtatása

A következő parancsokhoz használjon egy terminál-ablakot.

1. Navigáljon ahhoz a mappához, amely tartalmazza a **Package. JSON** fájlt és a kód többi részét.
1. Telepítse a minta csomagjait `npm install`.  Ez a parancs letölti a csomagokat, amelyeken a kód függ.
1. Futtassa a programot a `node index.js`.

A program által végrehajtott műveleteket ismertető üzenetnek kell megjelennie. Ha szeretné megtekinteni a kérések részletesebb ismertetését, a **AzureSearchClient. js**fájlban a [`AzureSearchClient.request()` módszer https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/AzureSearchClient.js#L21-L27)] elején található [sorok lehetőséggel törölheti a megjegyzéseket. 

Nyissa meg a keresési szolgáltatás **áttekintését** a Azure Portal. Válassza ki az **indexek** lapot. A következőhöz hasonlónak kell megjelennie:

![Képernyőkép a Azure Portalről, a keresési szolgáltatás áttekintése, indexek lap](media/search-get-started-nodejs/create-index-no-data.png)

A következő lépésben hozzá kell adnia az adatindexhez. 

## <a name="2---load-documents"></a>2 – dokumentumok betöltése 

Az Azure Cognitive Searchban a dokumentumok olyan adatstruktúrák, amelyek mind a lekérdezések indexeléséhez, mind pedig a kimenetekhez tartoznak. Ezeket az adatsorokat az indexbe kell ELKÜLDENI. Ez egy másik végpontot használ, mint az előző lépésben végzett műveletek. Nyissa meg a **AzureSearchClient. js fájlt** , és adja hozzá a következő metódust `getIndexUrl()`után:

```javascript
 getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
```

A `AzureSearchClient.createIndexAsync(definition)`hoz hasonlóan olyan függvényre van szükség, amely meghívja a `AzureSearchClient.request()`, és a szállodai adatban továbbítja a törzsének. A **AzureSearchClient. js** fájlban adja hozzá a `postDataAsync(hotelsData)` `createIndexAsync(definition)`után:

```javascript
async postDataAsync(hotelsData) {
    console.log("\n Adding hotel data...");
    const endpoint = this.getPostDataUrl();
    const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

 A dokumentumok bemenetei lehetnek egy adatbázisban lévő sorok, a blob Storage-ban található Blobok, vagy a példában szereplő JSON-dokumentumok a lemezen. Töltse le a [Hotels. JSON](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels.json) fájlt, vagy hozzon létre saját **Hotels. JSON** fájlt a következő tartalommal:

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

Az adatgyűjtés programba való betöltéséhez módosítsa az **index. js fájlt** úgy, hogy hozzáadja a `hotelData` közelében található sort a felső részhez:

```javascript
const nconf = require('nconf');

const hotelData = require('./hotels.json');
const indexDefinition = require('./hotels_quickstart_index.json');
```

Most módosítsa a `run()` függvényt az **index. js**fájlban. Az index elérhetővé válása néhány másodpercig is eltarthat, így a `AzureSearchClient.postDataAsync(hotelData)`meghívása előtt vegyen fel egy 2 másodperces szüneteltetést:

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

Futtassa újra a programot `node index.js`ával. Az 1. lépésben látott, némileg eltérő üzeneteket kell látnia. Ebben az esetben _az index létezik_ , és az alkalmazás törlésével kapcsolatos üzenetnek kell megjelennie, mielőtt az alkalmazás létrehozza az új indexet, és az adatait közzéteszi. 

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Térjen vissza az **indexek** lapra a Azure Portal keresési szolgáltatásának **áttekintésében** . Az index most négy dokumentumot tartalmaz, és bizonyos mennyiségű tárterületet használ (ez eltarthat néhány percig, amíg a felhasználói felület megfelelően tükrözi az index mögöttes állapotát). Kattintson az index nevére a **keresési tallózóhoz**. Ez a lap lehetővé teszi az adatlekérdezésekkel való kísérletezést. Próbálkozzon `*&$count=true` lekérdezési karakterláncával, és az összes dokumentumot és az eredmények számát vissza kell kérnie. Próbálja ki a lekérdezési karakterláncot `historic&highlight=Description&$filter=Rating gt 4` és egyetlen dokumentumot kell visszakapnia, amelynek a "régi" szó `<em></em>` címkékbe van csomagolva. További információ az [Azure Cognitive Search-beli lekérdezések összeállításáról](https://docs.microsoft.com/azure/search/search-query-overview). 

A lekérdezéseket a kódban az **index. js** megnyitásával, majd a kód felülre történő hozzáadásával reprodukálhatja.

```javascript
const queries = [
    "*&$count=true",
    "historic&highlight=Description&$filter=Rating gt 4&"
];
```

Az **index. js** fájlban írja be az alább látható `doQueriesAsync()` függvényt. Ez a függvény egy `AzureSearchClient` objektumot vesz igénybe, és a `AzureSearchClient.queryAsync` metódust alkalmazza a `queries` tömb egyes értékeire. A `Promise.all()` függvény használatával egyetlen `Promise` ad vissza, amely csak akkor oldódik fel, ha az összes lekérdezés megoldódott. A `JSON.stringify(body, null, 4)` meghívása, hogy a lekérdezés eredménye olvashatóbb legyen.

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

Módosítsa az `run()` függvényt, hogy az indexelő működjön, majd hívja meg a `doQueriesAsync(client)` függvényt:

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

`AzureSearchClient.queryAsync(query)`megvalósításához szerkessze a **AzureSearchClient. js**fájlt. A kereséshez eltérő végpontra van szükség, és a keresési kifejezések URL-argumentumok lesznek, ezért a függvényt a `getIndexUrl()` és a már megírt `getPostDataUrl()` módszerek mellett adja hozzá `getSearchUrl(searchTerm)`.

```javascript
getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
 ```

A `queryAsync(searchTerm)` függvény a **AzureSearchClient. js** fájlban is szerepel, és ugyanazt a struktúrát követi, mint `postDataAsync(data)` és a többi lekérdezési funkció: 

```javascript
async queryAsync(searchTerm) {
    console.log("\n Querying...")
    const endpoint = this.getSearchUrl(searchTerm);
    const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
    AzureSearchClient.throwOnHttpError(response);
    return response;
}
```

A keresés a "GET" művelettel és a törzs nélkül történik, mivel a keresési kifejezés az URL részét képezi. Figyelje meg, hogy a `queryAsync(searchTerm)` a `this.queryKey`használja, a többi olyan függvénytől eltérően, amely a felügyeleti kulcsot használta. A lekérdezési kulcsok, ahogy a név is jelenti, csak az index lekérdezésére használható, és nem használható az index bármilyen módon történő módosítására. A lekérdezési kulcsok ezért biztonságosabbá tétele az ügyfélalkalmazások számára történő terjesztéshez.

Futtassa a programot a `node index.js`. Az előző lépéseken kívül a rendszer elküldi a lekérdezéseket és a konzolra írt eredményeket.

### <a name="about-the-sample"></a>A minta ismertetése

A minta kis mennyiségű szállodai adat használatát mutatja be, amely elegendő az Azure Cognitive Search index létrehozásának és lekérdezésének alapjaihoz.

A **AzureSearchClient** osztály a keresési szolgáltatás konfigurációját, URL-címeit és alapszintű http-kéréseit ágyazza be. Az **index. js** fájl betölti az Azure Cognitive Search szolgáltatás konfigurációs adatait, az indexeléshez feltöltött szállodai adatok, valamint a `run` függvényében megrendeléseket, és végrehajtja a különböző műveleteket.

A `run` függvény általános viselkedése az Azure Cognitive Search index törlése, ha létezik, hozza létre az indexet, adjon hozzá néhány adathoz, és hajtson végre néhány lekérdezést.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha saját előfizetésében dolgozik, a projekt végén érdemes megállapítani, hogy továbbra is szüksége van-e a létrehozott erőforrásokra. A már futó erőforrások pénzbe kerülnek. Az erőforrásokat egyenként is törölheti, vagy az erőforráscsoport törlésével törölheti a teljes erőforrás-készletet.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>Következő lépések

Ebben a Node. js-útmutatóban egy sor feladatot dolgozott ki egy index létrehozásához, a dokumentumok betöltéséhez és a lekérdezések futtatásához. Bizonyos lépések, például a konfiguráció olvasása és a lekérdezések meghatározása a lehető legegyszerűbb módon történt. Egy valós alkalmazásban ezeket a problémákat külön modulokban érdemes elhelyezni, amelyek rugalmasságot és beágyazást tesznek lehetővé. 
 
Ha már rendelkezik valamilyen háttérrel az Azure Cognitive Search-ban, ezt a mintát használhatja kiindulópontként a kipróbálási javaslatok (típus-vagy automatikus kiegészítés), a szűrők és a csiszolt navigáció érdekében. Ha még nem ismeri az Azure Cognitive Searcht, javasoljuk, hogy más oktatóanyagokat próbáljon meg létrehozni, hogy megértsük, mit hozhat létre. További forrásokat a [dokumentációs oldalunkon](https://azure.microsoft.com/documentation/services/search/) talál. 

> [!div class="nextstepaction"]
> [Azure-Cognitive Search meghívása weboldalról JavaScript használatával](https://github.com/liamca/azure-search-javascript-samples)
