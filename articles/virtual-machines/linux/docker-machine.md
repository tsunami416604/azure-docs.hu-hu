---
title: Linux-gazdagépek létrehozása a Docker Machine segítségével
description: Bemutatja, hogyan hozhat létre Docker-gazdagépeket a Docker-gép használatával az Azure-ban.
author: cynthn
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: c3165410809d98fd0ac4eeb515fbf30578633ef3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968805"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>A Docker Machine használata gazdagépek létrehozásához az Azure-ban
Ez a cikk részletezi, hogyan hozhat létre állomásokat a [Docker Machine](https://docs.docker.com/machine/) használatával az Azure-ban. A `docker-machine` parancs létrehoz egy Linux virtuális gépet (VM) az Azure-ban, majd telepíti a Dockert. Ezután kezelheti a Docker-állomások at Az Azure-ban ugyanazokat a helyi eszközöket és munkafolyamatokat. A docker-machine windows 10-ben való használatához Linux bash-t kell használnia.

## <a name="create-vms-with-docker-machine"></a>Virtuális gépek létrehozása a Docker-géppel
Először szerezze be az Azure-előfizetés-azonosítóját [az az-fiókkal](/cli/azure/account) az alábbiak szerint:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Docker-gazdagépeket hozhat létre `docker-machine create` az Azure-ban az *azure-illesztőprogramként* való megadásával. További információt a [Docker Azure Driver dokumentációjában](https://docs.docker.com/machine/drivers/azure/) talál.

A következő példa létrehoz egy *myVM*nevű virtuális gép , a "Standard D2 v2" csomag alapján, létrehoz egy *azureuser*nevű felhasználói fiókot, és megnyitja a *80-as* portot a gazdagép virtuális gépen. Kövesse az utasításokat az Azure-fiókjába való bejelentkezéshez, és adjon engedélyt a Docker Machine-nek az erőforrások létrehozásához és kezeléséhez.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_DS2_v2" \
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

## <a name="configure-your-docker-shell"></a>A Docker-rendszerhéj konfigurálása
Ha csatlakozni szeretne a Docker-állomáshoz az Azure-ban, adja meg a megfelelő kapcsolatbeállításokat. Ahogy azt a kimenet végén is megjegyeztük, tekintse meg a Docker-gazdagép kapcsolati adatait az alábbiak szerint: 

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

A kapcsolatbeállításainak meghatározásához futtassa a`eval $(docker-machine env myvm)`javasolt konfigurációs parancsot ( ), vagy manuálisan is beállíthatja a környezeti változókat. 

## <a name="run-a-container"></a>Tároló futtatása
Ha működés közben szeretne egy tárolót látni, futtasson egy alapvető NGINX webkiszolgálót. Hozzon létre `docker run` egy tárolót, és tegye elérhetővé a 80-as portot a webes forgalomszámára az alábbiak szerint:

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

A futó `docker ps`tárolók megtekintése a segítségével. A következő példa kimeneta a 80-as porttal működő NGINX-tárolót mutatja:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>A tartály tesztelése
Szerezze be a Docker-állomás nyilvános IP-címét az alábbiak szerint:


```bash
docker-machine ip myvm
```

A tároló működés közbeni megtekintéséhez nyisson meg egy webböngészőt, és adja meg az előző parancs kimenetében megadott nyilvános IP-címet:

![Ngnix-tároló futtatása](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>További lépések
A Docker Compose használatával kapcsolatos példákat a [Docker és a Compose azure-ban való használatának első lépései.](docker-compose-quickstart.md)
