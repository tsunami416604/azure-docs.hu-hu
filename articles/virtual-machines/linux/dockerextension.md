---
title: Használja az Azure Docker Virtuálisgép-bővítmény |} Microsoft Docs
description: 'Útmutató: a Docker Virtuálisgép-bővítmény használatával gyorsan és biztonságosan központi telepítése egy Docker-környezethez az Azure Resource Manager-sablonok és az Azure CLI 2.0 használatával'
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: 6cf77a6fa5e2cb7f9ce349e72444e76d4c687f49
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937652"
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Hozzon létre egy Docker-környezetet az Azure-ban a Docker Virtuálisgép-bővítmény
Docker egy népszerű tárolóinak kezelése és a lemezkép-készítési platform, amely lehetővé teszi, hogy gyorsan alkalmazásában tárolók Linux rendszeren. Az Azure különböző módja van Docker igény szerint telepítheti. Ez a cikk foglalkozik a a Docker Virtuálisgép-bővítmény és az Azure Resource Manager-sablonok az Azure CLI 2.0-s verziójával. 

> [!WARNING]
> A Linux Azure Docker Virtuálisgép-bővítménnyel elavult, és November 2018 rendszerből.
> A bővítmény Docker, csupán telepítését, így alternatív eszközökbe, például felhő-init vagy az egyéni parancsprogramok futtatására szolgáló bővítmény választott Docker-verzió telepítése fejlettebb módszert. Felhő inicializálás használatáról további információk: [testre szabhatja a Linux virtuális gép és felhő inicializálás](tutorial-automate-vm-deployment.md).

## <a name="azure-docker-vm-extension-overview"></a>Az Azure Docker Virtuálisgép-bővítmény áttekintése
Az Azure Docker Virtuálisgép-bővítmény telepíti és konfigurálja a Docker démon, a Docker-ügyfél és a Docker Compose a Linux virtuális gép (VM). Az Azure Docker Virtuálisgép-bővítmény használatával, hogy további vezérlő és szolgáltatásokkal, mint egyszerűen Docker számítógépet használja, vagy hozzon létre a Docker-állomás, saját magának. Ezek a további szolgáltatásokat, például a [Docker Compose](https://docs.docker.com/compose/overview/), ellenőrizze az Azure Docker Virtuálisgép-bővítmény olyan robusztusabb fejlesztői vagy üzemi környezetben ajánlott.

További információ a különböző központi telepítési módszer, beleértve a Docker gép és az Azure tárolószolgáltatások tekintse meg a következő cikkeket:

* Gyorsan típusnak egy alkalmazást, létrehozhat egy Docker állomáshoz használatával [Docker gép](docker-machine.md).
* Adja meg a további ütemezés és a felügyeleti eszközök éles használatra kész, méretezhető környezetek létrehozása, központilag telepítheti egy [Kubernetes](../../container-service/kubernetes/index.yml) vagy [Docker Swarm](../../container-service/dcos-swarm/index.yml) Azure tárolószolgáltatások a fürt.


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>A sablon az Azure Docker Virtuálisgép-bővítmény telepítése
Gyors üzembe helyezés meglévő sablon használatával hozzon létre egy Ubuntu virtuális gép által használt az Azure Docker Virtuálisgép-bővítmény telepítése és konfigurálása a Docker-állomás. A sablon Itt tekintheti meg: [egyszerű Ubuntu virtuális gép a Docker-telepítés](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). A legújabb kell [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login).

Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Ezután telepítse a virtuális gép és [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#az_group_deployment_create) , amely tartalmazza az Azure Docker Virtuálisgép-bővítmény a [a Githubon az Azure Resource Manager sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Amikor a rendszer kéri, adja meg a saját egyedi értékeket az *newStorageAccountName*, *adminUsername*, *adminPassword*, és *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Az üzembe helyezés befejeződik néhány percet vesz igénybe.


## <a name="deploy-your-first-nginx-container"></a>Az első NGINX tároló üzembe
A virtuális gép, beleértve a DNS-nevét, a részletek megtekintéséhez használja a [az vm megjelenítése](/cli/azure/vm#az_vm_show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

SSH-kapcsolatot az új Docker-állomás. Adja meg a saját felhasználónevét és a DNS-név az előző lépéseket:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Miután bejelentkezett a Docker-állomás, most futtassa egy NGINX-tároló:

```bash
sudo docker run -d -p 80:80 nginx
```

A kimenete a következő példához hasonló a NGINX kép letöltése és a tároló lépések:

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

Tekintse meg a tárolók a Docker gazdagépen futó az alábbiak szerint:

```bash
sudo docker ps
```

A kimenet az alábbi példához hasonló, megjelenítő, amely a NGINX tároló fut, és a 80-as és 443-as TCP-portot és a továbbított:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

A tároló, a művelet egy webböngészőben megnyílik, és adja meg a Docker-állomás DNS-neve:

![Futó ngnix tároló](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Az Azure Docker Virtuálisgép-bővítmény sablon hivatkozása
Az előző példában egy meglévő gyors üzembe helyezés sablont használja. Az Azure Docker Virtuálisgép-bővítmény a saját Resource Manager-sablonok is telepíthet. Ehhez adja hozzá a következőket a Resource Manager-sablonok, meghatározása a `vmName` a virtuális gép megfelelően:

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

Részletes útmutató Resource Manager-sablonok használatával olvasásával található [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md).

## <a name="next-steps"></a>További lépések
Kezdésként érdemes lehet [a Docker démon TCP-port konfigurálása](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), megértéséhez [Docker biztonsági](https://docs.docker.com/engine/security/security/), vagy használatával tároló üzembe helyezése [Docker Compose](https://docs.docker.com/compose/overview/). Az Azure Docker Virtuálisgép-bővítmény maga további információkért tekintse meg a [GitHub-projekt](https://github.com/Azure/azure-docker-extension/).

Az Azure-ban további Docker telepítési beállításokkal kapcsolatos további információkért olvassa el:

* [Docker használata az Azure-illesztőprogram](docker-machine.md)  
* [Ismerkedés a Docker és a Compose határozza meg, és futtassa a több tároló alkalmazást egy Azure virtuális gépen a](docker-compose-quickstart.md).
* [Az Azure Container Service-fürt üzembe helyezése](../../container-service/dcos-swarm/container-service-deployment.md)

