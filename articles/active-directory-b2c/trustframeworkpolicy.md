---
title: TrustFrameworkPolicy – az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg a TrustFrameworkPolicy elem egyéni szabályzat az Azure Active Directory B2C-t.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 820e98b200071c95dc3d9506dd0cd0bbe86e2aae
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54849685"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Egyéni szabályzat jelenik meg egy vagy több XML-formátumú fájlt, amely hierarchikus láncban hivatkozzon egymásra. Az XML-elemeket adja meg a házirendet, például a jogcímek séma, a jogcímek átalakítása, tartalomdefiníciók, Jogcímszolgáltatók, technikai profilok, felhasználói interakciósorozat és vezénylési lépésekből elemei. Minden egyes házirendfájl van meghatározva a legfelső szintű **TrustFrameworkPolicy** házirendfájl eleme. 

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


A **TrustFrameworkPolicy** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Igen | A séma verziója, amely a szabályzat végrehajtható. Az értéknek kell lennie `0.3.0.0` |
| TenantObjectId | Nem | Az Azure Active Directory (Azure AD) B2C-bérlő objektum egyedi azonosítója. |
| TenantId | Igen | A bérlő, amelyhez tartozik a szabályzat egyedi azonosítója. |
| PolicyId | Igen | A szabályzat egyedi azonosítója. Ez az azonosító előtaggal kell ellátni *B2C_1A_* |
| PublicPolicyUri | Igen | Az URI-t, a házirend, ami a bérlő Azonosítóját és a házirend-azonosítóhoz. |
| DeploymentMode | Nem | A lehetséges értékek: `Production`, `Debugging`, vagy `Development`. A `Production` az alapértelmezett formátum. Ez a tulajdonság használatával hibakeresése a szabályzatot. További információkért lásd: [naplók összegyűjtése](active-directory-b2c-troubleshoot-custom.md). |
| UserJourneyRecorderEndpoint | Nem | A végpont, amely használni **DeploymentMode** értékre van állítva `Development`. Az értéknek kell lennie `urn:journeyrecorder:applicationinsights`. További információkért lásd: [naplók összegyűjtése](active-directory-b2c-troubleshoot-custom.md). |


Az alábbi példa bemutatja, hogyan adja meg a **TrustFrameworkPolicy** elem:

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

## <a name="inheritance-model"></a>Öröklés modell

Ilyen típusú szabályzat fájlok jellemzően egy felhasználói interakciósorozatban:

- A **alap** fájlt, amely a legtöbb kapcsolatos definíciókat tartalmazza. Hibaelhárítási és hosszú távú karbantartási szabályzat érdekében ajánlott, hogy ehhez a fájlhoz győződjön meg a minimálisan szükséges módosításokat.
- Egy **bővítmények** fájlt, amely tartalmazza a egyedi konfigurációs módosításokat a bérlő számára. Ez a házirend a fájl az alap fájlból származik. Ez a fájl használatával új funkciók hozzáadása és nem írhatók felül a meglévő funkciók. Például használja ezt a fájlt új identitásszolgáltatókkal összevonásához.
- A **függő entitás (RP)** feladat témájú az egyetlen fájl, amelyet közvetlenül a függő entitás alkalmazások, például a webes, mobil és asztali alkalmazások hív fájlt. Minden egyedi tevékenységhez regisztrálási vagy bejelentkezési, jelszó-visszaállítás, vagy a profil szerkesztheti, például a saját RP házirend fájl szükséges. Ez a házirend a fájl a bővítmények fájlból származik. 

Egy függő entitás alkalmazás meghívja a helyreállítási pont Védettként házirendfájl egy adott feladat végrehajtásához. Ha például a bejelentkezési folyamat elindítására. Azure AD B2C-ben az identitás-kezelőfelületi keretrendszer hozzáadja az elemek először az alap-fájlból, majd a bővítmények fájlból, és végül a helyreállítási pont Védettként házirend fájlból összegyűjtése, a jelenlegi házirend érvényben. Azonos típusú és nevét az RP-fájl elemeinek ezek a bővítmények lévő elemeket bírálják felül, és bővítmények felülbírálások talál. Az alábbi ábrán látható, a-fájlok és a függő entitások alkalmazásainak közötti kapcsolatot.

![Öröklés modell](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Az öröklés modell a következőképpen történik:

- A szülő-szabályzat és a gyermek házirend is annak ugyanazzal a sémával.
- A gyermek házirend, minden szinten is öröklik a szülő-szabályzat és kibővíthetők új elemekkel.
- A szintek száma nincs korlátozva van.

További információkért lásd: [egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md).

## <a name="base-policy"></a>Alap házirend

Egy másik szabályzat egy szabályzatot örökli a **BasePolicy** elem alatt kell deklarálni a **TrustFrameworkPolicy** elem a házirend-fájl. A **BasePolicy** elem eszköztáblára mutató hivatkozás a kiinduló házirendet, amelyről a szabályzat származtatott.  

A **BasePolicy** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | --------|
| TenantId | 1:1 | Az Azure AD B2C-bérlő azonosítója. |
| PolicyId | 1:1 | A szülő házirend azonosítója. |


Az alábbi példa bemutatja, hogyan adjon meg egy alap szabályzatot. Ez **B2C_1A_TrustFrameworkExtensions** házirend származik a **B2C_1A_TrustFrameworkBase** házirend. 

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

## <a name="policy-execution"></a>A végrehajtási házirend

Egy függő entitás alkalmazások, például egy webes, mobil és asztali alkalmazás hívja a [függő entitásonkénti (RP) házirend](relyingparty.md). A helyreállítási pont Védettként házirendfájl végrehajt egy adott feladat, például a bejelentkezés, a jelszó alaphelyzetbe állítása vagy a profil szerkesztését. Az RP-házirendet a függő entitás kap jogcímeket listáját a jogkivonatnak részeként konfigurálja. Több alkalmazás ugyanabban a szabályzatban használhatja. Minden alkalmazás ugyanezt a tokent a jogcímeket kap, és a felhasználó végighalad az ugyanazon felhasználói interakciósorozat. Egyetlen alkalmazás több szabályzatokat használhatja.

Az RP-házirend fájlban adja meg a **DefaultUserJourney** elem, amely a [UserJourney](userjourneys.md). A felhasználói út általában van megadva az alap- vagy bővítmények szabályzat.

B2C_1A_signup_signin policy:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase or B2C_1A_TrustFrameworkExtensionPolicy:

```XML
<UserJourneys>
  <UserJourney Id="SignOrSignIn">
  ...
```

Felhasználói út határozza meg, mi a felhasználó végighalad az üzleti logikát. Minden egyes felhasználói interakciósorozat egy vezénylési lépésekből, amely elvégzi a műveletek, hitelesítés és az információk gyűjtése tekintetében sorrendben. 

A **SocialAndLocalAccounts** házirend fájlt a [kezdőcsomag](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom#download-starter-pack-and-modify-policies) SignUpOrSignIn, ProfileEdit, PasswordReset felhasználói utak tartalmazza. Hozzáadhat egy másik forgatókönyvek, például megváltoztathatja az e-mail-címmel, további felhasználói utak hivatkozásra, és leválasztása a közösségi fiók vagy a jelszó alaphelyzetbe állítását. 

A vezénylési lépésekből meghívhatja egy [technikai profil](technicalprofiles.md). Technikai profil egy beépített mechanizmus, amellyel kommunikál a különböző típusú felek keretrendszert biztosít. Például a technikai profil hajthatja végre ezeket a műveleteket, többek között:

- A felhasználói környezet jelennek meg.
- Bejelentkezés közösségi vagy vállalati fiók, többek között a Facebookhoz, Microsoft-fiók, a Google, a Salesforce-hoz vagy bármely más identitásszolgáltató engedélyezése.
- Állítsa be az MFA telefonos ellenőrzés.
- Adatok olvasását és írását, és a egy Azure AD B2C identitáskezelési áruházból.
- Hívja meg egy egyéni REST-alapú API-szolgáltatás.

![A végrehajtási házirend](./media/trustframeworkpolicy/custom-policy-execution.png)

 A **TrustFrameworkPolicy** elem a következő elemeket tartalmazza:

- BasePolicy megadott fenti
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)

