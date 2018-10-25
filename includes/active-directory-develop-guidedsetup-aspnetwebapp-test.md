---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: d3f2efc0ae3dcf9bdcae3f82258b28b761944487
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988508"
---
## <a name="test-your-code"></a>Tesztelheti a kódját

Az alkalmazás tesztelése a Visual Studióban, nyomja le a **F5** , futtassa a projektet. A böngésző megnyitja a http://<span></span>localhost: {port} helyen, és tekintse meg a **Bejelentkezés Microsoft-fiókkal** gombra. Válassza ki a gombra kattintva indítsa el a bejelentkezési folyamatot.

Amikor elkészült, futtassa a tesztet, használja a Microsoft Azure Active Directory (Azure AD-) fiók (munkahelyi vagy iskolai fiók) vagy személyes Microsoft-fiókkal (<span>élő.</span> COM vagy <span>outlook.</span> COM) való bejelentkezéshez.

![Bejelentkezés Microsoft-fiókkal](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Jelentkezzen be Microsoft-fiókjával](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Kérelem eredményének megtekintése

Miután bejelentkezett, a rendszer átirányítja a felhasználót, a webhely kezdőlapjára. A kezdőlapon az alkalmazás regisztrációs információit a Microsoft alkalmazásregisztrációs portálon megadott HTTPS URL-címet. A kezdőlap tartalmaz egy üdvözlő üzenet *"Hello \<felhasználó >,"* jelentkezzen ki, és a egy hivatkozásra kattintva a felhasználói jogcímeket. A felhasználói jogcímek mutató hivatkozás címre lép a *jogcímek* tartományvezérlővel, amely a korábban létrehozott.

### <a name="browse-to-see-the-users-claims"></a>Megkeresheti a felhasználói jogcímek

A felhasználói jogcímeket, jelölje ki a hivatkozásra kattintva keresse meg a vezérlő nézet csak a hitelesített felhasználók számára elérhető.

#### <a name="view-the-claims-results"></a>A jogcímek eredmények megtekintése

A vezérlő nézetben keresse meg, miután egy táblát, amely tartalmazza a felhasználók alapszintű tulajdonságainak kell megjelennie:

|Tulajdonság |Érték |Leírás |
|---|---|---|
|**Name (Név)** |A felhasználó teljes neve | A felhasználó vezetékneve és nevét.
|**Felhasználónév** |Felhasználó<span>@domain.com</span> | A felhasználónév, amely a felhasználó azonosítására szolgál.
|**Tárgy** |Tárgy |Egy karakterlánc, amely egyedileg azonosítja a felhasználó a weben.|
|**Bérlő azonosítója** |GUID | A **guid** , amely a felhasználó Azure ad-ben szervezete egyedi jelöli.|

Emellett megtekintheti az egy táblázat minden jogcím található a hitelesítési kérelmet. További információkért lásd: a [listája, amelyeket egy Azure AD-azonosító jogkivonat](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>(Nem kötelező) engedélyezés attribútummal rendelkezik metódus való hozzáférés tesztelése

Hozzáférés teszteléséhez a vezérlőhöz névtelen felhasználó védettként a `Authorize` attribútumot, kövesse az alábbi lépéseket:

1. Kattintson a Kijelentkezés a felhasználói és a kijelentkezési folyamat befejezéséhez.
2. A böngészőben, írja be a http://<span></span>localhost: {port} / eléréséhez a tartományvezérlő által védett jogcímek a `Authorize` attribútum.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>A hozzáférést a védett tartományvezérlő után várt eredmény

Kéri, hogy a védett tartományvezérlő-nézet használata a hitelesítéshez.

## <a name="advanced-options"></a>Speciális beállítások

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Az egész webhely védelme
Védelméhez, az egész webhely a **Global.asax** fájlt, adja hozzá a `AuthorizeAttribute` attribútumot a `GlobalFilters` szűrőt a `Application_Start` módszer:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Korlátozhatja, hogy ki jelentkezhet be az alkalmazás

Alapértelmezés szerint ez az útmutató által létrehozott alkalmazás készítése során az alkalmazás fogadja bejelentkezések a személyes fiókok (beleértve az Outlook.com-os, live.com, és mások) valamint a munkahelyi és iskolai fiókok bármely vállalat vagy szervezet, amely integrálva van az Az Azure Active Directory. Ez a beállítás ajánlott a SaaS-alkalmazásokhoz.

Az alkalmazás felhasználói bejelentkezési hozzáférés korlátozása, több lehetőség is elérhető:

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>1. lehetőség: A felhasználók korlátozása a szervezet csak egy Active Directory-példányból, jelentkezzen be az alkalmazás (egybérlős)

Ez a beállítás akkor gyakran előfordul *LOB-alkalmazások*: szeretné-e az alkalmazás csak egy adott Azure Active Directory-példányhoz tartozó számlák történő bejelentkezések fogadására (beleértve a *vendégfiókok*az adott példány) tegye a következőket:

1. Az a **web.config** fájl, módosítsa az értéket a a `Tenant` paramétert `Common` a szervezet bérlőjének a nevével, például `contoso.onmicrosoft.com`.
2. Az a [OWIN indítási osztályt](#configure-the-authentication-pipeline)állítsa be a `ValidateIssuer` argumentumának `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>2. lehetőség: Az alkalmazás a szervezetek a felhasználók számára egy adott listában való hozzáférés korlátozása

Jelentkezzen be a hozzáférés csak felhasználói fiókok, egy Azure AD-szervezethez, amely szerepel a listán az engedélyezett szervezetek korlátozhatja:
1. Az a [OWIN indítási osztályt](#configure-the-authentication-pipeline)állítsa be a `ValidateIssuer` argumentumának `true`.
2. Az értékét állítsa be a `ValidIssuers` paraméter engedélyezett szervezetek listájához.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>3. lehetőség: Egyéni módszer használatával kiállítók ellenőrzése

Egy egyéni módszert, kiállítók használatával valósítható meg a **IssuerValidator** paraméter. Ez a paraméter használatával kapcsolatos további információkért olvassa el a [TokenValidationParameters osztály](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx) az MSDN Webhelyén.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]