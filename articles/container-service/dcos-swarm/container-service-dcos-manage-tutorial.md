---
title: Azure Container Service-oktatóanyag – A DC/OS kezelése
description: Azure Container Service-oktatóanyag – A DC/OS kezelése
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 1c06605db3044234f6171d8b784bafb7e7ce759e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-container-service-tutorial---manage-dcos"></a>Azure Container Service-oktatóanyag – A DC/OS kezelése

A DC/OS elosztott platformot nyújt a modern és tárolóalapú alkalmazások futtatásához. Az Azure Container Service használatával egyszerűen és gyorsan építhető ki üzemkész DC/OS fürt. Ez a rövid útmutató a DC/OS fürt üzembe helyezéséhez és az alapvető számítási feladatok futtatásához szükséges alaplépéseket részletezi.

> [!div class="checklist"]
> * ACS DC/OS fürt létrehozása
> * Csatlakozás a fürthöz
> * A DC/OS parancssori felületének telepítése
> * Alkalmazás üzembe helyezése a fürtön
> * Alkalmazás méretezése a fürtön
> * A DC/OS fürt csomópontjainak méretezése
> * Alapszintű DC/OS-kezelés
> * A DC/OS fürt törlése

Az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="create-dcos-cluster"></a>DC/OS-fürt létrehozása

Első lépésként hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *westeurope* helyen.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Ezután hozzon létre egy DC/OS fürtöt az [az acs create](/cli/azure/acs#az_acs_create) paranccsal.

Az alábbi példa egy *myDCOSCluster* nevű DC/OS fürtöt és SSH-kulcsokat hoz létre, ha azok még nem léteznek. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  

```azurecli
az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

Néhány perc múlva befejeződik a parancs végrehajtása, és visszaadja az üzembe helyezéssel kapcsolatos adatokat.

## <a name="connect-to-dcos-cluster"></a>Csatlakozás DC/OS fürthöz

A DC/OS fürt létrehozása után az SSH-alagúton keresztül érhető el. Futtassa a következő parancsot a fő DC/OS nyilvános IP-címének visszaadásához. Ez az IP-cím változóban van tárolva, és a következő lépésben használjuk.

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

Az SSH-alagút létrehozásához futtassa a következő parancsot, és kövesse a képernyőn látható utasításokat. Ha a 80-as port már használatban van, a parancs meghiúsul. Frissítse a bújtatott portot egy nem használtra, például a következőre: `85:localhost:80`. 

```azurecli
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

## <a name="install-dcos-cli"></a>A DC/OS parancssori felület telepítése

Telepítse a DC/OS parancssori felületet az [az acs dcos install-cli](/azure/acs/dcos#install-cli) paranccsal. Az Azure Cloud Shell használata esetén a DC/OS parancssori felület már telepítve van. Ha az Azure CLI felületet macOS vagy Linux rendszeren futtatja, előfordulhat, hogy sudo módban kell futtatnia a parancsot.

```azurecli
az acs dcos install-cli
```

A parancssori felületet fürttel való használata előtt konfigurálnia kell azt az SSH-alagút használatához. Ehhez futtassa az alábbi parancsot, szükség esetén a port módosításával.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Alkalmazás futtatása

Az ACS DC/OS fürtök alapértelmezett ütemezési mechanizmusa a Marathon. A Marathon az alkalmazások elindítására és az alkalmazás állapotának kezelésére szolgál a DC/OS fürtön. Ha a Marathonon keresztül szeretne ütemezni egy alkalmazást, hozza létre a **marathon-app.json** nevű fájlt, és másolja bele a következő tartalmakat. 

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

Futtassa a következő parancsot az alkalmazás DC/OS fürttel való futásának ütemezéséhez.

```azurecli
dcos marathon app add marathon-app.json
```

Az alkalmazás üzembe helyezési állapotának megtekintéséhez futtassa a következő parancsot.

```azurecli
dcos marathon app list
```

Amikor a **TASKS** (Feladatok) oszlop értéke *0/1* értékről *1/1* értékre vált, az alkalmazás üzembe helyezése befejeződött.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     0/1    ---       ---      False      DOCKER   None
```

## <a name="scale-marathon-application"></a>A Marathon-alkalmazás méretezése

Az előző példában egy egypéldányos alkalmazást hoztunk létre. Ahhoz, hogy frissíthessük az üzemi környezetet, hogy az alkalmazás három példányban legyen elérhető, nyissa meg a **marathon-app.json** fájlt, és a Példány tulajdonság értékét módosítsa 3-ra.

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

Frissítse az alkalmazást a `dcos marathon app update` parancs használatával.

```azurecli
dcos marathon app update demo-app-private < marathon-app.json
```

Az alkalmazás üzembe helyezési állapotának megtekintéséhez futtassa a következő parancsot.

```azurecli
dcos marathon app list
```

Amikor a **TASKS** (Feladatok) oszlop értéke *1/3* értékről *3/1* értékre vált, az alkalmazás üzembe helyezése befejeződött.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/3    ---       ---      False      DOCKER   None
```

## <a name="run-internet-accessible-app"></a>Az internetről elérhető alkalmazás futtatása

Az ACS DC/OS fürt két csomópontkészletet tartalmaz: az egyik nyilvános és elérhető az interneten keresztül, a másik pedig privát, és az interneten keresztül nem érhető el. Az alapértelmezett készlet a privát csomópontoké, azt használtuk a legutóbbi példában.

Ahhoz, hogy az alkalmazás elérhető legyen az interneten, a nyilvános csomópontkészleten kell üzembe helyezni. Ehhez az `acceptedResourceRoles` objektumhoz a `slave_public` értéket kell rendelni.

Hozzon létre egy fájlt **nginx-public.json** néven, és másolja bele a következő tartalmat.

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

Futtassa a következő parancsot az alkalmazás DC/OS fürttel való futásának ütemezéséhez.

```azurecli 
dcos marathon app add nginx-public.json
```

Szerezze be a DC/OS nyilvános fürtügynökök nyilvános IP-címét.

```azurecli 
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Ha erre a címre ugrik, a rendszer visszaadja az alapértelmezett NGINX helyet.

![NGINX](./media/container-service-dcos-manage-tutorial/nginx.png)

## <a name="scale-dcos-cluster"></a>DC/OS fürt méretezése

Az előzőekben egy alkalmazást többpéldányosra méreteztünk. A DC/OS infrastruktúra is méretezhető attól függően, hogy több vagy kevesebb számítási kapacitásra van-e szükség. Ez a művelet az [az acs scale]() paranccsal hajtható végre. 

A DC/OS ügynökök aktuális számának megtekintéséhez használja az [az acs show](/cli/azure/acs#az_acs_show) parancsot.

```azurecli
az acs show --resource-group myResourceGroup --name myDCOSCluster --query "agentPoolProfiles[0].count"
```

Növelje a számot 5-re az [az acs scale](/cli/azure/acs#az_acs_scale) paranccsal. 

```azurecli
az acs scale --resource-group myResourceGroup --name myDCOSCluster --new-agent-count 5
```

## <a name="delete-dcos-cluster"></a>DC/OS fürt törlése

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az_group_delete) paranccsal eltávolítható az erőforráscsoport, a DC/OS fürt és az összes kapcsolódó erőforrás.

```azurecli 
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a DC/OS alapvető kezelési feladataival ismerkedtünk meg: 

> [!div class="checklist"]
> * ACS DC/OS fürt létrehozása
> * Csatlakozás a fürthöz
> * A DC/OS parancssori felületének telepítése
> * Alkalmazás üzembe helyezése a fürtön
> * Alkalmazás méretezése a fürtön
> * A DC/OS fürt csomópontjainak méretezése
> * A DC/OS fürt törlése

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan osztható el az alkalmazások terhelése a DC/OS-ben az Azure rendszerében. 

> [!div class="nextstepaction"]
> [Terheléselosztási alkalmazások](container-service-load-balancing.md)