---
title: Beépített edgeAgent közvetlen metódusok – Azure IoT Edge
description: IoT Edge-telepítés figyelése és kezelése az IoT Edge-ügynök futásidejű modulbeépített közvetlen metódusai használatával
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240349"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Kommunikáció az edgeAgent-el beépített közvetlen módszerekkel

Az IoT Edge-alkalmazások figyelése és kezelése az IoT Edge-ügynök modulban található közvetlen módszerek használatával. Közvetlen metódusok vannak megvalósítva az eszközön, és ezután meghívható a felhőből. Az IoT Edge-ügynök közvetlen metódusok, amelyek segítségével figyelheti és kezelheti az IoT Edge-eszközök távolról.

A közvetlen módszerekről, azok használatáról és a saját modulokban való megvalósításukról az [IoT Hub ból származó közvetlen metódusok megértése és meghívása](../iot-hub/iot-hub-devguide-direct-methods.md)című témakörben talál további információt.

Ezeknek a közvetlen metódusok neveinél a kis- és nagybetűk nem különböznek.

## <a name="ping"></a>Ping

A **ping** módszer akkor hasznos, ha ellenőrzi, hogy az IoT Edge fut-e egy eszközön, vagy hogy az eszköz nyitott kapcsolat tal rendelkezik-e az IoT Hubhoz. Ezzel a közvetlen módszerrel pingeli az IoT Edge-ügynököt, és leszeretné az állapotát. A sikeres ping üres hasznos terhet és **"állapotot" ad vissza: 200**.

Példa:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

Az Azure Portalon hívja meg a `ping` metódust a metódus `{}`nevével és egy üres JSON-tartalommal.

![A "ping" közvetlen metódus meghívása az Azure Portalon](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Újrakezdési modul

A **RestartModule** metódus lehetővé teszi az IoT Edge-eszközön futó modulok távoli kezelését. Ha egy modul hibás állapotot vagy más nem megfelelő állapotot jelent, aktiválhatja az IoT Edge-ügynök újraindítását. A sikeres újraindítási parancs üres hasznos adattal és **"állapottal" ad vissza: 200**.

A RestartModule metódus az IoT Edge 1.0.9-es és újabb verziójában érhető el. 

A RestartModule közvetlen metódust bármely IoT Edge-eszközön futó modulon használhatja, beleértve magát az edgeAgent modult is. Ha azonban ezt a közvetlen módszert használja az edgeAgent leállításához, nem kap sikeres eredményt, mivel a kapcsolat megszakad, miközben a modul újraindul.

Példa:

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

Az Azure Portalon hívja meg a `RestartModule` metódust a metódus nevével és a következő JSON-tartalommal:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![A "RestartModule" közvetlen metódus meghívása az Azure Portalon](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>Kísérleti módszerek

Új közvetlen módszer lehetőségek állnak rendelkezésre kísérleti funkciók tesztelésére, beleértve a következőket:

* [UploadLogs:](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md)A modulnaplók lekérése és feltöltése az Azure Blob Storage-ba.
* [GetTaskStatus](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus): Ellenőrizze a feltöltési naplók kérésének állapotát.
* [GetLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs): A modulnaplók beolvasása a közvetlen metódus válaszában.

## <a name="next-steps"></a>További lépések

[Az IoT Edge-ügynök és az IoT Edge hub modul twins tulajdonságai](module-edgeagent-edgehub.md)
