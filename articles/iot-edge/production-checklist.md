---
title: Eszközök és az éles környezetben – Azure IoT Edge üzemelő példányok előkészítése |} A Microsoft Docs
description: Megtudhatja, hogyan hozhatja létre a Azure IoT Edge-megoldását a fejlesztéstől a Termelésig, beleértve az eszközök beállítását a megfelelő tanúsítványokkal, valamint a jövőbeli programkód-frissítések üzembe helyezési tervének kidolgozását.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 29a771b93e1d686f7972e7dc4d9e78e5858644d6
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899403"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Az üzembe helyezés éles környezetben az IoT Edge-megoldás előkészítése

Ha készen áll az IoT Edge-megoldás a fejlesztéstől az éles környezetben elvégzendő, győződjön meg arról, hogy van-e konfigurálva a folyamatban lévő teljesítmény.

A cikkben szereplő információk nem egyenlőek. Könnyebben rangsorolja, minden szakasz eloszthatja a munkahelyi felosztása két szakasszá listák kezdődik: **fontos** befejezését, mielőtt éles, vagy **hasznos** , hogy ismeri.

## <a name="device-configuration"></a>Eszközök konfigurálása

IoT Edge-eszközökön a hordozható egy kiszolgálón futó virtuális géphez a Raspberry Pi bármi lehet. Előfordulhat, hogy rendelkezik az eszköz fizikailag vagy virtuális-kapcsolaton keresztül, vagy lehet elkülönített huzamosabb ideig. Mindkét esetben meg kell győződnie arról, hogy a megfelelő működésre van konfigurálva. 

* **Fontos**
    * Éles tanúsítványok telepítése
    * Egy eszköz felügyeleti csomagra
    * A tároló motorként Moby használata

* **Hasznos**
    * Válassza ki a felsőbb rétegbeli protokoll

### <a name="install-production-certificates"></a>Éles tanúsítványok telepítése

Minden IoT Edge-eszköz éles környezetben szüksége van egy eszköz tanúsítványt hitelesítésszolgáltató (CA) tanúsítvány telepítve van. A Hitelesítésszolgáltatói tanúsítvány majd van meghatározva, hogy az IoT Edge-futtatókörnyezet a config.yaml fájlban. Hogy a fejlesztést és tesztelést, az IoT Edge futásidejű tanúsítványokat hoz létre ideiglenes Ha nincsenek tanúsítványok vannak deklarálva a config.yaml fájlban. Ezek a tanúsítványok ideiglenes azonban három hónapig érvényesek, és nem biztonságos a termelési forgatókönyvekhez. 

A szerepkör az eszköz Hitelesítésszolgáltatói tanúsítvány ismertetése: [Azure IoT Edge-tanúsítványokat használ](iot-edge-certs.md).

Telepítse a tanúsítványokat a IoT Edge-eszköz, és hivatkozni tudjon rájuk a config.yaml fájlból kapcsolatos további információkért lásd: [a transzparens átjáróként működő IoT Edge-eszköz konfigurálása](how-to-create-transparent-gateway.md). A tanúsítványok konfigurálásának lépéseit megegyeznek-e az eszközre települni, egy átjárót, vagy nem használható. A cikk ismerteti a parancsfájlok készítése a mintául szolgáló tanúsítványok csak tesztelési. Mintául szolgáló tanúsítványok ne használja éles környezetben. 

### <a name="have-a-device-management-plan"></a>Egy eszköz felügyeleti csomagra

Mielőtt bármilyen eszközön az éles környezetben ismernie kell azt a módszert, kezelheti a jövőbeli frissítéseket. Az IoT Edge-eszköz frissítéséhez összetevők listájának lehetnek:

* Eszköz belső vezérlőprogramjának
* Operációs rendszer kódtárak
* Tároló-motort, például Moby
* IoT Edge-démon
* Hitelesítésszolgáltatói tanúsítványok

További információ: [a IoT Edge futtatókörnyezet frissítése](how-to-update-iot-edge.md). Az aktuális frissítési az IoT Edge-démon módszerhez fizikai vagy SSH-hozzáférést az IoT Edge-eszközön. Ha sok eszközt szeretne frissíteni, vegye fel a frissítési lépéseket egy parancsfájlba, vagy használjon olyan Automation-eszközt, mint például a Ansible.

### <a name="use-moby-as-the-container-engine"></a>A tároló motorként Moby használata

A tároló-motor minden IoT Edge eszköz előfeltétele. Éles környezetben csak moby-motor használata támogatott. Egyéb tároló-motorok, például a Dockert, az IoT Edge szolgáltatással működik, és ezek motorok fejlesztési célra rendben. Az Azure IoT Edge használatakor újra a moby-motor, és a Microsoft biztosítja, hogy ez a motor karbantartása.

### <a name="choose-upstream-protocol"></a>Válassza ki a felsőbb rétegbeli protokoll

A IoT Hubhoz való upstream kommunikációhoz használt protokoll (és így a port is) konfigurálható mind a IoT Edge-ügynök, mind a IoT Edge hub számára. Az alapértelmezett protokoll AMQP, de érdemes módosíthatja, hogy a hálózati konfigurációtól függően. 

A két futásidejű modulok is rendelkeznek egy **UpstreamProtocol** környezeti változót. A változó az érvényes értékek a következők: 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Konfigurálja a UpstreamProtocol változót a IoT Edge ügynökhöz a config. YAML fájlban az eszközön. Ha például a IoT Edge eszköz egy olyan proxykiszolgáló mögött található, amely blokkolja a AMQP-portokat, előfordulhat, hogy a IoT Edge-ügynököt úgy kell konfigurálnia, hogy az AMQP-t használja a WebSocket (AMQPWS) használatával a kezdeti kapcsolat létrehozásához a IoT Hub. 

Miután csatlakozik az IoT Edge-eszköz, ügyeljen arra, folytathatja a későbbiekben mindkét futásidejű modulok UpstreamProtocol változója konfigurálását. Ez a folyamat, például a [proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Környezet

* **Hasznos**
    * Használja következetesen a felsőbb rétegbeli protokoll
    * Gazdagép-tároló beállítása rendszermodulokhoz
    * Csökkentse a IoT Edge hub által használt memória méretét
    * Ne használjon modul képeket hibakeresés verziói

### <a name="be-consistent-with-upstream-protocol"></a>Használja következetesen a felsőbb rétegbeli protokoll

Ha úgy konfigurálta a IoT Edge ügynököt a IoT Edge eszközön, hogy az alapértelmezett AMQP eltérő protokollt használjon, akkor minden jövőbeli telepítésnél ugyanazt a protokollt kell deklarálnia. Például ha az IoT Edge-eszköz, amely blokkolja az AMQP-portok proxykiszolgáló mögött található, valószínűleg konfigurálta az eszköz felett WebSocket (AMQPWS) amqp-n keresztül csatlakozhat. Amikor modulokat telepít az eszközre, konfigurálja ugyanazt a APQPWS protokollt a IoT Edge-ügynökhöz és a IoT Edge hub-hoz, különben az alapértelmezett AMQP felülbírálja a beállításokat, és megakadályozza a csatlakozást. 

A UpstreamProtocol környezeti változót csak a IoT Edge ügynökhöz és IoT Edge hub-modulokhoz kell konfigurálnia. Minden további modulok fogad el, függetlenül a protokoll van beállítva, a futásidejű modulok. 

Ez a folyamat, például a [proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Gazdagép-tároló beállítása rendszermodulokhoz

Az IoT Edge hub és az Agent modulok a helyi tárterületet használják az állapot fenntartásához és a modulok, eszközök és a felhő közötti üzenetkezelés engedélyezéséhez. A jobb megbízhatóság és teljesítmény érdekében konfigurálja a rendszermodulokat a gazdagép fájlrendszerén a tároló használatára.

További információ: [Host Storage for System modulok](offline-capabilities.md#host-storage-for-system-modules).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>IoT Edge hub által használt memória méretének csökkentése

Ha korlátozott számú memóriával rendelkező korlátozott méretű eszközöket helyez üzembe, az IoT Edge hub-t úgy konfigurálhatja, hogy egyszerűbben fusson, és kevesebb lemezterületet használjon. Ezek a konfigurációk korlátozzák az IoT Edge hub teljesítményét, ezért érdemes megkeresni a megoldáshoz használható megfelelő egyensúlyt. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Ne optimalizálja a teljesítmény korlátozott eszközökön

Az IoT Edge hub alapértelmezés szerint a teljesítményre van optimalizálva, így nagy mennyiségű memóriát próbál lefoglalni. Ez a konfiguráció stabilitását problémákat okozhat például a Raspberry Pi kisebb eszközökön. Ha korlátozott erőforrásokkal rendelkező eszközöket helyez üzembe, érdemes lehet **hamis** értékre állítani a **OptimizeForPerformance** környezeti változót az IoT Edge központban. 

További információkért lásd: [stabilitási problémák erőforráson eszközök korlátozott](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>A fel nem használt protokollok letiltása

Az IoT Edge hub teljesítményének optimalizálása és a memóriahasználat csökkentése is lehetővé teszi, hogy kikapcsolja a protokollok fejeit a megoldásában nem használt protokollok esetében. 

A protokollok a központi telepítési jegyzékekben a IoT Edge hub modul logikai környezeti változóinak beállításával konfigurálhatók. A három változók az alábbiak:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Minden mindhárom változóval rendelkezik *két aláhúzásjeleket* és állítható a true vagy FALSE (hamis). 

#### <a name="reduce-storage-time-for-messages"></a>Üzenetek tárolása idő csökkentése

Az IoT Edge hub-modul átmenetileg tárolja az üzeneteket, ha bármilyen okból nem lehet IoT Hub továbbítani őket. Azt is beállíthatja, hogy az IoT Edge hub mennyi ideig tartson a nem leküldött üzeneteknek a lejárat előtt. Ha a memóriával kapcsolatos problémái vannak az eszközön, csökkentheti a **timeToLiveSecs** értéket az IoT Edge hub-modul Twin-ben. 

Az alapértelmezett timeToLiveSecs paraméter értéke 7200 másodperc, két órát jelent. 

### <a name="do-not-use-debug-versions-of-module-images"></a>Ne használjon modul képeket hibakeresés verziói

Ha termelési forgatókönyvekhez tesztelési helyzetek áthelyezését, ne felejtse el eltávolítani a hibakeresési konfigurációk az üzembe helyezés jegyzékek. Ellenőrizze, hogy egyetlen sem található a központi telepítési jegyzékek modul rendszerképek rendelkezik a  **\.debug** utótag. Ha hozzáadott lehetőségei a hibakeresés, a modulok portokat tesz elérhetővé, távolítsa el azokat, hozzon létre lehetőségeket is. 

## <a name="container-management"></a>Tárolófelügyelet

* **Fontos**
    * A tárolóregisztrációs adatbázisba való hozzáférés kezelése
    * Címkék használatával verziók kezelése

### <a name="manage-access-to-your-container-registry"></a>A tárolóregisztrációs adatbázisba való hozzáférés kezelése

Éles IoT Edge-eszközökön a modulok telepítéséhez, győződjön meg arról, hogy elérését Ön szabályozza a tárolóregisztrációs adatbázisba, hogy illetéktelenek nem eléréséhez, vagy módosítja a tárolórendszerképeket. Nem nyilvános, privát tárolójegyzéket használatával kezelheti a tárolólemezképeket. 

Az oktatóanyagok és egyéb dokumentáció hogy kérje meg, hogy az ugyanazon tárolóban a tárolójegyzék hitelesítő adatainak használata az IoT Edge-eszköz használata során a fejlesztői gépen. Ezek az utasítások csak a segítségével könnyebben tesztelési és fejlesztési környezet beállítása szolgál, és nem kell követni egy éles forgatókönyvet. Az Azure Container Registry javasolja [hitelesítését a szolgáltatásnevek](../container-registry/container-registry-auth-service-principal.md) Ha alkalmazások vagy szolgáltatások kérni tárolórendszerképeket felügyelet nélküli módban automatizált vagy más módon, IoT Edge-eszközök esetében. Egyszerű szolgáltatás létrehozása a tárolóregisztrációs adatbázis csak olvasható hozzáférést, és adja meg a felhasználónevet és jelszót a manifest nasazení.

### <a name="use-tags-to-manage-versions"></a>Címkék használatával verziók kezelése

A címke egy Docker-koncepció, amellyel megkülönböztethető a Docker-tárolók verziói között. A címkék olyan például utótagok **1.0** , amely egy tároló-beállításjegyzékbe végén lépjen. Ha például **mcr.microsoft.com/azureiotedge-agent:1.0**. A címkék mutable és módosítható átirányítása egy másik tárolóba tetszőleges időpontban, így csapata egy konvenciókhoz, és hajtsa végre a továbblépés modul rendszerképek frissítése során meg kell egyezniük. 

A címkék segítségével érvényesítését a frissítések az IoT Edge-eszközökön. Amikor leküld egy frissített verzióját egy modul a tárolóregisztrációs adatbázisba, növelje meg a címke. Ezután küldje le új központi telepítést az eszközök növekszik a címkével ellátott. A tároló motor fogja felismerni a megnövelt címke új verzióként, és kéri a modul legújabb saját eszközre. 

Egy címke egyezmény egy példa: [az IoT Edge-futtatókörnyezet frissítése](how-to-update-iot-edge.md#understand-iot-edge-tags) megtudhatja, hogyan IoT Edge segítségével működés közbeni címkék és a meghatározott címkék követi nyomon verziók. 

## <a name="networking"></a>Hálózat

* **Hasznos**
    * Kimenő és bejövő konfiguráció áttekintése
    * IoT Edge eszközök kapcsolatainak engedélyezése
    * Egy proxyn keresztül történő kommunikáció konfigurálása

### <a name="review-outboundinbound-configuration"></a>Kimenő és bejövő konfiguráció áttekintése

Azure IoT Hub és az IoT Edge közötti kommunikációs csatornákat mindig legyen a kimenő vannak konfigurálva. A legtöbb IoT Edge-forgatókönyvekhez csak három kapcsolatok szükségesek. A tároló motor kell csatlakoztatnia a tárolóregisztrációs adatbázis (vagy beállításjegyzékek), amely a modul képeket tárol. Az IoT Edge-futtatókörnyezet kell csatlakozni az IoT Hub eszköz konfigurációt információgyűjtéshez, és az üzenetek és a telemetriai adatok küldése. És az Automatikus kiépítés használatakor az IoT Edge-démon kell-e csatlakozni a Device Provisioning Service. További információkért lásd: [tűzfal és a port konfigurációs szabályok](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="allow-connections-from-iot-edge-devices"></a>IoT Edge eszközök kapcsolatainak engedélyezése

Ha a hálózatkezelés beállítása megköveteli, hogy explicit módon engedélyezze a IoT Edge eszközökről érkező kapcsolatokat, tekintse át az alábbi IoT Edge összetevők listáját:

* **IoT Edge-ügynök** kapcsolatot nyit a állandó AMQP és MQTT az IoT hubhoz, valószínűleg a websockets protokoll keresztül. 
* **IoT Edge hubot** megnyit egy állandó egyetlen AMQP-kapcsolaton vagy több MQTT-kapcsolatot az IoT hubhoz, valószínűleg a websockets protokoll keresztül. 
* **IoT Edge-démon** az IoT hub időszakos HTTPS-hívást hajt végre. 

Három esetben a DNS-név megfelel a mintának \*.azure-devices.net. 

Ezenkívül a **Container-motor** tárolójegyzékek-hívást hajt végre a HTTPS-kapcsolaton keresztül. Az IoT Edge-futtatókörnyezet tárolórendszerképek lekéréséhez a DNS-név mcr.microsoft.com. A tároló motor más beállításjegyzékek csatlakozik, a központi telepítésben lévő konfigurálva. 

Ez az ellenőrzőlista a tűzfalszabályok kiindulási pontja:

   | URL-\* cím (= helyettesítő karakter) | Kimenő TCP-portok | Használat |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft Container Registry |
   | global.azure-devices-provisioning.net  | 443 | DPS-hozzáférés (nem kötelező) |
   | \*.azurecr.io | 443 | Személyes és harmadik féltől származó tároló-nyilvántartások |
   | \*.blob.core.windows.net | 443 | Rendszerkép-különbözetek letöltése | 
   | \*. azure-devices.net | 5671, 8883, 443 | IoT Hub hozzáférés |
   | \*. docker.io  | 443 | Docker hub-hozzáférés (nem kötelező) |

### <a name="configure-communication-through-a-proxy"></a>Egy proxyn keresztül történő kommunikáció konfigurálása

Az eszközök fog üzembe helyezni a hálózaton, proxykiszolgálót használ, ha azok kell elérni az IoT Hub és a tároló-beállításjegyzékek a proxyn keresztül kommunikálnak. További információkért lásd: [proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Megoldások kezelése

* **Hasznos**
    * Naplók és diagnosztika beállítása
    * Vegye figyelembe a tesztek és CI/CD-folyamatok

### <a name="set-up-logs-and-diagnostics"></a>Naplók és diagnosztika beállítása

Linux rendszeren a IoT Edge démon az alapértelmezett naplózási illesztőprogramként használja a naplókat. A parancssori eszközzel `journalctl` lekérdezni a démon naplózza. A Windows az IoT Edge-démon a PowerShell diagnosztikai használ. Használat `Get-IoTEdgeLog` a lekérdezések naplói a démonból. IoT Edge modulok a JSON-illesztőprogramot használják a naplózáshoz, amely az alapértelmezett.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Amikor egy IoT Edge-példányban tesztel, kérheti le a naplókat és hárítsa el az eszközök általában hozzáférhet. A telepítési forgatókönyvben nem lehet ezt a lehetőséget. Vegye figyelembe, hogy azt a módszert, éles környezetben az eszközökkel kapcsolatos információk összegyűjtéséhez. Az egyik lehetőség, hogy egy naplózási modul, amely adatokat gyűjt a más modulok tesznek, és elküldi a felhőbe. Például egy naplózási modul [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics), vagy a saját is tervezhet. 

### <a name="place-limits-on-log-size"></a>Korlátok elhelyezése a naplók méretében

Alapértelmezés szerint a Moby Container Engine nem állítja be a tároló naplójának méretére vonatkozó korlátozásokat. Az idő múlásával az eszköz betöltődik a naplókba, és elfogyott a szabad lemezterület. Ennek elkerüléséhez vegye figyelembe a következő lehetőségeket:

**Beállítás Az összes tároló modulra érvényes globális korlátok megadása**

Az összes tároló-naplófájl méretét korlátozhatja a tároló-motor naplójának beállításaiban. A következő példa a log illesztőprogramot `json-file` a (javasolt) értékre állítja be, amely korlátozza a méretet és a fájlok számát:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Adja hozzá (vagy fűzze hozzá) ezt az információt egy `daemon.json` nevű fájlhoz, és helyezze el a megfelelő helyet az eszköz platformjának.

| Platform | Location |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

A módosítások életbe léptetéséhez újra kell indítani a tároló motorját.

**Beállítás Az egyes tároló modulok naplózási beállításainak módosítása**

Ezt megteheti az egyes modulok **createOptions** . Példa:

```yml
"createOptions": {
    "HostConfig": {
        "LogConfig": {
            "Type": "json-file",
            "Config": {
                "max-size": "10m",
                "max-file": "3"
            }
        }
    }
}
```

**További beállítások Linux rendszereken**

* Konfigurálja úgy a tároló motort, hogy `systemd` az alapértelmezett naplózási illesztőprogram beállításával [](https://docs.docker.com/config/containers/logging/journald/) `journald` küldje el a naplókat a naplókba. 

* A logrotate eszköz telepítésével rendszeresen távolítsa el a régi naplókat az eszközről. Használja a következő fájl megadása: 

   ```
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>Vegye figyelembe a tesztek és CI/CD-folyamatok

A leghatékonyabb IoT Edge-környezetben fontolja meg az éles környezet integrálása a tesztelés és a CI/CD-folyamatok. Az Azure IoT Edge CI/CD több platformon, beleértve az Azure DevOps támogatja. További információkért lásd: [folyamatos integrációt és folyamatos üzembe helyezés az Azure IoT Edge-ben](how-to-ci-cd.md).

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [IoT Edge automatikus központi telepítési](module-deployment-monitoring.md).
* Tekintse meg, hogyan támogatja az IoT Edge [folyamatos integrációt és folyamatos üzembe helyezés](how-to-ci-cd.md).
