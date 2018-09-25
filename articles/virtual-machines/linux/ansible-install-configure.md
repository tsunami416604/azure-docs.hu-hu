---
title: Az Ansible telepítése Azure-beli virtuális gépeken
description: Megtudhatja, hogyan telepítheti és konfigurálhatja az Ansible-t az Azure-erőforrások felügyeletéhez az Ubuntu, a CentOS és az SLES rendszereken
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, forgatókönyv, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: b714470cd12bb7a0cd2d2a00b4f09467726f505d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987050"
---
# <a name="install-ansible-on-azure-virtual-machines"></a>Az Ansible telepítése Azure-beli virtuális gépeken

Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. Az Ansible segítségével ugyanúgy felügyelheti a virtuális gépeket (VM-eket) az Azure-ban, ahogy azt bármely más erőforrással tenné. A cikk részletesen bemutatja az Ansible és a szükséges Azure Python SDK-modulok telepítésének módját a leggyakrabban használt Linux-disztribúciókon. Az Ansible más disztribúciókra is telepíthető, ha a telepített csomagokat az adott platformhoz igazítja. Az Azure-erőforrások biztonságos létrehozása érdekében megtudhatja azt is, hogyan hozhat létre és definiálhat az Ansible által használt hitelesítő adatokat. A Cloud Shellben elérhető további eszközök listájáért tekintse meg [az Azure Cloud Shellben elérhető Bash-funkciókat és -eszközöket bemutató cikket](../../cloud-shell/features.md#tools).

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Hozzáférés a Linuxhoz vagy egy Linux rendszerű virtuális géphez** – Ha nem rendelkezik Linux rendszerű géppel, hozzon létre egy [Linux rendszerű virtuális gépet](https://docs.microsoft.com/azure/virtual-network/quick-create-cli).

- **Azure-beli szolgáltatásnév**: Kövesse az [Azure-beli szolgáltatásnév létrehozása az Azure CLI 2.0-val](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal) című cikk **a szolgáltatásnév létrehozását** ismertető szakaszában foglaltakat. Jegyezze fel az **appId**, a **displayName**, a **password** és a **tenant** értékét.

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Az Ansible telepítése Azure-beli Linux rendszerű virtuális gépeken

Jelentkezzen be a Linux rendszerű gépre, és válassza ki az alábbi disztribúciók egyikét az Ansible telepítésének lépéseiért:

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Az Azure Python SDK modulok és az Ansible szükséges csomagjainak telepítéséhez írja be a következő parancsokat egy terminálablakba vagy Bash-ablakba:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Kövesse [az Azure-beli hitelesítő adatok létrehozását](#create-azure-credentials) ismertető szakasz utasításait.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Az Azure Python SDK modulok és az Ansible szükséges csomagjainak telepítéséhez írja be a következő parancsokat egy terminálablakba vagy Bash-ablakba:


```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Kövesse [az Azure-beli hitelesítő adatok létrehozását](#create-azure-credentials) ismertető szakasz utasításait.

### <a name="sles-12-sp2"></a>SLES 12 SP2

Az Azure Python SDK modulok és az Ansible szükséges csomagjainak telepítéséhez írja be a következő parancsokat egy terminálablakba vagy Bash-ablakba:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Kövesse [az Azure-beli hitelesítő adatok létrehozását](#create-azure-credentials) ismertető szakasz utasításait.

## <a name="create-azure-credentials"></a>Azure-beli hitelesítő adatok létrehozása

Az előfizetés-azonosító és a szolgáltatásnév létrehozása alkalmával kapott adatok kombinációjával az Ansible hitelesítő adatai kétféleképpen konfigurálhatók:

- [Az Ansible hitelesítő adatait tartalmazó fájl létrehozása](#file-credentials)
- [Ansible környezeti változók használata](#env-credentials)

Az Ansible Tower, a Jenkins vagy más hasonló eszközök használata esetén azt a lehetőséget kell alkalmaznia, hogy a szolgáltatásnevek értékét környezeti változókként deklarálja.

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Az Ansible hitelesítő adatait tartalmazó fájl létrehozása

Ez a témakör azt ismerteti, hogy hogyan lehet egy helyi, hitelesítő adatokat tartalmazó fájlt létrehozni az Ansible hitelesítő adatainak biztosítására. További információ az Ansible hitelesítő adatainak definiálásáról: [Azure modulok hitelesítő adatainak biztosítása](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

Fejlesztési környezetben a virtuális gazdagépen futó Ansible *hitelesítő adatait* tartalmazó fájlt a következő módon hozhat létre:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

Szúrja be a következő sorokat a *hitelesítő adatokat* tartalmazó fájlba, a helyőrzők helyére a szolgáltatásnév létrehozása során használt adatokat behelyettesítve.

```bash
[default]
subscription_id=<your-subscription_id>
client_id=<security-principal-appid>
secret=<security-principal-password>
tenant=<security-principal-tenant>
```

Mentse és zárja be a fájlt.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Ansible környezeti változók használata

Ez a szakasz azt ismerteti, hogy hogyan konfigurálhatja az Ansible hitelesítő adatait környezeti változóként exportálva azokat.

Adja meg a következő parancsokat egy terminál- vagy Bask-ablakban:

```bash
export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
export AZURE_CLIENT_ID=<security-principal-appid>
export AZURE_SECRET=<security-principal-password>
export AZURE_TENANT=<security-principal-tenant>
```

## <a name="verify-the-configuration"></a>A konfiguráció ellenőrzése
A sikeres konfiguráció ellenőrzése végett most már létrehozhat egy erőforráscsoportot az Ansible használatával.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Linux rendszerű virtuális gép létrehozása az Azure-ban az Ansible használatával](./ansible-create-vm.md)
