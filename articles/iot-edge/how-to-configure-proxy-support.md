---
title: Eszközök konfigurálása a hálózati proxyk – Azure IoT Edge |} A Microsoft Docs
description: Hogyan állíthatja be az Azure IoT Edge-futtatókörnyezet és bármely internetkapcsolattal rendelkező proxykiszolgálón keresztül kommunikáljon az IoT Edge-modulok.
author: kgremban
ms.author: kgremban
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9a4ed17ceddf01ec628d80dc3ba9f4d7ee305f3b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457150"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Egy proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása

IoT Edge-eszközökön az IoT hubbal való kommunikációhoz HTTPS-kéréseket küldeni. Ha az eszköz csatlakoztatva van a hálózathoz, amelyek proxykiszolgálót használ, a kiszolgálón keresztül kommunikálnak az IoT Edge-futtatókörnyezet konfigurálása szeretné. A proxykiszolgálók az egyes IoT Edge modulokat is befolyásolhatják, ha olyan HTTP-vagy HTTPS-kérelmeket tesznek elérhetővé, amelyek nem az IoT Edge hub-on keresztül vannak átirányítva 

Ez a cikk bemutatja a következő négy lépést a proxykiszolgáló mögötti IoT Edge eszköz konfigurálásához és kezeléséhez: 

1. **Telepítse a IoT Edge futtatókörnyezetet az eszközön.**

   A IoT Edge telepítési parancsfájlok lekérik a csomagokat és fájlokat az internetről, így az eszköznek a proxykiszolgálón keresztül kell kommunikálnia a kérések elvégzéséhez. Részletes útmutatást a jelen cikk a [futtatókörnyezet telepítése proxyn keresztül](#install-the-runtime-through-a-proxy) című szakaszában talál. Windows-eszközök esetén a telepítési parancsfájl [Offline telepítési](how-to-install-iot-edge-windows.md#offline-installation) lehetőséget is biztosít. 

   Ez a lépés egy egyszeri folyamat, amelyet a IoT Edge eszközön végeznek el az első beállításakor. A IoT Edge futtatókörnyezet frissítésekor ugyanezek a kapcsolatok is szükségesek. 

2. **Konfigurálja a Docker-démont és a IoT Edge démont az eszközön.**

   IoT Edge két démont használ az eszközön, és mindkettőnek a proxykiszolgálón keresztül kell webes kéréseket tennie. A IoT Edge démon felelős a IoT Hubekkel folytatott kommunikációért. A Moby Daemon feladata a tárolók kezelése, így kommunikál a tároló-beállításjegyzékkel. A részletes lépésekért tekintse meg a jelen cikk [démonok konfigurálása](#configure-the-daemons) című szakaszát. 

   Ez a lépés egy egyszeri folyamat, amelyet a IoT Edge eszközön végeznek el az első beállításakor.

3. **Konfigurálja a IoT Edge ügynök tulajdonságait a config. YAML fájlban az eszközön.**

   A IoT Edge démon először elindítja a edgeAgent modult, de a edgeAgent modul felelős az üzembe helyezési jegyzék IoT Hubból való lekéréséhez és az összes többi modul elindításához. Ahhoz, hogy a IoT Edge-ügynök a kezdeti kapcsolattal IoT Hub, konfigurálja a edgeAgent modul környezeti változóit manuálisan az eszközön. A kezdeti kapcsolat után távolról is konfigurálhatja a edgeAgent modult. A részletes lépésekért tekintse meg a jelen cikk [IoT Edge ügynök konfigurálása](#configure-the-iot-edge-agent) című szakaszát.

   Ez a lépés egy egyszeri folyamat, amelyet a IoT Edge eszközön végeznek el az első beállításakor.

4. **A modul minden későbbi központi telepítésére vonatkozóan állítsa be a környezeti változókat a proxyn keresztül kommunikáló modulok esetében.**

   Miután beállította IoT Edge eszközét, és csatlakoztatva van IoT Hub a proxykiszolgálón keresztül, meg kell őriznie a kapcsolatot az összes jövőbeli modul-telepítésben. A részletes lépésekért tekintse meg a jelen cikk [központi telepítési jegyzékek konfigurálása](#configure-deployment-manifests) című szakaszát. 

   Ez a lépés egy folyamatban lévő folyamat, amely lehetővé teszi, hogy minden új modul vagy központi telepítés frissítése fenntartsa az eszköznek a proxykiszolgálón keresztüli kommunikációt. 

## <a name="know-your-proxy-url"></a>Tudja, a proxy URL-címe

Mielőtt elkezdené a cikkben ismertetett lépéseket, ismernie kell a proxy URL-címét.

A proxy URL-címei a következő formátumot végzik el: **protokoll**://**proxy_host**:**proxy_port**.

* A **protokoll** http vagy HTTPS. A Docker-démon bármely protokollt használhat a tároló beállításjegyzék-beállításaitól függően, de a IoT Edge démon és a futásidejű tárolók mindig HTTP-vel csatlakozhatnak a proxyhoz.

* A **proxy_host** a proxykiszolgáló címe. Ha a proxykiszolgáló hitelesítést igényel, a proxykiszolgáló részeként megadhatja a hitelesítő adatait a következő formátumban: **felhasználó**:**Password**\@**proxy_host**.

* A **proxy_port** az a hálózati port, amelyen a proxy válaszol a hálózati forgalomra.

## <a name="install-the-runtime-through-a-proxy"></a>A futtatókörnyezet telepítése proxyn keresztül

Azt jelzi, hogy a IoT Edge-eszköz Windows vagy Linux rendszeren fut-e, a proxykiszolgálón keresztül kell hozzáférnie a telepítési csomagokhoz. Az operációs rendszertől függően hajtsa végre az IoT Edge futtatókörnyezetnek a proxykiszolgálón keresztüli telepítéséhez szükséges lépéseket. 

### <a name="linux"></a>Linux

Az IoT Edge-futtatókörnyezet telepítése egy Linux rendszerű eszközön, konfigurálja a package manager haladhat végig a proxykiszolgáló eléréséhez a telepítési csomagot. Tegyük fel például, [hogy az apt-get http-proxy használatára van beállítva](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Ha a csomagkezelő konfigurálva van, kövesse az [Azure IoT Edge futtatókörnyezet telepítése Linux rendszeren](how-to-install-iot-edge-linux.md) a szokásos módon című témakör utasításait.

### <a name="windows"></a>Windows

Ha Windows rendszerű eszközön telepíti a IoT Edge futtatókörnyezetet, kétszer kell megadnia a proxykiszolgálót. Az első csatlakozás letölti a telepítő parancsfájlt, a második pedig a telepítés során a szükséges összetevők letöltéséhez. A proxybeállításokat a Windows beállításaiban konfigurálhatja, vagy közvetlenül a PowerShell-parancsokban is megadhatja a proxy adatait. 

Az alábbi lépések a `-proxy` argumentumot használó Windows-telepítés példáját szemléltetik:

1. A meghívó-webkérési parancsnak proxy-információra van szüksége a telepítési parancsfájl eléréséhez. Ezután az Deploy-IoTEdge parancsnak szüksége van a proxy adataira a telepítési fájlok letöltéséhez. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Az inicializálás-IoTEdge parancsnak nem kell átesnie a proxykiszolgálóhoz, így a második lépés csak a Request-webkéréshez szükséges proxy-információkat igényli.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Ha az URL-cím nem tartalmazhatja a proxykiszolgáló összetett hitelesítő adatait, használja a `-ProxyCredential` paramétert `-InvokeWebRequestParameters`on belül. Például:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

A proxy paraméterekkel kapcsolatos további információkért lásd: [meghívó-webkérelem](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). További információ a Windows telepítési lehetőségeiről, beleértve az offline telepítést is: [Azure IoT Edge futtatókörnyezet telepítése Windows](how-to-install-iot-edge-windows.md)rendszeren.

## <a name="configure-the-daemons"></a>A démonok konfigurálása

A IoT Edge a IoT Edge eszközön futó két démonra támaszkodik. A Moby Daemon lehetővé teszi a webes kérések számára a tárolói lemezképek lekérését a tároló-beállításjegyzékből. Az IoT Edge-démon lehetővé teszi, hogy webes kéréseket az IoT hubbal való kommunikációhoz.

A Moby és a IoT Edge démonokat is konfigurálni kell, hogy a proxykiszolgálót használják a folyamatos eszköz működéséhez. Ez a lépés a IoT Edge eszközön történik a kezdeti eszköz beállítása során. 

### <a name="moby-daemon"></a>Moby Daemon

Mivel a Moby a Docker-re épül, tekintse meg a Docker dokumentációját, és konfigurálja a Moby Daemon-t környezeti változók használatával. A legtöbb tároló-nyilvántartás (beleértve a DockerHub és az Azure Container nyilvántartót is) támogatja a HTTPS-kérelmeket, így a beállított paraméter **HTTPS_PROXY**. Ha olyan beállításjegyzékből húz le képeket, amely nem támogatja a TLS-t, akkor a **HTTP_PROXY** paramétert kell beállítania. 

Válassza ki a IoT Edge eszköz operációs rendszerére vonatkozó cikket: 

* [Docker-démon konfigurálása Linuxon](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * A Linux-eszközökön a Moby Daemon megőrzi a Docker nevet.
* [A Docker-démon konfigurálása Windows rendszeren](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * A Windows-eszközökön a Moby Daemon neve iotedge-Moby. A nevek eltérőek, mert lehetséges a Docker Desktop és a Moby párhuzamos futtatása Windows-eszközön. 

### <a name="iot-edge-daemon"></a>IoT Edge-démon

A IoT Edge démon a Moby démonhoz hasonló módon van konfigurálva. Az alábbi lépések segítségével egy környezeti változót a szolgáltatáshoz, az operációs rendszer alapján. 

A IoT Edge démon mindig HTTPS-t használ, hogy kéréseket küldjön a IoT Hubnak.

#### <a name="linux"></a>Linux

Nyissa meg egy szövegszerkesztőben konfigurálása az IoT Edge-démon a terminálon. 

```bash
sudo systemctl edit iotedge
```

Adja meg a következő szöveget, és cserélje le **\<proxy URL >-** címét a proxykiszolgáló címére és portjára. Ezt követően mentse, és kilép. 

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Frissítse a Service Managert az IoT Edge új konfigurációjának kiválasztásához.

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

Nyissa meg rendszergazdaként egy PowerShell-ablakot, és futtassa a következő parancsot a beállításjegyzék szerkesztése a az új környezeti változót. Cserélje le **\<proxy URL->** a proxykiszolgáló címére és portjára. 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

IoT Edge a módosítások érvénybe léptetéséhez indítsa újra.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>A IoT Edge-ügynök konfigurálása

A IoT Edge ügynök az első modul, amely bármely IoT Edge eszközön elindul. Az IoT Edge config.yaml fájl található információk alapján első alkalommal elindul. A IoT Edge ügynök ezután csatlakozik a IoT Hubhoz az üzembe helyezési jegyzékek lekéréséhez, amely azt deklarálja, hogy az eszközön milyen más modulok legyenek telepítve.

Ez a lépés a IoT Edge eszközön a kezdeti eszköz beállításakor kerül megrendezésre. 

1. Nyissa meg az IoT Edge-eszköz config.yaml fájlt. Linux rendszereken a fájl a következő helyen található: **/etc/iotedge/config.YAML**. Windows rendszereken ez a fájl a következő helyen található: **C:\ProgramData\iotedge\config.YAML**. A konfigurációs fájl védett, így hozzáférési rendszergazdai jogosultságok szükségesek. Linux rendszereken használja a `sudo` parancsot, mielőtt megnyitja a fájlt a kívánt szövegszerkesztőben. Windows rendszeren nyisson meg egy szövegszerkesztőt, például a Jegyzettömböt rendszergazdaként, majd nyissa meg a fájlt. 

2. A config. YAML fájlban keresse meg az **Edge Agent moduljának spec** szakaszát. A IoT Edge ügynök definíciója tartalmaz egy **env** paramétert, ahol környezeti változókat adhat hozzá. 

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

Miután a IoT Edge eszköz konfigurálva van a proxykiszolgálóhoz való együttműködésre, továbbra is deklarálnia kell a környezeti változókat a jövőbeli üzembe helyezési jegyzékekben. Az üzembe helyezési jegyzékeket szerkesztheti a Azure Portal varázslóval vagy a telepítési jegyzékfájl JSON-fájljának szerkesztésével is. 

Mindig konfigurálja a két futásidejű modult, a edgeAgent-t és a edgeHub-t úgy, hogy az a proxykiszolgálón keresztül kommunikáljon, hogy a kapcsolatot a IoT Hubával is karban lehessen tartani. Ha eltávolítja a edgeAgent modulból a proxy adatait, a kapcsolat újbóli létrehozásához szerkessze a config. YAML fájlt az eszközön az előző szakaszban leírtak szerint. 

Az internethez csatlakozó más IoT Edge-modulokat is konfigurálni kell a proxykiszolgálón keresztüli kommunikációhoz. Azok a modulok azonban, amelyek az üzeneteket az edgeHub-on keresztül irányítják, vagy csak az eszköz más moduljaival kommunikálnak, nincs szükségük a proxykiszolgáló részleteire. 

Ez a lépés a IoT Edge eszköz teljes élettartama alatt zajlik. 

### <a name="azure-portal"></a>Azure Portal

Ha a **modulok beállítása** varázslóval hoz létre központi telepítéseket IoT Edge eszközökhöz, minden modulhoz tartozik egy **környezeti változók** szakasz, amely a proxykiszolgáló-kapcsolatok konfigurálására használható. 

A IoT Edge-ügynök és a IoT Edge hub-modulok konfigurálásához válassza a **speciális Edge-futtatókörnyezet beállításainak konfigurálása** elemet a varázsló első lépésében. 

![Edge-futtatókörnyezet speciális beállításainak megadása](./media/how-to-configure-proxy-support/configure-runtime.png)

Adja hozzá a **https_proxy** környezeti változót mind a IoT Edge Agent, mind a IoT Edge hub modul-definícióhoz. Ha a **UpstreamProtocol** környezeti változót a IoT Edge eszköz config. YAML fájljában is felveszi, adja hozzá az IoT Edge Agent modul definícióját is. 

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

Ha a **UpstreamProtocol** környezeti változót a IoT Edge eszköz config. YAML fájljában is felveszi, adja hozzá az IoT Edge Agent modul definícióját is. 

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

További információ a [IoT Edge futtatókörnyezet](iot-edge-runtime.md)szerepköreiről.

Telepítési és konfigurációs hibák elhárítása [gyakori problémákkal és](troubleshoot.md) megoldásokkal a Azure IoT Edge

