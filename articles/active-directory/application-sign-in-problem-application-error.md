---
title: Hiba történt a bejelentkezés után az alkalmazás oldalon |} Microsoft Docs
description: Ha magának az alkalmazásnak megfelelően kibocsát egy hiba történt az Azure AD bejelentkezési problémák megoldásához
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
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: ce37ab8ac3324226d0d50491b334fe837b77c49d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330305"
---
# <a name="error-on-an-applications-page-after-signing-in"></a>Hiba történt a bejelentkezés után az alkalmazás az oldalon

Ebben a forgatókönyvben az Azure AD a felhasználónak a írt alá, de nem engedélyezi a felhasználó a bejelentkezési folyamat sikeres befejezéséhez hiba megjelenítése az alkalmazáshoz. Ebben a forgatókönyvben az alkalmazás nem fogad el a válasz probléma az Azure ad.

Ennek oka néhány miért az alkalmazás nem fogadta el az Azure AD választ. Ha a hiba történt az alkalmazásban nem alkalmasnak tudni, hogy mi hiányzik a válaszból, majd:

-   Ha az alkalmazás az Azure AD-katalógusában, ellenőrizze a cikkben szereplő összes lépésekkel [SAML-alapú egyszeri bejelentkezés az Azure Active Directoryban alkalmazások hibakeresése](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Egy eszköz, például használja [Fiddler](http://www.telerik.com/fiddler) SAML-kérelmet, SAML-válasz és SAML-jogkivonat rögzítéséhez.

-   A SAML-válasz megosztása tudni, hogy mi hiányzik az alkalmazás gyártójának segítségét.

## <a name="missing-attributes-in-the-saml-response"></a>Hiányzó attribútumokat a SAML-válasz

Az Azure AD beállításai az Azure AD válaszul küldendő attribútum hozzáadásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes Alkalmazások.**

6.  Válassza ki az egyszeri bejelentkezés konfigurálni kívánt alkalmazást.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **megtekintés és Szerkesztés minden más felhasználói attribútumok alapján** a **felhasználói attribútumok** szakaszban az alkalmazás a SAML-jogkivonat küldését, amikor a felhasználók bejelentkeznek az attribútumok szerkesztése.

   Attribútum hozzáadása:

   * Kattintson a **Hozzáadás attribútum**. Adja meg a **neve** majd válassza a **érték** a legördülő listából.

   * Kattintson a **mentéséhez.** Az új attribútumot a táblázatban látható.

9.  A konfiguráció mentéséhez.

A felhasználó bejelentkezik az alkalmazást, amikor legközelebb az Azure AD küldenek az új attribútumot a SAML-válasz.

## <a name="the-application-expects-a-different-user-identifier-value-or-format"></a>Az alkalmazás egy másik felhasználói azonosító értéke vagy a format vár

A bejelentkezés az alkalmazás sikertelen, mert a SAML-válasz attribútumok például a szerepkörök hiányzik, vagy az alkalmazás által várt paraméterekkel a entityid beállítást attribútum formátumát.

## <a name="add-an-attribute-in-the-azure-ad-application-configuration"></a>Az Azure AD alkalmazás konfigurációjának attribútum hozzáadása:

A felhasználói azonosító értékének módosításához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes Alkalmazások.**

6.  Válassza ki az egyszeri bejelentkezés konfigurálni kívánt alkalmazást.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Az a **felhasználói attribútumok**, válassza ki a felhasználók egyedi azonosítója a **felhasználói azonosító** legördülő menüből.

## <a name="change-entityid-user-identifier-format"></a>Entityid (felhasználói azonosító) beállítást formátumának módosítása

Ha az alkalmazás vár a entityid beállítást attribútum egy másik formátumát. Ezt követően nem fogja tudni válassza ki, hogy az Azure AD elküldi az alkalmazásnak a válaszban szereplő felhasználók hitelesítése után entityid beállítást (felhasználói azonosító) formátumát.

Az Azure AD a NameID attribútum (felhasználói azonosító) formátumát a megadott érték alapján kijelölése, vagy a SAML AuthRequest az alkalmazás által kért formátuma. További információ látogasson el a [egyszeri bejelentkezés SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) NameIDPolicy szakaszban.

## <a name="the-application-expects-a-different-signature-method-for-the-saml-response"></a>Az alkalmazás vár egy eltérő aláírással módszer, amellyel a SAML-válasz

Módosíthatja a SAML-jogkivonat részeinek Azure Active Directory által digitálisan aláírtakét. Kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes Alkalmazások.**

6.  Válassza ki az egyszeri bejelentkezés konfigurálni kívánt alkalmazást.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **megjelenítése speciális tanúsítvány-aláírási beállítások** alatt a **SAML-aláíró tanúsítványa** szakasz.

9.  Válassza ki a megfelelő **aláíró beállítás** az alkalmazás által várt:

  * SAML-válasz aláírása

  * SAML-válasz és -előfeltétel aláírása

  * SAML-előfeltétel aláírása

A felhasználó bejelentkezik az alkalmazást, amikor legközelebb az Azure AD a kiválasztott SAML-válasz részét aláírásához.

## <a name="the-application-expects-the-signing-algorithm-to-be-sha-1"></a>Az alkalmazás vár az aláíró algoritmus az SHA-1

Alapértelmezés szerint az Azure AD aláírja a SAML-jogkivonat a legtöbb biztonsági algoritmus használatával. A bejelentkezési algoritmus az SHA-1 módosítása nem ajánlott, kivéve, ha az alkalmazás által igényelt.

Az aláíró algoritmus módosításához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes Alkalmazások.**

6.  Válassza ki az egyszeri bejelentkezés konfigurálni kívánt alkalmazást.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **megjelenítése speciális tanúsítvány-aláírási beállítások** alatt a **SAML-aláíró tanúsítványa** szakasz.

9.  Az SHA-1, jelölje ki a **aláírási algoritmus**.

A felhasználó bejelentkezik az alkalmazást, amikor legközelebb az Azure AD az SHA-1 algoritmussal SAML-jogkivonat aláírása.

## <a name="next-steps"></a>További lépések
[SAML-alapú egyszeri bejelentkezés az Azure Active Directoryban alkalmazások hibakeresése](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
