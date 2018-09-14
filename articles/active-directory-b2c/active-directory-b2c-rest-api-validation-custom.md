---
title: REST API-jogcímek cseréje érvényesítése az Azure Active Directory B2C-t |} A Microsoft Docs
description: A témakör az Azure Active Directory B2C-vel egyéni szabályzatok.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/24/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 0ac9b98a9dfe06492775481cd590bfb4d0db4b55
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542582"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Forgatókönyv: A felhasználói bevitel auditáló integrálása a REST API-val jogcím cseréje az Azure AD B2C felhasználói interakciósorozatban szereplő

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az identitás élmény keretrendszer (IEF) alapjául szolgáló Azure Active Directory B2C (Azure AD B2C-vel) lehetővé teszi, hogy az identitás fejlesztői integrálható a RESTful API-t a felhasználói út használata közben.  

Ez az útmutató végén lesz egy Azure AD B2C felhasználói interakciósorozat, együttműködő létrehozása a REST-alapú szolgáltatások.

A IEF adatokat küld a jogcímeket, valamint adatokat fogad újra a jogcím. A kapcsolati API-val:

- Megtervezhetők úgy, egy REST API-val jogcímcsere, vagy egy érvényesítési profilt, amely egy vezénylési lépés belül történik.
- Felhasználói adatbevitel általában érvényesíti. Az érték a felhasználó elutasítása esetén a felhasználó újra próbálkozhat, adjon meg egy érvényes értéket arra, hogy hibaüzenetet ad vissza.

Egy vezénylési lépés a közötti is tervezhet. További információkért lásd: [forgatókönyv: integrálása a REST API-val cserék az Azure AD B2C felhasználói interakciósorozat egy vezénylési lépés, a jogcímek](active-directory-b2c-rest-api-step-custom.md).

Ha érvényesítési profil például az alapszintű csomag fájlban ProfileEdit.xml profil szerkesztése felhasználói interakciósorozat használjuk.

Ellenőrizzük, hogy a profil szerkesztése a felhasználó által megadott név nem része egy kizárási listát.

## <a name="prerequisites"></a>Előfeltételek

- Az Azure AD B2C-bérlő egy helyi fiók regisztrálási-regisztrálási vagy bejelentkezési, végrehajtásához leírtak szerint konfigurálva [bevezetés](active-directory-b2c-get-started-custom.md).
- REST API-végpont használatával kommunikálhat. Ebben a bemutatóban beállítottuk a bemutató webhely nevű [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) egy REST API-szolgáltatást.

## <a name="step-1-prepare-the-rest-api-function"></a>1. lépés: Készítse elő a REST API-függvénye

> [!NOTE]
> REST API-függvények, a telepítő nem ez a cikk foglalkozik. [Az Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) biztosít egy kiváló eszközkészlet RESTful szolgáltatás létrehozásához a felhőben.

Létrehoztunk Önnek egy Azure-függvényt, amely fogad egy jogcímet, amely azt vár `playerTag`. A függvény azt ellenőrzi, ez a jogcím létezik-e. Hozzáférhet a teljes Azure-függvény kódja [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

```csharp
if (requestContentAsJObject.playerTag == null)
{
  return request.CreateResponse(HttpStatusCode.BadRequest);
}

var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
{
  return request.CreateResponse<ResponseContent>(
    HttpStatusCode.Conflict,
    new ResponseContent
    {
      version = "1.0.0",
      status = (int) HttpStatusCode.Conflict,
      userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
    },
    new JsonMediaTypeFormatter(),
    "application/json");
}

return request.CreateResponse(HttpStatusCode.OK);
```

A IEF vár a `userMessage` jogcímet, amelyet az Azure-függvény adja vissza. Ez a jogcím megjelenik egy karakterláncként a felhasználónak, ha az érvényesítés sikertelen, például ha az előző példában 409 ütközés állapotot adott vissza.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>2. lépés: Az RESTful API jogcímcsere konfigurálása TrustFrameworkExtensions.xml fájlban technikai profil

Egy technikai profil a RESTful szolgáltatás kívánt exchange teljes konfigurációját. Nyissa meg a TrustFrameworkExtensions.xml fájlt, és adja hozzá a következő XML-részletet belül a `<ClaimsProviders>` elemet.

> [!NOTE]
> A következő XML formátumú, RESTful szolgáltató `Version=1.0.0.0` protokoll leírását. Érdemes használni, mint a függvényt, amely a külső szolgáltatás használni fog. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
            <ValidationTechnicalProfiles>
                <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

A `InputClaims` elem definiálja a jogcímeket, amely a REST-szolgáltatás, a IEF kapnak. Ebben a példában a jogcím tartalmát `givenName` küld a REST szolgáltatás `playerTag`. Ebben a példában a IEF nem várt vissza jogcímeket. Ehelyett azt a válaszra vár a REST-szolgáltatás és a állapotkódok, amely alapján működik.

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>3. lépés: Közé tartozik a RESTful szolgáltatás jogcímcsere önellenőrzött technikai profilban, ahol szeretné a felhasználói bevitel ellenőrzése

Az ellenőrzési lépés leggyakoribb használatát egy felhasználói interakció szerepel. Összes műveletet, amikor a felhasználó várhatóan információk megadása a rendszer *önálló kiszolgáló által megerősített technikai profilok*. Ebben a példában hozzáadjuk az érvényesítés az önkiszolgáló Asserted ProfileUpdate műszaki profilba. Ez a műszaki profilja, amelyet a függő entitásonkénti (RP) házirendfájl `Profile Edit` használja.

Az önellenőrzött technikai profilban jogcímcsere hozzáadása:

1. Nyissa meg a TrustFrameworkBase.xml fájlt, és keressen rá a `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Tekintse át a konfigurációt, a technikai profil. Figyelje meg, hogy a felhasználó exchange számít, ha jogcímeket meg kell adnia annak a felhasználónak (a bemeneti jogcímek), és visszaküldi az önellenőrzött szolgáltató (kimeneti jogcímek) vár jogcímeket.
3. Keresse meg `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`, és figyelje meg, hogy ez a profil hívja meg, a vezénylési lépés 5 `<UserJourney Id="ProfileEdit">`.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>4. lépés: Töltse fel, és a profil szerkesztése RP házirend fájl tesztelése

1. Töltse fel a TrustFrameworkExtensions.xml fájl új verzióját.
2. Használat **Futtatás most** a profil szerkesztése RP házirendfájl teszteléséhez.
3. Tesztelje az érvényesítés azáltal, hogy valamelyik meglévő nevét (például mcvinny) a **Utónév** mező. Ha minden helyesen van beállítva, meg kell kapnia egy üzenet, amely értesíti a felhasználót, hogy a lejátszó címke már használatban van.

## <a name="next-steps"></a>További lépések

[Módosítsa a profil szerkesztése és a felhasználó regisztrációját, hogy további információkat gyűjtsön a felhasználók számára](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Forgatókönyv: Integrálása az Azure AD B2C felhasználói interakciósorozatban szereplő REST API-val jogcím cseréje egy vezénylési lépés](active-directory-b2c-rest-api-step-custom.md)
