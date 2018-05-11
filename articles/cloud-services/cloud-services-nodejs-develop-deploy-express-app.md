---
title: Hozza létre, és a Node.js Express-alkalmazás telepítése Azure Cloud Services csomag
description: Hozza létre, és a node.js Express.js alkalmazást központilag az Azure Cloud Services csomag
services: cloud-services
documentationcenter: nodejs
author: thraka
manager: timlt
editor: ''
ms.assetid: 24f8e7ef-e90d-4554-9b1e-a9b31d5824e5
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: adegeo
ms.openlocfilehash: 8bb78330591b0557b036a161eb83e47b41a8fd43
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Hozza létre és Express használata az Azure Felhőszolgáltatások a Node.js-webalkalmazás telepítése

NODE.js a core runtime a legszükségesebb funkciókat tartalmazza.
A fejlesztők gyakran használnak a 3. fél modulok nyújt további szolgáltatásokat, a Node.js-alkalmazások fejlesztése során. Ebben az oktatóanyagban létrehoz egy új alkalmazás használja a [Express](https://github.com/expressjs/express) modult, amely egy MVC keretrendszert biztosít a Node.js webalkalmazás létrehozásához.

A kész alkalmazás képernyőfelvételének alatt van:

![A webböngészőben Üdvözli az Express, az Azure-ban](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Egy Felhőszolgáltatás-projekt létrehozása
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Hajtsa végre a következő lépésekkel hozza létre egy új felhőszolgáltatás-projekt "expressapp" nevű:

1. Az a **Start menü** vagy **kezdőképernyőn**, keressen **Windows PowerShell**. Végül kattintson a jobb gombbal **Windows PowerShell** válassza **Futtatás rendszergazdaként**.
   
    ![Az Azure PowerShell ikon](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Lépjen a **c:\\csomópont** könyvtár és írja be a következő parancsok futtatásával hozzon létre egy új megoldás nevű **expressapp** és a webes szerepkör nevű **WebRole1**:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Alapértelmezés szerint **Add-AzureNodeWebRole** Node.js régebbi verzióját használja. A **Set-AzureServiceProjectRole** fenti utasítás arra utasítja a csomópont v0.10.21 használatához Azure.  Vegye figyelembe, hogy a paraméter-és nagybetűk.  Node.js megfelelő verziója van beállítva ellenőrzésével ellenőrizheti a **motorok** tulajdonság **WebRole1\package.json**.
    > 
    > 

## <a name="install-express"></a>Express telepítése
1. Az Express generátor telepítése a következő parancsot:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    Az alábbi eredmény kell hasonlítania az npm parancs kimenetét. 
   
    ![Az npm kimenetének megjelenítése a Windows PowerShell telepítési expressz parancs.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Lépjen a **WebRole1** könyvtárra, és használja a kifejezett parancsot egy új alkalmazás létrehozásához:
   
        PS C:\node\expressapp\WebRole1> express
   
    Kérni fogja a korábbi alkalmazás felülírását. Adja meg **y** vagy **Igen** folytatja. Express az app.js fájl és az alkalmazás felépítése mappaszerkezetét hoz létre.
   
    ![Az expressz parancs](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. A package.json fájlban meghatározott további függőségek telepítéséhez írja be a következő parancsot:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![Az npm kimenete telepítési parancs](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Másolja az alábbi parancs segítségével a **bin/www** fájl **server.js**. Ez a helyzet a felhőalapú szolgáltatás található a belépési pont ehhez az alkalmazáshoz.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   Ez a parancs után rendelkeznie kell egy **server.js** a WebRole1 fájl.
5. Módosítsa a **server.js** egyik eltávolításához a "." karaktert tartalmaz a következő sort.
   
       var app = require('../app');
   
   Miután kiválasztotta ezt a módosítást, a sor meg kell jelennie az alábbiak szerint.
   
       var app = require('./app');
   
   Ez a változás szükség, mert a fájl helyeztük (korábbi nevén **bin/www**,) a szükséges alkalmazásfájl könyvtárába. A módosítás elvégzése után mentse a **server.js** fájlt.
6. A következő paranccsal futtassa az alkalmazást az Azure emulátorban:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Üdvözli a express tartalmazó webhelyet.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>A nézet módosítása
Most módosítsa úgy a nézetet, az üzenet "Üdvözli az Express az Azure-bA" megjelenítéséhez.

1. Adja meg a index.jade fájl a következő parancsot:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![A index.jade fájl tartalmát.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade Express alkalmazások által használt alapértelmezett megjelenítési motort. A Jade nézet motoron további információkért lásd: [ http://jade-lang.com ] [ http://jade-lang.com].
2. Módosítsa a szöveg utolsó sora hozzáfűzésével **az Azure-ban**.
   
   ![A index.jade fájl utolsó sora beolvassa: p Üdvözöljük \#{title} az Azure-ban](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Mentse a fájlt, és lépjen ki a Jegyzettömbből.
4. Frissítse a böngészőt, és látni fogja a módosításokat.
   
   ![Egy böngészőablakban, a lap tartalmazza-üdvözli a Express, az Azure-ban](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Miután az alkalmazás tesztelése, használja a **Stop-AzureEmulator** parancsmagot, hogy az emulátor leállítása.

## <a name="publishing-the-application-to-azure"></a>Az Azure-bA az alkalmazás közzététele
Az Azure PowerShell-ablakban, használja a **Publish-AzureServiceProject** parancsmagot, hogy az alkalmazás egy felhőszolgáltatásra történő központi telepítése

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Miután a telepítési művelet befejeződik, a böngészőben nyissa meg, és megjeleníteni a weblapot.

![A webböngészőben az Express oldalon. Az URL-cím azt jelzi, hogy most már futó Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>További lépések
További információk: [Node.js fejlesztői központ](/develop/nodejs/).

[Node.js Web Application]: http://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: http://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com


