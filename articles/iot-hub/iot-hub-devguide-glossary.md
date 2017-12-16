---
title: "Az Azure IoT Hub szószedet |} Microsoft Docs"
description: "Fejlesztői útmutató - Azure IoT Hub kapcsolatos gyakori kifejezések."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 16ef29ea-a185-48c3-ba13-329325dc6716
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 406fd095896e2c00920555d3dfce1b5c2ae7fca7
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="glossary-of-iot-hub-terms"></a>Az IoT-központ szószedet
Ez a cikk az általános kifejezés, amely az IoT-központ cikkeket mutatja be.

## <a name="advanced-message-queueing-protocol"></a>Speciális üzenet-Várólistázást protokoll
[Üzenet-Várólistázást protokoll (AMQP) speciális](https://www.amqp.org/) van egy, az az üzenetküldési protokollok, amelyek [IoT-központ](#iot-hub) eszközökkel való kommunikáció támogatja. Amely támogatja az IoT-központ az üzenetküldési protokollokkal kapcsolatos további információkért lásd: [üzeneteket küldjön és fogadjon IoT hubbal](iot-hub-devguide-messaging.md).

## <a name="azure-cli"></a>Azure CLI
A [Azure CLI](../cli-install-nodejs.md) platformfüggetlen, nyílt forráskódú, rendszerhéj-alapú, a parancs eszköz létrehozására és kezelésére a Microsoft Azure erőforrásait. Ebben a parancssori felület a Node.js segítségével van megvalósítva.

## <a name="azure-cli-20"></a>Azure CLI 2.0
A [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) platformfüggetlen, nyílt forráskódú, rendszerhéj-alapú, a parancs eszköz létrehozására és kezelésére a Microsoft Azure erőforrásait. Ez a parancssori felület előzetes verzióját a Python segítségével van megvalósítva.


## <a name="azure-iot-device-sdks"></a>Az Azure SDK-k IoT-eszközök
Nincsenek _eszköz SDK-k_ érhető el, amelyek lehetővé teszik, hogy hozzon létre több nyelven [eszköz alkalmazások](#device-app) , amely kommunikálni az IoT-központ. Az IoT-központ oktatóanyagok bemutatják a ezek eszközoldali SDK-k használata. A Forráskód és SDK-k eszközzel kapcsolatos további információk találhatók a Githubon [tárház](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-service-sdks"></a>Az Azure IoT szolgáltatás SDK-k
Nincsenek _SDK szolgáltatás_ érhető el, amelyek lehetővé teszik, hogy hozzon létre több nyelven [háttér-alkalmazások](#back-end-app) , amely kommunikálni az IoT-központ. Az IoT-központ oktatóanyagok bemutatják a használja a szolgáltatás SDK-IT. A Forráskód és a szolgáltatás SDK-kkal kapcsolatos további információkat talál a Githubon [tárház](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-portal"></a>Azure Portal
A [Microsoft Azure-portálon](https://portal.azure.com) a központi hely, ahol kioszthatja és az Azure erőforrások kezeléséhez. A tartalom használatával szervezi _paneleken_.

## <a name="azure-powershell"></a>Azure PowerShell
[Az Azure PowerShell](/powershell/azure/overview) parancsmagok segítségével kezelheti a Windows PowerShell-lel Azure gyűjteménye. A parancsmagok használatával hozzon létre, tesztelhetők, telepítheti, és a megoldások és az Azure platformon keresztül továbbítva szolgáltatások kezelésére.

## <a name="azure-resource-manager"></a>Azure Resource Manager
[Az Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) lehetővé teszi, hogy az alkalmazásában lévő erőforrásokat csoportként a megoldásban. Telepítéséhez, frissítéséhez, vagy törli az erőforrást a megoldás egyetlen, koordinált műveletben.

## <a name="azure-service-bus"></a>Azure Service Bus
[A Service Bus](../service-bus/index.md) biztosít a felhő engedélyezve van a vállalati üzenetkezelési és továbbítón keresztüli kommunikációt, amely segít a helyszíni megoldásokkal csatlakozzon a felhővel. Az IoT-központ oktatóprogramok ellenőrizze a Service busszal használni [várólisták](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="azure-storage"></a>Azure Storage
[Az Azure Storage](../storage/common/storage-introduction.md) egy felhőalapú tárolómegoldás. Ez magában foglalja a Blob Storage szolgáltatás strukturálatlan adatok tárolására használható. Az IoT-központ oktatóprogramok a blob storage használata.

## <a name="back-end-app"></a>Háttér-alkalmazás
A környezetében [IoT-központ](#iot-hub), egy háttér-alkalmazás egy alkalmazást, amely a szolgáltatás felé néző végpontok az IoT-központ egyik kapcsolódik. Például előfordulhat, hogy lekér egy háttér-alkalmazás [eszközről a felhőbe](#device-to-cloud)üzenetek, vagy kezelje a [identitásjegyzékhez](#identity-registry). Általában egy háttér-alkalmazás a felhőben fut, de számos olyan az oktatóanyagok a háttér-alkalmazások a helyi fejlesztési számítógépén futó konzol alkalmazásokra.

## <a name="built-in-endpoints"></a>Beépített végpontok
Minden IoT-központ tartalmazza a beépített [végpont](iot-hub-devguide-endpoints.md) , amely Event Hub-kompatibilis. Bármely mechanizmus, amely az eszköz a felhőbe küldött üzeneteket beolvasni a végpont az Event Hubs is használhatja.

## <a name="cloud-gateway"></a>Átjáró
Egy felhőátjáróhoz lehetővé teszi, hogy a kapcsolatot a eszközöket, amelyek közvetlenül nem kapcsolódnak [IoT-központ](#iot-hub). Egy felhőátjáróhoz tárolódik a felhőben kal ellentétben a egy [mező átjáró](#field-gateway) helyi futtat az eszközökön. Egy tipikus használati eset felhő átjáróra, hogy az eszközök protokollfordításhoz alkalmazza.

## <a name="cloud-to-device"></a>Felhőből-az-eszközre
Az IoT-központ a csatlakoztatott eszközre küldött üzenetek hivatkozik. Gyakran ezek az üzenetek nem parancsok, melyek arra utasítják az eszköz műveletet. További információkért lásd: [üzeneteket küldjön és fogadjon IoT hubbal](iot-hub-devguide-messaging.md).

## <a name="connection-string"></a>Kapcsolati karakterlánc
Segítségével kapcsolati karakterláncokat az alkalmazás kódjában foglalják magukban a végpont való kapcsolódáshoz szükséges adatokat. A kapcsolati karakterlánc többek között a végpont és a biztonsági adatokat, de a kapcsolati karakterlánc formátumok eltérő szolgáltatásban címét. A kapcsolati karakterlánc az IoT-központ szolgáltatáshoz tartozó két típusa van:
- *Eszköz kapcsolati karakterláncok* eszközök csatlakoztatása az IoT-központ az eszköz felé néző végpontok engedélyezése.
- *Az IoT-központ kapcsolati karakterláncok* háttér-alkalmazások csatlakozni a az IoT-központ szolgáltatás felé néző végpontok engedélyezése.

## <a name="custom-endpoints"></a>Egyéni végpontokat
Létrehozhat egyéni [végpontok](iot-hub-devguide-endpoints.md) az IoT-központ által küldött üzeneteket a egy [útválasztási szabály](#routing-rules). Egyéni végpontokat Csatlakozás közvetlenül egy eseményközpontot, egy Service Bus-üzenetsorba, vagy egy Service Bus-témakörbe.

## <a name="custom-gateway"></a>Egyéni átjáró
Átjáró lehetővé teszi, hogy a kapcsolatot a eszközöket, amelyek közvetlenül nem kapcsolódnak [IoT-központ](#iot-hub). Használhat [Azure IoT peremhálózati](#azure-iot-edge) hozhat létre egyéni átjárók, amelyek megvalósítják az egyéni logika üzenetek, az egyéni protokoll konverziót, illetve a más a peremhálózati terhelése kezelésére.

## <a name="data-point-message"></a>Adatpont üzenet
Adatpont üzenetet egy [eszközről a felhőbe](#device-to-cloud) tartalmazó üzenetet [telemetriai](#telemetry) adatok, például a szél sebesség vagy hőmérséklet.

## <a name="desired-configuration"></a>Szükséges Konfigurációkezelő
A környezetében egy [eszköz iker](iot-hub-devguide-device-twins.md), szükséges a konfigurációs tulajdonságok és az eszköz iker, amely az eszköz szinkronizálnia kell a metaadatok teljes készletének hivatkozik.

## <a name="desired-properties"></a>Kívánt tulajdonságai
Környezetében egy [eszköz iker](iot-hub-devguide-device-twins.md), a keresett tulajdonságok az egy alkalmazásra vonatkozó a használt eszköz iker [tulajdonságok jelentett](#reported-properties) eszközök konfigurálása és az állapot szinkronizálásához. Kívánt tulajdonságok csak állítható be egy [háttér-alkalmazás](#back-end-app) és betartják a [eszközalkalmazás](#device-app).

## <a name="device-to-cloud"></a>Eszközről-a-felhőbe
A csatlakoztatott eszközről küldött üzenetek hivatkozik [IoT-központ](#iot-hub). Lehet, hogy ezek az üzenetek [adatpont](#data-point-message) vagy [interaktív](#interactive-message) üzeneteket. További információkért lásd: [üzeneteket küldjön és fogadjon IoT hubbal](iot-hub-devguide-messaging.md).

## <a name="device"></a>Eszköz
IoT összefüggésében egy általában csak kevés számítógépet érintő, önálló számítástechnikai eszközről, amely adatokat gyűjteni, vagy más eszközök. Egy eszköz lehet például egy környezeti figyelési eszköz vagy egy tartományvezérlő üvegházhatású watering és szellőzőrendszerek rendszerhez. A [eszköz katalógus](https://catalog.azureiotsuite.com/) történő együttműködésre hitelesített hardvereszközök listája [IoT-központ](#iot-hub).

## <a name="device-app"></a>Eszköz alkalmazás
Egy eszköz-alkalmazás fut. a [eszköz](#device) és kezeli a szolgáltatással való kommunikációhoz a [IoT-központ](#iot-hub). Általában akkor használják, egy a [Azure IoT-eszközök SDK-k](#azure-iot-device-sdks) Ha egy eszköz alkalmazás üzembe helyezése. Az IoT-oktatóanyagok számos, használhat egy [szimulált eszköz](#simulated-device) kényelmét szolgálja.

## <a name="device-condition"></a>Eszköz feltétel
Eszközök állapotának adatai, a kapcsolat jelenleg használt módszer, például által jelentett módon hivatkozik egy [eszközalkalmazás](#device-app). [Eszköz alkalmazások](#device-app) is jelentheti képességeit. Eszköz twins feltételt, és képes adatait is kereshet.

## <a name="device-data"></a>Eszközadatok
Az IoT Hub tárolt eszközönkénti adatokra hivatkozik eszközadatok [identitásjegyzékhez](#identity-registry). Akkor lehet importálni és exportálni ezeket az adatokat.

## <a name="device-explorer"></a>Eszköz explorer
A [eszköz explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) olyan eszköz, amely a Windows rendszeren fut, és lehetővé teszi az eszközök kezelését a [identitásjegyzékhez](#identity-registry). Az eszköz is küldhet és fogadhat üzeneteket az eszközökön.

## <a name="device-identities-rest-api"></a>Eszközidentitások REST API-ja
A [eszköz identitások REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource) lehetővé teszi a regisztrált eszközök kezelését a [identitásjegyzékhez](#identity-registry) egy REST API használatával. Általában kell használnia a magasabb szintű egyik [SDK szolgáltatás](#azure-iot-service-sdks) látható módon az IoT-központ oktatóanyagok.

## <a name="device-identity"></a>Eszközidentitás
Az eszközidentitást minden regisztrált eszközhöz hozzárendelt egyedi azonosítója a [identitásjegyzékhez](#identity-registry).

## <a name="device-management"></a>Eszközfelügyelet
Eszköz felügyelete magába foglalja a teljes életciklusát társított az IoT-megoldásból, beleértve a tervezési, kiépítési, konfigurálása, a figyelés és a rajtuk lévő eszközök kezelése.

## <a name="device-management-patterns"></a>Eszközfelügyeleti minták
[Az IoT-központ](#iot-hub) lehetővé teszi, hogy a közös eszköz felügyeleti minták újraindul, hajt végre a gyári alaphelyzetbe állítást és az eszközök belső vezérlőprogram frissítések végrehajtásához.

## <a name="device-messaging-rest-api"></a>Eszközök üzenetküldési REST API-ja
Használhatja a [Device Messaging REST API-val](https://docs.microsoft.com/rest/api/iothub/httpruntime) az eszköz a felhőbe küldött üzeneteket küldhet egy IoT-központot, és az eszköz [felhő eszközre](#cloud-to-device) az IoT-központ üzeneteit. Általában kell használnia a magasabb szintű egyik [eszköz SDK-k](#azure-iot-device-sdks) látható módon az IoT-központ oktatóanyagok.

## <a name="device-provisioning"></a>Eszköz kiépítése
A kezdeti hozzáadása során a rendszer eszköz kiépítése [eszközadatok](#device-data) a megoldásban áruházak. Ahhoz, hogy egy új eszközt szeretne az elosztóhoz csatakoztatni, hozzá kell adnia egy Eszközazonosító és a kulcsok az IoT Hub [identitásjegyzékhez](#identity-registry). A telepítési folyamat részeként szükség lehet inicializálni az eszközre vonatkozó adatokat más megoldás tárolja.

## <a name="device-twin"></a>Ikereszközök
A [eszköz iker](iot-hub-devguide-device-twins.md) JSON-dokumentum, amely például metaadat, a konfiguráció és a feltételek eszköz állapot adatait tárolja. [Az IoT-központ](#iot-hub) továbbra is fennáll az egyes eszközök, az IoT hub megadó egy eszköz iker. Eszköz twins lehetővé teszik a szinkronizálandó [eszköz feltételek](#device-condition) és az eszköz és a megoldás közötti konfigurációk háttér. Keresse meg az adott eszközöket és a hosszú ideig futó műveletek állapotának lekérdezése eszköz twins kérdezheti le.

## <a name="device-twin-queries"></a>Eszköz iker lekérdezések
[Eszköz iker lekérdezések](iot-hub-devguide-query-language.md) adatok lekérését az eszköz twins az SQL-szerű IoT Hub lekérdezési nyelv segítségével. Az IoT-központ azonos lekérdezési nyelv használatával kapcsolatos információkat lekérni [feladatok](#job) az IoT hub futtatja.

## <a name="device-twin-rest-api"></a>Eszköz iker REST API-n
Használhatja a [eszköz iker REST API](https://docs.microsoft.com/rest/api/iothub/devicetwinapi) a megoldásban való háttér az eszköz twins kezeléséhez. Az API lehetővé teszi a lekérésére és frissítésére [eszköz iker](#device-twin) tulajdonságait, és meghívja [módszerek közvetlen](#direct-method). Általában kell használnia a magasabb szintű egyik [SDK szolgáltatás](#azure-iot-service-sdks) látható módon az IoT-központ oktatóanyagok.

## <a name="device-twin-synchronization"></a>Eszköz két szinkronizálás
Eszköz két szinkronizálást használ a [szükséges tulajdonságok](#desired-properties) az eszközök és beolvasása az eszköz twins a [tulajdonságok jelentett](#reported-properties) az eszközökről az eszköz iker tárolni.

## <a name="direct-method"></a>Közvetlen módszer
A [közvetlen módszer](iot-hub-devguide-direct-methods.md) módja a metódus indításához, az eszközön az API-k az IoT hub a figyelőn hajtható végre.

## <a name="endpoint"></a>Végpont
Az IoT-központ mutatja meg több [végpontok](iot-hub-devguide-endpoints.md) , amely engedélyezi, hogy az alkalmazások az IoT-központhoz való kapcsolódáshoz. Nincsenek az eszközök felé néző végpontok, amelyek lehetővé teszik az eszközök, például küldés műveletek végrehajtásához [eszközről a felhőbe](#device-to-cloud) üzenetek és a fogadás [felhő eszközre](#cloud-to-device) üzeneteket. Nincsenek a szolgáltatás elérhető felügyeleti végpontok, amelyek lehetővé teszik [háttér-alkalmazások](#back-end-app) műveletek végrehajtásához [eszközidentitás](#device-identity) felügyeleti és a két kezelése. Nincsenek a szolgáltatás felé néző [beépített végpontok](#built-in-endpoints) eszközről a felhőbe üzenetek olvasásához. Létrehozhat [egyéni végpontokat](#custom-endpoints) által az eszközről a felhőbe üzeneteket fogadni a [útválasztási szabály](#routing-rules).

## <a name="event-hubs-service"></a>Event Hubs szolgáltatás
[Az Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) egy kiválóan méretezhető adatbefogadási szolgáltatás, amely több millió fogadására képes van az események másodpercenkénti számát. A szolgáltatás lehetővé teszi, hogy feldolgozhatja és elemezheti a nagy mennyiségű adatot a csatlakoztatott eszközök és alkalmazások által létrehozott. Az IoT-központ szolgáltatáshoz, lásd: [összehasonlítása az Azure IoT-központ és az Azure Event Hubs](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Event Hubs-kompatibilis végpont
Olvasni [eszközről a felhőbe](#device-to-cloud) üzeneteket küldeni az IoT hub, a központ végpont csatlakozni, és bármely Event Hub-kompatibilis a módszert használja e-mailek olvasása. Event Hub-kompatibilis módszerekre használatával a [Event Hubs SDK-k](../event-hubs/event-hubs-programming-guide.md) és [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>A mező átjáró
Egy mező átjáró lehetővé teszi, hogy a kapcsolatot a eszközöket, amelyek közvetlenül nem kapcsolódnak [IoT-központ](#iot-hub) és általában helyileg telepíteni az eszközöket. További információkért lásd: [Mi az Azure IoT Hub?](iot-hub-what-is-iot-hub.md)

## <a name="free-account"></a>Ingyenes fiók
Létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/) az IoT-központ oktatóanyagok befejeződését, és az IoT-központ szolgáltatás (és más Azure-szolgáltatások) kísérletezhet.

## <a name="gateway"></a>Átjáró
Átjáró lehetővé teszi, hogy a kapcsolatot a eszközöket, amelyek közvetlenül nem kapcsolódnak [IoT-központ](#iot-hub). Lásd még: [átjáró mezőben](#field-gateway), [átjáró Cloud](#cloud-gateway), és [egyéni átjáró](#custom-gateway).

## <a name="identity-registry"></a>Identitásjegyzékhez
A [identitásjegyzékhez](iot-hub-devguide-identity-registry.md) az IoT-központ, amely az egyes eszközökre vonatkozó információkat tárolja a beépített összetevője csatlakozni az IoT-központ engedélyezett.

## <a name="interactive-message"></a>Interaktív üzenet
Egy interaktív üzenetről, hogy egy [felhő eszközre](#cloud-to-device) üzenet, amely elindítja a megoldás háttérrendszeréhez egy azonnali műveletet. Például egy eszköz el tudja küldeni egy kell automatikusan kilépteti a CRM-rendszerbe, a hibával kapcsolatos riasztás.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub
IoT-központot egy teljes körűen felügyelt Azure szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációs eszközök millióira között, és a megoldás háttérrendszere. További információkért lásd: [Mi az Azure IoT Hub?](iot-hub-what-is-iot-hub.md) Használja a [Azure-előfizetés](#subscription), IoT-központok a munkaterhelések üzenetküldési IoT kezelésére is létrehozhat.

## <a name="iot-hub-metrics"></a>Az IoT-központ metrikák
[Az IoT-központ metrikák](iot-hub-metrics.md) biztosítanak az IoT-központok állapotára vonatkozó adatokat a [Azure-előfizetés](#subscription). Az IoT-központ metrikák lehetővé teszik a felmérheti a szolgáltatás és a hozzá csatlakozó eszközök általános állapotát. Az IoT-központ mérőszámok segítségével tekintse meg az IoT hubbal jelenít meg, és vizsgálja meg a problémák kiváltó okának anélkül, hogy az Azure ügyfélszolgálatához.

## <a name="iot-hub-query-language"></a>Az IoT-központ lekérdezési nyelv
A [IoT-központ lekérdezési nyelv](iot-hub-devguide-query-language.md) olyan SQL-szerű nyelv, amely lehetővé teszi, hogy a lekérdezés a [feladatok](#job) és eszköz twins.

## <a name="iot-hub-resource-provider-rest-api"></a>Az IoT-központ erőforrás-szolgáltató REST API-n
Használhatja a [IoT Hub erőforrás szolgáltató REST API](https://docs.microsoft.com/rest/api/iothub/resourceprovider/iot-hub-resource-provider-rest) az IoT-központok kezelheti a [Azure-előfizetés](#subscription) például létrehozása, frissítése és törlése hubok műveletet hajt végre.

## <a name="iot-suite"></a>IoT Suite
Az Azure IoT Suite csomagok együtt előkonfigurált megoldások a több Azure-szolgáltatásokhoz. Ezek előre konfigurált megoldások lehetővé teszik a használatának gyors megkezdése szabhatják IoT-végpontok közötti implementációja. További információkért lásd: [Mi az Azure IoT Suite?](../iot-suite/iot-suite-overview.md)

## <a name="iothub-explorer"></a>IOT hubbal-explorer
A [IOT hubbal-explorer](https://github.com/azure/iothub-explorer) platformfüggetlen, a parancssori eszköz. Az eszköz lehetővé teszi az eszközök kezelését a [identitásjegyzékhez](#identity-registry), küldése és üzenetek és fájlok fogadása az eszközök és az IoT hub operatív figyeléséhez.

## <a name="job"></a>Feladat
A megoldás háttérrendszeréhez használható [feladatok](iot-hub-devguide-jobs.md) ütemezéséhez és az eszközök regisztrálva az IoT hub tevékenységek követését. Tevékenységei közé tartoznak a két eszköz frissítése [szükséges tulajdonságok](#desired-properties), frissítési eszköz iker [címkék](#tags), és hívja [módszerek közvetlen](#direct-method). [Az IoT-központ](#iot-hub) is használja a feladatok [történő importálására és exportálására](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) a a [identitásjegyzékhez](#identity-registry).

## <a name="jobs-rest-api"></a>Feladatok REST API-n
A [feladatok REST API](https://docs.microsoft.com/rest/api/iothub/jobapi) kezelését teszi [feladatok](#job) az IoT hub futtatja.

## <a name="mqtt"></a>MQTT
[MQTT](http://mqtt.org/) az egyik az üzenetkezelési protokollok, amelyek [IoT-központ](#iot-hub) eszközökkel való kommunikáció támogatja. Amely támogatja az IoT-központ az üzenetküldési protokollokkal kapcsolatos további információkért lásd: [üzeneteket küldjön és fogadjon IoT hubbal](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Műveletek figyelése
Az IoT-központ [figyelési műveletek](iot-hub-operations-monitoring.md) lehetővé teszi az IoT hub valós idejű műveletek állapotának figyelésére. [Az IoT-központ](#iot-hub) nyomon követi az események között számos modulkategória közül műveletek. Dönthet úgy is, az egy vagy több kategóriához események küldése az IoT-központ végpontjának feldolgozásra. Az adatok a hibák figyelése, vagy adatokat minták alapján összetettebb feldolgozás beállítása.

## <a name="physical-device"></a>Fizikai eszköz
Egy fizikai eszköz egy valódi eszköz, például egy málna Pi, amely összeköti az IoT-központ. Kényelmi célokat szolgál, az IoT-központ oktatóanyagok számos használja [eszközök szimulált](#simulated-device) ahhoz, hogy futtassa a mintákat a helyi számítógépen.

## <a name="primary-and-secondary-keys"></a>Az elsődleges és másodlagos kulcsok
Amikor egy eszköz- vagy szolgáltatás néző végpont az IoT-központ a csatlakozik a [kapcsolati karakterlánc](#connection-string) hozzáférést kulcsot tartalmaz. Az eszköz hozzáadásakor a [identitásjegyzékhez](#identity-registry) , vagy adja hozzá a [megosztott hozzáférési házirend](#shared-access-policy) a központnak a szolgáltatás egy elsődleges és másodlagos kulcsot hoz létre. A két kulcs lehetővé teszi váltása az egyik kulcsról a másikra az IoT hub való hozzáférés elvesztése nélkül a kulcs frissítésekor.

## <a name="protocol-gateway"></a>Protokoll-átjáró
A protokoll-átjáró általában a rendszer a felhőben, és protokoll fordítási szolgáltatásokat biztosít a kapcsolódó eszközök [IoT-központ](#iot-hub). További információkért lásd: [Mi az Azure IoT Hub?](iot-hub-what-is-iot-hub.md)

## <a name="quotas-and-throttling"></a>Kvóták és szabályozás
Nincsenek a különböző [kvóták](iot-hub-devguide-quotas-throttling.md) , amelyek érvényesek a használatára [IoT-központ](#iot-hub), a kvóták számos az IoT hub szintjének függően változhat. [Az IoT-központ](#iot-hub) is érvényes [azelőtt gyorsítja fel](iot-hub-devguide-quotas-throttling.md) futás közben a szolgáltatás használatát.

## <a name="reported-configuration"></a>Jelentett konfiguráció
A környezetében a [eszköz iker](iot-hub-devguide-device-twins.md), jelentett konfigurációs tulajdonságok és az eszköz a két, a megoldás háttérrendszeréhez jelentendő metaadatok teljes készletének hivatkozik.

## <a name="reported-properties"></a>Jelentett tulajdonságai
Környezetében egy [eszköz iker](iot-hub-devguide-device-twins.md), egy alkalmazásra, a használt eszköz iker vonatkozó tulajdonságok jelentette [szükségeskonfiguráció-tulajdonságok](#desired-properties) eszközök konfigurálása és az állapot szinkronizálásához. Jelentett tulajdonságok csak akkor állítható a [eszközalkalmazás](#device-app) és olvashatók és által lekérdezett egy [háttér-alkalmazás](#back-end-app).

## <a name="resource-group"></a>Erőforráscsoport
[Az Azure Resource Manager](#azure-resource-manager) erőforrás csoportokat használ a kapcsolódó erőforrások csoportosítása. Erőforráscsoport segítségével a csoport összes erőforrásra műveleteket végezhet egy időben.

## <a name="retry-policy"></a>Újrapróbálkozási házirend
Újrapróbálkozási házirendje használatával kezelni [átmeneti hibák](https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx) egy felhőszolgáltatásra történő csatlakoztatásakor.

## <a name="routing-rules"></a>Útválasztási szabályokat
Konfigurálja [útválasztási szabályok](iot-hub-devguide-messages-read-custom.md) az eszközről a felhőbe üzenetek továbbításához az IoT hub egy [beépített végpont](#built-in-endpoints) vagy [egyéni végpontokat](#custom-endpoints) feldolgozásra a megoldás háttérrendszeréhez .

## <a name="sasl-plain"></a>SASL EGYSZERŰ
SASL egyszerű egy olyan protokoll, amely a [AMQP](#advanced-message-queue-protocol) protokollt használ a biztonsági jogkivonatokat átviteléhez.

## <a name="shared-access-signature"></a>Közös hozzáférésű jogosultságkódot
Megosztott hozzáférési aláírásokkal (SAS) olyan hitelesítési mechanizmus biztonságos SHA-256 kivonatokkal vagy URI-k alapján. SAS hitelesítési két részből áll: egy _megosztott elérési házirendet_ és egy _közös hozzáférésű Jogosultságkód_ (gyakran nevezik jogkivonat). Egy eszköz SAS használatával hitelesítik magukat az IoT-központ. [Háttér-alkalmazások](#back-end-app) SAS segítségével a szolgáltatás felé néző végpontok az IoT-központ a hitelesítést. Általában a SAS-jogkivonat közé tartozik a [kapcsolati karakterlánc](#connection-string) , hogy egy alkalmazás használ egy IoT-központot egy kapcsolatot létesíteni.

## <a name="shared-access-policy"></a>Megosztott elérési házirendet
A megosztott elérési házirend meghatározása az engedélyeket, bárki, aki rendelkezik érvényes [elsődleges vagy másodlagos kulcsot](#primary-and-secondary-keys) házirendhez társított. A központ a kezelheti a megosztott elérési házirendek és kulcsok a [portal](#azure-portal).

## <a name="simulated-device"></a>Szimulált eszköz
Kényelmi célokat szolgál az IoT-központ oktatóanyagok számos használatával szimulált eszközök lehetővé teszik a mintákat a helyi számítógépen való futtatásához. Ezzel szemben egy [fizikai eszköz](#physical-device) egy valódi eszköz, például egy málna Pi, amely összeköti az IoT-központ.

## <a name="solution"></a>Megoldás
A _megoldás_ jelentheti a Visual Studio megoldás, amely egy vagy több projekt tartalmazza. A _megoldás_ is utalhat egy IoT-megoldás, amely tartalmazza az eszközök, például elemek [eszköz alkalmazások](#device-app), az IoT-központ, a más Azure-szolgáltatásokkal, és [háttér-alkalmazások](#back-end-app).

## <a name="subscription"></a>Előfizetés
Az Azure-előfizetésre, ha a számlázási történik. Minden Azure-erőforrás létrehozása, vagy használja az Azure szolgáltatás egy-egy előfizetéshez társítva. Sok kvóták is vonatkoznak az előfizetés szintjén.

## <a name="system-properties"></a>Rendszertulajdonságok
A környezetében a [eszköz iker](iot-hub-devguide-device-twins.md), rendszer tulajdonság csak olvasható, és például az utolsó tevékenység idő és a kapcsolat állapota az eszköz használatára vonatkozó információval.

## <a name="tags"></a>Címkék
A környezetében a [eszköz iker](iot-hub-devguide-device-twins.md), címke található eszköz metaadatait tárolja, és melyeket a megoldás háttérrendszeréhez, egy JSON-dokumentum formájában. Címkék nem láthatók el alkalmazásokat az eszközön.

## <a name="telemetry"></a>Telemetria
Eszközök gyűjt telemetrikus adatokat, például a szél sebesség vagy hőmérséklet, és használjon [adatpont üzenetek](#data-point-messages) a telemetriai adatokat küldhet egy IoT-központot.

## <a name="token-service"></a>Jogkivonat-szolgáltatás
A jogkivonat-szolgáltatás segítségével valósítja meg a hitelesítési módszer az eszközökhöz. Az IoT-központ használ [megosztott hozzáférési házirend](#shared-access-policy) rendelkező **DeviceConnect** létrehozásához szükséges engedélyek *eszköz hatókörű* jogkivonatokat. Ezeket a jogkivonatokat engedélyezheti az IoT hub való kapcsolódáshoz. Egy eszköz egy egyéni hitelesítési módszer használatával hitelesítik magukat a jogkivonat-szolgáltatás. Ha az eszköz sikeresen hitelesíti magát, a jogkivonat-szolgáltatás kibocsát egy SAS-jogkivonat az eszközt az IoT hub elérésére használhat.

## <a name="x509-client-certificate"></a>Ügyfél X.509-tanúsítvány
Egy eszköz egy X.509 tanúsítvány használatával hitelesítik magukat [IoT-központ](#iot-hub). Egy X.509 tanúsítvány használata helyett egy [SAS-jogkivonat](#shared-access-signature).
