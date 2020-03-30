---
title: Alkalmazás & erőforrás-hozzáférés kezelése csoportok használatával – Azure AD
description: Megtudhatja, hogyan kezelheti a hozzáférést a szervezet felhőalapú alkalmazásaihoz, helyszíni alkalmazásaihoz és erőforrásaihoz az Azure Active Directory-csoportok használatával.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: ajburnle
ms.reviewer: piotrci
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 961444e15ae1c45db1fc7423a6ac3cc96cc7b3fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768010"
---
# <a name="manage-app-and-resource-access-using-azure-active-directory-groups"></a>Alkalmazás- és erőforrás-hozzáférés kezelése Azure Active Directory-csoportokkal
Az Azure Active Directory (Azure AD) segítségével csoportok használatával kezelheti a felhőalapú alkalmazásokhoz, a helyszíni alkalmazásokhoz és az erőforrásokhoz való hozzáférést. Az erőforrások az Azure AD-szervezet részét lehetnek, például az objektumok azure AD-n belüli szerepkörökön keresztüli vagy a szervezeten kívüli, például a Szoftver, szolgáltatás (SaaS) alkalmazások, az Azure-szolgáltatások, a SharePoint-webhelyek és a helyszíni erőforrások kezelésére vonatkozó engedélyeket.

>[!NOTE]
>Az Azure Active Directory használatához Azure-fiókra lesz szükség. Ha nincs fiókja, [regisztrálhat egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/).
>
> Az Azure Portalon láthatja azon csoportokat, amelyek tagságát és csoportadatait nem kezelheti a portálon:
>
> - A helyszíni Active Directoryból szinkronizált csoportok csak a helyszíni Active Directoryban kezelhetők.
> - Más csoporttípusokat, például a terjesztési listákat és a levelezést támogató biztonsági csoportokat csak az Exchange Felügyeleti központban vagy a Microsoft 365 Felügyeleti központban kezeli a rendszer. Ezeknek a csoportoknak a kezeléséhez be kell jelentkeznie az Exchange Felügyeleti központba vagy a Microsoft 365 Felügyeleti központba.

## <a name="how-access-management-in-azure-ad-works"></a>A hozzáférés-kezelés működése az Azure AD-ben

Az Azure AD segítségével hozzáférést biztosít a szervezet erőforrásaihoz azáltal, hogy hozzáférési jogokat biztosít egyetlen felhasználónak vagy egy teljes Azure AD-csoportnak. A csoportok használatával az erőforrás (vagy az Azure AD-címtár) tulajdonosa hozzáférési engedélyeket rendelhet a csoport minden tagjához ahelyett, hogy ezt egyesével kellene megtennie. Az erőforrás vagy a címtár tulajdonosa rendszergazdai jogokat is adhat a taglistához valaki másnak, például egy részlegvezetőnek vagy egy ügyfélszolgálati rendszergazdának, lehetővé téve az adott személy számára, hogy szükség szerint tagokat vegyen fel és távolítson el. A csoporttulajdonosok kezeléséről a [Csoporttulajdonosok kezelése című](active-directory-accessmanagement-managing-group-owners.md) témakörben talál további információt.

![Ábra: Az Azure Active Directory hozzáférés-kezelése](./media/active-directory-manage-groups/active-directory-access-management-works.png)

## <a name="ways-to-assign-access-rights"></a>A hozzáférési jogok hozzárendelésének módjai

Az erőforrás-hozzáférési jogokat négyféleképpen rendelheti hozzá a felhasználókhoz:

- **Közvetlen megbízás.** Az erőforrás tulajdonosa közvetlenül hozzárendeli a felhasználót az erőforráshoz.

- **Csoportos hozzárendelés.** Az erőforrás tulajdonosa hozzárendel egy Azure AD-csoportot az erőforráshoz, amely automatikusan hozzáférést biztosít a csoport összes tagjának az erőforráshoz. A csoporttagságot a csoport tulajdonosa és az erőforrás tulajdonosa is kezeli, így a tulajdonos tagokat vehet fel vagy távolíthat el a csoportból. A csoporttagság hozzáadásáról és eltávolításáról további információt a [Csoporthozzáadásak szolgáltatásból](active-directory-groups-membership-azure-portal.md)a Csoport hozzáadása vagy eltávolítása az Azure Active Directory portálon című témakörben talál. 

- **Szabályalapú hozzárendelés.** Az erőforrás tulajdonosa létrehoz egy csoportot, és egy szabály segítségével határozza meg, hogy mely felhasználók vannak hozzárendelve egy adott erőforráshoz. A szabály az egyes felhasználókhoz rendelt attribútumokon alapul. Az erőforrás tulajdonosa kezeli a szabályt, és meghatározza, hogy mely attribútumok és értékek szükségesek az erőforrás eléréséhez. További információ: [Dinamikus csoport létrehozása és állapot ellenőrzése.](../users-groups-roles/groups-create-rule.md)

    Ezt a rövid videót a dinamikus csoportok létrehozásáról és használatáról szóló rövid magyarázattal is megtekintheti:

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]

- **Külső hatósági megbízás.** A hozzáférés külső forrásból, például egy helyszíni címtárból vagy egy SaaS-alkalmazásból származik. Ebben az esetben az erőforrás tulajdonosa hozzárendel egy csoportot, hogy hozzáférést biztosítson az erőforráshoz, majd a külső forrás kezeli a csoport tagokat.

   ![Ábra: A hozzáférés-kezelés áttekintése](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="can-users-join-groups-without-being-assigned"></a>Csatlakozhatnak a felhasználók csoportokhoz hozzárendelés nélkül?
A csoport tulajdonosa lehetővé teheti a felhasználók számára, hogy saját csoportokat találjanak, amelyekhez csatlakozhatnak, ahelyett, hogy hozzárendelenék őket. A tulajdonos beállíthatja a csoportot úgy is, hogy automatikusan fogadja az összes csatlakozott felhasználót, vagy jóváhagyást igényel.

Miután egy felhasználó kéri, hogy csatlakozzon egy csoporthoz, a kérelem továbbításra kerül a csoport tulajdonosának. Ha szükséges, a tulajdonos jóváhagyhatja a kérelmet, és a felhasználó értesítést kap a csoporttagságról. Ha azonban több tulajdonosa van, és egyikük nem hagyja jóvá, a felhasználó értesítést kap, de nem kerül hozzáadásra a csoporthoz. További információ és útmutatás arról, hogy miként engedheti meg a felhasználóknak a csoportokhoz való csatlakozást, olvassa [el az Azure AD beállítása, hogy a felhasználók csoportokhoz való csatlakozást kérhessenek](../users-groups-roles/groups-self-service-management.md)

## <a name="next-steps"></a>További lépések
Most, hogy már bemutat egy bevezetést a csoportok használatával történő hozzáférés-kezeléshez, elkezdi kezelni az erőforrásokat és az alkalmazásokat.

- [Új csoport létrehozása az Azure Active Directory használatával,](active-directory-groups-create-azure-portal.md) vagy [új csoport létrehozása és kezelése PowerShell-parancsmagokkal](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Csoportok használata hozzáférés hozzárendelése integrált SaaS-alkalmazáshoz](../users-groups-roles/groups-saasapps.md)

- [Helyszíni csoport szinkronizálása az Azure-ral az Azure AD Connect használatával](../hybrid/whatis-hybrid-identity.md)
