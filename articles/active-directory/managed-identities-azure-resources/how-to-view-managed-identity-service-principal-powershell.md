---
title: A szolgáltatás egyszerű PowerShell-lel egy felügyelt identitás megtekintése
description: Részletes utasítások a szolgáltatásnév a PowerShell használatával egy felügyelt identitás megtekintéséhez.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: priyamo
ms.openlocfilehash: 8c406dda1d6ce0fe7b73f400444d7bcd8a9e8400
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55185958"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>A szolgáltatás egyszerű PowerShell-lel egy felügyelt identitás megtekintése

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebből a cikkből elsajátíthatja a szolgáltatás egyszerű PowerShell-lel egy felügyelt identitás megtekintése.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md).
- Ha még nem rendelkezik Azure-fiók [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/).
- Engedélyezése [virtuális gépen a rendszer által hozzárendelt identitással](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) vagy [alkalmazás](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Telepítse a legújabb verzióját, [Azure PowerShell-lel](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Az egyszerű szolgáltatás megtekintése

A következő parancs bemutatja, hogyan megtekintéséhez egy virtuális Géphez vagy alkalmazáshoz, az egyszerű szolgáltatás engedélyezve van a rendszer által hozzárendelt identitással. Cserélje le `<VM or application name>` a saját értékeire.

```PowerShell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>További lépések

A PowerShell-lel az Azure AD szolgáltatásnevek megtekintése további információkért lásd: [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).


