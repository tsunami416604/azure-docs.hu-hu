---
title: Python-alkalmazás létrehozása Linux rendszeren – Azure App Service | Microsoft Docs
description: Percek alatt üzembe helyezheti első Hello World Python-alkalmazását a Linuxon futó Azure App Service-ben.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: cephalin
ms.openlocfilehash: c7306de4af7f958ef8f89dac41a5e08209916432
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020213"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux"></a>Python-alkalmazás létrehozása Azure App Service Linuxon

Ebben a rövid útmutatóban egy egyszerű Python-alkalmazást telepít [app Service Linuxon](app-service-linux-intro.md), amely egy rugalmasan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt. Az Azure parancssori felületét (az [Azure CLI](/cli/azure/install-azure-cli)-t) az interaktív, böngészőalapú Azure Cloud Shell használatával hajthatja végre, így követheti a Mac, Linux vagy Windows rendszerű számítógépek használatának lépéseit.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-python/hello-world-in-browser.png)

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

* <a href="https://www.python.org/downloads/" target="_blank">A Python 3.7 telepítése</a>
* <a href="https://git-scm.com/" target="_blank">A Git telepítése</a>
* Azure-előfizetés. Ha még nem rendelkezik ilyennel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a Kezdés előtt.

## <a name="download-the-sample-locally"></a>Minta helyi letöltése

Egy terminál-ablakban futtassa a következő parancsokat a minta alkalmazás helyi gépre történő klónozásához, majd nyissa meg a könyvtárat a mintakód használatával.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

A tárház tartalmaz egy *Application.py*, amely azt jelzi, app Service, hogy az adattár tartalmaz egy lombik alkalmazást. További információ: [tároló indítási folyamata és testreszabása](how-to-configure-python.md).

## <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Futtassa helyileg az alkalmazást, hogy lássa, hogyan fog kinézni az Azure-ban üzembe helyezve. Nyisson meg egy terminálablakot, és használja az alábbi parancsokat a szükséges függőségek telepítéséhez, majd indítsa el a beépített fejlesztési kiszolgálót. 

```bash
# In Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py flask run

# In PowerShell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

Nyisson meg egy webböngészőt, és nyissa meg a `http://localhost:5000/`minta alkalmazást a következő címen:.

Az oldalon látható mintaalkalmazáson ekkor a **Hello World!** üzenet jelenik meg.

![A helyileg futó mintaalkalmazás](media/quickstart-python/hello-world-in-browser.png)

A terminálablakban nyomja le a **Ctrl+C** billentyűkombinációt a webkiszolgálóból történő kilépéshez.

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

A következő példában `<app-name>` cserélje le egy globálisan egyedi alkalmazás nevére ( *`a-z`érvényes `0-9`karakterek `-`* :, és).

```bash
cd python-docs-hello-world

az webapp up -n <app-name>
```

A parancs futtatása eltarthat néhány percig. Futtatás közben a parancs a következő példához hasonló információkat jelenít meg:

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app-name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app-name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Tallózással keresse meg az üzembe helyezett alkalmazást a webböngésző használatával.

```bash
http://<app-name>.azurewebsites.net
```

A Python-mintakód App Service Linux rendszeren fut, beépített képpel.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-python/hello-world-in-browser.png)

**Gratulálunk!** Elvégezte az első Python-webapp üzembe helyezését az App Service-ben Linux rendszeren.

## <a name="update-locally-and-redeploy-the-code"></a>A kód frissítése helyileg és ismételt üzembe helyezése

A Cloud Shell a Cloud Shell- `code application.py` szerkesztő megnyitásához írja be a következőt:.

![Application.py kód](media/quickstart-python/code-applicationpy.png)

 Végezzen el egy kis módosítást a `return` hívásának szövegében:

```python
return "Hello Azure!"
```

Mentse a módosításokat, és zárja be a szerkesztőt. A mentéshez a `^S`, a kilépéshez a `^Q` parancsot használja.

Telepítse újra az alkalmazást a [`az webapp up`](/cli/azure/webapp#az-webapp-up) parancs használatával. Helyettesítse be az alkalmazás `<app-name>`nevét, és adjon meg egy `<location-name>` helyet (a [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) parancsban látható értékek egyikének használatával).

```bash
az webapp up -n <app-name> -l <location-name>
```

Az üzembe helyezés befejezését követően váltson vissza **Az alkalmazás megkeresése tallózással** lépésben megnyitott böngészőablakra, és frissítse az oldalt.

![Az Azure-ban futó frissített mintaalkalmazás](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Az új Azure-alkalmazás kezelése

A létrehozott alkalmazás kezeléséhez lépjen a <a href="https://portal.azure.com" target="_blank">Azure Portal</a> .

A bal oldali menüben válassza a **app Services**lehetőséget, majd válassza ki az Azure-alkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/quickstart-python/app-service-list.png)

Megtekintheti az alkalmazás áttekintés lapját. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés.

![Az App Service lap az Azure Portalon](media/quickstart-python/app-service-detail.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Python (Django) webalkalmazás és PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Python-alkalmazás futtatása egyéni tárolóban](tutorial-custom-docker-image.md)
