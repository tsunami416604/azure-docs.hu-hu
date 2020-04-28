---
title: Application Insights technikai profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Application Insights technikai profilt definiálhat egy egyéni házirendben Azure Active Directory B2Cban.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80108574"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Application Insights műszaki profil definiálása egy Azure AD B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) a Azure AD B2C számára biztosított rendszerállapot-kulcs használatával támogatja az esemény-adatok közvetlenül a [Application Insightsre](../azure-monitor/app/app-insights-overview.md) való küldését.  Application Insights technikai profillal részletes és testreszabott eseménynaplókat kaphat a felhasználói számára:

* A felhasználói viselkedés megállapítása.
* Saját házirendek fejlesztése a fejlesztésben vagy éles környezetben.
* Teljesítmény mérése.
* Értesítések létrehozása Application Insightsból.


## <a name="protocol"></a>Protocol (Protokoll)

A **protokoll** elem `Proprietary` **Name** attribútumát be kell állítani. A **kezelő** attribútumnak tartalmaznia kell a Azure AD B2C által Application Insights számára használt protokollkezelő-szerelvény teljes nevét:`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Az alábbi példa a közös Application Insights műszaki profilt mutatja be. Az egyéb Application Insights technikai profilok közé tartozik a AzureInsights, hogy kihasználja a konfigurációját.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** elem a Application Insights küldendő jogcímek listáját tartalmazza. A jogcím nevét egy olyan névre is leképezheti, amelyet szeretne Application Insights megjeleníteni. Az alábbi példa bemutatja, hogyan küldhet telemetriáiról a Application Insightsba. Egy esemény tulajdonságainak hozzáadása a szintaxissal `{property:NAME}`történik, ahol a Name tulajdonság az eseményhez kerül. A DefaultValue lehet statikus érték vagy olyan érték, amelyet a rendszer a támogatott [jogcím](claim-resolver-overview.md)-feloldások egyikével feloldott.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

A **InputClaimsTransformations** elem olyan **InputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a bemeneti jogcímek módosítására vagy újak előállítására szolgálnak, mielőtt elküldené őket a Application Insightsba.

## <a name="persist-claims"></a>Jogcímek fenntartása

A PersistedClaims elem nincs használatban.

## <a name="output-claims"></a>Kimeneti jogcímek

A OutputClaims és a OutputClaimsTransformations elemek nincsenek használatban.

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A CryptographicKeys elem nincs használatban.


## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| InstrumentationKey| Igen | A Application Insights kialakítási [kulcs](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key), amely az események naplózására szolgál majd. | 
| DeveloperMode| Nem | Logikai érték, amely jelzi, hogy engedélyezve van-e a fejlesztői mód. Lehetséges értékek: `true` vagy `false` (alapértelmezett). Ez a metaadatok azt vezérlik, hogy az események hogyan legyenek puffereltek. A minimális méretű eseménnyel rendelkező fejlesztési környezetekben a fejlesztői üzemmód engedélyezésekor a rendszer azonnal elvégzi az események küldését Application Insights.|  
|DisableTelemetry |Nem |Logikai érték, amely azt jelzi, hogy engedélyezni kell-e a telemetria. Lehetséges értékek: `true` vagy `false` (alapértelmezett).| 


## <a name="next-steps"></a>További lépések

- [Application Insights erőforrás létrehozása](../azure-monitor/app/create-new-resource.md)
- Megtudhatja, hogyan [követheti nyomon a felhasználói viselkedést Azure Active Directory B2C használatával Application Insights](analytics-with-application-insights.md)
