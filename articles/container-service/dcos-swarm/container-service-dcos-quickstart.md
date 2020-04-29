---
title: ELAVULT Azure Container Service rövid útmutató – DC/OS fürt üzembe helyezése
description: Azure Container Service gyors útmutató – DC/OS fürt üzembe helyezése
author: iainfoulds
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6274e24bae2e2a6eade0122fe244652eb29cacf9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78399229"
---
# <a name="deprecated-deploy-a-dcos-cluster"></a>ELAVULT DC/OS-fürt üzembe helyezése

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

A DC/OS elosztott platformot nyújt a modern és tárolóalapú alkalmazások futtatásához. Az Azure Container Service használatával egyszerűen és gyorsan építhető ki üzemkész DC/OS fürt. Ez a rövid útmutató részletesen ismerteti a DC/OS-fürt üzembe helyezéséhez és az alapvető számítási feladatok futtatásához szükséges alapszintű lépéseket.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

Az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha frissíteni szeretne, tekintse meg [az Azure CLI telepítését ismertető]( /cli/azure/install-azure-cli) szakaszt. 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure-előfizetésbe az [az login](/cli/azure/reference-index#az-login) paranccsal, és kövesse a képernyőn látható utasításokat.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-dcos-cluster"></a>DC/OS-fürt létrehozása

Hozzon létre egy DC/OS fürtöt az [az acs create](/cli/azure/acs#az-acs-create) paranccsal.

Az alábbi példa egy *myDCOSCluster* nevű DC/OS fürtöt és SSH-kulcsokat hoz létre, ha azok még nem léteznek. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  

```azurecli
az acs create --orchestrator-type dcos --resource-group myResourceGroup --name myDCOSCluster --generate-ssh-keys
```

Egyes esetekben – például korlátozott próbaverziónál – az Azure-előfizetés korlátozott hozzáféréssel rendelkezik az Azure-erőforrásokhoz. Ha az üzembe helyezés az elérhető magok korlátozott száma miatt hiúsul meg, csökkentse az alapértelmezett ügynökök számát az `--agent-count 1` az [az acs create](/cli/azure/acs#az-acs-create) parancshoz történő hozzáadásával. 

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

Az SSH-alagút a következő cím felkeresésével tesztelhető: `http://localhost`. Ha nem a 80-as portot használta, ennek megfelelően módosítsa a helyet. 

Ha az SSH-alagút sikeresen létrejött, a rendszer visszaadja a DC/OS portált.

![DCOS felhasználói felület](./media/container-service-dcos-quickstart/dcos-ui.png)

## <a name="install-dcos-cli"></a>A DC/OS parancssori felület telepítése

A DC/OS parancssori felülettel kezelhetők a DC/OS fürtök a parancssorból. Telepítse a DC/OS parancssori felületet az [az acs dcos install-cli](/cli/azure/acs/dcos#az-acs-dcos-install-cli) paranccsal. Az Azure Cloud Shell használata esetén a DC/OS parancssori felület már telepítve van. 

Ha az Azure CLI felületet macOS vagy Linux rendszeren futtatja, előfordulhat, hogy sudo módban kell futtatnia a parancsot.

```azurecli
az acs dcos install-cli
```

A parancssori felületet fürttel való használata előtt konfigurálnia kell azt az SSH-alagút használatához. Ehhez futtassa az alábbi parancsot, szükség esetén a port módosításával.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Alkalmazás futtatása

Az ACS DC/OS fürtök alapértelmezett ütemezési mechanizmusa a Marathon. A Marathon az alkalmazások elindítására és az alkalmazás állapotának kezelésére szolgál a DC/OS fürtön. Ha a Marathonon keresztül szeretne ütemezni egy alkalmazást, hozza létre a *marathon-app.json* nevű fájlt, és másolja bele a következő tartalmakat. 

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

```console
dcos marathon app add marathon-app.json
```

Az alkalmazás üzembe helyezési állapotának megtekintéséhez futtassa a következő parancsot.

```console
dcos marathon app list
```

Amikor a **WAITING** (Várakozás) oszlop értéke *True* (Igaz) értékről *False* (Hamis) értékre vált, az alkalmazás üzembe helyezése befejeződött.

```output
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/1    ---       ---      False      DOCKER   None
```

Szerezze be a DC/OS fürtügynökök nyilvános IP-címét.

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Ha erre a címre ugrik, a rendszer visszaadja az alapértelmezett NGINX helyet.

![NGINX](./media/container-service-dcos-quickstart/nginx.png)

## <a name="delete-dcos-cluster"></a>DC/OS fürt törlése

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az-group-delete) paranccsal eltávolítható az erőforráscsoport, a DC/OS fürt és az összes kapcsolódó erőforrás.

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy DC/OS fürtöt, és futtatott egy egyszerű Docker-tárolót a fürtön. Az Azure Container Service-ről további információt az ACS oktatóanyagokban talál.

> [!div class="nextstepaction"]
> [ACS DC/OS fürt kezelése](container-service-dcos-manage-tutorial.md)
