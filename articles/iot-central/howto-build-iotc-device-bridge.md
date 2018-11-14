---
title: Hozhat létre az Azure IoT Central eszköz híd |} A Microsoft Docs
description: Hozhat létre az IoT-központ eszköz híd (Sigfox, Particle a dolgok hálózati stb.) más IoT-felhők csatlakozni az IoT Central alkalmazáshoz.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 11/8/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 83f053a8815f31803f536920497fdc42e72d2a2d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629111"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Az IoT-központ eszköz híd egyéb IoT-felhők csatlakozni az IoT-központ létrehozása

*Ez a témakör a rendszergazdák vonatkozik.*

Az IoT-központ eszköz bridge, amely egy nyílt forráskódú megoldás, amely a Sigfox, Particle, a dolgokat Network és más felhők csatlakozik az IoT Central-alkalmazást. Akár nyomon követése Sigfox az alacsony szintű Power hálózathoz csatlakoztatott eszközök használata, légi minőség a Particle eszközről a felhőbe az eszközök figyelése, vagy használja a TTN eszközök figyelése talajjal nedvesség használatával közvetlenül használja ki az IoT Az IoT-központ eszköz bridge segítségével központi. Az eszköz híd egyéb IoT-felhők összekapcsolja az IoT-központ által a más felhőkben keresztül az IoT Central alkalmazáshoz küldeni az eszközök az adatokat továbbító. Az IoT-központ alkalmazásban is hozhat létre szabályokat és elemzések futtatására az adatok, munkafolyamatok létrehozása a Microsoft Flow és az Azure Logic appsben, exportálhatja az adatokat, és még sok más. Első a [IoT-központ eszköz híd](https://aka.ms/iotcentralgithubdevicebridge) a Githubról

## <a name="what-is-it-and-how-does-it-work"></a>Mi ez, és hogyan működik?
Az IoT-központ eszköz bridge, amely egy nyílt forráskódú megoldás a Githubon. Nyissa meg a "Üzembe helyezése az Azure-bA" gomb, amely az Azure-előfizetésben helyezi üzembe helyezi a egyéni Azure Resource Manager-sablonnal a több Azure-erőforrások készen áll. Az erőforrások a következők:
-   Azure-függvényalkalmazás
-   Azure Storage-tárfiók neve
-   App Service-csomag (S1 szint)
-   Az Azure Key Vault a függvényalkalmazás az eszköz híd kritikus részeihez. HTTP POST-kérelmet kap más IoT platformok vagy bármilyen egyéni platformon egy egyszerű webhook-integrációt. Példák azt mutatják be, hogyan csatlakozhat Sigfox Particle és TTN felhők adtunk meg. Ez a megoldás az egyéni IoT-felhőbe csatlakozni, ha a platform HTTP POST kéréseket küldhetnek a függvényalkalmazás könnyen kiterjesztheti.
A függvényalkalmazás alakítja át az adatokat olyan formátumra, fogadja el az IoT-központ, majd továbbítja azt mentén DPS API-kon keresztül.

![Az Azure functions képernyőképe](media/howto-build-iotc-device-bridge/azfunctions.png)

Ha az IoT Central-alkalmazást az eszköz által Eszközazonosítót a továbbított üzenet felismeri, egy új mérték jelenik meg az eszközön. Ha az eszköz azonosítója soha nem látják az IoT Central-alkalmazást, a függvényalkalmazás megkísérli egy új eszközt regisztrálhat az adott eszköz Azonosítóját, és az IoT Central-alkalmazást egy "társítatlan eszköz" állapottal fog megjelenni. 

## <a name="how-do-i-set-it-up"></a>Hogyan állíthatok azt be?
Az utasításokat az információs fájl, a Github-adattárat a részletes listáját. 

## <a name="pricing"></a>Díjszabás
Ez az összes üzemeltetett Azure-előfizetésében. A becsült költsége a kiépített erőforrások többségét származik a [ára a standard szintű App Service-csomag]( https://azure.microsoft.com/en-us/pricing/details/app-service/windows/). További információkért, és hogy hogyan lehetséges csökkentése érdekében ez az információs fájlban.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozhat létre az IoT-központ eszköz híd, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Saját eszközök kezelése](howto-manage-devices.md)