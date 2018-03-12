---
title: "Használjon Docker Compose a Linux virtuális gép az Azure-ban |} Microsoft Docs"
description: "Docker és Compose használata az Azure parancssori Felülettel rendelkező Linux virtuális gépeken"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: 387f3095e8eebce3fa6c2f47ffc87995e65bfe2b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Ismerkedés a Docker és a Compose határozza meg, és futtassa a több tároló alkalmazást az Azure-ban
A [Compose](http://github.com/docker/compose), egy egyszerű szöveges fájl használatával definiálja az alkalmazást, amely több Docker-tároló. Majd lépett fel az alkalmazást egy parancs, amelyet az adott környezet telepítéséhez. Tegyük fel ez a cikk bemutatja, hogyan gyorsan be lehessen állítani egy WordPress-bloghoz az Ubuntu virtuális gép MariaDB SQL database-fájlok. Használhatja a Compose összetettebb alkalmazásokat beállítása.


## <a name="set-up-a-linux-vm-as-a-docker-host"></a>Linux virtuális gépet egy Docker-gazdagépként
Segítségével különböző Azure eljárások és a rendelkezésre álló képeket vagy a Resource Manager-sablonok az Azure piactéren hozzon létre egy Linux virtuális Gépet, és állítsa be a Docker-gazdagépként. Lásd például: [a Docker Virtuálisgép-bővítmény használatával a környezet](dockerextension.md) létrehozhat egy Ubuntu virtuális gép az Azure Docker Virtuálisgép-bővítmény használatával egy [gyorsindítási sablonon](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

A Docker Virtuálisgép-bővítmény használata esetén a virtuális gép automatikusan be van állítva egy Docker-gazdagépként, és új már telepítve van.


### <a name="create-docker-host-with-azure-cli-20"></a>Az Azure CLI 2.0 Docker állomás létrehozása
Telepítse a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) és való bejelentkezéshez az Azure fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login).

Először hozzon létre egy erőforráscsoportot a Docker környezetében [az csoport létrehozása](/cli/azure/group#az_group_create). A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Ezután telepítse a virtuális gép és [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#az_group_deployment_create) , amely tartalmazza az Azure Docker Virtuálisgép-bővítmény a [a Githubon az Azure Resource Manager sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Amikor a rendszer kéri, adja meg a saját egyedi értékeket az *newStorageAccountName*, *adminUsername*, *adminPassword*, és *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Az üzembe helyezés befejeződik néhány percet vesz igénybe.


## <a name="verify-that-compose-is-installed"></a>Győződjön meg arról, hogy telepítve van-e a Compose
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

Ellenőrizze, hogy az új telepítve van-e a virtuális Gépre, a következő parancsot:

```bash
docker-compose --version
```

Megjelenik a hasonló kimenetet *docker compose 1.6.2, a 4d 72027 build*.

> [!TIP]
> Ha egy másik módszer segítségével hozzon létre egy Docker-állomás, és telepítenie kell a Compose saját kezűleg, lásd: a [dokumentáció ír](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="create-a-docker-composeyml-configuration-file"></a>Egy docker-compose.yml konfigurációs fájl létrehozása
Ezután létrehozhat egy `docker-compose.yml` fájlt, amely csak egy konfigurációs szövegfájl, a Docker-tárolókban, a virtuális Gépen futtatandó meghatározásához. A fájl határozza meg a lemezkép futhat az egyes tárolókban (vagy annak oka lehet egy Dockerfile a build), szükséges környezeti változókat és a függőségeket, a portok és a tárolók közötti kapcsolatokat. További részletek a yml fájl Szintaxis: [hivatkozást a Compose](https://docs.docker.com/compose/compose-file/).

Hozzon létre egy *docker-compose.yml* fájlt. Kedvenc szövegszerkesztőjével segítségével bizonyos adatok hozzáadása a fájlhoz. Az alábbi példakód létrehozza a fájl egy kérdéshez tartozó `sensible-editor` kiválasztásához egy szerkesztőt, amelynek szeretné használni:

```bash
sensible-editor docker-compose.yml
```

Illessze be a következő példa a Docker Compose-fájlba. Ezt a konfigurációt használja a képek a [DockerHub beállításjegyzék](https://registry.hub.docker.com/_/wordpress/) WordPress (a nyílt forráskódú bloggolás és a tartalom rendszerrel) és a csatolt háttérkiszolgálón MariaDB SQL-adatbázis telepítése. Adja meg a saját *MYSQL_ROOT_PASSWORD* az alábbiak szerint:

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

## <a name="start-the-containers-with-compose"></a>Indítsa el a tárolók Compose
Ugyanabban a könyvtárban a *docker-compose.yml* fájl, a következő parancsot (a használt környezettől függően szükség lehet futtatásához `docker-compose` használatával `sudo`):

```bash
docker-compose up -d
```

A paranccsal elindítja a Docker-tároló megadott *docker-compose.yml*. Egy percet, amíg ez a lépés végrehajtásához szükséges. Az alábbi példához hasonló kimenet jelenik meg:

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> Ügyeljen arra, hogy használja a **-d** indítási lehetőséget, hogy a tárolók folyamatosan fut a háttérben.


Arról, hogy a tárolók, írja be a következőt `docker-compose ps`. Hasonlót kell megjelennie:

```bash
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

WordPress közvetlenül a 80-as porton virtuális Gépre is csatlakozhat. Nyisson meg egy webböngészőt, és írja be a virtuális Gépet DNS-nevét (például `http://mypublicdns.eastus.cloudapp.azure.com`). Meg kell jelennie a WordPress kezdőképernyőn, ahol fejezheti be a telepítést, és ismerkedjen meg az alkalmazást.

![WordPress kezdőképernyője][wordpress_start]

## <a name="next-steps"></a>További lépések
* Lépjen a [Docker VM bővítményének használati útmutatója](https://github.com/Azure/azure-docker-extension/blob/master/README.md) Docker és Compose konfigurálása a Docker VM további lehetőségekért. Például egy lehetőség áll a Compose yml fájlt (JSON formátumúvá konvertálni) közvetlenül a Docker Virtuálisgép-bővítmény konfigurációját.
* Tekintse meg a [állítható össze a parancssori útmutatójának](http://docs.docker.com/compose/reference/) és [felhasználói útmutató](http://docs.docker.com/compose/) további példák a kialakításához, és több tároló alkalmazások központi telepítéséhez.
* Az Azure Resource Manager-sablon, vagy használja a saját vagy egy része volt a a [közösségi](https://azure.microsoft.com/documentation/templates/), hogy az Azure virtuális gép Docker és állítsa be a Compose kérelmet. Például a [központi telepítése egy WordPress-bloghoz az Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) sablon és használ a Docker Compose gyorsan üzembe helyezhet WordPress egy Ubuntu virtuális gép a MySQL-fájlok.
* Próbálja a Docker Compose integrálása a Docker Swarm-fürt. Lásd: [használatával állítsa össze a Swarm](https://docs.docker.com/compose/swarm/) forgatókönyvek esetén.

<!--Image references-->

[wordpress_start]: media/docker-compose-quickstart/WordPress.png
