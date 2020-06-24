---
title: Váratlan alkalmazás szerepel a saját alkalmazások listájában | Microsoft Docs
description: Az összes alkalmazás megjelenítése a bérlőben és annak megértése, hogyan jelennek meg az alkalmazások a minden alkalmazás listában a vállalati alkalmazások területen
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1878004f51a66ba414c3e74be97c7135a51295bc
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763703"
---
# <a name="unexpected-application-in-my-applications-list"></a>Váratlan alkalmazás szerepel az alkalmazások listájában

Ez a cikk segít megérteni, hogyan jelennek meg az alkalmazások a **minden alkalmazás** listában a **vállalati alkalmazások**területen. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>A bérlőben található összes alkalmazás megjelenítése

A bérlő összes alkalmazásának megtekintéséhez a **szűrő** vezérlőelemet kell használnia **az összes alkalmazás listán** szereplő **összes alkalmazás** megjelenítéséhez. Kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5.  kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

6.  kattintson a **minden alkalmazás lista**tetején található **szűrő** vezérlőelem használata elemre.

7.  A **szűrő** ablaktáblán állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Miért jelenik meg egy adott alkalmazás az összes alkalmazás listán?

Ha az **összes alkalmazásra**szűri, a **minden alkalmazás** **lista** megjeleníti a bérlő összes egyszerű szolgáltatásnév objektumát. A szolgáltatás egyszerű objektumai több módon is megjelenhetnek a listában:

1. Ha bármely alkalmazást hozzáad az alkalmazás-katalógusból, beleértve a következőket:

   1. **Azure ad Gallery-alkalmazások** – az Azure ad-vel való egyszeri bejelentkezéshez előre integrált alkalmazás

   2. **Alkalmazásproxy-alkalmazások** – egy olyan alkalmazás, amely a helyszíni környezetben fut, és biztonságos egyszeri bejelentkezést kíván biztosítani a külsőleg

   3. **Egyéni** fejlesztésű alkalmazások – a szervezet által az Azure ad Application Development platformon fejleszteni kívánt alkalmazás, de még nem létezik

   4. **Galérián kívüli alkalmazások** – saját alkalmazások használata Bármely olyan Webhivatkozás, amelyet szeretne, vagy bármely olyan alkalmazás, amely felhasználónevet és jelszót jelenít meg, támogatja az SAML vagy az OpenID Connect protokollokat, illetve támogatja a SCIM, amelyeket az Azure AD-vel való egyszeri bejelentkezéshez szeretne integrálni.

2. Amikor regisztrál a-ra, vagy bejelentkezik a szolgáltatásba, egy Azure Active Directory-vel integrált, 3<sup>Távoli asztali</sup> alkalmazás. Az egyik példa a [Smartsheet](https://app.smartsheet.com/b/home) vagy a [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3. Amikor regisztrál a-ra, vagy hozzáad egy licencet egy felhasználóhoz vagy egy csoporthoz egy első féltől származó alkalmazáshoz, például [Microsoft Office 365](https://products.office.com/)

4. Új alkalmazás regisztrációjának hozzáadásakor hozzon létre egy egyéni fejlesztésű alkalmazást az [alkalmazás-beállításjegyzék](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration) használatával

5. Új alkalmazás regisztrációjának hozzáadásakor hozzon létre egy egyéni fejlesztésű alkalmazást a [v 2.0 alkalmazás regisztrációs portálján](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)

6. Ha olyan alkalmazást ad hozzá, amelyet a Visual Studio [ASP.net hitelesítési módszereivel](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) vagy [csatlakoztatott szolgáltatásaival](https://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx) fejleszt

7. Egyszerű szolgáltatás létrehozásakor az [Azure ad PowerShell-modul](/powershell/azure/install-adv2?view=azureadps-2.0) használatával

8. Ha az [alkalmazáshoz való beleegyezett](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) , hogy rendszergazdaként használja a bérlő adatait

9. Amikor egy [felhasználó beleegyezett az alkalmazásba](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) , hogy a bérlőn lévő adatait használja

10. Ha olyan szolgáltatásokat engedélyez, amelyek a bérlőn tárolt adatait tárolják. Az egyik példa a jelszó alaphelyzetbe állítása, amelyet egyszerű szolgáltatásnévként kell tárolni a jelszó-visszaállítási házirend biztonságos tárolásához.

Az alkalmazások címtárba való felvételével kapcsolatos további részletekért olvassa el, [Hogyan és miért adja hozzá az alkalmazásokat az Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added)-hez.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Egy adott felhasználó vagy csoport hozzárendelését szeretném eltávolítani egy alkalmazáshoz

Ha el szeretne távolítani egy felhasználói vagy csoport-hozzárendelést egy alkalmazáshoz, kövesse a [felhasználó vagy csoport kiosztásának eltávolítása vállalati alkalmazásból Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) cikkben felsorolt lépéseket.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Le szeretném tiltani egy alkalmazás összes hozzáférését minden felhasználó számára

Ha le szeretné tiltani az összes felhasználói bejelentkezést egy alkalmazásba, kövesse a [vállalati alkalmazás felhasználói bejelentkezésének letiltása Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) cikkben felsorolt lépéseket.

## <a name="i-want-to-delete-an-application-entirely"></a>Teljesen törölni szeretném az alkalmazást

**Egy alkalmazás törléséhez**kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

   * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6. Válassza ki a törölni kívánt alkalmazást.

7. Az alkalmazás betöltése után kattintson a **Törlés** ikonra a felső alkalmazás **Áttekintés** paneljén.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Le szeretném tiltani az összes jövőbeli felhasználói beleegyező műveletet bármely alkalmazáshoz

A teljes címtár felhasználói beleegyezésének letiltása megakadályozza, hogy a végfelhasználók bármilyen alkalmazást fogadnak el. A rendszergazdák továbbra is engedélyezhetik a felhasználó nevében való hozzájárulásukat. Ha többet szeretne megtudni az alkalmazások beleegyezéséről, és miért nem szeretne beleegyezést kapni, olvassa el a [felhasználói és rendszergazdai beleegyezést](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)ismertető témakört.

Ha **le szeretné tiltani az összes jövőbeli felhasználói beleegyező műveletet a teljes címtárban**, kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson a **felhasználói beállítások**elemre.

6.  Tiltsa le az összes jövőbeli felhasználói megkötési műveletet úgy, hogy a **felhasználók engedélyezik, hogy az alkalmazások hozzáférjenek az adatokhoz** , és **ne** kattintson a **Save (Mentés** ) gombra.

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése Azure Active Directory](what-is-application-management.md)
