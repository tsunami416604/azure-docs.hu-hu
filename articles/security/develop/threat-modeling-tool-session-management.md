---
title: Munkamenet-kezelés - Microsoft Threat Modeling Tool - Azure | Microsoft dokumentumok
description: a fenyegetésmodellezési eszközben elérhető fenyegetések enyhítése
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 5d9dc1595e3cc812ba060d958b6e981867500ae2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73161508"
---
# <a name="security-frame-session-management"></a>Biztonsági keret: Munkamenet-kezelés
| Termék/szolgáltatás | Cikk |
| --------------- | ------- |
| **Azure Hirdetés**    | <ul><li>[Valósítsa meg a megfelelő kijelentkezést ADAL-metódusokkal az Azure AD használatakor](#logout-adal)</li></ul> |
| IoT-eszköz | <ul><li>[Véges élettartamok használata a generált SaS-tokenek esetén](#finite-tokens)</li></ul> |
| **Azure Document DB** | <ul><li>[Minimális jogkivonat-élettartam használata a létrehozott erőforrás-jogkivonatokhoz](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Megfelelő kijelentkezés megvalósítása WsFederation metódusokkal Az ADFS használatakor](#wsfederation-logout)</li></ul> |
| **Identitás-kiszolgáló** | <ul><li>[Megfelelő kijelentkezés megvalósítása az Identitáskiszolgáló használatakor](#proper-logout)</li></ul> |
| **Webalkalmazás** | <ul><li>[A HTTPS-en keresztül elérhető alkalmazásoknak biztonságos cookie-kat kell használniuk](#https-secure-cookies)</li><li>[Minden http-alapú alkalmazásnak csak a cookie-definícióhoz kell megadnia a http-t](#cookie-definition)</li><li>[A webhelyek közötti kérelemhamisítás (CSRF) ASP.NET weboldalakon elkövetett támadásainak enyhítése](#csrf-asp)</li><li>[Munkamenet beállítása az inaktivitás élettartamára](#inactivity-lifetime)</li><li>[Megfelelő kijelentkezés megvalósítása az alkalmazásból](#proper-app-logout)</li></ul> |
| **Webes API** | <ul><li>[A webhelyek közötti kérelemhamisítás (CSRF) ASP.NET webes API-k elleni támadásainak enyhítése](#csrf-api)</li></ul> |

## <a name="implement-proper-logout-using-adal-methods-when-using-azure-ad"></a><a id="logout-adal"></a>Valósítsa meg a megfelelő kijelentkezést ADAL-metódusokkal az Azure AD használatakor

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Ha az alkalmazás az Azure AD által kiadott hozzáférési jogkivonatra támaszkodik, a kijelentkezési eseménykezelőnek meg kell hívnia |

### <a name="example"></a>Példa
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Példa
Azt is meg kell semmisítenie a felhasználói munkamenet meghívásával Session.Abandon() metódus. A következő módszer a felhasználói kijelentkezés biztonságos megvalósítását mutatja be:
```csharp
    [HttpPost]
        [ValidateAntiForgeryToken]
        public void LogOff()
        {
            string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            AuthenticationContext authContext = new AuthenticationContext(Authority + TenantId, new NaiveSessionCache(userObjectID));
            authContext.TokenCache.Clear();
            Session.Clear();
            Session.Abandon();
            Response.SetCookie(new HttpCookie("ASP.NET_SessionId", string.Empty));
            HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
        } 
```

## <a name="use-finite-lifetimes-for-generated-sas-tokens"></a><a id="finite-tokens"></a>Véges élettartamok használata a generált SaS-tokenek esetén

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Az Azure IoT Hubhitelesítéshez létrehozott SaS-jogkivonatoknak véges lejárati idővel kell rendelkezniük. Tartsa a SaS-jogkivonat élettartamát a lehető legkisebbre, hogy korlátozza a tokenek feltörése esetén visszajátszható időt.|

## <a name="use-minimum-token-lifetimes-for-generated-resource-tokens"></a><a id="resource-tokens"></a>Minimális jogkivonat-élettartam használata a létrehozott erőforrás-jogkivonatokhoz

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Document DB | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Csökkentse az erőforrás-jogkivonat időtartományát egy minimálisan szükséges értékre. Az erőforrás-jogkivonatok alapértelmezett érvényességi ideje 1 óra.|

## <a name="implement-proper-logout-using-wsfederation-methods-when-using-adfs"></a><a id="wsfederation-logout"></a>Megfelelő kijelentkezés megvalósítása WsFederation metódusokkal Az ADFS használatakor

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | ADFS | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Ha az alkalmazás az ADFS által kibocsátott STS-jogkivonatra támaszkodik, a kijelentkezési eseménykezelőnek meg kell hívnia a WSFederationAuthenticationModule.FederatedSignOut() metódust a felhasználó kijelentkeztetéséhez. Szintén az aktuális munkamenetet meg kell semmisíteni, és a munkamenet token értékét alaphelyzetbe kell állítani és semmisíteni kell.|

### <a name="example"></a>Példa
```csharp
        [HttpPost, ValidateAntiForgeryToken]
        [Authorization]
        public ActionResult SignOut(string redirectUrl)
        {
            if (!this.User.Identity.IsAuthenticated)
            {
                return this.View("LogOff", null);
            }

            // Removes the user profile.
            this.Session.Clear();
            this.Session.Abandon();
            HttpContext.Current.Response.Cookies.Add(new System.Web.HttpCookie("ASP.NET_SessionId", string.Empty)
                {
                    Expires = DateTime.Now.AddDays(-1D),
                    Secure = true,
                    HttpOnly = true
                });

            // Signs out at the specified security token service (STS) by using the WS-Federation protocol.
            Uri signOutUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Issuer);
            Uri replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm);
            if (!string.IsNullOrEmpty(redirectUrl))
            {
                replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm + redirectUrl);
            }
           //     Signs out of the current session and raises the appropriate events.
            var authModule = FederatedAuthentication.WSFederationAuthenticationModule;
            authModule.SignOut(false);
        //     Signs out at the specified security token service (STS) by using the WS-Federation
        //     protocol.            
            WSFederationAuthenticationModule.FederatedSignOut(signOutUrl, replyUrl);
            return new RedirectResult(redirectUrl);
        }
```

## <a name="implement-proper-logout-when-using-identity-server"></a><a id="proper-logout"></a>Megfelelő kijelentkezés megvalósítása az Identitáskiszolgáló használatakor

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identitás-kiszolgáló | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [IdentityServer3-Federated kijelentkezés](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Lépéseket** | IdentityServer támogatja a külső identitásszolgáltatókkal való összeegyeztetést. Amikor egy felhasználó kijelentkezik egy felsőbb szintbeli identitásszolgáltatóból, a használt protokolltól függően előfordulhat, hogy értesítést kaphat, amikor a felhasználó kijelentkezik. Lehetővé teszi, hogy az IdentityServer értesítse ügyfeleit, így azok is kijelentkezhetnek a felhasználóból. A megvalósítás részleteit a hivatkozások részben található dokumentációban találja.|

## <a name="applications-available-over-https-must-use-secure-cookies"></a><a id="https-secure-cookies"></a>A HTTPS-en keresztül elérhető alkalmazásoknak biztonságos cookie-kat kell használniuk

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | EnvironmentType - OnPrem |
| **Referencia**              | [httpCookies elem (ASP.NET Settings Schema)](https://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [HttpCookie.Secure tulajdonság](https://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Lépéseket** | A cookie-k általában csak azon domain számára érhetők el, amelyre hatókörük volt. Sajnos a "domain" definíciója nem tartalmazza a protokollt, így a HTTPS-en keresztül létrehozott cookie-k HTTP-n keresztül érhetők el. A "secure" attribútum azt jelzi a böngészőnek, hogy a cookie-t csak HTTPS-en keresztül szabad elérhetővé tenni. Győződjön meg arról, hogy a HTTPS protokollon keresztül beállított összes cookie a **biztonságos** attribútumot használja. A követelmény a web.config fájlban érvényesíthető a requireSSL attribútum true értékre állításával. Ez az előnyben részesített megközelítés, mert az összes jelenlegi és jövőbeli **cookie-k biztonságos** attribútumát kényszeríti, anélkül, hogy további kódmódosításokat kellene végrehajtania.|

### <a name="example"></a>Példa
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
A beállítás akkor is érvénybelép, ha http-t használ az alkalmazás eléréséhez. Ha HTTP-t használ az alkalmazás eléréséhez, a beállítás megszakítja az alkalmazást, mert a cookie-k a biztonságos attribútummal vannak beállítva, és a böngésző nem küldi vissza őket az alkalmazásnak.

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Webes űrlapok, MVC5 |
| **Attribútumok**              | EnvironmentType - OnPrem |
| **Referencia**              | N/A  |
| **Lépéseket** | Ha a webalkalmazás a függő entitás, és az IdP ADFS-kiszolgáló, a FedAuth token biztonságos attribútuma konfigurálható a requireSSL true beállításával a web.config szakaszában: `system.identityModel.services`|

### <a name="example"></a>Példa
```csharp
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a name="all-http-based-application-should-specify-http-only-for-cookie-definition"></a><a id="cookie-definition"></a>Minden http-alapú alkalmazásnak csak a cookie-definícióhoz kell megadnia a http-t

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Biztonságos cookie attribútum](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Lépéseket** | A webhelyek közötti parancsfájlok (XSS) támadással történő információközlés kockázatának csökkentése érdekében egy új attribútumot vezettek be - httpOnly - a cookie-kban, és az összes főbb böngésző támogatja. Az attribútum azt határozza meg, hogy a cookie-k nem érhetők el parancsfájlon keresztül. A HttpOnly cookie-k használatával a webes alkalmazások csökkentik annak lehetőségét, hogy a cookie-ban található bizalmas információkat parancsfájlon keresztül ellopják és elküldhetik a támadó webhelyére. |

### <a name="example"></a>Példa
A cookie-kat használó összes HTTP-alapú alkalmazásnak meg kell adnia a HttpOnly értéket a cookie-definícióban a következő konfiguráció megvalósításával a web.config fájlban:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Webes űrlapok |
| **Attribútumok**              | N/A  |
| **Referencia**              | [FormsAuthentication.requireSSL tulajdonság](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Lépéseket** | A RequireSSL tulajdonság értéke egy ASP.NET alkalmazás konfigurációs fájljában van beállítva a konfigurációs elem requireSSL attribútumával. A ASP.NET alkalmazás Web.config fájljában megadhatja, hogy az ssl (Secure Sockets Layer) szükséges-e az űrlap-hitelesítési cookie kiszolgálónak való visszaküldéséhez a requireSSL attribútum beállításával.|

### <a name="example"></a>Példa 
A következő kódpélda beállítja a requireSSL attribútumot a Web.config fájlban.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | MVC5 |
| **Attribútumok**              | EnvironmentType - OnPrem |
| **Referencia**              | [Windows Identity Foundation (WIF) konfiguráció – II.](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Lépéseket** | A FedAuth cookie-k httpOnly attribútumának beállításához a hideFromCsript attribútumértékét Igaz értékre kell állítani. |

### <a name="example"></a>Példa
A következő konfiguráció a helyes konfigurációt mutatja:
```XML
<federatedAuthentication>
<cookieHandler mode="Custom"
                       hideFromScript="true"
                       name="FedAuth"
                       path="/"
                       requireSsl="true"
                       persistentSessionLifetime="25">
</cookieHandler>
</federatedAuthentication>
```

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-pages"></a><a id="csrf-asp"></a>A webhelyek közötti kérelemhamisítás (CSRF) ASP.NET weboldalakon elkövetett támadásainak enyhítése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | A helyek közötti kérelemhamisítás (CSRF vagy XSRF) egy olyan típusú támadás, amelyben a támadó műveleteket hajthat végre egy másik felhasználó által létrehozott munkamenet biztonsági környezetében egy webhelyen. A cél a tartalom módosítása vagy törlése, ha a megcélzott webhely kizárólag munkamenet-cookie-kra támaszkodik a fogadott kérelem hitelesítéséhez. A támadó úgy használhatja ki a biztonsági rést, hogy egy másik felhasználó böngészőjébe veszi, hogy betöltsön egy URL-t egy olyan sebezhető webhelyről, amelyen a felhasználó már be van jelentkezve. Ezt a támadó nak számos módja van, például egy másik webhely üzemeltetésével, amely betölt egy erőforrást a sebezhető kiszolgálóról, vagy rálehet kérni a felhasználót, hogy kattintson egy hivatkozásra. A támadás megelőzhető, ha a kiszolgáló további jogkivonatot küld az ügyfélnek, megköveteli, hogy az ügyfél minden jövőbeli kérelemben felszámítsa a tokent, és ellenőrzi, hogy minden jövőbeli kérelem tartalmaz-e egy, az aktuális munkamenetre vonatkozó jogkivonatot, például a ASP.NET AntiForgeryToken vagy ViewState. |

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [XSRF/CSRF megelőzés ASP.NET MVC-ben és weboldalakon](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Lépéseket** | Anti-CSRF és ASP.NET MVC-űrlapok – Használja a `AntiForgeryToken` nézetek segítő módszerét; hogy `Html.AntiForgeryToken()` egy a forma, például,|

### <a name="example"></a>Példa
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>Példa
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Példa
Ugyanakkor a Html.AntiForgeryToken() egy __RequestVerificationToken nevű cookie-t ad a látogatónak, amelynek értéke megegyezik a fent látható véletlenszerű rejtett értékkel. Ezután a bejövő űrlapbejegyzés érvényesítéséhez adja hozzá a [ValidateAntiForgeryToken] szűrőt a célműveletmetódushoz. Példa:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Engedélyezési szűrő, amely ellenőrzi, hogy:
* A bejövő kérelemben van egy cookie, amit __RequestVerificationToken
* A bejövő kérelemnek `Request.Form` van egy __RequestVerificationToken nevű bejegyzése.
* Ezek a `Request.Form` cookie-k és értékek egyeznek Feltételezve, hogy minden rendben van, a kérelem megy keresztül a szokásos módon. De ha nem, akkor egy engedélyezési hiba üzenettel: "A szükséges hamisítás elleni jogkivonat nem lett megadva, vagy érvénytelen" üzenettel. 

### <a name="example"></a>Példa
Anti-CSRF és AJAX: Az űrlaptoken problémát jelenthet az AJAX-kérelmeknél, mivel egy AJAX-kérelem JSON-adatokat küldhet, nem HTML-űrlapadatokat. Az egyik megoldás az, hogy a jogkivonatok küldése egy egyéni HTTP-fejlécben. A következő kód borotva szintaxist használ a jogkivonatok létrehozásához, majd hozzáadja a jogkivonatokat egy AJAX-kérelemhez. 
```csharp
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }

    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Példa
A kérelem feldolgozásakor bontsa ki a jogkivonatokat a kérelem fejlécéből. Ezután hívja meg az AntiForgery.Validate metódust a tokenek érvényesítéséhez. Az Érvényesítés metódus kivételt okoz, ha a tokenek érvénytelenek.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Webes űrlapok |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Használja ki ASP.NET beépített funkciókat a webes támadások kivédeni](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Lépéseket** | A WebForm alapú alkalmazások BAN elkövetett CSRF-támadások mérsékelhetők, ha a ViewStateUserKey kulcsot egy véletlenszerű karakterláncra állítja, amely felhasználónként változik – felhasználói azonosító, vagy ami még jobb, munkamenet-azonosító. Számos technikai és közösségi okokból a munkamenet-azonosító sokkal jobban illeszkedik, mivel a munkamenet-azonosító kiszámíthatatlan, időtúljár, és felhasználónként változik.|

### <a name="example"></a>Példa
Itt van a kód, amire szüksége döngissze az összes oldalában:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a name="set-up-session-for-inactivity-lifetime"></a><a id="inactivity-lifetime"></a>Munkamenet beállítása az inaktivitás élettartamára

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [HttpSessionState.Timeout tulajdonság](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Lépéseket** | A munkamenet-időtúllépést az jelenti, amely akkor következik be, amikor a felhasználó nem hajt végre semmilyen műveletet a webhelyen egy időköz alatt (a webkiszolgáló határozza meg). Az esemény a kiszolgálóoldalon a felhasználói munkamenet állapotát "érvénytelenre" módosítja (például "nem használják többé"), és utasítja a webkiszolgálót, hogy semmisítse meg (a benne lévő összes adat törlése). A következő kódpélda az időout-munkamenet attribútumot 15 percre állítja a Web.config fájlban.|

### <a name="example"></a>Példa
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Fenyegetésészlelés engedélyezése az Azure SQL-ben

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Webes űrlapok |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Űrlapelem hitelesítéshez (ASP.NET A beállítások sémája)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Lépéseket** | Az Űrlapok hitelesítési jegy cookie-jának időmeghosszabbítása 15 percre|

### <a name="example"></a>Példa
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Webes űrlapok, MVC5 |
| **Attribútumok**              | EnvironmentType - OnPrem |
| **Referencia**              | [asdeqa között](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Lépéseket** | Ha a webes alkalmazás függő entitás, és az ADFS az STS, a hitelesítési cookie-k élettartama - FedAuth tokenek - a következő konfigurációval állítható be a web.config-ban:|

### <a name="example"></a>Példa
```XML
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:15:0" />
      <!-- Set requireHttps=true; -->
      <wsFederation passiveRedirectEnabled="true" issuer="http://localhost:39529/" realm="https://localhost:44302/" reply="https://localhost:44302/" requireHttps="true"/>
      <!--
      Use the code below to enable encryption-decryption of claims received from ADFS. Thumbprint value varies based on the certificate being used.
      <serviceCertificate>
        <certificateReference findValue="4FBBBA33A1D11A9022A5BF3492FF83320007686A" storeLocation="LocalMachine" storeName="My" x509FindType="FindByThumbprint" />
      </serviceCertificate>
      -->
    </federationConfiguration>
  </system.identityModel.services>
```

### <a name="example"></a>Példa
Az ADFS által kibocsátott SAML jogcímjogkivonat élettartamát is 15 percre kell állítani a következő powershell parancs a ADFS-kiszolgálón történő végrehajtásával:
```csharp
Set-ADFSRelyingPartyTrust -TargetName "<RelyingPartyWebApp>" -ClaimsProviderName @("Active Directory") -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a name="implement-proper-logout-from-the-application"></a><a id="proper-app-logout"></a>Megfelelő kijelentkezés megvalósítása az alkalmazásból

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Végezze el a megfelelő kijelentkezést az alkalmazásból, amikor a felhasználó megnyomja a kijelentkezés gombot. Kijelentkezéskor az alkalmazásnak meg kell semmisítenie a felhasználói munkamenetet, és alaphelyzetbe kell állítania és érvénytelenítenie kell a munkamenet cookie-értékét, valamint vissza kell állítania és érvénytelenítenie kell a hitelesítési cookie értékét. Ha több munkamenet van egyetlen felhasználói identitáshoz kötve, akkor azokat a kiszolgálóoldalon időkimaradáskor vagy kijelentkezéskor együttesen le kell állítani. Végül győződjön meg arról, hogy a Kijelentkezés funkció minden oldalon elérhető. |

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-apis"></a><a id="csrf-api"></a>A webhelyek közötti kérelemhamisítás (CSRF) ASP.NET webes API-k elleni támadásainak enyhítése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | A helyek közötti kérelemhamisítás (CSRF vagy XSRF) egy olyan típusú támadás, amelyben a támadó műveleteket hajthat végre egy másik felhasználó által létrehozott munkamenet biztonsági környezetében egy webhelyen. A cél a tartalom módosítása vagy törlése, ha a megcélzott webhely kizárólag munkamenet-cookie-kra támaszkodik a fogadott kérelem hitelesítéséhez. A támadó úgy használhatja ki a biztonsági rést, hogy egy másik felhasználó böngészőjébe veszi, hogy betöltsön egy URL-t egy olyan sebezhető webhelyről, amelyen a felhasználó már be van jelentkezve. Ezt a támadó nak számos módja van, például egy másik webhely üzemeltetésével, amely betölt egy erőforrást a sebezhető kiszolgálóról, vagy rálehet kérni a felhasználót, hogy kattintson egy hivatkozásra. A támadás megelőzhető, ha a kiszolgáló további jogkivonatot küld az ügyfélnek, megköveteli, hogy az ügyfél minden jövőbeli kérelemben felszámítsa a tokent, és ellenőrzi, hogy minden jövőbeli kérelem tartalmaz-e egy, az aktuális munkamenetre vonatkozó jogkivonatot, például a ASP.NET AntiForgeryToken vagy ViewState. |

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [A webhelyek közötti kérelemhamisítás (CSRF) támadásainak megakadályozása ASP.NET webes API-ban](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Lépéseket** | Anti-CSRF és AJAX: Az űrlaptoken problémát jelenthet az AJAX-kérelmeknél, mivel egy AJAX-kérelem JSON-adatokat küldhet, nem HTML-űrlapadatokat. Az egyik megoldás az, hogy a jogkivonatok küldése egy egyéni HTTP-fejlécben. A következő kód borotva szintaxist használ a jogkivonatok létrehozásához, majd hozzáadja a jogkivonatokat egy AJAX-kérelemhez. |

### <a name="example"></a>Példa
```Javascript
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }
    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Példa
A kérelem feldolgozásakor bontsa ki a jogkivonatokat a kérelem fejlécéből. Ezután hívja meg az AntiForgery.Validate metódust a tokenek érvényesítéséhez. Az Érvényesítés metódus kivételt okoz, ha a tokenek érvénytelenek.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

### <a name="example"></a>Példa
Anti-CSRF és ASP.NET MVC-űrlapok – Használja az AntiForgeryToken segédmódszert nézeteken; html.antiforgerytoken() formátumba, például:
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Példa
A fenti példa a következőhez hasonló lesz:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Példa
Ugyanakkor a Html.AntiForgeryToken() egy __RequestVerificationToken nevű cookie-t ad a látogatónak, amelynek értéke megegyezik a fent látható véletlenszerű rejtett értékkel. Ezután a bejövő űrlapbejegyzés érvényesítéséhez adja hozzá a [ValidateAntiForgeryToken] szűrőt a célműveletmetódushoz. Példa:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Engedélyezési szűrő, amely ellenőrzi, hogy:
* A bejövő kérelemben van egy cookie, amit __RequestVerificationToken
* A bejövő kérelemnek `Request.Form` van egy __RequestVerificationToken nevű bejegyzése.
* Ezek a `Request.Form` cookie-k és értékek egyeznek Feltételezve, hogy minden rendben van, a kérelem megy keresztül a szokásos módon. De ha nem, akkor egy engedélyezési hiba üzenettel: "A szükséges hamisítás elleni jogkivonat nem lett megadva, vagy érvénytelen" üzenettel.

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | MVC5, MVC6 |
| **Attribútumok**              | Identitásszolgáltató – ADFS, identitásszolgáltató – Azure AD |
| **Referencia**              | [Webes API biztonságossá tétele egyéni fiókokkal és helyi bejelentkezéssel ASP.NET Web API 2.2-ben](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Lépéseket** | Ha a webes API az OAuth 2.0 használatával védett, akkor egy tulajdonosi jogkivonatot vár az engedélyezési kérelem fejlécében, és csak akkor biztosít hozzáférést a kéréshez, ha a jogkivonat érvényes. A cookie-alapú hitelesítéssel ellentétben a böngészők nem csatolják a tulajdonosi jogkivonatokat a kérelmekhez. A kérelmező ügyfélnek explicit módon csatolnia kell a tulajdonosi jogkivonatot a kérelem fejlécében. Ezért ASP.NET OAuth 2.0-s használatával védett webes API-k esetében a bemutatóra szóló jogkivonatok a CSRF-támadások elleni védelemnek minősülnek. Kérjük, vegye figyelembe, hogy ha az alkalmazás MVC része űrlaphitelesítést használ (azaz cookie-kat használ), az MVC webalkalmazásnak hamisítás elleni tokeneket kell használnia. |

### <a name="example"></a>Példa
A webes API-t tájékoztatni kell arról, hogy csak a tulajdonosi tokenekre támaszkodjon, és ne a cookie-kra. Meg lehet tenni a következő `WebApiConfig.Register` konfiguráció módszer:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

Az SuppressDefaultHostAuthentication metódus arra utasítja a Webes API-t, hogy figyelmen kívül hagyja az olyan hitelesítéseket, amelyek a kérelem elérése előtt történnek a webes API-folyamathoz, akár az IIS, akár az OWIN köztes szoftver által. Így korlátozhatjuk a webes API-t, hogy csak tulajdonosi jogkivonatokkal hitelesítse magát.
