---
title: Az Azure IoT Central Device Bridge létrehozása | Microsoft Docs
description: A IoT Central Device Bridge használatával összekapcsolhatók más IoT-felhők (Sigfox, részecske, The Things Network stb.) a IoT Central alkalmazáshoz.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: how-to
manager: peterpr
ms.openlocfilehash: 6499c9c29d10a2056b0af5499b68b5edd67d82cb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80158418"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Hozza létre a IoT Central Device Bridge eszközt más IoT-felhők csatlakoztatásához IoT Central

*Ez a témakör a rendszergazdákra vonatkozik.*

A IoT Central Device Bridge egy nyílt forráskódú megoldás, amely összekapcsolja a Sigfox, a részecske-t, a hálózatokat és más felhőket a IoT Central alkalmazáshoz. Függetlenül attól, hogy a Sigfox alacsony energiaellátású hálózatához csatlakoztatott eszköz-követési eszközöket használ, vagy a levegőminőség-figyelési eszközöket a részecske-eszköz felhőben használja, vagy a TTN a talaj nedvességtartalmának figyelésére szolgáló eszközöket használ, közvetlenül kihasználhatja a IoT Central erejét a IoT Central Device Bridge használatával. Az eszköz-híd más IoT-Felhőkkel csatlakozik a IoT Centralhoz, és továbbítja azokat az eszközöket, amelyeket az eszközök a többi felhőbe továbbítanak a IoT Central alkalmazásba. A IoT Central alkalmazásban szabályokat hozhat létre, és elemzéseket futtathat ezen az adatain, munkafolyamatokat hozhat létre a Microsoft Flow és az Azure Logic apps szolgáltatásban, majd exportálhatja azokat. A [IoT Central-eszköz híd](https://aka.ms/iotcentralgithubdevicebridge) beszerzése a githubról

## <a name="what-is-it-and-how-does-it-work"></a>Mi ez, és hogyan működik?
A IoT Central Device Bridge egy nyílt forráskódú megoldás a GitHubban. Készen áll a "üzembe helyezés az Azure-ban" gombra, amely egy egyéni Azure Resource Manager-sablont helyez üzembe több Azure-erőforrással az Azure-előfizetésében. Az erőforrások a következők:
-    Azure Function-alkalmazás
-    Azure Storage-tárfiók neve
-    Használatalapú csomag
-    Azure Key Vault

A Function alkalmazás az eszköz híd kritikus eleme. HTTP POST-kéréseket kap más IoT platformokról vagy egyéni platformokról egy egyszerű webhook-integráción keresztül. Olyan példákat ismertetünk, amelyek bemutatják, hogyan csatlakozhat a Sigfox, a részecske-és a TTN-felhőkhöz. Ezt a megoldást egyszerűen kiterjesztheti az egyéni IoT-felhőhöz való kapcsolódáshoz, ha a platform HTTP POST-kéréseket küldhet a Function alkalmazásnak.
A Function alkalmazás az IoT Central által elfogadott formátumba átalakítja az adathalmazt, és a DPS API-kon keresztül továbbítja azokat.

![Képernyőkép az Azure functions szolgáltatásról](media/howto-build-iotc-device-bridge/azfunctions.png)

Ha a IoT Central alkalmazás a továbbított üzenetben az eszköz azonosítója alapján ismeri fel az eszközt, az adott eszközhöz új mérték fog megjelenni. Ha az eszköz AZONOSÍTÓját soha nem látták a IoT Central alkalmazás, a Function alkalmazás megpróbál regisztrálni egy új eszközt az eszköz azonosítójával, és a rendszer a IoT Central alkalmazásban "nem társított eszközként" jelenik meg. 

## <a name="how-do-i-set-it-up"></a>Hogyan beállítani?
Az utasításokat a GitHub-adattár README fájljában találja meg részletesen. 

## <a name="pricing"></a>Díjszabás
Az Azure-erőforrások az Azure-előfizetésében lesznek üzemeltetve. A díjszabással kapcsolatos további információkért tekintse meg a [Readme fájlt](https://aka.ms/iotcentralgithubdevicebridge).

## <a name="next-steps"></a>További lépések
Most, hogy megismerte, hogyan hozhatja létre a IoT Central-eszköz hidat, itt látható a javasolt következő lépés:

> [!div class="nextstepaction"]
> [Eszközök kezelése](howto-manage-devices.md)
