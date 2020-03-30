---
title: TrustFrameworkPolicy – Azure Active Directory B2C | Microsoft dokumentumok
description: Adja meg az egyéni szabályzat TrustFrameworkPolicy elemét az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c964a7bde0b7db9357c73fc79d2df3170075fcc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186386"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az egyéni házirend egy vagy több XML-formátumú fájlként jelenik meg, amelyek hierarchikus láncban hivatkoznak egymásra. Az XML-elemek határozzák meg a házirend elemeit, például a jogcímséma, a jogcímátalakítások, a tartalomdefiníciók, a jogcímek szolgáltatói, a technikai profilok, a felhasználói utazás és a vezénylési lépések elemeit. Minden házirendfájl egy házirendfájl legfelső szintű **TrustFrameworkPolicy** elemében van definiálva.

```XML
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
| PolicySchemaVersion | Igen | A sémaverziója, amelyet a házirend végrehajtásához kell használni. Az értéket`0.3.0.0` |
| TenantObjectId azonosító | Nem | Az Azure Active Directory B2C (Azure AD B2C) bérlő egyedi objektumazonosítója. |
| TenantId | Igen | Annak a bérlőnek az egyedi azonosítója, amelyhez ez a házirend tartozik. |
| PolicyId | Igen | A házirend egyedi azonosítója. Ezt az azonosítót *B2C_1A_* előtaggal kell |
| PublicPolicyUri (PublicPolicyUri) | Igen | A házirend URI-ja, amely a bérlőazonosító és a házirend-azonosító kombinációja. |
| Telepítési mód | Nem | Lehetséges értékek: `Production` `Development`vagy . A `Production` az alapértelmezett formátum. Ezzel a tulajdonsággal hibakeresésre használhatja a házirendet. További információt a Naplók gyűjtése című [témakörben](troubleshoot-with-application-insights.md)talál. |
| UserJourneyRecorderVégpont | Nem | A **deploymentmode** beállításakor használt `Development`végpont. Az értéknek `urn:journeyrecorder:applicationinsights`. További információt a Naplók gyűjtése című [témakörben](troubleshoot-with-application-insights.md)talál. |


A következő példa bemutatja, hogyan adható meg a **TrustFrameworkPolicy** elem:

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

Az ilyen típusú házirendfájlokat általában egy felhasználói út során használják:

- **Alapfájl,** amely a legtöbb definíciót tartalmazza. A hibaelhárítás és a házirendek hosszú távú karbantartása érdekében ajánlott a fájl minimális számú módosítását végrehajtani.
- Egy **extensions** fájl, amely tartalmazza a bérlő egyedi konfigurációs módosításait. Ez a házirendfájl az alapfájlból származik. Ezzel a fájllal új funkciókat adhat hozzá, vagy felülbírálhatja a meglévő funkciókat. Ezzel a fájllal például új identitásszolgáltatókkal lehet összeegyeztetni.
- A **függő entitás (RP)** fájl, amely az egyetlen feladat-központú fájl, amelyet közvetlenül a függő entitás alkalmazás, például a webes, mobil- vagy asztali alkalmazások hívnak meg. Minden egyedi feladathoz, például a regisztrációhoz vagy a bejelentkezéshez, a jelszó alaphelyzetbe állításához vagy a profilszerkesztéshez saját RP-házirendfájl szükséges. Ez a házirendfájl a Bővítmények fájlból származik.

Egy függő entitás alkalmazás meghívja az RP-házirendfájlt egy adott feladat végrehajtásához. Például a bejelentkezési folyamat elindításához. Az Azure AD B2C identitáskezelési keretrendszere hozzáadja az összes elemet először az alapfájlból, majd a Extensions fájlból, végül pedig az RP-házirendfájlból az aktuális szabályzat összeállításához. Az RP-fájlban azonos típusú és nevű elemek felülbírálják a Bővítmények elemeit, a Bővítmények pedig felülbírálják az Alapelemet. Az alábbi ábra a házirendfájlok és a függő entitás alkalmazások közötti kapcsolatot mutatja be.

![A megbízhatósági keretrendszer házirendjének öröklődési modelljét bemutató diagram](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Az öröklési modell a következő:

- A szülő- és a gyermekházirend ugyanabból a sémából származik.
- A gyermekházirend bármely szinten örökölhet a szülőházirendből, és új elemek hozzáadásával kiterjesztheti azt.
- A szintek száma nincs korlátozva.

További információt az [Egyéni házirendek – Első lépések](custom-policy-get-started.md)című témakörben talál.

## <a name="base-policy"></a>Alapházirend

Ha egy házirendet egy másik házirendtől szeretne örökölni, a házirendfájl **TrustFrameworkPolicy** eleme alatt egy **BasePolicy** elemet kell deklarálni. A **BasePolicy** elem arra az alapházirendre mutató hivatkozás, amelyből ez a házirend származik.

A **BasePolicy** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | --------|
| TenantId | 1:1 | Az Azure AD B2C-bérlő azonosítója. |
| PolicyId | 1:1 | A szülőházirend azonosítója. |


A következő példa bemutatja, hogyan adhat meg egy alapházirendet. Ez **a B2C_1A_TrustFrameworkExtensions** házirend a **B2C_1A_TrustFrameworkBase** házirendből származik.

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

## <a name="policy-execution"></a>Házirend végrehajtása

A függő entitás alkalmazása, például egy webes, mobil- vagy asztali alkalmazás meghívja a [függő entitásra (RP) vonatkozó házirendet.](relyingparty.md) Az RP házirendfájl egy adott feladatot hajt végre, például bejelentkezik, alaphelyzetbe állít egy jelszót vagy szerkeszt egy profilt. Az RP-házirend konfigurálja a függő entitás alkalmazás által kapott jogcímek listáját a kiadott jogkivonat részeként. Több alkalmazás is használhatja ugyanazt a házirendet. Minden alkalmazás ugyanazt a jogkivonatot kapja a jogcímekkel, és a felhasználó ugyanazon a felhasználói úton halad keresztül. Egyetlen alkalmazás több szabályzatot is használhat.

Az RP házirendfájlban megadhatja a **DefaultUserJourney** elemet, amely a [UserJourney elemre](userjourneys.md)mutat. A felhasználói út általában a Bázis vagy a Bővítmények házirendben van meghatározva.

B2C_1A_signup_signin politika:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase vagy B2C_1A_TrustFrameworkExtensionPolicy:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

A felhasználói út határozza meg a felhasználó által keresztülmegy üzleti logikát. Minden felhasználói út vezénylési lépések készlete, amely műveletek sorozatát hajtja végre a hitelesítés és az információgyűjtés tekintetében.

Az [induló](custom-policy-get-started.md#custom-policy-starter-pack) **csomagSocialAndLocalAccounts** házirendfájlja tartalmazza a SignUpOrSignIn, ProfileEdit, PasswordReset felhasználói utakat. Más esetekhez további felhasználói utakat adhat hozzá, például egy e-mail cím módosításához vagy a közösségi fiók összekapcsolásához és leválasztását.

A vezénylési lépések [technikai profilt](technicalprofiles.md)hívhatnak. A technikai profil keretet biztosít a különböző típusú felekkel való kommunikációhoz. Egy technikai profil például többek között a következő műveleteket hajthatja végre:

- Felhasználói élmény renderelése.
- Lehetővé teszi a felhasználók számára, hogy közösségi vagy vállalati fiókkal, például Facebookkal, Microsoft-fiókkal, Google-lal, Salesforce-szal vagy bármely más identitásszolgáltatóval jelentkezzenek be.
- Telefonos ellenőrzés beállítása az MFA-hoz.
- Adatok olvasása és írása egy Azure AD B2C identitástárolóba és onnan.
- Hívjon meg egy egyéni restful API-szolgáltatást.

![A házirend végrehajtási folyamatát bemutató diagram](./media/trustframeworkpolicy/custom-policy-execution.png)

 A **TrustFrameworkPolicy** elem a következő elemeket tartalmazza:

- BasePolicy a fent meghatározottak szerint
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
