---
title: "Egyéni Docker lemezképet használ az tárolókat - Azure webalkalmazás számára |} Microsoft Docs"
description: "Hogyan használható az egyéni Docker-lemezkép webalkalmazás az tárolókat."
keywords: azure app service, web app, linux, docker, container
services: app-service
documentationcenter: 
author: SyntaxC4
manager: SyntaxC4
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 6a89db8b93f29c29e935afd94da727d2460af889
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="use-a-custom-docker-image-for-web-app-for-containers"></a>Egyéni Docker kép webalkalmazás a tárolók használata

[A tárolók webalkalmazás](app-service-linux-intro.md) biztosít beépített Docker képek Linux-támogatással rendelkező a különböző verziókat, például a PHP 7.0 és a Node.js 4.5. Web App az tárolókat használ a Docker-tároló technológia beépített képek, mind az egyéni lemezképek szolgáltatásként platformként. Ebben az oktatóanyagban elsajátíthatja egyéni Docker-lemezkép elkészítése és telepíteni kell a webalkalmazást az tárolókat. Ebben a mintában akkor hasznos, ha a beépített képek nem jelennek meg a választott nyelv, vagy ha az alkalmazás által igényelt, amelyet nem a beépített képekben adott konfigurációval.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* [Git](https://git-scm.com/downloads)
* Az aktív [Azure-előfizetés](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Docker](https://docs.docker.com/get-started/#setup)
* A [Docker Hub fiók](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>A minta letöltése

Egy terminálablakot futtassa a következő parancsot klónozza a sample app tárházat a helyi számítógépen, majd nyissa meg azt a könyvtárat a mintakódot tartalmazó.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>A lemezkép a Docker-fájlból

A Git-tárházban, vessen egy pillantást _Dockerfile_. Ezt a fájlt az alkalmazás futtatásához szükséges Python-környezetben ismerteti. Emellett a lemezképet állít be egy [SSH](https://www.ssh.com/ssh/protocol/) server, a tároló és a gazdagép közötti biztonságos kommunikációhoz.

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

A Docker-lemezkép létrehozásához futtassa a `docker build` parancsot, és adjon meg egy nevet, `mydockerimage`, és címkében `v1.0.0`. Cserélje le `<docker-id>` a Docker hubbal fiók azonosítóját.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

A parancs a következőhöz hasonló kimenetet hoz létre:

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

Tesztelje, hogy a build működik a Docker-tároló futtatásával. A probléma a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) parancsot, és átadni nevét és a lemezkép címke. Adja meg a portot a `-p` argumentum.

```bash
docker run -p 2222:8000 <docker-ID>/mydockerimage:v1.0.0
```

Ellenőrizze, hogy a webalkalmazás és a tároló vannak jól működő meghajtói tallózással `http://localhost:2222`.

![Teszt webalkalmazást helyileg](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="push-the-docker-image-to-docker-hub"></a>A Docker kép leküldése Docker Hub

A beállításjegyzék olyan alkalmazás, amely üzemelteti a képek és szolgáltatások kép és a tároló szolgáltatásokat biztosít. Ahhoz, hogy megoszthatja a képet, meg kell leküldéses a beállításjegyzékhez. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> Kérdez le, hogy a Magánfelhő Docker beállításjegyzék? A választható utasítások [a Docker-lemezkép leküldése titkos beállításjegyzék](#push-a-docker-image-to-private-registry-optional).

<!--## [Docker Hub](#tab/docker-hub)-->

Docker, amely lehetővé teszi a saját adattárak nyilvános vagy titkos beállításjegyzékbeli Docker lemezképek is. Egyéni Docker-lemezkép leküldése a nyilvános Docker-hubhoz, használja a [docker leküldéses](https://docs.docker.com/engine/reference/commandline/push/) parancsot, és a teljes lemezképet a felhasználónév és a címke. A teljes lemezképet nevét és az alábbi minta néz címke:

```
<docker-id>/image-name:tag
```

Ha még nem jelentkezett Docker-hubhoz, a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/) előtt lemezkép próbáló parancsot.

```bash
docker login --username <docker-id> --password <docker-hub-password>
```

"Sikeres bejelentkezés" üzenet jelzi, hogy van bejelentkezve. Miután bejelentkezett, a lemezkép leküldése Docker Hub használata a [docker leküldéses](https://docs.docker.com/engine/reference/commandline/push/) parancsot.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Győződjön meg arról, hogy az sikeres volt. a parancs megvizsgálásával leküldéses kimenetét.

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

## <a name="deploy-app-to-azure"></a>Alkalmazás telepítése az Azure-bA

Natív Linux alkalmazásaikat a felhőben tárolhatja, Azure Web Apps használatával. Hozzon létre egy webalkalmazást az tárolókat, hozzon létre egy csoportot, majd egy service-csomagot, végül a webes alkalmazás maga az Azure parancssori felület parancsait kell futtatnia. 

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-linux-app-service-plan"></a>Linux App Service-csomag létrehozása

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)] 

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

A Cloud Shellben az [az webapp create](/cli/azure/webapp#create) paranccsal hozzon létre egy [webalkalmazást](app-service-linux-intro.md) a `myAppServicePlan` App Service-csomagban. Ne felejtse el lecserélni `<app_name>` egy olyan egyedi alkalmazás nevét, és a < docker-azonosító > a a Docker-azonosító.

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

A legtöbb Docker képeknek környezeti változókat, amelyek úgy kell konfigurálni. Ha valaki más beépített meglévő Docker lemezképet használ, a lemezképet használhatják a 80-as portra. A lemezkép használatával által használt port kapcsolatos utasítja az Azure a `WEBSITES_PORT` Alkalmazásbeállítás. A GitHub-oldalon a [ebben az oktatóanyagban a Python minta](https://github.com/Azure-Samples/docker-django-webapp-linux) azt mutatja, hogy be kell állítani `WEBSITES_PORT` való _8000_.

Alkalmazásbeállítások beállításához használja a [az webapp config appsettings frissítése](/cli/azure/webapp/config/appsettings#update) a felhő rendszerhéj parancsot. Alkalmazásbeállítások a kis-és nagybetűket, és szóközökkel elválasztott.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_PORT=8000
```

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> Telepíti a saját Docker beállításjegyzékből? A választható utasítások [webalkalmazás konfigurálása a Docker-tároló titkos beállításjegyzékből használandó](#configure-web-app-to-use-docker-container-from-a-private-registry-optional).

<!-- # [Docker Hub](#tab/docker-hub)-->

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

### <a name="test-the-web-app"></a>A webes alkalmazás tesztelése

Győződjön meg arról, hogy a webalkalmazás működik-e azt (`http://<app_name>azurewebsites.net`). 

![Teszt web app port konfigurálása](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Webalkalmazás módosítása és helyezze üzembe újra

Nyissa meg a helyi Git-tárház app/templates/app/index.html. Keresse meg az első HTML-elem, és módosítsa úgy, hogy.

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">         
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>       
      </div>            
    </div>
  </nav> 
```

Miután módosította a Python-fájlt, és menti azt, és építse újra kell, az új Docker-lemezkép leküldéses. A webalkalmazás a módosítások életbe léptetéséhez indítsa újra. Használja ugyanazokat a parancsokat, amelyek ebben az oktatóanyagban korábban használt. Olvassa el a [a Docker-fájlból a lemezkép](#build-the-image-from-the-docker-file) és [a Docker kép leküldése Docker Hub](#push-the-docker-image-to-docker-hub). A webes alkalmazás tesztelése a utasításait követve [a webes alkalmazás tesztelése a](#test-the-web-app).

## <a name="connect-to-web-app-for-containers-using-ssh"></a>Csatlakozzon az SSH használatával tárolókat webalkalmazás

SSH lehetővé teszi, hogy a tároló és az ügyfél közötti biztonságos kommunikációhoz. Ahhoz, hogy egy egyéni Docker lemezképeinek SSH meg kell összeállítani, egy Dockerfile be. Engedélyezi a SSH magában a Docker fájlban. Az SSH-utasításokat már hozzáadott minta dockerfile, a saját egyéni rendszerképét is kövesse az alábbi utasításokat:

* A [futtatása](https://docs.docker.com/engine/reference/builder/#run) utasítást, amely behívja `apt-get`, majd a rendszergazdafiók jelszavának beállítása `"Docker!"`.

    ```docker
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Ez a konfiguráció nem teszi lehetővé a tároló külső kapcsolatot. SSH csak a Kudu/SCM webhelyen keresztül érhető el. A Kudu/SCM hely hitelesítése a közzétételi hitelesítő adatokkal.

* A [MÁSOLÁSI](https://docs.docker.com/engine/reference/builder/#copy) utasítást, amely arra utasítja a Docker-motorhoz másolása a [sshd_config](http://man.openbsd.org/sshd_config) fájlt a */stb/ssh/* könyvtár. A konfigurációs fájl alapján [a sshd_config fájl](https://github.com/Azure-App-Service/node/blob/master/6.11.1/sshd_config).

    ```docker
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > A *sshd_config* fájl a következő elemeket kell tartalmaznia: 
    > * `Ciphers`tartalmaznia kell legalább egy elemet a listában szereplő: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs`tartalmaznia kell legalább egy elemet a listában szereplő: `hmac-sha1,hmac-sha1-96`.

* Egy [teszi közzé](https://docs.docker.com/engine/reference/builder/#expose) utasítást, hogy tesz elérhetővé a tárolóban 2222 port. Bár a gyökér szintű jelszó is ismert, port 2222 nem érhető el az internetről. Egy belső port elérhető csak által a virtuális magánhálózat híd hálózati tárolókra is. Ezt követően a parancsok SSH konfigurációs részletek másolása, és indítsa el a `ssh` szolgáltatás.

    ```docker
    EXPOSE 8000 2222
    ```

* Ügyeljen arra, hogy [indítsa el az ssh szolgáltatás ](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh) /bin könyvtárban rendszerhéj parancsfájl használatával.
 
    ```bash
    #!/bin/bash
    service ssh start
    ```
     
### <a name="open-ssh-connection-to-container"></a>Tároló SSH-kapcsolat

Webes alkalmazás a tárolók nem engedélyezi a külső kapcsolatokat a tárolóhoz. Az SSH egy csak a a Kudu helyre, amely elérhető a `https://<app_name>.scm.azurewebsites.net`.

Szeretne csatlakozni, tallózással `https://<app_name>.scm.azurewebsites.net/webssh/host` és jelentkezzen be az Azure-fiókjával.

Ezután átirányítja egy interaktív konzolt olyan oldalán. 

Kezdésként érdemes lehet ellenőrizze, hogy egyes alkalmazások futnak-e a tárolóban. Vizsgálja meg a tároló és a folyamatok futtatásával ellenőrizze, ki a `top` parancsot a parancssorba.

```bash
top
```

A `top` parancs közzététele tároló minden futó folyamatot.

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

Gratulálunk! A webalkalmazás az tárolókat konfigurálta az egyéni Docker-lemezkép.

## <a name="use-a-private-image-from-docker-hub-optional"></a>(Választható) Docker hubról titkos lemezkép

A [webalkalmazás létrehozása](#create-a-web-app), Docker csomópontjában megadott lemezkép a `az webapp create` parancsot. Ez az elég jó egy nyilvános lemezképet. A saját lemezképek használatához szüksége a Docker Fiókazonosítót és a jelszó beállítása az Azure web app alkalmazásban.

A felhő rendszerhéj kövesse a `az webapp create` parancsot [az webapp tároló konfiguráció](/cli/azure/webapp/config/container#az_webapp_config_container_set). Cserélje le  *\<alkalmazás_neve >*, és is _< docker-azonosító >_ és  _<password>_  a Docker Azonosítóját és jelszavát.

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

A parancs azt jelzi, hogy a következő JSON-karakterláncban, megjeleníti, hogy a konfiguráció módosítása sikeres volt hasonló kimenetet:

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

## <a name="use-a-docker-image-from-any-private-registry-optional"></a>A Docker-lemezkép használata a beállításjegyzékből. minden személyes (nem kötelező)

Ebben a szakaszban megismerheti, hogyan használható a Docker-lemezkép Web App alkalmazásban egy titkos beállításjegyzékből tárolók, és Azure tároló beállításjegyzék használ példaként. A más személyes nyilvántartó használatához vezető lépések hasonlóak. 

Azure tároló beállításjegyzék egy olyan felügyelt Docker-szolgáltatás az Azure-ból titkos lemezképek tárolásához. Lehet, hogy a központi telepítések bármilyen, beleértve a [Docker Swarm](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io/), és a Web App az tárolókat. 

### <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása

A felhő rendszerhéj használata a [az acr létrehozása](https://docs.microsoft.com/cli/azure/acr#az_acr_create) parancs segítségével hozza létre az Azure-tároló beállításkulcs. Adjon át a neve, az erőforráscsoport, és `Basic` a termékváltozat. Elérhető termékváltozatok vannak `Classic`, `Basic`, `Standard`, és `Premium`.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

Tároló létrehozása a következő kimenetet hoz létre:

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

### <a name="log-in-to-azure-container-registry"></a>Jelentkezzen be Azure-tárolót beállításjegyzék

Ahhoz, hogy a lemezkép leküldése a beállításjegyzéket, hogy hitelesítő adatait kell megadnia, a beállításjegyzék elfogadják a leküldéses. Ezek a hitelesítő adatok használatával kérheti le a [az acr megjelenítése](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show) a felhő rendszerhéj parancsot. 

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

A parancs megjeleníti a felhasználónévhez használt két jelszavak.

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

Az a helyi terminálablakot, jelentkezzen be az Azure-tároló beállításjegyzék használatával a `docker login` parancsot. A kiszolgáló nevét kell-e jelentkezni. A formátum használata `{azure-container-registry-name>.azurecr.io`.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username> --password <password> 
```

Győződjön meg arról, hogy a bejelentkezés sikeres volt. 

### <a name="push-an-image-to-azure-container-registry"></a>A képfájl leküldése Azure tároló beállításjegyzék

> [!NOTE]
> Saját rendszerkép használata, címkét a lemezképet az alábbiak szerint:
> ```bash
> docker tag <azure-container-registry-name>.azurecr.io/mydockerimage
> ```

A lemezkép használatával leküldéses a `docker push` parancsot. Címke a kép és a beállításjegyzék, a lemezkép neve, majd a címke nevét.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Győződjön meg arról, hogy a leküldéses sikeresen hozzáadva egy tároló a beállításjegyzék ehhez listázza az ACR tárházak találhatók. 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

A beállításjegyzékben a képek listázása megerősíti, hogy `mydockerimage` a rendszerleíró adatbázis.

```json
[
  "mydockerimage"
]
```

### <a name="configure-web-app-to-use-the-image-from-azure-container-registry-or-any-private-registry"></a>A lemezképen Azure tároló beállításjegyzék (vagy bármely titkos beállításjegyzék) webalkalmazás konfigurálása

Web App az tárolókat beállíthatja, hogy fut az Azure-tároló beállításjegyzékben tárolt tárolója. Csakúgy, mint bármely titkos beállításjegyzék használ, így ha meg szeretné használni a saját titkos beállításjegyzék, a feladat végrehajtásához szükséges lépések hasonlóak az Azure-tároló beállításjegyzékkel van.

A felhő Rendszerhéjában futtassa az [az acr hitelesítő adatok megjelenítése](/cli/azure/acr/credential#az_acr_credential_show) Azure tároló beállításjegyzék a felhasználónév és jelszó megjelenítéséhez. Másolja a felhasználónév és a jelszavak egyike, hogy használhassa a webalkalmazás konfigurálása a következő lépésben.

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

A felhő Rendszerhéjában futtassa a [az webapp tároló konfiguráció](/cli/azure/webapp/config/container#az_webapp_config_container_set) parancs a Docker egyéni rendszerkép hozzárendelése a webalkalmazást. Cserélje le  *\<alkalmazás_neve >*,  *\<docker-beállításjegyzék-kiszolgáló-URL-címe >*,  _\<beállításjegyzék-username >_, és  _\<jelszó >_. Az Azure-tároló beállításjegyzék  *\<docker-beállításjegyzék-kiszolgáló-URL-címe >* formátumú `https://<azure-container-registry-name>.azurecr.io`. 

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> `https://`a szükséges  *\<docker-beállításjegyzék-kiszolgáló-URL-címe >*.
>

A parancs azt jelzi, hogy a következő JSON-karakterláncban, megjeleníti, hogy a konfiguráció módosítása sikeres volt hasonló kimenetet:

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure-ban Docker Python és PostgreSQL webalkalmazás létrehozása](tutorial-docker-python-postgresql-app.md)
