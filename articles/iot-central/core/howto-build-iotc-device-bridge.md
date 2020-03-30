---
title: Az Azure IoT Central eszközhíd létrehozása | Microsoft dokumentumok
description: Az IoT Central eszközhíd létrehozása más IoT-felhők (Sigfox, Particle, The Things Network stb.) és az IoT Central alkalmazás csatlakoztatásához.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: how-to
manager: peterpr
ms.openlocfilehash: 6499c9c29d10a2056b0af5499b68b5edd67d82cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158418"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Az IoT Central eszközhíd létrehozása más IoT-felhők és az IoT-központi

*Ez a témakör a rendszergazdákra vonatkozik.*

Az IoT Central eszközhíd egy nyílt forráskódú megoldás, amely összeköti a Sigfox, a Particle, a The Things Network és más felhők et az IoT Central alkalmazással. Akár a Sigfox alacsony energiafelvételű széles területhálózatához csatlakoztatott eszközkövető eszközöket használ, akár a részecskeeszköz-felhő levegőminőség-ellenőrző eszközeit használja, vagy talajnedvesség-figyelő eszközöket használ a TTN-en, közvetlenül kihasználhatja az IoT erejét. Központi az IoT Central eszközhíd használatával. Az eszközhíd összeköti a többi IoT-felhőt az IoT Centrallal azáltal, hogy továbbítja az eszközöket a többi felhőnek küldött adatokat az IoT Central alkalmazásba. Az IoT Central alkalmazásban szabályokat hozhat létre, és elemzéseket futtathat az adatokon, munkafolyamatokat hozhat létre a Microsoft Flow és az Azure Logic-alkalmazásokban, exportálhatja ezeket az adatokat és még sok mást. Az [IoT Central eszközhíd](https://aka.ms/iotcentralgithubdevicebridge) beszerezhető a GitHubról

## <a name="what-is-it-and-how-does-it-work"></a>Mi ez, és hogyan működik?
Az IoT Central eszközhíd egy nyílt forráskódú megoldás a GitHubon. Készen áll a "Üzembe helyezés az Azure-ba" gombbal, amely egy egyéni Azure Resource Manager-sablont telepít több Azure-erőforrással az Azure-előfizetésében. A források a következők:
-    Azure Függvény alkalmazás
-    Azure Storage-tárfiók neve
-    Használatalapú csomag
-    Azure Key Vault

A függvényalkalmazás az eszközhíd kritikus eleme. Http POST-kéréseket kap más IoT-platformokról vagy bármely egyéni platformról egy egyszerű webhook-integráción keresztül. Olyan példákat mutattunk be, amelyek bemutatják, hogyan lehet csatlakozni a Sigfox, Particle és TTN felhőkhöz. Ezt a megoldást egyszerűen kiterjesztheti az egyéni IoT-felhőhöz való csatlakozáshoz, ha a platform HTTP POST-kérelmeket tud küldeni a függvényalkalmazásnak.
A Függvény alkalmazás átalakítja az adatokat az IoT Central által elfogadott formátumba, és továbbítja azokat a DPS API-kon keresztül.

![Az Azure-függvények képernyőképe](media/howto-build-iotc-device-bridge/azfunctions.png)

Ha az IoT Central alkalmazás felismeri az eszközt a továbbított üzenetben eszközazonosító szerint, egy új mérés jelenik meg az adott eszközhöz. Ha az iot central alkalmazás még soha nem látta az eszközazonosítót, a függvényalkalmazás megpróbál regisztrálni egy új eszközt az adott eszközazonosítóval, és "Nem társított eszközként" fog megjelenni az IoT Central alkalmazásban. 

## <a name="how-do-i-set-it-up"></a>Hogyan állíthatom be?
Az utasítások részletesen szerepelnek a GitHub-tárházban a README fájlban. 

## <a name="pricing"></a>Díjszabás
Az Azure-erőforrások az Azure-előfizetésben lesznek tárolva. Az árképzésről a [README fájlban olvashat bővebben.](https://aka.ms/iotcentralgithubdevicebridge)

## <a name="next-steps"></a>További lépések
Most, hogy megtanulta az IoT Central eszközhíd felépítését, itt a javasolt következő lépés:

> [!div class="nextstepaction"]
> [Eszközök kezelése](howto-manage-devices.md)
