---
title: REST API-val jogcím cseréje az Azure Active Directory B2C felhasználói interakciósorozatban szereplő integrálása |} A Microsoft Docs
description: Integrálja a REST API-val jogcím cseréje az Azure AD B2C felhasználói interakciósorozatban szereplő, a felhasználói bevitel ellenőrzése.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/30/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9e5f8171dde2f6da153441dafd9a87fc5c946e71
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853051"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>A felhasználói adatbevitel auditáló REST API-val jogcím cseréje az Azure AD B2C felhasználói interakciósorozatban szereplő integrálása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az az identitás-kezelőfelületi keretrendszer, amely alapjául szolgál az Azure Active Directory B2C (Azure AD B2C) integrálható egy felhasználói interakciósorozat egy RESTful API-val. Ebben az útmutatóban megismerheti, hogyan kommunikál az Azure AD B2C-vel .NET-keretrendszer RESTful services (webes API-t).

## <a name="introduction"></a>Bevezetés
Saját RESTful szolgáltatás hívása az Azure AD B2C használatával saját üzleti logikája is hozzáadhat egy felhasználói interakciósorozat. Az identitás-kezelőfelületi keretrendszer a RESTful szolgáltatás adatokat küld egy *bemeneti jogcímek* gyűjtemény, valamint adatokat fogad visszaküldi a RESTful- *kimeneti jogcímek* gyűjtemény. RESTful szolgáltatás integrációnak köszönhetően a következőket teheti:

* **Felhasználó által bevitt adatok érvényesítéséhez**: Ez a művelet megakadályozza, hogy nem megfelelően formázott megőrizhetők az Azure ad-ben. Ha az érték a felhasználó nem érvényes, a RESTful szolgáltatás, amely felszólítja a felhasználót egy bejegyzést adjon meg a hibaüzenetet adja vissza. Ellenőrizheti például, hogy az a felhasználó által megadott e-mail-cím szerepel-e a felhasználó-adatbázisa alapján.
* **Írja felül a bemeneti jogcímek között**: Például ha egy felhasználó sikeresen megadja a csupa nagybetűvel vagy az utónevet csupa kisbetűket, a neve a formázhatók csak az első betűje nagybetű.
* **Felhasználói adatokat feldúsítani a vállalati – üzletági alkalmazások további integrálásával**: A RESTful szolgáltatás kap a felhasználó e-mail címét, az ügyfél-adatbázis lekérdezése, és az Azure AD B2C-vel a felhasználói hűség szám visszaadása. A visszaadandó jogcímek a felhasználó Azure AD-fiókot, a következő kiértékelése tárolhatja *Vezénylési lépésekből*, vagy a hozzáférési jogkivonatot tartalmazza.
* **Egyéni üzleti logika futtatása**: Is leküldéses értesítések küldése, vállalati adatbázisok frissítése, felhasználó migrálási folyamat futtatása, kezelheti az engedélyeiket, adatbázis naplózási és egyéb műveleteket hajthat végre.

Megtervezheti a REST-alapú szolgáltatásokkal való integrációt, a következő módon:

* **Ellenőrzési technikai profil**: A RESTful szolgáltatás hívása akkor történik meg az érvényesítési technikai profilban megadott technikai profil belül. Az ellenőrzési technikai profil ellenőrzi a felhasználó által megadott felhasználói interakciósorozat előrelépés előtt. Az ellenőrzési technikai profil segítségével:
   * A bemeneti jogcímek küldése.
   * Ellenőrizze a bemeneti jogcímek között, és egyéni hibaüzenetek throw.
   * Vissza a kimeneti jogcímek küldése.

* **Az exchange-jogcímek**: Ez a kialakítás az érvényesítési technikai profil hasonló, de azt egy vezénylési lépés belül történik. Ez a definíció korlátozva:
   * A bemeneti jogcímek küldése.
   * Vissza a kimeneti jogcímek küldése.

## <a name="restful-walkthrough"></a>REST-alapú forgatókönyv
Ez az útmutató a .NET-keretrendszer webes API, a felhasználói bevitel érvényesíti, és számos olyan felhasználói hűség fejleszthet. Ha például az alkalmazás adhat hozzáférést *platinum előnyöket* hűségprogramok használatán keresztül száma alapján.

Áttekintés:
* Fejlesztés a RESTful szolgáltatás (.NET-keretrendszer webes API-t).
* A RESTful szolgáltatás használata a felhasználói interakciósorozatban szereplő.
* A bemeneti jogcímek küldése és olvashatja őket a kód.
* Ellenőrizze a felhasználó utónevét.
* Küldjön vissza hűségprogramok használatán keresztül szám.
* Adja hozzá a hűségprogramok használatán keresztül számot, egy JSON webes jogkivonat (JWT).

## <a name="prerequisites"></a>Előfeltételek
A lépések elvégzéséhez a [Ismerkedés az egyéni szabályzatok](active-directory-b2c-get-started-custom.md) cikk.

## <a name="step-1-create-an-aspnet-web-api"></a>1. lépés: Az ASP.NET webes API létrehozása

1. A Visual Studióban hozzon létre egy projektet kiválasztásával **fájl** > **új** > **projekt**.

2. Az a **új projekt** ablakban válassza **Visual C#** > **webes** > **ASP.NET Web Application (.NET Framework)**.

3. Az a **neve** mezőbe írja be az alkalmazás nevét (például *Contoso.AADB2C.API*), majd válassza ki **OK**.

    ![Új visual studio-projekt létrehozása](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4. Az a **új ASP.NET-webalkalmazás** ablakban válassza ki a **webes API** vagy **Azure API-alkalmazás** sablon.

    ![Webes API-sablon kiválasztása](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

5. Győződjön meg arról, hogy a hitelesítés értéke **nincs hitelesítés**.

6. A projekt létrehozásához válassza az **OK** lehetőséget.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>2. lépés: Készítse elő a REST API-végpont

### <a name="step-21-add-data-models"></a>2.1. lépés: Adatmodell hozzáadása
A modellek felel meg a bemeneti jogcímek között, és a kimeneti jogcím-adatok a REST-alapú service-ben. A kód beolvassa a bemeneti adatok deszerializálása során a bemeneti jogcímek között modell egy JSON-karakterlánc egy C#-objektumot (a modell) által. Az ASP.NET web API automatikusan deserializes a kimeneti jogcímek modell vissza a JSON és a szerializált adatok ezután ír a HTTP-válaszüzenet törzsében.

Hozzon létre egy modell a bemeneti jogcímek között az alábbiak szerint:

1. Ha Megoldáskezelőben még nem nyitott, válassza ki a **nézet** > **Megoldáskezelőben**.
2. A Solution Explorer (Megoldáskezelő) ablakában kattintson a jobb gombbal a **Models** (Modellek) mappára, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **Class** (Osztály) gombra.

    ![Modell hozzáadása](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

3. Az osztály neve `InputClaimsModel`, majd adja hozzá a következő tulajdonságokat a `InputClaimsModel` osztály:

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

4. Hozzon létre egy új modell `OutputClaimsModel`, majd adja hozzá a következő tulajdonságokat a `OutputClaimsModel` osztály:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

5. Hozzon létre egy további modell `B2CResponseContent`, amellyel throw bemenet-ellenőrzés hibaüzenetek. Adja hozzá a következő tulajdonságot a `B2CResponseContent` osztályban adja meg a hiányzó hivatkozásokat, és mentse a fájlt:

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

### <a name="step-22-add-a-controller"></a>2.2. lépés: Vezérlő hozzáadása
A webes API-hoz egy _vezérlő_ olyan objektum, amely HTTP-kéréseket. A vezérlő adja vissza a kimeneti jogcímek, vagy az első név nem érvényes, ha egy ütköző HTTP-hibaüzenetet jelez.

1. A Solution Explorer (Megoldáskezelő) ablakában kattintson a jobb gombbal a **Controllers** (Vezérlők) mappára, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **Controller** (Vezérlő) gombra.

    ![Új vezérlő felvétele](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2. Az a **hozzáadása Scaffold** ablakban válassza **webes API-vezérlő - üres**, majd válassza ki **Hozzáadás**.

    ![Válassza ki webes API 2 vezérlő – üres](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3. Az a **vezérlő hozzáadása** ablakban, a vezérlő neve **IdentityController**, majd válassza ki **Hozzáadás**.

    ![Írja be a vezérlő neve](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    A keret létrehozásához létrehoz egy fájlt *IdentityController.cs* a a *tartományvezérlők* mappát.

4. Ha a *IdentityController.cs* fájlban még nem nyitott, és kattintson rá duplán, majd cserélje le a fájlban lévő kódot az alábbira:

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

## <a name="step-3-publish-the-project-to-azure"></a>3. lépés: A projekt közzététele az Azure-ban
1. A Megoldáskezelőben kattintson a jobb gombbal a **Contoso.AADB2C.API** projektre, és válassza ki **közzététel**.

    ![A Microsoft Azure App Service-ben való közzététele](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2. Az a **közzététel** ablakban válassza **Microsoft Azure App Service**, majd válassza ki **közzététel**.

    ![Hozzon létre új Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    A **létrehozása App Service** ablak nyílik meg. Az összes szükséges Azure-erőforrást az ASP.NET-webalkalmazás futtatása az Azure-ban létrehoz.

    > [!NOTE]
    >Hogyan tehet közzé kapcsolatos további információkért lásd: [ASP.NET-webalkalmazás létrehozása az Azure-ban](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. Az a **webalkalmazás neve** mezőbe írja be egy egyedi névre (érvényes karakterek: a – z, 0 – 9 és kötőjelet (-). A webalkalmazás URL-je http://<app_name>.azurewebsites.NET, ahol *app_name* a webalkalmazás neve. Elfogadhatja az automatikusan létrehozott nevet is, amely már egyedi.

    ![Adja meg a tulajdonságokat App Service-ben](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4. Azure-erőforrások létrehozását, jelölje **létrehozás**.  
    Az ASP.NET-webalkalmazás létrehozása után a varázsló közzéteszi az Azure-ba, és elindítja az alkalmazást az alapértelmezett böngészőben.

6. Másolja a web app URL-címet.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>4. lépés: Az új `loyaltyNumber` jogcímet a TrustFrameworkExtensions.xml fájl sémája
A `loyaltyNumber` jogcím még nem határozott meg a sémában. Adja hozzá belül definícióját a `<BuildingBlocks>` elemet, amely elején annak a *TrustFrameworkExtensions.xml* fájlt.

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

## <a name="step-5-add-a-claims-provider"></a>5. lépés: A jogcímeket szolgáltató hozzáadása
Minden jogcím-szolgáltatói rendelkeznie kell egy vagy több technikai profilok, amelyek meghatározzák a végpontok és a jogcímszolgáltató folytatott kommunikációhoz szükséges protokollok.

Jogcím-szolgáltatóktól rendelkezhet több technikai profil különböző okok miatt. Például több technikai profil definiálhatók a jogcímszolgáltató több protokollt is támogat, végpontok különböző képességekkel is rendelkeznek, vagy hogy kiadásokban is tartalmazhat, amely számos különböző biztonsági szintek jogcímeket. Egy felhasználói interakciósorozatban szereplő, de nem a másik bizalmas jogcímeket kiadni elfogadható lehet.

A következő XML-kódrészlet két technikai profil a jogcímeket szolgáltató csomópontot tartalmaz:

* **TechnicalProfile Id="REST-API-SignUp"**: A RESTful szolgáltatás határozza meg.
   * `Proprietary` ismertetjük a protokoll egy RESTful-alapú szolgáltató.
   * `InputClaims` határozza meg a jogcímeket küld az Azure AD B2C-ből a REST-szolgáltatást.

   Ebben a példában a tartalom a jogcím `givenName` küld a REST-szolgáltatás, mint `firstName`, a tartalom a jogcím `surname` küld a REST-szolgáltatás, mint `lastName`, és `email` , küld. A `OutputClaims` elem definiálja a jogcímek lekért RESTful szolgáltatás vissza az Azure AD B2C-t.

* **TechnicalProfile Id="LocalAccountSignUpWithLogonEmail"**: Egy ellenőrzési technikai profil hozzáadása egy meglévő technikai profilban (alap szabályzatban definiált). A regisztrációs folyamatok során az érvényesítési technikai profil a korábbi technikai profil hív meg. Ha a RESTful szolgáltatás hibát jelez a HTTP 409 (ütközés hiba), a hibaüzenet jelenik meg a felhasználó számára.

Keresse meg a `<ClaimsProviders>` csomópontot, majd adja hozzá a következő XML-részletet a `<ClaimsProviders>` csomópont:

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
        <Item Key="AllowInsecureAuthInProduction">true</Item>
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

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>6. lépés: Adja hozzá a `loyaltyNumber` jogcím a függő entitás házirendfájlt, így az a jogcím megkap az alkalmazás
Szerkessze a *SignUpOrSignIn.xml* függő entitásonkénti (RP) fájlt, és módosítsa a TechnicalProfile azonosító = "PolicyProfile" elemet, adja hozzá a következő: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Miután az új jogcímet ad hozzá, a függő entitás kód a következőhöz hasonló:

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

## <a name="step-7-upload-the-policy-to-your-tenant"></a>7. lépés: A szabályzat feltöltése a bérlőhöz

1. Az a [az Azure portal](https://portal.azure.com), váltson át a [az Azure AD B2C-bérlője kontextusában](active-directory-b2c-navigate-to-b2c-context.md), majd nyissa meg **Azure AD B2C-vel**.

2. Válassza ki **identitás-kezelőfelületi keretrendszer**.

3. Nyissa meg **összes szabályzat**.

4. Válassza ki **szabályzat feltöltése**.

5. Válassza ki a **szabályzat felülírása, ha létezik** jelölőnégyzetet.

6. Töltse fel a TrustFrameworkExtensions.xml fájlt, és győződjön meg arról, hogy érvényesítési továbbítja.

7. Ismételje meg az előző lépés SignUpOrSignIn.xml-fájllal.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>8. lépés: Az egyéni házirend tesztelése a Futtatás most
1. Válassza ki **Azure AD B2C-beállítások**, majd lépjen **identitás-kezelőfelületi keretrendszer**.

    > [!NOTE]
    > **Futtatás most** kell előzetesen regisztrálva, a bérlő legalább egy alkalmazás szükséges. Megtudhatja, hogyan regisztrálja az alkalmazást, tekintse meg az Azure AD B2C [Ismerkedés](active-directory-b2c-get-started.md) cikk vagy a [alkalmazásregisztráció](active-directory-b2c-app-registration.md) cikk.

2. Nyissa meg **B2C_1A_signup_signin**, a függő entitásonkénti (RP) egyéni-szabályzattal, feltöltött, és válassza ki **Futtatás most**.

    ![A B2C_1A_signup_signin ablak](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. Írja be a folyamat teszteléséhez **teszt** a a **Utónév** mezőbe.  
    Az Azure AD B2C hibaüzenetet jelenít meg az ablak tetején.

    ![A házirend tesztelése](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4. Az a **Utónév** mezőbe írjon be egy nevet (nem a "Test").  
    Az Azure AD B2C a felhasználó regisztrál, és a egy loyaltyNumber és az alkalmazás továbbítja. Vegye figyelembe a JWT szereplő számot.

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
* Miután elvégezte a [egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md) forgatókönyv, azt javasoljuk, hogy a forgatókönyv a saját egyéni házirend-fájlok használatával hozhat létre. Referenciaként adtunk meg [házirendfájljait minta](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw).
* Letöltheti a teljes kódját [mintát a Visual Studio-megoldás referenciaként](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).

## <a name="next-steps"></a>További lépések
* [Biztonságos a RESTful API-címhez alapszintű hitelesítéssel (felhasználónév és jelszó)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [Az ügyféltanúsítványokat a RESTful API biztonságossá tétele](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
