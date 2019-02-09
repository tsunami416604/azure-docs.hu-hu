---
title: Egy konfigurált összevont katalógusból származó alkalmazásra való bejelentkezésnél egyszeri bejelentkezés |} A Microsoft Docs
description: Útmutató a hibák egy alkalmazásba való bejelentkezéskor a konfigurált SAML-alapú összevont egyszeri bejelentkezés az Azure ad-vel
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.openlocfilehash: bd82808e53e8272cf55fbb43aed1b5f2f9f5ff56
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55963607"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Egy összevont egyszeri bejelentkezésre beállított katalógusából származó alkalmazásba történő bejelentkezésnél

A probléma elhárításához ellenőrizze az alkalmazás konfigurációját az Azure AD a következőket kell:

-   Az Azure AD katalógusából származó alkalmazásba a konfigurációs lépéseket követte.

-   A azonosítója és a válasz URL-cím, az aad-ben konfigurált egyezik, az alkalmazás a várt értékek

-   Az alkalmazás hozzárendelt felhasználók

## <a name="application-not-found-in-directory"></a>Az alkalmazás nem található a címtárban

*Hiba AADSTS70001: Alkalmazás-azonosító "https://contoso.com" nem található a címtárban*.

**Lehetséges ok**

Attribútum, az alkalmazás Azure ad-ben az SAML-kérelmet küld a kiállító nem felel meg a az alkalmazás Azure ad-ben konfigurált azonosító értékét.

**Felbontás**

Ellenőrizze, hogy a kibocsátó attribútumot az SAML-kérelmet, a megfelelő az azonosító az Azure ad-ben konfigurált értéket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazást szeretné az egyszeri bejelentkezés konfigurálása

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Lépjen a **tartomány és URL-címek** szakaszban. Győződjön meg arról, hogy az azonosító szövegbeviteli mező értékét a értéket az azonosító az a hiba jelenik meg a megfelelő-e.

Miután frissítette az Azure ad-ben az azonosító értékét, és azt van megfelelő az érték küld az alkalmazásnak az SAML-kérelmet, jelentkezzen be az alkalmazást, képesnek kell lennie.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>A válaszcím nem egyezik meg a az alkalmazáshoz konfigurált válaszcímekkel.

*Hiba AADSTS50011: A válaszcím https://contoso.com"nem egyezik meg a az alkalmazáshoz konfigurált válaszcímekkel*

**Lehetséges ok**

Az SAML-kérelmet AssertionConsumerServiceURL értéke nem egyezik, a válasz URL-cím értékét vagy az Azure ad-ben konfigurált minta. Az SAML-kérelmet AssertionConsumerServiceURL érték jelenik meg a hiba az URL-CÍMÉT.

**Felbontás**

Ellenőrizze, hogy az SAML-kérelmet, a válasz URL-cím a megfelelő AssertionConsumerServiceURL értékét az Azure ad-ben konfigurált értéket.

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazást szeretné az egyszeri bejelentkezés konfigurálása

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Lépjen a **tartomány és URL-címek** szakaszban. Győződjön meg arról, vagy frissítse az értéket a válasz URL-cím szövegmezőbe az SAML-kérelmet AssertionConsumerServiceURL értékének megfelelően.  
    * Ha nem látja a válasz URL-címe szövegmezőbe, válassza ki a **speciális URL-beállítások megjelenítése** jelölőnégyzetet.

Miután frissítése az Azure ad-ben a válasz URL-Címének értékét, és azt van megfelelő az érték küld az alkalmazásnak az SAML-kérelmet, jelentkezzen be az alkalmazást, képesnek kell lennie.

## <a name="user-not-assigned-a-role"></a>Felhasználói szerepkör nincs hozzárendelve

*Hiba AADSTS50105: A bejelentkezett felhasználó "brian@contoso.com" nincs hozzárendelve egy szerepkörhöz az alkalmazás*.

**Lehetséges ok**

A felhasználó nem kapott hozzáférést az alkalmazáshoz az Azure ad-ben.

**Felbontás**

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

Után rövid idő alatt a kiválasztott felhasználók tudják elindítani ezeket az alkalmazásokat a megoldás leírása szakaszban leírt módszerek használatával.

## <a name="not-a-valid-saml-request"></a>Nem egy érvényes SAML-kérelmet

*Hiba AADSTS75005: A kérés nem érvényes egy Saml2 protokoll üzenetet.*

**Lehetséges ok**

Az Azure AD nem támogatja az alkalmazás egyszeri bejelentkezésre vonatkozó SAML-kérelmét. Néhány gyakori hibák a következők:

-   Az SAML-kérelmet kötelező mezők hiányoznak

-   SAML-kérelem kódolt metódusa

**Felbontás**

1.  Rögzítse a SAML-kérelmet. az oktatóanyag [az Azure ad-ben az SAML-alapú egyszeri bejelentkezés alkalmazások hibakeresése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) megtudhatja, hogyan rögzítheti az SAML-kérelmet.

2.  Lépjen kapcsolatba az alkalmazás szállítójához és megosztás:

   -   SAML-kérelem

   -   [Az Azure AD egyszeri bejelentkezési SAML protokolljának követelményei](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Ellenőrizni kell az Azure AD SAML végrehajtása támogatja az egyszeri bejelentkezés.

## <a name="no-resource-in-requiredresourceaccess-list"></a>Nincs erőforrás requiredResourceAccess listában

*Hiba AADSTS65005: az ügyfélalkalmazás kért erőforrás elérésére "00000002-0000-0000-c000-000000000000'. A kérelem nem sikerült, mert az ügyfél nem megadva ehhez az erőforráshoz a requiredResourceAccess listájára*.

**Lehetséges ok**

Az alkalmazás objektum sérült.

**Megoldás: 1. lehetőséget**

A problémának a megoldásához adja hozzá az egyedi azonosító értékét az Azure AD-konfigurációban. Azonosító érték hozzáadása, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazás egyszeri bejelentkezésre konfigurálta.

7.  Ha az alkalmazás betöltött, kattintson a a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menü

8.  Alatt a **tartomány és URL-cím** részen ellenőrizheti a **speciális URL-beállítások megjelenítése**.

9.  az a **azonosító** szövegmezőbe írja be az alkalmazás egyedi azonosítója.

10. **Mentés** a konfigurációt.


**A felbontási beállítás 2**

Ha a fenti 1 beállítás nem működik az Ön számára, próbálkozzon az alkalmazás eltávolítása a címtárból. Adja hozzá, és konfigurálja újra az alkalmazást, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazást szeretné az egyszeri bejelentkezés konfigurálása

7.  Kattintson a **törlése** felső – bal oldalán az alkalmazás **áttekintése** ablaktáblán.

8.  Frissítse az Azure ad-ben, és vegye fel az alkalmazást az Azure AD katalógusából. Ezt követően az alkalmazás konfigurálása

<span id="_Hlk477190176" class="anchor"></span>Után az alkalmazás újbóli beállításához kell tudni bejelentkezni az alkalmazásba.

## <a name="certificate-or-key-not-configured"></a>Tanúsítvány és kulcs nincs konfigurálva

*Hiba AADSTS50003: Nincs konfigurálva aláírási kulcsát.*

**Lehetséges ok**

Az alkalmazás objektum sérült, és az Azure AD nem ismeri fel a tanúsítványt az alkalmazáshoz konfigurált.

**Felbontás**

Törölje, és hozzon létre egy új tanúsítványt, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

 * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazást szeretné az egyszeri bejelentkezés konfigurálása

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **új tanúsítvány létrehozása** alatt a **SAML-aláíró tanúsítvány** szakaszban.

9.  Válassza ki a lejárati dátumot. Kattintson a **mentéséhez.**

10. Ellenőrizze **új tanúsítvány aktívvá** az aktív tanúsítvány felülbírálásához. Kattintson a **mentése** a panel tetején, és fogadja el a helyettesítő tanúsítvány aktiválását.

11. Alatt a **SAML-aláíró tanúsítvány** területén kattintson **eltávolítása** eltávolítása a **nem használt** tanúsítványt.

## <a name="saml-request-not-present-in-the-request"></a>SAML-kérelem nem szerepel a kérelemben

*Hiba AADSTS750054: SAMLRequest vagy SAMLResponse, a lekérdezési karakterlánc paramétert az SAML-átirányítási kötés HTTP-kérelem elérhetőnek kell lennie.*

**Lehetséges ok**

Az Azure AD nem lehet azonosítani az SAML-kérelmet a HTTP-kérelem URL-cím paramétereket lévő. Ez akkor fordulhat elő, ha az alkalmazás nem használja a HTTP átirányítás kötés az Azure ad-ben az SAML-kérelmet küld.

**Felbontás**

A kérelmet kell küldenie az SAML-kérelmet a location fejlécet, HTTP átirányítás kötés használatával kódolja. A megvalósítása, további információt, olvassa el a HTTP átirányítás kötés szakaszt az a [SAML protokoll specifikáció szerinti dokumentum](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).


## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>A probléma, ha az alkalmazás küldött SAML-jogcímek testreszabása

A SAML-attribútum jogcímek, az alkalmazás testreszabásával kapcsolatban lásd: [Jogcímtársítások az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) további információt.

## <a name="next-steps"></a>További lépések
[Az Azure ad-ben az SAML-alapú egyszeri bejelentkezés alkalmazások hibakeresése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)
