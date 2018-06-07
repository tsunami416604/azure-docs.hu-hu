---
title: Az Azure IoT Edge hibaelhárítása | Microsoft Docs
description: Az Azure IoT Edge gyakori problémáinak megoldása és a hibaelhárítási készségek elsajátítása
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/23/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ad22b0cd1457c1d4146a75047ff18e916c0c7ccd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633536"
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
   iotedgectl setup --connection-string "{device connection string}" --runtime-log-level debug
   ```
   
* Az iotedgectl-parancsokból származó részletes naplók megtekintése:

   ```cmd
   iotedgectl --verbose DEBUG <command>
   ```

* Ha csatlakozási problémákba ütközik, vizsgálja meg a peremhálózati eszköz környezeti változóit, például az eszköz kapcsolati sztringjét:

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

## <a name="iotedgectl-cant-find-docker"></a>Az iotedgectl nem találja a Dockert.

A parancsok `iotedgectl setup` vagy `iotedgectl start` sikertelen lesz, és nyomtassa ki a naplók a következő üzenetet:
```output
File "/usr/local/lib/python2.7/dist-packages/edgectl/host/dockerclient.py", line 98, in get_os_type
  info = self._client.info()
File "/usr/local/lib/python2.7/dist-packages/docker/client.py", line 174, in info
  return self.api.info(*args, **kwargs)
File "/usr/local/lib/python2.7/dist-packages/docker/api/daemon.py", line 88, in info
  return self._result(self._get(self._url("/info")), True)
```

### <a name="root-cause"></a>Gyökérok
Az iotedgectl nem találja a Dockert, amely előfeltétel.

### <a name="resolution"></a>Megoldás:
Telepítse a Dockert, futtassa, majd próbálkozzon újra.

## <a name="iotedgectl-setup-fails-with-an-invalid-hostname"></a>iotedgectl telepítője a egy érvénytelen állomásnév

A parancs `iotedgectl setup` sikertelen lesz, és kiírja a következő üzenetet: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Gyökérok
Az IoT-Edge futásidejű csak támogat, amelyek 64 karakternél rövidebb állomásnevek. Ez általában a fizikai gépek lemezhely, de akkor fordulhat elő, ha beállít egy virtuális gépen a futtatókörnyezet. Az automatikusan létrehozott állomásnevek Windows virtuális gépek Azure-ban üzemeltetett különösen általában hosszú. 

### <a name="resolution"></a>Megoldás:
Ha ez a hibaüzenet jelenik meg, hogyan oldható meg konfigurálásával a virtuális gép DNS-nevét, és majd beállítását az állomásnevet a setup parancs a DNS-nevét.

1. Az Azure-portálon lépjen a virtuális gép – áttekintés oldalra. 
2. Válassza ki **konfigurálása** DNS-neve alatt. Ha a virtuális gép már a DNS-név konfigurálva van, nem kell egy új konfigurálása. 

   ![DNS-nevét konfigurálja](./media/troubleshoot/configure-dns.png)

3. Adjon meg egy értéket a **DNS-névcímke** válassza **mentése**.
4. Másolja az új DNS-neve, ami a formátumúnak kell lennie  **\<DNSnamelabel\>.\< vmlocation\>. cloudapp.azure.com**.
5. A virtuális gépen belül az alábbi parancs segítségével állítsa be a DNS-névvel az IoT peremhálózati futásidejű:

   ```input
   iotedgectl setup --connection-string "<connection string>" --nopass --edge-hostname "<DNS name>"
   ```

## <a name="next-steps"></a>További lépések
Úgy gondolja, hogy hibát talált az IoT Edge platformon? Kérjük, a kijavításához [küldje el a problémát](https://github.com/Azure/iot-edge/issues). 
