---
title: Eszközök konfigurálása a hálózati proxyk – Azure IoT Edge |} A Microsoft Docs
description: Hogyan állíthatja be az Azure IoT Edge-futtatókörnyezet és bármely internetkapcsolattal rendelkező proxykiszolgálón keresztül kommunikáljon az IoT Edge-modulok.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 34a94a1b9c38070f5c9de22d9a9e4f24183a876c
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151003"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Egy proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása

IoT Edge-eszközökön az IoT hubbal való kommunikációhoz HTTPS-kéréseket küldeni. Ha az eszköz csatlakoztatva van a hálózathoz, amelyek proxykiszolgálót használ, a kiszolgálón keresztül kommunikálnak az IoT Edge-futtatókörnyezet konfigurálása szeretné. Proxykiszolgálók is befolyásolhatja az egyes IoT Edge-modulok ha azok HTTP vagy HTTPS-kéréseket, amely nincs átirányítva az IoT Edge hubon keresztül teszik. 

Ez a cikk végigvezeti a következő négy konfigurálását, és ezután kezelheti az IoT Edge-eszköz, egy proxykiszolgáló mögött található: 

1. **Telepítse az IoT Edge-futtatókörnyezet az eszközön.**

   Az IoT Edge telepítési parancsfájlok lekéréses csomagok és a fájlokat az internetről, így az eszköz keresztül kell kommunikálnia a proxykiszolgálót, hogy ezeket a kérelmeket. Részletes lépéseiért lásd: a [proxyn keresztül a futtatókörnyezet telepítéséhez](#install-the-runtime-through-a-proxy) című szakaszát. Windows-eszközök esetén a telepítési parancsfájlt is biztosít egy [kapcsolat nélküli telepítés](how-to-install-iot-edge-windows.md#offline-installation) lehetőséget. 

   Ebben a lépésben egy olyan egyszeri folyamat végrehajtani az IoT Edge-eszközön, ha először állítsa azt be. Ugyanezeket a kapcsolatokat is szükségesek, amikor frissíti az IoT Edge-futtatókörnyezet. 

2. **A Docker-démont és az IoT Edge-démon konfigurálja az eszközön.**

   IoT Edge két démonok az eszközön, mindkettő, hogy webes kéréseket a proxykiszolgálón keresztül kell használja. Az IoT Edge-démon feladata az IoT hubbal való kommunikációhoz. A Moby démon tárolókezelés felelős, tehát a tároló-beállításjegyzékek kommunikál. Részletes lépéseiért lásd: a [konfigurálása a démonok](#configure-the-daemons) című szakaszát. 

   Ebben a lépésben egy olyan egyszeri folyamat végrehajtani az IoT Edge-eszközön, ha először állítsa azt be.

3. **Az IoT Edge-ügynök tulajdonságainak konfigurálása a config.yaml fájlban az eszközön.**

   Az IoT Edge-démon a edgeAgent modul először elindul, de majd a edgeAgent modul felelős az manifest nasazení beolvasására az IoT hubról, és a többi modulnak indítása. A kezdeti kapcsolat létrehozása az IoT hub az IoT Edge-ügynök, a edgeAgent modul környezeti változók konfigurálása manuálisan magán az eszközön. A kezdeti kapcsolódást követően távolról konfigurálhatja a edgeAgent modul. Részletes lépéseiért lásd: a [az IoT Edge-ügynök konfigurálása](#configure-the-iot-edge-agent) című szakaszát.

   Ebben a lépésben egy olyan egyszeri folyamat végrehajtani az IoT Edge-eszközön, ha először állítsa azt be.

4. **Minden jövőbeli modul üzemelő példánya esetében állítsa be a környezeti változók bármilyen modul a proxyn keresztül kommunikál.**

   Miután az IoT Edge-eszköz beállításához és az IoT hubhoz a proxykiszolgálón keresztül csatlakozik, kell az összes jövőbeli modul telepítések esetén a kapcsolat fenntartása érdekében. Részletes lépéseiért lásd: a [konfigurálása a központi telepítési jegyzékek](#configure-deployment-manifests) című szakaszát. 

   Ebben a lépésben végrehajtott távolról, hogy minden új modul vagy a központi telepítés frissítése megőrzi az eszköz abban, hogy a proxykiszolgálón keresztül folyamatban. 

## <a name="know-your-proxy-url"></a>Tudja, a proxy URL-címe

A jelen cikkben ismertetett lépések bármelyike megkezdése előtt kell tudni, hogy a proxykiszolgáló URL-CÍMÉT.

Proxykiszolgáló URL-címeket is a következő formátumban: **protokoll**://**proxy_host**:**proxyport**.

* A **protokoll** HTTP vagy HTTPS van. A Docker-démon használhatja mindkét protokollt, a tároló-beállításjegyzékek beállításai, attól függően, de az IoT Edge-démont és futásidejű tárolók HTTP mindig használja a proxykiszolgálóhoz való csatlakozáshoz.

* A **proxy_host** esetében a proxykiszolgáló-cím. Ha a proxykiszolgáló hitelesítést igényel, megadhatja a hitelesítő adatait a proxyállomás részeként a következő formátumban: **felhasználói**:**jelszó**\@**proxy_host** .

* A **proxyport** a hálózati portot, amelyen a proxy válaszol hálózati forgalmat.

## <a name="install-the-runtime-through-a-proxy"></a>A modul egy proxyn keresztül történő telepítése

Az IoT Edge-eszköz Windows vagy Linux rendszeren fut, hogy a proxykiszolgálón keresztül telepítőcsomagok eléréséhez szükséges. Az operációs rendszertől függően egy proxykiszolgálón keresztül az IoT Edge-futtatókörnyezet telepítésének lépéseit kövesse. 

### <a name="linux"></a>Linux

Az IoT Edge-futtatókörnyezet telepítése egy Linux rendszerű eszközön, konfigurálja a package manager haladhat végig a proxykiszolgáló eléréséhez a telepítési csomagot. Ha például [apt-get paranccsal beállítása egy http-proxy használatára](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Miután konfigurálta a Csomagkezelő, kövesse a [(ARM32v7/armhf) Linux rendszeren telepítse az Azure IoT Edge-futtatókörnyezet](how-to-install-iot-edge-linux-arm.md) vagy [(x64) linuxon az Azure IoT Edge-futtatókörnyezet telepítéséhez](how-to-install-iot-edge-linux.md) a szokásos módon.

### <a name="windows"></a>Windows

Ha az IoT Edge-futtatókörnyezet telepít egy Windows-eszközön, akkor kétszer nyissa meg a proxykiszolgálón keresztül. Az első kapcsolat letölti a telepítési parancsfájlt, és a második kapcsolat letölteni a szükséges összetevők telepítése során. A Windows-beállítások konfigurálása a proxyadatokat, vagy is tartalmazza. a proxy közvetlenül a PowerShell-parancsokat. 

A következő lépések bemutatják, a windows telepítési használatának példája a `-proxy` argumentum:

1. Az Invoke-WebRequest parancs van szüksége a proxyadatok a telepítő szkripthez történő hozzáféréshez. Az üzembe helyezés – IoTEdge parancsot kell a telepítési fájlok letöltéséhez a proxyadatokat. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Nyissa meg a proxykiszolgálón keresztül, így a második lépésben csak szükséges webproxy-információkat az Invoke-WebRequest az Initialize-IoTEdge parancs nem szükséges.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge

If you have complicated credentials for the proxy server that can't be included in the URL, use the `-ProxyCredential` parameter within `-InvokeWebRequestParameters`. For example,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Proxy-paraméterekkel kapcsolatos további információkért lásd: [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). További információ a Windows-telepítési lehetőségekről, beleértve a kapcsolat nélküli telepítés: [telepítse az Azure IoT Edge-modul a Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>A démonok konfigurálása

IoT Edge az IoT Edge-eszközön futó két démonok támaszkodik. A Moby démon webes kéréseket hajt végre lekéréses tárolórendszerképeket a tároló-beállításjegyzékek. Az IoT Edge-démon lehetővé teszi, hogy webes kéréseket az IoT hubbal való kommunikációhoz.

Folyamatban lévő eszköz funkciójának a proxykiszolgáló használatára kell konfigurálni kell a Moby és az IoT Edge démonok is. Ebben a lépésben történik az IoT Edge-eszköz kezdeti Eszközbeállítás során. 

### <a name="moby-daemon"></a>Moby démon

Mivel Moby Docker épül, tekintse meg a Docker – dokumentáció a Moby démon konfigurálása a környezeti változók. A legtöbb tároló-beállításjegyzékek (beleértve a DockerHub és az Azure Container Registry) támogatja a HTTPS-kéréseket, így a paraméter, amely kell beállítania **HTTPS_PROXY**. Ha Ön stahují se Image. a beállításjegyzékből, amely nem támogatja a transport layer security (TLS), majd állítsa be a **HTTP_PROXY** paraméter. 

Válassza ki a cikket, amely az IoT Edge-eszköz operációs rendszerének vonatkozik: 

* [Docker-démon konfigurálása linuxon](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * A Linux rendszerű eszközök Moby démon Docker neve követi.
* [Windows Docker-démon konfigurálása](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * A Windows-eszközök Moby démon iotedge-moby nevezzük. A nevek eltérőek, mert lehetséges, Docker asztal és a Moby párhuzamosan futtatni egy Windows-eszközön. 

### <a name="iot-edge-daemon"></a>IoT Edge-démon

Az IoT Edge-démon a Moby démon hasonló módon van konfigurálva. Az alábbi lépések segítségével egy környezeti változót a szolgáltatáshoz, az operációs rendszer alapján. 

Az IoT Edge-démon kérelmek küldésére az IoT hub mindig HTTPS PROTOKOLLT használ.

#### <a name="linux"></a>Linux

Nyissa meg egy szövegszerkesztőben konfigurálása az IoT Edge-démon a terminálon. 

```bash
sudo systemctl edit iotedge
```

A következő szöveget, és cserélje le  **\<proxy URL-címe >** a proxykiszolgáló címe és portja. Ezt követően mentse, és kilép. 

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

A service manager az új konfigurációt az IoT Edge-hez való frissítése.

```bash
sudo systemctl daemon-reload
```

IoT Edge a módosítások érvénybe léptetéséhez indítsa újra.

```bash
sudo systemctl restart iotedge
```

Győződjön meg arról, hogy a környezeti változó lett létrehozva, és az új konfiguráció be lett töltve. 

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Nyissa meg rendszergazdaként egy PowerShell-ablakot, és futtassa a következő parancsot a beállításjegyzék szerkesztése a az új környezeti változót. Cserélje le  **\<proxy URL-címe >** a proxykiszolgáló címe és portja. 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

IoT Edge a módosítások érvénybe léptetéséhez indítsa újra.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Az IoT Edge-ügynök konfigurálása

Az IoT Edge-ügynök minden IoT Edge-eszközön indítsa el az első modult. Az IoT Edge config.yaml fájl található információk alapján első alkalommal elindul. Üzembe helyezési jegyzékek, amely deklarálja, hogy milyen más modulok kell lekérni az IoT Hub ezután csatlakozik az IoT Edge-ügynök telepítve az eszközön.

Ebben a lépésben történik az IoT Edge-eszköz egyszer a kezdeti Eszközbeállítás során. 

1. Nyissa meg az IoT Edge-eszköz config.yaml fájlt. Linux rendszerek esetében ez a fájl nem található: **/etc/iotedge/config.yaml**. A Windows rendszerek esetében ez a fájl következő helyen található **C:\ProgramData\iotedge\config.yaml**. A konfigurációs fájl védett, így hozzáférési rendszergazdai jogosultságok szükségesek. A Linux rendszerek esetében használja a `sudo` parancs előtt meg megnyitni a fájlt az előnyben részesített szövegszerkesztőben. A Windows nyisson meg egy szövegszerkesztőt, például a Jegyzettömb alkalmazást rendszergazdaként, és nyissa meg a fájlt. 

2. Config.yaml a fájlban keresse meg a **az Edge Agent modul specifikációja** szakaszban. Az IoT Edge-ügynök definíció egy **env** paraméter, ahol hozzáadhat környezeti változókat. 

3. Távolítsa el a kapcsos zárójeleket helyőrzőket az env paraméterhez, és az új változó hozzáadása egy új sort. Ne feledje, hogy a YAML francia éppen két szóköz. 

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. Az IoT Edge-futtatókörnyezet alapértelmezés szerint az AMQP használatával kommunikáljon az IoT Hub. Proxykiszolgálók, amelyeken az AMQP-portok blokkolására. Ha ez a helyzet, majd is szüksége edgeAgent AMQP használatával websocketen megadását. Adjon hozzá egy második környezeti változót.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![a környezeti változók edgeAgent definíciója](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Mentse a módosításait config.yaml, és zárja be a szerkesztőt. IoT Edge a módosítások érvénybe léptetéséhez indítsa újra. 

   * Linux: 

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Üzembe helyezés jegyzékek konfigurálása  

Ha a proxykiszolgáló használata az IoT Edge-eszköz van konfigurálva, a jövőben üzembe helyezési jegyzékfájlok környezeti változókat deklarálni továbbra is szeretné. Szerkesztheti a telepítési jegyzékek vagy az Azure portal varázsló segítségével, vagy egy központi telepítési szerkesztésével manifest JSON-fájlt. 

Mindig úgy állítsa be a két futásidejű modulok, edgeAgent és edgeHub való kommunikációhoz a proxykiszolgálón keresztül, így ezek folyamatosan az IoT Hub-kapcsolattal. Ha eltávolítja a proxyadatokat edgeAgent modulból, helyreállítani a kapcsolatot az egyetlen módszer az eszközön a config.yaml fájl szerkesztésével van, az előző szakaszban leírtak szerint. 

Egyéb IoT Edge-modulok, amely csatlakozik az internethez való kommunikációhoz túl a proxykiszolgálón keresztül kell konfigurálni. Azonban a modulokat, amely az üzeneteket a edgeHub keresztül irányíthatja, illetve, hogy csak kommunikálhat más modulok az eszközön nem szükséges a proxykiszolgáló adatai. 

Ez a lépés nem folyamatos, az IoT Edge-eszköz élettartama során. 

### <a name="azure-portal"></a>Azure Portal

Használatakor a **modulok beállítása** eszközök, IoT Edge üzemelő példány létrehozása varázsló minden modul rendelkezik egy **környezeti változók** szakaszt, amely a proxy server kapcsolatok konfigurálásához használhatja. 

Az IoT Edge-ügynök és hub IoT Edge-modulok konfigurálásához válasszon **speciális Edge-futtatókörnyezet-beállítások konfigurálása** a varázsló első lépése. 

![Edge-futtatókörnyezet speciális beállításainak megadása](./media/how-to-configure-proxy-support/configure-runtime.png)

Adja hozzá a **https_proxy** környezeti változót, az IoT Edge-ügynök és az IoT Edge hubot modulmeghatározásokat. Ha tartalmazza a **UpstreamProtocol** környezeti változót az IoT Edge-eszközön a config.yaml fájlt ad hozzá, amely az IoT Edge-ügynök modul definíció túl. 

![Https_proxy környezeti változó beállítása](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Minden más modulok tesznek, egy manifest nasazení hozzáadni kívánt ugyanezt a mintát követi. Az itt lehet megadni a modul nevét és a lemezkép lapon nincs egy környezeti változók szakaszban.

### <a name="json-deployment-manifest-files"></a>Üzembe helyezési manifest JSON-fájlok

Eszközök a sablonok használatával a Visual Studio Code-ban, vagy manuálisan hozzon létre JSON-fájlokat hoz létre központi telepítések az IoT Edge-hez, ha közvetlenül az egyes modul definíciója a környezeti változókat is hozzáadhat. 

Használja a következő JSON formátummal: 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

A a környezeti változókat tartalmaz a modul definition edgeHub az alábbihoz hasonlóan kell kinéznie:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Ha tartalmazza a **UpstreamProtocol** környezeti változót az IoT Edge-eszközön a confige.yaml fájlt ad hozzá, amely az IoT Edge-ügynök modul definíció túl. 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>További lépések

További információ a szerepköröket a [IoT Edge-futtatókörnyezet](iot-edge-runtime.md).

A telepítési és konfigurációs hibák elhárítása [gyakori problémák és megoldásuk az Azure IoT Edge-hez](troubleshoot.md)

