---
title: Több bérlői interakció jellemzői – Azure AD | Microsoft Docs
description: Az Azure aktív bérlői bérlők kezelése a bérlők teljes mértékben független erőforrásokkal való megismerésével
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4eb09ab7fa31af5edf14b113a6a88e08df2d115
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562258"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>A Azure Active Directory-bérlők interakciójának megismerése

Azure Active Directory (Azure AD) esetében minden bérlő egy teljesen független erőforrás: olyan társ, amely logikailag független a többi, Ön által kezelt bérlőtől. Nincs szülő-gyermek kapcsolat a bérlők között. A bérlők közötti függetlenség magában foglalja az erőforrások függetlenségét, a felügyelet függetlenségét és a szinkronizálás függetlenségét.

## <a name="resource-independence"></a>Erőforrás-függetlenség
* Ha egy bérlőben hoz létre vagy töröl egy erőforrást, az nem érinti a többi bérlő erőforrásait, a külső felhasználók részleges kivételével. 
* Ha az egyik tartománynevet egy Bérlővel használja, nem használható más Bérlővel.

## <a name="administrative-independence"></a>Rendszergazdai függetlenség
Ha a "contoso" bérlő nem rendszergazda felhasználója létrehoz egy teszt bérlői tesztet, akkor:

* Alapértelmezés szerint a bérlőt létrehozó felhasználó külső felhasználóként lesz hozzáadva az új bérlőhöz, és a globális rendszergazdai szerepkört rendelte hozzá a bérlőhöz.
* A "contoso" bérlő rendszergazdái nem rendelkeznek közvetlen rendszergazdai jogosultsággal a bérlői teszthez, kivéve, ha a "test" rendszergazdája kifejezetten megadja nekik ezeket a jogosultságokat. A "contoso" rendszergazdái azonban szabályozhatják a "teszt" bérlőhöz való hozzáférést, ha a "test" nevű felhasználói fiókot vezérlik.
* Ha egy bérlőben egy felhasználóhoz hozzáad vagy eltávolít egy rendszergazdai szerepkört, a módosítás nem érinti a felhasználó olyan rendszergazdai szerepköreit, amelyek egy másik bérlőn vannak.

## <a name="synchronization-independence"></a>Szinkronizálási függetlenség
Az egyes Azure AD-bérlők egymástól függetlenül konfigurálhatók úgy, hogy az adatokat a következők egyetlen példányáról szinkronizálják:

* Az Azure AD Connect eszköz egyetlen AD-erdővel történő szinkronizáláshoz.
* A Forefront Identity Managerhez készült Azure Active bérlői összekötő, amely egy vagy több helyszíni erdővel és/vagy nem Azure AD-adatforrással szinkronizálja az adatokat.

## <a name="add-an-azure-ad-tenant"></a>Azure AD-bérlő hozzáadása
Ha Azure AD-bérlőt szeretne felvenni a Azure Portalba, jelentkezzen be [a Azure Portalba](https://portal.azure.com) egy olyan fiókkal, amely az Azure ad globális rendszergazdája, és a bal oldalon válassza az **új**lehetőséget.

> [!NOTE]
> A többi Azure-erőforrástól eltérően a bérlők nem az Azure-előfizetések alárendelt erőforrásai. Ha az Azure-előfizetését megszakították vagy lejártak, a bérlői adatait Azure PowerShell, a Microsoft Graph API-val vagy a Microsoft 365 felügyeleti központtal is elérheti. [Egy másik előfizetést is társíthat a bérlőhöz](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>További lépések
Az Azure AD licencelési problémáinak és az ajánlott eljárások átfogó áttekintését lásd: [Mi az az Azure Active bérlői licencelés?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
