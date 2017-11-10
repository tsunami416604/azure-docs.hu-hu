---
title: "Docker gép használata a Linux-állomások létrehozása az Azure-ban |} Microsoft Docs"
description: "Docker-gazdagépekből létrehozása az Azure-ban Docker gép használatát ismerteti."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: iainfou
ms.openlocfilehash: efd0b60079017e476b54acfc65ffe8f35ffe4933
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Docker-számítógép segítségével gazdagépek létrehozása az Azure-ban
Ez a cikk részletesen használata [Docker gép](https://docs.docker.com/machine/) gazdagépek létrehozása az Azure-ban. A `docker-machine` parancs létrehoz egy Linux virtuális gép (VM) az Azure-ban, majd telepíti a Docker. Az Azure-ban, a helyi eszközök és a munkafolyamatok a Docker-gazdagépekből kezelhetők. A Windows 10-es docker-gép használatához a Linux bash eszközt kell használnia.

## <a name="create-vms-with-docker-machine"></a>Hozzon létre virtuális gépek Docker gép
Először szerezze be az Azure-előfizetése Azonosítóját [az fiók megjelenítése](/cli/azure/account#show) az alábbiak szerint:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Létrehozott egy Docker állomás virtuális gépet az Azure-ban `docker-machine create` megadásával *azure* az illesztőprogramként. További információkért lásd: a [Docker Azure illesztőprogram dokumentáció](https://docs.docker.com/machine/drivers/azure/)

Az alábbi példakód létrehozza a virtuális gépek nevű *myVM*, a "Standard D2 v2" terv alapján, létrehoz egy felhasználói fiókot nevű *azureuser*, és a portot nyit meg *80* virtuális gép a gazdagépen. Jelentkezzen be az Azure-fiókjával, és a Docker gép engedélyezze, hogy hozzon létre és kezelheti az erőforrásokat bármilyen útmutatást követve.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_D2_v2" \
    myvm
```

A kimeneti az alábbihoz hasonlít:

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(myvmdocker) Completed machine pre-create checks.
Creating machine...
(myvmdocker) Querying existing resource group.  name="docker-machine"
(myvmdocker) Creating resource group.  name="docker-machine" location="westus"
(myvmdocker) Configuring availability set.  name="docker-machine"
(myvmdocker) Configuring network security group.  name="myvmdocker-firewall" location="westus"
(myvmdocker) Querying if virtual network already exists.  rg="docker-machine" location="westus" name="docker-machine-vnet"
(myvmdocker) Creating virtual network.  name="docker-machine-vnet" rg="docker-machine" location="westus"
(myvmdocker) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(myvmdocker) Creating public IP address.  name="myvmdocker-ip" static=false
(myvmdocker) Creating network interface.  name="myvmdocker-nic"
(myvmdocker) Creating storage account.  sku=Standard_LRS name="vhdski0hvfazyd8mn991cg50" location="westus"
(myvmdocker) Creating virtual machine.  location="westus" size="Standard_A2" username="azureuser" osImage="canonical:UbuntuServer:16.04.0-LTS:latest" name="myvmdocker"
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvmdocker
```

## <a name="configure-your-docker-shell"></a>A Docker rendszerhéj konfigurálása
Csatlakozhat a Docker-állomás az Azure-ban, a megfelelő csatlakozási beállítások megadása. Amint a kimeneti végén, tekintse meg a kapcsolat a Docker-állomás az alábbiak szerint: 

```bash
docker-machine env myvmdocker
```

A kimenet a következő példához hasonló:

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://40.68.254.142:2376"
export DOCKER_CERT_PATH="/Users/user/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env myvmdocker)
```

A kapcsolat definiálásának beállítások is vagy futtassa az ajánlott konfiguráció parancsot (`eval $(docker-machine env myvmdocker)`), vagy manuálisan állíthatja be a környezeti változók. 

## <a name="run-a-container"></a>Egy tároló futtatása
A művelet futtatása egy alapszintű NGINX webkiszolgáló megadható, hogy a tároló megtekintéséhez. Hozzon létre egy tárolót a `docker run` és tegye elérhetővé a webes forgalom 80-as porton az alábbiak szerint:

```bash
docker run -d -p 80:80 --restart=always nginx
```

A kimenet a következő példához hasonló:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
ff3d52d8f55f: Pull complete
226f4ec56ba3: Pull complete
53d7dd52b97d: Pull complete
Digest: sha256:41ad9967ea448d7c2b203c699b429abe1ed5af331cd92533900c6d77490e0268
Status: Downloaded newer image for nginx:latest
675e6056cb81167fe38ab98bf397164b01b998346d24e567f9eb7a7e94fba14a
```

Tárolók futtató nézet `docker ps`. Az alábbi példa kimenetben látható a NGINX tároló kitett 80-as port fut:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>A tároló tesztelése
Szerezze be a nyilvános IP-cím Docker-állomás az alábbiak szerint:


```bash
docker-machine ip myvmdocker
```

Tekintse meg a tároló működés közben, nyisson meg egy webböngészőt, és adja meg a nyilvános IP-cím, az előző parancs kimenetében az áttelepítés előtt feljegyzett:

![Futó ngnix tároló](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Következő lépések
Állomások is létrehozhat a [Docker Virtuálisgép-bővítmény](dockerextension.md). A Docker Compose használ, tekintse meg a [Ismerkedés a Docker és az Azure-ban Compose](docker-compose-quickstart.md).
