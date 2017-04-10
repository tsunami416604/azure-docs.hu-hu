---
title: "PHP-alkalmazás létrehozása a webappban | Microsoft Docs"
description: "Percek alatt üzembe helyezheti az első Hello World PHP-jét az App Service webappban."
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/31/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: e6a486ebb442b8e2404fd53081fcad6561b7fb41
ms.lasthandoff: 04/04/2017


---
# <a name="create-a-php-application-on-web-app"></a>PHP-alkalmazás létrehozása a webappban

Ez a rövid útmutató a PHP-alkalmazások Azure rendszeren történő fejlesztésén és üzembe helyezésén vezeti végig. Az alkalmazás futtatását Linux-alapú Azure App Service-szolgáltatás használatával végezzük majd el, az új webapp létrehozása és konfigurálása pedig az Azure CLI használatával fog történni. Ezt követően a git segítségével üzembe helyezzük a PHP-alkalmazást az Azure-ban.

![hello-world-in-browser](media/app-service-web-get-started-php/hello-world-in-browser.png)

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató gépen is követheti. Az alábbi lépések végrehajtása nagyjából 5 percet vehet igénybe.

## <a name="before-you-begin"></a>Előkészületek

A minta futtatása előtt telepítse helyben a következő előfeltételeket:

1. [A git letöltése és telepítése](https://git-scm.com/)
1. [A PHP letöltése és telepítése](https://php.net)
1. Az [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) letöltése és telepítése

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>A minta letöltése

Klónozza a Hello World mintaalkalmazás tárházát a helyi számítógépre.

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
```

> [!TIP]
> Másik lehetőségként [letöltheti a mintát](https://github.com/Azure-Samples/php-docs-hello-world/archive/master.zip) ZIP-fájlként, majd kicsomagolhatja azt.

Váltson arra a könyvtárra, amelyben a mintakód megtalálható.

```bash
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Az alkalmazás helyi futtatásához nyissa meg a terminálablakot a minta `php` parancssorának használatával, hogy elindítsa a beépített PHP-webkiszolgálót.

```bash
php -S localhost:8080
```

Nyisson meg egy webböngészőt, majd keresse meg a mintát.

```bash
http://localhost:8080
```

Az oldalon látható mintaalkalmazáson ekkor a **Hello World** üzenetnek kell megjelennie.

![localhost-hello-world-in-browser](media/app-service-web-get-started-php/localhost-hello-world-in-browser.png)

A terminálablakban nyomja le a **Ctrl+C** billentyűkombinációt a webkiszolgálóból történő kilépéshez.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Most az Azure CLI 2.0-t fogjuk egy terminálablakban használni a PHP-alkalmazás Azure-ban történő üzemeltetéséhez szükséges erőforrások létrehozásához. Jelentkezzen be az Azure-előfizetésbe az [az login](/cli/azure/#login) paranccsal, és kövesse a képernyőn látható utasításokat.

```azurecli
az login
```

## <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

Az FTP és a helyi Git esetében üzembe helyező felhasználót kell konfigurálni a kiszolgálón az üzemelő példány hitelesítéséhez. Az üzembe helyező felhasználó konfigurálását csak egyszer kell elvégezni. Jegyezze fel a felhasználónevet és a jelszót, mivel az alább látható lépésben ezeket fogja használni.

> [!NOTE]
> Az FTP és a helyi Git WebAppban történő üzembe helyezéséhez üzembe helyező felhasználóra van szükség.
> A `username` és a `password` fiókszintűek, így eltérnek az Azure-előfizetés hitelesítő adataitól. **Ezeket a hitelesítő adatokat csupán egyszer kell létrehozni.**
>

A fiókszintű hitelesítő adatok létrehozásához használja az [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) parancsot.

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat (például webappokat, adatbázisokat és tárfiókokat).

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-an-azure-app-service"></a>Azure App Service-szolgáltatás létrehozása

Hozzon létre egy Linux-alapú App Service-csomagot az [az appservice plan create](/cli/azure/appservice/plan#create) paranccsal.

> [!NOTE]
> Az App Service-csomagok az alkalmazások üzemeltetéséhez használt fizikai erőforrások gyűjteményét jelölik. Egy App Service-csomaghoz rendelt összes alkalmazás ugyanazokat az általa meghatározott erőforrásokat használja, így több alkalmazás üzemeltetése esetén is csökkenthetők a költségek.
>
> Az App Service-csomagok a következőket határozzák meg:
> * Régió (Észak-Európa, az USA keleti régiója, Délkelet-Ázsia)
> * Példányméret (kicsi, közepes, nagy)
> * Méretezési szám (egy, kettő vagy három példány stb.)
> * Termékváltozat (Ingyenes, Közös, Alapszintű, Standard, Prémium)
>

Az alábbi példa egy App Service-csomag létrehozását mutatja be a `quickStartPlan` nevű Linux-feldolgozókon, a **Standard** tarifacsomag használatával.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku S1 --is-linux
```

Az App Service-csomag létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg.

```json
{
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "kind": "linux",
    "location": "West Europe",
    "sku": {
    "capacity": 1,
    "family": "S",
    "name": "S1",
    "tier": "Standard"
    },
    "status": "Ready",
    "type": "Microsoft.Web/serverfarms"
}
```

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Most, hogy létrehoztuk az App Service-csomagot, hozzon létre egy webappot a `quickStartPlan` App Service-csomagján belül. A webapp üzemeltetési tárterületet biztosít a kód üzembe helyezéséhez, valamint megadja az üzembe helyezett alkalmazás megtekintéséhez szükséges URL-címet. A webapp létrehozásához használja az [az appservice web create](/cli/azure/appservice/web#create) parancsot.

Az alábbi parancsban az <app_name> helyőrző helyére írja be saját, egyedi alkalmazásnevét. Az <app_name> nevet a rendszer a webapp alapértelmezett DNS-helyének részeként használja, ezért annak egyedinek kell lennie az Azure összes alkalmazása tekintetében. A későbbiekben leképezhet bármely DNS-bejegyzést a webapphoz, mielőtt elérhetővé teszi a felhasználók számára.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

A webapp létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg.

```json
{
    "clientAffinityEnabled": true,
    "defaultHostName": "<app_name>.azurewebsites.net",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>",
    "isDefaultContainer": null,
    "kind": "app",
    "location": "West Europe",
    "name": "<app_name>",
    "repositorySiteName": "<app_name>",
    "reserved": true,
    "resourceGroup": "myResourceGroup",
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "state": "Running",
    "type": "Microsoft.Web/sites",
}
```

Az újonnan létrehozott webapp megtekintéséhez tallózással keresse meg a helyet.

```bash
http://<app_name>.azurewebsites.net
```

![app-service-web-service-created](media/app-service-web-get-started-php/app-service-web-service-created.png)

Ezzel létrehoztunk egy üres, új webappot az Azure-ban. A PHP használatához és az alkalmazás azon történő üzembe helyezéséhez most végezzük el a webapp konfigurálását.

## <a name="configure-to-use-php"></a>A PHP használatának konfigurálása

A PHP `7.0.x` verziójának használatához futtassa az [az appservice web config update](/cli/azure/app-service/web/config#update) parancsot a webapp konfigurálásához.

> [!TIP]
> A PHP-verzió e konfigurációja a platform által biztosított alapértelmezett tárolót használja. Ha saját tárolót szeretne használni, tekintse meg az [az appservice web config container update](https://docs.microsoft.com/cli/azure/appservice/web/config/container#update) parancs CLI-referenciáját.

```azurecli
az appservice web config update --linux-fx-version "PHP|7.0" --name <app_name> --resource-group myResourceGroup
```

## <a name="configure-local-git-deployment"></a>A Git helyi üzemelő példányának konfigurálása

A webappon történő üzembe helyezésnek számos módja létezik, így például az FTP, a helyi Git, valamint a GitHub, a Visual Studio Team Services és a Bitbucket.

A webapphoz a helyi git alkalmazásával történő hozzáférés konfigurálásához használja az [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) parancsot.

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

A művelet kimenetét másolja ki a terminálról, mivel azt a következő lépésben használni fogja.

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

## <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

Adjon hozzá egy távoli Azure-mappát a helyi Git-tárházhoz.

```bash
git remote add azure <paste-previous-command-output-here>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást. Az üzembe helyező felhasználó létrehozásának részeként a rendszer fel fogja kérni a korábban megadott jelszó megadására.

```azurecli
git push azure master
```

Az üzembe helyezés során az Azure App Service-szolgáltatás megküldi annak előrehaladási állapotát a Git részére.

```bash
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
   cc39b1e..25f1805  master -> master
```

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Tallózással keresse meg az üzembe helyezett alkalmazást a webböngésző használatával.

```bash
http://<app_name>.azurewebsites.net
```

Ekkor a Hello World üzenetet megjelenítő oldal egy Azure App Service-webappként futó PHP-kód használatával fut.



## <a name="updating-and-deploying-the-code"></a>A kód frissítése és központi telepítése

Egy helyi szövegszerkesztő használatával nyissa meg a `index.php` fájlt a PHP-alkalmazáson belül, majd módosítsa kissé annak szövegét a `echo` melletti karakterláncon belül:

```php
echo "Hello Azure!";
```

Mentse a módosításokat a gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash
git commit -am "updated output"
git push azure master
```

Az üzembe helyezés befejezését követően váltson vissza Az alkalmazás megkeresése tallózással lépésben megnyitott böngészőablakra, és frissítse azt.

![hello-world-in-browser](media/app-service-web-get-started-php/hello-world-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Az új Azure-webapp kezelése

Az Azure Portalon tekintse meg a létrehozott webappot.

Ehhez jelentkezzen be a következő címen: [https://portal.azure.com](https://portal.azure.com).

A bal oldali menüben kattintson az **App Service** lehetőségre, majd az Azure-webapp nevére.

![Navigálás a portálon az Azure-webapphoz](./media/app-service-web-get-started-php/php-docs-hello-world-app-service-list.png)

Ekkor a webapp _paneljére_ (vízszintesen megnyíló portáloldalára) jut.

Alapértelmezés szerint a webapp panelje az **Áttekintés** oldalt mutatja. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. A panel bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![Az App Service panel az Azure Portalon](media/app-service-web-get-started-php/php-docs-hello-world-app-service-detail.png)

A panel ezen lapja a webapphoz hozzáadható nagyszerű szolgáltatásokat jelenítik meg. Az alábbi lista csupán néhány lehetőséget sorol fel:

* Egyéni DNS-név leképezése
* Egyéni SSL-tanúsítvány kötése
* Folyamatos üzembe helyezés konfigurálása
* Vertikális felskálázás és kibővítés
* Felhasználói hitelesítés hozzáadása

**Gratulálunk!** Elvégezte az első PHP-webapp üzembe helyezését az App Service-ben.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

Az előre létrehozott, [Web Apps CLI-szkriptek](app-service-cli-samples.md) vizsgálata.
