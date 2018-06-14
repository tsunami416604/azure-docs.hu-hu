---
title: A MongoDB a Linux virtuális gép és az Azure parancssori felület telepítése |} Microsoft Docs
description: Megtudhatja, hogyan telepítheti és konfigurálhatja a Linux virtuális gép iusing az Azure CLI 2.0 MongoDB
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: b21b33a265d499136dbe3e72538923d8295e9876
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852234"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Hogyan kell telepíteni, és konfigurálja a MongoDB a Linux virtuális gép
[MongoDB](http://www.mongodb.org) egy népszerű nyílt forráskódú, nagy teljesítményű NoSQL-adatbázis. Ez a cikk bemutatja, hogyan telepítse és konfigurálja a MongoDB a Linux virtuális gép az Azure CLI 2.0. Az [Azure CLI 1.0-s](install-mongodb-nodejs.md) verziójával is elvégezheti ezeket a lépéseket. Példák, amelyek részletesen hogyan számára:

* [Kézi telepítését és konfigurálását egy alapszintű MongoDB-példány](#manually-install-and-configure-mongodb-on-a-vm)
* [Egy alapszintű MongoDB-példány használata a Resource Manager-sablon létrehozása](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Hozzon létre egy összetett MongoDB replikával szilánkos fürt beállítja a Resource Manager-sablon használatával](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Kézi telepítését és konfigurálását a MongoDB a virtuális gép
MongoDB [telepítési utasításokkal](https://docs.mongodb.com/manual/administration/install-on-linux/) a Linux disztribúciókkal, beleértve a Red Hat / CentOS, SUSE, Ubuntu és Debian. Az alábbi példa létrehoz egy *CentOS* virtuális gép. Ebben a környezetben, meg kell a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login).

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. Az alábbi példakód létrehozza a virtuális gépek nevű *myVM* nevű felhasználóval történő *azureuser* SSH nyilvános kulcsos hitelesítés használatával

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH-kapcsolatot a virtuális gép saját felhasználónév és a `publicIpAddress` kimenete az előző lépést a felsorolt:

```bash
ssh azureuser@<publicIpAddress>
```

Adja hozzá a telepítési források mongodb, hozzon létre egy **yum** tárház fájlt az alábbiak szerint:

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

Telepítse a MongoDB használatával **yum** az alábbiak szerint:

```bash
sudo yum install -y mongodb-org
```

Alapértelmezés szerint SELinux megvalósul CentOS képek, amely megakadályozza a MongoDB-hozzáférése. Csoportházirend-kezelő eszközök telepítése és konfigurálása SELinux engedélyezi a MongoDB működéséhez az alapértelmezett TCP-porton 27017 az alábbiak szerint:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Indítsa el a MongoDB szolgáltatást a következőképpen:

```bash
sudo service mongod start
```

Ellenőrizze a MongoDB telepítése a helyi használatával `mongo` ügyfél:

```bash
mongo
```

Bizonyos adatok hozzáadása, és majd a Keresés most tesztelje a MongoDB-példány:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Igény szerint konfigurálja automatikus indításra során a rendszer újraindítását MongoDB:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Alapszintű MongoDB-példány létrehozása sablon használatával CentOS
Létrehozhat egy alapszintű MongoDB-példány egy CentOS virtuális a Githubról a következő Azure gyors üzembe helyezési sablonja segítségével. Ez a sablon használatával az egyéni parancsprogramok futtatására szolgáló bővítmény Linux adhat hozzá egy **yum** tárház az újonnan létrehozott CentOS virtuális gép és a MongoDB telepítse.

* [Alapszintű MongoDB-példány a CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) -https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Ebben a környezetben, meg kell a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login). Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Ezután telepítse a MongoDB-sablont a [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#az_group_deployment_create). Amikor a rendszer kéri, adja meg a saját egyedi értékeket az *newStorageAccountName*, *dnsNameForPublicIP*, és a rendszergazda felhasználónevét és jelszavát:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Jelentkezzen be a virtuális gép használata a nyilvános DNS-címét, a virtuális gép. Megtekintheti a nyilvános DNS-cím [az vm megjelenítése](/cli/azure/vm#az_vm_show):

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH-kapcsolatot a virtuális Gépet, a saját felhasználónévvel és a nyilvános DNS-címét:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Ellenőrizze a MongoDB telepítése a helyi használatával `mongo` ügyfél az alábbiak szerint:

```bash
mongo
```

Most tesztfiókok az egyes adatok hozzáadása, majd keresse az alábbiak szerint:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>A sablon használatával CentOS összetett MongoDB szilánkos fürt létrehozása
Egy összetett MongoDB szilánkos fürtöt a következő Azure gyors üzembe helyezés sablont a Githubból is létrehozhat. Ez a sablon a következő a [MongoDB szilánkos fürt gyakorlati tanácsok](https://docs.mongodb.com/manual/core/sharded-cluster-components/) redundancia és a magas rendelkezésre állás biztosításához. A sablon két szilánkok, az egyes replika három csomópontot hoz létre. Egy konfigurációs kiszolgálót replikakészlethez három csomópontot is létrejön, és két **mongos** útválasztó kiszolgálók között a szilánkok származó alkalmazások konzisztencia biztosításához.

* [MongoDB horizontális fürt a CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) -https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Az összetett szilánkos MongoDB-fürt telepítése szükséges több mint 20 mag, vagyis általában az alapértelmezett magok száma az előfizetéshez régiónként. Nyissa meg a alapszámának növeléséhez az Azure támogatási kérelmet.

Ebben a környezetben, meg kell a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login). Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Ezután telepítse a MongoDB-sablont a [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#az_group_deployment_create). Adja meg a saját erőforrás nevét, és ahol szükséges, az ilyen méretű *mongoAdminUsername*, *sizeOfDataDiskInGB*, és *configNodeVmSize*:

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

A központi telepítés is igénybe vehet, akár egy óráig telepítheti és konfigurálhatja a Virtuálisgép-példányok. A `--no-wait` jelző használata az előző parancs végén a parancssorba való visszatéréshez vezérlő, amennyiben az a sablon-üzembehelyezés elfogadta az Azure platformon. Megtekintheti a központi telepítési állapot [az csoport telepítési megjelenítése](/cli/azure/group/deployment#az_group_deployment_show). A következő példa a állapota megtekinti a *myMongoDBCluster* központi telepítést, a *myResourceGroup* erőforráscsoport:

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>További lépések
Ezekben a példákban csatlakozik a MongoDB-példány helyileg a virtuális gépről. Ha szeretné a MongoDB-példány egy másik virtuális gép vagy a hálózati csatlakozás, ellenőrizze a megfelelő [jönnek létre a hálózati biztonsági csoportszabályok](nsg-quickstart.md).

Ezek a példák központi telepítése a core MongoDB környezet fejlesztési célokra szolgál. Alkalmazza a szükséges biztonsági konfigurációs beállításokat a környezetéhez. További információkért lásd: a [MongoDB biztonsági docs](https://docs.mongodb.com/manual/security/).

Sablonok használata létrehozásával kapcsolatos további információkért lásd: a [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md).

Az Azure Resource Manager-sablonok segítségével az egyéni parancsprogramok futtatására szolgáló bővítmény letöltésére és végrehajtására parancsfájlok a virtuális gépeken. További információkért lásd: [használata az Azure egyéni parancsprogramok futtatására szolgáló bővítmény Linux virtuális gépek](extensions-customscript.md).

