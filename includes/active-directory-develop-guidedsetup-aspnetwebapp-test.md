---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 7e9518f8a90faa0566b96d58992b01e4b0a642f4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
## <a name="test-your-code"></a>Tesztelheti a kódját

A Visual Studióban az alkalmazás teszteléséhez nyomja le az **F5** a projekt futtatásához. A böngésző megnyitja a http://<span></span>localhost: {port} helyre, és tekintse meg a **jelentkezzen be Microsoft** gombra. Kattintson a gombra, majd indítsa el a bejelentkezési folyamat.

Ha készen a teszt futtatása, használja a Microsoft Azure Active Directory (Azure AD-) fiók (munkahelyi vagy iskolai fiókkal) vagy személyes Microsoft-fiókkal (<span>élő.</span> COM vagy <span>outlook.</span> COM) jelentkezzen be.

![Jelentkezzen be Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Jelentkezzen be Microsoft-fiókja](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Alkalmazás-eredmények megtekintése
Miután bejelentkezik, a felhasználót a rendszer átirányítja a kezdőlapot, a webhely. A kezdőlap a Microsoft alkalmazás-regisztrálási portál alkalmazás regisztrációs adatait a megadott HTTPS URL-CÍMÉT. A kezdőlap magában foglalja az üdvözlő üzenet *"Hello \<felhasználó >,"* kijelentkezés mutató hivatkozást, és megtekintheti a felhasználói jogcímek mutató hivatkozást. A hivatkozás a felhasználói jogcímek nyit a *jogcímek* korábban létrehozott vezérlő.

### <a name="browse-to-see-the-users-claims"></a>Keresse ki a felhasználói jogcímek
A felhasználói jogcímeket, jelölje ki a hivatkozásra kattintva tallózással keresse meg a vezérlő nézet csak a hitelesített felhasználók számára elérhető.

#### <a name="view-the-claims-results"></a>A jogcímek eredmények megtekintése
Után keresse meg a vezérlő nézetet, a felhasználók alapszintű tulajdonságainak tartalmazó kell megjelennie:

|Tulajdonság |Érték |Leírás |
|---|---|---|
|**Name (Név)** |Felhasználó teljes neve | A felhasználó nagyapja vezeték- és keresztneve.
|**Felhasználónév** |Felhasználó<span>@domain.com</span> | A felhasználónév, amely a felhasználó azonosítására szolgál.
|**Tárgy** |Tárgy |Egy karakterlánc, amely egyedileg azonosítja a felhasználó a weben.|
|**Bérlő azonosítója** |GUID | A **guid** , amely egyedi módon a felhasználó az Azure AD szervezete jelöli.|

Ezenkívül megjelenik egy tábla, amelyek a hitelesítési kérelem minden jogcím. További információkért lásd: a [listája, amelyeket egy Azure AD Azonosítót jogkivonatban](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>(Választható) engedélyezés attribútummal rendelkezik: metódusra-hozzáférés tesztelése
Való hozzáférés tesztelése a vezérlőhöz névtelen felhasználó védettként a `Authorize` attribútumot, kövesse az alábbi lépéseket:
1. Jelölje ki a felhasználó kijelentkezik, majd fejezze be a kijelentkezési folyamat hivatkozást.
2. A böngészőben, írja be a http://<span></span>localhost: {port} / jogcímeket a tartományvezérlő által védett eléréséhez a `Authorize` attribútum.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Kívánt eredmény elérése érdekében a hozzáférést a védett tartományvezérlő után
Használja a védett tartományvezérlő felé történő hitelesítésre kéri.

## <a name="advanced-options"></a>Speciális beállítások

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>A teljes webhelyet védelme
A teljes webhely védeni a **Global.asax** fájlt, adja hozzá a `AuthorizeAttribute` attribútumot a `GlobalFilters` szűrés a `Application_Start` metódus:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Korlátozza, aki jelentkezhetnek be az alkalmazás
Alapértelmezés szerint ez az útmutató által létrehozott alkalmazás építésekor az alkalmazás elfogadja bejelentkezési modulok a személyes fiókok (például outlook.com, live.com és mások), valamint a munkahelyi és iskolai fiókok bármely vállalat vagy szervezet, amely integrálva van Az Azure Active Directory. Ez a beállítás ajánlott az SaaS-alkalmazásokhoz.

Az alkalmazás felhasználói bejelentkezési hozzáférés korlátozásához, több lehetőség is elérhető:

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>1. lehetőség: A felhasználók korlátozása csak egy szervezet Active Directory példányból bejelentkezni az alkalmazás (single-bérlő)

Ez a beállítás akkor egy általános forgatókönyv a *LOB-alkalmazások*: Ha szeretné-e az alkalmazás csak az Azure Active Directory adott példányához tartozó fiókok bejelentkezések fogadására (beleértve a *vendégfiókok*, hogy a példány) tegye a következőket:

1. Az a **web.config** fájl, módosítsa a `Tenant` paramétert `Common` számára a szervezet bérlőjének a nevével, mint `contoso.onmicrosoft.com`.
2. Az a [OWIN indítási osztály](#configure-the-authentication-pipeline), beállíthatja a `ValidateIssuer` argumentumának `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>2. lehetőség: Az alkalmazás a felhasználók számára az adott szervezetek való hozzáférés korlátozása

Bejelentkezési hozzáférés csak felhasználói fiókot, amely szerepel a listában az engedélyezett szervezetek az Azure AD szervezetében korlátozhatja:
1. Az a [OWIN indítási osztály](#configure-the-authentication-pipeline), beállíthatja a `ValidateIssuer` argumentumának `true`.
2. Állítsa a `ValidIssuers` paraméter engedélyezett szervezetek listájához.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>3. lehetőség: Egyéni módszer segítségével ellenőrizheti azokat kibocsátók
Megvalósíthat egy egyéni módszer használatával kiállítók érvényesítéséhez a **IssuerValidator** paraméter. Ez a paraméter használatával kapcsolatos további információkért olvassa el a [TokenValidationParameters osztály](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx) az MSDN Webhelyén.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
