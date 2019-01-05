---
title: Az Azure Cloud Shellben lévő Bash Ansible futtatása
description: Az Azure Cloud Shell Bash-környezet különböző Ansible feladatok elvégzése
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, forgatókönyv, bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 08/07/2018
ms.topic: article
ms.openlocfilehash: 6bfac47e4afa41b4c75a8d33b4eea1ff5103296d
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050897"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Az Azure Cloud Shellben lévő Bash Ansible futtatása

Ebben az oktatóanyagban elsajátíthatja, hogyan használhatja a Cloud Shellben lévő Bash Ansible munkaterületét egy Azure-előfizetés konfigurálása. 

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Az Azure Cloud Shell konfigurálása** – Ha még csak most ismerkedik az Azure Cloud Shell használatával, [a Bash az Azure Cloud Shellben való használatát ismertető rövid útmutatóból](https://docs.microsoft.com/azure/cloud-shell/quickstart) megtudhatja, hogy indíthatja el és konfigurálhatja a Cloud Shellt. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Hitelesítő adatok automatikus konfigurálása

Miután bejelentkezett a Cloud Shellben, az Ansible hitelesíti a további konfiguráció nélkül infrastruktúra kezelése Azure-ral. Ha több előfizetéssel rendelkezik, hogy melyik előfizetéssel, az Ansible exportálása által működnie kiválaszthatja a `AZURE_SUBSCRIPTION_ID` környezeti változót. Az Azure-előfizetések kilistázásához, futtassa a következő parancsot:

```azurecli-interactive
az account list
```

Használatával a **azonosító** az előfizetés kívánja használni, állítsa be a **AZURE_SUBSCRIPTION_ID** módon:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>A konfiguráció ellenőrzése
A sikeres konfigurációjának ellenőrzéséhez hozzon létre egy erőforráscsoportot az Ansible használatával.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Alapszintű virtuális gép létrehozása az Azure-ban, az ansible segítségével](/azure/virtual-machines/linux/ansible-create-vm)