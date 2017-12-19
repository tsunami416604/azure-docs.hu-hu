---
title: "A Node.js Ismerkedés az Azure CDN SDK-val |} Microsoft Docs"
description: "Node.js-alkalmazások kezelése az Azure CDN írásának ismertetése."
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: 
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 46ae8cd9775432d126cbde856c1fb06ea319297e
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="get-started-with-azure-cdn-development"></a>Ismerkedés az Azure CDN-fejlesztéssel
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Használhatja a [Azure CDN SDK for Node.js](https://www.npmjs.com/package/azure-arm-cdn) létrehozása és a CDN-profil és a végpontok automatizálására.  Ez az oktatóanyag bemutatja, hogyan kell létrehozni egy egyszerű Node.js-Konzolalkalmazás, azt mutatja be a rendelkezésre álló műveletek számos.  Ez az oktatóanyag nem célja, hogy a Node.js, részletesen leírja az Azure CDN SDK minden szempontját.

Az oktatóanyag elvégzéséhez, akkor már rendelkezik [Node.js](http://www.nodejs.org) **4.x.x** vagy újabb rendszerre telepített és konfigurált.  Használhat bármilyen szövegszerkesztővel, a Node.js-alkalmazás létrehozásához.  Ez az oktatóanyag írni használt [Visual Studio Code](https://code.visualstudio.com).  

> [!TIP]
> A [az oktatóanyagot befejezett projekt](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) letölthető az MSDN Webhelyén.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>A projekt létrehozása és hozzáadása NPM függőségek
Most, hogy előre létrehozott erőforráscsoport a CDN-profil és a CDN-profil és a csoporton belüli végpontok kezelése az Azure AD-alkalmazás engedélyt kap, nem lehet elindítani, az alkalmazás létrehozása.

Hozzon létre egy mappát az alkalmazás tárolásához.  Az aktuális elérési úthoz, a Node.js eszközökkel konzolon az aktuális hely beállítása az új mappába, és a projekt inicializálása a következő futtatásával:

    npm init

Majd választhat a projekt inicializálása kérdések sorát teszi fel.  A **belépési pont**, ez az oktatóanyag használja *app.js*.  Az egyéb lehetőségek az alábbi példában látható.

![NPM init kimeneti](./media/cdn-app-dev-node/cdn-npm-init.png)

A projekt már inicializálva van egy *packages.json* fájlt.  A projekt üzemeltetéséhez kívánja használni az NPM csomagban foglalt Azure könyvtárak.  Az Azure-ügyfél futásidejű a Node.js (ms-rest-azure) és az Azure CDN ügyféloldali kódtára a Node.js (azure-arm-cd) fogjuk használni.  Szerint függőségeinek adjuk hozzá azokat a projekthez.

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Miután végzett a csomagok telepítése, a *package.json* fájl ebben a példában (verzió: számok eltérőek lehetnek) hasonlóan kell kinéznie:

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Végezetül a szövegszerkesztővel, hozzon létre egy üres szöveges fájlt, és mentse a projekt mappában gyökérmappájában *app.js*.  Most még készen kód írása.

## <a name="requires-constants-authentication-and-structure"></a>Szükséges, állandók, hitelesítési és szerkezete
A *app.js* nyissa meg a szerkesztőben, folytassuk a program írása alapvető szerkezete.

1. Vegye fel a "szükséges" az NPM-csomagok tetején a következő:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. Adja meg az egyes állandók a módszerek fogja használni kell.  Adja hozzá a következő.  Ügyeljen arra, hogy cserélje le a helyőrzőket, beleértve a  **&lt;csúcsos zárójelek&gt;**, igény szerint a saját értékekkel.
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Lesz ezután azt példányt létrehozni a CDN-felügyeleti ügyfél, és adjon neki a hitelesítő adatokat.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Ha egyéni felhasználói hitelesítést használ, két sort némileg eltérő fog kinézni.
   
   > [!IMPORTANT]
   > Ha használja a kódmintában a felhasználói hitelesítés helyett egy egyszerű szolgáltatás kiválasztása.  Ügyeljen rá, és tartsa titokban az egyéni felhasználói hitelesítő adatait.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Ügyeljen arra, hogy az elemek cseréje  **&lt;csúcsos zárójelek&gt;**  a megfelelő információkkal.  A `<redirect URI>`, használja az átirányítási URI-t az Azure ad-ben az alkalmazás regisztrálásakor megadott.
4. A Node.js-Konzolalkalmazás lesz e parancssori paraméterek.  Most ellenőrzi, hogy legalább egy paramétert.
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. Amely számos lehetőséget kínál, a program, ahol azt fiókirodai más funkciók alapján milyen paraméterek lettek átadva a fő részére.
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. A program több helyen igazolnia kell győződjön meg arról, hogy a megfelelő számú paraméter lett átadva, és néhány Súgó megjelenítése, ha megfelelő nem megfelelő.  Hozzon létre, amely függvényt.
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. Végezetül a fogjuk használni a CDN-felügyeleti ügyfél függvényei aszinkron, egy metódust kell meghívni, amikor kész van szükségük.  Ellenőrizze egy, a kimenet a CDN-felügyeleti ügyfél (ha van ilyen), és a szabályosan kilép a programból.
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Most, hogy a program alapvető szerkezete írása, azt a függvény hívása a paraméterek alapján kell létrehoznia.

## <a name="list-cdn-profiles-and-endpoints"></a>Lista CDN-profil és -végpontok
Kezdjük kód a meglévő profilok és a végpontok listáját.  A kód megjegyzéseket adja meg a várt szintaxist, hogy tudjuk, ahol az egyes paramétereket kerül.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>CDN-profil és a végpontok létrehozása
A Funkciók, profilok és a végpontok létrehozása a következő fog írni azt.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>A végpont törlése
Feltéve, hogy a végpont létrehozását, azt szeretnénk, előfordulhat, hogy a program végrehajtásához egy közös tevékenység a végpont tartalmának van kiürítése.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN-profil és a végpontok törlése
Az utolsó függvény is végpontok és a profilok törlése.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>A program futtatása
Most már a Node.js-program használatával a kedvenc hibakereső végezhetünk vagy a konzolon.

> [!TIP]
> Visual Studio Code az hibakereső használata, ha szüksége a környezet kialakítása felelt meg a parancssori paraméterek.  A Visual Studio Code nem ez a **lanuch.json** fájlt.  Keresse meg nevű tulajdonság **argumentum** , és adja hozzá a karakterlánc-értékeket a paraméterek tömbje úgy, hogy hasonló: `"args": ["list", "profiles"]`.
> 
> 

Először a profilok listázása.

![Lista profilok](./media/cdn-app-dev-node/cdn-list-profiles.png)

Azt a kapott vissza üres tömb.  Jelenleg nincs a profil az erőforráscsoportban, mivel a várt érték, amely.  Most hozzon létre most egy profilt.

![Profil létrehozása](./media/cdn-app-dev-node/cdn-create-profile.png)

Most adjuk hozzá egy végpontot.

![-Végpont létrehozása](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Végezetül most törli a profilt.

![Profil törlése](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Következő lépések
Ez a forgatókönyv a befejezett projekt megjelenítéséhez [a minta letöltéséhez](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Tekintse meg a referencia az Azure CDN SDK-ban a Node.js, tekintse meg a [hivatkozás](http://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

További dokumentációjában talál az Azure SDK-val Node.js, tekintse meg a [hivatkozás teljes](http://azure.github.io/azure-sdk-for-node/).

A CDN erőforrások kezelése [PowerShell](cdn-manage-powershell.md).

