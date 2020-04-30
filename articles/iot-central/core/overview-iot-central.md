---
title: Mi az az Azure IoT Central? | Microsoft Docs
description: Az Azure IoT Central egy IoT-alkalmazási platform, amely leegyszerűsíti a IoT-megoldások létrehozását, és segít csökkenteni a IoT-kezelési műveletek terheit és költségeit, valamint a fejlesztést. Ez a cikk áttekintést nyújt az Azure IoT Central jellemzőiről.
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 05d73ae09d95879c37e86f5f3e73e35ed4607296
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82560965"
---
# <a name="what-is-azure-iot-central"></a>Mi az az Azure IoT Central?

A IoT Central egy IoT, amely csökkenti a nagyvállalati szintű IoT-megoldások fejlesztésének, kezelésének és karbantartásának terheit és költségeit. A IoT Central való kiépítésének köszönhetően lehetősége van arra, hogy az üzleti környezet IoT-adataival való átalakításának idejére, pénzére és energiára koncentráljon, nem csupán a komplex és folyamatosan fejlődő IoT-infrastruktúra fenntartását és frissítését.

A webes felhasználói felülettel figyelheti az eszközök feltételeit, szabályokat hozhat létre, és több millió eszközt és adatmennyiséget kezelhet életciklusa során. Továbbá lehetővé teszi, hogy az IoT-intelligenciát az üzletági alkalmazásokra kiterjesztve az eszköz elemzéseit is fellépjen.

Ez a cikk IoT Central:

- A projekthez társított tipikus szerepek.
- Egy alkalmazás létrehozása.
- Az eszközök az alkalmazáshoz történő csatlakoztatása
- Az alkalmazás felügyelete.
- IoT Central Azure IoT Edge képességei.
- A Azure IoT Edge Runtime-alapú eszközök csatlakoztatása az alkalmazáshoz.

## <a name="known-issues"></a>Ismert problémák

- A folyamatos adatexportálás nem támogatja a Avro formátumot (inkompatibilitás).
- A GeoJSON jelenleg nem támogatott.
- A Térkép csempe jelenleg nem támogatott.
- A feladatok nem támogatják az összetett típusokat.
- A tömb sémájának típusai nem támogatottak.
- A rendszer csak a C eszközoldali SDK-t és a Node. js-eszközt és a Service SDK-kat támogatja.
- IoT Central jelenleg a Egyesült Államok, Európa, Ázsia és a Csendes-óceáni térség, Ausztrália, Egyesült Királyság és Japán helyszíneken érhető el.
- Az **egyéni alkalmazás (örökölt)** alkalmazás-sablon nem használható az Egyesült Királyság és Japán helyein.
- Az eszköz képességeinek modelljeinek minden, ugyanabban a fájlban definiált illesztőfelülettel kell rendelkezniük.
- A [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) támogatása előzetes verzióban érhető el, és csak a kiválasztott régiókban támogatott.

## <a name="personas"></a>Szerepek

Az IoT Central dokumentációja négy olyan személyre utal, akik IoT Central alkalmazással működnek:

- A _megoldás-szerkesztő_ feladata, hogy meghatározza az alkalmazáshoz kapcsolódó eszközök típusát, és testreszabja az alkalmazást az operátor számára.
- Az _operátor_ kezeli az alkalmazáshoz csatlakoztatott eszközöket.
- A _rendszergazdák_ felelősek az olyan felügyeleti feladatokért, mint például a [felhasználói szerepkörök és engedélyek](howto-administer.md) kezelése az alkalmazáson belül.
- Az _eszközök fejlesztői_ létrehozzák az alkalmazáshoz csatlakoztatott eszközön vagy IoT Edge modulon futó kódot.

## <a name="create-your-iot-central-application"></a>A IoT Central-alkalmazás létrehozása

Megoldás-szerkesztőként a IoT Central használatával létrehozhat egy egyéni, felhőalapú IoT-megoldást a szervezet számára. Az egyéni IoT-megoldások általában a következőket foglalják magukban:

- Egy felhőalapú alkalmazás, amely telemetriai adatokat fogad az eszközökről és lehetővé teszi az eszközök felügyeletét.
- Több eszköz, amelyek egyéni kódokat futtatnak és a felhőalapú alkalmazáshoz csatlakoznak.

Gyorsan üzembe helyezhet egy új IoT Central alkalmazást, és testre szabhatja a böngészőben megadott igényei szerint. Az általános _alkalmazási sablonnal_ vagy a [kereskedelmi](../retail/overview-iot-central-retail.md), [energia](../energy/overview-iot-central-energy.md)-, [kormányzati](../government/overview-iot-central-government.md)és [egészségügyi](../healthcare/overview-iot-central-healthcare.md)alkalmazások egyik iparági alkalmazási sablonjaival is elindítható.

Megoldás-szerkesztőként a webalapú eszközök használatával hozzon létre egy _sablont_ az alkalmazáshoz csatlakozó eszközökhöz. Az eszköz sablonja az a terv, amely meghatározza egy adott típusú eszköz jellemzőit és viselkedését, például:

- Telemetria.
- Az operátor által módosítható üzleti tulajdonságok.
- Azok az eszköztulajdonságok, amelyeket az eszköz állít be, és az alkalmazásban csak olvashatók.
- A tulajdonságok, amelyeket az operátor állít be, amelyek meghatározzák az eszköz viselkedését.

Ez az eszköz a következőket tartalmazza:

- Egy _eszköz-képesség modell_ , amely leírja az eszköz által megvalósított képességeket, például az általa küldött telemetria és az általa jelentett tulajdonságokat.
- Az eszközön nem tárolt Felhőbeli tulajdonságok.
- A IoT Central alkalmazás részét képező testreszabások, irányítópultok és űrlapok.

### <a name="pricing"></a>Díjszabás

A 7 napos ingyenes próbaverzió használatával IoT Central alkalmazást hozhat létre, vagy használhat standard díjszabási csomagot is.

- Az *ingyenes* csomag használatával létrehozott alkalmazások hét napig ingyenesen használhatók, és legfeljebb öt eszközt támogatnak. Egy standard díjszabási csomag használatára a lejárat előtt bármikor átalakítható.
- A *standard* csomag használatával létrehozott alkalmazások számlázása eszközönként történik, a **standard 1** vagy **Standard 2** díjszabási csomaggal pedig az első két eszköz ingyenesen használható. Az ingyenes és standard díjszabási csomagokról az [Azure IoT Central díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/iot-central/)tájékozódhat.

### <a name="create-device-templates"></a>Eszközök sablonjainak létrehozása

A [IoT Plug and Play (előzetes verzió)](../../iot-pnp/overview-iot-plug-and-play.md) lehetővé teszi, hogy a IoT Central beágyazott eszköz kódjának írása nélkül integrálja az eszközöket. A IoT Plug and Play (előzetes verzió) magja egy eszköz-képesség modell sémája, amely leírja az eszközök képességeit. IoT Central alkalmazásban az eszközök sablonjai ezeket a IoT Plug and Play (előzetes verzió) eszköz-képesség modelleket használják.

Megoldás-szerkesztőként több lehetőség közül választhat az eszközök sablonjainak létrehozásához:

- Importáljon egy eszköz-képességi modellt az [Azure Certified for IoT-Device Catalog eszközből](https://aka.ms/iotdevcat) , majd adja hozzá a IoT Central alkalmazás igényeinek megfelelő Felhőbeli tulajdonságokat, testreszabásokat és irányítópultokat.
- Tervezze meg IoT Central az eszköz sablonját, majd implementálja az eszköz képességeinek modelljét az eszköz kódjában.
- Hozzon létre egy eszköz-képesség modellt a Visual Studio Code használatával, és tegye közzé a modellt egy adattárban. Implementálja az eszköz kódját a modellből, és kapcsolja össze az eszközt a IoT Central alkalmazással. IoT Central megkeresi az eszköz képességeinek modelljét a tárházból, és létrehoz egy egyszerű eszközt a sablonhoz.
- Eszköz-képesség modell létrehozása a Visual Studio Code használatával. Implementálja az eszköz kódját a modellből. Manuálisan importálja az eszköz képességeinek modelljét a IoT Central alkalmazásba, majd adja hozzá a IoT Central alkalmazás igényeinek megfelelő Felhőbeli tulajdonságokat, testreszabásokat és irányítópultokat.

Megoldás-szerkesztőként a IoT Central segítségével programkódot hozhatja meg az eszközök teszteléséhez az eszköz sablonjainak ellenőrzéséhez.

### <a name="customize-the-ui"></a>A felhasználói felület testreszabása

Megoldás-szerkesztőként testreszabhatja a IoT Central alkalmazás felhasználói felületét azon operátorok esetében is, akik felelősek az alkalmazás napi használatért. A megoldás-szerkesztő által végrehajtható testreszabások:

- Egy eszközsablonban található tulajdonságok és beállítások elrendezésének meghatározása.
- Egyéni irányítópultok konfigurálása, amelyek segítik az operátorokat a megállapítások felismerésében és a problémák gyorsabb megoldásában.
- Egyéni elemzések konfigurálása, amelyek a csatlakoztatott eszközökről származó idősorozat-adatokat vizsgálják.

## <a name="pricing"></a>Díjszabás

A 7 napos ingyenes próbaverzió használatával IoT Central alkalmazást hozhat létre, vagy használhat standard díjszabási csomagot is.

- Az *ingyenes* csomag használatával létrehozott alkalmazások hét napig ingyenesen használhatók, és legfeljebb öt eszközt támogatnak. Egy standard díjszabási csomag használatára a lejárat előtt bármikor átalakítható.
- A *standard* csomag használatával létrehozott alkalmazások számlázása eszközönként történik, a **standard 1** vagy **Standard 2** díjszabási csomaggal pedig az első két eszköz ingyenesen használható. További információ a [IoT Central díjszabásáról](https://aka.ms/iotcentral-pricing).

## <a name="connect-your-devices"></a>Az eszközök csatlakoztatása

Az Azure IoT Central az [azure IoT hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) használatával kezeli az összes eszköz regisztrációját és kapcsolódását.

A DPS használata lehetővé teszi a következőket:

- IoT Central az eszközök nagy léptékű bevezetésének és csatlakoztatásának támogatásához.
- Az eszköz hitelesítő adatainak előállításához és az eszközök offline konfigurálásához az eszközök IoT Central felhasználói felületen keresztül történő regisztrálása nélkül.
- Közös hozzáférésű aláírásokkal való kapcsolódáshoz használt eszközök.
- Az iparági szabványnak megfelelő X. 509 tanúsítványok használatával csatlakoztatható eszközök.
- A saját eszközök azonosítóinak használatával regisztrálhatja az eszközöket a IoT Centralban. A saját eszköz-azonosítók használatával egyszerűbbé válik a meglévő Back-Office rendszerekkel való integráció.
- Egyetlen, egységes módszer az eszközök IoT Centralhoz való csatlakoztatására.

További információért lásd: [Csatlakozás az Azure IoT Centralhoz](./concepts-get-connected.md).

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge-eszközök

Továbbá az [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)-k használatával létrehozott eszközökhöz [Azure IoT Edge eszközöket](../../iot-edge/about-iot-edge.md) is csatlakozhat egy IoT Central alkalmazáshoz. Azure IoT Edge lehetővé teszi, hogy közvetlenül a IoT Central által felügyelt IoT-eszközökön futtassa a felhőalapú intelligenciát és az egyéni logikát. A IoT Edge Runtime a következőket teszi lehetővé:

- A számítási feladatok telepítése és frissítése az eszközön.
- Azure IoT Edge biztonsági szabványok fenntartása az eszközön.
- Győződjön meg arról, hogy IoT Edge modulok mindig futnak.
- Jelentési modul állapota a felhőben távoli figyeléshez.
- Kezelheti az alárendelt levelek eszközei és a IoT Edge eszközök közötti kommunikációt egy IoT Edge eszköz moduljai és egy IoT Edge eszköz és a felhő között.

További információ: [Azure IoT Edge eszközök és IoT Central](concepts-architecture.md#azure-iot-edge-devices).

## <a name="stay-connected"></a>A kapcsolat fenntartása

IoT Central alkalmazásait teljes mértékben a Microsoft üzemelteti, ami csökkenti az alkalmazások felügyeletének terhelését.

Kezelőként a IoT Central alkalmazással felügyelheti az eszközöket a IoT Central-megoldásban. A kezelők az alábbiakhoz hasonló feladatokat végzik el:

- Az alkalmazáshoz csatlakoztatott eszközök monitorozása.
- Az eszközök hibáinak keresése és elhárítása.
- Új eszközök üzembe helyezése.

Megoldás-szerkesztőként olyan egyéni szabályokat és műveleteket adhat meg, amelyek a csatlakoztatott eszközökről végeznek adatátvitelt. Az operátor eszközszinten engedélyezheti vagy letilthatja ezeket a szabályokat, az alkalmazáson belüli feladatok vezérlése és automatizálása céljából.

A rendszergazdák [felhasználói szerepkörökkel és engedélyekkel](howto-administer.md)kezelhetik az alkalmazáshoz való hozzáférést.

A nagy léptékű működésre tervezett IoT-megoldások esetében fontos az eszközkezelés strukturált megközelítése. Nem elég egyszerűen csatlakoztatni az eszközöket a felhőhöz, és az eszközöket csatlakoztatni és kifogástalan állapotba kell hozni. Az operátorok az alábbi IoT Central képességekkel kezelhetik eszközeiket az alkalmazások életciklusa során:

### <a name="dashboards"></a>Irányítópultok 

A beépített [irányítópultok](./howto-set-up-template.md#generate-default-views) testreszabható felhasználói felületet biztosítanak az eszközök állapotának és telemetria figyeléséhez. Kezdje egy beépített irányítópulttal egy [alkalmazás-sablonban](howto-use-app-templates.md) , vagy hozzon létre saját irányítópultokat az operátorok igényei szerint. Az irányítópultokat megoszthatja az alkalmazás összes felhasználójának használatával, vagy megtarthatja azokat.

### <a name="rules-and-actions"></a>Szabályok és műveletek

Az eszköz állapotán és telemetria alapuló [Egyéni szabályok](tutorial-create-telemetry-rules.md) alapján azonosíthatja az eszközöket, amelyeknek figyelmet igényelnek. Konfigurálja a megfelelő személyek értesítéséhez szükséges műveleteket, és gondoskodjon arról, hogy a javítási intézkedések időben legyenek elvégezve.

### <a name="jobs"></a>Feladatok

A [feladatok](howto-run-a-job.md) lehetővé teszik az eszközökön történő egyszeri vagy tömeges frissítés beállítását tulajdonságok vagy hívási parancsok megadásával. 

### <a name="user-roles-and-permissions"></a>Felhasználói szerepkörök és engedélyek

A [szerepkörök és engedélyek](howto-manage-users-roles.md) lehetővé teszik a rendszergazdák számára az egyes felhasználói élmények testreszabását. A rendszergazdák a webes felhasználói felületen hozhatnak létre szerepköröket és rendelhetnek engedélyeket.

## <a name="transform-your-iot-data"></a>IoT-adatai átalakítása

Az alkalmazási platformként a IoT Central lehetővé teszi a IoT-információk átalakítását az üzleti elemzésekben, amelyek a gyakorlatban hasznosítható eredményeket vezetnek. A [szabályok](./tutorial-create-telemetry-rules.md), az [adatexportálás](./howto-export-data.md)és a [nyilvános REST API](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) példák arra, hogyan integrálható az IoT Central az üzletági alkalmazásokkal:

![Hogyan alakíthatja át a IoT Central a IoT-adatait](media/overview-iot-central/transform.png)

### <a name="monitor-device-health-and-operations-using-rules"></a>Eszközök állapotának és műveleteinek figyelése szabályok használatával

Amikor az eszközök csatlakoznak és adatokat küldenek, a szabályok azonosítják a problémákat észlelő eszközöket, vagy hibaüzeneteket küldenek, így minimális állásidővel javíthatók. Hozzon létre szabályokat a IoT Central alkalmazásban az eszközök telemetria figyeléséhez, és figyelmeztessen egy operátort, ha egy metrika átlép egy küszöbértéket, vagy egy eszköz egy adott üzenetet küld. A szabályokhoz tartozó e-mail-műveletek és webhookok értesítik a megfelelő személyeket és a megfelelő alsóbb rétegbeli rendszereket.

### <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Egyéni elemzések és feldolgozás futtatása az exportált adatain

Létrehozhat olyan üzleti elemzéseket, mint például a gépi hatékonysági trendek meghatározása vagy a jövőbeli energiafelhasználás megjósolása a gyári szinten, ha egyéni elemzési folyamatokat hoz létre az telemetria az eszközökről való feldolgozásához és az eredmények tárolásához. Konfigurálja az adatexportálást a IoT Central alkalmazásban a telemetria, az eszköz tulajdonságainak változásainak és az eszköz sablonjának más szolgáltatásokra való módosításainak exportálására, ahol elemezheti, tárolhatja és megjelenítheti az adatait az Ön által előnyben részesített eszközökkel.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Egyéni IoT-megoldások és-integrációk létrehozása a REST API-kkal

IoT-megoldások létrehozása, például:

- Az eszközök távoli beállítására és vezérlésére képes mobil Companion-alkalmazások.
- Olyan egyéni integrációk, amelyek lehetővé teszik a meglévő üzletági alkalmazások számára a IoT-eszközökkel és-adataikkal való interakciót.
- Eszközkezelő alkalmazások az eszközök modellezéséhez, előkészítéséhez, kezeléséhez és az adathozzáféréshez.

## <a name="quotas"></a>Kvóták

Minden Azure-előfizetéshez tartozik alapértelmezett kvóta, amely hatással lehet a IoT-megoldás hatókörére. Jelenleg IoT Central korlátozza az előfizetésben üzembe helyezhető alkalmazások számát 10-re. Ha a korlát növelésére van szüksége, forduljon a [Microsoft ügyfélszolgálatához](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a IoT Centralt, néhány javasolt lépés:

- Ismerje meg az elérhető [Azure-technológiákat és-szolgáltatásokat a IoT-megoldások létrehozásához](../../iot-fundamentals/iot-services-and-technologies.md).
- Ismerkedés az [Azure IoT Central kezelőfelületével](overview-iot-central-tour.md).
- [Egy Azure IoT Central-alkalmazás létrehozásának](quick-deploy-iot-central.md) első lépései.
- További információ a [IoT Plug and Play (előzetes verzió)](../../iot-pnp/overview-iot-plug-and-play.md)szolgáltatásról.
- Útmutató [Azure IoT Edge eszköz csatlakoztatásához](./tutorial-add-edge-as-leaf-device.md).
- További információ az [Azure IoT technológiákról és szolgáltatásokról](../../iot-fundamentals/iot-services-and-technologies.md).

Ha Ön egy eszköz fejlesztője, és szeretne belemerülni egy kódra, a javasolt következő lépés egy [ügyfélalkalmazás létrehozása és csatlakoztatása az Azure IoT Central-alkalmazáshoz](./tutorial-connect-device-nodejs.md).
