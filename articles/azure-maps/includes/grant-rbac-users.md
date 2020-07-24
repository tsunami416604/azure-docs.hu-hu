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
ms.openlocfilehash: 2b138dab2a97537a93b8d873f79b6ee9c00b4af4
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87126745"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Szerepköralapú hozzáférés biztosítása a felhasználók számára a Azure Maps

*Szerepköralapú hozzáférés-vezérlést* (RBAC) biztosít egy Azure ad-csoport vagy rendszerbiztonsági tag egy vagy több Azure Maps hozzáférés-vezérlési szerepkör-definícióhoz való hozzárendelésével. Azure Maps számára elérhető RBAC-szerepkör-definíciók megtekintéséhez lépjen a **hozzáférés-vezérlés (iam)** elemre. Válassza ki a **szerepkörök**elemet, majd keressen rá a *Azure Maps*kezdetű szerepkörökre.

* Az [Azure ad-csoportokkal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)hatékonyan kezelhet nagy mennyiségű felhasználói hozzáférést Azure Mapshoz.
* Ahhoz, hogy a felhasználók hitelesíteni tudják az alkalmazást, a felhasználókat létre kell hozni az Azure AD-ben. Lásd: [felhasználók hozzáadása vagy törlése az Azure ad-](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory)vel.

További információk az [Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/) -ben a felhasználók címtárának hatékony kezeléséhez.

1. Nyissa meg **Azure Maps-fiókját**. Válassza a **hozzáférés-vezérlés (iam)**  >  **szerepkör-hozzárendelés**elemet.

    ![RBAC megadása](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. A **szerepkör-hozzárendelések** lap **szerepkör**területén válassza ki a beépített Azure Maps szerepkör-definíciót, például **Azure Maps Adatolvasó** vagy **Azure Maps adatközreműködő**. A **hozzáférés társítása**területen válassza az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév**lehetőséget. Válassza ki a résztvevőt név alapján. Ez után válassza a **Mentés** lehetőséget.

   * A [szerepkör-hozzárendelések hozzáadása vagy eltávolítása](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)című témakörben talál további információt.

> [!WARNING]
> Azure Maps beépített szerepkör-definíciók nagyon nagy engedélyezési hozzáférést biztosítanak számos Azure Maps REST API-hoz. Ha minimálisra szeretné korlátozni az API-kat, tekintse meg az [Egyéni szerepkör-definíció létrehozása és a felhasználók társítása](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) az egyéni szerepkör-definícióhoz című témakört. Ez lehetővé teszi a felhasználók számára, hogy az alkalmazáshoz szükséges legalacsonyabb jogosultsággal rendelkezzenek.