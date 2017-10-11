---
title: "Egy hozzárendelt alkalmazás nem jelenik meg a hozzáférési panel |} Microsoft Docs"
description: "Ezért az alkalmazás nem jelenik meg a hozzáférési Panel hibaelhárítása"
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
ms.reviwer: japere
ms.openlocfilehash: 9ea5744d77b90929598ea5feb80c7bbdff3772fc
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Egy hozzárendelt alkalmazás nem jelenik meg a hozzáférési panel

A hozzáférési Panel egy webes portál, amely lehetővé teszi a felhasználó munkahelyi vagy iskolai fiókkal az Azure Active Directory (Azure AD) a megtekintése, és indítsa el a felhőalapú alkalmazások, hogy az Azure AD-rendszergazda engedélyezte őket hozzáférés annak. Ezeket az alkalmazásokat úgy vannak konfigurálva, az Azure AD portálon a felhasználó nevében. Az alkalmazás megfelelően konfigurálva legyen, és a felhasználó vagy egy csoport, a felhasználó tagja a hozzáférési panelen az alkalmazást.

Alkalmazások azért jelent meg a felhasználó típusa a következő kategóriákba tartoznak:

-   Office 365-alkalmazások

-   Összevonási-alapú egyszeri bejelentkezési modellel konfigurált a Microsoft és külső alkalmazások

-   Jelszó-alapú egyszeri bejelentkezés alkalmazásokhoz

-   A már meglévő SSO megoldásaival alkalmazások

## <a name="general-issues-to-check-first"></a>Először ellenőrizze a általános problémák

-   Ha egy alkalmazás egy felhasználó előbbiekben felvett, próbáljon újból bejelentkezni bejövő és kimenő adatforgalma újra a felhasználó hozzáférési panelre néhány perc múlva szerepel-e az alkalmazás van.

-   Ha a licenc csak felhasználó vagy csoport, a felhasználó nem tagja ennek eltarthat egy ideig, attól függően, hogy méretét és összetettségét, el kell végezni a változásokat a csoport el lett távolítva. Lehetővé teszi további idő előtt jelentkezik be a hozzáférési Panel.

## <a name="problems-related-to-application-configuration"></a>Alkalmazás-konfigurációval kapcsolatos problémák

Egy alkalmazás nem lehetséges, hogy megjelenjen a a felhasználó hozzáférési Panel, mert nincs megfelelően konfigurálva. Az alábbiakban néhány módszert alkalmazás konfigurációjával kapcsolatos problémák hibaelhárítását hajthatja végre:

-   [Összevont egyszeri bejelentkezés az Azure AD gyűjtemény alkalmazás konfigurálása](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Összevont egyszeri bejelentkezés nem galéria alkalmazások konfigurálása](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Egy jelszó egyszeri bejelentkezési alkalmazás gyűjtemény az Azure AD alkalmazás konfigurálása](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [A jelszó egyszeri bejelentkezési alkalmazás nem galéria alkalmazások konfigurálása](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Összevont egyszeri bejelentkezés az Azure AD gyűjtemény alkalmazás konfigurálása

Minden alkalmazást a vállalati egyszeri bejelentkezésre alkalmas engedélyezve van az Azure AD-katalógus elérhető egy részletes oktatóanyag rendelkezik. Érheti el a [SaaS-alkalmazásokhoz az Azure Active Directoryval integrációjával kapcsolatos bemutatók felsorolása](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) részletes lépésenkénti útmutatót.

Egy alkalmazás kell az Azure AD-galériából konfigurálása:

-   [Alkalmazás hozzáadása az Azure AD-galériából](#add-an-application-from-the-azure-ad-gallery)

-   [Az alkalmazás metaadatai értékeinek konfigurálása az Azure AD (bejelentkezési URL-cím, az azonosítója, válasz URL-címe)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Válassza ki a felhasználói azonosítóját, és elküldi az alkalmazás felhasználói attribútumok hozzáadása](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD-metaadatok és a tanúsítvány lekérése](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurálja az Azure AD metaadatok értékeket az alkalmazásban (bejelentkezési URL-címet, a kibocsátó, a kijelentkezési URL-cím és a tanúsítvány)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Alkalmazás hozzáadása az Azure AD-galériából

Hozzáadhat egy alkalmazást az Azure AD-gyűjteményből, kövesse az alábbi lépéseket:

1.  Nyissa meg a [Azure Portal](https://portal.azure.com) , és jelentkezzen be egy **globális rendszergazda** vagy **társadminisztrátornak**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **Hozzáadás** gombra a jobb felső sarkában a **vállalati alkalmazások** panelen.

6.  Az a **adjon meg egy nevet** a szövegmező a **hozzáadása a gyűjteményből** területen írja be az alkalmazás nevét.

7.  Válassza ki az alkalmazást, az egyszeri bejelentkezés konfigurálni szeretné.

8.  Ad hozzá az alkalmazást, mielőtt a nevét módosíthatja a **neve** szövegmező.

9.  Kattintson a **Hozzáadás** gombra, vegye fel az alkalmazást.

Egy rövid időszak után el az alkalmazás konfigurációs panelen láthatók.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Egyszeri bejelentkezés egy alkalmazás az Azure AD-galériából konfigurálása

Egyszeri bejelentkezés egy alkalmazás konfigurálásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes alkalmazást.**

6.  Válassza ki az egyszeri bejelentkezés konfigurálni kívánt alkalmazást.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Válassza ki **SAML-alapú bejelentkezés** a a **mód** legördülő menüből.

9.  Adja meg a szükséges értékeket a **tartomány és az URL-címeket.** Ezeket az értékeket az alkalmazás gyártójának szerezheti be.

   1. Az alkalmazás beállítása a Szolgáltató által kezdeményezett egyszeri Bejelentkezést, a bejelentkezési URL-címen nem kötelező megadni. Egyes alkalmazások azonosító is kötelező megadni.

   2. Az alkalmazás beállítása a kiállító terjesztési hely által kezdeményezett egyszeri Bejelentkezést, a válasz URL-CÍMEN nem kötelező megadni. Egyes alkalmazások azonosító is kötelező megadni.

10. **Választható lehetőség:** kattintson **megjelenítése speciális URL-beállításainak** Ha meg szeretné tekinteni a nem szükséges értékeket.

11. Az a **felhasználói attribútumok**, válassza ki a felhasználók egyedi azonosítója a **felhasználói azonosító** legördülő menüből.

12. **Választható lehetőség:** kattintson **nézet és egyéb felhasználói attribútumok szerkesztése** kell küldeni a alkalmazását a SAML-jogkivonat felhasználói bejelentkezés attribútumok szerkesztéséhez.

   Attribútum hozzáadása:

   1. Kattintson a **Hozzáadás attribútum**. Adja meg a **neve** majd válassza a **érték** a legördülő listából.

   2. Kattintson a **mentéséhez.** Az új attribútumot a táblázatban látható.

13. Kattintson a **konfigurálása &lt;alkalmazásnév&gt;**  hozzáférés dokumentációjának egyszeri bejelentkezést az alkalmazás konfigurálásához. Is hogy rendelkezik, a metadata URL-címek és az egyszeri bejelentkezés beállítása az alkalmazáshoz szükséges tanúsítvány.

14. Kattintson a **mentése** a konfiguráció mentéséhez.

15. Felhasználók hozzárendelése az alkalmazáshoz.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosítóját, és elküldi az alkalmazás felhasználói attribútumok hozzáadása

Válassza ki a felhasználói azonosító, vagy adja hozzá a felhasználói attribútumok, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha nem látja az alkalmazás szeretné itt jelenik meg, akkor használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes alkalmazást.**

6.  Válassza ki az alkalmazást, az egyszeri bejelentkezés konfigurálta.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Az a **felhasználói attribútumok** területen válassza ki a felhasználók egyedi azonosítója a **felhasználói azonosító** legördülő menüből. A kiválasztott beállítás a várt érték a felhasználó hitelesítésére az alkalmazásban egyezniük kell.

   >[!NOTE] 
   >Az Azure AD a NameID attribútum (felhasználói azonosító) formátumát a megadott érték alapján kijelölése, vagy a SAML AuthRequest az alkalmazás által kért formátuma. További információ látogasson el a [egyszeri bejelentkezés SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) NameIDPolicy szakaszban.
   >
   >

9.  Felhasználói attribútumok hozzáadásához kattintson **nézet és egyéb felhasználói attribútumok szerkesztése** kell küldeni a alkalmazását a SAML-jogkivonat felhasználói bejelentkezés attribútumok szerkesztéséhez.

   Attribútum hozzáadása:

   1. Kattintson a **Hozzáadás attribútum**. Adja meg a **neve** majd válassza a **érték** a legördülő listából.

   2. Kattintson a **mentéséhez.** Látni fogja az új attribútumot a táblában.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD-metaadatok vagy a tanúsítvány letöltése

Az alkalmazás metaadatainak vagy tanúsítvány letöltéséhez az Azure AD, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes alkalmazást.**

6.  Válassza ki az alkalmazást, az egyszeri bejelentkezés konfigurálta.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Ugrás a **SAML-aláíró tanúsítványa** területen, majd kattintson **letöltése** oszlop értékét. Attól függően, hogy milyen az alkalmazáshoz az szükséges, az egyszeri bejelentkezés konfigurálása lásd: a metaadatok XML-kód letöltése beállítás, vagy a tanúsítvány.

    Az Azure AD nem biztosít a metaadatok beolvasása URL-CÍMÉT. A metaadatok XML-fájlként csak olvasható.

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Összevont egyszeri bejelentkezés nem galéria alkalmazások konfigurálása

Beállíthat egy nem galéria-alkalmazást, akkor kell rendelkeznie az Azure AD premium és az alkalmazás támogatja az SAML 2.0-s. Az Azure AD-verziókkal kapcsolatos további információkért látogasson el a [az Azure AD árképzési](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Az alkalmazás metaadatai értékeinek konfigurálása az Azure AD (bejelentkezési URL-cím, az azonosítója, válasz URL-címe)](#configuring-single-sign-on)

-   [Válassza ki a felhasználói azonosítóját, és elküldi az alkalmazás felhasználói attribútumok hozzáadása](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD-metaadatok és a tanúsítvány lekérése](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurálja az Azure AD metaadatok értékeket az alkalmazásban (bejelentkezési URL-címet, a kibocsátó, a kijelentkezési URL-cím és a tanúsítvány)](#configuring-single-sign-on)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Az alkalmazás metaadatai értékeinek konfigurálása az Azure AD (bejelentkezési URL-cím, az azonosítója, válasz URL-címe)

Egyszeri bejelentkezés egy alkalmazás, amely nincs az Azure AD-katalógus konfigurálásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **Hozzáadás** gombra a jobb felső sarkában a **vállalati alkalmazások** panelen.

6.  Kattintson a **nem-gyűjtemény alkalmazás** a a **saját alkalmazás felvétele** szakasz.

7.  Adja meg az alkalmazás nevét a **neve** szövegmező.

8.  Kattintson a **Hozzáadás** gombra, vegye fel az alkalmazást.

9.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

10. Válassza ki **SAML-alapú bejelentkezés** a a **mód** legördülő menüből.

11. Adja meg a szükséges értékeket a **tartomány és az URL-címeket.** Ezeket az értékeket az alkalmazás gyártójának szerezheti be.

   1. Az alkalmazás beállítása a kiállító terjesztési hely által kezdeményezett egyszeri Bejelentkezést, írja be a válasz URL-CÍMEN és azonosítóját.

   2.  **Választható lehetőség:** SP által kezdeményezett egyszeri Bejelentkezést, a bejelentkezési URL-címen, az alkalmazás konfigurálásához szükséges érték.

12. Az a **felhasználói attribútumok**, válassza ki a felhasználók egyedi azonosítója a **felhasználói azonosító** legördülő menüből.

13. **Választható lehetőség:** kattintson **nézet és egyéb felhasználói attribútumok szerkesztése** kell küldeni a alkalmazását a SAML-jogkivonat felhasználói bejelentkezés attribútumok szerkesztéséhez.

   Attribútum hozzáadása:

   1. Kattintson a **Hozzáadás attribútum**. Adja meg a **neve** majd válassza a **érték** a legördülő listából.

   2. Kattintson a **mentéséhez.** Az új attribútumot a táblázatban látható.

14. Kattintson a **konfigurálása &lt;alkalmazásnév&gt;**  hozzáférés dokumentációjának egyszeri bejelentkezést az alkalmazás konfigurálásához. Is hogy rendelkezik, az Azure AD URL-címek és az alkalmazáshoz szükséges tanúsítvány.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosítóját, és elküldi az alkalmazás felhasználói attribútumok hozzáadása

Válassza ki a felhasználói azonosító, vagy adja hozzá a felhasználói attribútumok, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes alkalmazást.**

6.  Válassza ki az alkalmazást, az egyszeri bejelentkezés konfigurálta.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Az a **felhasználói attribútumok** területen válassza ki a felhasználók egyedi azonosítója a **felhasználói azonosító** legördülő menüből. A kiválasztott beállítás a várt érték a felhasználó hitelesítésére az alkalmazásban egyezniük kell.

   >[!NOTE] 
   >Az Azure AD a NameID attribútum (felhasználói azonosító) formátumát a megadott érték alapján kijelölése, vagy a SAML AuthRequest az alkalmazás által kért formátuma. További információ látogasson el a [egyszeri bejelentkezés SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) NameIDPolicy szakaszban.
   >
   >

9.  Felhasználói attribútumok hozzáadásához kattintson **nézet és egyéb felhasználói attribútumok szerkesztése** kell küldeni a alkalmazását a SAML-jogkivonat felhasználói bejelentkezés attribútumok szerkesztéséhez.

   Attribútum hozzáadása:

   1. Kattintson a **Hozzáadás attribútum**. Adja meg a **neve** majd válassza a **érték** a legördülő listából.

   2. Kattintson a **mentéséhez.** Az új attribútumot a táblázatban látható.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD-metaadatok vagy a tanúsítvány letöltése

Az alkalmazás metaadatainak vagy tanúsítvány letöltéséhez az Azure AD, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes alkalmazást.**

6.  Válassza ki az alkalmazást, az egyszeri bejelentkezés konfigurálta.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Ugrás a **SAML-aláíró tanúsítványa** területen, majd kattintson **letöltése** oszlop értékét. Attól függően, hogy milyen az alkalmazáshoz az szükséges, az egyszeri bejelentkezés konfigurálása lásd: a metaadatok XML-kód letöltése beállítás, vagy a tanúsítvány.

Az Azure AD nem biztosít a metaadatok beolvasása URL-CÍMÉT. A metaadatok XML-fájlként csak olvasható.

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jelszó egyszeri bejelentkezés az Azure AD gyűjtemény alkalmazás konfigurálása

Egy alkalmazás kell az Azure AD-galériából konfigurálása:

-   [Alkalmazás hozzáadása az Azure AD-galériából](#add-an-application-from-the-azure-ad-gallery)

-   [Állítsa be az alkalmazását jelszó egyszeri bejelentkezést.](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Alkalmazás hozzáadása az Azure AD-galériából

Hozzáadhat egy alkalmazást az Azure AD-gyűjteményből, kövesse az alábbi lépéseket:

1.  Nyissa meg a [Azure Portal](https://portal.azure.com) , és jelentkezzen be egy **globális rendszergazda** vagy **társadminisztrátornak**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **Hozzáadás** gombra a jobb felső sarkában a **vállalati alkalmazások** panelen.

6.  Az a **adjon meg egy nevet** a szövegmező a **hozzáadása a gyűjteményből** területen írja be az alkalmazás nevét.

7.  Válassza ki az alkalmazást, az egyszeri bejelentkezés konfigurálni szeretné.

8.  Ad hozzá az alkalmazást, mielőtt a nevét módosíthatja a **neve** szövegmező.

9.  Kattintson a **Hozzáadás** gombra, vegye fel az alkalmazást.

Egy rövid időszak után el az alkalmazás konfigurációs panelen láthatók.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Állítsa be az alkalmazását jelszó egyszeri bejelentkezést.

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

9.  [Felhasználók hozzárendelése az alkalmazás](#how-to-assign-a-user-to-an-application-directly).

10. Emellett is megadhatja a felhasználó nevében hitelesítő adatok a felhasználók a sorok kijelöléséhez és parancsával **frissítéséhez szükséges hitelesítő adatokat** , majd gépelje be a felhasználónevet és jelszót a felhasználók nevében. Ellenkező esetben megkérdezi a felhasználókat a hitelesítő adatok magukat az indítás után.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jelszó egyszeri bejelentkezés nem galéria alkalmazások konfigurálása

Egy alkalmazás kell az Azure AD-galériából konfigurálása:

-   [Nem galéria alkalmazás hozzáadása](#add-a-non-gallery-application)

-   [Állítsa be az alkalmazását jelszó egyszeri bejelentkezést.](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Nem galéria alkalmazás hozzáadása

Hozzáadhat egy alkalmazást az Azure AD-gyűjteményből, kövesse az alábbi lépéseket:

1.  Nyissa meg a [Azure Portal](https://portal.azure.com) , és jelentkezzen be egy **globális rendszergazda** vagy **társadminisztrátor**.

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **Hozzáadás** gombra a jobb felső sarkában a **vállalati alkalmazások** panelen.

6.  Kattintson a **nem-gyűjtemény alkalmazás.**

7.  Adja meg az alkalmazás nevét a **neve** szövegmező. Válassza ki **hozzáadása.**

Egy rövid időszak után el az alkalmazás konfigurációs panelen láthatók.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Állítsa be az alkalmazását jelszó egyszeri bejelentkezést.

Egyszeri bejelentkezés egy alkalmazás konfigurálásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

    1.  Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes alkalmazást.**

6.  Válassza ki az egyszeri bejelentkezés konfigurálni kívánt alkalmazást.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Válassza ki a módot **jelszóalapú bejelentkezés.**

9.  Adja meg a **bejelentkezési URL-cím**. Ez az URL-CÍMÉT ahol adja meg a felhasználók a felhasználónevével és jelszavával bejelentkezni. Győződjön meg arról, a mezők bejelentkezési URL-címen láthatók.

10. [Felhasználók hozzárendelése az alkalmazás](#how-to-assign-a-user-to-an-application-directly).

11. Emellett is megadhatja a felhasználó nevében hitelesítő adatok a felhasználók a sorok kijelöléséhez és parancsával **frissítéséhez szükséges hitelesítő adatokat** , majd gépelje be a felhasználónevet és jelszót a felhasználók nevében. Ellenkező esetben megkérdezi a felhasználókat a hitelesítő adatok magukat az indítás után.

## <a name="problems-related-to-assigning-applications-to-users"></a>Felhasználók hozzárendelése kapcsolatos problémák

A felhasználó nem lehet annak, hogy egy alkalmazás a hozzáférési panelen, mert az alkalmazás nem vannak rendelve. Az alábbiakban néhány ellenőrizze módjai:

-   [Ellenőrizze, hogy ha a felhasználó hozzá van rendelve az alkalmazáshoz](#check-if-a-user-is-assigned-to-the-application)

-   [A felhasználó közvetlenül egy alkalmazás hozzárendelése](#how-to-assign-a-user-to-an-application-directly)

-   [Ellenőrizze, hogy ha a felhasználó hozzá van rendelve az alkalmazáshoz kapcsolódó licenc](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Egy felhasználói licenc hozzárendelése](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Ellenőrizze, hogy ha a felhasználó hozzá van rendelve az alkalmazáshoz

Ellenőrizze, hogy ha a felhasználó hozzá van rendelve az alkalmazást, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

6.  **Keresési** a szóban forgó alkalmazás nevét.

7.  Kattintson a **felhasználók és csoportok**.

8.  Ellenőrizze, hogy ha a felhasználó hozzá van rendelve az alkalmazáshoz.

   * Ha nem, kövesse a "How to felhasználó hozzárendelése egy alkalmazás közvetlenül" ehhez.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>A felhasználó közvetlenül egy alkalmazás hozzárendelése

Hozzárendelése egy vagy több felhasználó alkalmazás közvetlenül, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda**.

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

Rövid ideig a kijelölt felhasználók tudják elindítani ezeket az alkalmazásokat a hozzáférési Panel.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Annak ellenőrzése, hogy a felhasználó a licenc, az alkalmazással kapcsolatos

A felhasználó licenc-hozzárendeléseket ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amely licencek, a felhasználó jelenleg hozzá van rendelve.

  * Ha a felhasználó hozzá van rendelve egy Office licenc a engedélyezése első fél Office-alkalmazásokat jelennek meg a felhasználó hozzáférési Panel.

### <a name="how-to-assign-a-user-a-license"></a>Egy felhasználói licenc hozzárendelése 

A licenc hozzárendelése egy felhasználóhoz, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amely licencek, a felhasználó jelenleg hozzá van rendelve.

8.  Kattintson a **hozzárendelése** gombra.

9.  Válassza ki **egy vagy több termék** választható termékek közül.

10. **Nem kötelező** kattintson a **hozzárendelés beállításai** elem termékek granularly hozzárendelni. Kattintson a **Ok** amikor ez befejeződik.

11. Kattintson a **hozzárendelése** gomb a licencek hozzárendelése a felhasználóhoz.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Csoportok alkalmazásoké kapcsolatos problémák

Egy felhasználó lehet annak, hogy egy alkalmazás a hozzáférési Panel a mert, amelyet az alkalmazáshoz rendelt csoportba. Az alábbiakban néhány ellenőrizze módjai:

-   [A felhasználói csoporttagság ellenőrzése](#check-a-users-group-memberships)

-   [Egy alkalmazás egy csoporthoz közvetlenül hozzárendelése](#how-to-assign-an-application-to-a-group-directly)

-   [Annak ellenőrzése, hogy a felhasználó csoporthoz hozzárendelt licenc](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [A licenc hozzárendelése egy csoportot](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>A felhasználói csoporttagság ellenőrzése

Ellenőrizze a csoport tagságát, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **csoportok**.

8.  Ellenőrizze, hogy a felhasználók egy csoportjának az alkalmazás része.

  * Ha a felhasználó a csoportból eltávolítani kívánt **sorára kattintson** a csoport- és select tulajdonosával.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Egy alkalmazás egy csoporthoz közvetlenül hozzárendelése

Közvetlenül egy alkalmazás hozzárendelése egy vagy több csoportot, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda**.

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

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Annak ellenőrzése, hogy a felhasználó csoporthoz hozzárendelt licenc

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **csoportok**.

8.  egy adott csoport sorára kattintson.

9.  Kattintson a **licencek** megtekintéséhez, amely licencek, a csoport van rendelve.

   * Ha a csoport egy Office-licencet, ezzel lehetővé teheti a bizonyos első fél Office alkalmazások jelennek meg a felhasználó hozzáférési Panel van hozzárendelve.

### <a name="how-to-assign-a-license-to-a-group"></a>A licenc hozzárendelése egy csoportot

A licenc hozzárendelése egy csoporthoz, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **összes csoport**.

6.  **Keresési** érdekli csoport és **sorára kattintson** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amely licencek, a csoport jelenleg hozzá van rendelve.

8.  Kattintson a **hozzárendelése** gombra.

9.  Válassza ki **egy vagy több termék** választható termékek közül.

10. **Nem kötelező** kattintson a **hozzárendelés beállításai** elem termékek granularly hozzárendelni. Kattintson a **Ok** amikor ez befejeződik.

11. Kattintson a **hozzárendelése** gomb a licencek hozzárendelése ehhez a csoporthoz. Ez eltarthat egy ideig, attól függően, hogy méretét és összetettségét, a csoport.

>[!NOTE]
>Ehhez gyorsabb, érdemes lehet ideiglenesen rendel egy licencet a felhasználó közvetlenül. 
>
>

## <a name="next-steps"></a>Következő lépések
[Új felhasználók hozzáadása az Azure Active Directoryhoz](active-directory-users-create-azure-portal.md)

