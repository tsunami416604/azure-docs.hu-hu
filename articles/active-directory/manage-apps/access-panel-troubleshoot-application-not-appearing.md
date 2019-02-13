---
title: Egy hozzárendelt alkalmazás nem jelenik meg a hozzáférési panelen |} A Microsoft Docs
description: Egy alkalmazás nem jelenik meg a hozzáférési panelen okozó hibák elhárításához
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2018
ms.author: celested
ms.reviwer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21ed169003caded8ef80689ceec61f6d2fc4305c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178991"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Egy hozzárendelt alkalmazás nem jelenik meg a hozzáférési panelen

A hozzáférési panelen egy webes portál, amely lehetővé teszi, hogy egy felhasználó munkahelyi vagy iskolai fiókkal az Azure Active Directoryban (Azure AD), megtekintheti, és indítsa el a felhőalapú alkalmazásokat, hogy az Azure AD-rendszergazda hozzáférést adott őket. Ezeket az alkalmazásokat úgy vannak konfigurálva, az Azure AD portálon a felhasználó nevében. Az alkalmazás megfelelően konfigurálva legyen, és a felhasználó vagy a felhasználó tagja, az alkalmazást a hozzáférési panelen csoport rendelve.

A felhasználó lehet annak, hogy alkalmazások típusa a következő kategóriákba tartoznak:

-   Office 365-alkalmazások

-   A Microsoft és külső alkalmazások összevonási-alapú egyszeri bejelentkezés konfigurálva

-   Jelszavas egyszeri bejelentkezés alkalmazásokhoz

-   Meglévő egyszeri bejelentkezés megoldásokkal rendelkező alkalmazások

## <a name="general-issues-to-check-first"></a>Először ellenőrizze a általános problémák

-   Ha egy alkalmazás csak adott felhasználó, próbáljon meg bejelentkezni be és ki újra a felhasználó hozzáférési panelre néhány perc elteltével megtekintheti, ha az alkalmazás hozzáadása.

-   Ha a licenc csak a felhasználó vagy csoport, a felhasználó nem tagja, ez eltarthat egy ideig, a mérete és összetettsége kell tenni a módosításokat a csoport el lett távolítva. Lehetővé teszi további időpont előtt jelentkezzen be a hozzáférési panelen.

## <a name="problems-related-to-application-configuration"></a>Az alkalmazáskonfigurációval kapcsolatos problémák

Egy alkalmazás előfordulhat, hogy nem jelennek meg a felhasználó hozzáférési panelen, mert nem megfelelően van konfigurálva. Az alábbiakban néhány módszer alkalmazáskonfigurációval kapcsolatos problémák:

-   [Összevont egyszeri bejelentkezés az Azure AD katalógusából származó alkalmazásba konfigurálása](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Összevont egyszeri bejelentkezés nem katalógusból származó alkalmazásra konfigurálása](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Jelszó egyetlen bejelentkezési kérelmet az Azure AD katalógusából származó alkalmazásba konfigurálása](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Hogyan lehet egy állítson be jelszót egyszeri bejelentkezés nem katalógusból származó alkalmazásra](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Összevont egyszeri bejelentkezés az Azure AD katalógusából származó alkalmazásba konfigurálása

A vállalati szintű egyszeri bejelentkezés funkció engedélyezve az Azure AD-katalógusban szereplő összes alkalmazás rendelkezik elérhető részletes oktatóanyaga. Elérheti a [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) egy részletes részletes útmutatást.

Az Azure AD katalógusából származó kell az alkalmazás konfigurálása:

-   [Az Azure AD katalógusából származó alkalmazás hozzáadása](#add-an-application-from-the-azure-ad-gallery)

-   [Az alkalmazás metaadatértékeket konfigurálása az Azure ad-ben (bejelentkezési URL-CÍMÉT, az azonosítója, válasz URL-cím)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Válassza ki a felhasználói azonosító és az alkalmazásnak küldendő felhasználói attribútumok hozzáadása](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD-metaadatok és a tanúsítvány lekéréséhez](#download-the-azure-ad-metadata-or-certificate)

-   [Az Azure AD metaadatértékeket konfigurálása az alkalmazásban (bejelentkezési URL-címet, a kiállító, a kijelentkezési URL-címe és a tanúsítvány)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Az Azure AD katalógusából származó alkalmazás hozzáadása

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

Egy rövid időszak után megtekintheti az alkalmazás konfigurációs panelen lehet.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Egyszeri bejelentkezés az Azure AD katalógusából az alkalmazás konfigurálása

Egyszeri bejelentkezés az alkalmazás konfigurálásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazás egyszeri bejelentkezéshez konfigurálandó.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Válassza ki **SAML-alapú bejelentkezés** származó a **mód** legördülő listából.

9.  Adja meg a szükséges értékeket a **tartomány és URL-címeket.** Az alkalmazás gyártójának szerezheti be ezeket az értékeket.

   1. Az alkalmazás konfigurálásához az SP által kezdeményezett egyszeri Bejelentkezést, a bejelentkezési URL-cím, az értéke egy szükséges. Egyes alkalmazások esetében az azonosító értéke is szükséges.

   2. Az alkalmazás identitásszolgáltató által kezdeményezett egyszeri Bejelentkezést, a válasz URL-cím konfigurálása a szükséges érték. Egyes alkalmazások esetében az azonosító értéke is szükséges.

10. **Választható lehetőség:** kattintson **speciális URL-beállítások megjelenítése** Ha meg szeretné tekinteni a nem szükséges értékeket.

11. Az a **felhasználói attribútumok**, válassza ki a felhasználók az egyedi azonosítója a **felhasználóazonosító** legördülő listából.

12. **Választható lehetőség:** kattintson **megtekintése és egyéb felhasználói attribútumok szerkesztése** kell küldeni az alkalmazás SAML-jogkivonatban, amikor a felhasználók bejelentkeznek az attribútumok szerkesztését.

   Attribútum hozzáadása:

   1. Kattintson a **attribútum hozzáadása**. Adja meg a **neve** , és válassza a a **érték** a legördülő listából.

   2. Kattintson a **mentéséhez.** Megjelenik az új attribútum a táblán.

13. Kattintson a **konfigurálása &lt;alkalmazásnév&gt;**  hozzáférés konfigurálása egyszeri bejelentkezéshez, az alkalmazás dokumentációban. Is hogy a metaadatok URL-címek és az egyszeri bejelentkezés beállítása az alkalmazáshoz szükséges tanúsítvány.

14. Kattintson a **mentése** a konfiguráció mentéséhez.

15. Felhasználók hozzárendelése az alkalmazáshoz.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosító és az alkalmazásnak küldendő felhasználói attribútumok hozzáadása

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
   >Az Azure AD a kiválasztott érték alapján a NameID-attribútum (felhasználóazonosító) formátumát vagy formátumát az SAML AuthRequest az alkalmazás által kért jelöl ki. További részletekért látogasson el a cikk [egyszeri bejelentkezéses SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) NameIDPolicy szakaszban.
   >
   >

9.  Felhasználói attribútumok hozzáadásához kattintson **megtekintése és egyéb felhasználói attribútumok szerkesztése** kell küldeni az alkalmazás SAML-jogkivonatban, amikor a felhasználók bejelentkeznek az attribútumok szerkesztését.

   Attribútum hozzáadása:

   1. Kattintson a **attribútum hozzáadása**. Adja meg a **neve** , és válassza a a **érték** a legördülő listából.

   2. Kattintson a **mentéséhez.** Látni fogja a táblázatban az új attribútumot.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD-metaadatok vagy a tanúsítvány letöltése

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

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Összevont egyszeri bejelentkezés nem katalógusból származó alkalmazásra konfigurálása

Konfigurálhatja egy katalógusban nem szereplő alkalmazást, szüksége lesz a prémium szintű Azure AD és az alkalmazás támogatja az SAML 2.0. Az Azure AD-verziókkal kapcsolatos további információkért látogasson el a [az Azure AD díjszabási](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Az alkalmazás metaadatértékeket konfigurálása az Azure ad-ben (bejelentkezési URL-CÍMÉT, az azonosítója, válasz URL-cím)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Válassza ki a felhasználói azonosító és az alkalmazásnak küldendő felhasználói attribútumok hozzáadása](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD-metaadatok és a tanúsítvány lekéréséhez](#download-the-azure-ad-metadata-or-certificate)

-   [Az Azure AD metaadatértékeket konfigurálása az alkalmazásban (bejelentkezési URL-címet, a kiállító, a kijelentkezési URL-címe és a tanúsítvány)](#configure-the-application-for-password-single-sign-on-1)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Az alkalmazás metaadatértékeket konfigurálása az Azure ad-ben (bejelentkezési URL-CÍMÉT, az azonosítója, válasz URL-cím)

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

10. Válassza ki **SAML-alapú bejelentkezés** a a **mód** legördülő listából.

11. Adja meg a szükséges értékeket a **tartomány és URL-címeket.** Az alkalmazás gyártójának szerezheti be ezeket az értékeket.

   1. Az alkalmazás identitásszolgáltató által kezdeményezett egyszeri Bejelentkezést, konfigurálását, adja meg a válasz URL-cím és az azonosító.

   2.  **Nem kötelező:** Az alkalmazás konfigurálásához az SP által kezdeményezett egyszeri Bejelentkezést, a bejelentkezési URL-cím, az értéke egy szükséges.

12. Az a **felhasználói attribútumok**, válassza ki a felhasználók az egyedi azonosítója a **felhasználóazonosító** legördülő listából.

13. **Választható lehetőség:** kattintson **megtekintése és egyéb felhasználói attribútumok szerkesztése** kell küldeni az alkalmazás SAML-jogkivonatban, amikor a felhasználók bejelentkeznek az attribútumok szerkesztését.

   Attribútum hozzáadása:

   1. Kattintson a **attribútum hozzáadása**. Adja meg a **neve** , és válassza a a **érték** a legördülő listából.

   2. Kattintson a **mentéséhez.** Megjelenik az új attribútum a táblán.

14. Kattintson a **konfigurálása &lt;alkalmazásnév&gt;**  hozzáférés konfigurálása egyszeri bejelentkezéshez, az alkalmazás dokumentációban. Is hogy az Azure AD URL-címek és az alkalmazáshoz szükséges tanúsítványokat.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosító és az alkalmazásnak küldendő felhasználói attribútumok hozzáadása

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
   >Az Azure AD a kiválasztott érték alapján a NameID-attribútum (felhasználóazonosító) formátumát vagy formátumát az SAML AuthRequest az alkalmazás által kért jelöl ki. További részletekért látogasson el a cikk [egyszeri bejelentkezéses SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) NameIDPolicy szakaszban.
   >
   >

9.  Felhasználói attribútumok hozzáadásához kattintson **megtekintése és egyéb felhasználói attribútumok szerkesztése** kell küldeni az alkalmazás SAML-jogkivonatban, amikor a felhasználók bejelentkeznek az attribútumok szerkesztését.

   Attribútum hozzáadása:

   1. Kattintson a **attribútum hozzáadása**. Adja meg a **neve** , és válassza a a **érték** a legördülő listából.

   2. Kattintson a **mentéséhez.** Megjelenik az új attribútum a táblán.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD-metaadatok vagy a tanúsítvány letöltése

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

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jelszó egyszeri bejelentkezés az Azure AD katalógusából származó alkalmazásba konfigurálása

Az Azure AD katalógusából származó kell az alkalmazás konfigurálása:

-   [Az Azure AD katalógusából származó alkalmazás hozzáadása](#add-an-application-from-the-azure-ad-gallery)

-   [Az alkalmazás a jelszavas egyszeri bejelentkezés konfigurálása](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Az Azure AD katalógusából származó alkalmazás hozzáadása

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

#### <a name="configure-the-application-for-password-single-sign-on"></a>Az alkalmazás a jelszavas egyszeri bejelentkezés konfigurálása

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

9.  [Felhasználók hozzárendelése az alkalmazás](#how-to-assign-a-user-to-an-application-directly).

10. Ezenkívül is megadhatja a felhasználó nevében legyenek hitelesítő adatok a sorok, a felhasználók kiválasztva, majd kattintson a **a hitelesítő adatok frissítése** és a felhasználónév és jelszó megadásával a felhasználók nevében. Ellenkező esetben megkérdezi a felhasználókat a hitelesítő adatokat magukat az indítás után.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jelszavas egyszeri bejelentkezés nem katalógusból származó alkalmazásra konfigurálása

Az Azure AD katalógusából származó kell az alkalmazás konfigurálása:

-   [A katalógusban nem szereplő alkalmazás hozzáadása](#add-a-non-gallery-application)

-   [Az alkalmazás a jelszavas egyszeri bejelentkezés konfigurálása](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>A katalógusban nem szereplő alkalmazás hozzáadása

Az Azure AD katalógusából származó alkalmazás hozzáadásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [az Azure portal](https://portal.azure.com) , és jelentkezzen be egy **globális rendszergazdai** vagy **társadminisztrátor**.

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **Hozzáadás** gombra a jobb felső sarokban lévő a **vállalati alkalmazások** ablaktáblán.

6.  Kattintson a **katalógusban nem szereplő alkalmazást.**

7.  Adja meg az alkalmazás nevét a **neve** szövegmezőbe. Válassza ki **hozzáadása.**

Egy rövid időszak után megtekintheti az alkalmazás konfigurációs panelen lehet.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Az alkalmazás a jelszavas egyszeri bejelentkezés konfigurálása

Egyszeri bejelentkezés az alkalmazás konfigurálásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

    1.  Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazás egyszeri bejelentkezéshez konfigurálandó.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Válassza ki a módot **jelszóalapú bejelentkezés.**

9.  Adja meg a **bejelentkezési URL-**. Ez az az URL-címet, adja meg a felhasználók a felhasználónevével és jelszavával bejelentkezni. Győződjön meg arról, a bejelentkezési mezők láthatók az URL-címen.

10. [Felhasználók hozzárendelése az alkalmazás](#how-to-assign-a-user-to-an-application-directly).

11. Ezenkívül is megadhatja a felhasználó nevében legyenek hitelesítő adatok a sorok, a felhasználók kiválasztva, majd kattintson a **a hitelesítő adatok frissítése** és a felhasználónév és jelszó megadásával a felhasználók nevében. Ellenkező esetben megkérdezi a felhasználókat a hitelesítő adatokat magukat az indítás után.

## <a name="problems-related-to-assigning-applications-to-users"></a>A felhasználók számára alkalmazások hozzárendelése kapcsolatos problémák

A felhasználó nem lehet annak, hogy egy alkalmazás a hozzáférési panelen láthatnak, mert az nem kap az alkalmazáshoz. Az alábbiakban néhány módszer ellenőrzése:

-   [Ha egy felhasználó hozzá van rendelve az alkalmazás ellenőrzése](#check-if-a-user-is-assigned-to-the-application)

-   [Közvetlenül a felhasználó hozzárendelése alkalmazáshoz](#how-to-assign-a-user-to-an-application-directly)

-   [Ellenőrizze, hogy ha egy felhasználó hozzá van rendelve az alkalmazáshoz kapcsolódó licenc](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Hogyan lehet licencet hozzárendelni egy felhasználóhoz](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Ha egy felhasználó hozzá van rendelve az alkalmazás ellenőrzése

Ha egy felhasználó hozzá van rendelve az alkalmazás ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

6.  **Keresés** a kérdéses alkalmazás neve.

7.  Kattintson a **felhasználók és csoportok**.

8.  Ellenőrizze, hogy ha a felhasználó az alkalmazáshoz van hozzárendelve.

   * Ha nem, kövesse az "Útmutató a felhasználó hozzárendelése alkalmazáshoz közvetlenül" ehhez.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Közvetlenül a felhasználó hozzárendelése alkalmazáshoz

Közvetlenül rendelni egy vagy több felhasználó egy alkalmazást, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai**.

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

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Ellenőrizze, hogy van-e egy felhasználó egy licenc, az alkalmazással kapcsolatos

A felhasználó hozzárendelt licencek ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amelyek licencek, a felhasználó jelenleg hozzá van rendelve.

  * Ha a felhasználó hozzá van rendelve egy Office-licencet, ez lehetővé teszi, hogy a felhasználó hozzáférési panelen megjelenő első fél Office alkalmazások.

### <a name="how-to-assign-a-user-a-license"></a>A felhasználói licenc hozzárendelése 

Licenc hozzárendelése egy felhasználóhoz, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amelyek licencek, a felhasználó jelenleg hozzá van rendelve.

8.  Kattintson a **hozzárendelése** gombra.

9.  Válassza ki **egy vagy több termék** elérhető termékek listájában.

10. **Nem kötelező** kattintson a **hozzárendelési beállítások** rendeljen kínálja a termékek az elemet. Kattintson a **Ok** amikor a művelet befejeződött.

11. Kattintson a **hozzárendelése** gomb ezek a licencek hozzárendelése a felhasználóhoz.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Az alkalmazások csoportokhoz való hozzárendelésével kapcsolatos problémák

A felhasználó lehet annak, hogy egy alkalmazás a hozzáférési panelen láthatnak, mert egy csoportot, amely az alkalmazás részét képezik. Az alábbiakban néhány módszer ellenőrzése:

-   [A vendégfelhasználói csoporttagságok ellenőrzése](#check-a-users-group-memberships)

-   [Az alkalmazás egy csoportot közvetlenül hozzárendelése](#how-to-assign-an-application-to-a-group-directly)

-   [Ellenőrizze, hogy egy felhasználó-e egy licencet rendelt csoport része](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Licenc hozzárendelése egy csoporthoz](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>A vendégfelhasználói csoporttagságok ellenőrzése

Ellenőrizze a csoport tagságát, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **csoportok**.

8.  Ellenőrizze, hogy ha a felhasználó az alkalmazáshoz hozzárendelt csoport része.

  * Ha azt szeretné, a felhasználó eltávolítása a csoportból, **kattintson az adott sorra** , a csoport- és válassza a törlés.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Az alkalmazás egy csoportot közvetlenül hozzárendelése

Közvetlenül egy alkalmazás hozzárendelése egy vagy több csoportot, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai**.

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7.  Ha az alkalmazás betöltött, kattintson a **felhasználók és csoportok** az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **Hozzáadás** a gombot a **felhasználók és csoportok** listán, megnyílik a **hozzárendelés hozzáadása** ablaktáblán.

9.  Kattintson a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

10. Írja be a **teljes csoportnév** a csoport Önt érdeklő való hozzárendelése a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. A kurzort a **csoport** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a csoport profilfénykép, vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

12. **Nem kötelező:** Ha szeretné **egynél több csoport hozzáadása**, írjon be egy másik **teljes csoportnév** be a **Keresés név vagy e-mail cím alapján** keresőmezőbe, és kattintson a jelölőnégyzetbe, ez a csoport hozzáadása az a **kijelölt** listája.

13. Ha befejezte a csoportok kiválasztásával, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** ablaktáblán válassza ki a szerepkör hozzárendelése a kiválasztott csoportokhoz.

15. Kattintson a **hozzárendelése** gombra az alkalmazás a kijelölt csoportokhoz hozzárendelni.

Rövid ideig a kijelölt felhasználók tudják elindítani ezeket az alkalmazásokat a hozzáférési panelen.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Ellenőrizze, hogy egy felhasználó-e egy licencet rendelt csoport része

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **csoportok**.

8.  Kattintson az adott csoportban sorát.

9.  Kattintson a **licencek** megtekintéséhez, amely licencek, a csoporthoz van hozzárendelve.

   * Ha a csoport hozzá van rendelve egy Office-licencet, ez lehetséges, hogy lehetővé teszik az egyes első fél Office alkalmazások jelennek meg a felhasználó hozzáférési paneljén.

### <a name="how-to-assign-a-license-to-a-group"></a>Licenc hozzárendelése egy csoporthoz

Licenc hozzárendelése egy csoporthoz, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **összes csoport**.

6.  **Keresés** a csoport Önt érdeklő és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amelyek licencek, a csoport jelenleg hozzá van rendelve.

8.  Kattintson a **hozzárendelése** gombra.

9.  Válassza ki **egy vagy több termék** elérhető termékek listájában.

10. **Nem kötelező** kattintson a **hozzárendelési beállítások** rendeljen kínálja a termékek az elemet. Kattintson a **Ok** amikor a művelet befejeződött.

11. Kattintson a **hozzárendelése** gomb ezek a licencek hozzárendelése ehhez a csoporthoz. Ez eltarthat egy ideig, a mérete és összetettsége, a csoport.

>[!NOTE]
>Gyorsabb ehhez érdemes lehet ideiglenesen rendel egy licencet a felhasználó közvetlenül. 
>
>

## <a name="next-steps"></a>További lépések
[Új felhasználók hozzáadása az Azure Active Directoryhoz](./../fundamentals/add-users-azure-active-directory.md)

