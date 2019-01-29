---
title: A hozzáférési panelen alkalmazáshoz való bejelentkezésnél |} A Microsoft Docs
description: Hogyan háríthatók el a problémák a Microsoft Azure AD hozzáférési Panel itt myapps.microsoft.com az alkalmazáshoz való hozzáférés
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: 529191cf867b6003b0c2eab5ed919087277c7095
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178035"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>A hozzáférési panelen alkalmazáshoz való bejelentkezésnél

A hozzáférési panelen egy webes portál, amely lehetővé teszi, hogy egy felhasználó munkahelyi vagy iskolai fiókkal az Azure Active Directoryban (Azure AD), megtekintheti, és indítsa el a felhőalapú alkalmazásokat, hogy az Azure AD-rendszergazda hozzáférést adott őket. 

Ezeket az alkalmazásokat úgy vannak konfigurálva, az Azure AD portálon a felhasználó nevében. Az alkalmazás megfelelően konfigurálva legyen, és a felhasználó vagy a felhasználó tagja, az alkalmazást a hozzáférési panelen csoport rendelve.

A felhasználó lehet annak, hogy alkalmazások típusa a következő kategóriákba tartoznak:

-   Office 365-alkalmazások

-   A Microsoft és külső alkalmazások összevonási-alapú egyszeri bejelentkezés konfigurálva

-   Jelszavas egyszeri bejelentkezés alkalmazásokhoz

-   Meglévő egyszeri bejelentkezés megoldásokkal rendelkező alkalmazások

## <a name="general-issues-to-check-first"></a>Először ellenőrizze a általános problémák

-   Győződjön meg arról, hogy a használatával egy **böngésző** , amely megfelel a minimális követelményei a hozzáférési panelen.

-   Győződjön meg arról, hogy a felhasználó böngészőjében az URL-címe az alkalmazás hozzá van adva, a **megbízható helyek**.

-   Ellenőrizze, hogy az alkalmazás **konfigurált** megfelelően.

-   Ellenőrizze, hogy a felhasználó fiókjának **engedélyezve** bejelentkezéshez szükséges.

-   Ellenőrizze, hogy a felhasználó fiókjának **nincs zárolva.**

-   Győződjön meg arról, hogy a felhasználó **nem lejárt vagy elfelejtett jelszó.**

-   Győződjön meg arról, hogy **multi-factor Authentication** nem blokkolja a felhasználói hozzáférést.

-   Győződjön meg arról, hogy egy **feltételes hozzáférési szabályzat** vagy **Identity Protection** házirend nem blokkolja a felhasználói hozzáférést.

-   Győződjön meg arról, hogy a felhasználó **hitelesítési kapcsolattartási adatok** naprakésznek, hogy a multi-factor Authentication vagy feltételes hozzáférési házirendek kikényszerítéséhez.

-   Győződjön meg arról, hogy is megpróbálja törölje a böngésző cookie-kat, majd próbálkozzon újra bejelentkezni.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>A hozzáférési Panel a böngészőben követelmények teljesítése

A hozzáférési Panel a böngészőben, amely támogatja a JavaScript szükséges, és CSS engedélyezve van. Jelszavas egyszeri bejelentkezés (SSO) használata a hozzáférési panelen, a felhasználó böngészőjében a hozzáférési Panel bővítményt kell telepíteni. Ezzel a bővítménnyel automatikusan letöltődik, amikor a felhasználó kiválaszt egy alkalmazást, amely konfigurálva van a jelszóalapú egyszeri bejelentkezés.

A jelszóalapú egyszeri bejelentkezés a végfelhasználó böngészők lehet:

-   Az Internet Explorer 8, 9, 10, 11 – a Windows 7 vagy újabb

-   A Microsoft Edge a Windows 10 Évfordulós kiadása vagy újabb

-   Chrome – A Windows 7 vagy újabb, és MacOS X rendszeren vagy újabb

-   A Firefox 26.0 vagy később – a Windows XP SP2 vagy újabb, és a Mac OS X 10,6 vagy újabb

## <a name="how-to-install-the-access-panel-browser-extension"></a>A hozzáférési Panel webböngésző-bővítmény telepítése

A hozzáférési Panel webböngésző-bővítmény telepítéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [hozzáférési Panel](https://myapps.microsoft.com) az egyik támogatott böngészők és való bejelentkezést egy **felhasználói** az Azure AD-ben.

2.  Kattintson egy **jelszó-SSO alkalmazás** a hozzáférési panelen.

3.  Válassza ki a rendszer kéri a szoftver telepítéséhez, **telepítés most**.

4.  A rendszer átirányítja a letöltési hivatkozás böngészőtől függően. **Adjon hozzá** a bővítményt a böngészőben.

5.  Ha a böngésző kéri, válassza ki vagy **engedélyezése** vagy **engedélyezése** a bővítményt.

6.  Miután telepítette, **indítsa újra a** a böngésző-munkamenetet.

7.  Jelentkezzen be a hozzáférési panelre, és tekintse meg, ha a **indítsa el a** a jelszó-SSO-alkalmazások

Az alábbi közvetlen hivatkozások a Chrome és a Microsoft Edge is letöltheti a bővítményt:

-   [Chrome-hozzáférési Panel bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Microsoft Edge Access Panel Extension](https://www.microsoft.com/store/apps/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Összevont egyszeri bejelentkezés az Azure AD katalógusából származó alkalmazásba konfigurálása

Az Azure AD katalógusából származó a vállalati szintű egyszeri bejelentkezés funkció engedélyezve van az alkalmazás összes elérhető egy részletes oktatóanyag rendelkezik. Elérheti a [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) egy részletes részletes útmutatást.

Az Azure AD katalógusából származó kell az alkalmazás konfigurálása:

-   [Az Azure AD katalógusából származó alkalmazás hozzáadása](#add-an-application)

-   [Az alkalmazás metaadatértékeket konfigurálása az Azure ad-ben (bejelentkezési URL-CÍMÉT, az azonosítója, válasz URL-cím)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Válassza ki a felhasználói azonosító és az alkalmazásnak küldendő felhasználói attribútumok hozzáadása](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD-metaadatok és a tanúsítvány lekéréséhez](#download-the-azure-ad-metadata-or-certificate)

-   [Az Azure AD metaadatértékeket konfigurálása az alkalmazásban (bejelentkezési URL-címet, a kiállító, a kijelentkezési URL-címe és a tanúsítvány)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Felhasználók hozzárendelése az alkalmazáshoz](#assign-users-to-the-application)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Az Azure AD katalógusából származó alkalmazás hozzáadása

Az Azure AD katalógusából származó alkalmazás hozzáadásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [az Azure portal](https://portal.azure.com) , és jelentkezzen be egy **globális rendszergazdai** vagy **társadminisztrátor**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **Hozzáadás** gombra a jobb felső sarokban lévő a **vállalati alkalmazások** ablaktáblán.

6.  Az a **adjon meg egy nevet** a szövegmezőbe a **Hozzáadás a katalógusból** területén adja meg az alkalmazás nevét.

7.  Válassza ki az egyszeri bejelentkezést a konfigurálni kívánt alkalmazást.

8.  Ad hozzá az alkalmazást, mielőtt a nevét módosíthatja a **neve** szövegmezőbe.

9.  Kattintson a **Hozzáadás** gombjára, és vegye fel az alkalmazást.

Az alkalmazás konfigurációs panelen megjelenik egy rövid időszak után.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Egyszeri bejelentkezés az Azure AD katalógusából az alkalmazás konfigurálása

Egyszeri bejelentkezés az alkalmazás konfigurálásához kövesse az alábbi lépéseket:

1.  <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazás egyszeri bejelentkezéshez konfigurálandó.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Válassza ki **SAML-alapú bejelentkezés** származó a **mód** legördülő listából.

9.  Adja meg a szükséges értékeket a **tartomány és URL-címeket.** Az alkalmazás gyártójának szerezheti be ezeket az értékeket.

   1. Az alkalmazás konfigurálása SP által kezdeményezett egyszeri Bejelentkezést, a bejelentkezési URL-értéke szükséges. Egyes alkalmazások esetében az azonosító értéke is szükséges.

   2. Konfigurálása az alkalmazás identitásszolgáltató által kezdeményezett egyszeri Bejelentkezést, a válasz URL-cím értéke szükséges. Egyes alkalmazások esetében az azonosító értéke is szükséges.

10. **Választható lehetőség:** kattintson **speciális URL-beállítások megjelenítése** Ha meg szeretné tekinteni a nem szükséges értékeket.

11. Az a **felhasználói attribútumok**, válassza ki a felhasználók az egyedi azonosítója a **felhasználóazonosító** legördülő listából.

12. **Választható lehetőség:** kattintson **megtekintése és egyéb felhasználói attribútumok szerkesztése** kell küldeni az alkalmazás SAML-jogkivonatban, amikor a felhasználók bejelentkeznek az attribútumok szerkesztését.

   Attribútum hozzáadása:

   1. Kattintson a **attribútum hozzáadása**. Adja meg a **neve** , és válassza a a **érték** a legördülő listából.

   2. Kattintson a **mentéséhez.** Megjelenik az új attribútum a táblán.

13. Kattintson a **konfigurálása &lt;alkalmazásnév&gt;**  hozzáférés konfigurálása egyszeri bejelentkezéshez, az alkalmazás dokumentációban. Is hogy a metaadatok URL-címek és az egyszeri bejelentkezés beállítása az alkalmazáshoz szükséges tanúsítvány.

14. Kattintson a **mentése** a konfiguráció mentéséhez.

15. Felhasználók hozzárendelése az alkalmazáshoz.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosító és az alkalmazásnak küldendő felhasználói attribútumok hozzáadása

Válassza ki a felhasználói azonosító, vagy adja hozzá a felhasználói attribútumok, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha nem látja az alkalmazást szeretné-e itt megjelenni, használja a a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást  **Minden alkalmazást.**

6.  Válassza ki az alkalmazás egyszeri bejelentkezésre konfigurálta.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Alatt a **felhasználói attribútumok** területen válassza ki a felhasználók az egyedi azonosítója a **felhasználóazonosító** legördülő listából. A kiválasztott beállítást meg kell felelnie a várt értékkel, az alkalmazás a felhasználó hitelesítéséhez.

    >[!NOTE]
    >Kiválasztott Azure AD válassza ki a formátumot a NameID-attribútum (felhasználóazonosító) érték alapján, vagy formátumát az SAML AuthRequest az alkalmazás által kért. További részletekért látogasson el a cikk [egyszeri bejelentkezéses SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) NameIDPolicy szakaszban.
    >
    >

9.  Felhasználói attribútumok hozzáadásához kattintson **megtekintése és egyéb felhasználói attribútumok szerkesztése** kell küldeni az alkalmazás SAML-jogkivonatban, amikor a felhasználók bejelentkeznek az attribútumok szerkesztését.

   Attribútum hozzáadása:

   1. Kattintson a **attribútum hozzáadása**. Adja meg a **neve** , és válassza a a **érték** a legördülő listából.

   2. Kattintson a **mentéséhez.** Megjelenik az új attribútum a táblán.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD-metaadatok vagy a tanúsítvány letöltése

Az Azure ad-ből az alkalmazás metaadatait, vagy a tanúsítvány letöltéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazás egyszeri bejelentkezésre konfigurálta.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Lépjen a **SAML-aláíró tanúsítvány** területen, majd kattintson a **letöltése** oszlop értékét. Attól függően, melyik az alkalmazás van szüksége, az egyszeri bejelentkezés konfigurálása láthatja az metaadatainak XML-fájl letöltése lehetőséget, vagy a tanúsítványt.

    Az Azure AD nem biztosít a metaadatok beolvasása egy URL-címe. A metaadatok is csak olvasható be XML-fájlba.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Összevont egyszeri bejelentkezés nem katalógusból származó alkalmazásra konfigurálása

Konfigurálhatja egy katalógusban nem szereplő alkalmazást, szüksége lesz a prémium szintű Azure AD és az alkalmazás támogatja az SAML 2.0. Az Azure AD-verziókkal kapcsolatos további információkért látogasson el a [az Azure AD díjszabási](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Az alkalmazás metaadatértékeket konfigurálása az Azure ad-ben (bejelentkezési URL-CÍMÉT, az azonosítója, válasz URL-cím)](#configuring-single-sign-on)

-   [Válassza ki a felhasználói azonosító és az alkalmazásnak küldendő felhasználói attribútumok hozzáadása](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD-metaadatok és a tanúsítvány lekéréséhez](#download-the-azure-ad-metadata-or-certificate)

-   [Az Azure AD metaadatértékeket konfigurálása az alkalmazásban (bejelentkezési URL-címet, a kiállító, a kijelentkezési URL-címe és a tanúsítvány)](#configuring-single-sign-on)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Az alkalmazás metaadatértékeket konfigurálása az Azure ad-ben (bejelentkezési URL-CÍMÉT, az azonosítója, válasz URL-cím)

Konfigurálása egyszeri bejelentkezéshez, amely nem szerepel az Azure AD katalógusából származó alkalmazáshoz, hajtsa végre az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **Hozzáadás** gombra a jobb felső sarokban lévő a **vállalati alkalmazások** ablaktáblán.

6.  Kattintson a **katalógusban nem szereplő alkalmazás** a a **saját alkalmazás hozzáadása** szakaszban.

7.  Adja meg az alkalmazás nevét a **neve** szövegmezőbe.

8.  Kattintson a **Hozzáadás** gombjára, és vegye fel az alkalmazást.

9.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

10. Válassza ki **SAML-alapú bejelentkezés** a a **mód** legördülő menü

11. Adja meg a szükséges értékeket a **tartomány és URL-címeket.** Az alkalmazás gyártójának szerezheti be ezeket az értékeket.

  1. Az alkalmazás identitásszolgáltató által kezdeményezett egyszeri Bejelentkezést, konfigurálását, adja meg a válasz URL-cím és az azonosító.

  2. **Nem kötelező:** Az alkalmazás konfigurálása SP által kezdeményezett egyszeri Bejelentkezést, a bejelentkezési URL-értéke szükséges.

12. Az a **felhasználói attribútumok**, válassza ki a felhasználók az egyedi azonosítója a **felhasználóazonosító** legördülő listából.

13. **Választható lehetőség:** kattintson **megtekintése és egyéb felhasználói attribútumok szerkesztése** kell küldeni az alkalmazás SAML-jogkivonatban, amikor a felhasználók bejelentkeznek az attribútumok szerkesztését.

   Attribútum hozzáadása:

   1. Kattintson a **attribútum hozzáadása**. Adja meg a **neve** , és válassza a a **érték** a legördülő listából.

   2. Kattintson a **mentéséhez.** Megjelenik az új attribútum a táblán.

14. Kattintson a **konfigurálása &lt;alkalmazásnév&gt;**  hozzáférés konfigurálása egyszeri bejelentkezéshez, az alkalmazás dokumentációban. Is hogy az Azure AD URL-címek és az alkalmazás számára szükséges tanúsítványt.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosító és az alkalmazásnak küldendő felhasználói attribútumok hozzáadása

Válassza ki a felhasználói azonosító, vagy adja hozzá a felhasználói attribútumok, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazás egyszeri bejelentkezésre konfigurálta.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Alatt a **felhasználói attribútumok** területen válassza ki a felhasználók az egyedi azonosítója a **felhasználóazonosító** legördülő listából. A kiválasztott beállítást meg kell felelnie a várt értékkel, az alkalmazás a felhasználó hitelesítéséhez.

   >[!NOTE]
   >Kiválasztott Azure AD válassza ki a formátumot a NameID-attribútum (felhasználóazonosító) érték alapján, vagy formátumát az SAML AuthRequest az alkalmazás által kért. További részletekért látogasson el a cikk [egyszeri bejelentkezéses SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) NameIDPolicy szakaszban.
   >
   >

9.  Felhasználói attribútumok hozzáadásához kattintson **megtekintése és egyéb felhasználói attribútumok szerkesztése** kell küldeni az alkalmazás SAML-jogkivonatban, amikor a felhasználók bejelentkeznek az attribútumok szerkesztését.

   Attribútum hozzáadása:

   1. Kattintson a **attribútum hozzáadása**. Adja meg a **neve** , és válassza a a **érték** a legördülő listából.

   2 kattintson **mentéséhez.** Megjelenik az új attribútum a táblán.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD-metaadatok vagy a tanúsítvány letöltése

Az Azure ad-ből az alkalmazás metaadatait, vagy a tanúsítvány letöltéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazás egyszeri bejelentkezésre konfigurálta.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Lépjen a **SAML-aláíró tanúsítvány** területen, majd kattintson a **letöltése** oszlop értékét. Attól függően, melyik az alkalmazás van szüksége, az egyszeri bejelentkezés konfigurálása láthatja az metaadatainak XML-fájl letöltése lehetőséget, vagy a tanúsítványt.

    Az Azure AD nem biztosít a metaadatok beolvasása egy URL-címe. A metaadatok is csak olvasható be XML-fájlba.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jelszó egyszeri bejelentkezés az Azure AD katalógusából származó alkalmazásba konfigurálása

Az Azure AD katalógusából származó kell az alkalmazás konfigurálása:

-   [Az Azure AD katalógusából származó alkalmazás hozzáadása](#add-an-application)

-   [Az alkalmazás a jelszavas egyszeri bejelentkezés konfigurálása](#configure-the-application)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Az Azure AD katalógusából származó alkalmazás hozzáadása

Az Azure AD katalógusából származó alkalmazás hozzáadásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [az Azure portal](https://portal.azure.com) , és jelentkezzen be egy **globális rendszergazdai** vagy **társadminisztrátor**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **Hozzáadás** gombra a jobb felső sarokban lévő a **vállalati alkalmazások** ablaktáblán.

6.  Az a **adjon meg egy nevet** a szövegmezőbe a **Hozzáadás a katalógusból** területén adja meg az alkalmazás nevét

7.  Válassza ki az alkalmazást szeretne konfigurálni az egyszeri bejelentkezés

8.  Ad hozzá az alkalmazást, mielőtt a nevét módosíthatja a **neve** szövegmezőbe.

9.  Kattintson a **Hozzáadás** gombjára, és vegye fel az alkalmazást.

Az alkalmazás konfigurációs panelen megjelenik egy rövid időszak után.

### <a name="configure-the-application-for-password-single-sign-on"></a>Az alkalmazás a jelszavas egyszeri bejelentkezés konfigurálása

Egyszeri bejelentkezés az alkalmazás konfigurálásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

 * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazást szeretné az egyszeri bejelentkezés konfigurálása

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Válassza ki a módot **jelszóalapú bejelentkezés.**

9.  Felhasználók hozzárendelése az alkalmazáshoz.

10. Ezenkívül is megadhatja a felhasználó nevében legyenek hitelesítő adatok a sorok, a felhasználók kiválasztva, majd kattintson a **a hitelesítő adatok frissítése** és a felhasználónév és jelszó megadásával a felhasználók nevében. Ellenkező esetben megkérdezi a felhasználókat a hitelesítő adatokat magukat az indítás után.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jelszavas egyszeri bejelentkezés nem katalógusból származó alkalmazásra konfigurálása

Az Azure AD katalógusából származó kell az alkalmazás konfigurálása:

-   [A katalógusban nem szereplő alkalmazás hozzáadása](#add-a-non-gallery-application)

-   [Az alkalmazás a jelszavas egyszeri bejelentkezés konfigurálása](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>A katalógusban nem szereplő alkalmazás hozzáadása

Az Azure AD katalógusából származó alkalmazás hozzáadásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [az Azure portal](https://portal.azure.com) , és jelentkezzen be egy **globális rendszergazdai** vagy **társadminisztrátor**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **Hozzáadás** gombra a jobb felső sarokban lévő a **vállalati alkalmazások** ablaktáblán.

6.  Kattintson a **katalógusban nem szereplő alkalmazást.**

7.  Adja meg az alkalmazás nevét a **neve** szövegmezőbe. Válassza ki **hozzáadása.**

Egy rövid időszak után megtekintheti az alkalmazás konfigurációs panelen lehet.

### <a name="configure-the-application-for-password-single-sign-on"></a>Az alkalmazás a jelszavas egyszeri bejelentkezés konfigurálása

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

9.  Adja meg a **bejelentkezési URL-**. Ez az URL-CÍMÉT, írja be a felhasználók a felhasználónevével és jelszavával bejelentkezni. Győződjön meg arról, a bejelentkezési mezők láthatók az URL-címen.

10. Felhasználók hozzárendelése az alkalmazáshoz.

11. Ezenkívül is megadhatja a felhasználó nevében legyenek hitelesítő adatok a sorok, a felhasználók kiválasztva, majd kattintson a **a hitelesítő adatok frissítése** és a felhasználónév és jelszó megadásával a felhasználók nevében. Ellenkező esetben megkérdezi a felhasználókat a hitelesítő adatokat magukat az indítás után.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Közvetlenül a felhasználó hozzárendelése alkalmazáshoz

Közvetlenül rendelni egy vagy több felhasználó egy alkalmazást, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7.  Ha az alkalmazás betöltött, kattintson a **felhasználók és csoportok** az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **Hozzáadás** a gombot a **felhasználók és csoportok** listán, megnyílik a **hozzárendelés hozzáadása** ablaktáblán.

9.  Kattintson a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

10. Írja be a **teljes név** vagy **e-mail-cím** Önt érdeklő való hozzárendelése a felhasználó a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. A kurzort a **felhasználói** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a felhasználó profilfényképének vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

12. **Nem kötelező:** Ha szeretné **egynél több felhasználó hozzáadása**, írjon be egy másik **teljes név** vagy **e-mail-cím** be a **Keresés név vagy e-mail cím alapján** keresőmezőbe, majd kattintson a jelölőnégyzet bejelölésével adja hozzá a felhasználót a **kijelölt** listája.

13. Amikor elkészült, válassza a felhasználók, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

15. Kattintson a **hozzárendelése** gombra kattintva rendelje hozzá az alkalmazás a kijelölt felhasználók számára.

Rövid ideig a kijelölt felhasználók tudják elindítani ezeket az alkalmazásokat a hozzáférési panelen.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Ha ezeket a hibaelhárítási lépéseket végre nem a hárítsa el a problémát

Nyisson meg egy támogatási jegyet a következő adatokat, ha rendelkezésre áll:

-   Megfelelési hiba azonosítója

-   Egyszerű felhasználónév (felhasználó e-mail-címe)

-   TenantID

-   Böngésző típusa

-   Időzóna és idő/időkeret során hiba történik.

-   Fiddler-nyomkövetés

## <a name="next-steps"></a>További lépések
[Az alkalmazásproxy egyszeri bejelentkezést az alkalmazásokba biztosít](application-proxy-configure-single-sign-on-with-kcd.md)

