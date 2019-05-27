---
title: Egyszeri bejelentkezés – katalógusban nem szereplő alkalmazások – a Microsoft identity platform |} A Microsoft Docs
description: A Microsoft identity platform (Azure AD) a katalógusban nem szereplő alkalmazásokhoz való egyszeri bejelentkezés (SSO) konfigurálása
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: e34e6257b4800387470cdc1b7d624bf3ebd1d3e6
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989226"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-microsoft-identity-platform"></a>Egyszeri bejelentkezés nem katalógusból származó alkalmazásokhoz a Microsoft identity platform konfigurálása

Ez a cikk egy szolgáltatás, amely lehetővé teszi a rendszergazdák egyszeri bejelentkezés alkalmazásokhoz a Microsoft identity platform alkalmazásgyűjtemény hiányzó tárgya *kódírás nélkül*.

Ha inkább az egyéni alkalmazások integrálása az Azure AD-kódon keresztül: Fejlesztői útmutató keres, tekintse meg [hitelesítési forgatókönyvek az Azure ad-ben](../develop/authentication-scenarios.md).

A Microsoft identity platform alkalmazáskatalógusában felsorolja a alkalmazások ismert támogatja az egyszeri bejelentkezés a Microsoft identity platform révén az űrlap leírtak szerint [Ez a cikk](what-is-single-sign-on.md). Ha (a egy informatikai specialistája vagy támaszkodva a szervezetben) megtalálta az alkalmazást, amelyhez csatlakozni, az egyszeri bejelentkezés engedélyezése az Azure Portalon megjelenő részletes utasításokat követve kezdheti.

Az alábbi képességek is elérhetők, a licencszerződésben. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/active-directory/) olvasható.

- Például egy modern protokollt használó alkalmazás önkiszolgáló integrációja [OpenId Connect vagy OAuth](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) annak a felhasználók hitelesítéséhez és a jogkivonatok lekérésére [Microsoft Graph](https://graph.microsoft.com).
- Bármely alkalmazás, amely támogatja az önkiszolgáló integrációja [Security Assertion Markup Language (SAML) 2.0-s](https://wikipedia.org/wiki/SAML_2.0) Identitásszolgáltatók (SP által kezdeményezett vagy identitásszolgáltató által kezdeményezett)
- Bármely, amely rendelkezik egy a bejelentkezési oldal HTML-alapú webes alkalmazás önkiszolgáló integrációja [jelszóalapú egyszeri bejelentkezés](what-is-single-sign-on.md#password-based-sso)
- Önkiszolgáló kapcsolatot használó alkalmazások a [tartományok közötti Identity Management (SCIM) protokoll a felhasználók átadásának rendszer](use-scim-to-provision-users-and-groups.md)
- Lehetővé teszi, hogy minden olyan alkalmazáshoz, a hivatkozások a [Office 365 appindítóban](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) vagy a [Azure AD hozzáférési panel](what-is-single-sign-on.md#linked-sign-on)

Ezeket a képességeket tartalmazhatnak önkiszolgáló integrációja a szoftver (SaaS-) alkalmazás, amely használja, akkor is, ha senkinek nem előkészítve az alkalmazást az Azure AD alkalmazáskatalógusában még. Egy másik szolgáltatás érhető el, amelyek azt szabályozzák, a felhőben vagy a helyszíni kiszolgálók alkalmazva van a szervezet más gyártók webes alkalmazás önkiszolgáló integrációja.

Más néven *integrációs alkalmazássablonok*, ezek a képességek szabványokon alapuló kapcsolódási pontok adja meg az alkalmazásokat, amelyek támogatják a SAML, SCIM vagy űrlap alapú hitelesítéssel. A képességek közé tartoznak a rugalmas lehetőségek és beállítások széles körű számos alkalmazásban való kompatibilitás érdekében.

## <a name="adding-an-unlisted-application"></a>Egy listán nem szereplő alkalmazás hozzáadása

A Microsoft identity platform az alkalmazásregisztrációt két mechanizmust biztosít.

Például egy modern protokollt használó alkalmazás [OpenId Connect vagy OAuth](../develop/active-directory-v2-protocols.md) , a felhasználók hitelesítésére van regisztrálva használatával a [alkalmazásregisztrációs portálon](../develop/quickstart-register-app.md).

Az alkalmazásregisztrációt segítségével bármilyen típusú [támogatott hitelesítési mechanizmusok](what-is-single-sign-on.md), például a [SAML](../develop/single-sign-on-saml-protocol.md) protokollt, használja a **vállalati alkalmazások** panelen való csatlakozáshoz azokat a Microsoft identity platform használatával.

Egy listán nem szereplő alkalmazást egy alkalmazás-integráció sablon használatával csatlakozhat, hajtsa végre ezeket a lépéseket:

1. Jelentkezzen be a [Azure Active Directory portálon](https://aad.portal.azure.com/) a Microsoft identity platform rendszergazdai fiók használatával.
2. Válassza ki **vállalati alkalmazások** > **új alkalmazás**.
3. (Nem kötelező, de ajánlott) Az a **Hozzáadás a katalógusból** keresőmezőbe, írja be az alkalmazás megjelenítendő neve. Ha az alkalmazás megjelenik a keresési eredmények között, válassza ki, és hagyja ki ezt az eljárást az.
4. Válassza ki **katalógusban nem szereplő alkalmazás**. A **saját alkalmazás hozzáadása** lap jelenik meg.

   ![Alkalmazás hozzáadása](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. Adja meg az új alkalmazás megjelenített neve.
6. Válassza a **Hozzáadás** lehetőséget.

Az alkalmazás így hozzáadásával előre integrált alkalmazások, elérhető egy hasonló élményt biztosítani. Először válasszon **egyszeri bejelentkezési** való az alkalmazás oldalsávon. A következő oldalon (**egyszeri bejelentkezési módszer**) kapcsolódhatnak az egyszeri bejelentkezés konfigurálásához:

- **SAML**
- **Jelszóalapú**
- **A csatolt**

![Egyszeri bejelentkezési mód kiválasztása](./media/configure-single-sign-on-non-gallery-applications/select-a-single-sign-on-method.png)

Ezek a beállítások kapcsolatos további információkért tekintse meg a következő szakaszok a jelen cikk.

## <a name="saml-based-single-sign-on"></a>SAML-alapú egyszeri bejelentkezés

Válassza ki a **SAML** beállítást, konfigurálhat a SAML-alapú hitelesítést az alkalmazáshoz. (Ez utóbbi lehetőség megköveteli, hogy az alkalmazás támogatja-e a SAML 2.0-s.) A **állítsa be egyszeri bejelentkezést az SAML** lap jelenik meg.

![Egyszeri bejelentkezés az SAML beállítása](./media/configure-single-sign-on-non-gallery-applications/set-up-single-sign-on-with-saml.png)

Ezen a lapon öt különböző fejlécek rendelkezik:

| Címsor száma | Fejléc neve | Ezt a címsort összefoglalását lásd: |
| --- | --- | --- |
| 1. | **Alapszintű SAML-konfigurációja** | [Adja meg az alapszintű SAML-konfigurációja](#enter-basic-saml-configuration) |
| 2 | **Felhasználói attribútumok és jogcímek** | [Tekintse át, vagy a SAML-jogkivonatban kiadott jogcímek testreszabása](#review-or-customize-the-claims-issued-in-the-saml-token) |
| 3 | **SAML-aláíró tanúsítvány** | [Felülvizsgálat tanúsítvány lejárati dátum, állapot és e-mail-értesítés](#review-certificate-expiration-data-status-and-email-notification) |
| 4 | **Állítsa be a \<alkalmazás neve >** | [Cél-alkalmazás beállítása](#set-up-target-application) |
| 5 | **Egyszeri bejelentkezés az tesztelése \<alkalmazás neve >** | [Az SAML-alkalmazás tesztelése](#test-the-saml-application) |

Most már gyűjt adatokat a folytatás előtt az alkalmazás SAML funkcióinak használatát. Hajtsa végre az alábbi szakaszok az egyszeri bejelentkezés konfigurálása az alkalmazás és az Azure AD között.

### <a name="enter-basic-saml-configuration"></a>Adja meg az alapszintű SAML-konfigurációja

Az Azure AD beállításához, nyissa meg a **alapszintű SAML-konfigurációja** címsor, és válassza ki a **szerkesztése** ikon (a Ceruza). Manuálisan adja meg az értékeket, vagy a mező értékének kinyerése-metaadatfájl feltöltésére.

![Alapszintű SAML-konfigurációja](./media/configure-single-sign-on-non-gallery-applications/basic-saml-configuration.png)

Az alábbi két mező szükség:

- **Azonosító**. Ez az érték egyedileg kell azonosítania az alkalmazást, amelyhez egyszeri bejelentkezés konfigurálása történik. Ezt az értéket annak a **kibocsátó** eleme a **AuthnRequest** (SAML-kérelem) az alkalmazás által küldött. Ez az érték is frissült: a **Entitásazonosító** a az alkalmazás által biztosított bármely SAML-metaadatokat. Ellenőrizze az alkalmazás SAML dokumentációjában, a részletek a **Entitásazonosító** vagy **célközönség** érték.

  A következő kód bemutatja a **azonosító** vagy **kibocsátó** az SAML-kérelmet, amely az Azure AD elküldi az alkalmazás jelenik meg:

  ```xml
  <samlp:AuthnRequest
  xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
  ID="id6c1c178c166d486687be4aaf5e482730"
  Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
  xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
  </samlp:AuthnRequest>
  ```

- **Válasz URL-cím**. A válasz URL-címe, ahol az alkalmazás megjelenik az SAML-jogkivonat vár. Az URL-címet is nevezzük a helyességi feltétel fogyasztói szolgáltatás (ACS) URL-CÍMÉT. Ellenőrizze az alkalmazás SAML dokumentációjában, a SAML token válasz URL-cím vagy ACS URL-CÍMÉT.

  Több válasz URL-címek konfigurálásához használhatja a következő PowerShell-parancsfájlt.

  ```powershell
  $sp = Get-AzureADServicePrincipal -SearchString "<Exact app name>"
  $app = Get-AzureADApplication -SearchString "<Exact app name>"
  $urllist = New-Object "System.Collections.Generic.List[String]"
  $urllist.Add("<reply URL 1>")
  $urllist.Add("<reply URL 2>")
  $urllist.Add("<reply URL 3>")
  Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls $urllist
  Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls $urllist
  ```

A következő három mezőt nem kötelező:

- **Bejelentkezési URL-cím (SP által kezdeményezett csak)**. Ez azt jelzi, hogy a felhasználó itt, jelentkezzen be az alkalmazás. Ha az alkalmazás által végrehajtott SP által kezdeményezett egyszeri Bejelentkezést, majd amikor a felhasználók az URL-címet, az SP elvégzi szükséges átirányítást az Azure AD-hitelesítést, és jelentkezzen be a felhasználó. Ha megadja ezt a mezőt, az Azure AD az URL-cím használatával indítsa el az alkalmazást, az Office 365 és az Azure AD hozzáférési Panel. Ha kihagyja ezt a mezőt, az Azure AD inkább hajt végre identitásszolgáltató által kezdeményezett bejelentkezési – Office 365-höz, az Azure AD hozzáférési Panel vagy az Azure AD egyszeri bejelentkezési URL-címet az alkalmazás elindítása során (amely másolhatja a **irányítópult** lap).

- **Továbbítási állapot**. A továbbítási állapot SAML-arra utasítani az alkalmazás hol irányítsa át a felhasználókat a hitelesítés után is megadhat. Az értéke általában egy URL-címe vagy URL-cím elérési útja, amely egy adott helyen az alkalmazáson belül.

- **Kijelentkezési URL-címe**. Ez az érték szolgál az alkalmazásnak kijelentkezési SAML-válasz küldése.

További információkért lásd: [egyszeri bejelentkezéses SAML-protokoll](../develop/single-sign-on-saml-protocol.md).

### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Tekintse át, vagy a SAML-jogkivonatban kiadott jogcímek testreszabása

Amikor egy felhasználó hitelesíti magát az alkalmazást, az Azure AD kibocsát az alkalmazás SAML-jogkivonat az adatokat (vagy jogcímek), amely egyedileg azonosítja a felhasználóról. Alapértelmezés szerint ezt az információt tartalmazza, a felhasználó felhasználónevét, e-mail címét, Utónév és Vezetéknév.

Megtekintését és szerkesztését a jogcímek küldése az alkalmazásnak az SAML-jogkivonat:

- Nyissa meg a **felhasználói attribútumok & jogcímek** címsor, és válassza ki a **szerkesztése** ikonra. A **felhasználói attribútumok & jogcímek** lap jelenik meg.

![Felhasználói attribútumok és jogcímek](./media/configure-single-sign-on-non-gallery-applications/user-attributes-and-claims.png)

A két okból az SAML-jogkivonatban kiadott jogcímek szerkesztése lehet szükség:

- Az alkalmazáshoz szükséges különböző jogcím-URI-k vagy a jogcímértékek.
- Az alkalmazás által kért a **azonosító értékét nevet** jogcím-verziójáétól eltérő a felhasználónév (más néven az egyszerű felhasználónév), a Microsoft identity platform tárolja.

További információkért lásd: [hogyan: Vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása](../develop/active-directory-saml-claims-customization.md).

### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Felülvizsgálat tanúsítvány lejárati dátum, állapot és e-mail-értesítés

Egy katalógusban vagy egy katalógusban nem szereplő alkalmazást hoz létre, amikor az Azure AD-alkalmazás-specifikus tanúsítvány, amely a létrehozás dátumától számítva három év lejár hoz létre. Ez a tanúsítvány beállítása az Azure AD közötti megbízhatósági kapcsolat van szüksége, és az alkalmazás. A tanúsítvány formátuma részletes ismertetéséért tekintse meg az alkalmazás SAML dokumentációját.

Az Azure ad-ből, töltse le az aktív tanúsítvány Base64 vagy Raw formátumban közvetlenül a fő **állítsa be egyszeri bejelentkezést az SAML** lapot. Azt is megteheti beszerezheti az aktív tanúsítvány az alkalmazás metaadatainak XML-fájl letöltése vagy az alkalmazás összevonási metaadatainak URL-cím használatával.

Megtekintéséhez, létrehozásához, vagy töltse le a tanúsítványokat (aktív vagy inaktív), nyissa meg a **SAML-aláíró tanúsítvány** címsor, és válassza ki a **szerkesztése** ikonra. A **SAML-aláíró tanúsítvány** jelenik meg.

![SAML-aláíró tanúsítvány](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)

Ellenőrizze a tanúsítvány rendelkezik-e:

- *A kívánt lejárati dátuma.* Az elkövetkező konfigurálhatja úgy a lejárati dátum legfeljebb három évig.
- *A kívánt tanúsítvány aktív állapotú.* Ha az állapot **inaktív**, állítsa a állapotát **aktív**. Ha módosítani szeretné az állapot, kattintson a jobb gombbal a kívánt tanúsítvány sor, és válassza ki **tanúsítvány aktívvá**.
- *A helyes aláírási beállítás és algoritmus.*
- *A megfelelő értesítési e-mail-címeit.* Ha az aktív tanúsítvány a lejárati dátum közelében, az Azure AD értesítést küld az ebben a mezőben beállított e-mail-cím.  

További információkért lásd: [összevont egyszeri bejelentkezés tanúsítványai kezelése](manage-certificates-for-federated-single-sign-on.md) és [speciális tanúsítvány-aláírás a SAML-jogkivonat beállítások](certificate-signing-options.md).

### <a name="set-up-target-application"></a>Cél-alkalmazás beállítása

Adja meg az alkalmazás egyszeri bejelentkezéshez, keresse meg az alkalmazás dokumentációját. A dokumentációban találja, a **beállítása \<alkalmazásnév >** címsor, és válassza ki **részletes útmutató megtekintéséhez**. Megjelenik a dokumentáció a **bejelentkezés konfigurálása** lapot. Az oldal végigvezeti Önt a kitöltött a **bejelentkezési URL-cím**, **az Azure AD-azonosító**, és **kijelentkezési URL-címe** lévő értékeknek a **beállítása \<alkalmazás neve >** fejléc.

A szükséges értékeket az alkalmazás függően változnak. Részletekért tekintse meg az alkalmazás SAML dokumentációját. A **bejelentkezési URL-cím** és **kijelentkezési URL-címe** értékek mindkettő egyazon végpont, amely a SAML-kérelem-kezelési végpont esetében az Azure AD példányával feloldani. A **az Azure AD-azonosító** értéke a **kibocsátó** az alkalmazás számára kibocsátott SAML-jogkivonatban.

### <a name="assign-users-and-groups-to-your-saml-application"></a>Felhasználók és csoportok hozzárendelése az SAML-alkalmazáshoz

Miután konfigurálta az alkalmazás használhatja az Azure AD SAML-alapú identitás-szolgáltatóként, csaknem készen áll teszteléséhez. Biztonsági vezérlőként az Azure AD csak kiad egy jogkivonatot, lehetővé téve a felhasználót, hogy jelentkezzen be az alkalmazást, ha az Azure AD hozzáférést a felhasználó számára engedélyezett. Felhasználók közvetlenül vagy egy csoport tagsága is elérhet.

Az alkalmazás egy új felhasználó vagy csoport hozzárendelése:

1. Az alkalmazás oldalsávon válassza **felhasználók és csoportok**. A  **\<alkalmazás neve >-felhasználók és csoportok** lap, amely hozzárendelt felhasználók és csoportok aktuális listáját jeleníti meg.
2. Válassza ki **felhasználók hozzáadása**. A **hozzáadása hozzárendelések** lap jelenik meg.
3. Válassza ki **felhasználók és csoportok (\<szám > kijelölt)**. A **felhasználók és csoportok** elérhető felhasználók és csoportok listáját tartalmazó lap jelenik meg,.
4. Típus vagy görgessen lefelé, keresse meg a felhasználót vagy csoportot szeretne hozzárendelni a listából.
5. Válassza ki a felhasználó vagy csoport, amelyet szeretne hozzáadni, és válassza ki a **kiválasztása** gombra. A **felhasználók és csoportok** lap eltűnik.
6. Az a **hozzáadása hozzárendelések** lapon jelölje be **hozzárendelése**. A  **<application name> -felhasználók és csoportok** lap jelenik meg a listában szereplő további felhasználóival.

   ![Alkalmazás-felhasználók és csoportok](./media/configure-single-sign-on-non-gallery-applications/application-users-and-groups.png)

Ebből a listából a következőket teheti:

- Felhasználó eltávolítása.
- A szerepkör szerkesztéséhez.
- Frissítse a hitelesítő adataikat (felhasználónév és jelszó), úgy, hogy a felhasználó hitelesíthető az alkalmazás a felhasználó hozzáférési panelen végezzenek.

Előfordulhat, hogy szerkesztheti, vagy egyszerre több felhasználó vagy csoport eltávolítása.

Felhasználó hozzárendelése lehetővé teszi, hogy az Azure AD a felhasználói jogkivonatok kiállítása. Ez az alkalmazás a felhasználó hozzáférési panelen jelennek meg a csempe is okoz. Egy alkalmazás-csempét is megjelenik az Office 365 alkalmazásindítójában, ha a felhasználó Office 365-höz használ.

> [!NOTE]
> Feltöltheti az alkalmazást a csempe emblémájának a **embléma feltöltése** gombot a **konfigurálása** az alkalmazás lapon.

### <a name="test-the-saml-application"></a>Az SAML-alkalmazás tesztelése

Az SAML-alkalmazás tesztelése, előtt kell már az Azure AD-alkalmazás beállítása és az alkalmazás hozzárendelt felhasználók vagy csoportok. Az SAML-alkalmazás teszteléséhez válassza **egyszeri bejelentkezési**, ad vissza, hogy a **SAML-alapú bejelentkezés** lapot. (Ha más egyszeri bejelentkezési módszert volt érvényben, válassza ki a **egyszeri bejelentkezési mód módosítása** > **SAML** túl.) Ezt a a **egyszeri bejelentkezéssel való teszteléséhez \<alkalmazásnév >** szakaszban kattintson **teszt**. További információkért lásd: [Debug SAML-alapú egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="password-single-sign-on"></a>Jelszavas egyszeri bejelentkezés

Válassza ezt a beállítást, konfigurálhat [jelszóalapú egyszeri bejelentkezés](what-is-single-sign-on.md) egy bejelentkezési lap HTML-webalkalmazás számára. Jelszavas egyszeri Bejelentkezést, jelszó vaulting, engedélyezi a kezelheti a felhasználói hozzáférés és a jelszavakat a webes alkalmazások, amelyek nem támogatják az identitás-összevonást is hivatkoznak. Emellett akkor is hasznos, ahol több felhasználó kell megosztani egyetlen fiókban, mint például a szervezet közösségi alkalmazás fiókok forgatókönyvekhez.

Miután kiválasztotta **jelszóalapú**, adja meg az alkalmazás webes bejelentkezési oldal URL-CÍMÉT kéri.

![Jelszóalapú egyszeri bejelentkezés](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

Majd hajtsa végre az alábbi lépéseket:

1. Adja meg az URL-címet. Ez a karakterlánc a lapon, a felhasználónév beviteli mezőt kell lennie.
2. Kattintson a **Mentés** gombra. Az Azure AD megpróbálja elemezni a bejelentkezési lapon adjon meg egy felhasználónevet és jelszót adjon meg.
3. Ha az Azure ad-ben a elemzési kísérlet sikertelen lesz, válassza ki a **konfigurálása \<alkalmazásnév > jelszó egyszeri bejelentkezési beállításainak** megjelenítéséhez a **bejelentkezés konfigurálása** lap. (Ha a próbálkozás sikerre vezet, figyelmen kívül hagyhatja ezt az eljárást a többi.)
4. Válassza ki **bejelentkezési mezők manuális észlelése**. A bejelentkezési mezők manuális észlelése további utasítások jelennek meg.

   ![A jelszóalapú egyszeri bejelentkezés manuális konfigurálása](./media/configure-single-sign-on-non-gallery-applications/password-configure-sign-on.png)
5. Válassza ki **bejelentkezési mezők rögzítése**. Egy rögzítési állapot oldal: az üzenet egy új lapon nyílik meg **metaadatok rögzítése folyamatban van**.
6. Ha a **hozzáférési Panel bővítmény szükséges** mező megjelenik egy új lapon válassza ki **telepítés most** telepítéséhez a **saját alkalmazások biztonságos bejelentkezési bővítménye** webböngésző-bővítmény. (A webböngésző-bővítmény használatához a Microsoft Edge, Chrome és a Firefox.) Majd telepítse, indítsa el, és engedélyezi a bővítményt, és frissíti a rögzítési állapot lapot.

   A webböngésző-bővítmény majd megnyílik egy másik lapra, amely megjeleníti a megadott URL-címe.
7. A megadott URL-CÍMÉT tartalmazó lapra keresse meg a bejelentkezési folyamatot. Töltse ki a felhasználónév és jelszó megadására, és próbáljon meg bejelentkezni. (Nem kell a helyes jelszót adja meg.)

   Kérdés megkérdezi, hogy mentse a rögzített bejelentkezési mezők.
8. Kattintson az **OK** gombra. Bezárja a lapot, a webböngésző-bővítmény a rögzítési állapotlapon frissíti az üzenet **metaadatok frissítve lett az alkalmazás**, és a böngésző lapon is bezárul.
9. Az Azure AD-ben **bejelentkezés konfigurálása** lapon jelölje be **rendben, sikerült jelentkezzen be az alkalmazás sikeresen**.
10. Kattintson az **OK** gombra.

Után a bejelentkezési oldalon a rögzítési, felhasználók és csoportok lehet hozzárendelni, és beállíthatja a hitelesítőadat-szabályzatok, csakúgy, mint a normál [jelszó egyszeri bejelentkezési alkalmazások](what-is-single-sign-on.md).

> [!NOTE]
> Feltöltheti az alkalmazást a csempe emblémájának a **embléma feltöltése** gombot a **konfigurálása** az alkalmazás lapon.

## <a name="existing-single-sign-on"></a>Meglévő egyszeri bejelentkezés

Válassza ki ezt a beállítást, hivatkozás hozzáadása az alkalmazáshoz a szervezet Azure AD hozzáférési Panel vagy az Office 365 portálon. Ez a módszer segítségével, amelyek jelenleg használják az Active Directory összevonási szolgáltatások (vagy más összevonási szolgáltatásból) egyéni webes alkalmazásokra mutató hivatkozások hozzáadása az Azure AD-hitelesítés helyett. Vagy mélyhivatkozással hozzáadása adott SharePoint-oldalakba vagy más weblapokat csak át szeretné megjeleníteni a felhasználók hozzáférési Paneljein.

Miután kiválasztotta **társított**, kéri, hogy az alkalmazás összekapcsolása az URL-címét. Írja be az URL-címet, és válassza ki **mentése**. Felhasználók és csoportok az alkalmazásról, így jelenik meg az alkalmazás engedményezheti a [Office 365 appindítóban](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) vagy a [Azure AD hozzáférési panel](end-user-experiences.md) azoknak a felhasználóknak.

> [!NOTE]
> Feltöltheti az alkalmazást a csempe emblémájának a **embléma feltöltése** gombot a **konfigurálása** az alkalmazás lapon.

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Útmutató: Vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása](../develop/active-directory-saml-claims-customization.md)
- [SAML-alapú egyszeri bejelentkezés az Azure Active Directory-alkalmazások hibakeresése](../develop/howto-v1-debug-saml-sso-issues.md)
- [A Microsoft identity platform (korábbi nevén Azure Active Directory fejlesztők számára)](../develop/index.yml)
