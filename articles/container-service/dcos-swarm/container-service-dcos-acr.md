---
title: (ELAVULT) Az ACR használata Azure DC/OS-fürt
description: Azure Container Registry használata DC/OS-fürttel az Azure Container Service szolgáltatásban
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 03/23/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: 8319f2f5405271679d0c11d4ac68492cdec8fc14
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100121"
---
# <a name="deprecated-use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>(ELAVULT) ACR használata DC/OS-fürttel az alkalmazás központi telepítése

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Ebben a cikkben az Azure Container Registry a DC/OS-fürtökkel való használatával ismerkedünk meg. Az ACR használatával a tárolórendszerképeket privát módon tárolhatja és kezelheti. Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Azure Container Registry üzembe helyezése (szükség esetén)
> * ACR-hitelesítés konfigurálása DC/OS-fürtön
> * Rendszerkép feltöltése az Azure Container Registrybe
> * Tárolórendszerkép futtatása az Azure Container Registryből

Az oktatóanyagban ismertetett lépések végrehajtásához szüksége lesz egy ACS DC/OS-fürtre. Amennyiben szükséges, [ezzel a mintaszkripttel](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) létrehozhat egyet.

Az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha frissíteni szeretne, tekintse meg [az Azure CLI telepítését ismertető]( /cli/azure/install-azure-cli) szakaszt. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-azure-container-registry"></a>Azure Container Registry üzembe helyezése

Amennyiben szükséges, hozzon létre egy Azure tárolóregisztrációs adatbázist az [az acr create](/cli/azure/acr#az-acr-create) paranccsal. 

Az alábbi példa egy véletlenszerűen elnevezett beállításjegyzéket hoz létre. A beállításjegyzékhez egy rendszergazdai fiók is konfigurálva lesz az `--admin-enabled` argumentum használatával.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic
```

A beállítás létrehozása után az Azure parancssori felület az alábbihoz hasonló adatokat jelenít meg. Jegyezzük fel a `name` és a `loginServer` értékét, ezeket a későbbi lépések során használni fogjuk.

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T03:40:56.511597+00:00",
  "id": "/subscriptions/f2799821-a08a-434e-9128-454ec4348b10/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry23489",
  "location": "eastus",
  "loginServer": "mycontainerregistry23489.azurecr.io",
  "name": "myContainerRegistry23489",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr034017"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Kérje le a tárolóregisztrációs adatbázis hitelesítő adatait az [az acr credential show](/cli/azure/acr/credential) paranccsal. A `--name` helyére írja be az előző lépésben feljegyzett értéket. Jegyezze fel az egyik jelszót, mivel egy későbbi lépésben szükség lesz rá.

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

Az Azure Container Registryvel kapcsolatos további információkért lásd: [A privát Docker-tárolójegyzékek bemutatása](../../container-registry/container-registry-intro.md). 

## <a name="manage-acr-authentication"></a>Az ACR-hitelesítés kezelése

A rendszerképek a privát jegyzékekbe való hagyományos küldése vagy az onnan való lekérése során először hitelesítést kell végezni a beállításjegyzékkel. Ez a `docker login` parancs használatával lehetséges bármely olyan ügyfélen, amelynek hozzá kell férnie a privát jegyzékhez. Mivel a DC/OS-fürt számos csomópontot tartalmazhat, amelyek mindegyikét hitelesíteni kell az ACR-rel, célszerű ezt a folyamatot automatizálni az egyes csomópontokon. 

### <a name="create-shared-storage"></a>Megosztott tároló létrehozása

Ez a folyamat egy, a fürt minden egyes csomópontjához csatlakoztatott Azure-fájlmegosztást alkalmaz. Ha még nem állított volna be megosztott tárolót, tekintse meg a [fájlmegosztás a DC/OS-fürtökben való beállítását](container-service-dcos-fileshare.md) ismertető szakaszt.

### <a name="configure-acr-authentication"></a>Az ACR-hitelesítés konfigurálása

Először kérje le a DC/OS főkiszolgáló teljes tartománynevét, és tárolja egy változóban.

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

Hozzon létre SSH-kapcsolatot a DC/OS-alapú fürt főkiszolgálójával (vagy az első főkiszolgálójával). Amennyiben a fürt eredeti létrehozása alkalmával nem alapértelmezett értéket használt, frissítse a felhasználónevet.

```azurecli-interactive
ssh azureuser@$FQDN
```

Futtassa a következő parancsot az Azure Container Registrybe való bejelentkezéshez. A `--username` helyére írja a tárolóregisztrációs adatbázis nevét, a `--password` helyére pedig a megadott jelszavak egyikét. A példában szereplő utolsó argumentum (*mycontainerregistry.azurecr.io*) helyére írja a tárolóregisztrációs adatbázis loginServer-nevét. 

A parancs a hitelesítési adatok értékét helyileg, a `~/.docker` útvonalon tárolja.

```azurecli-interactive
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry.azurecr.io
```

Hozzon létre egy tömörített fájlt, amely tartalmazza a tárolóregisztrációs adatbázis hitelesítési adatainak értékeit.

```azurecli-interactive
tar czf docker.tar.gz .docker
```

Másolja ezt a fájlt a fürt megosztott tárolójába. Ezzel a lépéssel a fájl a DC/OS fürt összes csomópontján elérhetővé válik.

```azurecli-interactive
cp docker.tar.gz /mnt/share/dcosshare
```

## <a name="upload-image-to-acr"></a>A rendszerkép feltöltése az ACR-be

Most egy fejlesztési gépen, vagy bármely olyan rendszeren, amelyen a Docker telepítve van, hozzon létre egy rendszerképet, és töltse fel az Azure Container Registrybe.

Hozzon létre egy tárolót az Ubuntu-rendszerképből.

```azurecli-interactive
docker run ubuntu --name base-image
```

Most rögzítése a tárolót egy új rendszerképbe. A rendszerkép nevének tartalmaznia kell a tárolóregisztrációs adatbázis `loginServer` nevét `loginServer/imageName` formátumban.

```azurecli-interactive
docker -H tcp://localhost:2375 commit base-image mycontainerregistry30678.azurecr.io/dcos-demo
```

Jelentkezzen be az Azure Container Registrybe. A név helyére írja a loginServer-nevet, a --username helyére a tárolóregisztrációs adatbázis nevét, a --password helyére pedig a megadott jelszavak egyikét.

```azurecli-interactive
docker login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

Végezetül töltse fel a rendszerképet az ACR-beállításjegyzékbe. Ebben a példában a dcos-demo nevű rendszerképet töltjük fel.

```azurecli-interactive
docker push mycontainerregistry30678.azurecr.io/dcos-demo
```

## <a name="run-an-image-from-acr"></a>Rendszerkép futtatása az ACR-ből

Az ACR-beállításjegyzékben tárolt rendszerkép használatához hozzon létre egy fájlt *acrDemo.json* néven, és másolja bele a következő szöveget. A rendszerkép neve helyére a tárolóregisztrációs adatbázis loginServer-nevét és tárolónevét írja, például `loginServer/imageName`. Jegyezze fel az `uris` tulajdonságot. Ez a tulajdonság tárolja a tárolóregisztrációs adatbázis hitelesítési fájljának helyét, amely esetünkben a DC/OS-fürt egyes csomópontjaihoz csatlakoztatott Azure-fájlmegosztás.

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "mycontainerregistry30678.azurecr.io/dcos-demo",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "uris":  [
       "file:///mnt/share/dcosshare/docker.tar.gz"
   ]
}
```

Helyezze üzembe az alkalmazást a DC/OS parancssori felületével.

```azurecli-interactive
dcos marathon app add acrDemo.json
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a DC/OS-t az Azure Container Registry használatára konfiguráltuk, beleértve az alábbi feladatokat:

> [!div class="checklist"]
> * Azure Container Registry üzembe helyezése (szükség esetén)
> * ACR-hitelesítés konfigurálása DC/OS-fürtön
> * Rendszerkép feltöltése az Azure Container Registrybe
> * Tárolórendszerkép futtatása az Azure Container Registryből
