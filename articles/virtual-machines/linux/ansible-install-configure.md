---
title: Rövid útmutató – telepítés Ansible az Azure-beli Linuxos virtuális gépeken |} A Microsoft Docs
description: Ebből a gyorsútmutatóból megtudhatja, hogyan Ansible telepítése és konfigurálása az Ubuntu, a CentOS és a SLES az Azure-erőforrások kezeléséhez
keywords: ansible, azure, devops, bash, cloudshell, forgatókönyv, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: e7a664be48d1e26e09faf4f330fd1267ec003315
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685599"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Gyors útmutató: Az Azure-beli Linuxos virtuális gépeken az Ansible telepítése

Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. Ez a cikk bemutatja a leggyakoribb Linux-disztribúciók némelyike az Ansible konfigurálása. Az Ansible telepítése a többi a disztribúciókat, módosíthatja a telepített csomagokat, az adott platform esetében. 

## <a name="prerequisites"></a>Előfeltételek

- [!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [open-source-devops-prereqs-create-sp.md](../../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Hozzáférés a Linuxhoz vagy egy Linux rendszerű virtuális géphez** – Ha nem rendelkezik Linux rendszerű géppel, hozzon létre egy [Linux rendszerű virtuális gépet](/azure/virtual-network/quick-create-cli).

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Az Ansible telepítése Azure-beli Linux rendszerű virtuális gépeken

Jelentkezzen be a Linux rendszerű gépre, és válassza ki az alábbi disztribúciók egyikét az Ansible telepítésének lépéseiért:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Ebben a szakaszban a CentOS használata az Ansible konfigurálása.

1. Nyisson meg egy terminálablakot.

1. Adja meg az Azure Python SDK-modulok a szükséges csomagok telepítéséhez a következő parancsot:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Adja meg a szükséges csomagokat az Ansible telepítése a következő parancsot:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Hozzon létre az Azure-beli hitelesítő](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Ebben a szakaszban Ubuntu használata az Ansible konfigurálása.

1. Nyisson meg egy terminálablakot.

1. Adja meg az Azure Python SDK-modulok a szükséges csomagok telepítéséhez a következő parancsot:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Adja meg a szükséges csomagokat az Ansible telepítése a következő parancsot:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Hozzon létre az Azure-beli hitelesítő](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Ebben a szakaszban SLES használata az Ansible konfigurálása.

1. Nyisson meg egy terminálablakot.

1. Adja meg az Azure Python SDK-modulok a szükséges csomagok telepítéséhez a következő parancsot:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Adja meg a szükséges csomagokat az Ansible telepítése a következő parancsot:

    ```bash
    sudo pip install ansible[azure]
    ```

1. Adja meg a következő paranccsal távolítható el az ütköző Python cryptography package:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Hozzon létre az Azure-beli hitelesítő](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Azure-beli hitelesítő adatok létrehozása

Az Ansible hitelesítő adatok konfigurálásához szüksége van a következő információkat:

* Az Azure-előfizetés azonosítója 
* A szolgáltatás egyszerű értékek

Az Ansible Tower vagy a Jenkins használata, környezeti változókként a szolgáltatás egyszerű értékek deklarálását ismerteti.

Adja meg az Ansible hitelesítő adatokat a következő módszerek egyikével:

- [Az Ansible hitelesítő adatait tartalmazó fájl létrehozása](#file-credentials)
- [Ansible környezeti változók használata](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Az Ansible hitelesítő adatait tartalmazó fájl létrehozása

Ebben a szakaszban hozzon létre egy helyi hitelesítő adatokkal fájlt az Ansible hitelesítő adatok megadása. 

Az Ansible hitelesítő adatok meghatározása kapcsolatos további információkért lásd: [hitelesítő adatok megadása az Azure-modulok](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

1. Fejlesztői környezet, hozzon létre egy fájlt `credentials` a gazdagép virtuális gépen:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. A következő sorokat beszúrni a fájlba. A helyőrzőket cserélje le a szolgáltatás egyszerű értékeket.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Mentse és zárja be a fájlt.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Ansible környezeti változók használata

Ebben a szakaszban exportálja a szolgáltatás hitelesítő adatait az Ansible konfigurálása egyszerű értékeket.

1. Nyisson meg egy terminálablakot.

1. A szolgáltatás egyszerű értékek exportálása:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>A konfiguráció ellenőrzése

A sikeres konfigurációjának ellenőrzéséhez az Ansible használatával hozzon létre egy Azure-erőforráscsoportot.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Rövid útmutató: Linux rendszerű virtuális gép konfigurálása az Azure-ban az Ansible-lel](./ansible-create-vm.md)