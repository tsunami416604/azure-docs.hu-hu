---
title: Beépített edgeAgent közvetlen metódusok – Azure IoT Edge
description: IoT Edge üzemelő példány figyelése és kezelése beépített közvetlen módszerekkel az IoT Edge Agent Runtime modulban
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80240349"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Kommunikáció a edgeAgent a beépített közvetlen módszerek használatával

IoT Edge központi telepítések figyelése és kezelése az IoT Edge Agent modulban foglalt közvetlen módszerek használatával. A közvetlen metódusok implementálva vannak az eszközön, majd a felhőből is meghívhatók. A IoT Edge ügynök olyan közvetlen metódusokat tartalmaz, amelyek segítségével távolról figyelheti és kezelheti IoT Edge eszközeit.

További információ a közvetlen módszerekről, a használatáról és a saját modulok megvalósításáról: [közvetlen módszerek megismerése és meghívása a IoT hubból](../iot-hub/iot-hub-devguide-direct-methods.md).

Ezeknek a közvetlen metódusoknak a neve a kis-és nagybetűket nem megkülönböztető módon kezeli.

## <a name="ping"></a>Ping

A **pingelési** módszer hasznos annak ellenőrzéséhez, hogy IoT Edge fut-e az eszközön, vagy hogy az eszköz rendelkezik-e nyitott internetkapcsolattal IoT hub. Ezzel a közvetlen módszerrel pingelheti a IoT Edge-ügynököt, és lekérheti annak állapotát. A sikeres pingelés üres adattartalmat ad vissza, és **"status": 200**.

Például:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

A Azure Portalban hívja meg a metódust a metódus nevével `ping` és egy üres JSON-adattartalommal `{}` .

![Közvetlen "ping" metódus meghívása Azure Portal](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Modul újraindítása

A **RestartModule** metódus lehetővé teszi a IoT Edge eszközön futó modulok távoli felügyeletét. Ha egy modul hibás állapotot vagy más nem kifogástalan viselkedést jelent, akkor a IoT Edge-ügynök kiváltható az újraindításhoz. Egy sikeres újraindítási parancs üres adattartalmat ad vissza, és **"status": 200**.

A RestartModule metódus a IoT Edge 1.0.9 és újabb verziókban érhető el. 

A RestartModule Direct metódust bármely IoT Edge eszközön futó modulon használhatja, beleértve a edgeAgent modult is. Ha azonban ezt a közvetlen módszert használja a edgeAgent leállítására, nem fog sikeres eredményt kapni, mert a rendszer megszakította a kapcsolódást a modul újraindításakor.

Például:

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

A Azure Portal hívja meg a metódust a metódus nevével `RestartModule` és a következő JSON-adattartalommal:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Közvetlen "RestartModule" metódus hívása Azure Portal](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>Kísérleti módszerek

Az új közvetlen metódus-beállítások a teszteléshez használható kísérleti funkciókként érhetők el, beleértve a következőket:

* [UploadLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md): beolvassa a modul naplófájljait, és feltölti őket az Azure Blob Storageba.
* [GetTaskStatus](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus): a feltöltési naplókra vonatkozó kérelem állapotának beadása.
* [GetLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs): a közvetlen metódus válaszában beágyazott modul-naplók beolvasása.

## <a name="next-steps"></a>További lépések

[A IoT Edge-ügynök és az IoT Edge hub-modulok ikrek tulajdonságai](module-edgeagent-edgehub.md)
