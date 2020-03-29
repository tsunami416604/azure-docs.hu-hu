---
title: Váratlan alkalmazás az alkalmazáslistámon | Microsoft dokumentumok
description: Az összes alkalmazás megtekintése a bérlőben, és annak megértése, hogy az alkalmazások hogyan jelennek meg a Minden alkalmazás listában a Vállalati alkalmazások csoportban
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1cb4eeb52d0680695bda266ad1a563b2ef5ee02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65781099"
---
# <a name="unexpected-application-in-my-applications-list"></a>Nem várt alkalmazás az alkalmazások listájában

Ez a cikk segítséget nyújt annak megértéséhez, hogy az alkalmazások hogyan jelennek meg a Vállalati **alkalmazások**csoport **Minden alkalmazás** listájában. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Az összes alkalmazás megtekintése a bérlőben

A bérlőben lévő összes alkalmazás megtekintéséhez a **Szűrő** vezérlővel kell **megjelenítenie** **az** Összes alkalmazás listát. Kövesse az alábbi lépéseket:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5.  Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

6.  kattintson a **Szűrő** vezérlő gombjára a **Minden alkalmazás lista tetején.**

7.  A **Szűrő** ablaktáblán állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Miért jelenik meg egy adott alkalmazás az összes alkalmazáslistában?

Az Összes **alkalmazás**ra szűrve a **Minden alkalmazás** **lista** a bérlő összes egyszerű szolgáltatásobjektumát megjeleníti. Az egyszerű szolgáltatásobjektumok többféleképpen jelenhetnek meg a listában:

1. Ha bármilyen alkalmazást hozzáad az alkalmazásgalériából, beleértve a következőket:

   1. **Azure AD Gallery-alkalmazások** – Olyan alkalmazás, amely et előre integráltaz Azure AD-vel való egyszeri bejelentkezéshez

   2. **Alkalmazásproxy-alkalmazások** – A helyszíni környezetben futó alkalmazás, amelyet biztonságos egyszeri bejelentkezéssel szeretne biztosítani külsőleg

   3. **Egyéni legfejlettebb alkalmazások** – Olyan alkalmazás, amelyet a szervezet fejleszteni kíván az Azure AD alkalmazásfejlesztési platformon, de előfordulhat, hogy még nem létezik

   4. **Nem Galéria alkalmazások** - Hozd a saját alkalmazások! Bármely webes hivatkozás, vagy bármely olyan alkalmazás, amely egy felhasználónév és jelszó mezőt jelenít meg, támogatja az SAML vagy openId Connect protokollokat, vagy támogatja az SCIM-et, amelyet integrálni szeretne az Azure AD-vel való egyszeri bejelentkezéshez.

2. Amikor regisztrál, vagy bejelentkezik az Azure Active Directoryval integrált 3<sup>rd</sup> party alkalmazásra, vagy bejelentkezik. Erre példa a [Smartsheet](https://app.smartsheet.com/b/home) vagy a [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3. Amikor regisztrál, vagy licencet ad egy felhasználóhoz vagy csoporthoz egy külső alkalmazáshoz, például a [Microsoft Office 365-höz](https://products.office.com/)

4. Ha új alkalmazásregisztrációt ad hozzá egy egyéni fejlesztésű alkalmazás létrehozásával az [alkalmazás-beállításjegyzék](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration) használatával

5. Ha új alkalmazásregisztrációt ad hozzá egy egyéni fejlesztésű alkalmazás létrehozásával a [V2.0 alkalmazásregisztrációs portálhasználatával](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)

6. Amikor a Visual Studio [ASP.net hitelesítési módszerével](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) vagy [csatlakoztatott szolgáltatásokkal](https://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx) fejlesztő alkalmazást ad hozzá

7. Amikor létrehoz egy egyszerű szolgáltatásobjektumot az [Azure AD PowerShell-modul használatával](/powershell/azure/install-adv2?view=azureadps-2.0)

8. Ha [beleegyezik abba, hogy egy alkalmazás](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) rendszergazdaként használja az adatokat a bérlőben

9. Amikor egy [felhasználó beleegyezik egy alkalmazásba](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) a bérlőben lévő adatok használatához

10. Ha engedélyezi bizonyos szolgáltatások, amelyek adatokat tárolnak a bérlőben. Egy példa a jelszó-visszaállítás, amely egyszerű szolgáltatásként van modellezve a jelszó-visszaállítási házirend biztonságos tárolásához.

Ha további részleteket szeretne megtudni arról, hogyan kerülnek hozzá az alkalmazások a címtárhoz, olvassa el a [Hogyan és miért adják hozzá az alkalmazásokat az Azure AD-hez.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added)

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>El szeretném távolítani egy adott felhasználó vagy csoport hozzárendelését egy alkalmazáshoz

Ha el szeretne távolítani egy felhasználó- vagy csoporthozzárendelést egy alkalmazáshoz, kövesse a [Felhasználó vagy csoport hozzárendeléseltávolítása vállalati alkalmazásból](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) az Azure Active Directory cikkben szereplő lépéseket.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Azt akarom, hogy tiltsa le az összes hozzáférést egy alkalmazás minden felhasználó számára

Az alkalmazásba való összes felhasználói bejelentkezés letiltásához kövesse a [vállalati alkalmazások felhasználói bejelentkezéseinek letiltása az Azure Active Directory-cikkben](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) szereplő lépéseket.

## <a name="i-want-to-delete-an-application-entirely"></a>Teljesen törölni szeretnék egy alkalmazást

**Alkalmazás törléséhez**hajtsa végre az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Jelölje ki a törölni kívánt alkalmazást.

7. Miután az alkalmazás betöltődik, kattintson a **Törlés** ikonra a felső alkalmazás **áttekintő** ablaktábláján.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Azt akarom, hogy tiltsa le az összes jövőbeli felhasználói hozzájárulási műveletek bármely alkalmazás

A teljes címtár felhasználói hozzájárulásának letiltása megakadályozza, hogy a végfelhasználók bármely alkalmazáshoz hozzájáruljanak. A rendszergazdák továbbra is beleegyezhetnek a felhasználó nevében. Ha többet szeretne megtudni az alkalmazás beleegyezéséről, és arról, hogy miért szeretne vagy miért nem szeretne hozzájárulni, olvassa el [a Felhasználói és rendszergazdai hozzájárulás ismertetése című témakört.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)

A **teljes címtárban lévő összes jövőbeli felhasználói hozzájárulási művelet letiltásához hajtsa**végre az alábbi lépéseket:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5.  kattintson a **Felhasználói beállítások gombra.**

6.  Tiltsa le az összes jövőbeli felhasználói hozzájárulási műveletet, ha beállítja, hogy a **Felhasználók engedélyezhetik, hogy az alkalmazások hozzáférjenek az adataikhoz,** **és** a **Mentés** gombra kattintva.

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)
