---
title: A Docker Compose használata
description: A Docker és a Compose linuxos virtuális gépeken való telepítése és használata az Azure CLI-vel.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 434a3ef8c9bc1738252d59a5dca5bec16d85e45e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970302"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Ismerkedjen meg a Dockerrel és a Compose szolgáltatással egy többtárolós alkalmazás definiálásához és futtatásához az Azure-ban
A [Compose](https://github.com/docker/compose)segítségével egy egyszerű szöveges fájlt definiálni egy alkalmazás, amely több Docker-tárolók. Ezután egyetlen parancsban üzembe helyezheti az alkalmazást, amely mindent megtesz a meghatározott környezet üzembe helyezéséhez. Például ez a cikk bemutatja, hogyan lehet gyorsan beállítani egy WordPress blogot egy háttér MariaDB SQL adatbázissal egy Ubuntu VM-en. Összetettebb alkalmazások beállításához is használhatja a Compose segítségével.

Ezt a cikket utoljára 2019.02.14-én teszteltük az [Azure Cloud Shell](https://shell.azure.com/bash) és az Azure [CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 2.0.58-as verziójával.

## <a name="create-docker-host-with-azure-cli"></a>Docker-gazdagép létrehozása az Azure CLI-vel
Telepítse a legújabb [Azure CLI-t,](/cli/azure/install-az-cli2) és jelentkezzen be egy Azure-fiókba [az az login](/cli/azure/reference-index)használatával.

Először hozzon létre egy erőforráscsoportot a Docker-környezethez [az csoport létrehozásával.](/cli/azure/group) A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Hozzon létre egy *cloud-init.txt* nevű fájlt, és illessze be a következő konfigurációt. Írja be a `sensible-editor cloud-init.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. 

```yaml
#include https://get.docker.com
```

Most hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az-vm-create) paranccsal. Használja a `--custom-data` paramétert a cloud-init konfigurációs fájl megadásához. Adja meg a *cloud-init.txt* konfiguráció teljes elérési útját, ha az aktuális munkakönyvtáron kívülre mentette. A következő példa létrehoz egy *myDockerVM* nevű virtuális gép, és megnyitja a 80-as portot a webes forgalom számára.

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

                 

## <a name="install-compose"></a>Compose telepítése


SSH az új Docker-gazdagép virtuális gép. Adja meg saját IP-címét.

```bash
ssh azureuser@10.10.111.11
```

Telepítse a Compose alkalmazást a virtuális gépre.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Docker-compose.yml konfigurációs fájl létrehozása
Hozzon `docker-compose.yml` létre egy konfigurációs fájlt a virtuális gépen futtatandó Docker-tárolók meghatározásához. A fájl határozza meg az egyes tárolókon futtatandó lemezképet, a szükséges környezeti változókat és függőségeket, portokat és a tárolók közötti kapcsolatokat. Az yml fájl szintaxisával kapcsolatos részleteket a [Fájlhivatkozás összeállítása .com.](https://docs.docker.com/compose/compose-file/)

Hozzon létre egy *docker-compose.yml* fájlt. Kedvenc szövegszerkesztője segítségével adhat hozzá adatokat a fájlhoz. A következő példa a fájlt `sensible-editor` egy általa használni kívánt szerkesztő kiválasztására vonatkozó üzenettel hozza létre.

```bash
sensible-editor docker-compose.yml
```

Illessze be a következő példát a Docker-írás fájlba. Ez a konfiguráció a [DockerHub rendszerleíró adatbázisból](https://registry.hub.docker.com/_/wordpress/) származó lemezképeket használja a WordPress (a nyílt forráskódú blog- és tartalomkezelő rendszer) és a kapcsolódó Háttér-MariaDB SQL adatbázis telepítéséhez. Adja meg saját *MYSQL_ROOT_PASSWORD*.

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

## <a name="start-the-containers-with-compose"></a>A tárolók indítása a Compose segítségével
Ugyanabban a könyvtárban, mint a *docker-compose.yml* fájl, fuss a következő parancsot `sudo`(attól függően, hogy a környezet, akkor lehet, hogy fut a): `docker-compose`

```bash
sudo docker-compose up -d
```

Ez a parancs elindítja a *docker-compose.yml-ben*megadott Docker-tárolókat. A lépés végrehajtásához egy-két percet vesz igénybe. A kimenet az alábbihoz hasonló lesz:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Annak ellenőrzéséhez, hogy a `sudo docker-compose ps`tárolók fent vannak-e, írja be a következőt: Ennek nagyjából a következőképpen kell kinéznie:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Most már közvetlenül a 80-as porton csatlakozhat a WordPress-hez a virtuális gépen. Nyisson meg egy webböngészőt, és adja meg a virtuális gép IP-címét. Most látnia kell a WordPress kezdőképernyőjét, ahol befejezheti a telepítést és elkezdheti az alkalmazást.

![WordPress kezdőképernyő](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>További lépések
* Tekintse meg a [Parancssori útmutató](https://docs.docker.com/compose/reference/) és [a felhasználói útmutató](https://docs.docker.com/compose/) további példákat a többtárolós alkalmazások létrehozásáról és üzembe helyezéséről.
* Azure Resource Manager-sablon használatával, akár a saját, vagy egy hozzájárult a [közösség,](https://azure.microsoft.com/documentation/templates/)üzembe helyezhet egy Azure virtuális gép a Docker és egy alkalmazás beállítása a Compose. Például a [Deploy a WordPress blog Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) sablon segítségével Docker és compose gyorsan telepíteni WordPress egy MySQL háttérrendszer egy Ubuntu VM.
* Próbálja meg integrálni a Docker Compose-t egy Docker-raj fürttel. Lásd: [A Sraj összeállítása](https://docs.docker.com/compose/swarm/) forgatókönyveket.

