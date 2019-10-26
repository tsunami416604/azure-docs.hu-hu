---
title: Mi az az Azure IoT Central? | Microsoft Docs
description: Az Azure IoT Central egy teljes körű SaaS-megoldás, amellyel létrehozhatja és kezelheti az egyéni IoT-megoldást. Ez a cikk áttekintést nyújt az Azure IoT Central jellemzőiről.
author: dominicbetts
ms.author: dobett
ms.date: 08/26/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 0cb3ac836569c782d7b7d84917af276567e89c86
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957705"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>Mi az az Azure IoT Central?

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Az Azure IoT Central egy IoT, amely csökkenti a nagyvállalati szintű IoT-megoldások fejlesztésével, kezelésével és karbantartásával járó terheket és költségeket. Az Azure IoT Central kiválasztásával lehetősége van arra, hogy az Ön üzleti adatait IoT-adataival alakítsa át, és nem csupán egy összetett és folyamatosan fejlődő IoT-infrastruktúra karbantartására és frissítésére koncentrál.

A könnyen használható kezelőfelülettel egyszerűen figyelheti az eszközök feltételeit, szabályokat hozhat létre, és több millió eszközt és adatmennyiséget kezelhet életciklusa során. Továbbá lehetővé teszi, hogy az IoT-intelligenciát az üzletági alkalmazásokra kiterjesztve az eszköz elemzéseit is fellépjen.

Ez a cikk az Azure IoT Central esetében ismerteti:

- A projekthez társított tipikus szerepek.
- Egy alkalmazás létrehozása.
- Az eszközök az alkalmazáshoz történő csatlakoztatása
- Az alkalmazás felügyelete.

## <a name="personas"></a>Szerepek

Az Azure IoT Central dokumentációja négy olyan személyre utal, akik egy Azure IoT Central alkalmazással működnek:

- A _szerkesztő_ feladata, hogy meghatározza, milyen típusú eszközök csatlakoznak az alkalmazáshoz, valamint testreszabja az alkalmazást az operátor számára.
- Az _operátor_ kezeli az alkalmazáshoz csatlakoztatott eszközöket.
- A _rendszergazdák_ felelősek olyan feladatokért, mint például a [felhasználói szerepkörök és engedélyek](howto-administer.md) kezelése az alkalmazáson belül.
- Az _eszközfejlesztő_ hozza létre azokat a kódokat, amelyek az alkalmazáshoz csatlakoztatott eszközökön futnak.

## <a name="create-your-azure-iot-central-application"></a>Azure IoT Central-alkalmazás létrehozása

Szerkesztőként az Azure IoT Central használatával egyéni, a felhőben üzemeltetett IoT-megoldásokat hozhat létre cége számára. Az egyéni IoT-megoldások általában a következőket foglalják magukban:

- Egy felhőalapú alkalmazás, amely telemetriai adatokat fogad az eszközökről és lehetővé teszi az eszközök felügyeletét.
- Több eszköz, amelyek egyéni kódokat futtatnak és a felhőalapú alkalmazáshoz csatlakoznak.

Gyorsan üzembe helyezhet egy új Azure IoT Central alkalmazást, és testre szabhatja a böngészőben megadott igényei szerint. Építőként a webalapú eszközök használatával hozzon létre egy _sablont_ az alkalmazáshoz csatlakozó eszközökhöz. Az eszköz sablonja az a terv, amely meghatározza egy adott típusú eszköz jellemzőit és viselkedését, például:

- Telemetria.
- Az operátor által módosítható üzleti tulajdonságok.
- Azok az eszköztulajdonságok, amelyeket az eszköz állít be, és az alkalmazásban csak olvashatók.
- Az alkalmazás által válaszoló küszöbértékek.
- Az eszköz viselkedését meghatározó beállítások.

Az eszközsablonok és az alkalmazások azonnal tesztelhetők az Azure IoT Central által létrehozott szimulált adatokkal.

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

A rendszergazdák [felhasználói szerepkörökkel és engedélyekkel](howto-administer.md)kezelhetik az alkalmazáshoz való hozzáférést.

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az Azure IoT Central jellemzőit, tekintse meg a javasolt következő lépéseket:

- [Az Azure IoT Central és az Azure IoT-megoldásgyorsítók](overview-iot-options.md) különbségeinek összehasonlítása.
- Ismerkedés az [Azure IoT Central kezelőfelületével](overview-iot-central-tour.md).
- [Egy Azure IoT Central-alkalmazás létrehozásának](quick-deploy-iot-central.md) első lépései.
- Egy oktatóanyag-sorozat mutatja be a következőket:
  - [Szerkesztőként: Eszközsablon létrehozása](tutorial-define-device-type.md)
  - [Szerkesztőként: Szabályok hozzáadása a megoldás automatizálásához](tutorial-configure-rules.md)
  - [Szerkesztőként: Az alkalmazás testreszabása az operátorok számára](tutorial-customize-operator.md)
  - [Operátorként: Eszközök monitorozása](tutorial-monitor-devices.md)
  - [Operátorként: Valós eszköz hozzáadása a megoldáshoz](tutorial-add-device.md)
  - [Eszközfejlesztőként: Kód létrehozása az eszközökhöz](tutorial-add-device.md#prepare-the-client-code)
