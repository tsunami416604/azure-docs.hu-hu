---
title: Az Azure Application Insights intelligens észlelési szabály beállításainak konfigurálása Azure Resource Manager-sablonokkal | Microsoft Docs
description: Az Azure Application Insights intelligens észlelési szabályok kezelésének és konfigurálásának automatizálása Azure Resource Manager-sablonokkal
services: application-insights
documentationcenter: ''
author: harelbr
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: e7a54c2e207a27f3519375df09d0c930a92d52d6
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193718"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Application Insights intelligens észlelési szabályok kezelése Azure Resource Manager-sablonok használatával

A Application Insightsban található intelligens észlelési szabályok kezelhetők és konfigurálhatók [Azure Resource Manager sablonok](../../azure-resource-manager/resource-group-authoring-templates.md)használatával.
Ez a módszer akkor használható, ha új Application Insights erőforrásokat telepít Azure Resource Manager automatizálással, vagy a meglévő erőforrások beállításainak módosítására.

## <a name="smart-detection-rule-configuration"></a>Intelligens észlelési szabály konfigurációja

Az intelligens detektálási szabályokhoz a következő beállításokat konfigurálhatja:
- Ha a szabály engedélyezve van (az alapértelmezett érték **igaz**.)
- Ha az e-maileket el kell juttatni az előfizetés [figyelési olvasójának](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) felhasználóinak, illetve a közreműködői szerepkörök [figyelésére](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) , ha észlelés található (az alapértelmezett érték **igaz**.)
- Minden további e-mail-címzett, akinek értesítést kell kapnia, amikor észlelés található.
    -  Az e-mail-konfiguráció nem érhető el az _előzetes_verzióként megjelölt intelligens észlelési szabályokhoz.

Ha engedélyezni szeretné a szabályok beállításait a Azure Resource Manageron keresztül, az intelligens észlelési szabály konfigurációja mostantól belső erőforrásként is elérhető a **ProactiveDetectionConfigs**nevű Application Insights erőforráson belül.
A maximális rugalmasság érdekében minden egyes intelligens észlelési szabályt egyedi értesítési beállításokkal lehet konfigurálni.

## 

## <a name="examples"></a>Példák

Az alábbiakban néhány példát láthat arra, hogyan konfigurálhatja az intelligens észlelési szabályok beállításait Azure Resource Manager-sablonok használatával.
Az összes minta egy _"myApplication"_ nevű Application Insights erőforrásra, valamint a "hosszú függőségi időtartam intelligens észlelési szabályára" vonatkozik, amely belsőleg _"longdependencyduration"_ néven szerepel.
Ügyeljen rá, hogy cserélje le a Application Insights erőforrás nevét, és adja meg a vonatkozó intelligens észlelési szabály belső nevét. Az egyes intelligens észlelési szabályokhoz tartozó belső Azure Resource Manager-nevek listáját az alábbi táblázatban találja.

### <a name="disable-a-smart-detection-rule"></a>Intelligens észlelési szabály letiltása

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>E-mail-értesítések küldésének letiltása intelligens észlelési szabályhoz

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>További e-mail-címzettek hozzáadása intelligens észlelési szabályhoz

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```

### <a name="failure-anomalies-v2-non-classic-alert-rule"></a>Hiba anomália v2 (nem klasszikus) riasztási szabály

Ez a Azure Resource Manager-sablon azt mutatja be, hogy a hiba anomália v2 riasztási szabályt 2 értékkel. Az új Azure-riasztási platformnak ez a meghibásodási rendellenesség miatti riasztási szabálynak az új verziója, amely a klasszikus [riasztások nyugdíjazási folyamatának](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/)részeként kivont klasszikus verziót váltja fel.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Detects a spike in the failure rate of requests or dependencies",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> Ez a Azure Resource Manager sablon egyedi a hiba anomália v2 riasztási szabályhoz, és eltér a jelen cikkben ismertetett klasszikus intelligens észlelési szabályoktól.   

## <a name="smart-detection-rule-names"></a>Intelligens észlelési szabályok nevei

Az alábbi táblázat az intelligens észlelési szabályok neveit mutatja, ahogy azok a portálon jelennek meg, a belső nevükkel együtt, amelyeket a Azure Resource Manager sablonban kell használni.

> [!NOTE]
> Az előzetesként megjelölt intelligens észlelési szabályok nem támogatják az e-mailes értesítéseket. Ezért csak a szabályok _engedélyezve_ tulajdonságát állíthatja be. 

| Azure Portal szabály neve | A belső név
|:---|:---|
| Lassú lapbetöltés | slowpageloadtime |
| Lassú kiszolgálói válasz | slowserverresponsetime |
| Hosszú függőségi időtartam | longdependencyduration |
| Kiszolgáló válaszidejének romlása | degradationinserverresponsetime |
| Függőségi időtartam csökkenése | degradationindependencyduration |
| A nyomkövetés súlyossági arányának csökkenése (előzetes verzió) | extension_traceseveritydetector |
| Rendellenes növekedés a kivétel mennyisége (előzetes verzió) | extension_exceptionchangeextension |
| Potenciális memóriavesztés észlelhető (előzetes verzió) | extension_memoryleakextension |
| Lehetséges biztonsági probléma észlelhető (előzetes verzió) | extension_securityextensionspackage |
| A napi adatmennyiség rendellenes növekedése (előzetes verzió) | extension_billingdatavolumedailyspikeextension |

## <a name="next-steps"></a>További lépések

További információ az automatikus észlelésről:

- [Hiba-anomáliák](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Memóriavesztés](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Teljesítmény-anomáliák](../../azure-monitor/app/proactive-performance-diagnostics.md)
