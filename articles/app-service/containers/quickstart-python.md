---
title: 'Rövid útmutató: Linux Python-alkalmazás létrehozása'
description: Az Azure App Service Linux-alkalmazásai üzembe helyezésével az első Python-alkalmazás egy Linux-tárolóba az App Service-ben.
ms.topic: quickstart
ms.date: 10/22/2019
ms.custom: seo-python-october2019, cli-validate
experimental: true
experiment_id: 01a9132f-eaab-4c
ms.openlocfilehash: 9cc314edf35d6a327522ed49fcc0c7798c7dcf63
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80045663"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Rövid útmutató: Python-alkalmazás létrehozása linuxos Azure App Service-ben

Ebben a rövid útmutatóban egy Python-webalkalmazást telepít az [App Service Linuxon szolgáltatásra,](app-service-linux-intro.md)az Azure nagymértékben méretezhető, önjavító webhosting szolgáltatását. A helyi [Azure parancssori felületet (CLI)](/cli/azure/install-azure-cli) Mac, Linux vagy Windows rendszerű számítógépeken használja. A konfigurált webalkalmazás egy ingyenes App Service-réteget használ, így a cikk során nem jár költségek.

Ha az alkalmazásokat egy IDE-n keresztül szeretné telepíteni, olvassa [el a Python-alkalmazások telepítése az App Service szolgáltatásba a Visual Studio-kódból ..](/azure/python/tutorial-deploy-app-service-on-linux-01)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3.7</a> (Python 3.6 is támogatott)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>

## <a name="download-the-sample"></a>A minta letöltése

A terminálablakban futtassa a következő parancsot a mintaalkalmazás helyi számítógépre történő klónozásához. 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Akkor menj abba a mappába:

```terminal
cd python-docs-hello-world
```

A tárház tartalmaz egy *application.py* fájlt, amely közli az App Service-rel, hogy a kód lombik alkalmazást tartalmaz. További információt a [Tároló indítási folyamata és testreszabása című témakörben talál.](how-to-configure-python.md)

## <a name="run-the-sample"></a>Minta futtatása

A terminálablakban az alábbi parancsokkal (az operációs rendszernek megfelelően) telepítse a szükséges függőségeket, és indítsa el a beépített fejlesztői kiszolgálót. 

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
export FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
SET FLASK_APP=application.py
flask run
```

---

Nyisson meg egy webböngészőt, `http://localhost:5000/`és nyissa meg a mintaalkalmazást a helyen. Az alkalmazás megjeleníti az üzenetet **Hello World!**.

![Python-mintaalkalmazás helyi futtatása](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

A terminálablakban nyomja le a**Ctrl C** **billentyűt**+a webkiszolgálóból való kilépéshez.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Az Azure CLI számos kényelmes parancsot biztosít, amelyeket egy helyi terminálról az Azure-erőforrások parancssorból történő kiépítéséhez és kezeléséhez használ. A parancsok segítségével ugyanazokat a feladatokat végezheti el, mint az Azure Portalon keresztül egy böngészőben. A parancsfájlokban CLI-parancsokat is használhat a felügyeleti folyamatok automatizálásához.

Az Azure-parancsok futtatásához az Azure CLI, `az login` először be kell jelentkeznie a paranccsal. Ez a parancs megnyit egy böngészőt a hitelesítő adatok összegyűjtéséhez.

```azurecli
az login
```

## <a name="deploy-the-sample"></a>A minta telepítése

A [`az webapp up`](/cli/azure/webapp#az-webapp-up) parancs létrehozza a webalkalmazást az App Service szolgáltatásban, és telepíti a kódot.

A mintakódot tartalmazó *python-docs-hello-world* mappában `az webapp up` futtassa a következő parancsot. Cserélje `<app-name>` le egy globálisan egyedi alkalmazásnévre (*érvényes karakterek `a-z`, `0-9`és `-` *a ). Cserélje `<location-name>` le az Azure-régióra is, például **a centralusra,** **az eastasia-ra,** **a westeurope-ra,** **a koreasouth-ra,** **a brazilsouth-ra,** **a centralindia-ra**és így tovább. (A parancs futtatásával lekérheti az [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) Azure-fiókjához engedélyezett régiók listáját.)


```azurecli
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Ez a parancs futtatása néhány percet is igénybe vehet. Futtatás közben a parancs a következő példához hasonló információkat jelenít meg:

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

Tallózással keresse meg az üzembe `http://<app-name>.azurewebsites.net`helyezett alkalmazást a webböngészőben az URL-címen.

A Python-mintakód egy Linux-tárolót futtat az App Service-ben egy beépített rendszerkép használatával.

![Minta Python-alkalmazás futtatása az Azure-ban](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Gratulálok!** Telepítette a Python-alkalmazást az App Service Linux-on.

## <a name="redeploy-updates"></a>Frissítések újratelepítése

A kedvenc kódszerkesztőben nyissa meg `return` *application.py,* és módosítsa az utolsó sorban lévő utasítást úgy, hogy megfeleljen a következő kódnak. Az `print` utasítás itt található a következő szakaszban végzett naplózási kimenet létrehozásához. 

```python
print("Handling request to home page.")
return "Hello Azure!"
```

Mentse a módosításokat, és lépjen ki a szerkesztőből. 

Telepítse újra az alkalmazást `az webapp up` a következő paranccsal, ugyanazt a parancsot `<app-name>` `<location-name>` használva, amelyet az alkalmazás első üzembe helyezéséhez használt, és a korábban használt neveket helyettesítve. 

```azurecli
az webapp up --sku F1 -n <app-name> -l <location-name>
```

A telepítés befejezése után váltson vissza `http://<app-name>.azurewebsites.net` a megnyitott böngészőablakra, és frissítse a lapot, amelynek meg kell jelenítenie a módosított üzenetet:

![Frissített Python-mintaalkalmazás futtatása az Azure-ban](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> A Visual Studio Code hatékony bővítményeket biztosít a Python és az Azure App Service számára, amelyek leegyszerűsítik a Python-webalkalmazások appservice-be való üzembe helyezésének folyamatát. További információ: [Python-alkalmazások telepítése az App Service szolgáltatásba a Visual Studio-kódból](/azure/python/tutorial-deploy-app-service-on-linux-01)című témakörben talál.

## <a name="stream-logs"></a>Naplók streamelése

Az alkalmazáson belülről és a tárolóból létrehozott konzolnaplók at érheti el. A naplók tartalmazzák az `print` utasítások használatával létrehozott kimeneteket.

Először kapcsolja be a tárolónaplózást úgy, `<app-name>` hogy a következő parancsot futtatja egy terminálon, és lecseréli az alkalmazás nevét és `<resource-group-name>` a használt `az webapp up` parancs kimenetében látható erőforráscsoport nevét (például "appsvc_rg_Linux_centralus"):

```azurecli
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

A tárolónaplózás bekapcsolása után futtassa a következő parancsot a naplófolyam megjelenítéséhez:

```azurecli
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Frissítse az alkalmazást a böngészőben konzolnaplók létrehozásához, amelyeknek a következő szöveghez hasonló sorokat kell tartalmazniuk. Ha nem látja azonnal a kimenetet, próbálkozzon újra 30 másodperc en belül.

```output
2019-10-23T12:40:03.815574424Z Handling request to home page.
2019-10-23T12:40:03.815602424Z 172.16.0.1 - - [23/Oct/2019:12:40:03 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.63 Safari/537.36 Edg/78.0.276.19"
```

A naplófájlokat a böngészőből `https://<app-name>.scm.azurewebsites.net/api/logs/docker`is megtekintheti a(.

Ha bármikor le szeretné állítani `Ctrl` + `C`a naplóstreamelést, írja be a következőt:

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

Nyissa meg az <a href="https://portal.azure.com" target="_blank">Azure Portalon</a> a létrehozott alkalmazás kezeléséhez. Keresse meg és válassza **az App Services**lehetőséget.

![Navigálás az App Services szolgáltatásra az Azure Portalon](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Válassza ki az Azure-alkalmazás nevét.

![Keresse meg a Python-alkalmazást az App Servicesben az Azure Portalon](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Megjelenik az alkalmazás áttekintése lap. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés.

![A Python-alkalmazás kezelése az Azure Portal Áttekintés lapján](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Az App Service menü különböző oldalakat biztosít az alkalmazás konfigurálásához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Az erőforráscsoport neve a tartózkodási helytől függően "appsvc_rg_Linux_CentralUS" . Ha az ingyenes F1 szinttől eltérő App Service Termékváltozatot használ, ezek az erőforrások folyamatos költségekkel járnak.

Ha a jövőben nem várható, hogy szükség lesz ezekre az erőforrásokra, `<resource-group-name>` törölje az erőforráscsoportot a `az webapp up` következő parancs futtatásával, és cserélje le a parancs kimenetében látható erőforráscsoportra, például "appsvc_rg_Linux_centralus". A parancs egy percig is eltarthat.

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Python (Django) webalkalmazás a PostgreSQL-rel](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Python-alkalmazás futtatása egyéni tárolóban](tutorial-custom-docker-image.md)
