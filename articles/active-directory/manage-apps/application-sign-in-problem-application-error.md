---
title: Hiba történt a bejelentkezés után az alkalmazás lapon |} A Microsoft Docs
description: Ha maga az alkalmazás bocsát ki hiba az Azure AD bejelentkezési kapcsolatos problémák megoldása
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 5146b471ffd72e606d0915bbc897bc104e0282b2
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475390"
---
# <a name="error-on-an-applications-page-after-signing-in"></a>Hiba történt a bejelentkezés után az alkalmazás lapon

Ebben a forgatókönyvben az Azure AD a felhasználó bejelentkezett, de az alkalmazás nem engedélyezi a felhasználó sikeresen befejeződik a bejelentkezési folyamat hibát jelenít meg. Ebben a forgatókönyvben az alkalmazás nem fogad a válasz probléma az Azure AD által.

Nincsenek miért fogadja el az alkalmazást nem a választ az Azure ad-ből ennek lehetséges okai. Ha a hiba, az alkalmazás nem egyértelmű elég tudnia, mi a válaszban hiányzó majd:

-   Az Azure AD katalógusából származó alkalmazás esetén ellenőrizze a cikkben található összes lépéseket követte [SAML-alapú egyszeri bejelentkezés az Azure Active Directory-alkalmazások hibakeresése](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Egy eszköz, például használata [Fiddler](https://www.telerik.com/fiddler) az SAML-kérelmet, a SAML-válasz és az SAML-jogkivonat.

-   Az SAML-válasz megoszthatja tudnia, mi hiányzik az alkalmazás gyártójától.

## <a name="missing-attributes-in-the-saml-response"></a>Hiányzik a SAML-válasz attribútumok

Attribútum hozzáadása az Azure AD-konfigurációjának az Azure ad-ben válaszul küldendő, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazás egyszeri bejelentkezéshez konfigurálandó.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **megtekintés és Szerkesztés minden egyéb felhasználói attribútumok alapján** a **felhasználói attribútumok** szakaszban az alkalmazás SAML-jogkivonatban kell küldeni, amikor a felhasználók bejelentkeznek az attribútumok szerkesztését.

   Attribútum hozzáadása:

   * Kattintson a **attribútum hozzáadása**. Adja meg a **neve** , és válassza a a **érték** a legördülő listából.

   * Kattintson a **mentéséhez.** Megjelenik az új attribútum a táblán.

9.  Mentse a konfigurációt.

A felhasználó bejelentkezik az alkalmazásba, amikor legközelebb az Azure AD küldése az új attribútumot a SAML-válasz.

## <a name="the-application-expects-a-different-user-identifier-value-or-format"></a>Az alkalmazás különböző felhasználói azonosító értékét vagy formátumot vár

A bejelentkezés az alkalmazáshoz sikertelen, mert hiányzik a SAML-válasz attribútumok, például a szerepkörök, vagy az alkalmazás, a várt EntityID attribútum egy másik formátumra.

## <a name="add-an-attribute-in-the-azure-ad-application-configuration"></a>Az Azure AD-alkalmazás beállítása az attribútum hozzáadása:

A felhasználói azonosító értékének módosításához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazás egyszeri bejelentkezéshez konfigurálandó.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Alatt a **felhasználói attribútumok**, válassza ki a felhasználók az egyedi azonosítója a **felhasználóazonosító** legördülő listából.

## <a name="change-entityid-user-identifier-format"></a>EntityID (felhasználóazonosító) formátum módosítása

Ha az alkalmazás a EntityID attribútum egy másik formátumot vár. Ezt követően nem szeretné választani az Azure AD elküldi az alkalmazás a válaszban szereplő felhasználók hitelesítése után EntityID (felhasználóazonosító) formátumban.

Kiválasztott Azure AD válassza ki a formátumot a NameID-attribútum (felhasználóazonosító) érték alapján, vagy formátumát az SAML AuthRequest az alkalmazás által kért. További részletekért látogasson el a cikk [egyszeri bejelentkezéses SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) NameIDPolicy szakaszban.

## <a name="the-application-expects-a-different-signature-method-for-the-saml-response"></a>Az alkalmazás vár módszert más aláírás a SAML-válasz

Módosíthatja az SAML-jogkivonat mely részeit írja alá digitálisan az Azure Active Directory. Kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazás egyszeri bejelentkezéshez konfigurálandó.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **speciális tanúsítvány-aláírási beállítások megjelenítése** alatt a **SAML-aláíró tanúsítvány** szakaszban.

9.  Válassza ki a megfelelő **aláírási beállítás** az alkalmazás által várt:

  * SAML-válasz aláírása

  * SAML-válasz és -előfeltétel aláírása

  * SAML-előfeltétel aláírása

A felhasználó bejelentkezik az alkalmazásba, amikor legközelebb az Azure AD bejelentkezés a kiválasztott SAML-válasz részét.

## <a name="the-application-expects-the-signing-algorithm-to-be-sha-1"></a>Az alkalmazás vár az aláíró algoritmus az SHA-1

Alapértelmezés szerint az Azure AD bejelentkezésekor az SAML-jogkivonat a legtöbb biztonsági algoritmus használatával. A bejelentkezési algoritmus az SHA-1 nem ajánlott, kivéve, ha az alkalmazás számára szükséges.

Az aláíró algoritmus módosításához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazás egyszeri bejelentkezéshez konfigurálandó.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **speciális tanúsítvány-aláírási beállítások megjelenítése** alatt a **SAML-aláíró tanúsítvány** szakaszban.

9.  Az SHA-1, válassza ki a **aláíró algoritmus**.

A felhasználó bejelentkezik az alkalmazásba, amikor legközelebb az Azure AD jelentkezzen az SAML-jogkivonat SHA-1 algoritmust használja.

## <a name="next-steps"></a>További lépések
[SAML-alapú egyszeri bejelentkezés az Azure Active Directory-alkalmazások hibakeresése](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
