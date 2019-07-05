---
title: Azure Application Insights intelligens észlelési szabály beállításainak konfigurálása az Azure Resource Manager-sablonokkal |} A Microsoft Docs
description: Felügyeleti és Azure Resource Manager-sablonok az Azure Application Insights intelligens detektálási szabályok konfigurálásának automatizálása
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
ms.openlocfilehash: 6bb89eec0b4905e101bed87d3d3fc617dec589e0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477861"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Azure Resource Manager-sablonok használatával az Application Insights intelligens detektálási szabályok kezelése

Az Application Insights intelligens detektálási szabályok kezelhetők, és konfiguráltak [Azure Resource Manager-sablonok](../../azure-resource-manager/resource-group-authoring-templates.md).
Ez a módszer használható, az Azure Resource Manager automation, illetve a meglévő erőforrások beállítások módosítása új Application Insights-erőforrások üzembe helyezésekor.

## <a name="smart-detection-rule-configuration"></a>Az intelligens észlelési szabály konfigurációja

A következő értékeket a intelligens detektálási szabályra konfigurálhatja:
- Ha a szabály engedélyezve van (az alapértelmezett érték **igaz**.)
- Ha e-maileket küldjön el az előfizetéshez társított felhasználók [Monitoring Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) és [közreműködő figyelése](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) szerepkörök, amikor egy észlelési talál (az alapértelmezett érték **igaz**.)
- Bármilyen e-mail további címzettjei ki kell értesítést kaphat, ha egy észlelési található.
    -  E-mail-konfiguráció nem érhető el, az intelligens detektálási szabályok megjelölve _előzetes_.

Ahhoz, hogy a szabály konfigurálása Azure Resource Manageren keresztül, az intelligens észlelési szabály konfigurációja már elérhető az Application Insights-erőforrást, nevű belül belső erőforrásként **ProactiveDetectionConfigs**.
Maximális rugalmassággal egyedi értesítési beállításokat minden intelligens detektálási szabályra is konfigurálhatók.

## 

## <a name="examples"></a>Példák

Az alábbiakban néhány példát bemutató az Azure Resource Manager-sablonok használatával intelligens detektálási szabályok konfigurálásához.
Az összes minta tekintse meg az Application Insights-erőforrás nevű _"myApplication"_ , és az "hosszú függőségi időtartam intelligens detektálási szabályra", amely belsőleg nevű _"longdependencyduration"_ .
Ellenőrizze, hogy cserélje le az Application Insights-erőforrás nevét, és adja meg a megfelelő intelligens észlelés a szabály belső nevét. Ellenőrizze a megfelelő belső Azure Resource Manager nevét az egyes intelligens detektálási szabályok listáját az alábbi táblázatot.

### <a name="disable-a-smart-detection-rule"></a>Intelligens detektálási szabályra letiltása

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

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Intelligens detektálási szabályra küldését e-mail-értesítések letiltása

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

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Adjon hozzá egy intelligens detektálási szabályra e-mail további címzettjei

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

### <a name="failure-anomalies-v2-non-classic-alert-rule"></a>Sikertelen rendellenességek v2 (nem klasszikus) végrehajtásának riasztási szabálya

Az Azure Resource Manager-sablon azt mutatja be, rendellenes hibák v2 riasztási szabály konfigurálása egy 2 súlyosságát. Az új verzió a rendellenes hibák riasztási szabály része az új Azure-platform riasztások, és lecseréli a klasszikus verzióra, amely részeként kivezetjük a [klasszikus riasztások használatból való kivonást egyaránt folyamat](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
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
> Az Azure Resource Manager-sablon a rendellenes hibák v2 riasztási szabály az egyedi, és eltér a hagyományos intelligens detektálási szabályok a cikkben ismertetett.   

## <a name="smart-detection-rule-names"></a>Intelligens detektálási szabályok neve

Alább az intelligens detektálási szabályok nevének táblázatát, mivel azok megjelennek a portálon, és azok belső nevekkel, amelyek az Azure Resource Manager-sablonban használandó.

> [!NOTE]
> Intelligens detektálási szabályok megjelölve _előzetes_ nem támogatják az e-mail-értesítéseket. Ezért csak akkor állíthat a _engedélyezve_ ezek a szabályok tulajdonsága. 

| Az Azure portal szabály neve | A belső név
|:---|:---|
| Lassú lapbetöltés | slowpageloadtime |
| Lassú kiszolgálói válaszidő | slowserverresponsetime |
| Hosszú függőségi időtartam | longdependencyduration |
| Kiszolgálói válaszidő romlása | degradationinserverresponsetime |
| A függőségi időtartam teljesítménycsökkenése | degradationindependencyduration |
| Csökkenés a következőben nyomkövetési súlyossági arány (előzetes verzió) | extension_traceseveritydetector |
| Rendellenes növekedése a kivételek mennyiségének (előzetes verzió) | extension_exceptionchangeextension |
| Potenciális memóriavesztést észlelt (előzetes verzió) | extension_memoryleakextension |
| Potenciális biztonsági problémát észlelt (előzetes verzió) | extension_securityextensionspackage |
| Rendellenes növekedése a napi adatmennyiség (előzetes verzió) | extension_billingdatavolumedailyspikeextension |

## <a name="next-steps"></a>További lépések

További információk automatikus észlelése:

- [Hiba-anomáliák](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Memóriavesztés](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Teljesítmény-anomáliák](../../azure-monitor/app/proactive-performance-diagnostics.md)
