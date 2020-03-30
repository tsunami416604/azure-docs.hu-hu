---
title: Az Application Insights technikai profiljának definiálása egyéni szabályzatban
titleSuffix: Azure AD B2C
description: Az Application Insights technikai profiljának definiálása egyéni szabályzatban az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f50373b0841b7626bc405f121015c15ae1587a97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80108574"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Az Application Insights technikai profiljának definiálása egy Azure AD B2C egyéni szabályzatban

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) támogatja az eseményadatok közvetlen küldését az [Application Insightsnak](../azure-monitor/app/app-insights-overview.md) az Azure AD B2C-nek biztosított instrumentációs kulcs használatával.  Az Application Insights technikai profiljával részletes és személyre szabott eseménynaplókat kaphat a felhasználói utakhoz:

* Betekintést nyerhet a felhasználók viselkedéséről.
* Saját szabályzatok elhárítása fejlesztés vagy éles környezetben.
* A teljesítmény mérése.
* Értesítéseket hozhat létre az Application Insightsból.


## <a name="protocol"></a>Protocol (Protokoll)

A **Protokoll** elem **Name** attribútumát a `Proprietary`parancsra kell állítani. A **kezelő** attribútumának tartalmaznia kell az Azure AD B2C által az Application Insightshoz használt protokollkezelő szerelvény teljesen minősített nevét:`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

A következő példa bemutatja a közös Application Insights technikai profil. Az Application Insights egyéb technikai profiljai közé tartozik az AzureInsights-Common a konfiguráció jának kihasználásához.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Bemeneti jogcímek

Az **InputClaims** elem az Application Insightsnak küldandó jogcímek listáját tartalmazza. A jogcím nevét is leképezheti egy olyan névre, amelyet az Application Insightsban szeretne megjeleníteni. A következő példa bemutatja, hogyan küldhet telemetriák at Application Insights. Az esemény tulajdonságai a szintaxison keresztül kerülnek hozzáadásra, `{property:NAME}`ahol a NAME tulajdonság hozzáadódik az eseményhez. A DefaultValue lehet statikus érték vagy a támogatott [jogcímfeloldók](claim-resolver-overview.md)egyike által feloldott érték.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

Az **InputClaimsTransformations** elem tartalmazhat **InputClaimsTransformation** elemek gyűjteményét, amelyek a bemeneti jogcímek módosítására vagy újak létrehozására szolgálnak az Application Insights nak való küldés előtt.

## <a name="persist-claims"></a>Követelések megőrzése

A PersistedClaims elem nem használatos.

## <a name="output-claims"></a>Kimeneti jogcímek

A OutputClaims és a OutputClaimsTransformations elemek nem használatosak.

## <a name="cryptographic-keys"></a>Kriptográfiai kulcsok

A Kriptográfiai kulcsok elem nem használatos.


## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| InstrumentationKey (Műszerezési kulcs)| Igen | Az Application Insights [instrumentation kulcs](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key), amely az események naplózására szolgál. | 
| DeveloperMode| Nem | Logikai érték, amely azt jelzi, hogy engedélyezve van-e a fejlesztői mód. Lehetséges `true` értékek: `false` vagy (alapértelmezett). Ez a metaadat határozza meg az események pufferelését. Minimális eseménymennyiséggel rendelkező fejlesztői módban a fejlesztői mód engedélyezése esetén az események azonnal elküldésre kerülnek az Application Insightsba.|  
|DisableTelemetria |Nem |Logikai érték, amely azt jelzi, hogy a telemetriai adatokat engedélyezni kell-e vagy sem. Lehetséges `true` értékek: `false` vagy (alapértelmezett).| 


## <a name="next-steps"></a>További lépések

- [Application Insights-erőforrás létrehozása](../azure-monitor/app/create-new-resource.md)
- Megtudhatja, hogy [miként követheti nyomon a felhasználói viselkedést az Azure Active Directory B2C szolgáltatásában az Application Insights használatával](analytics-with-application-insights.md)
