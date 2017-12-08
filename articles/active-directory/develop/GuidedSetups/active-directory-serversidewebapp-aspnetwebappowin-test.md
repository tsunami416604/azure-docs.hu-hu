---
title: "Az Azure AD v2 ASP.NET Web Server első lépések – tesztelése |} Microsoft Docs"
description: "A Microsoft bejelentkezés végrehajtási egy ASP.NET-megoldás a hagyományos böngészőalapú webalkalmazás a szabványos OpenID Connect"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: bfa2563a6a58370d9a611440017441a751b46244
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
## <a name="test-your-code"></a>Tesztelheti a kódját

Nyomja le az `F5` a projektet a Visual Studio futtatásához. A böngészőben megnyitja és, hogy közvetlen *http://localhost: {port}* ahol láthatja a *jelentkezzen be Microsoft* gombra. Lépjen tovább, és kattintson rá a bejelentkezéshez.

Amikor készen áll a tesztelése, használatával munkahelyi vagy iskolai (az Azure Active Directory) vagy egy személyes (live.com, outlook.com) fiók jelentkezzen be. 

![Jelentkezzen be Microsoft-böngészőablakot](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin.png)

![Jelentkezzen be Microsoft-böngészőablakot](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Kívánt eredmény elérése érdekében
Bejelentkezés, miután a felhasználó a kezdőlapot, a webhely, amely a HTTPS URL-címét, a Microsoft alkalmazás-regisztrálási portál alkalmazás regisztrációs adatait a megadott átirányítási. Ezen a lapon látható most *Hello {felhasználó}* és kijelentkezési mutató hivatkozást, és egy hivatkozásra kattintva megtekintheti az a felhasználói jogcímek – ami az engedélyezés vezérlő mutató hivatkozást a korábban létrehozott-e.

### <a name="see-users-claims"></a>Tekintse meg a felhasználói jogcímek
Válassza ki a hivatkozásra kattintva tekintse meg a felhasználói jogcímeket. A részletes útmutatást a tartományvezérlő, tekintse meg, hogy csak hitelesített felhasználók számára elérhető.

#### <a name="expected-results"></a>Kívánt eredmény elérése érdekében
 A bejelentkezett felhasználó alaptulajdonságait tartalmazó táblát kell megjelennie:

| Tulajdonság | Érték | Leírás|
|---|---|---|
| Név | {Felhasználó teljes neve} | A felhasználó nagyapja vezeték- és keresztneve
|Felhasználónév | <span>user@domain.com</span>| A felhasználó azonosítására használt felhasználónév
| Tárgy| {Tulajdonos}|A karakterlánc egyedi azonosítására a weben a felhasználói bejelentkezés|
| Bérlőazonosító| {Guid}| A *guid* a felhasználó Azure Active Directory szervezeti egyedileg ábrázolásához.|

Ezenkívül megjelenik egy táblázatot, beleértve az összes felhasználó szerepel a hitelesítési kérelmet. Egy azonosító Token és magyarázat minden jogcím listáját lásd: a [cikk](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "lista a jogcímek a lexikális elem azonosítója").


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>Egy metódust, amelynek elérésekor teszt egy *[engedélyezés]* attribútum (nem kötelező)
Ebben a lépésben tesztelni a hitelesített vezérlő névtelen felhasználóként elérésekor:<br/>
Válassza ki a Kijelentkezés hivatkozásra a felhasználó, és kijelentkezési folyamat befejezéséhez.<br/>
A böngészőben, a parancssorba írja be a http://localhost: {port} / hitelesített a tartományvezérlő által védett eléréséhez a `[Authorize]` attribútum

#### <a name="expected-results"></a>Kívánt eredmény elérése érdekében
A parancssorba, nincs szükség, hogy a nézet hitelesítést kell kapnia.

## <a name="additional-information"></a>További információ

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>A teljes webhelyet védelme
A teljes webhelyet védelme érdekében vegye fel a `AuthorizeAttribute` való `GlobalFilters` a `Global.asax` `Application_Start` módszert:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

> [!NOTE]
> **Jelentkezzen be az alkalmazás csak egy szervezet a felhasználók korlátozása**

> Alapértelmezés szerint személyes fiókok (például outlook.com, live.com és mások), valamint a munkahelyi és iskolai fiókok bármely vállalat vagy szervezet, amely az Azure Active Directoryval integrált is bejelentkezhet az alkalmazásba. 

> Ha azt szeretné, hogy az alkalmazás csak egy Azure Active Directory szervezeti bejelentkezések fogadására, cserélje le a `Tenant` paraméterének *web.config* a `Common` a bérlő neve a szervezet – példa, *contoso.onmicrosoft.com*. Ezt követően módosíthatja a `ValidateIssuer` argumentumának a *OWIN indítási osztály* való `true`.

> Engedélyezi a felhasználók csak bizonyos szervezetekkel listája, állítsa `ValidateIssuer` true, és használja a `ValidIssuers` paraméter adja meg a szervezetek listáját.

> Egy másik lehetőség, hogy a kiállítók IssuerValidator paraméter használatával érvényesítéséhez egyéni módszer alkalmazza. További információ `TokenValidationParameters`, lásd: [ez](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "TokenValidationParameters MSDN-cikk") MSDN-cikk tárgyalja.

[!INCLUDE  [Help and Support Options](../../../../includes/active-directory-develop-help-support-include.md)]