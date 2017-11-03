---
title: "Jelszó egyszeri bejelentkezés egy nem galéria applicationn a konfigurálása |} Microsoft Docs"
description: "Egyéni nem galéria alkalmazás biztonságos jelszó-alapú egyszeri bejelentkezés konfigurálása, ha nem szerepel az Azure AD Application Gallery"
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
ms.openlocfilehash: f629ec99824199306ebf825901beaa99d83d434d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jelszó egyszeri bejelentkezés nem galéria alkalmazások konfigurálása

Az Azure AD Application Gallery belül található lehetőségek mellett is is felvehet egy **nem galéria alkalmazás** amikor kívánt alkalmazás nem szerepel ott. Használja ezt a képességet, hozzáadhat bármely alkalmazás, amely a szervezet már létezik, vagy bármilyen alkalmazás, amelyet esetleg olyan szállítótól, aki nem már része a [az Azure AD Application Gallery](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery).

Miután hozzáadja egy nem galéria-alkalmazást, majd konfigurálhatja az egyszeri bejelentkezés az alkalmazás által használt módszer kiválasztásával a **egyszeri bejelentkezés** navigációs elem az adott vállalati alkalmazást a [Azure Portal](https://portal.azure.com/).

Az egyszeri bejelentkezés módszer áll rendelkezésre, egyik a [jelszó-alapú egyszeri bejelentkezést](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) lehetőséget. Az a **nem galéria alkalmazás hozzáadása** élményt, egy HTML-alapú felhasználónév Renderelés bármely olyan alkalmazás integrálható, és a jelszó bemeneti mező, még akkor is, ha nincs-e az előre integrált alkalmazások csoportját.

A ez működik úgy, hogy egy lap lekaparást technológia, amely része a hozzáférési Panel bővítmény, amely lehetővé teszi, hogy a felhasználónév és jelszó bemeneti mező automatikus észlelésű, tárolja őket biztonságos helyen az adott alkalmazáspéldány esetében. Majd biztonságosan visszajátszásos felhasználónevek és jelszavak mezőkhöz, ha a felhasználók megnyitják az alkalmazást az alkalmazás-hozzáférési panelre.

Első lépésként bármilyen alkalmazás integrálása az Azure AD gyorsan remek mód, és lehetővé teszi:

-   Integrálni **a világ bármely alkalmazás** az Azure AD-bérlőn, akkor ez a beállítás egy HTML felhasználónév és jelszó beviteli mezőt, amennyiben

-   Engedélyezése **egyszeri bejelentkezéshez a felhasználók** biztonságos helyen tárolja, és visszajátszását felhasználóneveket és jelszavakat az alkalmazás által integrált már az Azure ad szolgáltatással

-   **Az automatikus észlelés bemeneti** mezők bármely alkalmazás, és lehetővé teszi, hogy manuálisan ezeket a mezőket a hozzáférési Panel bővítmény, abban az esetben, ha automatikus észlelés nem talál őket

-   **Több bejelentkezési mező igénylő alkalmazások támogatását** csupán felhasználónév és jelszó megadására való bejelentkezéshez igénylő alkalmazások

-   **A címkék testreszabása** megjelenik a felhasználók felhasználónév és jelszó bemeneti mező a [alkalmazás hozzáférési Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) Ha a hitelesítő adatok megadása

-   Engedélyezi a **felhasználók** azok vannak, írja be manuálisan a meglévő alkalmazás fiókok számára a saját felhasználónevek és jelszavak biztosításához a [alkalmazás hozzáférési Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Lehetővé teszi egy **az üzleti csoport tagja** számára adja meg a felhasználónevek és jelszavak használatával a felhasználóhoz rendelt a [önkiszolgáló alkalmazás-hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) szolgáltatás

-   Lehetővé teszi egy **rendszergazda** adja meg, hogy a felhasználónevek és jelszavak a frissítés hitelesítő adatok használatával a felhasználóhoz rendelt funkciót [egy felhasználó hozzárendelése egy alkalmazás](#_How_to_configure_1)

-   Lehetővé teszi egy **rendszergazda** adhatja meg a megosztott felhasználónév és jelszó a frissítés hitelesítő adatok segítségével egy csoport tagjainak által használt funkciót [csoport hozzárendelése egy alkalmazás](#assign-an-application-to-a-group-directly)

Az alábbiakban ismerteti, hogyan engedélyezheti [jelszó-alapú egyszeri bejelentkezést](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) bármely alkalmazás használatával hozzáadott a **nem galéria alkalmazás hozzáadása** tapasztalhat.

## <a name="overview-of-steps-required"></a>A szükséges lépések – áttekintés

Egy alkalmazás kell az Azure AD-galériából konfigurálása:

-   [Nem galéria alkalmazás hozzáadása](#add-a-non-gallery-application)

-   [Állítsa be az alkalmazását jelszó egyszeri bejelentkezést.](#configure-the-application-for-password-single-sign-on)

-   [Az alkalmazást egy felhasználó vagy csoport](#assign-the-application-to-a-user-or-a-group)

    -   [Felhasználó hozzárendelése egy alkalmazás közvetlenül](#assign-a-user-to-an-application-directly)

    -   [Rendelje hozzá közvetlenül egy alkalmazás egy csoporthoz](#assign-an-application-to-a-group-directly)

## <a name="add-a-non-gallery-application"></a>Nem galéria alkalmazás hozzáadása

Hozzáadhat egy alkalmazást az Azure AD-gyűjteményből, kövesse az alábbi lépéseket:

1.  Nyissa meg a [Azure Portal](https://portal.azure.com) , és jelentkezzen be egy **globális rendszergazda** vagy **társadminisztrátornak**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **Hozzáadás** gombra a jobb felső sarkában a **vállalati alkalmazások** panel

6.  Kattintson a **nem-gyűjtemény alkalmazás.**

7.  Adja meg az alkalmazás nevét a **neve** szövegmező. Válassza ki **hozzáadása.**

Egy rövid időszak után el az alkalmazás konfigurációs panelen láthatók.

## <a name="configure-the-application-for-password-single-sign-on"></a>Állítsa be az alkalmazását jelszó egyszeri bejelentkezést.

Egyszeri bejelentkezés egy alkalmazás konfigurálásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes alkalmazást.**

6.  Válassza ki az egyszeri bejelentkezés konfigurálni kívánt alkalmazást.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Válassza ki a módot **jelszóalapú bejelentkezés.**

9.  Adja meg a **bejelentkezési URL-cím**. Ez az URL-CÍMÉT ahol adja meg a felhasználók a felhasználónevével és jelszavával bejelentkezni. Győződjön meg arról, a mezők bejelentkezési URL-címen láthatók.

10. Felhasználók hozzárendelése az alkalmazáshoz.

11. Emellett is megadhatja a felhasználó nevében hitelesítő adatok a felhasználók a sorok kijelöléséhez és parancsával **frissítéséhez szükséges hitelesítő adatokat** , majd gépelje be a felhasználónevet és jelszót a felhasználók nevében. Ellenkező esetben megkérdezi a felhasználókat a hitelesítő adatok magukat az indítás után.

## <a name="assign-a-user-to-an-application-directly"></a>Felhasználó hozzárendelése egy alkalmazás közvetlenül

Hozzárendelése egy vagy több felhasználó alkalmazás közvetlenül, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes alkalmazást.**

6.  Válassza ki szeretné osztani a felhasználót, hogy a listában az alkalmazást.

7.  Ha az alkalmazás betölt, kattintson **felhasználók és csoportok** az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **Hozzáadás** gombra kattint, a a **felhasználók és csoportok** nyissa meg a listában a **hozzáadása hozzárendelés** panelen.

9.  Kattintson a **felhasználók és csoportok** a választó a **hozzáadása hozzárendelés** panelen.

10. Írja be a **teljes név** vagy **e-mail cím** érdekli hozzárendelése a felhasználó a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. Vigye a **felhasználói** a listában, hogy láthatóvá váljon a **jelölőnégyzet**. A felhasználói profil fénykép vagy adja hozzá a felhasználót emblémát jelölőnégyzetét, kattintson a **kijelölt** listája.

12. **Választható lehetőség:** Ha azt szeretné, hogy **egynél több felhasználó hozzáadása**, egy másik típus **teljes név** vagy **e-mail cím** be a **Keresés név vagy e-mail cím alapján** mező, és a jelölőnégyzet bejelölésével adja hozzá a felhasználót, hogy a **kijelölt** listája.

13. Ha elkészült, válassza a felhasználók, kattintson a **válasszon** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazáshoz.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** a választó a **hozzáadása hozzárendelés** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

15. Kattintson a **hozzárendelése** gombra kattintva a kijelölt felhasználók az alkalmazást.

## <a name="assign-an-application-to-a-group-directly"></a>Rendelje hozzá közvetlenül egy alkalmazás egy csoporthoz

Közvetlenül egy alkalmazás hozzárendelése egy vagy több csoportot, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes alkalmazást.**

6.  Válassza ki szeretné osztani a felhasználót, hogy a listában az alkalmazást.

7.  Ha az alkalmazás betölt, kattintson **felhasználók és csoportok** az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **Hozzáadás** gombra kattint, a a **felhasználók és csoportok** nyissa meg a listában a **hozzáadása hozzárendelés** panelen.

9.  Kattintson a **felhasználók és csoportok** a választó a **hozzáadása hozzárendelés** panelen.

10. Írja be a **teljes csoportnév** érdekli való hozzárendelése a csoport a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. Vigye a **csoport** a listában, hogy láthatóvá váljon a **jelölőnégyzet**. A csoport profilképet vagy adja hozzá a felhasználót emblémát jelölőnégyzetét, kattintson a **kijelölt** listája.

12. **Nem kötelező:** Ha azt szeretné, hogy **egynél több csoport hozzáadása**, egy másik típus **teljes csoportnév** be a **Keresés név vagy e-mail cím alapján** mező, és kattintson a jelölőnégyzetbe, a csoport hozzáadása a **kijelölt** lista.

13. Ha befejezte a csoportok kiválasztásával, kattintson a **válasszon** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazáshoz.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** a választó a **hozzáadása hozzárendelés** panelt, és válassza ki a szerepkör hozzárendelése a kijelölt csoportok.

15. Kattintson a **hozzárendelése** gombra az alkalmazás a kiválasztott csoportok számára.

Rövid ideig a kijelölt felhasználók tudják elindítani ezeket az alkalmazásokat a hozzáférési Panel.

## <a name="next-steps"></a>Következő lépések
[Adja meg az egyszeri bejelentkezés az alkalmazásokba a Proxy](active-directory-application-proxy-sso-using-kcd.md)
