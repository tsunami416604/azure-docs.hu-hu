---
title: "Telepítse és konfigurálja a Ansible Azure virtuális gépeken való használatra |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítse és konfigurálja a Ansible Ubuntu, CentOS és SLES Azure-erőforrások kezeléséhez"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: iainfou
ms.openlocfilehash: c5257ef5c635080f5eaca371e1882b13cc37e0fd
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Telepítse és konfigurálja az Azure virtuális gépek kezeléséhez Ansible
Ez a cikk részletesen Ansible és a szükséges Azure Python SDK-modulok telepítése a leggyakrabban használt Linux disztribúciókkal részénél. Más disztribúciókkal Ansible telepíthető a telepített csomagok, hogy elférjen az adott platform beállításával. Szeretne létrehozni Azure-erőforrások biztonságos elérését, is megismerheti, hogyan hozhat létre és Ansible használandó hitelesítő adatok megadása. 

További telepítési opciókon és a további platformok lépéseket, tekintse meg a [Ansible telepítése útmutató](https://docs.ansible.com/ansible/intro_installation.html).


## <a name="install-ansible"></a>Ansible telepítése
Először hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#create). Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroupAnsible* a a *eastus* helye:

```azurecli
az group create --name myResourceGroupAnsible --location eastus
```

Most hozzon létre egy virtuális Gépet, és telepítse a következő disztribúciókkal egyike Ansible:

- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [7.3 centOS](#centos-73)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS
Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#create) paranccsal. Az alábbi példakód létrehozza a virtuális gépek nevű *myVMAnsible*:

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image UbuntuLTS \
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


### <a name="centos-73"></a>7.3 centOS
Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#create) paranccsal. Az alábbi példakód létrehozza a virtuális gépek nevű *myVMAnsible*:

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image CentOS \
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


### <a name="sles-12-sp2"></a>SLES 12 SP2
Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#create) paranccsal. Az alábbi példakód létrehozza a virtuális gépek nevű *myVMAnsible*:

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image SLES \
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
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 python-devel \
    libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Most helyezze át a [létrehozása Azure hitelesítő adatok](#create-azure-credentials).


## <a name="create-azure-credentials"></a>Az Azure hitelesítő adatok létrehozása
Ansible kommunikál az Azure-ban, a felhasználónév és jelszó vagy egy egyszerű szolgáltatást. Egy Azure szolgáltatás egyszerű egy biztonsági azonosító, amely alkalmazások, szolgáltatások és automatizálási eszközökkel, például a Ansible használható. Szabályozza, és adja meg az engedélyeket, hogy milyen műveletek a szolgáltatás egyszerű hajthat végre az Azure-ban. A biztonság növelése érdekében csak a felhasználónév és jelszó megadása keresztül alábbi példa létrehoz egy alapszintű service egyszerű.

Az egyszerű szolgáltatás létrehozása [az ad sp létrehozása-az-rbac](/cli/azure/ad/sp#create-for-rbac) és a hitelesítő adatait, amelyet a Ansible kimeneti:

```azurecli
az ad sp create-for-rbac --query [appId,password,tenant]
```

A kimenet a fenti parancsok például a következőképpen történik:

```json
[
  "eec5624a-90f8-4386-8a87-02730b5410d5",
  "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "72f988bf-86f1-41af-91ab-2d7cd011db47"
]
```

A hitelesítéshez az Azure-ba is be kell szereznie az Azure-előfizetése Azonosítóját [az fiók megjelenítése](/cli/azure/account#show):

```azurecli
az account show --query [id] --output tsv
```

Ez a két parancs kimenete a következő lépésben használhatja.


## <a name="create-ansible-credentials-file"></a>Ansible hitelesítőadat-fájlt létrehozni
Adja meg a hitelesítő adatokat a Ansible, adja meg a környezeti változókat, vagy hozzon létre egy helyi hitelesítőadat-fájlt. További információ a Ansible hitelesítő adatok megadását: [hitelesítő adatokat biztosító Azure-modulok](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules). 

A fejlesztési környezet létrehozása egy *hitelesítő adatok* fájl Ansible a virtuális gép a gazdagépen az alábbiak szerint:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

A *hitelesítő adatok* maga az előfizetés-azonosító egyesíti az egyszerű szolgáltatás létrehozása kimenetét. Az előző kimeneti [az ad sp létrehozása-az-rbac](/cli/azure/ad/sp#create-for-rbac) parancs a ugyanabban a sorrendben, igény szerint *client_id*, *titkos*, és *bérlői*. Az alábbi példa *hitelesítő adatok* fájl ezeket az értékeket az előző kimeneti megfelelő jeleníti meg. Adja meg a saját értékek a következők szerint:

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```


## <a name="use-ansible-environment-variables"></a>Ansible környezeti változók használata
Ha az eszközöket, például a Ansible torony vagy Jenkins használni kívánja, megadhatja környezeti változók az alábbiak szerint. Ezek a változók egyesíthető kimenetét a egyszerű szolgáltatás létrehozása az előfizetés-azonosító. Az előző kimeneti [az ad sp létrehozása-az-rbac](/cli/azure/ad/sp#create-for-rbac) parancs a ugyanabban a sorrendben, igény szerint *AZURE_CLIENT_ID*, *AZURE_SECRET*, és *AZURE_TENANT* . 

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>Következő lépések
Most már rendelkezik Ansible és a szükséges Azure Python SDK modulok telepítve és Ansible megadott hitelesítő adatok használatához. Megtudhatja, hogyan [hozzon létre egy virtuális gép Ansible](ansible-create-vm.md). Azt is megtudhatja hogyan [teljes Azure virtuális gép létrehozása és az azt támogató erőforrások Ansible](ansible-create-complete-vm.md).
