---
title: Azure eszközök internetes hálózata (IoT) technológiák és megoldások
description: Ismerteti az Azure IoT-megoldás létrehozásához használható technológiák és szolgáltatások gyűjteménye.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.openlocfilehash: 0b04b5170c13f6f6c3fd74976461f03e4367060a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77046061"
---
# <a name="azure-technologies-and-services-for-creating-iot-solutions"></a>Azure-technológiák és -szolgáltatások IoT-megoldások létrehozásához

Az Azure IoT-technológiák és -szolgáltatások olyan IoT-megoldások széles skáláját hozhatják létre, amelyek lehetővé teszik a digitális átalakítást a szervezet számára. Megteheti például a következőt:

- Használja [az Azure IoT Central,](https://apps.azureiotcentral.com)felügyelt IoT-alkalmazás platform, egy biztonságos, nagyvállalati szintű IoT-megoldás létrehozásához és üzembe helyezéséhez. Az IoT Central iparág-specifikus alkalmazássablonok, például a kiskereskedelem és az egészségügy gyűjteményét tartalmazza a megoldásfejlesztési folyamat felgyorsítása érdekében.
- Bővítse ki a nyílt forráskódú kódbázist egy Azure [IoT-megoldásgyorsítóhoz](https://www.azureiotsolutions.com) egy közös IoT-forgatókönyv, például távoli figyelés vagy prediktív karbantartás megvalósításához.
- Azure IoT platformszolgáltatások, például [az Azure IoT Hub](../iot-hub/about-iot-hub.md) és az Azure [IoT-eszköz SDK-k](../iot-hub/iot-hub-devguide-sdks.md) egyéni IoT-megoldás a semmiből.

![Azure IoT-technológiák, -szolgáltatások és -megoldások](./media/iot-services-and-technologies/iot-technologies-services.png)

## <a name="azure-iot-central"></a>Azure IoT Central

Az [IoT Central alkalmazásplatform](https://apps.azureiotcentral.com) csökkenti a nagyvállalati szintű IoT-megoldások fejlesztésének, kezelésének és karbantartásának terheit és költségeit. Az IoT Central testre szabható webes felhasználói felülete lehetővé teszi az eszközfeltételek figyelésével, szabályok létrehozásában, valamint eszközök és adataik millióinak kezelésével azok teljes életciklusa során. Az IoT Central API-felülete programozott hozzáférést biztosít az IoT-megoldás konfigurálásához és kezeléséhez.

Az Azure IoT Central egy teljes körűen felügyelt alkalmazásplatform, amely segítségével egyéni IoT-megoldásokat hozhat létre. Az IoT Central alkalmazássablonokat használ a megoldások létrehozásához. Vannak sablonok általános megoldások és az egyes iparágak, mint például az energia, az egészségügy, a kormány, és a kiskereskedelem. Az IoT Central alkalmazássablonjai lehetővé teszik egy IoT Central-alkalmazás percek alatt történő üzembe helyezését, amelyet a témákkal, irányítópultokkal és nézetekkel testre szabhat.

Válassza ki az eszközöket az [Azure Certified for IoT eszközkatalógusból,](https://catalog.azureiotsolutions.com) hogy gyorsan csatlakozzon a megoldáshoz. Az IoT Central webes felhasználói felületével figyelheti és kezelheti eszközeit, hogy egészségesek és csatlakoztatva maradjanak. Összekötők és API-k használatával integrálhatja az IoT Central alkalmazást más üzleti alkalmazásokkal.

Teljes körűen felügyelt alkalmazásplatformként az IoT Central egy egyszerű, kiszámítható díjszabási modellel rendelkezik.

## <a name="azure-iot-solution-accelerators"></a>Azure IoT-megoldásgyorsítók

Az [Azure IoT-megoldásgyorsítók](https://www.azureiotsolutions.com) testre szabható nagyvállalati szintű megoldások gyűjteménye. Ezeket a megoldásokat telepítheti, ahogy vannak, vagy egyéni IoT-megoldást fejleszthet a nyílt forráskódú Java vagy .NET forráskód használatával.

Az Azure IoT-megoldásgyorsítók magas szintű vezérlést biztosítanak az IoT-megoldás felett. A megoldásgyorsítók előre elkészített megoldásokat tartalmaznak a gyakori IoT-forgatókönyvekhez, amelyeket percek alatt telepíthet az Azure-előfizetésére. A forgatókönyvek a következők:

  - Távoli figyelés
  - Csatlakoztatott gyár
  - Prediktív karbantartás
  - Eszközszimuláció

Az összes megoldásgyorsító nyílt forráskódú kódbázisa elérhető a GitHubon. Töltse le a kódot a megoldásgyorsító testreszabásához, hogy megfeleljen az ön által meghatározott IoT-követelményeknek.

A megoldásgyorsítók olyan Azure-szolgáltatásokat használnak, mint például az Azure IoT Hub és az Azure Storage, amelyeket az Azure-előfizetésben kell kezelnie.

## <a name="custom-solutions"></a>Egyéni megoldások

Ha teljesen új IoT-megoldást szeretne létrehozni, vagy ki szeretne terjeszteni egy IoT Central vagy egy megoldásgyorsító használatával létrehozott megoldást, használja az alábbi Azure IoT-technológiák és -szolgáltatások egyikét:

### <a name="devices"></a>Eszközök

IoT-eszközeit az [Azure IoT Starter Kit-ek](https://catalog.azureiotsolutions.com/kits) egyikével fejlesztheti, vagy választhat egy eszközt az [Azure Certified for IoT eszközkatalógusból.](https://catalog.azureiotsolutions.com) Valósítsa meg a beágyazott kódot a nyílt forráskódú [eszköz SDK-k](../iot-hub/iot-hub-devguide-sdks.md)használatával. Az eszköz SDK-k több operációs rendszert támogatnak, például Linuxot, Windowst és valós idejű operációs rendszereket. Több programozási nyelvhez is vannak SDK-k, például [C,](https://github.com/Azure/azure-iot-sdk-c) [Node.js,](https://github.com/Azure/azure-iot-sdk-node) [Java,](https://github.com/Azure/azure-iot-sdk-java) [.NET](https://github.com/Azure/azure-iot-sdk-csharp)és [Python.](https://github.com/Azure/azure-iot-sdk-python)

Az [IoT Plug and Play Preview](../iot-pnp/overview-iot-plug-and-play.md) szolgáltatás használatával tovább egyszerűsítheti, hogyan hozza létre az eszközök beágyazott kódját. Az IoT Plug and Play lehetővé teszi a megoldásfejlesztők számára, hogy beágyazott kód írása nélkül integrálják az eszközöket a megoldásaikba. Az IoT Plug and Play lényege egy _eszközképességi_ modellséma, amely leírja az eszköz képességeit. Az eszközképességi modell segítségével hozza létre a beágyazott eszközkódot, és konfigurálhat egy felhőalapú megoldást, például egy IoT Central-alkalmazást.

[Az Azure IoT Edge](../iot-edge/about-iot-edge.md) lehetővé teszi, hogy az IoT-számítási feladatok egy részét kiszervezze az Azure felhőszolgáltatásaiból az eszközökre. Az IoT Edge csökkentheti a késést a megoldásban, csökkentheti az eszközök által a felhővel folytatott adatcsere mennyiségét, és engedélyezheti az off-line forgatókönyveket. Az IoT Central ioT Central-i és néhány megoldásgyorsítóból kezelheti az IoT Edge-eszközöket.

Az [Azure Sphere](https://docs.microsoft.com/azure-sphere/product-overview/what-is-azure-sphere) egy biztonságos, magas szintű alkalmazásplatform, amely beépített kommunikációs és biztonsági funkciókkal rendelkezik az internethez csatlakoztatott eszközökhöz. Ez magában foglalja a biztonságos mikrovezérlő egység, egyéni Linux-alapú operációs rendszer, és a felhő-alapú biztonsági szolgáltatás, amely folyamatos, megújuló biztonságot.

### <a name="cloud-connectivity"></a>Felhőbeli kapcsolat

Az [Azure IoT Hub](../iot-hub/about-iot-hub.md) szolgáltatás megbízható és biztonságos kétirányú kommunikációt tesz lehetővé több millió IoT-eszköz és egy felhőalapú megoldás között. [Az Azure IoT Hub-eszközkiépítési szolgáltatás](../iot-dps/about-iot-dps.md) az IoT Hub segítő szolgáltatása. A szolgáltatás nulla érintéssel, just-in-time kiépítése eszközök a megfelelő IoT hub emberi beavatkozás nélkül. Ezek a képességek lehetővé teszik az ügyfelek számára, hogy biztonságos és méretezhető módon több millió eszközt biztosítsanak.

Az IoT Hub a megoldásgyorsítók egyik központi összetevője, és az IoT-megvalósítási kihívások, például a következők teljesítésére használhatja:

* Nagy mennyiségű eszköz csatlakoztatása és felügyelete.
* Nagy mennyiségű telemetriaadat feldolgozása.
* Eszközök irányítása és vezérlése.
* Eszközbiztonság kikényszerítése.

### <a name="bridging-the-gap-between-the-physical-and-digital-worlds"></a>A fizikai és a digitális világ közötti szakadék áthidalása

[Az Azure Digital Twins](../digital-twins/about-digital-twins.md) egy IoT-szolgáltatás, amely lehetővé teszi a fizikai környezet modellezését. Térinformatikai diagramot használ az emberek, terek és eszközök közötti kapcsolatok modellezésére. Az adatok digitális és fizikai világok közötti összevetésével környezettudatos megoldásokat hozhat létre.

Az Iot Central digitális ikerpárokkal szinkronizálja a valós világban lévő eszközöket és adatokat azokkal a digitális modellekkel, amelyek lehetővé teszik a felhasználók számára a csatlakoztatott eszközök figyelését és kezelését.

### <a name="data-and-analytics"></a>Adatok és analitika

Az IoT-eszközök általában nagy mennyiségű idősorozat-adatot generálnak, például az érzékelők hőmérsékleti leolvasásait. [Az Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) csatlakozhat egy IoT-központhoz, olvashatja az eszközökről a telemetriai adatfolyamot, tárolhatja ezeket az adatokat, és lehetővé teheti azok lekérdezését és megjelenítését.

[Az Azure Maps](/azure/azure-maps) olyan térinformatikai szolgáltatások gyűjteménye, amelyek friss leképezési adatokat használnak a webes és mobilalkalmazások pontos földrajzi környezetének biztosításához. Az alkalmazások létrehozásához rest API-t, webalapú JavaScript-vezérlőt vagy Android SDK-t használhat.

## <a name="next-steps"></a>További lépések

A gyakorlati élményért próbálja ki az egyik rövid útmutatót:

- [Azure IoT Central-alkalmazás létrehozása](../iot-central/core/quick-deploy-iot-central.md)
- [Telemetriai adatok küldése egy eszközről egy IoT-központba](../iot-hub/quickstart-send-telemetry-cli.md)
- [Távoli felhőalapú monitorozási megoldás kipróbálása](../iot-accelerators/quickstart-remote-monitoring-deploy.md)
