---
title: REST API a jogcím-cserét érvényesítésként Azure Active Directory B2C
description: Útmutató a REST-szolgáltatásokkal kommunikáló Azure AD B2C felhasználói út létrehozásához.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 45fad1fab419c448febb3f3b760996fba278e154
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69644975"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Forgatókönyv: REST API jogcímek cseréjének integrálása a Azure AD B2C felhasználói úton a felhasználói adatok érvényesítése során

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) alapját képező Identity Experience Framework (IEF) lehetővé teszi, hogy az identitás-fejlesztő egy REST API-val egyesítse az interakciót egy felhasználói úton.

A bemutató végén létrehozhat egy Azure AD B2C felhasználói utat, amely a REST-szolgáltatásokkal kommunikál.

A IEF adatokat küld a jogcímekben, és visszafogadja az adatokat a jogcímekben. Az API-val való interakció:

- REST API jogcím-csereként vagy ellenőrzési profilként is megtervezhető, amely egy előkészítési lépésen belül történik.
- Általában ellenőrzi a felhasználótól érkező adatokat. Ha a felhasználó által visszautasított értéket elutasítja a rendszer, a felhasználó újra próbálkozhat egy érvényes érték megadásával, amely lehetővé teszi a hibaüzenetek visszaadását.

Az interakciót összehangoló lépésként is megtervezheti. További információ [: útmutató: Integrációs lépésként](active-directory-b2c-rest-api-step-custom.md)integrálhatja REST API jogcímek cseréjét a Azure ad B2C felhasználói úton.

Az ellenőrzési profil esetében például a felhasználói út szerkesztése a Starter Pack ProfileEdit. xml fájlban.

Ellenőrizheti, hogy a felhasználó által a profil szerkesztése során megadott név nem része-e kizárási listának.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure AD B2C bérlő, amely egy helyi fiók regisztrálására és bejelentkezésére van konfigurálva, az [első lépések](active-directory-b2c-get-started-custom.md)című témakörben leírtak szerint.
- Egy REST API végpont, amely együttműködik a szolgáltatással. Ebben az útmutatóban egy [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) nevű bemutató webhelyet állítottunk be egy REST API szolgáltatással.

## <a name="step-1-prepare-the-rest-api-function"></a>1\. lépés: A REST API függvény előkészítése

> [!NOTE]
> REST API függvények beállítása kívül esik ennek a cikknek a hatókörén. A [Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) kiváló eszközkészletet biztosít a REST-szolgáltatások Felhőbeli létrehozásához.

Létrehoztunk egy Azure-függvényt, amely egy olyan jogcímet fogad el `playerTag`, amely a várt módon fog megjelenni. A függvény ellenőrzi, hogy létezik-e ez a jogcím. Elérheti a teljes Azure-függvény kódját a [githubon](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

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

A IEF az Azure- `userMessage` függvény által visszaadott jogcímet várja. Ez a jogcím karakterláncként jelenik meg a felhasználó számára, ha az ellenőrzés sikertelen, például ha az előző példában egy 409 ütközési állapotot ad vissza.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>2\. lépés: A REST API-jogcímek cseréje technikai profilként a TrustFrameworkExtensions. xml fájlban

A technikai profil a REST-szolgáltatással kívánt Exchange teljes konfigurációja. Nyissa meg a TrustFrameworkExtensions. xml fájlt, és adja hozzá a következő `<ClaimsProviders>` XML-kódrészletet az elemben.

> [!NOTE]
> A következő XML-ben a REST- `Version=1.0.0.0` szolgáltató protokollként van leírva. Vegye figyelembe azt a függvényt, amely a külső szolgáltatással fog működni. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="SendClaimsIn">Body</Item>
                <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
                <Item Key="AuthenticationType">None</Item>
                <!-- REMOVE the following line in production environments -->
                <Item Key="AllowInsecureAuthInProduction">true</Item>
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

Az `InputClaims` elem határozza meg azokat a jogcímeket, amelyeket a rendszer a IEF küld a REST-szolgáltatásnak. Ebben a példában a jogcím `givenName` tartalmát a REST `playerTag`szolgáltatásnak küldi el a rendszer. Ebben a példában a IEF nem várja vissza a jogcímeket. Ehelyett a REST-szolgáltatástól érkező választ vár, és a kapott állapotkódok alapján viselkedik.

A fenti `AuthenticationType` megjegyzések és `AllowInsecureAuthInProduction` az éles környezetbe való áttéréskor végrehajtott módosítások megadása. A REST API-k éles környezetben történő biztonságossá tételéhez tekintse meg a [biztonságos REST API](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) -k alapszintű hitelesítéssel és [biztonságos REST API](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)-k hitelesítéssel című témakörét.

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>3\. lépés: Adja meg a REST-alapú szolgáltatási jogcímek Exchange-et önérvényesített technikai profilban, ahol érvényesíteni kívánja a felhasználói adatbevitelt

Az érvényesítési lépés leggyakoribb használata a felhasználóval való interakció. Minden olyan interakció, amelyben a felhasználónak meg kell adnia a bemenetet, *saját maga által megadott technikai profilok*. Ebben a példában az érvényesítést hozzáadjuk az önérvényesített ProfileUpdate műszaki profilhoz. Ez az a technikai profil, amelyet a függő entitás (RP) házirend fájlja `Profile Edit` használ.

A jogcím-Exchange hozzáadása az önérvényesített technikai profilhoz:

1. Nyissa meg a TrustFrameworkBase. xml fájlt, `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`és keresse meg a következőt:.
2. Tekintse át a technikai profil konfigurációját. Figyelje meg, hogy a felhasználóval való Exchange hogyan van definiálva jogcímként, amelyet a rendszer a felhasználó (bemeneti jogcímek) és azon jogcímek esetében kér, amelyek vissza fognak térni az önérvényesített szolgáltatótól (kimeneti jogcímek).
3. Keresse meg `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`, és figyelje meg, hogy ez a profil a `<UserJourney Id="ProfileEdit">`5. lépésként van meghívva.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>4\. lépés: Töltse fel és tesztelje a profil szerkesztése RP-szabályzat fájlját

1. Töltse fel a TrustFrameworkExtensions. xml fájl új verzióját.
2. A **Futtatás most** paranccsal tesztelheti az RP-fájl szerkesztésére szolgáló profilt.
3. Tesztelje az ellenőrzést úgy, hogy megadja a meglévő nevek (például mcvinny) egyikét a **megadott név** mezőben. Ha minden megfelelően be van állítva, olyan üzenetet kap, amely értesíti a felhasználót arról, hogy a lejátszó címkéje már használatban van.

## <a name="next-steps"></a>További lépések

[A profil szerkesztése és a felhasználó regisztrációjának módosítása további információk összegyűjtéséhez a felhasználóktól](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Útmutató REST API jogcímek cseréjének integrálása a Azure AD B2C felhasználói úton az előkészítési lépésként](active-directory-b2c-rest-api-step-custom.md)

[Referencia REST-technikai profil](restful-technical-profile.md)

Az API-k biztonságossá tételéhez tekintse meg a következő cikkeket:

* [A REST API biztonságossá tétele egyszerű hitelesítéssel (Felhasználónév és jelszó)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [A REST API biztonságossá tétele Ügyféltanúsítványok használatával](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
