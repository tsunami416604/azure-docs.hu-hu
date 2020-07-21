---
title: A Docker Compose használata
description: A Docker telepítése és használata Linux rendszerű virtuális gépeken az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: a56ad3fd5999600dfc576f0a0e34ff7221267934
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86502452"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Ismerkedés a Docker és a levélírás használatával többtárolós alkalmazások definiálásához és futtatásához az Azure-ban
Az [összeállítás](https://github.com/docker/compose)során egy egyszerű szövegfájl használatával határozhat meg egy több Docker-tárolóból álló alkalmazást. Ezután elindíthatja az alkalmazást egyetlen parancsban, amely mindent megtesz a definiált környezet üzembe helyezéséhez. Ebből a cikkből megtudhatja, hogyan hozhat létre gyorsan egy WordPress-blogot egy háttérbeli MariaDB az SQL Database-ben egy Ubuntu rendszerű virtuális gépen. Az összeállítás segítségével összetettebb alkalmazásokat is beállíthat.

Ez a cikk a [Azure Cloud Shell](https://shell.azure.com/bash) és az [Azure CLI](/cli/azure/install-azure-cli) 2.0.58-verziójának használatával a 2/14/2019-as utolsó tesztelésen esett át.

## <a name="create-docker-host-with-azure-cli"></a>Docker-gazdagép létrehozása az Azure CLI-vel
Telepítse a legújabb [Azure CLI](/cli/azure/install-az-cli2) -t, és jelentkezzen be egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával.

Először hozzon létre egy erőforráscsoportot a Docker-környezethez az [az Group Create](/cli/azure/group)paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Hozzon létre egy *cloud-init.txt* nevű fájlt, és illessze be a következő konfigurációt. Írja be a `sensible-editor cloud-init.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. 

```yaml
#include https://get.docker.com
```

Most hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az-vm-create) paranccsal. Használja a `--custom-data` paramétert a cloud-init konfigurációs fájl megadásához. Adja meg a *cloud-init.txt* konfiguráció teljes elérési útját, ha az aktuális munkakönyvtáron kívülre mentette. Az alábbi példa egy *myDockerVM* nevű virtuális gépet hoz létre, és megnyitja a 80-as portot a webes forgalom számára.

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

                 

## <a name="install-compose"></a>Összeállítás telepítése


SSH-t az új Docker-gazda virtuális gépre. Adja meg a saját IP-címét.

```bash
ssh azureuser@10.10.111.11
```

Telepítse a levélírás szolgáltatást a virtuális gépre.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Docker-compose. YML konfigurációs fájl létrehozása
Hozzon létre egy `docker-compose.yml` konfigurációs fájlt, amely meghatározza a virtuális gépen futtatandó Docker-tárolókat. A fájl meghatározza az egyes tárolókban futtatandó rendszerképet, a szükséges környezeti változókat, a függőségeket, a portokat és a tárolók közötti kapcsolatokat. A YML-fájl szintaxisával kapcsolatos részletekért lásd a [fájl összeállításának leírása](https://docs.docker.com/compose/compose-file/)című témakört.

Hozzon létre egy *Docker-compose. YML* fájlt. A fájlhoz a kedvenc szövegszerkesztővel adhat hozzá adatfájlokat. A következő példa létrehoz egy fájlt, amely rákérdez, `sensible-editor` hogy kiválassza a használni kívánt szerkesztőt.

```bash
sensible-editor docker-compose.yml
```

Illessze be a következő példát a Docker-összeállítási fájlba. Ez a konfiguráció a DockerHub- [beállításjegyzékből](https://registry.hub.docker.com/_/wordpress/) származó rendszerképeket használ a WordPress (nyílt forráskódú blog és tartalomkezelő rendszer) és egy csatolt háttérbeli MariaDB SQL Database telepítéséhez. Adja meg saját *MYSQL_ROOT_PASSWORDét*.

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

## <a name="start-the-containers-with-compose"></a>A tárolók elindítása összeállítással
A *Docker-compose. YML* fájllal megegyező könyvtárban futtassa a következő parancsot (a környezettől függően előfordulhat, hogy a használatával kell futtatnia `docker-compose` `sudo` ):

```bash
sudo docker-compose up -d
```

Ez a parancs elindítja a *Docker-compose. YML*által megadott Docker-tárolókat. Ennek a lépésnek a befejezéséhez egy-két percet vesz igénybe. A következőhöz hasonló kimenet jelenik meg:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


A tárolók előírásának ellenőrzéséhez írja be a következőt: `sudo docker-compose ps` . Ennek nagyjából a következőképpen kell kinéznie:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Mostantól közvetlenül a 80-es porton keresztül csatlakozhat a WordPresshez a virtuális gépen. Nyisson meg egy webböngészőt, és adja meg a virtuális gép IP-címét. Ekkor meg kell jelennie a WordPress Start képernyőjén, ahol elvégezheti a telepítést, és megkezdheti az alkalmazás megkezdését.

![WordPress-kezdőképernyő](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Következő lépések
* A többtárolós alkalmazások létrehozásával és üzembe helyezésével kapcsolatos további példákért tekintse meg az [összeállítás parancssori referenciáját](https://docs.docker.com/compose/reference/) és a [felhasználói útmutatót](https://docs.docker.com/compose/) .
* Használjon egy olyan Azure Resource Manager sablont, amely a saját vagy a [Közösség](https://azure.microsoft.com/documentation/templates/)által biztosított, egy Azure-beli virtuális gép üzembe helyezése a Docker használatával és egy összeállítással beállított alkalmazás. Például a [WordPress blog üzembe helyezése a Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) sablonnal a Docker használatával és a levélírás használatával gyorsan üzembe helyezheti a WordPresst egy Ubuntu virtuális gépen futó MySQL-háttérrel.
* Próbálja meg a Docker-összeállítást Docker Swarm-fürttel integrálni. Lásd: a [levélírás és a Swarm használata](https://docs.docker.com/compose/swarm/) forgatókönyvek esetén.
