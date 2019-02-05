---
title: Az Azure Docker VM bővítmény használata |} A Microsoft Docs
description: A Docker VM bővítmény használata gyors és biztonságos üzembe helyezéséhez egy Docker-környezetben az Azure Resource Manager-sablonokkal és az Azure CLI használatával
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: cynthn
ms.openlocfilehash: 1bc250ac70e48a548d393c3bc6025868948dc022
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699159"
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Hozzon létre egy Docker-környezetben az Azure-ban a Docker VM-bővítmény

A docker egy népszerű tárolókezelő és képalkotó platform, amely lehetővé teszi, hogy a linuxon futó tárolók gyors használatát egy. Az Azure-ban igény szerint telepítheti a Docker különböző módon is. Ez a cikk foglalkozik az Azure CLI-vel a Docker VM-bővítmény és Azure Resource Manager-sablonok használatával. 

> [!WARNING]
> Az Azure Docker Virtuálisgép-bővítmény linuxhoz elavult, és 2018. November kivezetjük.
> A bővítmény Docker, csupán telepíti, így a lehetőségeket, például a cloud-init vagy az egyéni Szkriptbővítmény annak választott Docker verziójának telepítéséhez. A cloud-init használatával további információkért lásd: [testreszabása a cloud-Init használatával egy Linux rendszerű virtuális gép](tutorial-automate-vm-deployment.md).

## <a name="azure-docker-vm-extension-overview"></a>Az Azure Docker VM-bővítmény áttekintése
Az Azure Docker VM bővítmény telepíti, és a Docker-démont, a Docker-ügyfél és a Docker Compose konfigurálja a Linux rendszerű virtuális gépen (VM). Az Azure Docker VM bővítmény használatával, hogy további ellenőrzési és szolgáltatások, mint a egyszerűen a Docker Machine használatával vagy saját maga a Docker-gazdagép létrehozása. Ezek a további funkciók, például [Docker Compose](https://docs.docker.com/compose/overview/), ügyeljen az olyan adatbázisoknál robusztusabb fejlesztési és éles környezetek az Azure Docker Virtuálisgép-bővítménnyel.

További információ a különböző központi telepítési módszer, például a Docker Machine és az Azure Container Service szolgáltatásait tekintse meg a következő cikkeket:

* Az alkalmazás gyorsan prototípusként egyetlen Docker-gazdagép használatával hozhat létre [a Docker Machine](docker-machine.md).
* Éles használatra kész, méretezhető környezetek, amelyek további ütemezési és felügyeleti eszközöket hozhat létre, telepíthet egy [Kubernetes](../../container-service/kubernetes/index.yml) vagy [Docker Swarm](../../container-service/dcos-swarm/index.yml) fürtöt az Azure Container Service szolgáltatásait.


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Az Azure Docker Virtuálisgép-bővítménnyel-sablon üzembe helyezése
Egy meglévő gyorsindítási sablon használatával hozzon létre egy Ubuntu virtuális gép, amely használja az Azure Docker VM-bővítmény telepítése és konfigurálása a Docker-gazdagép. A sablon Itt tekintheti meg: [A docker használatával egy Ubuntu virtuális gép üzembe](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). A legújabb kell [Azure CLI-vel](/cli/azure/install-az-cli2) telepítve, és bejelentkezett egy Azure-fiókba az [az bejelentkezési](/cli/azure/reference-index).

Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Ezután telepítse a virtuális gép [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment) , amely tartalmazza az Azure Docker VM-bővítmény [ezen Azure Resource Manager-sablon a Githubon](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Amikor a rendszer kéri, adja meg a saját egyedi értékek *newStorageAccountName*, *adminUsername*, *adminPassword*, és *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Az üzembe helyezés befejeződik néhány percet vesz igénybe.


## <a name="deploy-your-first-nginx-container"></a>Az első az NGINX-tároló üzembe helyezése
A virtuális gép, beleértve a DNS-név, a részletek megtekintéséhez használja a [az vm show](/cli/azure/vm):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

Ssh-KAPCSOLATOT az új Docker-gazdagép. Adja meg a saját felhasználónevét és a DNS-nevet az előző lépésekből származó:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Miután bejelentkezett a Docker-gazdagép, futtassunk egy NGINX-tároló:

```bash
sudo docker run -d -p 80:80 nginx
```

A kimenet a az alábbi példához hasonló, mint az NGINX rendszerképet töltődik le, és egy tárolót az első lépéseket:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

A következőképpen a Docker gazdagépen futó tárolók állapotának ellenőrzéséhez:

```bash
sudo docker ps
```

A kimenet a következő példához hasonló, amely igazolja, hogy az NGINX-tároló fut, és a 80-as és 443-as TCP-portot, és továbbítva:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

A tároló működés közben látni, nyissa meg egy webböngészőt, és adja meg a DNS-nevét, a Docker-gazdagép:

![Futó ngnix tároló](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Az Azure Docker VM bővítmény sablonreferenciája
Az előző példában egy meglévő rövid sablont használ. Saját Resource Manager-sablonokkal is telepítheti az Azure Docker Virtuálisgép-bővítménnyel. Ehhez adja hozzá a következő Resource Manager-sablonokkal, meghatározása a `vmName` a virtuális gép megfelelően:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.*",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Annak a Resource Manager-sablonok használatával olvassa el a forgatókönyv részletes [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md).

## <a name="next-steps"></a>További lépések
Kezdésként érdemes lehet [a Docker-démon TCP-portot konfigurálja](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), ismertetése [Docker biztonsági](https://docs.docker.com/engine/security/security/), vagy helyezze üzembe a tárolók [Docker Compose](https://docs.docker.com/compose/overview/). Az Azure Docker VM-bővítmény maga a további információkért lásd: a [GitHub-projekt](https://github.com/Azure/azure-docker-extension/).

Olvassa el az Azure-ban további Docker üzembe helyezési lehetőségekről további információt:

* [A Docker Machine használata az Azure-illesztő](docker-machine.md)  
* [Ismerkedés a Docker és a Compose megadásához és a egy többtárolós alkalmazást futtatunk egy Azure virtuális gép](docker-compose-quickstart.md).
* [Az Azure Container Service-fürt üzembe helyezése](../../container-service/dcos-swarm/container-service-deployment.md)

