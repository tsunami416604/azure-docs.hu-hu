---
title: A Docker Compose használata az Azure-beli Linuxos virtuális gépre |} A Microsoft Docs
description: Linux rendszerű virtuális gépek az Azure CLI-vel és a Docker Compose használatával
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
ms.date: 12/18/2017
ms.author: cynthn
ms.openlocfilehash: 227e6b87e5a131147ffebdeac045b9b27ab20dc8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991317"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Ismerkedés a Docker és a Compose megadásához és a egy többtárolós alkalmazást futtatni az Azure-ban
A [összeállítás](http://github.com/docker/compose), alkalmazások több Docker-tároló feladatátvétele meghatározásához használhat egy egyszerű szöveges fájlt. Majd alkalmazás üzembe helyezése egyetlen paranccsal, amely a megadott környezet telepítéséhez. Tegyük fel ez a cikk bemutatja, hogyan gyorsan beállíthat egy WordPress-blogbejegyzés-háttérrendszer MariaDB SQL database Ubuntu rendszerű virtuális gépen. Összeállítás használatával állítsa be a összetettebb alkalmazásokat.


## <a name="set-up-a-linux-vm-as-a-docker-host"></a>Állítsa be a Linux rendszerű virtuális gép Docker-gazdagépként
Segítségével különböző Azure eljárásokat és az elérhető rendszerképek vagy Resource Manager-sablonok az Azure piactéren elérhető Linux virtuális gépet, és állítsa be a Docker-gazdagépként. Lásd a [a környezet üzembe helyezése a Docker VM bővítmény használatával](dockerextension.md) hozhat létre gyorsan egy Ubuntu virtuális gép az Azure Docker VM bővítmény használatával egy [gyorsindítási sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

A Docker VM bővítmény használata esetén a virtuális gép automatikusan be van állítva a Docker-gazdagépként, és a Compose már telepítve van.


### <a name="create-docker-host-with-azure-cli"></a>Docker-gazdagép létrehozása az Azure CLI-vel
Telepítse a legújabb [Azure CLI-vel](/cli/azure/install-az-cli2) , és jelentkezzen be az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login).

Először hozzon létre egy erőforráscsoportot a Docker-környezetben a [az csoport létrehozása](/cli/azure/group#az_group_create). A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Ezután telepítse a virtuális gép [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#az_group_deployment_create) , amely tartalmazza az Azure Docker VM-bővítmény [ezen Azure Resource Manager-sablon a Githubon](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Amikor a rendszer kéri, adja meg a saját egyedi értékek *newStorageAccountName*, *adminUsername*, *adminPassword*, és *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Az üzembe helyezés befejeződik néhány percet vesz igénybe.


## <a name="verify-that-compose-is-installed"></a>Győződjön meg arról, hogy telepítve van-e az összeállítás
A virtuális gép, beleértve a DNS-név, a részletek megtekintéséhez használja a [az vm show](/cli/azure/vm#az_vm_show):

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

Ellenőrizze, hogy az összeállítás telepítve van-e a virtuális gépen, futtassa a következő parancsot:

```bash
docker-compose --version
```

Hasonló kimenet jelenik meg *1.6.2-es verzióján docker-compose, a 4d 72027 összeállítása*.

> [!TIP]
> Ha egy másik módszer, amellyel Docker-gazdagép létrehozása, és telepítenie kell a Compose saját magának, tekintse meg a [dokumentáció Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="create-a-docker-composeyml-configuration-file"></a>Hozzon létre egy docker-compose.yml konfigurációs fájlt
Következő lépésként létrehoz egy `docker-compose.yml` fájlt, amely csak egy szöveges-konfigurációs fájlt, adja meg a Docker-tárolók futtatásához a virtuális gépen. A fájl adja meg a kép a tárolók futtatását (vagy lehet, hogy készítsen egy Docker-fájlból) a szükséges környezeti változókat és a függőségek, a portok és a tárolók közötti hivatkozásokat. További részletek a yml file szintaxissal: [hivatkozást a Compose](https://docs.docker.com/compose/compose-file/).

Hozzon létre egy *docker-compose.yml* fájlt. A kedvenc szövegszerkesztőjével segítségével adatokat adhat hozzá a fájlhoz. Az alábbi példában a fájlt hoz létre egy kérés `sensible-editor` választja ki a használni kívánt szerkesztővé:

```bash
sensible-editor docker-compose.yml
```

Illessze be az alábbi példában a Docker Compose-fájlt. Ez a konfiguráció képeket használ a [DockerHub beállításjegyzék](https://registry.hub.docker.com/_/wordpress/) WordPress (a nyílt forráskódú blogplatform és a tartalom felügyeleti rendszer) és a egy csatolt háttérrendszer MariaDB SQL-adatbázis telepítése. Adja meg a saját *MYSQL_ROOT_PASSWORD* módon:

```sh
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
docker-compose up -d
```

Ez a parancs elindítja a Docker-tárolók megadott *docker-compose.yml*. Egy-a lépés végrehajtásához két percet vesz igénybe. Az alábbi példához hasonló kimenet jelenik meg:

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> Ügyeljen arra, hogy a **-d** indítási beállítást, hogy a tárolók folyamatosan fut a háttérben.


Győződjön meg arról, hogy a tárolók fel-e, írja be a következőt `docker-compose ps`. Hasonló üzenet jelenik meg:

```bash
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Mostantól csatlakozhat közvetlenül a virtuális gép 80-as porton a WordPress. Nyisson meg egy webböngészőt, és adja meg a virtuális gép DNS-nevét (például `http://mypublicdns.eastus.cloudapp.azure.com`). Ekkor megjelenik a WordPress start képernyőre, ahol elvégezheti a telepítést, és az alkalmazás használatának első lépései.

![A WordPress kezdőképernyője][wordpress_start]

## <a name="next-steps"></a>További lépések
* Nyissa meg a [Docker VM bővítmény – használati útmutató](https://github.com/Azure/azure-docker-extension/blob/master/README.md) további lehetőségei a Docker-beli virtuális gépen a Docker és a Compose konfigurálása. Például egy lehetőség, hogy az összeállítás yml fájl (konvertálja JSON) közvetlenül a Docker VM-bővítmény a konfigurációban.
* Tekintse meg a [összeállítása parancssori útmutatójának](http://docs.docker.com/compose/reference/) és [felhasználói útmutató](http://docs.docker.com/compose/) létrehozásának és többtárolós alkalmazások üzembe helyezésének további példákat.
* Az Azure Resource Manager-sablon, vagy használja a saját vagy egy hozzájárult a a [közösségi](https://azure.microsoft.com/documentation/templates/), egy Azure virtuális Gépen a Docker és a egy alkalmazást, állítsa be a összeállítás telepítéséhez. Például a [üzembe helyezése a docker használatával egy WordPress-bloghoz](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) sablon használja a Docker és a Compose gyors üzembe helyezését a WordPress egy MySQL-háttérrendszerrel Ubuntu rendszerű virtuális gépen.
* Próbálja ki a Docker Compose integrálása egy Docker Swarm-fürtöt. Lásd: [és a swarm együttes használatával Compose](https://docs.docker.com/compose/swarm/) forgatókönyvek esetén.

<!--Image references-->

[wordpress_start]: media/docker-compose-quickstart/WordPress.png
