---
title: Hibaelhárítás – az Azure IoT Edge |} A Microsoft Docs
description: Ebben a cikkben megismerheti standard diagnosztikai képességek az Azure IoT Edge, például összetevő-állapot és a naplók beolvasása, és a leggyakoribb hibák elhárításához
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 448fe3e7d6f3129ce6a40494abcf993f6dc4e4f7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081404"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Az Azure IoT Edge gyakori problémái és azok megoldásai

Ha a környezetében az Azure IoT Edge futtatásakor problémákat tapasztal, ezt a cikket útmutatóként használhatja a hibaelhárításhoz és a megoldáshoz. 

## <a name="standard-diagnostic-steps"></a>Általános diagnosztikai lépések 

Ha problémát tapasztal, további információ az IoT Edge-eszköz állapotát a tárolónaplók és az üzeneteket, hogy az eszköz áttekintésével. Az ebben a szakaszban szereplő parancsokkal és eszközökkel további információt gyűjthet. 

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

Megtekintheti az Edge hubon áthaladó üzeneteket, és gyűjtsön információt a futtatókörnyezet tárolóiból származó részletes naplók a. Ezek a tárolók a részletes naplók bekapcsolásához állítsa `RuntimeLogLevel` a yaml-konfigurációs fájlban. A fájl megnyitásához:

Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows rendszeren:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Alapértelmezés szerint a `agent` elem a következő példához hasonlóan fog kinézni:

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

Az IoT Hub és az IoT Edge-eszközök között küldött üzeneteket is ellenőrizheti. Ezeket az üzeneteket a Visual Studio Code [Azure IoT-eszközkészlet](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) bővítményével tekintheti meg. További információkért lásd: [hasznos eszközt az Azure IoT fejlesztése során](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

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

Az Edge Agent elindul, és körülbelül egy percig sikeresen fut, majd leáll. A naplók jelzik, hogy az Edge Agent megpróbál kapcsolódni az IoT hubhoz amqp-n keresztül, és ezután megpróbál csatlakozni az AMQP használatával websocketen. Ha ez nem sikerül, az Edge Agent kilép. 

Példa az Edge Agent-naplókra:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Gyökérok
A gazdahálózaton egy hálózati konfiguráció meggátolja, hogy az Edge Agent elérje a hálózatot. Az ügynök először megpróbál AMQP-n keresztül csatlakozni (az 5671-es porton). Ha a kapcsolódás sikertelen, a websockettel (a 443-as porton).

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
Az IoT Edge-futtatókörnyezet csak támogatja, amelyek 64 karakternél rövidebb gazdanévvel. Fizikai gépek általában nem rendelkezik hosszú állomásnevek, de a probléma gyakoribb a virtuális gépen. Az automatikusan létrehozott gazdanevek Windows virtuális gépek az Azure-ban üzemeltetett, általában hosszú. 

### <a name="resolution"></a>Megoldás:
Ha ezt a hibát látja, feloldhatja konfigurálásával a virtuális gép DNS-nevét, és beállítja a DNS-nevét a setup parancs az állomásnevet.

1. Az Azure Portalon lépjen a virtuális gép áttekintés oldalán. 
2. Válassza ki **konfigurálása** DNS-neve alatt. Ha a virtuális géphez már tartozik egy DNS-név konfigurálva, nem kell egy új konfigurálása. 

   ![Konfigurálja a virtuális gép DNS-neve](./media/troubleshoot/configure-dns.png)

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
Az edge hub, az edge-futtatókörnyezet része, amely alapértelmezés szerint a teljesítmény optimalizáltuk, és megpróbálja nagy mennyiségű memóriát lefoglalni. Az optimalizálás nem ideális korlátozott peremhálózati eszközökre, és stabilitását problémákat okozhat.

### <a name="resolution"></a>Megoldás:
Az edge hub, a környezeti változó értéke **OptimizeForPerformance** való **hamis**. Ehhez két módja van:

A felhasználói felületen: 

A portálon *eszközadatok*->*modulok beállítása*->*speciális Edge-futtatókörnyezet-beállítások konfigurálása*, egy környezeti változó létrehozása nevű *OptimizeForPerformance* , amely *hamis* számára a *Edge hubot*.

![OptimizeForPerformance "false" értékűre.](./media/troubleshoot/optimizeforperformance-false.png)

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
A `Get-WinEvent` PowerShell-paranccsal támaszkodik egy beállításjegyzékbeli bejegyzést jelen naplók keresése egy adott `ProviderName`.

### <a name="resolution"></a>Megoldás:
Állítsa be az IoT Edge-démon egy beállításjegyzék-bejegyzést. Hozzon létre egy **iotedge.reg** fájlt az alábbi tartalommal, és a Windows beállításjegyzék importálása a dupla kattintással vagy használatával a `reg import iotedge.reg` parancsot:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>IoT Edge-modul egy üzenet küldéséhez az edgeHub 404-es hibaüzenettel meghiúsul

IoT Edge-modul nem tud üzenetet küldeni a edgeHub a 404-es egyéni `Module not found` hiba. Az IoT Edge-démon a naplókat az alábbi üzenetet jelenít meg: 

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 ) 
```

### <a name="root-cause"></a>Gyökérok
Az IoT Edge-démon érvényesíti a folyamat azonosítóját összes modult a biztonsági okokból edgeHub csatlakozik. Ellenőrzi, hogy egy modul által küldött összes üzenet származik-e a fő a modul Folyamatazonosítója. Egy üzenet egy másik folyamat azonosítója, mint az eredetileg létrehozott a modul által küldött, ha azt az üzenetet a 404-es hibaüzenettel elutasítja.

### <a name="resolution"></a>Megoldás:
Győződjön meg arról, hogy az azonos Folyamatazonosító mindig használják az egyéni IoT Edge-modul üzeneteket küldeni a edgeHub. Például, ügyeljen arra, hogy `ENTRYPOINT` helyett `CMD` parancsot a Docker-fájlban, mivel `CMD` eredményezi egy folyamat. a modul és a bash-parancs a fő program fut, mivel egy másik folyamat azonosító `ENTRYPOINT` lesz egy egyetlen folyamat azonosítója.


## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Üzemelő IoT Edge-példány konfigurációs szabályokat tűzfal- és portbeállítások
Az Azure IoT Edge lehetővé teszi, hogy az Azure-felhőben az IoT Hub támogatott protokollok használatával és egy helyszíni peremhálózati kiszolgáló közötti kommunikáció, lásd: [kommunikációs protokoll kiválasztása](../iot-hub/iot-hub-devguide-protocols.md). A fokozott biztonság érdekében a kommunikációs csatornák között az Azure IoT Edge és az Azure IoT Hub mindig konfigurálni kell a kimenő. Ez a konfiguráció alapján a [szolgáltatások támogatott kommunikációs mintát](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), amely minimálisra csökkenti a támadási felületet a rosszindulatú entitás megismerése. Bejövő kommunikáció csak akkor szükséges, ahol az Azure IoT Hub kell üzenetek leküldése az Azure IoT Edge-eszköz bizonyos forgatókönyvek esetén. Felhőből az eszközre irányuló üzenetek biztonságos TLS-csatorna használatával véd, és további segítségével biztosítható X.509-tanúsítványokat és a TPM-eszköz modulok. Az Azure IoT Edge biztonsági Manager szabályozza, hogy ez a kommunikáció hogyan lehet létrehozni, tekintse meg [IoT Edge-biztonságkezelő](../iot-edge/iot-edge-security-manager.md).

IoT Edge biztosít továbbfejlesztett konfigurálása az Azure IoT Edge-futtatókörnyezet, és üzembe helyezett modulokat, az továbbra is függ az alapul szolgáló machine és a hálózati konfiguráció. Ezért elengedhetetlen, annak érdekében, hogy biztonságos peremhálózati és a felhő közötti kommunikációhoz megfelelő hálózati és tűzfalbeállításokat szabályok vannak beállítva. A következő használhatja útmutatóként tűzfalszabályok konfigurálása az alapul szolgáló kiszolgálók az Azure IoT Edge-futtatókörnyezet üzemeltető:

|Protokoll|Port|bejövő|Kimenő|Útmutatás|
|--|--|--|--|--|
|MQTT|8883|TILTOTT (alapértelmezett)|TILTOTT (alapértelmezett)|<ul> <li>Konfigurálja a kimenő (kimenő) kell nyílt, amikor az MQTT protokoll használatával.<li>az MQTT 1883 IoT Edge által nem támogatott. <li>Bejövő (bejövő) kapcsolatok le kell tiltani.</ul>|
|AMQP|5671|TILTOTT (alapértelmezett)|NYÍLT (alapértelmezett)|<ul> <li>Alapértelmezett kommunikációs protokollt az IoT Edge-hez. <li> Nyissa meg kell, ha más támogatott protokollok nincs konfigurálva az Azure IoT Edge vagy az AMQP protokoll kívánt kell konfigurálni.<li>az AMQP 5672 IoT Edge által nem támogatott.<li>Tiltsa le ezt a portot, ha az Azure IoT Edge használja egy másik IoT Hub protokoll támogatott.<li>Bejövő (bejövő) kapcsolatok le kell tiltani.</ul></ul>|
|HTTPS|443|TILTOTT (alapértelmezett)|NYÍLT (alapértelmezett)|<ul> <li>Konfigurálja a kimenő (kimenő) kell nyissa meg a 443-as kiépítése az IoT Edge. Ez a konfiguráció manuális parancsprogramokkal vagy az Azure IoT Device Provisioning Service (DPS) szükség. <li>Bejövő (bejövő) kapcsolatot kell lennie a nyílt csak az adott forgatókönyveket: <ul> <li>  Ha a levéleszközök is küldhet kéréseket metódus, amely transzparens átjáró. Ebben az esetben 443-as portot nem kell megnyitni a külső hálózatokhoz IoTHub csatlakozni, vagy adja meg az IoTHub-szolgáltatások az Azure IoT Edge segítségével. Így a bejövő szabály csak megnyitásához a belső hálózatról bejövő (bejövő) korlátozott lehet. <li> -Ügyfél (C2D) eszközök esetén.</ul><li>IoT Edge által nem támogatott a 80-as HTTP-hez.<li>Ha nem HTTP-protokoll (például AMQP és MQTT) nem konfigurálható a vállalat; az üzenetek küldhetők a websockets protokoll. 443-as portot ebben az esetben WebSocket-kommunikációhoz fogja használni.</ul>|


## <a name="next-steps"></a>További lépések
Úgy gondolja, hogy hibát talált az IoT Edge platformon? [Küldje el a problémát](https://github.com/Azure/iotedge/issues) , hogy továbbra is javíthatja. 

Ha további kérdése van, hozzon létre egy [támogatási kérelem](https://portal.azure.com/#create/Microsoft.Support) segítséget. 

