---
title: Az Azure IoT Hub kifejezések szószedete | Microsoft Docs
description: Fejlesztői útmutató – a Szószedet ismerteti az Azure IoT Hub cikkeiben használt általános feltételeket.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.openlocfilehash: d73dcb89739395e4dd52b1ef4d39c41ad6125666
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457162"
---
# <a name="glossary-of-iot-hub-terms"></a>IoT Hub kifejezések glosszáriuma
Ez a cikk a IoT Hub cikkeiben használt általános kifejezéseket sorolja fel.

## <a name="advanced-message-queueing-protocol"></a>Speciális üzenetsor-kezelési protokoll
A [speciális üzenetsor-kezelési protokoll (AMQP)](https://www.amqp.org/) a [IoT hub](#iot-hub) által támogatott üzenetküldési protokollok egyike az eszközökkel való kommunikációhoz. További információ a IoT Hub által támogatott üzenetküldési protokollokról: [üzenetek küldése és fogadása IoT hub](iot-hub-devguide-messaging.md)használatával.

## <a name="automatic-device-management"></a>Automatikus eszközkezelés
Az Azure-ban az automatikus eszközkezelés IoT Hub automatizálja a nagyméretű eszközök flottáinak teljes életciklusával kapcsolatos ismétlődő és összetett feladatokat. Az automatikus eszközkezelés lehetővé teszi, hogy a tulajdonságok alapján csoportosítsa az eszközöket, Definiáljon egy kívánt konfigurációt, és hagyja IoT Hub frissíteni az eszközöket, amikor azok hatókörbe kerülnek.  [Automatikus eszköz-konfigurációkból](iot-hub-auto-device-config.md) és [IoT Edge automatikus központi telepítésből](../iot-edge/how-to-deploy-monitor.md)áll.

## <a name="automatic-device-configuration"></a>Eszköz automatikus konfigurálása
A megoldás háttérbe állítása [automatikus eszköz-konfigurációk](iot-hub-auto-device-config.md) használatával rendelhető hozzá a kívánt tulajdonságok az [eszközök](#device-twin) egy készletéhez és a jelentés állapotához a rendszermetrikák és az egyéni metrikák használatával. 

## <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület
A [klasszikus Azure CLI](../cli-install-nodejs.md) egy platformfüggetlen, nyílt forráskódú, rendszerhéjon alapuló parancssori eszköz, amellyel a Microsoft Azure erőforrásai hozhatók létre és kezelhetők. A parancssori felület ezen verzióját csak a klasszikus központi telepítésekhez szabad használni.

## <a name="azure-cli"></a>Azure parancssori felület (CLI)
Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2) egy platformfüggetlen, nyílt forráskódú, rendszerhéjon alapuló parancssori eszköz, amellyel a Microsoft Azure erőforrásai hozhatók létre és kezelhetők.


## <a name="azure-iot-device-sdks"></a>Azure IoT-eszközök SDK-k
Több nyelven is elérhetők az _eszközök SDK_ -k, amelyek lehetővé teszik, hogy az IoT-hubhoz interaktív [eszközöket](#device-app) hozzon létre. A IoT Hub oktatóanyagok bemutatják, hogyan használhatja ezeket az eszköz SDK-kat. Ebben a GitHub- [tárházban](https://github.com/Azure/azure-iot-sdks)megtalálja a forráskódot és további információkat az eszköz SDK-ról.

## <a name="azure-iot-service-sdks"></a>Azure IoT Service SDK-k
Több nyelven is elérhetők olyan _szolgáltatási SDK_ -k, amelyek lehetővé teszik, hogy az IoT-hubhoz interaktív [háttérbeli alkalmazásokat](#back-end-app) hozzon létre. A IoT Hub oktatóanyagok bemutatják, hogyan használhatja ezeket a szolgáltatási SDK-kat. Ebben a GitHub- [tárházban](https://github.com/Azure/azure-iot-sdks)megtalálja a forráskódot és a szolgáltatás SDK-k további információit.

## <a name="azure-iot-tools"></a>Azure IoT-eszközök
Az [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) egy platformfüggetlen, nyílt forráskódú Visual Studio Code-bővítmény, amely segít az Azure-IoT hub és-eszközök felügyeletében a vs Code-ban. Az Azure IoT Tools révén a IoT-fejlesztők könnyedén hozhatnak létre IoT-projekteket a VS Code-ban.

## <a name="azure-portal"></a>Azure portál
A [Microsoft Azure Portal](https://portal.azure.com) egy központi hely, ahol üzembe helyezheti és kezelheti Azure-erőforrásait. A tartalmat a _pengék_használatával rendezi.

## <a name="azure-powershell"></a>Azure PowerShell
A [Azure PowerShell](/powershell/azure/overview) parancsmagok gyűjteménye, amelyekkel az Azure a Windows PowerShell segítségével kezelhető. A parancsmagok segítségével az Azure platformon keresztül továbbított megoldásokat és szolgáltatásokat hozhat létre, tesztelheti, üzembe helyezheti és kezelheti.

## <a name="azure-resource-manager"></a>Azure Resource Manager
A [Azure Resource Manager](../azure-resource-manager/management/overview.md) lehetővé teszi, hogy csoportként működjön együtt a megoldás erőforrásaival. Egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti a megoldás erőforrásait.

## <a name="azure-service-bus"></a>Azure Service Bus
A [Service Bus](../service-bus/index.md) felhőalapú kommunikációt tesz lehetővé vállalati üzenetkezeléssel és közvetített kommunikációval, amely segít a helyszíni megoldások felhővel való összekapcsolásában. Egyes IoT Hub oktatóanyagok Service Bus [várólisták](../service-bus-messaging/service-bus-messaging-overview.md)használatát teszik elérhetővé.

## <a name="azure-storage"></a>Azure Storage
Az [Azure Storage](../storage/common/storage-introduction.md) egy felhőalapú tárolási megoldás. Tartalmazza azt a Blob Storage szolgáltatást, amelyet a strukturálatlan objektumok adattárolásához használhat. Néhány IoT Hub oktatóanyag a blob Storage-ot használja.

## <a name="back-end-app"></a>Háttérbeli alkalmazás
[IoT hub](#iot-hub)kontextusában a háttérbeli alkalmazás egy olyan alkalmazás, amely egy IoT hub szolgáltatáshoz kapcsolódó végpontokhoz csatlakozik. A háttérbeli alkalmazások például lekérhetik az [eszközről a felhőbe irányuló](#device-to-cloud) üzeneteket, vagy kezelhetik az [identitás-beállításjegyzéket](#identity-registry). A háttérben futó alkalmazások jellemzően a felhőben futnak, de számos oktatóanyagban a háttérbeli alkalmazások a helyi fejlesztési gépen futó konzolos alkalmazások.

## <a name="built-in-endpoints"></a>Beépített végpontok
Minden IoT hub tartalmaz egy beépített [végpontot](iot-hub-devguide-endpoints.md) , amely az Event hub-kompatibilis. Az eszközről a felhőbe irányuló üzenetek a végpontról való olvasásához használhatja a Event Hubs-vel használható bármely mechanizmust.

## <a name="cloud-gateway"></a>Felhőbeli átjáró
A Cloud Gateway lehetővé teszi a csatlakozást olyan eszközökhöz, amelyek nem tudnak közvetlenül csatlakozni a [IoT hubhoz](#iot-hub). A felhőben üzemelő átjárók a felhőben futnak, szemben egy helyi [átjáróval](#field-gateway) , amely az eszközein fut. A Felhőbeli átjáró tipikus használati esete a protokollok fordításának megvalósítása az eszközökön.

## <a name="cloud-to-device"></a>Felhőből eszközre
Egy IoT hub-ból egy csatlakoztatott eszközre küldött üzenetekre hivatkozik. Ezek az üzenetek gyakran olyan parancsok, amelyek arra utasítja az eszközt, hogy végezze el a műveletet. További információ: [üzenetek küldése és fogadása IoT Hubsal](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Konfiguráció
Az [automatikus eszköz-konfiguráció](iot-hub-auto-device-config.md)kontextusában a IoT hub belüli konfiguráció határozza meg az eszközök egy készletének kívánt konfigurációját, és mérőszámokat biztosít az állapot és a folyamat jelentéséhez.

## <a name="connection-string"></a>Kapcsolati sztring
Az alkalmazás kódjában kapcsolati karakterláncokat használ a végponthoz való kapcsolódáshoz szükséges információk beágyazásához. A kapcsolatok karakterlánca általában tartalmazza a végpontok és a biztonsági információk címeit, de a kapcsolatok karakterlánc-formátumai eltérőek a szolgáltatások között. A IoT Hub szolgáltatáshoz két típusú kapcsolattípus van társítva:
- Az *eszköz kapcsolati karakterláncai* lehetővé teszik az eszközök számára az IoT hub eszközre irányuló végpontokhoz való kapcsolódását.
- *IoT hub kapcsolati karakterláncok* lehetővé teszik a háttérbeli alkalmazások számára, hogy csatlakozzanak a IoT hub szolgáltatáshoz csatlakozó végpontokhoz.

## <a name="custom-endpoints"></a>Egyéni végpontok
Létrehozhat egyéni [végpontokat](iot-hub-devguide-endpoints.md) egy IoT-hubhoz az [útválasztási szabályok](#routing-rules)által elküldött üzenetek kézbesítéséhez. Az egyéni végpontok közvetlenül kapcsolódnak egy Event hub-hoz, egy Service Bus-várólistához vagy egy Service Bus témakörhöz.

## <a name="custom-gateway"></a>Egyéni átjáró
Az átjárók lehetővé teszik a kapcsolódást olyan eszközökhöz, amelyek nem tudnak közvetlenül csatlakozni a [IoT hubhoz](#iot-hub). A Azure IoT Edge használatával olyan egyéni átjárókat hozhat létre, amelyek egyéni logikát implementálnak az üzenetek kezelésére, az egyéni protokoll átalakítására és az Edge egyéb feldolgozására.

## <a name="data-point-message"></a>Adatpontra vonatkozó üzenet
Az adatpontok üzenete egy olyan [eszközről a felhőbe](#device-to-cloud) irányuló üzenet, amely a [telemetria](#telemetry) , például a szél sebességét vagy a hőmérsékletet tartalmazza.

## <a name="desired-configuration"></a>Kívánt konfiguráció
Egy [különálló eszköz](iot-hub-devguide-device-twins.md)kontextusában a kívánt konfiguráció a tulajdonságok és metaadatok teljes készletét jelenti, amelyeket szinkronizálni kell az eszközzel.

## <a name="desired-properties"></a>Kívánt tulajdonságok
Az [eszközök Twin](iot-hub-devguide-device-twins.md)-környezetében a kívánt tulajdonságok a [jelentett tulajdonságokkal](#reported-properties) rendelkező, az eszköz konfigurációjának vagy feltételének szinkronizálásához használt eszköz-iker alszakasza. A kívánt tulajdonságokat csak [háttérbeli alkalmazások](#back-end-app) állíthatják be, és az [eszköz alkalmazás](#device-app)figyeli őket.

## <a name="device-to-cloud"></a>Eszközről a felhőbe
Egy csatlakoztatott eszközről [IoT Hubre](#iot-hub)küldött üzenetekre hivatkozik. Ezek az üzenetek [adatponthoz](#data-point-message) vagy [interaktív](#interactive-message) üzenetekhez tartozhatnak. További információ: [üzenetek küldése és fogadása IoT Hubsal](iot-hub-devguide-messaging.md).

## <a name="device"></a>Eszköz
A IoT kontextusában az eszköz általában egy kis méretű, különálló számítástechnikai eszköz, amely adatokat gyűjthet vagy más eszközöket is vezérelheti. Előfordulhat például, hogy egy eszköz egy környezeti figyelő eszköz, vagy egy olyan vezérlő, amely egy üvegházban található öntözési és szellőzési rendszerekhez szükséges. Az [eszköz katalógusa](https://catalog.azureiotsolutions.com/) felsorolja a [IoT hub](#iot-hub)használatával tanúsított hardvereszközök listáját.

## <a name="device-app"></a>Eszköz alkalmazás
Az eszközön fut egy alkalmazás [, és kezeli](#device) a kommunikációt az [IoT hubhoz](#iot-hub). Általában az [Azure IoT Device SDK](#azure-iot-device-sdks) -k egyikét használja egy eszköz alkalmazásának megvalósításakor. A IoT számos oktatóanyagában [szimulált eszközt](#simulated-device) használ a kényelem érdekében.

## <a name="device-condition"></a>Eszköz feltétele
Az eszköz állapotával kapcsolatos információk, például a jelenleg használatban lévő kapcsolati módszer, amelyet egy [eszköz alkalmazás](#device-app)jelentett. Az [eszközök alkalmazásai](#device-app) is jelenthetik a képességeiket. A feltételek és a képességek adatait az eszközök ikrek használatával kérdezheti le.

## <a name="device-data"></a>Eszközbeállítások
Az eszköz adatai a IoT Hub [Identity registryben](#identity-registry)tárolt eszközönkénti adatokra vonatkoznak. Az ilyen típusú adatimportálás és-exportálás lehetséges.

## <a name="device-explorer"></a>Eszközkereső
Az [Eszközkezelő](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) olyan eszköz, amely Windows rendszeren fut, és lehetővé teszi az eszközök kezelését az [Identity registryben](#identity-registry). Az eszköz üzeneteket is küldhet és fogadhat az eszközeire.

## <a name="device-identity"></a>Eszközidentitás
Az eszköz identitása az [Identity registryben](#identity-registry)regisztrált összes eszközhöz rendelt egyedi azonosító.

## <a name="device-management"></a>Eszközfelügyelet
Az eszközkezelés magában foglalja a IoT-megoldás eszközeinek kezeléséhez kapcsolódó teljes életciklust, beleértve a tervezést, a létesítést, a konfigurálást, a figyelést és a kivonást.

## <a name="device-management-patterns"></a>Eszközfelügyeleti minták
Az [IoT hub](#iot-hub) lehetővé teszi a közös eszközkezelés mintázatát, beleértve az újraindítást, a gyári beállítások visszaállítását és a belső vezérlőprogram frissítését az eszközökön.

## <a name="device-rest-api"></a>Eszköz REST API
Az eszközről [REST API eszközről](https://docs.microsoft.com/rest/api/iothub/device) az eszközről a felhőbe irányuló üzeneteket küldhet egy IoT hubhoz, és a [felhőből az eszközre](#cloud-to-device) irányuló üzeneteket fogadhat egy IoT hub-ból. Általában a IoT Hub oktatóanyagokban látható, a magasabb szintű [eszközök SDK](#azure-iot-device-sdks) -k egyikét kell használnia.

## <a name="device-provisioning"></a>Eszköz kiépítés
Az eszköz kiépítés során a rendszer a kezdeti [eszköz adatait](#device-data) hozzáadja a megoldásban lévő üzletekhez. Ahhoz, hogy egy új eszköz csatlakozhasson a központhoz, hozzá kell adnia egy eszköz AZONOSÍTÓját és kulcsait a IoT Hub [Identity registryhez](#identity-registry). A kiépítési folyamat részeként előfordulhat, hogy más megoldás-tárolókban is inicializálnia kell az eszközre vonatkozó adatmennyiséget.

## <a name="device-twin"></a>Ikereszközök
Az [eszköz Twin](iot-hub-devguide-device-twins.md) egy JSON-dokumentum, amely az eszköz állapotával kapcsolatos információkat, például a metaadatokat, a konfigurációkat és a feltételeket tárolja. [IoT hub](#iot-hub) megőrzi az IoT hub-ban kiépített eszközökhöz tartozó különálló eszközöket. Az eszközökhöz tartozó ikrek lehetővé teszik [az eszközök és a konfigurációk szinkronizálását](#device-condition) az eszköz és a megoldás hátterében. Lekérdezheti az eszközökből származó ikreket, hogy megkeresse az adott eszközöket, és lekérdezze a hosszan futó műveletek állapotát.

## <a name="direct-method"></a>Közvetlen metódus
A [közvetlen módszer](iot-hub-devguide-direct-methods.md) segítségével elindíthat egy metódust az eszközön, ha az IOT hub API-ját hívja meg.

## <a name="endpoint"></a>Végpont
Az IoT hub több [végpontot](iot-hub-devguide-endpoints.md) tesz elérhetővé, amelyek lehetővé teszik az alkalmazások számára az IoT hubhoz való kapcsolódást. Az eszközökön elérhető végpontok olyan műveleteket hajthatnak végre, mint például az [eszközről a felhőbe](#device-to-cloud) irányuló üzenetek küldése és [a felhőből az eszközre irányuló](#cloud-to-device) üzenetek fogadása. Vannak olyan szolgáltatásokkal kapcsolatos felügyeleti végpontok, amelyek lehetővé teszik a [háttérbeli alkalmazások](#back-end-app) számára, hogy olyan műveleteket hajtsanak végre, mint például az [eszközök identitásának](#device-identity) kezelése és az eszközök kettős kezelése. Az eszközről a felhőbe irányuló üzenetek olvasásához a szolgáltatáshoz kapcsolódó [beépített végpontok](#built-in-endpoints) tartoznak. Létrehozhat [Egyéni végpontokat](#custom-endpoints) a [útválasztási szabályok](#routing-rules)által elküldött eszközről a felhőbe irányuló üzenetek fogadásához.

## <a name="event-hubs-service"></a>Event Hubs szolgáltatás
A [Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) egy rugalmasan méretezhető adatbeviteli szolgáltatás, amely másodpercenként több millió eseményt képes fogadni. A szolgáltatás segítségével feldolgozhatja és elemezheti a csatlakoztatott eszközök és alkalmazások által előállított nagy mennyiségű adatmennyiséget. Az IoT Hub szolgáltatással való összehasonlításért tekintse meg az [azure IoT hub és az azure Event Hubs összehasonlítását](iot-hub-compare-event-hubs.md)ismertető témakört.

## <a name="event-hub-compatible-endpoint"></a>Event hub-kompatibilis végpont
Az IoT hub-ba küldött, az [eszközről a felhőbe](#device-to-cloud) irányuló üzenetek olvasásához csatlakozhat egy végponthoz a hub-on, és bármilyen Event hub-kompatibilis módszert használhat az üzenetek olvasásához. Az Event hub-kompatibilis módszerek közé tartozik a [Event Hubs SDK](../event-hubs/event-hubs-programming-guide.md) -k és a [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md)használata.

## <a name="field-gateway"></a>Mező átjáró
A helyszíni átjárók lehetővé teszik a csatlakozást olyan eszközökhöz, amelyek nem tudnak közvetlenül csatlakozni a [IoT hubhoz](#iot-hub) , és általában helyileg vannak telepítve az eszközökön. További információ: [Mi az az Azure IoT hub?](about-iot-hub.md)

## <a name="free-account"></a>Ingyenes fiók
Létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/) , amellyel elvégezheti a IoT hub oktatóanyagokat, és kísérletezhet a IoT hub szolgáltatással (és más Azure-szolgáltatásokkal).

## <a name="gateway"></a>Átjáró
Az átjárók lehetővé teszik a kapcsolódást olyan eszközökhöz, amelyek nem tudnak közvetlenül csatlakozni a [IoT hubhoz](#iot-hub). Lásd még: [Field Gateway](#field-gateway), [Cloud Gateway](#cloud-gateway)és [Custom Gateway](#custom-gateway).

## <a name="identity-registry"></a>Identitás-nyilvántartó
Az [Identity Registry](iot-hub-devguide-identity-registry.md) egy IoT hub beépített összetevője, amely az IoT hubhoz való kapcsolódásra jogosult egyes eszközök adatait tárolja.

## <a name="interactive-message"></a>Interaktív üzenet
Az interaktív üzenet egy [felhőből az eszközre](#cloud-to-device) irányuló üzenet, amely azonnali műveletet indít el a megoldás hátterében. Előfordulhat például, hogy egy eszköz riasztást küld egy olyan hibáról, amelyet automatikusan be kell jelentkeznie egy CRM-rendszerbe.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub
A IoT Hub egy teljes körűen felügyelt Azure-szolgáltatás, amely megbízható és biztonságos kétirányú kommunikációt tesz lehetővé több millió eszköz és egy megoldás hátterében. További információ: [Mi az az Azure IoT hub?](about-iot-hub.md) Az [Azure-előfizetését](#subscription)használva IoT hubokat hozhat létre a IoT üzenetkezelési számítási feladatainak kezeléséhez.

## <a name="iot-hub-metrics"></a>IoT Hub metrikák
[IoT hub mérőszámok](iot-hub-metrics.md) adatokat biztosítanak az [Azure-előfizetésében](#subscription)lévő IoT-hubok állapotáról. IoT Hub mérőszámok lehetővé teszik a szolgáltatás általános állapotának és a hozzájuk kapcsolódó eszközök értékelését. IoT Hub mérőszámok segítségével megtekintheti, hogy mi történik az IoT-hubhoz, és hogyan vizsgálja meg a kiváltó problémákat anélkül, hogy kapcsolatba kellene lépnie az Azure ügyfélszolgálatával.

## <a name="iot-hub-query-language"></a>IoT Hub lekérdezés nyelve
A [IoT hub lekérdezési nyelv](iot-hub-devguide-query-language.md) egy SQL-szerű nyelv, amely lehetővé teszi a [](#job) és az eszközök lekérését.

## <a name="iot-hub-resource-rest-api"></a>Erőforrás-REST API IoT Hub
A [IoT hub erőforrás-REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource) segítségével kezelheti az [Azure-előfizetéshez](#subscription) tartozó IoT-hubokat olyan műveletek végrehajtásához, mint például a hubok létrehozása, frissítése és törlése.

## <a name="iot-solution-accelerators"></a>IoT-megoldástámogatók
Az Azure IoT megoldás-gyorsító csomag több Azure-szolgáltatást is kínál a megoldásokhoz. Ezek a megoldások lehetővé teszik a gyakori IoT-forgatókönyvek teljes körű megvalósításának gyors megkezdését. További információ: [Mi az Azure IoT megoldás-gyorsító?](../iot-accelerators/about-iot-accelerators.md)

## <a name="the-iot-extension-for-azure-cli"></a>Az Azure CLI-hez készült IoT-bővítmény 
[Az Azure CLI-hez készült IoT-bővítmény](https://github.com/Azure/azure-iot-cli-extension) egy platformfüggetlen, parancssori eszköz. Az eszköz lehetővé teszi az eszközök kezelését az [Identity registryben](#identity-registry), üzenetek és fájlok küldését és fogadását az eszközökről, valamint az IoT hub-műveletek figyelését.

## <a name="job"></a>Feladat
A megoldás háttérbe állítása [feladatok](iot-hub-devguide-jobs.md) használatával ütemezhet és követheti a tevékenységeket az IoT hub-ban regisztrált eszközökön. A tevékenységek közé tartozik az eszköz dupla [kívánt tulajdonságainak](#desired-properties)frissítése, az eszköz kettős [címkék](#tags)frissítése és a [közvetlen metódusok](#direct-method)meghívása. A [IoT hub](#iot-hub) az azonosítót is használja az [identitás-beállításjegyzékből](#identity-registry) [való importálásra és exportálásra](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) .

## <a name="modules"></a>Modulok
Az eszköz oldalon a IoT Hub eszköz SDK-k lehetővé teszik, hogy olyan [modulokat](iot-hub-devguide-module-twins.md) hozzon létre, amelyekben mindegyik egy független kapcsolódást nyit meg a IoT hubhoz. Ez a funkció lehetővé teszi, hogy külön névtereket használjon az eszköz különböző összetevőihez.

A modul identitása és a modul Twin ugyanazokat a funkciókat biztosítja, mint az [eszköz identitása](#device-identity) és az [eszközök Twin](#device-twin) , de finomabb részletességgel. Ez a finomabb részletesség lehetővé teszi, hogy a képes eszközök, például az operációs rendszer alapú eszközök vagy a belső vezérlőprogram eszközei több összetevőt kezelnek, az egyes összetevők konfigurációjának és feltételeinek elkülönítéséhez.

## <a name="module-identity"></a>Modul identitása
A modul identitása az eszközhöz tartozó összes modulhoz hozzárendelt egyedi azonosító. A modul identitása is regisztrálva van az [Identity registryben](#identity-registry).

## <a name="module-twin"></a>Modul Twin
Az eszközökhöz hasonlóan a Twin modul egy JSON-dokumentum, amely a modul állapotával kapcsolatos információkat, például metaadatokat, konfigurációkat és feltételeket tárol. IoT Hub megőrzi az IoT hub eszköz-identitása alatt kiépített modul-identitások külön modulját. Az ikrek modul lehetővé teszi, hogy szinkronizálja a modul feltételeit és konfigurációit a modul és a megoldás háttér-végpontja között. Az ikrek modulban lekérdezheti az adott modulokat, és lekérdezheti a hosszan futó műveletek állapotát.

## <a name="mqtt"></a>MQTT
A [MQTT](https://mqtt.org/) az egyik üzenetküldési protokoll, amelyet a [IoT hub](#iot-hub) támogat az eszközökkel való kommunikációhoz. További információ a IoT Hub által támogatott üzenetküldési protokollokról: [üzenetek küldése és fogadása IoT hub](iot-hub-devguide-messaging.md)használatával.

## <a name="operations-monitoring"></a>Műveletek monitorozása
A IoT Hub [Operations monitoring](iot-hub-operations-monitoring.md) lehetővé teszi, hogy valós időben figyelje a IoT hub műveleteinek állapotát. [IoT hub](#iot-hub) az eseményeket több különböző kategóriába tartozó műveletben követi nyomon. A feldolgozáshoz egy vagy több kategóriából is küldhet eseményeket IoT Hub végpontra. A hibák figyelése és az adatmintázatok alapján összetettebb feldolgozás is beállítható.

## <a name="physical-device"></a>Fizikai eszköz
A fizikai eszközök olyan valódi eszközök, mint például a málna PI, amely egy IoT hubhoz csatlakozik. A IoT Hub számos oktatóanyaga [szimulált eszközök](#simulated-device) használatával teszi lehetővé, hogy mintákat futtasson a helyi gépen.

## <a name="primary-and-secondary-keys"></a>Elsődleges és másodlagos kulcsok
Ha egy IoT hub eszközre irányuló vagy szolgáltatáshoz kapcsolódó végpontján csatlakozik, a [kapcsolati sztring](#connection-string) tartalmaz egy kulcsot, amely hozzáférést biztosít Önnek. Ha hozzáad egy eszközt az [azonosító beállításjegyzékhez](#identity-registry) , vagy egy [megosztott hozzáférési szabályzatot](#shared-access-policy) ad hozzá a központhoz, a szolgáltatás létrehoz egy elsődleges és egy másodlagos kulcsot. A két kulcs lehetővé teszi, hogy átadja az egyik kulcsról a másikra, ha az IoT hub elérésének elvesztése nélkül frissíti a kulcsot.

## <a name="protocol-gateway"></a>Protokoll-átjáró
A protokoll-átjáró jellemzően a felhőben van üzembe helyezve, és protokoll-fordítási szolgáltatásokat biztosít a [IoT hubhoz](#iot-hub)csatlakozó eszközökhöz. További információ: [Mi az az Azure IoT hub?](about-iot-hub.md)

## <a name="quotas-and-throttling"></a>Kvóták és szabályozás
Számos [kvóta](iot-hub-devguide-quotas-throttling.md) vonatkozik a [IoT hub](#iot-hub)használatára, a kvóták nagy része az IoT hub szintjétől függ. A [IoT hub](#iot-hub) a szolgáltatás futás közbeni használatára vonatkozó [szabályozást](iot-hub-devguide-quotas-throttling.md) is alkalmaz.

## <a name="reported-configuration"></a>Jelentett konfiguráció
Egy [különálló eszköz](iot-hub-devguide-device-twins.md)kontextusában a jelentett konfiguráció a tulajdonságok és a metaadatok teljes készletét jelenti, amelyeket jelenteni kell a megoldás hátterének.

## <a name="reported-properties"></a>Jelentett tulajdonságok
Az [eszközök Twin](iot-hub-devguide-device-twins.md)-környezetében a jelentett tulajdonságok a [kívánt tulajdonságokkal](#desired-properties) rendelkező eszközök iker szakasza az eszköz konfigurációjának vagy feltételének szinkronizálásához. A jelentett tulajdonságok csak az [eszköz alkalmazásban](#device-app) állíthatók be, és egy [háttérbeli alkalmazás](#back-end-app)által olvashatók és kérhetők le.

## <a name="resource-group"></a>Erőforráscsoport
A [Azure Resource Manager](#azure-resource-manager) erőforráscsoportok használatával csoportosítja a kapcsolódó erőforrásokat. Egy erőforráscsoport használatával egyszerre hajthat végre műveleteket a csoporton lévő összes erőforráson.

## <a name="retry-policy"></a>Újrapróbálkozási szabályzat
A felhőalapú szolgáltatásokhoz való csatlakozáskor az újrapróbálkozási szabályzat segítségével kezelheti az [átmeneti hibákat](/azure/architecture/best-practices/transient-faults) .

## <a name="routing-rules"></a>Útválasztási szabályok
A IoT hub [útválasztási szabályait](iot-hub-devguide-messages-read-custom.md) úgy konfigurálja, hogy az eszközről a felhőbe irányuló üzeneteket egy [beépített végpontra](#built-in-endpoints) vagy [Egyéni végpontokra](#custom-endpoints) irányítsa a megoldás hátterének feldolgozásához.

## <a name="sasl-plain"></a>SASL PLAIN
SASL PLAIN egy protokoll, amelyet a AMQP protokoll a biztonsági jogkivonatok átvitelére használ.

## <a name="service-rest-api"></a>Szolgáltatási REST API
Az eszközök kezeléséhez használhatja a megoldás háttér- [REST API a szolgáltatását](https://docs.microsoft.com/rest/api/iothub/service) . Az API lehetővé teszi az [eszközök Twin](#device-twin) tulajdonságainak lekérését és frissítését, a [közvetlen metódusok](#direct-method)meghívását és a [feladatok](#job)ütemezését. Általában a magasabb szintű [szolgáltatási SDK](#azure-iot-service-sdks) -k egyikét kell használnia, ahogy az a IoT hub oktatóanyagokban is látható.

## <a name="shared-access-signature"></a>Közös hozzáférésű jogosultságkód
A közös hozzáférésű aláírások (SAS) az SHA-256 biztonságos kivonatokon vagy URI-kon alapuló hitelesítési mechanizmus. Az SAS-hitelesítésnek két összetevője van: egy _közös hozzáférési házirend_ és egy _közös hozzáférési aláírás_ (más néven token). Az eszközök SAS használatával végzik el a hitelesítést egy IoT hub használatával. A [háttérbeli alkalmazások](#back-end-app) az SAS használatával is hitelesítik magukat az IoT hub szolgáltatással szemben lévő végpontokkal. Általában az SAS-tokent a [kapcsolati karakterláncban](#connection-string) adja meg, amelyet az alkalmazás használ a IoT hub-kapcsolat létesítéséhez.

## <a name="shared-access-policy"></a>Megosztott elérési házirend
A megosztott elérési házirend határozza meg, hogy az adott szabályzathoz tartozó érvényes [elsődleges vagy másodlagos kulccsal](#primary-and-secondary-keys) rendelkezők számára milyen engedélyek vannak megadva. A központ megosztott hozzáférési házirendjeit és kulcsait a [portálon](#azure-portal)kezelheti.

## <a name="simulated-device"></a>Szimulált eszköz
A IoT Hub számos oktatóanyaga szimulált eszközök használatával teszi lehetővé, hogy mintákat futtasson a helyi gépen. Ezzel szemben a [fizikai eszköz](#physical-device) egy valódi eszköz, például egy málna PI, amely egy IoT hubhoz csatlakozik.

## <a name="solution"></a>Megoldás
Egy _megoldás_ olyan Visual Studio-megoldásra is hivatkozhat, amely egy vagy több projektet tartalmaz. A _megoldások_ olyan IoT-megoldásra is hivatkozhatnak, amely olyan elemeket tartalmaz, mint például az eszközök, az eszközök [alkalmazásai](#device-app), az IoT hub, az egyéb Azure-szolgáltatások és a [háttérbeli alkalmazások](#back-end-app).

## <a name="subscription"></a>Előfizetés
Az Azure-előfizetések esetében a számlázás zajlik. Minden Ön által létrehozott vagy az Azure-szolgáltatáshoz használt Azure-erőforrás egyetlen előfizetéshez van társítva. Az előfizetés szintjén számos kvóta is érvényes.

## <a name="system-properties"></a>Rendszertulajdonságok
Egy [különálló eszköz](iot-hub-devguide-device-twins.md)kontextusában a Rendszertulajdonságok csak olvashatók, és információkat tartalmaznak az eszköz használatáról, például a legutóbbi tevékenység idejéről és a kapcsolatok állapotáról.

## <a name="tags"></a>Címkék
A [Twin eszközök](iot-hub-devguide-device-twins.md)kontextusában a címkék a megoldás által tárolt, és egy JSON-dokumentum formájában lekért eszköz-metaadatok. A címkék nem láthatók az eszközön futó alkalmazások számára.

## <a name="telemetry"></a>Telemetria
Az eszközök telemetria adatokat gyűjtenek, például a szél sebességét vagy a hőmérsékletet, és adatpont-üzenetek használatával küldik el a telemetria egy IoT-hubhoz.

## <a name="token-service"></a>Jogkivonat-szolgáltatás
A jogkivonat-szolgáltatás használatával hitelesítési mechanizmust valósíthat meg az eszközökhöz. Egy IoT Hub [megosztott hozzáférési szabályzatot](#shared-access-policy) használ, amely **DeviceConnect** engedélyekkel rendelkezik az *eszköz hatókörű* jogkivonatok létrehozásához. Ezek a tokenek lehetővé teszik, hogy az eszköz csatlakozhasson az IoT hubhoz. Az eszköz egyéni hitelesítési mechanizmust használ a jogkivonat-szolgáltatással történő hitelesítéshez. Ha az eszköz hitelesítése sikeresen megtörtént, a jogkivonat szolgáltatás egy SAS-tokent bocsát ki az eszköz számára az IoT hub eléréséhez.

## <a name="twin-queries"></a>Dupla lekérdezés
Az [eszközök és modulok két lekérdezése](iot-hub-devguide-query-language.md) az SQL-Like IoT hub lekérdezési nyelvet használja az eszközöktől az ikrekből vagy a modulokból származó adatok lekéréséhez. Ugyanezzel a IoT Hub lekérdezési nyelvvel kérheti le az IoT [](#job) hub futtatásával kapcsolatos információkat.

## <a name="twin-synchronization"></a>Kettős szinkronizálás
A Twin synchronization a [kívánt tulajdonságokat](#desired-properties) használja az ikrek vagy a modul ikrek számára az eszközök vagy modulok konfigurálásához és a [jelentett tulajdonságok](#reported-properties) lekéréséhez a Twin-ben való tároláshoz.

## <a name="x509-client-certificate"></a>X. 509 ügyféltanúsítvány
Az eszközök X. 509 tanúsítvánnyal hitelesíthetők a [IoT hub](#iot-hub)használatával. Az X. 509 tanúsítvány használata a [sas-tokenek](#shared-access-signature)használatának alternatívája.
