---
title: Ismerkedés az Azure CDN SDK for Node.js | Microsoft dokumentumok
description: Ismerje meg, hogyan írhat Node.js alkalmazásokat az Azure CDN kezeléséhez.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 18dbcbf93947306334ccc2c156d9266884198e19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594130"
---
# <a name="get-started-with-azure-cdn-development"></a>Ismerkedés az Azure CDN-fejlesztéssel
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Az Azure [CDN SDK node.js](https://www.npmjs.com/package/azure-arm-cdn) használatával automatizálhatja a CDN-profilok és végpontok létrehozását és felügyeletét.  Ez az oktatóanyag egy egyszerű Node.js konzolalkalmazás létrehozásán vezet be, amely számos elérhető műveletet mutat be.  Ez az oktatóanyag nem az Azure CDN SDK node.js részletes leírására szolgál.

Az oktatóanyag befejezéséhez már telepítve és konfigurálva kell lennie [a Node.js](https://www.nodejs.org) **4.x.x** vagy újabb verzióinak.  A Node.js alkalmazás létrehozásához bármilyen szövegszerkesztőt használhat.  Írni ezt a bemutató, én is [Visual Studio Code](https://code.visualstudio.com).  

> [!TIP]
> Az [oktatóanyag befejezett projektje](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) letölthető az MSDN-ről.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>A projekt létrehozása és NPM-függőségek hozzáadása
Most, hogy létrehoztunk egy erőforráscsoportot a CDN-profilokhoz, és az Azure AD-alkalmazásunk engedélyt adott a CDN-profilok és a csoporton belüli végpontok kezelésére, elkezdhetjük az alkalmazás létrehozását.

Hozzon létre egy mappát az alkalmazás tárolására.  Az aktuális elérési úton található Node.js eszközökkel rendelkező konzolról állítsa be az aktuális tartózkodási helyét erre az új mappára, és a projektet a végrehajtással inicializálja:

    npm init

Ezután egy sor kérdést fog nak bemutatni a projekt inicializálásához.  A **belépési pont**, ez az oktatóanyag használja *app.js*.  A következő példában a többi választásomat is láthatod.

![NPM init kimenet](./media/cdn-app-dev-node/cdn-npm-init.png)

A projektünk et most egy *packages.json* fájllal inicializáljuk.  Projektünk az NPM-csomagokban található egyes Azure-kódtárakat fogja használni.  Az Azure Client Runtime for Node.js (ms-rest-azure) és az Azure CDN ügyfélkódtár node.js (azure-arm-cd) használatát fogjuk használni.  Vegyük fel ezeket függőségként a projekthez.

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

A csomagok telepítése után a *package.json* fájlnak a példához hasonlóan kell kinéznie (a verziószámok eltérhetnek):

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

Végül a szövegszerkesztő használatával hozzon létre egy üres szövegfájlt, és mentse a projektmappa gyökerébe *app.js*néven.  Most már készen állunk a kód írására.

## <a name="requires-constants-authentication-and-structure"></a>Igényel, állandók, hitelesítés, és a szerkezet
Ha *az app.js* meg van nyitva a szerkesztőnkben, írjuk meg a programunk alapvető szerkezetét.

1. Adja hozzá a "szükséges" a mi NPM csomagok tetején a következő:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. Meg kell határoznunk néhány állandót, amit a módszereink használni fognak.  Adja hozzá a következőket.  Ügyeljen arra, hogy a helyőrzőket, beleértve a ** &lt;szögletes&gt;zárójeleket**is, szükség szerint cserélje ki a saját értékeire.
   
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
3. Ezután a CDN-kezelő ügyfelet példányosítjuk, és megadjuk neki a hitelesítő adatokat.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Ha egyéni felhasználói hitelesítést használ, ez a két sor kissé eltérő lesz.
   
   > [!IMPORTANT]
   > Csak akkor használja ezt a kódmintát, ha az egyszerű szolgáltatás helyett egyéni felhasználói hitelesítést választ.  Legyen óvatos, hogy őr az egyéni felhasználói hitelesítő adatokat, és tartsa őket titokban.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Ügyeljen arra, hogy a ** &lt;szögletes&gt; zárójelben** lévő elemeket a megfelelő információkkal cserélje le.  A `<redirect URI>`, használja az átirányítási URI-t, amikor regisztrálta az alkalmazást az Azure AD-ben.
4. A Node.js konzol alkalmazás unk néhány parancssori paramétert fog használni.  Érvényesítsük, hogy legalább egy paraméter át lett-e adva.
   
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
5. Ez elvezet minket a program fő részéhez, ahol más funkciókra ágazunk el a paraméterek alapján.
   
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
6. A program unk több helyén meg kell győződnünk arról, hogy a megfelelő számú paraméter tapadt be, és meg kell jeleníteni a segítséget, ha nem megfelelőnek tűnnek.  Hozzunk létre funkciókat erre.
   
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
7. Végül a CDN-felügyeleti ügyfélen használt funkciók aszinkronak, ezért szükségük van egy metódusra, amellyel visszahívhatók, amikor elkészült.  Tegyünk egy, amely képes megjeleníteni a kimenetet a CDN felügyeleti ügyfél (ha van ilyen), és kilép a programból kecsesen.
   
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

Most, hogy a programunk alapvető szerkezete meg van írva, létre kell hoznunk a paraméterek alapján hívott funkciókat.

## <a name="list-cdn-profiles-and-endpoints"></a>CDN-profilok és végpontok listázása
Kezdjük a kóddal a meglévő profilok és végpontok listázásához.  Saját kód megjegyzéseket adja meg a várt szintaxist, így tudjuk, hol minden paraméter megy.

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

## <a name="create-cdn-profiles-and-endpoints"></a>CDN-profilok és végpontok létrehozása
Ezután írjuk a függvényeket profilok és végpontok létrehozásához.

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

## <a name="purge-an-endpoint"></a>Végpont kiürítése
Feltételezve, hogy a végpont létrejött, az egyik gyakori feladat, hogy érdemes elvégezni a program ban a tartalom tisztítása a végpontunkban.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN-profilok és végpontok törlése
Az utolsó függvény fogunk tartalmazni végpontok és profilok törlése.

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
Most már végre a Node.js program segítségével kedvenc hibakereső, vagy a konzolon.

> [!TIP]
> Ha a Visual Studio-kódot használja hibakeresőként, be kell állítania a környezetet úgy, hogy az a parancssori paraméterekben haladjon át.  A Visual Studio Code ezt a **launch.json** fájlban teszi.  Keressen egy args nevű **tulajdonságot,** és adjon hozzá karakterláncértékeket a paraméterekhez, hogy az a következőhöz hasonlóan nézzen ki: `"args": ["list", "profiles"]`.
> 
> 

Kezdjük a profilunk listázásával.

![Listaprofilok](./media/cdn-app-dev-node/cdn-list-profiles.png)

Visszaszereztünk egy üres tömböt.  Mivel az erőforráscsoportban nincsenek profilok, ez várható.  Hozzunk létre egy profilt most.

![Profil létrehozása](./media/cdn-app-dev-node/cdn-create-profile.png)

Most adjunk hozzá egy végpontot.

![Végpont létrehozása](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Végül töröljük a profilunkat.

![Profil törlése](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Következő lépések
A forgatókönyvből a befejezett projekt megtekintéséhez [töltse le a mintát.](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74)

Az Azure CDN SDK node.js esetén az Azure CDN SDK hivatkozását a [tekintse](https://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/)meg.

További dokumentációt az Azure SDK node.js, tekintse meg a [teljes referencia](https://azure.github.io/azure-sdk-for-node/).

A CDN-erőforrások kezelése a [PowerShell használatával.](cdn-manage-powershell.md)

