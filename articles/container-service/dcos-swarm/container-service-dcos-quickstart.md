---
title: "Azure-tárolót szolgáltatás gyors üzembe helyezés – DC/OS-fürt központi telepítése |} Microsoft Docs"
description: "Azure-tárolót szolgáltatás gyors üzembe helyezés – DC/OS-fürt központi telepítése"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, tárolók, mikroszolgáltatások, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 8070d224fe6281e61f67483d4f1dd905a2ab99eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-dcos-cluster"></a>DC/OS fürt üzembe helyezése

A DC/OS elosztott platformot kínál a futó modern és indexelése alkalmazások. Teljes Azure Tárolószolgáltatás egy éles készen áll a DC/OS-fürtben üzembe, akkor egyszerűen és gyorsan. A gyors üzembe helyezési és részletes adatai az alapvető lépéseken, amelyekkel telepítheti a DC/OS fürt alapvető munkaterhelés futtassa.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure-előfizetésbe az [az login](/cli/azure/#login) paranccsal, és kövesse a képernyőn látható utasításokat.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-dcos-cluster"></a>A DC/OS-fürt létrehozása

A DC/OS-fürt létrehozása a [az acs létre](/cli/azure/acs#create) parancsot.

Az alábbi példakód létrehozza a DC/OS-fürt nevű *myDCOSCluster* és SSH-kulcsok létrehozása, ha még nem léteznek. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  

```azurecli
az acs create --orchestrator-type dcos --resource-group myResourceGroup --name myDCOSCluster --generate-ssh-keys
```

Egyes esetekben – például korlátozott próbaverziónál – az Azure-előfizetés korlátozott hozzáféréssel rendelkezik az Azure-erőforrásokhoz. Ha az üzembe helyezés az elérhető magok korlátozott száma miatt hiúsul meg, csökkentse az alapértelmezett ügynökök számát az `--agent-count 1` az [az acs create](/cli/azure/acs#create) parancshoz történő hozzáadásával. 

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

Az SSH-alagút tallózással tesztelhető `http://localhost`. Ha egy portot más, a 80-as használták, állítsa be a megfelelő helyre. 

Ha az SSH-alagút létrehozása sikeresen megtörtént, a DC/OS portal ad vissza.

![VEZÉNYLŐTÍPUSÚ FELHASZNÁLÓI FELÜLET](./media/container-service-dcos-quickstart/dcos-ui.png)

## <a name="install-dcos-cli"></a>DC/OS parancssori felület telepítése

A DC/OS parancssori felület használatával a DC/OS-fürt kezeléséhez parancsot a parancssorból. A DC/OS parancssori felület használatával telepítse a [az acs vezénylőtípusú install-cli](/azure/acs/dcos#install-cli) parancsot. Ha Azure CloudShell használ, a DC/OS parancssori felület már telepítve van. 

Ha az Azure parancssori felület macOS vagy Linux rendszer használata esetén szükség lehet a sudo futtassa a parancsot.

```azurecli
az acs dcos install-cli
```

Megelőzően a parancssori felület használható a fürt, az SSH-alagút használatára kell konfigurálni. Ehhez futtassa a következő parancsot, és beállítja a port, ha szükséges.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Alkalmazás futtatása

Az alapértelmezett ütemezés mechanizmus egy ACS DC/OS-fürt Marathon. Marathon olyan alkalmazást, és a DC/OS-fürtön alkalmazás állapota kezelésére szolgál. Ütemezés marathon egy alkalmazás, hozzon létre egy fájlt *marathon-app.json*, és a következő tartalom másolása. 

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
dcos marathon app add marathon-app.json
```

Az alkalmazás központi telepítési állapotának megtekintéséhez futtassa a következő parancsot.

```azurecli
dcos marathon app list
```

Ha a **Várakozás** oszlopérték vált *igaz* való *hamis*, alkalmazás központi telepítése befejeződött.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/1    ---       ---      False      DOCKER   None
```

A nyilvános IP-címét a DC/OS-fürt ügynökök beolvasni.

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Az alapértelmezett NGINX hely keresse meg ezt a címet adja vissza.

![NGINX](./media/container-service-dcos-quickstart/nginx.png)

## <a name="delete-dcos-cluster"></a>A DC/OS-fürt törlése

Ha már nincs szüksége, használhatja a [az csoport törlése](/cli/azure/group#delete) parancs eltávolítja az erőforráscsoportot, a DC/OS-fürtről, és az összes kapcsolódó erőforrásokat.

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezési a DC/OS-fürt telepítése után, és rendelkezik a fürtön futtatni egy egyszerű Docker-tároló. További információt az Azure Tárolószolgáltatás, továbbra is az ACS-oktatóanyagok.

> [!div class="nextstepaction"]
> [Az ACS a DC/OS-fürt kezeléséhez](container-service-dcos-manage-tutorial.md)