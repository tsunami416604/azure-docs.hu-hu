---
title: Problémák a nem katalógusba való bejelentkezéskori egyszeri bejelentkezési alkalmazásba
description: Útmutató az Azure AD-vel való SAML-alapú összevont egyszeri bejelentkezéshez konfigurált alkalmazásba való bejelentkezéskor felmerülő konkrét problémákhoz
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
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cd951f0b4d2f4887630e29cbd3b0ae429b9f6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367857"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problémák az összevont egyszeri bejelentkezéshez konfigurált nem katalógusalkalmazásba való bejelentkezéssel kapcsolatban

Az alábbi bejelentkezési problémák elhárításához javasoljuk, hogy kövesse az alábbi javaslatokat a jobb diagnózis érdekében, és automatizálja a megoldási lépéseket:

- Telepítse a [My Apps Secure Browser Extension-t,](access-panel-extension-problem-installing.md) hogy az Azure Active Directory (Azure AD) jobb diagnózist és felbontást nyújthasson az Azure Portalon a tesztelési élmény használatakor.
- Reprodukálja a hibát az Azure Portal alkalmazáskonfigurációs lapján található tesztelési felület használatával. További információ a [Debug SAML-alapú egyszeri bejelentkezési alkalmazásokról](../azuread-dev/howto-v1-debug-saml-sso-issues.md)

## <a name="application-not-found-in-directory"></a>Az alkalmazás nem található a könyvtárban

*Hiba AADSTS70001: Az `https://contoso.com` azonosítóval rendelkező alkalmazás nem található a címtárban.*

**Lehetséges ok**

A Kiállító attribútum küld az alkalmazásból az Azure AD az SAML-kérelem nem egyezik meg az Azure AD-ben konfigurált azonosító érték.

**Resolution** (Osztás)

Győződjön `Issuer` meg arról, hogy az SAML-kérelemben lévő attribútum megegyezik az Azure AD-ben konfigurált azonosító értékkel. Ha az Azure Portalon a My Apps Secure Browser Extension használatával használja a [tesztelési élményt,](../azuread-dev/howto-v1-debug-saml-sso-issues.md) nem kell manuálisan végrehajtania ezeket a lépéseket.

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Jelölje ki az egyszeri bejelentkezést konfigurálni kívánt alkalmazást.

7. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8. Miután az alkalmazás betöltött, nyissa meg az **SAML-alapkonfiguráció** szakaszt. Ellenőrizze, hogy az Azonosító szövegmezőben lévő érték megegyezik-e a hibában megjelenített azonosítóérték értékével.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>A válaszcím nem egyezik meg az alkalmazáshoz konfigurált válaszcímekkel. 

*Hiba AADSTS50011: A `https://contoso.com` válasz cím nem egyezik meg az alkalmazáshoz beállított válaszcímekkel* 

**Lehetséges ok** 

Az SAML-kérelemben az AssertionConsumerServiceURL értéke nem felel meg az Azure AD-ben konfigurált válasz-URL-cím értékének vagy mintázatának. Az SAML-kérelemben az AssertionConsumerServiceURL értéke a hibában szereplő URL-cím. 

**Resolution** (Osztás) 

Győződjön `Issuer` meg arról, hogy az SAML-kérelemben lévő attribútum megegyezik az Azure AD-ben konfigurált azonosító értékkel. Ha az Azure Portalon a My Apps Secure Browser Extension használatával használja a [tesztelési élményt,](../azuread-dev/howto-v1-debug-saml-sso-issues.md) nem kell manuálisan végrehajtania ezeket a lépéseket.
 
1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.** 

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva. 

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.** 

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra. 

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez. 

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**
  
6. Válassza ki az egyszeri bejelentkezést konfigurálni kívánt alkalmazást

7. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8. Miután az alkalmazás betöltött, nyissa meg az **SAML-alapkonfiguráció** szakaszt. Ellenőrizze vagy frissítse az értéket a Válasz `AssertionConsumerServiceURL` URL-cím mezőjében, hogy megfeleljen az SAML-kérelemben megadott értéknek.    
    
Miután frissítette a válasz URL-értékét az Azure AD-ben, és megegyezik az alkalmazás által az SAML-kérelemben küldött értékkel, képesnek kell lennie arra, hogy jelentkezzen be az alkalmazásba.

## <a name="user-not-assigned-a-role"></a>A felhasználónem kapott szerepkört

*Hiba AADSTS50105: A `brian\@contoso.com` bejelentkezett felhasználó nincs hozzárendelve az alkalmazás szerepköréhez*

**Lehetséges ok**

A felhasználó nem kapott hozzáférési jogot az alkalmazáshoz az Azure AD-ben.

**Resolution** (Osztás)

Ha egy vagy több felhasználót közvetlenül szeretne egy alkalmazáshoz rendelni, kövesse az alábbi lépéseket. Ha az Azure Portalon a My Apps Secure Browser Extension használatával használja a [tesztelési élményt,](../azuread-dev/howto-v1-debug-saml-sso-issues.md) nem kell manuálisan végrehajtania ezeket a lépéseket.

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. A listából jelölje ki azt az alkalmazást, amelyhez felhasználót szeretne rendelni.

7. Miután az alkalmazás betöltődik, kattintson az alkalmazás bal oldali navigációs menüjének **Felhasználók és csoportok** parancsára.

8. Kattintson a **Hozzáadás** gombra a **Felhasználók és csoportok** lista tetején a Hozzárendelés **hozzáadása** ablaktábla megnyitásához.

9. kattintson a **Felhasználók és csoportok** választóra a Hozzárendelés **hozzáadása** ablaktáblán.

10. Írja be annak a felhasználónak a **teljes nevét** vagy **e-mail címét,** akit szeretne hozzárendelni a **Keresés név vagy e-mail cím** alapján keresőmezőbe.

11. A **jelölőnégyzet**felfedéséhez mutasson a **felhasználó** fölé a listában. Kattintson a felhasználó profilfotója vagy emblémája melletti jelölőnégyzetre, ha hozzá szeretné adni a felhasználót a **Kijelölt** listához.

12. **Nem kötelező:** Ha **egynél több felhasználót**szeretne felvenni, írja be a másik **teljes nevet** vagy **e-mail címet** a Keresés név vagy **e-mail cím** alapján keresőmezőbe, és kattintson a jelölőnégyzetre, ha hozzá szeretné adni a **felhasználót** a Kiválasztott listához.

13. Ha befejezte a felhasználók kiválasztását, a **Kijelölés gombra** kattintva hozzáadhatja őket az alkalmazáshoz rendelendő felhasználók és csoportok listájához.

14. **Nem kötelező:** kattintson a Hozzárendelés **hozzáadása** ablaktábla **Szerepkör kiválasztása** kijelölő gombra a kijelölt felhasználókhoz rendelő szerepkör kiválasztásához.

15. Kattintson a **Hozzárendelés** gombra az alkalmazás kijelölt felhasználókhoz rendeléséhez.

Rövid idő elteltével a kiválasztott felhasználók elindíthatják ezeket az alkalmazásokat a megoldás leírásában a szakaszban leírt módszerekkel.

## <a name="not-a-valid-saml-request"></a>Nem érvényes SAML-kérelem

*Hiba AADSTS75005: A kérelem nem érvényes Saml2 protokoll üzenet.*

**Lehetséges ok**

Az Azure AD nem támogatja az alkalmazás egyszeri bejelentkezésre vonatkozó SAML-kérelmét. A leggyakoribb problémák a következők:

-   Kötelező mezők hiányoznak az SAML-kérelemből

-   Az SAML-kérelem metódusa kódolt

**Resolution** (Osztás)

1.  Saml-kérelem rögzítése. kövesse az [azure-beli alkalmazásokra való saml-alapú egyszeri bejelentkezés hibakereséséről](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) szóló oktatóanyagot, amelyből megtudhatja, hogyan rögzítheti az SAML-kérelmet.

2.  Lépjen kapcsolatba az alkalmazás forgalmazójával, és ossza meg a következőket:

    -   SAML-kérelem

    -   [Az Azure AD egyszeri bejelentkezési SAML protokollkövetelményei](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Az alkalmazás szállítójának ellenőriznie kell, hogy támogatják-e az Azure AD SAML implementációegyszeri bejelentkezéshez.

## <a name="misconfigured-application"></a>Helytelenül konfigurált alkalmazás

*Hiba AADSTS650056: Rosszul konfigurált alkalmazás. Ennek oka a következő: Az ügyfél nem szerepel a kért engedélyek az ügyfél alkalmazásregisztrációjában. Vagy, A rendszergazda nem járult hozzá a bérlő. Vagy ellenőrizze az alkalmazásazonosítót a kérelemben, és győződjön meg arról, hogy az megfelel a konfigurált ügyfélalkalmazás-azonosítónak. Kérjük, forduljon a rendszergazdához, hogy javítsa ki a konfigurációt vagy a hozzájárulást a bérlő nevében.*. .

**Lehetséges ok**

Az `Issuer` alkalmazásból az Azure AD-nek az SAML-kérelemben küldött attribútum nem egyezik meg az alkalmazáshoz az Azure AD-ben konfigurált azonosító értékkel.

**Resolution** (Osztás)

Győződjön `Issuer` meg arról, hogy az SAML-kérelemben lévő attribútum megegyezik az Azure AD-ben konfigurált azonosító értékkel. Ha az Azure Portalon a My Apps Secure Browser Extension használatával használja a [tesztelési élményt,](../azuread-dev/howto-v1-debug-saml-sso-issues.md) nem kell manuálisan végrehajtania az alábbi lépéseket:

1.  Nyissa meg az [**Azure-portált,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

1.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Minden szolgáltatás** kiválasztásával.

1.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

1.  Válassza a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüben válassza ki.

1.  Válassza **a Minden alkalmazás** lehetőséget az összes alkalmazás listájának megtekintéséhez.

    Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást a **Minden alkalmazás**beállításra.

1.  Válassza ki azt az alkalmazást, amelyet egyszeri bejelentkezéshez szeretne konfigurálni.

1.  Miután az alkalmazás betöltött, nyissa meg az **SAML-alapkonfiguráció** szakaszt. Ellenőrizze, hogy az Azonosító szövegmezőben lévő érték megegyezik-e a hibában megjelenített azonosítóérték értékével.

## <a name="certificate-or-key-not-configured"></a>A tanúsítvány vagy kulcs nincs konfigurálva

Hiba AADSTS50003: Nincs aláíró kulcs konfigurálva.

**Lehetséges ok**

Az alkalmazásobjektum sérült, és az Azure AD nem ismeri fel az alkalmazáshoz konfigurált tanúsítványt.

**Resolution** (Osztás)

Új tanúsítvány törléséhez és létrehozásához kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Jelölje ki az egyszeri bejelentkezést konfigurálni kívánt alkalmazást.

7. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8. Kattintson az **Új tanúsítvány létrehozása elemre** az **SAML aláíró tanúsítvány** szakaszban.

9. Válassza a Lejárati dátumot. Ezután kattintson a **Mentés gombra.**

10. Jelölje be **Az új tanúsítvány aktívvá átalakítása** az aktív tanúsítvány felülbírálásához jelölőnégyzetet. Ezután kattintson a panel tetején a **Mentés** gombra, és fogadja el a helyettesítő tanúsítvány aktiválását.

11. Az **SAML aláíró tanúsítvány csoportban** kattintson az **Eltávolítás** gombra a **nem használt** tanúsítvány eltávolításához.

## <a name="saml-request-not-present-in-the-request"></a>Saml kérelem nem szerepel a kérelemben

*AADSTS750054 hiba: Az SAMLRequest vagy AZ SAMLResponse protokollnak lekérdezési karakterlánc-paraméterként kell lennie az SAML átirányítási kötés HTTP-kérelmében.*

**Lehetséges ok**

Az Azure AD nem tudta azonosítani az SAML-kérelmet a HTTP-kérelem URL-paraméterein belül. Ez akkor fordulhat elő, ha az alkalmazás nem http-átirányítási kötést használ, amikor elküldi az SAML-kérelmet az Azure AD-nek.

**Resolution** (Osztás)

Az alkalmazásnak http-átirányítási kötéssel kell elküldenie a helyfejlécbe kódolt SAML-kérelmet. Ennek megvalósításáról további információt [az SAML protokoll specifikációját tartalmazó dokumentumban](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf) talál.

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Az Azure AD helytelen végpontra küldi a jogkivonatot

**Lehetséges ok**

Egyszeri bejelentkezés közben, ha a bejelentkezési kérelem nem tartalmaz explicit válasz URL-címet (helyességi feltétel fogyasztói szolgáltatás URL-jét), majd az Azure AD kiválasztja a konfigurált támaszkodó URL-eket az adott alkalmazáshoz. Még akkor is, ha az alkalmazás explicit válasz URL-címet konfigurált, a felhasználó lehet, hogy átirányítja https://127.0.0.1:444. 

Amikor az alkalmazás hozzá lett adva nem katalógusbeli alkalmazásként, az Azure Active Directory ezt a válasz-URL-címet alapértelmezett értékként hozta létre. Ez a viselkedés azóta megváltozott, és az Azure Active Directory már nem adja hozzá ezt az URL-címet alapértelmezés szerint. 

**Resolution** (Osztás)

Törölje az alkalmazáshoz konfigurált nem használt válasz URL-címeket.

1.  Nyissa meg az [**Azure-portált,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Minden szolgáltatás** kiválasztásával.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  Válassza a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüben válassza ki.

5.  Válassza **a Minden alkalmazás** lehetőséget az összes alkalmazás listájának megtekintéséhez.

    Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást a **Minden alkalmazás**beállításra.

6.  Válassza ki azt az alkalmazást, amelyet egyszeri bejelentkezéshez szeretne konfigurálni.

7.  Miután az alkalmazás betöltött, nyissa meg az **SAML-alapkonfiguráció** szakaszt. A **Válasz URL-címében (helyességi feltétel fogyasztói szolgáltatás URL-címe)** törölje a rendszer által létrehozott nem használt vagy alapértelmezett Válasz URL-eket. Például: `https://127.0.0.1:444/applications/default.aspx`.



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Probléma az alkalmazásnak küldött SAML-jogcímek testreszabásakor

Az alkalmazásnak küldött SAML-attribútumjogcímek testreszabásáról további információt az Azure Active Directory ban található [Jogcímleképezés](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) című témakörben talál.

## <a name="next-steps"></a>További lépések
[Az Azure AD egyszeri bejelentkezési SAML protokollkövetelményei](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
