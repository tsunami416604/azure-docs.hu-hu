---
title: "Helyezze üzembe az első ASP.NET-webalkalmazását öt perc alatt az Azure-ban | Microsoft Docs"
description: "Egy ASP.NET-mintaalkalmazás üzembe helyezésével megtudhatja, mennyire egyszerű a webalkalmazások futtatása az App Service-ben. Gyorsan hozzáfoghat a valós fejlesztéshez, az eredményeket pedig azonnal meg is tekintheti."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 0458cc3b-b630-4632-8cd0-76e2ed96498b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 05e61d2fc751c4239aef4b10ad897765c59fe928
ms.openlocfilehash: 3fe44c37bee330f06b3e41b7bdb89138372c0c7a


---
# <a name="deploy-your-first-aspnet-web-app-to-azure-in-five-minutes"></a>Helyezze üzembe az első ASP.NET-webalkalmazását öt perc alatt az Azure-ban

> [!div class="op_single_selector"]
> * [Első HTML-webhely](app-service-web-get-started-html-cli-nodejs.md)
> * [Első .NET-alkalmazás](app-service-web-get-started-dotnet-cli-nodejs.md)
> * [Első PHP-alkalmazás](app-service-web-get-started-php-cli-nodejs.md)
> * [Első Node.js-alkalmazás](app-service-web-get-started-nodejs-cli-nodejs.md)
> * [Első Python-alkalmazás](app-service-web-get-started-python-cli-nodejs.md)
> * [Első Java-alkalmazás](app-service-web-get-started-java.md)
> 
> 

Ez az oktatóanyag segítséget nyújt egy egyszerű ASP.NET-webapp üzembe helyezéséhez az [Azure App Service-ben](../app-service/app-service-value-prop-what-is.md).
Az App Service használatával webalkalmazásokat, [mobilalkalmazások háttérkomponenseit](/documentation/learning-paths/appservice-mobileapps/) és [API-alkalmazásokat](../app-service-api/app-service-api-apps-why-best-platform.md) hozhat létre.

Az alábbiakat fogja elvégezni: 

* Webalkalmazás létrehozása az Azure App Service használatával.
* ASP.NET-mintakód üzembe helyezése.
* A kód élőben, üzemi környezetben való futtatása.
* Ugyanúgy frissítheti a webalkalmazását, mint ahogy azt a [Git-véglegesítéseknél is tenné](https://git-scm.com/docs/git-push).

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók

A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Azure CLI 1.0](app-service-web-get-started-dotnet-cli-nodejs.md) – parancssori felületünk a klasszikus és a Resource Management üzemi modellekhez
- [Azure CLI 2.0 (előzetes verzió)](app-service-web-get-started-dotnet.md) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli

## <a name="prerequisites"></a>Előfeltételek
* [Git](http://www.git-scm.com/downloads).
* [Azure parancssori felület (CLI)](../xplat-cli-install.md).
* Egy Microsoft Azure-fiók. Ha nincs fiókja, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), vagy [aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Az [App Service kipróbálása](http://go.microsoft.com/fwlink/?LinkId=523751) Azure-fiók nélkül is lehetséges. Hozzon létre egy kezdő szintű alkalmazást, amellyel legfeljebb egy óráig foglalkozhat – ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

## <a name="deploy-an-aspnet-web-app"></a>ASP.NET-webapp üzembe helyezése
1. Nyisson meg egy új Windows-parancssort, PowerShell-ablakot, Linux shellt vagy egy OS X terminált. A `git --version` és az `azure --version` paranccsal ellenőrizheti, hogy a Git és az Azure parancssori felülete telepítve van-e a számítógépen.
   
    ![Parancssori felület tesztelési célú telepítése az első Azure-webalkalmazáshoz](./media/app-service-web-get-started/1-test-tools.png)
   
    Ha még nem telepítette az eszközöket, akkor telepítse őket. A letöltési hivatkozásokat az [Előfeltételek](#Prerequisites) szakaszban találja.
2. Jelentkezzen be az Azure-ba a következő módon:
   
        azure login
   
    A bejelentkezési folyamat folytatásához kövesse a súgóüzenetet.
   
    ![Bejelentkezés az Azure-ba az első webalkalmazás létrehozásához](./media/app-service-web-get-started/3-azure-login.png)

3. Váltsa az Azure parancssori felületét ASM üzemmódba, majd állítsa be az üzembe helyező felhasználót az App Service számára. A kód üzembe helyezését később fogja elvégezni a hitelesítő adatok használatával.
   
        azure config mode asm
        azure site deployment user set --username <username> --pass <password>

4. Váltson egy munkakönyvtárra (a `CD` paranccsal), és klónozza a ASP.NET-példaalkalmazást.
   
        git clone https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git

5. Váltson a mintaalkalmazás adattárára. 
   
        cd app-service-web-dotnet-get-started

6. Hozzon létre egy egyedi alkalmazásnévvel rendelkező App Service alkalmazás-erőforrást az Azure-ban a korábban beállított üzembe helyező felhasználóval. Amikor a rendszer kéri, adja meg a kívánt régió számát.
   
        azure site create <app_name> --git --gitusername <username>
   
    ![Azure-erőforrás létrehozása az első Azure webalkalmazáshoz](./media/app-service-web-get-started-languages/dotnet-site-create.png)
   
    Az alkalmazás elkészült az Azure-ban. Emellett inicializálódott az aktuális könyvtár a Git-hez, és távoli Git-mappaként csatlakoztatva lett az új App Service-alkalmazáshoz.
    Az alkalmazás URL-címén (http://&lt;alkalmazás_neve>.azurewebsites.net) böngészőben meg is tekintheti az alkalmazást az alapértelmezett HTML-oldallal. Most pedig a saját kód írása következik.

7. Helyezze üzembe a mintakódot az Azure-alkalmazásban ugyanúgy, ahogy Git-kódok esetében is tenné. Ha a rendszer kéri, használja a korábban beállított jelszót.
   
        git push azure master
   
    ![Kód beillesztése az első Azure webalkalmazásba](./media/app-service-web-get-started-languages/dotnet-git-push.png)
   
    `git push`nemcsak az Azure-ba illeszti be a kódot, hanem visszaállítja a szükséges csomagokat, és létrehozza az ASP.NET-binárisfájlokat is. 

Sikeresen üzembe helyezte az alkalmazást az Azure App Service-ben!

## <a name="see-your-app-running-live"></a>Az alkalmazás megtekintése működés közben
Az Azure-alkalmazást az adattár valamelyik könyvtárában kiadott alábbi paranccsal tekintheti meg működés közben:

    azure site browse

## <a name="make-updates-to-your-app"></a>Az alkalmazás módosítása (frissítése)
A Git segítségével mostantól bármikor leküldhet a projekt (adattár) gyökérkönyvtárából, ha frissítenie kell az élő webhelyet. Ezt ugyanolyan módon teheti meg, mint amikor az első alkalommal helyezte üzembe a kódot. Például minden alkalommal, amikor egy új, helyileg tesztelt módosítást kíván leküldeni, akkor egyszerűen csak futtatnia kell az alábbi parancsokat a projekt (adattár) gyökérkönyvtárából:

    git add .
    git commit -m "<your_message>"
    git push azure master


## <a name="next-steps"></a>Következő lépések
Az [ASP.NET-webalkalmazás telepítése az Azure App Service-be a Visual Studióval](web-sites-dotnet-get-started.md) című szakaszból megtudhatja, hogyan hozhat létre, fejleszthet és helyezhet üzembe .NET-webalkalmazásokat az Azure-on a Visual Studióval.

Vagy tegyen még többet az első webalkalmazásával. Példa:

* Próbálja meg [egyéb módokon üzembe helyezni a kódot az Azure-ban](web-sites-deploy.md). Ha például az egyik GitHub-adattárból szeretné elvégezni a telepítést, egyszerűen válassza a **GitHub** lehetőséget a **Helyi git-tárház** helyett az **Üzembehelyezési lehetőségek** területen.
* Új szintre emelheti Azure alkalmazását. Hitelesítheti felhasználóit. Igény szerint méretezheti. Beállíthat a teljesítménnyel kapcsolatos riasztásokat. Mindezt csupán néhány kattintással. Lásd: [Funkciók hozzáadása az első webalkalmazásához](app-service-web-get-started-2.md).




<!--HONumber=Jan17_HO1-->


