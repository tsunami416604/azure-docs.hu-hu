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
manager: timlt
ms.openlocfilehash: 27862ce11785f1b264d9f0238a9bae2684c87d02
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989926"
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

- A folyamatos adatexportálás nem támogatja a Avro formátumot (inkompatibilitás).
- A GeoJSON jelenleg nem támogatott.
- A Térkép csempe jelenleg nem támogatott.
- A feladatok nem támogatják az összetett típusokat.
- A tömb sémájának típusai nem támogatottak.
- A rendszer csak a C eszközoldali SDK-t és a Node. js-eszközt és a Service SDK-kat támogatja.
- Ez csak a Egyesült Államok és Európa helyein érhető el.
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

### <a name="pricing"></a>Díjszabás

Létrehozhat IoT Central alkalmazást 7 napos ingyenes próbaverzióval, vagy használhat szabványos előfizetést is.

- Az **ingyenes** csomag használatával létrehozott alkalmazások hét napig ingyenesen használhatók, és legfeljebb öt eszközt támogatnak. Egy standard díjszabási csomag használatára a lejárat előtt bármikor átalakítható.
- A **standard** csomag használatával létrehozott alkalmazások számlázása eszközönként történik, a **standard 1** vagy **Standard 2** díjszabási csomaggal pedig az első két eszköz ingyenesen használható. Az ingyenes és standard díjszabási csomagokról az [Azure IoT Central díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/iot-central/)tájékozódhat.


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

### <a name="azure-iot-edge-devices"></a>Eszközök Azure IoT Edge

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

## <a name="quotas"></a>Kvóták

Minden Azure-előfizetéshez tartozik alapértelmezett kvóta, amely hatással lehet a IoT-megoldás hatókörére. Jelenleg IoT Central korlátozza az előfizetésben üzembe helyezhető alkalmazások számát 10-re. Ha a korlát növelésére van szüksége, forduljon a [Microsoft ügyfélszolgálatához](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette a IoT Centralt, a következő lépések javasoltak:

- Ismerje meg az elérhető [Azure-technológiákat és-szolgáltatásokat a IoT-megoldások létrehozásához](../../iot-fundamentals/iot-services-and-technologies.md).
- Ismerkedés az [Azure IoT Central kezelőfelületével](overview-iot-central-tour.md).
- [Egy Azure IoT Central-alkalmazás létrehozásának](quick-deploy-iot-central.md) első lépései.
- További információ a [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md)
- Tudnivalók [Azure IoT Edge eszköz sablonjának létrehozásáról](./tutorial-define-edge-device-type.md)
