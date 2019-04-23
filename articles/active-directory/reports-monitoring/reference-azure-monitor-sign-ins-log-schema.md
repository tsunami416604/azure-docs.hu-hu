---
title: Az Azure Active Directory bejelentkezési napló séma az Azure Monitor |} A Microsoft Docs
description: Az Azure AD-be használható az Azure Monitor log-séma leírása
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
ms.openlocfilehash: a8ac6c56dca100ea9836158f46881c4eb12213e1
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001150"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Az Azure AD bejelentkezési naplók séma az Azure monitorban értelmezése

Ez a cikk ismerteti az Azure monitorban az Azure Active Directory (Azure AD) bejelentkezési napló séma. A legtöbb bejelentkezési kapcsolatos információ áll rendelkezésre a a *tulajdonságok* attribútuma a `records` objektum.


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


## <a name="field-descriptions"></a>Mező leírása

| Mező neve | Leírás |
|------------|-------------|
| Time | Dátuma és időpontja (UTC). |
| ResourceId | Ez az érték nem leképezett, és biztonságosan figyelmen kívül hagyhatja ezt a mezőt.  |
| OperationName | A bejelentkezések, az értéke mindig *bejelentkezési tevékenység*. |
| OperationVersion | Az ügyfél által kért REST API-verzió. |
| Category | A bejelentkezések, az értéke mindig *bejelentkezési*. | 
| TenantId | GUID, a naplók társított tenant. |
| ResultType | A bejelentkezési művelet eredménye lehet *sikeres* vagy *hiba*. | 
| ResultSignature | Tartalmazza a hiba kódja, ha bármely, a bejelentkezési művelet. |
| ResultDescription | A bejelentkezési művelet hiba leírását. |
| DurationMs |  Ez az érték nem leképezett, és biztonságosan figyelmen kívül hagyhatja ezt a mezőt.|
| CallerIpAddress | A kérést leadó ügyfél IP-címét. | 
| CorrelationId | A nem kötelező GUID, amely az ügyfél által átadott. Ez az érték segít összevetését ügyféloldali műveletek a kiszolgálóoldali műveletek, és ez akkor hasznos, ha követi nyomon, amelyek szolgáltatások naplók. |
| Identitás | Az identitás a tokenben szereplő leadta a kérelmet. Lehet, hogy egy felhasználói fiókot, rendszerfiók vagy egyszerű szolgáltatást. |
| Szint | Üzenet típusa biztosít. A naplózás, a rendszer mindig *tájékoztató*. |
| Location egység | Itt a bejelentkezési tevékenységek helyét. |
| Tulajdonságok | Az összes társított bejelentkezési tulajdonságok listája. További információkért lásd: [Microsoft Graph API-referencia](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). Ebben a sémában a bejelentkezési erőforrásként attribútum ugyanazokat a neveket használja az olvashatóság érdekében.

## <a name="next-steps"></a>További lépések

* [Az auditnaplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-audit-log-schema.md)
* [További információ az Azure diagnosztikai naplók](../../azure-monitor/platform/diagnostic-logs-overview.md)