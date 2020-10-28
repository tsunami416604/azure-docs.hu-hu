---
title: Szerepköralapú hozzáférés biztosítása a felhasználók számára
titleSuffix: Azure Maps
description: Szerepköralapú hozzáférés-vezérlés használata a felhasználók engedélyezésének megadásához Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 19806fe24d0ff3b87ebe61b45ac302947c734fa0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895568"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Szerepköralapú hozzáférés biztosítása a felhasználók számára a Azure Maps

Azure *szerepköralapú hozzáférés-vezérlést (Azure RBAC)* biztosít egy Azure ad-csoport vagy rendszerbiztonsági tag egy vagy több Azure Maps szerepkör-definícióhoz való hozzárendelésével. Az Azure Maps számára elérhető Azure-szerepkör-definíciók megtekintéséhez lépjen a **hozzáférés-vezérlés (iam)** elemre. Válassza ki a **szerepkörök** elemet, majd keressen rá a *Azure Maps* kezdetű szerepkörökre.

* Az [Azure ad-csoportokkal](../../active-directory/fundamentals/active-directory-manage-groups.md)hatékonyan kezelhet nagy mennyiségű felhasználói hozzáférést Azure Mapshoz.
* Ahhoz, hogy a felhasználók hitelesíteni tudják az alkalmazást, a felhasználókat létre kell hozni az Azure AD-ben. Lásd: [felhasználók hozzáadása vagy törlése az Azure ad-](../../active-directory/fundamentals/add-users-azure-active-directory.md)vel.

További információk az [Azure ad](../../active-directory/fundamentals/index.yml) -ben a felhasználók címtárának hatékony kezeléséhez.

1. Nyissa meg **Azure Maps-fiókját** . Válassza a **hozzáférés-vezérlés (iam)**  >  **szerepkör-hozzárendelés** elemet.

    ![Hozzáférés biztosítása az Azure RBAC](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. A **szerepkör-hozzárendelések** lap **szerepkör** területén válassza ki a beépített Azure Maps szerepkör-definíciót, például **Azure Maps Adatolvasó** vagy **Azure Maps adatközreműködő** . A **hozzáférés társítása** területen válassza az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév** lehetőséget. Válassza ki a résztvevőt név alapján. Kattintson a **Mentés** gombra.

   * A [szerepkör-hozzárendelések hozzáadása vagy eltávolítása](../../role-based-access-control/role-assignments-portal.md)című témakörben talál további információt.

> [!WARNING]
> Azure Maps beépített szerepkör-definíciók nagyon nagy engedélyezési hozzáférést biztosítanak számos Azure Maps REST API-hoz. Ha minimálisra szeretné korlátozni az API-kat, tekintse meg az [Egyéni szerepkör-definíció létrehozása és a felhasználók társítása](../../role-based-access-control/custom-roles.md) az egyéni szerepkör-definícióhoz című témakört. Ez lehetővé teszi a felhasználók számára, hogy az alkalmazáshoz szükséges legalacsonyabb jogosultsággal rendelkezzenek.