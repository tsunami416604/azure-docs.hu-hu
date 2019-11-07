---
title: Az egyéni szerepkör-definíciók áttekintése a Azure Managed Applicationsban | Microsoft Docs
description: A felügyelt alkalmazásokhoz tartozó egyéni szerepkör-definíciók létrehozásának fogalmát ismerteti.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: d645eebefde473e404f7760d2bc8a67c7e3e9087
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609033"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Egyéni szerepkör-definíciós összetevő a Azure Managed Applications

Az egyéni szerepkör-definíció egy opcionális összetevő a felügyelt alkalmazásokban. Annak meghatározására szolgál, hogy milyen engedélyek szükségesek a felügyelt alkalmazás számára a függvények végrehajtásához.

Ez a cikk áttekintést nyújt az egyéni szerepkör-definíciós összetevőről és annak képességeiről.

## <a name="custom-role-definition-artifact"></a>Egyéni szerepkör-definíciós összetevő

Az egyéni szerepkör-definíciós összetevőnek **customRoleDefinition. JSON** nevűnek kell lennie, és a felügyelt alkalmazás definícióját létrehozó. zip-csomagban található **createUiDefinition. JSON** és **mainTemplate. JSON** fájlnak megfelelő szintre kell helyeznie. A. zip csomag létrehozásával és a felügyelt alkalmazás definíciójának közzétételével kapcsolatos további információkért lásd: [felügyelt alkalmazás definíciójának közzététele.](publish-managed-app-definition-quickstart.md)

## <a name="custom-role-definition-schema"></a>Egyéni szerepkör-definíciós séma

A **customRoleDefinition. JSON** fájl legfelső szintű `roles` tulajdonsága, amely a szerepkörök tömbje. Ezek a szerepkörök a felügyelt alkalmazás működéséhez szükséges engedélyek. Jelenleg csak a beépített szerepkörök engedélyezettek, de több szerepkör is megadható. A szerepkört a szerepkör-definíció vagy a szerepkör neve is hivatkozhat.

Egyéni szerepkör-definíciók JSON-mintája:

```json
{
    "contentVersion": "0.0.0.1",
    "roles": [
        {
            "properties": {
                "roleName": "Contributor"
            }
        },
        {
            "id": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
        },
        {
            "id": "/providers/Microsoft.Authorization/roledefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
        }
    ]
}
```

## <a name="role"></a>Szerepkör

A szerepkörök egy `$.properties.roleName` vagy `id`ból állnak.

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!Note]
> A `id` vagy a `roleName` mező közül csak az egyik szükséges. Ezekkel a mezőkkel lehet megkeresni az alkalmazandó szerepkör-definíciót. Ha mindkettő meg van adva, a rendszer a `id` mezőt fogja használni.

|Tulajdonság|Kötelező|Leírás|
|---------|---------|---------|
|ID (Azonosító)|*igen*|A beépített szerepkör azonosítója. Ez a tulajdonság lehet a teljes azonosító vagy csak a GUID.|
|RoleName|*igen*|A beépített szerepkör neve.|