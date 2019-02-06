---
title: Nem várt alkalmazás alkalmazáslistában |} A Microsoft Docs
description: Tekintse meg a bérlő összes alkalmazást, és megismerheti, hogyan alkalmazások jelennek meg a minden alkalmazás lista a vállalati alkalmazások
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 55632acf516198b5ad0b6430480836dcc62fd5a5
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755381"
---
# <a name="unexpected-application-in-my-applications-list"></a>Nem várt alkalmazás alkalmazáslistában

Ez a cikk segít megérteni, hogyan az alkalmazások megjelennek az **minden alkalmazás** alatt **vállalati alkalmazások**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>A bérlőn belüli összes alkalmazás megtekintése

A bérlőn belüli összes alkalmazás megjelenítéséhez kell használnia a **szűrő** vezérlőelem **minden alkalmazás** alatt a **minden alkalmazás** listája. Kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

6.  Kattintson a használata a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista**.

7.  Az a **szűrő** panelen adja meg a **megjelenítése** beállítást **összes alkalmazáshoz.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Miért jelenik meg egy adott alkalmazás összes alkalmazáslistában?

Ha a szűrt **minden alkalmazás**, a **minden alkalmazás** **lista** jeleníti meg minden szolgáltatásnév-objektumot a bérlőben. Egyszerű szolgáltatási objektumok ebben a listában, a különböző módon jelenhetnek meg:

1.  Bármely alkalmazás hozzáadása az alkalmazáskatalógusból, ha többek között:

   1. **Az Azure AD-katalógusban alkalmazások** –, az egyszeri bejelentkezés az Azure ad-vel előre integrált alkalmazás

   2. **Application Proxy alkalmazásai** – szeretne biztosítani a biztonságos egyszeri bejelentkezés, a külsőleg a helyszíni környezetben futó alkalmazásokhoz

   3. **Saját fejlesztésű alkalmazásokat** – el alkalmazást, amely a szervezet által blokkolni kívánt fejlesztése az Azure AD-alkalmazás fejlesztési platform, amely nem még létezik

   4. **Katalógusban nem szereplő alkalmazások** – saját alkalmazások importálása! Összes webes hivatkozás szeretne, vagy egy felhasználónév és jelszó mezőt megjelenítő alkalmazás támogatja az SAML- vagy OpenID Connect protokollok, vagy az egyszeri bejelentkezés az Azure ad-vel integrálni kívánt SCIM támogatja.

2.  Ha regisztrál a, vagy egy 3-ra bejelentkezés<sup>távoli asztali</sup> gyártótól származó alkalmazás az Azure Active Directoryval integrált. Ez egy [Smartsheet](https://app.smartsheet.com/b/home) vagy [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3.  Amikor a regisztrációt, vagy licencet ad hozzá egy felhasználó vagy csoport első gyártótól származó alkalmazás, például [a Microsoft Office 365-höz](https://products.office.com/)

4.  Ha egy egyénileg fejlesztett alkalmazásba használatával létrehoz egy új alkalmazás regisztrálása hozzáadása a [alkalmazás beállításjegyzékében](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)

5.  Ha egy egyénileg fejlesztett alkalmazásba használatával létrehoz egy új alkalmazás regisztrálása hozzáadása a [V2.0 az alkalmazásregisztrációs portálon](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)

6.  Amikor hozzáad egy alkalmazást fejleszt, a Visual Studio használatával [ASP.net hitelesítési módszerek](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) vagy [csatlakoztatott szolgáltatás](https://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)

7.  Amikor hoz létre egy szolgáltatás rendszerbiztonsági tag típusú objektumot használ a [Azure AD PowerShell-modul](/powershell/azure/install-adv2?view=azureadps-2.0)

8.  Ha Ön [járul hozzá az alkalmazás](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) adatok használata a bérlőben rendszergazdaként

9.  Ha egy [felhasználó jóváhagy egy alkalmazást](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) adatok használata a bérlőben

10. Amikor engedélyezi bizonyos szolgáltatásokat, amelyek adatokat tárolnak a bérlőben. Ez egy jelszó-visszaállítás, amely szerint egy egyszerű szolgáltatást a jelszó tárolásához új kérésére vonatkozó szabályzat van modellezve biztonságosan.

Az alkalmazások hogyan kerülnek a címtár további információért olvassa el a [hogyan és miért érdemes az Azure AD-alkalmazások felvétele](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Egy alkalmazás egy adott felhasználó vagy csoport-hozzárendelés eltávolítása

Egy felhasználó vagy csoport-hozzárendelés alkalmazáshoz való eltávolításához kövesse a felsorolt lépéseket a [egy felhasználó vagy csoport-hozzárendelés eltávolítása a vállalati alkalmazásokat az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) cikk.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Letiltani a minden felhasználó számára az összes alkalmazás elérése

Összes felhasználói bejelentkezés egy alkalmazás letiltásához kövesse a felsorolt lépéseket a [letiltása a felhasználók bejelentkezési folyamatába egy vállalati alkalmazás az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) cikk.

## <a name="i-want-to-delete-an-application-entirely"></a>Egy alkalmazás teljesen törölni

A **törölhető az alkalmazás**, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki a törölni kívánt alkalmazást.

7.  Ha az alkalmazás betöltött, kattintson a **törlése** ikonra a felső alkalmazás **áttekintése** ablaktáblán.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Szeretném tiltani minden jövőbeli felhasználói jóváhagyási művelet bármely alkalmazás

Felhasználói beleegyezés letiltása, a teljes címtárban megakadályozza, hogy a végfelhasználók hozzájárul ahhoz, hogy minden olyan alkalmazás esetében. A rendszergazdák továbbra is a felhasználó behalves hagyhatja jóvá. További információ az alkalmazás jóváhagyásának, és ezért lehetséges, hogy, vagy nem biztos, hogy engedélyt adjanak, olvassa el a [ismertetése felhasználói és rendszergazdai jóváhagyás](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

A **tiltsa le az összes jövőbeli felhasználói jóváhagyási művelet a teljes címtárban**, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **felhasználói beállítások**.

6.  Tiltsa le az összes jövőbeli felhasználó hozzájárulási műveleteket beállításával a **felhasználók engedélyezhetik alkalmazások hozzáférhetnek az adataikhoz való** kapcsolót **nem** , és kattintson a **mentése** gombra.

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)
