---
title: Bejelentkezési naplóséma az Azure Monitorban | Microsoft dokumentumok
description: Az Azure AD-bejelentkezés az Azure Monitorban való használatra szánt naplóséma ismertetése
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5525f2f8ab4ef83ba9c3aeeff945bc9d875600d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748664"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Értelmezze az Azure AD bejelentkezési naplóinak sémáját az Azure Monitorban

Ez a cikk ismerteti az Azure Active Directory (Azure AD) bejelentkezési naplóséma az Azure Monitorban. A bejelentkezésekkel kapcsolatos legtöbb információ az `records` objektum *Tulajdonságok* attribútuma alatt található.


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>Mezőleírások

| Mező neve | Leírás |
|------------|-------------|
| Time | A dátum és az idő, UTC-ben. |
| ResourceId | Ez az érték nincs leképezve, és nyugodtan figyelmen kívül hagyhatja ezt a mezőt.  |
| OperationName | A bejelentkezések esetén ez az érték mindig *bejelentkezési tevékenység.* |
| OperationVersion verzió | Az ügyfél által kért REST API-verzió. |
| Kategória | A bejelentkezések esetén ez az érték mindig *SignIn.* | 
| TenantId | A bérlő i.GUID, amely a naplók társított. |
| ResultType (Eredménytípusa) | A bejelentkezési művelet eredménye lehet *Sikeres* vagy *Sikertelen.* | 
| ResultSignature | A bejelentkezési művelet hez adott hibakódot tartalmazza, ha van ilyen. |
| Eredményleírása | A bejelentkezési művelet hibaleírását tartalmazza. |
| riskDetail | riskDetail | A kockázatos felhasználók, bejelentkezés vagy kockázatészlelés adott állapotának "okát" adja. A lehetséges értékek `none` `adminGeneratedTemporaryPassword`a `userPerformedSecuredPasswordChange` `userPerformedSecuredPasswordReset`következők: `userPassedMFADrivenByRiskBasedPolicy`, `adminDismissedAllRiskForUser` `adminConfirmedSigninCompromised`, `unknownFutureValue` `adminConfirmedSigninSafe`, `aiConfirmedSigninSafe`, , , . Az `none` érték azt jelenti, hogy a felhasználón vagy a bejelentkezésen eddig nem történt művelet. <br>**Megjegyzés:** A tulajdonság részleteihez Azure AD Premium P2 licenc szükséges. Más licencek visszaad a . `hidden` |
| riskEventTypes | riskEventTypes | A bejelentkezéshez társított kockázatészlelési típusok. A `unlikelyTravel`lehetséges értékek: `anonymizedIPAddress` `maliciousIPAddress`, `unfamiliarFeatures` `malwareInfectedIPAddress`, `suspiciousIPAddress` `leakedCredentials`, `investigationsThreatIntelligence` `generic`, `unknownFutureValue`, , és . |
| riskLevelAggregated | riskLevel (kockázati szint) | Összesített kockázati szint. A lehetséges `none`értékek: `low` `medium`, `high` `hidden`, `unknownFutureValue`, , , és . Az `hidden` érték azt jelenti, hogy a felhasználó vagy a bejelentkezés nem volt engedélyezve az Azure AD Identity Protection. **Megjegyzés:** A tulajdonság részletei csak az Azure AD Premium P2-ügyfelek számára érhetők el. Az összes többi `hidden`ügyfél visszakerül . |
| riskLevelDuringSignIn | riskLevel (kockázati szint) | Kockázati szint a bejelentkezés során. A lehetséges `none`értékek: `low` `medium`, `high` `hidden`, `unknownFutureValue`, , , és . Az `hidden` érték azt jelenti, hogy a felhasználó vagy a bejelentkezés nem volt engedélyezve az Azure AD Identity Protection. **Megjegyzés:** A tulajdonság részletei csak az Azure AD Premium P2-ügyfelek számára érhetők el. Az összes többi `hidden`ügyfél visszakerül . |
| riskÁllam | riskÁllam | A kockázatos felhasználó állapotát, a bejelentkezést vagy a kockázatészlelést jelenti. A lehetséges értékek `none` `confirmedSafe`a `remediated` `dismissed`következők: `confirmedCompromised` `unknownFutureValue`, , , `atRisk`, , . |
| Időtartamok |  Ez az érték nincs leképezve, és nyugodtan figyelmen kívül hagyhatja ezt a mezőt. |
| CallerIpAddress cím | A kérést küldő ügyfél IP-címe. | 
| CorrelationId | Az ügyfél által átadott opcionális GUID. Ez az érték segíthet az ügyféloldali műveletek és a kiszolgálóoldali műveletek korrelációjában, és akkor hasznos, ha a szolgáltatásokra kiterjedő naplókat követ. |
| Identitás | A kérelem benyújtásakor bemutatott jogkivonat identitása. Ez lehet felhasználói fiók, rendszerfiók vagy egyszerű szolgáltatás. |
| Szint | Az üzenet típusát adja meg. Az audit, ez mindig *tájékoztató*. |
| Hely | A bejelentkezési tevékenység helyét adja meg. |
| Tulajdonságok | A bejelentkezésekhez társított összes tulajdonság listája. További információt a [Microsoft Graph API-útmutató című témakörben talál.](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin) Ez a séma ugyanazokat az attribútumneveket használja, mint a bejelentkezési erőforrás, az olvashatóság érdekében.

## <a name="next-steps"></a>További lépések

* [Naplónaplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-audit-log-schema.md)
* [További információ az Azure diagnosztikai naplóiról](../../azure-monitor/platform/platform-logs-overview.md)
