---
title: "Helyezze üzembe első PHP-webalkalmazását öt perc alatt az Azure-ban (CLI 2.0 – előzetes verzió) | Microsoft Docs"
description: "Egy PHP-mintaalkalmazás üzembe helyezésével megtudhatja, mennyire egyszerű a webalkalmazások futtatása az App Service-ben. Gyorsan hozzáfoghat a valós fejlesztéshez, az eredményeket pedig azonnal meg is tekintheti."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/27/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 68298208d2e2cc1fe7ab4050afecb25ca7d619cd
ms.lasthandoff: 02/28/2017


---
# <a name="deploy-your-first-php-web-app-to-azure-in-five-minutes-cli-20-preview"></a>Helyezze üzembe első PHP-webalkalmazását öt perc alatt az Azure-ban (CLI 2.0 – előzetes verzió)
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

Ez az oktatóanyag segítséget nyújt az első PHP-webalkalmazás üzembe helyezéséhez az [Azure App Service-ben](../app-service/app-service-value-prop-what-is.md).
Az App Service használatával webalkalmazásokat, [mobilalkalmazások háttérkomponenseit](/documentation/learning-paths/appservice-mobileapps/) és [API-alkalmazásokat](../app-service-api/app-service-api-apps-why-best-platform.md) hozhat létre.

Az alábbiakat fogja elvégezni: 

* Webalkalmazás létrehozása az Azure App Service használatával.
* PHP-mintakód üzembe helyezése.
* A kód élőben, üzemi környezetben való futtatása.
* Ugyanúgy frissítheti a webalkalmazását, mint ahogy azt a [Git-véglegesítéseknél is tenné](https://git-scm.com/docs/git-push).

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók

A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Azure CLI 1.0](app-service-web-get-started-php-cli-nodejs.md) – parancssori felületünk a klasszikus és a Resource Management üzemi modellekhez
- [Azure CLI 2.0](app-service-web-get-started-php.md) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.

## <a name="prerequisites"></a>Előfeltételek
* [Git](http://www.git-scm.com/downloads).
* [Azure CLI 2.0 előzetes verzió](/cli/azure/install-az-cli2).
* Egy Microsoft Azure-fiók. Ha nincs fiókja, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), vagy [aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Az [App Service kipróbálása](https://azure.microsoft.com/try/app-service/) Azure-fiók nélkül is lehetséges. Hozzon létre egy kezdő szintű alkalmazást, amellyel legfeljebb egy óráig foglalkozhat – ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

## <a name="deploy-a-php-web-app"></a>PHP-alapú webalkalmazás üzembe helyezése
1. Nyisson meg egy új Windows-parancssort, PowerShell-ablakot, Linux shellt vagy egy OS X terminált. A `git --version` és az `az --version` paranccsal ellenőrizheti, hogy a Git és az Azure parancssori felülete telepítve van-e a számítógépen.
   
    ![Parancssori felület tesztelési célú telepítése az első Azure-webalkalmazáshoz](./media/app-service-web-get-started-languages/1-test-tools-2.0.png)
   
    Ha még nem telepítette az eszközöket, akkor telepítse őket. A letöltési hivatkozásokat az [Előfeltételek](#Prerequisites) szakaszban találja.
2. Jelentkezzen be az Azure-ba a következő módon:
   
        az login
   
    A bejelentkezési folyamat folytatásához kövesse a súgóüzenetet.
   
    ![Bejelentkezés az Azure-ba az első webalkalmazás létrehozásához](./media/app-service-web-get-started-languages/3-azure-login-2.0.png)

3. Állítsa be az üzembe helyező felhasználót az App Service számára. A kód üzembe helyezését később fogja elvégezni ezen hitelesítő adatok használatával.
   
        az appservice web deployment user set --user-name <unique-username> --password <8-char-or-longer-password-letters-and-numbers>

3. Hozzon létre egy új [erőforráscsoportot](../azure-resource-manager/resource-group-overview.md). Az első App Service-oktatóanyaghoz nem feltétlenül szükséges tudnia, mi ez.

        az group create --location "<location>" --name my-first-app-group

    A `<location>` paraméterhez használható lehetséges értékek megtekintéséhez, használja az `az appservice list-locations` CLI-parancsot.

3. Hozzon létre egy új, „INGYENES” [App Service-csomagot](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Az első App Service-oktatóanyaggal kapcsolatban tudnia kell, hogy nem kell fizetnie az ebben a csomagban szereplő webappokért.

        az appservice plan create --name my-free-appservice-plan --resource-group my-first-app-group --sku FREE

4. Hozzon létre egy új, egyéni névvel rendelkező webappot az `<app_name>` paraméterben.

        az appservice web create --name <app_name> --resource-group my-first-app-group --plan my-free-appservice-plan

4. Ezután kapja meg az üzembe helyezni kívánt PHP-mintakódot. Váltson egy munkakönyvtárra (a `CD` paranccsal), és klónozza a példaalkalmazást az alábbi lépések szerint:
   
        cd <working_directory>
        git clone https://github.com/Azure-Samples/app-service-web-php-get-started.git

5. Váltson a mintaalkalmazás adattárára. Példa:
   
        cd app-service-web-php-get-started
5. Konfigurálja az App Service webapphoz tartozó helyi Git üzemelő példányt a következő paranccsal:

        az appservice web source-control config-local-git --name <app_name> --resource-group my-first-app-group

    Egy ehhez hasonló JSON-kimenet jön létre, ami azt jelenti, hogy a távoli Git-tárház be lett állítva:

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. Adja hozzá a JSON-ban található URL-címet a helyi tárházhoz távoli Git-elemként (az egyszerűség kedvéért nevezzük a következőnek: `azure`).

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. Helyezze üzembe a mintakódot az Azure-alkalmazásban ugyanúgy, ahogy Git-kódok esetében is tenné. Ha a rendszer kéri, használja a korábban beállított jelszót.
   
        git push azure master
   
    ![Kód beillesztése az első Azure webalkalmazásba](./media/app-service-web-get-started-languages/php-git-push.png)
   
    A `git push` nem csak az Azure-be illeszti be a kódot, hanem üzembe helyezési feladatokat is aktivál az üzembe helyezési motorban. Ezen kívül a engedélyezheti a  [Composer bővítményt](web-sites-php-mysql-deploy-use-git.md#composer) is, így a composer.json fájlok automatikusan fel lesznek dolgozva a PHP-alkalmazásokban.

Sikeresen üzembe helyezte az alkalmazást az Azure App Service-ben!

## <a name="see-your-app-running-live"></a>Az alkalmazás megtekintése működés közben
Az Azure-alkalmazást az adattár valamelyik könyvtárában kiadott alábbi paranccsal tekintheti meg működés közben:

    az appservice web browse --name <app_name> --resource-group my-first-app-group

## <a name="make-updates-to-your-app"></a>Az alkalmazás módosítása (frissítése)
A Git segítségével mostantól bármikor leküldhet a projekt (adattár) gyökérkönyvtárából, ha frissítenie kell az élő webhelyet. Ezt ugyanolyan módon teheti meg, mint amikor az első alkalommal helyezte üzembe a kódot. Például minden alkalommal, amikor egy új, helyileg tesztelt módosítást kíván leküldeni, akkor egyszerűen csak futtatnia kell az alábbi parancsokat a projekt (adattár) gyökérkönyvtárából:

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>Következő lépések
[Laravel-webalkalmazás létrehozása, konfigurálása és üzembe helyezése az Azure-on](app-service-web-php-get-started.md). Az oktatóanyag utasításait követve szert tehet a bármely PHP-webalkalmazás Azure-ban való futtatásához szükséges alapszintű készségekre, többek között az alábbiakra:

* Alkalmazások létrehozása és konfigurálása az Azure-ban Powershellből/Bashből.
* A PHP-verzió beállítása.
* Egy nem a gyökér-alkalmazáskönyvtárban található indítófájl használata.
* Composer-automatizálás engedélyezése.
* Hozzáférés környezetspecifikus változókhoz.
* Gyakori hibák elhárítása.

Vagy tegyen még többet az első webalkalmazásával. Példa:

* Próbálja meg [egyéb módokon üzembe helyezni a kódot az Azure-ban](web-sites-deploy.md). Ha például az egyik GitHub-adattárból szeretné elvégezni a telepítést, egyszerűen válassza a **GitHub** lehetőséget a **Helyi git-tárház** helyett az **Üzembehelyezési lehetőségek** területen.
* Új szintre emelheti Azure alkalmazását. Hitelesítheti felhasználóit. Igény szerint méretezheti. Beállíthat a teljesítménnyel kapcsolatos riasztásokat. Mindezt csupán néhány kattintással. Lásd: [Funkciók hozzáadása az első webalkalmazásához](app-service-web-get-started-2.md).


