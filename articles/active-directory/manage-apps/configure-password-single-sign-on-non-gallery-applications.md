---
title: Jelszó egyszeri bejelentkezést a katalógusban nem szereplő applicationn a konfigurálása |} A Microsoft Docs
description: Egyéni katalógusban nem szereplő alkalmazás biztonságos jelszavas egyszeri bejelentkezés konfigurálása, ha nem szerepel az Azure AD Alkalmazáskatalógusában
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 37ac3b5fb680a9966f5b8f3da43a2b3013554557
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356640"
---
# <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jelszavas egyszeri bejelentkezés nem katalógusból származó alkalmazásra konfigurálása

Mellett a lehetőségek, az Azure AD Alkalmazáskatalógusában belül található, akkor is lehetőséget ad egy **katalógusban nem szereplő alkalmazás** mikor kívánt alkalmazás nem szerepel ott. Ezzel a funkcióval hozzáadhat minden olyan alkalmazás, amely a szervezet már létezik, vagy bármely harmadik fél alkalmazás, amelyet esetleg olyan szállítótól, aki nem már része a [az Azure AD Alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery).

Miután hozzáadta a katalógusban nem szereplő alkalmazást, majd konfigurálhatja az egyszeri bejelentkezési módszer kiválasztásával használja ezt az alkalmazást a **egyszeri bejelentkezés** navigációs elem a vállalati alkalmazások a [azAzureportal](https://portal.azure.com/).

Egyszeri bejelentkezés módszer érhető el, hogy a rendszer a [jelszóalapú egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) lehetőséget. Az a **vegye fel a katalógusban nem szereplő alkalmazást** élmény, bármilyen alkalmazás, amely egy HTML-alapú felhasználónév rendereli integrálható, és a jelszót a mezőben adjon meg akkor is, ha nem szerepel az előre integrált alkalmazások készlete.

Az is működik mindez úgy, hogy egy oldalt automatizované získávání dat technológia, amely része a hozzáférési Panel bővítményt, amely lehetővé teszi számunkra, hogy a felhasználónév és jelszó bemeneti mezők automatikusan észlelni, az adott alkalmazáspéldány esetében biztonságosan tárolja őket. Ezután biztonságos visszajátszani felhasználóneveket és jelszavakat ezekhez a mezőkhöz amikor a felhasználók csak az adott alkalmazást, az alkalmazás-hozzáférési panelen.

Ez ideális megoldás a kezdéshez bármilyen típusú alkalmazásban gyorsan integrálása az Azure AD-be, és lehetővé teszi, hogy:

-   Integráció **a világ bármely alkalmazás** együtt az Azure AD-bérlőjében, amennyiben vártak egy HTML formátumú felhasználónévvel és jelszóval beviteli mező

-   Engedélyezése **egyszeri bejelentkezést a felhasználók** való biztonságos tárolása és felhasználónevek és jelszavak az alkalmazás visszajátszásával integrált már az Azure ad-vel

-   **Automatikus észlelésű bemeneti** bármely alkalmazás mezőket, és lehetővé teszi ezeket a mezőket a hozzáférési Panel Böngészőbővítményének használatánál, manuális észlelése, abban az esetben automatikus észlelés nem található

-   **Több bejelentkezési mezők igénylő alkalmazások támogatásához** több, mint felhasználónév és jelszó megadására való bejelentkezéshez igénylő alkalmazásokhoz

-   **A feliratok testreszabása** a felhasználók felhasználónév és jelszó bemeneti mező a [alkalmazás-hozzáférési Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) amikor belép a hitelesítő adatok

-   Lehetővé teszi a **felhasználók** biztosít a saját felhasználónevek és jelszavak között, vannak írja be manuálisan a meglévő alkalmazás fiókok számára a [alkalmazás-hozzáférési Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Lehetővé teszi egy **az üzleti csoport tagjai** a felhasználónevek és jelszavak használatával a felhasználóhoz való megadásához a [önkiszolgáló alkalmazás-hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) funkció

-   Lehetővé teszi egy **rendszergazda** adja meg, hogy a felhasználónevek és jelszavak a frissítés hitelesítő adatok használatával a felhasználóhoz rendelt funkciót [felhasználó hozzárendelése egy alkalmazáshoz](#_How_to_configure_1)

-   Lehetővé teszi egy **rendszergazda** adja meg a megosztott felhasználónév vagy jelszó a frissítési hitelesítő adatok használatával egy csoport tagjainak által használt funkciót [csoport hozzárendelése egy alkalmazáshoz](#assign-an-application-to-a-group-directly)

Az alábbi szakasz ismerteti, hogyan engedélyezheti [jelszóalapú egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) bármely alkalmazás használatával adja hozzá a **vegye fel a katalógusban nem szereplő alkalmazást** élményt.

## <a name="overview-of-steps-required"></a>Szükséges lépések áttekintése

Az Azure AD katalógusából származó kell az alkalmazás konfigurálása:

-   [A katalógusban nem szereplő alkalmazás hozzáadása](#add-a-non-gallery-application)

-   [Az alkalmazás a jelszavas egyszeri bejelentkezés konfigurálása](#configure-the-application-for-password-single-sign-on)

-   [Az alkalmazás egy felhasználó vagy csoport hozzárendelése](#assign-the-application-to-a-user-or-a-group)

    -   [Közvetlenül a felhasználó hozzárendelése egy alkalmazáshoz](#assign-a-user-to-an-application-directly)

    -   [Közvetlenül egy csoportba alkalmazás hozzárendelése](#assign-an-application-to-a-group-directly)

## <a name="add-a-non-gallery-application"></a>A katalógusban nem szereplő alkalmazás hozzáadása

Az Azure AD katalógusából származó alkalmazás hozzáadásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [az Azure portal](https://portal.azure.com) , és jelentkezzen be egy **globális rendszergazdai** vagy **társadminisztrátor**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **Hozzáadás** gombra a jobb felső sarokban lévő a **vállalati alkalmazások** ablaktáblán.

6.  Kattintson a **katalógusban nem szereplő alkalmazás**.

7.  Adja meg az alkalmazás nevét a **neve** szövegmezőbe. Válassza ki **hozzáadása.**

Egy rövid időszak után megtekintheti az alkalmazás konfigurációs panelen lehet.

## <a name="configure-the-application-for-password-single-sign-on"></a>Az alkalmazás a jelszavas egyszeri bejelentkezés konfigurálása

Egyszeri bejelentkezés az alkalmazás konfigurálásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazás egyszeri bejelentkezéshez konfigurálandó.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Válassza ki a módot **jelszóalapú bejelentkezés.**

9.  Adja meg a **bejelentkezési URL-**. Ez az az URL-címet, adja meg a felhasználók a felhasználónevével és jelszavával bejelentkezni. Győződjön meg arról, a bejelentkezési mezők láthatók az URL-címen.

10. Felhasználók hozzárendelése az alkalmazáshoz.

11. Ezenkívül is megadhatja a felhasználó nevében legyenek hitelesítő adatok a sorok, a felhasználók kiválasztva, majd kattintson a **a hitelesítő adatok frissítése** és a felhasználónév és jelszó megadásával a felhasználók nevében. Ellenkező esetben megkérdezi a felhasználókat a hitelesítő adatokat magukat az indítás után.

12. **Választható lehetőség:** bizonyos közösségi média alkalmazások, például a Twitteren és a Facebookon, van a lehetőség a jelszó az alkalmazás a kijelölt gyakorisággal automatikus átvitel engedélyezése. Ahhoz, hogy ez válassza **szeretnék az Azure ad-ben az automatikus kezelésére, ez a felhasználó vagy csoport jelszavát** nevében egy felhasználó vagy csoport hitelesítő adatok megadása közben. Válassza ki a **váltás gyakorisága (hét)**.

## <a name="assign-a-user-to-an-application-directly"></a>Közvetlenül a felhasználó hozzárendelése egy alkalmazáshoz

Közvetlenül rendelni egy vagy több felhasználó egy alkalmazást, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7.  Ha az alkalmazás betöltött, kattintson a **felhasználók és csoportok** az alkalmazás bal oldali navigációs menüjében.

8.  Megnyitásához a **hozzárendelés hozzáadása** ablaktáblán kattintson a **Hozzáadás** a gomb a **felhasználók és csoportok** listája.

9.  Kattintson a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

10. Írja be a **teljes név** vagy **e-mail-cím** Önt érdeklő való hozzárendelése a felhasználó a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. A kurzort a **felhasználói** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a felhasználó profilfényképének vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

12. **Választható lehetőség:** Ha szeretné **egynél több felhasználó hozzáadása**, írjon be egy másik **teljes név** vagy **e-mail-cím** be a **Keresés név alapján, vagy e-mail-cím** keresőmezőbe, majd kattintson a jelölőnégyzet bejelölésével adja hozzá a felhasználót a **kijelölt** listája.

13. Amikor elkészült, válassza a felhasználók, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

15. Kattintson a **hozzárendelése** gombra kattintva rendelje hozzá az alkalmazás a kijelölt felhasználók számára.

## <a name="assign-an-application-to-a-group-directly"></a>Közvetlenül egy csoportba alkalmazás hozzárendelése

Közvetlenül egy alkalmazás hozzárendelése egy vagy több csoportot, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7.  Ha az alkalmazás betöltött, kattintson a **felhasználók és csoportok** az alkalmazás bal oldali navigációs menüjében.

8.  Megnyitásához a **hozzárendelés hozzáadása** ablaktáblán kattintson a **Hozzáadás** a gomb a **felhasználók és csoportok** listája.

9.  Kattintson a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

10. Írja be a **teljes csoportnév** a csoport Önt érdeklő való hozzárendelése a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. A kurzort a **csoport** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a csoport profilfénykép, vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

12. **Választható lehetőség:** Ha szeretné **egynél több csoport hozzáadása**, írjon be egy másik **teljes csoportnév** be a **Keresés név vagy e-mail cím alapján** keresőmezőbe, és a jelölőnégyzet bejelölésével adja hozzá a csoport a **kijelölt** listája.

13. Ha befejezte a csoportok kiválasztásával, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** ablaktáblán válassza ki a szerepkör hozzárendelése a kiválasztott csoportokhoz.

15. Kattintson a **hozzárendelése** gombra az alkalmazás a kijelölt csoportokhoz hozzárendelni.

Rövid ideig a kijelölt felhasználók tudják elindítani ezeket az alkalmazásokat a hozzáférési panelen.

## <a name="next-steps"></a>További lépések
[Az alkalmazásproxy egyszeri bejelentkezést az alkalmazásokba biztosít](application-proxy-configure-single-sign-on-with-kcd.md)
