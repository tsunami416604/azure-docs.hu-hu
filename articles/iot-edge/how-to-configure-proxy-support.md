---
title: Eszközök konfigurálása hálózati proxykhoz - Azure IoT Edge | Microsoft dokumentumok
description: Konfigurálja az Azure IoT Edge futásidejű és az internetre néző IoT Edge-modulok at egy proxykiszolgálón keresztül.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 270e6a0173ed0088ff5d37c989947f5272634200
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687200"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>IoT Edge-eszköz konfigurálása proxykiszolgálón keresztüli kommunikációra

Az IoT Edge-eszközök HTTPS-kérelmeket küldenek az IoT Hubbal való kommunikációhoz. Ha az eszköz proxykiszolgálót használó hálózathoz csatlakozik, konfigurálnia kell az IoT Edge futásidejét a kiszolgálón keresztüli kommunikációhoz. A proxykiszolgálók hatással lehetnek az egyes IoT Edge-modulokra is, ha olyan HTTP- vagy HTTPS-kérelmeket hoznak, amelyek nem az IoT Edge-központon keresztül továbbítódnak.

Ez a cikk bemutatja az alábbi négy lépést a proxykiszolgáló mögötti IoT Edge-eszközök konfigurálásához és kezeléséhez:

1. **Telepítse az IoT Edge futásidejű eszközére.**

   Az IoT Edge telepítési parancsfájlok lekéri a csomagokat és fájlokat az internetről, így az eszköznek kommunikálnia kell a proxykiszolgálón keresztül, hogy ezeket a kéréseket. A részletes lépéseket a [Runtime telepítése a](#install-the-runtime-through-a-proxy) cikk proxyn keresztül című szakaszában olvashatja. Windows-eszközök esetén a telepítési parancsfájl [offline telepítési](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation) lehetőséget is biztosít.

   Ez a lépés egy egyszeri folyamat az IoT Edge-eszközön, amikor először beállította. Az IoT Edge-futásidejű frissítésekor is ugyanazokra a kapcsolatokra van szükség.

2. **Konfigurálja a Docker démont és az IoT Edge démont az eszközön.**

   Az IoT Edge két démont használ az eszközön, amelyek mindegyike webes kéréseket kell leadnia a proxykiszolgálón keresztül. Az IoT Edge-démon felelős az IoT Hubbal folytatott kommunikációért. A Moby démon felelős a tárolók kezeléséért, így kommunikál a tároló-nyilvántartások. A részletes lépéseket a cikk [démonok konfigurálása](#configure-the-daemons) című szakaszában olvashat.

   Ez a lépés egy egyszeri folyamat az IoT Edge-eszközön, amikor először beállította.

3. **Konfigurálja az IoT Edge-ügynök tulajdonságait az eszközön lévő config.yaml fájlban.**

   Az IoT Edge démon kezdetben elindítja az edgeAgent modult, de majd az edgeAgent modul felelős a központi telepítési jegyzékfájl lekérésért az IoT Hubból, és az összes többi modul indítása. Az IoT Edge-ügynök, hogy a kezdeti kapcsolat az IoT Hub, konfigurálja az edgeAgent modul környezeti változók manuálisan az eszközön is. A kezdeti kapcsolat után az edgeAgent modult távolról konfigurálhatja. Részletes lépéseket a cikk [IoT Edge-ügynökének konfigurálása](#configure-the-iot-edge-agent) című szakaszában olvashat.

   Ez a lépés egy egyszeri folyamat az IoT Edge-eszközön, amikor először beállította.

4. **Az összes jövőbeli modul-telepítéshez állítsa be a környezeti változókat a proxyn keresztül kommunikáló bármely modulhoz.**

   Miután az IoT Edge-eszköz be van állítva, és csatlakozik az IoT Hub a proxykiszolgálón keresztül, meg kell fenntartani a kapcsolatot az összes jövőbeli modul központi telepítések. A részletes lépéseket a jelen cikk [telepítési jegyzékfájljainak konfigurálása](#configure-deployment-manifests) című szakaszában talál.

   Ez a lépés egy folyamatban lévő folyamat távolról, hogy minden új modul vagy központi telepítési frissítés fenntartja az eszköz képes kommunikálni a proxykiszolgálón keresztül.

## <a name="know-your-proxy-url"></a>Ismerje meg a proxy URL-címét

Mielőtt elkezdené a cikk ben leírt lépéseket, ismernie kell a proxy URL-címét.

A proxy URL-címek a következő formátumot használják: **protocol**://**proxy_host**:**proxy_port**.

* A **protokoll** HTTP vagy HTTPS. A Docker démon bármelyik protokollt használhatja, a tároló beállításjegyzék-beállításaitól függően, de az IoT Edge démon- és futásidejű tárolóknak mindig HTTP-t kell használniuk a proxyhoz való csatlakozáshoz.

* A **proxy_host** a proxykiszolgáló címe. Ha a proxykiszolgáló hitelesítést igényel, a hitelesítő adatokat a proxyállomás részeként a következő formátumban adja meg: **user**:**password**\@**proxy_host**.

* A **proxy_port** az a hálózati port, amelyen a proxy reagál a hálózati forgalomra.

## <a name="install-the-runtime-through-a-proxy"></a>A futásidő telepítése proxyn keresztül

Függetlenül attól, hogy az IoT Edge-eszköz Windows vagy Linux rendszeren fut, a telepítési csomagokat a proxykiszolgálón keresztül kell elérnie. Az operációs rendszertől függően kövesse az IoT Edge futásidejű proxykiszolgálón keresztüli telepítésének lépéseit.

### <a name="linux-devices"></a>Linux-eszközök

Ha az IoT Edge futásidejét linuxos eszközre telepíti, konfigurálja úgy a csomagkezelőt, hogy a proxykiszolgálón keresztül férjen hozzá a telepítőcsomaghoz. Beállíthatja például, [hogy az apt-get http-proxyt használjon.](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy) A csomagkezelő konfigurálása után kövesse az [Azure IoT Edge futásidejű telepítése linuxos futtatási](how-to-install-iot-edge-linux.md) útmutató utasításait a szokásos módon.

### <a name="windows-devices"></a>Windows rendszerű eszközök

Ha az IoT Edge futásidejét windowsos eszközre telepíti, kétszer kell átmennie a proxykiszolgálón. Az első kapcsolat letölti a telepítő parancsfájlt, a második kapcsolat pedig a telepítés során a szükséges összetevők letöltéséhez szükséges. A Proxyadatokat konfigurálhatja a Windows beállításaiban, vagy a proxyadatokat közvetlenül a PowerShell-parancsokban is megadhatja.

A következő lépések egy windowsos telepítést mutatnak be az `-proxy` argumentum használatával:

1. Az Invoke-WebRequest parancsnak proxyadatokra van szüksége a telepítő parancsfájl eléréséhez. Ezután a Deploy-IoTEdge parancsnak szüksége van a proxyadatokra a telepítési fájlok letöltéséhez.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Az Initialize-IoTEdge parancsnak nem kell a proxykiszolgálón keresztül mennie, így a második lépés csak az Invoke-WebRequest proxyadatait igényli.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Ha bonyolult hitelesítő adatokkal rendelkezik a proxykiszolgálóhoz, amely nem `-ProxyCredential` szerepelhet az URL-címben, használja a paramétert a területen. `-InvokeWebRequestParameters` Például:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

A proxyparaméterekről az [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest)című témakörben talál további információt. A Windows telepítési beállításairól, beleértve az offline telepítést is, az [Azure IoT Edge futásidejének telepítése Windows rendszeren](how-to-install-iot-edge-windows.md)című témakörben talál további információt.

## <a name="configure-the-daemons"></a>A démonok konfigurálása

Az IoT Edge két démonra támaszkodik, amelyek az IoT Edge-eszközön futnak. A Moby démon webes kérelmeket küld a tárolórendszerképek lekérése a tároló-jegyzékekből. Az IoT Edge démon webes kérelmeket küld az IoT Hubbal való kommunikációhoz.

Mind a Moby és az IoT Edge démonok kell konfigurálni, hogy a proxykiszolgáló t a folyamatban lévő eszköz működését. Ez a lépés az IoT Edge-eszközön történik a kezdeti eszköz beállítása során.

### <a name="moby-daemon"></a>Moby démon

Mivel a Moby a Docker-re épül, tekintse meg a Docker dokumentációját a Moby démon környezeti változókkal való konfigurálásához. A legtöbb tárolóbeállítás (beleértve a DockerHub ot és az Azure-tároló-beállításjegyzékeket is) támogatja a HTTPS-kérelmeket, így a beállítandó paraméter **HTTPS_PROXY.** Ha olyan rendszerleíró adatbázisból keres képeket, amely nem támogatja az átviteliréteg-biztonságot (TLS), akkor állítsa be a **HTTP_PROXY** paramétert.

Válassza ki az IoT Edge-eszköz operációs rendszerére vonatkozó cikket:

* [Docker démon konfigurálása Linuxon](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) A Moby démon Linux-eszközökön megtartja a Docker nevet.
* [Docker démon konfigurálása Windows rendszeren](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) A Moby démon a Windows eszközökön az úgynevezett iotedge-moby. A nevek különbözőek, mert a Docker Desktop és a Moby is futtathat párhuzamosan egy Windows-eszközön.

### <a name="iot-edge-daemon"></a>IoT Edge démon

Az IoT Edge démon a Moby démonhoz hasonló módon van konfigurálva. Az alábbi lépésekkel az operációs rendszer től függően állíthatja be a szolgáltatás környezeti változóját.

Az IoT Edge démon mindig HTTPS-t használ az IoT Hubnak küldött kérelmek küldéséhez.

#### <a name="linux"></a>Linux

Nyisson meg egy szerkesztőt a terminálon az IoT Edge démon konfigurálásához.

```bash
sudo systemctl edit iotedge
```

Írja be a következő szöveget, lecserélve ** \<a proxy URL-címét>** a proxykiszolgáló címére és portjára. Ezután mentse és lépjen ki.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Frissítse a szolgáltatáskezelőt az IoT Edge új konfigurációjának felvételéhez.

```bash
sudo systemctl daemon-reload
```

Indítsa újra az IoT Edge-et a módosítások érvénybe léptetéséhez.

```bash
sudo systemctl restart iotedge
```

Ellenőrizze, hogy létrejött-e a környezeti változó, és hogy az új konfiguráció be lett-e töltve.

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Nyisson meg egy PowerShell-ablakot rendszergazdaként, és futtassa a következő parancsot a rendszerleíró adatbázis szerkesztéséhez az új környezeti változóval. Cserélje le ** \<a proxy URL->** a proxykiszolgáló címére és portjára.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Indítsa újra az IoT Edge-et a módosítások érvénybe léptetéséhez.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Az IoT Edge-ügynök konfigurálása

Az IoT Edge-ügynök az első modul, amely bármely IoT Edge-eszközön elindul. Először az IoT Edge config.yaml fájlban lévő információk alapján indult el. Az IoT Edge-ügynök ezután csatlakozik az IoT Hubhoz központi jegyzékek lekéréséhez, amely deklarálja, hogy milyen más modulokat kell telepíteni az eszközön.

Ez a lépés egyszer történik meg az IoT Edge-eszközön a kezdeti eszköz beállítása során.

1. Nyissa meg a config.yaml fájlt az IoT Edge-eszközön. Linux rendszereken ez a fájl az **/etc/iotedge/config.yaml**. Windows rendszereken ez a fájl a **C:\ProgramData\iotedge\config.yaml**. A konfigurációs fájl védett, ezért rendszergazdai jogosultságokra van szüksége a hozzáféréshez. Linux rendszereken használja `sudo` a parancsot, mielőtt megnyitja a fájlt a kívánt szövegszerkesztőben. Windows rendszerben nyisson meg egy olyan szövegszerkesztőt, mint a Jegyzettömb rendszergazdaként, majd nyissa meg a fájlt.

2. A config.yaml fájlban keresse meg az **Edge Agent modul specifikációját.** Az IoT Edge-ügynök definíciója tartalmaz egy **env paramétert,** ahol környezeti változókat adhat hozzá.

3. Távolítsa el az env paraméter helyőrzőinek göndör zárójeleit, és adja hozzá az új változót egy új sorhoz. Ne feledje, hogy a YAML-ben a behúzás két szóköz.

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. Az IoT Edge futásidejű használ AMQP alapértelmezés szerint az IoT Hub. Egyes proxykiszolgálók blokkolják az AMQP-portokat. Ha ez a helyzet, akkor is be kell állítania az edgeAgent-t az AMQP websocket-en keresztüli használatához. Adjon hozzá egy második környezeti változót.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![edgeAgent definíció környezeti változókkal](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Mentse a módosításokat config.yaml és zárja be a szerkesztőt. Indítsa újra az IoT Edge-et a módosítások érvénybe léptetéséhez.

   * Linux:

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Központi telepítési jegyzékek konfigurálása  

Miután az IoT Edge-eszköz úgy van konfigurálva, hogy működjön együtt a proxykiszolgálóval, továbbra is deklarálnia kell a HTTPS_PROXY környezeti változót a jövőbeli központi telepítési jegyzékekben. A központi telepítési jegyzékek szerkesztheti az Azure Portal varázsló vagy egy központi telepítési jegyzékjegyzék JSON-fájl szerkesztésével.

A két futásidejű modult, az edgeAgentet és az edgeHubot mindig konfigurálja a proxykiszolgálón keresztüli kommunikációra, hogy azok kapcsolatban tudjanak tartani az IoT Hubbal. Ha eltávolítja a proxyadatokat az edgeAgent modulból, a kapcsolat visszaállításának egyetlen módja a config.yaml fájl szerkesztése az eszközön, az előző szakaszban leírtak szerint.

Az edgeAgent és az edgeHub modulok mellett más moduloknak is szükségük lehet a proxykonfigurációra. Ezek olyan modulok, amelyek az IoT Hubon kívül az Azure-erőforrások eléréséhez, például a blob storage eléréséhez kell, és rendelkezniük kell a HTTPS_PROXY változóval az adott modulhoz a központi telepítési jegyzékfájlban.

A következő eljárás az IoT Edge-eszköz teljes élettartama alatt alkalmazható.

### <a name="azure-portal"></a>Azure Portal

Ha a **Modulok beállítása** varázsló segítségével hozza létre az IoT Edge-eszközök központi telepítéseit, minden modul rendelkezik egy **környezeti változók szakaszsal,** amely proxykiszolgáló-kapcsolatok konfigurálásához használható.

Az IoT Edge-ügynök és az IoT Edge hub-modulok konfigurálásához válassza a **Futásidejű beállítások lehetőséget** a varázsló első lépésében.

![Speciális Edge futásidejű beállítások konfigurálása](./media/how-to-configure-proxy-support/configure-runtime.png)

Adja hozzá a **https_proxy** környezeti változót az IoT Edge-ügynök höz és az IoT Edge hubmodul-definíciókhoz. Ha az **UpstreamProtocol** környezeti változót az IoT Edge-eszközön lévő config.yaml fájlba is felvette, adja hozzá az IoT Edge-ügynök moduldefiníciójához is.

![Https_proxy környezeti változó beállítása](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

A központi telepítési jegyzékhez hozzáadott összes többi modul ugyanazt a mintát követi. A modul nevét és a lemezképet tartalmazó lapon van egy környezeti változók szakasza.

### <a name="json-deployment-manifest-files"></a>JSON telepítési jegyzékfájljai

Ha az IoT Edge-eszközök höz a Visual Studio Code sablonjainak használatával vagy a JSON-fájlok manuális létrehozásával hoz létre központi telepítéseket, a környezeti változókat közvetlenül hozzáadhatja az egyes moduldefiníciókhoz.

Használja a következő JSON formátumot:

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

A környezeti változók is, a modul definíciója kell kinéznie a következő edgeHub példa:

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

Ha az **UpstreamProtocol** környezeti változót az IoT Edge-eszközön lévő confige.yaml fájlba is felvette, adja hozzá az IoT Edge-ügynök moduldefiníciójához is.

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

További információ az [IoT Edge-futásidejű](iot-edge-runtime.md)szerepköreiről.

Telepítési és konfigurációs hibák elhárítása [az Azure IoT Edge gyakori problémáival és megoldásaival](troubleshoot.md)
