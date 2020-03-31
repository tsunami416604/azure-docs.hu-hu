---
title: Rövid útmutató – Ansible-forgatókönyvek futtatása a Bash használatával az Azure Cloud Shellben
description: Ebben a rövid útmutatóban megtudhatja, hogyan végezhet el különböző Ansible feladatokat a Bash segítségével az Azure Cloud Shellben
keywords: ansible, azure, devops, bash, cloudshell, forgatókönyv, bash
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: 2a938179cf2e07a61749042db32ef9e1c9d843ba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78247892"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Rövid útmutató: Ansible forgatókönyvek futtatása a Bash használatával az Azure Cloud Shellben

Az Azure Cloud Shell interaktív, böngészőben elérhető rendszerhéj az Azure-erőforrások felügyeletéhez. A Cloud Shell lehetővé teszi a Bash vagy a PowerShell parancssor ának használatát. Ebben a cikkben az Azure Cloud Shellen belüli Bash használatával futtathat egy Ansible forgatókönyvet.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Az Azure Cloud Shell konfigurálása** – Ha most jön az Azure Cloud Shell, olvassa el [a Bash az Azure Cloud Shell ben című témakört.](https://docs.microsoft.com/azure/cloud-shell/quickstart)
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Hitelesítő adatok automatikus konfigurálása

Amikor bejelentkezett a Cloud Shellbe, az Ansible az Azure-ral hitelesíti magát az infrastruktúra további konfiguráció nélküli kezeléséhez. 

Ha több előfizetéssel dolgozik, adja meg az Ansible előfizetési felhasználást a `AZURE_SUBSCRIPTION_ID` környezeti változó exportálásával. 

Az összes Azure-előfizetés listájának listázásához futtassa a következő parancsot:

```azurecli-interactive
az account list
```

Az Azure-előfizetés-azonosító használatával `AZURE_SUBSCRIPTION_ID` állítsa be az alábbiakat:

```console
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>A konfiguráció ellenőrzése
A sikeres konfiguráció ellenőrzéséhez használja az Ansible segítségével hozzon létre egy Azure-erőforráscsoportot.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Rövid útmutató: Konfigurálja a virtuális gépet az Azure-ban az Ansible használatával](./ansible-create-vm.md)