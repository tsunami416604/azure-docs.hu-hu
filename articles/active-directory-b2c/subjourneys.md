---
title: Alútvonalak a Azure Active Directory B2Cban | Microsoft Docs
description: Egy egyéni házirend Alútvonal elemének megadásával Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6609dabe9bd507751bd131a4effe24295e2aac04
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952447"
---
# <a name="subjourneys"></a>Alútvonalak

A bevezetések segítségével rendezheti és egyszerűsítheti a folyamat lépéseinek menetét a felhasználói úton. A [felhasználói utazások](userjourneys.md) olyan explicit elérési utakat határoznak meg, amelyeken keresztül a szabályzat lehetővé teszi, hogy a függő entitások egy felhasználó számára megfelelő jogcímeket szerezzenek. A felhasználó ezen elérési utakon keresztül kéri le a függő entitásnak benyújtandó jogcímeket. Más szóval a felhasználói útvonalak határozzák meg azt az üzleti logikát, amit a végfelhasználó a Azure AD B2C identitás-kezelési keretrendszere feldolgozza a kérést. A felhasználói út olyan összehangoló sorozatot jelöl, amelyet egy sikeres tranzakcióhoz kell követni. Egy előkészítési lépés [ClaimsExchange](userjourneys.md#claimsexchanges) eleme egyetlen [technikai profilhoz](technical-profiles-overview.md) van kötve, amely végre van hajtva.

A bevezetési lépések csoportosítása a felhasználói úton belüli bármely ponton meghívható. A bevezetések használatával újrafelhasználható lépéseket hozhat létre, vagy megvalósíthatja az üzleti logikát jobban reprezentáló elágazásokat.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="user-journey-branching"></a>Felhasználói utazás elágazása

A bevezetések úgy viselkednek, mint a [felhasználói utazások](userjourneys.md), mivel mindkettő olyan előkészítési sorozatot képvisel, amelyet egy sikeres tranzakcióhoz kell követni. A felhasználói útvonalak saját maguk is meghívhatók, és a végrehajtáshoz SendClaims lépés szükséges. A bevezetések a felhasználói utazások összetevői, és nem hívhatók meg egymástól függetlenül, és a rendszer mindig egy felhasználói útra hívja meg őket.

Az elágazás legfontosabb összetevője, hogy lehetővé tegye az üzleti logika jobb feldolgozását a felhasználói úton. A közös előkészítési lépések különálló darabokra vannak csoportosítva, amelyeket külön kell meghívni. A bevezetéssel leegyszerűsíthető egy olyan út, ahol több összekapcsolási lépés is összekapcsolható (azonos előfeltételekkel). A rendszer csak a felhasználói útra hívja meg a bevezetést, ezért nem hívhat meg másik alútvonalat.

Kétféle Alútvonal létezik:

- **Call** -visszaadja a vezérlőt a hívónak. A rendszer végrehajtja a bevezetést, majd a vezérlést a felhasználói útra jelenleg végrehajtó előkészítési lépéshez adja vissza.
- **Átvitel** – átirányítja a vezérlést a bevezetésbe (visszafordíthatatlan elágazás). A alútvonalnak rendelkeznie kell egy SendClaims lépéssel, hogy visszaadja a jogcímeket a függő entitás alkalmazásának.

## <a name="example-scenarios"></a>Példaforgatókönyvek

### <a name="call-subjourney"></a>Alútvonal hívása

A hívási Alútvonal az alábbi helyzetekben hasznos:

- Age kapuzás: az Age kapuzás esetében számos közös összetevő van a felhasználói útvonalak között. Az elágazás lehetővé teszi a közös elemek megosztható összetevőkbe való fordítását.  
- Szülői engedély: az elágazás lehetővé teszi a szülői beleegyező kialakítás kényelmét azáltal, hogy lehetővé teszi, hogy a felhasználótól érkező jogcímeket elérjük a kiskorú által végrehajtott, valamint a szülői beleegyező felhasználói útra, miután megtalálta, hogy a felhasználó beleegyezik. 
- Regisztráljon a bejelentkezéshez: Vegyünk egy olyan forgatókönyvet, amelyben egy felhasználó már létezik a címtárban, de elfelejtette, hogy valóban létrehozott egy fiókot. Előfordulhat, hogy a felhasználónak nem kell megadnia, hogy a megadott hitelesítő adatok már léteznek, és arra kényszerítse a felhasználót, hogy indítsa újra az utat, hogy a házirend képes legyen a feliratkozási folyamatból az adott felhasználó bejelentkezési folyamatára váltani.  

### <a name="transfer-subjourney"></a>Átvitel alútvonala

Az átvitel alútvonala az alábbi helyzetekben hasznos:

- Egy blokk oldal megjelenítése.
- A/B tesztelés, ha a kérést egy alútvonalra irányítja, hogy végrehajtsa és kiadja a jogkivonatot.

## <a name="adding-a-subjourney-element"></a>Alútvonali elem hozzáadása

A következő példa egy `SubJourney` típusú elemre `Call` mutat, amely visszaadja a vezérlést a felhasználói útra.

```xml
<SubJourneys>
  <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
    <OrchestrationSteps>
      <OrchestrationStep Order="1" Type="ClaimsExchange">
       <ClaimsExchanges>
        <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
       </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>conditionalAccessClaimCollection</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges>
          <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

Az alábbi példa egy `SubJourney` típusú elemet `Transfer` mutat be, amely visszaadja a tokent a függő entitás alkalmazásának.

```xml
<SubJourneys>
  <SubJourney Id="B" Type="Transfer">
    <OrchestrationSteps>
      ...
      <OrchestrationStep Order="5" Type="SendClaims">
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

### <a name="invoke-a-subjourney-step"></a>Alútvonali lépés meghívása

A rendszer egy új előkészítési lépést `InvokeSubJourney` használ egy Alútvonal végrehajtásához. A következő példa egy példát mutat be a jelen előkészítési lépés összes végrehajtási elemére.

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> Egy Alútvonal nem hívhat meg egy másik alútvonalat.

## <a name="components"></a>Összetevők

A szabályzat által támogatott alútvonalak definiálásához adjon hozzá egy **Alútvonal** elemet a házirendfájl legfelső szintű eleméhez.

A **alútvonalak** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Alútvonal | 1: n | Egy Alútvonal, amely meghatározza a teljes felhasználói folyamathoz szükséges összes szerkezetet. |

A **alútvonalak** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Id | Igen | A felhasználó által a szabályzat alútvonalára való hivatkozáshoz használható Alútvonal-azonosító. A [jelölt](userjourneys.md#journeylist) elem **SubJourneyReferenceId** eleme erre az attribútumra mutat. |
| Típus | Igen | Lehetséges értékek: `Call` , vagy `Transfer` . További információ: [felhasználói utazás elágazása](#user-journey-branching)|

A **Alútvonal** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1: n | Egy olyan előkészítési sorozatot, amelyet követni kell egy sikeres tranzakcióhoz. Minden felhasználói út a sorrendben végrehajtott előkészítési lépések rendezett listáját tartalmazza. Ha bármelyik lépés meghiúsul, a tranzakció meghiúsul. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

A előkészítési lépés elemeinek teljes listáját lásd: [UserJourneys](userjourneys.md).

## <a name="next-steps"></a>További lépések

[UserJourneys](userjourneys.md)