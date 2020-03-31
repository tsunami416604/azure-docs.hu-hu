---
title: Felkészülés a megoldás éles környezetben való üzembe helyezésére - Azure IoT Edge
description: Ismerje meg, hogyan viheti az Azure IoT Edge-megoldást a fejlesztésről az éles környezetre, beleértve az eszközök beállítását a megfelelő tanúsítványokkal, és a jövőbeli kódfrissítések központi telepítési tervének elkészítését.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5320c9d7f1ea5ae882c67ee631f5bbafbf97b039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530869"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Készüljön fel az IoT Edge-megoldás éles környezetben történő üzembe helyezésére

Ha készen áll arra, hogy az IoT Edge-megoldás a fejlesztésből az éles környezetbe, győződjön meg arról, hogy a folyamatos teljesítmény konfigurálva van.

Az ebben a cikkben található információk nem minden egyenlő. Az rangsorolás érdekében az egyes szakaszok olyan listákkal kezdődnek, amelyek a munkát két részre osztják: **fontos,** hogy a gyártás előtt befejeződjön, vagy **hasznos** lehet tudni.

## <a name="device-configuration"></a>Eszközök konfigurálása

Az IoT Edge-eszközök a Raspberry Pi-től a laptopon át a kiszolgálón futó virtuális gépekig bármi lehet. Lehet, hogy fizikailag vagy virtuális kapcsolaton keresztül férhet hozzá az eszközhöz, vagy hosszabb ideig elkülöníthető. Akárhogy is, győződjön meg arról, hogy megfelelően működik.Either is, you want to make sure that it's configured to work appropriately.

* **Fontos**
  * Éles tanúsítványok telepítése
  * Eszközkezelési tervvel rendelkezik
  * A Moby használata konténermotorként

* **Hasznos**
  * Felső folyási protokoll kiválasztása

### <a name="install-production-certificates"></a>Éles tanúsítványok telepítése

A termelésben minden IoT Edge-eszközhöz telepítve van egy eszközhitelesítési tanúsítvány. Ezt a hitelesítésszolgáltatói tanúsítványt ezután a config.yaml fájlban deklarálja az IoT Edge futásideje. Fejlesztési és tesztelési forgatókönyvek esetén az IoT Edge futásidejű ideiglenes tanúsítványokat hoz létre, ha a config.yaml fájlban nincs deklarált tanúsítvány. Ezek az ideiglenes tanúsítványok azonban három hónap után lejárnak, és nem biztonságosak az éles forgatókönyvek számára.

Az eszköz hitelesítésszolgáltatói tanúsítványának szerepkörét lásd: [Hogyan használja az Azure IoT Edge a tanúsítványokat.](iot-edge-certs.md)

A tanúsítványok IoT Edge-eszközön történő telepítéséről és a config.yaml fájlból való hivatkozásról az [Éles tanúsítványok telepítése IoT Edge-eszközön című](how-to-manage-device-certificates.md)témakörben talál további információt.

### <a name="have-a-device-management-plan"></a>Eszközkezelési tervvel rendelkezik

Mielőtt bármilyen eszközt éles környezetbe helyezne, tudnia kell, hogyan fogja kezelni a jövőbeli frissítéseket. IoT Edge-eszköz esetén a frissíthető összetevők listája a következőket tartalmazhatja:

* Az eszköz belső vezérlőprogramja
* Operációs rendszer tárak
* Konténermotor, mint moby
* IoT Edge démon
* CA-tanúsítványok

További információ: [Az IoT Edge futásidejének frissítése.](how-to-update-iot-edge.md) Az IoT Edge-démon frissítésének jelenlegi módszerei fizikai vagy SSH-hozzáférést igényelnek az IoT Edge-eszközhöz. Ha sok eszközt kell frissítenie, fontolja meg a frissítési lépések hozzáadását egy parancsfájlhoz, vagy használjon egy automatizálási eszközt, például az Ansible-t.

### <a name="use-moby-as-the-container-engine"></a>A Moby használata konténermotorként

A tárolómotor minden IoT Edge-eszköz előfeltétele. Csak a moby-motor támogatott a termelésben. Más tárolómotorok, például a Docker, együttműködnek az IoT Edge-el, és ezeket a motorokat nem használhatja fejlesztésre. A moby-motor az Azure IoT Edge használatával való használat esetén terjeszthető, és a Microsoft karbantartást biztosít ehhez a motorhoz.

### <a name="choose-upstream-protocol"></a>Felső folyási protokoll kiválasztása

Konfigurálhatja a protokollt (amely meghatározza a használt portot) az IoT Hub bal upstream kommunikációjához az IoT Edge-ügynök és az IoT Edge hub számára. Az alapértelmezett protokoll az AMQP, de ezt a hálózati beállítástól függően módosíthatja.

A két futásidejű modul egy **upstreamProtocol** környezeti változóval rendelkezik. A változó érvényes értékei a következők:

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Konfigurálja az UpstreamProtocol változót az IoT Edge-ügynökhöz a config.yaml fájlban az eszközön. Ha például az IoT Edge-eszköz egy proxykiszolgáló mögött van, amely blokkolja az AMQP-portokat, előfordulhat, hogy konfigurálnia kell az IoT Edge-ügynököt az AMQP websocketen keresztüli (AMQPWS) használatára az IoT Hubhoz való kezdeti kapcsolat létrehozásához.

Miután az IoT Edge-eszköz csatlakozik, győződjön meg róla, hogy továbbra is konfigurálja az UpstreamProtocol változó mindkét futásidejű modulok a későbbi központi telepítések. Erre a folyamatra példa az [IoT Edge-eszköz konfigurálása proxykiszolgálón keresztüli kommunikációra](how-to-configure-proxy-support.md)című részben található.

## <a name="deployment"></a>Környezet

* **Hasznos**
  * Konzisztensnek kell lennie az upstream protokollal
  * Állomástárolás beállítása rendszermodulokhoz
  * Az IoT Edge hub által használt memóriaterület csökkentése
  * Ne használjon hibakeresési verziókat a modulképekből

### <a name="be-consistent-with-upstream-protocol"></a>Konzisztensnek kell lennie az upstream protokollal

Ha úgy konfigurálta az IoT Edge-ügynököt az IoT Edge-eszközön, hogy az alapértelmezett AMQP-től eltérő protokollt használjon, akkor minden jövőbeli telepítésben ugyanazt a protokollt kell deklarálnia. Ha például az IoT Edge-eszköz egy proxykiszolgáló mögött van, amely blokkolja az AMQP-portokat, valószínűleg úgy konfigurálta az eszközt, hogy a WebSocketen (AMQPWS) keresztül csatlakozzon az AMQP-n keresztül. Amikor modulokat telepít az eszközre, konfigurálja ugyanazt az AMQPWS protokollt az IoT Edge-ügynökhöz és az IoT Edge hubhoz, különben az alapértelmezett AMQP felülbírálja a beállításokat, és megakadályozza, hogy újra csatlakozzon.

Csak az UpstreamProtocol környezeti változót kell konfigurálnia az IoT Edge-ügynökhöz és az IoT Edge hub-modulokhoz. Minden további modul bármilyen protokollt alkalmaz, amely a futásidejű modulokban van beállítva.

Erre a folyamatra példa az [IoT Edge-eszköz konfigurálása proxykiszolgálón keresztüli kommunikációra](how-to-configure-proxy-support.md)című részben található.

### <a name="set-up-host-storage-for-system-modules"></a>Állomástárolás beállítása rendszermodulokhoz

Az IoT Edge-központ és az ügynökmodulok helyi tárolót használnak az állapot fenntartásához és a modulok, eszközök és a felhő közötti üzenetküldés engedélyezéséhez. A nagyobb megbízhatóság és teljesítmény érdekében konfigurálja úgy a rendszermodulokat, hogy a gazdafájlrendszeren lévő tárolót használják.

További információ: [Host storage for system modules](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Az IoT Edge hub által használt memóriaterület csökkentése

Ha korlátozott eszközök telepítését korlátozott memória áll rendelkezésre, beállíthatja az IoT Edge hub fut egy egyszerűbb kapacitással, és kevesebb lemezterületet. Ezek a konfigurációk azonban korlátozzák az IoT Edge hub teljesítményét, ezért keresse meg a megoldáshoz megfelelő egyensúlyt.

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Ne optimalizálja a teljesítményt a korlátozott eszközökön

Az IoT Edge hub alapértelmezés szerint a teljesítményre van optimalizálva, így nagy memóriadarabokat próbál lefoglalni. Ez a konfiguráció stabilitási problémákat okozhat a kisebb eszközökön, például a Raspberry Pi-n. Ha korlátozott erőforrásokkal rendelkező eszközöket telepít, érdemes lehet az **OptimizeForPerformance** környezeti változót **hamisra** állítani az IoT Edge hubon.

Ha az **OptimizeForPerformance** **értéke igaz,** az MQTT protokollfej a PooledByteBufferAllocator-t használja, amely jobb teljesítményt nyújt, de több memóriát foglal le. A allokátor nem működik megfelelően 32 bites operációs rendszereken vagy kevés memóriával rendelkező eszközökön. Emellett a teljesítményre optimalizált RocksDb több memóriát foglal le a helyi tárolószolgáltatóként betöltött szerepére.

További információ: [Stabilitási problémák az erőforrás-korlátozott eszközökön.](troubleshoot.md#stability-issues-on-resource-constrained-devices)

#### <a name="disable-unused-protocols"></a>Nem használt protokollok letiltása

Az IoT Edge hub teljesítményének optimalizálása és a memóriahasználat csökkentése egy másik módja, ha kikapcsolja a protokollfejeket a megoldásban nem használt protokollok hoz.

A protokollfejek logikai környezeti változók beállításával vannak konfigurálva az IoT Edge hub modulhoz a központi telepítési jegyzékekben. A három változó a következő:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Mindhárom változó *két aláhúzásjel,* és lehet állítani, hogy igaz vagy hamis.

#### <a name="reduce-storage-time-for-messages"></a>Az üzenetek tárolási idejének csökkentése

Az IoT Edge hub modul ideiglenesen tárolja az üzeneteket, ha azok bármilyen okból nem kézbesíthetők az IoT Hubba. Beállíthatja, hogy az IoT Edge hub mennyi ideig tart a kézbesítetlen üzenetek, mielőtt hagyja, hogy lejár. Ha memóriaproblémák vannak az eszközön, csökkentheti az IoT Edge hub ikermodul **timeToLiveSecs** értékét.

A timeToLiveSecs paraméter alapértelmezett értéke 7200 másodperc, ami két óra.

### <a name="do-not-use-debug-versions-of-module-images"></a>Ne használjon hibakeresési verziókat a modulképekből

Ha a tesztforgatókönyvekről éles forgatókönyvekre lép át, ne felejtse el eltávolítani a hibakeresési konfigurációkat a központi telepítési jegyzékekből. Ellenőrizze, hogy a központi telepítési jegyzékekben lévő modullemezképek egyike sem ** \.rendelkezik-e hibakeresési** utótaggal. Ha létrehozási beállításokat adott hozzá a modulok ban lévő portok hibakereséshez való felfedéséhez, távolítsa el ezeket a létrehozási beállításokat is.

## <a name="container-management"></a>Tárolók kezelése

* **Fontos**
  * Hozzáférés kezelése a tároló beállításjegyzékéhez
  * Címkék használata a verziók kezeléséhez

### <a name="manage-access-to-your-container-registry"></a>Hozzáférés kezelése a tároló beállításjegyzékéhez

Mielőtt üzembe helyezné a modulokat az éles IoT Edge-eszközökhöz, győződjön meg arról, hogy szabályozza a tároló beállításjegyzékéhez való hozzáférést, hogy a kívülállók ne férhessenek hozzá a tárolórendszerképekhez, és ne módosíthassák azokat. Használjon privát, nem nyilvános tárolóbeállításjegyzéket a tárolórendszerképek kezeléséhez.

Az oktatóanyagokban és egyéb dokumentációkban azt utasítjuk, hogy ugyanazokat a tároló-beállításjegyzék-hitelesítő adatokat használja az IoT Edge-eszközön, mint a fejlesztői gépen. Ezek az utasítások csak a tesztelési és fejlesztési környezetek könnyebb beállításának elősegítésére szolgálnak, és nem követhetők éles környezetben. Az Azure Container Registry azt [javasolja, hogy a szolgáltatásnévi tagok,](../container-registry/container-registry-auth-service-principal.md) amikor az alkalmazások vagy szolgáltatások lekérése tárolórendszerképek automatizált vagy más módon felügyelet nélkül, mint az IoT Edge-eszközök nem. Hozzon létre egy egyszerű szolgáltatás, amely írásvédett hozzáférést biztosít a tároló beállításjegyzékéhez, és adja meg ezt a felhasználónevet és jelszót a központi telepítési jegyzékben.

### <a name="use-tags-to-manage-versions"></a>Címkék használata a verziók kezeléséhez

A címke egy docker-koncepció, amely segítségével különbséget tehet a docker-tárolók verziói között. A címkék olyan utótagok, mint az **1.0,** amelyek egy tárolótárház végén vannak. Például **mcr.microsoft.com/azureiotedge-agent:1.0**. A címkék változékonyak, és bármikor módosíthatók, hogy egy másik tárolóra mutassanak, ezért a csapatnak meg kell állapodnia egy olyan egyezményben, amelyet követnie kell, amikor frissíti a modulképeket.

A címkék segítenek az IoT Edge-eszközök frissítéseinek kényszerítéséhez is. Amikor leadja egy modul frissített verzióját a tároló beállításjegyzékébe, a címke növekménye. Ezután leküldéses egy új központi telepítés az eszközök a címke növekszik. A tárolómotor új verzióként ismeri fel a növekménycímkét, és lehúzza a legújabb modulverziót a készülékre.

Egy címkekonvenció példáját az [IoT Edge futási ideje frissítése](how-to-update-iot-edge.md#understand-iot-edge-tags) című témakörben olvashatja, hogy az IoT Edge hogyan használja a gördülő címkéket és az adott címkéket a verziók nyomon követéséhez.

## <a name="networking"></a>Hálózat

* **Hasznos**
  * Kimenő/bejövő konfiguráció áttekintése
  * Kapcsolatok engedélyezése IoT Edge-eszközökről
  * Kommunikáció konfigurálása proxyn keresztül

### <a name="review-outboundinbound-configuration"></a>Kimenő/bejövő konfiguráció áttekintése

Az Azure IoT Hub és az IoT Edge közötti kommunikációs csatornák mindig kimenőre vannak konfigurálva. A legtöbb IoT Edge-forgatókönyvek csak három kapcsolat szükséges. A tárolómotornak csatlakoznia kell a modullemezképeket tartalmazó tárolóbeállításjegyzékhez (vagy jegyzékhez). Az IoT Edge futásidejű kell csatlakoznia az IoT Hub az eszköz konfigurációs adatainak lekéréséhez, valamint üzenetek és telemetriai adatok küldéséhez. És ha automatikus kiépítést használ, az IoT Edge démonnak csatlakoznia kell az eszközkiépítési szolgáltatáshoz. További információt a [Tűzfal- és portkonfigurációs szabályok](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment)című témakörben talál.

### <a name="allow-connections-from-iot-edge-devices"></a>Kapcsolatok engedélyezése IoT Edge-eszközökről

Ha a hálózati beállítás megköveteli, hogy kifejezetten engedélyezze az IoT Edge-eszközökről származó kapcsolatokat, tekintse át az IoT Edge-összetevők alábbi listáját:

* **Az IoT Edge-ügynök** egy állandó AMQP/MQTT-kapcsolatot nyit meg az IoT Hubhoz, esetleg websocketeken keresztül.
* **Az IoT Edge hub** egyetlen állandó AMQP-kapcsolatot vagy több MQTT-kapcsolatot nyit meg az IoT Hubhoz, esetleg websocketeken keresztül.
* **Az IoT Edge démon** időszakos HTTPS-hívásokat kezdeményez az IoT Hubhoz.

Mindhárom esetben a DNS-név megegyezik \*a .azure-devices.net mintával.

Emellett a **Container motor** https-en keresztül hívásokat kezdeményez a tároló-jegyzékek számára. Az IoT Edge futásidejű tárolórendszerképek beolvasásához a DNS-név mcr.microsoft.com. A tárolómotor a központi telepítésben konfigurált módon csatlakozik más jegyzékekhez.

Ez az ellenőrzőlista a tűzfalszabályok kiindulópontja:

   | URL\* ( = helyettesítő karakter) | Kimenő TCP-portok | Használat |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft tárolóbeállítás-nyilvántartás |
   | global.azure-devices-provisioning.net  | 443 | DPS-hozzáférés (nem kötelező) |
   | \*azurecr.io | 443 | Személyes és külső tárolónyilvántartások |
   | \*.blob.core.windows.net | 443 | Az Azure Container Registry rendszerképének különbözetei letöltése a blobstorage-ból |
   | \*azure-devices.net | 5671, 8883, 443 | IoT Hub-hozzáférés |
   | \*docker.io.  | 443 | Docker Hub-hozzáférés (nem kötelező) |

Ezek a tűzfalszabályok az Azure Container Registry örökölt. További információ: [Szabályok konfigurálása az Azure-tároló beállításjegyzékének tűzfal mögötti eléréséhez](../container-registry/container-registry-firewall-access-rules.md)című témakörben.

### <a name="configure-communication-through-a-proxy"></a>Kommunikáció konfigurálása proxyn keresztül

Ha az eszközök egy proxykiszolgálót használó hálózaton lesznek telepítve, képesnek kell lenniük a proxyn keresztüli kommunikációra az IoT Hub és a tárolórendszerjegyzékek eléréséhez. További információ: [Az IoT Edge-eszköz konfigurálása proxykiszolgálón keresztüli kommunikációhoz](how-to-configure-proxy-support.md)című témakörben talál.

## <a name="solution-management"></a>Megoldáskezelés

* **Hasznos**
  * Naplók és diagnosztika beállítása
  * Fontolja meg a teszteket és a CI/CD-folyamatokat

### <a name="set-up-logs-and-diagnostics"></a>Naplók és diagnosztika beállítása

Linuxon az IoT Edge démon a naplókat használja alapértelmezett naplózási illesztőprogramként. A parancssori eszközzel `journalctl` lekérdezheti a démonnaplókat. Windows rendszerben az IoT Edge démon PowerShell-diagnosztikát használ. Naplók `Get-IoTEdgeLog` lekérdezésére használható a démonból. Az IoT Edge-modulok a JSON-illesztőprogramot használják a naplózáshoz, amely az alapértelmezett.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

IoT Edge-telepítés tesztelése során általában hozzáférhet az eszközökhöz a naplók lekéréséhez és a hibaelhárításhoz. Egy központi telepítési forgatókönyv, előfordulhat, hogy nem rendelkezik ezzel a lehetőséggel. Gondolja át, hogyan gyűjti össze az éles környezetben lévő eszközeivel kapcsolatos információkat. Az egyik lehetőség egy naplózási modul használata, amely adatokat gyűjt a többi modulból, és elküldi azokat a felhőbe. Egy példa a naplózási modul [logspout-loganalytics,](https://github.com/veyalla/logspout-loganalytics)vagy tervezhet saját.

### <a name="place-limits-on-log-size"></a>Korlátozások a napló méretére

Alapértelmezés szerint a Moby tárolómotor nem állítja be a tárolónapló méretkorlátait. Idővel ez azt eredményezheti, hogy az eszköz megtelik a naplókkal, és elfogy a lemezterület. Ennek megakadályozása érdekében vegye figyelembe az alábbi lehetőségeket:

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>Beállítás: Az összes tárolómodulra vonatkozó globális korlátok beállítása

Korlátozhatja az összes tárolónaplófájl méretét a tárolómotor naplóbeállításaiban. A következő példa a `json-file` napló-illesztőprogramot (ajánlott) állítja be a fájlok méretének és számának korlátozásával:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Adja hozzá (vagy fűzzhozzá) `daemon.json` ezekhez az adatokhoz egy nevű fájlhoz, és helyezze el az eszköz platformjának megfelelő helyen.

| Platform | Hely |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

A változások életbe léptetéséhez a tartálymotort újra kell indítani.

#### <a name="option-adjust-log-settings-for-each-container-module"></a>Lehetőség: Az egyes tárolómodulok naplóbeállításainak módosítása

Ezt megteheti az egyes modulok **createOptions** elemében. Példa:

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

#### <a name="additional-options-on-linux-systems"></a>További lehetőségek Linux rendszereken

* Állítsa be úgy a tárolómotort, hogy naplókat küldjön a `systemd` [naplóba](https://docs.docker.com/config/containers/logging/journald/) alapértelmezett naplózási illesztőprogramként beállítással. `journald`

* A logrotate eszköz telepítésével rendszeresen távolítsa el a régi naplókat az eszközről. Használja a következő fájlspecifikációt:

   ```txt
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

### <a name="consider-tests-and-cicd-pipelines"></a>Fontolja meg a teszteket és a CI/CD-folyamatokat

A leghatékonyabb IoT Edge-telepítési forgatókönyv érdekében fontolja meg az éles környezet integrálását a tesztelési és CI/CD-folyamatokba. Az Azure IoT Edge több CI/CD-platformot támogat, beleértve az Azure DevOps-t is. További információ: [Folyamatos integráció és folyamatos üzembe helyezés az Azure IoT Edge-ben.](how-to-ci-cd.md)

## <a name="next-steps"></a>További lépések

* További információ az [IoT Edge automatikus központi telepítéséről.](module-deployment-monitoring.md)
* Tekintse meg, hogyan támogatja az IoT Edge a [folyamatos integrációt és a folyamatos üzembe helyezést.](how-to-ci-cd.md)
