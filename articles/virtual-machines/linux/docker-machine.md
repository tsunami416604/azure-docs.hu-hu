---
title: Linux-gazdagépek létrehozása a Docker Machine használatával
description: Ismerteti, hogyan hozhat létre Docker-gazdagépeket az Azure-ban a Docker Machine használatával.
author: cynthn
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: c3165410809d98fd0ac4eeb515fbf30578633ef3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78968805"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Gazdagépek létrehozása az Azure-ban a Docker Machine használatával
Ebből a cikkből megtudhatja, hogyan hozhat létre gazdagépeket az Azure-ban a [Docker Machine](https://docs.docker.com/machine/) használatával. A `docker-machine` parancs létrehoz egy linuxos virtuális gépet (VM) az Azure-ban, majd telepíti a Docker-t. Ezután az Azure-beli Docker-gazdagépeket ugyanazzal a helyi eszközökkel és munkafolyamatokkal kezelheti. A Windows 10-es Docker-Machine használatához Linux bash-t kell használnia.

## <a name="create-vms-with-docker-machine"></a>Virtuális gépek létrehozása Docker-géppel
Először szerezze be az Azure-előfizetés AZONOSÍTÓját az [az Account show](/cli/azure/account) paranccsal az alábbiak szerint:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Az Azure-ban `docker-machine create` az *Azure* -t kell megadnia illesztőprogramként a Docker-gazdagépek létrehozásához. További információt a [Docker Azure-illesztőprogram dokumentációjában](https://docs.docker.com/machine/drivers/azure/) talál.

Az alábbi példa egy *myVM*nevű virtuális gépet hoz létre a "standard D2 v2" csomag alapján, létrehoz egy *azureuser*nevű felhasználói fiókot, és megnyitja a *80* -as portot a gazdagép virtuális gépén. Az Azure-fiókba való bejelentkezéshez és az erőforrások létrehozásához és kezeléséhez a Docker Machine-engedélyek megadásához kövesse az utasításokat.

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
Ha a Docker-gazdagéphez szeretne csatlakozni az Azure-ban, adja meg a megfelelő kapcsolatbeállításokat. A kimenet végén látható módon tekintse meg a Docker-gazdagéphez tartozó kapcsolatok adatait a következőképpen: 

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

A kapcsolódási beállítások megadásához futtassa a javasolt konfigurációs parancsot ( `eval $(docker-machine env myvm)` ), vagy manuálisan is beállíthatja a környezeti változókat. 

## <a name="run-a-container"></a>Tároló futtatása
Ha működés közben szeretné megtekinteni egy tárolót, lehetővé teszi egy alapszintű NGINX-webkiszolgáló futtatását. Az alábbiak szerint hozzon létre egy tárolót, `docker run` és tegye elérhetővé a 80-as portot a webes forgalom számára:

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

Futó tárolók megtekintése a rel `docker ps` . A következő példa kimenetében a 80-as porton futó NGINX-tároló látható:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>A tároló tesztelése
Szerezze be a Docker-gazdagép nyilvános IP-címét a következőképpen:


```bash
docker-machine ip myvm
```

A tároló működés közbeni megtekintéséhez nyisson meg egy webböngészőt, és adja meg az előző parancs kimenetében feljegyzett nyilvános IP-címet:

![Ngnix-tároló futtatása](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>További lépések
A Docker-összeállítás használatával kapcsolatos Példákért lásd: a [Docker használatának első lépései és az Azure-beli összeállítás](docker-compose-quickstart.md).
