---
title: A Docker Machine használata Linux-rendszerű gazdagépek létrehozása az Azure-ban |} A Microsoft Docs
description: Docker gazdagépek létrehozása az Azure-ban a Docker Machine használatával módját ismerteti.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: a295c7533369033f0a8c732c134481760a8e913e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930579"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>A Docker Machine használata gazdagépek létrehozása az Azure-ban
Ez a cikk részletesen használata [a Docker Machine](https://docs.docker.com/machine/) gazdagépek létrehozása az Azure-ban. A `docker-machine` parancs létrehoz egy Linux rendszerű virtuális gép (VM) az Azure-ban, majd telepíti a Docker. Ezután kezelheti az Azure-ban, a helyi eszközök és a munkafolyamatok a Docker-gazdagépek. Dockeres gép használata a Windows 10, Linux bash kell használnia.

## <a name="create-vms-with-docker-machine"></a>Virtuális gép létrehozása a Docker Machine-vel
Először szerezze be az Azure-előfizetési Azonosítóját a [az fiók show](/cli/azure/account#az_account_show) módon:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Docker-gazdagép virtuális gépeket hoz létre az Azure-ban `docker-machine create` megadásával *azure* , az illesztőprogramot. További információkért lásd: a [illesztőprogram a Docker Azure-dokumentáció](https://docs.docker.com/machine/drivers/azure/)

A következő példában létrehozunk egy nevű virtuális Gépet *myVM*, "Standard D2 v2" terv alapján, nevű felhasználói fiókot hoz létre *azureuser*, és megnyílik a port *80-as* a gazdagépen a virtuális gép. Minden olyan útmutatást követve jelentkezzen be az Azure-fiókjával, és a Docker Machine engedélyeket erőforrások létrehozásához és kezeléséhez.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_D2_v2" \
    myvm
```

A kimenet a következő példához hasonlóan néz ki:

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(myvm) Completed machine pre-create checks.
Creating machine...
(myvm) Querying existing resource group.  name="docker-machine"
(myvm) Creating resource group.  name="docker-machine" location="westus"
(myvm) Configuring availability set.  name="docker-machine"
(myvm) Configuring network security group.  name="myvm-firewall" location="westus"
(myvm) Querying if virtual network already exists.  rg="docker-machine" location="westus" name="docker-machine-vnet"
(myvm) Creating virtual network.  name="docker-machine-vnet" rg="docker-machine" location="westus"
(myvm) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(myvm) Creating public IP address.  name="myvm-ip" static=false
(myvm) Creating network interface.  name="myvm-nic"
(myvm) Creating storage account.  sku=Standard_LRS name="vhdski0hvfazyd8mn991cg50" location="westus"
(myvm) Creating virtual machine.  location="westus" size="Standard_A2" username="azureuser" osImage="canonical:UbuntuServer:16.04.0-LTS:latest" name="myvm
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
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvm
```

## <a name="configure-your-docker-shell"></a>A Docker-shell konfigurálása
Szeretne csatlakozni az Azure-ban a Docker-gazdagép, a megfelelő kapcsolati beállítások megadása. A kimeneti végén feljegyzett megtekintése a Docker-gazdagép kapcsolati adatait a következő: 

```bash
docker-machine env myvm
```

A kimenet a következő példához hasonló:

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://40.68.254.142:2376"
export DOCKER_CERT_PATH="/Users/user/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env myvm)
```

Adja meg a kapcsolat beállításait, vagy futtathatja a javasolt konfiguráció parancsot (`eval $(docker-machine env myvm)`), vagy manuálisan is beállíthatja a környezeti változókat. 

## <a name="run-a-container"></a>Egy tároló futtatása
A művelet lehetővé teszi, hogy egy alapszintű NGINX-webkiszolgáló futtatása egy tároló megtekintéséhez. Hozzon létre egy tárolót az `docker run` és elérhetővé tehettük webes forgalom 80-as portot a következő:

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

Futó tárolók a nézet `docker ps`. Az alábbi példa kimenetében látható az NGINX-tároló elérhető 80-as portot és a fut:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>A tároló tesztelése
Szerezze be a Docker-gazdagép a nyilvános IP-cím a következő:


```bash
docker-machine ip myvm
```

A tároló működés közben látni, nyisson meg egy webböngészőt, és adja meg a nyilvános IP-címet az előző parancs kimenetében feljegyzett:

![Futó ngnix tároló](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>További lépések
A gazdagépek is létrehozhat a [Docker VM-bővítmény](dockerextension.md). A Docker Compose használatával példákért lásd [Ismerkedés a Docker és a Compose az Azure-ban](docker-compose-quickstart.md).
