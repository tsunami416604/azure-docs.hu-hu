---
title: Rövid útmutató – Ansible-forgatókönyvek futtatása az Azure Cloud Shell Bash-n keresztül |} A Microsoft Docs
description: Ebből a gyorsútmutatóból megtudhatja, hogyan műveletekkel különböző Ansible-feladatokat az Azure Cloud Shell Bash-környezet
keywords: ansible, azure, devops, bash, cloudshell, forgatókönyv, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: a0d0544601bfc6ce0c7ef642f576ed780c8642ba
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763360"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Gyors útmutató: Keresztül az Azure Cloud Shell Bash Ansible-forgatókönyvek futtatása

Az Azure Cloud Shell egy interaktív, a böngésző által elérhető shell Azure-erőforrások kezeléséhez. A cloud Shell lehetővé teszi, hogy biztosítja, hogy egy Bash- vagy Powershell parancsot használja. Ebben a cikkben használatával Azure Cloud Shellben lévő Bash Ansible-forgatókönyvek futtatása.

## <a name="prerequisites"></a>Előfeltételek

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Konfigurálja az Azure Cloud Shell** – Ha most ismerkedik az Azure Cloud Shellt, lásd: [gyors útmutató: az Azure Cloud Shell Bash](https://docs.microsoft.com/azure/cloud-shell/quickstart).
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Hitelesítő adatok automatikus konfigurálása

Miután bejelentkezett a Cloud Shellben, az Ansible hitelesíti a további konfiguráció nélkül infrastruktúra kezelése Azure-ral. 

Ha több előfizetés használata, adja meg az Ansible exportálásával használja előfizetést a `AZURE_SUBSCRIPTION_ID` környezeti változót. 

Az Azure-előfizetések kilistázásához, futtassa a következő parancsot:

```azurecli-interactive
az account list
```

Az Azure-előfizetés azonosítója segítségével állítsa be a `AZURE_SUBSCRIPTION_ID` módon:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>A konfiguráció ellenőrzése
A sikeres konfigurációjának ellenőrzéséhez az Ansible használatával hozzon létre egy Azure-erőforráscsoportot.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Rövid útmutató: Virtuális gép az Azure-ban az Ansible konfigurálása](/azure/virtual-machines/linux/ansible-create-vm)