---
title: Mi az az Azure IoT Central? | Microsoft Docs
description: Az Azure IoT Central egy teljes körű SaaS-megoldás, amellyel létrehozhatja és kezelheti az egyéni IoT-megoldást. Ez a cikk áttekintést nyújt az Azure IoT Central jellemzőiről.
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: dfb550fd0bc93a6b865495bf2c19f394fb0737ed
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72987885"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Mi az Azure IoT Central (előzetes verziójú funkciók)?

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Az Azure IoT Central [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) képességei jelenleg nyilvános előzetes verzióban érhetők el. Ne használjon IoT Plug and Play engedélyezett IoT Central alkalmazást az éles számítási feladatokhoz. Éles környezetekhez használjon egy aktuális, általánosan elérhető IoT-alapú központi alkalmazást.

Az Azure IoT Central egy IoT-alkalmazási platform, amely csökkenti a nagyvállalati szintű IoT-megoldások fejlesztésének, kezelésének és karbantartásának terheit és költségeit. Az Azure IoT Central létrehozásával lehetőség van arra, hogy az időt, pénzt és energiát a IoT adataival alakítsa át, nem csupán a komplex és folyamatosan fejlődő IoT-infrastruktúra karbantartására és frissítésére.

A webes felhasználói felülettel figyelheti az eszközök feltételeit, szabályokat hozhat létre, és több millió eszközt és adatmennyiséget kezelhet életciklusa során. Továbbá lehetővé teszi, hogy az IoT-intelligenciát az üzletági alkalmazásokra kiterjesztve az eszköz elemzéseit is fellépjen.

Ez a cikk az Azure IoT Central esetében ismerteti:

- A projekthez társított tipikus szerepek.
- Egy alkalmazás létrehozása.
- Az eszközök az alkalmazáshoz történő csatlakoztatása
- Az alkalmazás felügyelete.
- A IoT Central IoT Edge képességeinek áttekintése
- A Azure IoT Edge Runtime-alapú eszközök csatlakoztatása az alkalmazáshoz.


## <a name="known-issues"></a>Ismert problémák

> [!Note]
> Ezek az ismert problémák csak a IoT Central Preview alkalmazásra vonatkoznak.

- A szabályok nem támogatják az összes műveletet (csak e-mail-cím).
- Összetett típusok esetén – a szabályok, az elemzések és az eszközök csoportjai nem támogatottak.
- A folyamatos adatexportálás nem támogatja a Avro formátumot (inkompatibilitás).
- A szimulált eszközök nem támogatják az összes összetett típust.
- A GeoJSON jelenleg nem támogatott.
- A Térkép csempe jelenleg nem támogatott.
- A feladatok nem támogatják az összetett típusokat.
- A tömb sémájának típusai nem támogatottak.
- Az alkalmazás sablonjának exportálása és az alkalmazás másolása nem támogatott.
- A rendszer csak a C eszközoldali SDK-t és a Node. js-eszközt és a Service SDK-kat támogatja.
- Ez csak az észak-európai és az USA középső régiójában érhető el.
- Az eszköz képességeinek modelljeinek minden, ugyanabban a fájlban definiált illesztőfelülettel kell rendelkezniük.

## <a name="personas"></a>Szerepek

Az Azure IoT Central dokumentációja négy olyan személyre utal, akik egy Azure IoT Central alkalmazással működnek:

- A _megoldás-szerkesztő_ feladata, hogy meghatározza az alkalmazáshoz kapcsolódó eszközök típusát, és testreszabja az alkalmazást az operátor számára.
- Az _operátor_ kezeli az alkalmazáshoz csatlakoztatott eszközöket.
- A _rendszergazdák_ felelősek az olyan felügyeleti feladatokért, mint például a [felhasználói szerepkörök és engedélyek](howto-administer.md) kezelése az alkalmazáson belül.
- Az _eszközfejlesztő_ hozza létre azokat a kódokat, amelyek az alkalmazáshoz csatlakoztatott eszközökön futnak.
- Az _eszköz/modul fejlesztői_ létrehozzák az alkalmazáshoz csatlakoztatott eszközön futó kódot/modult.

## <a name="create-your-azure-iot-central-application"></a>Azure IoT Central-alkalmazás létrehozása

Szerkesztőként az Azure IoT Central használatával egyéni, a felhőben üzemeltetett IoT-megoldásokat hozhat létre cége számára. Az egyéni IoT-megoldások általában a következőket foglalják magukban:

- Egy felhőalapú alkalmazás, amely telemetriai adatokat fogad az eszközökről és lehetővé teszi az eszközök felügyeletét.
- Több eszköz, amelyek egyéni kódokat futtatnak és a felhőalapú alkalmazáshoz csatlakoznak.

Gyorsan üzembe helyezhet egy új Azure IoT Central alkalmazást, és testre szabhatja a böngészőben megadott igényei szerint. Építőként a webalapú eszközök használatával hozzon létre egy _sablont_ az alkalmazáshoz csatlakozó eszközökhöz. Az eszköz sablonja az a terv, amely meghatározza egy adott típusú eszköz jellemzőit és viselkedését, például:

- Telemetria.
- Az operátor által módosítható üzleti tulajdonságok.
- Azok az eszköztulajdonságok, amelyeket az eszköz állít be, és az alkalmazásban csak olvashatók.
- A tulajdonságok, amelyeket az operátor állít be, amelyek meghatározzák az eszköz viselkedését.

Ez az eszköz a következőket tartalmazza:

- Egy _eszköz-képesség modell_ , amely leírja az eszköz által megvalósított képességeket, például az általa küldött telemetria és az általa jelentett tulajdonságokat.
- Az eszközön nem tárolt Felhőbeli tulajdonságok.
- A IoT Central alkalmazás részét képező testreszabások, irányítópultok és űrlapok.

### <a name="create-device-templates"></a>Eszközök sablonjainak létrehozása

A [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) lehetővé teszi, hogy a IoT Central beágyazott eszköz kódjának írása nélkül integrálja az eszközöket. A IoT-Plug and Play középpontjában az eszköz képességeinek leírására szolgáló eszköz-képességi modell séma található. IoT Central előnézeti alkalmazásban az eszközök sablonjai ezeket a IoT Plug and Play eszköz-képesség modelleket használják.

A Builder számos lehetőséget kínál az eszközök sablonjainak létrehozására:

- Tervezze meg IoT Central az eszköz sablonját, majd implementálja az eszköz képességeinek modelljét az eszköz kódjában.
- Importáljon egy eszköz-képességi modellt az [Azure Certified for IoT-Device Catalog eszközből](https://aka.ms/iotdevcat) , majd adja hozzá a IoT Central alkalmazás igényeinek megfelelő Felhőbeli tulajdonságokat, testreszabásokat és irányítópultokat.
- Eszköz-képesség modell létrehozása a Visual Studio Code használatával. Implementálja az eszköz kódját a modellből, és kapcsolja össze az eszközt a IoT Central alkalmazással. IoT Central megkeresi az eszköz képességeinek modelljét egy adattárból, és létrehoz egy egyszerű sablont.
- Eszköz-képesség modell létrehozása a Visual Studio Code használatával. Implementálja az eszköz kódját a modellből. Manuálisan importálja az eszköz képességeinek modelljét a IoT Central alkalmazásba, majd adja hozzá a IoT Central alkalmazás igényeinek megfelelő Felhőbeli tulajdonságokat, testreszabásokat és irányítópultokat.

Építőként használhatja a IoT Centralt az eszközök tesztelésére szolgáló kód létrehozásához az eszköz sablonjainak ellenőrzéséhez.

### <a name="customize-the-ui"></a>A felhasználói felület testreszabása

A szerkesztők emellett testreszabhatják az Azure IoT Central-alkalmazás kezelőfelületét az operátorok számára, akiknek az alkalmazás napi használata a feladata. A szerkesztők által elvégezhető testreszabási lehetőségek közé tartoznak a következők:

- Egy eszközsablonban található tulajdonságok és beállítások elrendezésének meghatározása.
- Egyéni irányítópultok konfigurálása, amelyek segítik az operátorokat a megállapítások felismerésében és a problémák gyorsabb megoldásában.
- Egyéni elemzések konfigurálása, amelyek a csatlakoztatott eszközökről származó idősorozat-adatokat vizsgálják.

## <a name="connect-your-devices"></a>Az eszközök csatlakoztatása

Miután a szerkesztő meghatározta, hogy az alkalmazáshoz milyen típusú eszközök csatlakozhatnak, egy eszközfejlesztő létrehozza az eszközökön futtatandó kódokat. Az eszközfejlesztők a Microsoft nyílt forráskódú [Azure IoT SDK-it](https://github.com/Azure/azure-iot-sdks) használhatják az eszközkódok létrehozásához. Ezek az SDK-k számos nyelvet, platformot és protokollt támogatnak, hogy megfeleljenek az Ön igényeinek az eszközök és az Azure IoT Central-alkalmazás csatlakoztatását illetően. Az SDK-k segítenek a következő eszköz-képességek megvalósításában:

- Biztonságos kapcsolat létrehozása.
- Telemetria küldése.
- Állapotjelentés.
- Konfigurációfrissítések fogadása.

További információkért olvassa el a következő blogbejegyzést: [Milyen előnyökkel jár, ha az Azure IoT SDK-kat használja, és milyen buktatókkal, ha nem?](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)

## <a name="manage-your-application"></a>Az alkalmazás felügyelete

Az Azure IoT Central-alkalmazásokat teljes mértékben a Microsoft üzemelteti, ami csökkenti az alkalmazások felügyelete miatti adminisztrációs terheket.

Az operátorok az Azure IoT Central-alkalmazással kezelik az eszközöket az Azure IoT Central-megoldásban. A kezelők az alábbiakhoz hasonló feladatokat végzik el:

- Az alkalmazáshoz csatlakoztatott eszközök monitorozása.
- Az eszközök hibáinak keresése és elhárítása.
- Új eszközök üzembe helyezése.

Építőként olyan egyéni szabályokat és műveleteket adhat meg, amelyek a csatlakoztatott eszközökről származó adatfolyamként működnek. Az operátor eszközszinten engedélyezheti vagy letilthatja ezeket a szabályokat, az alkalmazáson belüli feladatok vezérlése és automatizálása céljából.

A rendszergazdák [felhasználói szerepkörökkel és engedélyekkel](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)kezelhetik az alkalmazáshoz való hozzáférést.


## <a name="what-is-azure-iot-central-with-azure-iot-edge-preview-features"></a>Mi az Azure IoT Central és a Azure IoT Edge (előzetes verziójú funkciók)

A IoT Central Azure IoT Edge eszközök támogatásával bővíti a portfólióját. 

A vállalatok mostantól közvetlenül is futtathatják a felhőalapú intelligenciát az Azure IoT Central által felügyelt IoT-eszközökön. Ez az új szolgáltatás segít a vállalkozásoknak a Azure IoT Edge futtatókörnyezetet futtató Azure IoT Edge eszközök csatlakoztatásában és kezelésében, a szoftverek üzembe helyezésében, az adatok közzétételében, valamint a méretezési műveletek elvégzésében, a IoT Centralon belül. 

[Azure IoT Edge áttekintése](../../iot-edge/about-iot-edge.md)

### <a name="overview-of-iot-edge-capabilities-in-iot-central"></a>A IoT Central IoT Edge képességeinek áttekintése

Az Azure IoT Edge-futtatókörnyezet egyéni és felhőbeli logikák is használatát is lehetővé teszi az IoT Edge-eszközökön. IoT Edge eszközt a futtatókörnyezet működteti, és felügyeleti és kommunikációs műveleteket hajt végre. 

Azure IoT Edge Runtime a következő funkciókat hajtja végre:

- A számítási feladatok telepítése és frissítése az eszközön.
- Azure IoT Edge biztonsági szabványok fenntartása az eszközön.
- Győződjön meg arról, hogy IoT Edge modulok mindig futnak.
- Jelentési modul állapota a felhőben távoli figyeléshez.
- Kezelheti az alárendelt levelek eszközei és a IoT Edge eszközök közötti kommunikációt egy IoT Edge eszköz moduljai és egy IoT Edge eszköz és a felhő között.

![IoT Central Azure IoT Edge áttekintéssel](./media/overview-iot-central-pnp-edge/iotedge.png)

Az Azure IoT Central a következő funkciókat hajtja végre: 

- Azure IoT Edge az eszköz sablonjának támogatását, amely leírja, hogy egy Azure IoT Edge eszköz milyen képességeket kell megvalósítani, például 
  1. üzembe helyezési jegyzékfájl feltöltési képessége, amely segítséget nyújt a jegyzékfájlok kezeléséhez az eszközök flottáján
  2. a Azure IoT Edge eszközön futtatandó modulok
  3. az egyes modulok telemetria
  4. tulajdonságok az egyes modulok jelentései és 
  5. a parancs minden modulra válaszol
  6. Kapcsolatok létrehozása Azure IoT Edge átjáró eszköz képesség modellje és az alsóbb rétegbeli eszköz képességeinek modellje között
  7. A Azure IoT Edge eszközön nem tárolt Felhőbeli tulajdonságok
  8. A IoT Central alkalmazás részét képező testreszabások, irányítópultok és űrlapok

  [Azure IoT Edge eszköz sablonjának létrehozása](./tutorial-define-edge-device-type-pnp.md)
   
- Azure IoT Edge eszközök méretezése az Azure IoT Device kiépítési szolgáltatással
- Szabályok kiváltása és műveletek végrehajtása Azure IoT Edge eszközökön
- Irányítópultok és elemzések készítése 
- Azure IoT Edge eszközökről áramló telemetria folyamatos adatexportálása

### <a name="azure-iot-edge-device-types-in-iot-central"></a>Azure IoT Edge eszközök típusai a IoT Central

Az Azure IoT Central az alábbi módon osztályozza Azure IoT Edge eszközök típusát:

- Azure IoT Edge eszköz levélként. Azure IoT Edge eszközön lehetnek alsóbb rétegbeli eszközök, de az alsóbb rétegbeli eszközök nincsenek kiépítve a IoT Central
- Azure IoT Edge eszköz átjáró-eszközként az alárendelt eszközökkel. Az átjáró-eszköz és az alsóbb rétegbeli eszközök kiépítve IoT Central

![IoT Central Azure IoT Edge áttekintéssel](./media/overview-iot-central-pnp-edge/gatewayedge.png)

### <a name="azure-iot-edge-patterns-supported-in-iot-central"></a>A IoT Central által támogatott Azure IoT Edge minták

- Azure IoT Edge a levélként szolgáló eszközként ![Azure IoT Edge a levél](./media/overview-iot-central-pnp-edge/edgeasleafdevice.png) Azure IoT Edge az eszköz IoT Central-ban és az alsóbb rétegbeli eszközökön lesz kiépítve, és a telemetria Azure IoT Edge eszközről érkezőként fog megjelenni. Az alsóbb rétegbeli eszközök, ha a Azure IoT Edge eszközhöz csatlakoztatott bármelyik nem lesz kiépítve a IoT Central. 

- Azure IoT Edge az átjáró eszközét, amely az eszközön lévő alsóbb rétegbeli ![eszközökhöz csatlakozik, és az Azure IoT Edge az alsóbb rétegbeli eszköz azonosítóját](./media/overview-iot-central-pnp-edge/edgewithdownstreamdeviceidentity.png) Azure IoT Edge eszköz IoT Central a Azure IoT Edgehoz csatlakoztatott alsóbb rétegű eszközökkel együtt lesz kiépítve. eszköz. A futásidejű eszközök az átjárón keresztül történő kiépítés utáni támogatását a jövőre tervezték. IoT Central a felhőben először kiépíti az alsóbb rétegbeli eszközöket, a hitelesítő adatokat pedig manuálisan felügyeli az alárendelt eszközön. Az eszköz a jövőbeli félévekhez tervezett alsóbb rétegbeli eszközök kiépítés. 

- Azure IoT Edge átjáró-eszköz az Edge Gateway által biztosított identitást használó alsóbb rétegbeli eszközökhöz ![Azure IoT Edge az identitás nélküli alsóbb rétegbeli eszközzel,](./media/overview-iot-central-pnp-edge/edgewithoutdownstreamdeviceidentity.png) Azure IoT Edge eszköz az alsóbb réteggel együtt lesz kiépítve a Azure IoT Edge eszközhöz csatlakoztatott eszközök. A rendszer az átjáró támogatását biztosítja az alsóbb rétegbeli eszközök személyazonosságának biztosításához és az alsóbb rétegbeli eszközök kiépítési tervekhez. Saját személyazonosság-fordítási modult is használhat, és a IoT Central támogatni fogja ezt a mintát. 

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az Azure IoT Central jellemzőit, tekintse meg a javasolt következő lépéseket:

- [Az Azure IoT Central és az Azure IoT-megoldásgyorsítók](overview-iot-options.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) különbségeinek összehasonlítása.
- Ismerkedés az [Azure IoT Central kezelőfelületével](overview-iot-central-tour-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- [Egy Azure IoT Central-alkalmazás létrehozásának](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) első lépései.
- További információ a [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md)
- Tudnivalók [Azure IoT Edge eszköz sablonjának létrehozásáról](./tutorial-define-edge-device-type-pnp.md)
