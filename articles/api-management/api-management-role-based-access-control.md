---
title: Szerepköralapú hozzáférés-vezérlés használata az Azure API-kezelésben | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja a beépített szerepköröket, és hogyan hozhat létre egyéni szerepköröket az Azure API Management ben
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: ed0cd51fc686735f2d9c110ce46d5904107cafc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430620"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Szerepköralapú hozzáférés-vezérlés használata az Azure API Managementben

Az Azure API Management az Azure szerepköralapú hozzáférés-vezérlésre (RBAC) támaszkodik, amely részletes hozzáférés-kezelést tesz lehetővé az API-kezelési szolgáltatások és entitások (például API-k és szabályzatok) számára. Ez a cikk áttekintést nyújt az API Management beépített és egyéni szerepköreiről. Az Azure Portalon a hozzáférés-kezelésről az [Azure Portalon a Hozzáférés-kezelés első lépései című témakörben](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/)talál további információt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Beépített szerepkörök

Az API Management jelenleg három beépített szerepkört biztosít, és a közeljövőben további két szerepkört ad hozzá. Ezek a szerepkörök különböző hatókörökhöz rendelhetők hozzá, beleértve az előfizetést, az erőforráscsoportot és az egyes API Management-példányokat. Például ha az "API Management Service Reader" szerepkört egy felhasználóhoz rendeli az erőforráscsoport szintjén, akkor a felhasználó olvasási hozzáféréssel rendelkezik az erőforráscsoporton belüli összes API Management-példányhoz. 

Az alábbi táblázat röviden ismerteti a beépített szerepköröket. Ezeket a szerepköröket az Azure Portalon vagy más eszközökkel rendelheti hozzá, például az Azure [PowerShell,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) [az Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)és a [REST API használatával.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest) A beépített szerepkörök hozzárendeléséről a [Szerepkör-hozzárendelések használata az Azure-előfizetési erőforrásokhoz való hozzáférés kezeléséhez](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)című témakörben talál további információt.

| Szerepkör          | Olvasási hozzáférés<sup>[1]</sup> | Írási hozzáférés<sup>[2]</sup> | Szolgáltatás létrehozása, törlése, méretezése, VPN- és egyéni tartománykonfigurációja | Hozzáférés az örökölt közzétevői portálhoz | Leírás
| ------------- | ---- | ---- | ---- | ---- | ---- 
| API-felügyeleti szolgáltatás közreműködője | ✓ | ✓ | ✓ | ✓ | Szuper felhasználó. Teljes CRUD-hozzáféréssel rendelkezik az API Management szolgáltatásokhoz és entitásokhoz (például API-khoz és szabályzatokhoz). Hozzáféréssel rendelkezik az örökölt közzétevői portálhoz. |
| API-felügyeleti szolgáltatás olvasó | ✓ | | || Írásvédett hozzáféréssel rendelkezik az API Management szolgáltatásokhoz és entitásokhoz. |
| API-felügyeleti szolgáltatás operátora | ✓ | | ✓ | | Kezelheti az API Management szolgáltatásokat, de az entitásokat nem.|
| API-felügyeleti szolgáltatásszerkesztő<sup>*</sup> | ✓ | ✓ | |  | Kezelheti az API Management entitásokat, de a szolgáltatásokat nem.|
| API-kezelés tartalomkezelő<sup>*</sup> | ✓ | | | ✓ | Kezelheti a fejlesztői portált. Csak olvasható hozzáférés a szolgáltatásokhoz és entitásokhoz.|

<sup>[1] Olvasási hozzáférés az API Management szolgáltatásokhoz és entitásokhoz (például API-khoz és szabályzatokhoz).</sup>

<sup>[2] Az API Management-szolgáltatásokhoz és -entitásokhoz való hozzáférés írása a következő műveletek kivételével: példány létrehozása, törlése és méretezése; VPN-konfiguráció; és az egyéni tartomány beállítása.</sup>

<sup>\*A Szolgáltatásszerkesztő szerepkör akkor lesz elérhető, miután áttelepítettük az összes rendszergazdai felhasználói felületet a meglévő közzétevői portálról az Azure Portalra. A Tartalomkezelő szerepkör akkor lesz elérhető, ha a közzétevői portál újralett, hogy csak a fejlesztői portál kezelésével kapcsolatos funkciókat tartalmazzon.</sup>  

## <a name="custom-roles"></a>Egyéni szerepkörök

Ha a beépített szerepkörök egyike sem felel meg az ön igényeinek, egyéni szerepkörök hozhatók létre, hogy részletesebb hozzáférés-kezelést biztosítsanak az API Management entitások számára. Létrehozhat például egy egyéni szerepkört, amely csak olvasható hozzáféréssel rendelkezik egy API Management szolgáltatáshoz, de csak egy adott API-hoz rendelkezik írási hozzáféréssel. Ha többet szeretne megtudni az egyéni szerepkörökről, olvassa el [az Egyéni szerepkörök az Azure RBAC-ban című témakört.](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) 

> [!NOTE]
> Ahhoz, hogy egy API Management-példány az Azure Portalon, egy egyéni szerepkör kell tartalmaznia a ```Microsoft.ApiManagement/service/read``` műveletet.

Amikor létrehoz egy egyéni szerepkört, könnyebb en a beépített szerepkörök egyikével kezdeni. Módosítsa az attribútumokat **a Műveletek**, **NotActions**vagy **AssignableScopes**hozzáadásához, majd mentse a módosításokat új szerepkörként. A következő példa az "API Management Service Reader" szerepkörrel kezdődik, és létrehoz egy "Számológép API-szerkesztő" nevű egyéni szerepkört. Az egyéni szerepkört hozzárendelheti egy adott API-hoz. Következésképpen ez a szerepkör csak az API-hoz való hozzáféréssel rendelkezik. 

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

Az [Azure Resource Manager erőforrás-szolgáltató műveletek](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) cikk tartalmazza az API-kezelés szintjén adható engedélyek listáját.

## <a name="video"></a>Videó


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni az Azure-beli szerepköralapú hozzáférés-vezérlésről, olvassa el az alábbi cikkeket:
  * [Bevezetés a hozzáférés-kezelés Azure Portalon történő használatába](../role-based-access-control/overview.md)
  * [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](../role-based-access-control/role-assignments-portal.md)
  * [Egyéni szerepkörök az Azure RBAC-ban](../role-based-access-control/custom-roles.md)
  * [Az Azure Resource Manager erőforrás-szolgáltatóműveletei](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
