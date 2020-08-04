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
ms.openlocfilehash: 8dfc3714362b082168c32ba73e234e9b5fb43525
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87545110"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Szerepköralapú hozzáférés biztosítása a felhasználók számára a Azure Maps

*Szerepköralapú hozzáférés-vezérlést* (RBAC) biztosít egy Azure ad-csoport vagy rendszerbiztonsági tag egy vagy több Azure Maps hozzáférés-vezérlési szerepkör-definícióhoz való hozzárendelésével. Az Azure Maps számára elérhető Azure-szerepkör-definíciók megtekintéséhez lépjen a **hozzáférés-vezérlés (iam)** elemre. Válassza ki a **szerepkörök**elemet, majd keressen rá a *Azure Maps*kezdetű szerepkörökre.

* Az [Azure ad-csoportokkal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)hatékonyan kezelhet nagy mennyiségű felhasználói hozzáférést Azure Mapshoz.
* Ahhoz, hogy a felhasználók hitelesíteni tudják az alkalmazást, a felhasználókat létre kell hozni az Azure AD-ben. Lásd: [felhasználók hozzáadása vagy törlése az Azure ad-](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory)vel.

További információk az [Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/) -ben a felhasználók címtárának hatékony kezeléséhez.

1. Nyissa meg **Azure Maps-fiókját**. Válassza a **hozzáférés-vezérlés (iam)**  >  **szerepkör-hozzárendelés**elemet.

    ![RBAC megadása](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. A **szerepkör-hozzárendelések** lap **szerepkör**területén válassza ki a beépített Azure Maps szerepkör-definíciót, például **Azure Maps Adatolvasó** vagy **Azure Maps adatközreműködő**. A **hozzáférés társítása**területen válassza az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév**lehetőséget. Válassza ki a résztvevőt név alapján. Ez után válassza a **Mentés** lehetőséget.

   * A [szerepkör-hozzárendelések hozzáadása vagy eltávolítása](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)című témakörben talál további információt.

> [!WARNING]
> Azure Maps beépített szerepkör-definíciók nagyon nagy engedélyezési hozzáférést biztosítanak számos Azure Maps REST API-hoz. Ha minimálisra szeretné korlátozni az API-kat, tekintse meg az [Egyéni szerepkör-definíció létrehozása és a felhasználók társítása](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) az egyéni szerepkör-definícióhoz című témakört. Ez lehetővé teszi a felhasználók számára, hogy az alkalmazáshoz szükséges legalacsonyabb jogosultsággal rendelkezzenek.