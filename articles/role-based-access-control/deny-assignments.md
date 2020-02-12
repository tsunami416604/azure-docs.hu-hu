---
title: Az Azure-erőforrások megtagadási hozzárendeléseinek megismerése
description: További információ az Azure-erőforrások szerepköralapú hozzáférés-vezérlési (RBAC) hozzárendeléseinek megtagadásáról.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 7ab811635ca50c3a28ecd8bdf6d0f18fad4c384f
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137374"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Az Azure-erőforrások megtagadási hozzárendeléseinek megismerése

A szerepkör-hozzárendeléshez hasonlóan a megtagadási *hozzárendelések* egy adott hatókörben egy felhasználóhoz, csoporthoz vagy egyszerű szolgáltatáshoz tartozó megtagadási műveletet csatolnak a hozzáférés megtagadása céljából. A hozzárendelések megtagadása esetén a felhasználók bizonyos Azure-erőforrás-műveleteket hajtanak végre, még akkor is, ha egy szerepkör-hozzárendelés hozzáférést biztosít

Ez a cikk a megtagadási hozzárendelések definiálásának módját ismerteti.

## <a name="how-deny-assignments-are-created"></a>A megtagadási hozzárendelések létrehozása

A megtagadási hozzárendeléseket az Azure hozza létre és kezeli az erőforrások elleni védelem érdekében. Az Azure-tervezetek és az Azure által felügyelt alkalmazások megtagadási hozzárendelésekkel védik a rendszerfelügyelt erőforrásokat. Az Azure-tervezetek és az Azure által felügyelt alkalmazások kizárólag a megtagadási hozzárendelések létrehozására használhatók. Nem hozhat létre közvetlenül saját megtagadási hozzárendeléseket.  További információkért lásd: [új erőforrások elleni védelem az Azure-tervekben – erőforrás-zárolások](../governance/blueprints/tutorials/protect-new-resources.md).

> [!NOTE]
> Nem hozhat létre közvetlenül saját megtagadási hozzárendeléseket.

## <a name="compare-role-assignments-and-deny-assignments"></a>Szerepkör-hozzárendelések összehasonlítása és a hozzárendelések megtagadása

A megtagadási hozzárendelések a szerepkör-hozzárendelések hasonló mintáját követik, de vannak eltérések is.

| Képesség | Szerepkör-kijelölés | Hozzárendelés megtagadása |
| --- | --- | --- |
| Hozzáférés biztosítása | :heavy_check_mark: |  |
| Hozzáférés megtagadása |  | :heavy_check_mark: |
| Közvetlenül is létrehozható | :heavy_check_mark: |  |
| Alkalmazás hatókörön | :heavy_check_mark: | :heavy_check_mark: |
| Rendszerbiztonsági tag kizárása |  | :heavy_check_mark: |
| Alárendelt hatókörök öröklésének megakadályozása |  | :heavy_check_mark: |
| Alkalmazás a [klasszikus előfizetés-rendszergazdai](rbac-and-directory-admin-roles.md) hozzárendelésekre |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Hozzárendelési tulajdonságok megtagadása

 Egy megtagadási hozzárendelés a következő tulajdonságokkal rendelkezik:

> [!div class="mx-tableFixed"]
> | Tulajdonság | Kötelező | Típus | Leírás |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Igen | Sztring | A megtagadási hozzárendelés megjelenített neve. A névnek egyedinek kell lennie egy adott hatókörhöz. |
> | `Description` | Nem | Sztring | A megtagadási hozzárendelés leírása. |
> | `Permissions.Actions` | Legalább egy művelet vagy egy DataActions | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza azokat a felügyeleti műveleteket, amelyekhez a megtagadási hozzárendelés blokkolja a hozzáférést. |
> | `Permissions.NotActions` | Nem | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza a megtagadási hozzárendelésből kizárandó felügyeleti műveleteket. |
> | `Permissions.DataActions` | Legalább egy művelet vagy egy DataActions | Karakterlánc [] | Karakterláncok tömbje, amelyek meghatározzák azokat az adatműveleteket, amelyekhez a megtagadási hozzárendelés blokkolja a hozzáférést. |
> | `Permissions.NotDataActions` | Nem | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza a megtagadási hozzárendelésből kizárandó adatműveleteket. |
> | `Scope` | Nem | Sztring | Egy karakterlánc, amely megadja azt a hatókört, amelyre a megtagadási hozzárendelés vonatkozik. |
> | `DoNotApplyToChildScopes` | Nem | Logikai | Meghatározza, hogy a megtagadási hozzárendelés alárendelt hatókörökre vonatkozzon-e. Az alapértelmezett érték false (hamis). |
> | `Principals[i].Id` | Igen | Karakterlánc [] | Az Azure AD rendszerbiztonsági tag-azonosítók (felhasználó, csoport, szolgáltatásnév vagy felügyelt identitás) tömbje, amelyre a megtagadási hozzárendelés vonatkozik. Az összes résztvevőt jelölő üres GUID `00000000-0000-0000-0000-000000000000` értékre van állítva. |
> | `Principals[i].Type` | Nem | Karakterlánc [] | Objektumtípusok tömbje, amelyet a rendszerbiztonsági tag [i]. id. az összes rendszerbiztonsági tag jelölésére beállított `SystemDefined`. |
> | `ExcludePrincipals[i].Id` | Nem | Karakterlánc [] | Az Azure AD Principal-objektumazonosítók (felhasználó, csoport, szolgáltatásnév vagy felügyelt identitás) tömbje, amelyre a megtagadási hozzárendelés nem vonatkozik. |
> | `ExcludePrincipals[i].Type` | Nem | Karakterlánc [] | Az ExcludePrincipals [i]. id által jelölt objektumtípusok tömbje. |
> | `IsSystemProtected` | Nem | Logikai | Megadja, hogy a megtagadási hozzárendelést az Azure hozta-e létre, és nem szerkeszthető és nem törölhető. Jelenleg minden megtagadási hozzárendelés a rendszer által védett. |

## <a name="the-all-principals-principal"></a>Az összes rendszerbiztonsági tag

A megtagadási hozzárendelések támogatásához a rendszer által definiált rendszerbiztonsági *tag lett* bevezetve. Ez a rendszerbiztonsági tag az összes felhasználót, csoportot, egyszerű szolgáltatást és felügyelt identitást jelképezi egy Azure AD-címtárban. Ha a résztvevő azonosítója nulla GUID `00000000-0000-0000-0000-000000000000`, és a rendszerbiztonsági tag `SystemDefined`, a rendszerbiztonsági tag az összes résztvevőt képviseli. Azure PowerShell kimenetben az összes rendszerbiztonsági tag a következőhöz hasonlóan néz ki:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Minden rendszerbiztonsági tag kombinálható `ExcludePrincipals`ekkel, hogy az összes résztvevőt megtagadja, kivéve egyes felhasználókat. Minden rendszerbiztonsági tag a következő korlátozásokkal rendelkezik:

- Csak `Principals` használható, és nem használható `ExcludePrincipals`ban.
- a `Principals[i].Type` `SystemDefined`re kell beállítani.

## <a name="next-steps"></a>Következő lépések

* [Azure-erőforrások megtagadási hozzárendeléseinek listázása a Azure Portal használatával](deny-assignments-portal.md)
* [Az Azure-erőforrások szerepkör-definícióinak megismerése](role-definitions.md)
