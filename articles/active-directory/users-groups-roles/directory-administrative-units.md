---
title: Felügyeleti egységek kezelése (előzetes verzió) - Azure AD | Microsoft dokumentumok
description: Felügyeleti egységek használata az Azure Active Directory engedélyeinek részletesebb delegálásához
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406471"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Felügyeleti egységek kezelése az Azure Active Directoryban (előzetes verzió)

Ez a cikk az Azure Active Directory (Azure AD) felügyeleti egységeit ismerteti. A felügyeleti egység egy Azure AD-erőforrás, amely más Azure AD-erőforrások tárolója is lehet. Ebben az előzetes verzióban a felügyeleti egység csak felhasználókat és csoportokat tartalmazhat.

A felügyeleti egységek lehetővé teszik, hogy olyan rendszergazdai engedélyeket adjon meg, amelyek a szervezet egy részlegére, régiójára vagy más, ön által meghatározott szegmensére korlátozódnak. A felügyeleti egységek segítségével engedélyeket delegálhat a regionális rendszergazdáknak, vagy részletes en állíthatja be a házirendet. A felhasználói fiók rendszergazdája például frissítheti a profiladatokat, alaphelyzetbe állíthatja a jelszavakat, és csak a felügyeleti egységben rendelhet licenceket a felhasználókhoz.

 Például a regionális támogatási szakembereka [Helpdesk Administrator](directory-assign-admin-roles.md#helpdesk-administrator) szerepkör takarásában csak a felhasználók a régióban, akik támogatják.

## <a name="deployment-scenario"></a>Központi telepítési forgatókönyv

A felügyeleti hatókör felügyeleti egységek használatával történő korlátozása hasznos lehet olyan szervezeteknél, amelyek bármilyen független részlegből állnak. Tekintsük a példát egy nagy egyetem, amely áll a sok autonóm iskolák (School of Business, School of Engineering, és így tovább), hogy minden van egy csapat informatikai adminok, akik szabályozzák a hozzáférést, a felhasználók kezelése, és állítsa be a politikákat az iskola. A központi ügyintéző:

- Felügyeleti engedélyekkel rendelkező szerepkör létrehozása csak az Azure AD-felhasználók számára az üzleti iskola felügyeleti egységében
- Közigazgatási egység létrehozása az Üzleti Iskola számára
- Az adminisztrációs egység feltöltése csak az üzleti diákokkal és a személyzettel
- Az üzleti iskola informatikai csapatának hozzáadása a hatókörrel rendelkező szerepkörhöz

## <a name="license-requirements"></a>Licenckövetelmények

A felügyeleti egységek használatához minden felügyeleti egység rendszergazdájához Azure Active Directory Premium-licencszükséges, és az Azure Active Directory ingyenes licencei a felügyeleti egység tagjaihoz. További információ: [Az Azure AD Premium – első lépések.](../fundamentals/active-directory-get-started-premium.md)

## <a name="manage-administrative-units"></a>Felügyeleti egységek kezelése

Ebben az előzetes verzióban kezelheti a felügyeleti egységeket az Azure Portalon, a PowerShell-parancsmagok és -parancsfájlok vagy a Microsoft Graph használatával. A részleteket a dokumentációnkban találja:

- [Szerepkörök létrehozása, eltávolítása, feltöltése és hozzáadása a felügyeleti egységekhez:](roles-admin-units-manage.md)Útmutató eljárások befejezése
- [Felügyeleti egységek használata:](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)Felügyeleti egységek használata a PowerShell használatával
- [Felügyeleti egység graph támogatás](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta): Részletes dokumentáció a Microsoft Graph a közigazgatási egységek.

### <a name="planning-your-administrative-units"></a>A közigazgatási egységek megtervezése

Felügyeleti egységek segítségével logikailag csoportosíthatja az Azure AD-erőforrások. Például egy olyan szervezet esetében, amelynek informatikai részlege globálisan el van szórva, célszerű lehet olyan felügyeleti egységeket létrehozni, amelyek meghatározzák ezeket a földrajzi határokat. Egy másik forgatókönyv, ahol egy multinacionális szervezet különböző "alszervezetekkel" rendelkezik, amelyek a műveletekben félig autonómak, minden alszervezetet egy felügyeleti egység képviselhet.

A felügyeleti egységek létrehozásának feltételei a szervezet egyedi követelményeinek megfelelően kerülnek alkalmazásra. A közigazgatási egységek gyakori módja im-es szolgáltatások szerkezetének meghatározásának. Javasoljuk, hogy a felügyeleti egységeket az M365-ös szolgáltatásokban való használatukkal együtt készítse elő. A felügyeleti egységek maximális értékét akkor kaphatja meg, ha az M365-ös készülékközös erőforrásait egy felügyeleti egység alatt társíthatja.

A szervezeten belüli felügyeleti egységek létrehozása várhatóan a következő szakaszokon megy keresztül:

1. Kezdeti bevezetés: A szervezet megkezdi a felügyeleti egységek létrehozását a kezdeti feltételek alapján, és a felügyeleti egységek száma a feltételek finomításával növekszik.
1. Metszés: Ha a feltételek jól meghatározottak, a már nem szükséges felügyeleti egységek törlődnek.
1. Stabilizáció: A szervezeti struktúra jól meghatározott, és a felügyeleti egységek száma rövid időtartamok alatt nem fog jelentősen változni.

## <a name="currently-supported-scenarios"></a>Jelenleg támogatott forgatókönyvek

A globális rendszergazdák vagy a kiemelt szerepkör-rendszergazdák az Azure AD portálsegítségével felügyeleti egységeket hozhatnak létre, felhasználókat vehetnek fel felügyeleti egységek tagjaiként, majd informatikai személyzetet rendelhetnek hozzá a felügyeleti egység hatókörrel rendelkező rendszergazdai szerepkörökhöz. A felügyeleti egység hatókörrel rendelkező rendszergazdái ezután az Office 365 portált használhatják a felhasználók felügyeleti egységeikben lévő felhasználók alapvető felügyeletére.

Emellett a csoportok felügyeleti egység tagjaiként is hozzáadhatók, és egy rendszergazdai egység hatókörrel rendelkező csoport rendszergazdája kezelheti őket a PowerShell, a Microsoft Graph és az Azure AD portál használatával.

Az alábbi táblázat a felügyeleti egységforgatókönyvek aktuális támogatását ismerteti.

### <a name="administrative-unit-management"></a>Igazgatási egység irányítása

Engedélyek |   MS Graph/PowerShell   | Azure AD portál | Microsoft 365 felügyeleti központ
----------- | ----------------------- | --------------- | -----------------
Felügyeleti egységek létrehozása és törlése   |    Támogatott    |   Támogatott   |    Nem támogatott
A felügyeleti egység tagjainak egyenkénti hozzáadása és eltávolítása    |   Támogatott    |   Támogatott   |    Nem támogatott
Felügyeleti egység tagok tömeges hozzáadása és eltávolítása .csv fájl használatával   |    Nem támogatott     |  Támogatott   |    Nincs támogatási terv
Felügyeleti egység hatókörrel rendelkező rendszergazdák hozzárendelése  |     Támogatott    |   Támogatott    |   Nem támogatott
AU-tagok hozzáadása és eltávolítása dinamikusan attribútumok alapján | Nem támogatott | Nem támogatott | Nem támogatott

### <a name="user-management"></a>Felhasználókezelés

Engedélyek |   MS Graph/PowerShell   | Azure AD portál | Microsoft 365 felügyeleti központ
----------- | ----------------------- | --------------- | -----------------
felhasználói tulajdonságok, jelszavak, licencek felügyeleti egységszintű kezelése   |    Támogatott     |  Támogatott   |   Támogatott
a felhasználói bejelentkezések felügyeleti egységszintű blokkolása és zárolásának feloldása    |   Támogatott   |    Támogatott   |    Támogatott
a felhasználói MFA-hitelesítő adatok felügyeleti egységszintű kezelése   |    Támogatott   |   Támogatott   |   Nem támogatott

### <a name="group-management"></a>Csoportkezelés

Engedélyek |   MS Graph/PowerShell   | Azure AD portál | Microsoft 365 felügyeleti központ
----------- | ----------------------- | --------------- | -----------------
a csoporttulajdonságok és -tagok adminisztratív egységszintű kezelése     |  Támogatott   |    Támogatott    |  Nem támogatott
a csoportos licencelés adminisztratív egységszintű kezelése   |    Támogatott  |    Támogatott   |   Nem támogatott

> [!NOTE]
>
> A felügyeleti egység hatókörével rendelkező rendszergazdák nem kezelhetik a dinamikus csoporttagsági szabályokat.

A felügyeleti egységek csak a felügyeleti engedélyekre vonatkoznak. Nem akadályozzák meg a tagokat vagy a rendszergazdákat abban, hogy [az alapértelmezett felhasználói engedélyüket](../fundamentals/users-default-permissions.md) a felügyeleti egységen kívüli más felhasználók, csoportok vagy erőforrások böngészésére használják. Az Office 365-portálon a hatókörrel rendelkező rendszergazda felügyeleti egységein kívüli felhasználók kiszűrésre kerülnek, de az Azure AD portálon, a PowerShellben és más Microsoft-szolgáltatásokban más felhasználók is böngészhetnek.

## <a name="next-steps"></a>További lépések

- [AUs kezelése](roles-admin-units-manage.md)
- [Felhasználók kezelése az ATSz-ben](roles-admin-units-add-manage-users.md)
- [Csoportok kezelése az ATSz-ben](roles-admin-units-add-manage-groups.md)
- [Hatókörrel rendelkező szerepkörök hozzárendelése AU-hoz](roles-admin-units-assign-roles.md)