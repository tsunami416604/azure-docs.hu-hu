---
title: Webhookok létrehozása szabályokon az Azure IoT Centralban | Microsoft dokumentumok
description: Hozzon létre webhookokat az Azure IoT Centralban, hogy automatikusan értesítse a többi alkalmazást, amikor a szabályok elindulnak.
author: viv-liu
ms.author: viviali
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: d97bd7a3c6de92f22a9880040f407960d5257f6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158095"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Webhook-műveletek létrehozása szabályokon az Azure IoT Centralban

*Ez a témakör az építőkre és a rendszergazdákra vonatkozik.*

A webhookok lehetővé teszik az IoT Central alkalmazás más alkalmazásokkal és szolgáltatásokkal való csatlakoztatását a távoli figyelés és értesítések érdekében. A webhookok automatikusan értesítik a többi alkalmazást és szolgáltatást, amelyeket akkor kapcsol össze, amikor egy szabály aktiválódik az IoT Central alkalmazásban. Az IoT Central alkalmazás postakérést küld a másik alkalmazás HTTP-végpontjára, amikor egy szabály aktiválódik. A hasznos adat tartalmazza az eszköz adatait és a szabály eseményindító részleteit.

## <a name="set-up-the-webhook"></a>A webhook beállítása

Ebben a példában a RequestBin-hoz csatlakozik, hogy értesítést kapjon, ha a szabályok webhookok használatával tűz.

1. Nyissa [meg a RequestBin](https://requestbin.net/)t.

1. Hozzon létre egy új RequestBin-t, és másolja a **raktárhely URL-címét.**

1. [Telemetriai szabály](tutorial-create-telemetry-rules.md)létrehozása. Mentse a szabályt, és adjon hozzá új műveletet.

    ![Webhook létrehozási képernyő](media/howto-create-webhooks/webhookcreate.png)

1. Válassza ki a webhook műveletet, és adjon meg egy megjelenítendő nevet, és illessze be a bin URL-t a visszahívás URL-címeként.

1. Mentsd a szabályt.

Most, amikor a szabály aktiválódik, megjelenik egy új kérelem jelenik meg a RequestBin.

## <a name="payload"></a>Hasznos teher

Amikor egy szabály aktiválódik, egy HTTP POST-kérelem érkezik a visszahívási URL-címet tartalmazó json tartalom a telemetriai adatok, eszköz, szabály és az alkalmazás részleteit. A hasznos teher a következőkre néz ki:

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

Jelenleg nincs programozott módon feliratkozás / leiratkozás ezekből webhooks egy API-t.

Ha van ötlete, hogyan lehetne javítani ezt a funkciót, tegye a javaslatokat, hogy a [felhasználói hang fórum](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta a webhookok beállítását és használatát, a javasolt következő lépés az [Azure Monitor műveletcsoportok konfigurálásának](howto-use-action-groups.md)feltárása.
