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
ms.date: 02/18/2019
ms.author: celested
ms.reviewer: luleon, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 623d684f701df8b1a7c4b84a2bd3840f039ad174
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312699"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Egy összevont egyszeri bejelentkezésre beállított katalógusából származó alkalmazásba történő bejelentkezésnél

Hibaelhárítás a jelentkezzen be az alábbi, javasoljuk, kövesse az alábbi javaslat jobb diagnosztikát és a megoldási lépések automatizálásához:

- Telepítse a [saját alkalmazások biztonságos a böngészőbővítmény](access-panel-extension-problem-installing.md) érdekében az Azure Active Directory (Azure AD) biztosít jobb diagnosztikát és a megoldások használatakor a tesztelés élmény az Azure Portalon.
- A hiba, a vizsgálati élmény használatával az Azure Portalon, az alkalmazás konfigurációs lapja reprodukálnia. További információ a [Debug SAML-alapú egyszeri bejelentkezési alkalmazások](../develop/howto-v1-debug-saml-sso-issues.md)


## <a name="application-not-found-in-directory"></a>Az alkalmazás nem található a címtárban

*Hiba AADSTS70001: Alkalmazás azonosítója a "https:\//contoso.com" nem található a címtárban*.

**Lehetséges ok**

A `Issuer` az alkalmazás Azure ad-ben az SAML-kérelmet küldött attribútum nem felel meg az azonosító értékét, amely az alkalmazás Azure AD-ben van konfigurálva.

**Felbontás**

Ügyeljen arra, hogy a `Issuer` az SAML-kérelmet a attribútuma megfelel az Azure AD-ben konfigurált azonosító értékét. Ha használja a [vizsgálati élmény](../develop/howto-v1-debug-saml-sso-issues.md) az Azure Portalon, a saját alkalmazások biztonságos böngésző-bővítménnyel, nem kell manuálisan kövesse az alábbi lépéseket.

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **társadminisztrátor**.

1.  Nyissa meg a **Azure Active Directory-bővítmény** kiválasztásával **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

1.  Típus **"Azure Active Directory"** a szűrőt a keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

1.  Válassza ki **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

1.  Válassza ki **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

    Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Alkalmazások**.

1.  Válassza ki az egyszeri bejelentkezést a konfigurálni kívánt alkalmazást.

1.  Miután betölti az alkalmazást, nyissa meg a **alapszintű SAML-konfigurációja**. Győződjön meg arról, hogy az azonosító szövegbeviteli mező értéke megegyezik-e értéket az azonosító a jelenik meg a hiba a.



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>A válaszcím nem egyezik az alkalmazáshoz konfigurált válaszcímekkel

*Hiba AADSTS50011: A válaszcím "https:\//contoso.com" nem egyezik meg a az alkalmazáshoz konfigurált válaszcímekkel*

**Lehetséges ok**

A `AssertionConsumerServiceURL` a SAML-kérelemben szereplő érték nem felel meg, a válasz URL-cím értékét vagy az Azure ad-ben konfigurált minta. A `AssertionConsumerServiceURL` az SAML-kérelmet érték jelenik meg a hiba az URL-CÍMÉT.

**Felbontás**

Ügyeljen arra, hogy a `AssertionConsumerServiceURL` az SAML-kérelmet a válasz URL-cím értéke megegyezik az Azure ad-ben konfigurált. Ha használja a [vizsgálati élmény](../develop/howto-v1-debug-saml-sso-issues.md) az Azure Portalon, a saját alkalmazások biztonságos böngésző-bővítménnyel, nem kell manuálisan kövesse az alábbi lépéseket.

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **társadminisztrátor**.

1.  Nyissa meg a **Azure Active Directory-bővítmény** kiválasztásával **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

1.  Típus **"Azure Active Directory"** a szűrőt a keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

1.  Válassza ki **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

1.  Válassza ki **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

    Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Alkalmazások**.

1.  Válassza ki az egyszeri bejelentkezést a konfigurálni kívánt alkalmazást.

1.  Miután betölti az alkalmazást, nyissa meg a **alapszintű SAML-konfigurációja**. Győződjön meg arról, vagy frissítse az értéket a megfelelő válasz URL-címe szövegmezőbe a `AssertionConsumerServiceURL` értékét az SAML-kérelmet.    
    
Miután frissítette az Azure ad-ben a válasz URL-Címének értékét, és a kulcs megegyezik-e az SAML-kérelmet a az alkalmazás által küldött értékét, jelentkezzen be az alkalmazást, képesnek kell lennie.

## <a name="user-not-assigned-a-role"></a>Felhasználói szerepkör nincs hozzárendelve

*Hiba AADSTS50105: A bejelentkezett felhasználó "brian\@contoso.com" nincs hozzárendelve egy szerepkörhöz az alkalmazás*.

**Lehetséges ok**

A felhasználó nem kapott hozzáférést az alkalmazáshoz az Azure ad-ben.

**Felbontás**

Közvetlenül rendelni egy vagy több felhasználó egy alkalmazást, kövesse az alábbi lépéseket. Ha használja a [vizsgálati élmény](../develop/howto-v1-debug-saml-sso-issues.md) az Azure Portalon, a saját alkalmazások biztonságos böngésző-bővítménnyel, nem kell manuálisan kövesse az alábbi lépéseket.

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai**.

1.  Nyissa meg a **Azure Active Directory-bővítmény** kiválasztásával **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

1.  Típus **"Azure Active Directory**" a szűrőt a keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

1.  Válassza ki **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

1.  Válassza ki **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

    Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Alkalmazások**.

1.  Alkalmazások listájából válassza ki a kívánt rendelje hozzá egy felhasználót.

1.  Miután betölti az alkalmazást, válassza ki **felhasználók és csoportok** az alkalmazás bal oldali navigációs menüjében.

1.  Kattintson a **Hozzáadás** a gombot a **felhasználók és csoportok** listán, megnyílik a **hozzárendelés hozzáadása** ablaktáblán.

1.  Válassza ki a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

1. Az a **Keresés név vagy e-mail cím alapján** keresőmezőbe írja be a teljes név vagy e-mail-címét, akit szeretne hozzáadni.

1. A kurzort a **felhasználói** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a felhasználó profilfényképének vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

1. **Nem kötelező:** Ha szeretné **egynél több felhasználó hozzáadása**, adjon meg egy másik teljes nevet vagy e-mail-címét írja be a **Keresés név vagy e-mail cím alapján** keresőmezőbe, majd kattintson a jelölőnégyzet bejelölésével adja hozzá a felhasználót a **kiválasztva**  listája.

1. Ha elkészült, válassza a felhasználók, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

1. **Nem kötelező:** Kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

1. Kattintson a **hozzárendelése** gombra kattintva rendelje hozzá az alkalmazás a kijelölt felhasználók számára.

Után rövid idő alatt a kiválasztott felhasználók ezeket az alkalmazásokat a megoldás leírása szakaszban ismertetett módszerekkel indítására lesz.

## <a name="not-a-valid-saml-request"></a>Nem egy érvényes SAML-kérelem

*Hiba AADSTS75005: A kérés nem érvényes egy Saml2 protokoll üzenetet.*

**Lehetséges ok**

Az Azure AD nem támogatja az egyszeri bejelentkezés az alkalmazás által küldött SAML-kérelmet. Néhány gyakori hibák a következők:

-   Az SAML-kérelemből hiányzó kötelező mezők
-   SAML-kérelem kódolási metódusa

**Felbontás**

1. Rögzítse az SAML-kérelmet. az oktatóanyag [az Azure ad-ben az SAML-alapú egyszeri bejelentkezés alkalmazások hibakeresése](../develop/howto-v1-debug-saml-sso-issues.md) megtudhatja, hogyan rögzítheti az SAML-kérelmet.

1. Az alkalmazás gyártójától, és megoszthatja a következő adatokat:

   -   SAML-kérelem

   -   [Az Azure AD egyszeri bejelentkezési SAML protokolljának követelményei](../develop/single-sign-on-saml-protocol.md)

Az alkalmazás gyártójától ellenőrizni kell, hogy az egyszeri bejelentkezés az Azure AD SAML-implementáció támogatják.

## <a name="misconfigured-application"></a>Helytelenül konfigurált alkalmazás

*Hiba AADSTS650056: Az alkalmazás helytelenül konfigurált. Ezt a következők egyike okozhatja: Az ügyfél nem rendelkezik szerepel a listán az "AAD Graph" az alkalmazás regisztrálása az ügyfél a kért engedélyek engedélyek. Másik lehetőségként a rendszergazda nem egyezett bele a bérlőben. Vagy tekintse meg az alkalmazásazonosító, annak érdekében, hogy a konfigurált alkalmazás azonosítója egyezik a kérésben. Lépjen kapcsolatba a rendszergazdát, hogy a konfiguráció kijavításának vagy hozzájárulás megadása a bérlő nevében.* .

**Lehetséges ok**

A `Issuer` az alkalmazás Azure ad-ben az SAML-kérelmet küldött attribútum nem felel meg az Azure AD-ben az alkalmazáshoz konfigurált azonosító értékét.

**Felbontás**

Ügyeljen arra, hogy a `Issuer` az SAML-kérelmet a attribútuma megfelel az Azure AD-ben konfigurált azonosító értékét. Ha használja a [vizsgálati élmény](../develop/howto-v1-debug-saml-sso-issues.md) az Azure Portalon, a saját alkalmazások biztonságos böngésző-bővítménnyel, nem kell manuálisan kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **társadminisztrátor**.

1.  Nyissa meg a **Azure Active Directory-bővítmény** kiválasztásával **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

1.  Típus **"Azure Active Directory"** a szűrőt a keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

1.  Válassza ki **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

1.  Válassza ki **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

    Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Alkalmazások**.

1.  Válassza ki az egyszeri bejelentkezést a konfigurálni kívánt alkalmazást.

1.  Miután betölti az alkalmazást, nyissa meg a **alapszintű SAML-konfigurációja**. Győződjön meg arról, hogy az azonosító szövegbeviteli mező értéke megegyezik-e értéket az azonosító a jelenik meg a hiba a.


## <a name="certificate-or-key-not-configured"></a>Tanúsítvány és kulcs nincs konfigurálva

*Hiba AADSTS50003: Nincs konfigurálva aláírási kulcsát.*

**Lehetséges ok**

Az alkalmazás objektum sérült, és az Azure AD nem ismeri fel a tanúsítványt az alkalmazáshoz konfigurált.

**Felbontás**

Törölje, és hozzon létre egy új tanúsítványt, kövesse az alábbi lépéseket:

1. Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **társadminisztrátor**.

1. Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

1. Típus **"Azure Active Directory"** a szűrőt a keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

1. Válassza ki **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

1. Válassza ki **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

    Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Alkalmazások**.

1. Válassza ki az alkalmazást szeretné az egyszeri bejelentkezés konfigurálása

1. Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

1. Válassza ki **új tanúsítvány létrehozása** alatt a **SAML-aláíró tanúsítvány** szakaszban.

1. Válassza ki a lejárati dátumot, és kattintson a **mentése**.

1. Ellenőrizze **új tanúsítvány aktívvá** az aktív tanúsítvány felülbírálásához. Kattintson a **mentése** a panel tetején, és fogadja el a helyettesítő tanúsítvány aktiválását.

1. Alatt a **SAML-aláíró tanúsítvány** területén kattintson **eltávolítása** eltávolítása a **nem használt** tanúsítványt.

## <a name="saml-request-not-present-in-the-request"></a>SAML-kérelem nem szerepel a kérelemben

*Hiba AADSTS750054: SAMLRequest vagy SAMLResponse, a lekérdezési karakterlánc paramétert az SAML-átirányítási kötés HTTP-kérelem elérhetőnek kell lennie.*

**Lehetséges ok**

Az Azure AD nem lehet azonosítani az SAML-kérelmet a HTTP-kérelem URL-cím paramétereket lévő. Ez akkor fordulhat elő, ha az alkalmazás nem használja a HTTP-átirányítás kötés az Azure ad-hez a SAML-kérelem küldésekor.

**Felbontás**

A kérelmet kell küldenie az SAML-kérelmet kódolja a location fejlécet, HTTP-n keresztül átirányítani a kötést. A megvalósítása, további információt, olvassa el a HTTP átirányítás kötés szakaszt az a [SAML protokoll specifikáció szerinti dokumentum](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure ad-ben a jogkivonat küld egy nem megfelelő végpont

**Lehetséges ok**

Során egyszeri bejelentkezés Ha a bejelentkezési kérés nem tartalmaz explicit válasz URL-cím (helyességi feltétel fogyasztói szolgáltatás URL-címe), majd az Azure AD választ ki a konfigurált bármelyikét használja a kereső URL-címeket az adott alkalmazáshoz. Akkor is, ha az alkalmazás konfigurálása kifejezett válasz URL-cím, a felhasználó lehet átirányítja https://127.0.0.1:444. 

Amikor az alkalmazás hozzá lett adva nem katalógusbeli alkalmazásként, az Azure Active Directory ezt a válasz-URL-címet alapértelmezett értékként hozta létre. Ez a viselkedés azóta megváltozott, és az Azure Active Directory már nem adja hozzá ezt az URL-címet alapértelmezés szerint. 

**Felbontás**

Törölje a nem használt válasz-URL az alkalmazáshoz konfigurált.

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **társadminisztrátor**.

2.  Nyissa meg a **Azure Active Directory-bővítmény** kiválasztásával **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Típus **"Azure Active Directory"** a szűrőt a keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Válassza ki **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Válassza ki **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

    Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Alkalmazások**.

6.  Válassza ki az egyszeri bejelentkezést a konfigurálni kívánt alkalmazást.

7.  Miután betölti az alkalmazást, nyissa meg a **alapszintű SAML-konfigurációja**. Az a **válasz URL-cím (helyességi feltétel fogyasztói szolgáltatás URL-címe)**, a rendszer által létrehozott alapértelmezett válasz URL-címek vagy fel nem használt törlése. Például: `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>A probléma, ha az alkalmazás küldött SAML-jogcímek testreszabása

A SAML-attribútum jogcímek, az alkalmazás testreszabásával kapcsolatban lásd: [Jogcímtársítások az Azure Active Directory](../develop/active-directory-claims-mapping.md).

## <a name="next-steps"></a>További lépések

[Az Azure ad-ben az SAML-alapú egyszeri bejelentkezés alkalmazások hibakeresése](../develop/howto-v1-debug-saml-sso-issues.md)
