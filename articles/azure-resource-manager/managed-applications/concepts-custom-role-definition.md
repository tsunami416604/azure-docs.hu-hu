---
title: Egyéni szerepkör-definíciók áttekintése
description: A felügyelt alkalmazásokhoz tartozó egyéni szerepkör-definíciók létrehozásának fogalmát ismerteti.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 88e42fd9626276f6c77b46b33c138407f91d06ca
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650759"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Egyéni szerepkör-definíciós összetevő a Azure Managed Applications

Az egyéni szerepkör-definíció egy opcionális összetevő a felügyelt alkalmazásokban. Annak meghatározására szolgál, hogy a felügyelt alkalmazás milyen engedélyeket kell végrehajtania a függvények működéséhez.

Ez a cikk áttekintést nyújt az egyéni szerepkör-definíciós összetevőről és annak képességeiről.

## <a name="custom-role-definition-artifact"></a>Egyéni szerepkör-definíciós összetevő

A customRoleDefinition. JSON nevű egyéni szerepkör-definíciót kell megadnia. Helyezze el ugyanazon a szinten, mint a createUiDefinition. JSON és a mainTemplate. JSON fájl a felügyelt alkalmazás definícióját létrehozó. zip csomagban. A. zip csomag létrehozásával és a felügyelt alkalmazás definíciójának közzétételével kapcsolatos további információkért lásd: [felügyelt alkalmazás definíciójának közzététele.](publish-managed-app-definition-quickstart.md)

## <a name="custom-role-definition-schema"></a>Egyéni szerepkör-definíciós séma

A customRoleDefinition. JSON fájl legfelső szintű `roles` tulajdonsága, amely a szerepkörök tömbje. Ezek a szerepkörök a felügyelt alkalmazás működéséhez szükséges engedélyek. Jelenleg csak a beépített szerepkörök engedélyezettek, de több szerepkör is megadható. A szerepköröket a szerepkör-definíció vagy a szerepkör neve is hivatkozhat.

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

## <a name="roles"></a>Szerepkörök

A szerepkörök egy `$.properties.roleName` vagy egy `id`ből állnak:

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> A `id` vagy a `roleName` mezőt is használhatja. Csak egy szükséges. Ezekkel a mezőkkel lehet megkeresni a szerepkör-definíciót, amelyet alkalmazni kell. Ha mindkettő meg van adva, a rendszer a `id` mezőt fogja használni.

|Tulajdonság|Kötelező?|Leírás|
|---------|---------|---------|
|id|Igen|A beépített szerepkör azonosítója. Használhatja a teljes azonosítót vagy csak a GUID azonosítót.|
|roleName|Igen|A beépített szerepkör neve.|