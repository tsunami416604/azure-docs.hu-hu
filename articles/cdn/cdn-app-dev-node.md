---
title: Az Azure CDN SDK-val első lépései a node.js-hez |} A Microsoft Docs
description: Ismerje meg, hogyan írhat az Azure CDN szolgáltatás felügyelete a Node.js-alkalmazások.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 46ae8cd9775432d126cbde856c1fb06ea319297e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38301564"
---
# <a name="get-started-with-azure-cdn-development"></a>Ismerkedés az Azure CDN-fejlesztéssel
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Használhatja a [Azure CDN SDK for Node.js](https://www.npmjs.com/package/azure-arm-cdn) létrehozása és a CDN-profilok és a végpontok felügyeletének automatizálására.  Ez az oktatóanyag végigvezeti az egyszerű Node.js-konzolalkalmazást, amely bemutatja az elérhető műveletek számos létrehozását.  Ebben az oktatóanyagban nem célja, hogy minden szempontból az Azure CDN SDK for node.js használatával részletesen leírja.

Az oktatóanyag elvégzéséhez, már rendelkezik [Node.js](http://www.nodejs.org) **4.x.x** vagy újabb verziója telepítve és konfigurálva.  Használhat bármilyen szövegszerkesztővel, a Node.js-alkalmazás létrehozásához.  Ebben az oktatóanyagban írni használt [Visual Studio Code](https://code.visualstudio.com).  

> [!TIP]
> A [ebből az oktatóanyagból befejezett projekt](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) letölthető az MSDN Webhelyén.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>A projekt létrehozása és az NPM-függőségek hozzáadása
Most, hogy már létre egy erőforráscsoportot a CDN-profilok és kap az Azure AD alkalmazás engedélyt, kezelheti a CDN-profilokat és a csoporton belüli végpontok, hogy megkezdheti az alkalmazás létrehozása.

Hozzon létre egy mappát az alkalmazás tárolásához.  A Node.js-eszközök az aktuális elérési úton a konzolon az aktuális tartózkodási helyét adja meg az új mappába, és úgy, hogy végrehajtja a projekt inicializálása:

    npm init

Ezután megjelenik a projekt inicializálása kérdéssor.  A **belépési pont**, ebben az oktatóanyagban *app.js*.  Az egyéb lehetőségek az alábbi példában látható.

![Az NPM init kimenet](./media/cdn-app-dev-node/cdn-npm-init.png)

A projekt már inicializálva van a egy *packages.json* fájlt.  A projekt van fogjuk használni az egyes Azure-kódtárak NPM csomagok található.  Az Azure-ügyfél modul a node.js-hez (ms-rest – azure) és az Azure CDN ügyféloldali kódtára a node.js-ben (az azure-arm-cd) fogjuk használni.  Függőségek, adjuk hozzá azokat a projekthez.

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Miután végzett a csomagok telepítése, a *package.json* fájl ebben a példában (verzió számok és tartalma eltérhet) hasonlóan kell kinéznie:

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

Végül szövegszerkesztőben, hozzon létre egy üres szövegfájlt, és mentse a projekt mappába gyökere *app.js*.  Készen állunk most már megkezdheti a kódírás.

## <a name="requires-constants-authentication-and-structure"></a>Szükség van, állandók, hitelesítési és szerkezete
A *app.js* nyissa meg a szerkesztőben, adja meg a program írt alapszintű struktúrát.

1. Adja hozzá a "szükséges" az NPM csomagok tetején a következő:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. Azt kell meghatároznia az egyes állandókat a módszert fogja használni.  Adja hozzá a következőket.  Ügyeljen arra, hogy cserélje le a zárójelben, beleértve a  **&lt;csúcsos zárójeleket&gt;**, igény szerint a saját értékeire.
   
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
3. Lesz ezután azt példányt létrehozni a CDN felügyeleti ügyfél, és adjon meg a hitelesítő adatokat.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Ha egyéni felhasználói hitelesítést használ, két sort némileg eltérő fog kinézni.
   
   > [!IMPORTANT]
   > Ez a kódminta csak akkor használja, ha szeretné, hogy az egyes felhasználói hitelesítés helyett egy egyszerű szolgáltatás kiválasztása.  Ügyeljen arra, hogy ezáltal az egyéni felhasználói hitelesítő adatait, és tartsa titokban.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Ne felejtse el elemeinek **&lt;csúcsos zárójeleket&gt;** a megfelelő információkkal.  A `<redirect URI>`, használja az átirányítási URI-t az Azure ad-ben az alkalmazás regisztrációja során megadott.
4. A Node.js-Konzolalkalmazás egyes parancssori paraméterek lesz.  Biztosítsa, hogy legalább egy paraméter lett átadva.
   
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
5. Értünk a program, ahol azt ágban más funkciók alapján mely paraméterek lettek átadva a fő részét.
   
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
6. A programra több helyen kell ellenőrizze, hogy a megfelelő számú paraméter lett átadva a, és néhány Súgó megjelenítése, ha azok nem tűnik megfelelő.  Hozzunk létre funkciók valósítható meg.
   
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
7. Végül a CDN felügyeleti ügyfél fogjuk használni a függvények aszinkron, így egy módszer meghívásához vissza, ha kész van szükségük.  Tekintsük egyet, amely a CDN felügyeleti ügyfél (ha vannak) kimenetének megjelenítéséhez és szabályosan Kilépés a programból.
   
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

Most, hogy a program az alapszintű struktúrát írt, hogy hozzon létre a függvényeket, a paraméterek alapján nevű.

## <a name="list-cdn-profiles-and-endpoints"></a>A CDN-profilok listázása és végpontok
Kezdjük a kód a meglévő profilok és a végpontok listáját.  Saját kód megjegyzéseket a várt szintaxist biztosít, így a tudjuk, ahol az egyes paraméterek kerül.

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

## <a name="create-cdn-profiles-and-endpoints"></a>CDN-profilok és a végpontok létrehozása
Ezt követően a funkciók profilok és a végpontok létrehozásához fog írunk.

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

## <a name="purge-an-endpoint"></a>-Végpont végleges törlése
Feltéve, hogy a végpont létrejött, egy gyakori tevékenység, amelyet meg szeretnénk előfordulhat, hogy hajtsa végre a programra van kiürítése a végpont a tartalom.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN-profilok és a végpontok törlése
Az utolsó függvény bevezetjük a végpontok és a profilok törlése.

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
Most már végezhetünk, a Node.js-program a kedvenc hibakereső használatával vagy a konzolon.

> [!TIP]
> Ha a Visual Studio Code-ot használ az hibakereső, szüksége a környezet beállítása a parancssori paraméterek át.  A Visual Studio Code-ot azért teszi ezt a **lanuch.json** fájlt.  Vlastnost s keressen **args** , és adja hozzá a karakterlánc-értékeket a paraméterekhez tömbje, hogy ehhez hasonlóan néz ki: `"args": ["list", "profiles"]`.
> 
> 

Először listázza a profilok.

![Profilok listázása](./media/cdn-app-dev-node/cdn-list-profiles.png)

Fájt vissza egy üres tömb.  Mivel nem rendelkezünk a profilokat létrehoztuk az erőforráscsoportot, a várt érték, amely.  Hozzunk létre most egy profilt.

![Profil létrehozása](./media/cdn-app-dev-node/cdn-create-profile.png)

Most adjunk hozzá egy végpontot.

![Végpont létrehozása](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Végül tekintsük törli a profilt.

![Profil törlése](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>További lépések
A kész projektet ebben a bemutatóban a megtekintéséhez [töltse le a mintát](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

A hivatkozás megtekintéséhez az Azure CDN SDK a node.js-ben, megtekintheti a [referencia](http://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

További dokumentáció keresése az Azure SDK a Node.js-hez, tekintse meg a [referencia teljes](http://azure.github.io/azure-sdk-for-node/).

A CDN-erőforrások kezelése a [PowerShell](cdn-manage-powershell.md).

