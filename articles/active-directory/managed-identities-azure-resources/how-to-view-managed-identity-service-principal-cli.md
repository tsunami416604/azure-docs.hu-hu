---
title: Felügyelt identitás egyszerű szolgáltatásának megtekintése – Azure CLI – Azure AD
description: Részletes útmutató a felügyelt identitások Azure CLI használatával történő megtekintéséhez.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fedef003c6d4143d3ad1814081d55b61d5ee020
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298699"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Felügyelt identitás egyszerű szolgáltatásának megtekintése az Azure CLI használatával

Az Azure-erőforrások felügyelt identitásai az Azure-szolgáltatásokat a Azure Active Directory automatikusan felügyelt identitással biztosítják. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést anélkül, hogy hitelesítő adatokat kellene megadnia a kódban. 

Ebből a cikkből megtudhatja, hogyan tekintheti meg egy felügyelt identitás egyszerű szolgáltatását az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md).
- Ha még nem rendelkezik Azure-fiókkal, [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A [rendszerhez rendelt identitás engedélyezése egy virtuális gépen](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) vagy [alkalmazáson](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).
- Három lehetősége van a CLI-példaszkriptek futtatásához:
    - Használja a Azure Portal [Azure Cloud shellt](../../cloud-shell/overview.md) (lásd a következő szakaszt).
    - Használja a beágyazott Azure Cloud Shell-t a "Kipróbálom" gomb, mindegyik blokk jobb felső sarkában található.
    - [Telepítse az Azure CLI legújabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli) , ha inkább egy helyi CLI-konzolt szeretne használni, és bejelentkezik az Azure-ba a `az login` használatával
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Az egyszerű szolgáltatás megtekintése

Ez a következő parancs azt mutatja be, hogyan lehet megtekinteni a felügyelt identitással rendelkező virtuális gépek vagy alkalmazások egyszerű szolgáltatását. Cserélje le a `<VM or application name>`t a saját értékeire. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>További lépések

Az Azure AD egyszerű szolgáltatásainak Azure CLI használatával történő kezelésével kapcsolatos további információkért lásd [az az ad SP](/cli/azure/ad/sp)-ben című témakört.


