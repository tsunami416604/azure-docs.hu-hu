---
title: Mi az az Azure IoT Central? | Microsoft Docs
description: Az Azure IoT Central egy teljes körű Szolgáltatottszoftver-megoldás használatával hozhat létre és kezelheti az egyéni IoT-megoldás. Ez a cikk áttekintést nyújt az Azure IoT Central jellemzőiről.
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 9fc565996797c90a6d2ac9b3851ac3408f1842c7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183271"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>Mi az az Azure IoT Central?

Az Azure IoT Central egy teljes körűen felügyelt IoT szoftverszolgáltatási megoldás, amely megkönnyíti a fizikai és a digitális világok között kapcsolatot teremtő termékek létrehozását. A csatlakoztatott termékre vonatkozó elképzelését a következőképpen valósíthatja meg:

- Új megállapítások kinyerése a csatlakoztatott eszközökről, hogy az ügyfelek jobb termékekhez és élményekhez juthassanak.
- Új üzleti lehetőségek teremtése a cég számára.

Az Azure IoT a tipikus IoT-projektekhez képest a következőképpen teszi egyszerűbbé az IoT-megoldások felügyeletét:

- Csökkenti a felügyeleti terheket.
- Csökkenti az üzemeltetési költségeket és terhelést.
- Megkönnyíti az alkalmazások testreszabását a következők segítségével:
  - Iparágvezető technológiák, mint például az [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) és az [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) alkalmazása.
  - Nagyvállalati szintű biztonsági funkciók, például végpontok közötti titkosítás.

A következő videó áttekintést nyújt az Azure IoT Centralról:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

A cikk többi része az Azure IoT Central következő jellemzőit ismerteti:

- A projekthez társított tipikus szerepek.
- Egy alkalmazás létrehozása.
- Az eszközök az alkalmazáshoz történő csatlakoztatása
- Az alkalmazás felügyelete.

## <a name="personas"></a>Szerepek

Az Azure IoT Central dokumentációja négyféle szerepet különböztet meg az Azure IoT Central-alkalmazásokkal kapcsolatban:

- A _szerkesztő_ feladata, hogy meghatározza, milyen típusú eszközök csatlakoznak az alkalmazáshoz, valamint testreszabja az alkalmazást az operátor számára.
- Az _operátor_ kezeli az alkalmazáshoz csatlakoztatott eszközöket.
- A _rendszergazda_ felelős az olyan adminisztrációs feladatokért, mint a felhasználók és szerepkörök kezelése az alkalmazáson belül.
- Az _eszközfejlesztő_ hozza létre azokat a kódokat, amelyek az alkalmazáshoz csatlakoztatott eszközökön futnak.

## <a name="create-your-azure-iot-central-application"></a>Azure IoT Central-alkalmazás létrehozása

Szerkesztőként az Azure IoT Central használatával egyéni, a felhőben üzemeltetett IoT-megoldásokat hozhat létre cége számára. Az egyéni IoT-megoldások általában a következőket foglalják magukban:

- Egy felhőalapú alkalmazás, amely telemetriai adatokat fogad az eszközökről és lehetővé teszi az eszközök felügyeletét.
- Több eszköz, amelyek egyéni kódokat futtatnak és a felhőalapú alkalmazáshoz csatlakoznak.

Gyorsan létrehozhat egy új Azure IoT Central-alkalmazást, majd közvetlenül a böngészőben testreszabhatja saját egyéni igényei szerint. Azure IoT Central-szerkesztőként a webes eszközökkel létrehozhat egy _eszközsablont_ az alkalmazáshoz csatlakozó eszközök számára. Az eszközsablon adja az eszközmodellek alapját. Az ugyanabból az eszközsablonból létrehozott összes eszköznek közös a sablonja. Az eszközsablon egy adott eszköztípus olyan jellemzőit és viselkedéstípusait határozza meg, mint:

- Az eszköz által küldött telemetriai adatok.
- Az operátor által módosítható üzleti tulajdonságok.
- Azok az eszköztulajdonságok, amelyeket az eszköz állít be, és az alkalmazásban csak olvashatók.
- Az alkalmazás reakcióinak küszöbértékei.
- Az eszköz viselkedését meghatározó beállítások.

Az eszközsablonok és az alkalmazások azonnal tesztelhetők az Azure IoT Central által létrehozott szimulált adatokkal.

A szerkesztők emellett testreszabhatják az Azure IoT Central-alkalmazás kezelőfelületét az operátorok számára, akiknek az alkalmazás napi használata a feladata. A szerkesztők által elvégezhető testreszabási lehetőségek közé tartoznak a következők:

- Egy eszközsablonban található tulajdonságok és beállítások elrendezésének meghatározása.
- Egyéni irányítópultok konfigurálása, amelyek segítik az operátorokat a megállapítások felismerésében és a problémák gyorsabb megoldásában.
- Egyéni elemzések konfigurálása, amelyek a csatlakoztatott eszközökről származó idősorozat-adatokat vizsgálják.

## <a name="connect-your-devices"></a>Az eszközök csatlakoztatása

Miután a szerkesztő meghatározta, hogy az alkalmazáshoz milyen típusú eszközök csatlakozhatnak, egy eszközfejlesztő létrehozza az eszközökön futtatandó kódokat. Az eszközfejlesztők a Microsoft nyílt forráskódú [Azure IoT SDK-it](https://github.com/Azure/azure-iot-sdks) használhatják az eszközkódok létrehozásához. Ezek az SDK-k számos nyelvet, platformot és protokollt támogatnak, hogy megfeleljenek az Ön igényeinek az eszközök és az Azure IoT Central-alkalmazás csatlakoztatását illetően. Az SDK-k segítségével a következő feladatok végezhetők el az Azure IoT Centralhoz csatlakoztatott eszközökön:

- Biztonságos kapcsolat létrehozása.
- Telemetria küldése.
- Állapotjelentés.
- Konfigurációfrissítések fogadása.

További információkért olvassa el a következő blogbejegyzést: [Milyen előnyökkel jár, ha az Azure IoT SDK-kat használja, és milyen buktatókkal, ha nem?](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)

## <a name="manage-your-application"></a>Az alkalmazás felügyelete

Az Azure IoT Central-alkalmazásokat teljes mértékben a Microsoft üzemelteti, ami csökkenti az alkalmazások felügyelete miatti adminisztrációs terheket.

Az operátorok az Azure IoT Central-alkalmazással kezelik az eszközöket az Azure IoT Central-megoldásban. Az operátorok a következő feladatokat végezhetik el:

- Az alkalmazáshoz csatlakoztatott eszközök monitorozása.
- Az eszközök hibáinak keresése és elhárítása.
- Új eszközök üzembe helyezése.

A szerkesztő az eszközsablon szintjén határozhat meg egyéni szabályokat és műveleteket a streamelési adatokra vonatkozóan. Az operátor eszközszinten engedélyezheti vagy letilthatja ezeket a szabályokat, az alkalmazáson belüli feladatok vezérlése és automatizálása céljából.

A rendszergazda [felhasználói szerepkörök és engedélyek](howto-administer.md) beállításával felügyelheti a hozzáférést az alkalmazásokhoz.

## <a name="next-steps"></a>További lépések

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
