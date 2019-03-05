---
title: Eszközök konfigurálása a hálózati proxyk – Azure IoT Edge |} A Microsoft Docs
description: Hogyan állíthatja be az Azure IoT Edge-futtatókörnyezet és bármely internetkapcsolattal rendelkező proxykiszolgálón keresztül kommunikáljon az IoT Edge-modulok.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: d1ad1f34f51adbc177e5b4163d528dbe45ce03af
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339147"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Egy proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása

IoT Edge-eszközökön az IoT hubbal való kommunikációhoz HTTPS-kéréseket küldeni. Ha az eszköz csatlakoztatva van a hálózathoz, amelyek proxykiszolgálót használ, a kiszolgálón keresztül kommunikálnak az IoT Edge-futtatókörnyezet konfigurálása szeretné. Proxykiszolgálók is befolyásolhatja az egyes IoT Edge-modulok ha azok HTTP vagy HTTPS-kéréseket, amely nincs átirányítva az IoT Edge hubon keresztül teszik. 

Proxykiszolgáló használata az IoT Edge-eszköz konfigurálása a következő alapvető lépéseket: 

1. Telepítse az IoT Edge-futtatókörnyezet az eszközön. 
2. A Docker-démont és az IoT Edge-démon konfigurálja a proxykiszolgáló használatára az eszközön.
3. Az eszközön a config.yaml fájlban edgeAgent tulajdonságainak konfigurálása.
4. Környezeti változókat az IoT Edge-futtatókörnyezet és az egyéb IoT Edge modulok beállítása manifest nasazení. 

## <a name="know-your-proxy-url"></a>Tudja, a proxy URL-címe

A Docker-démont és az IoT Edge konfigurálása az eszközön, akkor ismernie kell a proxykiszolgáló URL-CÍMÉT. 

Proxykiszolgáló URL-címeket is a következő formátumban: **protokoll**://**proxy_host**:**proxyport**. 

* A **protokoll** HTTP vagy HTTPS van. A Docker-démon használhatja mindkét protokollt, a tároló-beállításjegyzékek beállításai, attól függően, de az IoT Edge-démont és futásidejű tárolók mindig HTTPS PROTOKOLLT használnak.

* A **proxy_host** esetében a proxykiszolgáló-cím. Ha a proxykiszolgáló hitelesítést igényel, a hitelesítő adatokat biztosíthat a proxy_host formátumban részeként **felhasználói**:**jelszó**@**proxy_host**. 

* A **proxyport** a hálózati portot, amelyen a proxy válaszol hálózati forgalmat. 

## <a name="install-the-runtime"></a>A modul telepítése

Az IoT Edge-futtatókörnyezet telepítése egy Linux rendszerű eszközön, konfigurálja a package manager haladhat végig a proxykiszolgáló eléréséhez a telepítési csomagot. Ha például [apt-get paranccsal beállítása egy http-proxy használatára](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Miután konfigurálta a Csomagkezelő, kövesse a [(ARM32v7/armhf) Linux rendszeren telepítse az Azure IoT Edge-futtatókörnyezet](how-to-install-iot-edge-linux-arm.md) vagy [(x64) linuxon az Azure IoT Edge-futtatókörnyezet telepítéséhez](how-to-install-iot-edge-linux.md) a szokásos módon. 

Ha az IoT Edge-futtatókörnyezet telepít egy Windows-eszközön, akkor nyissa meg a proxykiszolgálón keresztül után töltse le a telepítési parancsfájlt, majd újból letölteni a szükséges összetevők telepítése során. A Windows-beállítások konfigurálása a proxyadatokat, vagy a proxy adatait tartalmazzák a telepítési parancsfájlt közvetlenül a. A következő powershell-parancsfájl példaként szolgál a windows telepítési használatával a `-proxy` argumentum:

```powershell
. {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -proxy <proxy URL>
```

Ha Ön rendelkezik bonyolult, amely nem szerepelhet az URL-címet a proxykiszolgáló hitelesítő adatait, használja a `-ProxyCredential` paraméter `-InvokeWebRequestParameters`. Például:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Proxy-paraméterekkel kapcsolatos további információkért lásd: [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Telepítési lehetőségekkel kapcsolatos további információkért lásd: [telepítse az Azure IoT Edge-modul a Windows](how-to-install-iot-edge-windows.md).

Miután telepítette az IoT Edge-futtatókörnyezet, az alábbi szakasz segítségével konfigurálja azt a proxy adatait. 

## <a name="configure-the-daemons"></a>A démonok konfigurálása

A Docker és az IoT Edge démonok, az IoT Edge-eszközön fut, hogy a proxykiszolgáló használatára kell konfigurálni kell. A Docker-démon webes kéréseket hajt végre lekéréses tárolórendszerképeket a tároló-beállításjegyzékek. Az IoT Edge-démon lehetővé teszi, hogy webes kéréseket az IoT hubbal való kommunikációhoz.

### <a name="docker-daemon"></a>Docker-démon

Tekintse meg a Docker dokumentációt a Docker-démon konfigurálása a környezeti változókat. A legtöbb tároló-beállításjegyzékek (beleértve a DockerHub és az Azure Container Registry) támogatja a HTTPS-kéréseket, így a paraméter, amely kell beállítania **HTTPS_PROXY**. Ha Ön stahují se Image. a beállításjegyzékből, amely nem támogatja a transport layer security (TLS), majd állítsa be a **HTTP_PROXY** paraméter. 

Válassza ki a cikket, amely a Docker verziójára: 

* [Linuxhoz készült docker](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [A Windows docker](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)

### <a name="iot-edge-daemon"></a>IoT Edge-démon

Az IoT Edge-démon a Docker-démont, hasonló módon van konfigurálva. Az IoT Edge által az IoT hubnak küldött kéréseket a HTTPS használatára. Az alábbi lépések segítségével egy környezeti változót a szolgáltatáshoz, az operációs rendszer alapján. 

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

Nyissa meg az IoT Edge-eszköz config.yaml fájlt. Linux rendszerek esetében ez a fájl nem található: **/etc/iotedge/config.yaml**. A Windows rendszerek esetében ez a fájl következő helyen található **C:\ProgramData\iotedge\config.yaml**. A konfigurációs fájl védett, így hozzáférési rendszergazdai jogosultságok szükségesek. A Linux rendszerek esetében, amely azt jelenti, hogy használatával a `sudo` parancs előtt meg megnyitni a fájlt az előnyben részesített szövegszerkesztőben. Windows, az azt jelenti, hogy nyissa meg egy szövegszerkesztőben, például a Jegyzettömbben Futtatás rendszergazdaként, és majd a fájl megnyitásakor. 

Config.yaml a fájlban keresse meg a **az Edge Agent modul specifikációja** szakaszban. Az IoT Edge-ügynök definíció egy **env** paraméter, ahol hozzáadhat környezeti változókat. 

<!--
![edgeAgent definition](./media/how-to-configure-proxy-support/edgeagent-unedited.png)
-->

Távolítsa el a kapcsos zárójeleket helyőrzőket az env paraméterhez, és az új változó hozzáadása egy új sort. Ne feledje, hogy a YAML francia éppen két szóköz. 

```yaml
https_proxy: "<proxy URL>"
```

Az IoT Edge-futtatókörnyezet alapértelmezés szerint az AMQP használatával kommunikáljon az IoT Hub. Proxykiszolgálók, amelyeken az AMQP-portok blokkolására. Ha ez a helyzet, majd is szüksége edgeAgent AMQP használatával websocketen megadását. Adjon hozzá egy második környezeti változót.

```yaml
UpstreamProtocol: "AmqpWs"
```

![a környezeti változók edgeAgent definíciója](./media/how-to-configure-proxy-support/edgeagent-edited.png)

Mentse a módosításait config.yaml, és zárja be a szerkesztőt. IoT Edge a módosítások érvénybe léptetéséhez indítsa újra. 

* Linux: 

   ```bash
   sudo systemctl restart iotedge
   ```

* Windows:

   ```powershell
   Restart-Service iotedge
   ```

## <a name="configure-deployment-manifests"></a>Üzembe helyezés jegyzékek konfigurálása  

Ha a proxykiszolgáló használata az IoT Edge-eszköz van konfigurálva, a jövőben üzembe helyezési jegyzékfájlok környezeti változókat deklarálni továbbra is szeretné. Mindig úgy állítsa be a két futásidejű modulok, edgeAgent és edgeHub való kommunikációhoz a proxykiszolgálón keresztül, így ezek folyamatosan az IoT Hub-kapcsolattal. Az internethez csatlakozó egyéb IoT Edge-modulok esetében a proxykiszolgáló kell konfigurálni. Azonban a modulokat, amely az üzeneteket a edgeHub keresztül irányíthatja, illetve, hogy csak kommunikálhat más modulok az eszközön nem szükséges a proxykiszolgáló adatai. 

Az Azure portal használatával, vagy manuálisan a JSON-fájl szerkesztése telepítési jegyzékek hozhat létre. 

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
            "value": "https://proxy.example.com:3128"
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

