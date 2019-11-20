---
title: Felügyelt identitás egyszerű szolgáltatásának megtekintése a PowerShell használatával – Azure AD
description: Részletes útmutató a felügyelt identitások szolgáltatásának a PowerShell használatával történő megtekintéséhez.
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
ms.openlocfilehash: 9ac2d1d4fc5e669f63bff49a7948db74ec1baa62
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184125"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Felügyelt identitás egyszerű szolgáltatásának megtekintése a PowerShell használatával

Az Azure-erőforrások felügyelt identitásai az Azure-szolgáltatásokat a Azure Active Directory automatikusan felügyelt identitással biztosítják. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban. 

Ebből a cikkből megtudhatja, hogyan tekintheti meg egy felügyelt identitás egyszerű szolgáltatását a PowerShell használatával.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md).
- Ha még nem rendelkezik Azure-fiókkal, [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A [rendszerhez rendelt identitás engedélyezése egy virtuális gépen](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) vagy [alkalmazáson](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- A [Azure PowerShell](/powershell/azure/install-az-ps) legújabb verziójának telepítése

## <a name="view-the-service-principal"></a>Az egyszerű szolgáltatás megtekintése

Az alábbi parancs bemutatja, hogyan tekintheti meg egy virtuális gép vagy alkalmazás egyszerű szolgáltatását, ha a rendszerhez hozzárendelt identitás engedélyezve van. Cserélje le a `<VM or application name>`t a saját értékeire.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Következő lépések

Az Azure AD-szolgáltatások PowerShell használatával történő megtekintésével kapcsolatos további információkért lásd: [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).


