---
title: Munkamenet-kezelés – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: a Threat Modeling Toolban elérhető fenyegetések enyhítése
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
ms.openlocfilehash: fd7a12dca92a4b84ecd3a2c9644509a1dc705c35
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727853"
---
# <a name="security-frame-session-management"></a>Biztonsági keret: Munkamenet-kezelés
| Termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[A megfelelő kijelentkezés megvalósítása a ADAL metódusok használatával az Azure AD használatakor](#logout-adal)</li></ul> |
| IoT-eszköz | <ul><li>[A generált SaS-tokenek véges élettartamának használata](#finite-tokens)</li></ul> |
| **Azure Document DB** | <ul><li>[A jogkivonat minimális élettartamának használata a generált erőforrás-tokenekhez](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Megfelelő kijelentkezés implementálása a WsFederation metódusok használatával az ADFS használatakor](#wsfederation-logout)</li></ul> |
| **Identity Server** | <ul><li>[Megfelelő kijelentkezés megvalósítása az Identity Server használatakor](#proper-logout)</li></ul> |
| **Webalkalmazás** | <ul><li>[A HTTPS protokollon keresztül elérhető alkalmazások biztonságos cookie-kat kell használniuk](#https-secure-cookies)</li><li>[A HTTP-alapú alkalmazásoknak csak a cookie-definícióhoz kell megadniuk a http-t](#cookie-definition)</li><li>[A helyek közötti kérelmek hamisításával (CSRF) szembeni támadások elleni ASP.NET weblapok](#csrf-asp)</li><li>[Munkamenet beállítása inaktivitás élettartama esetén](#inactivity-lifetime)</li><li>[A megfelelő kijelentkezés megvalósítása az alkalmazásból](#proper-app-logout)</li></ul> |
| **Webes API** | <ul><li>[A ASP.NET webes API-k közötti CSRF-támadások elleni védekezés](#csrf-api)</li></ul> |

## <a id="logout-adal"></a>A megfelelő kijelentkezés megvalósítása a ADAL metódusok használatával az Azure AD használatakor

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Ha az alkalmazás az Azure AD által kiállított hozzáférési jogkivonatra támaszkodik, a kijelentkezési eseménykezelőnek meg kell hívnia a következőt: |

### <a name="example"></a>Példa
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Példa
A munkamenet. megszakítás () metódus meghívásával is el kell pusztítani a felhasználói munkamenetet. A következő módszer a felhasználó kijelentkezésének biztonságos megvalósítását mutatja be:
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

## <a id="finite-tokens"></a>A generált SaS-tokenek véges élettartamának használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Az Azure IoT Hub való hitelesítéshez létrehozott SaS-tokeneknek véges lejárati időtartammal kell rendelkezniük. Az SaS-jogkivonat élettartamát minimumra kell korlátozni, hogy korlátozza a visszajátszható időkereteket abban az esetben, ha a jogkivonatok biztonsága sérül.|

## <a id="resource-tokens"></a>A jogkivonat minimális élettartamának használata a generált erőforrás-tokenekhez

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Document DB | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Csökkentse az erőforrás-token TimeSpan a szükséges minimális értékre. Az erőforrás-jogkivonatok alapértelmezett érvényes TimeSpan 1 óra.|

## <a id="wsfederation-logout"></a>Megfelelő kijelentkezés implementálása a WsFederation metódusok használatával az ADFS használatakor

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | ADFS | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Ha az alkalmazás az ADFS által kiállított STS-tokenre támaszkodik, a kijelentkezési eseménykezelőnek meg kell hívnia a WSFederationAuthenticationModule. FederatedSignOut () metódust a felhasználó kijelentkezéséhez. Az aktuális munkamenetet is meg kell semmisíteni, és a munkamenet-jogkivonat értékét alaphelyzetbe kell állítani és el kell pusztítani.|

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

## <a id="proper-logout"></a>Megfelelő kijelentkezés megvalósítása az Identity Server használatakor

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identity Server | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [IdentityServer3 – összevont kijelentkezés](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Lépések** | A IdentityServer támogatja a külső összevonása való kapcsolódás lehetőségét. Ha a felhasználó kijelentkezik egy felsőbb rétegbeli identitás-szolgáltatótól, a használt protokolltól függően előfordulhat, hogy értesítést kap, amikor a felhasználó kijelentkezik. Lehetővé teszi, hogy a IdentityServer értesítse ügyfeleit, hogy a felhasználók is alá tudják írni a felhasználókat. A megvalósítás részleteiért olvassa el a hivatkozások szakaszban található dokumentációt.|

## <a id="https-secure-cookies"></a>A HTTPS protokollon keresztül elérhető alkalmazások biztonságos cookie-kat kell használniuk

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | EnvironmentType – helyszíni |
| **Hivatkozik**              | [HttpCookies elem (ASP.net-beállítási séma)](https://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [HttpCookie. Secure tulajdonság](https://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Lépések** | A cookie-k általában csak azon tartomány számára érhetők el, amelyhez hatókörük van. Sajnos a "tartomány" definíciója nem tartalmazza a protokollt, így a HTTPS-kapcsolaton keresztül létrehozott cookie-k HTTP-n keresztül érhetők el. A "Secure" attribútum azt jelzi a böngészőnek, hogy a cookie-t csak HTTPS-kapcsolaton keresztül kell elérhetővé tenni. Győződjön meg arról, hogy a HTTPS protokollon beállított összes cookie a **biztonságos** attribútumot használja. A követelmény a web. config fájlban a requireSSL attribútum true értékre állításával kényszeríthető ki. Ez az előnyben részesített megközelítés, mivel az összes jelenlegi és jövőbeli cookie **biztonságos** attribútumát a kód további módosításának szükségessége nélkül fogja kikényszeríteni.|

### <a name="example"></a>Példa
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
A beállítás akkor is érvényben van, ha a HTTP-t használja az alkalmazás eléréséhez. Ha a HTTP az alkalmazás elérésére szolgál, a beállítás megtöri az alkalmazást, mert a cookie-k a Secure attribútummal vannak beállítva, és a böngésző nem küldi vissza azokat az alkalmazásnak.

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Web Forms, MVC5 |
| **Attribútumok**              | EnvironmentType – helyszíni |
| **Hivatkozik**              | –  |
| **Lépések** | Ha a webalkalmazás a függő entitás, a identitásszolgáltató pedig az ADFS-kiszolgáló, akkor a FedAuth- `system.identityModel.services` token biztonságos attribútuma a requireSSL True értékre állításával konfigurálható a web. config fájlban:|

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

## <a id="cookie-definition"></a>A HTTP-alapú alkalmazásoknak csak a cookie-definícióhoz kell megadniuk a http-t

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Biztonságos cookie-attribútum](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Lépések** | A többhelyes parancsfájlok (XSS) elleni támadások kockázatának enyhítése érdekében egy új attribútum – httpOnly – a cookie-khoz lett bevezetve, és az összes jelentős böngésző támogatja. Az attribútum azt adja meg, hogy a cookie nem érhető el parancsfájlon keresztül. A HttpOnly-cookie-k használatával a webalkalmazások csökkentik a cookie-ban található bizalmas információk meglopásának lehetőségét, és elküldhetik őket a támadók webhelyére. |

### <a name="example"></a>Példa
A cookie-kat használó összes HTTP-alapú alkalmazásnak meg kell adnia a HttpOnly a cookie-definícióban a következő konfigurációnak a web. config fájlban való megvalósításával:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Web Forms |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [FormsAuthentication. RequireSSL tulajdonság](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Lépések** | A RequireSSL tulajdonság értéke egy ASP.NET-alkalmazás konfigurációs fájljában van beállítva a konfigurációs elem requireSSL attribútumának használatával. Megadhatja a ASP.NET alkalmazás Web. config fájljában, hogy az requireSSL attribútum beállításával az SSL (SSL) szükséges-e az űrlapalapú hitelesítési cookie-nak a kiszolgálóra való visszaküldéséhez.|

### <a name="example"></a>Példa 
A következő mintakód a web. config fájlban állítja be a requireSSL attribútumot.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | MVC5 |
| **Attribútumok**              | EnvironmentType – helyszíni |
| **Hivatkozik**              | [Windows Identity Foundation (WIF) – konfiguráció – II. rész](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Lépések** | A FedAuth-cookie-k httpOnly attribútumának beállításához a hideFromCsript attribútum értékét igaz értékre kell állítani. |

### <a name="example"></a>Példa
A következő konfiguráció a megfelelő konfigurációt jeleníti meg:
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

## <a id="csrf-asp"></a>A helyek közötti kérelmek hamisításával (CSRF) szembeni támadások elleni ASP.NET weblapok

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | A helyek közötti kérelmek hamisítása (CSRF vagy XSRF) olyan támadási típus, amelyben a támadók műveleteket végezhetnek egy másik felhasználó munkamenetének biztonsági környezetében egy webhelyen. A cél a tartalom módosítása vagy törlése, ha a célként megadott webhely kizárólag munkamenet-cookie-kra támaszkodik a fogadott kérések hitelesítéséhez. Egy támadó kihasználhatja ezt a biztonsági rést azáltal, hogy egy másik felhasználó böngészőjében betölt egy URL-címet egy olyan sebezhető helyről, amelyen a felhasználó már be van jelentkezve. A támadók többféleképpen is elvégezhetik ezt a megoldást, például egy másik webhely üzemeltetésével, amely egy erőforrást tölt be a sebezhető kiszolgálóról, vagy egy hivatkozásra kattintva beolvassa a felhasználót. A támadás megakadályozható, ha a kiszolgáló további jogkivonatot küld az ügyfélnek, az ügyfélnek tartalmaznia kell a tokent az összes jövőbeli kérelemben, és ellenőriznie kell, hogy az összes jövőbeli kérelem tartalmaz-e jogkivonatot, amely az aktuális munkamenetre vonatkozik, például a ASP.NET használatával. AntiForgeryToken vagy ViewState. |

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | MVC5, MVC6 |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [XSRF/CSRF-megelőzés a ASP.NET MVC és weblapok esetében](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Lépések** | CSRF és ASP.net MVC-űrlapok – a `AntiForgeryToken` segítő metódus használata a nézetekben `Html.AntiForgeryToken()` , például a következő formában:|

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
Ugyanakkor a HTML. AntiForgeryToken () egy __RequestVerificationToken nevű cookie-t biztosít a látogatónak, ugyanazzal az értékkel, mint a fent látható véletlenszerű rejtett érték. Ezután a bejövő űrlap bejegyzésének érvényesítéséhez adja hozzá a [ValidateAntiForgeryToken] szűrőt a cél műveleti metódushoz. Példa:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
A következőket ellenőrző engedélyezési szűrő:
* A bejövő kérelem rendelkezik egy __RequestVerificationToken nevű cookie-val.
* A bejövő kérelem egy `Request.Form` __RequestVerificationToken nevű bejegyzést tartalmaz.
* Ezek a cookie `Request.Form` -és értékek megegyeznek, feltételezve, hogy minden rendben van, a kérés a szokásos módon halad át. De ha nem, akkor az "egy szükséges hamisítási jogkivonat nem lett megadva vagy érvénytelen" üzenettel rendelkező engedélyezési hiba. 

### <a name="example"></a>Példa
Anti-CSRF és AJAX: Az űrlap-jogkivonat az AJAX-kérelmek esetében lehet probléma, mivel az AJAX-kérések JSON-adatküldést is küldhetnek, nem HTML-űrlapokat. Az egyik megoldás, ha egy egyéni HTTP-fejlécben küldi el a jogkivonatokat. A következő kód a borotva szintaxisát használja a tokenek létrehozásához, majd hozzáadja a jogkivonatokat egy AJAX-kérelemhez. 
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
A kérelem feldolgozása során bontsa ki a tokeneket a kérelem fejlécébe. Ezután hívja meg a hamisítást. Ellenőrizze a metódust a jogkivonatok érvényesítéséhez. Az érvényesítési metódus kivételt jelez, ha a tokenek nem érvényesek.
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

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Web Forms |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Használja ki a ASP.NET beépített funkcióinak előnyeit a webes támadások kivédéséhez](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Lépések** | A webűrlapon alapuló alkalmazások CSRF elleni támadásait úgy lehet enyhíteni, ha a ViewStateUserKey egy véletlenszerű karakterláncra állítja be, amely minden felhasználói AZONOSÍTÓra, vagy még jobb, de a munkamenet-AZONOSÍTÓra változik. Számos technikai és társadalmi okból kifolyólag a munkamenet-azonosító sokkal jobban illeszkedik, mert a munkamenet-azonosító előre nem látható, időtúllépést okoz, és felhasználónként változhat.|

### <a name="example"></a>Példa
Az összes oldalon a következő kódot kell megadnia:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a id="inactivity-lifetime"></a>Munkamenet beállítása inaktivitás élettartama esetén

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [HttpSessionState. timeout tulajdonság](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Lépések** | A munkamenet időkorlátja arra az esetre vonatkozik, amikor egy felhasználó nem hajt végre semmilyen műveletet a webhelyen egy adott időszakban (a webkiszolgáló által definiált) intervallumban. Az esemény a kiszolgáló oldalán módosítsa a felhasználói munkamenet állapotát az "Érvénytelen" értékre (például "nincs használatban"), és utasítsa a webkiszolgálót, hogy elpusztítsa azt (az összes benne található adat törlése). A következő kódrészlet a web. config fájlban 15 percre állítja be az időtúllépési munkamenet attribútumát.|

### <a name="example"></a>Példa
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a id="threat-detection"></a>Veszélyforrások észlelésének engedélyezése az Azure SQL-ben

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Web Forms |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Űrlapalapú elem a hitelesítéshez (ASP.NET-beállítási séma)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Lépések** | Az űrlapos hitelesítési jegy cookie időkorlátjának beállítása 15 percre|

### <a name="example"></a>Példa
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Web Forms, MVC5 |
| **Attribútumok**              | EnvironmentType – helyszíni |
| **Hivatkozik**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Lépések** | Ha a webalkalmazás függő entitást használ, és az ADFS az STS, a hitelesítési cookie-k FedAuth-tokenek élettartama a web. config fájlban a következő konfigurációval állítható be:|

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
A következő PowerShell-parancsnak az ADFS-kiszolgálón történő végrehajtásával az SAML-jogcím tokenjét is 15 percre kell állítani.
```csharp
Set-ADFSRelyingPartyTrust -TargetName “<RelyingPartyWebApp>” -ClaimsProviderName @(“Active Directory”) -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a id="proper-app-logout"></a>A megfelelő kijelentkezés megvalósítása az alkalmazásból

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Megfelelő kijelentkezés végrehajtása az alkalmazásból, amikor a felhasználó megnyomja a kijelentkezés gombot. A kijelentkezéskor az alkalmazásnak el kell pusztítani a felhasználó munkamenetét, és vissza kell állítania és meg kell semmisítenie a munkamenet cookie-értékét, valamint az alaphelyzetbe állítását és a nullifying Továbbá, ha több munkamenet egyetlen felhasználói identitáshoz van kötve, akkor a kiszolgáló oldalán, időtúllépéssel vagy kijelentkezéssel együttesen kell leállítani őket. Végül ellenőrizze, hogy a kijelentkezés funkció minden oldalon elérhető-e. |

## <a id="csrf-api"></a>A ASP.NET webes API-k közötti CSRF-támadások elleni védekezés

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | A helyek közötti kérelmek hamisítása (CSRF vagy XSRF) olyan támadási típus, amelyben a támadók műveleteket végezhetnek egy másik felhasználó munkamenetének biztonsági környezetében egy webhelyen. A cél a tartalom módosítása vagy törlése, ha a célként megadott webhely kizárólag munkamenet-cookie-kra támaszkodik a fogadott kérések hitelesítéséhez. Egy támadó kihasználhatja ezt a biztonsági rést azáltal, hogy egy másik felhasználó böngészőjében betölt egy URL-címet egy olyan sebezhető helyről, amelyen a felhasználó már be van jelentkezve. A támadók többféleképpen is elvégezhetik ezt a megoldást, például egy másik webhely üzemeltetésével, amely egy erőforrást tölt be a sebezhető kiszolgálóról, vagy egy hivatkozásra kattintva beolvassa a felhasználót. A támadás megakadályozható, ha a kiszolgáló további jogkivonatot küld az ügyfélnek, az ügyfélnek tartalmaznia kell a tokent az összes jövőbeli kérelemben, és ellenőriznie kell, hogy az összes jövőbeli kérelem tartalmaz-e jogkivonatot, amely az aktuális munkamenetre vonatkozik, például a ASP.NET használatával. AntiForgeryToken vagy ViewState. |

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | MVC5, MVC6 |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [A helyek közötti kérelmek hamisításának (CSRF) megelőzése a ASP.NET webes API-ban](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Lépések** | Anti-CSRF és AJAX: Az űrlap-jogkivonat az AJAX-kérelmek esetében lehet probléma, mivel az AJAX-kérések JSON-adatküldést is küldhetnek, nem HTML-űrlapokat. Az egyik megoldás, ha egy egyéni HTTP-fejlécben küldi el a jogkivonatokat. A következő kód a borotva szintaxisát használja a tokenek létrehozásához, majd hozzáadja a jogkivonatokat egy AJAX-kérelemhez. |

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
A kérelem feldolgozása során bontsa ki a tokeneket a kérelem fejlécébe. Ezután hívja meg a hamisítást. Ellenőrizze a metódust a jogkivonatok érvényesítéséhez. Az érvényesítési metódus kivételt jelez, ha a tokenek nem érvényesek.
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
CSRF és ASP.NET MVC-űrlapok – a AntiForgeryToken Helper metódus használata a nézetekben; Helyezzen egy HTML. AntiForgeryToken () formátumot az űrlapra, például:
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Példa
A fenti példában a következőhöz hasonló kimenet fog kinézni:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Példa
Ugyanakkor a HTML. AntiForgeryToken () egy __RequestVerificationToken nevű cookie-t biztosít a látogatónak, ugyanazzal az értékkel, mint a fent látható véletlenszerű rejtett érték. Ezután a bejövő űrlap bejegyzésének érvényesítéséhez adja hozzá a [ValidateAntiForgeryToken] szűrőt a cél műveleti metódushoz. Példa:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
A következőket ellenőrző engedélyezési szűrő:
* A bejövő kérelem rendelkezik egy __RequestVerificationToken nevű cookie-val.
* A bejövő kérelem egy `Request.Form` __RequestVerificationToken nevű bejegyzést tartalmaz.
* Ezek a cookie `Request.Form` -és értékek megegyeznek, feltételezve, hogy minden rendben van, a kérés a szokásos módon halad át. De ha nem, akkor az "egy szükséges hamisítási jogkivonat nem lett megadva vagy érvénytelen" üzenettel rendelkező engedélyezési hiba.

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | MVC5, MVC6 |
| **Attribútumok**              | Identitás-szolgáltató – ADFS, identitás-szolgáltató – Azure AD |
| **Hivatkozik**              | [Webes API-k biztonságossá tétele egyéni fiókokkal és helyi bejelentkezéssel a ASP.NET web API 2,2-ban](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Lépések** | Ha a webes API a OAuth 2,0-es verzióval védett, akkor az engedélyezési kérelem fejlécében egy tulajdonosi jogkivonatot vár, és csak akkor biztosít hozzáférést a kéréshez, ha a jogkivonat érvényes. A cookie-alapú hitelesítéstől eltérően a böngészők nem csatlakoztatják a kérelmekhez a tulajdonosi jogkivonatokat. A kérelmező ügyfélnek explicit módon csatolnia kell a tulajdonosi jogkivonatot a kérelem fejlécében. Ezért a OAuth 2,0-et használó ASP.NET webes API-k esetében a tulajdonosi jogkivonatok CSRF támadások elleni védekezésnek tekintendők. Vegye figyelembe, hogy ha az alkalmazás MVC része űrlapos hitelesítést használ (például cookie-kat használ), a hamisítási jogkivonatokat az MVC webalkalmazásnak kell használnia. |

### <a name="example"></a>Példa
A webes API-t tájékoztatni kell, hogy csak a tulajdonosi jogkivonatokra támaszkodjon, nem pedig a cookie-kra. A `WebApiConfig.Register` metódus a következő konfigurációval végezhető el:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

A SuppressDefaultHostAuthentication metódus közli a webes API-val, hogy figyelmen kívül hagyja a kérést, mielőtt a kérelem eléri a webes API-folyamatot vagy az IIS-t vagy a OWIN middleware-t. Így a webes API-t csak a tulajdonosi jogkivonatok használatával lehet hitelesíteni.
