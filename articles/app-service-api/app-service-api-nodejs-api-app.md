---
title: "Node.js API-alkalmazás az Azure App Service platformon | Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre Node.js RESTful API-t, és hogyan telepítheti egy Azure App Service platformon futó API-alkalmazásba."
services: app-service\api
documentationcenter: node
author: bradygaster
manager: erikre
editor: 
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: get-started-article
ms.date: 06/13/2017
ms.author: rachelap
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 8a5d0c60e101f4038dff6f76c8f23dbb2b44661c
ms.contentlocale: hu-hu
ms.lasthandoff: 06/28/2017


---
<a id="build-a-nodejs-restful-api-and-deploy-it-to-an-api-app-in-azure" class="xliff"></a>

# Node.js RESTful API buildjének elkészítése és telepítése Azure-ban futó API-alkalmazásba
[!INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Ez a gyors útmutató bemutatja, hogyan hozhat létre [Swagger](http://swagger.io/)-definícióból használt [Express](http://expressjs.com/) keretrendszerű Node.js REST API-t, és hogyan helyezheti üzembe [API-alkalmazásként](app-service-api-apps-why-best-platform.md) az Azure-on. Az alkalmazást parancssori eszközök használatával hozza létre, az erőforrások konfigurálását az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) segítségével végzi el, míg az alkalmazást a Git használatával helyezi üzembe.  Amikor végzett, Azure-on futó, működő minta REST API áll rendelkezésére majd.

<a id="prerequisites" class="xliff"></a>

## Előfeltételek

* [Git](https://git-scm.com/)
* [ Node.js és NPM](https://nodejs.org/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakör az Azure CLI 2.0-s vagy annál újabb verziójának futtatását követeli meg. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

<a id="prepare-your-environment" class="xliff"></a>

## A környezet előkészítése

1. A következő parancsot terminálablakban futtatva klónozhatja a mintát a helyi gépen.

    ```bash
    git clone https://github.com/Azure-Samples/app-service-api-node-contact-list
    ```

2. Váltson arra a könyvtárra, amelyben a mintakód megtalálható.

    ```bash
    cd app-service-api-node-contact-list
    ```

3. Telepítse a helyi gépen a [Swaggerize](https://www.npmjs.com/package/swaggerize-express) eszközt. A Swaggerize olyan eszköz, amely Swagger-definícióból generál Node.js-kódot a REST API-hoz.

    ```bash
    npm install -g yo
    npm install -g generator-swaggerize
    ```

<a id="generate-nodejs-code" class="xliff"></a>

## Node.js-kód generálása 

Az oktatóanyagnak ez a fejezete az API-fejlesztésnek azt a folyamatát modellezi, amelynek során először létrehozzuk a Swagger-metaadatokat, majd ezek segítségével automatikusan generáljuk az API kiszolgálói kódját. 

Lépjen a *start*mappa könyvtárába, majd futtassa a `yo swaggerize` parancsot. A Swaggerize az *api.json* fájlban lévő Swagger-definícióból hoz létre Node.js-projektet az API-hoz.

    ```bash
    cd start
    yo swaggerize --apiPath api.json --framework express
    ```

     When Swaggerize asks for a project name, use *ContactList*.
   
   ```bash
   Swaggerize Generator
   Tell us a bit about your application
   ? What would you like to call this project: ContactList
   ? Your name: Francis Totten
   ? Your github user name: fabfrank
   ? Your email: frank@fabrikam.net
   ```
   
<a id="customize-the-project-code" class="xliff"></a>

## Projektkód testreszabása

1. Másolja a *lib* mappát a `yo swaggerize` által létrehozott *ContactList* mappába, majd lépjen a *ContactList* könyvtárába.

    ```bash
    cp -r lib/ ContactList/
    cd ContactList
    ```

2. Telepítse a `jsonpath` és a `swaggerize-ui` NPM-modulokat. 

    ```bash
    npm install --save jsonpath swaggerize-ui
    ```

3. Cserélje a *handlers/contacts.js* fájlban lévő kódot az alábbira: 
    ```javascript
    'use strict';

    var repository = require('../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.all())
        }
    };
    ```
    A kód a *lib/contactRepository.js* által kiszolgált *lib/contacts.json* fájlban lévő JSON-adatokat használja. Az új *contacts.js* kód az adattárban található valamennyi névjegyet JSON-adattartalomként adja vissza. 

4. Cserélje a **handlers/contacts/{id}.js** fájlban lévő kódot az alábbira:

    ```javascript
    'use strict';

    var repository = require('../../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.get(req.params['id']));
        }    
    };
    ```

    Ez a kód elérésiút-változó használatát teszi lehetővé annak érdekében, hogy csak az adott azonosítóval rendelkező névjegyet adja vissza.

5. Cserélje a **server.js** fájlban lévő kódot az alábbira:

    ```javascript
    'use strict';

    var port = process.env.PORT || 8000; 

    var http = require('http');
    var express = require('express');
    var bodyParser = require('body-parser');
    var swaggerize = require('swaggerize-express');
    var swaggerUi = require('swaggerize-ui'); 
    var path = require('path');

    var app = express();

    var server = http.createServer(app);

    app.use(bodyParser.json());

    app.use(swaggerize({
        api: path.resolve('./config/swagger.json'),
        handlers: path.resolve('./handlers'),
        docspath: '/swagger' 
    }));

    // change four
    app.use('/docs', swaggerUi({
        docs: '/swagger'  
    }));

    server.listen(port, function () { 
    });
    ```   

    Ez a kód kis módosításokat hajt végre az Azure App Service platformmal való használat lehetővé tétele érdekében, és interaktív webes felületet tesz közzé az API-hoz.

<a id="test-the-api-locally" class="xliff"></a>

### API tesztelése helyileg

1. A Node.js-alkalmazás indítása
    ```bash
    npm start
    ```
    
2. A http://localhost:8000/contacts címet megnyitva megtekintheti a teljes névjegylistához tartozó JSON-kimenetet.
   
   ```json
    {
        "id": 1,
        "name": "Barney Poland",
        "email": "barney@contoso.com"
    },
    {
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    },
    {
        "id": 3,
        "name": "Lora Riggs",
        "email": "lora@contoso.com"
    }
   ```

3. A http://localhost:8000/contacts/2 címet megnyitva megtekintheti a kettes számú `id`hoz tartozó névjegyet.
   
    ```json
    { 
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    }
    ```

4. Tesztelje az API-t a http://localhost:8000/docs címen lévő Swagger webes felületen keresztül.
   
    ![Swagger webes felület](media/app-service-api-nodejs-api-app/swagger-ui.png)

## <a id="createapiapp"></a> API-alkalmazás létrehozása

Ebben a szakaszban az Azure CLI 2.0 használatával hozhatja létre az API Azure App Service szolgáltatásban való üzemeltetéséhez szükséges erőforrásokat. 

1.  Jelentkezzen be az Azure-előfizetésbe az [az login](/cli/azure/#login) paranccsal, és kövesse a képernyőn látható utasításokat.

    ```azurecli-interactive
    az login
    ```

2. Ha több Azure-előfizetéssel is rendelkezik, módosítsa az alapértelmezett előfizetést a kívántra.

    ````azurecli-interactive
    az account set --subscription <name or id>
    ````

3. [!INCLUDE [Create resource group](../../includes/app-service-api-create-resource-group.md)] 

4. [!INCLUDE [Create app service plan](../../includes/app-service-api-create-app-service-plan.md)]

5. [!INCLUDE [Create API app](../../includes/app-service-api-create-api-app.md)] 


<a id="deploy-the-api-with-git" class="xliff"></a>

## API üzembe helyezése GIT segítségével

Úgy tudja telepíteni a kódot az API-alkalmazásba, hogy az Azure App Service-ben található helyi Git-tárházból beküldi a véglegesítéseket.

1. [!INCLUDE [Configure your deployment credentials](../../includes/configure-deployment-user-no-h.md)] 

2. Inicializáljon új tárházat a *ContactList* könyvtárban. 

    ```bash
    git init .
    ```

3. Zárja ki az npm által, az oktatóanyag korábbi lépésében létrehozott *node_modules* könyvtárat a Gitből. Hozzon létre új `.gitignore`-fájlt a jelenlegi könyvtárban, majd a fájl valamelyik új sorában adja hozzá a következő szöveget.

    ```
    node_modules/
    ```
    Ellenőrizze, hogy a rendszer figyelmen kívül hagyja-e a `node_modules`-mappát a `git status` állapottal.

4. Véglegesítse a tárházon végzett módosításokat.
    ```bash
    git add .
    git commit -m "initial version"
    ```

5. [!INCLUDE [Push to Azure](../../includes/app-service-api-git-push-to-azure.md)]  
 
<a id="test-the-api--in-azure" class="xliff"></a>

## Az API tesztelése az Azure-ban

1. Nyissa meg valamilyen böngészőben a http://app_name.azurewebsites.net/contacts webhelyet. Ugyanazt a visszaadott JSON-t láthatja, mint amikor az oktatóanyag egyik korábbi szakaszában helyileg leadta a kérelmet.

   ```json
   {
       "id": 1,
       "name": "Barney Poland",
       "email": "barney@contoso.com"
   },
   {
       "id": 2,
       "name": "Lacy Barrera",
       "email": "lacy@contoso.com"
   },
   {
       "id": 3,
       "name": "Lora Riggs",
       "email": "lora@contoso.com"
   }
   ```

2. Nyissa meg valamelyik böngészőben a `http://app_name.azurewebsites.net/docs` végpontot, és próbálja ki az Azure-ban futó Swagger felhasználói felületet.

    ![Swagger Ii](media/app-service-api-nodejs-api-app/swagger-azure-ui.png)

    Úgy helyezheti üzembe mostantól a minta API frissítéseit az Azure-ban, hogy a véglegesítéseket egyszerűen beküldi az Azure Git-tárházba.

<a id="clean-up" class="xliff"></a>

## A fölöslegessé vált elemek eltávolítása

Az ebben az oktatóanyagban létrehozott erőforrások törléséhez futtassa a következő Azure CLI parancsot:

```azurecli-interactive
az group delete --name myResourceGroup
```

<a id="next-step" class="xliff"></a>

## Következő lépés 
> [!div class="nextstepaction"]
> [API-alkalmazások használata JavaScript-ügyfelekkel a CORS segítségével](app-service-api-cors-consume-javascript.md)


