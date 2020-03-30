---
title: Intelligens észlelési szabályok beállításai – Azure Application Insights
description: Az Azure Application Insights intelligens észlelési szabályainak felügyeletének és konfigurálásának automatizálása az Azure Resource Manager-sablonokkal
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 7ca4df620739b2ab55b8ba986031cc48fe87f1fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294919"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Az Application Insights intelligens észlelési szabályainak kezelése az Azure Resource Manager-sablonok használatával

Az Application Insights intelligens észlelési szabályai az [Azure Resource Manager-sablonok](../../azure-resource-manager/templates/template-syntax.md)használatával kezelhetők és konfigurálhatók.
Ez a módszer akkor használható, ha új Application Insights-erőforrásokat telepít az Azure Resource Manager automatizálásával, vagy a meglévő erőforrások beállításainak módosításához.

## <a name="smart-detection-rule-configuration"></a>Intelligens észlelési szabály konfigurációja

Az intelligens detektálási szabályokhoz a következő beállításokat konfigurálhatja:
- Ha a szabály engedélyezve van (az alapértelmezett **érték igaz**.)
- Ha e-maileket kell küldeni az előfizetés [figyelési olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) és [figyelési közreműködői](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) szerepköreihez társított felhasználóknak, ha észlelést talál (az alapértelmezett **érték igaz.)**
- Minden további e-mail címzettek, akik értesítést kapnak, ha egy észlelést talál.
    -  Az e-mail konfiguráció nem érhető el az _előnézetként_megjelölt intelligens észlelési szabályokhoz.

A szabálybeállítások Azure Resource Manageren keresztüli konfigurálásának engedélyezéséhez az intelligens észlelési szabály konfigurációja mostantól elérhető az Application Insights erőforráson belül, **proaktívészlelési konfigurációk**néven.
A maximális rugalmasság érdekében minden intelligens észlelési szabály egyedi értesítési beállításokkal konfigurálható.

## <a name="examples"></a>Példák

Az alábbiakban néhány példa bemutatja, hogyan konfigurálhatja az intelligens észlelési szabályok beállításait az Azure Resource Manager-sablonok használatával.
Minden minta egy _"myApplication"_ nevű Application Insights-erőforrásra és a "hosszú függőségi időtartam intelligens észlelési szabály",amely belső neve _"longdependencyduration"_.
Győződjön meg arról, hogy cserélje le az Application Insights erőforrás nevét, és adja meg a megfelelő intelligens észlelési szabály belső nevét. Tekintse meg az alábbi táblázatban a megfelelő belső Azure Resource Manager-nevek listáját az egyes intelligens észlelési szabályok.

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

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Intelligens észlelési szabály e-mail értesítéseinek küldésének letiltása

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

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>További e-mail címzettek hozzáadása intelligens észlelési szabályhoz

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


## <a name="smart-detection-rule-names"></a>Intelligens észlelési szabályok nevei

Az alábbiakban egy táblázatot az intelligens észlelési szabály nevei jelennek meg a portálon, valamint a belső nevek, amelyeket az Azure Resource Manager sablonban kell használni.

> [!NOTE]
> Az _előnézetként_ megjelölt intelligens észlelési szabályok nem támogatják az e-mailes értesítéseket. Ezért csak az _engedélyezett_ tulajdonságot állíthatja be ezekhez a szabályokhoz. 

| Az Azure Portal szabályneve | Belső név
|:---|:---|
| Lassú oldalbetöltési idő | slowpageloadtime (lassúoldalbetöltési idő) |
| Lassú szerver válaszidő | slowserverresponsetime (lassúkiszolgáló-elhárítási idő) |
| Hosszú függőségi időtartam | longdependencyduration (longdependencyduration) |
| Romlás a kiszolgáló válaszidején | degradationinserverresponsetime |
| Lebomlás a függőség időtartamában | degradációsfüggőségi időtartam |
| Lebomlás a nyomkövetési súlyossági arányban (előnézet) | extension_traceseveritydetector |
| A kivétel mennyiségének rendellenes növekedése (előzetes verzió) | extension_exceptionchangeextension |
| Potenciális memóriavesztés észlelve (előzetes verzió) | extension_memoryleakextension |
| Potenciális biztonsági probléma észlelése (előzetes verzió) | extension_securityextensionspackage |
| Rendellenes növekedése a napi adatmennyiség (előzetes verzió) | extension_billingdatavolumedailyspikeextension |

### <a name="failure-anomalies-alert-rule"></a>Hibaanomáliák riasztási szabály

Ez az Azure Resource Manager-sablon bemutatja a 2 súlyosságú hibaanomáliák riasztási szabály konfigurálását. A hibaanomáliák riasztási szabály új verziója az új Azure riasztási platform része, és felváltja a [klasszikus riasztások kivonási folyamat](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/)részeként megszüntetett klasszikus verziót.

> [!NOTE]
> A hibaanomáliák globális szolgáltatás, ezért a szabály helye a globális helyen jön létre.

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
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
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
> Ez az Azure Resource Manager-sablon a hibaanomáliák riasztási szabály, és eltér a többi klasszikus intelligens észlelési szabályok ebben a cikkben leírt. Ha azt szeretné, hogy a hibaanomáliák manuálisan ez történik az Azure Monitor riasztások, míg az összes többi intelligens észlelési szabályok kezelése az intelligens észlelési ablaktáblában a felhasználói felület.

## <a name="next-steps"></a>Következő lépések

További információ az automatikus észlelésről:

- [Hibaanomáliák](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Memóriavesztés](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Teljesítményanomáliák](../../azure-monitor/app/proactive-performance-diagnostics.md)
