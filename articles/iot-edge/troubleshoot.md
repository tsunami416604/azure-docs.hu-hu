---
title: Az Azure IoT Edge hibaelhárítása | Microsoft Docs
description: Az Azure IoT Edge gyakori problémáinak megoldása és a hibaelhárítási készségek elsajátítása
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9ec396e8a1ad36e85e1291995345ca1de24668d0
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128060"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Az Azure IoT Edge gyakori problémái és azok megoldásai

Ha a környezetében az Azure IoT Edge futtatásakor problémákat tapasztal, ezt a cikket útmutatóként használhatja a hibaelhárításhoz és a megoldáshoz. 

## <a name="standard-diagnostic-steps"></a>Általános diagnosztikai lépések 

Ha problémát tapasztal, a tárolónaplók és az eszközre küldött és az arról származó üzenetek áttekintésével további információt tudhat meg az IoT Edge-eszköz állapotáról. Az ebben a szakaszban szereplő parancsokkal és eszközökkel további információt gyűjthet. 

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Az IoT-peremhálózati biztonságkezelő és a naplók állapotának ellenőrzése:

Linux:
- Az IoT Edge-biztonságkezelő állapotának megtekintése:

   ```bash
   sudo systemctl status iotedge
   ```

- A naplók a IoT peremhálózati biztonsági Manager megtekintéséhez:

    ```bash
    sudo journalctl -u iotedge -f
    ```

- Az IoT Edge-biztonságkezelő naplók részletes megjelenítése:

   - A iotedge démon beállításainak szerkesztése:

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - Frissítse a következő sorokat:
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - Indítsa újra az IoT-Edge biztonsági démon:
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

Windows rendszeren:
- Az IoT Edge-biztonságkezelő állapotának megtekintése:

   ```powershell
   Get-Service iotedge
   ```

- A naplók a IoT peremhálózati biztonsági Manager megtekintéséhez:

   ```powershell
   # Displays logs from today, newest at the bottom.
 
   Get-WinEvent -ea SilentlyContinue `
   -FilterHashtable @{ProviderName= "iotedged";
     LogName = "application"; StartTime = [datetime]::Today} |
   select TimeCreated, Message |
   sort-object @{Expression="TimeCreated";Descending=$false}
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Ha az IoT-peremhálózati biztonságkezelő nem fut, ellenőrizze a yam konfigurációs fájlt

> [!WARNING]
> YAM-fájlok nem lehetnek identation lapokon. 2 szóközt használja.

Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows rendszeren:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Tároló naplókban problémák

Ha az IoT-Edge biztonsági démon fut, tekintse meg a naplókat a problémák észlelése tárolók. Kezdje az üzembe helyezett tárolókkal, majd tekintse meg az IoT Edge-futtatókörnyezetet alkotó tárolókat: az Edge Agentet és az Edge Hubot. Az Edge Agent-naplók általában az egyes tárolók életciklusáról nyújtanak információt. Az Edge Hub-naplók az üzenetküldésről és az útválasztásról nyújtanak információt. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-edge-hub"></a>A peremhálózati hub áthaladás üzenetek megjelenítése

A peremhálózati hub áthaladás üzenetek megjelenítése, és elemzések gyűjt az eszköz tulajdonságok frissítések részletes naplókat a edgeAgent és edgeHub futtatókörnyezet tárolókból. Ezek a tárolók részletes naplókat bekapcsolásához állítsa be a `RuntimeLogLevel` környezeti változót: 

Linux:
    
   ```cmd
   export RuntimeLogLevel="debug"
   ```
    
Windows rendszeren:
    
   ```powershell
   [Environment]::SetEnvironmentVariable("RuntimeLogLevel", "debug")
   ```

Az IoT Hub és az IoT Edge-eszközök között küldött üzeneteket is ellenőrizheti. Ezeket az üzeneteket a Visual Studio Code [Azure IoT-eszközkészlet](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) bővítményével tekintheti meg. További útmutatásért lásd [az Azure IoT-fejlesztések esetén hasznos eszközt](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Indítsa újra a tárolók
Után vizsgálja meg a naplókat és az üzenetek információt, próbálkozhat újraindítással tárolók:

```
iotedge restart <container name>
```

Indítsa újra az IoT-Edge futásidejű tárolókban:

```
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Indítsa újra a IoT Edge-kezelő

Ha a probléma továbbra is megőrzése, megpróbálhatja az IoT-Edge biztonságkezelő újraindítani.

Linux:

   ```cmd
   sudo systemctl restart iotedge
   ```

Windows rendszeren:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
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
Győződjön meg arról, hogy a beállításjegyzék hitelesítő adatok helyesen vannak-e megadva az üzembe helyezési jegyzékben

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Az IoT-Edge biztonsági démon meghiúsul, és egy érvénytelen állomásnév

A parancs `sudo journalctl -u iotedge` sikertelen lesz, és kiírja a következő üzenetet: 

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

   - Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - Windows rendszeren:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="next-steps"></a>További lépések
Úgy gondolja, hogy hibát talált az IoT Edge platformon? Kérjük, a kijavításához [küldje el a problémát](https://github.com/Azure/iotedge/issues). 
