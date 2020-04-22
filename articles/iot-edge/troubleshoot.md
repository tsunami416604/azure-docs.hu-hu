---
title: Hibaelhárítás – Azure IoT Edge | Microsoft dokumentumok
description: Ezzel a cikkel az Azure IoT Edge szabványos diagnosztikai készségeinek elsajátítása, például az összetevők állapotának és naplóinak beolvasása, valamint a gyakori problémák megoldása
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 3529d6a67c6c8c19c053fe3170298658e90b4a54
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729275"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Az Azure IoT Edge gyakori problémái és azok megoldásai

Ha a környezetében az Azure IoT Edge futtatásakor problémákat tapasztal, ezt a cikket útmutatóként használhatja a hibaelhárításhoz és a megoldáshoz.

## <a name="run-the-iotedge-check-command"></a>Futtassa az iotedge "check" parancsot

Az első lépés az IoT Edge hibaelhárításakor kell használni a `check` parancsot, amely a konfigurációs és kapcsolódási tesztek gyűjteménye fut a gyakori problémák. A `check` parancs az [1.0.7-es](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) és újabb verzióban érhető el.

A parancs `check` futtatásához az alábbiak szerint, vagy a `--help` zászló, hogy egy teljes listát a lehetőségek:

* Linuxalatt:

  ```bash
  sudo iotedge check
  ```

* Windows rendszeren:

  ```powershell
  iotedge check
  ```

Az eszköz által végzett ellenőrzések típusai a következőképpen sorolhatók be:

* Konfigurációs ellenőrzések: Megvizsgálja azokat a részleteket, amelyek megakadályozhatják, hogy az Edge-eszközök csatlakozzanak a felhőhöz, beleértve a *config.yaml* és a tárolómotor problémáit.
* Kapcsolatellenőrzések: Ellenőrzi, hogy az IoT Edge futásidejű elérheti-e a portokat a gazdaeszközön, és az összes IoT Edge-összetevő csatlakozhat az IoT Hubhoz.
* Üzemkészültségi ellenőrzések: Az ajánlott gyártási gyakorlati tanácsok, például az eszközhitelesítési tanúsítványok állapota és a modul naplófájljának konfigurációja.

A diagnosztikai ellenőrzések teljes listáját [a Beépített hibaelhárítási funkció című témakörben](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md)található.

## <a name="gather-debug-information-with-iotedge-support-bundle-command"></a>Hibakeresési információk gyűjtése az iotedge "support-bundle" paranccsal

Ha egy IoT Edge-eszközről kell naplókat gyűjtenie, a `support-bundle` legkényelmesebb módja a parancs használata. Alapértelmezés szerint ez a parancs összegyűjti a modult, az IoT Edge Security Manager téseit és a tárolómotor-naplókat, az "iotedge check" JSON kimenetet és más hasznos hibakeresési információkat. Tömöríti őket egyetlen fájlba a könnyű megosztás érdekében. A `support-bundle` parancs az [1.0.9-es](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) és újabb verzióban érhető el.

Futtassa `support-bundle` a `--since` parancsot a jelzővel, és adja meg, hogy mennyi ideig szeretne naplókat beszerezni a múlttól. Például `6h` kap naplók, mivel az `6d` elmúlt 6 órában, mivel az elmúlt 6 nap, `6m` mivel az elmúlt 6 perc, és így tovább. A `--help` beállítások teljes listájának megtekintéséhez adja meg a jelzőt.


* Linuxalatt:

  ```bash
  sudo iotedge support-bundle --since 6h
  ```

* Windows rendszeren:

  ```powershell
  iotedge support-bundle --since 6h
  ```

> [!WARNING]
> A `support-bundle` parancs kimenete tartalmazhat állomás-, eszköz- és modulneveket, a modulok által naplózott információkat stb. Kérjük, vegye figyelembe ezt, ha megosztja a kimenetet egy nyilvános fórumon.

## <a name="standard-diagnostic-steps"></a>Általános diagnosztikai lépések

Ha problémát tapasztal, az IoT Edge-eszköz állapotáról a tárolónaplók és az eszközről átadott üzenetek áttekintésével többet is megtudhat. Az ebben a szakaszban szereplő parancsokkal és eszközökkel további információt gyűjthet.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Az IoT Edge Security Manager és naplói állapotának ellenőrzése

Linuxalatt:

* Az IoT Edge security manager állapotának megtekintése:

   ```bash
   sudo systemctl status iotedge
   ```

* Az IoT Edge biztonsági kezelőnaplóinak megtekintése:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Az IoT Edge security manager részletesebb naplóinak megtekintéséhez:

  * Az iotedge démonbeállításainak szerkesztése:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Frissítse a következő sorokat:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Indítsa újra az IoT Edge biztonsági démont:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

Windows rendszeren:

* Az IoT Edge security manager állapotának megtekintése:

   ```powershell
   Get-Service iotedge
   ```

* Az IoT Edge biztonsági kezelőnaplóinak megtekintése:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Ha az IoT Edge Security Manager nem fut, ellenőrizze a yaml konfigurációs fájlt

> [!WARNING]
> A YAML-fájlok behúzásként nem tartalmazhatnak tabulátorokat. Használjon helyette 2 szóközt. A legfelső szintű elemeknek nem lehetnek kezdőszóközök.

Linuxalatt:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows rendszeren:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>A tárolónaplók ellenőrzése problémák miatt

Miután az IoT Edge biztonsági démon fut, tekintse meg a naplókat a tárolók problémák észleléséhez. Kezdje az üzembe helyezett tárolókkal, majd tekintse meg az IoT Edge futásidejű: edgeAgent és edgeHub tárolókat. Az IoT Edge-ügynök naplók általában az egyes tárolók életciklusára vonatkozó információkat. Az IoT Edge hub naplók az üzenetküldésésésésés és az útválasztási adatok.

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Az IoT Edge hubon keresztül küldött üzenetek megtekintése

Megtekintheti az Üzenetek et az IoT Edge hubon keresztül, és információkat gyűjthet a futásidejű tárolók részletes naplóiból. Ha be szeretné kapcsolni a részletes naplókat ezeken a tárolókon, állítsa be `RuntimeLogLevel` a yaml konfigurációs fájlban. A fájl megnyitása:

Linuxalatt:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows rendszeren:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Alapértelmezés szerint `agent` az elem a következő példához hasonlóan fog kinézni:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Csere `env: {}` erre:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > A YAML-fájlok nem tartalmazhatnak tabulátorokat identációként. Használjon helyette 2 szóközt. A legfelső szintű elemeknek nem lehet kezdő szóközük.

Mentse a fájlt, és indítsa újra az IoT Edge biztonsági kezelőjét.

Az IoT Hub és az IoT Edge-eszközök között küldött üzeneteket is ellenőrizheti. Tekintse meg ezeket az üzeneteket az [Azure IoT Hub bővítmény a Visual Studio-kódhoz.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) További információ: [Handy tool when you develop with Azure IoT.](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)

### <a name="restart-containers"></a>Tárolók újraindítása

A naplók és az üzenetek vizsgálata után megpróbálhatja újraindítani a tárolókat:

```cmd
iotedge restart <container name>
```

Indítsa újra az IoT Edge futásidejű tárolókat:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Az IoT Edge biztonsági kezelőjének újraindítása

Ha a probléma továbbra is fennáll, megpróbálhatja újraindítani az IoT Edge biztonsági kezelőjét.

Linuxalatt:

   ```cmd
   sudo systemctl restart iotedge
   ```

Windows rendszeren:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>Az IoT Edge-ügynök körülbelül egy perc múlva leáll

Az edgeAgent modul elindul, és sikeresen fut körülbelül egy percig, majd leáll. A naplók azt jelzik, hogy az IoT Edge-ügynök megkísérli az IoT Hubhoz való csatlakozást AMQP-n keresztül, majd megpróbál csatlakozni az AMQP használatával a WebSocketen keresztül. Ha ez nem sikerül, az IoT Edge-ügynök kilép.

Példa edgeAgent naplók:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Gyökérok**

A gazdahálózat hálózati konfigurációja megakadályozza, hogy az IoT Edge-ügynök elérje a hálózatot. Az ügynök először megpróbál AMQP-n keresztül csatlakozni (az 5671-es porton). Ha a kapcsolat meghibásodik, megpróbálja websockets (port 443).

Az IoT Edge-futtatókörnyezet minden modulon beállít egy-egy hálózatot a kommunikációhoz. Linux rendszeren ez a hálózat egy hídhálózat. Windows rendszeren NAT-ot használ. Ez a probléma gyakoribb a NAT-hálózatot használó Windows-tárolókat igénybe vevő windowsos eszközökön.

**Resolution** (Osztás)

Győződjön meg arról, hogy elérhető egy útvonal az internethez az ehhez a híd-/NAT-hálózathoz rendelt IP-címek esetén. Néha a gazdagépen lévő VPN-konfiguráció felülbírálja az IoT Edge-hálózatot.

## <a name="iot-edge-hub-fails-to-start"></a>Az IoT Edge hub nem indul el

Az edgeHub modul nem indul el, és a következő üzenetet nyomtatja ki a naplókba:

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

**Gyökérok**

A gazdagépen egy másik folyamat foglalja le a 443-as portot. Az IoT Edge hub leképezi az 5671-es és 443-as portokat az átjárói forgatókönyvekben való használatra. Ez a portleképezés sikertelen, ha egy másik folyamat már lefoglalta a portot.

**Resolution** (Osztás)

Keresse meg, és állítsa le a 443-as portot használó folyamatot. Ez a folyamat általában a webkiszolgáló.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>Az IoT Edge-ügynök nem tud hozzáférni egy modul lemezképéhez (403)

Egy tároló nem fut, és az edgeAgent naplók 403-as hibát jelenítek meg.

**Gyökérok**

Az IoT Edge-ügynök nem rendelkezik a modul lemezképének eléréséhez szükséges engedélyekkel.

**Resolution** (Osztás)

Győződjön meg arról, hogy a rendszerleíró hitelesítő adatok megfelelően vannak megadva a központi telepítési jegyzékben

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Az IoT Edge biztonsági démona sikertelen egy érvénytelen állomásnévvel

A `sudo journalctl -u iotedge` parancs sikertelen, és a következő üzenetet nyomtatja ki:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Gyökérok**

Az IoT Edge futásidejű csak 64 karakternél rövidebb állomásneveket támogathat. A fizikai gépek általában nem rendelkeznek hosszú állomásnévvel, de a probléma gyakoribb egy virtuális gépen. Az Azure-ban üzemeltetett Windows virtuális gépek automatikusan létrehozott állomásnevei általában hosszúak.

**Resolution** (Osztás)

Ha ezt a hibát látja, a probléma a virtuális gép DNS-nevének konfigurálásával, majd a DNS-név állomásnévként való beállításával oldható meg a telepítési parancsban.

1. Az Azure Portalon keresse meg a virtuális gép áttekintő lapját.
2. Válassza a **Konfigurálás** lehetőséget a DNS-név alatt. Ha a virtuális gép már rendelkezik konfigurált DNS-névvel, nem kell újat konfigurálnia.

   ![A virtuális gép DNS-nevének konfigurálása](./media/troubleshoot/configure-dns.png)

3. Adjon meg egy értéket a **DNS-névcímkéhez,** és válassza a **Mentés gombot.**
4. Másolja az új DNS-nevet, amelynek ** \<DNSnamelabel\>formátumban kell lennie.\< vmlocation\>.cloudapp.azure.com**.
5. A virtuális gépen belül a következő paranccsal állítsa be az IoT Edge futási időt a DNS-nevével:

   * Linuxalatt:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * Windows rendszeren:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Stabilitási problémák az erőforrásokkal korlátozott eszközökön

Stabilitási problémák léphetnek fel a korlátozott eszközökön, például a Raspberry Pi-n, különösen, ha átjáróként használják. A jelenségek közé tartoznak a peremhálózati hubmodulban lévő memóriakivételek, az alsóbb rétegbeli eszközök nem tudnak csatlakozni, vagy az eszköz néhány óra elteltével leállítja a telemetriai üzenetek küldését.

**Gyökérok**

Az IoT Edge hub, amely része az IoT Edge futásidejű, alapértelmezés szerint a teljesítmény re van optimalizálva, és megpróbálja lefoglalni a memória nagy része. Ez az optimalizálás nem ideális a korlátozott élű eszközök számára, és stabilitási problémákat okozhat.

**Resolution** (Osztás)

Az IoT Edge hub, állítsa be a környezeti változó **OptimizeForPerformance** **hamis.** A környezeti változók kétféleképpen állíthatók be:

Az Azure Portalon:

Az IoT Hubban válassza ki az IoT Edge-eszközt, és az eszköz részletek lapján válassza a **Modulok** > **futásidejű beállításainak**beállítása lehetőséget. Hozzon létre egy környezeti változót az *OptimizeForPerformance* nevű Edge Hub modulhoz, amely *hamis.*

![OptimizeForPerformance értéke hamis](./media/troubleshoot/optimizeforperformance-false.png)

**Vagy**

A központi telepítési jegyzékben:

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

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Nem lehet beszerezni az IoT Edge démonnaplókat Windows rendszeren

Ha A Windows használatakor `Get-WinEvent` EventLogException-et kap, ellenőrizze a rendszerleíró bejegyzéseket.

**Gyökérok**

A `Get-WinEvent` PowerShell parancs egy rendszerleíró bejegyzésre támaszkodik, amely `ProviderName`egy adott napló tanusítására vonatkozik.

**Resolution** (Osztás)

Állítson be egy rendszerleíró bejegyzést az IoT Edge démonhoz. Hozzon létre egy **iotedge.reg** fájlt a következő tartalommal, és importáljon a `reg import iotedge.reg` Windows rendszerleíró adatbázisába, kattintson duplán, vagy használja a parancsot:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>Az IoT Edge-modul nem tud üzenetet küldeni az edgeHubnak 404-es hibával

Egy egyéni IoT Edge-modul nem küld üzenetet az edgeHub 404-es `Module not found` hibával. Az IoT Edge démon a következő üzenetet nyomtatja ki a naplókba:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Gyökérok**

Az IoT Edge démon biztonsági okokból kényszeríti a folyamatazonosítást az edgeHubhoz biztonsági okokból csatlakozó összes modulhoz. Ellenőrzi, hogy a modul által küldött összes üzenet a modul fő folyamatazonosítójából származik-e. Ha egy üzenetet egy, az eredetileg létrehozotttól eltérő folyamatazonosítóból származó modul küld, akkor 404-es hibaüzenettel utasítja el az üzenetet.

**Resolution** (Osztás)

Az 1.0.7-es verziótól minden modulfolyamat jogosult a csatlakozásra. Ha az 1.0.7-es verzióra való frissítés nem lehetséges, hajtsa végre az alábbi lépéseket. További információt az [1.0.7-es kiadás changelog című témakörben talál.](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1)

Győződjön meg arról, hogy ugyanazt a folyamatazonosítót mindig az egyéni IoT Edge-modul használja az edgeHub üzenetek küldéséhez. Például győződjön `ENTRYPOINT` meg `CMD` arról, hogy a `CMD` parancs helyett a Docker-fájlban, mivel vezet egy folyamat azonosítója a `ENTRYPOINT` modul és egy másik folyamat azonosítója a bash parancsot futtató fő program, míg vezet egyetlen folyamat azonosítóját.

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Tűzfal- és portkonfigurációs szabályok az IoT Edge telepítéséhez

Az Azure IoT Edge lehetővé teszi a kommunikációt egy helyszíni kiszolgálóról az Azure-felhőbe a támogatott IoT Hub protokollok használatával, [lásd: egy kommunikációs protokoll kiválasztása.](../iot-hub/iot-hub-devguide-protocols.md) A fokozott biztonság érdekében az Azure IoT Edge és az Azure IoT Hub közötti kommunikációs csatornák mindig kimenőként vannak konfigurálva. Ez a konfiguráció a [szolgáltatások által támogatott kommunikáció mintán](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/)alapul, amely minimálisra csökkenti a támadási felületet egy rosszindulatú entitás számára. Bejövő kommunikáció csak akkor van szükség, bizonyos forgatókönyvek, ahol az Azure IoT Hub kell üzeneteket küldeni az Azure IoT Edge-eszközre. A felhőből az eszközre irányuló üzenetek biztonságos TLS-csatornákkal védettek, és az X.509-es tanúsítványokkal és a TPM-eszközmodulokkal tovább védhetők. Az Azure IoT Edge Security Manager szabályozza, hogyan lehet létrehozni ezt a kommunikációt, lásd: [IoT Edge Security Manager.](../iot-edge/iot-edge-security-manager.md)

Bár az IoT Edge továbbfejlesztett konfigurációt biztosít az Azure IoT Edge futásidejű és üzembe helyezett modulok biztonságossá tétele érdekében, továbbra is függ az alapul szolgáló gép és hálózati konfiguráció. Ezért elengedhetetlen, hogy megfelelő hálózati és tűzfalszabályok vannak beállítva a biztonságos peremhálózati és felhőalapú kommunikációhoz. Az alábbi táblázat iránymutatásként használható az on-line kiszolgálók tűzfalszabályainak konfigurálásakor, ahol az Azure IoT Edge futásidejű található:

|Protocol (Protokoll)|Port|Bejövő|Kimenő|Útmutatás|
|--|--|--|--|--|
|MQTT|8883|BLOKKOLVA (alapértelmezett)|BLOKKOLVA (alapértelmezett)|<ul> <li>Állítsa be, hogy a kimenő (kimenő) nyitott legyen, ha az MQTT protokollt használja kommunikációs protokollként.<li>1883 az MQTT nem támogatja az IoT Edge. <li>A bejövő (bejövő) kapcsolatokat le kell tiltani.</ul>|
|AMQP|5671|BLOKKOLVA (alapértelmezett)|OPEN (alapértelmezett)|<ul> <li>Az IoT Edge alapértelmezett kommunikációs protokollja. <li> Nyitottnak kell lennie, ha az Azure IoT Edge nincs konfigurálva más támogatott protokollokhoz, vagy az AMQP a kívánt kommunikációs protokoll.<li>Az AMQP 5672-es tartományt az IoT Edge nem támogatja.<li>Tiltsa le ezt a portot, ha az Azure IoT Edge egy másik IoT Hub által támogatott protokollt használ.<li>A bejövő (bejövő) kapcsolatokat le kell tiltani.</ul></ul>|
|HTTPS|443|BLOKKOLVA (alapértelmezett)|OPEN (alapértelmezett)|<ul> <li>Konfigurálja a kimenő (kimenő) nyitott 443-on IoT Edge-kiépítés. Ez a konfiguráció manuális parancsfájlok vagy Az Azure IoT-eszközlétesítési szolgáltatás (DPS) használata esetén szükséges. <li>A bejövő (bejövő) kapcsolat csak bizonyos esetekben legyen megnyitva: <ul> <li>  Ha van egy átlátszó átjáró levél eszközök, amelyek metóduskérelmek küldésére. Ebben az esetben a 443-as portnak nem kell nyitottnak lennie a külső hálózatok számára az IoTHubhoz való csatlakozáshoz vagy az Azure IoT Edge-en keresztül ioTHub-szolgáltatások biztosításához. Így a bejövő szabály csak a belső hálózatról nyitott bejövő (bejövő) területre korlátozható. <li> Ügyfélről eszközre (C2D) forgatókönyvek.</ul><li>Az IoT Edge nem támogatja a 80 http-hez.<li>Ha a nem HTTP protokollok (például AMQP vagy MQTT) nem konfigurálhatók a vállalaton belül; az üzenetek et websocketeken keresztül lehet elküldeni. Ebben az esetben a 443-as port ot használjuk a WebSocket kommunikációhoz.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Az Edge Agent modul folyamatosan jelenti az "üres konfigurációs fájlt", és nem indulnak el modulok az eszközön

Az eszköz nem tudja elindítani a központi telepítésben definiált modulokat. Csak az edgeAgent fut, de folyamatosan jelenti az "üres konfigurációs fájlt...".

**Gyökérok**

Alapértelmezés szerint az IoT Edge elindítja a modulokat a saját elkülönített tárolóhálózatukban. Lehet, hogy az eszköznek problémája van a DNS-névfeloldással ezen a magánhálózaton belül.

**Resolution** (Osztás)

**1. lehetőség: DNS-kiszolgáló beállítása a tárolómotor beállításaiban**

Adja meg a környezethez tartozó DNS-kiszolgálót a tárolómotor-beállításokban, amely a motor által indított összes tárolómodulra vonatkozik. Hozzon létre `daemon.json` egy nevű fájlt, amely megadja a használni kívánt DNS-kiszolgálót. Például:

```json
{
    "dns": ["1.1.1.1"]
}
```

A fenti példa a DNS-kiszolgálót nyilvánosan elérhető DNS-szolgáltatásra állítja be. Ha a peremhálózati eszköz nem tudja elérni ezt az IP-címet a környezetéből, cserélje le a dns-kiszolgáló elérhető címére.

Helyezze `daemon.json` el a megfelelő helyre a platform:

| Platform | Hely |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows-állomás Windows-tárolókkal | `C:\ProgramData\iotedge-moby\config` |

Ha a hely `daemon.json` már tartalmaz fájlt, adja hozzá a **DNS kulcsot,** és mentse a fájlt.

Indítsa újra a tárolómotort, hogy a frissítések érvénybe lépjenek.

| Platform | Parancs |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (Felügyeleti Powershell) | `Restart-Service iotedge-moby -Force` |

**2. lehetőség: DNS-kiszolgáló beállítása az IoT Edge telepítésében modulonként**

Az IoT Edge központi telepítésében beállíthatja az egyes modulok *createOptions* dns-kiszolgálóját. Például:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Ügyeljen arra, hogy ezt a konfigurációt az *edgeAgent* és *edgeHub* modulokat is állítsa be.

## <a name="next-steps"></a>További lépések

Úgy gondolja, hogy hibát talált az IoT Edge platformon? [Nyújtson be egy problémát,](https://github.com/Azure/iotedge/issues) hogy tovább fejlődhessünk.

Ha további kérdései vannak, hozzon létre támogatási támogatási [kérelmet.](https://portal.azure.com/#create/Microsoft.Support)
