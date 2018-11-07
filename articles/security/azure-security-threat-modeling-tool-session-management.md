---
title: Munkamenet Management – Microsoft Fenyegetésmodellezési eszköz – Azure |} A Microsoft Docs
description: megoldások a fenyegetések között szerepelnek a Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 182a0232b5317b1a375a20bdd4c6467578dc775b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232742"
---
# <a name="security-frame-session-management"></a>Biztonsági keret: Munkamenet-kezelés
| Termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Megvalósítása az ADAL módszerekkel, amikor az Azure AD megfelelő kijelentkeztetése](#logout-adal)</li></ul> |
| IoT-eszköz | <ul><li>[Használni létrehozott SaS-jogkivonatok élettartama véges](#finite-tokens)</li></ul> |
| **Az Azure Document DB-ről** | <ul><li>[Minimális jogkivonatok élettartamának használni létrehozott erőforrás-jogkivonatok](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Alkalmazzon megfelelő kijelentkezési WsFederation módszerekkel ADFS használata esetén](#wsfederation-logout)</li></ul> |
| **Identitáskezelési kiszolgáló** | <ul><li>[Az identitás-kiszolgáló megfelelő kijelentkezési megvalósítási](#proper-logout)</li></ul> |
| **Webalkalmazás** | <ul><li>[HTTPS-kapcsolaton keresztül elérhető alkalmazások biztonságos cookie-kat kell használnia.](#https-secure-cookies)</li><li>[Az összes http-alapú alkalmazás csak a cookie-k meghatározása http kell megadnia.](#cookie-definition)</li><li>[Az ASP.NET-weblapokhoz többhelyes kérelem hamisítására (CSRF) támadások ellen](#csrf-asp)</li><li>[A munkamenet inaktivitás élettartamának beállítása](#inactivity-lifetime)</li><li>[Az alkalmazás megfelelő kijelentkezési megvalósítása](#proper-app-logout)</li></ul> |
| **Webes API** | <ul><li>[Az ASP.NET webes API-k többhelyes kérelem hamisítására (CSRF) támadások ellen](#csrf-api)</li></ul> |

## <a id="logout-adal"></a>Megvalósítása az ADAL módszerekkel, amikor az Azure AD megfelelő kijelentkeztetése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Ha az alkalmazás Azure AD által kiállított hozzáférési jogkivonat támaszkodik, meg kell hívnia a kijelentkezési eseménykezelő |

### <a name="example"></a>Példa
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Példa
Azt kell is megsemmisíteni a felhasználói munkamenet Session.Abandon() metódus meghívásával. A következő metódust és a felhasználó kijelentkeztetése biztonságos megvalósítását mutatja be:
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

## <a id="finite-tokens"></a>Használni létrehozott SaS-jogkivonatok élettartama véges

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Hitelesítéséhez az Azure IoT hubra generált SaS-tokeneket rendelkeznie kell egy véges lejárati időszak. Tartsa meg az SaS-jogkivonatok élettartamának minimális visszajátszani abban az esetben a jogkivonatok kerülnek veszélybe idő korlátozásához.|

## <a id="resource-tokens"></a>Minimális jogkivonatok élettartamának használni létrehozott erőforrás-jogkivonatok

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az Azure Document DB-ről | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Csökkentse az időtartam, az erőforrás-jogkivonat szükséges minimális értékre. Erőforrás-jogkivonatokat rendelkezik az alapértelmezett 1 órás érvényes időtartam.|

## <a id="wsfederation-logout"></a>Alkalmazzon megfelelő kijelentkezési WsFederation módszerekkel ADFS használata esetén

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | ADFS | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Ha az alkalmazás AD FS által kiállított STS-jogkivonatok támaszkodik, a kijelentkezési eseménykezelő kell hívja meg a felhasználó kijelentkezik WSFederationAuthenticationModule.FederatedSignOut() metódust. Is az aktuális munkamenet meg kell semmisíteni, és a munkamenet-jogkivonat értéke kell alaphelyzetbe állítása és hatálytalanítja.|

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

## <a id="proper-logout"></a>Az identitás-kiszolgáló megfelelő kijelentkezési megvalósítási

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identitáskezelési kiszolgáló | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [IdentityServer3 összevont jelentkezzen ki](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Lépések** | IdentityServer támogatja a külső identitásszolgáltató összevonni kívánt. Ha egy felhasználó kijelentkezik a felsőbb rétegbeli Identitásszolgáltatóként, attól függően, hogy a használt, protokoll, előfordulhat, hogy lehetséges értesítést kapnak, ha a felhasználó kijelentkezik. Lehetővé teszi a IdentityServer értesíteni az ügyfeleknek, hogy a felhasználó is jelentkezzen ki. A dokumentációban a a megvalósítás részleteit a hivatkozások szakaszban.|

## <a id="https-secure-cookies"></a>HTTPS-kapcsolaton keresztül elérhető alkalmazások biztonságos cookie-kat kell használnia.

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | EnvironmentType - rendszert |
| **Hivatkozások**              | [Elem (schéma nastavení Technologie) httpCookies](https://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [HttpCookie.Secure tulajdonság](https://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Lépések** | A cookie-k általában csak a tartományhoz, amelynek hatóköre is elérhető. "Tartomány" definíciójának sajnos nem tartalmazza a protokollt így cookie-k jönnek létre HTTPS-kapcsolaton keresztül, HTTP-n keresztül hozzáférhetők. A "biztonságos" attribútum azt jelöli, a böngészőnek, hogy a cookie-k csak elérhetővé kell tenni HTTPS-kapcsolaton keresztül. Győződjön meg arról, hogy az összes cookie-k beállítása HTTPS használatát a **biztonságos** attribútum. A követelmény a alapú attribútum igaz értékre állításával kényszeríthető ki a web.config fájlban. A legmegfelelőbb módszer nem kényszeríti, mert a **biztonságos** anélkül, hogy további kódot kellene az összes jelenlegi és jövőbeli cookie attribútuma.|

### <a name="example"></a>Példa
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
A beállítás akkor lép életbe, akkor is, ha az alkalmazás eléréséhez használt HTTP. HTTP-használata esetén az alkalmazás eléréséhez a beállítás megsérti az alkalmazás, mert a cookie-kat a biztonságos attribútummal vannak beállítva, és a böngésző nem küld őket az alkalmazásnak.

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Web Forms MVC5 |
| **Attribútumok**              | EnvironmentType - rendszert |
| **Hivatkozások**              | –  |
| **Lépések** | A webalkalmazás a függő entitás, és az identitásszolgáltatótól az ADFS-kiszolgáló, a FedAuth-jogkivonat biztonságos attribútum konfigurálható úgy, hogy a valódi alapú `system.identityModel.services` web.config szakaszában:|

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

## <a id="cookie-definition"></a>Az összes http-alapú alkalmazás csak a cookie-k meghatározása http kell megadnia.

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Biztonságos Cookie-attribútum](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Lépések** | A többhelyes parancsfájl-kezelési (XSS) támadások az információfelfedés kockázatának csökkentése érdekében, új attribútum - httpOnly - jelent meg a cookie-k, és minden főbb böngésző támogatja. Az attribútum meghatározza, hogy a cookie-k nem érhető el parancsfájl segítségével. Egy webalkalmazás HttpOnly cookie-k használatával csökkenti a előfordulhat, hogy az a cookie-ban tárolt bizalmas információ keresztül parancsfájl ellopják, a támadó webhelynek küldött. |

### <a name="example"></a>Példa
Cookie-kat használó összes HTTP-alapú alkalmazások kell megadnia HttpOnly a cookie-definícióban, a rendszer az életbe léptetésével konfigurációs a Web.config fájlban a következő:
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
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Web Forms |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [FormsAuthentication.RequireSSL tulajdonság](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Lépések** | Alapú tulajdonság értéke a konfigurációs fájlban, az ASP.NET-alkalmazás a konfigurációs elem alapú attribútumának használatával. Megadhatja a Web.config fájlban az ASP.NET alkalmazás-e SSL (Secure Sockets Layer) szükséges az űrlap-hitelesítési cookie-k térjen vissza a kiszolgáló a alapú attribútum beállításával.|

### <a name="example"></a>Példa 
Az alábbi példakód a alapú attribútum beállítása a Web.config fájlban.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | MVC5 |
| **Attribútumok**              | EnvironmentType - rendszert |
| **Hivatkozások**              | [Windows Identity Foundation (WIF) konfigurációja – II. rész](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Lépések** | HttpOnly attribútum FedAuth cookie-kra vonatkozó beállítása, hideFromCsript attribútum értéke igaz értékre kell állítani. |

### <a name="example"></a>Példa
Következő konfigurációt a megfelelő konfiguráció látható:
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

## <a id="csrf-asp"></a>Az ASP.NET-weblapokhoz többhelyes kérelem hamisítására (CSRF) támadások ellen

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Webhelyközi kérések hamisításának megakadályozása (CSRF vagy XSRF) olyan támadás, amelyben egy támadó különféle műveleteket végezhet biztonsági környezetében létrehozott munkamenet egy másik felhasználó egy webhelyről. A célja módosítsa vagy törölje a tartalmat, ha a célként kijelölt webhely kizárólag támaszkodik munkamenet cookie-k fogadott kérelmek hitelesítéséhez. A támadó a biztonsági rés betölteni egy parancs egy URL-címet, amelyre a felhasználó már bejelentkezett a sebezhető helyről egy másik felhasználó böngésző lekérésével. Többféleképpen teheti meg, hogy, mint például egy másik webhely, amely egy erőforrás tölt be a sebezhető kiszolgálót üzemeltető, vagy a felhasználó első egy támadó egy hivatkozásra kattintva. A támadás megelőzhető, ha a kiszolgáló egy kiegészítő jogkivonatot elküldi az ügyfélnek, az ügyfél tartalmazza ezt a jogkivonatot az összes jövőbeni kérések igényel, és ellenőrzi, hogy az összes jövőbeni kérések tartalmazzák-e egy jogkivonatot, amely vonatkozik az aktuális munkamenethez, mint például az ASP.NET használatával AntiForgeryToken vagy ViewState. |

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | MVC5, MVC6 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az ASP.NET MVC és weblapok XSRF/CSRF megelőzése](http://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Lépések** | Kártevőirtó-CSRF és űrlapok ASP.NET MVC - használja a `AntiForgeryToken` nézetek segédmetódus; put- `Html.AntiForgeryToken()` az űrlapon, például|

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
Egy időben Html.AntiForgeryToken() nevű __RequestVerificationToken fenti rejtett véletlenszerű értéket ugyanarra az értékre, a cookie-k lehetővé teszi a látogató adott területre. Ezután egy bejövő űrlap post ellenőrzéséhez adja hozzá a [ValidateAntiForgeryToken] szűrő a cél tartozó műveleti módszer. Példa:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtr autorizace, amely ellenőrzi, hogy:
* A bejövő kérelem rendelkezik __RequestVerificationToken nevű cookie-k
* A bejövő kérelem egy `Request.Form` __RequestVerificationToken nevű bejegyzés
* Ezek a cookie-k és `Request.Form` hodnoty se shodují, feltéve, hogy minden jól, a kérelem halad végig szokásos módon. De ha nem, majd üzenettel engedélyezési hiba "egy szükséges hamisításgátló jogkivonat nem lett megadva vagy érvénytelen a". 

### <a name="example"></a>Példa
Kártevőirtó-CSRF és AJAX: az űrlap jogkivonat az AJAX-kérelmeket, problémát jelenthetnek, mivel egy AJAX-kérést küldhet JSON-adatokat, nem a HTML-űrlapot adatok. Egy megoldás, ha a jogkivonatok küldeni egy egyéni HTTP-fejlécben. Az alábbi kód Razor-szintaxist használja a jogkivonatokat hoz létre, és ezt követően hozzáadja a tokenek egy AJAX-kérelem. 
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
Feldolgozni a kérést, ha a kérelem fejlécében bontsa ki a jogkivonatokat. Ezután hívja meg a AntiForgery.Validate metódust a jogkivonatainak érvényesítéséhez szükséges. Az érvényesítés metódus kivételt jelez, ha a jogkivonatok nem érvényes.
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
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Web Forms |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [A webes támadásokkal kivédése ASP.NET beépített funkcióinak kihasználása](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Lépések** | Támadás CSRF-alapú webes alkalmazások enyhíthetők ViewStateUserKey beállítást, amely véletlenszerű karakterlánc minden felhasználó – felhasználói azonosító, vagy még jobb a munkamenet-azonosítót. Számos műszaki és közösségi oka, a munkamenet-azonosító sokkal jobban megfelel azért, mert a munkamenet-azonosító nem határozható meg előre, túllépi az időkorlátot, és a felhasználónkénti alapon változik.|

### <a name="example"></a>Példa
Szüksége lesz az összes weblapot a kód itt látható:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a id="inactivity-lifetime"></a>A munkamenet inaktivitás élettartamának beállítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [HttpSessionState.Timeout tulajdonság](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Lépések** | Munkamenet időkorlátja jelöli az esemény lépett fel, amikor a felhasználó nem bármely művelet elvégzésére egy webhelyen található egy (a webkiszolgáló által meghatározott) időköze alatt kerülne sor. Az esemény, a kiszolgálói oldalon, módosítsa a felhasználói munkamenet állapota "érvénytelen" (például "nem használt többé"), és kérje meg a webkiszolgáló megsemmisítése, (bele tárolt összes adat törlése). Az alábbi példakód a Web.config fájlban az időtúllépési munkamenetet attribútum állítja 15 perc.|

### <a name="example"></a>Példa
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a id="threat-detection"></a>Az Azure SQL fenyegetésészlelés engedélyezése
```

| Title                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web Application | 
| **SDL Phase**               | Build |  
| **Applicable Technologies** | Web Forms |
| **Attributes**              | N/A  |
| **References**              | [forms Element for authentication (ASP.NET Settings Schema)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Steps** | Set the Forms Authentication Ticket cookie timeout to 15 minutes|

### Example
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Web Forms MVC5 |
| **Attribútumok**              | EnvironmentType - rendszert |
| **Hivatkozások**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Lépések** | A webes alkalmazás függő entitás pedig az AD FS az STS-re, amikor élettartama a hitelesítési cookie-k – FedAuth jogkivonatok - adhatók a Web.config fájlban a következő konfigurációt:|

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
Az AD FS kibocsátott SAML is jogcím-jogkivonatának élettartamát beállításaként pedig a 15 perc, az ADFS-kiszolgálón a következő powershell-parancs végrehajtásával:
```csharp
Set-ADFSRelyingPartyTrust -TargetName “<RelyingPartyWebApp>” -ClaimsProviderName @(“Active Directory”) -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a id="proper-app-logout"></a>Az alkalmazás megfelelő kijelentkezési megvalósítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Hajtsa végre megfelelő jelentkezzen ki az alkalmazásból, amikor a felhasználó identitásváltása Kijelentkezés gombra. Jelentkezzen ki, hogy alkalmazás semmisítse meg a felhasználó munkamenetét, és emellett alaphelyzetbe állítása és érvényteleníti a munkamenet cookie-értéket alaphelyzetbe állítását és a hitelesítési cookie-értéket érvénytelenítését együtt. Emellett több munkamenetet egyetlen felhasználói identitást vannak társítva, amikor azok kell együttesen végződniük időtúllépés vagy jelentkezzen ki a kiszolgálói oldalon. Végül győződjön meg arról, hogy kijelentkezési funkció érhető el minden olyan lapon. |

## <a id="csrf-api"></a>Az ASP.NET webes API-k többhelyes kérelem hamisítására (CSRF) támadások ellen

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Webhelyközi kérések hamisításának megakadályozása (CSRF vagy XSRF) olyan támadás, amelyben egy támadó különféle műveleteket végezhet biztonsági környezetében létrehozott munkamenet egy másik felhasználó egy webhelyről. A célja módosítsa vagy törölje a tartalmat, ha a célként kijelölt webhely kizárólag támaszkodik munkamenet cookie-k fogadott kérelmek hitelesítéséhez. A támadó a biztonsági rés betölteni egy parancs egy URL-címet, amelyre a felhasználó már bejelentkezett a sebezhető helyről egy másik felhasználó böngésző lekérésével. Többféleképpen teheti meg, hogy, mint például egy másik webhely, amely egy erőforrás tölt be a sebezhető kiszolgálót üzemeltető, vagy a felhasználó első egy támadó egy hivatkozásra kattintva. A támadás megelőzhető, ha a kiszolgáló egy kiegészítő jogkivonatot elküldi az ügyfélnek, az ügyfél tartalmazza ezt a jogkivonatot az összes jövőbeni kérések igényel, és ellenőrzi, hogy az összes jövőbeni kérések tartalmazzák-e egy jogkivonatot, amely vonatkozik az aktuális munkamenethez, mint például az ASP.NET használatával AntiForgeryToken vagy ViewState. |

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | MVC5, MVC6 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Webhelyközi kérések hamisításának megakadályozása (CSRF) támadások ASP.NET webes API](http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Lépések** | Kártevőirtó-CSRF és AJAX: az űrlap jogkivonat az AJAX-kérelmeket, problémát jelenthetnek, mivel egy AJAX-kérést küldhet JSON-adatokat, nem a HTML-űrlapot adatok. Egy megoldás, ha a jogkivonatok küldeni egy egyéni HTTP-fejlécben. Az alábbi kód Razor-szintaxist használja a jogkivonatokat hoz létre, és ezt követően hozzáadja a tokenek egy AJAX-kérelem. |

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
Feldolgozni a kérést, ha a kérelem fejlécében bontsa ki a jogkivonatokat. Ezután hívja meg a AntiForgery.Validate metódust a jogkivonatainak érvényesítéséhez szükséges. Az érvényesítés metódus kivételt jelez, ha a jogkivonatok nem érvényes.
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
Kártevőirtó-CSRF- és ASP.NET MVC űrlapok - AntiForgeryToken segédmetódus használja a nézetet. például egy Html.AntiForgeryToken() elhelyezi az űrlap
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Példa
A fenti példa kimenete a következőhöz hasonló:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Példa
Egy időben Html.AntiForgeryToken() nevű __RequestVerificationToken fenti rejtett véletlenszerű értéket ugyanarra az értékre, a cookie-k lehetővé teszi a látogató adott területre. Ezután egy bejövő űrlap post ellenőrzéséhez adja hozzá a [ValidateAntiForgeryToken] szűrő a cél tartozó műveleti módszer. Példa:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtr autorizace, amely ellenőrzi, hogy:
* A bejövő kérelem rendelkezik __RequestVerificationToken nevű cookie-k
* A bejövő kérelem egy `Request.Form` __RequestVerificationToken nevű bejegyzés
* Ezek a cookie-k és `Request.Form` hodnoty se shodují, feltéve, hogy minden jól, a kérelem halad végig szokásos módon. De ha nem, majd üzenettel engedélyezési hiba "egy szükséges hamisításgátló jogkivonat nem lett megadva vagy érvénytelen a".

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | MVC5, MVC6 |
| **Attribútumok**              | Identity Provider - ADFS, identitásszolgáltató – Azure ad-ben |
| **Hivatkozások**              | [Az egyes fiókok és a helyi bejelentkezési ASP.NET webes API 2.2-es webes API biztonságossá tétele](http://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Lépések** | Ha a webes API biztosított az OAuth 2.0 használatával, ezután tulajdonosi jogkivonattal vár engedélyezési kérés fejlécében és csak akkor, ha a jogkivonat érvényes, amely engedélyezi a hozzáférést a kérelemre. Cookie-alapú hitelesítés, eltérően böngészők csatolja a tulajdonosi jogkivonatokat kérelmeket. A kérelmező ügyfélnek explicit módon csatolja a tulajdonosi jogkivonat a kérelem fejlécében kell. Ezért az ASP.NET webes API-k az OAuth 2.0 használatával védett, a tulajdonosi jogkivonatokat minősül egy CSRF támadások elleni védelmet. Vegye figyelembe, hogy ha az alkalmazás MVC részének a az űrlapos hitelesítés (azaz a használ cookie-k) használ, hamisításgátló jogkivonatokkal rendelkeznek az MVC webalkalmazás által használható. |

### <a name="example"></a>Példa
A webes API tájékoztatni kell, hogy csak a tulajdonosi jogkivonatokat és a cookie-k nem rendelkezik. A következő konfigurációt a teheti meg `WebApiConfig.Register` módszer:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

A SuppressDefaultHostAuthentication módszer arra utasítja a webes API-t, amely előtt a kérelem eléri a webes API-t folyamatban, az IIS vagy OWIN közbenső szoftver történik hitelesítés figyelmen kívül. Ezzel a módszerrel azt korlátozhatja a webes API-t csak a tulajdonosi jogkivonatok használatával történő hitelesítéshez.
