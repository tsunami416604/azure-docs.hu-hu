---
title: Felügyelt identitás egyszerű szolgáltatásának megtekintése - Azure CLI - Azure AD
description: A felügyelt identitás egyszerű szolgáltatásának az Azure CLI használatával való megtekintésének lépésenkénti útmutatója.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298699"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Felügyelt identitás egyszerű szolgáltatásának megtekintése az Azure CLI használatával

Az Azure-erőforrások felügyelt identitásai automatikusan felügyelt identitást biztosítaz Azure-szolgáltatásokszámára az Azure Active Directoryban. Ezzel az identitással hitelesítheti magát minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést anélkül, hogy hitelesítő adatokat a kódot. 

Ebben a cikkben megtudhatja, hogyan tekintheti meg a felügyelt identitás szolgáltatásnév az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md)
- Ha még nem rendelkezik Azure-fiókkal, [regisztráljon egy ingyenes fiókot.](https://azure.microsoft.com/free/)
- A [rendszerhez rendelt identitás engedélyezése virtuális gépen](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) vagy [alkalmazásban.](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity)
- A CLI parancsfájlpéldák futtatásához három lehetőség közül választhat:
    - Használja az [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon (lásd a következő szakaszban).
    - Használja a beágyazott Azure Cloud Shell segítségével a "Try It" gomb, található a jobb felső sarokban minden kódblokk.
    - [Telepítse az Azure CLI legújabb verzióját,](https://docs.microsoft.com/cli/azure/install-azure-cli) ha helyi CLI-konzolt szeretne használni, és jelentkezzen be az Azure-ba a`az login`
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Az egyszerű szolgáltatás megtekintése

Ez a következő parancs bemutatja, hogyan tekintheti meg egy virtuális gép vagy alkalmazás szolgáltatásnév i. Cserélje `<VM or application name>` ki a saját értékeit. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>További lépések

Az Azure AD szolgáltatástagok Azure CLI használatával történő kezeléséről az [az ad sp.](/cli/azure/ad/sp)


