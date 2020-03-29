---
title: Önkiszolgáló jelszó-visszaállítás licenc - Azure Active Directory
description: Az Azure AD önkiszolgáló jelszó-visszaállítási licencelési követelményei
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 598f3bd8500a59cd41cc4126915e6cccbd4fb2f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848561"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Az Azure AD önkiszolgáló jelszó-visszaállításlicenc-szerződésének licencelési követelményei

Az Azure Active Directory (Azure AD) több kiadásban érkezik: ingyenes, prémium p1 és prémium p2. Számos különböző funkciók, amelyek az önkiszolgáló jelszó-visszaállítás, beleértve a módosítás, alaphelyzetbe állítás, feloldása és a visszaírás, amelyek az Azure AD különböző kiadásaiban érhetők el. Ez a cikk megpróbálja elmagyarázni a különbségeket. Az egyes Azure AD-kiadásokban található funkciók további részletei az [Azure Active Directory díjszabási lapján](https://azure.microsoft.com/pricing/details/active-directory/)találhatók.

## <a name="compare-editions-and-features"></a>Kiadások és funkciók összehasonlítása

Az Azure AD önkiszolgáló jelszó-visszaállítás felhasználónként licencelt, a megfelelőségi szervezetek karbantartása szükséges a megfelelő licenc hozzárendeléséhez a felhasználók számára.

* Önkiszolgáló jelszómódosítása felhőalapú felhasználók számára
   * Csak **felhőalapú felhasználó vagyok,** és ismerem a jelszavamat.
      * Szeretném **megváltoztatni** a jelszavamat valami újra.
   * Ez a funkció az Azure AD összes kiadásában megtalálható.

* Önkiszolgáló jelszó-visszaállítás felhőalapú felhasználók számára
   * Csak **felhőalapú felhasználó** vagyok, és elfelejtettem a jelszavamat.
      * Szeretném **visszaállítani** a jelszavamat valamire, amit ismerek.
   * Ez a funkció az Azure AD Premium P1 vagy P2, a Microsoft 365 Vállalati verzió vagy az Office 365 része.

* Önkiszolgáló jelszó-visszaállítás/-módosítás/feloldás **helyszíni visszaírással**
   * **Hibrid felhasználó** vagyok, a helyszíni Active Directory felhasználói fiókom szinkronizálva van az Azure AD-fiókommal az Azure AD Connect használatával. Szeretném megváltoztatni a jelszavamat, elfelejtettem a jelszavamat, vagy kizártam.
      * Szeretném megváltoztatni a jelszavamat, vagy visszaállítani valamire, amit ismerek, vagy feloldom a fiókzárolást, **és** a módosítást vissza szeretném szinkronizálni a helyszíni Active Directoryba.
   * Ez a funkció az Azure AD Premium P1 vagy P2 vagy a Microsoft 365 Business szolgáltatásban található.

> [!WARNING]
> Az önálló Office 365-licenccsomagok *nem támogatják az "Önkiszolgáló jelszó-visszaállítást/-módosítást/feloldást helyszíni visszaírással",* és ehhez olyan csomagot igényelnek, amely tartalmazza az Azure AD Premium P1, a Prémium P2 vagy a Microsoft 365 Business szolgáltatást.
>

További licencelési információk, beleértve a költségeket, a következő oldalakon találhatók:

* [Az Azure Active Directory díjszabási webhelye](https://azure.microsoft.com/pricing/details/active-directory/)
* [Az Azure Active Directory szolgáltatásai és képességei](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Nagyvállalati verzió](https://www.microsoft.com/microsoft-365/enterprise)
* [A Microsoft 365 Vállalati szolgáltatás leírása](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Csoport- vagy felhasználóalapú licencelés engedélyezése

Az Azure AD mostantól támogatja a csoportalapú licencelést. A rendszergazdák tömegesen rendelhetnek licenceket a felhasználók egy csoportjához, ahelyett, hogy egyenként hozzárendelenék őket. További információt a [Licencek hozzárendelése, ellenőrzése és megoldása című témakörben talál.](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)

Nem minden Microsoft-szolgáltatás érhető el minden területen. Ahhoz, hogy egy licencet hozzá lehessen rendelni egy felhasználóhoz, a rendszergazdának meg kell **adnia** a felhasználó Használati hely tulajdonságát. A licencek hozzárendelése az Azure Portal **Felhasználói** > **profil** > **beállításai** szakaszában végezhető el. *A csoportos licenchozzárendelés használatakor a megadott használati hely nélküli felhasználók öröklik a könyvtár helyét.*

## <a name="next-steps"></a>További lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó alaphelyzetbe állítása vagy módosítása](../user-help/active-directory-passwords-update-your-own-password.md)
* [Regisztráció az önkiszolgáló jelszó alaphelyzetbe állításához](../user-help/active-directory-passwords-reset-register.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, valami eltört. Hogyan háríthatók el az SSPR hibái?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)
