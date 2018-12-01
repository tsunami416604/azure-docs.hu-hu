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
ms.date: 07/19/2018
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: 4af34663b595f2c1f794c75adba4f759ffd4346a
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722768"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Azure Resource Manager-sablonok használatával az Application Insights intelligens detektálási szabályok kezelése

Az Application Insights intelligens detektálási szabályok kezelhetők, és konfiguráltak [Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md).
Ez a módszer használható, az Azure Resource Manager automation, illetve a meglévő erőforrások beállítások módosítása új Application Insights-erőforrások üzembe helyezésekor.

## <a name="smart-detection-rule-configuration"></a>Az intelligens észlelési szabály konfigurációja

A következő értékeket a intelligens detektálási szabályra konfigurálhatja:
- Ha a szabály engedélyezve van (az alapértelmezett érték **igaz**.)
- Ha e-maileket küldjön el az előfizetés-tulajdonost, közreműködőknek és olvasóknak, amikor egy észlelési található (az alapértelmezett érték **igaz**.)
- Bármilyen e-mail további címzettjei ki kell értesítést kaphat, ha egy észlelési található.

Ahhoz, hogy a szabály konfigurálása Azure Resource Manageren keresztül, az intelligens észlelési szabály konfigurációja már elérhető az Application Insights-erőforrást, nevű belül belső erőforrásként **ProactiveDetectionConfigs**.
Maximális rugalmassággal egyedi értesítési beállításokat minden intelligens detektálási szabályra is konfigurálhatók.

## <a name="examples"></a>Példák

Az alábbiakban néhány példát bemutató az Azure Resource Manager-sablonok használatával intelligens detektálási szabályok konfigurálásához.
Az összes minta tekintse meg az Application Insights-erőforrás nevű _"myApplication"_, és az "hosszú függőségi időtartam intelligens detektálási szabályra", amely belsőleg nevű _"longdependencyduration"_.
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

## <a name="smart-detection-rule-names"></a>Intelligens detektálási szabályok neve

Alább az intelligens detektálási szabályok nevének táblázatát, mivel azok megjelennek a portálon, és azok belső nevekkel, amelyek az Azure Resource Manager-sablonban használandó.

> [!NOTE]
> Intelligens detektálási szabályok megjelölve az előzetes verzió nem támogatja e-mail-értesítéseket. Ezért csak akkor állíthat ezek a szabályok engedélyezve tulajdonságát. 

| Az Azure portal szabály neve | A belső név
|:---|:---|
| Lassú lapbetöltés | slowpageloadtime |
| Lassú kiszolgálói válasz | slowserverresponsetime |
| Hosszú függőségi időtartam | longdependencyduration |
| Kiszolgáló válaszidejének romlása | degradationinserverresponsetime |
| Függőségi időtartam csökkenése | degradationindependencyduration |
| Csökkenés a következőben nyomkövetési súlyossági arány (előzetes verzió) | extension_traceseveritydetector |
| Rendellenes növekedése a kivételek mennyiségének (előzetes verzió) | extension_exceptionchangeextension |
| Potenciális memóriavesztést észlelt (előzetes verzió) | extension_memoryleakextension |
| Potenciális biztonsági problémát észlelt (előzetes verzió) | extension_securityextensionspackage |
| Erőforrás-kihasználtsági problémát észlelt (előzetes verzió) | extension_resourceutilizationextensionspackage |

## <a name="next-steps"></a>További lépések

További információk automatikus észlelése:

- [Hiba-anomáliák](app-insights-proactive-failure-diagnostics.md)
- [Memóriavesztés](app-insights-proactive-potential-memory-leak.md)
- [Teljesítmény-anomáliák](app-insights-proactive-performance-diagnostics.md)