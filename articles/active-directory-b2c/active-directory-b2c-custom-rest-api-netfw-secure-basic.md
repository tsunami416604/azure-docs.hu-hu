---
title: REST-szolgáltatás biztonságossá tétele HTTP alapszintű hitelesítés használatával
titleSuffix: Azure AD B2C
description: A HTTP alapszintű hitelesítés használatával biztonságossá teheti az egyéni REST API jogcímek cseréjét a Azure AD B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 24aa0d3d3f12934c54ac9aaa5ab8ae5c0d710825
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930508"
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>A REST-szolgáltatások biztonságossá tétele HTTP alapszintű hitelesítés használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Egy [kapcsolódó Azure ad B2C cikkben](active-directory-b2c-custom-rest-api-netfw.md)olyan REST-alapú szolgáltatást (webes API) hoz létre, amely integrálható a Azure Active Directory B2C (Azure ad B2C) felhasználói útvonalak hitelesítés nélkül.

Ebben a cikkben egy egyszerű HTTP-hitelesítést ad hozzá a REST-szolgáltatáshoz, hogy csak az ellenőrzött felhasználók férhessenek hozzá az API-hoz. Az egyszerű HTTP-hitelesítéssel a felhasználói hitelesítő adatokat (az alkalmazás AZONOSÍTÓját és az alkalmazás titkos kulcsát) az egyéni szabályzatban állíthatja be.

További információ: [alapszintű hitelesítés a ASP.net web API-ban](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az [REST API jogcím-cserék integrálása a Azure ad B2C felhasználói útra](active-directory-b2c-custom-rest-api-netfw.md) című cikkben ismertetett lépéseket.

## <a name="step-1-add-authentication-support"></a>1\. lépés: a hitelesítési támogatás hozzáadása

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>1,1. lépés: Alkalmazásbeállítások hozzáadása a projekt web. config fájljához

1. Nyissa meg a korábban létrehozott Visual Studio-projektet.

2. Adja hozzá a következő Alkalmazásbeállítások a web. config fájlhoz a `appSettings` elem alatt:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Hozzon létre egy jelszót, majd állítsa be a `WebApp:ClientSecret` értéket.

    Összetett jelszó létrehozásához futtassa a következő PowerShell-kódot. Tetszőleges értéket is használhat.

    ```powershell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>1,2. lépés: a OWIN-kódtárak telepítése

A kezdéshez adja hozzá a OWIN köztes NuGet-csomagokat a projekthez a Visual Studio Package Manager konzoljának használatával:

```powershell
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>1,3. lépés: a hitelesítési middleware-osztály hozzáadása

Adja hozzá a `ClientAuthMiddleware.cs` osztályt a *App_Start* mappában. Ehhez tegye a következőket:

1. Kattintson a jobb gombbal a *App_Start* mappára, válassza a **Hozzáadás**, majd az **osztály**elemet.

   ![ClientAuthMiddleware.cs osztály hozzáadása a App_Start mappában](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. A **név** mezőbe írja be a következőt: **ClientAuthMiddleware.cs**.

   ![Új C# osztály létrehozása az új elem hozzáadása párbeszédpanelen a Visual Studióban](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Nyissa meg a *App_Start \clientauthmiddleware.cs* fájlt, és cserélje le a fájl tartalmát a következő kódra:

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

### <a name="step-14-add-an-owin-startup-class"></a>1,4. lépés: OWIN indítási osztály hozzáadása

Adjon hozzá egy `Startup.cs` nevű OWIN-indítási osztályt az API-hoz. Ehhez tegye a következőket:
1. Kattintson a jobb gombbal a projektre, válassza a > **új elem** **hozzáadása** lehetőséget, és keresse meg a **OWIN**.

   ![OWIN-indítási osztály létrehozása az új elem hozzáadása párbeszédpanelen a Visual Studióban](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Nyissa meg a *Startup.cs* fájlt, és cserélje le a fájl tartalmát a következő kódra:

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

### <a name="step-15-protect-the-identity-api-class"></a>1,5. lépés: az Identity API osztályának biztosítása

Nyissa meg a Controllers\IdentityController.cs, és adja hozzá a `[Authorize]` címkét a vezérlő osztályhoz. Ez a címke korlátozza a vezérlőhöz való hozzáférést azon felhasználók számára, akik teljesítik az engedélyezési követelményt.

![Az engedélyezés címke hozzáadása a vezérlőhöz](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>2\. lépés: közzététel az Azure-ban

A projekt közzétételéhez a Megoldáskezelő kattintson a jobb gombbal a **contoso. AADB2C. API** projektre, majd válassza a **Közzététel**lehetőséget.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>3\. lépés: a REST-szolgáltatások alkalmazás-AZONOSÍTÓjának és az alkalmazás titkos kulcsának hozzáadása a Azure AD B2C

Miután a REST-alapú szolgáltatást az ügyfél-azonosító (username) és a titkos kulcs védi, a hitelesítő adatokat a Azure AD B2C bérlőben kell tárolnia. Az egyéni szabályzat biztosítja a hitelesítő adatokat, amikor meghívja a REST-szolgáltatásokat.

### <a name="step-31-add-a-restful-services-client-id"></a>3,1. lépés: REST-szolgáltatások ügyfél-AZONOSÍTÓjának hozzáadása

1. A Azure AD B2C-bérlőben válassza a **B2C-beállítások** > **identitási élmény keretrendszere**lehetőséget.


2. Válassza ki a **házirend-kulcsok** elemet a bérlőben elérhető kulcsok megtekintéséhez.

3. Válassza a **Hozzáadás** lehetőséget.

4. A **Beállítások**lapon válassza a **manuális**lehetőséget.

5. A **név**mezőbe írja be a következőt: **B2cRestClientId**.
    Lehet, hogy az előtag *B2C_1A_* automatikusan hozzá lesz adva.

6. A **titok** mezőbe írja be a korábban megadott alkalmazás-azonosítót.

7. A **kulcshasználat**beállításnál válassza az **aláírás**lehetőséget.

8. Kattintson a **Létrehozás** gombra.

9. Győződjön meg arról, hogy létrehozta a `B2C_1A_B2cRestClientId` kulcsot.

### <a name="step-32-add-a-restful-services-client-secret"></a>3,2. lépés: REST-szolgáltatásbeli ügyfél titkos kulcsának hozzáadása

1. A Azure AD B2C-bérlőben válassza a **B2C-beállítások** > **identitási élmény keretrendszere**lehetőséget.

2. Válassza a **házirend-kulcsok** lehetőséget a bérlőben elérhető kulcsok megtekintéséhez.

3. Válassza a **Hozzáadás** lehetőséget.

4. A **Beállítások**lapon válassza a **manuális**lehetőséget.

5. A **név**mezőbe írja be a következőt: **B2cRestClientSecret**.
    Lehet, hogy az előtag *B2C_1A_* automatikusan hozzá lesz adva.

6. A **titok** mezőbe írja be a korábban meghatározott alkalmazás titkos kódját.

7. A **kulcshasználat**beállításnál válassza az **aláírás**lehetőséget.

8. Kattintson a **Létrehozás** gombra.

9. Győződjön meg arról, hogy létrehozta a `B2C_1A_B2cRestClientSecret` kulcsot.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>4\. lépés: a technikai profil módosítása az alapszintű hitelesítés támogatásához a bővítmény házirendjében

1. A munkakönyvtárában nyissa meg a kiterjesztési házirend fájlját (TrustFrameworkExtensions. xml).

2. Keresse meg a `Id="REST-API-SignUp"`t tartalmazó `<TechnicalProfile>` csomópontot.

3. Keresse meg a `<Metadata>` elemet.

4. Módosítsa a *AuthenticationType* az *alapszintű*értékre a következőképpen:

    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Közvetlenül a záró `<Metadata>` elem után adja hozzá a következő XML-kódrészletet:

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```

    A kódrészlet hozzáadása után a technikai profilnak a következő XML-kódhoz hasonlóan kell kinéznie:

    ![Alapszintű hitelesítési XML-elemek hozzáadása a Kivonatjogcím-hez](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>5\. lépés: a szabályzat feltöltése a bérlőbe

1. A [Azure Portal](https://portal.azure.com)válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.

1. A Azure Portal keresse meg és válassza a **Azure ad B2C**lehetőséget.

1. Válassza az **identitási élmény keretrendszert**.

1. Nyissa meg **az összes szabályzatot**.

1. Válassza a **szabályzat feltöltése**lehetőséget.

1. Jelölje be a **házirend felülírása, ha létezik** jelölőnégyzetet.

1. Töltse fel a *TrustFrameworkExtensions. XML* fájlt, és ellenőrizze, hogy átadja-e az érvényesítést.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>6\. lépés: az egyéni házirend tesztelése a Futtatás most használatával

1. Nyissa meg **Azure ad B2C a beállításokat**, majd válassza az **identitási élmény keretrendszert**.

    >[!NOTE]
    >A futtatáshoz szükség van legalább egy alkalmazás előregisztrálására a bérlőn. Az alkalmazások regisztrálásának megismeréséhez tekintse meg a Azure AD B2C első [lépések](active-directory-b2c-get-started.md) című cikket, vagy az [alkalmazás regisztrációját](active-directory-b2c-app-registration.md) ismertető cikket.

2. Nyissa meg **B2C_1A_signup_signin**, a függő entitás (RP) egyéni házirendjét, majd válassza a **Futtatás most**lehetőséget.

3. Tesztelje a folyamatot úgy, hogy beírja a **teszt** kifejezést a **megadott név** mezőbe.
    Azure AD B2C hibaüzenet jelenik meg az ablak tetején.

    ![A megadott név bemeneti ellenőrzésének tesztelése az Identity API-ban](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. A **megadott név** mezőbe írjon be egy nevet (a "test" helyett).
    Azure AD B2C aláírja a felhasználót, majd elküld egy lojalitási számot az alkalmazásnak. Jegyezze fel a példában szereplő számot:

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

## <a name="optional-download-the-complete-policy-files-and-code"></a>Választható A teljes szabályzat fájljainak és kódjának letöltése

* Miután elvégezte az [Egyéni szabályzatok használatának első lépései útmutatót](active-directory-b2c-get-started-custom.md) , javasoljuk, hogy a saját egyéni házirend-fájljaival hozza létre a forgatókönyvet. Az Ön referenciája alapján megadta a [minta házirend-fájlokat](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).
* A Visual Studio-megoldás teljes kódját a [következő hivatkozásra töltheti le: minta](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).

## <a name="next-steps"></a>Következő lépések

* [Ügyféltanúsítványok használata a REST API biztonságossá tételéhez](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
