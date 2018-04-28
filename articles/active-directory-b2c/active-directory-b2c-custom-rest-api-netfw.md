---
title: 'Az Azure Active Directory B2C: Integrálása az Azure AD B2C felhasználói út a REST API jogcím cseréje, felhasználói bevitel ellenőrzése'
description: REST API jogcím cseréje a Azure AD B2C felhasználói használatában az integrálását, felhasználói bevitel ellenőrzése.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 09/30/2017
ms.author: davidmu
ms.openlocfilehash: c4a530b3d6b4f6d4f5ec6087adcee839422dd01d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Az Azure AD B2C felhasználói út a REST API jogcímek cseréjét integrálása, felhasználói bevitel ellenőrzése

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A identitás élmény keretrendszerrel, amelynek alapjául szolgáló Azure Active Directory B2C (az Azure AD B2C), integrálható egy RESTful API-nak felhasználói út. Ez a forgatókönyv megtudhatja, hogyan kommunikál az Azure AD B2C .NET-keretrendszer RESTful szolgáltatás (webes API-k).

## <a name="introduction"></a>Bevezetés
Az Azure AD B2C segítségével adhat hozzá a saját üzleti logika felhasználói út saját RESTful szolgáltatás meghívásával. Az identitás élmény keretrendszer adatokat küld a RESTful szolgáltatás egy *bemeneti jogcímek* gyűjtemény és kap vissza adatokat a RESTful egy *kimeneti jogcímek* gyűjtemény. Az integráció RESTful szolgáltatás a következőket teheti:

* **Felhasználói bemeneti adatok érvényesítése**: Ez a művelet nem megfelelően formázott megakadályozza az Azure AD-be megőrzése. Ha a felhasználó az érték nem érvényes, a RESTful szolgáltatás olyan hibaüzenetet, amely arra utasítja a felhasználónak meg kell adnia egy bejegyzést ad vissza. Ellenőrizheti például, hogy az a felhasználó által megadott e-mail cím létezik a felhasználói adatbázisban.
* **Felülírja a bemeneti jogcímek**: például, ha a felhasználó utónevét adja az összes kis-és kisbetűket, a név a formázhatók csak az első betűje nagybetű.
* **Felhasználói adatok kiegészítése és vállalati-üzleti alkalmazások további integrálásával**: A RESTful szolgáltatás is kap a felhasználó e-mail címét, a felhasználói adatbázis lekérdezése, és térjen vissza a felhasználói hűség számát az Azure AD B2C. A visszatérési jogcímek a felhasználó Azure AD-fiókot, a következő kiértékelése tárolhatja *Vezénylési lépésekből*, vagy a hozzáférési jogkivonat szerepel.
* **Futtassa az üzleti logika**: is leküldéses értesítések küldéséhez, vállalati adatbázisok frissítése, futtatja a felhasználói áttelepítési folyamat, kezeli az engedélyeket, naplózási adatbázisokat, és egyéb műveleteket hajthat végre.

Az integráció a RESTful-szolgáltatásokat a kialakítani a következőképpen:

* **Ellenőrzési műszaki profil**: a megadott műszaki profil az érvényesítési műszaki profilon belül történik a RESTful szolgáltatás hívása. Az ellenőrzési műszaki profil ellenőrzi a felhasználó által megadott előtt a felhasználó út továbblép. Az érvényesítési műszaki profillal a következőket teheti:
   * A bemeneti jogcímek küldése.
   * Ellenőrizze a bemeneti jogcímek, és az egyéni hibaüzenetek helyi kivételt.
   * Vissza a kimeneti jogcímek küldése.

* **Exchange-jogcímek**: Ez a kialakítás hasonló műszaki érvényességi profilt, de az orchestration lépés belül történik. Ez a definíció korlátozva:
   * A bemeneti jogcímek küldése.
   * Vissza a kimeneti jogcímek küldése.

## <a name="restful-walkthrough"></a>RESTful forgatókönyv
Ebben a bemutatóban a .NET-keretrendszer webes API-t, amely érvényesíti a felhasználó által megadott, és számos olyan felhasználói hűség fejlesztéséhez. Például az alkalmazás hozzáférési jogosultságot tud biztosítani *platinum előnyöket* hűség száma alapján.

Áttekintés:
* A RESTful szolgáltatás (.NET-keretrendszer webes API-k) fejlesztéséhez.
* A RESTful szolgáltatás használata a felhasználói út.
* A bemeneti jogcímek küldése és olvashatja őket a kódban.
* Ellenőrizze a felhasználó utónevét.
* Küldött hűség számnak. 
* Adja hozzá a hűség számát a egy JSON webes jogkivonat (JWT).

## <a name="prerequisites"></a>Előfeltételek
Hajtsa végre a a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md) cikk.

## <a name="step-1-create-an-aspnet-web-api"></a>1. lépés: Egy ASP.NET web API létrehozása

1. A Visual Studio-projekt létrehozása kiválasztásával **fájl** > **új** > **projekt**.

2. Az a **új projekt** ablakban válassza ki **Visual C#** > **webes** > **ASP.NET Web Application (.NET-keretrendszer)**.

3. Az a **neve** mezőbe írja be az alkalmazás nevét (például *Contoso.AADB2C.API*), majd válassza ki **OK**.

    ![Új visual studio-projekt létrehozása](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4. Az a **új ASP.NET-webalkalmazás** ablakban válassza ki a **Web API** vagy **Azure API app** sablont.

    ![Webes API-sablon kiválasztása](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

5. Győződjön meg arról, hogy a hitelesítési értéke **nem hitelesítési**.

6. Válassza ki **OK** a projekt létrehozásához.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>2. lépés: Felkészülés a REST API-végpont

### <a name="step-21-add-data-models"></a>2.1. lépés: Az adatmodell hozzáadása
A minta a bemeneti jogcímek, és a kimeneti jogcímek a RESTful szolgáltatás adatokat. A kód beolvassa a bemeneti adatok deszerializálása során a bemeneti jogcímek modell JSON karakterláncnak egy C#-objektum (típus) által. Az ASP.NET web API automatikusan deserializes a kimeneti jogcímek modell vissza a JSON és a szerializált adatok majd ír a HTTP-válasz üzenet törzsét. 

Hozzon létre egy modell a bemeneti jogcímek a következő módon:

1. Ha még nincs megnyitva Megoldáskezelőben, válassza ki **nézet** > **Megoldáskezelőben**. 
2. A Megoldáskezelőben kattintson a jobb gombbal a **modellek** mappára, válassza **Hozzáadás**, majd válassza ki **osztály**.

    ![Modell hozzáadása](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

3. Az osztály neve `InputClaimsModel`, majd vegye fel a következő tulajdonságokat és a `InputClaimsModel` osztály:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

4. Hozzon létre egy új modell `OutputClaimsModel`, majd vegye fel a következő tulajdonságokat és a `OutputClaimsModel` osztály:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

5. Hozzon létre egy további modell `B2CResponseContent`, melynek segítségével throw bemenet-ellenőrzési hibaüzenetek. Vegye fel a következő tulajdonságokat a `B2CResponseContent` osztály, adja meg a hiányzó hivatkozások, és mentse a fájlt:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }    
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>2.2. lépés: A vezérlő hozzáadása
A webes API-t egy _vezérlő_ olyan objektum, amely HTTP-kérelmeket kezeli. A vezérlő adja vissza a kimeneti jogcímek, vagy a Keresztnév megadása nem érvényes, ha egy ütköző HTTP-hibaüzenetet jelez.

1. A Megoldáskezelőben kattintson a jobb gombbal a **tartományvezérlők** mappára, válassza **Hozzáadás**, majd válassza ki **vezérlő**.

    ![Új tartományvezérlő](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2. Az a **hozzáadása Scaffold** ablakban válassza ki **webes API-vezérlő - üres**, majd válassza ki **Hozzáadás**.

    ![Válassza ki a Web API 2 vezérlő - üres](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3. Az a **vezérlő hozzáadása** ablakban, a tartományvezérlő nevét **IdentityController**, majd válassza ki **Hozzáadás**.

    ![Írja be a tartományvezérlő neve](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    A állványok létrehoz egy nevű fájlt *IdentityController.cs* a a *tartományvezérlők* mappát.

4. Ha a *IdentityController.cs* fájl még nincs nyitva, kattintson rá duplán, és akkor cserélje le a fájlban lévő kódot az alábbira:

    ```csharp
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If no data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check the input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into an InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run an input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create an output claims object and set the loyalty number with a random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-the-project-to-azure"></a>3. lépés: A projekt közzététele az Azure-bA
1. A Megoldáskezelőben kattintson a jobb gombbal a **Contoso.AADB2C.API** projektre, majd válassza ki **közzététel**.

    ![A Microsoft Azure App Service közzététele](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2. Az a **közzététel** ablakban válassza ki **Microsoft Azure App Service**, majd válassza ki **közzététel**.

    ![Hozzon létre új Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    A **létrehozása az App Service** ablak nyílik meg. Az oktatóanyagban hoz létre minden a szükséges Azure-erőforrások futtatni az ASP.NET webalkalmazás az Azure-ban.

    > [!NOTE]
    >Közzététele kapcsolatos további információkért lásd: [egy ASP.NET-webalkalmazás létrehozása az Azure-ban](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. Az a **webalkalmazásnév** mezőbe írja be egy egyedi alkalmazásnévvel (érvényes karakterek: a – z, 0-9 és kötőjelet (-). A webalkalmazás URL-címe http://<app_name>.azurewebsites.NET, ahol *Alkalmazás_neve* a webes alkalmazás neve. Elfogadhatja az automatikusan létrehozott nevet is, amely már egyedi.

    ![Adja meg az App Service tulajdonságokat](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4. Azure-erőforrások létrehozása elindításához válassza ki a **létrehozása**.  
    Az ASP.NET-webalkalmazás létrehozása után a varázsló közzéteszi azokat az Azure-ba, és ezután elindítja az alkalmazás az alapértelmezett böngésző.

6. Másolja a webes alkalmazás URL-címet.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>4. lépés: Adja hozzá az új `loyaltyNumber` állítja, hogy a séma a TrustFrameworkExtensions.xml fájl
A `loyaltyNumber` jogcím még nincs definiálva a sémában. Adjon meg definíciót belül a `<BuildingBlocks>` elem, amely elején található a *TrustFrameworkExtensions.xml* fájlt.

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-add-a-claims-provider"></a>5. lépés: A jogcím-szolgáltató hozzáadása 
Minden jogcím-szolgáltató egy vagy több műszaki profilok, amelyek megadják a végpontok és a jogcímszolgáltató folytatott kommunikációhoz szükséges protokollok kell rendelkeznie. 

A jogcím-szolgáltató különböző okokból rendelkezhet több műszaki profil. Például több műszaki profil előfordulhat, hogy lehet megadni, mert a jogcím-szolgáltató több protokollt is támogat, végpontok is rendelkezik különböző képességeket vagy kiadásokban megbízhatósági szintek számos jogcímeket is tartalmazhat. Bizalmas jogcím egy felhasználó út, de nem egy másik kibocsátási elfogadható lehet. 

A következő XML-részletet két műszaki profil a jogcímeket szolgáltató csomópontokat tartalmazza:

* **TechnicalProfile Id = "REST-API-SignUp"**: határozza meg a RESTful szolgáltatás. 
   * `Proprietary` leírt protokollt a RESTful-alapú szolgáltató. 
   * `InputClaims` határozza meg a jogcímeket, amely a REST-szolgáltatást az Azure AD B2C kapnak. 

   Ebben a példában a tartalom a jogcím `givenName` küld a többi szolgáltatás `firstName`, a tartalom a jogcím `surname` küld a többi szolgáltatás `lastName`, és `email` esetben van. A `OutputClaims` elem definiálja a jogcímeket a rendszer beolvassa az RESTful szolgáltatás vissza az Azure AD B2C.

* **TechnicalProfile Id = "LocalAccountSignUpWithLogonEmail"**: technikai érvényesítési profil hozzáadása egy meglévő műszaki profilt (alap a házirendben meghatározott). Az előfizetési út során az érvényesítési műszaki profil hív meg az előző műszaki profil. Ha a RESTful szolgáltatás hibát jelez a HTTP 409 (ütközés hiba), a hibaüzenet jelenik meg a felhasználó számára. 

Keresse meg a `<ClaimsProviders>` csomópontot, majd adja hozzá a következő XML-részletet alatt a `<ClaimsProviders>` csomópont:

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
        <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
        </InputClaims>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

<!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>6. lépés: Adja hozzá a `loyaltyNumber` állítja, hogy a függő entitás házirendfájlt, a jogcím kap az alkalmazáshoz
Szerkessze a *SignUpOrSignIn.xml* függő entitásonkénti (RP) fájlt, és módosítsa a TechnicalProfile Id = "PolicyProfile" elemet adja hozzá a következő: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Miután hozzáadta az új jogcím, a függő entitás kód néz ki:

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>7. lépés: Töltse fel a házirend a bérlő

1. A a [Azure-portálon](https://portal.azure.com), váltson a [az Azure AD B2C-bérlő kontextusában](active-directory-b2c-navigate-to-b2c-context.md), majd nyissa meg **az Azure AD B2C**.

2. Válassza ki **identitás élmény keretrendszer**.

3. Nyissa meg **házirend**. 

4. Válassza ki **házirend feltöltése**.

5. Válassza ki a **felülírja a házirendet, ha létezik** jelölőnégyzetet.

6. Töltse fel a TrustFrameworkExtensions.xml fájlt, és győződjön meg arról, hogy ellenőrzése sikeres.

7. Ismételje meg az előző lépést az SignUpOrSignIn.xml fájllal.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>8. lépés: Az egyéni házirend tesztelése Futtatás most használatával
1. Válassza ki **az Azure AD B2C beállítások**, majd lépjen **identitás élmény keretrendszer**.

    > [!NOTE]
    > **Futtassa most** legalább egy alkalmazás a tenant preregistered lehet szükséges. Megtudhatja, hogyan kell regisztrálni az alkalmazások, tekintse meg az Azure AD B2C [Ismerkedés](active-directory-b2c-get-started.md) cikk vagy a [regisztrációja](active-directory-b2c-app-registration.md) cikk.

2. Nyissa meg **B2C_1A_signup_signin**, a függő entitásonkénti (RP) egyéni házirend feltöltött, és válassza **futtatása most**.

    ![A B2C_1A_signup_signin ablak](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. A folyamat ellenőrzéséhez írja be a **teszt** a a **Utónév** mezőbe.  
    Az Azure AD B2C hibaüzenet jelenik meg a az ablak tetején.

    ![A házirend tesztelése](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4.  Az a **Utónév** mezőbe írjon be egy nevet (nem a "Test").  
    Az Azure AD B2C a felhasználó regisztrál, és ezután elküldi a loyaltyNumber az alkalmazáshoz. Megjegyzés: a szám a jwt-t.

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
* Miután elvégezte a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md) forgatókönyv, azt javasoljuk, hogy a saját egyéni házirend-fájlok használatával történő létrehozása adott esetben. Referenciaként a adtunk [házirendfájljait minta](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw).
* Letöltheti a teljes kód látható a [minta Visual Studio megoldás referenciaként](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).
    
## <a name="next-steps"></a>További lépések
* [A RESTful API-t (felhasználónév és jelszó) egyszerű hitelesítést biztonságos](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [A RESTful API-t ügyféltanúsítványok biztonságos](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
