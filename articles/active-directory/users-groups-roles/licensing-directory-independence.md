---
title: Több bérlői interakció jellemzői - Azure AD | Microsoft dokumentumok
description: Az Azure Active Directory-bérlők ismertetése teljesen független szervezetként
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/07/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 175d9ce7db1657e0e654f46adaf8a8d8ef28c25e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878119"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Ismerje meg, hogyan működik együtt több Azure Active Directory-szervezet

Az Azure Active Directoryban (Azure AD) minden bérlő egy teljesen független szervezet: egy társ, amely logikailag független a többi Azure AD-szervezetek, amelyek et kezel. A szervezetek közötti függetlenség magában foglalja az erőforrások függetlenségét, a felügyeleti függetlenséget és a szinkronizálás függetlenségét. Nincs szülő-gyermek kapcsolat a szervezetek között.

## <a name="resource-independence"></a>Erőforrás-függetlenség

* Ha létrehoz vagy töröl egy Azure AD-erőforrást az egyik szervezetben, az nincs hatással egy másik szervezet egyetlen erőforrására sem, a külső felhasználók részleges kivételével.
* Ha regisztrál egy tartománynevet egy szervezetnél, azt más szervezet nem használhatja.

## <a name="administrative-independence"></a>Közigazgatási függetlenség

Ha a "Contoso" szervezet nem rendszergazda felhasználója létrehoz egy "Teszt" tesztszervezetet, akkor:

* Alapértelmezés szerint a szervezetet létrehozó felhasználó külső felhasználóként kerül hozzáadásra az új szervezetben, és hozzárendeli a globális rendszergazdai szerepkört az adott szervezetben.
* A "Contoso" szervezet rendszergazdái nem rendelkeznek közvetlen rendszergazdai jogosultságokkal a "Test" szervezethez, kivéve, ha a "Test" rendszergazdája kifejezetten megadja nekik ezeket a jogosultságokat. A "Contoso" rendszergazdái azonban szabályozhatják a "Test" szervezethez való hozzáférést, ha ők irányítják a "Teszt" felhasználói fiókot.
* Ha egy szervezet ben lévő felhasználóhoz hozzáad vagy eltávolít egy Azure AD-szerepkört, a módosítás nincs hatással a felhasználó bármely más Azure AD-szervezetben hozzárendelt szerepköreire.

## <a name="synchronization-independence"></a>A szinkronizálás függetlensége

Az egyes Azure AD-szervezetek egymástól függetlenül konfigurálhatók az adatok egyetlen példányából történő szinkronizáláshoz:

* Az Azure AD Connect eszköz, az adatok szinkronizálásához egyetlen AD erdővel.
* Az Azure Active Directory-összekötő forefront Identity Manager, adatok szinkronizálására egy vagy több helyszíni erdők és/vagy nem Azure AD adatforrások.

## <a name="add-an-azure-ad-organization"></a>Azure AD-szervezet hozzáadása

Ha azure AD-szervezetet szeretne hozzáadni az Azure Portalon, jelentkezzen be [az Azure Portalra](https://portal.azure.com) egy Azure AD globális rendszergazdai fiókkal, és válassza az **Új**lehetőséget.

> [!NOTE]
> Más Azure-erőforrásokkal ellentétben az Azure AD-szervezetek nem azure-előfizetés gyermekerőforrásai. Ha az Azure-előfizetése levan mondva vagy lejárt, továbbra is hozzáférhet az Azure AD-szervezet adataihoz az Azure PowerShell, a Microsoft Graph API vagy a Microsoft 365 felügyeleti központ használatával. A [szervezethez másik előfizetést](../fundamentals/active-directory-how-subscriptions-associated-directory.md)is társíthat.
>

## <a name="next-steps"></a>További lépések

Az Azure AD licencelési szempontjait és gyakorlati tanácsait a [Mi az Azure Active Directory licencelése című témakörben.](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
