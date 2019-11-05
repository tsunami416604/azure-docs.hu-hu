---
title: 'Gyors útmutató: Python-alkalmazás létrehozása Linux rendszeren – Azure App Service'
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
ms.topic: quickstart
ms.date: 10/22/2019
ms.author: cephalin
ms.custom: seo-python-october2019
experimental: false
experiment_id: 1e304dc9-5add-4b
ms.openlocfilehash: 69e7cfef01005432a99dd10ed5bc7f004562e582
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470817"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Gyors útmutató: Python-alkalmazás létrehozása Azure App Service Linuxon

Ebben a rövid útmutatóban egy Python-webalkalmazást helyez üzembe a [Linuxon](app-service-linux-intro.md), az Azure rugalmasan méretezhető, önjavító webes üzemeltetési szolgáltatásának app Service. A helyi [Azure parancssori felület (CLI)](/cli/azure/install-azure-cli) a Mac, Linux vagy Windows rendszerű számítógépeken használható. Az Ön által konfigurált webalkalmazás ingyenes App Service szintet használ, így a cikk során nem jár költségekkel.

Ha egy IDE-n keresztül szeretné telepíteni az alkalmazásokat, tekintse [meg a Python-alkalmazások üzembe helyezése a Visual Studio Code-ból való app Service](/azure/python/tutorial-deploy-app-service-on-linux-01)című témakört.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3,7</a> (Python 3,6 is támogatott)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>

## <a name="download-the-sample"></a>A minta letöltése

Egy terminál ablakban futtassa a következő parancsot a minta alkalmazás helyi számítógépre történő klónozásához. 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Ezután nyissa meg a mappát:

```terminal
cd python-docs-hello-world
```

A tárház tartalmaz egy *Application.py* -fájlt, amely azt jelzi, app Service, hogy a kód tartalmaz egy lombik alkalmazást. További információ: [tároló indítási folyamata és testreszabása](how-to-configure-python.md).

## <a name="run-the-sample"></a>Minta futtatása

A terminál ablakban használja az alábbi parancsokat (az operációs rendszernek megfelelően) a szükséges függőségek telepítéséhez és a beépített fejlesztői kiszolgáló elindításához. 

# <a name="bashtabbash"></a>[Bash](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py
flask run
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
SET FLASK_APP=application.py
flask run
```

---

Nyisson meg egy webböngészőt, és nyissa meg a minta alkalmazást a következő címen: `http://localhost:5000/`. Az alkalmazás a következő üzenetet jeleníti meg: **"Helló világ!" alkalmazás!** .

![Egy minta Python-alkalmazás helyi futtatása](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

A terminál ablakban nyomja le a **Ctrl**+**C** billentyűkombinációt a webkiszolgálóból való kilépéshez.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Az Azure CLI számos kényelmes parancsot biztosít, amelyeket egy helyi terminálon használhat az Azure-erőforrások parancssorból való üzembe helyezéséhez és kezeléséhez. A parancsok használatával ugyanazokat a feladatokat végezheti el, mint a böngészőben Azure Portal. Parancsfájlokban CLI-parancsokat is használhat a felügyeleti folyamatok automatizálására.

Az Azure-parancsok Azure CLI-ben való futtatásához először be kell jelentkeznie az `az login` parancs használatával. Ez a parancs egy böngészőt nyit meg a hitelesítő adatok összegyűjtéséhez.

```terminal
az login
```

## <a name="deploy-the-sample"></a>A minta üzembe helyezése

A [`az webapp up`](/cli/azure/webapp#az-webapp-up) parancs létrehozza a webalkalmazást app Service és üzembe helyezi a kódot.

A mintakódt tartalmazó *Python-docs-Hello-World* mappában futtassa a következő `az webapp up` parancsot. Cserélje le a `<app-name>`t egy globálisan egyedi alkalmazás nevére (*érvényes karakterek: `a-z`, `0-9`és `-`* ). Emellett cserélje le a `<location-name>`t egy olyan Azure-régióra, mint például a **CentralUS**, a **eastasia**, a **westeurope**, a **koreasouth**, a **brazilsouth**, a **centralindia**stb. (A [`az account locations-list`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) parancs futtatásával lekérheti az Azure-fiók számára engedélyezett régiók listáját.)


```terminal
az webapp up --sku F1 -n <app-name> -l <location-name>
```

A parancs végrehajtása néhány percet is igénybe vehet. Futtatás közben a parancs a következő példához hasonló információkat jelenít meg:

```output
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
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
  "resourcegroup": "appsvc_rg_Linux_centralus ",
  "serverfarm": "appsvc_asp_Linux_centralus",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Tallózással keresse meg az üzembe helyezett alkalmazást a webböngészőjében a `http://<app-name>.azurewebsites.net`URL-címen.

A Python-mintakód egy beépített rendszerképpel futtatja App Service Linux-tárolóját.

![Minta Python-alkalmazás futtatása az Azure-ban](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Gratulálunk!** Telepítette a Python-alkalmazást a Linuxon való App Service.

## <a name="redeploy-updates"></a>Frissítések újratelepítése

A kedvenc szerkesztőprogramban nyissa meg a *Application.py* , és módosítsa az utolsó sor `return` utasítását, hogy az megfeleljen a következő kódnak. A `print` utasítás itt található a következő szakaszban használt naplózási kimenet létrehozásához. 

```python
print("Handling request to home page.")
return "Hello Azure!"
```

Mentse a módosításokat, és zárja be a szerkesztőt. 

Telepítse újra az alkalmazást az alábbi `az webapp up` parancs használatával, ugyanazzal a paranccsal, amelyet az alkalmazás első alkalommal történő üzembe helyezéséhez használt, a `<app-name>` és a `<location-name>` helyett a korábban használt nevekkel. 

```terminal
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Az üzembe helyezés befejezését követően váltson vissza a `http://<app-name>.azurewebsites.net` böngészőablakra, és frissítse az oldalt, amely a módosított üzenetet jeleníti meg:

![Frissített minta Python-alkalmazás futtatása az Azure-ban](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> A Visual Studio Code hatékony bővítményeket biztosít a Python és a Azure App Service számára, ami leegyszerűsíti a Python-webalkalmazások App Serviceba való üzembe helyezésének folyamatát. További információ: [Python-alkalmazások üzembe helyezése a Visual Studio Code-ból App Service](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Naplók streamelése

Elérheti az alkalmazáson belül létrehozott konzol naplóit, valamint azt a tárolót, amelyben a fut. A naplók a `print` utasítások használatával generált kimeneteket tartalmazzák.

Először kapcsolja be a tároló naplózását úgy, hogy futtatja a következő parancsot egy terminálon, és lecseréli `<app-name>` az alkalmazás nevére, és `<resource-group-name>` a használt `az webapp up` parancs kimenetében látható erőforráscsoport nevével (például "appsvc_rg_Linux_centralus"). ):

```terminal
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Miután bekapcsolta a tároló-naplózást, futtassa a következő parancsot a log stream megjelenítéséhez:

```terminal
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Frissítse az alkalmazást a böngészőben a konzol naplófájljainak létrehozásához, amelyeknek az alábbi szöveghez hasonló vonalakat kell tartalmazniuk. Ha nem látja el azonnal a kimenetet, próbálkozzon újra 30 másodperc múlva.

```output
2019-10-23T12:40:03.815574424Z Handling request to home page.
2019-10-23T12:40:03.815602424Z 172.16.0.1 - - [23/Oct/2019:12:40:03 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.63 Safari/537.36 Edg/78.0.276.19"
```

A naplófájlokat a böngészőben is ellenőrizheti `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Ha bármikor le szeretné állítani a naplózási adatfolyamot, írja be a következőt: `Ctrl`+`C`.

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

A létrehozott alkalmazás kezeléséhez lépjen a <a href="https://portal.azure.com" target="_blank">Azure Portal</a> . Keresse meg és válassza ki a **app Services**.

![Navigáljon App Services a Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Válassza ki az Azure-alkalmazás nevét.

![Navigáljon a Python-alkalmazáshoz App Services a Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Megtekintheti az alkalmazás áttekintés lapját. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés.

![A Python-alkalmazás kezelése a Azure Portal Áttekintés oldalán](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

A App Service menü különböző lapokat biztosít az alkalmazás konfigurálásához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Az erőforráscsoport neve például "appsvc_rg_Linux_CentralUS", a tartózkodási helytől függően. Ha az ingyenes F1-es csomagtól eltérő App Service SKU-t használ, ezek az erőforrások folyamatos költségekkel járnak.

Ha nem várható, hogy szükség van ezekre az erőforrásokra a jövőben, törölje az erőforráscsoportot a következő parancs futtatásával, és cserélje le a `<resource-group-name>`t az `az webapp up` parancs kimenetében látható erőforráscsoporthoz, például "appsvc_rg_Linux_centralus". A parancs végrehajtása egy percet is igénybe vehet.

```terminal
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Python (Django) webalkalmazás és PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Python-alkalmazás futtatása egyéni tárolóban](tutorial-custom-docker-image.md)
