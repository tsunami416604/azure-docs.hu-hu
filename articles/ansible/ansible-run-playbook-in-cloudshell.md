---
title: Gyors útmutató – Ansible forgatókönyvek futtatása bash használatával Azure Cloud Shell
description: Ebből a rövid útmutatóból megtudhatja, hogyan hajthat végre különböző Ansible-feladatokat a bash használatával Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, forgatókönyv, bash
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: 2a938179cf2e07a61749042db32ef9e1c9d843ba
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78247892"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Gyors útmutató: Ansible forgatókönyvek futtatása bash használatával Azure Cloud Shell

A Azure Cloud Shell egy interaktív, böngészővel elérhető rendszerhéj az Azure-erőforrások kezeléséhez. A Cloud Shell lehetővé teszi a bash vagy a PowerShell parancssor használatát. Ebben a cikkben a bash-t a Azure Cloud Shellon belül egy Ansible forgatókönyv futtatására használhatja.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Azure Cloud Shell konfigurálása** – ha a Azure Cloud Shell új, tekintse meg a következőt: [a bash](https://docs.microsoft.com/azure/cloud-shell/quickstart)rövid útmutatója Azure Cloud shell.
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Hitelesítő adatok automatikus konfigurálása

Ha bejelentkezett a Cloud Shellba, a Ansible a hitelesítést az Azure-ban az infrastruktúra kezeléséhez, további konfiguráció nélkül. 

Több előfizetés használata esetén a `AZURE_SUBSCRIPTION_ID` környezeti változó exportálásával adhatja meg az előfizetés Ansible. 

Az összes Azure-előfizetésének listázásához futtassa a következő parancsot:

```azurecli-interactive
az account list
```

Az Azure-előfizetési AZONOSÍTÓjának használatával állítsa be a `AZURE_SUBSCRIPTION_ID` a következőképpen:

```console
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>A konfiguráció ellenőrzése
A sikeres konfiguráció ellenőrzéséhez a Ansible használatával hozzon létre egy Azure-erőforráscsoportot.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Gyors útmutató: virtuális gép konfigurálása az Azure-ban az Ansible használatával](./ansible-create-vm.md)