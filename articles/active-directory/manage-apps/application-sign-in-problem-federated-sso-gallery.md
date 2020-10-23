---
title: Problémák az SAML-alapú egyszeri bejelentkezés konfigurált alkalmazásaiba való bejelentkezéskor
description: Útmutató az SAML-alapú összevont egyszeri bejelentkezéshez konfigurált alkalmazásba való bejelentkezéskor megadott hibákhoz Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/18/2019
ms.author: kenwith
ms.reviewer: luleon, asteen
ms.custom: contperfq2
ms.openlocfilehash: ec39a6d106973808e26b7c06dce8b3054af490ff
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427385"
---
# <a name="problems-signing-in-to-saml-based-single-sign-on-configured-apps"></a>Problémák az SAML-alapú egyszeri bejelentkezés konfigurált alkalmazásaiba való bejelentkezéskor
Az alábbi bejelentkezési problémák elhárítása érdekében javasoljuk, hogy a következő lépésekkel jobban diagnosztizálja és automatizálja a megoldási lépéseket:

- Telepítse a [saját alkalmazások biztonságos böngésző bővítményét](access-panel-extension-problem-installing.md) , hogy Azure Active Directory (Azure ad) jobb diagnosztikai és megoldási funkciókat nyújtson a Azure Portal tesztelési felületének használatakor.
- Reprodukálja a hibát a Azure Portal alkalmazás konfigurációja lapján található tesztelési élmény használatával. További információ az [SAML-alapú egyszeri bejelentkezési alkalmazások hibakereséséről](../azuread-dev/howto-v1-debug-saml-sso-issues.md)

Ha a Azure Portal a saját alkalmazások biztonságos böngésző bővítménnyel használja a [tesztelési](../azuread-dev/howto-v1-debug-saml-sso-issues.md) funkciót, az alábbi lépéseket nem kell manuálisan végrehajtania az SAML-alapú egyszeri bejelentkezés konfigurációjának megnyitásához.

Az SAML-alapú egyszeri bejelentkezési konfiguráció oldalának megnyitása:
1.  Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** **vagy felügyelőként.**
1.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.
1.  Írja be a **"Azure Active Directory"** kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.
1.  Válassza ki a **vállalati alkalmazásokat** a Azure Active Directory bal oldali navigációs menüjéből.
1.  A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.
    Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra**.
1.  Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.
1. Az alkalmazás betöltése után válassza az **egyszeri bejelentkezés** lehetőséget az alkalmazás bal oldali navigációs menüjében.
1. Válassza ki az SAML-alapú egyszeri bejelentkezést.

## <a name="application-not-found-in-directory"></a>Az alkalmazás nem található a címtárban
`Error AADSTS70001: Application with Identifier 'https:\//contoso.com' was not found in the directory.`

**Lehetséges ok**

Az `Issuer` SAML-kérelemben az alkalmazásból az Azure ad-be elküldett attribútum nem egyezik az alkalmazáshoz az Azure ad-ben konfigurált azonosító értékkel.

**Resolution** (Osztás)

Győződjön meg arról, hogy az `Issuer` SAML-kérelemben szereplő attribútum megegyezik az Azure ad-ben konfigurált azonosító értékkel.

Az SAML-alapú SSO-konfiguráció lap **alapszintű SAML-konfiguráció** szakaszában ellenőrizze, hogy az azonosító szövegmezőben szereplő érték megegyezik-e a hibában megjelenő azonosító értékével.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>A válasz címe nem egyezik az alkalmazáshoz konfigurált Válaszcím-címekkel.
`Error AADSTS50011: The reply address 'https:\//contoso.com' does not match the reply addresses configured for the application.`

**Lehetséges ok**

Az `AssertionConsumerServiceURL` SAML-kérelemben szereplő érték nem felel meg az Azure ad-ben konfigurált válasz URL-értéknek vagy mintának. Az `AssertionConsumerServiceURL` SAML-kérelemben szereplő érték a hibaüzenetben látható URL-cím.

**Resolution** (Osztás)

Győződjön meg arról, hogy az `AssertionConsumerServiceURL` SAML-kérelemben szereplő érték megegyezik az Azure ad-ben konfigurált válasz URL-értékkel. 

Ellenőrizze vagy frissítse a válasz URL-címe szövegmezőben szereplő értéket, hogy az megfeleljen az `AssertionConsumerServiceURL` SAML-kérelemben szereplő értéknek.   
    
Miután frissítette a válasz URL-értékét az Azure AD-ben, és megfelel az alkalmazás által az SAML-kérelemben küldött értéknek, be kell tudnia jelentkezni az alkalmazásba.

## <a name="user-not-assigned-a-role"></a>A felhasználó nincs szerepkörhöz rendelve
`Error AADSTS50105: The signed in user 'brian\@contoso.com' is not assigned to a role for the application.`

**Lehetséges ok**

A felhasználó nem kapott hozzáférési jogot az alkalmazáshoz az Azure AD-ben.

**Resolution** (Osztás)

Ha egy vagy több felhasználót közvetlenül szeretne hozzárendelni egy alkalmazáshoz, tekintse meg a gyors útmutató [: felhasználók kiosztása egy](add-application-portal-assign-users.md)alkalmazáshoz című témakört.

## <a name="not-a-valid-saml-request"></a>Nem érvényes SAML-kérelem
`Error AADSTS75005: The request is not a valid Saml2 protocol message.`

**Lehetséges ok**

Az Azure AD nem támogatja az alkalmazás egyszeri bejelentkezésre vonatkozó SAML-kérelmét. A leggyakoribb problémák a következők:
- Kötelező mezők hiányoznak az SAML-kérelemből
- Az SAML-kérelem metódusa kódolt

**Resolution** (Osztás)

1. Az SAML-kérelem rögzítése. Kövesse az [SAML-alapú egyszeri bejelentkezés az Azure ad-alkalmazásokban való hibakeresését](../azuread-dev/howto-v1-debug-saml-sso-issues.md) ismertető oktatóanyagot, amelyből megtudhatja, hogyan rögzítheti az SAML-kérelmet.
1. Lépjen kapcsolatba az alkalmazás forgalmazójával, és adja meg a következő adatokat:
    - SAML-kérelem
    - [Az Azure AD egyszeri bejelentkezési SAML protokolljának követelményei](../develop/single-sign-on-saml-protocol.md)

Az alkalmazás gyártójának ellenőriznie kell, hogy az Azure AD SAML-implementációja támogatja-e az egyszeri bejelentkezést.

## <a name="misconfigured-application"></a>Hibásan konfigurált alkalmazás
`Error AADSTS650056: Misconfigured application. This could be due to one of the following: The client has not listed any permissions in the requested permissions in the client's application registration. Or, The admin has not consented in the tenant. Or, Check the application identifier in the request to ensure it matches the configured client application identifier. Please contact your admin to fix the configuration or consent on behalf of the tenant.`

**Lehetséges ok**

Az `Issuer` SAML-kérelemben az alkalmazásból az Azure ad-be elküldett attribútum nem egyezik az alkalmazáshoz az Azure ad-ben konfigurált azonosító értékkel.

**Resolution** (Osztás)

Győződjön meg arról, hogy az `Issuer` SAML-kérelemben szereplő attribútum megegyezik az Azure ad-ben konfigurált azonosító értékkel. 

Ellenőrizze, hogy az azonosító szövegmezőben szereplő érték megegyezik-e a hibában megjelenő azonosító értékével.

## <a name="certificate-or-key-not-configured"></a>Nincs konfigurálva a tanúsítvány vagy a kulcs
`Error AADSTS50003: No signing key configured.`

**Lehetséges ok**

Az Application objektum sérült, és az Azure AD nem ismeri fel az alkalmazáshoz konfigurált tanúsítványt.

**Resolution** (Osztás)

Új tanúsítvány törléséhez és létrehozásához kövesse az alábbi lépéseket:
1. Az SAML-alapú SSO konfigurációs képernyőjén válassza az **új tanúsítvány létrehozása** az **SAML aláíró tanúsítvány** szakaszban lehetőséget.
1. Válassza ki a lejárati dátumot, majd kattintson a **Mentés**gombra.
1. Ellenőrizze, **hogy az új tanúsítvány aktív** -e az aktív tanúsítvány felülbírálásához. Ezután kattintson a panel tetején a **Mentés** gombra, és fogadja el a helyettesítő tanúsítvány aktiválását.
1. Az **SAML aláíró tanúsítvány** szakaszban kattintson az **Eltávolítás** gombra a fel nem **használt** tanúsítvány eltávolításához.

## <a name="saml-request-not-present-in-the-request"></a>Az SAML-kérelem nem szerepel a kérelemben
`Error AADSTS750054: SAMLRequest or SAMLResponse must be present as query string parameters in HTTP request for SAML Redirect binding.`

**Lehetséges ok**

Az Azure AD nem tudta azonosítani az SAML-kérelmet a HTTP-kérelem URL-paraméterei között. Ez akkor fordulhat elő, ha az alkalmazás nem használ HTTP-átirányítási kötést az SAML-kérelem Azure AD-be való küldésekor.

**Resolution** (Osztás)

Az alkalmazásnak HTTP-átirányítási kötés használatával kell elküldenie a Location fejlécbe kódolt SAML-kérelmet. Ennek megvalósításáról további információt [az SAML protokoll specifikációját tartalmazó dokumentumban](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf) talál.

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Az Azure AD nem megfelelő végpontnak küldi a jogkivonatot.
**Lehetséges ok**

Az egyszeri bejelentkezés során, ha a bejelentkezési kérés nem tartalmaz explicit válasz URL-címet (a felhasználói szolgáltatás URL-címét), akkor az Azure AD kiválasztja az adott alkalmazáshoz konfigurált válasz URL-címeket. Annak ellenére, hogy az alkalmazás explicit válasz URL-címmel van konfigurálva, a felhasználó átirányítható https://127.0.0.1:444 . 

Amikor az alkalmazás hozzá lett adva nem katalógusbeli alkalmazásként, az Azure Active Directory ezt a válasz-URL-címet alapértelmezett értékként hozta létre. Ez a viselkedés azóta megváltozott, és az Azure Active Directory már nem adja hozzá ezt az URL-címet alapértelmezés szerint. 

**Resolution** (Osztás)

Törölje az alkalmazáshoz konfigurált nem használt válasz URL-címeket.

Az SAML-alapú SSO-konfiguráció lapon a **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)** szakaszban törölje a rendszer által létrehozott nem használt vagy alapértelmezett válasz URL-címeket. Például: `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Probléma az alkalmazásnak eljuttatott SAML-jogcímek testreszabásakor
Ha meg szeretné tudni, hogyan szabhatja testre az alkalmazásnak eljuttatott SAML-attribútumokat, tekintse meg a [jogcímek leképezése a Azure Active Directory](../develop/active-directory-claims-mapping.md)

## <a name="errors-related-to-misconfigured-apps"></a>Helytelenül konfigurált alkalmazásokkal kapcsolatos hibák
Ellenőrizze, hogy a portálon lévő konfigurációk megfelelnek-e az alkalmazásnak. Az ügyfél/alkalmazás-azonosító, a válasz URL-címek, az ügyfél titkos kulcsainak és a kulcsok, valamint az alkalmazás-azonosító URI összehasonlítása.

Hasonlítsa össze azt az erőforrást, amelyhez hozzáférést kér a kódban a **szükséges erőforrások** lapon beállított engedélyekkel, és győződjön meg arról, hogy csak a konfigurált erőforrásokat kéri.

## <a name="next-steps"></a>További lépések
- [Gyorsindítás sorozat az alkalmazás-kezelésben](add-application-portal-assign-users.md)
- [Az SAML-alapú egyszeri bejelentkezések hibakeresése az Azure AD-beli alkalmazásokban](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
- [Az Azure AD egyszeri bejelentkezési SAML protokolljának követelményei](../develop/active-directory-single-sign-on-protocol-reference.md)
