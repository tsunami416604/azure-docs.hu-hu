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
ms.openlocfilehash: a6102a6bc28486c24134bbc172b9e8a7e1a61244
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308037"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Az Azure IoT Edge gyakori problémái és azok megoldásai

Ha a környezetében az Azure IoT Edge futtatásakor problémákat tapasztal, ezt a cikket útmutatóként használhatja a hibaelhárításhoz és a megoldáshoz. 

## <a name="standard-diagnostic-steps"></a>Általános diagnosztikai lépések 

Ha problémát tapasztal, a tárolónaplók és az eszközre küldött és az arról származó üzenetek áttekintésével további információt tudhat meg az IoT Edge-eszköz állapotáról. Az ebben a szakaszban szereplő parancsokkal és eszközökkel további információt gyűjthet. 

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Az IoT Edge Security Manager és a naplók állapotának ellenőrzéséhez:

Linux:
- Az IoT Edge-biztonságkezelő állapotának megtekintése:

   ```bash
   sudo systemctl status iotedge
   ```

- A naplók az IoT Edge-biztonságkezelő megtekintéséhez:

    ```bash
    sudo journalctl -u iotedge -f
    ```

- Továbbiak megtekintése részletes naplók az IoT Edge-biztonságkezelő:

   - Szerkessze a iotedge démon beállításokat:

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - Frissítse a következő sorokat:
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - Az IoT Edge biztonsági démon újraindításához:
    
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

- A naplók az IoT Edge-biztonságkezelő megtekintéséhez:

   ```powershell
   # Displays logs from today, newest at the bottom.
 
   Get-WinEvent -ea SilentlyContinue `
   -FilterHashtable @{ProviderName= "iotedged";
     LogName = "application"; StartTime = [datetime]::Today} |
   select TimeCreated, Message |
   sort-object @{Expression="TimeCreated";Descending=$false} |
   format-table -autosize -wrap
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Ha az IoT Edge biztonsági kezelője nem fut, ellenőrizze a yaml-konfigurációs fájl

> [!WARNING]
> YAML-fájlok nem lehetnek identation lapokon. Használja helyette a 2 szóközöket.

Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows rendszeren:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Ellenőrizze a tároló naplóinak problémák

Miután az IoT Edge biztonsági démon fut, tekintse meg a naplókat a tárolók a hibák észlelése. Kezdje az üzembe helyezett tárolókkal, majd tekintse meg az IoT Edge-futtatókörnyezetet alkotó tárolókat: az Edge Agentet és az Edge Hubot. Az Edge Agent-naplók általában az egyes tárolók életciklusáról nyújtanak információt. Az Edge Hub-naplók az üzenetküldésről és az útválasztásról nyújtanak információt. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-edge-hub"></a>Az Edge hubon áthaladó üzeneteket megtekintése

Megtekintheti az Edge hubon áthaladó üzeneteket, és gyűjtsön információt a részletes naplók eszköztulajdonságok frissítéseiről a edgeAgent és edgeHub futtatókörnyezet tárolóiból származó. Ezek a tárolók a részletes naplók bekapcsolásához állítsa `RuntimeLogLevel` a yaml-konfigurációs fájlban. A fájl megnyitásához:

Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows rendszeren:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Alapértelmezés szerint a `agent` elem lesz a következőhöz hasonló:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Cserélje le `env: {}` együtt:

> [!WARNING]
> YAML-fájlok nem lehetnek identation lapokon. Használja helyette a 2 szóközöket.

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

Mentse a fájlt, és indítsa újra a IoT Edge-kezelő.

Az IoT Hub és az IoT Edge-eszközök között küldött üzeneteket is ellenőrizheti. Ezeket az üzeneteket a Visual Studio Code [Azure IoT-eszközkészlet](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) bővítményével tekintheti meg. További útmutatásért lásd [az Azure IoT-fejlesztések esetén hasznos eszközt](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Indítsa újra a tárolók
Miután megvizsgálta a naplók és üzenetek információit, próbálja meg újraindítani a tárolók:

```
iotedge restart <container name>
```

Indítsa újra az IoT Edge-futtatókörnyezet tárolóiból:

```
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Indítsa újra az IoT Edge-biztonságkezelő

Ha a probléma továbbra is átlátni, próbálja meg újraindítani az IoT Edge-biztonságkezelő.

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
Győződjön meg arról, hogy a tárolójegyzék hitelesítő adatainak helyesen vannak megadva a manifest nasazení

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge biztonsági démon meghiúsul, és a egy érvénytelen állomásnév

A parancs `sudo journalctl -u iotedge` meghiúsul, és kinyomtatja a következő üzenet: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Gyökérok
Az IoT Edge-futtatókörnyezet csak támogatja, amelyek 64 karakternél rövidebb gazdanévvel. Ez általában nem jelent problémát, fizikai gépek számára, de előfordulhatnak, ha a virtuális gépen a futtatókörnyezet beállítása. Az automatikusan létrehozott gazdanevek Windows virtuális gépek az Azure-ban üzemeltetett, általában hosszú. 

### <a name="resolution"></a>Megoldás:
Ha ezt a hibát látja, feloldhatja konfigurálásával a virtuális gép DNS-nevét, és beállítja a DNS-nevét a setup parancs az állomásnevet.

1. Az Azure Portalon lépjen a virtuális gép áttekintés oldalán. 
2. Válassza ki **konfigurálása** DNS-neve alatt. Ha a virtuális géphez már tartozik egy DNS-név konfigurálva, nem kell egy új konfigurálása. 

   ![Konfigurálja a DNS-név](./media/troubleshoot/configure-dns.png)

3. Adjon meg egy értéket a **DNS-névcímke** válassza **mentése**.
4. Másolja a következő formátumban kell lennie új DNS-név  **\<DNSnamelabel\>.\< vmlocation\>. cloudapp.Azure.com formát követi**.
5. A virtuális gépen belül a következő parancs használatával állítsa be az IoT Edge-futtatókörnyezet, a DNS-névvel:

   - Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - Windows rendszeren:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Erőforrás stabilitási problémák korlátozott eszközök 
Felmerülhet korlátozott eszközökön, például a Raspberry Pi-októl stabilitását, különösen akkor, ha az átjáróként használt. Memória kivételeket az edge hub modul kívül tünetei, alsóbb rétegbeli eszközök nem csatlakoznak, vagy az eszköz nem a telemetriai üzeneteket küld a néhány óra múlva.

### <a name="root-cause"></a>Gyökérok
Az edge hub, az edge-futtatókörnyezet része, amely alapértelmezés szerint a teljesítmény optimalizáltuk, és megpróbálja nagy mennyiségű memóriát lefoglalni. Ez nem ideális korlátozott edge-eszközök és stabilitását problémákat okozhat.

### <a name="resolution"></a>Megoldás:
Az Edge hub környezeti változó értéke **OptimizeForPerformance** való **hamis**. Ehhez két módja van:

A felhasználói felületen: 

A portálon *eszközadatok*->*modulok beállítása*->*speciális Edge-futtatókörnyezet-beállítások konfigurálása*, egy környezeti változó létrehozása nevű *OptimizeForPerformance* , amely *hamis* számára a *Edge hubot*.

![optimizeforperformance][img-optimize-for-perf]

**VAGY**

A manifest nasazení:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```
## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Nem olvasható be az IoT Edge démon naplóit a Windows
Ha egy EventLogException használatakor `Get-WinEvent` Windows, ellenőrizze a beállításjegyzékbeli bejegyzéseket.

### <a name="root-cause"></a>Gyökérok
A `Get-WinEvent` PowerShell-paranccsal támaszkodik egy bejegyzés egy adott naplók keresése jelen `ProviderName`.

### <a name="resolution"></a>Megoldás:
Állítsa be az IoT Edge-démon egy beállításjegyzék-bejegyzést. Hozzon létre egy **iotedge.reg** fájlt az alábbi tartalommal, és a Windows beállításjegyzék importálása a dupla kattintással vagy használatával a `reg import iotedge.reg` parancsot:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```


## <a name="next-steps"></a>További lépések
Úgy gondolja, hogy hibát talált az IoT Edge platformon? Kérjük, a kijavításához [küldje el a problémát](https://github.com/Azure/iotedge/issues). 

<!-- Images -->
[img-optimize-for-perf]: ./media/troubleshoot/OptimizeForPerformanceFalse.png
