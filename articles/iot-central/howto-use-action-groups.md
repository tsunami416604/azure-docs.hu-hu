---
title: Több művelet futtatása Azure IoT Central-szabályból | Microsoft Docs
description: Futtasson több műveletet egyetlen IoT Central szabályból, és hozzon létre újrafelhasználható műveleteket, amelyek több szabályból is futtathatók.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: ad5f660ff72eceecbb6db2e9557b023ed2c6ea99
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875813"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Több művelet csoportosítása egy vagy több szabályból való futtatáshoz

*Ez a cikk az építők és a rendszergazdákra vonatkozik.*

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Az Azure IoT Centralban szabályokat hozhat létre a műveletek futtatásához, ha egy feltétel teljesül. A szabályok az eszköz telemetria vagy eseményein alapulnak. Értesítheti például az operátort, ha az eszköz hőmérséklete meghaladja a küszöbértéket. Ez a cikk azt ismerteti, hogyan használhatók [Azure monitor](../azure-monitor/overview.md) *műveleti csoportok* több művelet egy IoT Central-szabályhoz való csatolásához. A műveleti csoportokat több szabályhoz is csatolhatja. A [műveleti csoport](../azure-monitor/platform/action-groups.md) az Azure-előfizetés tulajdonosa által meghatározott értesítési beállítások gyűjteménye.

## <a name="prerequisites"></a>Előfeltételek

- A Pay-As-You-Go application
- Azure-fiók és-előfizetés Azure Monitor műveleti csoportok létrehozásához és kezeléséhez

## <a name="create-action-groups"></a>Műveletcsoportok létrehozása

[Létrehozhat és kezelhet műveleti csoportokat a Azure Portal](../azure-monitor/platform/action-groups.md) vagy egy [Azure Resource Manager sablonnal](../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Egy műveleti csoport a következőket teheti:

- Értesítéseket küldhet, például e-mailben, SMS-ben vagy hanghívást készíthet.
- Futtasson egy olyan műveletet, mint például egy webhook hívása.

Az alábbi képernyőfelvételen egy olyan műveleti csoport látható, amely e-mailben és SMS-értesítéseket küld, és egy webhookot hív meg:

![Műveletcsoport](media/howto-use-action-groups/actiongroup.png)

Ha IoT Central szabályban szeretne műveleti csoportot használni, a műveleti csoportnak ugyanabban az Azure-előfizetésben kell lennie, mint a IoT Central alkalmazásnak.

## <a name="use-an-action-group"></a>Műveleti csoport használata

Ha egy műveleti csoportot szeretne használni a IoT Central alkalmazásban, először hozzon létre egy telemetria vagy egy eseményvezérelt szabályt. Ha műveletet ad hozzá a szabályhoz, válassza ki **Azure monitor műveleti csoportokat**:

![Művelet kiválasztása](media/howto-use-action-groups/chooseaction.png)

Válasszon egy műveleti csoportot az Azure-előfizetésből:

![Műveleti csoport kiválasztása](media/howto-use-action-groups/chooseactiongroup.png)

Kattintson a **Mentés** gombra. A műveleti csoport most megjelenik a szabály indításakor futtatandó műveletek listájában:

![Mentett műveleti csoport](media/howto-use-action-groups/savedactiongroup.png)

A következő táblázat összefoglalja a támogatott tevékenységtípusok számára továbbított adatokat:

| Művelettípus | Kimeneti formátum |
| ----------- | -------------- |
| Email       | Standard IoT Central e-mail sablon |
| SMS         | Azure IoT Central riasztás: $ {applicationName}-"$ {ruleName}" aktiválva: "$ {deviceName}" a $ {triggerDate} $ {triggerTime} |
| Hang       | Azure I. O. T központi riasztás: a (z) "$ {ruleName}" szabály aktiválva lett a (z) "$ {deviceName}" eszközön a $ {triggerDate} $ {triggerTime} alkalmazásban a $ {applicationName} alkalmazásban. |
| Webhook     | { "schemaId" : "AzureIoTCentralRuleWebhook", "adatok": {[normál webhook hasznos](#payload)adat}} |

A következő szöveg egy példa SMS-üzenetet küld egy műveleti csoportból:

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a>A következő JSON egy példát mutat be a webhook művelet hasznos adataira:

```json
{
  "schemaId":"AzureIoTCentralRuleWebhook",
  "data":{
    "id":"97ae27c4-17c5-4e13-9248-65c7a2c57a1b",
    "timestamp":"2019-03-20T10:53:17.059Z",
    "rule":{
      "id":"031b660e-528d-47bb-b33d-f1158d7e31bf",
      "name":"Low pressure alert",
      "enabled":true,
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      }
    },
    "device":{
      "id":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "name":"Refrigerator 2",
      "simulated":true,
      "deviceId":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      },
      "measurements":{
        "telemetry":{
           "pressure":343.269190673549
        }
      }
    },
    "application":{
      "id":"8e70742b-0d5c-4a1d-84f1-4dfd42e61c7b",
      "name":"Sample Contoso",
      "subdomain":"sample-contoso"
    }
  }
}
```

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan használhatók a műveleti csoportok szabályokkal, a javasolt következő lépés az [eszközök kezelésének](howto-manage-devices.md)megismerése.
