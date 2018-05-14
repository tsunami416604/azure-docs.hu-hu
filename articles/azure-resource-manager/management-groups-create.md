---
title: Hozzon létre felügyeleti csoportok Azure-erőforrások rendszerezéséhez |} Microsoft Docs
description: Megtudhatja, hogyan hozza létre az Azure felügyeleti csoportokat több erőforrást kezelnek.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/1/2018
ms.author: rithorn
ms.openlocfilehash: 1592e47509f2537bef9cbcefd3cf49618561edcc
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Az erőforrás-szervezet és felügyeleti felügyeleti csoportok létrehozása
Felügyeleti csoportok elősegítő tárolók hozzáférési házirend és megfelelőség kezeléséhez több előfizetéssel. Ezekben a tárolókban felépítését egy eredményesebbé és hatékonyabbá teszi a használható létrehozása [Azure házirend](../azure-policy/azure-policy-introduction.md) és [Azure szerepkör alapú hozzáférés-vezérlést](../role-based-access-control/overview.md). A felügyeleti csoportok további információkért lásd: [rendezheti az erőforrásokat az Azure felügyeleti csoportok ](management-groups-overview.md). 

A felügyeleti csoport funkciót egy nyilvános előzetes verziójában érhető el. Indíthatja a felügyeleti csoportok, jelentkezzen be a [Azure-portálon](https://portal.azure.com) vagy használhat [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview), [Azure CLI](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available), vagy a [REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview) számára felügyeleti csoportok létrehozása.   

Az első felügyeleti csoport létrehozása a könyvtárban akár 15 percet is beletelhet. Nincsenek a felügyeleti csoportok service, Azure-ban a címtáron beállításához először futtatott folyamatokat. Ha a folyamat befejeződik, hogy értesítést kap.  

## <a name="how-to-create-a-management-group"></a>A felügyeleti csoport létrehozása
A felügyeleti csoport is létrehozhat a portál, PowerShell vagy az Azure parancssori felület használatával.

### <a name="create-in-portal"></a>A portál létrehozása

1. Jelentkezzen be a [Azure-portálon](http://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**.
3. Fő lapján, válassza ki a **új felügyeleti csoportban.** 

    ![Fő csoport](media/management-groups/main.png) 
4.  Töltse ki a felügyeleti csoport azonosítója mezőt. 
    - A **felügyeleti csoport azonosítója** directory egyedi azonosítója, amely a felügyeleti csoportra parancsok továbbítására szolgál. Ez az azonosító nincs szerkeszthető létrehozása után, az Azure rendszerben ez a csoport azonosításához használt. 
    - A megjelenítési név mezője az Azure portálon megjelenő nevét. Egy külön megjelenített név mező kitöltése nem kötelező történő létrehozásakor a felügyeleti csoportban, és bármikor módosíthatók.  

    ![Létrehozás](media/management-groups/create_context_menu.png)  
5.  Válassza ki **mentése**


### <a name="create-in-powershell"></a>A PowerShell létrehozása
Belül PowerShell az Add-AzureRmManagementGroups parancsmagok használata.   

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso 
```
A **GroupName** egyedi azonosító létrehozása folyamatban. Az azonosító olyan más parancsok által hivatkozhasson rá az ehhez a csoporthoz, és azt később már nem módosítható.

Ha a felügyeleti csoport egy másik nevet az Azure portálon megjelenítendő szeretett volna, kell felvenni a **DisplayName** paraméter karakterlánccal. Például ha egy felügyeleti csoport létrehozása a Contoso GroupName és megjelenített neve a "Contoso csoport", használhatja a következő parancsmagot: 

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso -DisplayName "Contoso Group" -ParentId ContosoTenant
``` 
Használja a **ParentId** paramétert a felügyeleti csoportot létrehozni egy másik felügyeleti csoportban.  

### <a name="create-in-azure-cli"></a>Az Azure parancssori felület létrehozása
Az Azure parancssori felület, akkor használhat a az fiók felügyeleti-csoport létrehozása parancs. 

```azure-cli
C:\ az account management-group create --group-name <YourGroupName>
``` 

---

## <a name="next-steps"></a>További lépések 
Felügyeleti csoportok kapcsolatos további információkért lásd: 
- [Az erőforrások rendszerezéséhez az Azure felügyeleti csoportok ](management-groups-overview.md)
- [Módosítása, törlése és a felügyeleti csoportok kezelése](management-groups-manage.md)
- [Az Azure Powershell modul telepítése](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Tekintse át a REST API-specifikáció](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Az Azure CLI-bővítményének telepítése](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
