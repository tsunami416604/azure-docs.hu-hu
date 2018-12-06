---
title: Egyéni Docker-rendszerkép használata a Web App for Containers szolgáltatásban – Azure | Microsoft Docs
description: Egyéni Docker-rendszerkép használata a Web App for Containers szolgáltatásban.
keywords: azure app service, web app, linux, docker, container
services: app-service
documentationcenter: ''
author: SyntaxC4
manager: SyntaxC4
editor: ''
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 0811b8e81d8c2c4327885007685e15a8d2aea88e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963402"
---
# <a name="use-a-custom-docker-image-for-web-app-for-containers"></a>Egyéni Docker-rendszerkép használata a Web App for Containers szolgáltatásban

A [Web App for Containers](app-service-linux-intro.md) beépített Docker-rendszerképeket biztosít Linux rendszeren, a különböző verziók támogatásával (például: PHP 7.0 és Node.js 4.5). A Web App for Containers a Docker tárolótechnológiát használja beépített és egyéni rendszerképek szolgáltatásként nyújtott platformként történő üzemeltetéséhez. Ebből az oktatóanyagól megismerheti az egyéni Docker-rendszerképek készítésének és a Web App for Containers szolgáltatásban történő üzembe helyezésének módját. Ez a minta akkor hasznos, ha a beépített rendszerképek nem tartalmazzák a választott nyelvet, vagy ha az alkalmazás egy meghatározott konfigurációt igényel, amelyet a beépített rendszerképek nem tartalmaznak.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egyéni Docker-rendszerkép üzembe helyezése az Azure-ban
> * Környezeti változók konfigurálása a tároló futtatásához
> * A Docker-rendszerkép frissítése és ismételt üzembe helyezése
> * Csatlakozás a tárolóhoz SSH használatával
> * Privát Docker-rendszerkép üzembe helyezése az Azure-ban

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)
* Egy [Docker Hub-fiók](https://docs.docker.com/docker-id/)

## <a name="download-the-sample"></a>A minta letöltése

Egy terminálablakban futtassa a következő parancsot a mintaalkalmazás adattárának a helyi számítógépre történő klónozásához, majd lépjen át a mintakódot tartalmazó könyvtárba.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>A rendszerkép létrehozása a Docker-fájlból

A Git-adattárban tekintse meg a _Dockerfile_ nevű fájlt. Ez a fájl az alkalmazás futtatásához szükséges Python-környezetet írja le. Emellett a rendszerkép beállít egy [SSH](https://www.ssh.com/ssh/protocol/)-kiszolgálót a tároló és a gazdagép közötti biztonságos kommunikációhoz.

```docker
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

A Docker-rendszerkép létrehozásához futtassa a `docker build` parancsot, majd adjon meg egy nevet (_mydockerimage_) és egy címkét (_v1.0.0_). A _\<docker-id>_ helyére a Docker Hub-fiók azonosítóját írja.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

A parancs a következőhöz hasonló kimenetet eredményez:

```
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  5.558MB
Step 1/13 : FROM python:3.4
 ---> 9ff45ddb54e9
Step 2/13 : RUN mkdir /code
 ---> Using cache
 ---> f3f3ac01db0a
Step 3/13 : WORKDIR /code
 ---> Using cache
 ---> 38b32f15b442
.
.
.
Step 13/13 : ENTRYPOINT init.sh
 ---> Running in 5904e4c70043
 ---> e7cf08275692
Removing intermediate container 5904e4c70043
Successfully built e7cf08275692
Successfully tagged cephalin/mydockerimage:v1.0.0
```

Tesztelje a build működését a Docker-tároló futtatásával. Adja ki a [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) parancsot, és adja meg a rendszerkép nevét és címkéjét. Ügyeljen arra, hogy a `-p` argumentum használatával megadja a portot is.

```bash
docker run -p 2222:8000 <docker-ID>/mydockerimage:v1.0.0
```

Győződjön meg arról, hogy a webalkalmazás és a tároló megfelelően működik. Ehhez keresse fel a következő címet: `http://localhost:2222`.

![Webalkalmazás helyi tesztelése](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

> [!NOTE] 
> Az alkalmazástárolóhoz közvetlenül a helyi fejlesztési számítógépről is csatlakozhat SSH, SFTP vagy Visual Studio Code segítségével (Node.js-alkalmazások élő hibakereséséhez). További információkért tekintse meg a [Linuxon futó App Service-ben elérhető távoli hibakereséssel és SSH-val](https://aka.ms/linux-debug) kapcsolatos cikket.
>

## <a name="push-the-docker-image-to-docker-hub"></a>Docker-rendszerkép leküldése a Docker Hubba

A tárolójegyzék egy olyan alkalmazás, amely elérhetővé teszi a rendszerképeket, valamint rendszerkép- és tárolószolgáltatásokat is biztosít. A rendszerkép megosztásához le kell küldenie azt a tárolójegyzékbe. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> Leküldi a privát Docker-tárolójegyzékbe? Lásd a [bármelyik privát tárolójegyzék Docker-rendszerképének használatát](#use-a-docker-image-from-any-private-registry-optional) ismertető témakör nem kötelező lépéseit.

<!--## [Docker Hub](#tab/docker-hub)-->

A Docker Hub egy olyan tárolójegyzék a Docker-rendszerképek számára, amely saját nyilvános vagy privát adattárak üzemeltetését teszi lehetővé. Egyéni Docker-rendszerkép a nyilvános Docker Hubhoz történő leküldéséhez használja a [`docker push`](https://docs.docker.com/engine/reference/commandline/push/) parancsot, majd adja meg a teljes rendszerképnevet és a címkét. A teljes rendszerképnév és címke az alábbi mintához hasonlóan néz ki:

```
<docker-id>/image-name:tag
```

A rendszerkép leküldése előtt be kell jelentkeznie a Docker Hubra a [`docker login`](https://docs.docker.com/engine/reference/commandline/login/) paranccsal. A _\<docker-id >_ helyére a fiók nevét írja, majd adja meg a jelszót a konzol parancssorában.

```bash
docker login --username <docker-id>
```

A „login succeeded” (sikeres bejelentkezés) üzenet jelzi, hogy sikerült bejelentkeznie. Miután bejelentkezett, leküldheti a rendszerképet Docker Hubra a [`docker push`](https://docs.docker.com/engine/reference/commandline/push/) paranccsal.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

A parancs kimenetének megvizsgálásával győződjön meg a leküldés sikerességéről.

```
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
c33197c3f6d4: Pushed
ccd2c850ee43: Pushed
02dff2853466: Pushed
6ce78153632a: Pushed
efef3f03cc58: Pushed
3439624d77fb: Pushed
3a07adfb35c5: Pushed
2fcec228e1b7: Mounted from library/python
97d2d3bae505: Mounted from library/python
95aadeabf504: Mounted from library/python
b456afdc9996: Mounted from library/python
d752a0310ee4: Mounted from library/python
db64edce4b5b: Mounted from library/python
d5d60fc34309: Mounted from library/python
c01c63c6823d: Mounted from library/python
v1.0.0: digest: sha256:21f2798b20555f4143f2ca0591a43b4f6c8138406041f2d32ec908974feced66 size: 3676
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Alkalmazás üzembe helyezése az Azure-ban

Az Azure Web Apps használatával natív Linux-alkalmazásokat tehet elérhetővé a felhőben. A Web App for Containers szolgáltatás létrehozásához Azure CLI-parancsokat kell futtatnia, amelyek létrehoznak csoportot, majd egy szolgáltatáscsomagot, végül pedig magát a webalkalmazást. 

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-linux-app-service-plan"></a>Linux App Service-csomag létrehozása

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)] 

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

A Cloud Shellben az [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) paranccsal hozzon létre egy [webalkalmazást](app-service-linux-intro.md) a `myAppServicePlan` App Service-csomagban. Ne felejtse el egyedi alkalmazásnévre cserélni az _<appname>_, valamint a Docker-azonosítóra a _\<docker-ID>_ kifejezést.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --deployment-container-image-name <docker-ID>/mydockerimage:v1.0.0
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
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

A legtöbb Docker-rendszerképhez környezeti változók tartoznak, amelyeket konfigurálni kell. Ha valaki más által létrehozott, meglévő Docker-rendszerképet használ, előfordulhat, hogy a rendszerkép nem a 80-as portot használja. Az Azure-t a `WEBSITES_PORT` alkalmazásbeállítás használatával tájékoztathatja a rendszerkép által használt portról. A [jelen oktatóanyagban lévő Python-mintához](https://github.com/Azure-Samples/docker-django-webapp-linux) tartozó GitHub-oldalon az látható, hogy a `WEBSITES_PORT` értékét _8000_-re kell állítani.

Az alkalmazásbeállítások megadásához használja az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancsot a Cloud Shellben. Az alkalmazásbeállítások megkülönböztetik a kis-és nagybetűket, és szóközzel vannak elválasztva.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_PORT=8000
```

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> Privát Docker-tárolójegyzékből végez üzembe helyezést? Lásd a [bármelyik privát tárolójegyzék Docker-rendszerképének használatát](#use-a-docker-image-from-any-private-registry-optional) ismertető témakör nem kötelező lépéseit.

<!-- # [Docker Hub](#tab/docker-hub)-->

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

### <a name="test-the-web-app"></a>A webalkalmazás tesztelése

Tallózással győződjön meg arról, hogy a webalkalmazás működik (`http://<app_name>azurewebsites.net`). 

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

Miután módosította a Python-fájlt, és mentette azt, hozza létre újra, majd küldje le az új Docker-rendszerképet. Ezt követően indítsa újra a webalkalmazást a módosítások életbe léptetéséhez. Használja ugyanazokat a parancsokat, amelyeket ebben az oktatóanyagban korábban használt. Olvassa el a [rendszerkép Docker-fájlból történő létrehozását](#build-the-image-from-the-docker-file) és a [Docker-rendszerkép Docker Hubra történő leküldését](#push-the-docker-image-to-docker-hub) ismertető témakört. Tesztelje a webalkalmazást a [webalkalmazás tesztelését](#test-the-web-app) ismertető témakör utasításai szerint.

## <a name="connect-to-web-app-for-containers-using-ssh"></a>Csatlakozás a Web App for Containers szolgáltatáshoz az SSH használatával

Az SSH lehetővé teszi a tároló és az ügyfél közötti biztonságos kommunikációt. Ahhoz, hogy egy egyéni Docker-rendszerképe támogathassa az SSH-t, először egy Dockerfile-ban kell ezt engedélyeznie. Engedélyezi az SSH-t magában a Docker-fájlban. Az SSH-utasítások a mintául szolgáló dockerfile-hoz már hozzá lettek adva, így követheti ezeket az utasításokat az egyéni rendszerképe esetében is:

* Egy [RUN](https://docs.docker.com/engine/reference/builder/#run) utasítás, amely meghívja az `apt-get` parancsot, majd beállítja a rendszergazdafiók jelszavát erre: `"Docker!"`.

    ```docker
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Ez a konfiguráció nem tesz elérhetővé külső kapcsolatokat a tárolóhoz. Az SSH csak a Kudu/SCM webhelyen keresztül érhető el. A Kudu/SCM webhely a közzétételi hitelesítő adatokkal van hitelesítve.

* Egy [COPY](https://docs.docker.com/engine/reference/builder/#copy) utasítás, amely utasítja a Docker-motort az [sshd_config](https://man.openbsd.org/sshd_config) fájl az */etc/ssh/* könyvtárba történő másolására. A konfigurációs fájlnak [ezen az sshd_config fájlon](https://github.com/Azure-App-Service/node/blob/master/6.11.1/sshd_config) kell alapulnia.

    ```docker
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Az *sshd_config* fájlnak tartalmaznia kell a következő elemeket: 
    > * A `Ciphers` beállításnak tartalmaznia kell legalább egy elemet a következő listából: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * A `MACs` beállításnak tartalmaznia kell legalább egy elemet a következő listából: `hmac-sha1,hmac-sha1-96`.

* Egy [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) utasítás, amely elérhetővé teszi a tároló 2222-es portját. Bár a rendszergazdai szintű jelszó ismert, a 2222-es port nem érhető el az internet irányából. Ez egy belső port, amely csak egy privát virtuális hálózat hálózati hidas kapcsolatán belüli tárolók által érhető el. Ezt követően a parancsok átmásolják az SSH konfigurációs részleteit, majd elindítják az `ssh` szolgáltatást.

    ```docker
    EXPOSE 8000 2222
    ```

* Ügyeljen arra, hogy az [ssh szolgáltatás elindítását](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh) a /bin könyvtárban lévő héjparancsfájl használatával végezze.
 
    ```bash
    #!/bin/bash
    service ssh start
    ```
     
### <a name="open-ssh-connection-to-container"></a>A tároló SSH-kapcsolatának megnyitása

A Web App for Containers nem teszi lehetővé külső kapcsolatok létesítését a tárolóval. Az SSH csak a Kudu webhelyen keresztül érhető el, amelynek címe: `https://<app_name>.scm.azurewebsites.net`.

A keresse fel a következő címet: `https://<app_name>.scm.azurewebsites.net/webssh/host`, majd jelentkezzen be az Azure-fiókjával.

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

Gratulálunk! Sikeresen konfigurált egy egyéni Docker-rendszerképet a Web App for Containers szolgáltatáshoz.

## <a name="use-a-private-image-from-docker-hub-optional"></a>Docker Hubról származó privát rendszerkép használata (nem kötelező)

A [webalkalmazás létrehozása](#create-a-web-app) során megadott egy Docker Hubon lévő rendszerképet az `az webapp create` paranccsal. Ez nyilvános rendszerkép esetén használatos. Privát rendszerkép használatához be kell állítania a Docker-fiókja azonosítóját és jelszavát az Azure-webalkalmazásában.

A Cloud Shellben az `az webapp create` parancs után adja ki az [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) parancsot. Cserélje le az *\<app_name>* kifejezést, illetve a _\<docker-id>_ és a _\<password>_ esetén használja a Docker-azonosítóját és a jelszavát.

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

A parancs a következő JSON-sztringhez hasonló kimenetet jelenít meg, ezzel jelezve, hogy a konfiguráció módosítása sikeres volt:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "false"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<docker-id>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "DOCKER|<image-name-and-tag>"
  }
]
```

## <a name="use-a-docker-image-from-any-private-registry-optional"></a>Bármelyik privát tárolójegyzék Docker-rendszerképének használata (nem kötelező)

Ebben a szakaszban megismerheti, hogyan használható privát tárolójegyzékből származó Docker-rendszerkép a Web App for Containers szolgáltatásban. Példaként az Azure Container Registryt használjuk majd. Az egyéb privát tárolójegyzékek használatának lépései is hasonlóak. 

Az Azure Container Registry az Azure egy felügyelt, privát rendszerképek használatára szolgáló Docker-szolgáltatása. A környezetek típusa bármilyen lehet, beleértve a [Docker Swarm](https://docs.docker.com/engine/swarm/), a [Kubernetes](https://kubernetes.io/) és a Web App for Containers típust is. 

### <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása

A Cloud Shellben használja az [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) parancsot egy Azure Container Registry létrehozásához. Adja meg a nevet, az erőforráscsoportot és termékváltozatként a `Basic` értéket. Az elérhető termékváltozatok a következők: `Classic`, `Basic`, `Standard` és `Premium`.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

Tároló létrehozásakor a következő kimenet jön létre:

```
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="log-in-to-azure-container-registry"></a>Bejelentkezés az Azure Container Registrybe

A rendszerkép a tárolójegyzékbe küldéséhez meg kell adnia a hitelesítő adatokat, különben a tárolójegyzék nem fogadja el a leküldést. Ezeket a hitelesítő adatokat az [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) parancs használatával kérheti le a Cloud Shellben. 

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

A parancs megjeleníti a felhasználónévhez használható két jelszót.

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

A helyi terminálablakból jelentkezzen be az Azure Container Registrybe a `docker login` parancs használatával. A kiszolgálónév is szükséges a bejelentkezéshez. Használja a következő formátumot: `{azure-container-registry-name>.azurecr.io`. Adja meg a jelszót a konzol parancssorában.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Ellenőrizze, hogy a bejelentkezés sikeres volt-e. 

### <a name="push-an-image-to-azure-container-registry"></a>Rendszerkép leküldése az Azure Container Registrybe

> [!NOTE]
> Ha saját rendszerképet használ, lássa el azt címkével a következő módon:
> ```bash
> docker tag <azure-container-registry-name>.azurecr.io/mydockerimage
> ```

Küldje le a rendszerképet a `docker push` paranccsal. Lássa el a rendszerképet a tárolójegyzék nevével, amelyet a rendszerkép neve, majd a címke követ.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Az ACR-adattárak kilistázásával győződjön meg arról, hogy a leküldés sikeresen hozzáadott egy tárolót a tárolójegyzékhez. 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

A tárolójegyzék rendszerképeinek kilistázásával ellenőrizheti, hogy a tárolójegyzék tartalmazza-e a `mydockerimage` bejegyzést.

```json
[
  "mydockerimage"
]
```

### <a name="configure-web-app-to-use-the-image-from-azure-container-registry-or-any-private-registry"></a>A webalkalmazás beállítása az Azure Container Registryből (vagy bármely privát tárolójegyzékből) származó rendszerkép használatára

Beállíthatja a Web App for Containers szolgáltatást egy, az Azure Container Registryben eltárolt tároló futtatására. Az Azure Container Registry használata megegyezik bármilyen egyéb privát tárolójegyzékével, így ha saját, privát tárolójegyzéket kell használnia, a feladat elvégzéséhez szükséges lépések hasonlóak.

A Cloud Shellben futtassa az [`az acr credential show`](/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show) parancsot az Azure Container Registryhez tartozó felhasználónév és jelszó megjelenítéséhez. Másolja ki a felhasználónevet és a jelszavak egyikét, hogy felhasználhassa azokat a webalkalmazás konfigurálásához a következő lépésben.

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<registry-username>"
}
```

A Cloud Shellben futtassa az [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) parancsot az egyéni Docker-rendszerkép a webalkalmazáshoz való hozzárendeléséhez. Cserélje le az *\<app_name>*, a *\<docker-registry-server-url>*, a _\<registry-username>_ és a _\<password>_ értékét. Az Azure Container Registry esetében a *\<docker-registry-server-url>* formátuma a következő: `https://<azure-container-registry-name>.azurecr.io`. Ha bármilyen tárolójegyzéket használ a Docker Hubon kívül, a rendszerkép nevének a tárolójegyzék teljes tartománynevével (FQDN) kell kezdődnie. Az Azure Container Registry esetében ez így fog kinézni: `<azure-container-registry>.azurecr.io/mydockerimage`. 

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> `https://` használata szükséges a *\<docker-registry-server-url>* esetén.
>

A parancs a következő JSON-sztringhez hasonló kimenetet jelenít meg, ezzel jelezve, hogy a konfiguráció módosítása sikeres volt:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "<azure-container-registry-name>.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<registry-username>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Docker Python- és PostgreSQL-webalkalmazás létrehozása az Azure-ban](tutorial-python-postgresql-app.md)
