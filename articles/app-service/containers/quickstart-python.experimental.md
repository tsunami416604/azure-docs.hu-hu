---
title: 'Rövid útmutató: Linux Python-alkalmazás létrehozása'
description: Ismerkedjen meg a Linux-alkalmazásokkal Azure App Service az első Python-alkalmazás üzembe helyezésével a App Service Linux-tárolójában.
author: msangapu-msft
ms.topic: quickstart
ms.date: 08/23/2019
ms.custom: seodec18
experimental: false
experiment_id: 1e304dc9-5add-4b
ms.openlocfilehash: 7905b086ac390243f2fe02697f68c9b1c05ca28e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688995"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux"></a>Python-alkalmazás létrehozása Azure App Service Linuxon

Ebben a rövid útmutatóban egy egyszerű Python-alkalmazást telepít [app Service Linuxon](app-service-linux-intro.md), amely egy rugalmasan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt. Az Azure parancssori felületét (az [Azure CLI](/cli/azure/install-azure-cli)-t) az interaktív, böngészőalapú Azure Cloud Shell használatával hajthatja végre, így követheti a Mac, Linux vagy Windows rendszerű számítógépek használatának lépéseit.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>A minta letöltése

A Cloud Shellben hozzon létre egy quickstart könyvtárat, és lépjen a könyvtárba.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Ezután futtassa a következő parancsot a mintaalkalmazás-adattár a quickstart könyvtárba való klónozásához.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Futtatás közben a parancs a következő példához hasonló információkat jelenít meg:

```bash
Cloning into 'python-docs-hello-world'...
remote: Enumerating objects: 43, done.
remote: Total 43 (delta 0), reused 0 (delta 0), pack-reused 43
Unpacking objects: 100% (43/43), done.
Checking connectivity... done.
```

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Lépjen abba a könyvtárba, amelyben a mintakód található, és futtassa az `az webapp up` parancsot.

A következő példában cserélje ki az *\<app_name>* nevet egy globálisan egyedi névre (érvényes karakterek: `a-z`, `0-9` és `-`).

```bash
cd python-docs-hello-world

az webapp up -n <app_name>
```

A parancs futtatása eltarthat néhány percig. Futtatás közben a parancs a következő példához hasonló információkat jelenít meg:

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app_name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app_name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app_name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

Az `az webapp up` parancs a következő műveleteket hajtja végre:

- Egy alapértelmezett erőforráscsoport létrehozása.

- Egy alapértelmezett App Service-csomag létrehozása.

- Egy alkalmazás létrehozása a megadott néven.

- A [zip telepíti](https://docs.microsoft.com/azure/app-service/deploy-zip) a fájlokat az aktuális munkakönyvtárból az alkalmazásba.

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Tallózással keresse meg az üzembe helyezett alkalmazást a webböngésző használatával.

```bash
http://<app_name>.azurewebsites.net
```

A Python-mintakód App Service Linux rendszeren fut, beépített képpel.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-python/hello-world-in-browser.png)

**Gratulálunk!** Elvégezte az első Python-webapp üzembe helyezését az App Service-ben Linux rendszeren.

## <a name="update-and-redeploy-the-code"></a>A kód frissítése és ismételt üzembe helyezése

A Cloud Shell írja be a `code application.py`t a Cloud Shell-szerkesztő megnyitásához.

![Application.py kód](media/quickstart-python/code-applicationpy.png)

 Végezzen el egy kis módosítást a `return` hívásának szövegében:

```python
return "Hello Azure!"
```

Mentse a módosításokat, és zárja be a szerkesztőt. A mentéshez a `^S`, a kilépéshez a `^Q` parancsot használja.

Most újra üzembe helyezzük az alkalmazást. `<app_name>` helyettesítése az alkalmazással.

```bash
az webapp up -n <app_name>
```

Az üzembe helyezés befejezését követően váltson vissza **Az alkalmazás megkeresése tallózással** lépésben megnyitott böngészőablakra, és frissítse az oldalt.

![Az Azure-ban futó frissített mintaalkalmazás](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Az új Azure-alkalmazás kezelése

A létrehozott alkalmazás kezeléséhez lépjen a <a href="https://portal.azure.com" target="_blank">Azure Portal</a> .

A bal oldali menüben kattintson a **app Services**elemre, majd kattintson az Azure-alkalmazás nevére.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/quickstart-python/app-service-list.png)

Megtekintheti az alkalmazás áttekintés lapját. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés.

![Az App Service lap az Azure Portalon](media/quickstart-python/app-service-detail.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Python-alkalmazás és PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: üzembe helyezés Private Container adattárból](tutorial-custom-docker-image.md)
