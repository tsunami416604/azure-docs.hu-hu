---
title: SAML-alapú egyszeri bejelentkezés – Azure Active Directory hibakeresése |} A Microsoft Docs
description: SAML-alapú egyszeri bejelentkezés az Azure Active Directory-alkalmazások hibakeresését.
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.author: celested
ms.custom: aaddev
ms.reviewer: luleon, hirsin, smalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: f854c8b27065c2d2bf0c9964fe9dfce66aba423a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58104505"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>SAML-alapú egyszeri bejelentkezés az Azure Active Directory-alkalmazások hibakeresése

Ismerje meg, hogyan keresheti meg és javítsa ki [egyszeri bejelentkezési](../manage-apps/what-is-single-sign-on.md) problémák, amelyek támogatják az alkalmazások az Azure Active Directoryban (Azure AD) [Security Assertion Markup Language (SAML) 2.0-s](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Előkészületek

Javasoljuk, hogy telepíti a [saját alkalmazások biztonságos bejelentkezési bővítménye](../user-help/active-directory-saas-access-panel-user-help.md#i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension). A bővítmény megkönnyíti a gyűjtse össze a SAML-kérelmet, és az SAML-válasz információt, amit keresett való egyetlen bejelentkezéssel kapcsolatos problémák elhárítása. Abban az esetben, ha nem telepíti a bővítményt, a Ez a cikk bemutatja, hogyan és a bővítmény telepítése anélkül problémák megoldásához.

Töltse le és telepítse a saját alkalmazások biztonságos bejelentkezési bővítménye, használja az alábbi hivatkozások egyikét.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>SAML-alapú egyszeri bejelentkezés tesztelése

SAML-alapú egyszeri bejelentkezés az Azure AD között teszteléséhez és a egy célalkalmazás:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) a globális rendszergazdaként vagy más rendszergazdák, amelynek jogosítványa van az alkalmazások kezeléséhez.
1. A bal oldali panelen válassza ki a **Azure Active Directory**, majd válassza ki **vállalati alkalmazások**. 
1. Vállalati alkalmazások listájából válassza ki az alkalmazást, amely egyszeri bejelentkezés tesztelése szeretne, majd válassza a bal oldali lehetőségei közül **egyszeri bejelentkezési**.
1. Nyissa meg az SAML-alapú egyszeri bejelentkezés tesztelési használatát, lépjen a **egyszeri bejelentkezés tesztelése** (5. lépés). Ha a **teszt** gomb szürkén jelenik meg, töltse ki és mentse el a szükséges attribútumok először kell létrehoznia a **alapszintű SAML-konfigurációja** szakaszban.
1. Az a **egyszeri bejelentkezés tesztelése** panelen, a vállalati hitelesítő adatok használatával jelentkezzen be a célalkalmazás. Az aktuális felhasználó vagy egy másik felhasználóval jelentkezhetnek be. Jelentkezzen be másik felhasználóként, egy üzenet rákérdez hitelesítéséhez.

    ![Teszt SAML lap](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)


Sikeresen jelentkezett be, ha a teszt sikerült. Ebben az esetben az Azure AD az alkalmazás számára kibocsátott SAML-válasz jogkivonat. Az alkalmazás az SAML-jogkivonat sikeresen a bejelentkezéshez használ.

Ha a bejelentkezési oldal vállalati vagy az alkalmazás oldalán hiba, használja a következő szakaszok egyikét a hiba megoldásához.


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Oldja meg a vállalati bejelentkezési oldalon a bejelentkezési hiba

Amikor megpróbál bejelentkezni, előfordulhat, hogy hibaüzenet jelenik meg a vállalati bejelentkezési oldalon az alábbi példához hasonló.

![Bejelentkezési hiba](./media/howto-v1-debug-saml-sso-issues/error.png)

Ez a hiba hibakeresési, a hibaüzenetet, és az SAML-kérelmet kell. A saját alkalmazások biztonságos bejelentkezési bővítménye automatikusan gyűjti össze az adatokat, és megjeleníti a megoldási útmutató az Azure ad-ben. 

### <a name="to-resolve-the-sign-in-error-with-the-myapps-secure-sign-in-extension-installed"></a>Feloldani a bejelentkezési hiba a MyApps biztonságos bejelentkezési bővítménye telepítve

1. Ha hiba történik, a bővítmény átirányítja Önt az Azure AD vissza a **egyszeri bejelentkezés tesztelése** panelen. 
1. Az a **egyszeri bejelentkezés tesztelése** panelen válassza ki **töltse le az SAML-kérelmet**. 
1. Megtekintheti az adott megoldási útmutató a hibát és az SAML-kérelmet szereplő értékek alapján.
1. Látni fogja a **javítást** a probléma megoldásához az Azure AD-ben a konfiguráció automatikus frissítése gombra. Ha nem látja ezt a gombot, majd a bejelentkezési hiba, nem az Azure AD egy helytelen konfiguráció miatt.

Ha a megoldást nem áll rendelkezésre a bejelentkezési hiba, javasoljuk, hogy, hogy a visszajelzés szövegmező értesítsen minket, használjon.

### <a name="to-resolve-the-error-without-installing-the-myapps-secure-sign-in-extension"></a>A hiba megoldásához a MyApps biztonságos bejelentkezési bővítménye telepítése nélkül

1. Másolja az hibaüzenet jelenik meg a lap jobb alsó sarokban. A hibaüzenetben szerepelnek:
    - Korrelációs azonosító és időbélyeget. Ezeket az értékeket hoz létre egy támogatási esetet a Microsofttal, mivel a mérnökök a probléma azonosításához, és adja meg a probléma megoldását, egy pontos segítségükkel esetén fontos.
    - Egy utasítást a probléma kiváltó okának azonosításához.
1. Lépjen vissza az Azure ad-hez, és keresse meg a **egyszeri bejelentkezés tesztelése** panelen.
1. A fenti szövegmezőben **megoldási útmutató**, illessze be a hibaüzenetet.
1. Kattintson a **megoldási útmutató** megjelenítendő lépéseket a problémák elhárításához. Az útmutató a SAML-kérelmet, vagy a SAML-válasz információra lehet szükség. Ha nem használ a MyApps biztonságos bejelentkezési bővítménye, szükség lehet egy eszköz például [Fiddler](https://www.telerik.com/fiddler) a SAML-kérések és válaszok lekéréséhez.
1. Győződjön meg arról, hogy a SAML-kérelemben a cél megfelel a SAML egyszeri bejelentkezési szolgáltatás URL-cím az Azure ad-ből kapott.
1. Ellenőrizze-e az SAML-kérelmet a kibocsátó ugyanazzal az azonosítóval már konfigurálta az alkalmazást az Azure ad-ben. Azure ad-ben a kibocsátó a címtárban található az alkalmazás használ.
1. Ellenőrizze, hogy AssertionConsumerServiceURL, ahol az alkalmazás Azure ad-ben az SAML-jogkivonat fogadjon vár. Ezt az értéket konfigurálhatja az Azure ad-ben, de azt nem kötelező Ha része az SAML-kérelmet.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Oldja meg az alkalmazás oldalán található egy bejelentkezési hiba

Előfordulhat, hogy jelentkezzen be sikeresen, és tekintse meg a hiba az alkalmazás lapján. Ez akkor fordul elő, amikor az Azure AD az alkalmazásnak egy jogkivonatot állít ki, de az alkalmazás nem fogadja el a választ.   

A hiba elhárításához kövesse az alábbi lépéseket:

1. Ha az alkalmazás az Azure AD katalógusából származó, győződjön meg arról, hogy követte az alkalmazás integrálása az Azure AD összes lépését. Az alkalmazás integrációs utasításokat talál a [SaaS-alkalmazások integrációjának oktatóanyagai listája](../saas-apps/tutorial-list.md).
1. Az SAML-válasz lekérése.
    - Ha a saját alkalmazások biztonságos bejelentkezési bővítménye telepítve van, az a **egyszeri bejelentkezés tesztelése** panelen kattintson a **letöltési SAML-válasz**.
    - Ha a bővítmény nincs telepítve, használja a eszközt például [Fiddler](https://www.telerik.com/fiddler) SAML-válasz lekéréséhez. 
1. Figyelje meg ezeknek az elemeknek a SAML-válasz token:
   - Felhasználó egyedi azonosítója, NameID-érték és formázása
   - A jogkivonatban kiadott jogcímek
   - A jogkivonat aláírásához használt tanúsítványt. 

     SAML-válasszal kapcsolatos további információkért lásd: [egyszeri bejelentkezéses SAML-protokoll](single-sign-on-saml-protocol.md).

1. Most, hogy áttekintette a SAML-válasz, tekintse meg a [hiba történt a bejelentkezés után az alkalmazás lapon](../manage-apps/application-sign-in-problem-application-error.md) hogyan a probléma megoldásához. 
1. Ha továbbra sem tudja sikeresen bejelentkezni, megkérheti az alkalmazás gyártójától Mi hiányzik a SAML-válasz.


## <a name="next-steps"></a>További lépések

Most, hogy az egyszeri bejelentkezés az alkalmazás működik, próbálja meg [automatizálhatja a felhasználókiépítés és -megszüntetés SaaS-alkalmazásokhoz](../manage-apps/user-provisioning.md) vagy [feltételes hozzáférés használatának első lépései](../conditional-access/app-based-conditional-access.md).
