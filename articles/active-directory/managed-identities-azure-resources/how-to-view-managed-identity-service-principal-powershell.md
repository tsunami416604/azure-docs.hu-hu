---
title: Felügyelt identitás egyszerű szolgáltatásának megtekintése a PowerShell használatával – Azure AD
description: A felügyelt identitás okainak powershell használatával való megtekintésének lépésenkénti útmutatója.
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
ms.openlocfilehash: 33fc6fbfd7c47b5809e8aff8ee9806c9eeac1162
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298665"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Felügyelt identitás egyszerű szolgáltatásának megtekintése a PowerShell használatával

Az Azure-erőforrások felügyelt identitásai automatikusan felügyelt identitást biztosítaz Azure-szolgáltatásokszámára az Azure Active Directoryban. Ezzel az identitással hitelesítheti magát minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést, anélkül, hogy hitelesítő adatokat a kódot. 

Ebben a cikkben megtudhatja, hogyan tekintheti meg a powershell használatával felügyelt identitás egyszerű szolgáltatás.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md)
- Ha még nem rendelkezik Azure-fiókkal, [regisztráljon egy ingyenes fiókot.](https://azure.microsoft.com/free/)
- A [rendszerhez rendelt identitás engedélyezése virtuális gépen](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) vagy [alkalmazásban.](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity)
- Az [Azure PowerShell](/powershell/azure/install-az-ps) legújabb verziójának telepítése

## <a name="view-the-service-principal"></a>Az egyszerű szolgáltatás megtekintése

Ez a következő parancs bemutatja, hogyan tekintheti meg egy virtuális gép vagy alkalmazás egyszerű szolgáltatásának megtekintését, ha a rendszer hez rendelt identitás engedélyezve van. Cserélje `<VM or application name>` ki a saját értékeit.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>További lépések

Az Azure AD szolgáltatástagok PowerShell használatával való megtekintéséről a [Get-AzADServicePrincipal című témakörben talál](/powershell/module/az.resources/get-azadserviceprincipal)további információt.


