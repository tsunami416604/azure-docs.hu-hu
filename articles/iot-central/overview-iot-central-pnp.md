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
ms.openlocfilehash: af5b16448a39e0106dbc04724607ff603483484c
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997746"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Mi az Azure IoT Central (előzetes verziójú funkciók)?

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Az Azure IoT Central [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) képességei jelenleg nyilvános előzetes verzióban érhetők el. Ne használjon IoT Plug and Play engedélyezett IoT Central alkalmazást az éles számítási feladatokhoz. Éles környezetekhez használjon egy aktuális, általánosan elérhető IoT-alapú központi alkalmazást.

Az Azure IoT Central egy teljes körűen felügyelt IoT-szolgáltatás, amely megkönnyíti a fizikai és a digitális világok összekapcsolására szolgáló termékek létrehozását. A csatlakoztatott termékre vonatkozó elképzelését a következőképpen valósíthatja meg:

- Új megállapítások kinyerése a csatlakoztatott eszközökről, hogy az ügyfelek jobb termékekhez és élményekhez juthassanak.
- Új üzleti lehetőségek teremtése a cég számára.

Azure IoT Central egy tipikus IoT-projekthez képest:

- Csökkenti a felügyeleti terheket.
- Csökkenti a működési költségeket és az alacsonyabb költségeket.
- Megkönnyíti az alkalmazás testreszabását, miközben a következővel dolgozik:
  - Iparágvezető technológiák, mint például az [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) és az [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) alkalmazása.
  - Nagyvállalati szintű biztonsági funkciók, például végpontok közötti titkosítás.

A következő videó áttekintést nyújt az Azure IoT Centralról:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

Ez a cikk az Azure IoT Central esetében ismerteti:

- A projekthez társított tipikus szerepek.
- Egy alkalmazás létrehozása.
- Az eszközök az alkalmazáshoz történő csatlakoztatása
- Az alkalmazás felügyelete.

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
- Csak a Select régiókban érhető el.

## <a name="personas"></a>Szerepek

Az Azure IoT Central dokumentációja négy olyan személyre utal, akik egy Azure IoT Central alkalmazással működnek:

- A _szerkesztő_ feladata, hogy meghatározza, milyen típusú eszközök csatlakoznak az alkalmazáshoz, valamint testreszabja az alkalmazást az operátor számára.
- Az _operátor_ kezeli az alkalmazáshoz csatlakoztatott eszközöket.
- A _rendszergazda_ felelős az olyan adminisztrációs feladatokért, mint a felhasználók és szerepkörök kezelése az alkalmazáson belül.
- Az _eszközfejlesztő_ hozza létre azokat a kódokat, amelyek az alkalmazáshoz csatlakoztatott eszközökön futnak.

## <a name="create-your-azure-iot-central-application"></a>Azure IoT Central-alkalmazás létrehozása

Szerkesztőként az Azure IoT Central használatával egyéni, a felhőben üzemeltetett IoT-megoldásokat hozhat létre cége számára. Az egyéni IoT-megoldások általában a következőket foglalják magukban:

- Egy felhőalapú alkalmazás, amely telemetriai adatokat fogad az eszközökről és lehetővé teszi az eszközök felügyeletét.
- Több eszköz, amelyek egyéni kódokat futtatnak és a felhőalapú alkalmazáshoz csatlakoznak.

Gyorsan üzembe helyezhet egy új Azure IoT Central alkalmazást, és testre szabhatja a böngészőben megadott igényei szerint. Építőként a webalapú eszközök használatával hozzon létre egy _sablont_ az alkalmazáshoz csatlakozó eszközökhöz. Az eszköz sablonja az a terv, amely meghatározza egy adott típusú eszköz jellemzőit és viselkedését, például:

- Telemetria.
- Az operátor által módosítható üzleti tulajdonságok.
- Azok az eszköztulajdonságok, amelyeket az eszköz állít be, és az alkalmazásban csak olvashatók.
- Az eszköz viselkedését meghatározó operátor által beállított tulajdonságok.

Ez az eszköz a következőket tartalmazza:

- Egy _eszköz-képesség modell_ , amely leírja az eszköz által megvalósított képességeket, például az általa küldött telemetria és az általa jelentett tulajdonságokat.
- Az eszközön nem tárolt Felhőbeli tulajdonságok.
- A IoT Central alkalmazás részét képező testreszabások, irányítópultok és űrlapok.

### <a name="create-device-templates"></a>Eszközök sablonjainak létrehozása

A [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) lehetővé teszi, hogy a IoT Central beágyazott eszköz kódjának írása nélkül integrálja az eszközöket. A IoT Plug and Play a Device képesség modell sémája, amely leírja az eszközök képességeit. IoT Central előnézeti alkalmazásban az eszközök sablonjai ezeket a IoT Plug and Play eszköz-képesség modelleket használják.

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

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az Azure IoT Central jellemzőit, tekintse meg a javasolt következő lépéseket:

- [Az Azure IoT Central és az Azure IoT-megoldásgyorsítók](overview-iot-options.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) különbségeinek összehasonlítása.
- Ismerkedés az [Azure IoT Central kezelőfelületével](overview-iot-central-tour-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- [Egy Azure IoT Central-alkalmazás létrehozásának](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) első lépései.
- Egy oktatóanyag-sorozat mutatja be a következőket:
  - [Szerkesztőként: Eszközsablon létrehozása](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Szerkesztőként: Szabályok hozzáadása a megoldás automatizálásához](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Operátorként: Eszközök monitorozása](tutorial-monitor-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Kezelőként adjon hozzá egy eszközt a megoldáshoz](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- További információ a [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md)
