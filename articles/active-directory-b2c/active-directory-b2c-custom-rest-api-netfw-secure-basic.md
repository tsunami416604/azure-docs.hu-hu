---
title: Biztonságos RESTful-szolgáltatásokat egyszerű HTTP-hitelesítés az Azure Active Directory B2C használatával |} A Microsoft Docs
description: Egyszerű HTTP-hitelesítés használatával gondoskodhat a REST API-val egyéni jogcímek cseréje az Azure AD B2C-ben.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 07865b2120aa91381d3711688e1a5c8e3187fab3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58793379"
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>Biztonságos RESTful-szolgáltatásokat egyszerű HTTP-hitelesítés használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az egy [kapcsolódó Azure AD B2C-cikk](active-directory-b2c-custom-rest-api-netfw.md), létrehozhat egy RESTful szolgáltatás (webes API-t), amely integrálható az Azure Active Directory B2C (az Azure AD B2C) felhasználói utak hitelesítés nélkül.

Ebben a cikkben vegyen fel egyszerű HTTP-hitelesítés a RESTful szolgáltatás úgy, hogy csak ellenőrzött felhasználók, beleértve a B2C-vel, hozzáférhet az API-t. Az alapszintű HTTP-hitelesítést állítsa a felhasználói hitelesítő adatokat (Alkalmazásazonosító és titkos Alkalmazáskulcs) az egyéni házirend.

További információkért lásd: [az ASP.NET webes API-k egyszerű hitelesítés](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Előfeltételek

A lépések elvégzéséhez a [integrálása a REST API-t az Azure AD B2C felhasználói interakciósorozatban szereplő cseréje jogcímek](active-directory-b2c-custom-rest-api-netfw.md) cikk.

## <a name="step-1-add-authentication-support"></a>1. lépés: Hitelesítési támogatás hozzáadása

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>1.1. lépés: Alkalmazásbeállítások hozzáadása a projekthez web.config fájlban

1. Nyissa meg a Visual Studio-projektet, amelyet korábban hozott létre.

2. Adja hozzá a következő alkalmazás beállításokat a web.config fájl mellett a `appSettings` elem:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Hozzon létre egy jelszót, és állítsa a `WebApp:ClientSecret` értéket.

    Hozzon létre egy összetett jelszót, futtassa a következő PowerShell-kóddal. Használhat bármilyen tetszőleges érték.

    ```powershell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>1.2. lépés: OWIN-kódtárak telepítése

Első lépésként adja hozzá az OWIN közbenső NuGet-csomagok a projekt a Visual Studio Csomagkezelői konzol használatával:

```powershell
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>1.3. lépés: Egy közbenső hitelesítési osztály hozzáadása

Adja hozzá a `ClientAuthMiddleware.cs` osztály alatt a *App_Start* mappát. Ehhez tegye a következőket:

1. Kattintson a jobb gombbal a *App_Start* mappáját, válassza ki **Hozzáadás**, majd válassza ki **osztály**.

   ![A App_Start mappában ClientAuthMiddleware.cs osztály hozzáadása](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. Az a **neve** mezőbe írja be **ClientAuthMiddleware.cs**.

   ![Új C# osztály létrehozása](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Nyissa meg a *App_Start\ClientAuthMiddleware.cs* fájlt, és cserélje le a tartalmat a következő kódot a fájl:

    ```csharp
    
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

### <a name="step-14-add-an-owin-startup-class"></a>1.4 lépést: OWIN indítási osztály hozzáadása

Adjon hozzá egy OWIN indítási osztályt `Startup.cs` az API-hoz. Ehhez tegye a következőket:
1. Kattintson a jobb gombbal a projektre, válassza ki **Hozzáadás** > **új elem**, és keressen **OWIN**.

   ![OWIN indítási osztály hozzáadása](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Nyissa meg a *Startup.cs* fájlt, és cserélje le a tartalmat a következő kódot a fájl:

    ```csharp
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

Nyissa meg a Controllers\IdentityController.cs, és adja hozzá a `[Authorize]` címke a vezérlő osztályhoz. Ez a címke a tartományvezérlőt a felhasználók, akik megfelelnek a engedélyezési követelmény korlátozza a hozzáférést.

![A vezérlő az engedélyezés címke hozzáadása](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>2. lépés: Közzététel az Azure platformon

A projekt közzététele a Megoldáskezelőben, kattintson a jobb gombbal a **Contoso.AADB2C.API** projektre, és válassza ki **közzététel**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>3. lépés: Adja hozzá a RESTful-szolgáltatásokat alkalmazás Azonosítóját és az alkalmazás titkos kulcsát az Azure AD B2C-vel

Miután az ügyfél-azonosító (felhasználónév) és a titkos kulcs védelme a RESTful szolgáltatás, az Azure AD B2C-bérlőben a hitelesítő adatokat kell tárolnia. Az egyéni házirend hitelesítő adatokat biztosít, ha meghívja a REST-alapú szolgáltatások.

### <a name="step-31-add-a-restful-services-client-id"></a>3.1. lépés: Adjon hozzá egy RESTful szolgáltatás ügyfél-azonosító

1. Válassza ki az Azure AD B2C-bérlőben **B2C-beállítások** > **identitás-kezelőfelületi keretrendszer**.


2. Válassza ki **Szabályzatbejegyzések** a elérhető a bérlői kulcsok megtekintéséhez.

3. Válassza a **Hozzáadás** lehetőséget.

4. A **beállítások**válassza **manuális**.

5. A **neve**, típus **B2cRestClientId**.  
    Az előtag *B2C_1A_* automatikusan hozzáadhatók.

6. Az a **titkos** mezőbe írja be a korábban megadott Alkalmazásazonosító.

7. A **kulcshasználat**válassza **aláírás**.

8. Kattintson a **Létrehozás** gombra.

9. Győződjön meg arról, hogy létrehozta a `B2C_1A_B2cRestClientId` kulcsot.

### <a name="step-32-add-a-restful-services-client-secret"></a>3.2. lépés: REST-alapú szolgáltatások ügyfél titkos kód hozzáadása

1. Válassza ki az Azure AD B2C-bérlőben **B2C-beállítások** > **identitás-kezelőfelületi keretrendszer**.

2. Válassza ki **Szabályzatbejegyzések** elérhető a bérlői kulcsok megtekintéséhez.

3. Válassza a **Hozzáadás** lehetőséget.

4. A **beállítások**válassza **manuális**.

5. A **neve**, típus **B2cRestClientSecret**.  
    Az előtag *B2C_1A_* automatikusan hozzáadhatók.

6. Az a **titkos** mezőbe írja be a korábban megadott alkalmazás titkos kulcsát.

7. A **kulcshasználat**válassza **aláírás**.

8. Kattintson a **Létrehozás** gombra.

9. Győződjön meg arról, hogy létrehozta a `B2C_1A_B2cRestClientSecret` kulcsot.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>4. lépés: Alapszintű hitelesítés támogatásához a bővítmény a házirend a technikai profil módosítása

1. A munkakönyvtárban nyissa meg a bővítmény a házirend-fájl (TrustFrameworkExtensions.xml).

2. Keresse meg a `<TechnicalProfile>` tartalmazó csomópont `Id="REST-API-SignUp"`.

3. Keresse meg a `<Metadata>` elemet.

4. Módosítsa a *AuthenticationType* való *alapszintű*, az alábbiak szerint:

    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. A záró után azonnal `<Metadata>` elemben adja hozzá a következő XML-részletet:

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```

    Miután hozzáadta a kódrészletet, a technikai profil a következő XML-kódhoz hasonlóan kell kinéznie:
    
    ![Alapszintű hitelesítés XML-elemek hozzáadása](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>5. lépés: A szabályzat feltöltése a bérlőhöz

1. Az a [az Azure portal](https://portal.azure.com), váltson át a [az Azure AD B2C-bérlője kontextusában](active-directory-b2c-navigate-to-b2c-context.md), majd nyissa meg **Azure AD B2C-vel**.

2. Válassza ki **identitás-kezelőfelületi keretrendszer**.

3. Nyissa meg **összes szabályzat**.

4. Válassza ki **szabályzat feltöltése**.

5. Válassza ki a **szabályzat felülírása, ha létezik** jelölőnégyzetet.

6. Töltse fel a *TrustFrameworkExtensions.xml* fájlt, és ezután győződjön meg arról, hogy érvényesítési továbbítja.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>6. lépés: Az egyéni házirend tesztelése a Futtatás most

1. Nyissa meg **Azure AD B2C-beállítások**, majd válassza ki **identitás-kezelőfelületi keretrendszer**.

    >[!NOTE]
    >Futtatás most kell előzetesen regisztrálva, a bérlő legalább egy alkalmazás szükséges. Megtudhatja, hogyan regisztrálja az alkalmazást, tekintse meg az Azure AD B2C [Ismerkedés](active-directory-b2c-get-started.md) cikk vagy a [alkalmazásregisztráció](active-directory-b2c-app-registration.md) cikk.

2. Nyissa meg **B2C_1A_signup_signin**, a függő entitásonkénti (RP) egyéni-szabályzattal, feltöltött, és válassza ki **Futtatás most**.

3. Írja be a folyamat teszteléséhez **teszt** a a **Utónév** mezőbe.  
    Az Azure AD B2C hibaüzenetet jelenít meg az ablak tetején.

    ![Az identitás API tesztelése](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. Az a **Utónév** mezőbe írjon be egy nevet (nem a "Test").  
    Az Azure AD B2C a felhasználó regisztrál, és ezután elküldi a hűségprogramok használatán keresztül számnak az alkalmazáshoz. Megjegyzés: Ebben a példában a számot:

    ```
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
    }.{
      "exp": 1507125903,
      "nbf": 1507122303,
      "ver": "1.0",
      "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Nem kötelező) A teljes házirend fájlok és a kód letöltése

* Miután elvégezte a [egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md) forgatókönyv, azt javasoljuk, hogy a forgatókönyv a saját egyéni házirend-fájlok használatával hozhat létre. Referenciaként adtunk meg [házirendfájljait minta](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).
* Letöltheti a teljes kódját [mintát a Visual Studio-megoldás referenciaként](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).

## <a name="next-steps"></a>További lépések

* [Ügyféltanúsítványokkal szeretné a RESTful API biztonságossá tétele](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)