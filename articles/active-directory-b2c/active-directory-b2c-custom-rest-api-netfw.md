---
title: REST API jogcím-cserék integrálása a Azure Active Directory B2C felhasználói úton | Microsoft Docs
description: A felhasználói adatbevitel érvényesítése érdekében integrálhatja REST API jogcím-adatcserét a Azure AD B2C felhasználói úton.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/30/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ed26c4d90738e10f3eb5a9a486cd2734090abd0e
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227258"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>REST API jogcímek cseréjének integrálása a Azure AD B2C felhasználói úton a felhasználói bevitel ellenőrzéseként

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) identitás-kezelési keretrendszer segítségével a felhasználói úton lévő REST API-val integrálható. Ebben az útmutatóban megismerheti, hogyan működik a Azure AD B2C a .NET-keretrendszer REST-szolgáltatásaival (webes API-val).

## <a name="introduction"></a>Bevezetés
A Azure AD B2C használatával saját üzleti logikát adhat hozzá egy felhasználói útra, ha meghívja saját REST-szolgáltatását. Az identitás-keretrendszer a *bemeneti* jogcímek gyűjteményében adatokat küld a REST-szolgáltatásnak, és visszafogadja az adatokat a REST-adatokból a *kimeneti* jogcímek gyűjteményében. A REST-szolgáltatás integrációja révén a következőket teheti:

* **Felhasználói bemeneti adatok érvényesítése**: Ez a művelet megakadályozza, hogy a helytelenül formázott adatok megmaradjanak az Azure AD-ben. Ha a felhasználó értéke érvénytelen, a REST-szolgáltatás egy hibaüzenetet ad vissza, amely arra utasítja a felhasználót, hogy adjon meg egy bejegyzést. Ellenőrizheti például, hogy a felhasználó által megadott e-mail-cím szerepel-e az ügyfél adatbázisában.
* **Bemeneti**jogcímek felülírása: Ha például egy felhasználó az első nevet adja meg az összes kisbetűs vagy az összes nagybetűvel, a nevet csak az első nagybetűvel formázhatja.
* **A felhasználói adatmennyiség bővítése a vállalati üzletági alkalmazások további integrálásával**: A REST-szolgáltatás képes fogadni a felhasználó e-mail-címét, lekérdezni az ügyfél adatbázisát, és visszaküldeni a felhasználó hűségi számát Azure AD B2C. A visszaküldési jogcímeket a felhasználó Azure AD-fiókjába, a következő hangolási *lépésekben*vagy a hozzáférési jogkivonatba foglalt módon lehet tárolni.
* **Egyéni üzleti logika futtatása**: Leküldéses értesítések küldhetők, vállalati adatbázisok frissítése, felhasználói áttelepítési folyamat futtatása, engedélyek kezelése, naplózási adatbázisok és egyéb műveletek végrehajtása.

A REST-szolgáltatásokkal való integrációt a következő módokon lehet megtervezni:

* **Ellenőrzési technikai profil**: A REST-szolgáltatás meghívása a megadott technikai profil érvényesítési technikai profilján belül történik. Az érvényesítési technikai profil ellenőrzi a felhasználó által megadott, a felhasználói út továbbítása előtti lépéseket. Az érvényesítési technikai profillal a következőket teheti:
   * Bemeneti jogcímek küldése.
   * Érvényesítse a bemeneti jogcímeket, és dobja el az egyéni hibaüzeneteket.
   * Kimeneti jogcímek küldése.

* **Jogcím-csere**: Ez a kialakítás hasonló az érvényesítési technikai profilhoz, de egy előkészítési lépésen belül történik. Ez a definíció a következőre korlátozódik:
   * Bemeneti jogcímek küldése.
   * Kimeneti jogcímek küldése.

## <a name="restful-walkthrough"></a>REST-útmutató
Ebben az útmutatóban egy olyan .NET-keretrendszer webes API-t fejleszt ki, amely érvényesíti a felhasználói adatbevitelt, és egy felhasználói hűségi számot biztosít. Például az alkalmazás a hűségi szám alapján hozzáférést biztosíthat a *Platinum előnyeihez* .

Áttekintés:
* Fejlessze a REST-alapú szolgáltatást (.NET-keretrendszer webes API-ját).
* Használja a REST-szolgáltatást a felhasználói úton.
* Bemeneti jogcímek küldése és olvasása a kódban.
* Érvényesítse a felhasználó utónevét.
* Egy lojalitási szám visszaküldése.
* Adja hozzá a hűségi számot egy JSON Web Tokenhoz (JWT).

## <a name="prerequisites"></a>Előfeltételek
Hajtsa végre az [első lépések az egyéni házirendek](active-directory-b2c-get-started-custom.md) használatába című cikket.

## <a name="step-1-create-an-aspnet-web-api"></a>1\. lépés: ASP.NET webes API létrehozása

1. A Visual Studióban hozzon létre egy projektet a **fájl** > **új** > **projekt**lehetőség kiválasztásával.

2. Az **új projekt** ablakban válassza  > a **Visual C#**  **web** > **ASP.net Web Application (.NET-keretrendszer)** elemet.

3. A **név** mezőbe írja be az alkalmazás nevét (például *contoso. AADB2C. API*), majd kattintson az **OK gombra**.

    ![Új Visual Studio-projekt létrehozása a Visual Studióban](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4. Az **új ASP.net** -webalkalmazás ablakban válassza ki a **webes API** -t vagy az **Azure API app** -sablont.

    ![Webes API-sablon kiválasztása a Visual Studióban](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

5. Győződjön meg arról, hogy a hitelesítés **Nincs hitelesítés**.

6. A projekt létrehozásához válassza az **OK** lehetőséget.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>2\. lépés: Az REST API-végpont előkészítése

### <a name="step-21-add-data-models"></a>2,1. lépés: Adatmodellek hozzáadása
A modellek a bemeneti jogcímeket és a kimeneti jogcímeket képviselik a REST-szolgáltatásban. A kód beolvassa a bemeneti adatokat a bemeneti jogcím modell deszerializálása egy JSON-karakterláncból C# egy objektumra (a modellre). A ASP.NET web API automatikusan deszerializálja a kimeneti jogcímek modelljét a JSON-be, majd a szerializált adatokat a HTTP-válaszüzenet törzsére írja.

Hozzon létre egy modellt, amely a következő módon mutatja be a bemeneti jogcímeket:

1. Ha megoldáskezelő még nincs megnyitva, válassza a **Megtekintés** > **megoldáskezelő**lehetőséget.
2. A Solution Explorer (Megoldáskezelő) ablakában kattintson a jobb gombbal a **Models** (Modellek) mappára, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **Class** (Osztály) gombra.

    ![A Visual Studióban kiválasztott Class menüpont hozzáadása](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

3. Nevezze el az `InputClaimsModel`osztályt, majd adja hozzá a következő tulajdonságokat `InputClaimsModel` a osztályhoz:

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

4. Hozzon létre egy új `OutputClaimsModel`modellt, majd adja hozzá a következő tulajdonságokat a `OutputClaimsModel` osztályhoz:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

5. Hozzon létre még egy `B2CResponseContent`modellt, amelyet a bemeneti érvényesítési hibaüzenetek eldobásához használhat. Adja hozzá a következő tulajdonságokat a `B2CResponseContent` osztályhoz, adja meg a hiányzó hivatkozásokat, majd mentse a fájlt:

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

### <a name="step-22-add-a-controller"></a>2,2. lépés: Vezérlő hozzáadása
A webes API-ban a _vezérlő_ egy olyan objektum, amely kezeli a http-kérelmeket. A vezérlő visszaadja a kimeneti jogcímeket, vagy ha az utónév érvénytelen, ütközést okozó HTTP-hibaüzenetet küld.

1. A Solution Explorer (Megoldáskezelő) ablakában kattintson a jobb gombbal a **Controllers** (Vezérlők) mappára, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **Controller** (Vezérlő) gombra.

    ![Új vezérlő hozzáadása a Visual Studióban](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2. Az **állvány hozzáadása** ablakban válassza a **webes API-vezérlő – üres**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.

    ![A web API 2 vezérlő kiválasztása – üres a Visual Studióban](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3. A **vezérlő hozzáadása** ablakban nevezze el a vezérlő **IdentityController**, majd válassza a **Hozzáadás**lehetőséget.

    ![A vezérlő nevének megadása a Visual Studióban](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    Az állványzat létrehoz egy *IdentityController.cs* nevű fájlt a *vezérlők* mappában.

4. Ha a *IdentityController.cs* fájl nincs megnyitva, kattintson rá duplán, majd cserélje le a kódot a fájlban a következő kódra:

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

## <a name="step-3-publish-the-project-to-azure"></a>3\. lépés: A projekt közzététele az Azure-ban
1. Megoldáskezelő kattintson a jobb gombbal a **contoso. AADB2C. API** projektre, majd válassza a **Közzététel**lehetőséget.

    ![Közzététel Microsoft Azure App Service a Visual Studióval](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2. A **Közzététel** ablakban válassza a **Microsoft Azure app Service**, majd a **Közzététel**lehetőséget.

    ![Új Microsoft Azure-App Service létrehozása a Visual Studióval](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    Megnyílik a **Create app Service (létrehozás** ) ablak. Ebben az esetben létrehozza az összes szükséges Azure-erőforrást a ASP.NET-webalkalmazás Azure-ban való futtatásához.

    > [!NOTE]
    >További információ a közzétételéről: [ASP.net-Webalkalmazás létrehozása az Azure-ban](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).

3. A **Web App Name (webalkalmazás neve** ) mezőbe írjon be egy egyedi nevet (az érvényes karakterek: a-z, 0-9 és kötőjel (-). A webalkalmazás URL-címe a következő: http://< APP_NAME >. azurewebsites. NET, ahol a *APP_NAME* a webalkalmazás neve. Elfogadhatja az automatikusan létrehozott nevet is, amely már egyedi.

    ![A App Service tulajdonságainak konfigurálása](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4. Az Azure-erőforrások létrehozásának megkezdéséhez válassza a **Létrehozás**lehetőséget.
    A ASP.NET-Webalkalmazás létrehozása után a varázsló közzéteszi az Azure-ban, majd elindítja az alkalmazást az alapértelmezett böngészőben.

6. Másolja a webalkalmazás URL-címét.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>4\. lépés: Adja hozzá az `loyaltyNumber` új jogcímet a TrustFrameworkExtensions. xml fájl sémájához
A `loyaltyNumber` jogcím még nincs definiálva a sémában. Adjon hozzá egy definíciót `<BuildingBlocks>` a elemen belül, amelyet a *TrustFrameworkExtensions. XML* fájl elején talál.

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

## <a name="step-5-add-a-claims-provider"></a>5\. lépés: Jogcím-szolgáltató hozzáadása
Minden jogcím-szolgáltatónak rendelkeznie kell egy vagy több műszaki profillal, amelyek meghatározzák a jogcímek szolgáltatóval való kommunikációhoz szükséges végpontokat és protokollokat.

A jogcím-szolgáltató több technikai profillal is rendelkezhet különböző okokból. Például több technikai profil is definiálható, mert a jogcím-szolgáltató több protokollt is támogat, a végpontok eltérő képességekkel rendelkezhetnek, vagy a kiadások különböző megbízhatósági szinttel rendelkező jogcímeket tartalmazhatnak. Lehetséges, hogy a bizalmas jogcímeket egy felhasználói úton kell kibocsátani, de nem egy másikban.

A következő XML-kódrészlet tartalmaz egy jogcím-szolgáltatói csomópontot két technikai profillal:

* **TechnicalProfile Id="REST-API-SignUp"** : Meghatározza a REST-szolgáltatást.
  * `Proprietary`egy REST-alapú szolgáltató protokollja.
  * `InputClaims`meghatározza azokat a jogcímeket, amelyeket a rendszer az Azure AD B2Ctól a REST-szolgáltatásba küld.

    Ebben a példában `givenName` a jogcím tartalma a REST `firstName`szolgáltatásnak küldi el, a jogcím `surname` tartalma pedig a REST szolgáltatáshoz `lastName`, és `email` a következőként küldi el:. Az `OutputClaims` elem meghatározza azokat a jogcímeket, amelyeket a REST-szolgáltatásból lekértek vissza Azure ad B2Cre.

* **TechnicalProfile Id="LocalAccountSignUpWithLogonEmail"** : Egy érvényesítési műszaki profilt hoz létre egy meglévő technikai profilhoz (az alapházirenddel meghatározva). A regisztrációs útvonalon az ellenőrzési technikai profil meghívja az előző technikai profilt. Ha a REST-szolgáltatás 409 HTTP-hibát (ütközési hibát) ad vissza, a hibaüzenet jelenik meg a felhasználó számára.

Keresse meg `<ClaimsProviders>` a csomópontot, majd adja hozzá a következő XML- `<ClaimsProviders>` kódrészletet a csomópont alatt:

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

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>6\. lépés: Adja hozzá `loyaltyNumber` a jogcímet a függő entitás házirendjének fájljához, hogy a rendszer elküldje a jogcímet az alkalmazásnak
Szerkessze a *SignUpOrSignIn. XML* függő entitás (RP) fájlját, és módosítsa a kivonatjogcím id = "PolicyProfile" elemet a következők hozzáadásához `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`:.

Az új jogcím hozzáadása után a függő entitás kódja így néz ki:

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

## <a name="step-7-upload-the-policy-to-your-tenant"></a>7\. lépés: Töltse fel a szabályzatot a bérlőbe

1. A [Azure Portal](https://portal.azure.com)váltson a [Azure ad B2C bérlő kontextusára](active-directory-b2c-navigate-to-b2c-context.md), majd nyissa meg **Azure ad B2C**.

2. Válassza az **identitási élmény keretrendszert**.

3. Nyissa meg **az összes szabályzatot**.

4. Válassza a **szabályzat feltöltése**lehetőséget.

5. Jelölje be a **házirend felülírása, ha létezik** jelölőnégyzetet.

6. Töltse fel a TrustFrameworkExtensions. xml fájlt, és ellenőrizze, hogy átadja-e az érvényesítést.

7. Ismételje meg az előző lépést a SignUpOrSignIn. xml fájllal.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>8\. lépés: Az egyéni házirend tesztelése a Futtatás most használatával
1. Válassza ki **Azure ad B2C beállításokat**, majd lépjen az **Identity Experience Framework**elemre.

    > [!NOTE]
    > A futtatáshoz szükség van legalább egy alkalmazás előregisztrálására a bérlőn. Az alkalmazások regisztrálásának megismeréséhez tekintse meg a Azure AD B2C első [lépések](active-directory-b2c-get-started.md) című cikket, vagy az [alkalmazás regisztrációját](active-directory-b2c-app-registration.md) ismertető cikket.

2. Nyissa meg a **B2C_1A_signup_signin**, a függő entitás (RP) egyéni házirendjét, majd válassza a **Futtatás most**lehetőséget.

    ![A Azure Portal B2C_1A_signup_signin egyéni szabályzat lapja](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. Tesztelje a folyamatot úgy, hogy beírja a **teszt** kifejezést a **megadott név** mezőbe.
    Azure AD B2C hibaüzenet jelenik meg az ablak tetején.

    ![A megadott név bemeneti ellenőrzésének tesztelése a bejelentkezési oldalon](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4. A **megadott név** mezőbe írjon be egy nevet (a "test" helyett).
    Azure AD B2C aláírja a felhasználót, majd egy loyaltyNumber küld az alkalmazásnak. Jegyezze fel a JWT szereplő számot.

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
* Miután elvégezte az [Egyéni szabályzatok használatának első lépései útmutatót](active-directory-b2c-get-started-custom.md) , javasoljuk, hogy a saját egyéni házirend-fájljaival hozza létre a forgatókönyvet. Az Ön referenciája alapján megadta a [minta házirend-fájlokat](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw).
* A Visual Studio-megoldás teljes kódját a [következő hivatkozásra töltheti le: minta](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).

## <a name="next-steps"></a>További lépések
* [A REST API biztonságossá tétele egyszerű hitelesítéssel (Felhasználónév és jelszó)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [A REST API biztonságossá tétele Ügyféltanúsítványok használatával](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
