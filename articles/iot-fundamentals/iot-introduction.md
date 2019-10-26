---
title: Bevezetés az Azure eszközök internetes hálózataba (IoT)
description: Bevezetés az Azure IoT és a IoT szolgáltatások alapjaira, beleértve a IoT használatának szemléltetését segítő példákat is.
author: robinsh
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 10/11/2018
ms.author: robinsh
ms.openlocfilehash: 2f46ebcabd98c7a8c3376157c72da9ec5ed424a4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935232"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Mi az Azure eszközök internetes hálózata (IoT)?

Az Azure IoT (eszközök internetes hálózata) a Microsoft által felügyelt felhőszolgáltatások gyűjteménye, amelyek az IoT-hez csatlakozó eszközök millióit kötik össze, monitorozzák és irányítják. Egyszerűbben fogalmazva egy IoT-megoldás egy vagy több IoT-eszközből és egy vagy több, a felhőben futó háttérszolgáltatásból áll, amelyek kommunikálnak egymással. 

Ez a cikk a IoT alapjait, a használati eseteket tárgyalja, és röviden ismerteti az elérhető nyolc különálló szolgáltatást. A rendelkezésre álló elemek megismerésével kiderítheti, hogy mit szeretne jobban megtekinteni a forgatókönyv kialakításához.

## <a name="introduction"></a>Introduction (Bevezetés)

A IoT-megoldások fő részei a következők: eszközök, háttér-szolgáltatások, valamint a kettő közötti kommunikáció. 

### <a name="iot-devices"></a>IoT-eszközök

Az eszközök általában egy, az internethez csatlakozó érzékelőkkel rendelkező áramköri táblából állnak. Számos eszköz Wi-Fi chipen keresztül kommunikál. Íme néhány példa a IoT-eszközökre:

* nyomás érzékelők távoli olajszivattyú
* hőmérséklet-és páratartalom-érzékelők egy légkondicionáló egységben
* gyorsulásmérők egy liftben
* jelenléti érzékelők egy helyiségben

A Microsoft és a málna PI-eszközök alapszintű MX-IoT fejlesztői készlet a prototípusokhoz gyakran használt két eszköz. Az MX-fejlesztői készlet a hőmérséklet, a nyomás, a páratartalom, valamint a giroszkópok és gyorsulásmérők, a magnetometer és a Wi-Fi chipek beépített érzékelőkkel rendelkeznek. A málna PI egy IoT-eszköz, amelyhez számos különböző típusú érzékelőt csatolhat, így kiválaszthatja, hogy pontosan mire van szüksége a forgatókönyvhöz. 

Az elérhető IoT-eszközökről további információért tekintse meg az iparág legnagyobb [katalógusát, amely a IoT tanúsítvánnyal](https://catalog.azureiotsolutions.com/alldevices)rendelkezik.

A [IoT-eszköz SDK](../iot-hub/iot-hub-devguide-sdks.md) -k lehetővé teszik az eszközökön futó alkalmazások kiépítését, így képesek a szükséges feladatok végrehajtására. Az SDK-k segítségével telemetria küldhet az IoT hubhoz, fogadhat üzeneteket és frissítéseket a IoT Hubból, és így tovább.

### <a name="communication"></a>Kommunikáció

Az eszköz mindkét irányban képes kommunikálni a háttér-szolgáltatásokkal. Íme néhány példa arra, hogy az eszköz hogyan tud kommunikálni a háttér-megoldással.

#### <a name="examples"></a>Példák 

* Az eszköz 5 percenként elküldheti a hőmérsékletet egy mobil hűtési teherautóból egy IoT Hubba. 

* A háttérrendszer arra kérheti az eszközt, hogy gyakrabban küldjön telemetria, hogy segítsen a probléma diagnosztizálásában. 

* Az eszköz az érzékelőktől beolvasott értékek alapján küldhet riasztásokat. Ha például egy batch-reaktort figyel egy vegyipari üzemben, érdemes lehet riasztást küldeni, ha a hőmérséklet meghaladja az adott értéket.

* Az eszköz adatokat küldhet az irányítópultnak az emberi operátorok megtekintésére. Például egy olyan vezérlő helyisége, amely az egyes csövek hőmérsékletét és terhelését, valamint az adott csatornán keresztül áramló kötetet mutatja be, lehetővé teszi az operátorok számára a megtekintését. 

Ezeket a feladatokat és egyebeket a [IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md)-k használatával lehet megvalósítani.

#### <a name="connection-considerations"></a>Kapcsolatok szempontjai

Az IoT-megoldások esetében gyakran az eszközök biztonságos és megbízható csatlakoztatása jelenti a legnagyobb kihívást. Ennek az az oka, hogy a IoT-eszközök más jellemzőkkel rendelkeznek, mint a böngészők és a mobil alkalmazások. Pontosabban, az IoT-eszközök:

* Általában beágyazott, emberi beavatkozást nem igénylő rendszerek (a telefonokkal ellentétben).

* Távoli helyeken is üzembe helyezhetők, ahol a fizikai hozzáférés drága lenne.

* Előfordulhat, hogy csak a megoldás háttérrendszerén keresztül érhetők el. Az eszközzel más módon nem lehet kapcsolatba lépni.

* Áramellátásuk és feldolgozási erőforrásaik korlátozottak lehetnek.

* A hálózati kapcsolat időszakos, lassú vagy drága lehet.

* Saját fejlesztésű, egyedi vagy iparág-specifikus alkalmazás-protokollokra lehet szükség.

### <a name="back-end-services"></a>Háttérbeli szolgáltatások 

Íme néhány, a háttérrendszer által biztosított funkció.

* Telemetria fogadása az eszközökről, valamint az adatok feldolgozásának és tárolásának meghatározása.

* Elemezheti a telemetria, hogy valós időben vagy a tény alapján szolgáltasson betekintést.

* Parancsok küldése a felhőből egy adott eszközre. 

* Eszközök kiépítése és annak szabályozása, hogy mely eszközök tudnak csatlakozni az infrastruktúrához.

* Az eszközök állapotának szabályozása és a tevékenységek figyelése.

Egy prediktív karbantartási forgatókönyvben például a Felhőbeli háttér tárolja a korábbi telemetria. A megoldás ezeket az adatokat használja a lehetséges rendellenes viselkedés azonosításához az adott szivattyúknál, mielőtt az komoly problémát okozna. Az adatelemzés segítségével azonosítani tudja, hogy megelőző megoldásként vissza kell küldeni egy parancsot az eszköznek, hogy az hajtson végre egy korrekciós műveletet. A folyamat létrehoz egy automatizált visszajelzési hurkot az eszköz és a felhő között, amely jelentősen növeli a megoldás hatékonyságát.

## <a name="an-iot-example"></a>Példa IoT

Íme egy példa arra, hogy egy vállalat hogyan használta a IoT millió dollárt. 

Hatalmas szarvasmarha-Farm több százezer tehéntel. Nagy mennyiségű, hogy nyomon kövessük a sok tehénet, és tudják, hogyan csinálnak, és sok mindent megtesznek. Az érzékelőket minden egyes tehénhez csatolták, így például a GPS-koordinátákat és a hőmérsékletet a háttér-szolgáltatásba, hogy egy adatbázisba lehessen írni.

Ezután olyan analitikai szolgáltatással rendelkeznek, amely beolvassa a beérkező adatbevitelt, és elemzi az egyes tehénekhez tartozó összes olyan kérdést, amely a következőhöz hasonló:

* A tehén hőmérsékletet futtat? Meddig futott a tehén hőmérséklete? Ha egy napnál hosszabb ideig tart, szerezze be a GPS-koordinátákat, és keresse meg a tehenet, és ha szükséges, kezelje antibiotikumokkal. 

* A tehén ugyanazon a helyen van, mint egy napnál? Ha igen, szerezze be a GPS-koordinátákat, és keresse meg a tehénet. Elhullott a tehén a szikla? Sérült a tehén? Segítségre van szüksége a tehén? 

Ennek a IoT-megoldásnak a megvalósítása révén a vállalat gyorsan ellenőrizheti és kezelheti a teheneket, és lerövidítheti azt az időtartamot, amelyet az állatok ellenőrzésének megkezdése érdekében töltött, így sok pénzt takarít meg. A vállalatok IoT használatáról szóló további valós példákat lásd: [Microsoft technikai esettanulmányok IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

## <a name="iot-services"></a>IoT-szolgáltatások

Az Azure-ban számos IoT kapcsolódó szolgáltatás található, és zavaró lehet annak megállapítása, hogy melyiket kívánja használni. Egyesek, például a IoT Central és a IoT megoldás-gyorsítók olyan sablonokat biztosítanak, amelyek segítenek a saját megoldás létrehozásában és a gyors kezdésben. Saját megoldásait is teljes mértékben kifejlesztheti más elérhető szolgáltatásokkal – mindez attól függ, hogy mennyi segítségre van szüksége, és hogy Mennyibe kerül a vezérlés. Itt láthatja az elérhető szolgáltatások listáját, valamint azt, hogy mire használhatja őket.

1. [**IoT Central**](../iot-central/core/overview-iot-central.md): ez egy SaaS-megoldás, amely segítséget nyújt a IoT-eszközök csatlakoztatásához, figyeléséhez és felügyeletéhez. Az indításhoz ki kell választania egy sablont az eszköz típusához, és létre kell hoznia és tesztelni kell egy alapszintű IoT Central alkalmazást, amelyet az eszköz üzemeltetői használni fognak. A IoT Central alkalmazás az eszközök figyelését és az új eszközök kiépítését is lehetővé teszi. Ez a szolgáltatás olyan egyszerű megoldásokhoz használható, amelyek nem igénylik a mélyreható szolgáltatás testreszabását. 

2. [**IoT megoldás-gyorsítók**](/azure/iot-suite): Ez a IoT-megoldások fejlesztésének felgyorsítására szolgáló, a-ben felhasználható Pásti-megoldások gyűjteménye. Egy megadott IoT-megoldással kezdheti meg, majd teljes mértékben testreszabhatja a megoldást az igényeinek megfelelően. Java-vagy .NET-ismeretekre van szüksége a háttérbeli és a JavaScript-ismeretek testreszabásához a vizualizáció testreszabásához. 

3. [**IoT hub**](/azure/iot-hub/): Ez a szolgáltatás lehetővé teszi az eszközökről egy IoT-hubhoz való kapcsolódást, valamint több milliárd IoT-eszköz figyelését és felügyeletét. Ez különösen akkor hasznos, ha a IoT-eszközök és a háttér közötti kétirányú kommunikációra van szükség. Ez a mögöttes szolgáltatás a IoT Central és a IoT megoldás-gyorssegédek esetében. 

4. [**IoT hub Device Provisioning Service**](/azure/iot-dps/): ez egy segítő szolgáltatás a IoT hub számára, amellyel biztonságos módon kiépítheti az eszközöket az IoT hubhoz. Ezzel a szolgáltatással könnyedén hozhat létre több millió eszközt, ahelyett, hogy egyenként kiépíti őket. 

5. [**IoT Edge**](/azure/iot-edge/): a szolgáltatás a IoT hubra épül. Felhasználható a IoT-eszközökön lévő, nem a felhőben tárolt adatelemzésre. Ha a számítási feladat részeit a szélébe helyezi, kevesebb üzenetet kell elküldeni a felhőbe. 

6. [**Azure digitális Twins**](../digital-twins/index.yml): Ez a szolgáltatás lehetővé teszi a fizikai környezet átfogó modelljeinek létrehozását. A kapcsolatok és a személyek, a szóközök és az eszközök közötti interakciók modellezésére is lehetőség van. Előre jelezheti például a gyári karbantartási igényeket, elemezheti az elektromos hálózatok valós idejű energiaellátási követelményeit, vagy optimalizálhatja a rendelkezésre álló terület használatát egy irodában.

7. [**Time Series Insights**](/azure/time-series-insights): Ez a szolgáltatás lehetővé teszi, hogy a IoT-eszközök által generált nagy mennyiségű idősorozat-adathalmazt tárolja, megjelenítse és lekérdezze. Ezt a szolgáltatást a IoT Hub használhatja. 

8. [**Azure Maps**](/azure/azure-maps): Ez a szolgáltatás a webes és mobil alkalmazások számára biztosít földrajzi információkat. A REST API-k és a webalapú JavaScript-vezérlők teljes választékát, valamint az Apple és a Windows rendszerű eszközökön futó asztali vagy mobil alkalmazásokban működő rugalmas alkalmazások létrehozására használható.

## <a name="next-steps"></a>Következő lépések

Egyes tényleges üzleti esetekben és a használt architektúrában tekintse meg a [Microsoft Azure IoT műszaki esettanulmányok](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)című témakört.

Egyes IoT-fejlesztői készlet kipróbálható példákért tekintse meg a [IoT fejlesztői készlet Project Catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)című részt. 

A különböző szolgáltatások és azok használatának részletes ismertetését lásd: [Azure IoT Services és Technologies](iot-services-and-technologies.md).

Az IoT-architektúra részletes ismertetéséhez tekintse át a következő dokumentumot: [Microsoft Azure IoT Reference Architecture](https://aka.ms/iotrefarchitecture) (Microsoft Azure IoT-referenciaarchitektúra).
