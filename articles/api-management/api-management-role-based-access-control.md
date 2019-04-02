---
title: Szerepköralapú hozzáférés-vezérlés használata az Azure API Management |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja a beépített szerepkörök, és egyéni szerepkörök létrehozása az Azure API Management szolgáltatásban
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 2e53b0d582a69e10de22e85720833800d44058e3
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793866"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Szerepköralapú hozzáférés-vezérlés használata az Azure API Management szolgáltatásban

Az Azure API Management az Azure szerepköralapú hozzáférés vezérlése (RBAC) ahhoz, hogy az API Management-szolgáltatások és entitások (például API-k és házirendek) részletes hozzáférés-vezérlést támaszkodik. Ez a cikk áttekintést, a beépített és egyéni szerepkörök az API Management szolgáltatásban. A hozzáférés-kezelés az Azure Portalon további információkért lásd: [hozzáférés-kezelés az Azure portal – első lépések](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Beépített szerepkörök

Az API Management jelenleg három beépített szerepkört biztosít, és felveszi a közeljövőben két további szerepkört. Ezekkel a szerepkörökkel különféle hatókörökben, beleértve az előfizetés, erőforráscsoport és az API Management-példány is hozzárendelhető. Például ha a "Az Azure API Management szolgáltatás olvasói" szerepkört rendel egy felhasználó, az erőforráscsoport-szinten, majd a felhasználó rendelkezik olvasási hozzáférést minden API Management-példány az erőforráscsoportban. 

A következő táblázat a beépített szerepkörök rövid leírása. Ezeket a szerepköröket rendelhet az Azure Portalon vagy más eszközök, köztük az Azure használatával [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [Azure CLI-vel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli), és [REST API-val](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest). Beépített szerepkörök hozzárendelése kapcsolatos részletekért lásd: [az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése a szerepkör-hozzárendelésekkel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

| Szerepkör          | Olvasási hozzáférés<sup>[1]</sup> | Írási hozzáférés<sup>: [2]</sup> | Service creation, deletion, scaling, VPN, and custom domain configuration | Az örökölt publisher Portalon való hozzáférés | Leírás
| ------------- | ---- | ---- | ---- | ---- | ---- 
| Az Azure API Management szolgáltatás Közreműködője | ✓ | ✓ | ✓ | ✓ | Felügyelői. Entitások (például API-k és házirendek) és az API Management szolgáltatásainak teljes CRUD-hozzáféréssel rendelkezik. Az örökölt közzétevői portálon hozzáféréssel rendelkezik. |
| Az Azure API Management szolgáltatás olvasói | ✓ | | || API Management-szolgáltatások és entitásokat csak olvasási hozzáféréssel rendelkezik. |
| Az Azure API Management szolgáltatás operátori | ✓ | | ✓ | | API Management-szolgáltatások, de nem entitások kezelheti.|
| Az Azure API Management szolgáltatás szerkesztő<sup>*</sup> | ✓ | ✓ | |  | Felügyelheti az API Management entitásokat, de nem szolgáltatások.|
| Az Azure API Management tartalomkezelő<sup>*</sup> | ✓ | | | ✓ | A fejlesztői portálon kezelheti. Csak olvasási hozzáféréssel a szolgáltatásokhoz és entitások.|

<sup>[1] olvasási hozzáférés az API Management-szolgáltatások és entitások (például API-k és házirendek).</sup>

<sup>[2] írási hozzáférés az API Management-szolgáltatások és entitások kivételével a következő műveleteket:-példány létrehozását, törlését és skálázásával; VPN-konfiguráció; és az egyéni tartomány beállítása.</sup>

<sup>\* A Service Editor szerepkör után telepítjük át a rendszergazdai felhasználói felület a meglévő közzétevő portálról az Azure Portalra lesz elérhető. Tartalomkezelő szerepkör után a kiadói portál van újratervezhetők csak tartalmazza a fejlesztői portál felügyeletével kapcsolatos funkciók lesz elérhető.</sup>  

## <a name="custom-roles"></a>Egyéni szerepkörök

Ha a beépített szerepkörök egyik sem felel meg a saját különleges igényei, egyéni szerepkörök biztosít az API Management-entitások a részletes hozzáférés-kezelés hozható létre. Ha például egy egyéni biztonsági szerepkört, amely egy API Management-szolgáltatás csak olvasási hozzáféréssel rendelkezik, de csak egy adott API írási hozzáféréssel is létrehozhat. Egyéni szerepkörökkel kapcsolatos további információkért lásd: [egyéni szerepkörök az Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

> [!NOTE]
> Az, hogy tekintse meg az API Management-példány, az Azure Portalon, tartalmaznia kell egy egyéni biztonsági szerepkört a ```Microsoft.ApiManagement/service/read``` művelet.

Amikor létrehoz egy egyéni biztonsági szerepkört, célszerűbb a kezdéshez válasszon egyet a beépített szerepkörök. Adja hozzá az attribútumok szerkesztéséhez **műveletek**, **NotActions**, vagy **AssignableScopes**, és mentse a módosításokat, egy új szerepkör. Az alábbi példa az "Azure API Management szolgáltatás olvasói" szerepkörrel rendelkező kezdődik, és létrehoz egy egyéni szerepkört, úgynevezett "Számológép API-szerkesztő." Az egyéni szerepkör rendelhet egy adott API-t. Ennek következtében ezt a szerepkört csak az API hozzáféréssel rendelkezik. 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

A [Azure Resource Manager erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) cikk, amely az API Management szinten is megadható engedélyeket listáját tartalmazza.

## <a name="video"></a>Videó


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>További lépések

Szerepköralapú hozzáférés-vezérlés az Azure-ban kapcsolatos további információkért tekintse meg a következő cikkeket:
  * [Bevezetés a hozzáférés-kezelés Azure Portalon történő használatába](../role-based-access-control/overview.md)
  * [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](../role-based-access-control/role-assignments-portal.md)
  * [Egyéni szerepkörök az Azure RBAC-ben](../role-based-access-control/custom-roles.md)
  * [Az Azure Resource Manager erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)