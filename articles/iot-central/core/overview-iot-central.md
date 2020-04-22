---
title: Mi az az Azure IoT Central? | Microsoft Docs
description: Az Azure IoT Central egy IoT-alkalmazásplatform, amely leegyszerűsíti az IoT-megoldások létrehozását, és segít csökkenteni az IoT-kezelési műveletek és a fejlesztés terheit és költségeit. Ez a cikk áttekintést nyújt az Azure IoT Central jellemzőiről.
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: ffaab22efb2f6969f03720abba4a7afc6387021a
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758219"
---
# <a name="what-is-azure-iot-central"></a>Mi az az Azure IoT Central?

Az IoT Central egy IoT-alkalmazásplatform, amely csökkenti a nagyvállalati szintű IoT-megoldások fejlesztésének, kezelésének és karbantartásának terheit és költségeit. Ha úgy dönt, hogy az IoT Central segítségével épít, az időt, a pénzt és az energiát arra összpontosíthatja, hogy az IoT-adatokkal alakítsa át vállalkozását, ahelyett, hogy csak egy összetett és folyamatosan fejlődő IoT-infrastruktúrát tartna fenn és frissítene.

A webes felhasználói felület lehetővé teszi az eszközállapotok figyelését, szabályok létrehozását, valamint több millió eszköz és adat kezelése az életciklusuk során. Emellett lehetővé teszi, hogy az IoT-intelligencia üzleti alkalmazásokra való kiterjesztésével az eszközelemzési adatokalapján is fellépjen.

Ez a cikk ismerteti az IoT Central:

- A projekthez társított tipikus szerepek.
- Egy alkalmazás létrehozása.
- Az eszközök az alkalmazáshoz történő csatlakoztatása
- Az alkalmazás felügyelete.
- Az Azure IoT Edge-képességek az IoT Centralban.
- Az Azure IoT Edge futásidejű eszközeinek az alkalmazáshoz való csatlakoztatása.

## <a name="known-issues"></a>Ismert problémák

- A folyamatos adatexportálás nem támogatja az Avro formátumot (inkompatibilitás).
- A GeoJSON jelenleg nem támogatott.
- A térképcsempe jelenleg nem támogatott.
- A feladatok nem támogatják az összetett típusokat.
- A tömbsématípusok nem támogatottak.
- Csak a C-eszköz SDK és a Node.js eszköz és szolgáltatás SDK-k támogatottak.
- Az IoT Central jelenleg az Egyesült Államokban, Európában, ázsia és a csendes-óceáni térségben, Ausztráliában, az Egyesült Királyságban és Japánban érhető el.
- Az Egyéni **alkalmazás (örökölt)** alkalmazássablon nem használható az Egyesült Királyságban és Japánban.
- Az eszközképességi modelleknek ugyanabban a fájlban az összes, szövegközi kapcsolattal rendelkezniük kell.
- Az [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) támogatása előzetes verzióban érhető el, és csak a kiválasztott régiókban támogatott.

## <a name="personas"></a>Szerepek

Az IoT Central dokumentáció jaamás négy personas, akik egy IoT Central alkalmazás:

- A _megoldáskészítő_ felelős az alkalmazáshoz csatlakozó eszközök típusainak meghatározásáért és az alkalmazás testreszabásáért az operátor számára.
- Az _operátor_ kezeli az alkalmazáshoz csatlakoztatott eszközöket.
- A _rendszergazda_ felelős a felügyeleti feladatokért, például az alkalmazáson belüli [felhasználói szerepkörök és engedélyek](howto-administer.md) kezeléséért.
- Az _eszközfejlesztő_ létrehozza az alkalmazáshoz csatlakoztatott eszközön vagy IoT Edge-modulon futó kódot.

## <a name="create-your-iot-central-application"></a>Az IoT Central alkalmazás létrehozása

Megoldásszerkesztőként az IoT Central segítségével hozhat létre egy egyéni, felhőben üzemeltetett IoT-megoldást a szervezet számára. Az egyéni IoT-megoldások általában a következőket foglalják magukban:

- Egy felhőalapú alkalmazás, amely telemetriai adatokat fogad az eszközökről és lehetővé teszi az eszközök felügyeletét.
- Több eszköz, amelyek egyéni kódokat futtatnak és a felhőalapú alkalmazáshoz csatlakoznak.

Gyorsan üzembe helyezhet egy új IoT Central-alkalmazást, majd testreszabhatja azt a böngészőben az adott követelményeknek megfelelően. Kezdheti egy általános _alkalmazássablonnal,_ vagy a [kiskereskedelemre](../retail/overview-iot-central-retail-pnp.md), [energiára](../energy/overview-iot-central-energy.md), [kormányzati](../government/overview-iot-central-government.md)vagy [egészségügyi ellátásra](../healthcare/overview-iot-central-healthcare.md)vonatkozó iparág-központú alkalmazássablonokkal.

Megoldásszerkesztőként a webalapú eszközök segítségével hozzon létre egy _eszközsablont_ az alkalmazáshoz csatlakozó eszközökhöz. Az eszközsablon az a tervrajz, amely meghatározza egy eszköztípus jellemzőit és viselkedését, például a következőket:

- Telemetria, amit küld.
- Az operátor által módosítható üzleti tulajdonságok.
- Azok az eszköztulajdonságok, amelyeket az eszköz állít be, és az alkalmazásban csak olvashatók.
- Az eszköz viselkedését meghatározó tulajdonságok.

Ez az eszközsablon a következőket tartalmazza:

- Egy _eszköz képességmodell,_ amely leírja, hogy az eszköznek milyen képességeket kell megvalósítania, például az általa küldött telemetriai adatokat és az általa jelentettek tulajdonságokat.
- Felhőalapú tulajdonságok, amelyek nem tárolódnak az eszközön.
- Az IoT Central alkalmazás részét képező testreszabások, irányítópultok és űrlapok.

### <a name="pricing"></a>Díjszabás

Létrehozhat IoT Central alkalmazást egy 7 napos ingyenes próbaverzió használatával, vagy használhat egy szabványos díjszabási csomagot.

- Az *ingyenes* csomaggal létrehozott alkalmazások hét napig ingyenesek, és legfeljebb öt eszközt támogatnak. Bármikor átalakíthatja őket, hogy szabványos díjszabási csomagot használjanak, mielőtt azok lejárnának.
- A *szabványos* csomag használatával létrehozott alkalmazások számlázása eszközönként történik, így **Standard 2** az első két eszköz ingyenesen **választhat.** Az ingyenes és standard díjszabási csomagokról az [Azure IoT Central díjszabási oldalán olvashat bővebben.](https://azure.microsoft.com/pricing/details/iot-central/)

### <a name="create-device-templates"></a>Eszközsablonok létrehozása

[Az IoT Plug and Play (előzetes verzió)](../../iot-pnp/overview-iot-plug-and-play.md) lehetővé teszi az IoT Central számára az eszközök integrálását anélkül, hogy bármilyen beágyazott eszközkódot írna. Az IoT Plug and Play (előzetes verzió) középpontjában egy eszközképességi modellséma található, amely ismerteti az eszköz képességeit. Az IoT Central-alkalmazásokban az eszközsablonok ezeket az IoT Plug and Play (előzetes verzió) eszközképességi modelleket használják.

Megoldásszerkesztőként számos lehetősége van az eszközsablonok létrehozására:

- Importáljon egy eszközképességi modellt az [Azure Certified for IoT eszközkatalógusból,](https://aka.ms/iotdevcat) majd adja hozzá az IoT Central alkalmazás igényeinek megfelelő felhőtulajdonságokat, testreszabásokat és irányítópultokat.
- Tervezze meg az eszközsablont az IoT Centralban, majd valósítsa meg az eszközképességi modelljét az eszközkódban.
- Hozzon létre egy eszközképességi modellt a Visual Studio-kód használatával, és tegye közzé a modellt egy tárházban. Valósítsa meg az eszközkódját a modellből, és csatlakoztassa az eszközt az IoT Central alkalmazáshoz. Az IoT Central megkeresi az eszközképességi modellt a tárházból, és létrehoz egy egyszerű eszközsablont.
- Hozzon létre egy eszközképességi modellt a Visual Studio-kód használatával. Valósítsa meg az eszközkódját a modellből. Manuálisan importálja az eszközképességi modellt az IoT Central alkalmazásba, majd adja hozzá a felhőbeli tulajdonságokat, testreszabásokat és irányítópultokat, amelyeket az IoT Central alkalmazás igényeinek.

Megoldásszerkesztőként az IoT Central segítségével kódot hozhat létre a teszteszközökhöz az eszközsablonok érvényesítéséhez.

### <a name="customize-the-ui"></a>A felhasználói felület testreszabása

Megoldásszerkesztőként testre szabhatja az IoT Central alkalmazás felhasználói felületét az alkalmazás napi használatáért felelős operátorok számára. A megoldáskészítő által ellátható testreszabások a következők:

- Egy eszközsablonban található tulajdonságok és beállítások elrendezésének meghatározása.
- Egyéni irányítópultok konfigurálása, amelyek segítik az operátorokat a megállapítások felismerésében és a problémák gyorsabb megoldásában.
- Egyéni elemzések konfigurálása, amelyek a csatlakoztatott eszközökről származó idősorozat-adatokat vizsgálják.

## <a name="pricing"></a>Díjszabás

Létrehozhat IoT Central alkalmazást egy 7 napos ingyenes próbaverzió használatával, vagy használhat egy szabványos díjszabási csomagot.

- Az *ingyenes* csomaggal létrehozott alkalmazások hét napig ingyenesek, és legfeljebb öt eszközt támogatnak. Bármikor átalakíthatja őket, hogy szabványos díjszabási csomagot használjanak, mielőtt azok lejárnának.
- A *szabványos* csomag használatával létrehozott alkalmazások számlázása eszközönként történik, és választhat **a Standard 1** vagy a Standard **2** díjcsomag közül, ahol az első két eszköz ingyenes. További információ az [IoT Central díjszabásáról.](https://aka.ms/iotcentral-pricing)

## <a name="connect-your-devices"></a>Az eszközök csatlakoztatása

Az Azure IoT Central az [Azure IoT Hub-eszközkiépítési szolgáltatás (DPS)](../../iot-dps/about-iot-dps.md) segítségével kezeli az összes eszköz regisztrációját és kapcsolatát.

A DPS használata lehetővé teszi:

- IoT Central támogatja a bevezetési és csatlakozó eszközök nagy méretekben.
- Az eszközök hitelesítő adatainak létrehozásához és az eszközök offline konfigurálásához az eszközök IoT Central felhasználói felületen keresztültörténő regisztrálása nélkül.
- Közös hozzáférésű aláírásokkal történő csatlakozáshoz használt eszközök.
- Az ipari szabványnak megfelelő X.509-es tanúsítványok használatával történő csatlakozáshoz használt eszközök.
- Használhatja a saját eszközazonosítók eszközök regisztrálásához az IoT Central. A saját eszközazonosítók használata leegyszerűsíti a meglévő háttérirodai rendszerekkel való integrációt.
- Egyetlen, egységes módja az eszközök ioT Centralhoz való csatlakoztatásának.

További információ: [Csatlakozás az Azure IoT Centralhoz.](./concepts-get-connected.md)

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge-eszközök

Az [Azure IoT SDK-k](https://github.com/Azure/azure-iot-sdks)használatával létrehozott eszközök mellett [azure IoT Edge-eszközöket](../../iot-edge/about-iot-edge.md) is csatlakoztathat egy IoT Central-alkalmazáshoz. Az Azure IoT Edge lehetővé teszi a felhőintelligencia és az egyéni logika futtatását közvetlenül az IoT Central által felügyelt IoT-eszközökön. Az IoT Edge futásidejű lehetővé teszi, hogy:

- Telepítse és frissítse a számítási feladatokat az eszközön.
- Az Azure IoT Edge biztonsági szabványainak karbantartása az eszközön.
- Győződjön meg arról, hogy az IoT Edge-modulok mindig futnak.
- Modul állapotának jelentése a felhőbe a távoli figyeléshez.
- Az alsóbb rétegbeli eszközök és az IoT Edge-eszközök közötti kommunikáció kezelése, az IoT Edge-eszközön lévő modulok, valamint az IoT Edge-eszköz és a felhő közötti kommunikáció.

További információ: [Azure IoT Edge-eszközök és az IoT Central.](concepts-architecture.md#azure-iot-edge-devices)

## <a name="stay-connected"></a>A kapcsolat fenntartása

Az IoT Central-alkalmazásokteljes körű üzemeltetése a Microsoft, ami csökkenti az alkalmazások kezelésével kapcsolatos felügyeleti többletterhelést.

Operátorként az IoT Central alkalmazás használatával kezelheti az iot központi megoldásban lévő eszközöket. Az operátorok olyan feladatokat végeznek, mint például:

- Az alkalmazáshoz csatlakoztatott eszközök monitorozása.
- Az eszközök hibáinak keresése és elhárítása.
- Új eszközök üzembe helyezése.

Megoldásszerkesztőként egyéni szabályokat és műveleteket határozhat meg, amelyek a csatlakoztatott eszközökről történő adatfolyam-továbbításon keresztül működnek. Az operátor eszközszinten engedélyezheti vagy letilthatja ezeket a szabályokat, az alkalmazáson belüli feladatok vezérlése és automatizálása céljából.

A rendszergazdák [felhasználói szerepkörökkel és engedélyekkel](howto-administer.md)kezelhetik az alkalmazáshoz való hozzáférést.

Minden olyan IoT-megoldással, amelyet úgy terveztek, hogy nagy méretekben működjön, fontos az eszközkezelés strukturált megközelítése. Nem elég csak csatlakoztatni az eszközöket a felhőhöz, meg kell tartani az eszközöket csatlakoztatva és egészséges. Az operátor oka a következő IoT Central-képességek segítségével kezelheti eszközeit az alkalmazás teljes életciklusa során:

### <a name="dashboards"></a>Irányítópultok 

A beépített irányítópultok testre szabható felhasználói felületet [biztosítanak](./howto-set-up-template.md#generate-default-views) az eszközök állapotának és telemetriai adatainak figyeléséhez. Kezdje egy [alkalmazássablonban](howto-use-app-templates.md) lévő előre elkészített irányítópulttal, vagy hozzon létre saját irányítópultokat az operátorok igényeihez igazítva. Az irányítópultokat megoszthatja az alkalmazás összes felhasználójával, vagy bizalmasan is megoszthatja őket.

### <a name="rules-and-actions"></a>Szabályok és műveletek 

[Egyéni szabályokat](tutorial-create-telemetry-rules.md) hozhat létre az eszközállapot és a telemetria alapján a figyelmet igénylő eszközök azonosításához. Konfigurálja a megfelelő személyek értesítésére és a korrekciós intézkedések időben történt meghozására irányuló műveleteket.

### <a name="jobs"></a>Feladatok 

[A feladatok](howto-run-a-job.md) lehetővé teszik az eszközök egyszeri vagy tömeges frissítéseinek alkalmazását a tulajdonságok beállításával vagy a parancsok hívásával. 

### <a name="user-roles-and-permissions"></a>Felhasználói szerepkörök és engedélyek

[A szerepkörök és az engedélyek](howto-manage-users-roles.md) lehetővé teszik, hogy a rendszergazda testre szabja az egyes felhasználók élményét. A rendszergazda a webes felhasználói felület segítségével hoz létre szerepköröket és rendel engedélyeket.

## <a name="transform-your-iot-data"></a>IoT-adatok átalakítása

Alkalmazásplatformként az IoT Central lehetővé teszi, hogy az IoT-adatokat olyan üzleti elemzési adatokká alakítsa át, amelyek perelhető eredményeket eredményeznek. [A szabályok](./tutorial-create-telemetry-rules.md), [az adatexportálás](./howto-export-data.md)és a [nyilvános REST API](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) példák arra, hogyan integrálhatja az IoT Centralt az üzletági alkalmazásokkal:

![Hogyan alakíthatja át az IoT Central az IoT-adatokat?](media/overview-iot-central/transform.png)

### <a name="monitor-device-health-and-operations-using-rules"></a>Az eszköz állapotának és műveleteinek figyelése szabályok használatával

Amikor az eszközök csatlakoztatva vannak, és adatokat küldenek, a szabályok azonosíthatják a problémákat tapasztaló vagy hibaüzeneteket küldő eszközöket, így minimális állásidővel javíthatja őket. Szabályokat hozhat létre az IoT Central-alkalmazásban az eszközök telemetriai adatainak figyelésére, és riasztást az operátor, ha egy metrika átlép egy küszöbértéket, vagy egy eszköz egy adott üzenetet küld. E-mail műveletek és webhooks a szabályok értesíti a megfelelő embereket, és a megfelelő későbbi rendszerek.

### <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Egyéni elemzések és feldolgozás futtatása az exportált adatokon

Üzleti elemzéseket hozhat létre, például a gép hatékonysági trendek meghatározását vagy a jövőbeli energiafelhasználás előrejelzését a gyárban, egyéni elemzési folyamatok létrehozásával feldolgozhatja a telemetriai adatokat az eszközökről, és tárolhatja az eredményeket. Konfigurálja az adatok exportálását az IoT Central alkalmazásban telemetriai adatok exportálásához, az eszköztulajdonság-változásokhoz és az eszközsablon-módosításokhoz más szolgáltatásokba, ahol elemezheti, tárolhatja és vizualizálhatja az adatokat az előnyben részesített eszközökkel.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Egyéni IoT-megoldások és integrációk létrehozása a REST API-kkal

IoT-megoldásokat hozhat létre, például:

- Mobil társalkalmazások, amelyek távolról képesek beállítani és vezérelni az eszközöket.
- Egyéni integrációk, amelyek lehetővé teszik, hogy a meglévő üzletági alkalmazások kommunikáljanak az IoT-eszközökkel és -adatokkal.
- Eszközkezelő alkalmazások eszközmodellezéshez, bevezetéshez, kezeléshez és adathozzáféréshez.

## <a name="quotas"></a>Kvóták

Minden Azure-előfizetés alapértelmezett kvótákkal rendelkezik, amelyek hatással lehetnek az IoT-megoldás hatókörére. Jelenleg az IoT Central 10-re korlátozza az előfizetésben üzembe helyezhető alkalmazások számát. Ha növelni szeretné ezt a korlátot, forduljon a [Microsoft támogatási szolgálatához.](https://azure.microsoft.com/support/options/)

## <a name="next-steps"></a>További lépések

Most, hogy áttekintést kap az IoT Centralról, az alábbi lépéseket javasolta:

- Ismerje meg az IoT-megoldások létrehozásához rendelkezésre álló [Azure-technológiákat és -szolgáltatásokat.](../../iot-fundamentals/iot-services-and-technologies.md)
- Ismerkedés az [Azure IoT Central kezelőfelületével](overview-iot-central-tour.md).
- [Egy Azure IoT Central-alkalmazás létrehozásának](quick-deploy-iot-central.md) első lépései.
- További információ az [IoT Plug and Play (előzetes verzió)](../../iot-pnp/overview-iot-plug-and-play.md)című verzióról.
- Ismerje meg, hogyan [csatlakoztathat egy Azure IoT Edge-eszközt.](./tutorial-add-edge-as-leaf-device.md)
- További információ az [Azure IoT-technológiákról és -szolgáltatásokról.](../../iot-fundamentals/iot-services-and-technologies.md)

Ha Ön eszközfejlesztő, és szeretne belemerülni néhány kódot, a javasolt következő lépés az [ügyfélalkalmazás létrehozása és csatlakoztatása az Azure IoT Central alkalmazáshoz.](./tutorial-connect-device-nodejs.md)
