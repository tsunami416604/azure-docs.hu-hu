---
title: Azure AD-rendszergazdai szerepkörök hozzárendelése a Microsoft Graph API-val | Microsoft dokumentumok
description: Azure AD rendszergazdai szerepkörök hozzárendelése és eltávolítása az Azure Active DirectoryGraph-ban lévő Graph API-val
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3632f8a360df8837569104232b7380fdc8383953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559147"
---
# <a name="assign-custom-admin-roles-using-the-microsoft-graph-api-in-azure-active-directory"></a>Egyéni rendszergazdai szerepkörök hozzárendelése az Azure Active DirectoryBan a Microsoft Graph API használatával 

A Microsoft Graph API-val automatizálhatja, hogyan rendelhet szerepköröket a felhasználói fiókokhoz. Ez a cikk a szerepkör-hozzárendelések POST, GET és DELETE műveleteit ismerteti.

## <a name="required-permissions"></a>Szükséges engedélyek

Csatlakozzon az Azure AD-bérlőhöz egy globális rendszergazdai fiókkal vagy emelt szintű identitással szerepkörök hozzárendeléséhez vagy eltávolításához.

## <a name="post-operations-on-roleassignment"></a>POST műveletek a Szerepkör-hozzárendelésen

HTTP-kérelem egy szerepkör-hozzárendelés létrehozásához egy felhasználó és egy szerepkör-definíció között.

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json
```

Törzs

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

HTTP-kérelem olyan szerepkör-hozzárendelés létrehozására, amelyben a fő vagy a szerepkör-definíció nem létezik

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Törzs

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

HTTP-kérelem egyetlen erőforráshatókör-hozzárendelés létrehozására egy beépített szerepkör-definíción.

> [!NOTE] 
> A beépített szerepkörök ma már korlátozzák, hogy csak a "/" szervezet szintű hatókörre vagy az "/AU/*" hatókörre terjedjenek ki. Az egyerőforrás-hatókör nem működik a beépített szerepköröknél, de egyéni szerepköröknél is működik.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Törzs

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

## <a name="get-operations-on-roleassignment"></a>GET műveletek a RoleAssignment-n

HTTP-kérelem egy adott főtag szerepkör-hozzárendelésének lekéréséhez

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=principalId eq ‘<object-id-of-principal>’
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

HTTP-kérelem egy adott szerepkör-definíció szerepkör-hozzárendelésének lekéréséhez.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
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

HTTP-kérelem szerepkör-hozzárendelés id szerint.

GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Válasz

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1",
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":["/"]
}
```

## <a name="delete-operations-on-roleassignment"></a>DELETE műveletek a RoleAssignment-n

HTTP-kérelem egy szerepkör-hozzárendelés törléséhez egy felhasználó és egy szerepkör-definíció között.

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Válasz
``` HTTP
HTTP/1.1 204 No Content
```

A MÁR nem létező szerepkör-hozzárendelés törlésére irányuló HTTP-kérelem

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Válasz

``` HTTP
HTTP/1.1 404 Not Found
```

HTTP-kérelem az önálló és a beépített szerepkör-definíció közötti szerepkör-hozzárendelés törlésére

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
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

* Nyugodtan ossza meg velünk az [Azure AD felügyeleti szerepkörök fórum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* A szerepkörökről és a Rendszergazdai szerepkör-hozzárendelésről a [Rendszergazdai szerepkörök hozzárendelése című témakörben](directory-assign-admin-roles.md)van további témakör.
* Az alapértelmezett felhasználói engedélyeket [az alapértelmezett vendég- és tagjogosultságok összehasonlítása](../fundamentals/users-default-permissions.md)című témakörben olvashatja.
