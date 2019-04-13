---
title: Egyéni rendszerkép készítése és a Web App for containers szolgáltatásban – Azure App Service-ben |} A Microsoft Docs
description: Egyéni Docker-rendszerkép használata a Web App for Containers szolgáltatásban.
keywords: azure app service, web app, linux, docker, container
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8463ffcb9d9983ff435c01f75dd48f68bde31767
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545602"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>Oktatóanyag: Egyéni rendszerkép készítése és futtatása az App Service-ben privát tárolójegyzékből származó

[App Service-ben](app-service-linux-intro.md) biztosít beépített Docker-rendszerképek Linux támogatással a különböző verziók, például a PHP 7.0 és Node.js 4.5. Az App Service a Docker tárolótechnológiát használja beépített és egyéni rendszerképek tárolásához, egy szolgáltatásként nyújtott platformon. Ebben az oktatóanyagban megismerheti, hogyan elkészíthet egy egyéni rendszerképet, és futtassa az App Service-ben. Ez a minta akkor hasznos, ha a beépített rendszerképek nem tartalmazzák a választott nyelvet, vagy ha az alkalmazás egy meghatározott konfigurációt igényel, amelyet a beépített rendszerképek nem tartalmaznak.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egyéni rendszerkép üzembe helyezése egy privát tárolójegyzékben
> * Az egyéni rendszerkép futtatása az App Service-ben
> * Környezeti változók konfigurálása
> * Frissítése és ismételt üzembe helyezése a rendszerkép
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

A Git-adattárban tekintse meg a _Dockerfile_ nevű fájlt. Ez a fájl az alkalmazás futtatásához szükséges Python-környezetet írja le. Emellett a rendszerkép beállít egy [SSH](https://www.ssh.com/ssh/protocol/)-kiszolgálót a tároló és a gazdagép közötti biztonságos kommunikációhoz.

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
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

A Docker-rendszerképet készíthet a `docker build` parancsot.

```bash
docker build --tag mydockerimage .
```

Tesztelje a build működését a Docker-tároló futtatásával. Adja ki a [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) parancsot, és adja meg a rendszerkép nevét és címkéjét. Ügyeljen arra, hogy a `-p` argumentum használatával megadja a portot is.

```bash
docker run -p 8000:8000 mydockerimage
```

Győződjön meg arról, hogy a webalkalmazás és a tároló megfelelően működik. Ehhez keresse fel a következő címet: `http://localhost:8000`.

![Webalkalmazás helyi tesztelése](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Alkalmazás üzembe helyezése az Azure-ban

Hozzon létre egy alkalmazást, amely az újonnan létrehozott rendszerképet használ, hozzon létre egy erőforráscsoportot, leküldi a lemezképet, és ezután webalkalmazást hoz létre az App Service csomag futtatható Azure CLI-parancsokat futtassa.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása

A Cloud Shellben használja az [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) parancsot egy Azure Container Registry létrehozásához.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Jelentkezzen be az Azure Container Registrybe

Rendszerkép leküldése a beállításjegyzékbe, a privát beállításjegyzék hitelesíteni kell. A Cloud shellben használja az [ `az acr show` ](/cli/azure/acr?view=azure-cli-latest#az-acr-show) parancs használatával kérje le a hitelesítő adatokat a beállításjegyzékből hozott létre.

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

A kimenet tárja fel a felhasználó neve mellett két jelszót.

```json
<
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
  "username": "<registry-username>"
}
```

A helyi terminálablakból jelentkezzen be az Azure Container Registry használatával a `docker login` parancsot, az alábbi példában látható módon. Cserélje le  *\<azure-tároló-beállításjegyzék-neve >* és  *\<registry-username >* a beállításjegyzék értékekkel. Amikor a rendszer kéri, írja be az előző lépésben a jelszavak egyikét.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Győződjön meg arról, hogy a bejelentkezés sikeres.

### <a name="push-image-to-azure-container-registry"></a>Rendszerkép leküldése az Azure Container Registrybe

A helyi rendszerképet címkézése az Azure Container Registry esetében. Példa:
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Küldje le a rendszerképet a `docker push` paranccsal. Lássa el a rendszerképet a tárolójegyzék nevével, amelyet a rendszerkép neve, majd a címke követ.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Vissza a Cloud Shellben a ellenőrizze, hogy a leküldéses sikeres.

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

A következő kimenetet kell kapnia.

```json
[
  "mydockerimage"
]
```

### <a name="create-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Webalkalmazás létrehozása

A Cloud Shellben az [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) paranccsal hozzon létre egy [webalkalmazást](app-service-linux-intro.md) a `myAppServicePlan` App Service-csomagban. Cserélje le  _\<alkalmazás-neve >_ egy egyedi névre, és  _\<azure-tároló-beállításjegyzék-neve >_ a beállításjegyzék nevével.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló eredményeket jelenít meg:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-registry-credentials-in-web-app"></a>A web App alkalmazásban a tárolójegyzék hitelesítő adatainak konfigurálása

Az App Service, kérje le a saját rendszerképét, információkra lesz szüksége a beállításjegyzék és a lemezkép. A Cloud shellben megadnia őket a [ `az webapp config container set` ](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) parancsot. Cserélje le  *\<alkalmazás-neve >*,  *\<azure-tároló-beállításjegyzék-neve >*,  _\<registry-username >_, és  _\<jelszó >_.

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> Docker Hub, eltérő beállításjegyzék használatakor `--docker-registry-server-url` formátumban kell lenniük `https://` a beállításjegyzék teljesen minősített tartományneve követ.
>

### <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

A legtöbb Docker-rendszerképek egyéni környezeti változókat, például a nem a 80-as portot használja. App Service-ben, amely a rendszerkép használatával által használt portra vonatkozó mondja el a `WEBSITES_PORT` alkalmazásbeállítást. A [jelen oktatóanyagban lévő Python-mintához](https://github.com/Azure-Samples/docker-django-webapp-linux) tartozó GitHub-oldalon az látható, hogy a `WEBSITES_PORT` értékét _8000_-re kell állítani.

Az alkalmazásbeállítások megadásához használja az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancsot a Cloud Shellben. Az alkalmazásbeállítások megkülönböztetik a kis-és nagybetűket, és szóközzel vannak elválasztva.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>A webalkalmazás tesztelése

Tallózással győződjön meg arról, hogy a webalkalmazás működik (`http://<app-name>.azurewebsites.net`).

> [!NOTE]
> Az első alkalommal fér hozzá az alkalmazást, eltarthat egy ideig, mert az App Service-ben kérje le a teljes kép kell. Ha a böngésző időkorlátja lejár, csak frissítse az oldalt.

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

Miután módosította a Python-fájlt, és mentette azt, hozza létre újra, majd küldje le az új Docker-rendszerképet. Ezt követően indítsa újra a webalkalmazást a módosítások életbe léptetéséhez. Használja ugyanazokat a parancsokat, amelyeket ebben az oktatóanyagban korábban használt. Olvassa el [állítsa össze a rendszerképet a Docker-fájlból](#build-the-image-from-the-docker-file) és [leküldéses rendszerképet az Azure Container Registrybe](#push-image-to-azure-container-registry). Tesztelje a webalkalmazást a [webalkalmazás tesztelését](#test-the-web-app) ismertető témakör utasításai szerint.

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>SSH-kapcsolatok engedélyezése

Az SSH lehetővé teszi a tároló és az ügyfél közötti biztonságos kommunikációt. Ahhoz, hogy a tároló SSH-kapcsolatot, az egyéni rendszerkép kell konfigurálni hozzá. Vessünk egy pillantást a minta tárház, amely már rendelkezik a szükséges beállításokat.

* Az a [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile), a következő kódot az SSH-kiszolgálót telepíti, és beállítja a bejelentkezési hitelesítő adataival.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Ez a konfiguráció nem tesz elérhetővé külső kapcsolatokat a tárolóhoz. Az SSH csak a Kudu/SCM webhelyen keresztül érhető el. A Kudu/SCM webhely hitelesítése az Azure-fiókkal.

* A [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) másolja a [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config file in the repository) , a */etc/ssh/* könyvtár.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* A [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) közzéteszi a tároló 2222-es portját. Ez egy belső port, amely csak egy privát virtuális hálózat hálózati hidas kapcsolatán belüli tárolók által érhető el. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* A [bejegyzés parancsfájl](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5) elindítja az SSH-kiszolgálót.

      ```bash
      #!/bin/bash
      service ssh start
    ```

### Open SSH connection to container

SSH connection is available only through the Kudu site, which is accessible at `https://<app-name>.scm.azurewebsites.net`.

To connect, browse to `https://<app-name>.scm.azurewebsites.net/webssh/host` and sign in with your Azure account.

You are then redirected to a page displaying an interactive console.

You may wish to verify that certain applications are running in the container. To inspect the container and verify running processes, issue the `top` command at the prompt.

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

Gratulálunk! Az App Service-ben konfigurált egyéni Linux-tárolóban.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>További lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * Egyéni rendszerkép üzembe helyezése egy privát tárolójegyzékben
> * Az egyéni rendszerkép futtatása az App Service-ben
> * Környezeti változók konfigurálása
> * Frissítése és ismételt üzembe helyezése a rendszerkép
> * Diagnosztikai naplók elérése
> * Csatlakozás a tárolóhoz SSH használatával

Folytassa a következő oktatóanyaggal, megtudhatja, hogyan képezhet le egyedi DNS-nevet az alkalmazáshoz.

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni DNS-név leképezése az alkalmazás](../app-service-web-tutorial-custom-domain.md)

Vagy tekintse meg az egyéb erőforrások:

> [!div class="nextstepaction"]
> [Egyéni tároló konfigurálása](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Többtárolós WordPress-alkalmazás](tutorial-multi-container-app.md)
