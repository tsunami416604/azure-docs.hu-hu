---
title: 'Rövid útmutató: Linux Python-alkalmazás létrehozása'
description: Ismerkedjen meg a Linux-alkalmazásokkal Azure App Service az első Python-alkalmazás üzembe helyezésével a App Service Linux-tárolójában.
ms.topic: quickstart
ms.date: 06/30/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python
ms.openlocfilehash: 804ed6084454a70fa6fc4edff9a0579e43a3a872
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853483"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Gyors útmutató: Python-alkalmazás létrehozása Azure App Service Linuxon

Ebben a rövid útmutatóban egy Python-webalkalmazást helyez üzembe a [Linuxon](app-service-linux-intro.md), az Azure rugalmasan méretezhető, önjavító webes üzemeltetési szolgáltatásának app Service. A helyi [Azure parancssori felület (CLI)](/cli/azure/install-azure-cli) a Mac, Linux vagy Windows rendszerű számítógépeken használható. Az Ön által konfigurált webalkalmazás ingyenes App Service szintet használ, így a cikk során nem jár költségekkel.

Ha egy IDE-n keresztül szeretné telepíteni az alkalmazásokat, tekintse [meg a Python-alkalmazások üzembe helyezése a Visual Studio Code-ból való app Service](/azure/developer/python/tutorial-deploy-app-service-on-linux-01)című témakört.

## <a name="set-up-your-initial-environment"></a>A kezdeti környezet beállítása

Mielőtt elkezdené, a következőkkel kell rendelkeznie:

1. Rendelkeznie kell aktív előfizetéssel rendelkező Azure-fiókkal. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Telepítse a <a href="https://www.python.org/downloads/" target="_blank">Python 3,6-es vagy újabb</a>verzióját.
1. Telepítse az <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> -2.0.80 vagy újabb verzióját, amellyel az Azure-erőforrások kiépítéséhez és konfigurálásához bármilyen rendszerhéjban parancsokat futtathat.

Nyisson meg egy terminált, és győződjön meg arról, hogy a Python verziója 3,6 vagy újabb:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Győződjön meg arról, hogy az Azure CLI verziója 2.0.80 vagy magasabb:

```azurecli
az --version
```

Ezután jelentkezzen be az Azure-ba a CLI használatával:

```azurecli
az login
```

Ez a parancs egy böngészőt nyit meg a hitelesítő adatok összegyűjtéséhez. A parancs befejeződése után az előfizetésekkel kapcsolatos információkat tartalmazó JSON-kimenet jelenik meg.

Miután bejelentkezett, futtathatja az Azure-parancsokat az Azure CLI-vel, hogy az előfizetésében lévő erőforrásokkal működjön.

## <a name="clone-the-sample"></a>A minta klónozása

A minta tárház klónozása a következő paranccsal. ([Telepítse a git](https://git-scm.com/downloads) -t, ha még nem rendelkezik a git-vel.)

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Ezután nyissa meg a mappát:

```terminal
cd python-docs-hello-world
```

A mintakód tartalmaz egy *Application.py* -fájlt, amely azt jelzi, app Service, hogy a kód tartalmaz egy lombik alkalmazást. További információ: [tároló indítási folyamata és testreszabása](how-to-configure-python.md).

## <a name="run-the-sample"></a>Minta futtatása

# <a name="bash"></a>[Bash](#tab/bash)

Először hozzon létre egy virtuális környezetet, és telepítse a függőségeket:

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

Ezután állítsa be a `FLASK_APP` környezeti változót az alkalmazás belépési moduljába, és futtassa a lombik fejlesztői kiszolgálót:

```
export FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Először hozzon létre egy virtuális környezetet, és telepítse a függőségeket:

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
```

Ezután állítsa be a `FLASK_APP` környezeti változót az alkalmazás belépési moduljába, és futtassa a lombik fejlesztői kiszolgálót:

```powershell
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

Először hozzon létre egy virtuális környezetet, és telepítse a függőségeket:

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
```

Ezután állítsa be a `FLASK_APP` környezeti változót az alkalmazás belépési moduljába, és futtassa a lombik fejlesztői kiszolgálót:

```cmd
SET FLASK_APP=application.py
flask run
```

---

Nyisson meg egy webböngészőt, és nyissa meg a minta alkalmazást a következő címen: `http://localhost:5000/` . Az alkalmazás a következő üzenetet jeleníti meg: **"Helló világ!" alkalmazás!**.

![Egy minta Python-alkalmazás helyi futtatása](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

A terminál ablakban a **CTRL**C billentyű lenyomásával + **C** lépjen ki a lombik fejlesztői kiszolgálóról.

## <a name="deploy-the-sample"></a>A minta üzembe helyezése

Telepítse a kódot a helyi mappájába (*Python-docs-Hello-World*) a következő `az webapp up` parancs használatával:

```azurecli
az webapp up --sku F1 -n <app-name>
```

- Ha a `az` parancs nem ismerhető fel, győződjön meg arról, hogy az Azure CLI telepítve van [a kezdeti környezet beállítása](#set-up-your-initial-environment)című témakörben leírtak szerint.
- Cserélje le a `<app_name>` karaktert az összes Azure-beli egyedi névre (*érvényes karakterek: `a-z` , `0-9` és `-` *). Jó példa a vállalat nevének és az alkalmazás-azonosító kombinációjának használatára.
- Az `--sku F1` argumentum a webalkalmazást az ingyenes díjszabási szinten hozza létre. Hagyja ki ezt az argumentumot a gyorsabb prémium szint használatához, amely óradíjat eredményez.
- Igény szerint megadhatja az argumentumot, ahol egy olyan Azure-régió, mint például a `-l <location-name>` `<location_name>` **CentralUS**, a **eastasia**, a **westeurope**, a **koreasouth**, a **brazilsouth**, a **centralindia**stb. Az Azure-fiók számára engedélyezett régiók listáját a parancs futtatásával kérheti le [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) .

A parancs végrehajtása több percet is igénybe vehet. A futtatása közben üzeneteket biztosít az erőforráscsoport létrehozásához, a App Service terv és az üzemeltetési alkalmazáshoz, a naplózás konfigurálásához, majd a ZIP-telepítés elindításához. Ezután a következő üzenet jelenik meg: "az alkalmazás elindítható a http:// &lt; app-name &gt; . azurewebsites.net" néven, amely az alkalmazás URL-címe az Azure-ban.

![Példa az az WebApp up parancs kimenetére](./media/quickstart-python/az-webapp-up-output.png)

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Tallózással keresse meg az üzembe helyezett alkalmazást a böngészőben az URL-címen `http://<app-name>.azurewebsites.net` .

A Python-mintakód egy beépített rendszerképpel futtatja App Service Linux-tárolóját.

![Minta Python-alkalmazás futtatása az Azure-ban](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Gratulálunk!** Telepítette a Python-alkalmazást a Linuxon való App Service.

## <a name="redeploy-updates"></a>Frissítések újratelepítése

A kedvenc szerkesztőprogramban nyissa meg a *Application.py* , és frissítse a `hello` függvényt az alábbiak szerint. Ez a módosítás egy utasítást ad hozzá a `print` következő szakaszban használt naplózási kimenet létrehozásához. 

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

Ez a parancs azokat az értékeket használja, amelyek helyileg vannak gyorsítótárazva a *. Azure/config* fájlban, beleértve az alkalmazás nevét, az erőforráscsoportot és a app Service tervet.

Az üzembe helyezés befejezése után váltson vissza a böngészőablakra, és nyissa meg a párbeszédpanelt `http://<app-name>.azurewebsites.net` . Frissítse az oldalt, amelynek a módosított üzenetnek kell megjelennie:

![Frissített minta Python-alkalmazás futtatása az Azure-ban](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> A Visual Studio Code hatékony bővítményeket biztosít a Python és a Azure App Service számára, ami leegyszerűsíti a Python-webalkalmazások App Serviceba való üzembe helyezésének folyamatát. További információ: [Python-alkalmazások üzembe helyezése a Visual Studio Code-ból App Service](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Naplók streamelése

Elérheti az alkalmazáson belül létrehozott konzol naplóit, valamint azt a tárolót, amelyben a fut. A naplók tartalmazzák az utasítások használatával generált kimeneteket `print` .

A naplók továbbításához futtassa a következő parancsot:

```azurecli
az webapp log tail
```

Frissítse az alkalmazást a böngészőben a konzol naplófájljainak létrehozásához, amelyek az alkalmazásnak küldött HTTP-kérelmeket leíró üzeneteket tartalmaznak. Ha nem jelenik meg azonnal a kimenet, próbálkozzon újra 30 másodperc múlva.

A naplófájlokat a böngészőből is ellenőrizheti `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .

Ha bármikor le szeretné állítani a naplózási adatfolyamot, írja be a **CTRL C billentyűt** + **C**.

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

A létrehozott alkalmazás kezeléséhez lépjen a <a href="https://portal.azure.com" target="_blank">Azure Portal</a> . Keresse meg és válassza ki a **app Services**.

![Navigáljon App Services a Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Válassza ki az Azure-alkalmazás nevét.

![Navigáljon a Python-alkalmazáshoz App Services a Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Az alkalmazás kiválasztása megnyitja az **Áttekintés** lapot, ahol olyan alapszintű felügyeleti feladatokat hajthat végre, mint a Tallózás, Leállítás, indítás, újraindítás és törlés.

![A Python-alkalmazás kezelése a Azure Portal Áttekintés oldalán](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

A App Service menü különböző lapokat biztosít az alkalmazás konfigurálásához.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Az erőforráscsoport neve például "appsvc_rg_Linux_CentralUS", a tartózkodási helytől függően. Ha az ingyenes F1-es csomagtól eltérő App Service SKU-t használ, ezek az erőforrások folyamatos költségekkel járnak (lásd a [app Service díjszabását](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Ha nem várható, hogy a jövőben szüksége lesz ezekre az erőforrásokra, törölje az erőforráscsoportot a következő parancs futtatásával:

```azurecli
az group delete
```

A parancs az *. Azure/config* fájlban gyorsítótárazott erőforráscsoport-nevet használja.

A parancs végrehajtása egy percet is igénybe vehet.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Python (Django) webalkalmazás és PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Felhasználói bejelentkezés hozzáadása egy Python-webalkalmazáshoz](../../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Python-alkalmazás futtatása egyéni tárolóban](tutorial-custom-docker-image.md)
