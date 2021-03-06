---
title: Hibakeresés – Azure IoT Edge | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan sajátíthatja el a Azure IoT Edge általános diagnosztikai képességeit, például az összetevők állapotának és naplóinak beolvasását
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 035cf5be4471cad7ac11eb8ce9a8a0ecb13a68da
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462366"
---
# <a name="troubleshoot-your-iot-edge-device"></a>A IoT Edge eszköz hibáinak megoldása

Ha a környezetében Azure IoT Edge futtatott problémákat tapasztal, a hibaelhárításhoz és a diagnosztikahez tekintse meg a cikk útmutatását.

## <a name="run-the-check-command"></a>A "pipa" parancs futtatása

Az első lépés a IoT Edge hibaelhárításakor a `check` parancs használata, amely a konfigurációs és kapcsolati tesztek gyűjteményét futtatja a gyakori problémákhoz. A `check` parancs a [kiadási 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) és újabb verziókban érhető el.

>[!NOTE]
>A hibaelhárítási eszköz nem tudja futtatni a kapcsolati ellenőrzéseket, ha a IoT Edge-eszköz proxykiszolgáló mögött van.

A parancsot a következőképpen futtathatja `check` , vagy belefoglalhatja a `--help` jelzőt a lehetőségek teljes listájának megjelenítéséhez:

Linux rendszeren:

```bash
sudo iotedge check
```

Windows rendszeren:

```powershell
iotedge check
```

A hibaelhárító eszköz sok olyan ellenőrzést futtat, amelyek a következő három kategóriába vannak rendezve:

* A *konfigurációs ellenőrzések* olyan részleteket vizsgálnak meg, amelyek megakadályozhatják, hogy IoT Edge eszközök csatlakozzanak a felhőhöz, beleértve a *config. YAML* és a Container motorral kapcsolatos problémákat is.
* A *kapcsolat ellenőrzi* , hogy a IoT Edge futtatókörnyezet hozzáférhet-e a gazdagép-eszköz portjaihoz, és hogy az összes IoT Edge-összetevő csatlakozni tud-e a IoT hubhoz. Ez az ellenőrzés hibákat ad vissza, ha a IoT Edge eszköz proxy mögött van.
* Az *éles üzemi készültségi ellenőrzések* az ajánlott éles környezetek, például az eszközök hitelesítésszolgáltatói tanúsítványai és a modul naplófájljai konfigurációjának állapotát keresik.

A IoT Edge-ellenőrzési eszköz tárolót használ a diagnosztika futtatásához. A tároló képe `mcr.microsoft.com/azureiotedge-diagnostics:latest` a [Microsoft Container Registryon](https://github.com/microsoft/containerregistry)keresztül érhető el. Ha az internethez való közvetlen hozzáférés nélkül kell ellenőriznie az eszközt, az eszköznek hozzá kell férnie a tároló rendszerképéhez.

További információ az eszköz által futtatott diagnosztikai ellenőrzésekről, beleértve a teendőket, ha hibaüzenetet vagy figyelmeztetést kap: [IoT Edge-hibakeresési ellenőrzés](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-support-bundle-command"></a>Hibakeresési adatok összegyűjtése a "support-Bundle" paranccsal

Ha IoT Edge-eszközről kell összegyűjtenie a naplókat, a legkényelmesebb módszer a `support-bundle` parancs használata. Alapértelmezés szerint ez a parancs a modult, IoT Edge a Security Manager és a Container Engine-naplókat, a `iotedge check` JSON-kimenetet és más hasznos hibakeresési információkat gyűjt. Egyetlen fájlba tömöríti őket, így könnyen megosztható. A `support-bundle` parancs a [kiadási 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) és újabb verziókban érhető el.

Futtassa a `support-bundle` parancsot a `--since` jelzővel annak megadásához, hogy a múltban mennyi ideig szeretné lekérni a naplókat. Például az utolsó hat óra óta az elmúlt hat órában, az elmúlt hat `6h` `6d` `6m` percben és így tovább. A `--help` beállítások teljes listájának megjelenítéséhez adja meg a jelzőt.

Linux rendszeren:

```bash
sudo iotedge support-bundle --since 6h
```

Windows rendszeren:

```powershell
iotedge support-bundle --since 6h
```

[Közvetlen metódussal](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics) is meghívhatja az eszközt, hogy feltöltse a support-Bundle parancs kimenetét az Azure Blob Storageba.

> [!WARNING]
> A parancs kimenete `support-bundle` tartalmazhatja a gazdagép, az eszköz és a modul nevét, a modulok által naplózott adatokat stb. Kérjük, vegye figyelembe, hogy ha a kimenetet egy nyilvános fórumon osztja meg.

## <a name="check-your-iot-edge-version"></a>A IoT Edge verziójának keresése

Ha az IoT Edge egy régebbi verzióját futtatja, az új verzióra történő frissítés megoldhatja a problémát. Az `iotedge check` eszköz ellenőrzi, hogy a IoT Edge biztonsági démon a legújabb verzió-e, de nem ellenőrzi az IoT Edge hub és az ügynök moduljainak verzióját. Az eszközön futó futásidejű modulok verziójának vizsgálatához használja a parancsokat és a `iotedge logs edgeAgent` parancsot `iotedge logs edgeHub` . A verziószámot a rendszer a naplókban határozza meg a modul indításakor.

Az eszköz frissítésével kapcsolatos utasításokért tekintse meg [a IoT Edge biztonsági démon és futtatókörnyezet frissítése](how-to-update-iot-edge.md)című témakört.

## <a name="verify-the-installation-of-iot-edge-on-your-devices"></a>IoT Edge telepítésének ellenőrzése az eszközökön

Az eszközökön IoT Edge telepítésének ellenőrzéséhez tekintse meg [a edgeAgent-modul Twin](./how-to-monitor-module-twins.md)utasításait.

Ha a legújabb edgeAgent-modult szeretné beszerezni, futtassa a következő parancsot [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub module-twin show --device-id <edge_device_id> --module-id $edgeAgent --hub-name <iot_hub_name>
   ```

Ezzel a paranccsal a rendszer az összes [jelentett edgeAgent-tulajdonságot](./module-edgeagent-edgehub.md)megjeleníti. Íme néhány hasznos funkció az eszköz állapotának figyeléséhez:

* futtatókörnyezet állapota
* futtatókörnyezet indítási ideje
* futtatókörnyezet utolsó kilépésének időpontja
* futásidejű újraindítások száma

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>A IoT Edge Security Manager és a naplók állapotának ellenõrzése

A [IoT Edge Security Manager](iot-edge-security-manager.md) felelős az olyan műveletekért, mint a IoT Edge rendszer inicializálásakor az indítási és kiépítési eszközökön. Ha IoT Edge nem indul el, a Security Manager naplói hasznos információkat nyújthatnak.

Linux rendszeren:

* A IoT Edge Security Manager állapotának megtekintése:

   ```bash
   sudo systemctl status iotedge
   ```

* A IoT Edge Security Manager naplóinak megtekintése:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Tekintse meg a IoT Edge Security Manager részletes naplóit:

  * IoT Edge Daemon-beállítások szerkesztése:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Frissítse a következő sorokat:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Indítsa újra a IoT Edge biztonsági démont:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

Windows rendszeren:

* A IoT Edge Security Manager állapotának megtekintése:

   ```powershell
   Get-Service iotedge
   ```

* A IoT Edge Security Manager naplóinak megtekintése:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* Csak a IoT Edge Security Manager-naplók utolsó 5 percét tekintheti meg:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* Tekintse meg a IoT Edge Security Manager részletes naplóit:

  * Rendszerszintű környezeti változó hozzáadása:

      ```powershell
      [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "debug", [EnvironmentVariableTarget]::Machine)
      ```

  * Indítsa újra a IoT Edge biztonsági démont:

      ```powershell
      Restart-Service iotedge
      ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Ha a IoT Edge Security Manager nem fut, ellenőrizze a YAML konfigurációs fájlját.

> [!WARNING]
> A YAML-fájlok nem tartalmazhatnak behúzást tartalmazó lapokat. Ehelyett használjon két szóközt. A legfelső szintű elemeknek nincsenek kezdő szóközök.

Linux rendszeren:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows rendszeren:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="restart-the-iot-edge-security-manager"></a>A IoT Edge Security Manager újraindítása

Ha a probléma továbbra is fennáll, próbálja meg újraindítani a IoT Edge Security Managert.

Linux rendszeren:

   ```cmd
   sudo systemctl restart iotedge
   ```

Windows rendszeren:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="check-container-logs-for-issues"></a>A problémákhoz tartozó tároló-naplók keresése

Ha a IoT Edge biztonsági démon fut, tekintse meg a tárolók naplóit a problémák észleléséhez. Indítsa el a telepített tárolókat, majd tekintse meg a IoT Edge futtatókörnyezetet alkotó tárolókat: edgeAgent és edgeHub. A IoT Edge-ügynök naplói általában az egyes tárolók életciklusára vonatkozó információkat biztosítanak. Az IoT Edge hub naplói az üzenetküldéssel és az útválasztással kapcsolatos információkat biztosítanak.

```cmd
iotedge logs <container name>
```

Az eszközön található egyik modulhoz is használhat [közvetlen metódust](how-to-retrieve-iot-edge-logs.md#upload-module-logs) , hogy feltöltse a modul naplóit az Azure Blob Storageba.

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Az IoT Edge hub-on keresztül haladó üzenetek megtekintése

Megtekintheti az IoT Edge hub-on keresztül megjelenő üzeneteket, és bepillantást nyerhet a részletes naplókból a futásidejű tárolóból. A részletes naplók ezen tárolók bekapcsolásához állítsa be a `RuntimeLogLevel` YAML konfigurációs fájlját. A fájl megnyitása:

Linux rendszeren:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows rendszeren:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Alapértelmezés szerint az `agent` elem a következő példához hasonlóan fog kinézni:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Csere `env: {}` ezzel:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > A YAML-fájlok nem tartalmazhatnak a lapokat identitásként. Ehelyett használjon két szóközt. A legfelső szintű elemek nem rendelkezhetnek kezdő szóközökkel.

Mentse a fájlt, és indítsa újra a IoT Edge Security Manager alkalmazást.

Az IoT Hub és az IoT Edge-eszközök között küldött üzeneteket is ellenőrizheti. A [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)-hoz készült Azure IoT hub-bővítmény használatával tekintheti meg ezeket az üzeneteket. További információ: [praktikus eszköz az Azure IoT való fejlesztés során](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

## <a name="restart-containers"></a>Tárolók újraindítása

A naplók és üzenetek adatainak kivizsgálása után próbálkozzon újra a tárolók újraindításával:

```cmd
iotedge restart <container name>
```

Indítsa újra a IoT Edge futtatókörnyezeti tárolókat:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

## <a name="check-your-firewall-and-port-configuration-rules"></a>A tűzfal és a port konfigurációs szabályainak megkeresése

Azure IoT Edge lehetővé teszi a helyszíni kiszolgálóról az Azure-felhőbe való kommunikációt a támogatott IoT Hub protokollok használatával: [kommunikációs protokoll kiválasztása](../iot-hub/iot-hub-devguide-protocols.md). A fokozott biztonság érdekében a Azure IoT Edge és az Azure IoT Hub közötti kommunikációs csatornák mindig Kimenőként vannak konfigurálva. Ez a konfiguráció a szolgáltatások által [támogatott kommunikációs minta](/archive/blogs/clemensv/service-assisted-communication-for-connected-devices)alapján történik, ami lekicsinyíti a támadási felületet egy rosszindulatú entitás számára a felderítéshez. A bejövő kommunikációra csak bizonyos esetekben van szükség, amikor az Azure-IoT Hub üzeneteket kell leküldenie az Azure IoT Edge eszköznek. A felhőből az eszközre irányuló üzenetek biztonságos TLS-csatornák használatával védhetők, és az X. 509 tanúsítványokkal és TPM-eszközök moduljaival tovább biztonságossá tehetők. A Azure IoT Edge Security Manager szabályozza, hogyan lehet létrehozni a kommunikációt: [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

Míg a IoT Edge továbbfejlesztett konfigurációt biztosít Azure IoT Edge futtatókörnyezet és a telepített modulok biztonságossá tételéhez, továbbra is függ a mögöttes gépről és hálózati konfigurációtól. Ezért fontos, hogy a megfelelő hálózati és tűzfalszabályok a Felhőbeli kommunikáció biztonságos peremén legyenek beállítva. A következő táblázat használható a konfigurációs tűzfalszabályok azon alapuló kiszolgálókon való konfigurálásához, amelyeken a Azure IoT Edge Runtime fut:

|Protokoll|Port|Bejövő|Kimenő|Útmutató|
|--|--|--|--|--|
|MQTT|8883|Letiltva (alapértelmezett)|Letiltva (alapértelmezett)|<ul> <li>Konfigurálja a kimenő (kimenő) t, ha a MQTT kommunikációs protokollként való használatakor megnyitható.<li>a IoT Edge nem támogatja a MQTT használatát. 1883 <li>A bejövő (bejövő) kapcsolatokat le kell tiltani.</ul>|
|AMQP|5671|Letiltva (alapértelmezett)|Megnyitás (alapértelmezett)|<ul> <li>Az IoT Edge alapértelmezett kommunikációs protokollja. <li> Úgy kell konfigurálni, hogy nyitva legyen, ha Azure IoT Edge nincs más támogatott protokollhoz konfigurálva, vagy a AMQP a kívánt kommunikációs protokoll.<li>a IoT Edge nem támogatja a AMQP használatát. 5672<li>A port letiltása, ha a Azure IoT Edge egy másik IoT Hub támogatott protokollt használ.<li>A bejövő (bejövő) kapcsolatokat le kell tiltani.</ul></ul>|
|HTTPS|443|Letiltva (alapértelmezett)|Megnyitás (alapértelmezett)|<ul> <li>Konfigurálja a kimenő (kimenő) 443-es megnyitását IoT Edge kiépítés esetén. Ez a konfiguráció kézi parancsfájlok vagy Azure IoT Device kiépítési szolgáltatás (DPS) használata esetén szükséges. <li>A bejövő (bejövő) kapcsolatok csak bizonyos esetekben nyithatók meg: <ul> <li>  Ha olyan transzparens átjáróval rendelkezik, amely olyan Leaf-eszközökkel rendelkezik, amelyek metódus-kérelmeket küldhetnek. Ebben az esetben a 443-es portot nem szabad külső hálózatokhoz csatlakozni a IoTHub-hez való csatlakozáshoz vagy a IoTHub szolgáltatások nyújtásához Azure IoT Edgeon keresztül. Így a Bejövő szabály csak a bejövő (bejövő) és a belső hálózatról nyitható meg. <li> Az ügyfél – eszköz (C2D) forgatókönyvek esetében.</ul><li>a IoT Edge nem támogatja a HTTP-t (80).<li>Ha nem HTTP protokoll (például AMQP vagy MQTT) nem konfigurálható a vállalatban; az üzenetek elküldhetők websocketek használatával. Ebben az esetben a 443-es portot fogja használni a WebSocket-kommunikációhoz.</ul>|

## <a name="next-steps"></a>További lépések

Úgy gondolja, hogy hibát talált az IoT Edge platformon? [Küldjön el egy problémát](https://github.com/Azure/iotedge/issues) , hogy tovább javítsuk.

Ha további kérdései vannak, hozzon létre egy [support Request](https://portal.azure.com/#create/Microsoft.Support) segítséget.