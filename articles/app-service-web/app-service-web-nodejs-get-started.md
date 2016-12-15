---
title: "Ismerkedés a Node.js webalkalmazásokkal az Azure App Service-ben"
description: "Megtudhatja, hogyan telepíthet egy Node.js alkalmazást az Azure App Service-ben."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: wpickett
editor: 
ms.assetid: fb2b90c8-02b6-4700-929b-5de9a35d67cc
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: get-started-article
ms.date: 07/01/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 2050bda9c1a4390232d32370863e8d6a62ed5c2b
ms.openlocfilehash: 66f1a0987960c9251922f1d22ed647d10bb0d10e


---
# <a name="get-started-with-nodejs-web-apps-in-azure-app-service"></a>Ismerkedés a Node.js webalkalmazásokkal az Azure App Service-ben
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Az oktatóanyag bemutatja, hogy hogyan hozhat létre egy egyszerű [Node.js] alkalmazást, és hogyan telepítheti azt az [Azure App Service] olyan parancssori környezetben, mint a cmd.exe vagy a bash. Az oktatóanyag utasításai követhetők bármelyik olyan operációs rendszeren, amely képes a Node.js futtatására.

> [!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]
> 
> 

<a name="prereq"></a>

## <a name="prerequisites"></a>Előfeltételek
* [Node.js]
* [Bower]
* [Yeoman]
* [Git]
* [Azure CLI]
* Egy Microsoft Azure-fiók. Ha nincs fiókja, [regisztráljon egy ingyenes próbaverzióra], vagy [aktiválhatja a Visual Studio előfizetői előnyeit].

## <a name="create-and-deploy-a-simple-nodejs-web-app"></a>Egyszerű Node.js webalkalmazás létrehozása és telepítése
1. Nyissa meg a kívánt parancssori terminált, és telepítse a [Express generator for Yeoman]
   
        npm install -g generator-express
2. `CD` egy működő könyvtárba, és hozzon létre egy Express alkalmazást az alábbi szintaxissal:
   
        yo express
   
    Ha a rendszer felszólítja, válassza az alábbi beállításokat.  
   
    `? Would you like to create a new directory for your project?` **Igen**  
    `? Enter directory name` **{alkalmazásnév}**  
    `? Select a version to install:` **MVC**  
    `? Select a view engine to use:` **Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):` **Nincs**  
    `? Select a database to use:` **Nincs**  
    `? Select a build tool to use:` **Grunt**
3. `CD` az új alkalmazás gyökérkönyvtárára mutatva, majd indítsa el, hogy meggyőződjön arról, hogy fut a fejlesztési környezetben:
   
        npm start
   
    A böngészőben navigáljon a <http://localhost:3000> helyre, hogy meggyőződjön arról, hogy látja az Express kezdőlapot. Ha ellenőrizte, hogy az alkalmazás megfelelően fut-e, állítsa le a `Ctrl-C` segítségével.
4. Váltson ASM módra, és jelentkezzen be az Azure-ba (ehhez az [Azure CLI szükséges](#prereq)):
   
        azure config mode asm
        azure login
   
    A felszólítást követve folytassa a böngészőbe való bejelentkezést egy Azure-előfizetéssel rendelkező Microsoft-fiókkal.
5. Győződjön meg arról, hogy még az alkalmazás gyökérkönyvtárában van, majd hozza létre az App Service alkalmazás-erőforrást az Azure-ban egy egyedi alkalmazásnévvel a következő parancsot használva. Például: http://{appname}.azurewebsites.net
   
        azure site create --git {appname}
   
    A felszólítást követve válasszon egy Azure-régiót, ahová telepíteni szeretné. Ha még nem állított be Git/FTP telepítési hitelesítő adatokat az Azure-előfizetéséhez, a rendszer erre is felszólítja.
6. Nyissa meg a ./config/config fájlt az alkalmazás gyökeréből, és módosítsa az éles portot a `process.env.port` értékre; a `config` objektum `production` tulajdonságának pedig az alábbi példához hasonlóan kell kinéznie:
   
        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }
   
    Ezáltal a Node.js alkalmazás válaszolhat a webkérelmekre az alapértelmezett porton, amelyet az is figyel.
7. Nyissa meg a ./package.json fájlt, és adja hozzá az `engines` tulajdonságot [a kívánt Node.js verzió megadásához](#version).
   
        "engines": {
            "node": "6.6.0"
        }, 
8. Mentse el a módosításokat, majd a git segítségével telepítse az alkalmazást az Azure-ban:
   
        git add .
        git add -f config
        git commit -m "{your commit message}"
        git push azure master
   
    Az Express generátor már biztosít egy .gitignore fájlt, így a `git push` nem használja a sávszélességet a node_modules/ könyvtár feltöltésekor.
9. Végezetül indítsa el az élő Azure alkalmazást a böngészőben:
   
        azure site browse
   
    Ekkor elméletben a Node.js alkalmazásának élőben futnia kell az Azure App Service-ben.
   
    ![Példa a telepített alkalmazás tallózással való megkeresésére.][deployed-express-app]

## <a name="update-your-nodejs-web-app"></a>A Node.js webalkalmazás frissítése
Az App Service-ben futó Node.js webalkalmazás frissítéséhez csak futtassa a `git add`, `git commit` és `git push` parancsokat, mint ahogyan azt a webalkalmazás első telepítésekor tette.

## <a name="how-app-service-deploys-your-nodejs-app"></a>Hogyan telepíti az App Service a Node.js alkalmazást
Az Azure App Service az [iisnode] segítségével futtatja a Node.js alkalmazásokat. Az Azure CLI és a Kudu motor (Git telepítés) egymással együttműködve biztosítanak egy zökkenőmentes élményt a Node.js alkalmazások parancssori felületről való fejlesztésekor és telepítésekor. 

* `azure site create --git` felismeri a server.js vagy az app.js fájl közös Node.js mintázatát, és létrehoz egy iisnode.yml fájlt a gyökérkönyvtárban. Ezt a fájlt használhatja az iisnode testreszabásához.
* A `git push azure master` esetében a Kudu automatizálja az alábbi telepítési feladatokat:
  
  * Ha a package.json az adattár gyökérkönyvtárában van, futtassa az `npm install --production` parancsot.
  * Egy Web.config létrehozása az iisnode-hoz, amely az indítási parancsfájlra mutat a package.json kódjában (pl. server.js vagy app.js).
  * A Web.config testreszabása, hogy az alkalmazás készen álljon a Node-Inspectorral való hibakeresésre.

## <a name="use-a-nodejs-framework"></a>Egy Node.js keretrendszer használata
Ha az alkalmazásfejlesztéshez egy népszerű Node.js keretrendszert használ, mint a [Sails.js][SAILSJS] vagy a [MEAN.js][MEANJS], telepítheti ezeket az App Service-be. A népszerű Node.js keretrendszereknek megvannak saját jellegzetességei, és a csomagfüggőségeik folyamatosan frissülnek. Az App Service azonban elérhetővé teszi az stdout és az stderr naplókat, így pontosan tudhatja, hogy mi történik az alkalmazásával, és annak megfelelően végezhet módosításokat. További információk: [Az stdout és az stderr naplók lekérése az iisnode-ról](#iisnodelog).

Az alábbi oktatóanyagok bemutatják, hogyan használhat egy adott keretrendszert az App Service-ben:

* [Sails.js webalkalmazás telepítése az Azure App Service-be]
* [Node.js csevegőalkalmazás létrehozása a Socket.IO segítségével az Azure App Service-ben]
* [Az io.js használata az Azure App Service Web Apps szolgáltatással]

<a name="version"></a>

## <a name="use-a-specific-nodejs-engine"></a>Adott Node.js motor használata
Az általános munkamenetben úgy írhatja elő az App Service-nek egy adott Node.js motor használatát, ahogyan azt egy package.json esetén tenné.
Példa:

    "engines": {
        "node": "6.6.0"
    }, 

A Kudu telepítési motor az alábbi lépések szerint határozza meg, melyik Node.js motort használja:

* Először nézze meg az iisnode.yml fájlt, hogy a `nodeProcessCommandLine` meg van-e határozva. Ha igen, akkor használja azt.
* Ezután nézze meg a package.js fájlt, hogy a `"node": "..."` meg van-e határozva az `engines` objektumban. Ha igen, akkor használja azt.
* Válasszon egy alapértelmezett Node.js verziót.

> [!NOTE]
> Ajánlott explicit módon meghatározni a kívánt Node.js motort. Az alapértelmezett Node.js verzió módosulhat, és hibaüzenetek jelenhetnek meg az Azure-webalkalmazásban, mert az alapértelmezett Node.js verzió nem megfelelő az alkalmazás számára.
> 
> 

<a name="iisnodelog"></a>

## <a name="get-stdout-and-stderr-logs-from-iisnode"></a>Az stdout és az stderr naplók lekérése az iisnode-ról
Az iisnode naplók olvasásához kövesse az alábbi lépéseket.

> [!NOTE]
> A lépések elvégzése után előfordulhat, hogy a naplófájlok nem léteznek, amíg hiba nem történik.
> 
> 

1. Nyissa meg az Azure CLI által biztosított iisnode.yml fájlt.
2. Adja még a következő két paramétert: 
   
        loggingEnabled: true
        logDirectory: iisnode
   
    Együtt közlik az iisnode felé az App Service-ben, hogy az stdout és az stderror kimenetet a D:\home\site\wwwroot\**iisnode** könyvtárba helyezze.
3. Mentse el a módosításokat, majd továbbítsa azokat az Azure-ba az alábbi Git-parancsokkal:
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    Az iisnode konfigurálása ekkor kész. A következő lépésekből megtudhatja, hogyan érheti el ezeket a naplókat.
4. A böngészőjében nyissa meg a Kudu hibakereső konzolt az alkalmazásához. Ez a következő helyen található:
   
        https://{appname}.scm.azurewebsites.net/DebugConsole 
   
    Ez az URL-cím eltér a webalkalmazás URL-címétől abban, hogy hozzá lett fűzve egy ” *.scm.* ” tag a DNS-névhez. Ha kihagyja ezt a kiegészítést az URL-hez, 404 hibaüzenetet kap.
5. Lépjen a D:\home\site\wwwroot\iisnode helyre
   
    ![Navigálás az iisnode naplófájlok helyéhez.][iislog-kudu-console-find]
6. Kattintson az olvasni kívánt napló **Szerkesztés** ikonjára. Kattinthat a **Letöltés** vagy a **Törlés** lehetőségre is, ha szeretne.
   
    ![Egy iisnode naplófájl megnyitása.][iislog-kudu-console-open]
   
    Most megjelenik a napló, amely segít az App Service üzemelő példányon végzett hibakeresésben.
   
    ![Egy iisnode naplófájl vizsgálata.][iislog-kudu-console-read]

## <a name="debug-your-app-with-node-inspector"></a>Alkalmazás hibakeresése a Node-Inspector használatával
Ha a Node-Inspector segítségével szeretne hibakeresést végezni a Node.js alkalmazásokon, használhatja azt az élő App Service alkalmazáshoz. A Node-Inspector előre telepítve van az App Service iisnode telepítésében. Ha a Git segítségével végzi a telepítést, a Kudu automatikusan generált Web.config eleme már tartalmazza a Node-Inspector engedélyezéséhez szükséges összes konfigurációt.

A Node-Inspector engedélyezéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg az iisnode.yml fájlt az adattár gyökérkönyvtárában, és adja meg a következő paramétereket: 
   
        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll
2. Mentse el a módosításokat, majd továbbítsa azokat az Azure-ba az alábbi Git-parancsokkal:
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
3. Ekkor egyszerűen lépjen az alkalmazás indító fájljához, ahogy az a package.json indító parancsfájljában meg van határozva. Ehhez adja a /debug kiegészítést az URL-címhez. Például:
   
        http://{appname}.azurewebsites.net/server.js/debug
   
    Vagy
   
        http://{appname}.azurewebsites.net/app.js/debug

## <a name="more-resources"></a>További erőforrások
* [A Node.js verzió megadása az Azure alkalmazásban](../nodejs-specify-node-version-azure-apps.md)
* [Ajánlott eljárások és hibaelhárítási útmutató az Azure-on futó Node.js-alkalmazásokhoz](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
* [A Node.js webalkalmazás hibakeresése az Azure App Service-ben](web-sites-nodejs-debug.md)
* [A Node.js modulok használata az Azure alkalmazásokkal](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
* [Node.js fejlesztői központ](/develop/nodejs/)
* [Ismerkedés a webalkalmazásokkal az Azure App Service-ben](app-service-web-get-started.md)
* [A Szupertitkos Kudu hibakereső konzol felfedezése]

<!-- URL List -->

[Azure CLI]: ../xplat-cli-install.md
[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[aktiválhatja a Visual Studio előfizetői előnyeit]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[Node.js csevegőalkalmazás létrehozása a Socket.IO segítségével az Azure App Service-ben]: ./web-sites-nodejs-chat-app-socketio.md
[Sails.js webalkalmazás telepítése az Azure App Service-be]: ./app-service-web-nodejs-sails.md
[A Szupertitkos Kudu hibakereső konzol felfedezése]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[Express generator for Yeoman]: https://github.com/petecoop/generator-express
[Git]: http://www.git-scm.com/downloads
[Az io.js használata az Azure App Service Web Apps szolgáltatással]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node.js]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[regisztráljon egy ingyenes próbaverzióra]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png



<!--HONumber=Dec16_HO1-->


