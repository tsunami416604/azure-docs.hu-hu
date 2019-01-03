---
title: Eszközök és az éles környezetben – Azure IoT Edge üzemelő példányok előkészítése |} A Microsoft Docs
description: Útmutató az Azure IoT Edge megoldás a fejlesztéstől az éles környezetben, beleértve az eszközök beállítása a megfelelő tanúsítványokat és a egy jövőbeli kódfrissítéseket telepítési tervének érvénybe.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 86b33bfa0f5383ac68080e2f8f7f9a004a1364a0
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2018
ms.locfileid: "53652614"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Az üzembe helyezés éles környezetben az IoT Edge-megoldás előkészítése

Ha készen áll az IoT Edge-megoldás a fejlesztéstől az éles környezetben elvégzendő, győződjön meg arról, hogy van-e konfigurálva a folyamatban lévő teljesítmény.

Ebben a cikkben szereplő információk nem minden egyenlő. Könnyebben rangsorolja, minden szakasz eloszthatja a munkahelyi felosztása két szakasszá listák kezdődik: **fontos** befejezését, mielőtt éles, vagy **hasznos** , hogy ismeri.

## <a name="device-configuration"></a>Eszközök konfigurálása

IoT Edge-eszközökön a hordozható egy kiszolgálón futó virtuális géphez a Raspberry Pi bármi lehet. Előfordulhat, hogy rendelkezik az eszköz fizikailag vagy virtuális-kapcsolaton keresztül, vagy lehet elkülönített huzamosabb ideig. Győződjön meg arról, hogy van-e konfigurálva, ennek megfelelően szeretné mindkét módszer esetén. 

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

Az IoT Edge-démon frissíteni lépéseiért lásd: [az IoT Edge-futtatókörnyezet frissítése](how-to-update-iot-edge.md). Az aktuális frissítési az IoT Edge-démon módszerhez fizikai vagy SSH-hozzáférést az IoT Edge-eszközön. Ha sok eszköz frissítéséhez, fontolja meg a frissítési lépéseket egy szkript, vagy hajtsa végre, méretezhető frissítéseknek egy automation-eszköz, például az Ansible használatával.

### <a name="use-moby-as-the-container-engine"></a>A tároló motorként Moby használata

Egy tároló-motor az eszközön, akkor minden IoT Edge-eszköz előfeltétel. Éles környezetben csak moby-motor használata támogatott. Egyéb tároló-motorok, például a Dockert, az IoT Edge szolgáltatással működik, és ezek motorok fejlesztési célra rendben. Az Azure IoT Edge használatakor újra a moby-motor, és a Microsoft biztosítja, hogy ez a motor karbantartása. Egyéb tároló-motorok használatával az IoT Edge-eszköz nem támogatott.

### <a name="choose-upstream-protocol"></a>Válassza ki a felsőbb rétegbeli protokoll

A protokoll (és így a használt port) az Edge agent és az Edge hub az IoT hub felsőbb rétegbeli kommunikációs konfigurálható. Az alapértelmezett protokoll AMQP, de érdemes módosíthatja, hogy a hálózati konfigurációtól függően. 

A két futásidejű modulok is rendelkeznek egy **UpstreamProtocol** környezeti változót. A változó az érvényes értékek a következők: 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Konfigurálja a UpstreamProtocol változó az Edge agent magán az eszközön a config.yaml fájlban. Például ha az IoT Edge-eszköz, amely blokkolja az AMQP-portok proxykiszolgáló mögött található, szükség lehet az Edge agent keresztül WebSocket (AMQPWS) az AMQP használata a kezdeti kapcsolat az IoT hubhoz való konfigurálásához. 

Miután csatlakozik az IoT Edge-eszköz, ügyeljen arra, folytathatja a későbbiekben mindkét futásidejű modulok UpstreamProtocol változója konfigurálását. Ez a folyamat, például a [proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Környezet

* **Hasznos**
    * Használja következetesen a felsőbb rétegbeli protokoll
    * Csökkentse az Edge hub által elfoglalt memória
    * Ne használjon modul képeket hibakeresés verziói

### <a name="be-consistent-with-upstream-protocol"></a>Használja következetesen a felsőbb rétegbeli protokoll

Ha konfigurálta az Edge agent az IoT Edge eszközön, a másik, mint az alapértelmezett az AMQP protokollt használja, akkor ugyanazt a protokollt az összes későbbi központi telepítés kell deklarálni. Például ha az IoT Edge-eszköz, amely blokkolja az AMQP-portok proxykiszolgáló mögött található, valószínűleg konfigurálta az eszköz felett WebSocket (AMQPWS) amqp-n keresztül csatlakozhat. Modulokat az eszközön, ha nem adja meg az Edge agentet és az Edge hub az APQPWS protokollnak történő telepítésekor az alapértelmezett AMQP felülírják a beállításokat, és megakadályozza, hogy újra kapcsolódni. 

Csak kell konfigurálnia a UpstreamProtocol környezeti változót az Edge-ügynök és a hubhoz Edge-modulok. Minden további modulok fogad el, függetlenül a protokoll van beállítva, a futásidejű modulok. 

Ez a folyamat, például a [proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md).

### <a name="reduce-memory-space-used-by-edge-hub"></a>Csökkentse az Edge hub által elfoglalt memória

Ha telepíti a korlátozott eszközök korlátozott rendelkezésre álló memória, konfigurálhatja úgy a Edge hub az hatékonyabbá kapacitás futnak, és kevesebb lemezterületet használja. Ezek a beállítások azonban korlátozhatja az Edge hub teljesítményét, így keresse meg az egyensúlyt a legmegfelelőbb megoldás. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Ne optimalizálja a teljesítmény korlátozott eszközökön

Az Edge hub optimalizált teljesítményt alapértelmezés szerint, akkor próbálja meg nagy mennyiségű memóriát lefoglalni. Ez a konfiguráció stabilitását problémákat okozhat például a Raspberry Pi kisebb eszközökön. Ha telepíti a korlátozott erőforrásokkal eszközök, érdemes beállítani a **OptimizeForPerformance** környezeti változót, **hamis** az Edge hub. 

További információkért lásd: [stabilitási problémák erőforráson eszközök korlátozott](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>A fel nem használt protokollok letiltása

Az Edge hub teljesítményének optimalizálása és a memóriahasználat csökkentése másik módja, tiltsa le minden olyan protokoll, amely nem használ a megoldásban a protokoll aknázni. 

Protokoll aknázni az üzembe helyezési manifestech az Edge hub modul logikai környezeti változók beállításával vannak konfigurálva. A három változók az alábbiak:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Minden mindhárom változóval rendelkezik *két aláhúzásjeleket* és állítható a true vagy FALSE (hamis). 

#### <a name="reduce-storage-time-for-messages"></a>Üzenetek tárolása idő csökkentése

Az Edge hub modul ideiglenesen tárolja az üzeneteket, ha azok nem lehet kézbesíteni az IoT hub bármilyen okból. Beállíthatja, hogy mennyi ideig az Edge hub tárolja kézbesítetlen üzenetek előtt, amely tájékoztatja őket lejár. Ha kérdése memória az eszközön, csökkentheti a **timeToLiveSecs** az Edge hub ikermodul értéket. 

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

Egy címke egy Docker fogalom, amely segítségével a Docker-tárolók verzióinak megkülönböztetésére. A címkék olyan például utótagok **1.0** , amely egy tároló-beállításjegyzékbe végén lépjen. Ha például **mcr.microsoft.com/azureiotedge-agent:1.0**. A címkék mutable és módosítható átirányítása egy másik tárolóba tetszőleges időpontban, így csapata egy konvenciókhoz, és hajtsa végre a továbblépés modul rendszerképek frissítése során meg kell egyezniük. 

A címkék segítségével érvényesítését a frissítések az IoT Edge-eszközökön. Amikor leküld egy frissített verzióját egy modul a tárolóregisztrációs adatbázisba, növelje meg a címke. Ezután küldje le új központi telepítést az eszközök növekszik a címkével ellátott. A tároló motor fogja felismerni a megnövelt címke új verzióként, és kéri a modul legújabb saját eszközre. 

Egy címke egyezmény egy példa: [az IoT Edge-futtatókörnyezet frissítése](how-to-update-iot-edge.md#understand-iot-edge-tags) megtudhatja, hogyan IoT Edge segítségével működés közbeni címkék és a meghatározott címkék követi nyomon verziók. 

## <a name="networking"></a>Hálózat

* **Hasznos**
    * Kimenő és bejövő konfiguráció áttekintése
    * Engedélyezési lista kapcsolatok
    * Egy proxyn keresztül történő kommunikáció konfigurálása

### <a name="review-outboundinbound-configuration"></a>Kimenő és bejövő konfiguráció áttekintése

Azure IoT Hub és az IoT Edge közötti kommunikációs csatornákat mindig legyen a kimenő vannak konfigurálva. A legtöbb IoT Edge-forgatókönyvekhez csak három kapcsolatok szükségesek. A tároló motor kell csatlakoztatnia a tárolóregisztrációs adatbázis (vagy beállításjegyzékek), amely a modul képeket tárol. Az IoT Edge-futtatókörnyezet kell csatlakozni az IoT Hub eszköz konfigurációt információgyűjtéshez, és az üzenetek és a telemetriai adatok küldése. És az Automatikus kiépítés használatakor az IoT Edge-démon kell-e csatlakozni a Device Provisioning Service. További információkért lásd: [tűzfal és a port konfigurációs szabályok](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="whitelist-connections"></a>Engedélyezési lista kapcsolatok

Ha a hálózat beállításait igényli, hogy kifejezetten engedélyezett kapcsolatok végzett az IoT Edge-eszközök, tekintse át az IoT Edge-összetevők a következők közül:

* **IoT Edge-ügynök** kapcsolatot nyit a állandó AMQP és MQTT az IoT hubhoz, valószínűleg a websockets protokoll keresztül. 
* **IoT Edge hubot** megnyit egy állandó egyetlen AMQP-kapcsolaton vagy több MQTT-kapcsolatot az IoT hubhoz, valószínűleg a websockets protokoll keresztül. 
* **IoT Edge-démon** az IoT hub időszakos HTTPS-hívást hajt végre. 

Három esetben a DNS-név megfelel a mintának \*.azure-devices.net. 

Ezenkívül a **Container-motor** tárolójegyzékek-hívást hajt végre a HTTPS-kapcsolaton keresztül. Az IoT Edge-futtatókörnyezet tárolórendszerképek lekéréséhez a DNS-név mcr.microsoft.com. A tároló motor más beállításjegyzékek csatlakozik, a központi telepítésben lévő konfigurálva. 

Ezzel az ellenőrzőlistával tűzfalszabályokat kiindulópontként szolgál:

   | URL-címe (\* = helyettesítő karakter) | Kimenő TCP-portok | Használat |
   | ----- | ----- | ----- |
   | MCR.microsoft.com  | 443 | A Microsoft tárolóregisztrációs adatbázis |
   | Global.Azure-eszközök – provisioning.net  | 443 | A DPS hozzáféréséhez (nem kötelező) |
   | \*. azurecr.io | 443 | Személyes, mind a 3. fél tárolóregisztrációs adatbázis |
   | \*.blob.core.windows.net | 443 | Töltse le a lemezkép eltérések | 
   | \*.Azure-devices.net | 5671, 8883, 443-as porton | Az IoT Hub-hozzáférés |
   | \*. docker.io  | 443 | Docker-hozzáférés (nem kötelező) |

### <a name="configure-communication-through-a-proxy"></a>Egy proxyn keresztül történő kommunikáció konfigurálása

Az eszközök fog üzembe helyezni a hálózaton, proxykiszolgálót használ, ha azok kell elérni az IoT Hub és a tároló-beállításjegyzékek a proxyn keresztül kommunikálnak. További információkért lásd: [proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Megoldások kezelése

* **Hasznos**
    * Naplók és diagnosztika beállítása
    * Vegye figyelembe a tesztek és CI/CD-folyamatok

### <a name="set-up-logs-and-diagnostics"></a>Naplók és diagnosztika beállítása

Linux rendszeren az IoT Edge-démon journald használja az alapértelmezett naplózási illesztőprogramot. A parancssori eszközzel `journalctl` lekérdezni a démon naplózza. A Windows az IoT Edge-démon a PowerShell diagnosztikai használ. Használat `Get-WinEvent` a lekérdezések naplói a démonból. IoT Edge-modulok a JSON-illesztőprogram naplózása, a Docker alapértelmezett használja.  

Amikor egy IoT Edge-példányban tesztel, kérheti le a naplókat és hárítsa el az eszközök általában hozzáférhet. A telepítési forgatókönyvben nem lehet ezt a lehetőséget. Vegye figyelembe, hogy azt a módszert, éles környezetben az eszközökkel kapcsolatos információk összegyűjtéséhez. Az egyik lehetőség, hogy egy naplózási modul, amely adatokat gyűjt a más modulok tesznek, és elküldi a felhőbe. Például egy naplózási modul [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics), vagy a saját is tervezhet. 

Ha aggódik túl nagy a korlátozott erőforráshoz eszközön naplókat, a memóriahasználat csökkentése érdekében néhány lehetősége van. 

* A Docker-démont, maga az összes docker logfiles mérete kifejezetten korlátozhatja. A Linux rendszerre, konfigurálása, a démon `/etc/docker/daemon.json`. A Windows `C:\ProgramData\docker\confige\daemon.json`. 
* Ha azt szeretné beállítani az egyes tárolók naplófájl méretét, az egyes modul a CreateOptions megteheti. 
* Konfigurálja az automatikus kezelésére naplók által beállítás journald Docker illesztőprogram naplózása alapértelmezés szerint a Docker. 
* Bizonyos időközönként eltávolítja az régi naplókat az eszközről a logrotate eszköz a Docker telepítésével. Használja a következő fájl megadása: 

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
