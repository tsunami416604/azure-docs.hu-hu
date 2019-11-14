---
title: Gyors útmutató – Ansible telepítése Linux rendszerű virtuális gépeken az Azure-ban
description: Ebből a rövid útmutatóból megtudhatja, hogyan telepítheti és konfigurálhatja az Azure-erőforrások Ansible az Ubuntu, a CentOS és a SLES használatával
keywords: ansible, azure, devops, bash, cloudshell, forgatókönyv, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: a1ea5814ffd201456a2751b37b77f3062cac789a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037077"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Gyors útmutató: Ansible telepítése Linux rendszerű virtuális gépeken az Azure-ban

Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. Ez a cikk bemutatja, hogyan konfigurálhatja a Ansible a leggyakoribb Linux-disztribúciók esetében. A Ansible más disztribúciókban való telepítéséhez állítsa be a telepített csomagokat az adott platformra. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Hozzáférés a Linuxhoz vagy egy Linux rendszerű virtuális géphez** – Ha nem rendelkezik Linux rendszerű géppel, hozzon létre egy [Linux rendszerű virtuális gépet](/azure/virtual-network/quick-create-cli).

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Az Ansible telepítése Azure-beli Linux rendszerű virtuális gépeken

Jelentkezzen be a Linux rendszerű gépre, és válassza ki az alábbi disztribúciók egyikét az Ansible telepítésének lépéseiért:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Ebben a szakaszban a CentOS-t konfigurálja a Ansible használatára.

1. Nyisson meg egy terminálablakot.

1. Adja meg a következő parancsot az Azure Python SDK-modulok szükséges csomagjainak telepítéséhez:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Adja meg a következő parancsot a szükséges csomagok Ansible telepítéséhez:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Hozza létre az Azure-beli hitelesítő adatokat](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Ebben a szakaszban az Ubuntut konfigurálja a Ansible használatára.

1. Nyisson meg egy terminálablakot.

1. Adja meg a következő parancsot az Azure Python SDK-modulok szükséges csomagjainak telepítéséhez:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Adja meg a következő parancsot a szükséges csomagok Ansible telepítéséhez:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Hozza létre az Azure-beli hitelesítő adatokat](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Ebben a szakaszban a SLES-t konfigurálja a Ansible használatára.

1. Nyisson meg egy terminálablakot.

1. Adja meg a következő parancsot az Azure Python SDK-modulok szükséges csomagjainak telepítéséhez:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Adja meg a következő parancsot a szükséges csomagok Ansible telepítéséhez:

    ```bash
    sudo pip install ansible[azure]
    ```

1. A következő parancs megadásával távolítsa el az ütköző Python titkosítási csomagot:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Hozza létre az Azure-beli hitelesítő adatokat](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Azure-beli hitelesítő adatok létrehozása

A Ansible hitelesítő adatainak konfigurálásához a következő információkra lesz szüksége:

* Az Azure-előfizetés azonosítója 
* Az egyszerű szolgáltatás értékei

Ha Ansible-tornyot vagy Jenkins-t használ, állapítsa meg az egyszerű szolgáltatásnév értékét környezeti változókként.

Konfigurálja a Ansible hitelesítő adatait az alábbi módszerek egyikének használatával:

- [Az Ansible hitelesítő adatait tartalmazó fájl létrehozása](#file-credentials)
- [Ansible környezeti változók használata](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Az Ansible hitelesítő adatait tartalmazó fájl létrehozása

Ebben a szakaszban egy helyi hitelesítő adatokat tartalmazó fájlt hoz létre a Ansible hitelesítő adatainak megadásához. 

A Ansible hitelesítő adatainak definiálásával kapcsolatos további információkért lásd: [hitelesítő adatok megadása az Azure-modulokhoz](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

1. Fejlesztési környezet esetén hozzon létre egy `credentials` nevű fájlt a gazdagép virtuális gépén:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Szúrja be a következő sorokat a fájlba. Cserélje le a helyőrzőket az egyszerű szolgáltatásnév értékeire.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Mentse és zárja be a fájlt.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Ansible környezeti változók használata

Ebben a szakaszban az egyszerű szolgáltatásnév értékét exportálja a Ansible hitelesítő adatainak konfigurálásához.

1. Nyisson meg egy terminálablakot.

1. Az egyszerű szolgáltatás értékeinek exportálása:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>A konfiguráció ellenőrzése

A sikeres konfiguráció ellenőrzéséhez a Ansible használatával hozzon létre egy Azure-erőforráscsoportot.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Gyors útmutató: linuxos virtuális gép konfigurálása az Azure-ban az Ansible használatával](./ansible-create-vm.md)