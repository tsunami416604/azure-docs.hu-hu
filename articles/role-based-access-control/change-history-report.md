---
title: Hozzáférés reporting - Azure RBAC |} Microsoft Docs
description: A szerepköralapú hozzáférés-vezérlés az elmúlt 90 napban Azure-előfizetését a hozzáférést minden módosításának felsoroló jelentés készítése.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: deef89e62dcec3141be46549cc0fb34b33a6335a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="create-an-access-report-for-role-based-access-control"></a>A szerepköralapú hozzáférés-vezérléshez access jelentés létrehozása
Valaki engedélyezi, vagy visszavonja a hozzáférési jogosultságok előfizetése, amikor a módosításokat az Azure események naplózásra. Az elmúlt 90 napra vonatkozó összes módosítások megtekintéséhez hozzáférés módosítása előzmények jelentéseket is létrehozhat.

## <a name="create-a-report-with-azure-powershell"></a>Az Azure PowerShell-jelentés létrehozása
A PowerShell access módosítási előzményeit jelentés létrehozásához használja a [Get-AzureRMAuthorizationChangeLog](/powershell/module/azurerm.resources/get-azurermauthorizationchangelog) parancsot.

Ez a parancs hívásakor mely tulajdonsága a-hozzárendelésekhez felsorolt, beleértve a következőket adhatja meg:

| Tulajdonság | Leírás |
| --- | --- |
| **Művelet** |E hozzáférést kap vagy visszavonása |
| **hívó** |A tulajdonos felelős a hozzáférés módosítása |
| **PrincipalId** | A felhasználó, csoport vagy a szerepkörhöz rendelt alkalmazás egyedi azonosítója |
| **PrincipalName** |A felhasználó, csoport vagy alkalmazás neve |
| **PrincipalType** |A hozzárendelés egy felhasználó, csoport vagy alkalmazás volt-e |
| **RoleDefinitionId** |A megadott vagy visszavont szerepkör GUID-azonosítója |
| **RoleName** |A megadott vagy visszavont szerepkör |
| **Hatókör** | Az előfizetés, erőforráscsoportból vagy erőforrások, amelyek a hozzárendelés vonatkozik egyedi azonosítója | 
| **ScopeName** |Az előfizetés, erőforráscsoportból vagy erőforrás neve |
| **ScopeType** |A hozzárendelés az előfizetés, erőforráscsoport vagy erőforrás hatókör volt-e |
| **időbélyeg** |A dátum és idő hozzáférés megváltozott |

Példa felsorolja az összes változásokat az előfizetéshez tartozó az elmúlt hét napban:

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog – képernyőkép](./media/change-history-report/access-change-history.png)

## <a name="create-a-report-with-azure-cli"></a>Az Azure CLI-jelentés létrehozása
Az Azure parancssori felület (CLI) access módosítási előzményeit jelentés létrehozásához használja a `azure role assignment changelog list` parancsot.

## <a name="export-to-a-spreadsheet"></a>Egy táblázatot exportálása
Mentse a jelentést, vagy az adatok kezelését, a változásokat egy .csv-fájlba exportálja. A jelentés egy számolótáblába, tekintse át a majd megtekintése.

![Változásnaplója tekinthetők meg a táblázat – képernyőkép](./media/change-history-report/change-history-spreadsheet.png)

## <a name="next-steps"></a>További lépések
* Együttműködve [egyéni szerepkörök az Azure RBAC](custom-roles.md)
* Megtudhatja, hogyan kezelheti [Azure RBAC a PowerShell használatával](role-assignments-powershell.md)

