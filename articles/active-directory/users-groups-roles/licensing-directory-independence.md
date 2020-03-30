---
title: Több bérlői interakció jellemzői - Azure AD | Microsoft dokumentumok
description: Az Azure Active bérlőinek kezelése a bérlők teljesen független erőforrásokként való megértésével
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562258"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Ismerje meg, hogyan működik együtt több Azure Active Directory-bérlő

Az Azure Active Directoryban (Azure AD) minden bérlő egy teljesen független erőforrás: egy társ, amely logikailag független a többi bérlő, amely a kezelt. A bérlők között nincs szülő-gyermek kapcsolat. A bérlők közötti függetlenség magában foglalja az erőforrások függetlenségét, a felügyeleti függetlenséget és a szinkronizálás függetlenségét.

## <a name="resource-independence"></a>Erőforrás-függetlenség
* Ha létrehoz vagy töröl egy erőforrást az egyik bérlőben, az nincs hatással egy másik bérlő egyetlen erőforrására sem, a külső felhasználók részleges kivételével. 
* Ha az egyik tartománynevet egy bérlővel használja, nem használható más bérlővel.

## <a name="administrative-independence"></a>Közigazgatási függetlenség
Ha a "Contoso" bérlő nem rendszergazda iadott felhasználója "Teszt" tesztbérlőt hoz létre, akkor:

* Alapértelmezés szerint a bérlőt létrehozó felhasználó külső felhasználóként kerül hozzáadásra az új bérlőhöz, és hozzárendeli a globális rendszergazdai szerepkört az adott bérlőhöz.
* A "Contoso" bérlő rendszergazdái nem rendelkeznek közvetlen rendszergazdai jogosultságokkal a "Test" bérlőhöz, kivéve, ha a "Test" rendszergazdája kifejezetten megadja nekik ezeket a jogosultságokat. A "Contoso" rendszergazdái azonban szabályozhatják a "Test" bérlőhöz való hozzáférést, ha ők szabályozzák a "Teszt" felhasználói fiókot.
* Ha egy bérlő ben hozzáad/eltávolít egy rendszergazdai szerepkört egy felhasználóhoz, a módosítás nincs hatással a felhasználó egy másik bérlőben lévő rendszergazdai szerepköreire.

## <a name="synchronization-independence"></a>A szinkronizálás függetlensége
Az egyes Azure AD-bérlők egymástól függetlenül konfigurálhatók, hogy az adatok szinkronizálása a következők egyetlen példányából származik:

* Az Azure AD Connect eszköz, az adatok szinkronizálásához egyetlen AD erdővel.
* Az Azure Aktív bérlői összekötő forefront Identity Manager, adatok szinkronizálására egy vagy több helyszíni erdők és/vagy nem Azure AD adatforrások.

## <a name="add-an-azure-ad-tenant"></a>Azure AD-bérlő hozzáadása
Ha azure AD-bérlőt szeretne hozzáadni az Azure Portalon, jelentkezzen be [az Azure Portalra](https://portal.azure.com) egy Azure AD globális rendszergazdai fiókkal, és a bal oldalon válassza az **Új**lehetőséget.

> [!NOTE]
> Más Azure-erőforrásokkal ellentétben a bérlők nem azure-előfizetés gyermekerőforrásai. Ha az Azure-előfizetése levan mondva vagy lejárt, továbbra is hozzáférhet a bérlői adatokhoz az Azure PowerShell, a Microsoft Graph API vagy a Microsoft 365 felügyeleti központ használatával. A [bérlőhöz egy másik előfizetést](../fundamentals/active-directory-how-subscriptions-associated-directory.md)is társíthat.
>

## <a name="next-steps"></a>További lépések
Az Azure AD licencelési problémáinak és ajánlott eljárásainak átfogó áttekintését a [Mi az Azure Active bérlői licencelése.](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
