---
title: Webhookok létrehozása a szabályokban az Azure IoT Centralban | Microsoft Docs
description: Hozzon létre webhookokat az Azure IoT Centralban, hogy automatikusan értesítse a többi alkalmazást, amikor a szabályok tüzet mutatnak.
author: viv-liu
ms.author: viviali
ms.date: 12/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: db4e48a7bff9127810b051a9ab63bbe9d78cf6da
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271615"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Webhook-műveletek létrehozása az Azure-beli szabályokon IoT Central

*Ez a témakör az építők és a rendszergazdákra vonatkozik.*

A webhookok lehetővé teszik a IoT Central alkalmazás csatlakoztatását más alkalmazásokhoz és szolgáltatásokhoz távoli figyeléshez és értesítésekhez. A webhookok automatikusan értesítik a többi olyan alkalmazást és szolgáltatást, amely akkor csatlakozik, amikor egy szabály aktiválódik a IoT Central alkalmazásban. A IoT Central alkalmazás POST-kérést küld a másik alkalmazás HTTP-végpontjának, amikor egy szabály aktiválódik. A hasznos adat tartalmazza az eszköz adatait és a szabály-trigger részleteit.

## <a name="set-up-the-webhook"></a>A webhook beállítása

Ebben a példában a RequestBin-hez csatlakozik, hogy értesítést kapjon a szabályok webhookok használatával történő bejelentéséről.

1. Nyissa meg a [RequestBin](https://requestbin.net/).

1. Hozzon létre egy új RequestBin, és másolja a **bin URL-címét**.

1. Hozzon létre egy [telemetria-szabályt](tutorial-create-telemetry-rules.md). Mentse a szabályt, és adjon hozzá egy új műveletet.

    ![Webhook-létrehozási képernyő](media/howto-create-webhooks/webhookcreate.png)

1. Válassza ki a webhook műveletet, és adjon meg egy megjelenítendő nevet, és illessze be a bin URL-címet a visszahívási URL-címként.

1. Mentse a szabályt.

Most, hogy a szabály aktiválódik, megjelenik egy új kérelem a RequestBin.

## <a name="payload"></a>Adattartalom

Egy szabály indításakor a rendszer HTTP POST-kérelmet küld a telemetria, az eszköz, a szabály és az alkalmazás részleteit tartalmazó JSON-adattartalommal rendelkező visszahívási URL-címre. A hasznos adatok a következőhöz hasonlóak:

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout---PnP",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>Ismert korlátozások

Jelenleg nincs programozási mód a webhookok API-n keresztül történő előfizetésére/lemondása.

Ha Ötletei vannak a funkció fejlesztéséhez, tegye a javaslatait a [felhasználói hangalapú fórumba](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a webhookok beállítását és használatát, a javasolt következő lépés az [Azure monitor műveleti csoportok konfigurálásának](howto-use-action-groups.md)megismerése.
