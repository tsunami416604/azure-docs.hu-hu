---
title: "ACR használata az Azure DC/OS-fürt"
description: "Egy Azure-tároló beállításjegyzék használata a DC/OS fürtben, az Azure Tárolószolgáltatásban"
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 03/23/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: 4a3213c28f24e9d1dfc309c6d34771ccc062dae4
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>A DC/OS-fürtről ACR használni az alkalmazás központi telepítése

Ebben a cikkben megismerkedhet a Microsoft Azure tároló beállításjegyzék használata a DC/OS-fürtről. ACR használatával lehetővé teszi, hogy közvetlenül a Microsoftnak tárolásához és tároló-lemezképek kezelése. Ez az oktatóanyag ismerteti a következő feladatokat:

> [!div class="checklist"]
> * Telepítse az Azure-tároló beállításjegyzék, (ha szükséges)
> * A DC/OS-fürtről ACR hitelesítés beállítása
> * Lemezkép feltöltése az Azure-tároló beállításjegyzék
> * A tároló-lemezkép az Azure-tároló regisztrációs futtatása

Az ACS DC/OS-fürt az oktatóanyag lépéseinek végrehajtásához van szüksége. Ha szükséges, [a parancsfájl minta](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) hozhat létre egyet.

Az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-azure-container-registry"></a>Telepítse az Azure tároló beállításjegyzék

Szükség esetén hozzon létre egy Azure-tárolóba beállításjegyzéket a [az acr létrehozása](/cli/azure/acr#create) parancsot. 

Az alábbi példakód létrehozza a beállításjegyzéket a véletlenszerű előállításához a neve. A beállításjegyzék egy rendszergazdai fiók használatával is konfigurálva van a `--admin-enabled` argumentum.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic
```

A beállításkulcs létrehozása után az Azure parancssori felület kimenete az alábbihoz hasonló adatok. Vegye figyelembe a `name` és `loginServer`, ezek a későbbi lépésekben használhatók.

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

A tároló beállításjegyzék hitelesítő adatokat lekérni a [az acr hitelesítő adatok megjelenítése](/cli/azure/acr/credential) parancsot. Helyettesítő a `--name` az utolsó lépésben feljegyzett találhatóval. Jegyezze fel a egy jelszó, szükség esetén egy későbbi lépésben.

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

Azure tároló beállításjegyzékkel kapcsolatos további információkért lásd: [Docker-tároló TITKOS nyilvántartó bemutatása](../../container-registry/container-registry-intro.md). 

## <a name="manage-acr-authentication"></a>ACR hitelesítés kezelésére szolgál

A hagyományos leküldéses és lekéréses kép titkos beállításjegyzékből módja először a beállításjegyzékben a hitelesítést. Ehhez használja a `docker login` parancs bármely ügyfélnek, amely a saját beállításjegyzék hozzáférésre van szüksége. Mivel a DC/OS-fürtről tartalmazhat sok csomópontok, amelyek kell hitelesíteni a ACR adatokkal, akkor célszerű automatizálható a folyamat egyes csomópontok között. 

### <a name="create-shared-storage"></a>Megosztott tároló létrehozása

Ez a folyamat használja az Azure fájlmegosztások csatlakoztatva lett a fürt mindegyik csomópontján. Ha már nem beállított megosztott tárolót, lásd: [a DC/OS fürtben található fájlmegosztás beállítása](container-service-dcos-fileshare.md).

### <a name="configure-acr-authentication"></a>ACR hitelesítés konfigurálása

Először beolvasása a DC/OS fő teljes Tartománynevét, és tárolható egy változóban.

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

Az SSH-kapcsolat létrehozása a főkiszolgáló (vagy az első főkiszolgálójának) a DC/OS-alapú fürt. Frissítse a felhasználó nevét, ha egy nem alapértelmezett érték lett megadva, a fürt létrehozásakor.

```azurecli-interactive
ssh azureuser@$FQDN
```

A következő parancsot az Azure-tároló beállításjegyzék számára. Cserélje le a `--username` nevű, a tároló beállításjegyzék és a `--password` a megadott jelszavak egyikével. Cserélje le az utolsó argumentumnak *mycontainerregistry.azurecr.io* loginServer nevű példájában a tároló beállításjegyzék. 

Ez a parancs hitelesítési értékeit a helyileg tárolja a `~/.docker` elérési útja.

```azurecli-interactive
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry.azurecr.io
```

Hozzon létre egy tömörített fájl, amely tartalmazza a tároló hitelesítési beállításazonosítókat.

```azurecli-interactive
tar czf docker.tar.gz .docker
```

Ez a fájl átmásolása a fürt megosztott tároló. Ebben a lépésben elérhetővé teszi a fájl a DC/OS-fürt összes csomópontján.

```azurecli-interactive
cp docker.tar.gz /mnt/share/dcosshare
```

## <a name="upload-image-to-acr"></a>ACR Rendszerkép feltöltése

Most már a fejlesztési számítógépén, vagy bármely más rendszer Docker telepítve, a lemezkép létrehozása, és töltse fel az Azure-tároló beállításjegyzék.

Hozzon létre egy tárolót a Ubuntu lemezképből.

```azurecli-interactive
docker run ubuntu --name base-image
```

Most rögzítése a tárolót az új lemezképet. A lemezkép neve is kell tartalmaznia a `loginServer` neve a tároló registrywith formátuma a `loginServer/imageName`.

```azurecli-interactive
docker -H tcp://localhost:2375 commit base-image mycontainerregistry30678.azurecr.io/dcos-demo
````

Bejelentkezés az Azure-tárolót beállításjegyzékbe. A név helyére loginServer nevét, a--felhasználónév nevű, a tároló beállításjegyzék és a – a megadott jelszavak egyikével jelszót.

```azurecli-interactive
docker login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

Végezetül feltölti a lemezképet a ACR beállításjegyzék. Ez a példa feltölt egy képet vezénylőtípusú-bemutató nevű.

```azurecli-interactive
docker push mycontainerregistry30678.azurecr.io/dcos-demo
```

## <a name="run-an-image-from-acr"></a>Futtassa a képfájl ACR

A képfájl ACR beállításjegyzékből való használatához hozzon létre egy fájlt nevek *acrDemo.json* és a következő szöveg másolása. Cserélje le a lemezkép neve a beállításjegyzék loginServer Tárolónév és a lemezkép neve, például `loginServer/imageName`. Vegye figyelembe a `uris` tulajdonság. Ez a tulajdonság tárolja a tároló beállításjegyzék hitelesítési fájl, amely ebben az esetben a DC/OS-fürt mindegyik csomópontján csatlakoztatott Azure fájlmegosztás helyét.

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

A DC/c CLI az alkalmazás központi telepítését.

```azurecli-interactive
dcos marathon app add acrDemo.json
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban konfigurálnia kell a DC/OS használata Azure tároló beállításjegyzék, beleértve a következő feladatokat:

> [!div class="checklist"]
> * Telepítse az Azure-tároló beállításjegyzék, (ha szükséges)
> * A DC/OS-fürtről ACR hitelesítés beállítása
> * Lemezkép feltöltése az Azure-tároló beállításjegyzék
> * A tároló-lemezkép az Azure-tároló regisztrációs futtatása
