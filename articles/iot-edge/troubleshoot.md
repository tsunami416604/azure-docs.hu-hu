---
title: "Azure IoT peremhálózati hibaelhárítása |} Microsoft Docs"
description: "Gyakori problémák megoldásában, és ismerje meg a hibaelhárítási képességek az Azure IoT peremhálózati"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3f61f0bf8234e747ae38146d1a5ea030e3163fa3
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Gyakori problémák és megoldások Azure IoT szegély

A környezetben futó Azure IoT biztonsági problémákat tapasztal, használja a cikk útmutatóként hibakeresési és feloldási. 

## <a name="standard-diagnostic-steps"></a>Standard szintű diagnosztikai lépései 

Ha problémát tapasztal, további információ az IoT-peremhálózati eszköz állapotát a tároló naplókat és az üzeneteket, amelyek megfelelnek az eszközre érkező vagy oda irányuló megtekintésével. A parancsok és eszközök használata ebben a szakaszban összegyűjteni. 

* Tekintse meg a naplókat a problémák észlelése docker-tároló. Indítsa el a telepített tárolókat, majd tekintse meg a tárolók a IoT peremhálózati futásidejű alkotó: ügynök Edge és biztonsági központja. A peremhálózati ügynök naplók az egyes tárolókban életciklusát általában adatainak megadása. A peremhálózati Hub naplók üzenetküldési és útválasztási adatainak megadása. 

   ```cmd
   docker logs <container name>
   ```

* A peremhálózati Hub áthaladás üzenetek megjelenítése, és elemzések gyűjt az eszköz tulajdonságok frissítések részletes naplókat a futtatókörnyezet tárolókból.

   ```cmd
   iotedgectl setup --runtime-log-level DEBUG
   ```

* Ha csatlakozási problémákba ütközhetnek, vizsgálja meg a peremhálózati eszköz környezeti változók, például az eszköz kapcsolati karakterlánc:

   ```cmd
   docker exec edgeAgent printenv
   ```

Az IoT-központ és az IoT-peremeszközök között küldött üzenetek is ellenőrizheti. Ezek az üzenetek segítségével meg a [Azure IoT eszközkészlet](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) Visual Studio Code-kiterjesztését. További útmutatásért lásd: [hasznos eszköz Azure IoT fejlesztésekor](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

Után vizsgálja meg a naplókat és az üzenetek információt, is megpróbálhatja újraindítani az Azure IoT peremhálózati futásidejű:

   ```cmd
   iotedgectl restart
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>A szegély ügynök leállítása után körülbelül egy perce

A peremhálózati ügynök elindul és futtatása sikeresen körülbelül egy perce, majd leállítja. A naplók jelzi, hogy a peremhálózati ügynök megpróbál kapcsolódni az IoT hubhoz AMQP keresztül, és körülbelül 30 másodperc múlva próbálja meg AMQP keresztül websocket protokoll használatával kapcsolódó levelezőprogramokkal. Ha nem sikerül, a peremhálózati ügynök kilép. 

Példa peremhálózati ügynök naplói:

```
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Alapvető OK
A hálózati konfigurációt a gazdagép hálózati meggátolja, hogy a hálózat elérése a a peremhálózati ügynök. Az ügynök megpróbálja először csatlakoznak AMQP (port: 5671) keresztül. Ha ez nem sikerül, a rendszer megkísérli websocket elemeket (a 443-as port).

Az IoT-Edge futásidejű állít be a modulok mindegyikének egy hálózati kommunikációhoz. Linux a hálózati híd hálózatról. A Windows használja a hálózati címfordítást. A probléma napjainkban egyre általánosabbá Windows tárolók a NAT-hálózatot használó Windows-eszközökön. 

### <a name="resolution"></a>Megoldás:
Győződjön meg arról, hogy van-e az internethez híd/NAT hálózathoz hozzárendelt IP-címek egy útvonalat. A VPN-konfiguráció a gazdagépen néha a IoT peremhálózati hálózati felülbírálja. 

## <a name="edge-hub-fails-to-start"></a>Biztonsági központ nem indul el

Biztonsági központ nem tudja elindítani, és a naplók a következő üzenet megrendelése: 

```
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Alapvető OK
Egy másik folyamat, a gazdagépen 443-as porton van kötve. A peremhálózati Hub port: 5671 képezi le, és az átjáró alkalmazásának típusai a 443-as. A port hozzárendelése sikertelen lesz, ha egy másik folyamat már rendelkezik kötött ezt a portot. 

### <a name="resolution"></a>Megoldás:
Keresse meg, és leállítja a folyamatot, amely 443-as portot használja. Ez a folyamat általában azt a kiszolgálót.

## <a name="edge-agent-cant-access-a-modules-image-403"></a>Peremhálózati ügynök nem érhető el egy modul lemezképe (403)
A tároló nem fut, és a peremhálózati ügynök naplók megjelenítése a 403-as hiba. 

### <a name="root-cause"></a>Alapvető OK
A peremhálózati ügynök nincs engedélye a modul kép eléréséhez. 

### <a name="resolution"></a>Megoldás:
Próbálja meg fut a `iotedgectl login` újra a parancsot.

## <a name="next-steps"></a>További lépések
Gondolja, hogy egy hiba található a IoT peremhálózati platform? Kérjük [küldje el a problémát](https://github.com/Azure/iot-edge/issues) , hogy a Folytatás javítása érdekében. 
