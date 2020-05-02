---
title: 'Rövid útmutató: Linux Python-alkalmazás létrehozása'
description: Ismerkedjen meg a Linux-alkalmazásokkal Azure App Service az első Python-alkalmazás üzembe helyezésével a App Service Linux-tárolójában.
ms.topic: quickstart
ms.date: 04/03/2020
ms.custom: seo-python-october2019, cli-validate
ms.openlocfilehash: 5b055c3ed93d5f093295b52c7a28a73e242bfe75
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690879"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Gyors útmutató: Python-alkalmazás létrehozása Azure App Service Linuxon

Ebben a rövid útmutatóban egy Python-webalkalmazást helyez üzembe a [Linuxon](app-service-linux-intro.md), az Azure rugalmasan méretezhető, önjavító webes üzemeltetési szolgáltatásának app Service. A helyi [Azure parancssori felület (CLI)](/cli/azure/install-azure-cli) a Mac, Linux vagy Windows rendszerű számítógépeken használható. Az Ön által konfigurált webalkalmazás ingyenes App Service szintet használ, így a cikk során nem jár költségekkel.

Ha egy IDE-n keresztül szeretné telepíteni az alkalmazásokat, tekintse [meg a Python-alkalmazások üzembe helyezése a Visual Studio Code-ból való app Service](/azure/python/tutorial-deploy-app-service-on-linux-01)című témakört.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3,7</a> (Python 3,6 is támogatott)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> -2.0.80 vagy újabb. Futtassa az `az --version` parancsot a verzió ellenőrzéséhez.

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

Nyisson meg egy webböngészőt, és nyissa meg a `http://localhost:5000/`minta alkalmazást a következő címen:. Az alkalmazás a következő üzenetet jeleníti meg: **"Helló világ!" alkalmazás!**.

![Egy minta Python-alkalmazás helyi futtatása](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

A terminál ablakban nyomja le a **CTRL**+**C** billentyűkombinációt a webkiszolgálóból való kilépéshez.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Az Azure CLI számos kényelmes parancsot biztosít, amelyeket egy helyi terminálon használhat az Azure-erőforrások parancssorból való üzembe helyezéséhez és kezeléséhez. A parancsok használatával ugyanazokat a feladatokat végezheti el, mint a böngészőben Azure Portal. Parancsfájlokban CLI-parancsokat is használhat a felügyeleti folyamatok automatizálására.

Az Azure-parancsok Azure CLI-ben való futtatásához először be kell jelentkeznie `az login` a parancs használatával. Ez a parancs egy böngészőt nyit meg a hitelesítő adatok összegyűjtéséhez.

```azurecli
az login
```

## <a name="deploy-the-sample"></a>A minta üzembe helyezése

A [`az webapp up`](/cli/azure/webapp#az-webapp-up) parancs létrehozza a app Service webalkalmazást, és üzembe helyezi a kódot.

Futtassa a következő `az webapp up` parancsot a *Python-docs-Hello-World* mappában, amely tartalmazza a kódot. Cserélje `<app-name>` le egy globálisan egyedi alkalmazás nevére (*érvényes karakterek `a-z` `0-9`:, és `-` *).


```azurecli
az webapp up --sku F1 -n <app-name>
```
> [!CAUTION]
> Ha az **Azure-CLI-** t használja, akkor van egy regresszió, `az webapp up` amelyben bizonyos helyzetek meghiúsulnak, `-l <location-name>` ha a paraméter nem szerepel a rendszerben. Ezt a problémát [itt követheti nyomon](https://github.com/Azure/azure-cli/issues/13257).  
> 
>A `az --version` paranccsal megtekintheti, hogy az Azure-CLI melyik verzióját használja.
>

Az `--sku F1` argumentum a webalkalmazást az ingyenes díjszabási szinten hozza létre. Ezt az argumentumot kihagyhatja a prémium szintű csomag használatához, ami óradíjat eredményez.

Igény szerint megadhatja `-l <location-name>` az argumentumot `<location_name>` , ahol egy olyan Azure-régió, mint például a **CentralUS**, a **eastasia**, a **westeurope**, a **koreasouth**, a **brazilsouth**, a **centralindia**stb. Az Azure-fiók számára engedélyezett régiók listáját a [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) parancs futtatásával kérheti le.

A `az webapp up` parancs futtatása több percet is igénybe vehet. A futtatása közben a következő példához hasonló információkat jelenít meg, ahol `<app-name>` a korábban megadott nevet kapja:

<pre>
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
App service plan creation complete
Creating app '&lt;app-name&gt;' ....
Configuring default logging for the app, if not already enabled
Creating zip with contents of dir D:\Examples\python-docs-hello-world ...
Getting scm site credentials for zip deployment
Starting zip deployment. This operation can take a while to complete ...
Deployment endpoint responded with status code 202
You can launch the app at http://&lt;app-name&gt;.azurewebsites.net
{
  "URL": "http://&lt;app-name&gt;.net",
  "appserviceplan": "appsvc_asp_Linux_centralus",
  "location": "eastus",
  "name": "&lt;app-name&gt;",
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

Tallózással keresse meg az üzembe helyezett alkalmazást a böngészőben az URL- `http://<app-name>.azurewebsites.net`címen.

A Python-mintakód egy beépített rendszerképpel futtatja App Service Linux-tárolóját.

![Minta Python-alkalmazás futtatása az Azure-ban](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Gratulálok!** Telepítette a Python-alkalmazást a Linuxon való App Service.

## <a name="redeploy-updates"></a>Frissítések újratelepítése

A kedvenc szerkesztőprogramban nyissa meg a *Application.py* , és `hello` frissítse a függvényt az alábbiak szerint. Ez a módosítás egy `print` utasítást ad hozzá a következő szakaszban használt naplózási kimenet létrehozásához. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello Azure!"
```

Mentse a módosításokat, és zárja be a szerkesztőt. 

Telepítse újra az alkalmazást a `az webapp up` parancs használatával:

```azurecli
az webapp up
```

Ez a parancs a *. Azure/config* fájlban gyorsítótárazott értékeket használja, beleértve az alkalmazás nevét, az erőforráscsoportot és a app Service tervet.

Az üzembe helyezés befejezése után váltson vissza a böngészőablakra, `http://<app-name>.azurewebsites.net` és frissítse az oldalt, amely a módosított üzenetet jeleníti meg:

![Frissített minta Python-alkalmazás futtatása az Azure-ban](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> A Visual Studio Code hatékony bővítményeket biztosít a Python és a Azure App Service számára, ami leegyszerűsíti a Python-webalkalmazások App Serviceba való üzembe helyezésének folyamatát. További információ: [Python-alkalmazások üzembe helyezése a Visual Studio Code-ból App Service](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Naplók streamelése

Elérheti az alkalmazáson belül létrehozott konzol naplóit, valamint azt a tárolót, amelyben a fut. A naplók tartalmazzák az utasítások használatával `print` generált kimeneteket.

A naplók továbbításához futtassa a következő parancsot:

```azurecli
az webapp log tail
```

Frissítse az alkalmazást a böngészőben a konzol naplófájljainak létrehozásához, amelyeknek az alábbi szöveghez hasonló vonalakat kell tartalmazniuk. Ha nem látja el azonnal a kimenetet, próbálkozzon újra 30 másodperc múlva.

<pre>
2020-04-03T22:54:04.236405938Z Handling request to home page.
2020-04-03T22:54:04.236497641Z 172.16.0.1 - - [03/Apr/2020:22:54:04 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.83 Safari/537.36 Edg/81.0.416.41"
</pre>

A naplófájlokat a böngészőből is ellenőrizheti `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Ha bármikor le szeretné állítani a naplózási adatfolyamot, írja be a következőt `Ctrl` + `C`:.

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

A létrehozott alkalmazás kezeléséhez lépjen a <a href="https://portal.azure.com" target="_blank">Azure Portal</a> . Keresse meg és válassza ki a **app Services**.

![Navigáljon App Services a Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Válassza ki az Azure-alkalmazás nevét.

![Navigáljon a Python-alkalmazáshoz App Services a Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Megtekintheti az alkalmazás áttekintés lapját. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés.

![A Python-alkalmazás kezelése a Azure Portal Áttekintés oldalán](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

A App Service menü különböző lapokat biztosít az alkalmazás konfigurálásához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Az erőforráscsoport neve például "appsvc_rg_Linux_CentralUS", a tartózkodási helytől függően. Ha az ingyenes F1-es csomagtól eltérő App Service SKU-t használ, ezek az erőforrások folyamatos költségekkel járnak (lásd a [app Service díjszabását](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Ha nem várható, hogy a jövőben szüksége lenne ezekre az erőforrásokra, törölje az erőforráscsoportot a következő parancs futtatásával, `<resource-group-name>` és cserélje le a `az webapp up` parancsot a parancs kimenetében látható erőforráscsoporthoz, például: "appsvc_rg_Linux_centralus". A parancs végrehajtása egy percet is igénybe vehet.

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Python (Django) webalkalmazás és PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Felhasználói bejelentkezés hozzáadása egy Python-webalkalmazáshoz](../../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Python-alkalmazás futtatása egyéni tárolóban](tutorial-custom-docker-image.md)
