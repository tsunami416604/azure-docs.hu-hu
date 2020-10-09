---
title: TrustFrameworkPolicy – Azure Active Directory B2C | Microsoft Docs
description: A Azure Active Directory B2C egyéni házirendjének TrustFrameworkPolicy elemének megadásához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 29eddbcfb7c0da98e5438f968dd3976b77a44680
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85203095"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az egyéni szabályzatok egy vagy több XML-formátumú fájlként jelennek meg, amelyek egy hierarchikus láncban egymásra hivatkoznak. Az XML-elemek határozzák meg a szabályzat elemeit, például a jogcímek sémáját, a jogcímek átalakítását, a tartalmi definíciókat, a jogcím-szolgáltatókat, a technikai profilokat, a felhasználói útvonalakat, valamint a Minden házirendfájl a házirendfájl legfelső szintű **TrustFrameworkPolicy** elemében van definiálva.

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


A **TrustFrameworkPolicy** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Igen | A szabályzat végrehajtásához használandó séma verziója. Az értéknek meg kell felelnie `0.3.0.0` |
| TenantObjectId | Nem | A Azure Active Directory B2C (Azure AD B2C) bérlő egyedi objektumazonosító. |
| TenantId | Igen | Annak a bérlőnek az egyedi azonosítója, amelyhez ez a szabályzat tartozik. |
| PolicyId | Igen | A házirend egyedi azonosítója. Ezt az azonosítót *B2C_1A_* kell előállítani |
| PublicPolicyUri | Igen | A házirend URI-ja, amely a bérlői azonosító és a házirend-azonosító kombinációja. |
| Deploymentmode beállítása | Nem | Lehetséges értékek: `Production` , vagy `Development` . A `Production` az alapértelmezett formátum. Ezt a tulajdonságot használhatja a szabályzat hibakereséséhez. További információkért lásd: [naplók gyűjtése](troubleshoot-with-application-insights.md). |
| UserJourneyRecorderEndpoint | Nem | A végpont, amelyet a **deploymentmode beállítása** beállításakor használ a rendszer `Development` . Az értéknek a számnak kell lennie `urn:journeyrecorder:applicationinsights` . További információkért lásd: [naplók gyűjtése](troubleshoot-with-application-insights.md). |


Az alábbi példa bemutatja, hogyan adhatja meg a **TrustFrameworkPolicy** elemet:

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

## <a name="inheritance-model"></a>Öröklési modell

Az ilyen típusú házirend-fájlokat általában a felhasználói úton használják:

- A definíciók többségét tartalmazó **alapfájl.** Ha segítségre van szüksége a házirendek hibaelhárításához és hosszú távú karbantartásához, akkor azt javasoljuk, hogy módosítsa a fájl minimális számát.
- Egy **kiterjesztési** fájl, amely a bérlő egyedi konfigurációs módosításait tárolja. Ez a házirend-fájl az alapfájlból származik. Ezzel a fájllal új funkciókat adhat hozzá, vagy felülbírálhatja a meglévő funkciókat. Használja például ezt a fájlt, hogy összevonása az új identitás-szolgáltatókkal.
- Egy **függő entitást (RP)** tartalmazó fájl, amely egy, a függő entitás alkalmazás által közvetlenül meghívott, egyetlen feladat által irányított fájl, például a web-, mobil-vagy asztali alkalmazások. Az egyes egyedi feladatok, például a regisztrálás vagy a bejelentkezés, a jelszó-visszaállítás vagy a profil szerkesztése a saját RP-házirend fájlját igénylik. Ez a házirendfájl a kiterjesztések fájljából származik.

Egy függő entitás alkalmazás meghívja az RP-házirend fájlját egy adott feladat végrehajtásához. Például a bejelentkezési folyamat elindításához. A Azure AD B2C identitás-keretrendszerének keretrendszere hozzáadja az összes elemet az alapfájlból, majd a kiterjesztések fájlból, végül pedig az RP-házirend fájlból az aktuális szabályzat összeállításához. Az RP-fájl azonos típusú és nevű elemei felülbírálják a bővítmények ezen elemeit, és a bővítmények felülbírálási alapjait. Az alábbi ábrán a házirend-fájlok és a függő entitások alkalmazásai közötti kapcsolat látható.

![A megbízhatósági keretrendszer házirendjének öröklési modelljét bemutató ábra](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Az öröklési modell a következő:

- A szülő házirend és a gyermek házirend ugyanahhoz a sémához tartozik.
- A gyermek házirend bármilyen szinten örökölhető a szülő házirendtől, és új elemek hozzáadásával bővíthető.
- A szintek száma nincs korlátozva.

További információ: Ismerkedés [az egyéni szabályzatokkal](custom-policy-get-started.md).

## <a name="base-policy"></a>Alapházirend

Ha egy házirendet egy másik házirendből kíván örökölni, egy **BasePolicy** elemet kell deklarálnia a **TrustFrameworkPolicy** elem alatt. A **BasePolicy** elem azon alapházirendre hivatkozik, amelyből ez a házirend származik.

A **BasePolicy** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | --------|
| TenantId | 1:1 | A Azure AD B2C bérlő azonosítója. |
| PolicyId | 1:1 | A szülő házirend azonosítója. |


Az alábbi példa azt szemlélteti, hogyan lehet alapszintű szabályzatot megadni. Ez a **B2C_1A_TrustFrameworkExtensions** házirend a **B2C_1A_TrustFrameworkBase** házirendből származik.

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

## <a name="policy-execution"></a>Szabályzat-végrehajtás

A függő entitások, például a web-, mobil-vagy asztali alkalmazások meghívja a [függő entitás (RP) házirendjét](relyingparty.md). Az RP-házirend fájl egy adott feladatot hajt végre, például bejelentkezik, jelszó alaphelyzetbe állítása vagy profil szerkesztése. Az RP-szabályzat konfigurálja azon jogcímek listáját, amelyeket a függő entitás alkalmazás a kiállított jogkivonat részeként kap. Több alkalmazás is használhatja ugyanazt a házirendet. Minden alkalmazás ugyanazt a jogkivonatot fogadja a jogcímekkel, és a felhasználó ugyanazon a felhasználói úton halad. Egyetlen alkalmazás több szabályzatot is használhat.

Az RP-szabályzat fájljában meg kell adnia a **DefaultUserJourney** elemet, amely a [UserJourney](userjourneys.md)mutat. A felhasználói út általában az alap-vagy a kiterjesztési szabályzatban van definiálva.

B2C_1A_signup_signin házirend:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase vagy B2C_1A_TrustFrameworkExtensionPolicy:

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

A felhasználói út határozza meg, hogy a felhasználó milyen üzleti logikát mutat be. Minden felhasználói út olyan előkészítési lépések összessége, amelyek számos műveletet hajtanak végre a hitelesítés és az adatgyűjtés szempontjából.

Az [alapszintű csomagban](custom-policy-get-started.md#custom-policy-starter-pack) található **SocialAndLocalAccounts** házirend-fájl tartalmazza a SignUpOrSignIn, a ProfileEdit, a PasswordReset felhasználói útvonalakat. További felhasználói útvonalakat is hozzáadhat más forgatókönyvekhez, például egy e-mail-cím módosításához vagy egy közösségi fiók összekapcsolásához.

A előkészítési lépések egy [technikai profilt](technicalprofiles.md)hívhatnak meg. A technikai profil olyan keretrendszert biztosít, amely beépített mechanizmussal rendelkezik a különböző típusú felekkel való kommunikációhoz. A technikai profil például elvégezheti ezeket a műveleteket többek között:

- Felhasználói élmény megjelenítése.
- Lehetővé teszi a felhasználók számára a közösségi vagy vállalati fiókkal való bejelentkezést, például a Facebook, a Microsoft-fiók, a Google, a Salesforce vagy más identitás-szolgáltató használatával.
- Az MFA telefonos ellenőrzésének beállítása.
- Adatok olvasása és írása egy Azure AD B2C Identity Store-ból és-ból.
- Egy egyéni REST API-szolgáltatás meghívása.

![A házirend végrehajtási folyamatát ábrázoló diagram](./media/trustframeworkpolicy/custom-policy-execution.png)

 A **TrustFrameworkPolicy** elem a következő elemeket tartalmazza:

- A fent megadott BasePolicy
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
