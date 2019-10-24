---
title: Egyéni rendszergazdai szerepkörök kiosztása és eltávolítása Microsoft Graph API-Azure Active Directory használatával | Microsoft Docs
description: Azure AD-rendszergazdai szerepkörök kiosztása és eltávolítása Graph APIekkel Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 898f444e868a469aed5358f49f48f5bcbfab4450
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707577"
---
# <a name="assign-custom-admin-roles-using-graph-api-in-azure-active-directory"></a>Egyéni rendszergazdai szerepkörök kiosztása Graph API használatával Azure Active Directory 

Automatizálhatja, hogyan rendeljen hozzá szerepköröket felhasználói fiókokhoz Microsoft Graph API-hoz. Ez a cikk a roleAssignments POST, GET és DELETE műveleteit ismerteti.

## <a name="required-permissions"></a>Szükséges engedélyek

Kapcsolódjon az Azure AD-bérlőhöz egy globális rendszergazdai fiókkal vagy egy emelt szintű identitás-rendszergazdával szerepkörök hozzárendeléséhez vagy eltávolításához.

## <a name="post-operations-on-roleassignment"></a>RoleAssignment utáni műveletek

HTTP-kérelem a felhasználó és a szerepkör-definíció közötti szerepkör-hozzárendelés létrehozásához.

POST

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal
```

Body

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/"]
}
```

Válasz

``` HTTP
HTTP/1.1 201 Created
```

HTTP-kérelem olyan szerepkör-hozzárendelés létrehozásához, amelyben a rendszerbiztonsági tag vagy szerepkör-definíció nem létezik

POST

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal
```

Body

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/"]
}
```

Válasz

``` HTTP
HTTP/1.1 404 Not Found
```

HTTP-kérelem egyetlen erőforrás-hatókörű szerepkör-hozzárendelés létrehozásához egy beépített szerepkör-definícióban.

> [!NOTE] 
> A beépített szerepkörök ma már rendelkeznek egy korlátozással, amely csak a "/" szervezeti hatókörre vagy a "/AU/*" hatókörre alkalmazható. Az egyetlen erőforrás-hatókör nem működik a beépített szerepkörök esetében, de egyéni szerepkörök esetén is működik.

POST

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal
```

Body

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/ab2e1023-bddc-4038-9ac1-ad4843e7e539"]
}
```

Válasz

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Provided authorization scope is not supported for built-in role definitions."},
            "values":
            [
                {
                    "item":"scope",
                    "value":"/ab2e1023-bddc-4038-9ac1-ad4843e7e539"
                }
            ]
        }
    }
}
```

## <a name="get-operations-on-roleassignment"></a>Műveletek beolvasása a RoleAssignment

HTTP-kérelem egy adott résztvevő szerepkör-hozzárendelésének beszerzéséhez

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal&$filter=principalId eq ‘<object-id-of-principal>’
```

Válasz

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":["/"]
} ,
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

HTTP-kérelem egy adott szerepkör-definícióhoz tartozó szerepkör-hozzárendelés beszerzéséhez.

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Válasz

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

HTTP-kérelem a szerepkör-hozzárendelés azonosító alapján való lekéréséhez.

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

Válasz

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":["/"]
}
```

## <a name="delete-operations-on-roleassignment"></a>Műveletek törlése a RoleAssignment

HTTP-kérelem a felhasználó és a szerepkör-definíció közötti szerepkör-hozzárendelés törléséhez.

DELETE

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

Válasz
``` HTTP
HTTP/1.1 204 No Content
```

HTTP-kérelem a már nem létező szerepkör-hozzárendelés törléséhez

DELETE

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

Válasz

``` HTTP
HTTP/1.1 404 Not Found
```

HTTP-kérelem az önálló és a beépített szerepkör-definíció közötti szerepkör-hozzárendelés törléséhez

DELETE

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

Válasz

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Cannot remove self from built-in role definitions."},
            "values":null
        }
    }
}
```

## <a name="next-steps"></a>További lépések

* Nyugodtan ossza meg velünk az [Azure ad rendszergazdai szerepkörökkel](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)foglalkozó fórumát.
* A szerepkörökkel és a rendszergazdai szerepkör-hozzárendeléssel kapcsolatos további információkért lásd: [rendszergazdai szerepkörök hozzárendelése](directory-assign-admin-roles.md).
* Az alapértelmezett felhasználói engedélyek összehasonlítását lásd: a [vendég és a tag alapértelmezett felhasználói engedélyeinek összehasonlítása](../fundamentals/users-default-permissions.md).
