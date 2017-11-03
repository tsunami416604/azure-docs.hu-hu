---
title: "Az alkalmazások listáját a váratlan alkalmazás |} Microsoft Docs"
description: "Tekintse meg a bérlő összes alkalmazást, és megérteni, hogyan alkalmazások jelennek meg az összes alkalmazások listáját a vállalati alkalmazások"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 0f8136cb066fa6e3e4a8dd06e34b9f866e3916f6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="unexpected-application-in-my-applications-list"></a>Az alkalmazások listáját a váratlan alkalmazás

Ez a cikk segítenek megérteni, hogyan az alkalmazások megjelennek a **összes alkalmazás** listában az **vállalati alkalmazások**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>A bérlő szereplő összes alkalmazás megtekintése

A bérlő összes alkalmazás megtekintéséhez kell használnia a **szűrő** vezérlő megjelenítése **összes alkalmazás** alatt a **összes alkalmazás** listája. Ehhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

6.  Kattintson a használata a **szűrő** vezérlő tetején a **összes alkalmazások listáját**.

7.  A a **szűrő** panelen állítsa a **megjelenítése** lehetőséggel **összes alkalmazást.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Miért nem egy adott alkalmazás jelenik meg az összes alkalmazások listáját?

Ha szűrt **összes alkalmazás**, a **összes alkalmazás** **lista** minden egyszerű szolgáltatásnév objektum szerepel a bérlő. Egyszerű objektumok ebben a listában, a különböző módon jelenhetnek meg:

1.  Amikor bármely alkalmazás az alkalmazás gyűjteményből, beleértve:

   1. **Azure AD-gyűjtemény alkalmazások** –, amelyet az egyszeri bejelentkezés az Azure ad-vel előre integrált alkalmazás.

   2. **Alkalmazás Proxy alkalmazások** – szeretne biztosítani a biztonságos egyszeri bejelentkezést a külsőleg a helyszíni környezetben futó alkalmazáshoz.

   3. **Egyéni által fejlesztett alkalmazások** – az alkalmazás, amely a szervezet által platformon az Azure AD alkalmazás fejlesztési kialakításához, de, amely nem még létezik.

   4. **Nem-gyűjtemény alkalmazások** – kapcsolja a saját alkalmazásai! Bármely kívánt webes hivatkozást, vagy egy felhasználónevet és jelszót a mezőben, amely alkalmazás SAML vagy az OpenID Connect protokollt támogatja, vagy az egyszeri bejelentkezés az Azure ad-vel integrálni kívánt SCIM támogatja.

2.  Ha regisztrálni szeretne, vagy egy 3 történő bejelentkezés<sup>távoli asztali</sup> gyártótól származó alkalmazás Azure Active Directory integrált része. Egy példa erre, [Smartsheet](https://app.smartsheet.com/b/home) vagy [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3.  Ha regisztrálni szeretne, vagy licencet ad hozzá egy felhasználóhoz vagy csoporthoz első fél alkalmazás, például [Microsoft Office 365](http://products.office.com/).

4.  Amikor felvesz egy új alkalmazás regisztrációja hozzon létre egy egyéni által fejlesztett alkalmazás használja a [alkalmazás beállításjegyzék](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration).

5.  Amikor felvesz egy új alkalmazás regisztrációja hozzon létre egy egyéni által fejlesztett alkalmazás használja a [V2.0 alkalmazásregisztrációs portálra](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration#visit-the-microsoft-app-registration-portal).

6.  Ha egy alkalmazás kidolgozása Visual Studio használatával [ASP.net hitelesítési módszerek](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) vagy [kapcsolódó szolgáltatások](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx).

7.  Amikor hoz létre a szolgáltatás elsődleges objektumot használ a [Azure AD PowerShell modult](/powershell/azure/install-adv2?view=azureadps-2.0).

8.  Ha Ön [járul hozzá az alkalmazás](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) adatok használata a bérlői rendszergazda.

9.  Ha egy [felhasználói járul hozzá az alkalmazás](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) adatok használata az Ön bérelt szolgáltatásának.

10. Ha engedélyezi az egyes szolgáltatások tárolt adatok az Ön bérelt szolgáltatásának. Egy példa erre, jelszó alaphelyzetbe állítása, amely szerint a szolgáltatás egyszerű tárolni a jelszó-visszaállítási házirend van modellezve biztonságos helyen.

További részleteket a hogyan alkalmazások hozzáadódnak a könyvtárhoz, olvassa el [útmutató és érvek alkalmazások felvétele az Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Egy alkalmazás egy adott felhasználó vagy csoport-hozzárendelés eltávolítása

Egy felhasználó vagy csoport-hozzárendelés alkalmazáshoz való eltávolításához kövesse a lépéseket a [egy felhasználó vagy csoport-hozzárendelés eltávolítása egy vállalati alkalmazás Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) cikk.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Szeretném tiltani minden hozzáférést egy alkalmazáshoz minden felhasználó

Minden felhasználói bejelentkezések alkalmazáshoz való letiltásához kövesse a lépéseket a [tiltsa le a felhasználói bejelentkezések Azure Active Directory vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) cikk.

## <a name="i-want-to-delete-an-application-entirely"></a>Az alkalmazás teljesen törlése

A **alkalmazás törlése**, kövesse az alábbi utasításokat:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes alkalmazást.**

6.  Válassza ki a törölni kívánt alkalmazást.

7.  Ha az alkalmazás betölt, kattintson **törlése** ikonra a felső alkalmazás **áttekintése** panelen.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Szeretném tiltani az összes jövőbeni felhasználói hozzájárulás műveletek bármely alkalmazás

Felhasználói hozzájárulás letiltása, az a teljes címtár megakadályozhatja a végfelhasználók számára hozzájárul ahhoz, hogy minden alkalmazás. A rendszergazdák továbbra is a felhasználó behalves is hozzájárulás. Tudjon meg többet a kérelem jóváhagyását, és ezért előfordulhat, hogy, vagy előfordulhat, hogy nem szeretne ehhez olvassa el a [ismertetése felhasználói és rendszergazdai hozzájárulási](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

A **tiltsa le a teljes címtár minden jövőbeni felhasználói hozzájárulás műveletei**, kövesse az alábbi utasításokat:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **felhasználói beállítások**.

6.  Tiltsa le az összes jövőbeni felhasználói hozzájárulás műveletek úgy, hogy a **felhasználók is engedélyezi, hogy az alkalmazások hozzáférjenek az adataikhoz** kapcsolót **nem** , és kattintson a **mentése** gombra.

## <a name="next-steps"></a>Következő lépések
[Alkalmazások kezelése az Azure Active Directoryban](active-directory-enable-sso-scenario.md)
