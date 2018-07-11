---
title: Ansible telepítése és konfigurálása Azure virtual machines szolgáltatásban való használatra |} A Microsoft Docs
description: Ismerje meg, hogyan Ansible telepítése és konfigurálása az Ubuntu, a CentOS és a SLES az Azure-erőforrások kezeléséhez
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/04/2018
ms.author: cynthn
ms.openlocfilehash: e7d57ead2caff87db07380582b9085b831844f1e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930069"
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Az Azure-beli virtuális gépek kezelése az Ansible telepítése és konfigurálása

Az Ansible segítségével automatizálhatja a telepítését és konfigurálását az erőforrásoknak a környezetben. Az Ansible segítségével kezelheti a virtuális gépek (VM) az Azure-ban, ugyanaz, mint bármely más erőforrást. Ez a cikk részletesen Ansible és a szükséges Azure Python SDK-modulok telepítése a leggyakrabban használt Linux-disztribúciók magyarázatát. Az Ansible telepíthető többi a disztribúciókat, a telepített csomagokat, hogy illeszkedjen az adott platform módosításával. Hozhat létre Azure-erőforrások biztonságos módon, azt is megtudhatja, hogyan hozhat létre, és adja meg a hitelesítő adatok használata az Ansible.

További telepítési lehetőségei és további platformok lépései: a [Ansible telepítése útmutató](https://docs.ansible.com/ansible/intro_installation.html).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata a parancssori felület, ez a cikk megköveteli, hogy futnak-e az Azure CLI 2.0.30-as verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="install-ansible"></a>Az Ansible telepítése

A legegyszerűbb használata az Ansible az Azure-ral egyike az Azure Cloud Shell egy böngészőalapú rendszerhéj-felület és Azure-erőforrások fejlesztését. Az Ansible a Cloud Shellben előzetesen már telepítve, ezért kihagyhatja az utasításokat az Ansible telepítése, és nyissa meg a [létrehozása Azure hitelesítő adatok](#create-azure-credentials). További eszközök is elérhető a Cloud shellben listáját lásd: [funkciók és eszközök számára az Azure Cloud Shellben lévő Bash](../../cloud-shell/features.md#tools).

Az alábbi utasításokat követve bemutatják, hogyan hozzon létre egy Linux rendszerű virtuális Gépet a különböző disztribúciók és Ansible telepítését. Ha nem szeretne Linux virtuális gép létrehozása az Azure-erőforráscsoport létrehozásához az első lépéshez ugorjon. Ha szeretne egy virtuális gép létrehozása, először hozzon létre egy erőforráscsoportot az [az csoport létrehozása](/cli/azure/group#az_group_create). A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Most válasszon egyet a következő disztribúciókhoz hozhat létre egy virtuális Gépet, szükség esetén útmutatást, majd ezután telepítse az Ansible:

- [7.4 centOS](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>7.4 centOS

Szükség esetén hozzon létre egy virtuális Gépet a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). A következő példában létrehozunk egy nevű virtuális Gépet *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image OpenLogic:CentOS:7.4:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Ssh-KAPCSOLATOT a virtuális gépet a `publicIpAddress` a virtuális gép kimenetében feljegyzett-létrehozási művelet:

```bash
ssh azureuser@<publicIpAddress>
```

A virtuális Gépen a szükséges csomagokat az Azure Python SDK-modulokat és Ansible a következőképpen telepítheti:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Most helyezze át a [létrehozása Azure hitelesítő adatok](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Szükség esetén hozzon létre egy virtuális Gépet a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). A következő példában létrehozunk egy nevű virtuális Gépet *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Ssh-KAPCSOLATOT a virtuális gépet a `publicIpAddress` a virtuális gép kimenetében feljegyzett-létrehozási művelet:

```bash
ssh azureuser@<publicIpAddress>
```

A virtuális Gépen a szükséges csomagokat az Azure Python SDK-modulokat és Ansible a következőképpen telepítheti:

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Most helyezze át a [létrehozása Azure hitelesítő adatok](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Szükség esetén hozzon létre egy virtuális Gépet a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). A következő példában létrehozunk egy nevű virtuális Gépet *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image SUSE:SLES:12-SP2:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Ssh-KAPCSOLATOT a virtuális gépet a `publicIpAddress` a virtuális gép kimenetében feljegyzett-létrehozási művelet:

```bash
ssh azureuser@<publicIpAddress>
```

A virtuális Gépen a szükséges csomagokat az Azure Python SDK-modulokat és Ansible a következőképpen telepítheti:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Most helyezze át a [létrehozása Azure hitelesítő adatok](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Az Azure hitelesítő adatok létrehozása

Az Ansible kommunikál az Azure-ban a felhasználónév és jelszó vagy egy egyszerű szolgáltatást. Azure-beli szolgáltatásnév egy biztonsági identitás, amelyekkel az alkalmazások, szolgáltatások és automatizálási eszközökkel, mint például az Ansible. Szabályozhatja és az egyszerű szolgáltatás az Azure-ban hajthat végre műveleteket helyrendszerszerepkörökre engedélyeinek megadásához. A biztonság növelése érdekében csak a felhasználónév és jelszó megadása során ez a példa létrehoz egy alapszintű egyszerű szolgáltatást.

Az állomáson, vagy az Azure Cloud shellben hozzon létre egy szolgáltatás egyszerű [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac). Az Ansible szükséges hitelesítő adatokat a képernyőre kimeneti:

```azurecli-interactive
az ad sp create-for-rbac --query '{"client_id": appId, "secret": password, "tenant": tenant}'
```

Egy példa a kimenetre az az előző parancsokban a következőképpen történik:

```json
{
  "client_id": "eec5624a-90f8-4386-8a87-02730b5410d5",
  "secret": "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Az Azure-beli hitelesítésre, szükség beszerzése az Azure-előfizetés azonosítója segítségével [az fiók show](/cli/azure/account#az-account-show):

```azurecli-interactive
az account show --query "{ subscription_id: id }"
```

Ez a két parancs kimenete a következő lépésben fogja használni.

## <a name="create-ansible-credentials-file"></a>Az Ansible hitelesítő adatait tartalmazó fájl létrehozása

Adja meg a hitelesítő adatokat, az Ansible, a környezeti változókat határozhat meg, vagy hozzon létre egy helyi hitelesítő adatokkal fájlt. Az Ansible hitelesítő adatok definiálása kapcsolatos további információkért lásd: [hitelesítő adatok megadása az Azure-modulok](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

A fejlesztési környezet létrehozása egy *hitelesítő adatok* Ansible fájlt a gazdagépen a virtuális gép. Hozza létre a virtuális gépre, amelyre telepítette az Ansible az előző lépésben egy hitelesítőadat-fájlja:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

A *hitelesítő adatok* fájl magát az előfizetés-azonosító ötvözi a kimenet az egyszerű szolgáltatás létrehozása. Az előző kimeneti [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) parancs megegyezik az igény szerint *client_id*, *titkos*, és *bérlői*. Az alábbi példa *hitelesítő adatok* fájl bemutatja ezeket az értékeket az előző kimeneti megfelelő. Adja meg a saját értékeit a következők szerint:

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```

Mentse és zárja be a fájlt.

## <a name="use-ansible-environment-variables"></a>Az Ansible környezeti változók használata

Ha például Ansible Tower vagy Jenkins eszközei használatával, meg kell környezeti változókat határozhat meg. Ez a lépés is kimarad, ha csak kívánja használni az Ansible-ügyfél és az Azure hitelesítő adatait tartalmazó fájlt az előző lépésben létrehozott. A környezeti változók ugyanazokat az információkat az Azure-beli hitelesítő fájllal határozza meg:

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>További lépések

Most már az Ansible, és a szükséges Azure Python SDK-modulok telepítése, és Ansible meghatározott hitelesítő adatokat használja. Ismerje meg, hogyan [virtuális gép létrehozása az ansible segítségével](ansible-create-vm.md). Azt is megtudhatja, hogyan [teljes Azure virtuális gép létrehozása és a támogató erőforrások az ansible segítségével](ansible-create-complete-vm.md).
