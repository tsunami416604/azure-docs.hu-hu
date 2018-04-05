---
title: Szerepköralapú hozzáférés-vezérlés használata az Azure API Management |} Microsoft Docs
description: 'Útmutató: a beépített szerepkörök használatával, és hozzon létre egyéni szerepkörök az Azure API Management'
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
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: c775780a39c4d423c62bf88f55d35675c70442c7
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Szerepköralapú hozzáférés-vezérlés használata az Azure API Management
Az Azure API Management támaszkodik a átruházásához hozzáférés-vezérlés (RBAC) engedélyezéséhez a részletes hozzáféréskezelést az API Management-szolgáltatások és entitások (például API-k és házirendek). Ez a cikk áttekintést nyújt a beépített és egyéni szerepkörök az API Management. További információ a hozzáférés-kezelés az Azure-portálon: [Ismerkedés az Azure-portálon kezelési](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

## <a name="built-in-roles"></a>Beépített szerepkörök
Az API Management jelenleg három beépített szerepkörök biztosít, és felveszi a közeljövőben két további szerepköröket. Ezeket a szerepköröket, különböző hatókör, beleértve a előfizetés, erőforráscsoport és az egyes API Management-példány rendelhetők hozzá. Például, ha az "Azure API Management szolgáltatás olvasó" szerepkör hozzárendelése egy felhasználóhoz, az erőforráscsoport szintjén, majd a felhasználó rendelkezik olvasási hozzáférés belül az erőforráscsoport összes API Management-példányt. 

A következő táblázat a beépített szerepkörök rövid leírása. Ezeket a szerepköröket rendelhet az Azure-portálon vagy más eszközeit, beleértve az Azure használatával [PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell), [Azure CLI](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli), és [REST API](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-rest). További beépített szerepkörök társításával kapcsolatos információkért lásd: [az Azure-előfizetés erőforrásokhoz való hozzáférés kezelése a szerepkör-hozzárendelések segítségével](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

| Szerepkör          | Olvasási hozzáférés<sup>[1]</sup> | Írási<sup>[2]</sup> | Szolgáltatás létrehozása, törlés, a méretezés, a VPN- és az egyéni tartomány beállítása | Az örökölt publisher portálhoz való hozzáférés | Leírás
| ------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| Az Azure API Management szolgáltatás közreműködő | ✓ | ✓ | ✓ | ✓ | Felügyelői. Az API Management-szolgáltatások és entitások (például API-k és házirendek) teljes CRUD hozzáféréssel rendelkezik. Az örökölt publisher portal hozzáférése van. |
| Az Azure API Management szolgáltatás olvasó | ✓ | | || Az API Management-szolgáltatások és entitások olvasási hozzáférése van. |
| Az Azure API Management szolgáltatást üzemeltető | ✓ | | ✓ | | Kezelheti az API Management-szolgáltatások, de nem entitás.|
| Az Azure API Management Service-szerkesztő<sup>*</sup> | ✓ | ✓ | |  | Az API Management entitások, de nem szolgáltatások kezelheti.|
| Az Azure API Management tartalomkezelő<sup>*</sup> | ✓ | | | ✓ | Kezelheti a fejlesztői portálján. Csak olvasási hozzáféréssel szolgáltatásokhoz és entitások.|

<sup>[1] olvasási hozzáférés az API Management-szolgáltatások és entitások (például API-k és házirendek).</sup>

<sup>[2] írási hozzáféréssel az API Management-szolgáltatások és entitások, kivéve a következő műveleteket: példány létrehozása, törlése és a méretezés; VPN-konfiguráció; és egyéni tartomány beállítása.</sup>

<sup>\* A szolgáltatás szerkesztő szerepkör után azt át a rendszergazda felhasználói felület a meglévő publisher portálon az Azure-portálon lesz elérhető. Tartalomkezelő szerepkör az csak magában foglalja a fejlesztői portálján felügyeletével kapcsolatos funkciók átkerült a közzétevő portal után lesz elérhető.</sup>  

## <a name="custom-roles"></a>Egyéni szerepkörök
Ha az adott igényeknek a beépített szerepkörök egyike, egyéni szerepkörök arra, hogy részletesebb access management az API Management entitásokat is létrehozható. Például létrehozhat egy egyéni biztonsági szerepkört, amely az API Management szolgáltatásnak olvasási hozzáféréssel rendelkezik, de csak egy adott API írási hozzáféréssel. Egyéni szerepkörök kapcsolatos további információkért lásd: [egyéni szerepkörök az Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles). 

Amikor létrehoz egy egyéni biztonsági szerepkört, célszerűbb a beépített szerepkörök egyikével. Adja hozzá az attribútumok szerkesztése **műveletek**, **NotActions**, vagy **AssignableScopes**, és mentse a módosításokat egy új szerepkörként. Az alábbi példában az "Azure API Management szolgáltatás olvasó" szerepkör kezdődik, és létrehoz egy egyéni biztonsági szerepkört nevű "Számológép API szerkesztő." Az egyéni biztonsági szerepkört rendelhet egy adott API-t. Ezért ezt a szerepkört csak az adott API hozzáférése van. 

```
$role = Get-AzureRmRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzureRmRoleDefinition -Role $role
New-AzureRmRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

A [Azure Resource Manager erőforrás-szolgáltatói műveletekhez](../active-directory/role-based-access-control-resource-provider-operations.md#microsoftapimanagement) a cikk az engedélyeket, az API-felügyeleti szinten engedélyezhetők listáját tartalmazza.

## <a name="video"></a>Videó


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>További lépések

Szerepköralapú hozzáférés-vezérlés az Azure-ban kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
  * [Bevezetés a hozzáférés-kezelés Azure Portalon történő használatába](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/)
  * [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/)
  * [Egyéni szerepkörök az Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)
  * [Az Azure Resource Manager erőforrás-szolgáltatói műveletekhez](../active-directory/role-based-access-control-resource-provider-operations.md#microsoftapimanagement)

