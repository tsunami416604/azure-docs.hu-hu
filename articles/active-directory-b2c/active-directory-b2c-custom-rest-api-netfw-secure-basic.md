---
title: "Az Azure Active Directory B2C: A RESTful-szolgáltatásokat biztonságos alapvető HTTP-hitelesítés használatával"
description: "Az egyéni REST API-jogcímek cseréjét az Azure AD B2C biztonságos alapvető HTTP-hitelesítés használatával"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: d65d94bb5c807abfd6cbb1fae786a02f179e93d6
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>A RESTful-szolgáltatásokat biztonságos alapvető HTTP-hitelesítés használatával
Az egy [kapcsolódó az Azure AD B2C-cikk](active-directory-b2c-custom-rest-api-netfw.md), létrehozhat egy RESTful szolgáltatás (webes API-k), amely az Azure Active Directory B2C (az Azure AD B2C) felhasználói utak hitelesítés nélkül. 

Ebben a cikkben hozzáadhat egyszerű HTTP-hitelesítés a RESTful szolgáltatás érdekében, hogy a csak ellenőrzése és a felhasználók B2C, beleértve az API-t. HTTP egyszerű hitelesítéssel a felhasználói hitelesítő adatokat (Alkalmazásazonosító és alkalmazás titkos kulcs) az egyéni házirendek beállítása. 

További információkért lásd: [egyszerű hitelesítés az ASP.NET web API](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Előfeltételek
Hajtsa végre a a [integrálja a REST API-t jogcímek az az Azure AD B2C felhasználói út cseréjét](active-directory-b2c-custom-rest-api-netfw.md) cikk.

## <a name="step-1-add-authentication-support"></a>1. lépés:, Vegye fel a hitelesítéshez

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>1.1. lépés: Az alkalmazásbeállítások hozzáadása a projekthez web.config fájl
1. Nyissa meg a korábban létrehozott Visual Studio-projektet. 

2. A következő Alkalmazásbeállítások hozzáadása a web.config fájlt a `appSettings` elem:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Hozzon létre egy jelszót, és utána állítsa be a `WebApp:ClientSecret` érték.

    Összetett jelszót létrehozni, futtassa a következő PowerShell-kódot. Használhat bármilyen tetszőleges érték.

    ```PowerShell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>1.2. lépés: Telepítése OWIN függvénytárak
Első lépésként hozzá az OWIN köztes NuGet-csomagok a projekthez a Visual Studio Csomagkezelő konzol használatával:

```
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>1.3. lépés: Egy hitelesítési köztes osztály hozzáadása
Adja hozzá a `ClientAuthMiddleware.cs` az osztály a *App_Start* mappa. Ehhez tegye a következőket:

1. Kattintson a jobb gombbal a *App_Start* mappára, válassza **Hozzáadás**, majd válassza ki **osztály**.

   ![A App_Start mappában ClientAuthMiddleware.cs osztály hozzáadása](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. Az a **neve** mezőbe írja be **ClientAuthMiddleware.cs**.

   ![Hozzon létre új osztályt C#](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Nyissa meg a *App_Start\ClientAuthMiddleware.cs* fájlt, és cserélje le a fájlt a tartalom a következő kóddal:

    ```C#
    
    using Microsoft.Owin;
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using System.Security.Principal;
    using System.Text;
    using System.Threading.Tasks;
    using System.Web;
    
    namespace Contoso.AADB2C.API
    {
        /// <summary>
        /// Class to create a custom owin middleware to check for client authentication
        /// </summary>
        public class ClientAuthMiddleware
        {
            private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
            private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];
    
            /// <summary>
            /// Gets or sets the next owin middleware
            /// </summary>
            private Func<IDictionary<string, object>, Task> Next { get; set; }
    
            /// <summary>
            /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
            /// </summary>
            /// <param name="next"></param>
            public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
            {
                this.Next = next;
            }
    
            /// <summary>
            /// Invoke client authentication middleware during each request.
            /// </summary>
            /// <param name="environment">Owin environment</param>
            /// <returns></returns>
            public Task Invoke(IDictionary<string, object> environment)
            {
                // Get wrapper class for the environment
                var context = new OwinContext(environment);
    
                // Check whether the authorization header is available. This contains the credentials.
                var authzValue = context.Request.Headers.Get("Authorization");
                if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
                {
                    // Process next middleware
                    return Next(environment);
                }
    
                // Get credentials
                var creds = authzValue.Substring("Basic ".Length).Trim();
                string clientId;
                string clientSecret;
    
                if (RetrieveCreds(creds, out clientId, out clientSecret))
                {
                    // Set transaction authenticated as client
                    context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
                }
    
                return Next(environment);
            }
    
            /// <summary>
            /// Retrieve credentials from header
            /// </summary>
            /// <param name="credentials">Authorization header</param>
            /// <param name="clientId">Client identifier</param>
            /// <param name="clientSecret">Client secret</param>
            /// <returns>True if valid credentials were presented</returns>
            private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
            {
                string pair;
                clientId = clientSecret = string.Empty;
    
                try
                {
                    pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
                }
                catch (FormatException)
                {
                    return false;
                }
                catch (ArgumentException)
                {
                    return false;
                }
    
                var ix = pair.IndexOf(':');
                if (ix == -1)
                {
                    return false;
                }
    
                clientId = pair.Substring(0, ix);
                clientSecret = pair.Substring(ix + 1);
    
                // Return whether credentials are valid
                return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                    string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
            }
        }
    }
    ```

### <a name="step-14-add-an-owin-startup-class"></a>1.4. lépés: Az OWIN indítási osztály hozzáadása
Adja hozzá egy OWIN indítási osztályt `Startup.cs` az API-hoz. Ehhez tegye a következőket:
1. Kattintson jobb gombbal a projektre, válassza ki **Hozzáadás** > **új elem**, majd keresse meg a **OWIN**.

   ![OWIN indítási osztály hozzáadása](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Nyissa meg a *Startup.cs* fájlt, és cserélje le a fájlt a tartalom a következő kóddal:

    ```C#
    using Microsoft.Owin;
    using Owin;
    
    [assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
    namespace Contoso.AADB2C.API
    {
        public class Startup
        {
            public void Configuration(IAppBuilder app)
            {
                    app.Use<ClientAuthMiddleware>();
            }
        }
    }
    ```

### <a name="step-15-protect-the-identity-api-class"></a>1.5-ös. lépés: Az identitás API osztály védelme
Nyissa meg a Controllers\IdentityController.cs, és adja hozzá a `[Authorize]` címkén belül, hogy a vezérlő osztályhoz. Ezt a címkét a tartományvezérlőt a felhasználók, akik megfelelnek a engedélyezési követelmény korlátozza a hozzáférést.

![A vezérlő az engedélyezés címke hozzáadása](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>2. lépés: Az Azure-bA közzététele
A projekt közzététele a Megoldáskezelőben, kattintson a jobb gombbal a **Contoso.AADB2C.API** projektre, majd válassza ki **közzététel**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>3. lépés: A RESTful-szolgáltatásokat app ID és az alkalmazás titkos kulcs hozzáadása az Azure AD B2C-vel
A RESTful szolgáltatás által az ügyfél-azonosító (felhasználónév) és a titkos kulcs védett, miután az Azure AD B2C-bérlő a hitelesítő adatokat kell tárolni. Az egyéni házirend biztosítja a hitelesítő adatait, ha meghívja a RESTful-szolgáltatásokat. 

### <a name="step-31-add-a-restful-services-client-id"></a>3.1. lépés:, Vegye fel a RESTful-szolgáltatásokat ügyfél-azonosító
1. Válassza ki az Azure AD B2C-bérlő **B2C beállítások** > **identitás élmény keretrendszer**.


2. Válassza ki **házirend kulcsok** érhetők el a bérlői kulcsok megtekintéséhez.

3. Válassza a **Hozzáadás** lehetőséget.

4. A **beállítások**, jelölje be **manuális**.

5. A **neve**, típus **B2cRestClientId**.  
    Az előtag *B2C_1A_* előfordulhat, hogy automatikusan hozzáadja.

6. Az a **titkos** mezőbe írja be az alkalmazás azonosítója, amelyet korábban megadott.

7. A **kulcshasználat**, jelölje be **titkos**.

8. Kattintson a **Létrehozás** gombra.

9. Győződjön meg arról, hogy létrehozta a `B2C_1A_B2cRestClientId` kulcs.

### <a name="step-32-add-a-restful-services-client-secret"></a>3.2. lépés: A RESTful-szolgáltatásokat ügyfélkulcs hozzáadása
1. Válassza ki az Azure AD B2C-bérlő **B2C beállítások** > **identitás élmény keretrendszer**.

2. Válassza ki **házirend kulcsok** érhető el a bérlői kulcsok megtekintéséhez.

3. Válassza a **Hozzáadás** lehetőséget.

4. A **beállítások**, jelölje be **manuális**.

5. A **neve**, típus **B2cRestClientSecret**.  
    Az előtag *B2C_1A_* előfordulhat, hogy automatikusan hozzáadja.

6. Az a **titkos** mezőbe írja be a korábban meghatározott app titkos kulcsot.

7. A **kulcshasználat**, jelölje be **titkos**.

8. Kattintson a **Létrehozás** gombra.

9. Győződjön meg arról, hogy létrehozta a `B2C_1A_B2cRestClientSecret` kulcs.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>4. lépés: A műszaki profil az egyszerű hitelesítés támogatásához a bővítmény házirend módosítása
1. A munkakönyvtár nyissa meg a házirend bővítményfájl (TrustFrameworkExtensions.xml).

2. Keresse meg a `<TechnicalProfile>` tartalmazó csomópont `Id="REST-API-SignUp"`.

3. Keresse meg a `<Metadata>` elemet.

4. Módosítsa a *AuthenticationType* való *alapvető*, az alábbiak szerint:
    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. A záró után azonnal `<Metadata>` elemet, adja hozzá a következő XML-részletet: 

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```
    Miután hozzáadta a kódrészletet, a műszaki profil a következő XML-kódot kell hasonlítania:
    
    ![Az egyszerű hitelesítés XML-elemek hozzáadása](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>5. lépés: Töltse fel a házirend a bérlő

1. A a [Azure-portálon](https://portal.azure.com), váltson a [az Azure AD B2C-bérlő kontextusában](active-directory-b2c-navigate-to-b2c-context.md), majd nyissa meg **az Azure AD B2C**.

2. Válassza ki **identitás élmény keretrendszer**.

3. Nyissa meg **házirend**.

4. Válassza ki **házirend feltöltése**.

5. Válassza ki a **felülírja a házirendet, ha létezik** jelölőnégyzetet.

6. Töltse fel a *TrustFrameworkExtensions.xml* fájlt, és ezután győződjön meg arról, hogy teljesíti-e ellenőrző.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>6. lépés: Az egyéni házirend tesztelése Futtatás most használatával
1. Nyissa meg **az Azure AD B2C beállítások**, majd válassza ki **identitás élmény keretrendszer**.

    >[!NOTE]
    >Futtatás most a tenant preregistered kell legalább egy alkalmazás szükséges. Megtudhatja, hogyan kell regisztrálni az alkalmazások, tekintse meg az Azure AD B2C [Ismerkedés](active-directory-b2c-get-started.md) cikk vagy a [regisztrációja](active-directory-b2c-app-registration.md) cikk.

2. Nyissa meg **B2C_1A_signup_signin**, a függő entitásonkénti (RP) egyéni házirend feltöltött, és válassza **futtatása most**.

3. A folyamat ellenőrzéséhez írja be a **teszt** a a **Utónév** mezőbe.  
    Az Azure AD B2C hibaüzenet jelenik meg a az ablak tetején.

    ![A azonossága API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. Az a **Utónév** mezőbe írjon be egy nevet (nem a "Test").  
    Az Azure AD B2C a felhasználó regisztrál, és ezután elküldi az alkalmazás hűség számnak. Ebben a példában szereplő számot. Megjegyzés:

    ```
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
    }.{
      "exp": 1507125903,
      "nbf": 1507122303,
      "ver": "1.0",
      "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
      "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
      "acr": "b2c_1a_signup_signin",
      "nonce": "defaultNonce",
      "iat": 1507122303,
      "auth_time": 1507122303,
      "loyaltyNumber": "290",
      "given_name": "Emily",
      "emails": ["B2cdemo@outlook.com"]
    }
    ```

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Választható) A teljes házirend fájlok és a kód letöltése
* Miután elvégezte a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md) forgatókönyv, azt javasoljuk, hogy a saját egyéni házirend-fájlok használatával történő létrehozása adott esetben. Referenciaként a adtunk [házirendfájljait minta](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).
* Letöltheti a teljes kód látható a [minta Visual Studio megoldás referenciaként](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).

## <a name="next-steps"></a>Következő lépések
* [Ügyféltanúsítványokat használ a RESTful API biztonságossá tétele](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

