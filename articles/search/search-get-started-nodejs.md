---
title: 'Node. js gyors útmutató: Indexek létrehozása, betöltése és lekérdezése a Azure Search REST API-kkal – Azure Search'
description: Node. js-minta a Azure Searchhoz, amely bemutatja, hogyan lehet a JavaScriptből létrehozni, betölteni a betöltést és a lekérdezést.
author: lobrien
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: laobri
ms.openlocfilehash: 3a0b5706b41bdc51a4fe6e49b20296d3824b717c
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947122"
---
# <a name="quickstart-create-an-azure-search-index-in-nodejs"></a>Gyors útmutató: Azure Search index létrehozása a Node. js-ben
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Portál](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Hozzon létre egy Node. js-alkalmazást, amely egy Azure Search indexet hoz létre, tölt be és kérdez le. Ez a cikk bemutatja, hogyan hozhatja létre az alkalmazást lépésről lépésre. Másik lehetőségként [letöltheti a forráskódot és](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/) az adatforrást, és futtathatja az alkalmazást a parancssorból.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban a következő szolgáltatásokat, eszközöket és adatfájlokat használja a rendszer.

+ [Node.js](https://nodejs.org).
+ A [NPM](https://www.npmjs.com) -et a Node. js-nek kell telepítenie.
+ Ebben a cikkben vagy a tárházban a minta index struktúrája és a megfelelő dokumentumok [](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/)szerepelnek.
+ [Hozzon létre egy Azure Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat.

Ajánlott

* [Visual Studio Code](https://code.visualstudio.com).
* A VSCode [szebb](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) és [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) bővítményei.

<a name="get-service-info"></a>
## <a name="get-keys-and-urls"></a>Kulcsok és URL-címek lekérése

A szolgáltatás felé irányuló hívások URL-végpontot és hozzáférési kulcsot igényelnek minden kérelemben. Mindkettőhöz létrejön egy keresési szolgáltatás, így ha hozzáadta az előfizetéséhez az Azure Searchöt, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **áttekintése** lapon szerezze be a keresési szolgáltatás nevét. A szolgáltatás nevét a végpont URL-címének áttekintésével ellenőrizheti. Ha a végpont URL- `https://mydemo.search.windows.net`címe volt, a szolgáltatás neve `mydemo`a következő lesz:.

2. A **Beállítások** > **kulcsaiban**kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

    Kérje le a lekérdezési kulcsot is. Ajánlott a lekérdezési kérelmeket csak olvasási hozzáféréssel kibocsátani.

![A szolgáltatás nevének és a rendszergazda és a lekérdezési kulcsok beszerzése](media/search-get-started-nodejs/service-name-and-keys.png)

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kérelem fejlécében. Egy érvényes kulcs a kérést küldő alkalmazás és az azt kezelő szolgáltatás közötti megbízhatósági kapcsolatot hoz létre a kérelmek alapján.

## <a name="set-up-your-environment"></a>A környezet kialakítása

Először nyisson meg egy PowerShell-konzolt vagy más környezetet, amelyben telepítette a Node. js-t.

1. Hozzon létre egy fejlesztői könyvtárat, amely a `quickstart` nevét adja meg:

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. A futtatásával `npm init`inicializáljon egy üres projektet a NPM. Fogadja el az alapértelmezett értékeket, kivéve a licencet, amelyet a "MIT" értékre kell beállítani. 

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
      "description": "Azure Search Quickstart",
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
Hozzon létre egy **azure_search_config. JSON** fájlt a keresési szolgáltatás adatai tárolásához:

```json
{
    "serviceName" : "[SERVICE_NAME]",
    "adminKey" : "[ADMIN_KEY]",
    "queryKey" : "[QUERY_KEY]",
    "indexName" : "hotels-quickstart"
}
```

Cserélje le `[SERVICE_NAME]` az értéket a keresési szolgáltatás nevére. Cserélje `[ADMIN_KEY]` le `[QUERY_KEY]` a és a értéket a korábban feljegyzett kulcsokra. 

## <a name="1---create-index"></a>1 – index létrehozása 

Hozzon létre egy **hotels_quickstart_index. JSON**fájlt.  Ez a fájl határozza meg, hogy a Azure Search hogyan működik a következő lépésben betöltés alatt álló dokumentumokkal. Az egyes mezők azonosítása a `name` és a megadott `type`értékkel történik. Az egyes mezők több index-attribútummal is rendelkeznek, amelyek megadják, hogy Azure Search kereshet, szűrheti, rendezheti és részletezheti a mezőt. A mezők többsége egyszerű adattípusú, néhány például `AddressType` olyan összetett típus, amely lehetővé teszi, hogy gazdag adatstruktúrákat hozzon létre az indexében.  További információt a [támogatott](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) adattípusokról és az [index attribútumairól](https://docs.microsoft.com/azure/search/search-what-is-an-index#index-attributes)itt olvashat. 

Adja hozzá a következőt a **hotels_quickstart_index. JSON** fájlhoz, vagy [töltse le a fájlt](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/blob/master/hotels_quickstart_index.json). 

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
    

Célszerű elkülöníteni egy adott forgatókönyv sajátosságait a széles körben alkalmazandó kódból. A `AzureSearchClient` **AzureSearchClient. js** fájlban meghatározott osztály tudni fogja, hogyan kell létrehozni a kérelem URL-címeit, a beolvasás API-val történő kérést, és reagálni a válasz állapotkódot.

Kezdjen el dolgozni a **AzureSearchClient. js** -ben a **Node-Fetch** csomag importálásával és egy egyszerű osztály létrehozásával. A különböző konfigurációs értékek megadásával elkülönítheti a `AzureSearchClient` osztály cserélhető részeit:

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

Az osztály első feladata, hogy megtudja, hogyan hozhat létre olyan URL-címeket, amelyekhez a különböző kéréseket küldeni szeretné. Ezeket az URL-címeket olyan példány-metódusokkal hozza létre, amelyek az osztály konstruktorának átadott konfigurációs adathalmazt használják. Figyelje meg, hogy az általuk létrehozott URL-cím egy API-verzióra vonatkozik, és rendelkeznie kell egy olyan argumentummal, `2019-05-06`amely megadja az adott verziót (ebben az alkalmazásban). 

Adja hozzá a következő metódust az osztály törzsében:

```javascript
    getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }

```

A következő felelősség egy aszinkron kérelmet készít a beolvasási API-val. Az aszinkron statikus metódus `request` egy URL-címet, egy karakterláncot ad meg, amely megadja a http-metódust ("Get", "put", "post", "Delete"), a kérelemben használandó kulcsot, valamint egy opcionális JSON-objektumot. A `headers` változó leképezi az `queryKey` (akár a rendszergazdai kulcsot, akár az írásvédett lekérdezési kulcsot) az "API-Key" http-kérelem fejlécére. A kérések beállításai mindig tartalmazzák `method` a használni kívánt és a `headers`. Ha `bodyJson` `bodyJson`nem `null`, a HTTP-kérelem törzse a karakterlánc-ábrázolására van beállítva. A `request` a beolvasás API meghívását adja vissza a HTTP-kérelem végrehajtásához.

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

Bemutató céljára csak akkor fogunk kivételt okozni, ha a HTTP-kérelem nem sikeres. Egy valós alkalmazásban valószínűleg elvégezheti a http- `response` állapotkód naplózását és diagnosztizálását a keresési szolgáltatás kérelmében. 
    
```javascript
    static throwOnHttpError(response) {
    const statusCode = response.status;
    if (statusCode >= 300){
        console.log(`Request failed: ${JSON.stringify(response, null, 4)}`);
        throw new Error(`Failure in request. HTTP Status was ${statusCode}`);
    }
    }
```

Végül adja hozzá a Azure Search index észleléséhez, törléséhez és létrehozásához szükséges metódusokat. Ezek a metódusok mind ugyanazzal a struktúrával rendelkeznek:

* Szerezze be azt a végpontot, amelyhez a kérést el szeretné végezni.
* A kérelem előállítása a megfelelő végponttal, HTTP-művelettel, API-kulccsal és törzstel. `queryAsync()`a lekérdezési kulcsot használja, ellenkező esetben a rendszer a rendszergazdai kulcsot használja.
* `await`a kérelemre adott válasz.  
* A válasz állapotkód alapján járjon el.
* Egy megfelelő érték (Boolean, `this`vagy lekérdezési eredmények) ígéretének visszaadása. 

```javascript
    async indexExistsAsync() { 
        console.log("\n Checking if index exists...");
        const endpoint = this.getIndexUrl();
        const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
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

Egy objektumorientált osztály jó választás volt az esetlegesen újrafelhasználható **AzureSearchClient. js** -modulhoz, de a főprogramhoz nem szükséges, amelyet az **index. js**nevű fájlba helyezünk. 

Hozza létre az **index. js fájlt** , és kezdje a következőket:

* A **NConf** csomag, amely rugalmasságot biztosít a JSON-, környezeti változók vagy parancssori argumentumok konfigurációjának megadásához.
* Az **hotels_quickstart_index. JSON** fájlból származó adatok.
* Az `AzureSearchClient` modult.

```javascript
const nconf = require('nconf');

const indexDefinition = require('./hotels_quickstart_index.json');
const AzureSearchClient = require('./AzureSearchClient.js');
```

A [ **NConf** csomag](https://github.com/indexzero/nconf) lehetővé teszi, hogy különböző formátumokban (például környezeti változókban vagy a parancssorban) megadhatja a konfigurációs adatmennyiséget. A **NConf** alapszintű módon fogjuk használni a **azure_search_config. JSON** fájl olvasásához és a fájl tartalmának a szótárként való visszaküldéséhez. A **NConf** `get(key)` funkciójának használatával gyorsan megtekintheti, hogy a konfigurációs adatok megfelelően vannak-e testreszabva. Végül visszaállítjuk a konfigurációt:

```javascript
function getAzureConfiguration() {
    const config = nconf.file({ file: 'azure_search_config.json' });
    if (config.get('serviceName') === '[SEARCH_SERVICE_NAME' ) {
    throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
    }
    return config;
}
```

A `sleep` függvény létrehoz egy `Promise` , a megadott idő után feloldható megoldást. A függvény használata lehetővé teszi, hogy az alkalmazás szüneteltethető legyen, miközben várakozik az aszinkron indexelési műveletek befejezésére, és elérhetővé válik. Ilyen késleltetés hozzáadására általában csak a demók, tesztek és példák esetében van szükség.

```javascript
function sleep(ms)
{
    return(
        new Promise(function(resolve, reject)
        {
            setTimeout(function() { resolve(); }, ms);
        })
    );
}
```

Végül írja be és hívja meg a fő `run` aszinkron függvényt. Ez a függvény sorrendben hívja meg a többi függvényt, az s feloldásához `Promise`szükséges módon.

* A korábban írt konfiguráció `getAzureConfiguration()` beolvasása
* Hozzon létre `AzureSearchClient` egy új példányt, amely átadja a konfiguráció értékeit
* Ellenőrizze, hogy az index létezik-e, és ha igen, törölje
* Index létrehozása a `indexDefinition` betöltött **hotels_quickstart_index. JSON** használatával
* Adja hozzá a **Hotels. JSON** fájlból betöltött szállodákhoz tartozó dokumentumokat.
* A Azure Search index lekérdezése az `doQueriesAsync()` Ön által írt módszer használatával

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get["serviceName"]);
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        const indexDefinition = require('./hotels_quickstart_index.json');
        await client.createIndexAsync(indexDefinition);
    } catch (x) {
        console.log(x);
    }
}

run();
```

Ne felejtsük el, hogy a `run()`végső hívás! A következő lépésben való futtatáskor `node index.js` a program beléptetési pontja.

Figyelje meg `AzureSearchClient.indexExistsAsync()` , `AzureSearchClient.deleteIndexAsync()` hogy nem végez paramétereket. Ezek a függvények `AzureSearchClient.request()` argumentum nélkül `bodyJson` hívhatók. `AzureSearchClient.request()`A (z `bodyJson === null` `deleteIndexAsync()` `indexExistsAsync()` ) `true`óta a`init` struktúra úgy van beállítva, hogy csak a http-művelet ("Get" és "Delete") és a fejlécek legyenek megadva, amelyek meghatározzák a kérés kulcsát.  

Ezzel szemben a `AzureSearchClient.createIndexAsync(indexDefinition)` metódus egy paramétert hajt végre. A `run` függvény a `index.js`alkalmazásban továbbítja a **hotels_quickstart_index. JSON** fájl tartalmát a `AzureSearchClient.createIndexAsync(indexDefinition)` metódusnak. A `createIndexAsync()` metódus átadja ezt a `AzureSearchClient.request()`definíciót a következőnek:. A `AzureSearchClient.request()`-ben `bodyJson === null` azóta `false`a `body` struktúra nem csak a http-műveletet ("put") és a fejléceket tartalmazza, de az index definíciós adatát állítja be. `init`

### <a name="prepare-and-run-the-sample"></a>A minta előkészítése és futtatása

A következő parancsokhoz használjon egy terminál-ablakot.

1. Navigáljon ahhoz a mappához, amely tartalmazza a **Package. JSON** fájlt és a kód többi részét.
1. Telepítse a minta `npm install`csomagjait a következővel:.  Ez a parancs letölti a csomagokat, amelyeken a kód függ.
1. Futtassa a programot a `node index.js`alkalmazással.

A program által végrehajtott műveleteket ismertető üzenetnek kell megjelennie. Ha szeretné megtekinteni a kérések részletesebb ismertetését, a **AzureSearchClient. js** [ `AzureSearchClient.request()` metódusban](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/blob/master/AzureSearchClient.js#LL20-LL26) megadhatja a metódus elején található sorokat. 

Nyissa meg a keresési szolgáltatás áttekintését a Azure Portal. Válassza ki az indexek lapot. Az alábbihoz hasonló kimenet jelenik meg:

![Képernyőkép a Azure Portalről, Search Service áttekintése, indexek lap](media/search-get-started-nodejs/create-index-no-data.png)

A következő lépésben felvesszük az adatindexbe. 

## <a name="2---load-documents"></a>2 – dokumentumok betöltése 

Azure Search a dokumentumok olyan adatstruktúrák, amelyek mind a lekérdezések indexeléséhez, mind pedig a kimenetekhez tartoznak. Ezeket az adatsorokat az indexbe kell ELKÜLDENI. Ez egy másik végpontot használ, mint az előző lépésben végzett műveletek. Nyissa meg a **AzureSearchClient. js fájlt** , és `getIndexUrl()`adja hozzá a következő metódust az után:

```javascript
 getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
```

Ehhez `AzureSearchClient.createIndexAsync(definition)`hasonlóan olyan függvényre van szükség, `AzureSearchClient.request()` amely meghívja és továbbítja a szállodai adatát a törzsének. A **AzureSearchClient. js** Hozzáadás `postDataAsync(hotelsData)` után `createIndexAsync(definition)`:

```javascript
async postDataAsync(hotelsData) {
    console.log("\n Adding hotel data...");
    const endpoint = this.getPostDataUrl();
    const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

 A dokumentumok bemenetei lehetnek egy adatbázisban lévő sorok, a blob Storage-ban található Blobok, vagy a példában szereplő JSON-dokumentumok a lemezen. Töltse le a [Hotels. JSON](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/blob/master/hotels.json) fájlt, vagy hozzon létre saját **Hotels. JSON** fájlt a következő tartalommal:

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

Az adatgyűjtés programba való betöltéséhez módosítsa az **index. js fájlt** úgy, hogy `hotelData` hozzáadja a következőhöz közeli sort:

```javascript
const nconf = require('nconf');

const hotelData = require('./hotels.json');
const indexDefinition = require('./hotels_quickstart_index.json');
```

Most módosítsa a `run()` függvényt az **index. js**fájlban. Az index elérhetővé válása néhány másodpercig is eltarthat, így a hívás `AzureSearchClient.postDataAsync(hotelData)`előtt vegyen fel egy 2 másodperces szüneteltetést:

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

Futtassa újra `node index.js`a programot a alkalmazással. Az 1. lépésben látott, némileg eltérő üzeneteket kell látnia. Ebben az esetben az index létezik, és a törölt törléssel kapcsolatos üzenetnek kell megjelennie, mielőtt az alkalmazás létrehozza az új indexet, és beküldi az adatait. 

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Térjen vissza az **indexek** lapra a Azure Portal keresési szolgáltatásának **áttekintésében** . Az index most négy dokumentumot tartalmaz, és bizonyos mennyiségű tárterületet használ (ez eltarthat néhány percig, hogy a felhasználói felület megfelelően tükrözze az index mögöttes állapotát). Kattintson az index nevére a **keresési tallózóhoz**. Ez a lap lehetővé teszi az adatlekérdezésekkel való kísérletezést. Próbáljon meg lekérdezési karakterláncot `*&$count=true` keresni, és az összes dokumentumot és az eredmények számát vissza kell kérnie. Próbálja ki a lekérdezési `historic&highlight=Description&$filter=Rating gt 4` karakterláncot, és készítsen vissza egyetlen dokumentumot, amelynek a szövege a "Historic" `<em></em>` címkével van becsomagolva. További információ a [lekérdezések összeállításáról Azure Searchban](https://docs.microsoft.com/azure/search/search-query-overview). 

A lekérdezéseket a kódban az **index. js** megnyitásával, majd a kód felülre történő hozzáadásával reprodukálhatja.

```javascript
const queries = [
    "*&$count=true",
    "historic&highlight=Description&$filter=Rating gt 4&"
];
```

Ugyanebben az **index. js** fájlban írja be az `doQueries()` alább látható függvényt. Ez a függvény egy `AzureSearchClient` objektumot vesz igénybe `AzureSearchClient.queryAsync` , és a metódust alkalmazza a `queries` tömbben lévő összes értékre. A `Promise.all()` függvény használatával egyetlen `Promise` műveletet ad vissza, amely csak akkor oldódik fel, ha az összes lekérdezés megoldódott. A lekérdezési `JSON.stringify(body, null, 4)` eredmény olvashatóbbként való formázásának meghívása.

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

Módosítsa úgy `run()` a függvényt, hogy az indexelő működjön, majd hívja meg a `doQueriesAsync(client)` függvényt:

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

A megvalósításhoz `AzureSearchClient.queryAsync(query)`szerkessze a **AzureSearchClient. js**fájlt. A kereséshez eltérő végpont szükséges, ezért a függvényt `getSearchUrl(searchTerm)` `getPostDataUrl()` a `getIndexUrl()` már megírt módszerek mellett adja hozzá.

```javascript
getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
 ```

A `queryAsync(searchTerm)` függvény a **AzureSearchClient. js** fájlban is szerepel, és ugyanazt a struktúrát követi, mint `postDataAsync(data)` a többi lekérdezési funkció: 

```javascript
async queryAsync(searchTerm) {
    console.log("\n Querying...")
    const endpoint = this.getSearchUrl(searchTerm);
    const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
    AzureSearchClient.throwOnHttpError(response);
    return response;
}
```

A keresés a "GET" művelettel és a törzs nélkül történik, mivel a keresési kifejezés az URL részét képezi. Figyelje meg, hogy a használt `this.adminKey` `queryAsync(searchTerm)` többi függvénytől eltérően a `this.queryKey`használ. A lekérdezési kulcsok, ahogy a név is jelenti, csak az index lekérdezésére használható, és nem használható az index bármilyen módon történő módosítására. A lekérdezési kulcsok ezért biztonságosabbá tétele az ügyfélalkalmazások számára történő terjesztéshez.

Futtassa a programot a `node index.js`alkalmazással. Az előző lépéseken kívül a rendszer elküldi a lekérdezéseket és a konzolra írt eredményeket.

### <a name="about-the-sample"></a>A minta ismertetése

A minta kis mennyiségű szállodai adat használatát mutatja be, ami elegendő ahhoz, hogy bemutassa a Azure Search index létrehozásának és lekérdezésének alapjait.

A **AzureSearchClient** osztály a keresési szolgáltatás konfigurációját, URL-címeit és alapszintű http-kéréseit ágyazza be. Az **index. js** fájl betölti a Azure Search szolgáltatás konfigurációs adatait, az indexeléshez feltöltött szállodai adatok, valamint a `run` függvény, a megrendelések, és végrehajtja a különböző műveleteket.

A `run` függvény általános viselkedése az Azure Search index törlése, ha létezik, az index létrehozása, adatok hozzáadása és néhány lekérdezés végrehajtása.  

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása 

Ha saját előfizetésében dolgozik, a projekt végén érdemes megállapítani, hogy továbbra is szüksége van-e a létrehozott erőforrásokra. A már futó erőforrások pénzbe kerülnek. Az erőforrásokat egyenként is törölheti, vagy az erőforráscsoport törlésével törölheti a teljes erőforrás-készletet.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.
Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

Ebben a Node. js-útmutatóban egy sor feladatot dolgozott ki egy index létrehozásához, a dokumentumok betöltéséhez és a lekérdezések futtatásához. Bizonyos lépések, például a konfiguráció olvasása és a lekérdezések meghatározása a lehető legegyszerűbb módon történt. Egy valós alkalmazásban ezeket a problémákat külön modulokban érdemes elhelyezni, amelyek rugalmasságot és beágyazást tesznek lehetővé. 
 
Ha már rendelkezik bizonyos tapasztalattal az Azure Search használatában, ezt a mintát akár ugródeszkaként is használhatja a javaslattevők (előre begépelt vagy automatikusan kitöltött lekérdezések), szűrők és a jellemzőalapú navigáció kipróbálásához. Ha még nem ismeri a Azure Searcht, javasoljuk, hogy más oktatóanyagokat próbáljon meg létrehozni, hogy megértsük, mit hozhat létre. További forrásokat a [dokumentációs oldalunkon](https://azure.microsoft.com/documentation/services/search/) talál. 

> [!div class="nextstepaction"]
> [Weblapon lévő Azure Search hívása JavaScript használatával](https://github.com/liamca/azure-search-javascript-samples)
