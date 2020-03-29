---
title: Az Azure IoT Hub 404104-es deviceconnectionclosedremotely hibája
description: A 404104-es deviceConnectionClosedRemotely hiba javítása
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 686946b65b4b7540f404a291c87c5ad9c7b7a0ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960801"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

Ez a cikk a **404104 DeviceConnectionClosedRemotely** hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

### <a name="symptom-1"></a>1. tünet

Az eszközök rendszeres időközönként (például 65 percenként) bontják a kapcsolatot, és **404104 DeviceConnectionClosedRemotely** jelenik meg az IoT Hub diagnosztikai naplóiban. Néha **401003 IoTHubUnauthorized** és egy sikeres eszközkapcsolati esemény is megjelenik kevesebb, mint egy perccel később.

### <a name="symptom-2"></a>2. tünet

Az eszközök véletlenszerűen bontják a kapcsolatot, és az IoT Hub diagnosztikai naplóiban **404104 DeviceConnectionClosedRemotely** jelenik meg.

### <a name="symptom-3"></a>3. tünet

Sok eszköz bontja egyszerre, megjelenik egy dip a [csatlakoztatott eszközök metrika,](iot-hub-metrics.md)és több **404104 DeviceConnectionClosedRemotely** és [500xxx Belső hibák](iot-hub-troubleshoot-error-500xxx-internal-errors.md) diagnosztikai naplók, mint máskor.

## <a name="causes"></a>Okok

### <a name="cause-1"></a>1. ok

Az [IoT Hubhoz való csatlakozáshoz használt SAS-jogkivonat](iot-hub-devguide-security.md#security-tokens) lejárt, ami miatt az IoT Hub leválasztja az eszközt. A kapcsolat az eszköz által frissített jogkivonat frissítésekor újra létrejön. Például [a SAS-jogkivonat alapértelmezés szerint óránként lejár a C SDK számára,](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication)ami rendszeres kapcsolatbontáshoz vezethet.

További információ: [401003 IoTHubUnauthorized cause](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1).

### <a name="cause-2"></a>2. ok

Néhány lehetőség:

- Az eszköz az [MQTT életben tartásánál](iot-hub-mqtt-support.md#default-keep-alive-timeout)hosszabb ideig veszítette el az alapul szolgáló hálózati kapcsolatot, ami távoli tétlen időtúllépést eredményez. Az MQTT életben tartás beállítása eszközönként eltérő lehet.

- Az eszköz TCP/IP-szintű alaphelyzetbe állítást küldött, de `MQTT DISCONNECT`nem küldött alkalmazásszintű . Alapvetően a készülék hirtelen bezárta az alapul szolgáló aljzat kapcsolatot. Néha ezt a problémát az Azure IoT SDK régebbi verzióiban lévő hibák okozzák.

- A készülék oldali alkalmazás összeomlott.

### <a name="cause-3"></a>3. ok

Előfordulhat, hogy az IoT Hub átmeneti problémát tapasztal. Lásd: [IoT Hub belső kiszolgálóhiba oka.](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause)

## <a name="solutions"></a>Megoldások

### <a name="solution-1"></a>1. megoldás

Lásd [401003 IoTHubJogosulatlan megoldás 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>2. megoldás

- Győződjön meg arról, hogy az eszköz megfelelő kapcsolattal rendelkezik az IoT Hubhoz [a kapcsolat tesztelésével.](tutorial-connectivity.md) Ha a hálózat megbízhatatlan vagy időszakos, nem javasoljuk az életben tartás érték növelését, mert az észlelési (például az Azure Monitor riasztásokon keresztül) hosszabb időt vehet igénybe. 

- Használja az [IoT SDK-k legújabb verzióit.](iot-hub-devguide-sdks.md)

### <a name="solution-3"></a>3. megoldás

Tekintse [meg az IoT Hub belső kiszolgálóhibáinak megoldásait.](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution)

## <a name="next-steps"></a>További lépések

Javasoljuk, hogy az Azure IoT-eszköz SDK-k használatával megbízhatóan kezelje a kapcsolatokat. További információ: [Kapcsolat és megbízható üzenetküldés kezelése az Azure IoT Hub sdk-k használatával](iot-hub-reliability-features-in-sdks.md)