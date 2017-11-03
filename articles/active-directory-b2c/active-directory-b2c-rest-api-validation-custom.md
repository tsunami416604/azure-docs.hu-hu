---
title: "Az Azure Active Directory B2C: REST API-jogcímek cseréjét, érvényesítési |} Microsoft Docs"
description: "Témakör: Azure Active Directory B2C egyéni házirendek"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/24/2017
ms.author: joroja
ms.openlocfilehash: eb44a0d2234c9ee3801d8b3a1655d877aa2f4fef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Forgatókönyv: Integrálása az Azure AD B2C felhasználói út a REST API jogcímek cseréjét, a felhasználói bevitel ellenőrzése

Az identitás élmény keretrendszer (IEF) Azure Active Directory B2C alapjául szolgáló (az Azure AD B2C) lehetővé teszi, hogy a tevékenységet egy felhasználó út RESTful API-t integrálja a identitás fejlesztő.  

Ez az útmutató végén lesz létrehozása az Azure AD B2C felhasználói út, amely együttműködik a RESTful-szolgáltatásokat.

A IEF adatok jogcímekben adatokat küld és fogad vissza a jogcímeket. A interakció API-val:

- A REST API-jogcímek exchange vagy egy érvényességi profilt, amely egy vezénylési lépés belül történik tervezhető.
- Általában érvényesíti felhasználói beavatkozást. Az érték a felhasználó elutasítása esetén a felhasználó megpróbálja újra adjon meg egy érvényes értéket a számára, hogy egy hibaüzenetet adja vissza.

Is kialakíthat egy vezénylési lépés a közötti. További információkért lásd: [forgatókönyv: integrálja a REST API-t cseréjét használják az Azure AD B2C felhasználói út egy vezénylési lépés a jogcímek](active-directory-b2c-rest-api-step-custom.md).

Például az érvényességi profilt a profil szerkesztése felhasználói út az alapszintű csomag fájlban ProfileEdit.xml használjuk.

Azt is ellenőrizze, hogy a profil szerkesztése a felhasználó által megadott név nem része egy kizárási listát.

## <a name="prerequisites"></a>Előfeltételek

- Egy helyi fiókot sign-Close-Up/sign-in befejezéséhez, a konfigurált Azure AD B2C-bérlő [bevezetés](active-directory-b2c-get-started-custom.md).
- REST API-végpont kommunikál. A forgatókönyv azt létrehoztunk egy bemutató webhelyet nevű [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) egy REST API-szolgáltatással.

## <a name="step-1-prepare-the-rest-api-function"></a>1. lépés: Felkészülés a REST API-függvénye

> [!NOTE]
> Ez a cikk hatókörén kívül található a REST API-függvények telepítése. [Az Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) biztosít egy kiváló eszközkészlet RESTful szolgáltatás létrehozásához a felhőben.

Létrehoztunk egy Azure függvény, amely fogad egy jogcímet, akkor várja `playerTag`. A függvény ellenőrzi, hogy létezik-e ezt a kérelmet. Érheti el a teljes Azure funkciókódot [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

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

A IEF vár a `userMessage` jogcímet, amely az Azure függvényt ad vissza. A jogcím fog érzékelni karakterláncként a felhasználót, ha az érvényesítés meghiúsul, például amikor egy 409 ütközés állapot eredmény abban az esetben az előző példában.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>2. lépés: A RESTful API jogcímek az exchange konfigurálása a TrustFrameworkExtensions.xml fájlban műszaki profil

Műszaki profilt a RESTful szolgáltatás szükséges az exchange teljes konfigurációjának. Nyissa meg a TrustFrameworkExtensions.xml fájlt, és adja hozzá a következő XML-részletet belül a `<ClaimsProviders>` elemet.

> [!NOTE]
> A következő XML-kódban, RESTful szolgáltató `Version=1.0.0.0` protokoll leírását. Vegye figyelembe azt, a függvény, amely a külső szolgáltatással együtt fog működni. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

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

A `InputClaims` elem definiálja a jogcímeket, amely a REST-szolgáltatást a IEF a kapnak. Ebben a példában a jogcím tartalmát `givenName` kapnak a többi szolgáltatás `playerTag`. Ebben a példában a IEF nem várt jogcímek vissza. Ehelyett a REST-szolgáltatást és a fogadott állapotkódok alapján tevékenységéért válaszára megvárja.

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>3. lépés: A RESTful szolgáltatás jogcímek exchange foglalandó önálló szükséges műszaki profil, ahol szeretné érvényesíteni a felhasználói bevitel

A leggyakoribb ellenőrzési lépés használata az egy felhasználói beavatkozást. Minden interakció, ahol a felhasználó kellene adnia valamit vannak *önálló magas műszaki profilok*. Ebben a példában az érvényesítési önkiszolgáló Asserted ProfileUpdate műszaki profilt felveszi azt. Ez a műszaki profilja, a függő entitásonkénti (RP) házirendfájl `Profile Edit` használja.

A jogcímek exchange hozzáadása a saját szükséges műszaki profil:

1. Nyissa meg a TrustFrameworkBase.xml fájlt, és keresse meg `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Ellenőrizze a műszaki profil konfigurációját. Figyelje meg, hogy az exchange-ben a felhasználó nevezünk kérni fogja annak a felhasználónak (a bemeneti jogcímek), de az önálló szükséges szolgáltatót (kimeneti jogcímek) ból várhatóan jogcímeket.
3. Keresse meg `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`, és figyelje meg, hogy ez a profil hívja meg, az orchestration 6. lépésében `<UserJourney Id="ProfileEdit">`.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>4. lépés: Töltse fel, és a profil szerkesztése RP házirend fájl tesztelése

1. Töltse fel a TrustFrameworkExtensions.xml fájl új verzióját.
2. Használjon **futtatása most** a profil szerkesztése RP házirendfájl teszteléséhez.
3. Tesztelje az érvényesítési egyik meglévő nevét (például mcvinny) biztosításával a **Utónév** mező. Ha minden megfelelően van beállítva, kell kapnia egy üzenetet, amely értesíti a felhasználót, hogy a player címke már használatban van.

## <a name="next-steps"></a>Következő lépések

[Módosítsa a profil szerkesztése és felhasználói regisztráció gyűjtsön további információt a felhasználók](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Forgatókönyv: Integrálása az Azure AD B2C felhasználói út a REST API jogcímek cseréjét egy vezénylési lépés](active-directory-b2c-rest-api-step-custom.md)
