---
title: Telepítse és konfigurálja a Ansible Azure virtuális gépeken való használatra |} Microsoft Docs
description: Megtudhatja, hogyan telepítse és konfigurálja a Ansible Ubuntu, CentOS és SLES Azure-erőforrások kezeléséhez
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: e6fad548eda35d1832cb4ecc2fd9bdabf825f361
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Telepítse és konfigurálja az Azure virtuális gépek kezeléséhez Ansible

Ansible lehetővé teszi, hogy automatizálja a központi telepítési és konfigurációs az erőforrásoknak a környezetben. Ansible segítségével kezelheti a virtuális gépek (VM), ugyanaz, mint bármely egyéb erőforrásokat az Azure-ban. Ez a cikk részletesen Ansible és a szükséges Azure Python SDK-modulok telepítése a leggyakrabban használt Linux disztribúciókkal részénél. Más disztribúciókkal Ansible telepíthető a telepített csomagok, hogy elférjen az adott platform beállításával. Szeretne létrehozni Azure-erőforrások biztonságos elérését, is megismerheti, hogyan hozhat létre és Ansible használandó hitelesítő adatok megadása.

További telepítési opciókon és a további platformok lépéseket, tekintse meg a [Ansible telepítése útmutató](https://docs.ansible.com/ansible/intro_installation.html).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ez a cikk számára szükséges, hogy futnak-e az Azure parancssori felület 2.0.30 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="install-ansible"></a>Ansible telepítése

Az Azure felhőalapú rendszerhéj, egy webböngésző-alapú rendszerhéj élmény és Azure-erőforrások fejlesztését egyik legegyszerűbb módja Ansible használata Azure jelenti. Ansible előre telepített, a felhő rendszerhéj, így a utasításokat kihagyhatja Ansible telepítése, és navigáljon a [létrehozása Azure hitelesítő adatok](#create-azure-credentials). További eszközök is elérhető a felhő rendszerhéj listájáért lásd: [funkciók és eszközök számára az Azure-felhő rendszerhéj Bash](../../cloud-shell/features.md#tools).

Az alábbi utasítások alapján hozzon létre egy Linux virtuális Gépet a különböző disztribúciókkal és telepítse a Ansible megjelenítése. Ha nincs szüksége a Linux virtuális gép létrehozásához, hagyja ki az első lépés egy Azure erőforráscsoport létrehozásához. A virtuális gépek létrehozásához szükséges, ha először hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#az_group_create). A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Most válasszon egyet a következő disztribúciókkal a regisztráláshoz szükség esetén hozzon létre egy virtuális Gépet, és telepítse a Ansible:

- [7.4 centOS](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>7.4 centOS

Szükség esetén hozzon létre virtuális gép és [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). Az alábbi példakód létrehozza a virtuális gépek nevű *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image OpenLogic:CentOS:7.4:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH-kapcsolatot a virtuális gép használata a `publicIpAddress` feljegyzett kimenetét a virtuális Gépet a létrehozási művelet futtatásakor:

```bash
ssh azureuser@<publicIpAddress>
```

A virtuális gépen, a szükséges csomagok telepítése az Azure Python SDK modulok és Ansible az alábbiak szerint:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Most helyezze át a [létrehozása Azure hitelesítő adatok](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Szükség esetén hozzon létre virtuális gép és [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). Az alábbi példakód létrehozza a virtuális gépek nevű *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH-kapcsolatot a virtuális gép használata a `publicIpAddress` feljegyzett kimenetét a virtuális Gépet a létrehozási művelet futtatásakor:

```bash
ssh azureuser@<publicIpAddress>
```

A virtuális gépen, a szükséges csomagok telepítése az Azure Python SDK modulok és Ansible az alábbiak szerint:

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
pip install ansible[azure]
```

Most helyezze át a [létrehozása Azure hitelesítő adatok](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Szükség esetén hozzon létre virtuális gép és [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). Az alábbi példakód létrehozza a virtuális gépek nevű *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image SUSE:SLES:12-SP2:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH-kapcsolatot a virtuális gép használata a `publicIpAddress` feljegyzett kimenetét a virtuális Gépet a létrehozási művelet futtatásakor:

```bash
ssh azureuser@<publicIpAddress>
```

A virtuális gépen, a szükséges csomagok telepítése az Azure Python SDK modulok és Ansible az alábbiak szerint:

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

Ansible kommunikál az Azure-ban, a felhasználónév és jelszó vagy egy egyszerű szolgáltatást. Egy Azure szolgáltatás egyszerű egy biztonsági azonosító, amely alkalmazások, szolgáltatások és automatizálási eszközökkel, például a Ansible használható. Szabályozza, és adja meg az engedélyeket, hogy milyen műveletek a szolgáltatás egyszerű hajthat végre az Azure-ban. A biztonság növelése érdekében csak a felhasználónév és jelszó megadása keresztül alábbi példa létrehoz egy alapszintű service egyszerű.

Az állomáson, vagy az Azure-felhő rendszerhéj létre szolgáltatás egyszerű [az ad sp létrehozása-az-rbac](/cli/azure/ad/sp#create-for-rbac). Ansible szükséges hitelesítő adatok a képernyőre kimeneti:

```azurecli-interactive
az ad sp create-for-rbac --query '{"client_id": appId, "secret": password, "tenant": tenant}'
```

A kimenet a fenti parancsok például a következőképpen történik:

```json
{
  "client_id": "eec5624a-90f8-4386-8a87-02730b5410d5",
  "secret": "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

A hitelesítéshez az Azure-ba is be kell szereznie az Azure-előfizetés azonosítója használatával [az fiók megjelenítése](/cli/azure/account#az_account_show):

```azurecli-interactive
az account show --query "{ subscription_id: id }"
```

Ez a két parancs kimenete a következő lépésben használhatja.

## <a name="create-ansible-credentials-file"></a>Ansible hitelesítőadat-fájlt létrehozni

Adja meg a hitelesítő adatokat a Ansible, adja meg a környezeti változókat, vagy hozzon létre egy helyi hitelesítőadat-fájlt. További információ a Ansible hitelesítő adatok megadását: [hitelesítő adatokat biztosító Azure-modulok](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

A fejlesztési környezet létrehozása egy *hitelesítő adatok* fájl Ansible a virtuális gép a gazdagépen. Hozzon létre egy hitelesítőadat-fájlt a virtuális gépen, ahol az előző lépésben telepített Ansible:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

A *hitelesítő adatok* maga az előfizetés-azonosító egyesíti az egyszerű szolgáltatás létrehozása kimenetét. Az előző kimeneti [az ad sp létrehozása-az-rbac](/cli/azure/ad/sp#create-for-rbac) parancs megegyezik az igény szerint *client_id*, *titkos*, és *bérlői*. Az alábbi példa *hitelesítő adatok* fájl ezeket az értékeket az előző kimeneti megfelelő jeleníti meg. Adja meg a saját értékek a következők szerint:

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```

Mentse és zárja be a fájlt.

## <a name="use-ansible-environment-variables"></a>Ansible környezeti változók használata

Ha eszközöket, például a Ansible torony vagy Jenkins használni kívánja, akkor adja meg a környezeti változókat. Ezt a lépést, ha csak szeretné használni a Ansible ügyfél és az Azure hitelesítési adatait tartalmazó fájlt az előző lépésben létrehozott figyelmen kívül hagyja. Környezeti változók ugyanazokat az információkat, az Azure hitelesítési adatait tartalmazó fájlt határozza meg:

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>További lépések

Most már rendelkezik Ansible és a szükséges Azure Python SDK modulok telepítve és Ansible megadott hitelesítő adatok használatához. Megtudhatja, hogyan [hozzon létre egy virtuális gép Ansible](ansible-create-vm.md). Azt is megtudhatja hogyan [teljes Azure virtuális gép létrehozása és az azt támogató erőforrások Ansible](ansible-create-complete-vm.md).
