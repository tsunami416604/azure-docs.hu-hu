---
title: Az Azure IoT Hub szószedet |} A Microsoft Docs
description: Fejlesztői útmutató – Azure IoT hubra vonatkozó gyakori kifejezések.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.openlocfilehash: d79485fa7587a6cf8783f2e5779fa0a1d593aea7
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011432"
---
# <a name="glossary-of-iot-hub-terms"></a>Az IoT Hub kifejezések jegyzéke
Ez a cikk felsorolja az egyes az IoT Hub-cikkekben használt gyakori kifejezések.

## <a name="advanced-message-queueing-protocol"></a>Advanced Message Queueing Protocol
[Advanced Message Queueing Protocol (AMQP)](https://www.amqp.org/) van, amelyek közül az üzenetküldés van protokollokon [az IoT Hub](#iot-hub) támogatja a eszközökkel való kommunikációhoz. Az üzenetküldési protokollokat, amely az IoT Hub által támogatott kapcsolatos további információkért lásd: [küldhet és fogadhat üzeneteket az IoT Hub](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Automatikus eszközkezelés
Az Azure IoT Hub automatikus kezelés automatizálja az ismétlődő és összetett feladatok nagy eszköz flották kezelésének azok életciklusának teljes keresztül. Automatikus felügyeleti (MDM) cél az eszközök a hozzájuk tartozó tulajdonságok alapján, a kívánt konfiguráció definiálása és frissítheti az eszközeit, amikor hatókör lépnek az IoT Hub segítségével.  Áll [automatikus eszközkonfigurációk](iot-hub-auto-device-config.md) és [automatikus IoT Edge-telepítések](../iot-edge/how-to-deploy-monitor.md).

## <a name="automatic-device-configuration"></a>Eszközök automatikus konfigurálása
A megoldás háttérrendszere [automatikus eszközkonfigurációk](iot-hub-auto-device-config.md) körét a kívánt tulajdonságok hozzárendelése [ikereszközök](#device-twin) és állapot jelentése rendszermérőszámot és egyéni metrikák használatával. 

## <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület
A [Azure klasszikus parancssori felület](../cli-install-nodejs.md) egy platformfüggetlen, nyílt forráskódú, felületalapú, a parancs eszköz létrehozása és kezelése a Microsoft Azure erőforrásait. A CLI ezen verziójának kell csak a klasszikus üzemi modellben használható.

## <a name="azure-cli"></a>Azure CLI
A [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-az-cli2) egy platformfüggetlen, nyílt forráskódú, felületalapú, a parancs eszköz létrehozása és kezelése a Microsoft Azure erőforrásait.


## <a name="azure-iot-device-sdks"></a>Az Azure IoT eszközoldali SDK-k
Nincsenek _eszközoldali SDK-k_ érhető el, amelyek lehetővé teszik, hogy hozzon létre több nyelven [eszközalkalmazások](#device-app) , amely az IoT hub-interakciót. Az IoT Hub oktatóanyagok bemutatják, hogyan ezek eszközoldali SDK-k használatához. A Forráskód és a további információkat az eszköz SDK-k a Githubon találhatja [tárház](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-service-sdks"></a>Azure IoT service SDKs
Nincsenek _szolgáltatási SDK-k_ érhető el, amelyek lehetővé teszik, hogy hozzon létre több nyelven [háttéralkalmazásokat](#back-end-app) , amely az IoT hub-interakciót. Az IoT Hub oktatóanyagok bemutatják, hogyan ezen szolgáltatási SDK-k használatához. A Forráskód és a szolgáltatási SDK-k kapcsolatos további információkat a Githubon találhatja [tárház](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-tools"></a>Azure IoT-eszközök
A [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) egy platformfüggetlen, nyílt forráskódú Visual Studio Code bővítmény, amely segít kezelése az Azure IoT Hub és az eszközök a VS Code-ban. Az Azure IoT-eszközök IoT a fejlesztők egyszerűen fejleszteni IoT-projektet a VS Code-ban.

## <a name="azure-portal"></a>Azure Portal
A [Microsoft Azure-portálon](https://portal.azure.com) egy központi felület, amelyen üzembe helyezése és kezelése az Azure-erőforrások. A tartalom használatával szervezi _panelek_.

## <a name="azure-powershell"></a>Azure PowerShell
[Az Azure PowerShell](/powershell/azure/overview) segítségével kezelheti az Azure Windows PowerShell-parancsmagjainak gyűjteménye. A parancsmagok használatával hozzon létre, tesztelhetők, üzembe helyezése és kezelése az Azure platformon keresztül szolgáltatott megoldások és szolgáltatások.

## <a name="azure-resource-manager"></a>Azure Resource Manager
[Az Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) lehetővé teszi, hogy az erőforrásokat a megoldás egy csoportként dolgozzon. Telepíteni, frissíteni vagy törölni az erőforrásokat a megoldás egyetlen, koordinált műveletben.

## <a name="azure-service-bus"></a>Azure Service Bus
[A Service Bus](../service-bus/index.md) biztosít a felhőalapú vállalati üzenetkezeléssel és továbbítón keresztüli kommunikációt, amely segítséget nyújt a helyszíni megoldások csatlakoztatása a felhő segítségével. Egy IoT Hub oktatóanyagokat, győződjön meg a Service Bus [üzenetsorok](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="azure-storage"></a>Azure Storage
[Az Azure Storage](../storage/common/storage-introduction.md) egy felhőalapú tárolási megoldás. Ez magában foglalja a Blob Storage szolgáltatás strukturálatlan adatok tárolására használható. Egyes IoT Hub oktatóanyagok a blob storage használata.

## <a name="back-end-app"></a>Háttéralkalmazás
Kontextusában [az IoT Hub](#iot-hub), a háttéralkalmazás egy, a szolgáltatás felé néző végpontok egy IoT hubhoz csatlakozó alkalmazást. Például egy háttéralkalmazás kérelmeihez [eszközről a felhőbe](#device-to-cloud) üzeneteket, vagy kezelheti a [eszközidentitás-jegyzék](#identity-registry). Általában egy háttér-alkalmazást a felhőben fut, de sok oktatóanyagok a háttér-alkalmazások a helyi fejlesztői gépen futó konzolalkalmazással.

## <a name="built-in-endpoints"></a>Beépített végpontok
Minden IoT hub tartalmaz egy beépített [végpont](iot-hub-devguide-endpoints.md) , amely Event Hub-kompatibilis. Minden olyan mechanizmus, amely együttműködik a erről a végpontról eszköz a felhőbe irányuló üzenetek olvasásához az Event Hubs is használhatja.

## <a name="cloud-gateway"></a>Átjáró
Egy felhőátjáróhoz lehetővé teszi, hogy a kapcsolatok közvetlenül a nem csatlakozó eszközökön [az IoT Hub](#iot-hub). A felhőátjáró ellentétben a felhőben üzemeltetett egy [helyszíni átjáró](#field-gateway) , futtató helyi az eszközökön. Felhőalapú átjáró egy tipikus használati eset, hogy az eszközök protokollfordítás végrehajtása.

## <a name="cloud-to-device"></a>Felhőből-az-eszközre
Egy csatlakoztatott eszköznek az IoT hubról küldött üzenetek hivatkozik. Gyakran előfordul ezeket az üzeneteket olyan parancsok, melyek arra utasítják az eszköz egy műveletet. További információkért lásd: [küldhet és fogadhat üzeneteket az IoT Hub](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Konfiguráció
Kontextusában [automatikus eszközkonfiguráció](iot-hub-auto-device-config.md), belül az IoT Hub-konfiguráció kívánt beállításait határozza meg, az eszközök ikerállapotának és biztosít mérőszámokat jelentés állapotának és előrehaladtának.

## <a name="connection-string"></a>Kapcsolati sztring
Kapcsolati karakterláncok az alkalmazás kódjában, hogy magába foglalja a végpont való kapcsolódáshoz szükséges adatokat használhatja. A kapcsolati karakterláncok általában tartalmazza az a végpont és a biztonsági adatokat, de a kapcsolati karakterlánc-formátumok eltérőek lehetnek a különböző szolgáltatások címét. Kapcsolati karakterlánc az IoT Hub szolgáltatáshoz tartozó két típusa van:
- *Eszköz kapcsolati karakterláncok* eszközök csatlakoztatása az IoT hub eszköz által használt végpontok engedélyezése.
- *Az IoT Hub kapcsolati karakterláncok* háttér-alkalmazások, a szolgáltatás felé néző végpontok egy IoT hubhoz való kapcsolódáshoz.

## <a name="custom-endpoints"></a>Egyéni végpontok
Létrehozhat egyéni [végpontok](iot-hub-devguide-endpoints.md) kézbesíti az által küldött üzeneteket egy IoT hubhoz a [útválasztási szabály](#routing-rules). Egyéni végpontok közvetlenül csatlakozhat egy eseményközpontba, a Service Bus-üzenetsor vagy Service Bus-témakörbe.

## <a name="custom-gateway"></a>Egyéni átjáró
Az átjáró lehetővé teszi, hogy a kapcsolatok közvetlenül a nem csatlakozó eszközökön [az IoT Hub](#iot-hub). Az Azure IoT Edge segítségével képes kezelni az üzeneteket, egyéni protokoll átalakítások, más az Edge feldolgozás egyéni logikát megvalósító egyéni átjárók készíthetők.

## <a name="data-point-message"></a>Adatpont üzenet
Adatpont üzenetet egy [eszközről a felhőbe](#device-to-cloud) tartalmazó üzenetet [telemetriai](#telemetry) adatok, például a szél sebessége vagy hőmérséklet.

## <a name="desired-configuration"></a>Szükséges konfiguráció
Kontextusában egy [ikereszköz](iot-hub-devguide-device-twins.md), kívánt tulajdonságok és metaadatok szinkronizálni kell az eszköz az ikereszköz teljes készletének konfigurációs hivatkozik.

## <a name="desired-properties"></a>Kívánt tulajdonságok
Kontextusában egy [ikereszköz](iot-hub-devguide-device-twins.md), kívánt tulajdonságok egy alszakaszban az ikereszközök használt [jelentett tulajdonságokként](#reported-properties) eszközkonfiguráció vagy az állapot szinkronizálásához. Kívánt tulajdonságok csak akkor állítható egy [háttéralkalmazás](#back-end-app) és betartják a [eszközalkalmazás](#device-app).

## <a name="device-to-cloud"></a>Eszközről-a-felhőbe
Hivatkozik az a csatlakoztatott eszközökről küldött üzeneteket [az IoT Hub](#iot-hub). Lehet, hogy ezek az üzenetek [adatpont](#data-point-message) vagy [interaktív](#interactive-message) üzeneteket. További információkért lásd: [küldhet és fogadhat üzeneteket az IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device"></a>Eszköz
IoT kontextusában egy általában kisebb, különálló számítási eszközről, amely adatokat gyűjteni, vagy más eszközök szabályozásának. Egy eszköz lehet például egy környezeti monitorozási eszköz vagy egy tartományvezérlő üvegházhatású a kezelésre és szellőzőrendszerek rendszerekhez. A [eszközkatalógus](https://catalog.azureiotsolutions.com/) működőképesnek a hardvereszközök listája [az IoT Hub](#iot-hub).

## <a name="device-app"></a>Eszközalkalmazás
Egy eszköz-alkalmazást futtat az [eszköz](#device) és kezeli a szolgáltatással való kommunikációhoz a [az IoT hub](#iot-hub). Általában valamelyikét használja a [Azure IoT eszközoldali SDK-k](#azure-iot-device-sdks) mikor meg, hogy egy eszköz alkalmazást. Az IoT-oktatóanyagok számos használhatja egy [szimulált eszköz](#simulated-device) kényelmi célokat szolgál.

## <a name="device-condition"></a>Eszköz feltétel
Által jelentett Eszközállapot-adatokat, a kapcsolat jelenleg használt módszer, például hivatkozik egy [eszközalkalmazás](#device-app). [Eszközalkalmazások](#device-app) képességeikkel is jelentheti. Feltétel és a képességek információkat az ikereszközök használatával lekérdezheti.

## <a name="device-data"></a>Eszközadatok
Eszközadatok hivatkozik az eszközönkénti adatok tárolva az IoT Hub [eszközidentitás-jegyzék](#identity-registry). Ezek az adatok exportálására és importálásra lehetőség.

## <a name="device-explorer"></a>Eszközkereső
A [device explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) olyan eszköz, amely Windows fut, és lehetővé teszi az eszközök kezelését a [eszközidentitás-jegyzék](#identity-registry). Az eszköz is küldhet és fogadhat üzeneteket az eszközökön.

## <a name="device-identity"></a>Eszközidentitás
Az eszközidentitást hozzárendelve minden regisztrált eszköz egyedi azonosítója a [eszközidentitás-jegyzék](#identity-registry).

## <a name="device-management"></a>Eszközfelügyelet
Eszközfelügyelet magában foglalja a teljes életciklusát társított az IoT-megoldások, beleértve a tervezési, kiépítési, konfigurálását, figyelését és kivonása az eszközöknek a kezelésére.

## <a name="device-management-patterns"></a>Eszközfelügyeleti minták
[Az IoT hub](#iot-hub) például újraindítás, gyári alaphelyzetbe végrehajtása és végrehajtott belsővezérlőprogram-frissítéseket az eszközök az általános eszközfelügyeleti minták lehetővé teszi.

## <a name="device-rest-api"></a>Device REST API
Használhatja a [eszköz REST API-val](https://docs.microsoft.com/rest/api/iothub/device) eszköz – felhő üzeneteket küldhet egy IoT hubot, és a egy eszközről [felhőből az eszközre](#cloud-to-device) üzeneteket egy IoT-központból. Jellemzően kell használni a magasabb szintű egyik [eszközoldali SDK-k](#azure-iot-device-sdks) , ahogyan az az IoT Hub oktatóanyagok.

## <a name="device-provisioning"></a>Eszköz kiépítése
A kezdeti hozzáadása során a rendszer eszközkiépítési [eszközadatok](#device-data) a megoldás-áruházak. Ahhoz, hogy az új eszköz a hubhoz való csatlakozáshoz, hozzá kell adnia egy Eszközazonosítót és a kulcsok az IoT Hub [eszközidentitás-jegyzék](#identity-registry). A kiépítési folyamat részeként szükség lehet inicializálni az eszközre vonatkozó adatokat más megoldás tárolja.

## <a name="device-twin"></a>Ikereszközök
A [ikereszköz](iot-hub-devguide-device-twins.md) JSON-dokumentum, amely tárolja az eszközök állapotinformációit, például a metaadatokat, konfigurációkat és állapotokat. [Az IoT Hub](#iot-hub) az IoT hubban kiosztott minden egyes eszközhöz egy ikereszközt. Ikereszközök engedélyezése való szinkronizálását, [eszköz feltételek](#device-condition) és a háttéralkalmazás az eszköz és a megoldás közötti konfigurációkat. Keresse meg az adott eszközöket és a hosszú ideig futó műveletek állapotának lekérdezése ikereszközök lekérdezheti.

## <a name="direct-method"></a>Közvetlen módszer
A [közvetlen metódus](iot-hub-devguide-direct-methods.md) megoldást nyújt, hogy egy metódus-trigger által az IoT hub API meghívása egy eszközön végrehajtani.

## <a name="endpoint"></a>Végpont
Az IoT hub tesz több [végpontok](iot-hub-devguide-endpoints.md) , amely engedélyezi az alkalmazások, az IoT hubhoz való csatlakozáshoz. Nincsenek az eszköz felé néző végpontok, amelyek lehetővé teszik az eszközök, például a küldő műveletek végrehajtásához [eszközről a felhőbe](#device-to-cloud) üzenetek és a fogadó [felhőből az eszközre](#cloud-to-device) üzeneteket. Nincsenek a szolgáltatás által használt felügyeleti végpontok, amelyek lehetővé teszik [háttéralkalmazásokat](#back-end-app) műveletek végrehajtásához [eszközidentitás](#device-identity) felügyeleti és ikereszköz kezelése. Nincsenek a szolgáltatás által használt [beépített végpontokról](#built-in-endpoints) eszközt a felhőbe irányuló üzenetek olvasásához. Létrehozhat [egyéni végpontok](#custom-endpoints) által az eszköz a felhőbe irányuló üzenetek fogadása egy [útválasztási szabály](#routing-rules).

## <a name="event-hubs-service"></a>Event Hubs service
[Az Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) van egy kiválóan méretezhető adatbefogadási szolgáltatás, amely több millió fogadására képes az események száma másodpercenként. A szolgáltatás lehetővé teszi, hogy a csatlakoztatott eszközök és alkalmazások által létrehozott hatalmas adatmennyiségek elemzését. Az IoT Hub szolgáltatással összehasonlításáért lásd: [összehasonlítása az Azure IoT Hub és az Azure Event Hubs](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Event Hubs-kompatibilis végpont
Olvassa el a [eszközről a felhőbe](#device-to-cloud) küldi az üzeneteket az IoT hub a hub végpont csatlakozni, és bármely Event Hub-kompatibilis módszer használatával olvassa el ezeket az üzeneteket. Event Hub-kompatibilis módszerek közé tartozik a használatával a [Event Hubs SDK-k](../event-hubs/event-hubs-programming-guide.md) és [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Helyszíni átjáró
Helyszíni átjáró lehetővé teszi, hogy a kapcsolatok közvetlenül a nem csatlakozó eszközökön [az IoT Hub](#iot-hub) , és általában telepítve helyben, az eszközökkel. További információkért lásd: [Mi az Azure IoT Hub?](about-iot-hub.md)

## <a name="free-account"></a>Ingyenes fiók
Létrehozhat egy [ingyenes Azure-fiók](https://azure.microsoft.com/pricing/free-trial/) végezze el az IoT Hub-oktatóanyagok, és kísérletezzen az IoT Hub szolgáltatásból (és más Azure-szolgáltatásokhoz).

## <a name="gateway"></a>Átjáró
Az átjáró lehetővé teszi, hogy a kapcsolatok közvetlenül a nem csatlakozó eszközökön [az IoT Hub](#iot-hub). Lásd még: [átjáró mező](#field-gateway), [átjáró Felhőbeli](#cloud-gateway), és [egyéni átjáró](#custom-gateway).

## <a name="identity-registry"></a>Eszközidentitás-jegyzék
A [eszközidentitás-jegyzék](iot-hub-devguide-identity-registry.md) egy IoT hubot, amely az egyes eszközökre vonatkozó adatokat tárolja a beépített összetevője egy IoT hubhoz való csatlakozáshoz engedélyezett.

## <a name="interactive-message"></a>Interaktív üzenet
Egy interaktív az üzenet egy [felhőből az eszközre](#cloud-to-device) üzenet, amely a megoldás háttérrendszere azonnali műveletet aktivál. Például egy eszköz küldhet egy hibával, érdemes lehet automatikusan bejelentkezett egy CRM-rendszerrel kapcsolatos riasztást küld.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub
Az IoT Hub egy teljes körűen felügyelt Azure-szolgáltatás, amely megbízható és biztonságos kétirányú kommunikációt tesz lehetővé több millió eszköz között, és a megoldás háttérrendszere. További információkért lásd: [Mi az Azure IoT Hub?](about-iot-hub.md) Használatával a [Azure-előfizetés](#subscription), IoT hub az üzenetküldési feladatokhoz IoT kezelésére is létrehozhat.

## <a name="iot-hub-metrics"></a>IoT Hub metrics
[Az IoT Hub-metrikák](iot-hub-metrics.md) az IoT hubra állapotával kapcsolatos adatokat biztosítanak a [Azure-előfizetés](#subscription). Az IoT Hub-metrikák lehetővé teszi felmérheti a szolgáltatás és a hozzá csatlakozó eszközök általános állapotát. Az IoT Hub mérőszámok segítségével tekintse meg, mi történik az IoT hubbal és a kiváltó problémák kivizsgálásában anélkül, hogy forduljon az Azure ügyfélszolgálatához.

## <a name="iot-hub-query-language"></a>IoT Hub lekérdezési nyelv
A [IoT Hub lekérdezési nyelv](iot-hub-devguide-query-language.md) egy SQL-szerű nyelv, amely lehetővé teszi, hogy lekérdezést a [](#job) és ikereszközök.

## <a name="iot-hub-resource-rest-api"></a>IoT Hub Resource REST API
Használhatja a [IoT Hub erőforrás REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource) kezelheti az IoT hubra a [Azure-előfizetés](#subscription) például létrehozása, frissítése és törlése hubs műveleteket végez.

## <a name="iot-solution-accelerators"></a>IoT-megoldásgyorsítók
Az Azure IoT-megoldásgyorsítók csomagot több Azure-szolgáltatások megoldásokban. Ezek a megoldások gyors használatbavétele az általános IoT-forgatókönyveket teljes körű megvalósítását teszi lehetővé. További információkért lásd: [Mik az Azure IoT-megoldásgyorsítók?](../iot-accelerators/about-iot-accelerators.md)

## <a name="the-iot-extension-for-azure-cli"></a>Az IoT-bővítmény, az Azure CLI-vel 
[Az Azure CLI-vel az IoT-bővítmény](https://github.com/Azure/azure-iot-cli-extension) többplatformos, a parancssori eszköz. Az eszköz lehetővé teszi az eszközök kezelését a [eszközidentitás-jegyzék](#identity-registry), küldéséhez és üzenetek és fájlok fogadjon az eszközökről és az IoT hub-műveletek monitorozása.

## <a name="job"></a>Feladat
A megoldás háttérrendszere [feladatok](iot-hub-devguide-jobs.md) ütemezéséhez és az eszközök regisztrálása az IoT hubbal a tevékenységek nyomon követésére. Tevékenységek közé tartozik az eszköz frissítését [kívánt tulajdonságok](#desired-properties), frissítési ikereszköz [címkék](#tags), és a program invoking [közvetlen metódusok](#direct-method). [Az IoT Hub](#iot-hub) is használ az [való importálása és exportálása](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) származó a [eszközidentitás-jegyzék](#identity-registry).

## <a name="modules"></a>Modulok
Az eszköz oldalán, az IoT Hub eszközoldali SDK-k engedélyezése, hogy hozzon létre [modulok](iot-hub-devguide-module-twins.md) ahol mindegyik megnyílik egy független az IoT Hub-kapcsolat. Ez a funkció lehetővé teszi, hogy az önálló névterek különböző összetevőket az eszközön.

A modul identitás- és ikermodul adja meg ugyanazokat a lehetőségeket, mint [eszközidentitás](#device-identity) és [ikereszköz](#device-twin) , de nagyobb pontossággal. A kifinomultabb granularitási lehetővé teszi a kompatibilis eszközök, például az operációs rendszer-alapú eszközök vagy a belső vezérlőprogrammal rendelkező eszközök kezelése több összetevőből állnak, konfigurációja és az egyes összetevők feltételek elkülönítésére.

## <a name="module-identity"></a>A modul identitás
A modul identitás minden eszközhöz tartozó modul rendelt egyedi azonosítója. Modul identitás is regisztrálva van a [eszközidentitás-jegyzék](#identity-registry).

## <a name="module-twin"></a>Ikermodul
Ikereszköz hasonlóan egy ikermodul is JSON-dokumentumot, például a metaadatokat, konfigurációkat és állapotokat modul állapot adatait tárolja. Az IoT Hub továbbra is fennáll, egy ikermodul alatt egy új eszközidentitást az IoT hubban kiosztott minden egyes modul-identitás. Ikermodulokkal lehetővé szinkronizálják a modul feltételeket és a konfigurációk a modul és a megoldás háttérrendszere között. Keresse meg a modulokról, és a hosszú ideig futó műveletek állapotának lekérdezése ikermodulokkal lekérdezheti.

## <a name="mqtt"></a>MQTT
[MQTT](http://mqtt.org/) van, amelyek közül az üzenetküldés van protokollokon [az IoT Hub](#iot-hub) támogatja a eszközökkel való kommunikációhoz. Az üzenetküldési protokollokat, amely az IoT Hub által támogatott kapcsolatos további információkért lásd: [küldhet és fogadhat üzeneteket az IoT Hub](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Műveletek figyelése
Az IoT Hub [műveletek figyelése](iot-hub-operations-monitoring.md) lehetővé teszi az IoT hub valós idejű műveleti állapotának figyelése. [Az IoT Hub](#iot-hub) események nyomon követi a műveletek számos kategóriájában. Mekkorák egy vagy több kategóriához eseményeket küldeni egy IoT Hub-végponton feldolgozás céljából. Adatok minták alapján összetettebb feldolgozási beállítása, illetve figyelheti az adatait a hibákat.

## <a name="physical-device"></a>Fizikai eszköz
Fizikai eszköz, amely csatlakozik az IoT hub Raspberry Pi például valós eszköz. Az IoT Hub oktatóanyagok számos használja az egyszerűség kedvéért [szimulált eszközök](#simulated-device) ahhoz, hogy a minták futtatása a helyi gépen.

## <a name="primary-and-secondary-keys"></a>Az elsődleges és másodlagos kulcsok
Egy eszköz- vagy service néző végpont egy IoT hubhoz való csatlakozáskor a [kapcsolati karakterlánc](#connection-string) hozzáférési kulcsot tartalmaz. Amikor hozzáad egy eszközt, hogy a [eszközidentitás-jegyzék](#identity-registry) , vagy adja hozzá a [megosztott hozzáférési szabályzat](#shared-access-policy) a hubon, a szolgáltatás létrehoz egy elsődleges és másodlagos kulcsot. Két kulcs lehetővé teszi, hogy vihetők át az egyik kulcsról a másikra az IoT hubon való hozzáférés elvesztése nélkül a kulcs frissítésekor.

## <a name="protocol-gateway"></a>Protokoll-átjáró
A protokoll-átjáró általában a felhőben telepített, és a kapcsolódó eszközök fordítási szolgáltatásokat nyújt a protokoll [az IoT Hub](#iot-hub). További információkért lásd: [Mi az Azure IoT Hub?](about-iot-hub.md)

## <a name="quotas-and-throttling"></a>Kvóták és szabályozás
Nincsenek a különböző [kvóták](iot-hub-devguide-quotas-throttling.md) használatát, amely érvényes [az IoT Hub](#iot-hub), számos, a kvótákat a réteg az IoT hub függően változnak. [Az IoT Hub](#iot-hub) is érvényes [szabályozza](iot-hub-devguide-quotas-throttling.md) futási időben a szolgáltatás használatára.

## <a name="reported-configuration"></a>Jelentett konfiguráció
Kontextusában egy [ikereszköz](iot-hub-devguide-device-twins.md), jelentett tulajdonságok és metaadatok kell jelenteni, hogy a megoldás háttérrendszere ikereszköz teljes készletének konfigurációs hivatkozik.

## <a name="reported-properties"></a>Jelentett tulajdonságok
Kontextusában egy [ikereszköz](iot-hub-devguide-device-twins.md), jelentett tulajdonságok egy alszakaszban az ikereszközök együttes [kívánt tulajdonságok](#desired-properties) eszközkonfiguráció vagy az állapot szinkronizálásához. Jelentett tulajdonságokat csak állítható be a [eszközalkalmazás](#device-app) és olvashatók és kellettek egy [háttéralkalmazás](#back-end-app).

## <a name="resource-group"></a>Erőforráscsoport
[Az Azure Resource Manager](#azure-resource-manager) erőforráscsoportokat használ a kapcsolódó erőforrások csoportosítása. Egy erőforráscsoport műveletek végrehajtása az összes erőforrást a csoportban egyszerre használhatja.

## <a name="retry-policy"></a>Újrapróbálkozási szabályzat
Ez az újrapróbálkozási szabályzat kezeléséhez használja [átmeneti hibák](/azure/architecture/best-practices/transient-faults) felhőalapú szolgáltatásként való kapcsolódáskor.

## <a name="routing-rules"></a>Útválasztási szabályok
Konfigurálja [útválasztási szabályok](iot-hub-devguide-messages-read-custom.md) irányíthatja az eszköz a felhőbe irányuló üzeneteket az IoT hub egy [beépített végpont](#built-in-endpoints) vagy [egyéni végpontok](#custom-endpoints) a megoldás háttérrendszerén általi feldolgozáshoz .

## <a name="sasl-plain"></a>SASL EGYSZERŰ
EGYSZERŰ SASL protokoll, amely az AMQP protokollt használ a biztonsági jogkivonatokat átviteléhez.

## <a name="service-rest-api"></a>Szolgáltatás REST API-ja
Használhatja a [szolgáltatás REST API-ja](https://docs.microsoft.com/rest/api/iothub/service) a megoldás a háttéralkalmazás az eszközök kezeléséhez. Az API lehetővé teszi a lekérésére és frissítésére [ikereszköz](#device-twin) tulajdonságok meghívása [közvetlen metódusok](#direct-method), és az ütemezés [feladatok](#job). Jellemzően kell használni a magasabb szintű egyik [szolgáltatási SDK-k](#azure-iot-service-sdks) , ahogyan az az IoT Hub oktatóanyagok.

## <a name="shared-access-signature"></a>Közös hozzáférésű jogosultságkód
A közös hozzáférésű Jogosultságkódok (SAS) olyan hitelesítési mechanizmust, SHA-256 biztonságos kivonatok vagy URI-k alapján. SAS hitelesítési két részből áll: egy _megosztott elérési házirendet_ és a egy _közös hozzáférésű Jogosultságkód_ (más néven a jogkivonat). Egy eszköz SAS az IoT hub-hitelesítést használ. [Háttér-alkalmazások](#back-end-app) is használhatja az SAS hitelesítéshez a szolgáltatás felé néző végpontok egy IoT hubhoz. Általában a SAS-tokent az közé tartozik a [kapcsolati karakterlánc](#connection-string) , hogy egy alkalmazás használja az IoT hub-kapcsolatot létesíteni.

## <a name="shared-access-policy"></a>Megosztott elérési házirend
Egy megosztott elérési házirend határozza meg az engedélyeket, bárki, aki rendelkezik egy érvényes [elsődleges vagy másodlagos kulcsot](#primary-and-secondary-keys) adott szabályzathoz társított. A megosztott elérési házirendeket és a kulcsokat kezelheti, a központ az a [portál](#azure-portal).

## <a name="simulated-device"></a>Szimulált eszköz
Az egyszerűség kedvéért ahhoz, hogy a helyi gépen minták futtatása az IoT Hub oktatóanyagok számos használja a szimulált eszközök. Ezzel szemben egy [fizikai eszköz](#physical-device) egy igazi eszközön, például az IoT hubhoz csatlakozó Raspberry Pi van.

## <a name="solution"></a>Megoldás
A _megoldás_ olvassa el a Visual Studio-megoldás, amely egy vagy több projektet tartalmaz. A _megoldás_ is utalhat IoT-megoldás, amely tartalmazza az eszközök, például [eszközalkalmazások](#device-app), egy IoT hubot, más Azure-szolgáltatásokhoz és [háttéralkalmazásokat](#back-end-app).

## <a name="subscription"></a>Előfizetés
Azure-előfizetéssel, ha a számlázási történik. Egyes Azure-erőforrások létrehozása, vagy használhatja az Azure szolgáltatás egyetlen előfizetéssel társítva. Számos kvóták is vonatkoznak az előfizetés szintjén.

## <a name="system-properties"></a>Rendszertulajdonságok
Kontextusában egy [ikereszköz](iot-hub-devguide-device-twins.md), Rendszertulajdonságok csak olvashatók, és az eszköz használata, például a legutóbbi tevékenység időt és a kapcsolati állapot kapcsolatos információkat tartalmazzák.

## <a name="tags"></a>Címkék
Kontextusában egy [ikereszköz](iot-hub-devguide-device-twins.md), a címkék olyan eszköz metaadatait tárolja, és a megoldás háttérrendszere JSON-dokumentumok formájában által lekérése. Címkék nem láthatók el alkalmazásokat az eszközön.

## <a name="telemetry"></a>Telemetria
Eszközök például a szél sebessége vagy a hőmérsékletet, a telemetriai adatok gyűjtése, és adatpont üzenetek segítségével a telemetriát küldjön az IoT hubra.

## <a name="token-service"></a>Jogkivonat-szolgáltatás
Egy jogkivonat-szolgáltatás segítségével az eszközök hitelesítési mechanizmust. Használja az IoT Hub [megosztott hozzáférési szabályzat](#shared-access-policy) a **DeviceConnect** létrehozásához szükséges engedélyek *eszköz hatókörű* jogkivonatokat. Ezek a jogkivonatok csatlakozni az IoT hub eszköz engedélyezése. Egy eszköz egy egyéni hitelesítési mechanizmust használja a hitelesítéshez a jogkivonat-szolgáltatás. Ha az eszköz sikeresen hitelesíti magát, a jogkivonat-szolgáltatás kiad egy SAS-jogkivonatot az eszköz hozzáférhet az IoT hub.

## <a name="twin-queries"></a>Ikereszköz-lekérdezések
[Eszköz- és modul ikereszköz-lekérdezések](iot-hub-devguide-query-language.md) használja az SQL-szerű IoT Hub lekérdezési nyelv az ikereszközökhöz vagy ikermodulokkal lévő információk lekéréséhez. Az ugyanazon az IoT Hub lekérdezési nyelv használatával kapcsolatos információk lekéréséhez [](#job) futtatása az IoT hubban.

## <a name="twin-synchronization"></a>Ikereszköz-szinkronizálás
Ikereszköz szinkronizálást használ a [kívánt tulajdonságok](#desired-properties) az ikereszközök vagy ikermodulokkal az eszközök vagy a modulok beállítása és lekérése [jelentett tulajdonságokként](#reported-properties) ezekből az ikereszköz tárolja.

## <a name="x509-client-certificate"></a>X.509-ügyféltanúsítvány
Egy eszköz is egy X.509 tanúsítvány-hitelesítésre használható [az IoT Hub](#iot-hub). Egy X.509 tanúsítvány használata helyett egy [SAS-jogkivonat](#shared-access-signature).
