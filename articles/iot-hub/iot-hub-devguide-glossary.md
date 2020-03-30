---
title: Az Azure IoT Hub kifejezések szószedete | Microsoft dokumentumok
description: Fejlesztői útmutató – egy szószedet, amely ismerteti az Azure IoT Hub-cikkekben használt néhány gyakori kifejezést.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.openlocfilehash: 3471bd7297118dc75fb97390c4601179f61e6416
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497539"
---
# <a name="glossary-of-iot-hub-terms"></a>Az IoT Hub-kifejezések szószedete
Ez a cikk az IoT Hub-cikkekben használt néhány gyakori kifejezést sorol fel.

## <a name="advanced-message-queueing-protocol"></a>Speciális üzenetsorsor-küldési protokoll
[Advanced Message Queueing Protocol (AMQP)](https://www.amqp.org/) egyike azoknak az üzenetkezelési protokolloknak, amelyeket az [IoT Hub](#iot-hub) támogat az eszközökkel való kommunikációhoz. Az IoT Hub által támogatott üzenetkezelési protokollokról az [Üzenetek küldése és fogadása az IoT Hubbal](iot-hub-devguide-messaging.md)című témakörben talál további információt.

## <a name="automatic-device-management"></a>Automatikus eszközkezelés
Az Azure IoT Hub automatikus eszközkezelése automatizálja a nagy eszközflották teljes életciklusa során végzett ismétlődő és összetett feladatainak nagy feladatainak automatizálását. Az Automatikus eszközkezelés segítségével megcélozhatja az eszközök készletét a tulajdonságaik alapján, meghatározhatja a kívánt konfigurációt, és lehetővé teheti az IoT Hub számára, hogy frissítse az eszközöket, amikor azok hatókörbe kerülnek.  [Automatikus eszközkonfigurációkból](iot-hub-auto-device-config.md) és [IoT Edge automatikus központi telepítésekből](../iot-edge/how-to-deploy-monitor.md)áll.

## <a name="automatic-device-configuration"></a>Automatikus eszközkonfiguráció
A megoldás háttérrendszere [automatikus eszközkonfigurációk](iot-hub-auto-device-config.md) segítségével hozzárendelheti a kívánt tulajdonságokat az [ikereszközök](#device-twin) készletéhez, és rendszermetrikák és egyéni metrikák használatával jelentheti az állapotot. 

## <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület
Az [Azure classic CLI](../cli-install-nodejs.md) egy platformfüggetlen, nyílt forráskódú, rendszerhéj-alapú parancseszköz az erőforrások létrehozásához és kezeléséhez a Microsoft Azure-ban. A CLI ezen verziója csak klasszikus központi telepítésekhez használható.

## <a name="azure-cli"></a>Azure CLI
Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2) egy platformfüggetlen, nyílt forráskódú, rendszerhéj-alapú parancseszköz az erőforrások létrehozásához és kezeléséhez a Microsoft Azure-ban.


## <a name="azure-iot-device-sdks"></a>Azure IoT-eszköz SDK-k
Több nyelven is _elérhetőeszköz-SDK-k_ állnak rendelkezésre, amelyek lehetővé teszik az IoT-központtal interakcióba lépő [eszközalkalmazások](#device-app) létrehozását. Az IoT Hub oktatóanyag a következőket mutatja be, hogyan használhatja ezeket az eszközöket SDK-k. Ebben a [GitHub-tárházban](https://github.com/Azure/azure-iot-sdks)megtalálhatja az eszköz SDK-ival kapcsolatos további információkat.

## <a name="azure-iot-service-sdks"></a>Az Azure IoT szolgáltatás SDK-i
Több nyelven is elérhető _szolgáltatás SDK-k,_ amelyek lehetővé teszik az IoT-központtal kommunikáló [háttéralkalmazások](#back-end-app) létrehozását. Az IoT Hub oktatóanyagok bemutatják, hogyan használhatja ezeket a szolgáltatás SDK-k. Ebben a [GitHub-tárházban](https://github.com/Azure/azure-iot-sdks)megtalálhatja a forráskódot és a szolgáltatás SDK-ival kapcsolatos további információkat.

## <a name="azure-iot-tools"></a>Azure IoT-eszközök
Az [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) egy platformfüggetlen, nyílt forráskódú Visual Studio-kódbővítmény, amely segít az Azure IoT Hub és az eszközök vs code kezelésében. Az Azure IoT Tools segítségével az IoT-fejlesztők könnyedén fejleszthetik az IoT-projektet a VS Code-ban.

## <a name="azure-portal"></a>Azure portál
A [Microsoft Azure portal](https://portal.azure.com) egy központi hely, ahol azure-erőforrásokat építhet ki és kezelhet. Ez szervezi a tartalmát _a pengék_.

## <a name="azure-powershell"></a>Azure PowerShell
[Az Azure PowerShell](/powershell/azure/overview) parancsmagok gyűjteménye, amelyekkel kezelheti az Azure-t a Windows PowerShell használatával. A parancsmagokkal hozhat létre, tesztelhet, üzembe helyezhet és kezelhet az Azure platformon keresztül szállított megoldásokat és szolgáltatásokat.

## <a name="azure-resource-manager"></a>Azure Resource Manager
[Az Azure Resource Manager](../azure-resource-manager/management/overview.md) lehetővé teszi, hogy a megoldás erőforrásait csoportként dolgozzon. Egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti a megoldás erőforrásait.

## <a name="azure-service-bus"></a>Azure Service Bus
[A Service Bus](../service-bus/index.md) felhőalapú kommunikációt biztosít a vállalati üzenetküldéssel és a továbbított kommunikációval, amely segít a helyszíni megoldások és a felhő összekapcsolásához. Néhány IoT Hub-oktatóanyag a Service [Bus-várólistákat](../service-bus-messaging/service-bus-messaging-overview.md)használja.

## <a name="azure-storage"></a>Azure Storage
[Az Azure Storage](../storage/common/storage-introduction.md) egy felhőalapú tárolási megoldás. Ez magában foglalja a Blob Storage szolgáltatás, amely strukturálatlan objektumadatok tárolására használható. Néhány IoT Hub-oktatóanyag blobstorage-t használ.

## <a name="back-end-app"></a>Háttéralkalmazás
Az [IoT Hub](#iot-hub)környezetében egy háttéralkalmazás egy olyan alkalmazás, amely egy IoT-központ egyik szolgáltatásnéző végpontjához csatlakozik. Előfordulhat például, hogy egy háttéralkalmazás [lekéri az eszközről a felhőbe](#device-to-cloud) irányuló üzeneteket, vagy kezeli az [identitásjegyzéket.](#identity-registry) Általában egy háttéralkalmazás fut a felhőben, de sok az oktatóanyagok a háttéralkalmazások konzolalkalmazások futnak a helyi fejlesztői gépen.

## <a name="built-in-endpoints"></a>Beépített végpontok
Minden IoT hub tartalmaz egy beépített [végpontot,](iot-hub-devguide-endpoints.md) amely az Event Hub-kompatibilis. Bármilyen mechanizmust, amely együttműködik az Event Hubs az eszköz ről a felhőbe üzeneteket olvasni erről a végpontról.

## <a name="cloud-gateway"></a>Felhőátjáró
A felhőátjáró lehetővé teszi a kapcsolatot az on-fi hubhoz közvetlenül nem tud ógrás eszközök [számára.](#iot-hub) A felhőátjáró a felhőben található, szemben a [helyszíni átjáróval,](#field-gateway) amely helyi legel az eszközökön fut. A felhőátjáró kondicélja, hogy protokollfordítást valósítson meg az eszközökön.

## <a name="cloud-to-device"></a>Felhőből az eszközre
Az IoT hubról egy csatlakoztatott eszközre küldött üzenetekre hivatkozik. Ezek az üzenetek gyakran olyan parancsok, amelyek cselekvésre utasítják az eszközt. További információt az [Üzenetek küldése és fogadása az IoT Hubbal](iot-hub-devguide-messaging.md)című témakörben talál.

## <a name="configuration"></a>Konfiguráció
Az automatikus [eszközkonfiguráció](iot-hub-auto-device-config.md)környezetében az IoT Hubon belüli konfiguráció határozza meg a kívánt konfigurációt az ikereszközök egy készletéhez, és mérőszámokat biztosít az állapot és a haladás jelentéséhez.

## <a name="connection-string"></a>Kapcsolati sztring
Az alkalmazáskódban kapcsolati karakterláncok használatával beágyazni a végponthoz való csatlakozáshoz szükséges információkat. A kapcsolati karakterlánc általában tartalmazza a végpont címét és a biztonsági információkat, de a kapcsolati karakterlánc-formátumok szolgáltatásokonként eltérőek lehetnek. Az IoT Hub szolgáltatáshoz kétféle kapcsolati karakterlánc van társítva:
- *Az eszközkapcsolati karakterláncok* lehetővé teszik, hogy az eszközök csatlakozzanak az IT-központ eszközfelé néző végpontjaihoz.
- *Az IoT Hub kapcsolati karakterláncai* lehetővé teszik, hogy a háttéralkalmazások csatlakozzanak az IoT hub szolgáltatásnéző végpontjaihoz.

## <a name="custom-endpoints"></a>Egyéni végpontok
Egyéni [végpontokat](iot-hub-devguide-endpoints.md) hozhat létre egy IoT-központon az útválasztási szabály által feladott üzenetek [kézbesítéséhez.](#routing-rules) Az egyéni végpontok közvetlenül egy eseményközponthoz, egy Service Bus-várólistához vagy egy Service Bus-témakörhöz csatlakoznak.

## <a name="custom-gateway"></a>Egyéni átjáró
Az átjáró lehetővé teszi a kapcsolatot az on-t, amely nem tud közvetlenül csatlakozni az [IoT Hubhoz.](#iot-hub) Az Azure IoT Edge segítségével egyéni átjárókat hozhat létre, amelyek egyéni logikát valósítanak meg az üzenetek, az egyéni protokollkonverziók és a peremhálózaton lévő egyéb feldolgozások kezelésére.

## <a name="data-point-message"></a>Adatpontos üzenet
Az adatpont-üzenet egy [eszközről a felhőbe](#device-to-cloud) irányuló üzenet, amely [telemetriai](#telemetry) adatokat, például szélsebességet vagy hőmérsékletet tartalmaz.

## <a name="desired-configuration"></a>Kívánt konfiguráció
Az [ikereszköz](iot-hub-devguide-device-twins.md)környezetében a kívánt konfiguráció az ikereszköz ben lévő, az eszközzel szinkronizálandó tulajdonságok és metaadatok teljes készletére vonatkozik.

## <a name="desired-properties"></a>Kívánt tulajdonságok
Az [ikereszköz](iot-hub-devguide-device-twins.md)környezetében a kívánt tulajdonságok az ikereszköz egy alszakasza, amely [a jelentett tulajdonságokkal](#reported-properties) együtt szinkronizálja az eszköz konfigurációját vagy állapotát. A kívánt tulajdonságokat csak egy [háttéralkalmazás](#back-end-app) állíthatja be, és az [eszközalkalmazás](#device-app)figyeli.

## <a name="device-to-cloud"></a>Eszközről felhőre
A csatlakoztatott eszközről az [IoT Hubra](#iot-hub)küldött üzenetekre hivatkozik. Ezek az üzenetek lehetnek [adatpont-](#data-point-message) vagy [interaktív](#interactive-message) üzenetek. További információt az [Üzenetek küldése és fogadása az IoT Hubbal](iot-hub-devguide-messaging.md)című témakörben talál.

## <a name="device"></a>Eszköz
Az IoT környezetében az eszköz általában egy kis méretű, önálló számítástechnikai eszköz, amely adatokat gyűjthet, vagy más eszközök vezérlése. Lehet például egy eszköz egy környezeti ellenőrző eszköz, vagy egy ellenőrző vezérlő az öntöző- és szellőztető rendszerek egy üvegházban. Az [eszközkatalógus](https://catalog.azureiotsolutions.com/) az [IoT Hubbal](#iot-hub)való együttműködésre tanúsított hardvereszközök listáját tartalmazza.

## <a name="device-app"></a>Eszközalkalmazás
Egy eszközalkalmazás fut az [eszközön,](#device) és kezeli az [IoT hubbal](#iot-hub)való kommunikációt. Általában az [Azure IoT-eszköz SDK-k](#azure-iot-device-sdks) egyikét használja egy eszközalkalmazás megvalósításakor. Az IoT-oktatóanyagok nagy részében [szimulált eszközt](#simulated-device) használ a kényelem érdekében.

## <a name="device-condition"></a>Eszköz állapota
Az [eszközalkalmazás](#device-app)által jelentett eszközállapot-adatokra, például a jelenleg használt kapcsolódási módra hivatkozik. [Az eszközalkalmazások](#device-app) is jelenthetik képességeiket. Az ikereszközök használatával lekérdezheti a feltétel- és képességadatokat.

## <a name="device-data"></a>Eszközadatok
Az eszközadatok az IoT Hub [identitásbeállítási nyilvántartásában](#identity-registry)tárolt eszközönkénti adatokra vonatkoznak. Lehetőség van az adatok importálására és exportálására.

## <a name="device-explorer"></a>Eszközkereső
Az [eszközkezelő](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) egy Windows rendszeren futó eszköz, amely lehetővé teszi az eszközök kezelését az [identitás-beállításjegyzékben.](#identity-registry) Az eszköz üzeneteket is küldhet és fogadhat az eszközökre.

## <a name="device-identity"></a>Eszközidentitás
Az eszközidentitás az [identitásjegyzékben](#identity-registry)regisztrált összes eszközhöz rendelt egyedi azonosító.

## <a name="device-management"></a>Eszközfelügyelet
Az eszközkezelés magában foglalja az IoT-megoldásban lévő eszközök kezeléséhez kapcsolódó teljes életciklust, beleértve a tervezést, a kiépítést, a konfigurálást, a figyelést és a kivisszavonulást.

## <a name="device-management-patterns"></a>Eszközfelügyeleti minták
[Az IoT hub](#iot-hub) lehetővé teszi a közös eszközkezelési mintákat, beleértve az újraindítást, a gyári alapbeállítások elvégzését és a belső vezérlőprogram-frissítések et az eszközökön.

## <a name="device-rest-api"></a>Eszköz- és többmint.
Az eszközről származó [Device REST API-t](https://docs.microsoft.com/rest/api/iothub/device) használhatja az eszközről a felhőbe irányuló üzenetek IoT-központba küldésére, és [felhőből az eszközre](#cloud-to-device) irányuló üzenetek et fogadhat egy IoT-központból. Általában az IoT Hub oktatóanyagok ban látható módon a magasabb szintű [eszköz SDK-k](#azure-iot-device-sdks) egyikét kell használnia.

## <a name="device-provisioning"></a>Eszközkiépítés
Az eszközkiépítés a kezdeti [eszközadatok](#device-data) hozzáadása a megoldás tárolóihoz. Ahhoz, hogy új eszköz csatlakozzon a hubhoz, hozzá kell adnia egy eszközazonosítót és kulcsokat az IoT Hub [identitásbeállítási jegyzékéhez.](#identity-registry) A kiépítési folyamat részeként előfordulhat, hogy más megoldástárolókban inicializálnia kell az eszközspecifikus adatokat.

## <a name="device-twin"></a>Ikereszközök
Az [ikereszköz](iot-hub-devguide-device-twins.md) olyan JSON-dokumentum, amely az eszköz állapotadatait, például a metaadatokat, konfigurációkat és feltételeket tárolja. [Az IoT Hub](#iot-hub) egy ikereszközt tart fenn az IoT hubban üzembe helyezhető minden egyes eszközhöz. Az eszköztwins lehetővé teszi az [eszköz feltételek](#device-condition) és konfigurációk szinkronizálását az eszköz és a megoldás háttérrendszerének között. Lekérdezheti az eszköztwins adott eszközök megkereséséhez és a hosszú ideig futó műveletek állapotát.

## <a name="direct-method"></a>Közvetlen módszer
A [közvetlen módszer](iot-hub-devguide-direct-methods.md) egy módja annak, hogy egy metódust az eszközön végrehajtandó, az IoT hubon egy API-meghívásával.

## <a name="endpoint"></a>Végpont
Az IoT-központ több [végpontot](iot-hub-devguide-endpoints.md) tesz elérhetővé, amelyek lehetővé teszik, hogy az alkalmazások csatlakozzanak az IoT hubhoz. Vannak olyan eszközfelé néző végpontok, amelyek lehetővé teszik az eszközök számára, hogy olyan műveleteket hajtsanak végre, mint az [eszközről a felhőbe](#device-to-cloud) irányuló üzenetek küldése és [a felhőből az eszközre](#cloud-to-device) irányuló üzenetek fogadása. Vannak szolgáltatás-kapcsolatú felügyeleti végpontok, amelyek lehetővé teszik a [háttér-alkalmazások](#back-end-app) műveletek et, például [az eszköz identitás-kezelés](#device-identity) és az ikereszköz-felügyelet. Az eszközről a felhőbe irányuló üzenetek olvasásához szolgáltatásra néző [beépített végpontok](#built-in-endpoints) találhatók. [Egyéni végpontokat](#custom-endpoints) hozhat létre az útválasztási szabály által küldött eszközről a felhőbe irányuló üzenetek [fogadására.](#routing-rules)

## <a name="event-hubs-service"></a>Eseményközpontok szolgáltatás
[Az Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) egy jól méretezhető adat-be- éselőanyag-szolgáltatás, amely másodpercenként több millió eseményt képes beszolgálni. A szolgáltatás lehetővé teszi a csatlakoztatott eszközök és alkalmazások által előállított hatalmas mennyiségű adat feldolgozását és elemzését. Az IoT Hub szolgáltatás összehasonlítása: [Az Azure IoT Hub és az Azure Event Hubs összehasonlítása.](iot-hub-compare-event-hubs.md)

## <a name="event-hub-compatible-endpoint"></a>Az Eseményközponttal kompatibilis végpont
Az IoT hubba küldött [eszközök ről felhőbe](#device-to-cloud) irányuló üzenetek olvasásához csatlakozhat a hub egyik végpontjéhez, és bármely Event Hub-kompatibilis módszerrel elolvashatja ezeket az üzeneteket. Az Event Hub-kompatibilis módszerek közé tartozik az [Event Hubs SDK-k](../event-hubs/event-hubs-programming-guide.md) és az [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)használata.

## <a name="field-gateway"></a>Helyszíni átjáró
A helyszíni átjáró lehetővé teszi a kapcsolatot az eszközök, amelyek nem tudnak közvetlenül csatlakozni [az IoT Hubhoz,](#iot-hub) és általában helyileg telepített az eszközökkel. További információ: [Mi az Azure IoT Hub?](about-iot-hub.md)

## <a name="free-account"></a>Ingyenes fiók
Létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/) az IoT Hub oktatóanyagai befejezéséhez és kísérletezhet az IoT Hub szolgáltatással (és más Azure-szolgáltatásokkal).

## <a name="gateway"></a>Átjáró
Az átjáró lehetővé teszi a kapcsolatot az on-t, amely nem tud közvetlenül csatlakozni az [IoT Hubhoz.](#iot-hub) Lásd még: [Field Gateway](#field-gateway), [Cloud Gateway](#cloud-gateway)és Custom [Gateway](#custom-gateway).

## <a name="identity-registry"></a>Azonosító beállításjegyzék
Az [identitás-beállításjegyzék](iot-hub-devguide-identity-registry.md) egy IoT-központ beépített összetevője, amely az IoT-központhoz való csatlakozásra engedélyezett eszközök adatait tárolja.

## <a name="interactive-message"></a>Interaktív üzenet
Az interaktív üzenet egy [felhőből az eszközre](#cloud-to-device) irányuló üzenet, amely azonnali műveletet indít el a megoldás háttérrendszerében. Előfordulhat például, hogy egy eszköz riasztást küld egy olyan hibáról, amelyet automatikusan be kell jelentkeznie egy CRM-rendszerbe.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub
Az IoT Hub egy teljes körűen felügyelt Azure-szolgáltatás, amely megbízható és biztonságos kétirányú kommunikációt tesz lehetővé több millió eszköz és egy megoldás háttértartaléka között. További információ: [Mi az Azure IoT Hub?](about-iot-hub.md) Az [Azure-előfizetés](#subscription)használatával ioT-központokat hozhat létre az IoT-üzenetkezelési számítási feladatok kezeléséhez.

## <a name="iot-hub-metrics"></a>IoT Hub-metrikák
[Az IoT Hub-metrikák](iot-hub-metrics.md) az [Azure-előfizetésben](#subscription)lévő IoT-központok állapotára vonatkozó adatokat biztosítanak. Az IoT Hub-metrikák lehetővé teszik a szolgáltatás és a hozzá csatlakoztatott eszközök általános állapotának felmérését. Az IoT Hub-metrikák segítségével megtekintheti, hogy mi történik az IoT-központtal, és kivizsgálhatja a kiváltó problémákat anélkül, hogy kapcsolatba kellene lépnie az Azure-támogatással.

## <a name="iot-hub-query-language"></a>Az IoT Hub lekérdezési nyelve
Az [IoT Hub lekérdezési nyelv](iot-hub-devguide-query-language.md) egy SQL-szerű [](#job) nyelv, amely lehetővé teszi, hogy lekérdezze a és az eszköz twins.

## <a name="iot-hub-resource-rest-api"></a>IoT Hub erőforrás REST API
Az [IoT Hub Resource REST API-val](https://docs.microsoft.com/rest/api/iothub/iothubresource) kezelheti az IoT-központokat az [Azure-előfizetésében,](#subscription) amelyek olyan műveleteket hajtanak végre, mint például a hubok létrehozása, frissítése és törlése.

## <a name="iot-solution-accelerators"></a>IoT-megoldásgyorsítók
Az Azure IoT-megoldásgyorsítók több Azure-szolgáltatást is tartalmazó megoldásokat kínálnak. Ezek a megoldások lehetővé teszik, hogy gyorsan elkezdhesse a gyakori IoT-forgatókönyvek végpontok között történő megvalósításait. További információ: [Mik azok az Azure IoT-megoldásgyorsítók?](../iot-accelerators/about-iot-accelerators.md)

## <a name="the-iot-extension-for-azure-cli"></a>Az Azure CLI IoT-bővítménye 
[Az Azure CLI IoT-bővítménye](https://github.com/Azure/azure-iot-cli-extension) egy platformfüggetlen, parancssori eszköz. Az eszköz lehetővé teszi, hogy kezelje az eszközöket az [identitás-beállításjegyzékben,](#identity-registry)üzeneteket és fájlokat küldjön és fogadjon az eszközökről, és figyelje az IoT hub műveleteit.

## <a name="job"></a>Feladat
A megoldás háttér-háttér-feladatok [segítségével](iot-hub-devguide-jobs.md) ütemezheti és nyomon követheti a tevékenységeket az IoT hubregisztrált eszközök önszámára regisztrált eszközök önintézése. A tevékenységek közé tartozik az ikereszköz [kívánt tulajdonságainak](#desired-properties)frissítése , az eszköz [ikercímkéinek](#tags)frissítése és a [közvetlen módszerek](#direct-method)meghívása . [Az IoT Hub](#iot-hub) az [identitásjegyzékbe](#identity-registry) [történő importálást és exportálást](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) is használja.

## <a name="modules"></a>Modulok
Az eszköz oldalán az IoT Hub-eszköz SDK-k lehetővé teszik, hogy [olyan modulokat](iot-hub-devguide-module-twins.md) hozzon létre, ahol mindegyik önálló kapcsolatot nyit meg az IoT Hubhoz. Ez a funkció lehetővé teszi, hogy külön névtereket használjon az eszköz különböző összetevőihez.

A modulidentitás és az ikermodul ugyanazokat a képességeket biztosítja, mint az [eszközidentitás](#device-identity) és az [ikereszköz,](#device-twin) de finomabb részletességgel. Ez a finomabb részletesség lehetővé teszi, hogy a képes eszközök, például az operációs rendszer alapú eszközök vagy a több összetevőt kezelő belső vezérlőprogram-eszközök elkülönítsék az egyes összetevők konfigurációját és feltételeit.

## <a name="module-identity"></a>Modul identitása
A modulidentitás az eszközhöz tartozó minden modulhoz rendelt egyedi azonosító. A modulidentitás is regisztrálva van az [identitásjegyzékben.](#identity-registry)

## <a name="module-twin"></a>Ikermodul
Az ikereszközhöz hasonlóan az ikermodul is a JSON-dokumentum, amely a modul állapotadatait, például a metaadatokat, konfigurációkat és feltételeket tárolja. Az IoT Hub egy ikermodult tart minden egyes modulidentitáshoz, amelyet az IoT hubban egy eszközidentitás keretében létesít. A modultwins lehetővé teszi a modul feltételek és konfigurációk szinkronizálását a modul és a megoldás háttérvége között. A modul twins lekérdezése adott modulok megkereséséhez és a hosszú ideig futó műveletek állapotát.

## <a name="mqtt"></a>MQTT
[Az MQTT](https://mqtt.org/) az [IoT Hub](#iot-hub) által az eszközökkel való kommunikációhoz támogatott üzenetkezelési protokollok egyike. Az IoT Hub által támogatott üzenetkezelési protokollokról az [Üzenetek küldése és fogadása az IoT Hubbal](iot-hub-devguide-messaging.md)című témakörben talál további információt.

## <a name="operations-monitoring"></a>Műveletek figyelése
Az IoT [Hub-műveletek figyelése](iot-hub-operations-monitoring.md) lehetővé teszi, hogy valós időben figyelje az IoT hub on operations állapotát. [Az IoT Hub](#iot-hub) a műveletek több kategóriája között követi nyomon az eseményeket. Választhat ja: események küldése egy vagy több kategóriából egy IoT Hub-végpont feldolgozásra. Figyelheti az adatokat a hibákat, vagy összetettebb feldolgozást állíthat be az adatminták alapján.

## <a name="physical-device"></a>Fizikai eszköz
A fizikai eszköz egy valódi eszköz, például egy Raspberry Pi, amely csatlakozik egy IoT hubhoz. A kényelem érdekében az IoT Hub számos [oktatóanyaga szimulált eszközöket](#simulated-device) használ, hogy lehetővé tegye a minták futtatását a helyi számítógépen.

## <a name="primary-and-secondary-keys"></a>Elsődleges és másodlagos kulcsok
Amikor egy IOt-központ eszközfelé vagy szolgáltatásfelé néző végpontjához csatlakozik, a [kapcsolati karakterlánc](#connection-string) kulcsot tartalmaz a hozzáférés biztosításához. Amikor hozzáad egy eszközt az [identitásbeállításjegyzékhez,](#identity-registry) vagy [megosztott hozzáférési szabályzatot](#shared-access-policy) ad hozzá a hubhoz, a szolgáltatás létrehoz egy elsődleges és másodlagos kulcsot. Két kulcs használata lehetővé teszi, hogy az egyik kulcsról a másikra, amikor frissíti a kulcsot anélkül, hogy az IoT hubhoz való hozzáférés elvesztése.

## <a name="protocol-gateway"></a>Protokollátjáró
A protokollátjáró általában a felhőben van telepítve, és protokollfordítási szolgáltatásokat nyújt az [IoT Hubhoz](#iot-hub)csatlakozó eszközök számára. További információ: [Mi az Azure IoT Hub?](about-iot-hub.md)

## <a name="quotas-and-throttling"></a>Kvóták és szabályozás
Az [IoT Hub](#iot-hub)használatára különböző [kvóták](iot-hub-devguide-quotas-throttling.md) vonatkoznak, a kvóták nagy része az IoT hub szintjétől függően változik. [Az IoT Hub](#iot-hub) is alkalmazza [a szabályozást](iot-hub-devguide-quotas-throttling.md) a szolgáltatás futási időben történő használatára.

## <a name="reported-configuration"></a>Jelentett konfiguráció
Az [ikereszköz](iot-hub-devguide-device-twins.md)környezetében a jelentett konfiguráció az ikereszköz tulajdonságainak és metaadatainak teljes készletére vonatkozik, amelyeket jelenteni kell a megoldás háttérrendszerének.

## <a name="reported-properties"></a>Jelentett tulajdonságok
Az [ikereszköz](iot-hub-devguide-device-twins.md)környezetében a jelentett tulajdonságok az ikereszköz egyik alszakasza, amelyet az eszköz konfigurációjának vagy állapotának szinkronizálásához [kívánt tulajdonságokkal](#desired-properties) használnak. A jelentett tulajdonságokat csak az [eszközalkalmazás](#device-app) állíthatja be, és egy [háttéralkalmazás](#back-end-app)olvashatja és kérdezheti le.

## <a name="resource-group"></a>Erőforráscsoport
[Az Azure Resource Manager](#azure-resource-manager) erőforráscsoportok at használ a kapcsolódó erőforrások csoportosításához. Az erőforráscsoport segítségével egyszerre hajthatja végre a csoport összes erőforrását.

## <a name="retry-policy"></a>Újrapróbálkozási szabályzat
Az újrapróbálkozási szabályzat használatával átmeneti [hibákat,](/azure/architecture/best-practices/transient-faults) amikor csatlakozik egy felhőalapú szolgáltatáshoz.

## <a name="routing-rules"></a>Útválasztási szabályok
Az IoT-központban beállíthatja az [útválasztási szabályokat,](iot-hub-devguide-messages-read-custom.md) hogy az eszköz ről a felhőbe irányuló üzeneteket egy beépített végpontra vagy [egyéni végpontokra](#custom-endpoints) irányítsa a megoldás háttérrendszeráltali feldolgozása [érdekében.](#built-in-endpoints)

## <a name="sasl-plain"></a>SASL SIMA
A SASL PLAIN egy olyan protokoll, amelyet az AMQP protokoll biztonsági jogkivonatok átvitelére használ.

## <a name="service-rest-api"></a>Szolgáltatás REST API-ja
Használhatja a [Service REST API-t](https://docs.microsoft.com/rest/api/iothub/service/configuration) a megoldás háttér-az eszközök kezeléséhez. Az API lehetővé teszi [az ikereszköz tulajdonságainak](#device-twin) beolvasását és frissítését, [a közvetlen metódusok](#direct-method)meghívását és [a feladatok ütemezését.](#job) Általában az IoT Hub oktatóanyagok ban látható magasabb szintű [szolgáltatások egyikét](#azure-iot-service-sdks) kell használnia.

## <a name="shared-access-signature"></a>Közös hozzáférésű jogosultságkód
A megosztott hozzáférésű aláírások (SAS) az SHA-256 biztonságos kivonatain vagy URI-in alapuló hitelesítési mechanizmus. A SAS-hitelesítés két összetevőből áll: egy _megosztott hozzáférési szabályzatból_ és egy _megosztott hozzáférési kódból_ (más néven jogkivonatból). Egy eszköz SAS-t használ az IoT hubbal való hitelesítéshez. [A háttéralkalmazások](#back-end-app) is sas-t használnak a szolgáltatásfelé néző végpontok ioT hub on authenticate. Általában a SAS-jogkivonatot tartalmazza a [kapcsolati karakterláncban,](#connection-string) amelyet egy alkalmazás az IoT hubhoz való kapcsolat létrehozásához használ.

## <a name="shared-access-policy"></a>Megosztott hozzáférési házirend
A megosztott hozzáférési szabályzat határozza meg azokat az engedélyeket, akik érvényes [elsődleges vagy másodlagos kulccsal](#primary-and-secondary-keys) rendelkeznek a házirendhez társítva. A portálon kezelheti a hub megosztott hozzáférési szabályzatait és [kulcsait.](#azure-portal)

## <a name="simulated-device"></a>Szimulált eszköz
A kényelem érdekében az IoT Hub számos oktatóanyaga szimulált eszközöket használ, hogy lehetővé tegye a minták futtatását a helyi számítógépen. Ezzel szemben a [fizikai eszköz](#physical-device) egy valódi eszköz, például egy Raspberry Pi, amely csatlakozik egy IoT hubhoz.

## <a name="solution"></a>Megoldás
A _megoldás_ hivatkozhat egy Visual Studio-megoldásra, amely egy vagy több projektet tartalmaz. A _megoldás_ egy IoT-megoldásra is hivatkozhat, amely olyan elemeket tartalmaz, mint például eszközök, [eszközalkalmazások,](#device-app)egy IoT-központ, más [Azure-szolgáltatások és háttéralkalmazások.](#back-end-app)

## <a name="subscription"></a>Előfizetés
Az Azure-előfizetés az a hely, ahol a számlázás történik. Minden azure-erőforrást hoz létre, vagy az Azure-szolgáltatás használata egyetlen előfizetéstársított. Számos kvóta is vonatkozik az előfizetés szintjén.

## <a name="system-properties"></a>Rendszertulajdonságok
Az [ikereszköz](iot-hub-devguide-device-twins.md)környezetében a rendszertulajdonságok írásvédettek, és tartalmazzák az eszköz használatára vonatkozó információkat, például az utolsó tevékenység idejét és a kapcsolat állapotát.

## <a name="tags"></a>Címkék
Az [ikereszköz](iot-hub-devguide-device-twins.md)környezetében a címkék olyan eszközmetaadatok, amelyeket a megoldás háttérrendszerének egy JSON-dokumentum tárolja és olvas le. A címkék nem láthatók az eszközön lévő alkalmazások számára.

## <a name="telemetry"></a>Telemetria
Az eszközök telemetriai adatokat gyűjtenek, például a szélsebességet vagy a hőmérsékletet, és adatpont-üzenetek használatával elküldik a telemetriai adatokat egy IoT-központba.

## <a name="token-service"></a>Token szolgáltatás
Egy jogkivonat-szolgáltatás használatával valósíthatja meg az eszközök hitelesítési mechanizmus. Az IoT Hub [megosztott hozzáférési szabályzatot](#shared-access-policy) **használ a DeviceConnect** engedélyekkel *eszközhatókörrel rendelkező* jogkivonatok létrehozásához. Ezek a jogkivonatok lehetővé teszik, hogy egy eszköz csatlakozzon az IoT hubhoz. Az eszköz egy egyéni hitelesítési mechanizmust használ a jogkivonat-szolgáltatással való hitelesítéshez. Ha az eszköz sikeresen hitelesíti magát, a jogkivonat-szolgáltatás egy SAS-jogkivonatot ad ki az IT-központ eléréséhez.

## <a name="twin-queries"></a>Ikerlekérdezések
[Az eszköz- és moduliker-lekérdezések](iot-hub-devguide-query-language.md) az SQL-szerű IoT Hub lekérdezési nyelvet használják az adatok lekéréséhez az eszköz twins vagy modul twins. Ugyanazt az IoT Hub lekérdezési nyelvet [](#job) használhatja az IoT hubon való futtatással kapcsolatos információk lekéréséhez.

## <a name="twin-synchronization"></a>Ikerszinkronizálás
A két szinkronizálás az eszköz twins vagy modul twins [az](#desired-properties) eszközök vagy modulok konfigurálásához, és lekérni [a jelentett tulajdonságokat](#reported-properties) tőlük az ikertárolóban való tároláshoz.

## <a name="x509-client-certificate"></a>X.509 ügyféltanúsítvány
Az eszközök X.509-es tanúsítvánnyal hitelesíthetik magukat [az IoT Hubbal.](#iot-hub) Az X.509-es tanúsítvány használata a [SAS-jogkivonat](#shared-access-signature)használatának alternatívája.
