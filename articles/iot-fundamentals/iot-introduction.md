---
title: Az Azure Internet of Things (IoT) bemutatása
description: Bevezetés alapjait mutatja be, az Azure IoT- és az IoT-szolgáltatások, beleértve a példákat, amelyek segítségével tájékoztatja az IoT használatát mutatják be.
author: robinsh
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 10/11/2018
ms.author: robinsh
ms.openlocfilehash: ca99f9ac36281ecddf41bcc228440adcad90412b
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582428"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Mi az Azure Internet of Things (IoT)?

Az Azure Internet of Things (IoT) gyűjteménye, csatlakoztatása, figyelése és több milliárd IoT-eszközök felügyelete a Microsoft által felügyelt felhőalapú szolgáltatásokat. Egyszerűbben fogalmazva a IoT-megoldás épül fel egy vagy több IoT-eszközök és a egy vagy több háttér-szolgáltatás a felhőben futó, amely kommunikálni egymással. 

Ez a cikk bemutatja az alapokat, az IoT, használati esetek megemlít és röviden ismerteti az elérhető nyolc külön szolgáltatás. Megismerni, mi érhető el, hogy ismerhetik fel a forgatókönyv további szorosan tervező segítségével meg szeretné.

## <a name="introduction"></a>Bevezetés

Az IoT-megoldások fő részei a következők: eszközök, háttér-szolgáltatások és a kettő közötti kommunikációhoz. 

### <a name="iot-devices"></a>IoT-eszközök

Eszközök általában épülnek fel, csatolt censors egy áramköri lap, amely csatlakozik az internethez. Sok eszköz lapka Wi-Fi-n keresztül kommunikálnak. Íme néhány példa az IoT-eszközök:

* egy távoli olaj szivattyú pressure érzékelő
* egy légkondicionáló egység hőmérséklettel és páratartalommal kapcsolatos érzékelők
* az itt szereplő gyorsulásmérő
* szoba jelenlét érzékelők

Prototípus-készítés gyakran használt két eszközök lesznek az alapszintű MX lapka IoT Devkit a Microsoft és a Raspberry PI eszközökről. Az MX-lapkához Devkit hőmérséklet, nyomás, páratartalom, valamint egy Giroszkóp és érzékelőből, egy magnetométer és a Wi-Fi-lapkához beépített érzékelők rendelkezik. Raspberry PI egy IoT-eszköz, amelyhez is csatlakoztatható érzékelők, számos különböző típusú, ezért kiválaszthatja, hogy pontosan mit kell a forgatókönyvhöz. 

A [IoT eszközoldali SDK-k](../iot-hub/iot-hub-devguide-sdks.md) lehetővé teszi olyan alkalmazások fordítása, az eszközök futtatása a szükséges feladatok elvégzéséhez. Az SDK-k telemetriát küldjön az IoT hubhoz, üzenetek és a frissítések fogadása az IoT hubról, és így tovább.

### <a name="communication"></a>Kommunikáció

Az eszköz tud kommunikálni a háttérszolgáltatások mindkét irányban. Az alábbiakban néhány ötletet, amely az eszköz képes kommunikálni a háttér-megoldás.

#### <a name="examples"></a>Példák 

* Az eszköz lehet, hogy hőmérséklet a küldjön egy mobil hűtő teherautó 5 percenként egy IoT hubra. 

* A háttérszolgáltatás is kérje meg az eszközt, hogy a probléma diagnosztizálása érdekében gyakran telemetriát. 

* Az eszköz küldhetnek riasztásokat, olvassa el az érzékelőkről származó értékek alapján. Például ha a figyelés, a batch reaktor kémiai üzemben érdemes riasztás küldése, amikor a hőmérséklet meghaladja a megadott érték.

* Az eszköz képes információkat küldeni a irányítópultra emberi megtekintése. Például egy vezérlő szabad terület a finomítási valószínűleg a hőmérséklet és nyomás egyes cső, valamint a áthaladó pipe, így az operátorok azt. 

Ezeket a feladatokat, és más segítségével valósítható meg a [IoT eszközoldali SDK-k](../iot-hub/iot-hub-devguide-sdks.md).

#### <a name="connection-considerations"></a>Kapcsolat kapcsolatos szempontok

Az IoT-megoldások esetében gyakran az eszközök biztonságos és megbízható csatlakoztatása jelenti a legnagyobb kihívást. Ennek oka az, IoT-eszközök más jellemzőkkel ügyfelek, például böngészők vagy mobilalkalmazások képest rendelkeznek. Pontosabban, az IoT-eszközök:

* Általában beágyazott, emberi beavatkozást nem igénylő rendszerek (a telefonokkal ellentétben).

* Távoli helyeken is üzembe helyezhetők, ahol a fizikai hozzáférés drága lenne.

* Előfordulhat, hogy csak a megoldás háttérrendszerén keresztül érhetők el. Az eszközzel más módon nem lehet kapcsolatba lépni.

* Áramellátásuk és feldolgozási erőforrásaik korlátozottak lehetnek.

* A hálózati kapcsolat időszakos, lassú vagy drága lehet.

* Saját fejlesztésű, egyedi vagy iparág-specifikus alkalmazás-protokollokra lehet szükség.

### <a name="back-end-services"></a>Háttérszolgáltatások 

Íme néhány a egy háttér-szolgáltatás által biztosított funkciókat.

* Nagy mennyiségű telemetriai adatok fogadása az eszközök, és hogy hogyan dolgozza fel, és azokat.

* Elemzés, valós idejű vagy utólagosak biztosít a telemetria elemzése.

* Parancsok küldését a felhőből egy eszközre. 

* Eszközök és szabályozhatja, hogy mely eszközök csatlakozhatnak az infrastruktúra üzembe helyezése.

* Az eszközök állapotának szabályozásához és a tevékenységeik megfigyeléséhez.

Egy prediktív karbantartási forgatókönyvben például a felhő háttérrendszerének korábbi telemetriai adatokat tárolja. A megoldás ezeket az adatokat használja a lehetséges rendellenes viselkedés azonosításához az adott szivattyúknál, mielőtt az komoly problémát okozna. Az adatelemzés segítségével azonosítani tudja, hogy megelőző megoldásként vissza kell küldeni egy parancsot az eszköznek, hogy az hajtson végre egy korrekciós műveletet. A folyamat létrehoz egy automatizált visszajelzési hurkot az eszköz és a felhő között, amely jelentősen növeli a megoldás hatékonyságát.

## <a name="an-iot-example"></a>Egy IoT-példa

Íme egy példa egy vállalati IoT segítségével millió dollárt megtakarítani. 

Egy hatalmas szarvasmarha ranch tehenek akár több száz van. Ennyi tehenek nyomon követheti, és azok még ennek során, és vezetési körül sok igényel nagyon fontos. Minden egyetlen vonatkozó, például a GPS-koordinátáit és hőmérséklettel kapcsolatos adatokat küld egy adatbázisba írandó háttérszolgáltatás érzékelők azok csatolva.

Ott van egy analitikai szolgáltatás, amely megvizsgálja a bejövő adatok, és elemzi az adatokat az egyes vonatkozó, ellenőrizni az alábbihoz hasonló kérdéseket:

* A vonatkozó fut egy hőmérséklet-e? Mennyi ideig a vonatkozó futási idejének hőmérséklet? Ha egy napnál hosszabb volt, a GPS-koordinátáit lekérése, és keresse meg a vonatkozó, és ha szükséges, kezelje azt a antibiotikumokat találjanak. 

* A vonatkozó van ugyanazon a helyen a több mint egy nap? Ha igen, a GPS-koordinátáit lekérése, és nyissa meg a vonatkozó találja. Minden olyan egy sziklafal csökkent a mintavételt? A vonatkozó többen megsérültek egy Nem a vonatkozó segítségre van szüksége? 

Az IoT-megoldás megvalósítása tette lehetővé a cég számára, ellenőrizze, gyorsan kezelheti a tehenek és Kivágás fordítania a vezetői körül ellenőrzi az állatokat, pénzt rengeteg történő mentésével volt idő mennyisége. A vállalatok IoT használatáról további valós beszédhelyzetek példák: [Microsoft IOT műszaki esettanulmányok](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

## <a name="iot-services"></a>IoT-szolgáltatások

Nincsenek több IoT-hez kapcsolódó szolgáltatások az Azure-ban, és lehet kikapcsolnia döntse el, melyiket szeretné használni. Bizonyos, például az IoT-központ és az IoT-megoldásgyorsítók, adja meg a sablonok segítségével létrehozhat saját megoldást, és gyorsan használatba vehető. Elérhető más szolgáltatásokhoz a saját megoldások fejlesztését is teljes mértékben meg is – az összes, amelytől a mennyi súgó szeretne, és mekkora vezérlő. Itt érhető el a szolgáltatások listáját, valamint a mi használhatja azokat a.

1. [**IoT-központ**](../iot-central/overview-iot-central.md): Ez az a Szolgáltatottszoftver-megoldás, amely segítséget nyújt csatlakoztatása, monitorozása és az ioT-eszközök felügyeletére. Először válasszon ki egy sablont az eszköz típusának megfelelő és mentésekkel és ezek tesztelésével egy alapszintű IoT Central alkalmazáshoz, amelyet az eszköz az operátorok fog használni. Az IoT Central-alkalmazást is lehetővé teszi az eszközök figyelésére, és új eszközök kiépítése. Ez a szolgáltatás részletes szolgáltatás-testreszabást nem igénylő egyszerű megoldások van. 

2. [**IoT-megoldásgyorsítók**](/azure/iot-suite): Ez a PaaS-megoldások segítségével felgyorsíthatja az IoT-megoldások fejlesztését gyűjteménye. Kezdje egy megadott IoT-megoldás, és ezután teljes mértékben testre szabhatja, hogy a megoldás az igényeinek megfelelően. Szüksége van a háttér-testreszabásához Java vagy .NET képességek és a megjelenítés testreszabásához JavaScript-ismeretek. 

3. [**Az IoT Hub**](/azure/iot-hub/): Ez a szolgáltatás lehetővé teszi az eszközök csatlakozni az IoT hub és monitorozását és ellenőrzését is több milliárd IoT-eszközök. Ez különösen hasznos, ha az IoT-eszközök és a háttérrendszer közötti kétirányú kommunikáció van szüksége. Ez az IoT-központ és az IoT-megoldásgyorsítók a mögöttes szolgáltatás. 

4. [**IoT Hub Device Provisioning Service**](/azure/iot-dps/): Ez az, hogy biztonságosan kiépítése az IoT hub-eszközök használatával IoT hub segítő szolgáltatása. Ezzel a szolgáltatással egyszerűen létrehozhat több millió eszköz rövid idő alatt, ahelyett, hogy egyesével kiépítése. 

5. [**IoT Edge**](/azure/iot-edge/): Ez a szolgáltatás az IoT hubra épül. Az IoT-eszközökön, nem pedig a felhőben lévő adatok elemzéséhez használható. A számítási feladat része az Edge-ben való váltással kevesebb üzenet kell kell küldeni a felhőben. 

6. [**Az Azure digitális Twins**](../digital-twins/index.yml): Ez a szolgáltatás lehetővé teszi a fizikai környezet átfogó modelleket hozhat létre. A kapcsolatok és a személyek, szóközöket és eszközök közötti interakciókat modellezheti. Ha például tudja jelezni karbantartási Factory, egy elektromos rács követelményei valós idejű energia elemzéséhez, vagy optimalizálja az Office használatát, a rendelkezésre álló területet.

7. [**Time Series Insights**](/azure/time-series-insights): Ez a szolgáltatás lehetővé teszi tárolására, megjelenítését és nagy mennyiségű IoT-eszközök által generált idősorozat-adatok lekérdezése. Ezt a szolgáltatást is használhatja az IoT hubbal. 

8. [**Az Azure Maps**](/azure/azure-maps): Ez a szolgáltatás biztosítja a webes és mobilalkalmazások földrajzi információk. Nincs olyan teljes körű REST API-k, valamint egy webalapú JavaScript-vezérlővel rendelkezik az Apple- és Windows eszközökhöz azon asztali vagy mobil rugalmas alkalmazások létrehozásához használható.

## <a name="next-steps"></a>További lépések

Bizonyos tényleges üzleti esetekben és a használt architektúra, tekintse meg a [a Microsoft Azure IoT műszaki esettanulmányok](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured).

Az egy IoT DevKit kipróbálni néhány mintaprojektjeit, lásd: a [IoT DevKit projekt katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/). 

A különböző szolgáltatásokat és a használatuk részletesebb magyarázatáért lásd: [Azure IoT-szolgáltatások és technológiák](iot-services-and-technologies.md).

Az IoT-architektúra részletes ismertetéséhez tekintse át a következő dokumentumot: [Microsoft Azure IoT Reference Architecture](https://aka.ms/iotrefarchitecture) (Microsoft Azure IoT-referenciaarchitektúra).
