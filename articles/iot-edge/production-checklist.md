---
title: Eszközök és üzemelő példányok előkészítése a termeléshez – Azure IoT Edge | Microsoft Docs
description: Megtudhatja, hogyan hozhatja létre a Azure IoT Edge-megoldását a fejlesztéstől a Termelésig, beleértve az eszközök beállítását a megfelelő tanúsítványokkal, valamint a jövőbeli programkód-frissítések üzembe helyezési tervének kidolgozását.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 610e0088fe97bdda1dce7f7391530c5128428b29
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73096964"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Felkészülés a IoT Edge-megoldás éles környezetben történő üzembe helyezésére

Ha készen áll arra, hogy az IoT Edge-megoldást a fejlesztésből éles környezetbe hozza, győződjön meg arról, hogy a folyamatban lévő teljesítményre van konfigurálva.

A cikkben szereplő információk nem egyenlőek. A rangsorolás elősegítése érdekében az egyes szakaszok a következő két szakaszra oszthatják meg a munkát: **fontos** , hogy elvégezze az éles környezetbe való bevezetést, vagy a **hasznos** tudnivalókat.

## <a name="device-configuration"></a>Eszköz konfigurációja

IoT Edge az eszközök a málna PI-ből egy laptopra vagy egy kiszolgálón futó virtuális gépre is felhasználhatók. Lehet, hogy fizikailag vagy virtuális kapcsolaton keresztül fér hozzá az eszközhöz, vagy hosszabb ideig el lehet különíteni. Mindkét esetben meg kell győződnie arról, hogy a megfelelő működésre van konfigurálva. 

* **Fontos**
    * Éles tanúsítványok telepítése
    * Eszközkezelés
    * A Moby használata tároló motorként

* **Hasznos**
    * Felsőbb rétegbeli protokoll kiválasztása

### <a name="install-production-certificates"></a>Éles tanúsítványok telepítése

Minden üzemi IoT Edge eszközön telepítve kell lennie egy, az eszközre telepített HITELESÍTÉSSZOLGÁLTATÓI tanúsítványnak. Ezt követően a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány deklarálva lesz a config. YAML fájlban lévő IoT Edge futtatókörnyezetnek. A fejlesztés és a tesztelés egyszerűbbé tétele érdekében az IoT Edge Runtime ideiglenes tanúsítványokat hoz létre, ha nincsenek deklarálva tanúsítványok a config. YAML fájlban. Ezek az ideiglenes tanúsítványok azonban három hónap elteltével lejárnak, és nem biztonságosak az éles környezetekben. 

Az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa szerepkörének megismeréséhez tekintse meg a [hogyan használja a Azure IoT Edge a tanúsítványokat](iot-edge-certs.md).

Ha további információt szeretne arról, hogyan telepíthet tanúsítványokat egy IoT Edge eszközre, és hogyan hivatkozhat rájuk a config. YAML fájlból, tekintse [meg az IoT Edge eszköz konfigurálása transzparens átjáróként való](how-to-create-transparent-gateway.md)működésre című témakört. A tanúsítványok konfigurálásának lépései megegyeznek attól, hogy az eszköz átjáróként fog-e használni. Ez a cikk parancsfájlokat biztosít a csak tesztelési célú tanúsítványok létrehozásához. Ezeket a tanúsítványokat ne használja éles környezetben. 

### <a name="have-a-device-management-plan"></a>Eszközkezelés

Mielőtt üzembe helyezi az eszközt az éles környezetben, tudnia kell, hogyan fogja kezelni a jövőbeli frissítéseket. Egy IoT Edge eszköz esetében a frissítendő összetevők listája a következőket tartalmazhatja:

* Eszköz belső vezérlőprogramja
* Operációs rendszer kódtárai
* Tároló motor, például Moby
* IoT Edge démon
* HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok

További információ: [a IoT Edge futtatókörnyezet frissítése](how-to-update-iot-edge.md). A IoT Edge démon frissítésének jelenlegi módszerei fizikai vagy SSH-hozzáférést igényelnek a IoT Edge eszközhöz. Ha sok eszközt szeretne frissíteni, vegye fel a frissítési lépéseket egy parancsfájlba, vagy használjon olyan Automation-eszközt, mint például a Ansible.

### <a name="use-moby-as-the-container-engine"></a>A Moby használata tároló motorként

A tároló-motor minden IoT Edge eszköz előfeltétele. Csak a Moby Engine támogatott az éles környezetben. Más tároló-hajtóművek, például a Docker, a IoT Edge használatával működnek, és az ilyen motorokat a fejlesztéshez használják. A Moby Engine a Azure IoT Edge használatával terjeszthető újra, és a Microsoft karbantartást biztosít ehhez a motorhoz.

### <a name="choose-upstream-protocol"></a>Felsőbb rétegbeli protokoll kiválasztása

A IoT Hubhoz való upstream kommunikációhoz használt protokoll (és így a port is) konfigurálható mind a IoT Edge-ügynök, mind a IoT Edge hub számára. Az alapértelmezett protokoll a AMQP, de előfordulhat, hogy a hálózat beállításától függően módosítani kívánja a beállításokat. 

A két futásidejű modul **UpstreamProtocol** környezeti változóval rendelkezik. A változó érvényes értékei a következők: 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Konfigurálja a UpstreamProtocol változót a IoT Edge ügynökhöz a config. YAML fájlban az eszközön. Ha például a IoT Edge eszköz egy olyan proxykiszolgáló mögött található, amely blokkolja a AMQP-portokat, előfordulhat, hogy a IoT Edge-ügynököt úgy kell konfigurálnia, hogy az AMQP-t használja a WebSocket (AMQPWS) használatával a kezdeti kapcsolat létrehozásához a IoT Hub. 

Ha a IoT Edge-eszköz csatlakozik, mindenképpen folytassa a UpstreamProtocol változó konfigurálását mindkét futtatókörnyezeti modulban a jövőbeli telepítések során. Ennek a folyamatnak egy példája a [IoT Edge eszköz konfigurálása egy proxykiszolgálón keresztül történő kommunikációra](how-to-configure-proxy-support.md)című cikkben található.

## <a name="deployment"></a>Üzembe helyezés

* **Hasznos**
    * Konzisztensnek kell lennie a felsőbb rétegbeli protokollal
    * Gazdagép-tároló beállítása rendszermodulokhoz
    * Csökkentse a IoT Edge hub által használt memória méretét
    * A modul rendszerképeinek hibakeresési verzióját ne használja

### <a name="be-consistent-with-upstream-protocol"></a>Konzisztensnek kell lennie a felsőbb rétegbeli protokollal

Ha úgy konfigurálta a IoT Edge ügynököt a IoT Edge eszközön, hogy az alapértelmezett AMQP eltérő protokollt használjon, akkor minden jövőbeli telepítésnél ugyanazt a protokollt kell deklarálnia. Ha például az IoT Edge-eszköz egy olyan proxykiszolgáló mögött található, amely blokkolja a AMQP-portokat, akkor valószínűleg úgy konfigurálta az eszközt, hogy a AMQP-en keresztül kapcsolódjon a WebSocket (AMQPWS) használatával. Amikor modulokat telepít az eszközre, konfigurálja ugyanazt a AMQPWS protokollt a IoT Edge-ügynökhöz és a IoT Edge hub-hoz, különben az alapértelmezett AMQP felülbírálja a beállításokat, és megakadályozza a csatlakozást. 

A UpstreamProtocol környezeti változót csak a IoT Edge ügynökhöz és IoT Edge hub-modulokhoz kell konfigurálnia. Bármely további modul, amely bármilyen protokollt tartalmaz, a futásidejű modulokban van beállítva. 

Ennek a folyamatnak egy példája a [IoT Edge eszköz konfigurálása egy proxykiszolgálón keresztül történő kommunikációra](how-to-configure-proxy-support.md)című cikkben található.

### <a name="set-up-host-storage-for-system-modules"></a>Gazdagép-tároló beállítása rendszermodulokhoz

Az IoT Edge hub és az Agent modulok a helyi tárterületet használják az állapot fenntartásához és a modulok, eszközök és a felhő közötti üzenetkezelés engedélyezéséhez. A jobb megbízhatóság és teljesítmény érdekében konfigurálja a rendszermodulokat a gazdagép fájlrendszerén a tároló használatára.

További információ: [Host Storage for System modulok](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>IoT Edge hub által használt memória méretének csökkentése

Ha korlátozott számú memóriával rendelkező korlátozott méretű eszközöket helyez üzembe, az IoT Edge hub-t úgy konfigurálhatja, hogy egyszerűbben fusson, és kevesebb lemezterületet használjon. Ezek a konfigurációk korlátozzák az IoT Edge hub teljesítményét, ezért érdemes megkeresni a megoldáshoz használható megfelelő egyensúlyt. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>A korlátozott eszközök teljesítményének optimalizálása

Az IoT Edge hub alapértelmezés szerint a teljesítményre van optimalizálva, így nagy mennyiségű memóriát próbál lefoglalni. Ez a konfiguráció stabilitási problémákat okozhat a kisebb eszközökön, például a málna PI-ben. Ha korlátozott erőforrásokkal rendelkező eszközöket helyez üzembe, érdemes lehet **hamis** értékre állítani a **OptimizeForPerformance** környezeti változót az IoT Edge központban. 

Ha a **OptimizeForPerformance** értéke **true (igaz**), a MQTT-protokoll feje a PooledByteBufferAllocator-t használja, amely jobb teljesítményt nyújt, de több memóriát foglal le. A lefoglaló nem működik megfelelően a 32 bites operációs rendszereken, illetve az alacsony memóriával rendelkező eszközökön. Emellett, ha teljesítményre optimalizált, a RocksDb több memóriát foglal le a szerepköréhez, mint a helyi tároló szolgáltatója. 

További információ: [stabilitási problémák az erőforrás által korlátozott eszközökön](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Nem használt protokollok letiltása

Az IoT Edge hub teljesítményének optimalizálása és a memóriahasználat csökkentése is lehetővé teszi, hogy kikapcsolja a protokollok fejeit a megoldásában nem használt protokollok esetében. 

A protokollok a központi telepítési jegyzékekben a IoT Edge hub modul logikai környezeti változóinak beállításával konfigurálhatók. A három változó a következők:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Mindhárom változó *két aláhúzással* rendelkezik, és beállítható igaz vagy hamis értékre. 

#### <a name="reduce-storage-time-for-messages"></a>Az üzenetek tárolási idejének csökkentése

Az IoT Edge hub-modul átmenetileg tárolja az üzeneteket, ha bármilyen okból nem lehet IoT Hub továbbítani őket. Azt is beállíthatja, hogy az IoT Edge hub mennyi ideig tartson a nem leküldött üzeneteknek a lejárat előtt. Ha a memóriával kapcsolatos problémái vannak az eszközön, csökkentheti a **timeToLiveSecs** értéket az IoT Edge hub-modul Twin-ben. 

A timeToLiveSecs paraméter alapértelmezett értéke 7200 másodperc, amely két óra. 

### <a name="do-not-use-debug-versions-of-module-images"></a>A modul rendszerképeinek hibakeresési verzióját ne használja

Ha tesztelési forgatókönyveket éles környezetbe helyez át, ne felejtse el eltávolítani az üzembe helyezési jegyzékből a hibakeresési konfigurációkat. Győződjön meg arról, hogy az üzembe helyezési jegyzékekben egyik modul rendszerképe sem rendelkezik a **\.debug** utótaggal. Ha hozzáadta a létrehozási beállításokat a modulok számára a hibakereséshez, távolítsa el ezeket a létrehozási beállításokat is. 

## <a name="container-management"></a>Tárolófelügyelet

* **Fontos**
    * A tároló-beállításjegyzékhez való hozzáférés kezelése
    * Címkék használata a verziók kezeléséhez

### <a name="manage-access-to-your-container-registry"></a>A tároló-beállításjegyzékhez való hozzáférés kezelése

Mielőtt üzembe helyezi a modulokat a termelési IoT Edge eszközökön, győződjön meg arról, hogy a tároló-beállításjegyzékhez való hozzáférést felügyeli, hogy a kívülállók ne férhessenek hozzá a tároló-lemezképekhez, illetve ne legyenek A tároló-lemezképek kezeléséhez használjon privát, nem nyilvános, tároló-beállításjegyzéket. 

Az oktatóanyagokban és más dokumentációban arra utasítja Önt, hogy ugyanazt a tároló beállításjegyzékbeli hitelesítő adatokat használja a IoT Edge eszközön a fejlesztői gépen való használat során. Ezek az utasítások csak a tesztelési és fejlesztési környezetek egyszerűbb beállítására szolgálnak, és nem kell éles környezetben követniük. Azure Container Registry javasolja [az egyszerű szolgáltatásokkal történő hitelesítést](../container-registry/container-registry-auth-service-principal.md) , amikor az alkalmazások vagy szolgáltatások egy automatizált vagy más módon felügyelet nélkül lekérik a tárolók lemezképeit, mivel IoT Edge eszközök. Hozzon létre egy egyszerű szolgáltatást, amely csak olvasási hozzáféréssel rendelkezik a tároló beállításjegyzékéhez, és adja meg a felhasználónevet és a jelszót az üzembe helyezési jegyzékben.

### <a name="use-tags-to-manage-versions"></a>Címkék használata a verziók kezeléséhez

A címke egy Docker-koncepció, amellyel megkülönböztethető a Docker-tárolók verziói között. A címkék olyan utótagok, mint a **1,0** , amelyek a tárolók tárházának végére mutatnak. Például: **MCR.microsoft.com/azureiotedge-Agent:1.0**. A címkék változhatnak, és bármikor módosíthatók úgy, hogy egy másik tárolóra mutassanak, így a csapatnak meg kell egyeznie egy, a modul rendszerképeinek továbbításakor követendő konvencióval. 

A címkék a IoT Edge-eszközök frissítéseinek betartatásához is segítséget nyújtanak. Ha egy modul frissített verzióját leküldi a tároló-beállításjegyzékbe, növelje a címkét. Ezután leküldheti az eszközökre egy új központi telepítést a címke növelésével. A tároló motor felismeri a megnövelt címkét új verzióként, és lekéri a legújabb modul verziószámát az eszközre. 

A címkézési konvencióra példát a [IoT Edge futtatókörnyezet frissítése](how-to-update-iot-edge.md#understand-iot-edge-tags) című témakörben talál, amelyből megtudhatja, hogyan használja a IoT Edge a működés közbeni címkéket és adott címkéket a verziók nyomon követésére. 

## <a name="networking"></a>Hálózatkezelés

* **Hasznos**
    * Kimenő/bejövő konfiguráció áttekintése
    * IoT Edge eszközök kapcsolatainak engedélyezése
    * Kommunikáció konfigurálása proxyn keresztül

### <a name="review-outboundinbound-configuration"></a>Kimenő/bejövő konfiguráció áttekintése

Az Azure IoT Hub és IoT Edge közötti kommunikációs csatornák mindig kimenő értékre vannak konfigurálva. A legtöbb IoT Edge forgatókönyv esetén csak három kapcsolat szükséges. A tároló-motornak kapcsolódnia kell a modul lemezképeit tároló beállításjegyzékbeli (vagy kibocsátásiegység-nyilvántartási) adatbázishoz. Az IoT Edge futtatókörnyezetnek csatlakoznia kell a IoT Hubhoz az eszköz konfigurációs adatainak lekéréséhez, valamint üzenetek és telemetria küldéséhez. Ha az automatikus kiépítés szolgáltatást használja, a IoT Edge démonnak csatlakoznia kell az eszköz kiépítési szolgáltatásához. További információ: a [tűzfal és a port konfigurációs szabályai](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="allow-connections-from-iot-edge-devices"></a>IoT Edge eszközök kapcsolatainak engedélyezése

Ha a hálózatkezelés beállítása megköveteli, hogy explicit módon engedélyezze a IoT Edge eszközökről érkező kapcsolatokat, tekintse át az alábbi IoT Edge összetevők listáját:

* **IoT Edge-ügynök** állandó AMQP/MQTT-kapcsolaton keresztül nyitja meg a IoT hub, valószínűleg a WebSockets szolgáltatásban. 
* **IoT Edge hub** egyetlen állandó AMQP-kapcsolatot nyit meg, vagy több MQTT-kapcsolatot hoz IoT hub, valószínűleg a websocketek felett. 
* **IoT Edge démon** időszakos HTTPS-hívásokat végez IoT hub. 

A DNS-név mindhárom esetben a következő mintának felel meg: \*.azure-devices.net. 

Emellett a **Container Engine** a tároló-BEÁLLÍTÁSJEGYZÉKek HTTPS-kapcsolaton keresztüli hívását is lehetővé teszi. A IoT Edge Runtime-tároló lemezképének lekéréséhez a DNS-név mcr.microsoft.com. A tároló motor az üzemelő példányban konfigurált többi beállításjegyzékhez csatlakozik. 

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

### <a name="configure-communication-through-a-proxy"></a>Kommunikáció konfigurálása proxyn keresztül

Ha az eszközök egy proxykiszolgálót használó hálózaton lesznek telepítve, akkor képesnek kell lenniük arra, hogy a proxyn keresztül kommunikáljanak a IoT Hub és a tárolói kibocsátásiegység-forgalmi jegyzékek eléréséhez. További információ: [IoT Edge eszköz konfigurálása egy proxykiszolgálón keresztüli kommunikációhoz](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Megoldás-kezelés

* **Hasznos**
    * Naplók és diagnosztika beállítása
    * Tesztek és CI/CD-folyamatok megfontolása

### <a name="set-up-logs-and-diagnostics"></a>Naplók és diagnosztika beállítása

Linux rendszeren a IoT Edge démon az alapértelmezett naplózási illesztőprogramként használja a naplókat. A démon-naplók lekérdezéséhez használhatja a `journalctl` parancssori eszközt. Windows rendszeren a IoT Edge démon PowerShell-diagnosztikát használ. `Get-IoTEdgeLog` használatával kérdezheti le a naplókat a démonból. IoT Edge modulok a JSON-illesztőprogramot használják a naplózáshoz, amely az alapértelmezett.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

IoT Edge központi telepítés tesztelésekor általában hozzáférhet az eszközökhöz a naplók és a hibák lekérdezéséhez. Egy üzembe helyezési forgatókönyvben előfordulhat, hogy nem rendelkezik ezzel a lehetőséggel. Gondolja át, hogyan fog adatokat gyűjteni az eszközökről éles környezetben. Az egyik lehetőség egy olyan naplózási modul használata, amely adatokat gyűjt a többi modulból, és elküldi azt a felhőbe. A naplózási modul egy példája a [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics), vagy megtervezheti a sajátját is. 

### <a name="place-limits-on-log-size"></a>Korlátok elhelyezése a naplók méretében

Alapértelmezés szerint a Moby Container Engine nem állítja be a tároló naplójának méretére vonatkozó korlátozásokat. Az idő múlásával az eszköz betöltődik a naplókba, és elfogyott a szabad lemezterület. Ennek elkerüléséhez vegye figyelembe a következő lehetőségeket:

**Lehetőség: az összes tároló modulra érvényes globális korlátok megadása**

Az összes tároló-naplófájl méretét korlátozhatja a tároló-motor naplójának beállításaiban. A következő példa a `json-file` (ajánlott) értékre állítja be a napló illesztőprogramját a méret és a fájlok számának korlátozásával:

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

| Platform | Földrajzi egység |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

A módosítások életbe léptetéséhez újra kell indítani a tároló motorját.

**Lehetőség: az egyes tároló modulok naplózási beállításainak módosítása**

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

* Konfigurálja úgy a tároló motort, hogy a naplókat a `systemd` [naplóba](https://docs.docker.com/config/containers/logging/journald/) küldje el az alapértelmezett naplózási illesztőprogram `journald` beállításával. 

* A logrotate eszköz telepítésével rendszeresen távolítsa el a régi naplókat az eszközről. Használja a következő fájl-specifikációt: 

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

### <a name="consider-tests-and-cicd-pipelines"></a>Tesztek és CI/CD-folyamatok megfontolása

A leghatékonyabb IoT Edge üzembe helyezési forgatókönyv esetén érdemes lehet az éles üzembe helyezést a tesztelési és CI/CD-folyamatokba integrálni. Azure IoT Edge több CI/CD platformot támogat, beleértve az Azure DevOps is. További információ: [a folyamatos integráció és a folyamatos üzembe helyezés a Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Következő lépések

* További információ a [IoT Edge automatikus központi telepítéséről](module-deployment-monitoring.md).
* Ismerje meg, hogyan támogatja [a IoT Edge a folyamatos integrációt és a folyamatos üzembe helyezést](how-to-ci-cd.md).
