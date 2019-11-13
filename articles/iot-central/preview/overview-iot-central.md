---
title: Mi az az Azure IoT Central? | Microsoft Docs
description: Az Azure IoT Central egy teljes körű SaaS-megoldás, amellyel létrehozhatja és kezelheti az egyéni IoT-megoldást. Ez a cikk áttekintést nyújt az Azure IoT Central jellemzőiről.
author: dominicbetts
ms.author: dobett
ms.date: 11/12/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 597f9a80cad435bb24b38852065124ad292cd51b
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005871"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Mi az Azure IoT Central (előzetes verziójú funkciók)?

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Az Azure IoT Central [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) képességei jelenleg nyilvános előzetes verzióban érhetők el. Az éles számítási feladatokhoz ne használjon IoT Plug and Play engedélyezve IoT Central [alkalmazás sablonját](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) . Éles környezetekhez használjon egy aktuális, általánosan elérhető [IoT-alapú központi alkalmazást.](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)

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

> [!Note]
> Ezek az ismert problémák csak a IoT Central előzetes verzió alkalmazásaira vonatkoznak.

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

Az IoT Central dokumentációja négy olyan személyre utal, akik IoT Central alkalmazással működnek:

- A _megoldás-szerkesztő_ feladata, hogy meghatározza az alkalmazáshoz kapcsolódó eszközök típusát, és testreszabja az alkalmazást az operátor számára.
- Az _operátor_ kezeli az alkalmazáshoz csatlakoztatott eszközöket.
- A _rendszergazdák_ felelősek az olyan felügyeleti feladatokért, mint például a [felhasználói szerepkörök és engedélyek](howto-administer.md) kezelése az alkalmazáson belül.
- Az _eszközök fejlesztői_ létrehozzák az alkalmazáshoz csatlakoztatott eszközön vagy IoT Edge modulon futó kódot.

## <a name="create-your-iot-central-application"></a>A IoT Central-alkalmazás létrehozása

Megoldás-szerkesztőként a IoT Central használatával létrehozhat egy egyéni, felhőalapú IoT-megoldást a szervezet számára. Az egyéni IoT-megoldások általában a következőket foglalják magukban:

- Egy felhőalapú alkalmazás, amely telemetriai adatokat fogad az eszközökről és lehetővé teszi az eszközök felügyeletét.
- Több eszköz, amelyek egyéni kódokat futtatnak és a felhőalapú alkalmazáshoz csatlakoznak.

Gyorsan üzembe helyezhet egy új IoT Central alkalmazást, és testre szabhatja a böngészőben megadott igényei szerint. Megoldás-szerkesztőként a webalapú eszközök használatával hozzon létre egy _sablont_ az alkalmazáshoz csatlakozó eszközökhöz. Az eszköz sablonja az a terv, amely meghatározza egy adott típusú eszköz jellemzőit és viselkedését, például:

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

Megoldás-szerkesztőként több lehetőség közül választhat az eszközök sablonjainak létrehozásához:

- Tervezze meg IoT Central az eszköz sablonját, majd implementálja az eszköz képességeinek modelljét az eszköz kódjában.
- Importáljon egy eszköz-képességi modellt az [Azure Certified for IoT-Device Catalog eszközből](https://aka.ms/iotdevcat) , majd adja hozzá a IoT Central alkalmazás igényeinek megfelelő Felhőbeli tulajdonságokat, testreszabásokat és irányítópultokat.
- Eszköz-képesség modell létrehozása a Visual Studio Code használatával. Implementálja az eszköz kódját a modellből, és kapcsolja össze az eszközt a IoT Central alkalmazással. IoT Central megkeresi az eszköz képességeinek modelljét egy adattárból, és létrehoz egy egyszerű sablont.
- Eszköz-képesség modell létrehozása a Visual Studio Code használatával. Implementálja az eszköz kódját a modellből. Manuálisan importálja az eszköz képességeinek modelljét a IoT Central alkalmazásba, majd adja hozzá a IoT Central alkalmazás igényeinek megfelelő Felhőbeli tulajdonságokat, testreszabásokat és irányítópultokat.

Megoldás-szerkesztőként a IoT Central segítségével programkódot hozhatja meg az eszközök teszteléséhez az eszköz sablonjainak ellenőrzéséhez.

### <a name="customize-the-ui"></a>A felhasználói felület testreszabása

Megoldás-szerkesztőként testreszabhatja a IoT Central alkalmazás felhasználói felületét azon operátorok esetében is, akik felelősek az alkalmazás napi használatért. A megoldás-szerkesztő által végrehajtható testreszabások:

- Egy eszközsablonban található tulajdonságok és beállítások elrendezésének meghatározása.
- Egyéni irányítópultok konfigurálása, amelyek segítik az operátorokat a megállapítások felismerésében és a problémák gyorsabb megoldásában.
- Egyéni elemzések konfigurálása, amelyek a csatlakoztatott eszközökről származó idősorozat-adatokat vizsgálják.

## <a name="connect-your-devices"></a>Az eszközök csatlakoztatása

Miután a szerkesztő meghatározta, hogy az alkalmazáshoz milyen típusú eszközök csatlakozhatnak, egy eszközfejlesztő létrehozza az eszközökön futtatandó kódokat. Az eszközfejlesztők a Microsoft nyílt forráskódú [Azure IoT SDK-it](https://github.com/Azure/azure-iot-sdks) használhatják az eszközkódok létrehozásához. Ezek az SDK-k széles körű nyelvi, platform-és protokoll-támogatással rendelkeznek, hogy megfeleljenek az eszközeinek a IoT Central alkalmazáshoz való csatlakoztatásához szükséges követelményeknek. Az SDK-k segítenek a következő eszköz-képességek megvalósításában:

- Biztonságos kapcsolat létrehozása.
- Telemetria küldése.
- Állapotjelentés.
- Konfigurációfrissítések fogadása.

További információkért olvassa el a következő blogbejegyzést: [Milyen előnyökkel jár, ha az Azure IoT SDK-kat használja, és milyen buktatókkal, ha nem?](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)

### <a name="azure-iot-edge-devices"></a>Az Azure IoT Edge-eszközök

Továbbá az [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)-k használatával létrehozott eszközökhöz [Azure IoT Edge eszközöket](../../iot-edge/about-iot-edge.md) is csatlakozhat egy IoT Central alkalmazáshoz. Azure IoT Edge lehetővé teszi, hogy közvetlenül a IoT Central által felügyelt IoT-eszközökön futtassa a felhőalapú intelligenciát és az egyéni logikát. A IoT Edge Runtime a következőket teszi lehetővé:

- A számítási feladatok telepítése és frissítése az eszközön.
- Azure IoT Edge biztonsági szabványok fenntartása az eszközön.
- Győződjön meg arról, hogy IoT Edge modulok mindig futnak.
- Jelentési modul állapota a felhőben távoli figyeléshez.
- Kezelheti az alárendelt levelek eszközei és a IoT Edge eszközök közötti kommunikációt egy IoT Edge eszköz moduljai és egy IoT Edge eszköz és a felhő között.

További információ: [Azure IoT Edge eszközök és IoT Central](./concepts-architecture.md#azure-iot-edge-devices).

## <a name="manage-your-application"></a>Az alkalmazás felügyelete

IoT Central alkalmazásait teljes mértékben a Microsoft üzemelteti, ami csökkenti az alkalmazások felügyeletének terhelését.

Kezelőként a IoT Central alkalmazással felügyelheti az eszközöket a IoT Central-megoldásban. A kezelők az alábbiakhoz hasonló feladatokat végzik el:

- Az alkalmazáshoz csatlakoztatott eszközök monitorozása.
- Az eszközök hibáinak keresése és elhárítása.
- Új eszközök üzembe helyezése.

Megoldás-szerkesztőként olyan egyéni szabályokat és műveleteket adhat meg, amelyek a csatlakoztatott eszközökről végeznek adatátvitelt. Az operátor eszközszinten engedélyezheti vagy letilthatja ezeket a szabályokat, az alkalmazáson belüli feladatok vezérlése és automatizálása céljából.

A rendszergazdák [felhasználói szerepkörökkel és engedélyekkel](howto-administer.md)kezelhetik az alkalmazáshoz való hozzáférést.

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette a IoT Centralt, a következő lépések javasoltak:

- Ismerje meg a [IoT Central és az Azure IoT megoldás-gyorsító](../core/overview-iot-options.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)közötti különbségeket.
- Ismerkedés az [Azure IoT Central kezelőfelületével](overview-iot-central-tour.md).
- [Egy Azure IoT Central-alkalmazás létrehozásának](quick-deploy-iot-central.md) első lépései.
- További információ a [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md)
- Tudnivalók [Azure IoT Edge eszköz sablonjának létrehozásáról](./tutorial-define-edge-device-type.md)
