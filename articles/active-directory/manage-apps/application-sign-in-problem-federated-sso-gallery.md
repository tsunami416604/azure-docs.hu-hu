---
title: Problémák az összevont egyszeri bejelentkezési katalógus alkalmazásba való bejelentkezéskor | Microsoft Docs
description: Útmutató az Azure AD-vel az SAML-alapú összevont egyszeri bejelentkezéshez konfigurált alkalmazásba való bejelentkezéskor megadott hibákhoz
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: mimart
ms.reviewer: luleon, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97954123b6fc31dce09282c08c702438cd64c476
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159250"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Problémák az összevont egyszeri bejelentkezéshez konfigurált Gallery-alkalmazásba való bejelentkezéskor

Az alábbi bejelentkezési problémák elhárításához javasoljuk, hogy kövesse az alábbi javaslatokat, hogy jobb legyen a diagnosztika, és automatizálja a megoldás lépéseit:

- Telepítse a [saját alkalmazások biztonságos böngésző bővítményét](access-panel-extension-problem-installing.md) , hogy Azure Active Directory (Azure ad) jobb diagnosztikai és megoldási funkciókat nyújtson a Azure Portal tesztelési felületének használatakor.
- Reprodukálja a hibát a Azure Portal alkalmazás konfigurációja lapján található tesztelési élmény használatával. További információ az [SAML-alapú egyszeri bejelentkezési alkalmazások hibakereséséről](../azuread-dev/howto-v1-debug-saml-sso-issues.md)


## <a name="application-not-found-in-directory"></a>Az alkalmazás nem található a címtárban

*Hiba AADSTS70001: a (z) "https:\//contoso.com" azonosítójú alkalmazás nem található a címtárban*.

**Lehetséges ok**

Az SAML-kérelemben az alkalmazásból az Azure AD-be elküldett `Issuer` attribútum nem egyezik az alkalmazáshoz az Azure AD-ben konfigurált azonosító értékkel.

**Felbontás**

Győződjön meg arról, hogy az SAML-kérelem `Issuer` attribútuma megegyezik az Azure AD-ben konfigurált azonosító értékkel. Ha a Azure Portal a saját alkalmazások biztonságos böngésző bővítménnyel használja a [tesztelési élményt](../azuread-dev/howto-v1-debug-saml-sso-issues.md) , nem kell manuálisan végrehajtania ezeket a lépéseket.

1.  Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

1.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.

1.  Írja be a **"Azure Active Directory"** kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

1.  Válassza ki a **vállalati alkalmazásokat** a Azure Active Directory bal oldali navigációs menüjéből.

1.  A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.

    Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra**.

1.  Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

1.  Miután az alkalmazás betöltött, nyissa meg az **SAML-alapkonfiguráció** szakaszt. Ellenőrizze, hogy az azonosító szövegmezőben szereplő érték megegyezik-e a hibában megjelenő azonosító értékével.



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>A válasz címe nem egyezik az alkalmazáshoz konfigurált Válaszcím-címekkel.

*Hiba AADSTS50011: a (z) "https:\//contoso.com" válasz-cím nem felel meg az alkalmazáshoz konfigurált válaszoknak.*

**Lehetséges ok**

Az SAML-kérelem `AssertionConsumerServiceURL` értéke nem felel meg az Azure AD-ben konfigurált válasz URL-értéknek vagy mintának. Az SAML-kérelemben szereplő `AssertionConsumerServiceURL` érték a hibaüzenetben látható URL-cím.

**Felbontás**

Győződjön meg arról, hogy az SAML-kérelem `AssertionConsumerServiceURL` értéke megegyezik az Azure AD-ben konfigurált válasz URL-értékkel. Ha a Azure Portal a saját alkalmazások biztonságos böngésző bővítménnyel használja a [tesztelési élményt](../azuread-dev/howto-v1-debug-saml-sso-issues.md) , nem kell manuálisan végrehajtania ezeket a lépéseket.

1.  Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

1.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.

1.  Írja be a **"Azure Active Directory"** kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

1.  Válassza ki a **vállalati alkalmazásokat** a Azure Active Directory bal oldali navigációs menüjéből.

1.  A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.

    Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra**.

1.  Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

1.  Miután az alkalmazás betöltött, nyissa meg az **SAML-alapkonfiguráció** szakaszt. Ellenőrizze vagy frissítse a válasz URL-címe szövegmezőben szereplő értéket, hogy az megfeleljen az SAML-kérelem `AssertionConsumerServiceURL` értékének.    
    
Miután frissítette a válasz URL-értékét az Azure AD-ben, és megfelel az alkalmazás által az SAML-kérelemben küldött értéknek, be kell tudnia jelentkezni az alkalmazásba.

## <a name="user-not-assigned-a-role"></a>A felhasználó nincs szerepkörhöz rendelve

*Hiba AADSTS50105: a (z) "brian\@contoso.com" bejelentkezett felhasználó nincs hozzárendelve az alkalmazás egyik szerepköréhez sem*.

**Lehetséges ok**

A felhasználó nem kapott hozzáférési jogot az alkalmazáshoz az Azure AD-ben.

**Felbontás**

Egy vagy több felhasználó közvetlenül egy alkalmazáshoz való hozzárendeléséhez kövesse az alábbi lépéseket. Ha a Azure Portal a saját alkalmazások biztonságos böngésző bővítménnyel használja a [tesztelési élményt](../azuread-dev/howto-v1-debug-saml-sso-issues.md) , nem kell manuálisan végrehajtania ezeket a lépéseket.

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként**.

1.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.

1.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

1.  Válassza ki a **vállalati alkalmazásokat** a Azure Active Directory bal oldali navigációs menüjéből.

1.  A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.

    Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra**.

1.  Az alkalmazások listájából válassza ki azt, amelyhez felhasználót szeretne hozzárendelni.

1.  Az alkalmazás betöltése után válassza a **felhasználók és csoportok** lehetőséget az alkalmazás bal oldali navigációs menüjében.

1.  Kattintson a **Hozzáadás** gombra a **felhasználók és csoportok** lista tetején a **hozzárendelés hozzáadása** ablaktábla megnyitásához.

1.  Válassza ki a **felhasználók és csoportok** választót a **hozzárendelés hozzáadása** panelen.

1. A **Keresés név vagy e-mail-cím alapján** keresőmezőbe írja be annak a felhasználónak a teljes nevét vagy e-mail-címét, akit hozzá szeretne adni.

1. A **jelölőnégyzet**megjelenítéséhez vigye a kurzort a listában szereplő **felhasználó** fölé. Kattintson a felhasználó profiljának fényképe vagy emblémája melletti jelölőnégyzetre a felhasználó a **kiválasztott** listához való hozzáadásához.

1. Nem **kötelező:** Ha egynél **több felhasználót szeretne felvenni**, írjon be egy másik teljes nevet vagy e-mail-címet a **Keresés név vagy e-mail-cím** Keresés mezőbe, majd a jelölőnégyzetre kattintva adja hozzá a felhasználót a **kiválasztott** listához.

1. Ha végzett a felhasználók kiválasztásával, kattintson a **kiválasztás** gombra, és adja hozzá őket az alkalmazáshoz hozzárendelni kívánt felhasználók és csoportok listájához.

1. Nem **kötelező:** Kattintson a **szerepkör** kiválasztása lehetőségre a **hozzárendelés hozzáadása** panelen, és válassza ki a kiválasztott felhasználókhoz hozzárendelni kívánt szerepkört.

1. Kattintson a **hozzárendelés** gombra az alkalmazás a kijelölt felhasználókhoz való hozzárendeléséhez.

Rövid idő elteltével a kiválasztott felhasználók el tudják indítani ezeket az alkalmazásokat a megoldás leírása szakaszban leírt módszerek alapján.

## <a name="not-a-valid-saml-request"></a>Nem érvényes SAML-kérelem

*AADSTS75005 hiba: a kérés nem érvényes egy saml2 protokoll.*

**Lehetséges ok**

Az Azure AD nem támogatja az alkalmazás egyszeri bejelentkezésre vonatkozó SAML-kérelmét. A leggyakoribb problémák a következők:

-   Kötelező mezők hiányoznak az SAML-kérelemből
-   Az SAML-kérelem metódusa kódolt

**Felbontás**

1. Az SAML-kérelem rögzítése. Kövesse az [SAML-alapú egyszeri bejelentkezés az Azure ad-alkalmazásokban való hibakeresését](../azuread-dev/howto-v1-debug-saml-sso-issues.md) ismertető oktatóanyagot, amelyből megtudhatja, hogyan rögzítheti az SAML-kérelmet.

1. Lépjen kapcsolatba az alkalmazás forgalmazójával, és adja meg a következő adatokat:

   -   SAML-kérelem

   -   [Az Azure AD egyszeri bejelentkezési SAML protokolljának követelményei](../develop/single-sign-on-saml-protocol.md)

Az alkalmazás gyártójának ellenőriznie kell, hogy az Azure AD SAML-implementációja támogatja-e az egyszeri bejelentkezést.

## <a name="misconfigured-application"></a>Hibásan konfigurált alkalmazás

*Hiba történt a AADSTS650056: hibásan konfigurált alkalmazás. Ennek oka az lehet, hogy az ügyfél nem szerepel a "HRE Graph" számára a kért engedélyekben az ügyfél alkalmazás-regisztrációjában. Vagy a rendszergazda nem járult hozzá a bérlőhöz. Vagy ellenőrizze az alkalmazás azonosítóját a kérelemben annak ellenőrzéséhez, hogy az megfelel-e a konfigurált ügyfélalkalmazás-azonosítónak. Kérje meg a rendszergazdát, hogy javítsa ki a konfigurációt vagy a beleegyezik a bérlő nevében*.

**Lehetséges ok**

Az SAML-kérelemben az alkalmazásból az Azure AD-be elküldett `Issuer` attribútum nem egyezik az alkalmazáshoz az Azure AD-ben konfigurált azonosító értékkel.

**Felbontás**

Győződjön meg arról, hogy az SAML-kérelem `Issuer` attribútuma megegyezik az Azure AD-ben konfigurált azonosító értékkel. Ha a Azure Portal a saját alkalmazások biztonságos böngésző bővítménnyel használja a [tesztelési élményt](../azuread-dev/howto-v1-debug-saml-sso-issues.md) , nem kell manuálisan végrehajtania a következő lépéseket:

1.  Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

1.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.

1.  Írja be a **"Azure Active Directory"** kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

1.  Válassza ki a **vállalati alkalmazásokat** a Azure Active Directory bal oldali navigációs menüjéből.

1.  A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.

    Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra**.

1.  Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

1.  Miután az alkalmazás betöltött, nyissa meg az **SAML-alapkonfiguráció** szakaszt. Ellenőrizze, hogy az azonosító szövegmezőben szereplő érték megegyezik-e a hibában megjelenő azonosító értékével.


## <a name="certificate-or-key-not-configured"></a>Nincs konfigurálva a tanúsítvány vagy a kulcs

*Hiba AADSTS50003: nincs konfigurált aláíró kulcs.*

**Lehetséges ok**

Az Application objektum sérült, és az Azure AD nem ismeri fel az alkalmazáshoz konfigurált tanúsítványt.

**Felbontás**

Új tanúsítvány törléséhez és létrehozásához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

1. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

1. Írja be a **"Azure Active Directory"** kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

1. Válassza ki a **vállalati alkalmazásokat** a Azure Active Directory bal oldali navigációs menüjéből.

1. A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.

    Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra**.

1. Válassza ki az egyszeri bejelentkezést konfigurálni kívánt alkalmazást

1. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

1. Az **SAML aláíró tanúsítvány** szakaszban válassza az **új tanúsítvány létrehozása** lehetőséget.

1. Válassza ki a lejárati dátumot, majd kattintson a **Mentés**gombra.

1. Ellenőrizze, **hogy az új tanúsítvány aktív** -e az aktív tanúsítvány felülbírálásához. Ezután kattintson a panel tetején a **Mentés** gombra, és fogadja el a helyettesítő tanúsítvány aktiválását.

1. Az **SAML aláíró tanúsítvány** szakaszban kattintson az **Eltávolítás** gombra a fel nem **használt** tanúsítvány eltávolításához.

## <a name="saml-request-not-present-in-the-request"></a>Az SAML-kérelem nem szerepel a kérelemben

*Hiba AADSTS750054: a SAMLRequest vagy a SAMLResponse lekérdezési karakterlánc paraméterként kell szerepelnie a HTTP-kérelemben az SAML-átirányítási kötéshez.*

**Lehetséges ok**

Az Azure AD nem tudta azonosítani az SAML-kérelmet a HTTP-kérelem URL-paraméterei között. Ez akkor fordulhat elő, ha az alkalmazás nem használ HTTP-átirányítási kötést az SAML-kérelem Azure AD-be való küldésekor.

**Felbontás**

Az alkalmazásnak HTTP-átirányítási kötés használatával kell elküldenie a Location fejlécbe kódolt SAML-kérelmet. Ennek megvalósításáról további információt [az SAML protokoll specifikációját tartalmazó dokumentumban](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf) talál.

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Az Azure AD nem megfelelő végpontnak küldi a jogkivonatot.

**Lehetséges ok**

Az egyszeri bejelentkezés során, ha a bejelentkezési kérés nem tartalmaz explicit válasz URL-címet (a felhasználói szolgáltatás URL-címét), akkor az Azure AD kiválasztja az adott alkalmazáshoz konfigurált összes hivatkozhat URL-címet. Akkor is előfordulhat, ha az alkalmazás explicit válasz URL-címmel van konfigurálva, a felhasználó átirányíthatja https://127.0.0.1:444. 

Amikor az alkalmazás hozzá lett adva nem katalógusbeli alkalmazásként, az Azure Active Directory ezt a válasz-URL-címet alapértelmezett értékként hozta létre. Ez a viselkedés azóta megváltozott, és az Azure Active Directory már nem adja hozzá ezt az URL-címet alapértelmezés szerint. 

**Felbontás**

Törölje az alkalmazáshoz konfigurált nem használt válasz URL-címeket.

1.  Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.

3.  Írja be a **"Azure Active Directory"** kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  Válassza ki a **vállalati alkalmazásokat** a Azure Active Directory bal oldali navigációs menüjéből.

5.  A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.

    Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra**.

6.  Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

7.  Miután az alkalmazás betöltött, nyissa meg az **SAML-alapkonfiguráció** szakaszt. A **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)** területen törölje a rendszer által létrehozott nem használt vagy alapértelmezett válasz URL-címeket. Például: `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Probléma az alkalmazásnak eljuttatott SAML-jogcímek testreszabásakor

Ha meg szeretné tudni, hogyan szabhatja testre az alkalmazásnak eljuttatott SAML-attribútumokat, tekintse meg a [jogcímek leképezése a Azure Active Directory](../develop/active-directory-claims-mapping.md)

## <a name="next-steps"></a>Következő lépések

[Az SAML-alapú egyszeri bejelentkezések hibakeresése az Azure AD-beli alkalmazásokban](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
