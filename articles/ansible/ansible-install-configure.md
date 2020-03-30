---
title: Rövid útmutató – Ansible telepítése Linuxos virtuális gépekre az Azure-ban
description: Ebből a rövid útmutatóból megtudhatja, hogyan telepítheti és konfigurálhatja az Ansible-t az Azure-erőforrások kezelésére ubuntu, CentOS és SLES rendszeren.
keywords: ansible, azure, devops, bash, cloudshell, forgatókönyv, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 44007000475793005560914fd816cd0c16927f9a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77202409"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Rövid útmutató: Ansible telepítése Linuxos virtuális gépekre az Azure-ban

Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. Ez a cikk bemutatja, hogyan kell beállítani az Ansible-t a leggyakoribb Linux disztribúciókhoz. Ha az Ansible-t más distros-ra szeretné telepíteni, állítsa be a telepített csomagokat az adott platformhoz. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Hozzáférés a Linuxhoz vagy egy Linux rendszerű virtuális géphez** – Ha nem rendelkezik Linux rendszerű géppel, hozzon létre egy [Linux rendszerű virtuális gépet](/azure/virtual-network/quick-create-cli).

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Az Ansible telepítése Azure-beli Linux rendszerű virtuális gépeken

Jelentkezzen be a Linux rendszerű gépre, és válassza ki az alábbi disztribúciók egyikét az Ansible telepítésének lépéseiért:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Ebben a szakaszban a CentOS-t az Ansible használatára konfigurálja.

1. Nyisson meg egy terminálablakot.

1. Adja meg a következő parancsot az Azure Python SDK-modulok hoz szükséges csomagok telepítéséhez:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Írja be a következő parancsot, hogy telepítse a szükséges csomagokat Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Hozza létre az Azure-hitelesítő adatokat.](#create-azure-credentials)

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Ebben a szakaszban az Ubuntut az Ansible használatára konfigurálja.

1. Nyisson meg egy terminálablakot.

1. Adja meg a következő parancsot az Azure Python SDK-modulok hoz szükséges csomagok telepítéséhez:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Írja be a következő parancsot, hogy telepítse a szükséges csomagokat Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Hozza létre az Azure-hitelesítő adatokat.](#create-azure-credentials)

### <a name="sles-12-sp2"></a>SLES 12 SP2

Ebben a szakaszban az SLES-t az Ansible használatára konfigurálja.

1. Nyisson meg egy terminálablakot.

1. Adja meg a következő parancsot az Azure Python SDK-modulok hoz szükséges csomagok telepítéséhez:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Írja be a következő parancsot, hogy telepítse a szükséges csomagokat Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. Írja be a következő parancsot az ütköző Python-kriptográfiai csomag eltávolításához:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Hozza létre az Azure-hitelesítő adatokat.](#create-azure-credentials)

## <a name="create-azure-credentials"></a>Azure-beli hitelesítő adatok létrehozása

Az Ansible hitelesítő adatok konfigurálásához a következő adatokra van szükség:

* Az Azure-előfizetés azonosítója 
* A szolgáltatás egyszerű értékei

Ha Ansible Tower vagy Jenkins használatával, deklarálja az egyszerű szolgáltatás értékeket környezeti változókként.

Konfigurálja az Ansible hitelesítő adatokat az alábbi technikák egyikével:

- [Az Ansible hitelesítő adatait tartalmazó fájl létrehozása](#file-credentials)
- [Ansible környezeti változók használata](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Az Ansible hitelesítő adatait tartalmazó fájl létrehozása

Ebben a szakaszban hozzon létre egy helyi hitelesítő adatok at, hogy a hitelesítő adatokat Ansible. 

Az Ansible hitelesítő adatok meghatározásáról további információt az [Azure-modulok hitelesítő adatok megadása című témakörben talál.](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules)

1. Fejlesztői környezet ben hozzon `credentials` létre egy nevet a gazdavirtuális gépen nevű fájlt:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Szúrja be a következő sorokat a fájlba. Cserélje le a helyőrzőket az egyszerű szolgáltatásértékekre.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Mentse és zárja be a fájlt.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Ansible környezeti változók használata

Ebben a szakaszban exportálja a szolgáltatás egyszerű értékeket az Ansible hitelesítő adatok konfigurálásához.

1. Nyisson meg egy terminálablakot.

1. Az egyszerű szolgáltatásértékek exportálása:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>A konfiguráció ellenőrzése

A sikeres konfiguráció ellenőrzéséhez használja az Ansible segítségével hozzon létre egy Azure-erőforráscsoportot.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Rövid útmutató: Linuxos virtuális gép konfigurálása az Azure-ban az Ansible használatával](./ansible-create-vm.md)