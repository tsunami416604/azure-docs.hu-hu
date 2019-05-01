---
title: Mi az az Azure IoT Central? | Microsoft Docs
description: Az Azure IoT Central egy teljes körű Szolgáltatottszoftver-megoldás használatával hozhat létre és kezelheti az egyéni IoT-megoldás. Ez a cikk áttekintést nyújt az Azure IoT Central jellemzőiről.
author: dominicbetts
ms.author: dobett
ms.date: 04/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 84fa7aa006a6bc5365527dbf8043797617543590
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704540"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>Mi az az Azure IoT Central?

Az Azure IoT Central egy teljes körűen felügyelt IoT szoftver--szolgáltatásként megoldás, amely megkönnyíti a fizikai és a digitális világ csatlakozó termékek létrehozása is. A csatlakoztatott termékre vonatkozó elképzelését a következőképpen valósíthatja meg:

- Új megállapítások kinyerése a csatlakoztatott eszközökről, hogy az ügyfelek jobb termékekhez és élményekhez juthassanak.
- Új üzleti lehetőségek teremtése a cég számára.

Az Azure IoT Central, mint egy tipikus IoT-projektet a korábban megszokott:

- Csökkenti a kezelési terheket.
- Csökkenti a működési költségeket és az általános költségeket.
- Könnyen testre szabhatja az alkalmazás használata során:
  - Iparágvezető technológiák, mint például az [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) és az [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) alkalmazása.
  - Nagyvállalati szintű biztonsági funkciók, például végpontok közötti titkosítás.

A következő videó áttekintést nyújt az Azure IoT Centralról:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

Ez a cikk ismerteti az Azure IoT Central:

- A projekthez társított tipikus szerepek.
- Egy alkalmazás létrehozása.
- Az eszközök az alkalmazáshoz történő csatlakoztatása
- Az alkalmazás felügyelete.

## <a name="personas"></a>Szerepek

Az Azure IoT Central dokumentációja négy személyeknek, akik használhatják az Azure IoT Central alkalmazáshoz hivatkozik:

- A _szerkesztő_ feladata, hogy meghatározza, milyen típusú eszközök csatlakoznak az alkalmazáshoz, valamint testreszabja az alkalmazást az operátor számára.
- Az _operátor_ kezeli az alkalmazáshoz csatlakoztatott eszközöket.
- A _rendszergazda_ felelős az olyan adminisztrációs feladatokért, mint a felhasználók és szerepkörök kezelése az alkalmazáson belül.
- Az _eszközfejlesztő_ hozza létre azokat a kódokat, amelyek az alkalmazáshoz csatlakoztatott eszközökön futnak.

## <a name="create-your-azure-iot-central-application"></a>Azure IoT Central-alkalmazás létrehozása

Szerkesztőként az Azure IoT Central használatával egyéni, a felhőben üzemeltetett IoT-megoldásokat hozhat létre cége számára. Az egyéni IoT-megoldások általában a következőket foglalják magukban:

- Egy felhőalapú alkalmazás, amely telemetriai adatokat fogad az eszközökről és lehetővé teszi az eszközök felügyeletét.
- Több eszköz, amelyek egyéni kódokat futtatnak és a felhőalapú alkalmazáshoz csatlakoznak.

Gyorsan üzembe helyezhet egy új Azure IoT Central-alkalmazást, és majd pedig testreszabjuk, hogy az adott igények szerint a böngészőben. A jelentéskészítő használhatja a webes eszközökkel hozhat létre egy _eszköz sablon_ az eszközök számára az alkalmazás számára. Egy eszköz sablon a tervezet meghatározó jellemzőit és viselkedését egy típusú eszköz például a:

- A telemetriai adatokat küldi.
- Az operátor által módosítható üzleti tulajdonságok.
- Azok az eszköztulajdonságok, amelyeket az eszköz állít be, és az alkalmazásban csak olvashatók.
- Az alkalmazás válaszol küszöbértékeket.
- Az eszköz viselkedését meghatározó beállítások.

Az eszközsablonok és az alkalmazások azonnal tesztelhetők az Azure IoT Central által létrehozott szimulált adatokkal.

A szerkesztők emellett testreszabhatják az Azure IoT Central-alkalmazás kezelőfelületét az operátorok számára, akiknek az alkalmazás napi használata a feladata. A szerkesztők által elvégezhető testreszabási lehetőségek közé tartoznak a következők:

- Egy eszközsablonban található tulajdonságok és beállítások elrendezésének meghatározása.
- Egyéni irányítópultok konfigurálása, amelyek segítik az operátorokat a megállapítások felismerésében és a problémák gyorsabb megoldásában.
- Egyéni elemzések konfigurálása, amelyek a csatlakoztatott eszközökről származó idősorozat-adatokat vizsgálják.

## <a name="connect-your-devices"></a>Az eszközök csatlakoztatása

Miután a szerkesztő meghatározta, hogy az alkalmazáshoz milyen típusú eszközök csatlakozhatnak, egy eszközfejlesztő létrehozza az eszközökön futtatandó kódokat. Az eszközfejlesztők a Microsoft nyílt forráskódú [Azure IoT SDK-it](https://github.com/Azure/azure-iot-sdks) használhatják az eszközkódok létrehozásához. Ezek az SDK-k számos nyelvet, platformot és protokollt támogatnak, hogy megfeleljenek az Ön igényeinek az eszközök és az Azure IoT Central-alkalmazás csatlakoztatását illetően. Az SDK-k megvalósításához a következő eszköz képességeket nyújtanak segítséget:

- Biztonságos kapcsolat létrehozása.
- Telemetria küldése.
- Állapotjelentés.
- Konfigurációfrissítések fogadása.

További információkért olvassa el a következő blogbejegyzést: [Milyen előnyökkel jár, ha az Azure IoT SDK-kat használja, és milyen buktatókkal, ha nem?](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)

## <a name="manage-your-application"></a>Az alkalmazás felügyelete

Az Azure IoT Central-alkalmazásokat teljes mértékben a Microsoft üzemelteti, ami csökkenti az alkalmazások felügyelete miatti adminisztrációs terheket.

Az operátorok az Azure IoT Central-alkalmazással kezelik az eszközöket az Azure IoT Central-megoldásban. Operátorok például feladatokat hajthat végre:

- Az alkalmazáshoz csatlakoztatott eszközök monitorozása.
- Az eszközök hibáinak keresése és elhárítása.
- Új eszközök üzembe helyezése.

Mint szerkesztő megadhatja az egyéni szabályokat és műveleteket, amelyeket a csatlakoztatott eszközökről streamelt adatokon működnek. Az operátor eszközszinten engedélyezheti vagy letilthatja ezeket a szabályokat, az alkalmazáson belüli feladatok vezérlése és automatizálása céljából.

A rendszergazdák az alkalmazásokba való hozzáférés kezelése [felhasználói szerepköröket és engedélyeket](howto-administer.md).

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
