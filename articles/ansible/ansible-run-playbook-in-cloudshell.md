---
title: Az Azure Cloud Shellben lévő Bash Ansible futtatása
description: Az Azure Cloud Shell Bash-környezet különböző Ansible feladatok elvégzése
ms.service: ansible
keywords: az ansible, azure, devops, bash, cloud Shell, forgatókönyv, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 08/07/2018
ms.topic: article
ms.openlocfilehash: 9928f646905dd0da4b15166ec55e5d8a183cb210
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "42055493"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Az Azure Cloud Shellben lévő Bash Ansible futtatása

Ebben az oktatóanyagban elsajátíthatja, hogyan használhatja a Cloud Shellben lévő Bash Ansible munkaterületét egy Azure-előfizetés konfigurálása. 

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Konfigurálja az Azure Cloud Shell** – Ha még csak ismerkedik az Azure Cloud Shellt, a cikk [gyors útmutató: az Azure Cloud Shell Bash](https://docs.microsoft.com/azure/cloud-shell/quickstart), azt ábrázolja, hogyan elindítása és konfigurálása a Cloud Shellben. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatikus hitelesítő adatok konfigurációját

Miután bejelentkezett a Cloud Shellben, az Ansible hitelesíti a további konfiguráció nélkül infrastruktúra kezelése Azure-ral. Ha több előfizetéssel rendelkezik, hogy melyik előfizetéssel, az Ansible exportálása által működnie kiválaszthatja a `AZURE_SUBSCRIPTION_ID` környezeti változót. Az Azure-előfizetések kilistázásához, futtassa a következő parancsot:

```azurecli-interactive
az account list
```

Használatával a **azonosító** az előfizetés kívánja használni, állítsa be a **AZURE_SUBSCRIPTION_ID** módon:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Ellenőrizze
A sikeres konfigurációjának ellenőrzéséhez hozzon létre egy erőforráscsoportot az Ansible használatával.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Alapszintű virtuális gép létrehozása az Azure-ban, az ansible segítségével](/azure/virtual-machines/linux/ansible-create-vm)