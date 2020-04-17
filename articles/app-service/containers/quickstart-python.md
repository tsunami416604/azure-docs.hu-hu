---
title: 'Rövid útmutató: Linux Python-alkalmazás létrehozása'
description: Az Azure App Service Linux-alkalmazásai üzembe helyezésével az első Python-alkalmazás egy Linux-tárolóba az App Service-ben.
ms.topic: quickstart
ms.date: 04/03/2020
ms.custom: seo-python-october2019, cli-validate
experimental: true
experiment_id: 01a9132f-eaab-4c
ms.openlocfilehash: ea5df6a2d4a972638b52587f9edc868f65e25df5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532748"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Rövid útmutató: Python-alkalmazás létrehozása linuxos Azure App Service-ben

Ebben a rövid útmutatóban egy Python-webalkalmazást telepít az [App Service Linuxon szolgáltatásra,](app-service-linux-intro.md)az Azure nagymértékben méretezhető, önjavító webhosting szolgáltatását. A helyi [Azure parancssori felületet (CLI)](/cli/azure/install-azure-cli) Mac, Linux vagy Windows rendszerű számítógépeken használja. A konfigurált webalkalmazás egy ingyenes App Service-réteget használ, így a cikk során nem jár költségek.

Ha az alkalmazásokat egy IDE-n keresztül szeretné telepíteni, olvassa [el a Python-alkalmazások telepítése az App Service szolgáltatásba a Visual Studio-kódból ..](/azure/python/tutorial-deploy-app-service-on-linux-01)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3.7</a> (Python 3.6 is támogatott)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 vagy újabb. Futtassa az `az --version` parancsot a verzió ellenőrzéséhez.

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

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

A mintakódot tartalmazó *python-docs-hello-world* mappában `az webapp up` futtassa a következő parancsot. Cserélje `<app-name>` le egy globálisan egyedi alkalmazásnévre (*érvényes karakterek `a-z`, `0-9`és `-` *a ).


```azurecli
az webapp up --sku F1 -n <app-name>
```

Az `--sku F1` argumentum létrehozza a webalkalmazást az ingyenes tarifaszinten. Ezt az argumentumot kihagyhatja, hogy prémium szintű réteget használjon helyette, amely óránkénti költséget von maga után.

Tetszés szerint `-l <location-name>` megadhatja azt az argumentumot, `<location_name>` hogy hol található az Azure-régió, például **centralus,** **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia**és így tovább. Az Azure-fiók engedélyezett régióinak listáját a [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) parancs futtatásával kérheti le.

A `az webapp up` parancs teljes futtatása eltarthat néhány percig. Futás közben a következő példához hasonló `<app_name>` információkat jelenít meg, ahol a korábban megadott név lesz:

<pre>
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
App service plan creation complete
Creating app '<app-name>' ....
Configuring default logging for the app, if not already enabled
Creating zip with contents of dir D:\Examples\python-docs-hello-world ...
Getting scm site credentials for zip deployment
Starting zip deployment. This operation can take a while to complete ...
Deployment endpoint responded with status code 202
You can launch the app at http://<app-name>.azurewebsites.net
{
  "URL": "http://<app-name>.net",
  "appserviceplan": "appsvc_asp_Linux_centralus",
  "location": "eastus",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_centralus",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "FREE",
  "src_path": "D:\\Examples\\python-docs-hello-world"
}
</pre>

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Tallózással keresse meg az üzembe `http://<app-name>.azurewebsites.net`helyezett alkalmazást a webböngészőben az URL-címen.

A Python-mintakód egy Linux-tárolót futtat az App Service-ben egy beépített rendszerkép használatával.

![Minta Python-alkalmazás futtatása az Azure-ban](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Gratulálok!** Telepítette a Python-alkalmazást az App Service Linux-on.

## <a name="redeploy-updates"></a>Frissítések újratelepítése

A kedvenc kódszerkesztőben nyissa meg `hello` *application.py,* és frissítse a funkciót az alábbiak szerint. Ez a `print` módosítás egy utasítást ad hozzá a következő szakaszban végzett naplózási kimenet létrehozásához. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello Azure!"
```

Mentse a módosításokat, és lépjen ki a szerkesztőből. 

Telepítse újra az `az webapp up` alkalmazást a paranccsal:

```azurecli
az webapp up
```

Ez a parancs az *.azure/config* fájlban gyorsítótárazott értékeket használ, beleértve az alkalmazás nevét, az erőforráscsoportot és az App Service-csomagot.

A telepítés befejezése után váltson vissza `http://<app-name>.azurewebsites.net` a megnyitott böngészőablakra, és frissítse a lapot, amelynek meg kell jelenítenie a módosított üzenetet:

![Frissített Python-mintaalkalmazás futtatása az Azure-ban](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> A Visual Studio Code hatékony bővítményeket biztosít a Python és az Azure App Service számára, amelyek leegyszerűsítik a Python-webalkalmazások appservice-be való üzembe helyezésének folyamatát. További információ: [Python-alkalmazások telepítése az App Service szolgáltatásba a Visual Studio-kódból](/azure/python/tutorial-deploy-app-service-on-linux-01)című témakörben talál.

## <a name="stream-logs"></a>Naplók streamelése

Az alkalmazáson belülről és a tárolóból létrehozott konzolnaplók at érheti el. A naplók tartalmazzák az `print` utasítások használatával létrehozott kimeneteket.

A naplók streameléséhez futtassa a következő parancsot:

```azurecli
az webapp log tail
```

Frissítse az alkalmazást a böngészőben konzolnaplók létrehozásához, amelyeknek a következő szöveghez hasonló sorokat kell tartalmazniuk. Ha nem látja azonnal a kimenetet, próbálkozzon újra 30 másodperc en belül.

<pre>
2020-04-03T22:54:04.236405938Z Handling request to home page.
2020-04-03T22:54:04.236497641Z 172.16.0.1 - - [03/Apr/2020:22:54:04 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.83 Safari/537.36 Edg/81.0.416.41"
</pre>

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

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Az erőforráscsoport neve a tartózkodási helytől függően "appsvc_rg_Linux_CentralUS" . Ha az ingyenes F1 szinttől eltérő App Service Termékváltozatot használ, ezek az erőforrások folyamatos költségekkel járnak (lásd: [App Service-díjszabás).](https://azure.microsoft.com/pricing/details/app-service/linux/)

Ha a jövőben nem várható, hogy szükség lesz ezekre az erőforrásokra, `<resource-group-name>` törölje az erőforráscsoportot a `az webapp up` következő parancs futtatásával, és cserélje le a parancs kimenetében látható erőforráscsoportra, például "appsvc_rg_Linux_centralus". A parancs egy percig is eltarthat.

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Python (Django) webalkalmazás a PostgreSQL-rel](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Felhasználói bejelentkezés hozzáadása Python-webalkalmazáshoz](../../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Python-alkalmazás futtatása egyéni tárolóban](tutorial-custom-docker-image.md)
