---
title: Node.js Express alkalmazás létrehozása és üzembe helyezése az Azure Cloud Services szolgáltatásban
titleSuffix: Azure Cloud Services
description: Express.js alkalmazás létrehozása és üzembe helyezése a Node.js alkalmazásban az Azure Cloud Services szolgáltatásban
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.openlocfilehash: 79a998930a384420b22add8825ee4b2269eb4539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360752"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Node.js webalkalmazás létrehozása és üzembe helyezése az Express használatával egy Azure Felhőalapú szolgáltatáson

A Node.js minimális funkciókészletet tartalmaz a központi futásidőben.
A fejlesztők gyakran használnak külső modulokat, hogy további funkciókat biztosítsanak a Node.js alkalmazások fejlesztése során. Ebben a tutorial hoz létre egy új alkalmazást az [Express](https://github.com/expressjs/express) modul, amely egy MVC keretlétrehozása Node.js webes alkalmazások.

A kitöltött alkalmazás ról képernyőkép az alábbi:

![Az Üdvözli az Expressz az Azure-ban böngésző](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Felhőszolgáltatási projekt létrehozása
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Hajtsa végre az alábbi lépéseket egy "expressapp" nevű új felhőszolgáltatási projekt létrehozásához:

1. A **Start menüben** vagy a **Kezdőképernyőn**keresse meg a **Windows PowerShell parancsot.** Végül kattintson a jobb gombbal a **Windows PowerShell** elemre, és válassza a **Futtatás rendszergazdaként parancsot.**
   
    ![Az Azure PowerShell ikonja](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Módosítsa a könyvtárakat a **c:\\csomópont** könyvtárra, majd írja be a következő parancsokat egy **expressapp** nevű új megoldás és egy **WebRole1**nevű webes szerepkör létrehozásához:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Alapértelmezés szerint **az Add-AzureNodeWebRole** a Node.js egy régebbi verzióját használja. A **set-AzureServiceProjectRole** fenti utasítás arra utasítja az Azure-t, hogy használja a csomópont 0.10.21-es számú használatát.  Figyelje meg, hogy a paraméterek ben a kis- és nagybetűk et nem veszi figyelembe.  A **WebRole1\package.json**mappában található **engines** tulajdonság ellenőrzésével ellenőrizheti, hogy a Node.js megfelelő verzióját választotta-e ki.
    > 
    > 

## <a name="install-express"></a>Az Express telepítése
1. Telepítse az Express generátort a következő parancs kiadásával:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    Az npm parancs kimenetének az alábbi eredményhez hasonlóan kell kinéznie. 
   
    ![Az npm install express parancs kimenetét megjelenítő Windows PowerShell.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Módosítsa a könyvtárakat a **WebRole1** könyvtárra, és az expressz paranccsal hozzon létre egy új alkalmazást:
   
        PS C:\node\expressapp\WebRole1> express
   
    A rendszer kéri, hogy írja felül a korábbi alkalmazást. A folytatáshoz írja be **az y** vagy **az yes** értéket. Az Express létrehozza az app.js fájlt és egy mappastruktúrát az alkalmazás létrehozásához.
   
    ![Az expressz parancs kimenete](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. A package.json fájlban definiált további függőségek telepítéséhez írja be a következő parancsot:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![Az npm install parancs kimenete](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. A **bin/www** fájl **kiszolgáló.js**fájlba másolásához használja a következő parancsot. Ez azért van így, hogy a felhőszolgáltatás megtalálja az alkalmazás belépési pontját.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   A parancs befejezése után a WebRole1 könyvtárban egy **server.js** fájlt kell beépíteni.
5. Módosítsa a **server.js parancsot** úgy, hogy a következő sorból eltávolítsa a ".' karakterek egyikét.
   
       var app = require('../app');
   
   A módosítás elvégzése után a sor nak a következőképpen kell megjelennie.
   
       var app = require('./app');
   
   Ez a módosítás szükséges, mivel a fájlt **(korábban bin/www**,) áthelyeztük ugyanabba a könyvtárba, mint az alkalmazásfájl. A módosítás után mentse a **server.js** fájlt.
6. Az alkalmazás azure-emulátorban való futtatásához használja a következő parancsot:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Egy weboldal, amely tartalmazza, hogy kifejezzék.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>A nézet módosítása
Most módosítsa a nézetet, hogy megjelenjen a "Welcome to Express az Azure-ban" üzenet.

1. Az index.jade fájl megnyitásához írja be a következő parancsot:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![Az index.jade fájl tartalma.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   A Jade az Express alkalmazások által használt alapértelmezett nézetmotor. A Jade nézetmotorról további [http://jade-lang.com][http://jade-lang.com]információt a .
2. Az utolsó szövegsor módosítása az **Azure-ban**való hozzáfűzéssel.
   
   ![Az index.jade fájl, az utolsó sor \#így szól: p Üdvözöljük a(z) {title} az Azure-ban](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Mentse a fájlt, és lépjen ki a Jegyzettömbből.
4. Frissítse a böngészőt, és látni fogja a módosításokat.
   
   ![Böngészőablak, a lap tartalmazza a Welcome to Express in Azure-t](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Az alkalmazás tesztelése után a **Stop-AzureEmulator** parancsmag segítségével állítsa le az emulátor.

## <a name="publishing-the-application-to-azure"></a>Az alkalmazás közzététele az Azure-ban
Az Azure PowerShell ablakban a **Publish-AzureServiceProject** parancsmag segítségével telepítse az alkalmazást egy felhőszolgáltatásba

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

A telepítési művelet befejezése után a böngésző megnyílik, és megjeleníti a weboldalt.

![Az Expressz lapot megjelenítő webböngésző. Az URL-cím azt jelzi, hogy most már az Azure-ban található.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>További lépések
További információk: [Node.js fejlesztői központ](https://docs.microsoft.com/azure/javascript/).

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com





