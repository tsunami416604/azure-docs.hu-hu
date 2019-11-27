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
ms.openlocfilehash: 1d8ba8452f5f2d4ab05083e1a97fa0b9ba75017f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457305"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Az üzembe helyezés éles környezetben az IoT Edge-megoldás előkészítése

Ha készen áll az IoT Edge-megoldás a fejlesztéstől az éles környezetben elvégzendő, győződjön meg arról, hogy van-e konfigurálva a folyamatban lévő teljesítmény.

A cikkben szereplő információk nem egyenlőek. A rangsorolás elősegítése érdekében az egyes szakaszok a következő két szakaszra oszthatják meg a munkát: **fontos** , hogy elvégezze az éles környezetbe való bevezetést, vagy a **hasznos** tudnivalókat.

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

Az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa szerepkörének megismeréséhez tekintse meg a [hogyan használja a Azure IoT Edge a tanúsítványokat](iot-edge-certs.md).

Ha további információt szeretne arról, hogyan telepíthet tanúsítványokat egy IoT Edge eszközre, és hogyan hivatkozhat rájuk a config. YAML fájlból, tekintse [meg az IoT Edge eszköz konfigurálása transzparens átjáróként való](how-to-create-transparent-gateway.md)működésre című témakört. A tanúsítványok konfigurálásának lépéseit megegyeznek-e az eszközre települni, egy átjárót, vagy nem használható. A cikk ismerteti a parancsfájlok készítése a mintául szolgáló tanúsítványok csak tesztelési. Mintául szolgáló tanúsítványok ne használja éles környezetben. 

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

A két futásidejű modul **UpstreamProtocol** környezeti változóval rendelkezik. A változó az érvényes értékek a következők: 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Konfigurálja a UpstreamProtocol változót a IoT Edge ügynökhöz a config. YAML fájlban az eszközön. Ha például a IoT Edge eszköz egy olyan proxykiszolgáló mögött található, amely blokkolja a AMQP-portokat, előfordulhat, hogy a IoT Edge-ügynököt úgy kell konfigurálnia, hogy az AMQP-t használja a WebSocket (AMQPWS) használatával a kezdeti kapcsolat létrehozásához a IoT Hub. 

Miután csatlakozik az IoT Edge-eszköz, ügyeljen arra, folytathatja a későbbiekben mindkét futásidejű modulok UpstreamProtocol változója konfigurálását. Ennek a folyamatnak egy példája a [IoT Edge eszköz konfigurálása egy proxykiszolgálón keresztül történő kommunikációra](how-to-configure-proxy-support.md)című cikkben található.

## <a name="deployment"></a>Környezet

* **Hasznos**
    * Használja következetesen a felsőbb rétegbeli protokoll
    * Gazdagép-tároló beállítása rendszermodulokhoz
    * Csökkentse a IoT Edge hub által használt memória méretét
    * Ne használjon modul képeket hibakeresés verziói

### <a name="be-consistent-with-upstream-protocol"></a>Használja következetesen a felsőbb rétegbeli protokoll

Ha úgy konfigurálta a IoT Edge ügynököt a IoT Edge eszközön, hogy az alapértelmezett AMQP eltérő protokollt használjon, akkor minden jövőbeli telepítésnél ugyanazt a protokollt kell deklarálnia. Például ha az IoT Edge-eszköz, amely blokkolja az AMQP-portok proxykiszolgáló mögött található, valószínűleg konfigurálta az eszköz felett WebSocket (AMQPWS) amqp-n keresztül csatlakozhat. Amikor modulokat telepít az eszközre, konfigurálja ugyanazt a AMQPWS protokollt a IoT Edge-ügynökhöz és a IoT Edge hub-hoz, különben az alapértelmezett AMQP felülbírálja a beállításokat, és megakadályozza a csatlakozást. 

A UpstreamProtocol környezeti változót csak a IoT Edge ügynökhöz és IoT Edge hub-modulokhoz kell konfigurálnia. Minden további modulok fogad el, függetlenül a protokoll van beállítva, a futásidejű modulok. 

Ennek a folyamatnak egy példája a [IoT Edge eszköz konfigurálása egy proxykiszolgálón keresztül történő kommunikációra](how-to-configure-proxy-support.md)című cikkben található.

### <a name="set-up-host-storage-for-system-modules"></a>Gazdagép-tároló beállítása rendszermodulokhoz

Az IoT Edge hub és az Agent modulok a helyi tárterületet használják az állapot fenntartásához és a modulok, eszközök és a felhő közötti üzenetkezelés engedélyezéséhez. A jobb megbízhatóság és teljesítmény érdekében konfigurálja a rendszermodulokat a gazdagép fájlrendszerén a tároló használatára.

További információ: [Host Storage for System modulok](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>IoT Edge hub által használt memória méretének csökkentése

Ha korlátozott számú memóriával rendelkező korlátozott méretű eszközöket helyez üzembe, az IoT Edge hub-t úgy konfigurálhatja, hogy egyszerűbben fusson, és kevesebb lemezterületet használjon. Ezek a konfigurációk korlátozzák az IoT Edge hub teljesítményét, ezért érdemes megkeresni a megoldáshoz használható megfelelő egyensúlyt. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Ne optimalizálja a teljesítmény korlátozott eszközökön

Az IoT Edge hub alapértelmezés szerint a teljesítményre van optimalizálva, így nagy mennyiségű memóriát próbál lefoglalni. Ez a konfiguráció stabilitását problémákat okozhat például a Raspberry Pi kisebb eszközökön. Ha korlátozott erőforrásokkal rendelkező eszközöket helyez üzembe, érdemes lehet **hamis** értékre állítani a **OptimizeForPerformance** környezeti változót az IoT Edge központban. 

Ha a **OptimizeForPerformance** értéke **true (igaz**), a MQTT-protokoll feje a PooledByteBufferAllocator-t használja, amely jobb teljesítményt nyújt, de több memóriát foglal le. A lefoglaló nem működik megfelelően a 32 bites operációs rendszereken, illetve az alacsony memóriával rendelkező eszközökön. Emellett, ha teljesítményre optimalizált, a RocksDb több memóriát foglal le a szerepköréhez, mint a helyi tároló szolgáltatója. 

További információ: [stabilitási problémák az erőforrás által korlátozott eszközökön](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>A fel nem használt protokollok letiltása

Az IoT Edge hub teljesítményének optimalizálása és a memóriahasználat csökkentése is lehetővé teszi, hogy kikapcsolja a protokollok fejeit a megoldásában nem használt protokollok esetében. 

A protokollok a központi telepítési jegyzékekben a IoT Edge hub modul logikai környezeti változóinak beállításával konfigurálhatók. A három változók az alábbiak:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Mindhárom változó *két aláhúzással* rendelkezik, és beállítható igaz vagy hamis értékre. 

#### <a name="reduce-storage-time-for-messages"></a>Üzenetek tárolása idő csökkentése

Az IoT Edge hub-modul átmenetileg tárolja az üzeneteket, ha bármilyen okból nem lehet IoT Hub továbbítani őket. Azt is beállíthatja, hogy az IoT Edge hub mennyi ideig tartson a nem leküldött üzeneteknek a lejárat előtt. Ha a memóriával kapcsolatos problémái vannak az eszközön, csökkentheti a **timeToLiveSecs** értéket az IoT Edge hub-modul Twin-ben. 

Az alapértelmezett timeToLiveSecs paraméter értéke 7200 másodperc, két órát jelent. 

### <a name="do-not-use-debug-versions-of-module-images"></a>Ne használjon modul képeket hibakeresés verziói

Ha termelési forgatókönyvekhez tesztelési helyzetek áthelyezését, ne felejtse el eltávolítani a hibakeresési konfigurációk az üzembe helyezés jegyzékek. Győződjön meg arról, hogy az üzembe helyezési jegyzékekben egyik modul rendszerképe sem rendelkezik a **\.hibakeresési** utótaggal. Ha hozzáadott lehetőségei a hibakeresés, a modulok portokat tesz elérhetővé, távolítsa el azokat, hozzon létre lehetőségeket is. 

## <a name="container-management"></a>Tárolófelügyelet

* **Fontos**
    * A tárolóregisztrációs adatbázisba való hozzáférés kezelése
    * Címkék használatával verziók kezelése

### <a name="manage-access-to-your-container-registry"></a>A tárolóregisztrációs adatbázisba való hozzáférés kezelése

Éles IoT Edge-eszközökön a modulok telepítéséhez, győződjön meg arról, hogy elérését Ön szabályozza a tárolóregisztrációs adatbázisba, hogy illetéktelenek nem eléréséhez, vagy módosítja a tárolórendszerképeket. Nem nyilvános, privát tárolójegyzéket használatával kezelheti a tárolólemezképeket. 

Az oktatóanyagok és egyéb dokumentáció hogy kérje meg, hogy az ugyanazon tárolóban a tárolójegyzék hitelesítő adatainak használata az IoT Edge-eszköz használata során a fejlesztői gépen. Ezek az utasítások csak a segítségével könnyebben tesztelési és fejlesztési környezet beállítása szolgál, és nem kell követni egy éles forgatókönyvet. Azure Container Registry javasolja [az egyszerű szolgáltatásokkal történő hitelesítést](../container-registry/container-registry-auth-service-principal.md) , amikor az alkalmazások vagy szolgáltatások egy automatizált vagy más módon felügyelet nélkül lekérik a tárolók lemezképeit, mivel IoT Edge eszközök. Egyszerű szolgáltatás létrehozása a tárolóregisztrációs adatbázis csak olvasható hozzáférést, és adja meg a felhasználónevet és jelszót a manifest nasazení.

### <a name="use-tags-to-manage-versions"></a>Címkék használatával verziók kezelése

A címke egy Docker-koncepció, amellyel megkülönböztethető a Docker-tárolók verziói között. A címkék olyan utótagok, mint a **1,0** , amelyek a tárolók tárházának végére mutatnak. Például: **MCR.microsoft.com/azureiotedge-Agent:1.0**. A címkék mutable és módosítható átirányítása egy másik tárolóba tetszőleges időpontban, így csapata egy konvenciókhoz, és hajtsa végre a továbblépés modul rendszerképek frissítése során meg kell egyezniük. 

A címkék segítségével érvényesítését a frissítések az IoT Edge-eszközökön. Amikor leküld egy frissített verzióját egy modul a tárolóregisztrációs adatbázisba, növelje meg a címke. Ezután küldje le új központi telepítést az eszközök növekszik a címkével ellátott. A tároló motor fogja felismerni a megnövelt címke új verzióként, és kéri a modul legújabb saját eszközre. 

A címkézési konvencióra példát a [IoT Edge futtatókörnyezet frissítése](how-to-update-iot-edge.md#understand-iot-edge-tags) című témakörben talál, amelyből megtudhatja, hogyan használja a IoT Edge a működés közbeni címkéket és adott címkéket a verziók nyomon követésére. 

## <a name="networking"></a>Hálózat

* **Hasznos**
    * Kimenő és bejövő konfiguráció áttekintése
    * IoT Edge eszközök kapcsolatainak engedélyezése
    * Egy proxyn keresztül történő kommunikáció konfigurálása

### <a name="review-outboundinbound-configuration"></a>Kimenő és bejövő konfiguráció áttekintése

Azure IoT Hub és az IoT Edge közötti kommunikációs csatornákat mindig legyen a kimenő vannak konfigurálva. A legtöbb IoT Edge-forgatókönyvekhez csak három kapcsolatok szükségesek. A tároló motor kell csatlakoztatnia a tárolóregisztrációs adatbázis (vagy beállításjegyzékek), amely a modul képeket tárol. Az IoT Edge-futtatókörnyezet kell csatlakozni az IoT Hub eszköz konfigurációt információgyűjtéshez, és az üzenetek és a telemetriai adatok küldése. És az Automatikus kiépítés használatakor az IoT Edge-démon kell-e csatlakozni a Device Provisioning Service. További információ: a [tűzfal és a port konfigurációs szabályai](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="allow-connections-from-iot-edge-devices"></a>IoT Edge eszközök kapcsolatainak engedélyezése

Ha a hálózatkezelés beállítása megköveteli, hogy explicit módon engedélyezze a IoT Edge eszközökről érkező kapcsolatokat, tekintse át az alábbi IoT Edge összetevők listáját:

* **IoT Edge-ügynök** állandó AMQP/MQTT-kapcsolaton keresztül nyitja meg a IoT hub, valószínűleg a WebSockets szolgáltatásban. 
* **IoT Edge hub** egyetlen állandó AMQP-kapcsolatot nyit meg, vagy több MQTT-kapcsolatot hoz IoT hub, valószínűleg a websocketek felett. 
* **IoT Edge démon** időszakos HTTPS-hívásokat végez IoT hub. 

A DNS-név mindhárom esetben a következő mintának felel meg: \*. azure-devices.net. 

Emellett a **Container Engine** a tároló-BEÁLLÍTÁSJEGYZÉKek HTTPS-kapcsolaton keresztüli hívását is lehetővé teszi. Az IoT Edge-futtatókörnyezet tárolórendszerképek lekéréséhez a DNS-név mcr.microsoft.com. A tároló motor más beállításjegyzékek csatlakozik, a központi telepítésben lévő konfigurálva. 

Ez az ellenőrzőlista a tűzfalszabályok kiindulási pontja:

   | URL-cím (\* = helyettesítő karakter) | Kimenő TCP-portok | Használat |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft Container Registry |
   | global.azure-devices-provisioning.net  | 443 | DPS-hozzáférés (nem kötelező) |
   | \*. azurecr.io | 443 | Személyes és harmadik féltől származó tároló-nyilvántartások |
   | \*.blob.core.windows.net | 443 | Azure Container Registry rendszerkép-különbözetek letöltése a blob Storage-ból  | 
   | \*. azure-devices.net | 5671, 8883, 443 | IoT Hub hozzáférés |
   | \*. docker.io  | 443 | Docker hub-hozzáférés (nem kötelező) |

A tűzfalszabályok némelyike a Azure Container Registry örököl. További információkért lásd: [szabályok konfigurálása Azure Container Registry eléréséhez tűzfal mögött](../container-registry/container-registry-firewall-access-rules.md).

### <a name="configure-communication-through-a-proxy"></a>Egy proxyn keresztül történő kommunikáció konfigurálása

Az eszközök fog üzembe helyezni a hálózaton, proxykiszolgálót használ, ha azok kell elérni az IoT Hub és a tároló-beállításjegyzékek a proxyn keresztül kommunikálnak. További információ: [IoT Edge eszköz konfigurálása egy proxykiszolgálón keresztüli kommunikációhoz](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Megoldások kezelése

* **Hasznos**
    * Naplók és diagnosztika beállítása
    * Vegye figyelembe a tesztek és CI/CD-folyamatok

### <a name="set-up-logs-and-diagnostics"></a>Naplók és diagnosztika beállítása

Linux rendszeren a IoT Edge démon az alapértelmezett naplózási illesztőprogramként használja a naplókat. A démoni naplók lekérdezéséhez használhatja a `journalctl` parancssori eszközt. A Windows az IoT Edge-démon a PowerShell diagnosztikai használ. `Get-IoTEdgeLog` használatával kérdezheti le a naplókat a démonból. IoT Edge modulok a JSON-illesztőprogramot használják a naplózáshoz, amely az alapértelmezett.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Amikor egy IoT Edge-példányban tesztel, kérheti le a naplókat és hárítsa el az eszközök általában hozzáférhet. A telepítési forgatókönyvben nem lehet ezt a lehetőséget. Vegye figyelembe, hogy azt a módszert, éles környezetben az eszközökkel kapcsolatos információk összegyűjtéséhez. Az egyik lehetőség, hogy egy naplózási modul, amely adatokat gyűjt a más modulok tesznek, és elküldi a felhőbe. A naplózási modul egy példája a [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics), vagy megtervezheti a sajátját is. 

### <a name="place-limits-on-log-size"></a>Korlátok elhelyezése a naplók méretében

Alapértelmezés szerint a Moby Container Engine nem állítja be a tároló naplójának méretére vonatkozó korlátozásokat. Az idő múlásával az eszköz betöltődik a naplókba, és elfogyott a szabad lemezterület. Ennek elkerüléséhez vegye figyelembe a következő lehetőségeket:

**Lehetőség: az összes tároló modulra érvényes globális korlátok megadása**

Az összes tároló-naplófájl méretét korlátozhatja a tároló-motor naplójának beállításaiban. A következő példában a `json-file` (ajánlott) értékre állítja be a naplózási illesztőprogramot, amely korlátozza a méretet és a fájlok számát:

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

| Platform | Hely |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

A módosítások életbe léptetéséhez újra kell indítani a tároló motorját.

**Lehetőség: az egyes tároló modulok naplózási beállításainak módosítása**

Ezt megteheti az egyes modulok **createOptions** . Például:

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

* Konfigurálja úgy a tároló motort, hogy a naplókat `systemd` [naplóba](https://docs.docker.com/config/containers/logging/journald/) küldje el az alapértelmezett naplózási illesztőprogram beállításával `journald`. 

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

A leghatékonyabb IoT Edge-környezetben fontolja meg az éles környezet integrálása a tesztelés és a CI/CD-folyamatok. Az Azure IoT Edge CI/CD több platformon, beleértve az Azure DevOps támogatja. További információ: [a folyamatos integráció és a folyamatos üzembe helyezés a Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Következő lépések

* További információ a [IoT Edge automatikus központi telepítéséről](module-deployment-monitoring.md).
* Ismerje meg, hogyan támogatja [a IoT Edge a folyamatos integrációt és a folyamatos üzembe helyezést](how-to-ci-cd.md).
