---
title: A MongoDB telepítése Linux rendszerű virtuális gépen az Azure CLI-vel
description: Ismerje meg, hogyan telepíthet és konfigurálhat MongoDB Linux rendszerű virtuális gépeken az Azure CLI-iusing
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 953e474eb5d160b38981d61fbfda051f1da9f968
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069591"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>A MongoDB telepítése és konfigurálása Linux rendszerű virtuális gépen

A [MongoDB](https://www.mongodb.org) egy népszerű, nyílt forráskódú, nagy teljesítményű NoSQL-adatbázis. Ebből a cikkből megtudhatja, hogyan telepítheti és konfigurálhatja a MongoDB egy Linux rendszerű virtuális gépen az Azure CLI-vel. Ilyenek például az alábbiak:

* [Alapszintű MongoDB-példány manuális telepítése és konfigurálása](#manually-install-and-configure-mongodb-on-a-vm)
* [Alapszintű MongoDB-példány létrehozása Resource Manager-sablonnal](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Hozzon létre egy összetett MongoDB-hez tartozó fürtözött fürtöt egy Resource Manager-sablon használatával.](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>MongoDB manuális telepítése és konfigurálása virtuális gépen
A MongoDB a Linux-disztribúciók [telepítési utasításait](https://docs.mongodb.com/manual/administration/install-on-linux/) , például a Red Hat/CentOS, a SUSE, az Ubuntu és a Debian rendszert is tartalmazza. Az alábbi példa egy *CentOS* virtuális gépet hoz létre. A környezet létrehozásához a legújabb [Azure CLI](/cli/azure/install-az-cli2) -t kell telepítenie, és be kell jelentkeznie egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. Az alábbi példa egy *myVM* nevű virtuális gépet hoz létre egy *azureuser* nevű felhasználóval az SSH nyilvános kulcsú hitelesítés használatával

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH-t a virtuális géphez saját felhasználónevével és az `publicIpAddress` előző lépés kimenetében szereplővel:

```bash
ssh azureuser@<publicIpAddress>
```

A MongoDB telepítési forrásainak hozzáadásához hozzon létre egy **yum** adattár-fájlt a következőképpen:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Nyissa meg szerkesztésre a MongoDB-tárház fájlját, például: `vi` vagy `nano` . Adja hozzá a következő sorokat:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Telepítse a MongoDB a **yum** használatával a következőképpen:

```bash
sudo yum install -y mongodb-org
```

Alapértelmezés szerint a SELinux a CentOS-lemezképeken kényszeríti ki, amely megakadályozza a MongoDB elérését. Telepítse a házirend-felügyeleti eszközöket, és konfigurálja a SELinux, hogy a MongoDB az alapértelmezett 27017-as TCP-porton működjön a következőképpen:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Indítsa el a MongoDB szolgáltatást a következőképpen:

```bash
sudo service mongod start
```

Ellenőrizze a MongoDB telepítését a helyi ügyfél használatával történő csatlakozással `mongo` :

```bash
mongo
```

Most tesztelje a MongoDB-példányt egy bizonyos adathozzáadással, majd a kereséssel:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Ha szükséges, konfigurálja a MongoDB, hogy automatikusan induljon el a rendszer újraindításakor:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Alapszintű MongoDB-példány létrehozása CentOS-sablonnal sablon használatával
Létrehozhat egy alapszintű MongoDB-példányt egyetlen CentOS virtuális gépen a GitHubról a következő Azure gyors útmutató sablon használatával. Ez a sablon a Linux rendszerhez készült egyéni szkript-bővítményt használja egy **yum** -tárház hozzáadásához az újonnan létrehozott CentOS virtuális géphez, majd telepíti a MongoDB-t.

* [Alapszintű MongoDB-példány a CentOS-on](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

A környezet létrehozásához a legújabb [Azure CLI](/cli/azure/install-az-cli2) -t kell telepítenie, és be kell jelentkeznie egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával. Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Ezután telepítse a MongoDB sablont az [az Group Deployment Create](/cli/azure/group/deployment)paranccsal. Ha a rendszer kéri, adja meg a saját egyedi értékeit a *newStorageAccountName*, a *dnsNameForPublicIP*és a rendszergazdai Felhasználónév és jelszó számára:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Jelentkezzen be a virtuális gépre a virtuális gép nyilvános DNS-címe segítségével. A nyilvános DNS-címeket az [az VM show](/cli/azure/vm)paranccsal tekintheti meg:

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH-t a virtuális géphez saját felhasználónevével és nyilvános DNS-címeként:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

A következő módon ellenőrizze a MongoDB telepítését a helyi ügyfél használatával történő csatlakozással `mongo` :

```bash
mongo
```

Most tesztelje a példányt úgy, hogy felvesz néhányat az alábbi módon:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Összetett MongoDB-fürtözött fürt létrehozása a CentOS-ben sablon használatával
A GitHubról a következő Azure gyors útmutató sablon segítségével hozhat létre összetett MongoDB-szegmensben lévő fürtözött fürtöt. Ez a sablon a [MongoDB szilánkokra osztott ajánlott eljárásokat](https://docs.mongodb.com/manual/core/sharded-cluster-components/) követi a redundancia és a magas rendelkezésre állás biztosítása érdekében. A sablon két szegmenst hoz létre, három csomóponttal az egyes kópiakészlet-készletekben. A rendszer egy, három csomóponttal rendelkező konfigurációs kiszolgáló-replikát is létrehoz, és két **mongos** útválasztó kiszolgálót is biztosít a szegmensekben lévő alkalmazások közötti konzisztencia biztosításához.

* [MongoDB skálázási fürt a CentOS-ben](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Ennek az összetett MongoDB-szegmensnek a üzembe helyezése több mint 20 magot igényel, ami általában az előfizetés alapértelmezett alapszáma régiónként. Nyisson meg egy Azure-támogatási kérést az alapvető darabszám növeléséhez.

A környezet létrehozásához a legújabb [Azure CLI](/cli/azure/install-az-cli2) -t kell telepítenie, és be kell jelentkeznie egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával. Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Ezután telepítse a MongoDB sablont az [az Group Deployment Create](/cli/azure/group/deployment)paranccsal. Adja meg a saját erőforrások nevét és méretét, ha szükséges, például *mongoAdminUsername*, *sizeOfDataDiskInGB*és *configNodeVmSize*:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster \
  --no-wait
```

Ez az üzembe helyezés egy óráig is elvégezhető az összes virtuálisgép-példány üzembe helyezése és konfigurálása során. Az `--no-wait` előző parancs végén a jelzőt használja a rendszer a parancssor visszaadásához, amint az Azure platform elfogadta a sablon központi telepítését. Ezután megtekintheti a központi telepítés állapotát az [az Group Deployment show](/cli/azure/group/deployment)paranccsal. A következő példa a *myMongoDBCluster* -telepítés állapotát tekinti át a *myResourceGroup* erőforráscsoporthoz:

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>További lépések
Ezekben a példákban helyileg csatlakozik a MongoDB-példányhoz a virtuális gépről. Ha másik virtuális gépről vagy hálózatról szeretne csatlakozni a MongoDB-példányhoz, győződjön meg arról, hogy a megfelelő [hálózati biztonsági csoportra vonatkozó szabályok jönnek létre](nsg-quickstart.md).

Ezek a példák a központi MongoDB környezetet telepítik fejlesztési célokra. Alkalmazza a környezetéhez szükséges biztonsági konfigurációs beállításokat. További információt a [MongoDB biztonsági dokumentációjában](https://docs.mongodb.com/manual/security/)talál.

További információ a sablonok használatával történő létrehozásáról: [Azure Resource Manager Overview (áttekintés](../../azure-resource-manager/management/overview.md)).

A Azure Resource Manager-sablonok az egyéni szkriptek bővítményét használják a parancsfájlok letöltéséhez és végrehajtásához a virtuális gépeken. További információ: [Az Azure egyéni szkriptek bővítmény használata a Linux Virtual Machines használatával](../extensions/custom-script-linux.md).
