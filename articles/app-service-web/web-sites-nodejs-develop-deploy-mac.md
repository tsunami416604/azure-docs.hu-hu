---
title: "Node.js-webalkalmazás létrehozása az Azure App Service-ben | Microsoft Docs"
description: "Megtudhatja, hogyan helyezhet üzembe egy Node.js-alkalmazást az Azure App Service webalkalmazására."
services: app-service\web
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 85af23df-54af-4430-8d77-a1f97e2f5b10
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 4b8eb863a1fcb65c307870619ea9d8942c57a906


---
# <a name="create-a-nodejs-web-app-in-azure-app-service"></a>Node.js-webalkalmazás létrehozása az Azure App Service-ben
> [!div class="op_single_selector"]
> * [.Net](web-sites-dotnet-get-started.md)
> * [Node.js](web-sites-nodejs-develop-deploy-mac.md)
> * [Java](web-sites-java-get-started.md)
> * [PHP – Git](web-sites-php-mysql-deploy-use-git.md)
> * [PHP – FTP](web-sites-php-mysql-deploy-use-ftp.md)
> * [Python](web-sites-python-ptvs-django-mysql.md)
> 
> 

Az oktatóanyag bemutatja, hogyan hozhat létre egyszerű [Node.js](http://nodejs.org)-alkalmazást, és hogyan helyezheti azt üzembe azt egy [webalkalmazásra](app-service-web-overview.md) az [Azure App Service-ben](../app-service/app-service-value-prop-what-is.md) a [Git](http://git-scm.com) használatával. Az oktatóanyag utasításai követhetők bármely olyan operációs rendszeren, amely alkalmas a Node.js futtatására.

Az oktatóanyagból a következőket sajátíthatja el:

* Megtudhatja, hogyan hozhat létre egy webalkalmazást az Azure App Service-ben az Azure portál használatával.
* Megtudhatja, hogyan helyezhet üzembe egy Node.js-alkalmazást a webalkalmazásra a webalkalmazás Git-tárházába történő küldéssel.

A kész alkalmazás egy rövid „hello world” karakterláncot jelenít meg a böngészőben.

![A böngészőben megjelenő „Hello World” üzenet.][helloworld-completed]

Az összetettebb Node.js-alkalmazások oktatóanyagait és mintakódját, valamint a Node.js-nek az Azure felületén történő használatával kapcsolatos további információkat a következő témakörben talál: [Node.js fejlesztői központ](/develop/nodejs/).

> [!NOTE]
> Az oktatóanyag elvégzéséhez Microsoft Azure-fiókra lesz szüksége. Ha nincs fiókja, [aktiválhatja a Visual Studio előfizetői előnyeit](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), vagy [regisztráljon egy ingyenes próbaverzióra](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).
> 
> Ha nem szeretne regisztrálni Azure-fiókot az Azure App Service megismerése előtt, menjen [Az App Service kipróbálása](https://azure.microsoft.com/try/app-service/) oldalra. Itt azonnal létrehozhat egy rövid élettartamú alapszintű webalkalmazást az App Service-ben. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

## <a name="create-a-web-app-and-enable-git-publishing"></a>Webalkalmazás létrehozása és a Git-közzététel engedélyezése
Hajtsa végre a következő lépéseket webalkalmazás létrehozásához az Azure App Service-ben és a Git-közzététel engedélyezéséhez. 

A [Git](http://git-scm.com/) egy olyan elosztott verziókezelő rendszer, amely Azure-webhelyének üzembe helyezésére használható. A webalkalmazásához írt kódot egy helyi Git-tárházban fogja tárolni, és a kódot az Azure-on a kód egy távoli tárházba történő küldésével fogja üzembe helyezni. Ez az üzembe helyezési módszer az App Service Web Apps szolgáltatása.  

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Kattintson az Azure portál bal felső részén található **+ NEW** (+ ÚJ) ikonra.
3. Kattintson a **Web + Mobile** (Web + mobil), majd a **Web app** (Webalkalmazás) elemre.
   
    ![][portal-quick-create]
4. A **Webalkalmazás** mezőben adjon meg egy nevet a webalkalmazás számára.
   
    Ennek a névnek egyedinek kell lennie az azurewebsites.net tartományban, mert a webalkalmazás URL-címe {név}.azurewebsites.net lesz. Ha a megadott név nem egyedi, egy piros felkiáltójel jelenik meg a szövegmezőben.
5. Válasszon ki egy **előfizetést**.
6. Válasszon egy **erőforráscsoportot**, vagy hozzon létre egy újat.
   
    További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md).
7. Válasszon ki egy **App Service-csomagot/-helyet**, vagy hozzon létre egy újat.
   
    További információk az App Service-csomagokról: [Az Azure App Service-csomagok áttekintése](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)
8. Kattintson a **Létrehozás** gombra.
   
    ![][portal-quick-create2]
   
    Az Azure kis időn belül, általában kevesebb mint egy perc alatt létrehozza az új webalkalmazást.
9. Kattintson a következőre: **Web apps (Webalkalmazások) > {az Ön webalkalmazása}**.
   
    ![](./media/web-sites-nodejs-develop-deploy-mac/gotowebapp.png)
10. A **Web app** (Webalkalmazás) panelen kattintson a **Deployment** (Üzemelő példány) részre.
    
    ![][deployment-part]
11. A **Continuous Deployment** (Folyamatos üzembe helyezés) panelen kattintson a **Choose Source** (Forrás választása) lehetőségre
12. Kattintson a **Local Git Repository** (Helyi git-tárház) elemre, majd az **OK** gombra.
    
    ![][setup-git-publishing]
13. Ha még nem tette meg, állítsa be az üzemelő példány hitelesítő adatait.
    
    a. A Web app (Webalkalmazás) panelen kattintson a **Settings (Beállítások) > Deployment credentials (Telepítési hitelesítő adatok)** lehetőségre.
    
    ![][deployment-credentials]
    
    b. Hozzon létre felhasználónevet és jelszót. 
    
    ![](./media/web-sites-nodejs-develop-deploy-mac/setdeploycreds.png)
14. A Web app (Webalkalmazás) panelen kattintson a **Settings** (Beállítások), majd a **Properties** (Tulajdonságok) elemre.
    
    A közzétételhez egy távoli Git-tárházba való küldés szükséges. A tárházhoz tartozó URL-cím a **GIT URL** (GIT URL-címe) területen van felsorolva. Ezt az URL-címet az oktatóanyag későbbi részében fogja használni.
    
    ![][git-url]

## <a name="build-and-test-your-application-locally"></a>Az alkalmazás helyi összeállítása és tesztelése
Ebben a szakaszban létre fogja hozni a **server.js** fájlt, amely a [nodejs.org] „Hello World” példájának kis mértékben módosított verzióját tartalmazza. A kód az Azure-webalkalmazás futtatásakor figyelő portként a process.env.PORT portot veszi fel.

1. Hozzon létre egy könyvtárat *helloworld* néven.
2. Szövegszerkesztő használatával hozzon létre egy **server.js** nevű új fájlt a *helloworld* könyvtárban.
3. Másolja át az alábbi kódot a **server.js** fájlba, majd mentse a fájlt:
   
        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);
4. Nyissa meg a parancssort, majd az alábbi parancs használatával indítsa el a webalkalmazást helyileg.
   
        node server.js
5. Nyissa meg a webböngészőt, majd navigáljon a következő helyre: http://localhost:1337. 
   
    A következő képernyőfelvételen látható módon megjelenik egy weblap, amelyen a „Hello World” szöveg szerepel.
   
    ![A böngészőben megjelenő „Hello World” üzenet.][helloworld-localhost]

## <a name="publish-your-application"></a>Az alkalmazás közzététele
1. Ha még nem tette meg, telepítse a Git alkalmazást.
   
    Az Ön által használt platformra érvényes telepítési utasításokat lásd a [Git letöltési oldalán](http://git-scm.com/download).
2. A parancssorból lépjen a **helloworld** könyvtárra, majd adja meg a következő parancsot a helyi Git-tárház indításához.
   
        git init
3. Fájlok a tárházhoz adásához használja a következő parancsokat:
   
        git add .
        git commit -m "initial commit"
4. Az előzőleg létrehozott webalkalmazás-frissítések küldéséhez az alábbi paranccsal adjon hozzá egy távoli Git-tárházat:
   
        git remote add azure [URL for remote repository]
5. A következő parancs használatával továbbítsa a módosításokat az Azure-ba:
   
        git push azure master
   
    A rendszer rákérdez az előzőleg létrehozott jelszóra. A kimenet a következő példához hasonló.
   
        Counting objects: 3, done.
        Delta compression using up to 8 threads.
        Compressing objects: 100% (2/2), done.
        Writing objects: 100% (3/3), 374 bytes, done.
        Total 3 (delta 0), reused 0 (delta 0)
        remote: New deployment received.
        remote: Updating branch 'master'.
        remote: Preparing deployment for commit id '5ebbe250c9'.
        remote: Preparing files for deployment.
        remote: Deploying Web.config to enable Node.js activation.
        remote: Deployment successful.
        To https://user@testsite.scm.azurewebsites.net/testsite.git
         * [new branch]      master -> master
6. Az alkalmazás megtekintéséhez az Azure portál **Web App** (Webalkalmazás) részén kattintson a **Browse** (Tallózás) gombra.
   
    ![Tallózás gomb](./media/web-sites-nodejs-develop-deploy-mac/browsebutton.png)
   
    ![Hello world az Azure-ban](./media/web-sites-nodejs-develop-deploy-mac/helloworldazure.png)

## <a name="publish-changes-to-your-application"></a>Alkalmazásmódosítások közzététele
1. Egy szövegszerkesztőben nyissa meg a **server.js** fájlt, majd a „Hello World\n” szöveget módosítsa a következőre: „Hello Azure\n”. 
2. Mentse a fájlt.
3. A parancssorból lépjen a **helloworld** könyvtárba, majd futtassa a következő parancsokat:
   
        git add .
        git commit -m "changing to hello azure"
        git push azure master
   
    A rendszer újra rákérdez a jelszavára.
4. Frissítse azt a böngészőablakot, amelybe a webalkalmazás URL-címét beírta.
   
    ![A „Hello Azure” szöveget megjelenítő weblap][helloworld-completed]

## <a name="roll-back-a-deployment"></a>Üzemelő példány visszaállítása
A **Web app** (Webalkalmazás) panelen kattintson a **Settings (Beállítások) > Continuous Deployment (Folyamatos üzembe helyezés)** elemre az üzemelő példány előzményeinek a **Deployments** (Üzemelő példányok) panelen való megtekintéséhez. Egy korábbi üzemelő példányra való visszaállításhoz jelölje ki azt, majd kattintson a **Redeploy** (Ismételt üzembe helyezés) elemre a **Deployment Details** (Példány részletei) panelen.

## <a name="next-steps"></a>Következő lépések
Node.js-alkalmazást helyezett üzembe az Azure App Service egyik webalkalmazásában. További információk a Node.js-alkalmazások App Service-webalkalmazásokkal történő futtatásáról: [Azure App Service Web Apps: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx) és [Specifying a Node.js version in an Azure application](../nodejs-specify-node-version-azure-apps.md) (A Node.js-verzió megadása az Azure-alkalmazásban).

A Node.js a modulok széles választékát kínálja, amelyeket az alkalmazásai használhatnak. A Web Apps modulokkal történő használatával kapcsolatban lásd: [Using Node.js modules with Azure applications](../nodejs-use-node-modules-azure-apps.md) (Node.js-modulok használata Azure-alkalmazásokkal).

Ha az Azure-on történő üzembe helyezést követően problémája adódik az alkalmazás használatával, a probléma diagnosztizálásával kapcsolatban információkat a [How to debug a Node.js application in Azure App Service](web-sites-nodejs-debug.md) (Node.js-alkalmazás hibakeresése az Azure App Service szolgáltatásban) című részben talál.

Ebben a cikkben webalkalmazás létrehozását mutatjuk be az Azure portál használatával. Ugyanezen műveletek végrehajtásához használhatja az [Azure parancssori felületét](../xplat-cli-install.md) vagy az [Azure PowerShellt](/powershell/azureps-cmdlets-docs) is.

A Node.js-alkalmazásoknak az Azure-on történő fejlesztéséről további információkat a következő témakörben talál: [Node.js fejlesztői központ](/develop/nodejs/).

[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png
[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png
[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png
[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png
[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png



<!--HONumber=Jan17_HO3-->


