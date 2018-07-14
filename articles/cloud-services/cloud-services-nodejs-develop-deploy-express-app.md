---
title: Hozhat létre, és a egy Node.js Express-alkalmazás üzembe helyezése az Azure Cloud Services
description: Hozhat létre, és a node.js-ben szolgáló Express.js alkalmazás üzembe helyezése az Azure Cloud Services
services: cloud-services
documentationcenter: nodejs
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 24f8e7ef-e90d-4554-9b1e-a9b31d5824e5
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: jeconnoc
ms.openlocfilehash: ac62baae2ececf257d22f8220d472c1bcb5ee082
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009342"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Létrehozása és üzembe helyezése a Node.js-webalkalmazás az Azure Cloud Services az Express használatával

NODE.js a core Runtime funkció minimális készletét tartalmazza.
A fejlesztők gyakran használnak a 3. fél modulok további funkciókat biztosít a Node.js-alkalmazás fejlesztése során. Ebben az oktatóanyagban létrehozhat egy új alkalmazást a a [Express](https://github.com/expressjs/express) modult, amely egy MVC-keretrendszer biztosít Node.js-webalkalmazások létrehozásához.

Az elkészült alkalmazás képernyőképe nem éri el:

![A webböngészőben megjelenő Üdvözöljük, Express, az Azure-ban](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Egy Felhőszolgáltatás-projekt létrehozása
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

A következő lépésekkel hozzon létre egy új felhőszolgáltatás-projekt "expressapp" nevű:

1. Az a **Start menü** vagy **kezdőképernyőjén**, keressen **Windows PowerShell**. Végül kattintson a jobb gombbal **Windows PowerShell** válassza **Futtatás rendszergazdaként**.
   
    ![Az Azure PowerShell ikon](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Lépjen a **c:\\csomópont** könyvtár és írja be a következő parancsok futtatásával hozzon létre egy új megoldás nevű **expressapp** és a egy webes szerepkörben nevű **WebRole1**:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Alapértelmezés szerint **Add-AzureNodeWebRole** Node.js régebbi verzióját használja. A **Set-AzureServiceProjectRole** v0.10.21 csomópont használata az Azure arra utasítja a fenti nyilatkozat.  Vegye figyelembe, hogy a paraméterek nem kis-és nagybetűket.  Ellenőrizheti a Node.js megfelelő verzióját úgy lett kiválasztva a **motorok** tulajdonság **WebRole1\package.json**.
    > 
    > 

## <a name="install-express"></a>Az Express telepítése
1. Az Express generátor telepítése a következő parancsot:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    Az npm parancs kimenete az alábbi eredményt hasonlóan kell kinéznie. 
   
    ![Az npm-et, a kimenet megjelenítése a Windows PowerShell telepítési expressz parancs.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Lépjen a **WebRole1** könyvtárra, és egy új alkalmazás készítése az expressz parancsot használja:
   
        PS C:\node\expressapp\WebRole1> express
   
    A rendszer bekéri a korábbi alkalmazás felülírása. Adja meg **y** vagy **Igen** folytatásához. Express hoz létre az app.js fájlban és a egy mappaszerkezet az alkalmazása létrehozásához.
   
    ![Az expressz parancs kimenete](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. A package.json fájlban meghatározott további függőségek telepítéséhez, adja meg a következő parancsot:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![A kimenete az npm install parancs](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. A következő parancs használatával másolja a **bin/www** fájlt **server.js**. Erre azért van így, hogy a felhőszolgáltatás a belépési pont talál ehhez az alkalmazáshoz.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   Ez a parancs befejeződött, rendelkeznie kell egy **server.js** fájl WebRole1 a címtárban.
5. Módosítsa a **server.js** , távolítsa el az egyik a "." karaktereket a következő sort.
   
       var app = require('../app');
   
   Miután kiválasztotta ezt a módosítást, a sorban kell a következőképpen jelenik meg.
   
       var app = require('./app');
   
   Ez a módosítás szükség, óta az egekbe szökött a fájl (korábbi nevén **bin/www**,), az éppen szükséges fájl ugyanabba a könyvtárba. A módosítás elvégzése után mentse a **server.js** fájlt.
6. A következő paranccsal futtathatja az alkalmazást az Azure emulatorban:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Üdvözli az express tartalmazó webhelyet.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>A nézet módosítása
Most módosítsa a nézet a "Üdvözöljük, Express az Azure-ban" üzenet.

1. Adja meg az index.jade fájlban nyissa meg a következő parancsot:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![Az index.jade fájl tartalmát.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade az Express-alkalmazások által használt alapértelmezett megjelenítési motort. A Jade megjelenítési motort további információkért lásd: [ http://jade-lang.com ] [ http://jade-lang.com].
2. Módosíthatja a szöveg utolsó sora hozzáfűzésével **az Azure-ban**.
   
   ![Az index.jade fájlban, az utolsó sort olvas: p üdvözli \#{title} az Azure-ban](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Mentse a fájlt, és lépjen ki a Jegyzettömbből.
4. Frissítse a böngészőt, és látni fogja a módosításokat.
   
   ![Egy böngészőablakban, a lap tartalmazza Üdvözli az Express, az Azure-ban](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Az alkalmazás tesztelése után használja a **Stop-AzureEmulator** parancsmag használatával állítsa le az emulátorban.

## <a name="publishing-the-application-to-azure"></a>Az Azure-ban az alkalmazás közzététele
Az Azure PowerShell-ablakban, használja a **Publish-AzureServiceProject** parancsmagot, hogy a cloud service-alkalmazás üzembe helyezése

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

A központi telepítési művelet befejezése után a böngészőben nyissa meg, és megjeleníteni a weblapot.

![A webböngészőben megjelenő az Express oldal. Az URL-cím azt jelzi, hogy most már üzemel az Azure-ban.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>További lépések
További információk: [Node.js fejlesztői központ](https://docs.microsoft.com/en-us/javascript/azure/?view=azure-node-latest).

[Node.js Web Application]: http://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: http://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com


