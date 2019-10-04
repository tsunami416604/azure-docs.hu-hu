---
title: Az Azure Digital Twins áttekintése | Microsoft Docs
description: További információ a térbeli intelligenciát megvalósító Azure IoT-megoldásról, az Azure Digital Twinsről.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.date: 09/17/2019
ms.topic: overview
ms.service: digital-twins
services: digital-twins
ms.custom: mvc
ms.openlocfilehash: 2194afe4784d1b59dc5fd5628e3a9223d13f92be
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949290"
---
# <a name="overview-of-azure-digital-twins"></a>Az Azure Digital Twins áttekintése

Az Azure Digital Twins Preview egy Azure IoT szolgáltatás, amely átfogó modelleket hoz létre a fizikai környezetből. Térbeli intelligenciát ábrázoló diagramokat hozhat létre a személyek, a szóközök és az eszközök közötti kapcsolatok és interakciók modellezéséhez.

Az Azure Digital Twins használatával számos különböző érzékelő helyett fizikai területről is lekérdezheti az adatait. Ez a szolgáltatás segít felépíteni újrafelhasználható, rugalmasan méretezhető, térbeli módon felhasználható élményeit, amelyek a digitális és a fizikai világ különböző pontjain közvetítik az adatátvitelt. Ezek az alkalmazások az egyedi releváns kontextus-funkciókkal bővülnek. 

Az Azure Digital Twins minden típusú környezetre vonatkozik, például a raktárakra, az irodákra, az iskolákra, a kórházakra és a bankokra. Akár a stadionok, a gyárak, a parkolók, a parkok, az intelligens hálózatok és a városok számára is használható. Az alábbi forgatókönyvek az Azure digitális Twins hasznosak lehetnek:

- A gyári karbantartási igények előrejelzése.
- Az elektromos hálózatokra vonatkozó valós idejű energiaellátási követelmények elemzése.
- Optimalizálja a rendelkezésre álló terület használatát egy irodában.
- A napi hőmérséklet nyomon követése több állapot között.
- Elfoglalt drone-útvonalak figyelése.
- Autonóm járművek azonosítása.
- Egy összeállítás kihasználtsági szintjeinek elemzése.
- Keresse meg az áruházban a legforgalmasabb pénztárgép-regisztrációt.

A valós üzleti helyzettől függetlenül valószínű, hogy egy megfelelő digitális példány is kiépíthető az Azure digitális Twins használatával.

Az alábbi videó alaposabban megtekinti az Azure digitális ikreket.

> [!VIDEO https://www.youtube.com/embed/TvN_NxpgyzQ]

## <a name="key-capabilities"></a>Főbb képességek

Az Azure Digital Twins a következő főbb képességekkel rendelkezik.

### <a name="spatial-intelligence-graph"></a>Térbeliintelligencia-diagramok

A [*térbeli intelligencia gráf*](./concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph)vagy a *térbeli gráf*a fizikai környezet virtuális ábrázolása. A segítségével modellezheti a személyek, a helyek és az eszközök közötti kapcsolatokat.

Vegyünk egy olyan intelligens segédprogram-alkalmazást, amely több, a környékén összekapcsolt villamosenergia-használati mérőszámot is magában foglal. Az intelligens segédprogram vállalatának pontosan figyelnie kell és meg kell jósolnia a villamosenergia-használatot és a számlázást. Minden eszközt és érzékelőt a hely és a számlázásra kerülő ügyfél kontextusával kell modellezni. A térbeli intelligencia gráf használatával modellezheti az ilyen típusú összetett kapcsolatokat.

### <a name="digital-twin-object-models"></a>Digitális ikerobjektum-modellek

A [digitális kettős objektumok modelljei](./concepts-objectmodel-spatialgraph.md#digital-twins-object-models) előre definiált eszköz-protokollok és adatsémák. Összehangolják a megoldás tartomány-specifikus igényeit a fejlesztés felgyorsításához és egyszerűsítéséhez.

Előfordulhat például, hogy egy szoba használati alkalmazása előre definiált tárolóhelyeket használ, például az egyetemet, az épületet, a padlót és a termet.

### <a name="multiple-and-nested-tenants"></a>Többszörös és beágyazott bérlők

Olyan megoldásokat hozhat létre, amelyek biztonságosan méretezhetők, és több bérlő számára is felhasználhatók. Több albérlőt is létrehozhat, amelyek elérhetők és elkülönített és biztonságos módon használhatók.

Ilyen például egy olyan lemezterület-kihasználtsági alkalmazás, amely úgy van konfigurálva, hogy elkülönítse a bérlők adatait egy épületen belül. Vagy az alkalmazás használatával egyetlen bérlőhöz tartozó, számos épületben található adategyesítést lehet használni.

### <a name="advanced-compute-capabilities"></a>Fejlett számítási képességek

A [felhasználó által definiált függvények](./concepts-user-defined-functions.md)használatával egyéni függvényeket adhat meg és futtathat a bejövő [eszközök adatain](./concepts-device-ingress.md) , hogy jeleket küldjön előre definiált végpontoknak. Ez a speciális funkció javítja az eszközök feladatainak testreszabását és automatizálását.

Ilyen például egy olyan intelligens mezőgazdasági alkalmazás, amely egy felhasználó által definiált függvényt tartalmaz a talaj nedvességtartalmának érzékelő-és az időjárás-előrejelzés értékeléséhez. Az alkalmazás ezután jeleket küld az öntözési igényekről.

### <a name="built-in-access-control"></a>Beépített hozzáférés-vezérlés

A hozzáférés-és Identitáskezelés-kezelési funkciók, például a [szerepköralapú hozzáférés-vezérlés](./security-role-based-access-control.md) és a [Azure Active Directory](./security-authenticating-apis.md)használatával biztonságosan szabályozhatja az egyéni felhasználók és az eszközök hozzáférését.

Ilyen például egy olyan létesítmény-felügyeleti alkalmazás, amely lehetővé teszi, hogy egy helyiség utasai egy adott tartományon belül beállítsák a hőmérsékletet. A létesítmények kezelői a hőmérsékletet bármely tetszőleges értékre beállíthatják.

### <a name="ecosystem"></a>Ökoszisztéma

Az Azure Digital Twins-példányok számos hatékony Azure-szolgáltatáshoz csatlakoztathatók. Ezek a szolgáltatások többek között az Azure Stream Analytics, az Azure AI és az Azure Storage. Emellett a következők: Azure Maps, Microsoft Mixed Reality, Dynamics 365 vagy Office 365.

Egy példa egy olyan intelligens irodaház-alkalmazásra, amely az Azure Digital ikreket használja a sok emeleten található csapatok és eszközök képviseletére. Mivel az eszközök élő adatátvitelt küldenek a kiépített digitális kettős példányba, Stream Analytics feldolgozza azokat az adatfeldolgozást, amelyekkel a kulcsfontosságú információkhoz juthat. Az adattárolás az Azure Storage-ban történik, és a rendszer megosztható fájlformátumba konvertálja azokat. A fájl a teljes szervezeten belül az Office 365 használatával oszlik meg.

## <a name="solutions-that-benefit-from-azure-digital-twins"></a>Az Azure Digital Twins előnyeit kihasználó megoldások

Az Azure digitális Twins hasznos a fizikai világ és a sok kapcsolat ábrázolásához. Leegyszerűsíti a IoT modellezését, az adatfeldolgozást, az események kezelését és az eszközök nyomon követését. Tekintse át a következő forgatókönyveket több iparágban. A használatuk a következő előnyökkel jár:

* Egy ingatlan-felügyeleti vállalat megjelenítése a terület kihasználtsági szintjeinek időbeli kigyűjtése az irodaház legjobb konfigurálási módjairól.
* A munkahelyi rendelési jegyek elindítása a Mobile apps számára. Felhasználhatja a biztonsági őrök elküldésére, valamint a Házfelügyelői és egyéb szolgáltatások beosztására a kiskereskedelmi térben vagy a sport helyszínén.
* Megmutatjuk, hogy egy épületben hol találhatók a szobák valós időben egy épületben. Ezután segítsen az utasnak az igényeinek megfelelő munkaterületek fenntartásában.
* Nyomon követheti, hogy az eszközök Hol helyezkednek el egy adott helyen belül.
* Optimalizálja az elektromos járművek díjszabását a felhasználói preferenciák és az energiaellátási korlátok modellezésével.

## <a name="azure-digital-twins-in-the-context-of-other-iot-services"></a>Azure digitális Twins más IoT-szolgáltatások kontextusában

Az Azure Digital Twins az Azure IoT Hub segítségével csatlakozik az IoT-eszközökhöz és -érzékelőkhöz, amelyek naprakész adatokat szolgáltatnak a fizikai világról. Az alábbi ábra bemutatja, hogyan kapcsolódik az Azure digitális Twins más Azure IoT-szolgáltatásokhoz.

[a ![Azure digitális Twins egy Azure-ra épülő szolgáltatás, IoT Hub](media/overview/azure-digital-twins-in-iot-ecosystem.png)](media/overview/azure-digital-twins-in-iot-ecosystem.png#lightbox)

További információ a IoT: [Azure IoT-technológiák és-megoldások](../iot-fundamentals/iot-services-and-technologies.md).

## <a name="next-steps"></a>További lépések

Ugrás az Azure Digital Twins szolgáltatással kapcsolatos rövid bemutatóra:

>[!div class="nextstepaction"]
>[Rövid útmutató: Elérhető szobák keresése az Azure Digital Twins @ no__t-0 használatával

Az Azure Digital Twins használatával alaposan megtekintheti a létesítmények felügyeleti alkalmazásait:

>[!div class="nextstepaction"]
>[Oktatóanyag: Azure digitális Twins üzembe helyezése és térbeli gráf konfigurálása @ no__t-0

Az Azure Digital Twins alapvető fogalmainak ismertetése:

>[!div class="nextstepaction"]
>[A digitális Twins objektummodell és a térbeli intelligencia gráf megismerése](./concepts-objectmodel-spatialgraph.md)