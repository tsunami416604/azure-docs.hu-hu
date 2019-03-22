---
title: A MongoDB telepítése egy Linux rendszerű virtuális gépen az Azure CLI-vel |} A Microsoft Docs
description: Ismerje meg, hogyan telepítse és konfigurálja a MongoDB egy Linux rendszerű virtuális gép iusing az Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 5fadf23cc1fc2e1a6092c48033580d398fc689a0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58012810"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Telepítése és konfigurálása a MongoDB egy Linux rendszerű virtuális gépen

[MongoDB](https://www.mongodb.org) egy népszerű nyílt forráskódú, nagy teljesítményű NoSQL-adatbázis. Ez a cikk bemutatja, hogyan telepítése és konfigurálása a MongoDB egy Linux rendszerű virtuális gépen az Azure CLI használatával. Példák hogyan jelenik meg, amelyek részletesen a:

* [Kézi telepítését és konfigurálását egy alapszintű MongoDB-példányban](#manually-install-and-configure-mongodb-on-a-vm)
* [Hozzon létre egy alapszintű MongoDB-példányban, Resource Manager-sablon használatával](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Hozzon létre egy összetett MongoDB, a replika horizontálisan skálázott fürt beállítja a Resource Manager-sablon használatával](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Kézi telepítését és konfigurálását a MongoDB egy virtuális gépen
MongoDB [adja meg a telepítési utasításokat](https://docs.mongodb.com/manual/administration/install-on-linux/) többek között a Red Hat Linux-disztribúciók esetében / CentOS, SUSE, Ubuntu és a Debian. A következő példában létrehozunk egy *CentOS* virtuális Gépet. A környezet létrehozására, a legújabb kell [Azure CLI-vel](/cli/azure/install-az-cli2) telepítve, és bejelentkezett egy Azure-fiókba az [az bejelentkezési](/cli/azure/reference-index).

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. A következő példában létrehozunk egy nevű virtuális Gépet *myVM* nevű felhasználóhoz *azureuser* SSH nyilvános kulcsos hitelesítés használatával

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Ssh-KAPCSOLATOT a virtuális gép használata a saját felhasználónevét és a `publicIpAddress` a kimenetben az előző lépésben szereplő:

```bash
ssh azureuser@<publicIpAddress>
```

Adja hozzá a telepítési források a mongodb-hez, hozzon létre egy **yum** tárház fájlt az alábbiak szerint:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Nyissa meg a MongoDB-tárház fájlt szerkesztésre, mint például a `vi` vagy `nano`. Adja hozzá a következő sorokat:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Telepítse a MongoDB használatával **yum** módon:

```bash
sudo yum install -y mongodb-org
```

Alapértelmezés szerint a CentOS-lemezképek, amely meggátolja a mongodb-hez hozzáférő SELinux lép érvénybe. Csoportházirend-kezelő eszközök telepítése és konfigurálása, hogy az alapértelmezett TCP-porton 27017 működéséhez a következő MongoDB SELinux:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Indítsa el a MongoDB-szolgáltatást a következőképpen:

```bash
sudo service mongod start
```

Ellenőrizze a MongoDB telepítése a helyi `mongo` ügyfél:

```bash
mongo
```

Bizonyos adatok hozzáadásával, és ezután a keresés, tesztelje a MongoDB-példányban:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Ha szükséges, konfigurálja a MongoDB automatikus indításra során a rendszer újraindítása:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Alapszintű MongoDB-példány létrehozása a CentOS egy sablon használatával
Egy alapszintű MongoDB-példányban githubból az alábbi Azure gyors üzembe helyezés sablon használatával egy CentOS virtuális gépen is létrehozhat. Ez a sablon az egyéni szkriptek futtatására szolgáló bővítmény linuxhoz használatával adhat hozzá egy **yum** az újonnan létrehozott CentOS virtuális Gépet és telepítse a MongoDB-adattár.

* [Alapszintű, a CentOS MongoDB-példányban](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

A környezet létrehozására, a legújabb kell [Azure CLI-vel](/cli/azure/install-az-cli2) telepítve, és bejelentkezett egy Azure-fiókba az [az bejelentkezési](/cli/azure/reference-index). Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Ezután telepítse a MongoDB-sablont a [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment). Amikor a rendszer kéri, adja meg a saját egyedi értékek *newStorageAccountName*, *dnsNameForPublicIP*, és rendszergazdai felhasználónevét és jelszavát:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Jelentkezzen be a virtuális Gépet a virtuális gép nyilvános DNS-címének használatával. Megtekintheti a nyilvános DNS-cím [az vm show](/cli/azure/vm):

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

Ssh-KAPCSOLATOT a virtuális gépet, a saját felhasználónevét és a nyilvános DNS-cím:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Ellenőrizze a MongoDB telepítése a helyi `mongo` ügyfél az alábbiak szerint:

```bash
mongo
```

Most tesztelheti a példány hozzáadásával bizonyos adatokat, és keressen a következő:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Hozzon létre egy összetett horizontálisan skálázott MongoDB-fürtöt CentOS-sablon használatával
Létrehozhat egy összetett MongoDB horizontálisan skálázott fürtöt a következő Azure gyorsindítási sablont a Githubból. Ez a sablon a következő a [MongoDB horizontálisan skálázott fürthöz ajánlott eljárások](https://docs.mongodb.com/manual/core/sharded-cluster-components/) redundancia és magas rendelkezésre állás biztosításához. A sablon létrehozza a két szegmensek minden replika három csomóponttal. Egy konfigurációs kiszolgáló replika három csomóponttal rendelkező is létrejön, valamint két **mongos** útválasztó kiszolgálók biztosítsa a konzisztenciát az alkalmazások a szegmensek között.

* [A mongodb-hez a horizontális skálázás fürt CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Ez összetett horizontálisan skálázott MongoDB-fürt üzembe helyezésekor kell lennie, több mint 20 mag, ami általában az előfizetéshez tartozó régiónkénti alapértelmezett-magok száma. Nyisson meg egy Azure-támogatási kérést, hogy növelje a magok száma.

A környezet létrehozására, a legújabb kell [Azure CLI-vel](/cli/azure/install-az-cli2) telepítve, és bejelentkezett egy Azure-fiókba az [az bejelentkezési](/cli/azure/reference-index). Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Ezután telepítse a MongoDB-sablont a [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment). Adja meg a saját erőforrás-neveket, és ahol szükséges, például a méretű *mongoAdminUsername*, *sizeOfDataDiskInGB*, és *configNodeVmSize*:

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

A központi telepítés is igénybe vehet, telepítheti és konfigurálhatja a Virtuálisgép-példányok egy óránál. A `--no-wait` jelző szolgál az előző parancs végén vezérlési visszatérhet a parancssor használatával után a sablon üzembe helyezéséhez az Azure platform által elfogadott. Ezután megtekintheti a központi telepítési állapot [az csoport deployment show](/cli/azure/group/deployment). Az alábbi példa megjeleníti az állapotát a *myMongoDBCluster* központi telepítést, a *myResourceGroup* erőforráscsoportot:

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>További lépések
Ezekben a példákban csatlakozik a MongoDB-példányban helyileg a virtuális gépről. Ha szeretne egy másik virtuális Géphez vagy hálózati csatlakozás a MongoDB-példányban, győződjön meg, hogy a megfelelő [jönnek létre a hálózati biztonsági csoport szabályai](nsg-quickstart.md).

Ezekben a példákban üzembe helyezése a core MongoDB környezet fejlesztési célra. A alkalmazni a szükséges biztonsági beállítási lehetőségei a környezetben. További információkért lásd: a [MongoDB biztonsági docs](https://docs.mongodb.com/manual/security/).

Létrehozásával sablonok használatával kapcsolatos további információkért lásd: a [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md).

Az Azure Resource Manager-sablonok az egyéni Szkriptbővítmény használatával töltsön le, és szkriptek végrehajtása a virtuális gépeken. További információkért lásd: [az Azure egyéni szkriptek futtatására szolgáló bővítmény használata a Linux rendszerű virtuális gépek](extensions-customscript.md).

