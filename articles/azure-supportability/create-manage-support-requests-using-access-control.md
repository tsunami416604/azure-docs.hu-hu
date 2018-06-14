---
title: Azure szerepköralapú hozzáférés-vezérlés (RBAC) való hozzáférési jogosultságainak vezérlése létrehozásához és kezeléséhez a támogatási kérelmek |} Microsoft Docs
description: Azure szerepköralapú hozzáférés-vezérlés (RBAC) való hozzáférési jogosultságainak vezérlése létrehozásához és kezeléséhez a támogatási kérelmek
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: c3557d54b9fb3fd0173eec1e327050ac7cc80fc2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31392901"
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Azure szerepköralapú hozzáférés-vezérlés (RBAC) való hozzáférési jogosultságainak vezérlése létrehozásához és kezeléséhez a támogatási kérelmek

[Szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) lehetővé teszi a részletes hozzáféréskezelést az Azure-bA.
Támogatja a kérelem létrehozását az Azure-portálon [portal.azure.com](https://portal.azure.com), határozza meg, akik hozhat létre és támogatási kérelmek kezelése az Azure RBAC-modellben használatával.
Hozzáférés a megfelelő RBAC szerepkört rendel a felhasználók, csoportok és alkalmazások egy bizonyos hatókörhöz, amely lehet egy előfizetést, erőforráscsoport vagy egy erőforrás.

Vegyünk egy példát: egy erőforrás csoport tulajdonosának olvasási engedély az előfizetés hatókörben, kezelheti az erőforráscsoportot, például webhelyekhez, virtuális gépek és alhálózatok tartozó összes erőforrást.
Azonban amikor megpróbál létrehozni egy támogatási kérést, szemben a virtuálisgép-erőforrást, tapasztal a következő hiba

![Előfizetés hiba](./media/create-manage-support-requests-using-access-control/subscription-error.png)

A támogatási kérelem beépülő modulban kell írási engedéllyel, vagy a támogatási művelet Microsoft.Support/* hozhatók létre és támogatási kérelmek kezelése az előfizetés hatókörben által betöltött szerepkör.

A következő cikk ismerteti, hogyan használható Azure egyéni szerepköralapú hozzáférés-vezérlést (RBAC) létrehozása és kezelése az Azure portálon támogatási kérelmeket.

## <a name="getting-started"></a>Első lépések

A fenti példa használ, akkor tudná egy támogatási kérést az erőforrás létrehozásához, ha a volt az előfizetésben egyedi RBAC szerepkört az előfizetés tulajdonosa.
[Egyéni RBAC-szerepkörök](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) Azure PowerShell, Azure parancssori felület (CLI) és a REST API használatával hozhatók létre.

Egy egyéni biztonsági szerepkört a műveletek tulajdonsága határozza meg, amelyhez a szerepkör hozzáférést biztosít az Azure műveletek.
A támogatási kérelem felügyeleti egyéni szerepkör létrehozása, a szerepkörnek rendelkeznie kell a művelet Microsoft.Support/*

Íme egy példa egy egyéni biztonsági szerepkört hozhat létre és kezelhet a támogatási kérelmek.
Jelenleg ez a szerepkör "Támogatási kérelem közreműködői" nevű, és hogyan az egyéni szerepkör ebben a cikkben hivatkozunk.

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

Című rész lépéseit követve [Ez a videó](https://www.youtube.com/watch?v=-PaBaDmfwKI) megtudhatja, hogyan hozzon létre egy egyéni biztonsági szerepkört az előfizetés.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Hozzon létre, és az Azure portálon támogatási kérelmek kezelése

Vegyünk egy példát – tulajdonosaként az előfizetés "Visual Studio MSDN-előfizetés."
Joe a partner, aki az erőforráscsoportok ebben az előfizetésben egyes erőforrás tulajdonosa és rendelkezik-e olvasási engedéllyel az előfizetéshez.
Zónázással biztosítson hozzáférést a társ, Joe, hozzon létre, és az erőforrások az előfizetéshez tartozó támogatási jegyek kezelése kívánja.

1. Az első lépés az, hogy nyissa meg az előfizetéshez, és a "Beállítások" felhasználók listájának megtekintéséhez. A felhasználó az előfizetéshez olvasási hozzáféréssel rendelkező Joe kattintson, és most egy új egyéni biztonsági szerepkört rendel neki.

    ![Szerepkör hozzáadása](./media/create-manage-support-requests-using-access-control/add-role.png)

2. Kattintson a "Hozzáadás" a "Felhasználók" panel alatt. Válassza ki az egyéni biztonsági szerepkört "Támogatási kérelem közreműködői" a szerepkörök listájáról

    ![Támogatási közreműködői szerepkör hozzáadása](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. Miután kiválasztotta a szerepkör nevét, a "Hozzáadás felhasználók", és adja meg a Joe e-mail hitelesítő adatait. Kattintson a "Select"

    ![Felhasználók hozzáadása](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Kattintson az "Ok" gombra. a folytatáshoz

    ![Hozzáférés hozzáadása](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Ekkor megjelenik-e a felhasználót az újonnan hozzáadott egyéni biztonsági szerepkört "Támogatási kérelem közreműködői", amelynek a tulajdonosa az előfizetésben

    ![A hozzáadott felhasználónak](./media/create-manage-support-requests-using-access-control/user-added.png)

    Amikor Joe bejelentkezik a portálra, az előfizetést, amelyhez ő hozzá látja.

7. Joe "Új támogatási kérelem" a "Súgó és támogatása" paneljéről kattint, és hozhat létre támogatási kérelmek "Visual Studio Ultimate az MSDN"

    ![Új támogatási kérelem](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Kattintson az "Összes kérelmek támogatásához" Joe a listája látható a támogatási kérelmek létrehozása ehhez az előfizetéshez ![eset Részletek nézet](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Távolítsa el a támogatási kérelem hozzáférés az Azure-portálon

Ugyanúgy, mint az is lehet, hogy hozzáférést biztosítson a felhasználó létrehozásához és kezeléséhez a támogatási kérelmek, lehetőség, valamint a felhasználó hozzáférést.
Hozzon létre és támogatási kérelmek kezelése, hogy az előfizetés olyan eltávolításához kattintson a "Beállítások", és kattintson a felhasználónak (jelen esetben Joe).
Kattintson a jobb gombbal a szerepkör nevét, a "Támogatási kérelem közreműködői", és kattintson az "Eltávolítás"

![Támogatási kérelem hozzáférés](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Ha Joe jelentkezik be a portálra, és hozzon létre egy támogatási kérést próbálkozik, ezután a következő hibát tapasztal

![Előfizetés hiba – 2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Joe nem jelennek meg kérelmek támogatásához, ha "Az összes kérelmek támogatásához" elemre kattint

![eset adatainak megtekintése – 2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
