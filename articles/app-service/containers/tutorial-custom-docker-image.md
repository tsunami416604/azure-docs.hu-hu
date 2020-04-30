---
title: 'Oktatóanyag: egyéni rendszerkép létrehozása és futtatása'
description: Megtudhatja, hogyan hozhat létre olyan egyéni linuxos rendszerképeket, amelyek Azure App Service futtathatók, üzembe helyezhetők az Azure Container-jegyzékekben, és futtathatók a App Service.
keywords: azure app service, web app, linux, docker, container
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 2609ff908b3c2f872cb63d3dcd7dcd481d316484
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82085858"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>Oktatóanyag: egyéni rendszerkép létrehozása és App Service futtatása privát beállításjegyzékből

A [app Service](app-service-linux-intro.md) beépített Docker-rendszerképeket biztosít a Linuxon, és bizonyos verziókat, például a PHP 7,3-t és a Node. js 10,14-t támogatja. A App Service a Docker-tároló technológiáját használja a beépített rendszerképek és az Egyéni rendszerképek szolgáltatásként való üzemeltetéséhez. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egyéni rendszerképet, és hogyan futtathatja App Serviceban. Ez a minta akkor hasznos, ha a beépített rendszerképek nem tartalmazzák a választott nyelvet, vagy ha az alkalmazás egy meghatározott konfigurációt igényel, amelyet a beépített rendszerképek nem tartalmaznak.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egyéni rendszerkép üzembe helyezése egy privát tároló beállításjegyzékében
> * Az egyéni rendszerkép futtatása App Service
> * Környezeti változók konfigurálása
> * A rendszerkép frissítése és újbóli üzembe helyezése
> * Diagnosztikai naplók elérése
> * Csatlakozás a tárolóhoz SSH használatával

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)

## <a name="download-the-sample"></a>A minta letöltése

Egy terminálablakban futtassa a következő parancsot a mintaalkalmazás adattárának a helyi számítógépre történő klónozásához, majd lépjen át a mintakódot tartalmazó könyvtárba.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>A rendszerkép létrehozása a Docker-fájlból

A Git-adattárban tekintse meg a _Dockerfile_ nevű fájlt. Ez a fájl az alkalmazás futtatásához szükséges Python-környezetet írja le. Emellett a rendszerkép beállít egy [SSH](https://www.ssh.com/ssh/protocol/)-kiszolgálót a tároló és a gazdagép közötti biztonságos kommunikációhoz. A _Docker_ `ENTRYPOINT ["init.sh"]`utolsó sora, amely elindítja `init.sh` az SSH-szolgáltatást és a Python-kiszolgálót.

```Dockerfile
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#service SSH start
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Hozza létre a Docker-rendszerképet a `docker build` paranccsal.

```bash
docker build --tag mydockerimage .
```

Tesztelje a build működését a Docker-tároló futtatásával. Adja ki [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) a parancsot, és adja át a rendszerkép nevét és címkéjét. Ügyeljen arra, hogy a `-p` argumentum használatával megadja a portot is.

```bash
docker run -p 8000:8000 mydockerimage
```

Győződjön meg arról, hogy a webalkalmazás és a tároló megfelelően működik. Ehhez keresse fel a következő címet: `http://localhost:8000`.

![Webalkalmazás helyi tesztelése](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Alkalmazás üzembe helyezése az Azure-ban

Az imént létrehozott rendszerképet használó alkalmazás létrehozásához futtassa az Azure CLI-parancsokat, amelyek létrehoznak egy erőforráscsoportot, leküldi a rendszerképet, majd létrehozza a App Service terv webalkalmazást a futtatásához.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása

A Cloud Shell a [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) parancs használatával hozzon létre egy Azure Container Registry.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Bejelentkezés Azure Container Registry

Egy rendszerkép a beállításjegyzékbe való leküldéséhez hitelesítenie kell magát a privát beállításjegyzékben. A Cloud Shell a [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) parancs használatával kérje le a hitelesítő adatokat a létrehozott beállításjegyzékből.

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

A kimenet két jelszót mutat be a felhasználónévvel együtt.

<pre>
{
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "&lt;registry-username&gt;"
}
</pre>

A helyi terminál ablakban jelentkezzen be a Azure Container Registry a `docker login` paranccsal, az alábbi példában látható módon. Cserélje le * \<az Azure-Container-Registry-Name>* és * \<a Registry-username>* értéket a beállításjegyzék értékeire. Ha a rendszer kéri, írja be az előző lépésben szereplő jelszavak egyikét.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Győződjön meg arról, hogy a bejelentkezés sikeres.

### <a name="push-image-to-azure-container-registry"></a>Rendszerkép leküldése az Azure Container Registrybe

A Azure Container Registry helyi rendszerképének címkézése. Például:
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Küldje le a rendszerképet a `docker push` paranccsal. Lássa el a rendszerképet a tárolójegyzék nevével, amelyet a rendszerkép neve, majd a címke követ.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

A Cloud Shell ismét ellenőrizze, hogy a leküldése sikeres-e.

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

A következő kimenetnek kell megjelennie.

<pre>
[
  "mydockerimage"
]
</pre>

### <a name="create-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Webalkalmazás létrehozása

A Cloud Shellben az [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) paranccsal hozzon létre egy [webalkalmazást](app-service-linux-intro.md) a `myAppServicePlan` App Service-csomagban. Cserélje le _ \<az App-Name>t_ egy egyedi alkalmazás nevére, és _ \<az Azure-Container-Registry-Name>_ a beállításjegyzék nevével.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló eredményeket jelenít meg:

<pre>
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="configure-registry-credentials-in-web-app"></a>Beállításjegyzékbeli hitelesítő adatok konfigurálása a webalkalmazásban

A privát rendszerkép lekéréséhez a beállításjegyzékről és a rendszerképekről is információt kell App Service. A Cloud Shell adja meg a parancsot a [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) paranccsal. Cserélje le * \<az App-Name>*, az * \<Azure-Container-Registry-Name>*, a _ \<Registry-username>_ és _ \<a Password>_.

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> Ha a Docker hub-tól eltérő beállításjegyzéket `--docker-registry-server-url` használ, a nevet `https://` a beállításjegyzék teljes tartománynevének megfelelően kell formázni.
>

### <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

A legtöbb Docker-rendszerkép egyéni környezeti változókat használ, például egy 80-es portot. App Service arról, hogy milyen portot használ a rendszerkép az `WEBSITES_PORT` alkalmazás beállításával. A [jelen oktatóanyagban lévő Python-mintához](https://github.com/Azure-Samples/docker-django-webapp-linux) tartozó GitHub-oldalon az látható, hogy a `WEBSITES_PORT` értékét _8000_-re kell állítani.

Az Alkalmazásbeállítások megadásához használja a [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancsot a Cloud shell. Az alkalmazásbeállítások megkülönböztetik a kis-és nagybetűket, és szóközzel vannak elválasztva.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>A webalkalmazás tesztelése

Tallózással győződjön meg arról, hogy a webalkalmazás működik (`http://<app-name>.azurewebsites.net`).

> [!NOTE]
> Amikor először fér hozzá az alkalmazáshoz, eltarthat egy ideig, mert App Service le kell kérnie a teljes képet. Ha a böngésző túllépi az időkorlátot, csak frissítse az oldalt.

![Webalkalmazás portkonfigurációjának tesztelése](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Webalkalmazás módosítása és ismételt üzembe helyezése

A helyi Git-adattárban nyissa meg az app/templates/app/index.html fájlt. Keresse meg az első HTML-elemet, és módosítsa a következőre:

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
      </div>
    </div>
  </nav>
```

Miután módosította a Python-fájlt, és mentette azt, hozza létre újra, majd küldje le az új Docker-rendszerképet. Ezt követően indítsa újra a webalkalmazást a módosítások életbe léptetéséhez. Használja ugyanazokat a parancsokat, amelyeket ebben az oktatóanyagban korábban használt. A [rendszerképet a Docker-fájlból is felépítheti](#build-the-image-from-the-docker-file) , és [leküldheti a rendszerképet Azure Container Registry](#push-image-to-azure-container-registry). Tesztelje a webalkalmazást a [webalkalmazás tesztelését](#test-the-web-app) ismertető témakör utasításai szerint.

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>SSH-kapcsolatok engedélyezése

Az SSH lehetővé teszi a tároló és az ügyfél közötti biztonságos kommunikációt. A tárolóhoz való SSH-kapcsolatok engedélyezéséhez konfigurálnia kell az egyéni rendszerképet. Vessünk egy pillantást a minta adattárra, amely már rendelkezik a szükséges konfigurációval.

* A [Docker](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile)a következő kód telepíti az SSH-kiszolgálót, és beállítja a bejelentkezési hitelesítő adatokat is.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Ez a konfiguráció nem tesz elérhetővé külső kapcsolatokat a tárolóhoz. Az SSH csak a Kudu/SCM webhelyen keresztül érhető el. Az kudu/SCM-hely hitelesítése az Azure-fiókkal történik.

* A [Docker](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) átmásolja a tárházban lévő [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config) fájlt a */etc/ssh/* könyvtárba.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* A [Docker](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) a tárolóban a 2222-es portot teszi elérhetővé. Ez egy belső port, amely csak egy privát virtuális hálózat hálózati hidas kapcsolatán belüli tárolók által érhető el. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* A [bejegyzési parancsfájl](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5) elindítja az SSH-kiszolgálót.

    ```bash
    #!/bin/bash
    service ssh start
    ```

### <a name="open-ssh-connection-to-container"></a>A tároló SSH-kapcsolatának megnyitása

Az SSH-kapcsolat csak a kudu webhelyen érhető el, amely elérhető a `https://<app-name>.scm.azurewebsites.net`következő címen:.

A keresse fel a következő címet: `https://<app-name>.scm.azurewebsites.net/webssh/host`, majd jelentkezzen be az Azure-fiókjával.

Ezután a rendszer átirányítja egy interaktív konzolt tartalmazó oldalra.

Érdemes lehet ellenőrizze, hogy bizonyos alkalmazások futnak-e a tárolóban. A tároló és a futó folyamatok ellenőrzéséhez adja ki a `top` parancsot a parancssorban.

```bash
top
```

Az `top` parancs minden, a tárolóban futó folyamatot megjelenít.

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

Gratulálunk! Egyéni Linux-tárolót konfigurált a App Serviceban.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>További lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * Egyéni rendszerkép üzembe helyezése egy privát tároló beállításjegyzékében
> * Az egyéni rendszerkép futtatása App Service
> * Környezeti változók konfigurálása
> * A rendszerkép frissítése és újbóli üzembe helyezése
> * Diagnosztikai naplók elérése
> * Csatlakozás a tárolóhoz SSH használatával

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan képezhető le egyéni DNS-név az alkalmazáshoz.

> [!div class="nextstepaction"]
> [Oktatóanyag: egyéni DNS-név leképezése az alkalmazáshoz](../app-service-web-tutorial-custom-domain.md)

Vagy tekintse meg a többi erőforrást:

> [!div class="nextstepaction"]
> [Egyéni tároló konfigurálása](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Multi-Container WordPress-alkalmazás](tutorial-multi-container-app.md)
