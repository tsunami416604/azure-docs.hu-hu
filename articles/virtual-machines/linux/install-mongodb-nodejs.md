---
title: A MongoDB telepítése a Linux virtuális gépet az Azure CLI 1.0 |} Microsoft Docs
description: Megtudhatja, hogyan telepítse és konfigurálja a MongoDB Linux virtuális gépre az Azure-ban a Resource Manager üzembe helyezési modellben.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: a334a0b7b3b638229c61eef086b1919b4c303338
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm-using-the-azure-cli-10"></a>Hogyan kell telepíteni, és konfigurálja a MongoDB a Linux virtuális gépet az Azure CLI 1.0
[MongoDB](http://www.mongodb.org) egy népszerű nyílt forráskódú, nagy teljesítményű NoSQL-adatbázis. Ez a cikk bemutatja, hogyan telepítse és konfigurálja a MongoDB a Linux virtuális gép az Azure erőforrás-kezelő telepítési modellel. Példák, amelyek részletesen hogyan számára:

* [Kézi telepítését és konfigurálását egy alapszintű MongoDB-példány](#manually-install-and-configure-mongodb-on-a-vm)
* [Egy alapszintű MongoDB-példány használata a Resource Manager-sablon létrehozása](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Hozzon létre egy összetett MongoDB replikával szilánkos fürt beállítja a Resource Manager-sablon használatával](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- Azure CLI 1.0 – parancssori felületünk a klasszikus és a Resource Management üzemi modellekhez (a jelen cikkben)
- [Azure CLI 2.0](create-cli-complete-nodejs.md) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Kézi telepítését és konfigurálását a MongoDB a virtuális gép
MongoDB [telepítési utasításokkal](https://docs.mongodb.com/manual/administration/install-on-linux/) a Linux disztribúciókkal, beleértve a Red Hat / CentOS, SUSE, Ubuntu és Debian. Az alábbi példa létrehoz egy *CentOS* mentve SSH-kulcsot használó virtuális gépek *~/.ssh/id_rsa.pub*. A feltett kérdéseket megválaszolni a tárfiók neve, a DNS-neve vagy a rendszergazdai hitelesítő adatokat:

```azurecli
azure vm quick-create \
    --image-urn CentOS \
    --ssh-publickey-file ~/.ssh/id_rsa.pub 
```

Jelentkezzen be a virtuális gép használata a nyilvános IP-cím, az előző virtuális gép létrehozását lépést végén jelenik meg:

```bash
ssh azureuser@40.78.23.145
```

Adja hozzá a telepítési források mongodb, hozzon létre egy **yum** tárház fájlt az alábbiak szerint:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.4.repo
```

Nyissa meg a MongoDB-tárház fájlt szerkesztésre. Adja hozzá a következő sorokat:

```sh
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```

Telepítse a MongoDB használatával **yum** az alábbiak szerint:

```bash
sudo yum install -y mongodb-org
```

Alapértelmezés szerint SELinux megvalósul CentOS képek, amely megakadályozza a MongoDB-hozzáférése. Csoportházirend-kezelő eszközök telepítése és konfigurálása SELinux MongoDB működéséhez az alapértelmezett TCP-porton 27017 az alábbiak szerint engedélyezi. 

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
Létrehozhat egy alapszintű MongoDB-példány egy CentOS virtuális a Githubról a következő Azure gyors üzembe helyezési sablonja segítségével. Ez a sablon használatával az egyéni parancsprogramok futtatására szolgáló bővítmény Linux adhat hozzá egy `yum` tárház az újonnan létrehozott CentOS virtuális gép és a MongoDB telepítse.

* [Alapszintű MongoDB-példány a CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Az alábbi példa létrehoz egy erőforráscsoport nevű `myResourceGroup` a a `eastus` régióban. Adja meg a saját értékek a következők szerint:

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [!NOTE]
> Az Azure parancssori felület visszatér a kérdés a központi telepítés létrehozása néhány másodpercen belül, de a telepítés és konfigurálás néhány percet is igénybe vehet. A központi telepítés állapotának ellenőrzése `azure group deployment show myResourceGroup`, írja be ennek megfelelően az erőforráscsoport nevét. Várjon, amíg a **ProvisioningState** látható *sikeres* előtt SSH-kapcsolatot a virtuális Gépet.

Az üzembe helyezés befejeződik, a virtuális gép SSH. A virtuális gép használata az IP-cím beszerzése a `azure vm show` parancsot az alábbi példában látható módon:

```azurecli
azure vm show --resource-group myResourceGroup --name myLinuxVM
```

A kimeneti végéhez a nyilvános IP-cím akkor jelenik meg. SSH-kapcsolatot a virtuális Gépet a virtuális gép IP-címmel:

```bash
ssh azureuser@138.91.149.74
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

* [MongoDB horizontális fürt a CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Az összetett szilánkos MongoDB-fürt telepítése szükséges több mint 20 Vcpu, vagyis általában az alapértelmezett vCPU számának régiónként az előfizetéshez. Nyissa meg az Azure támogatási kérelmet a vCPU számának növeléséhez.

Az alábbi példa létrehoz egy erőforráscsoport nevű *myResourceGroup* a a *eastus* régióban. Adja meg a saját értékek a következők szerint:

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [!NOTE]
> Az Azure parancssori felület visszatér a kérdés a központi telepítés létrehozása néhány másodpercen belül, de a telepítés és konfigurálás akár óráig is eltarthat egy befejezéséhez. A központi telepítés állapotának ellenőrzése `azure group deployment show myResourceGroup`, ennek megfelelően beállítja az erőforráscsoport nevét. Várjon, amíg a **ProvisioningState** látható *sikeres* lévő virtuális géphez való csatlakozás előtt.


## <a name="next-steps"></a>További lépések
Ezekben a példákban csatlakozik a MongoDB-példány helyileg a virtuális gépről. Ha szeretné a MongoDB-példány egy másik virtuális gép vagy a hálózati csatlakozás, ellenőrizze a megfelelő [jönnek létre a hálózati biztonsági csoportszabályok](nsg-quickstart.md).

Sablonok használata létrehozásával kapcsolatos további információkért lásd: a [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md).

Az Azure Resource Manager-sablonok segítségével az egyéni parancsprogramok futtatására szolgáló bővítmény letöltésére és végrehajtására parancsfájlok a virtuális gépeken. További információkért lásd: [használata az Azure egyéni parancsprogramok futtatására szolgáló bővítmény Linux virtuális gépek](extensions-customscript.md).

