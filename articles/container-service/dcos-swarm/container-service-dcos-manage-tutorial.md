---
title: "Azure Tárolószolgáltatás útmutató – DC/OS kezelése"
description: "Azure Tárolószolgáltatás útmutató – DC/OS kezelése"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 07/17/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0c58bd764cf0fdacd55675f8343c6e7481a11823
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="azure-container-service-tutorial---manage-dcos"></a>Azure Tárolószolgáltatás útmutató – DC/OS kezelése

A DC/OS elosztott platformot kínál a futó modern és indexelése alkalmazások. Teljes Azure Tárolószolgáltatás egy éles készen áll a DC/OS-fürtben üzembe, akkor egyszerűen és gyorsan. A gyors üzembe helyezési részletek szükséges alapvető lépések a DC/OS-fürtről és futtatási alapvető munkaterhelés központi telepítése.

> [!div class="checklist"]
> * Az ACS a DC/OS-fürt létrehozása
> * Csatlakozás a fürthöz
> * A DC/OS parancssori felület telepítése
> * A fürt alkalmazás központi telepítése
> * Egy alkalmazás méretezni a fürtön
> * A DC/OS-fürt csomópontjai méretezése
> * Alapszintű DC/OS-kezelés
> * A DC/OS-fürt törlése

Az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="create-dcos-cluster"></a>A DC/OS-fürt létrehozása

Először hozzon létre egy erőforráscsoport a [az csoport létrehozása](/cli/azure/group#create) parancsot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *westeurope* helyen.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Ezután hozzon létre a DC/OS fürtben a [az acs létre](/cli/azure/acs#create) parancsot.

Az alábbi példakód létrehozza a DC/OS-fürt nevű *myDCOSCluster* és SSH-kulcsok létrehozása, ha még nem léteznek. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  

```azurecli
az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

Pár perc múlva a parancs befejeződik, és a központi telepítés információt ad vissza.

## <a name="connect-to-dcos-cluster"></a>Csatlakozzon a DC/OS-fürt

A DC/OS-fürt létrehozása után célszerű egy SSH-alagúton keresztül fér hozzá. A következő parancsot a DC/OS-főkiszolgáló nyilvános IP-címét adja vissza. Az IP-cím egy változó tárolja és használja a következő lépésben.

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

Az SSH-alagút létrehozásához a következő parancsot, és kövesse a képernyőn megjelenő utasításokat. Ha a 80-as port már használatban van, a parancs sikertelen lesz. Frissítse a bújtatott port nincs a használatát, például a `85:localhost:80`. 

```azurecli
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

## <a name="install-dcos-cli"></a>DC/OS parancssori felület telepítése

A DC/OS parancssori felület használatával telepítse a [az acs vezénylőtípusú install-cli](/azure/acs/dcos#install-cli) parancsot. Ha Azure CloudShell használ, a DC/OS parancssori felület már telepítve van. Ha az Azure parancssori felület macOS vagy Linux rendszer használata esetén szükség lehet a sudo futtassa a parancsot.

```azurecli
az acs dcos install-cli
```

Megelőzően a parancssori felület használható a fürt, az SSH-alagút használatára kell konfigurálni. Ehhez futtassa a következő parancsot, és beállítja a port, ha szükséges.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Alkalmazás futtatása

Az alapértelmezett ütemezés mechanizmus egy ACS DC/OS-fürt Marathon. Marathon olyan alkalmazást, és a DC/OS-fürtön alkalmazás állapota kezelésére szolgál. Ütemezés marathon egy alkalmazás, hozzon létre egy fájlt **marathon-app.json**, és a következő tartalom másolása. 

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

A következő parancsot az alkalmazás futtatásához a DC/OS-fürtön ütemezni.

```azurecli
dcos marathon app add marathon-app.json
```

Az alkalmazás központi telepítési állapotának megtekintéséhez futtassa a következő parancsot.

```azurecli
dcos marathon app list
```

Ha a **feladatok** oszlopérték vált *0 vagy 1* való *1/1*, alkalmazás központi telepítése befejeződött.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     0/1    ---       ---      False      DOCKER   None
```

## <a name="scale-marathon-application"></a>A Marathon alkalmazás skálázása

Az előző példában egy egypéldányos alkalmazás lett létrehozva. A központi telepítés, hogy az alkalmazás három példányainak frissítéséhez nyissa meg a **marathon-app.json** fájlt, és frissítse a példányt tulajdonságát a 3.

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 3,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Az alkalmazás használatával frissítse a `dcos marathon app update` parancsot.

```azurecli
dcos marathon app update demo-app-private < marathon-app.json
```

Az alkalmazás központi telepítési állapotának megtekintéséhez futtassa a következő parancsot.

```azurecli
dcos marathon app list
```

Ha a **feladatok** oszlopérték vált *1/3* való *3/1*, alkalmazás központi telepítése befejeződött.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/3    ---       ---      False      DOCKER   None
```

## <a name="run-internet-accessible-app"></a>Elérhető az internet-alkalmazás futtatása

Az ACS DC/OS fürtben két csomópont-készlet, az interneten elérhető egy nyilvános és egy saját, amely nem érhető el az interneten áll. Alapértelmezés szerint az utolsó példában használt a titkos csomópontok.

Ahhoz, hogy az alkalmazás elérhető az interneten, azok a nyilvános csomópont-készlethez. Ehhez az szükséges, hogy a `acceptedResourceRoles` érték objektum `slave_public`.

Hozzon létre egy fájlt **nginx-public.json** és a következő tartalom másolása.

```json
{
  "id": "demo-app",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  },
  "acceptedResourceRoles": [
    "slave_public"
  ]
}
```

A következő parancsot az alkalmazás futtatásához a DC/OS-fürtön ütemezni.

```azurecli 
dcos marathon app add nginx-public.json
```

A nyilvános IP-cím a DC/OS nyilvános fürt ügynökök beolvasni.

```azurecli 
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Az alapértelmezett NGINX hely keresse meg ezt a címet adja vissza.

![NGINX](./media/container-service-dcos-manage-tutorial/nginx.png)

## <a name="scale-dcos-cluster"></a>Skála DC/OS-fürtről

A fenti példákban egy alkalmazás több példánya lett méretezhető. A DC/OS-infrastruktúra is méretezhetők, amelyek több vagy kevesebb számítási kapacitást biztosítanak. Ez a lépés a [az acs méretezése]() parancsot. 

A DC/OS-ügynökök jelenlegi száma megjelenítéséhez használja a [az acs megjelenítése](/cli/azure/acs#show) parancsot.

```azurecli
az acs show --resource-group myResourceGroup --name myDCOSCluster --query "agentPoolProfiles[0].count"
```

5 számának növeléséhez használja a [az acs méretezése](/cli/azure/acs#scale) parancsot. 

```azurecli
az acs scale --resource-group myResourceGroup --name myDCOSCluster --new-agent-count 5
```

## <a name="delete-dcos-cluster"></a>A DC/OS-fürt törlése

Ha már nincs szüksége, használhatja a [az csoport törlése](/cli/azure/group#delete) parancs eltávolítja az erőforráscsoportot, a DC/OS-fürtről, és az összes kapcsolódó erőforrásokat.

```azurecli 
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóprogramban megtanulhatta, kapcsolatos alapvető DC/OS fájlkezelési feladat, többek között a következőket. 

> [!div class="checklist"]
> * Az ACS a DC/OS-fürt létrehozása
> * Csatlakozás a fürthöz
> * A DC/OS parancssori felület telepítése
> * A fürt alkalmazás központi telepítése
> * Egy alkalmazás méretezni a fürtön
> * A DC/OS-fürt csomópontjai méretezése
> * A DC/OS-fürt törlése

Továbblépés a következő oktatóanyag terheléselosztási a DC/OS Azure alkalmazás megismeréséhez. 

> [!div class="nextstepaction"]
> [Terheléselosztási alkalmazások](container-service-load-balancing.md)