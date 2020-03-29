---
title: Telepítse a MongoDB-t Linux os virtuális gépre az Azure CLI-vel
description: A MongoDB telepítése és konfigurálása Linuxos virtuális gépen az Azure CLI használatával
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
ms.openlocfilehash: e1bc7c8a6f97d6dc6bb1d6cb54825425244b2158
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944889"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>A MongoDB telepítése és konfigurálása Linux os virtuális gépen

[MongoDB](https://www.mongodb.org) egy népszerű nyílt forráskódú, nagy teljesítményű NoSQL adatbázis. Ez a cikk bemutatja, hogyan telepítheti és konfigurálhatja a MongoDB-t linuxos virtuális gépeken az Azure CLI-vel. Példák jelennek meg, amelyek részletesen ismertetik, hogyan:

* [AlapMongoDB-példány manuális telepítése és konfigurálása](#manually-install-and-configure-mongodb-on-a-vm)
* [Egyszerű MongoDB-példány létrehozása Erőforrás-kezelő sablon nal](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Összetett MongoDB szilánkos fürt létrehozása replikakészletekkel erőforrás-kezelő sablon használatával](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>A MongoDB manuális telepítése és konfigurálása virtuális gépen
A MongoDB [telepítési utasításokat ad](https://docs.mongodb.com/manual/administration/install-on-linux/) a Linux disztribúciókhoz, beleértve a Red Hat / CentOS, SUSE, Ubuntu és Debian disztribúciókat. A következő példa létrehoz egy *CentOS* virtuális gép. A környezet létrehozásához a legújabb [Azure CLI-t](/cli/azure/install-az-cli2) kell telepítenie, és be kell jelentkeznie egy Azure-fiókba [az a bejelentkezéshasználatával.](/cli/azure/reference-index)

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. A következő példa létrehoz egy *myVM* nevű virtuális gép egy *azureuser* nevű felhasználóval az SSH nyilvános kulcsú hitelesítéshasználatával

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH a virtuális gép a saját `publicIpAddress` felhasználónevét, és a felsorolt kimenetaz előző lépésben:

```bash
ssh azureuser@<publicIpAddress>
```

A MongoDB telepítési forrásainak hozzáadásához hozzon létre egy **yum** repository fájlt az alábbiak szerint:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Nyissa meg a MongoDB tárfájlját `vi` szerkesztésre, például a vagy `nano`a programmal. Adja hozzá a következő sorokat:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Telepítse a MongoDB-t **yum** használatával az alábbiak szerint:

```bash
sudo yum install -y mongodb-org
```

Alapértelmezés szerint az SELinux a CentOS-lemezképeken van érvényben, ami megakadályozza a MongoDB elérését. Telepítse a házirend-kezelő eszközöket, és konfigurálja az SELinux-ot úgy, hogy a MongoDB az alapértelmezett 27017-es TCP-porton működjön az alábbiak szerint:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Indítsa el a MongoDB szolgáltatást az alábbiak szerint:

```bash
sudo service mongod start
```

Ellenőrizze a MongoDB telepítését a `mongo` helyi ügyfél használatával történő csatlakozással:

```bash
mongo
```

Most tesztelje a MongoDB példányt néhány adat hozzáadásával, majd keressen:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Ha szükséges, állítsa be a MongoDB-t úgy, hogy automatikusan elinduljon a rendszer újraindítása közben:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Alapvető MongoDB-példány létrehozása a CentOS rendszeren sablon használatával
Létrehozhat egy alapszintű MongoDB-példányt egyetlen CentOS virtuális számítógépen a következő Azure gyorsindítási sablon használatával a GitHubról. Ez a sablon a Linux egyéni parancsfájl-bővítményt használja egy **yum** tárház hozzáadásához az újonnan létrehozott CentOS virtuális géphez, majd telepítse a MongoDB-t.

* [Egyszerű MongoDB-példány a CentOS rendszeren](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

A környezet létrehozásához a legújabb [Azure CLI-t](/cli/azure/install-az-cli2) kell telepítenie, és be kell jelentkeznie egy Azure-fiókba [az a bejelentkezéshasználatával.](/cli/azure/reference-index) Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Ezután telepítse a MongoDB sablont az [az csoport központi telepítésével.](/cli/azure/group/deployment) Amikor a rendszer kéri, adja meg saját egyedi értékeit az *újStorageAccountName*, *dnsNameForPublicIP*, valamint a rendszergazdai felhasználónév és jelszó:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Jelentkezzen be a virtuális gépre a virtuális gép nyilvános DNS-címével. Megtekintheti a nyilvános DNS-címet az vm show:You can view the public DNS address with [az vm show:](/cli/azure/vm)

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH a virtuális gép hez a saját felhasználónevével és nyilvános DNS-címével:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Ellenőrizze a MongoDB telepítését a `mongo` helyi ügyfél használatával történő csatlakozással az alábbiak szerint:

```bash
mongo
```

Most tesztelje a példányt néhány adat hozzáadásával és a következőképpen való kereséssel:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Összetett MongoDB sharded fürt létrehozása centos rendszeren sablon használatával
Létrehozhat egy összetett MongoDB szilánkos fürt a következő Azure-gyorsindítási sablon használatával a GitHubról. Ez a sablon a [MongoDB horizontálisan kidolgozott fürt ajánlott eljárásokat](https://docs.mongodb.com/manual/core/sharded-cluster-components/) követ a redundancia és a magas rendelkezésre állás biztosítása érdekében. A sablon két szegmenst hoz létre, minden replikakészletben három csomót. Egy három csomópontos konfigurációs kiszolgáló replikakészlet is létrejön, valamint két **mongo** útválasztó-kiszolgáló, amely konzisztenciát biztosít a szegmensekből származó alkalmazásokhoz.

* [MongoDB horizontális fürt CentOS rendszeren](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Az összetett MongoDB horizontálisan támogatott fürt telepítése több mint 20 mag, amely általában az alapértelmezett core count régiónként egy előfizetés. Nyisson meg egy Azure-támogatási kérelmet az alaplétszám növeléséhez.

A környezet létrehozásához a legújabb [Azure CLI-t](/cli/azure/install-az-cli2) kell telepítenie, és be kell jelentkeznie egy Azure-fiókba [az a bejelentkezéshasználatával.](/cli/azure/reference-index) Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Ezután telepítse a MongoDB sablont az [az csoport központi telepítésével.](/cli/azure/group/deployment) Szükség esetén adja meg saját erőforrásnevét és méretét, például *a mongoAdminUsername*, *sizeOfDataDiskInGB*és *configNodeVmSize esetében:*

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

Ez a központi telepítés több mint egy órát is igénybe vehet az összes virtuálisgép-példány okainak üzembe helyezése és konfigurálása. A `--no-wait` jelző az előző parancs végén használatos a parancssorba való visszaadáshoz, miután a sablon üzembe helyezését az Azure platform elfogadta. Ezután megtekintheti a központi telepítési állapotot az [az csoport központi telepítési show-jával.](/cli/azure/group/deployment) A következő példa a *myMongoDBCluster* központi telepítésének állapotát tekinti meg a *myResourceGroup erőforráscsoportban:*

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>További lépések
Ezekben a példákban a virtuális gépről helyileg csatlakozik a MongoDB-példányhoz. Ha egy másik virtuális gépről vagy hálózatról szeretne csatlakozni a MongoDB-példányhoz, győződjön meg arról, hogy létrejönnek a megfelelő [hálózati biztonsági csoportszabályok.](nsg-quickstart.md)

Ezek a példák üzembe helyezik az alapvető MongoDB környezetet fejlesztési célokra. Alkalmazza a környezetéhez szükséges biztonsági konfigurációs beállításokat. További információt a [MongoDB biztonsági dokumentumaiban](https://docs.mongodb.com/manual/security/)talál.

A sablonok használatával történő létrehozásról az [Azure Resource Manager áttekintése című témakörben olvashat bővebben.](../../azure-resource-manager/management/overview.md)

Az Azure Resource Manager-sablonok az egyéni parancsfájl-bővítmény használatával letölti és végrehajtja a parancsfájlokat a virtuális gépeken. További információ: [Az Azure Egyéni parancsfájl-bővítmény használata Linux virtuális gépekkel című témakörben.](extensions-customscript.md)

