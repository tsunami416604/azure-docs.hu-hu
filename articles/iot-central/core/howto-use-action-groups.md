---
title: Több művelet futtatása egy Azure IoT Central szabályból | Microsoft dokumentumok
description: Egyetlen IoT Central-szabályból több műveletet futtathat, és több szabályból futtatható műveletcsoportokat hozhat létre.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
manager: philmea
ms.openlocfilehash: b447f44d0c95693e560fd5bbfbff8c8daeec964e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157687"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Több művelet csoportosítása egy vagy több szabályból való futtatáshoz

*Ez a cikk az építőkre és a rendszergazdákra vonatkozik.*

Az Azure IoT Centralban szabályokat hozhat létre a műveletek futtatásához, ha egy feltétel teljesül. A szabályok eszköztelemetriai adatokon vagy eseményeken alapulnak. Például értesítheti az operátort, ha egy eszköz hőmérséklete meghaladja a küszöbértéket. Ez a cikk ismerteti, hogyan azure [monitor](../../azure-monitor/overview.md) *műveletcsoportok* at csatolni több műveletet egy IoT Central szabály. Műveletcsoportot több szabályhoz is csatolhat. A [műveletcsoport](../../azure-monitor/platform/action-groups.md) az Azure-előfizetés tulajdonosa által meghatározott értesítési beállítások gyűjteménye.

## <a name="prerequisites"></a>Előfeltételek

- Szabványos árképzési csomaggal létrehozott alkalmazás
- Azure-fiók és előfizetés az Azure Monitor műveletcsoportok létrehozásához és kezeléséhez

## <a name="create-action-groups"></a>Műveletcsoportok létrehozása

Az Azure Portalon vagy egy [Azure Resource Manager-sablonnal](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)hozhat létre és kezelhet [műveletcsoportokat.](../../azure-monitor/platform/action-groups.md)

Egy műveletcsoport a következőket teheti:

- Értesítéseket küldhet, például e-mailt, SMS-t vagy hanghívást.
- Futtasson egy műveletet, például egy webhook hívása.

A következő képernyőképen egy műveletcsoport látható, amely e-mail- és SMS-értesítéseket küld, és webhookot hív meg:

![Műveletcsoport](media/howto-use-action-groups/actiongroup.png)

Egy műveletcsoport használata egy IoT Központi szabály, a műveletcsoport nak ugyanabban az Azure-előfizetésben kell lennie, mint az IoT Central alkalmazás.

## <a name="use-an-action-group"></a>Műveletcsoport használata

Ha egy műveletcsoportot szeretne használni az IoT Central alkalmazásban, először hozzon létre egy szabályt. Amikor hozzáad egy műveletet a szabályhoz, válassza az **Azure Monitor műveletcsoportok**:

![Művelet kiválasztása](media/howto-use-action-groups/chooseaction.png)

Válasszon egy műveletcsoportot az Azure-előfizetésből:

![Műveletcsoport kiválasztása](media/howto-use-action-groups/chooseactiongroup.png)

Kattintson a **Mentés** gombra. A műveletcsoport most antól megjelenik a szabály aktiválásakor futtatandó műveletek listájában:

![Mentett műveletcsoport](media/howto-use-action-groups/savedactiongroup.png)

Az alábbi táblázat a támogatott művelettípusoknak küldött információkat foglalja össze:

| Művelettípus | Kimeneti formátum |
| ----------- | -------------- |
| E-mail       | Szabványos IoT Central e-mail sablon |
| SMS         | Azure IoT Central riasztás: ${applicationName} - "${ruleName}" a következőn aktiválódik: "${deviceName}" a ${triggerDate} ${triggerTime} címen |
| Hang       | Azure I.O.T központi riasztás: a "${deviceName}" eszköz "${deviceName}" aktiválási szabálya a ${triggerDate} ${triggerTime}-nál, a ${applicationName} alkalmazásban |
| Webhook     | { "schemaId" : "AzureIoTCentralRuleWebhook", "data": {[regular webhook payload](howto-create-webhooks.md#payload)}} |

A következő szöveg egy példa SMS-üzenet egy műveletcsoportból:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan használhatja a műveletcsoportokat a szabályokkal, a javasolt következő lépés az [eszközök kezelésének megismerése.](howto-manage-devices.md)
