---
title: Az Azure Docker Virtuálisgép-bővítmény használata az Azure CLI 1.0 |} Microsoft Docs
description: Útmutató a Docker Virtuálisgép-bővítmény használatával gyorsan és biztonságosan központi telepítése egy Docker-környezethez az Azure Resource Manager-sablonok használatával.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 2f981a50887138660d26a9d011870a05f1270c94
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension-with-the-azure-cli-10"></a>Hozzon létre egy Docker-környezetet a Docker Virtuálisgép-bővítmény használata az Azure CLI 1.0 Azure-ban
Docker egy népszerű tárolóinak kezelése és a lemezkép-készítési platform, amely lehetővé teszi, hogy gyorsan alkalmazásában tárolók Linux (és a Windows is). Az Azure különböző módja van Docker igény szerint telepítheti. Ez a cikk foglalkozik a Docker Virtuálisgép-bővítmény és az Azure Resource Manager-sablonok használatával. 

További információ a különböző központi telepítési módszer, beleértve a Docker gép és az Azure tárolószolgáltatások tekintse meg a következő cikkeket:

* Gyorsan típusnak egy alkalmazást, létrehozhat egy Docker állomáshoz használatával [Docker gép](docker-machine.md).
* Nagyobb, stabilabb környezetben is használhatja az Azure Docker Virtuálisgép-bővítmény, amely is támogatja a [Docker Compose](https://docs.docker.com/compose/overview/) üzemelő tárolópéldányokat konzisztens létrehozásához. Ez a cikk adatokat az Azure Docker Virtuálisgép-bővítmény használatával.
* Adja meg a további ütemezés és a felügyeleti eszközök éles használatra kész, méretezhető környezetek létrehozása, központilag telepítheti egy [Docker Swarm-fürt a Azure-tároló szolgáltatásokra](../../container-service/dcos-swarm/container-service-deployment.md).

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Az Azure CLI 1.0](#azure-docker-vm-extension-overview) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)
- [Azure CLI 2.0](dockerextension.md) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli. 

## <a name="azure-docker-vm-extension-overview"></a>Az Azure Docker Virtuálisgép-bővítmény áttekintése
Az Azure Docker Virtuálisgép-bővítmény telepíti és konfigurálja a Docker démon, a Docker-ügyfél és a Docker Compose a Linux virtuális gép (VM). Az Azure Docker Virtuálisgép-bővítmény használatával, hogy további vezérlő és szolgáltatásokkal, mint egyszerűen Docker számítógépet használja, vagy hozzon létre a Docker-állomás, saját magának. Ezek a további szolgáltatásokat, például a [Docker Compose](https://docs.docker.com/compose/overview/), ellenőrizze az Azure Docker Virtuálisgép-bővítmény olyan robusztusabb fejlesztői vagy üzemi környezetben ajánlott.

Az Azure Resource Manager-sablonok a környezet teljes struktúrát határozza meg. Sablonok erőforrások, például a Docker gazdagépen virtuális gépeket, tárolási, (RBAC) hozzáférés-vezérlést szerepköralapú és diagnosztika konfigurálása teszik lehetővé. Ezek a sablonok létrehozásához további központi telepítéseket egységes módon is felhasználhatja. További információ az Azure Resource Manager és a sablonok: [Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md). 

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>A sablon az Azure Docker Virtuálisgép-bővítmény telepítése
Gyors üzembe helyezés meglévő sablon használatával hozzon létre egy Ubuntu virtuális gép által használt az Azure Docker Virtuálisgép-bővítmény telepítése és konfigurálása a Docker-állomás. A sablon Itt tekintheti meg: [egyszerű Ubuntu virtuális gép a Docker-telepítés](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Van szüksége a [Azure CLI legújabb](../../cli-install-nodejs.md) telepítve, és bejelentkezett a Resource Manager módra használatával az alábbiak szerint:

```azurecli
azure config mode arm
```

A sablon az Azure parancssori felülettel, adja meg a sablon URI telepítéséhez. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *westus* helyen. A saját erőforráscsoport-név és hely a következőképpen használhatja:

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Válaszolja meg a tárfiók nevet, adjon meg egy felhasználónevet és jelszót és egy DNS-nevet adja meg a megjelenő utasításokat. A kimenet a következő példához hasonló:

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: azureuser
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicidns
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Az Azure CLI beolvasása után csupán néhány másodpercet, de a Docker-állomás a parancssor még mindig folyamatban létre és állította be a Azure Docker Virtuálisgép-bővítmény. Az üzembe helyezés befejeződik néhány percet vesz igénybe. A Docker gazdagép állapota használatával kapcsolatos részleteket a `azure vm show` parancsot.

A következő példa a nevű virtuális gép állapotát ellenőrzi *myDockerVM* (az alapértelmezett név a sablonból - ne módosítsa a nevet) az erőforráscsoport neve *myResourceGroup*. Adja meg az előző lépésben létrehozott erőforráscsoport nevét:

```azurecli
azure vm show --resource-group myResourceGroup --name myDockerVM
```

A kimenetét a `azure vm show` parancs a következőhöz hasonló:

```azurecli
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicdns.westus.cloudapp.azure.com
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

A kimeneti tetején megjelenik a **ProvisioningState** a virtuális gép. Amikor megjelenik *sikeres*, a telepítés véget ért, és a virtuális gép SSH is.

A kimeneti vége felé *FQDN* jeleníti meg a Docker-állomás teljesen minősített tartománynevét. Az FQDN-je mi segítségével SSH a Docker gazdagépén a további lépéseket.

## <a name="deploy-your-first-nginx-container"></a>Az első nginx tároló üzembe
Miután a telepítés véget ért, SSH az új Docker-állomásnak a helyi számítógépről. Adja meg a saját felhasználónév és a teljes tartománynév

```bash
ssh ops@mypublicdns.westus.cloudapp.azure.com
```

Miután bejelentkezett a Docker-állomás, most futtassa egy nginx-tároló:

```bash
sudo docker run -d -p 80:80 nginx
```

A kimenete a következő példához hasonló a nginx kép letöltése és a tároló lépések:

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

A kimenet az alábbi példához hasonló, megjelenítő, amely a nginx tároló fut, és a 80-as és 443-as TCP-portot és a továbbított:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

A tároló, a művelet egy webböngészőben megnyílik, és adja meg a Docker állomás FQDN nevét:

![Futó ngnix tároló](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Az Azure Docker Virtuálisgép-bővítmény sablon hivatkozása
Az előző példában egy meglévő gyors üzembe helyezés sablont használja. Az Azure Docker Virtuálisgép-bővítmény a saját Resource Manager-sablonok is telepíthet. Ehhez adja hozzá a következőket a Resource Manager-sablonok, meghatározása a *vmName* a virtuális gép megfelelően:

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
    "typeHandlerVersion": "1.1",
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

