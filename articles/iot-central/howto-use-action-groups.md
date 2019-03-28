---
title: Több művelet futtatása az Azure IoT Central szabályból |} A Microsoft Docs
description: IoT-központ egyetlen szabályról több művelet futtatása, és futtathatja a több szabály műveletek újrafelhasználható csoportok létrehozása.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 857d747fa691d1ec2b386d5931a7edea08b7e609
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522938"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Több művelet futtatható egy vagy több szabályt a csoporthoz

*Ez a cikk létrehozói és a rendszergazdák vonatkozik.*

Az Azure IoT Central szabályokat kell létrehozni egy feltétel teljesülése esetén a műveletek futtatására. Szabályok eszköz telemetriai adatok vagy események alapulnak. Például az operátort értesíteni, amikor egy eszköz hőmérséklete meghaladja a küszöbértéket. Ez a cikk ismerteti, hogyan használható [Azure Monitor](../azure-monitor/overview.md) *Műveletcsoportok* több művelet csatlakoztatása az IoT-központ szabály. Műveletcsoport csatlakoztathat több szabály. Egy [műveletcsoport](../azure-monitor/platform/action-groups.md) az Azure-előfizetés tulajdonosa által megadott értesítési beállítások gyűjteménye.

## <a name="prerequisites"></a>Előfeltételek

- A Pay-As-You-Go application
- Azure-fiókkal és -előfizetés létrehozása és kezelése az Azure Monitor Műveletcsoportok

## <a name="create-action-groups"></a>Műveletcsoportok létrehozása

Is [létrehozása és kezelése az Azure Portalon Műveletcsoportok](../azure-monitor/platform/action-groups.md) és a egy [Azure Resource Manager-sablon](../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Műveletcsoport a következőket teheti:

- Például egy e-mailt, az SMS-értesítések küldése vagy hangos hívás.
- Futtassa a művelet, például egy webhook hívása.

A következő képernyőképen látható, amely elküldi az e-mailek és SMS-értesítések és a egy webhookot hív műveletcsoport:

![Műveletcsoport](media/howto-use-action-groups/actiongroup.png)

Műveletcsoport egy IoT-központ szabályt használ, a műveletcsoport ugyanabban az előfizetésben az Azure IoT központi alkalmazás kell lennie.

## <a name="use-an-action-group"></a>Műveletcsoport használata

Műveletcsoport használhatja az IoT-központ alkalmazásban, először létre kell hoznia egy telemetria- vagy event szabályt. Amikor a szabály hozzá egy műveletet, válassza ki a **Azure Monitor Műveletcsoportok**:

![Művelet kiválasztása](media/howto-use-action-groups/chooseaction.png)

Műveletcsoport kiválasztása az Azure-előfizetésből:

![Műveletcsoport kiválasztása](media/howto-use-action-groups/chooseactiongroup.png)

Kattintson a **Mentés** gombra. A műveletcsoport most már a futtatását, amikor a szabály akkor lesz kiváltva műveletek listájában jelenik meg:

![Műveleti csoport mentése](media/howto-use-action-groups/savedactiongroup.png)

A következő táblázat összefoglalja a támogatott művelettípusok küldött adatok:

| Művelettípus | Kimeneti formátum |
| ----------- | -------------- |
| E-mail       | Standard szintű IoT-központ e-mail-sablon |
| SMS         | Azure IoT Central alert: ${applicationName} - "${ruleName}" triggered on "${deviceName}" at ${triggerDate} ${triggerTime} |
| Hang       | Azure I.O.T Central alert: rule "${ruleName}" triggered on device "${deviceName}" at ${triggerDate} ${triggerTime}, in application ${applicationName} |
| Webhook     | {"schemaId": "AzureIoTCentralRuleWebhook", "data": {[regular webhook payload](#payload)} } |

A következő szöveg egy példa a műveletcsoport SMS-üzenet:

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a> A következő JSON-példa webhook művelet hasznos adat látható:

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

Most, hogy megismerte a Műveletcsoportok használata a szabályok, a javasolt következő lépésre megtudhatja, hogyan [az eszközök kezeléséhez](howto-manage-devices.md).
