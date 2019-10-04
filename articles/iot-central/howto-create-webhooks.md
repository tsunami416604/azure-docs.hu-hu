---
title: Webhookok létrehozása a szabályokban az Azure IoT Centralban | Microsoft Docs
description: Hozzon létre webhookokat az Azure IoT Centralban, hogy automatikusan értesítse a többi alkalmazást, amikor a szabályok tüzet mutatnak.
author: viv-liu
ms.author: viviali
ms.date: 06/16/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 508e8b4b3a909e87f538f67b1ad9a5efdbcd9551
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876026"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Webhook-műveletek létrehozása az Azure-beli szabályokon IoT Central

*Ez a témakör az építők és a rendszergazdákra vonatkozik.*

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

A webhookok lehetővé teszik a IoT Central alkalmazás csatlakoztatását más alkalmazásokhoz és szolgáltatásokhoz távoli figyeléshez és értesítésekhez. A webhookok automatikusan értesítik a többi olyan alkalmazást és szolgáltatást, amely akkor csatlakozik, amikor egy szabály aktiválódik a IoT Central alkalmazásban. A IoT Central alkalmazás POST-kérést küld a másik alkalmazás HTTP-végpontjának, amikor egy szabály aktiválódik. A hasznos adat tartalmazza az eszköz adatait és a szabály-trigger részleteit.

## <a name="set-up-the-webhook"></a>A webhook beállítása

Ebben a példában a RequestBin-hez csatlakozik, hogy értesítést kapjon a szabályok webhookok használatával történő bejelentéséről.

1. Nyissa meg a [RequestBin](https://requestbin.net/).

1. Hozzon létre egy új RequestBin, és másolja a **bin URL-címét**.

1. Hozzon létre egy [telemetria szabályt](howto-create-telemetry-rules.md) vagy egy [eseményvezérelt szabályt](howto-create-event-rules.md). Mentse a szabályt, és adjon hozzá egy új műveletet.

    ![Webhook-létrehozási képernyő](media/howto-create-webhooks/webhookcreate.png)

1. Válassza ki a webhook műveletet, és adjon meg egy megjelenítendő nevet, és illessze be a bin URL-címet a visszahívási URL-címként.

1. Mentse a szabályt.

Most, hogy a szabály aktiválódik, megjelenik egy új kérelem a RequestBin.

## <a name="payload"></a>Adatok

Egy szabály kiváltásakor a rendszer egy HTTP POST-kérést küld egy JSON-adattartalomot tartalmazó visszahívási URL-címre a mérésekkel, az eszközkel, a szabállyal és az alkalmazás részleteivel. Telemetria-szabály esetén a hasznos adatok a következőhöz hasonlóak:

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>Ismert korlátozások

Jelenleg nincs programozási mód a webhookok API-n keresztül történő előfizetésére/lemondása.

Ha ötletekkel szeretné javítani ezt a funkciót, küldje el javaslatait a [uservoice fórumra](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a webhookok beállítását és használatát, a javasolt következő lépés a munkafolyamatok [kiépítése a Microsoft Flowban](howto-add-microsoft-flow.md).
