---
title: A Docker Compose használata az Azure-beli Linuxos virtuális gépre |} A Microsoft Docs
description: Hogyan telepítheti és használhatja a Docker és a Compose Linux rendszerű virtuális gépek az Azure CLI-vel
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 3aa3b29ef44d3efb21237dc0d82a1ee6e99e729b
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328945"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Ismerkedés a Docker és a Compose megadásához és a egy többtárolós alkalmazást futtatni az Azure-ban
A [összeállítás](http://github.com/docker/compose), alkalmazások több Docker-tároló feladatátvétele meghatározásához használhat egy egyszerű szöveges fájlt. Majd alkalmazás üzembe helyezése egyetlen paranccsal, amely a megadott környezet telepítéséhez. Tegyük fel ez a cikk bemutatja, hogyan gyorsan beállíthat egy WordPress-blogbejegyzés-háttérrendszer MariaDB SQL database Ubuntu rendszerű virtuális gépen. Összeállítás használatával állítsa be a összetettebb alkalmazásokat.

Ez a cikk utolsó tesztelésének 2/14/2019 használatával a [Azure Cloud Shell](https://shell.azure.com/bash) és a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli) 2.0.58 verzió.

## <a name="create-docker-host-with-azure-cli"></a>Docker-gazdagép létrehozása az Azure CLI-vel
Telepítse a legújabb [Azure CLI-vel](/cli/azure/install-az-cli2) , és jelentkezzen be az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index).

Először hozzon létre egy erőforráscsoportot a Docker-környezetben a [az csoport létrehozása](/cli/azure/group). A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Hozzon létre egy fájlt *cloud-init.txt* , és illessze be a következő konfigurációt. Írja be a `sensible-editor cloud-init.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. 

```yaml
#include https://get.docker.com
```

Most hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az-vm-create) paranccsal. Használja a `--custom-data` paramétert a cloud-init konfigurációs fájl megadásához. Adja meg a *cloud-init.txt* konfiguráció teljes elérési útját, ha az aktuális munkakönyvtáron kívülre mentette. A következő példában létrehozunk egy nevű virtuális Gépet *myDockerVM* , és megnyílik a webes forgalom 80-as port.

```azurecli-interactive
az vm create \
    --resource-group myDockerGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
az vm open-port --port 80 \
    --resource-group myDockerGroup \
    --name myDockerVM
```

A virtuális gép létrehozása, a csomagok telepítése és az alkalmazás elindítása néhány percig tart. Néhány háttérfeladat azután is tovább fut, hogy az Azure CLI visszairányítja Önt a parancssorhoz. A virtuális gép létrehozása után jegyezze fel az Azure CLI által megjelenített `publicIpAddress` értéket. 

                 

## <a name="install-compose"></a>Telepítés Compose


Az új Docker-gazdagép virtuális gép ssh-KAPCSOLATOT. Adja meg a saját IP-címét.

```bash
ssh azureuser@10.10.111.11
```

Telepítés állítsa össze a virtuális gépen.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Hozzon létre egy docker-compose.yml konfigurációs fájlt
Hozzon létre egy `docker-compose.yml` konfigurációs fájlt, és adja meg a Docker-tárolók futtatásához a virtuális gépen. A fájl adja meg a futó tárolók közötti egyes tárolók, szükséges környezeti változókat és a függőségek, portokat és a hivatkozások a lemezképet. További részletek a yml file szintaxissal: [hivatkozást a Compose](https://docs.docker.com/compose/compose-file/).

Hozzon létre egy *docker-compose.yml* fájlt. A kedvenc szövegszerkesztőjével segítségével adatokat adhat hozzá a fájlhoz. Az alábbi példában a fájlt hoz létre egy kérés `sensible-editor` választja ki egy szerkesztőt, amelyet használni kíván.

```bash
sensible-editor docker-compose.yml
```

Illessze be az alábbi példában a Docker Compose-fájlt. Ez a konfiguráció képeket használ a [DockerHub beállításjegyzék](https://registry.hub.docker.com/_/wordpress/) WordPress (a nyílt forráskódú blogplatform és a tartalom felügyeleti rendszer) és a egy csatolt háttérrendszer MariaDB SQL-adatbázis telepítése. Adja meg a saját *MYSQL_ROOT_PASSWORD*.

```yml
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>Indítsa el a tárolókat az összeállítás
Ugyanabban a címtárban a *docker-compose.yml* fájlt, az alábbi parancsot (a környezettől függően előfordulhat, hogy futtatni szeretné `docker-compose` használatával `sudo`):

```bash
sudo docker-compose up -d
```

Ez a parancs elindítja a Docker-tárolók megadott *docker-compose.yml*. Egy-a lépés végrehajtásához két percet vesz igénybe. Az alábbihoz hasonló kimenet jelenik meg:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Győződjön meg arról, hogy a tárolók fel-e, írja be a következőt `sudo docker-compose ps`. Hasonló üzenet jelenik meg:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Mostantól csatlakozhat közvetlenül a virtuális gép 80-as porton a WordPress. Nyisson meg egy webböngészőt, és adja meg a virtuális gép IP-cím neve. Ekkor megjelenik a WordPress start képernyőre, ahol elvégezheti a telepítést, és az alkalmazás használatának első lépései.

![A WordPress kezdőképernyője](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>További lépések
* Tekintse meg a [összeállítása parancssori útmutatójának](http://docs.docker.com/compose/reference/) és [felhasználói útmutató](http://docs.docker.com/compose/) létrehozásának és többtárolós alkalmazások üzembe helyezésének további példákat.
* Az Azure Resource Manager-sablon, vagy használja a saját vagy egy hozzájárult a a [közösségi](https://azure.microsoft.com/documentation/templates/), egy Azure virtuális Gépen a Docker és a egy alkalmazást, állítsa be a összeállítás telepítéséhez. Például a [üzembe helyezése a docker használatával egy WordPress-bloghoz](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) sablon használja a Docker és a Compose gyors üzembe helyezését a WordPress egy MySQL-háttérrendszerrel Ubuntu rendszerű virtuális gépen.
* Próbálja ki a Docker Compose integrálása egy Docker Swarm-fürtöt. Lásd: [és a swarm együttes használatával Compose](https://docs.docker.com/compose/swarm/) forgatókönyvek esetén.

