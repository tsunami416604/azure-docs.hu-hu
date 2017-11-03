---
title: "Szerepköralapú hozzáférés-vezérlés REST - az Azure AD |} Microsoft Docs"
description: "A REST API szerepköralapú hozzáférés-vezérlés kezelése"
services: active-directory
documentationcenter: na
author: andredm7
manager: femila
editor: 
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: active-directory
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: andredm
ms.openlocfilehash: a5c19fd87ce1ae3e199bf1dfc8cf82f5653baac2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-role-based-access-control-with-the-rest-api"></a>A REST API szerepköralapú hozzáférés-vezérlés kezelése
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
> * [REST API](role-based-access-control-manage-access-rest.md)

Szerepköralapú hozzáférés-vezérlés (RBAC) az Azure portálon, és az Azure Resource Manager API segítségével kezelhetők az előfizetés és a minden részletre kiterjedő szinten erőforrásokhoz való hozzáférését. Ez a szolgáltatás egyes szerepkörök hozzárendelése el egy adott hatókörhöz szerint engedélyezheti a hozzáférést az Active Directory felhasználók, csoportok vagy szolgáltatásnevekről.

## <a name="list-all-role-assignments"></a>Az összes szerepkör-hozzárendelések felsorolása
Megjeleníti a megadott hatókör és subscopes szerepkör-hozzárendelések.

A szerepkör-hozzárendelések listáját hozzáféréssel kell rendelkeznie `Microsoft.Authorization/roleAssignments/read` műveletet a hatókörben. A beépített szerepkörök művelet számára hozzáférést kapnak. További információ a szerepkör-hozzárendelések és az Azure-erőforrások kezelése hozzáférés: [átruházásához hozzáférés-vezérlés](role-based-access-control-configure.md).

### <a name="request"></a>Kérés
Használja a **beolvasása** metódus a következő URI-azonosítóhoz:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

Az URI belül győződjön testre szabhatja a kérelem a következő helyettesítések hozzáadása:

1. Cserélje le *{hatókör}* a hatókörben, amelynek kívánja a szerepkör-hozzárendelések listáját. A következő példák bemutatják, hogyan adhatja meg a különböző szintű hatóköre:

   * Előfizetés: /subscriptions/ {előfizetés-azonosító}  
   * Erőforráscsoport: /subscriptions/ {előfizetés-azonosító} / resourceGroups/myresourcegroup1  
   * Erőforrás: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Cserélje le *{api-version}* a 2015-07-01.
3. Cserélje le *{szűrő}* azzal a feltétellel, amelyet meg kíván alkalmazni a szerepkör-hozzárendelés csoportlista szűréséhez:

   * Szerepkör-hozzárendelések csak a megadott hatókör, a nem az a szerepkör-hozzárendelések subscopes, beleértve a listában:`atScope()`    
   * Egy adott felhasználó, csoport vagy alkalmazás szerepkör-hozzárendelések listában:`principalId%20eq%20'{objectId of user, group, or service principal}'`  
   * Egy adott felhasználó, például csoportokból örökölt szerepkör-hozzárendelések lista |}`assignedTo('{objectId of user}')`

### <a name="response"></a>Válasz
Állapotkód: 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role-assignment"></a>Szerepkör-hozzárendelés adatainak beolvasása
A szerepkör-hozzárendelés azonosítója által megadott egyetlen szerepkör-hozzárendelés információ lekérése.

Szerepkör-hozzárendelés adatainak beolvasása, hozzáféréssel kell rendelkeznie `Microsoft.Authorization/roleAssignments/read` műveletet. A beépített szerepkörök művelet számára hozzáférést kapnak. További információ a szerepkör-hozzárendelések és az Azure-erőforrások kezelése hozzáférés: [átruházásához hozzáférés-vezérlés](role-based-access-control-configure.md).

### <a name="request"></a>Kérés
Használja a **beolvasása** metódus a következő URI-azonosítóhoz:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Az URI belül győződjön testre szabhatja a kérelem a következő helyettesítések hozzáadása:

1. Cserélje le *{hatókör}* a hatókörben, amelynek kívánja a szerepkör-hozzárendelések listáját. A következő példák bemutatják, hogyan adhatja meg a különböző szintű hatóköre:

   * Előfizetés: /subscriptions/ {előfizetés-azonosító}  
   * Erőforráscsoport: /subscriptions/ {előfizetés-azonosító} / resourceGroups/myresourcegroup1  
   * Erőforrás: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Cserélje le *{szerepkör-hozzárendelés-azonosító}* azon szerepkör-hozzárendelés GUID azonosítóval.
3. Cserélje le *{api-version}* a 2015-07-01.

### <a name="response"></a>Válasz
Állapotkód: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## <a name="create-a-role-assignment"></a>Szerepkör-hozzárendelés létrehozása
Szerepkör-hozzárendelés létrehozása a megadott rendszerbiztonsági tag a megadott szerepkör megadása a megadott hatókörben.

Szerepkör-hozzárendelés létrehozása, hozzáférést kell biztosítani `Microsoft.Authorization/roleAssignments/write` műveletet. A beépített szerepkörök, csak *tulajdonos* és *felhasználói hozzáférés adminisztrátora* férhetnek hozzá ezt a műveletet. További információ a szerepkör-hozzárendelések és az Azure-erőforrások kezelése hozzáférés: [átruházásához hozzáférés-vezérlés](role-based-access-control-configure.md).

### <a name="request"></a>Kérés
Használja a **PUT** metódus a következő URI-azonosítóhoz:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Az URI belül győződjön testre szabhatja a kérelem a következő helyettesítések hozzáadása:

1. Cserélje le *{hatókör}* a hatókörben, ahol létre szeretne hozni a szerepkör-hozzárendeléseket. Szerepkör-hozzárendelés létrehozása a szülő hatókörben, minden gyermekhatókörében örökli a azonos szerepkör-hozzárendelés. A következő példák bemutatják, hogyan adhatja meg a különböző szintű hatóköre:

   * Előfizetés: /subscriptions/ {előfizetés-azonosító}  
   * Erőforráscsoport: /subscriptions/ {előfizetés-azonosító} / resourceGroups/myresourcegroup1   
   * Erőforrás: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Cserélje le *{szerepkör-hozzárendelés-azonosító}* egy új GUID, amely válik az új szerepkör-hozzárendelés GUID azonosítóját.
3. Cserélje le *{api-version}* a 2015-07-01.

A kérelem törzsében adja meg az értékeket a következő formátumban:

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Elem neve | Szükséges | Típus | Leírás |
| --- | --- | --- | --- |
| roledefinitionid-értékkel |Igen |Karakterlánc |A szerepkör azonosítóját. Az azonosító formátuma:`{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId |Igen |Karakterlánc |objectId az Azure AD rendszerbiztonsági tag (felhasználó, csoport vagy egyszerű szolgáltatásneve), amely a szerepkör hozzá van rendelve. |

### <a name="response"></a>Válasz
Állapotkód: 201-et

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## <a name="delete-a-role-assignment"></a>Szerepkör-hozzárendelés törlése
Szerepkör-hozzárendelés törlése a megadott hatókörből.

Szerepkör-hozzárendelés törlése, hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleAssignments/delete` műveletet. A beépített szerepkörök, csak *tulajdonos* és *felhasználói hozzáférés adminisztrátora* férhetnek hozzá ezt a műveletet. További információ a szerepkör-hozzárendelések és az Azure-erőforrások kezelése hozzáférés: [átruházásához hozzáférés-vezérlés](role-based-access-control-configure.md).

### <a name="request"></a>Kérés
Használja a **törlése** metódus a következő URI-azonosítóhoz:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Az URI belül győződjön testre szabhatja a kérelem a következő helyettesítések hozzáadása:

1. Cserélje le *{hatókör}* a hatókörben, ahol létre szeretne hozni a szerepkör-hozzárendeléseket. A következő példák bemutatják, hogyan adhatja meg a különböző szintű hatóköre:

   * Előfizetés: /subscriptions/ {előfizetés-azonosító}  
   * Erőforráscsoport: /subscriptions/ {előfizetés-azonosító} / resourceGroups/myresourcegroup1  
   * Erőforrás: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Cserélje le *{szerepkör-hozzárendelés-azonosító}* a GUID azonosító.
3. Cserélje le *{api-version}* a 2015-07-01.

### <a name="response"></a>Válasz
Állapotkód: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## <a name="list-all-roles"></a>Az összes szerepkör felsorolása
A megadott hatókörben kiosztására használható összes szerepköröket sorolja fel.

A lista szerepkörökhöz hozzáféréssel kell rendelkeznie `Microsoft.Authorization/roleDefinitions/read` műveletet a hatókörben. A beépített szerepkörök művelet számára hozzáférést kapnak. További információ a szerepkör-hozzárendelések és az Azure-erőforrások kezelése hozzáférés: [átruházásához hozzáférés-vezérlés](role-based-access-control-configure.md).

### <a name="request"></a>Kérés
Használja a **beolvasása** metódus a következő URI-azonosítóhoz:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

Az URI belül győződjön testre szabhatja a kérelem a következő helyettesítések hozzáadása:

1. Cserélje le *{hatókör}* a hatókörben, amelynek meg szeretné össze a szerepkörök listáját. A következő példák bemutatják, hogyan adhatja meg a különböző szintű hatóköre:

   * Előfizetés: /subscriptions/ {előfizetés-azonosító}  
   * Erőforráscsoport: /subscriptions/ {előfizetés-azonosító} / resourceGroups/myresourcegroup1  
   * Erőforrás /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Cserélje le *{api-version}* a 2015-07-01.
3. Cserélje le *{szűrő}* azzal a feltétellel, akkor a szerepkörök szűréséhez alkalmazni kíván:

   * Lista szerepkörökhöz rendelhető hozzá a megadott hatókörben és bármely gyermek hatóköréhez tartozó:`atScopeAndBelow()`
   * Keresse meg egy szerepkör használatával pontos megjelenített név: `roleName%20eq%20'{role-display-name}'`. Az URL-kódolású képernyőn a pontos megjelenített névbe, a szerepkör. Például`$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="response"></a>Válasz
Állapotkód: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role"></a>Egy szerepkör adatainak beolvasása
A szerepkör-definíció azonosítója által megadott egyetlen szerepkör információ lekérése. Ahhoz, hogy a megjelenített név megadásával egyetlen szerepkör információt, lásd: [listában az összes szerepkör](role-based-access-control-manage-access-rest.md#list-all-roles).

Egy szerepkör adatainak beolvasása, hozzáféréssel kell rendelkeznie `Microsoft.Authorization/roleDefinitions/read` műveletet. A beépített szerepkörök művelet számára hozzáférést kapnak. További információ a szerepkör-hozzárendelések és az Azure-erőforrások kezelése hozzáférés: [átruházásához hozzáférés-vezérlés](role-based-access-control-configure.md).

### <a name="request"></a>Kérés
Használja a **beolvasása** metódus a következő URI-azonosítóhoz:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Az URI belül győződjön testre szabhatja a kérelem a következő helyettesítések hozzáadása:

1. Cserélje le *{hatókör}* a hatókörben, amelynek kívánja a szerepkör-hozzárendelések listáját. A következő példák bemutatják, hogyan adhatja meg a különböző szintű hatóköre:

   * Előfizetés: /subscriptions/ {előfizetés-azonosító}  
   * Erőforráscsoport: /subscriptions/ {előfizetés-azonosító} / resourceGroups/myresourcegroup1  
   * Erőforrás: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Cserélje le *{szerepkör-definíció-azonosító}* a szerepkör-definíció GUID azonosítóval.
3. Cserélje le *{api-version}* a 2015-07-01.

### <a name="response"></a>Válasz
Állapotkód: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása
Hozzon létre egy egyéni biztonsági szerepkört.

Hozzon létre egy egyéni biztonsági szerepkört, hozzáférést kell biztosítani `Microsoft.Authorization/roleDefinitions/write` minden műveletet a `AssignableScopes`. A beépített szerepkörök, csak *tulajdonos* és *felhasználói hozzáférés adminisztrátora* férhetnek hozzá ezt a műveletet. További információ a szerepkör-hozzárendelések és az Azure-erőforrások kezelése hozzáférés: [átruházásához hozzáférés-vezérlés](role-based-access-control-configure.md).

### <a name="request"></a>Kérés
Használja a **PUT** metódus a következő URI-azonosítóhoz:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Az URI belül győződjön testre szabhatja a kérelem a következő helyettesítések hozzáadása:

1. Cserélje le *{hatókör}* az első *AssignableScope* az egyéni szerepkör. A következő példák bemutatják, hogyan határozhatja meg a különböző szintű hatókörét.

   * Előfizetés: /subscriptions/ {előfizetés-azonosító}  
   * Erőforráscsoport: /subscriptions/ {előfizetés-azonosító} / resourceGroups/myresourcegroup1  
   * Erőforrás: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Cserélje le *{szerepkör-definíció-azonosító}* egy új GUID, amely válik az új egyéni szerepkör GUID azonosítóját.
3. Cserélje le *{api-version}* a 2015-07-01.

A kérelem törzsében adja meg az értékeket a következő formátumban:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Elem neve | Szükséges | Típus | Leírás |
| --- | --- | --- | --- |
| név |Igen |Karakterlánc |Az egyéni szerepkör GUID azonosítója |
| properties.roleName |Igen |Karakterlánc |Az egyéni szerepkör nevét jeleníti meg. Legfeljebb 128 karakter. |
| Properties.Description |Nem |Karakterlánc |Az egyéni szerepkör leírása Maximális méret 1024 karakternél. |
| Properties.Type |Igen |Karakterlánc |Állítsa be a "CustomRole." |
| Properties.permissions.Actions |Igen |String] |Adja meg az egyéni szerepkör által biztosított műveletek művelet karakterláncokból álló tömb. |
| properties.permissions.notActions |Nem |String] |Ha szeretne kizárni a műveletek az egyéni szerepkör által biztosított műveletek művelet karakterláncokkal tömbjét. |
| properties.assignableScopes |Igen |String] |A tömb hatókörök, amelyben az egyéni biztonsági szerepkört is használható. |

### <a name="response"></a>Válasz
Állapotkód: 201-et

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="update-a-custom-role"></a>Frissítés egy egyéni biztonsági szerepkört
Módosítani egy egyéni biztonsági szerepkört.

Módosítja egy egyéni biztonsági szerepkört, hozzáféréssel kell rendelkeznie `Microsoft.Authorization/roleDefinitions/write` minden műveletet a `AssignableScopes`. A beépített szerepkörök, csak *tulajdonos* és *felhasználói hozzáférés adminisztrátora* férhetnek hozzá ezt a műveletet. További információ a szerepkör-hozzárendelések és az Azure-erőforrások kezelése hozzáférés: [átruházásához hozzáférés-vezérlés](role-based-access-control-configure.md).

### <a name="request"></a>Kérés
Használja a **PUT** metódus a következő URI-azonosítóhoz:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Az URI belül győződjön testre szabhatja a kérelem a következő helyettesítések hozzáadása:

1. Cserélje le *{hatókör}* az első *AssignableScope* az egyéni szerepkör. A következő példák bemutatják, hogyan adhatja meg a különböző szintű hatóköre:

   * Előfizetés: /subscriptions/ {előfizetés-azonosító}  
   * Erőforráscsoport: /subscriptions/ {előfizetés-azonosító} / resourceGroups/myresourcegroup1  
   * Erőforrás: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Cserélje le *{szerepkör-definíció-azonosító}* az egyéni szerepkör GUID azonosítóval.
3. Cserélje le *{api-version}* a 2015-07-01.

A kérelem törzsében adja meg az értékeket a következő formátumban:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Elem neve | Szükséges | Típus | Leírás |
| --- | --- | --- | --- |
| név |Igen |Karakterlánc |Az egyéni szerepkör GUID azonosítója |
| properties.roleName |Igen |Karakterlánc |A frissített egyéni szerepkör nevét jeleníti meg. |
| Properties.Description |Nem |Karakterlánc |A frissített egyéni szerepkör leírása |
| Properties.Type |Igen |Karakterlánc |Állítsa be a "CustomRole." |
| Properties.permissions.Actions |Igen |String] |Adja meg a frissített egyéni biztonsági szerepkört, amelyhez engedélyezi a hozzáférést a műveletek művelet karakterláncokból álló tömb. |
| properties.permissions.notActions |Nem |String] |Adja meg a műveleteket, így a frissített egyéni szerepkörök műveletekről kizárása művelet karakterláncokból álló tömb. |
| properties.assignableScopes |Igen |String] |A tömb hatókörök, amelyben a frissített egyéni biztonsági szerepkört is használható. |

### <a name="response"></a>Válasz
Állapotkód: 201-et

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="delete-a-custom-role"></a>Egyéni szerepkör törléséhez
Egyéni szerepkör törléséhez.

Egyéni szerepkör törléséhez hozzáféréssel kell rendelkeznie `Microsoft.Authorization/roleDefinitions/delete` minden műveletet a `AssignableScopes`. A beépített szerepkörök, csak *tulajdonos* és *felhasználói hozzáférés adminisztrátora* férhetnek hozzá ezt a műveletet. További információ a szerepkör-hozzárendelések és az Azure-erőforrások kezelése hozzáférés: [átruházásához hozzáférés-vezérlés](role-based-access-control-configure.md).

### <a name="request"></a>Kérés
Használja a **törlése** metódus a következő URI-azonosítóhoz:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Az URI belül győződjön testre szabhatja a kérelem a következő helyettesítések hozzáadása:

1. Cserélje le *{hatókör}* , amelyen a szerepkör-definíció törölni kívánt hatókörű. A következő példák bemutatják, hogyan adhatja meg a különböző szintű hatóköre:

   * Előfizetés: /subscriptions/ {előfizetés-azonosító}  
   * Erőforráscsoport: /subscriptions/ {előfizetés-azonosító} / resourceGroups/myresourcegroup1  
   * Erőforrás: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Cserélje le *{szerepkör-definíció-azonosító}* a GUID azonosító az egyéni szerepkör.
3. Cserélje le *{api-version}* a 2015-07-01.

### <a name="response"></a>Válasz
Állapotkód: 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
