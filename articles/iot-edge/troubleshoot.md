---
title: "Az Azure IoT Edge hibaelhárítása | Microsoft Docs"
description: "Az Azure IoT Edge gyakori problémáinak megoldása és a hibaelhárítási készségek elsajátítása"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5de069eb35e88c1dce6dcfa5a1661e8ab87302b1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Az Azure IoT Edge gyakori problémái és azok megoldásai

Ha a környezetében az Azure IoT Edge futtatásakor problémákat tapasztal, ezt a cikket útmutatóként használhatja a hibaelhárításhoz és a megoldáshoz. 

## <a name="standard-diagnostic-steps"></a>Általános diagnosztikai lépések 

Ha problémát tapasztal, a tárolónaplók és az eszközre küldött és az arról származó üzenetek áttekintésével további információt tudhat meg az IoT Edge-eszköz állapotáról. Az ebben a szakaszban szereplő parancsokkal és eszközökkel további információt gyűjthet. 

* A hibák észleléséhez tekintse meg a Docker-tárolók naplóit. Kezdje az üzembe helyezett tárolókkal, majd tekintse meg az IoT Edge-futtatókörnyezetet alkotó tárolókat: az Edge Agentet és az Edge Hubot. Az Edge Agent-naplók általában az egyes tárolók életciklusáról nyújtanak információt. Az Edge Hub-naplók az üzenetküldésről és az útválasztásról nyújtanak információt. 

   ```cmd
   docker logs <container name>
   ```

* Tekintse meg az Edge Hubon áthaladó üzeneteket, és az eszköztulajdonságok frissítéseiről gyűjtsön információt a futtatókörnyezet tárolóiból származó részletes naplókkal.

   ```cmd
   iotedgectl setup --runtime-log-level DEBUG
   ```

* Ha csatlakozási problémákba ütközik, vizsgálja meg a peremhálózati eszköz környezeti változóit, például az eszköz kapcsolati karakterláncát:

   ```cmd
   docker exec edgeAgent printenv
   ```

Az IoT Hub és az IoT Edge-eszközök között küldött üzeneteket is ellenőrizheti. Ezeket az üzeneteket a Visual Studio Code [Azure IoT-eszközkészlet](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) bővítményével tekintheti meg. További útmutatásért lásd [az Azure IoT-fejlesztések esetén hasznos eszközt](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

Miután megvizsgálta a naplók és üzenetek információit, az Azure IoT Edge-futtatókörnyezet újraindításával is megpróbálkozhat:

   ```cmd
   iotedgectl restart
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>Az Edge Agent körülbelül egy perc után leáll

Az Edge Agent elindul, és körülbelül egy percig sikeresen fut, majd leáll. A naplók szerint az Edge Agent megpróbál kapcsolódni az IoT Hubhoz AMQP-n keresztül, és körülbelül 30 másodperc múlva megpróbál csatlakozni az AMQP használatával websocketen keresztül. Ha ez nem sikerül, az Edge Agent kilép. 

Példa az Edge Agent-naplókra:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Gyökérok
A gazdahálózaton egy hálózati konfiguráció meggátolja, hogy az Edge Agent elérje a hálózatot. Az ügynök először megpróbál AMQP-n keresztül csatlakozni (az 5671-es porton). Ha ez nem sikerül, a websockettel próbálkozik (a 443-as porton).

Az IoT Edge-futtatókörnyezet minden modulon beállít egy-egy hálózatot a kommunikációhoz. Linux rendszeren ez a hálózat egy hídhálózat. Windows rendszeren NAT-ot használ. Ez a probléma gyakoribb a NAT-hálózatot használó Windows-tárolókat igénybe vevő windowsos eszközökön. 

### <a name="resolution"></a>Megoldás:
Győződjön meg arról, hogy elérhető egy útvonal az internethez az ehhez a híd-/NAT-hálózathoz rendelt IP-címek esetén. Néha a gazdagépen lévő VPN-konfiguráció felülbírálja az IoT Edge-hálózatot. 

## <a name="edge-hub-fails-to-start"></a>Az Edge Hub nem indul el

Az Edge Hub nem indul el, és a következő üzenetet írja a naplókba: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Gyökérok
A gazdagépen egy másik folyamat foglalja le a 443-as portot. Az Edge Hub az 5671-es és a 443-as portot képezi le az átjáró-forgatókönyvekhez. Ez a portleképezés sikertelen, ha egy másik folyamat már lefoglalta a portot. 

### <a name="resolution"></a>Megoldás:
Keresse meg, és állítsa le a 443-as portot használó folyamatot. Ez a folyamat általában a webkiszolgáló.

## <a name="edge-agent-cant-access-a-modules-image-403"></a>Az Edge Agent nem éri el egy modul rendszerképét (403)
Egy tároló nem fut, és az Edge Agent-naplók a 403-as hibát tartalmazzák. 

### <a name="root-cause"></a>Gyökérok
Az Edge Agentnek nincs engedélye egy modul rendszerképének eléréséhez. 

### <a name="resolution"></a>Megoldás:
Próbálja meg ismét futtatni az `iotedgectl login` parancsot.

## <a name="next-steps"></a>További lépések
Úgy gondolja, hogy hibát talált az IoT Edge platformon? Kérjük, a kijavításához [küldje el a problémát](https://github.com/Azure/iot-edge/issues). 
