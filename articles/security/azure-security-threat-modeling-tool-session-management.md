---
title: Munkamenet - Microsoft fenyegetés modellezési eszköz - kezelés Azure |} Microsoft Docs
description: a fenyegetések modellezése eszköz felfedett fenyegetések megoldást
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 24bd0e8eff616920dba0eb5353f983444e3161cd
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
ms.locfileid: "28019959"
---
# <a name="security-frame-session-management--articles"></a>Biztonsági keret: Munkamenet-kezelés |} Cikkek 
| A termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Alkalmazzon megfelelő kijelentkezési ADAL módszerek használatát, ha az Azure AD segítségével](#logout-adal)</li></ul> |
| IoT-eszközök | <ul><li>[Generált SaS-tokenje véges élettartamai használata](#finite-tokens)</li></ul> |
| **Az Azure Document DB rendszerbe** | <ul><li>[A létrehozott erőforrás-jogkivonatokat minimális jogkivonat élettartamát használata](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Alkalmazzon megfelelő kijelentkezési WsFederation módszerek használatát, ha az AD FS használatával](#wsfederation-logout)</li></ul> |
| **Identity Serverben** | <ul><li>[Alkalmazzon megfelelő kijelentkezési Identitáskiszolgálók használatakor](#proper-logout)</li></ul> |
| **Webalkalmazás** | <ul><li>[HTTPS-KAPCSOLATON keresztül elérhető alkalmazások biztonságos cookie-kat kell használni.](#https-secure-cookies)</li><li>[Az összes HTTP-alapú alkalmazások csak olyan cookie-k meghatározása http kell megadni](#cookie-definition)</li><li>[ASP.NET-weblapok többhelyes kérelem hamisítására (CSRF) támadások elleni](#csrf-asp)</li><li>[Állítsa be a munkamenet inaktivitás élettartama](#inactivity-lifetime)</li><li>[Az alkalmazás megfelelő kijelentkezési megvalósítása](#proper-app-logout)</li></ul> |
| **Webes API** | <ul><li>[ASP.NET webes API-k többhelyes kérelem hamisítására (CSRF) támadások elleni](#csrf-api)</li></ul> |

## <a id="logout-adal"></a>Alkalmazzon megfelelő kijelentkezési ADAL módszerek használatát, ha az Azure AD segítségével

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Ha az alkalmazás az Azure AD által kiállított jogkivonat támaszkodik, meg kell hívnia a kijelentkezési eseménykezelő |

### <a name="example"></a>Példa
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Példa
Akkor kell felhasználói munkamenet is megsemmisítése Session.Abandon() metódus meghívásával. A következő metódus jeleníti meg a felhasználó kijelentkezik biztonságos végrehajtásának:
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

## <a id="finite-tokens"></a>Generált SaS-tokenje véges élettartamai használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszközök | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | SaS-tokenje jön létre az Azure IoT Hub kell véges lejárati idővel rendelkeznek. A SaS-jogkivonat élettartamát nyomon legalább mennyi ideig visszajátszani abban az esetben, ha a jogkivonatok kerülnek veszélybe korlátozni.|

## <a id="resource-tokens"></a>A létrehozott erőforrás-jogkivonatokat minimális jogkivonat élettartamát használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az Azure Document DB rendszerbe | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Csökkentse az erőforrás-jogkivonat timespan szükséges minimális értékre. Erőforrás alapértelmezett 1 órás érvényes timespan lehet.|

## <a id="wsfederation-logout"></a>Alkalmazzon megfelelő kijelentkezési WsFederation módszerek használatát, ha az AD FS használatával

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | ADFS | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Ha az alkalmazás STS-jogkivonatot AD FS által kibocsátott, a kijelentkezési eseménykezelő kell metódushívás WSFederationAuthenticationModule.FederatedSignOut() jelentkezzen ki a felhasználó számára. Is az aktuális munkamenet meg kell semmisíteni, és a munkamenet biztonságijogkivonat legyen alaphelyzetbe állítása és hatálytalanítja.|

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

## <a id="proper-logout"></a>Alkalmazzon megfelelő kijelentkezési Identitáskiszolgálók használatakor

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identity Server | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [IdentityServer3 összevont kijelentkezés](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Lépések** | IdentityServer támogatja a külső Identitásszolgáltatók összevonni kívánt. Amikor egy felhasználó kijelentkezik a felsőbb rétegbeli identitásszolgáltató, a használt protokolltól függően esetleg is megkapja az értesítéseket, amikor a felhasználó kijelentkezik. Az, akkor a felhasználó is jelentkezzen ki az ügyfelek IdentityServer lehetővé teszi. A megvalósítás részletei hivatkozások részben dokumentációjában.|

## <a id="https-secure-cookies"></a>HTTPS-KAPCSOLATON keresztül elérhető alkalmazások biztonságos cookie-kat kell használni.

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | EnvironmentType - a helyi üzemeltetésű |
| **Hivatkozások**              | [Elem (ASP.NET beállítási séma) httpCookies](http://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [HttpCookie.Secure tulajdonság](http://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Lépések** | A cookie-k általában csak a tartományhoz, amelynek hatóköre volt elérhető. Definíciója: "tartományi" sajnos nem tartalmazza a protokollt úgy, hogy a cookie-k, amelyek létrejönnek a HTTPS-KAPCSOLATON keresztül elérhető HTTP Protokollon keresztül. A "biztonságos" attribútum azt jelöli, a böngészőnek, hogy a cookie-k csak elérhetővé kell tenni HTTPS-KAPCSOLATON keresztül. Győződjön meg arról, hogy az összes cookie beállítása HTTPS használata a **biztonságos** attribútum. A követelmény a requireSSL attribútum true értékre állításával kényszerítheti a web.config fájlban. Ennek az oka az előnyben részesített módszerrel kényszeríti ki azt a **biztonságos** attribútum jelenlegi és jövőbeli cookie-k nem kell további kód módosításokat.|

### <a name="example"></a>Példa
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
A beállítás akkor is, ha az alkalmazás eléréséhez használt HTTP lép életbe. Az alkalmazáshoz való hozzáférés HTTP használata esetén a beállítás az alkalmazás megszakítja, mert a cookie-kat a biztonságos attribútummal van beállítva, és a böngésző nem visszaküldi azokat az alkalmazás.

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Web Forms keretrendszerre, MVC5 |
| **Attribútumok**              | EnvironmentType - a helyi üzemeltetésű |
| **Hivatkozások**              | –  |
| **Lépések** | A webalkalmazás a függő entitáshoz, és az IdP ADFS-kiszolgáló, a FedAuth jogkivonat biztonságos attribútum konfigurálható úgy, hogy requireSSL TRUE a `system.identityModel.services` szakasz a Web.config fájl:|

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

## <a id="cookie-definition"></a>Az összes HTTP-alapú alkalmazások csak olyan cookie-k meghatározása http kell megadni

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Biztonságos Cookie-attribútum](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Lépések** | Az információfelfedés kockázatának csökkentéséhez a többhelyes scripting (lehetővé) támadás a mérséklése érdekében új attribútum - httpOnly - cookie-k jelent, és az összes ismertebb böngésző támogatja. Az attribútum Megadja, hogy a cookie-k nem érhető el a parancsfájlon keresztül. Webalkalmazás HttpOnly cookie-k használata, csökkenti a lehetősége, hogy a cookie-ban tárolt bizalmas információ ellopják keresztül parancsfájl-e, és hogy egy támadó webhelynek küldött. |

### <a name="example"></a>Példa
Cookie-k használó összes HTTP-alapú alkalmazások kell HttpOnly a cookie-definíció adható meg, alkalmazásával segítse a web.config konfigurációs követően:
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
| **Alkalmazandó technológiák** | Web Forms keretrendszerre |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [FormsAuthentication.RequireSSL tulajdonság](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Lépések** | A RequireSSL tulajdonság értéke a konfigurációs fájlban, az ASP.NET-alkalmazás a konfigurációs elem requireSSL attribútumával. Megadhatja a Web.config fájlban az ASP.NET alkalmazás hogy SSL (Secure Sockets Layer) szükséges az űrlap-hitelesítési cookie-k visszatérhet a kiszolgáló úgy, hogy a requireSSL attribútum.|

### <a name="example"></a>Példa 
Az alábbi példakód beállítja a requireSSL attribútumot a Web.config fájlban.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | MVC5 |
| **Attribútumok**              | EnvironmentType - a helyi üzemeltetésű |
| **Hivatkozások**              | [A Windows Identity Foundation (WIF) konfigurációja – II. rész](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Lépések** | A FedAuth cookie-k httpOnly attribútum beállítása, hideFromCsript attribútum értéke TRUE értéket kell megadni. |

### <a name="example"></a>Példa
Következő konfigurációt a helyes konfiguráció látható:
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

## <a id="csrf-asp"></a>ASP.NET-weblapok többhelyes kérelem hamisítására (CSRF) támadások elleni

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Webhelyközi kérések hamisítására (CSRF vagy XSRF), amelyben a támadó műveleteket végezhet el a biztonsági környezetében webhelyen egy másik felhasználói munkamenetet a támadás típusú. A célja módosítsa vagy törölje a tartalmat, ha a célként kijelölt webhely kizárólag a munkamenet-cookie-k hitelesítésére kérelem érkezett. A támadó a biztonsági rés olvasson be egy másik felhasználó böngésző betölteni egy parancs egy URL-címet egy sebezhető helyről, amikor a felhasználó már jelentkezett be. Számos módon egy támadó úgy teheti meg, amely, például egy erőforrás betölti a sebezhető kiszolgálóról egy másik webhely üzemeltetéséhez, vagy a felhasználó első hivatkozásra. Ha a kiszolgáló elküldi az ügyfélnek a további tokent, az ügyfél számára, hogy a jogkivonat szerepeljen minden jövőbeni kérelemhez szükséges, és ellenőrzi, hogy minden későbbi kérelmek tartalmaz egy jogkivonatot, amely vonatkozik az aktuális munkamenetről, például az ASP.NET használatával megelőzhető a támadás AntiForgeryToken vagy a megjelenítési állapot. |

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | MVC5, MVC6 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [ASP.NET MVC és weblapok XSRF/CSRF megelőzése](http://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Lépések** | Kártevőirtó-CSRF és az ASP.NET MVC űrlapok - a `AntiForgeryToken` segédmetódust a nézetek; put egy `Html.AntiForgeryToken()` az űrlapon, például|

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
Egy időben Html.AntiForgeryToken() __RequestVerificationToken, a fenti véletlenszerű rejtett érték azonos értékű nevű cookie lehetőséget ad a látogató adott területre. Ezután egy bejövő közzétett űrlapból ellenőrzéséhez adja hozzá a [ValidateAntiForgeryToken] szűrő a célmetódushoz való kötéskor művelet. Példa:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Ellenőrzi, hogy a szűrő engedélyezési:
* A bejövő kérelem rendelkezik egy cookie-k hívása __RequestVerificationToken
* A bejövő kérelem egy `Request.Form` __RequestVerificationToken nevezett bejegyzés
* A cookie-k és `Request.Form` jól értékek egyeznek, feltéve, hogy minden, a kérelem végighalad normál. De ha nem, majd üzenettel engedélyezési hiba "szükséges hamisításgátló jogkivonat nincs megadva vagy érvénytelen". 

### <a name="example"></a>Példa
Kártevőirtó-CSRF és AJAX: az űrlap token az AJAX-kérelmek problémát okozhat, mert AJAX-kérelmet el tudja küldeni a JSON-adatokat, nem a HTML-űrlapot adatok. Egy megoldás, a jogkivonatok küldeni egy egyéni HTTP-fejlécben. Az alábbi kód Razor szintaxist használja a jogkivonatok létrehozásához, és hozzáadja a tokenek egy AJAX-kérelemre. 
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
A kérelem feldolgozása során a jogkivonatok kinyerése a kérelem fejlécében. Majd hívja a következő érvényesítse az AntiForgery.Validate metódust. A Validate metódus kivételt jelez, ha a jogkivonatok nem érvényes.
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
| **Alkalmazandó technológiák** | Web Forms keretrendszerre |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az ASP.NET beépített a támadások elleni védekezésben kivédése előnyeit](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Lépések** | A webes alapú alkalmazásokban CSRF támadások mérsékelhető a ViewStateUserKey értékre állításával véletlenszerű karakterlánc, amely változtatja az egyes felhasználók - felhasználói Azonosítóját, vagy jobb még, munkamenet-azonosítót. Műszaki és közösségi okokból számos, a munkamenet-azonosító egy javulás méretezése azért, mert egy munkamenet-azonosító előre nem látható, túllépi az időkorlátot, és a felhasználónkénti alapon változik.|

### <a name="example"></a>Példa
Az összes weblapot van szükség a kód itt látható:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a id="inactivity-lifetime"></a>Állítsa be a munkamenet inaktivitás élettartama

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [HttpSessionState.Timeout tulajdonság](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Lépések** | Időtúllépés az esemény lépett fel, amikor a felhasználó nem bármely művelet elvégzésére webhelyen a időközben (a webkiszolgáló által megadott) jelöli. Az esemény, a kiszolgáló oldalán, módosítsa a felhasználói munkamenet állapota "érvénytelen" (például "nem használható többé"), és kérje meg a webkiszolgálón. szüntesse meg az (bele tárolt összes adat törlése). Az alábbi példakód állítja be a munkamenet időtúllépés attribútumot 15 perc a Web.config fájlban.|

### <a name="example"></a>Példa
A(z) "XML-kódot <configuration> < system.web > <sessionState mode="InProc" cookieless="true" timeout="15" /> < /system.web ></configuration>
```

## <a id="threat-detection"></a>Enable Threat detection on Azure SQL
```

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Web Forms keretrendszerre |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Elem űrlap-hitelesítés (ASP.NET beállítási séma)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Lépések** | Az űrlapos hitelesítési jegyet cookie-k időkorlátja 15 percre beállítva|

### <a name="example"></a>Példa
A(z) "XML-kódot<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Title                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web Application | 
| **SDL Phase**               | Build |  
| **Applicable Technologies** | Web Forms, MVC5 |
| **Attributes**              | EnvironmentType - OnPrem |
| **References**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Steps** | When the web application is Relying Party and ADFS is the STS, the lifetime of the authentication cookies - FedAuth tokens - can be set by the following configuration in web.config:|

### Example
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
Az AD FS SAML kiadott is jogcím-jogkivonat élettartamát meg 15 perc, a következő az ADFS-kiszolgálón a következő powershell-parancs futtatásával:
```csharp
Set-ADFSRelyingPartyTrust -TargetName “<RelyingPartyWebApp>” -ClaimsProviderName @(“Active Directory”) -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a id="proper-app-logout"></a>Az alkalmazás megfelelő kijelentkezési megvalósítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Hajtsa végre megfelelő Kijelentkezés az alkalmazásból, amikor a felhasználó présgépet Kijelentkezés gombra. Követően jelentkezzen ki alkalmazás kell semmisítse meg a felhasználó munkamenetét, és is alaphelyzetbe állítása és érvényteleníti a munkamenet cookie-értéket alaphelyzetbe állítása és a hitelesítési cookie-értéket érvénytelenítését együtt. Is ha több munkamenetet egy felhasználói azonosítót vannak társítva, azok kell együttesen lezárni időkorlát, vagy jelentkezzen ki, a kiszolgáló oldalán. Végül győződjön meg arról, hogy kijelentkezési funkció minden oldalon érhető el. |

## <a id="csrf-api"></a>ASP.NET webes API-k többhelyes kérelem hamisítására (CSRF) támadások elleni

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Webhelyközi kérések hamisítására (CSRF vagy XSRF), amelyben a támadó műveleteket végezhet el a biztonsági környezetében webhelyen egy másik felhasználói munkamenetet a támadás típusú. A célja módosítsa vagy törölje a tartalmat, ha a célként kijelölt webhely kizárólag a munkamenet-cookie-k hitelesítésére kérelem érkezett. A támadó a biztonsági rés olvasson be egy másik felhasználó böngésző betölteni egy parancs egy URL-címet egy sebezhető helyről, amikor a felhasználó már jelentkezett be. Számos módon egy támadó úgy teheti meg, amely, például egy erőforrás betölti a sebezhető kiszolgálóról egy másik webhely üzemeltetéséhez, vagy a felhasználó első hivatkozásra. Ha a kiszolgáló elküldi az ügyfélnek a további tokent, az ügyfél számára, hogy a jogkivonat szerepeljen minden jövőbeni kérelemhez szükséges, és ellenőrzi, hogy minden későbbi kérelmek tartalmaz egy jogkivonatot, amely vonatkozik az aktuális munkamenetről, például az ASP.NET használatával megelőzhető a támadás AntiForgeryToken vagy a megjelenítési állapot. |

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | MVC5, MVC6 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [ASP.NET webes API-t a Webhelyközi kérések hamisítására (CSRF) támadások megelőzése](http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Lépések** | Kártevőirtó-CSRF és AJAX: az űrlap token az AJAX-kérelmek problémát okozhat, mert AJAX-kérelmet el tudja küldeni a JSON-adatokat, nem a HTML-űrlapot adatok. Egy megoldás, a jogkivonatok küldeni egy egyéni HTTP-fejlécben. Az alábbi kód Razor szintaxist használja a jogkivonatok létrehozásához, és hozzáadja a tokenek egy AJAX-kérelemre. |

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
A kérelem feldolgozása során a jogkivonatok kinyerése a kérelem fejlécében. Majd hívja a következő érvényesítse az AntiForgery.Validate metódust. A Validate metódus kivételt jelez, ha a jogkivonatok nem érvényes.
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
Kártevőirtó-CSRF és űrlapok ASP.NET MVC - AntiForgeryToken segédmetódus használatát nézetek; például egy Html.AntiForgeryToken() üzembe a formátumban:
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Példa
A fenti példában a következő hasonlót fog kimeneti:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Példa
Egy időben Html.AntiForgeryToken() __RequestVerificationToken, a fenti véletlenszerű rejtett érték azonos értékű nevű cookie lehetőséget ad a látogató adott területre. Ezután egy bejövő közzétett űrlapból ellenőrzéséhez adja hozzá a [ValidateAntiForgeryToken] szűrő a célmetódushoz való kötéskor művelet. Példa:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Ellenőrzi, hogy a szűrő engedélyezési:
* A bejövő kérelem rendelkezik egy cookie-k hívása __RequestVerificationToken
* A bejövő kérelem egy `Request.Form` __RequestVerificationToken nevezett bejegyzés
* A cookie-k és `Request.Form` jól értékek egyeznek, feltéve, hogy minden, a kérelem végighalad normál. De ha nem, majd üzenettel engedélyezési hiba "szükséges hamisításgátló jogkivonat nincs megadva vagy érvénytelen".

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | MVC5, MVC6 |
| **Attribútumok**              | Identity Provider - ADFS, identitásszolgáltató - az Azure AD |
| **Hivatkozások**              | [Egyes partnerek és az ASP.NET Web API 2.2 helyi bejelentkezési webes API biztonságossá tétele](http://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Lépések** | Ha a webes API-t OAuth 2.0 használatával biztonságossá majd azt vár egy tulajdonosi jogkivonatot a hitelesítési kérelem fejlécében, és hozzáférést biztosít a kérelem csak akkor, ha a jogkivonat érvényes. Cookie-alapú hitelesítés, eltérően böngészők csatolja a tulajdonosi jogkivonatok kérelmeket. A kérelmező ügyfélnek kell explicit módon csatolja a tulajdonosi jogkivonattal, a kérelem fejlécében. Ezért az ASP.NET Web API-k OAuth 2.0 használatával védett, tulajdonosi jogkivonatok számít egy CSRF támadások elleni védelmet. Vegye figyelembe, hogy ha az alkalmazás MVC része az űrlapos hitelesítés (azaz használ cookie-k) használ, hamisítás lehet az MVC webes alkalmazás által használható. |

### <a name="example"></a>Példa
A webes API-nak kell tájékoztatni csak a tulajdonosi jogkivonatok és nem a cookie-k használják. A következő konfigurációs végezhető `WebApiConfig.Register` metódus: "" C-éles kód config. SuppressDefaultHostAuthentication(); Config. Filters.Add (új HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```
The SuppressDefaultHostAuthentication method tells Web API to ignore any authentication that happens before the request reaches the Web API pipeline, either by IIS or by OWIN middleware. That way, we can restrict Web API to authenticate only using bearer tokens.