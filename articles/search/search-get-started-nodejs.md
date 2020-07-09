---
title: 'Gyors útmutató: keresési index létrehozása Node.js REST API-k használatával'
titleSuffix: Azure Cognitive Search
description: Ebben a Node.js útmutatóban megtudhatja, hogyan hozhat létre indexet, tölthet be és hogyan futtathat lekérdezéseket az Azure Cognitive Search a JavaScript és a REST API-k használatával.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.devlang: nodejs
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: bd64faf46f91c3b73d58f7c226748cd0ac083701
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/30/2020
ms.locfileid: "85562146"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-nodejs-using-rest-apis"></a>Gyors útmutató: Azure Cognitive Search index létrehozása a Node.js REST API-k használatával
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Portál](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Hozzon létre egy Node.js alkalmazást, amely egy Azure Cognitive Search indexet hoz létre, tölt be és kérdez le. Ez a cikk bemutatja, hogyan hozhatja létre az alkalmazást lépésről lépésre. Azt is megteheti, hogy [letölti a forráskódot és](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/) az adatforrást, és futtatja az alkalmazást a parancssorból.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató összeállításához és teszteléséhez a következő szoftvereket és szolgáltatásokat használtuk:

+ [Node.js](https://nodejs.org)

+ A [NPM](https://www.npmjs.com) Node.js kell telepíteni

+ Ebben a cikkben a minta index struktúrája és a megfelelő dokumentumok szerepelnek, vagy a tárház [ **Gyorsindítás** könyvtárából](https://github.com/Azure-Samples/azure-search-javascript-samples/)

+ [Hozzon létre egy Azure Cognitive Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat.

Ajánlott:

* [Visual Studio Code](https://code.visualstudio.com)

* A VSCode [szebb](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) és [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) bővítményei.

<a name="get-service-info"></a>

## <a name="get-keys-and-urls"></a>Kulcsok és URL-címek lekérése

A szolgáltatás felé irányuló hívások URL-végpontot és hozzáférési kulcsot igényelnek minden kérelemben. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **áttekintése** lapon szerezze be a keresési szolgáltatás nevét. A szolgáltatás nevét a végpont URL-címének áttekintésével ellenőrizheti. Ha a végpont URL-címe volt `https://mydemo.search.windows.net` , a szolgáltatás neve a következő lesz: `mydemo` .

2. A **Beállítások**  >  **kulcsaiban**kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

    Kérje le a lekérdezési kulcsot is. Ajánlott a lekérdezési kérelmeket csak olvasási hozzáféréssel kibocsátani.

![A szolgáltatás nevének és a rendszergazda és a lekérdezési kulcsok beszerzése](media/search-get-started-nodejs/service-name-and-keys.png)

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kérelem fejlécében. Egy érvényes kulcs a kérést küldő alkalmazás és az azt kezelő szolgáltatás közötti megbízhatósági kapcsolatot hoz létre a kérelmek alapján.

## <a name="set-up-your-environment"></a>A környezet kialakítása

Először nyisson meg egy PowerShell-konzolt vagy más környezetet, amelyben Node.js telepítette.

1. Hozzon létre egy fejlesztői könyvtárat, amely a nevét adja meg `quickstart` :

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. A futtatásával inicializáljon egy üres projektet a NPM `npm init` . Fogadja el az alapértelmezett értékeket, kivéve a licencet, amelyet a "MIT" értékre kell beállítani. 

1. Adja hozzá azokat a csomagokat, amelyeket a kód és a fejlesztéshez nyújtott támogatás is függ:

    ```powershell
    npm install nconf node-fetch
    npm install --save-dev eslint eslint-config-prettier eslint-config-airbnb-base eslint-plugin-import prettier
    ```

4. Győződjön meg arról, hogy konfigurálta a projekteket és annak függőségeit, ha ellenőrzi, hogy a **package.js** fájl a következőhöz hasonlóan néz ki:

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

5. Hozzon létre egy fájlt **azure_search_config.json** a keresési szolgáltatás adatai tárolásához:

    ```json
    {
        "serviceName" : "[SEARCH_SERVICE_NAME]",
        "adminKey" : "[ADMIN_KEY]",
        "queryKey" : "[QUERY_KEY]",
        "indexName" : "hotels-quickstart"
    }
    ```

Cserélje le az `[SERVICE_NAME]` értéket a keresési szolgáltatás nevére. Cserélje `[ADMIN_KEY]` le `[QUERY_KEY]` a és a értéket a korábban feljegyzett kulcsokra. 

## <a name="1---create-index"></a>1 – index létrehozása 

Hozzon létre egy fájlt **hotels_quickstart_index.jsa**következőn:.  Ez a fájl határozza meg, hogyan működik az Azure Cognitive Search a következő lépésben betöltés alatt álló dokumentumokkal. Az egyes mezők azonosítása a `name` és a megadott értékkel történik `type` . Az egyes mezőkben index attribútumok is megadhatók, amelyek meghatározzák, hogy az Azure Cognitive Search kereshet, szűrhető, rendezhető és kategorizálható a mező alapján. A mezők többsége egyszerű adattípusú, néhány például `AddressType` olyan összetett típus, amely lehetővé teszi, hogy gazdag adatstruktúrákat hozzon létre az indexében.  További információt a [támogatott adattípusokról](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) és az [index attribútumairól](https://docs.microsoft.com/azure/search/search-what-is-an-index#index-attributes)itt olvashat. 

Adja hozzá a következőt a **hotels_quickstart_index.jshoz** , vagy [töltse le a fájlt](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels_quickstart_index.json). 

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
    

Célszerű elkülöníteni egy adott forgatókönyv sajátosságait a széles körben alkalmazandó kódból. A `AzureSearchClient` fájl **AzureSearchClient.jsban** definiált osztály tudni fogja, hogyan kell létrehozni a kérelmek URL-címét, a BEolvasás API-val történő kérést, és reagálni a válasz állapotkódot.

A **AzureSearchClient.js** a **Node-Fetch** csomag importálásával és egy egyszerű osztály létrehozásával megkezdheti a munkát. A `AzureSearchClient` különböző konfigurációs értékek megadásával elkülönítheti a osztály cserélhető részeit:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    constructor(searchServiceName, adminKey, queryKey, indexName) {
        this.searchServiceName = searchServiceName;
        this.adminKey = adminKey;
        // The query key is used for read-only requests and so can be distributed with less risk of abuse.
        this.queryKey = queryKey;
        this.indexName = indexName;
        this.apiVersion = '2020-06-30';
    }

    // All methods go inside class body here!
}

module.exports = AzureSearchClient;
```

Az osztály első feladata, hogy megtudja, hogyan hozhat létre olyan URL-címeket, amelyekhez a különböző kéréseket küldeni szeretné. Ezeket az URL-címeket olyan példány-metódusokkal hozza létre, amelyek az osztály konstruktorának átadott konfigurációs adathalmazt használják. Figyelje meg, hogy az általuk létrehozott URL-cím egy API-verzióra vonatkozik, és rendelkeznie kell egy olyan argumentummal, amely megadja az adott verziót (ebben az alkalmazásban `2020-06-30` ). 

Az első ilyen módszer az index URL-címét fogja visszaadni. Adja hozzá a következő metódust az osztály törzsében:

```javascript
getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }

```

A következő feladata, hogy `AzureSearchClient` aszinkron kérelmet küldjön a fetch API-val. Az aszinkron statikus metódus `request` egy URL-címet, egy karakterláncot ad meg, amely megadja a http-metódust ("Get", "put", "post", "Delete"), a kérelemben használandó kulcsot, valamint egy opcionális JSON-objektumot. A `headers` változó leképezi az `queryKey` (akár a rendszergazdai kulcsot, akár az írásvédett lekérdezési kulcsot) az "API-Key" http-kérelem fejlécére. A kérések beállításai mindig tartalmazzák a `method` használni kívánt és a `headers` . Ha `bodyJson` nem `null` , a HTTP-kérelem törzse a karakterlánc-ábrázolására van beállítva `bodyJson` . A `request` metódus a beolvasás API ígéretét adja vissza a HTTP-kérelem végrehajtásához.

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

Bemutató céljára csak akkor dobjon kivételt, ha a HTTP-kérelem nem sikeres. Egy valós alkalmazásban valószínűleg elvégezheti a HTTP-állapotkód naplózását és diagnosztizálását a `response` keresési szolgáltatás kérelmében. 
    
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
* A kérést a megfelelő végponttal, HTTP-művelettel, API-kulccsal, és ha szükséges, egy JSON-törzstel kell előállítani. `indexExistsAsync()`és `deleteIndexAsync()` nem rendelkezik JSON-törzstel, de nem `createIndexAsync(definition)` .
* `await`a kérelemre adott válasz.  
* A válasz állapotkód alapján járjon el.
* Egy megfelelő érték (Boolean, `this` vagy lekérdezési eredmények) ígéretének visszaadása. 

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

Győződjön meg arról, hogy a metódusok az osztályban belül vannak, és hogy az osztályt exportálja. **AzureSearchClient.js** legkülső hatókörének a következőket kell tennie:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    // ... code here ...
}

module.exports = AzureSearchClient;
```

Egy objektumorientált osztály jó választás volt a potenciálisan újrafelhasználható **AzureSearchClient.js** modulhoz, de a főprogramhoz nem szükséges, amelyet a **index.js**nevű fájlba kell helyezni. 

Hozza létre **index.js** és kezdje a következőket:

* A **NConf** csomag, amely rugalmasságot biztosít a JSON-, környezeti változók vagy parancssori argumentumok konfigurációjának megadásához.
* A fájl **hotels_quickstart_index.jsjának** adatait.
* Az `AzureSearchClient` modult.

```javascript
const nconf = require('nconf');

const indexDefinition = require('./hotels_quickstart_index.json');
const AzureSearchClient = require('./AzureSearchClient.js');
```

A [ **NConf** csomag](https://github.com/indexzero/nconf) lehetővé teszi, hogy különböző formátumokban (például környezeti változókban vagy a parancssorban) megadhatja a konfigurációs adatmennyiséget. Ez a példa alapszintű **NConf** használ a fájl **azure_search_config.jsének** beolvasásához és a fájl tartalmának a szótárként való visszaküldéséhez. A **NConf** `get(key)` funkciójának használatával gyorsan megtekintheti, hogy a konfigurációs adatok megfelelően vannak-e testreszabva. Végül a függvény a konfigurációt adja vissza:

```javascript
function getAzureConfiguration() {
    const config = nconf.file({ file: 'azure_search_config.json' });
    if (config.get('serviceName') === '[SEARCH_SERVICE_NAME]' ) {
        throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
    }
    return config;
}
```

A `sleep` függvény létrehoz egy `Promise` , a megadott idő után feloldható megoldást. A függvény használata lehetővé teszi, hogy az alkalmazás szüneteltethető legyen, miközben várakozik az aszinkron indexelési műveletek befejezésére, és elérhetővé válik. Ilyen késleltetés hozzáadására általában csak a demók, tesztek és példák esetében van szükség.

```javascript
function sleep(ms) {
    return(
        new Promise(function(resolve, reject) {
            setTimeout(function() { resolve(); }, ms);
        })
    );
}
```

Végül írja be és hívja meg a fő aszinkron `run` függvényt. Ez a függvény sorrendben hívja meg a többi függvényt, az s feloldásához szükséges módon `Promise` .

* A korábban írt konfiguráció beolvasása `getAzureConfiguration()`
* Hozzon létre egy új `AzureSearchClient` példányt, amely átadja a konfiguráció értékeit
* Ellenőrizze, hogy az index létezik-e, és ha igen, törölje
* Index létrehozása a `indexDefinition` betöltött **hotels_quickstart_index.js** használatával

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
    } catch (x) {
        console.log(x);
    }
}

run();
```

Ne felejtsük el, hogy a végső hívás `run()` ! A következő lépésben való futtatáskor a program beléptetési pontja `node index.js` .

Figyelje meg, hogy `AzureSearchClient.indexExistsAsync()` `AzureSearchClient.deleteIndexAsync()` nem végez paramétereket. Ezek a függvények `AzureSearchClient.request()` argumentum nélkül hívhatók `bodyJson` . `AzureSearchClient.request()`A (z `bodyJson === null` ) óta `true` a `init` struktúra úgy van beállítva, hogy csak a http-művelet ("Get" `indexExistsAsync()` és "Delete" `deleteIndexAsync()` ) és a fejlécek legyenek megadva, amelyek meghatározzák a kérés kulcsát.  

Ezzel szemben a `AzureSearchClient.createIndexAsync(indexDefinition)` metódus egy _does_ paramétert hajt végre. A `run` függvény a alkalmazásban `index.js` továbbítja a fájl tartalmát **hotels_quickstart_index.js** a `AzureSearchClient.createIndexAsync(indexDefinition)` metódusnak. A `createIndexAsync()` metódus átadja ezt a definíciót a következőnek: `AzureSearchClient.request()` . A-ben `AzureSearchClient.request()` azóta `bodyJson === null` `false` a `init` struktúra nem csak a http-műveletet ("put") és a fejléceket tartalmazza, de az `body` index definíciós adatát állítja be.

### <a name="prepare-and-run-the-sample"></a>A minta előkészítése és futtatása

A következő parancsokhoz használjon egy terminál-ablakot.

1. Navigáljon ahhoz a mappához, amely a fájl **package.jsét** és a kód többi részét tartalmazza.
1. Telepítse a minta csomagjait a következővel: `npm install` .  Ez a parancs letölti a csomagokat, amelyeken a kód függ.
1. Futtassa a programot a alkalmazással `node index.js` .

A program által végrehajtott műveleteket ismertető üzenetnek kell megjelennie. Ha szeretné megtekinteni a kérések részletesebb ismertetését, akkor a `AzureSearchClient.request()` https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/AzureSearchClient.js#L21-L27) **AzureSearchClient.jsban **megadhatja a [vonalakat a metódus elején.). 

Nyissa meg a keresési szolgáltatás **áttekintését** a Azure Portal. Válassza ki az **indexek** lapot. A következőhöz hasonlónak kell megjelennie:

![Képernyőkép a Azure Portalről, a keresési szolgáltatás áttekintése, indexek lap](media/search-get-started-nodejs/create-index-no-data.png)

A következő lépésben hozzá kell adnia az adatindexhez. 

## <a name="2---load-documents"></a>2 – dokumentumok betöltése 

Az Azure Cognitive Searchban a dokumentumok olyan adatstruktúrák, amelyek mind a lekérdezések indexeléséhez, mind pedig a kimenetekhez tartoznak. Ezeket az adatsorokat az indexbe kell ELKÜLDENI. Ez egy másik végpontot használ, mint az előző lépésben végzett műveletek. Nyissa meg **AzureSearchClient.js** és adja hozzá a következő metódust az után `getIndexUrl()` :

```javascript
 getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
```

Ehhez hasonlóan `AzureSearchClient.createIndexAsync(definition)` olyan függvényre van szükség, amely meghívja `AzureSearchClient.request()` és továbbítja a szállodai adatát a törzsének. **AzureSearchClient.js** Hozzáadás `postDataAsync(hotelsData)` után `createIndexAsync(definition)` :

```javascript
async postDataAsync(hotelsData) {
    console.log("\n Adding hotel data...");
    const endpoint = this.getPostDataUrl();
    const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

 A dokumentumok bemenetei lehetnek egy adatbázisban lévő sorok, a blob Storage-ban található Blobok, vagy a példában szereplő JSON-dokumentumok a lemezen. Letöltheti [hotels.js](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels.json) , vagy létrehozhat saját **hotels.js** a következő tartalommal rendelkező fájlon:

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

Az adatgyűjtés programba való betöltéséhez módosítsa **index.js** úgy, hogy hozzáadja a következőhöz `hotelData` közeli sort:

```javascript
const nconf = require('nconf');

const hotelData = require('./hotels.json');
const indexDefinition = require('./hotels_quickstart_index.json');
```

Most módosítsa a `run()` függvényt **index.jsban **. Az index elérhetővé válása néhány másodpercig is eltarthat, így a hívás előtt vegyen fel egy 2 másodperces szüneteltetést `AzureSearchClient.postDataAsync(hotelData)` :

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

Futtassa újra a programot a alkalmazással `node index.js` . Az 1. lépésben látott, némileg eltérő üzeneteket kell látnia. Ebben az esetben _az index létezik_ , és az alkalmazás törlésével kapcsolatos üzenetnek kell megjelennie, mielőtt az alkalmazás létrehozza az új indexet, és az adatait közzéteszi. 

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Térjen vissza az **indexek** lapra a Azure Portal keresési szolgáltatásának **áttekintésében** . Az index most négy dokumentumot tartalmaz, és bizonyos mennyiségű tárterületet használ (ez eltarthat néhány percig, amíg a felhasználói felület megfelelően tükrözi az index mögöttes állapotát). Kattintson az index nevére a **keresési tallózóhoz**. Ez a lap lehetővé teszi az adatlekérdezésekkel való kísérletezést. Próbáljon meg lekérdezési karakterláncot keresni, `*&$count=true` és az összes dokumentumot és az eredmények számát vissza kell kérnie. Próbálja ki a lekérdezési karakterláncot `historic&highlight=Description&$filter=Rating gt 4` , és készítsen vissza egyetlen dokumentumot, amelynek a szövege a "Historic" címkével van becsomagolva `<em></em>` . További információ az [Azure Cognitive Search-beli lekérdezések összeállításáról](https://docs.microsoft.com/azure/search/search-query-overview). 

A lekérdezéseket a kódban a **index.js** megnyitásával, majd a kód felüli hozzáadásával reprodukálhatja.

```javascript
const queries = [
    "*&$count=true",
    "historic&highlight=Description&$filter=Rating gt 4&"
];
```

Ugyanebben a **index.js** fájlban írja be az `doQueriesAsync()` alább látható függvényt. Ez a függvény egy objektumot vesz igénybe `AzureSearchClient` , és a `AzureSearchClient.queryAsync` metódust alkalmazza a tömbben lévő összes értékre `queries` . A függvény használatával `Promise.all()` egyetlen műveletet ad vissza `Promise` , amely csak akkor oldódik fel, ha az összes lekérdezés megoldódott. A `JSON.stringify(body, null, 4)` lekérdezési eredmény olvashatóbbként való formázásának meghívása.

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

Módosítsa `run()` úgy a függvényt, hogy az indexelő működjön, majd hívja meg a `doQueriesAsync(client)` függvényt:

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

A megvalósításához `AzureSearchClient.queryAsync(query)` szerkessze **AzureSearchClient.js**fájlt. A kereséshez eltérő végpontra van szükség, és a keresési kifejezések URL-argumentumok lesznek, így a függvényt `getSearchUrl(searchTerm)` a `getIndexUrl()` `getPostDataUrl()` már megírt metódusokkal együtt adja hozzá.

```javascript
getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
 ```

A `queryAsync(searchTerm)` függvény **AzureSearchClient.js** is megy, és ugyanazt a struktúrát követi, mint `postDataAsync(data)` a többi lekérdezési funkció: 

```javascript
async queryAsync(searchTerm) {
    console.log("\n Querying...")
    const endpoint = this.getSearchUrl(searchTerm);
    const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
    AzureSearchClient.throwOnHttpError(response);
    return response;
}
```

A keresés a "GET" művelettel és a törzs nélkül történik, mivel a keresési kifejezés az URL részét képezi. Figyelje meg, hogy a a `queryAsync(searchTerm)` `this.queryKey` -t használja, ellentétben a felügyeleti kulcsot használó többi függvénnyel. A lekérdezési kulcsok, ahogy a név is jelenti, csak az index lekérdezésére használható, és nem használható az index bármilyen módon történő módosítására. A lekérdezési kulcsok ezért biztonságosabbá tétele az ügyfélalkalmazások számára történő terjesztéshez.

Futtassa a programot a alkalmazással `node index.js` . Az előző lépéseken kívül a rendszer elküldi a lekérdezéseket és a konzolra írt eredményeket.

### <a name="about-the-sample"></a>A minta ismertetése

A minta kis mennyiségű szállodai adat használatát mutatja be, amely elegendő az Azure Cognitive Search index létrehozásának és lekérdezésének alapjaihoz.

A **AzureSearchClient** osztály a keresési szolgáltatás konfigurációját, URL-címeit és alapszintű http-kéréseit ágyazza be. A **index.js** fájl betölti az Azure Cognitive Search szolgáltatás konfigurációs adatait, az indexelésre feltöltendő szállodai adatok, valamint a `run` függvényében a megrendeléseit, és végrehajtja a különböző műveleteket.

A függvény általános viselkedése az `run` Azure Cognitive Search index törlése, ha létezik, hozza létre az indexet, adjon hozzá néhány adathoz, és hajtson végre néhány lekérdezést.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

Ebben a Node.js rövid útmutatóban egy index létrehozásához, a dokumentumokkal való betöltéshez és a lekérdezések futtatásához kapcsolódó feladatok sorozatán keresztül dolgozott. Bizonyos lépések, például a konfiguráció olvasása és a lekérdezések meghatározása a lehető legegyszerűbb módon történt. Egy valós alkalmazásban ezeket a problémákat külön modulokban érdemes elhelyezni, amelyek rugalmasságot és beágyazást tesznek lehetővé. 
 
Ha már rendelkezik valamilyen háttérrel az Azure Cognitive Search-ban, ezt a mintát használhatja kiindulópontként a kipróbálási javaslatok (típus-vagy automatikus kiegészítés), a szűrők és a csiszolt navigáció érdekében. Ha még nem ismeri az Azure Cognitive Searcht, javasoljuk, hogy más oktatóanyagokat próbáljon meg létrehozni, hogy megértsük, mit hozhat létre. További forrásokat a [dokumentációs oldalunkon](https://azure.microsoft.com/documentation/services/search/) talál. 

> [!div class="nextstepaction"]
> [Azure-Cognitive Search meghívása weboldalról JavaScript használatával](https://github.com/liamca/azure-search-javascript-samples)
