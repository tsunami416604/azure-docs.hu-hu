---
title: Egyéni szerepkör-definíciók – áttekintés
description: A felügyelt alkalmazások egyéni szerepkör-definícióinak létrehozásának fogalmát ismerteti.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7b7fff913c177703f959bfa103c8e310d01059e2
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391827"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Egyéni szerepkör-definíciós összetevő az Azure felügyelt alkalmazásokban

Az egyéni szerepkör-definíció egy választható műtermék a felügyelt alkalmazásokban. Annak meghatározására szolgál, hogy a felügyelt alkalmazásnak milyen engedélyekre van szüksége a függvények végrehajtásához.

Ez a cikk áttekintést nyújt az egyéni szerepkör-definíciós összetevőről és annak képességeiről.

## <a name="custom-role-definition-artifact"></a>Egyéni szerepkör-definíciós összetevő

Meg kell neveznie az egyéni szerepkör-definíciós összetevő customRoleDefinition.json nevet. Helyezze a createUiDefinition.json és a mainTemplate.json szintre a .zip csomagban, amely felügyelt alkalmazásdefiníciót hoz létre. A .zip csomag létrehozásáról és a felügyelt alkalmazásdefiníció közzétételéről a [Felügyelt alkalmazásdefiníció közzététele című](publish-service-catalog-app.md) témakörben olvashat.

## <a name="custom-role-definition-schema"></a>Egyéni szerepkör-definíciós séma

A customRoleDefinition.json fájl egy `roles` legfelső szintű tulajdonsággal rendelkezik, amely szerepkörök tömbje. Ezek a szerepkörök azok az engedélyek, amelyeket a felügyelt alkalmazásnak kell működnie. Jelenleg csak a beépített szerepkörök engedélyezettek, de több szerepkört is megadhat. Egy szerepkörre a szerepkör-definíció azonosítója vagy a szerepkör neve hivatkozhat.

Minta JSON egyéni szerepkör-definíció:

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

## <a name="roles"></a>Szerepkörök

A szerep a vagy `$.properties.roleName` egy: `id`

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> Használhatja a `id` vagy `roleName` a mezőt. Csak egy szükséges. Ezek a mezők az alkalmazandó szerepkör-definíció jának megkeresésére szolgálnak. Ha mindkettő tavan, a `id` mező lesz használva.

|Tulajdonság|Kötelező?|Leírás|
|---------|---------|---------|
|id|Igen|A beépített szerepkör azonosítója. Használhatja a teljes azonosítót, vagy csak a GUID.|
|szerepkörneve|Igen|A beépített szerepkör neve.|