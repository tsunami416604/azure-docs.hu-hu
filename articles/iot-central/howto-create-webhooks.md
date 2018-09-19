---
title: Hozzon létre a webhookok a szabályok az Azure IoT Central |} A Microsoft Docs
description: Webhookok létrehozása az Azure IoT Central más alkalmazások automatikusan értesíti, amikor szabályok aktiválódik.
author: viv-liu
ms.author: viviali
ms.date: 09/17/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 896d4e9c775fa0b0c8eb062d11d141901daa7242
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295982"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Webhook-műveletek létrehozása az Azure IoT Central szabályai

*Ez a témakör létrehozói és a rendszergazdák vonatkozik.*

Webhookok lehetővé teszi az IoT-központ alkalmazás csatlakoztatása a más alkalmazások és szolgáltatások a távoli figyelés és értesítések. Webhookok automatikus értesítéséhez egyéb alkalmazások és szolgáltatások, amikor egy szabály akkor lesz kiváltva csatlakozik az IoT-központ alkalmazásában. Az IoT Central-alkalmazást egy POST kérést küld az alkalmazás HTTP-végpontot, amikor egy szabály akkor lesz kiváltva. A hasznos adatokat fog tartalmazni, eszközadatok és a szabály a trigger részletei. 

## <a name="how-to-set-up-the-webhook"></a>Webhook beállítása
Ebben a példában értesítést kaphat, amikor a szabályok utáni webhookok használata a RequestBin fog csatlakozni. 

1. Nyissa meg [RequestBin](http://requestbin.net/). 
1. Hozzon létre egy új RequestBin és másolása a **Bin URL-cím**. 
1. Hozzon létre egy [telemetriai szabály](howto-create-telemetry-rules.md) vagy egy [esemény szabály](howto-create-event-rules.md). A szabály mentéséhez és a egy új művelet hozzáadása.
![Webhook-létrehozási képernyő](media/howto-create-webhooks/webhookcreate.PNG)
1. Válassza ki a webhook művelettel, és adjon meg egy megjelenített nevet, és illessze be a tár URL-CÍMÉT az visszahívási URL-címet. 
1. A szabály mentéséhez.

Most a szabály akkor lesz kiváltva, amikor meg kell jelennie egy új kérelmet RequestBin jelennek meg.

## <a name="payload"></a>Adatok
A szabály akkor lesz kiváltva, ha egy HTTP POST kérés jön létre a visszahívási URL-cím egy json-adattartalom a mérések, eszköz, a szabály és alkalmazásadatokat tartalmazó. A telemetriai adatokat a szabályhoz a hasznos lehet a következőhöz hasonló:

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
Jelenleg nincs ezek webhookok egy API-n keresztül, az előfizetés/iratkoznak nincs programozott módon.

Ha ez a szolgáltatás továbbfejlesztésére vonatkozó ötleteit, közzététel a javaslatok a [Uservoice fórumot](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>További lépések
Most, hogy, hogyan állíthatja be, és webhookok használata, a javasolt következő lépésre megismeréséhez [munkafolyamatokat a Microsoft Flow-hoz](howto-add-microsoft-flow.md).
