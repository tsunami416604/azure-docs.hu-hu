---
title: Hozzáférés-vezérlés a támogatási kérések létrehozásához az Azure-ban
description: Azure szerepköralapú Access Control (RBAC) a támogatási kérések létrehozásához és kezeléséhez szükséges hozzáférési jogosultságok szabályozásához
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: azure-supportability
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 328f4928aff6a892849ce3e08fe794578b5e03a4
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299256"
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Azure szerepköralapú Access Control (RBAC) a támogatási kérések létrehozásához és kezeléséhez szükséges hozzáférési jogosultságok szabályozásához

> [!IMPORTANT]
> Ezt a manuális megközelítést, amely az Azure-beli támogatási kérelem hozzáférési jogosultságait szabályozza, egy beépített szerepkör váltotta fel. További információ: [support Request közreműködő](../role-based-access-control/built-in-roles.md#support-request-contributor). 

A [szerepköralapú Access Control (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) lehetővé teszi a részletes hozzáférés-kezelést az Azure-hoz.
Support request létrehozása a [Portal.Azure.com](https://portal.azure.com)-ben, Azure Portal az Azure RBAC-modelljét használja annak meghatározására, hogy kik hozhatnak létre és kezelhetnek támogatási kérelmeket.
A hozzáféréshez a megfelelő RBAC-szerepkört kell hozzárendelni a felhasználókhoz, csoportokhoz és alkalmazásokhoz egy bizonyos hatókörben, amely lehet előfizetés, erőforráscsoport vagy erőforrás.

Tegyük fel például, hogy az erőforráscsoport tulajdonosa olvasási engedéllyel rendelkezik az előfizetés hatókörében, kezelheti az erőforráscsoport alatti összes erőforrást, például a webhelyeket, a virtuális gépeket és az alhálózatokat.
Ha azonban egy támogatási kérést próbál létrehozni a virtuális gép erőforrásával kapcsolatban, a következő hibaüzenet jelenik meg

![Előfizetés-hiba](./media/create-manage-support-requests-using-access-control/subscription-error.png)

A támogatási kérelmek kezelése során írási engedéllyel vagy olyan szerepkörrel kell rendelkeznie, amely támogatja a Microsoft. support/* támogatását az előfizetés hatókörében, hogy képes legyen a támogatási kérések létrehozására és kezelésére.

A következő cikk azt ismerteti, hogyan használható az Azure egyéni szerepköralapú Access Control (RBAC) a támogatási kérések létrehozásához és kezeléséhez a Azure Portalban.

## <a name="getting-started"></a>Első lépések

A fenti példa használatával létrehozhat egy támogatási kérést az erőforráshoz, ha az előfizetés tulajdonosa egyéni RBAC-szerepkört rendelt az előfizetéshez.
Az [Egyéni RBAC szerepkörök](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) a Azure PowerShell, az Azure parancssori felület (CLI) és a REST API használatával hozhatók létre.

Az egyéni szerepkör Actions tulajdonsága határozza meg azokat az Azure-műveleteket, amelyekhez a szerepkör hozzáférést biztosít.
Ha egyéni szerepkört szeretne létrehozni a támogatási kérelmek kezeléséhez, a szerepkörnek rendelkeznie kell a Microsoft. support/* művelettel.

Az alábbi példa egy olyan egyéni szerepkört mutat be, amely támogatási kérések létrehozásához és kezeléséhez használható.
Ennek a szerepkörnek a neve "támogatási kérelem közreműködője", és ez a cikk az egyéni szerepkörre hivatkozik.

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

Kövesse az [ebben a videóban](https://www.youtube.com/watch?v=-PaBaDmfwKI) ismertetett lépéseket, amelyből megtudhatja, hogyan hozhat létre egyéni szerepkört az előfizetéséhez.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Támogatási kérelmek létrehozása és kezelése a Azure Portal

Vegyük például a következőt: a Visual Studio MSDN-előfizetés tulajdonosa.
Joe az a társa, aki egy erőforrás-tulajdonos az előfizetésben található egyes erőforráscsoportok számára, és olvasási engedéllyel rendelkezik az előfizetéshez.
A partnernek, Joe-nak hozzáférést kíván adni, és támogatási jegyet hozhat létre és kezelhet az előfizetéshez tartozó erőforrásokhoz.

1. Első lépésként nyissa meg az előfizetést. A **Beállítások**területen láthatja a felhasználók listáját. Válassza ki azt a felhasználót, aki olvasói hozzáféréssel rendelkezik az előfizetéshez. Rendeljen hozzá egy új egyéni szerepkört a Joe-hoz.

    ![Szerepkör hozzáadása](./media/create-manage-support-requests-using-access-control/add-role.png)

2. Kattintson a "Hozzáadás" gombra a "felhasználók" panelen. Válassza ki a szerepkörök listájából a "támogatási kérelem közreműködője" egyéni szerepkört.

    ![Támogatási közreműködő szerepkör hozzáadása](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. A szerepkör nevének kiválasztása után kattintson a "felhasználók hozzáadása" lehetőségre, és adja meg a Joe e-mail hitelesítő adatait. Kattintson a Kiválasztás gombra.

    ![Felhasználók felvétele](./media/create-manage-support-requests-using-access-control/add-users.png)

4. A folytatáshoz kattintson az OK gombra.

    ![Hozzáférés hozzáadása](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Most megjelenik a "támogatási kérelem közreműködője" nevű újonnan hozzáadott egyéni szerepkörrel rendelkező felhasználó az előfizetés alatt, amelynek Ön a tulajdonosa

    ![Felhasználó hozzáadva](./media/create-manage-support-requests-using-access-control/user-added.png)

    Amikor Joe bejelentkezik a portálra, Joe látja azt az előfizetést, amelyhez Joe hozzá lett adva.

7. János a "Súgó és támogatás" panel "új Support request" gombjára kattint, és a "Visual Studio Ultimate MSDN-nel" támogatási kérelmeit is létrehozhatja

    ![Új támogatási kérelem](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. A "minden támogatási kérelem" gombra kattintva megtekintheti az előfizetéshez létrehozott támogatási kérelmek listáját ![a Case details nézetet](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Támogatási kérelem hozzáférésének eltávolítása a Azure Portal

Ugyanúgy, ahogy a felhasználók számára engedélyezhető a támogatási kérések létrehozása és kezelése, a felhasználó hozzáférését is el lehet távolítani.

A támogatási kérések létrehozásához és kezeléséhez nyissa meg az előfizetést, kattintson a "beállítások" elemre, és kattintson a felhasználóra (ebben az esetben Joe). Kattintson a jobb gombbal a szerepkör nevére, a "támogatási kérelem közreműködői" elemre, majd kattintson az Eltávolítás gombra.

![Támogatási kérelem hozzáférésének eltávolítása](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Amikor Joe bejelentkezik a portálra, és megpróbál létrehozni egy támogatási kérést, Joe a következő hibába ütközik:

![Előfizetés-hiba – 2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

János nem látja a támogatási kérelmeket, amikor Joe kiválasztja az "összes támogatási kérést"

![eset részleteinek nézete – 2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
