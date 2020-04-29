---
title: Felügyeleti egységek kezelése (előzetes verzió) – Azure AD | Microsoft Docs
description: Felügyeleti egységek használata az engedélyek részletesebb delegálásához Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12101b170066bc52e75f4aa281c6f1c21283c004
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406471"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Felügyeleti egységek felügyelete Azure Active Directoryban (előzetes verzió)

Ez a cikk a Azure Active Directory (Azure AD) felügyeleti egységeit ismerteti. A felügyeleti egység olyan Azure AD-erőforrás, amely más Azure AD-erőforrások tárolója lehet. Ebben az előzetes kiadásban egy felügyeleti egység csak felhasználókat és csoportokat tartalmazhat.

A felügyeleti egységek lehetővé teszik olyan rendszergazdai engedélyek megadását, amelyek az Ön által meghatározott szervezeti egységre, régióra vagy más szegmensre korlátozódnak. A felügyeleti egységekkel engedélyeket delegálhat a regionális rendszergazdáknak, vagy megadhatja a szabályzatot egy részletességi szinten. A felhasználói fiók rendszergazdája például frissítheti a profil adatait, alaphelyzetbe állíthatja a jelszavakat, és hozzárendelheti a licenceket a felhasználók számára a felügyeleti egységben.

 A regionális támogatási szakemberek számára való delegálás például az [ügyfélszolgálati rendszergazda](directory-assign-admin-roles.md#helpdesk-administrator) szerepkör, amely csak az általuk támogatott régióban lévő felhasználók kezelésére korlátozódik.

## <a name="deployment-scenario"></a>Központi telepítési forgatókönyv

A felügyeleti hatókör felügyeleti egységekkel történő korlátozása hasznos lehet olyan szervezeteknél, amelyek bármilyen típusú független részlegből állnak. Vegyünk például egy olyan nagy egyetemet, amely számos autonóm iskola (üzleti iskola, mérnöki iskola stb.) tagjai, akik a hozzáférést, a felhasználók felügyeletét és a szabályzatok beállítását végzik. A központi rendszergazda:

- Rendszergazdai engedélyekkel rendelkező szerepkör létrehozása csak az Azure AD-felhasználók számára a Business School felügyeleti egységben
- Felügyeleti egység létrehozása a vállalati iskola számára
- Töltse fel a felügyeleti egységet kizárólag az üzleti tanulók és a munkatársak számára
- Adja hozzá az üzleti iskola informatikai csapatát a szerepkörhöz a hatókörük szerint

## <a name="license-requirements"></a>Licenckövetelmények

A felügyeleti egységek használatához prémium szintű Azure Active Directory licencre van szükség minden felügyeleti egység rendszergazdájához, és ingyenes Azure Active Directory licenceket a felügyeleti egység tagjai számára. További információ: [Bevezetés a prémium szintű Azure ad](../fundamentals/active-directory-get-started-premium.md)használatába.

## <a name="manage-administrative-units"></a>Felügyeleti egységek kezelése

Ebben az előzetes kiadásban a felügyeleti egységeket a Azure Portal, a PowerShell-parancsmagok és a parancsfájlok, vagy a Microsoft Graph használatával kezelheti. A részletekért tekintse meg a dokumentációt:

- [Szerepkörök létrehozása, eltávolítása, feltöltése és hozzáadása felügyeleti egységekhez](roles-admin-units-manage.md): az eljárások befejezése
- Rendszergazdai [egységek](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)használata: a felügyeleti egységekkel való együttműködés a PowerShell használatával
- [Felügyeleti egység Graph-támogatás](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta): Részletes dokumentáció a Microsoft Graph felügyeleti egységekhez.

### <a name="planning-your-administrative-units"></a>A felügyeleti egységek megtervezése

A felügyeleti egységek az Azure AD-erőforrások logikai csoportosítására használhatók. Egy olyan szervezet esetében például, amelynek az IT-részlege globálisan elszórtan van, érdemes lehet olyan felügyeleti egységeket létrehozni, amelyek meghatározzák ezeket a földrajzi határokat. Egy másik forgatókönyvben, amelyben a többnemzetiségű szervezetek különböző "alszervezetekkel" rendelkeznek, amelyek a műveletek részben autonómok, minden egyes alszervezetet egy felügyeleti egység képvisel.

A felügyeleti egységek létrehozási feltételeit a szervezet egyedi követelményei vezérlik. A felügyeleti egységek közösen határozzák meg a szerkezetet a M365-szolgáltatások között. Javasoljuk, hogy a felügyeleti egységeket a M365-szolgáltatásokban való használatuk szem előtt tartásával készítse elő. A felügyeleti egységek maximális értékét lekérheti, ha közös erőforrásokat szeretne hozzárendelni a M365 a felügyeleti egység alatt.

A szervezeten belüli felügyeleti egységek létrehozását várhatóan a következő szakaszokon keresztül érheti el:

1. Kezdeti elfogadás: a szervezet a kezdeti feltételek alapján kezdi meg a felügyeleti egységek létrehozását, és a felügyeleti egységek száma növekedni fog, ahogy a feltételek finomítva vannak.
1. Metszés: Ha a feltételek jól definiálva vannak, a már nem szükséges felügyeleti egységeket a rendszer törli.
1. Stabilizáció: a szervezeti struktúra megfelelően van meghatározva, és a felügyeleti egységek száma nem változik jelentősen a rövid időtartamok alatt.

## <a name="currently-supported-scenarios"></a>Jelenleg támogatott forgatókönyvek

A globális rendszergazdák vagy a Kiemelt szerepkörű rendszergazdák az Azure AD-portál használatával felügyeleti egységeket hozhatnak létre, felhasználókat adhatnak hozzá a felügyeleti egységekhez, majd az informatikai munkatársakat a felügyeleti egység hatókörű rendszergazdai szerepköreihez rendelheti hozzá. A felügyeleti egység – a hatókörrel rendelkező rendszergazdák ezt követően az Office 365 portál használatával kezelhetik a felügyeleti egységekben lévő felhasználók alapszintű felügyeletét.

Emellett a csoportok a felügyeleti egység tagjaiként is hozzáadhatók, és a felügyeleti egység hatókörű csoport rendszergazdája a PowerShell, a Microsoft Graph és az Azure AD-portál használatával is kezelheti őket.

Az alábbi táblázat a felügyeleti egységek aktuális támogatását ismerteti.

### <a name="administrative-unit-management"></a>Felügyeleti egység kezelése

Engedélyek |   MS Graph/PowerShell   | Azure AD-portál | Microsoft 365 felügyeleti központ
----------- | ----------------------- | --------------- | -----------------
Felügyeleti egységek létrehozása és törlése   |    Támogatott    |   Támogatott   |    Nem támogatott
Felügyeleti egység tagjainak egyenkénti hozzáadása és eltávolítása    |   Támogatott    |   Támogatott   |    Nem támogatott
Felügyeleti egység tagjainak tömeges hozzáadása és eltávolítása. csv-fájl használatával   |    Nem támogatott     |  Támogatott   |    Nincs támogatási csomag
Felügyeleti egység kiosztása – hatókörrel rendelkező rendszergazdák  |     Támogatott    |   Támogatott    |   Nem támogatott
Az AU-tagok dinamikusan való hozzáadása és eltávolítása attribútumok alapján | Nem támogatott | Nem támogatott | Nem támogatott

### <a name="user-management"></a>Felhasználókezelés

Engedélyek |   MS Graph/PowerShell   | Azure AD-portál | Microsoft 365 felügyeleti központ
----------- | ----------------------- | --------------- | -----------------
felügyeleti egység – a felhasználói tulajdonságok, jelszavak és licencek hatókörön belüli kezelése   |    Támogatott     |  Támogatott   |   Támogatott
felügyeleti egység – a felhasználói bejelentkezések hatókörének blokkolása és feloldása    |   Támogatott   |    Támogatott   |    Támogatott
felügyeleti egység – felhasználói MFA hitelesítő adatainak hatókörön belüli felügyelete   |    Támogatott   |   Támogatott   |   Nem támogatott

### <a name="group-management"></a>Csoportkezelés

Engedélyek |   MS Graph/PowerShell   | Azure AD-portál | Microsoft 365 felügyeleti központ
----------- | ----------------------- | --------------- | -----------------
felügyeleti egység – a csoport tulajdonságainak és tagjainak hatókörön belüli kezelése     |  Támogatott   |    Támogatott    |  Nem támogatott
felügyeleti egység – a csoportos licencelés hatókörön belüli kezelése   |    Támogatott  |    Támogatott   |   Nem támogatott

> [!NOTE]
>
> A felügyeleti egység hatókörrel rendelkező rendszergazdák nem kezelhetik a dinamikus csoporttagság szabályait.

A felügyeleti egységek hatóköre csak a felügyeleti engedélyekre vonatkozik. Nem akadályozzák meg, hogy a tagok vagy a rendszergazdák az [alapértelmezett felhasználói engedélyeiket](../fundamentals/users-default-permissions.md) használják a felügyeleti egységen kívüli más felhasználók, csoportok vagy erőforrások tallózására. Az Office 365-portálon a hatókörön kívüli rendszergazda felügyeleti egységeken kívüli felhasználók is kiszűrve vannak, de az Azure AD-portálon, a PowerShellben és más Microsoft-szolgáltatásokban is böngészhet más felhasználók között.

## <a name="next-steps"></a>További lépések

- [Az AUs kezelése](roles-admin-units-manage.md)
- [Felhasználók kezelése az AUs-ban](roles-admin-units-add-manage-users.md)
- [Csoportok kezelése az AUs-ban](roles-admin-units-add-manage-groups.md)
- [Hatókörrel rendelkező szerepkörök társítása egy AU-hoz](roles-admin-units-assign-roles.md)