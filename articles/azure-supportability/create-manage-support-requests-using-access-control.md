---
title: Az Azure szerepköralapú hozzáférés vezérlés (RBAC) szabályozhatja a hozzáférési jogosultságokat, a támogatási kérések létrehozásához és kezeléséhez |} A Microsoft Docs
description: Az Azure szerepköralapú hozzáférés vezérlés (RBAC) szabályozhatja a hozzáférési jogosultságokat, a támogatási kérések létrehozásához és kezeléséhez
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: azure
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: d98d0637c6d520193b11f4267c59016772ef063a
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792480"
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Az Azure szerepköralapú hozzáférés vezérlés (RBAC) szabályozhatja a hozzáférési jogosultságokat, a támogatási kérések létrehozásához és kezeléséhez

[Szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) hozzáféréskezelést az Azure lehetővé teszi.
Támogatási kérelem létrehozása az Azure Portalon [portal.azure.com](https://portal.azure.com), határozza meg, akik létrehozása és támogatási kérések kezelése az Azure RBAC modellt használ.
A megfelelő RBAC-szerepkörök hozzárendelése a felhasználók, csoportok és alkalmazások egy bizonyos hatókörben, amely lehet egy előfizetés, erőforráscsoport vagy erőforrás hozzáférési jogot.

Vegyünk egy példát: Egy erőforráscsoport-tulajdonosnak olvasási engedélyekkel az előfizetések szintjén, mint az erőforráscsoport, például a websites, a virtuális gépek és alhálózatok tartozó összes erőforrást is kezelheti.
Azonban amikor megpróbál létrehozni egy támogatási kérelmet a virtuális gép típusú erőforrást, tapasztal a következő hiba

![Előfizetés hiba](./media/create-manage-support-requests-using-access-control/subscription-error.png)

A támogatási kérelem kezelése engedély, vagy egy szerepkör, amely rendelkezik a támogatási művelet Microsoft.Support/* tudják támogatási kérések létrehozásához és kezeléséhez az előfizetések szintjén kell írni.

A következő cikk azt ismerteti, hogyan használhatja az Azure egyéni szerepköralapú hozzáférés-vezérlés (RBAC), az Azure Portalon a támogatási kérések létrehozásához és kezeléséhez.

## <a name="getting-started"></a>Első lépések

A fenti példa használatával, akkor tudná hozhat létre egy támogatási kérést az erőforrás, ha a rendszer egy egyéni RBAC-szerepkört az előfizetésre az előfizetés tulajdonosa.
[Egyéni RBAC-szerepkörök](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) hozhatók létre az Azure PowerShell, az Azure parancssori felület (CLI) és a REST API használatával.

A műveletek tulajdonsága egy egyéni biztonsági szerepkört, amelyhez a szerepkör hozzáférést biztosít az Azure műveletek megadja.
Hozzon létre egy egyéni szerepkört, a támogatási kérések kezelése, a szerepkör kell rendelkeznie a művelet Microsoft.Support/*

Íme egy példa egy egyéni szerepkör segítségével támogatási kérések létrehozásához és kezeléséhez.
Azt már neve a "Támogatási kérelem közreműködő" szerepkör, és ez hogyan nevezzük az egyéni szerepkör ebben a cikkben.

``` Json
{
    "Name":  "Support Request Contributor",
    "Id":  "1f2aad59-39b0-41da-b052-2fb070bd7942",
    "IsCustom":  true,
    "Description":  "Lets you create and manage support tickets.",
    "Actions":  [
                    "Microsoft.Support/*"
                ],
    "NotActions":  [
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Kövesse az ismertetett lépéseket [ebben a videóban](https://www.youtube.com/watch?v=-PaBaDmfwKI) megtudhatja, hogyan hozhat létre egy egyéni biztonsági szerepkört az előfizetés.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Az Azure Portalon a támogatási kérések létrehozásához és kezeléséhez

Vegyünk egy példát – Ön a tulajdonosa az előfizetés "Visual Studio MSDN-előfizetés."
János a társ, akik ebben az előfizetésben az erőforráscsoportok némelyike erőforrás tulajdonosa, és rendelkezik-e olvasási engedéllyel az előfizetéshez.
A társ, Joe, hozzon létre és kezelhetik a támogatási jegyeket, ehhez az előfizetéshez tartozó erőforrásokhoz való hozzáférést szeretné.

1. Az első lépéseként nyissa meg az előfizetést, és a "Beállítások" felhasználók listájának megtekintéséhez. Kattintson a felhasználó János, akik olvasó hozzáféréssel rendelkezik az előfizetésre, és most egy új egyéni biztonsági szerepkört rendel neki.

    ![Szerepkör hozzáadása](./media/create-manage-support-requests-using-access-control/add-role.png)

2. Kattintson a "Hozzáadás" a "Felhasználók" panel alatt. Az egyéni "Támogatási kérelem közreműködő" szerepkör kiválasztása a szerepkörök listájáról

    ![Támogatási közreműködői szerepkör hozzáadása](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. Miután kiválasztotta a szerepkör nevét, kattintson a "Felhasználó hozzáadása", és adja meg a Joe e-mail hitelesítő adatait. Kattintson a Kiválasztás gombra.

    ![Felhasználók hozzáadása](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Kattintson az "Ok" gombra. a folytatáshoz

    ![Hozzáférés hozzáadása](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Megjelenik az újonnan hozzáadott egyéni szerepkör "Támogatási kérelem közreműködő", amelynek Ön a tulajdonosa az előfizetés rendelkező felhasználó

    ![Felhasználó hozzáadva](./media/create-manage-support-requests-using-access-control/user-added.png)

    János naplózza a portálon, ha látja az előfizetést, amelyhez úgy lett hozzáadva.

7. János "Új támogatási kérés" a "Súgó és támogatás" panelről kattint, és a támogatási kérések hozhat létre a "Visual Studio Ultimate az MSDN"

    ![Új támogatási kérelem](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Kattintson a "Minden támogatási kérelem" János a listája látható az előfizetéshez létrehozott támogatási kérések ![eset Részletek nézet](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Távolítsa el a támogatási kérelem hozzáférés az Azure Portalon

Ugyanúgy, mint az is lehet hozzáférést biztosítani egy felhasználónak, a támogatási kérések létrehozásához és kezeléséhez, akkor lehet tiltani a hozzáférését, valamint a felhasználó.
Távolítsa el a létrehozása és a támogatási kérések kezelése, nyissa meg az előfizetés lehetővé teszi, kattintson a "Beállítások" parancsra, majd kattintson a felhasználó (Ez esetben Attilára).
Kattintson a jobb gombbal a szerepkör nevét, a "Támogatási kérelem közreműködő", és kattintson az "Eltávolítás" parancsra

![Támogatási kérelem hozzáférés eltávolítása](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

János jelentkezik be a portálra, és megpróbálja hozzon létre egy támogatási kérést, ha akkor fordul elő a következő hiba

![Hiba – 2 előfizetés](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

János nem lát nyomokat kérelmek támogatásához, ha "Minden támogatási kérelem" kattint

![eset részletes adatai megtekintése – 2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
