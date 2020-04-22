---
title: Azure IoT Central-alkalmazás létrehozása | Microsoft Docs
description: Új Azure IoT Central-alkalmazás létrehozása. Hozza létre az alkalmazást az ingyenes díjszabási csomag vagy a standard díjcsomagok egyikével.
author: viv-liu
ms.author: viviali
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: b670776804c9758774bf216052254148f063e7da
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758143"
---
# <a name="create-an-azure-iot-central-application"></a>Azure IoT Central-alkalmazás létrehozása

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Azure IoT Central-alkalmazást.

## <a name="create-an-application"></a>Alkalmazás létrehozása

Keresse meg az [Azure IoT Central Build](https://aka.ms/iotcentral) webhelyet. Ezután jelentkezzen be microsoftos személyes, munkahelyi vagy iskolai fiókkal.

Hozzon létre egy új alkalmazást az iparágszempontjából releváns IoT Central-sablonok listájából, hogy gyorsan elkezdhesse a munkát, vagy kezdje a nulláról egy **egyéni alkalmazássablon** használatával. Ebben a rövid útmutatóban az **Egyéni alkalmazássablont** használja.

Új Azure IoT Central alkalmazás létrehozása az **egyéni alkalmazássablonból:**

1. Nyissa meg a **Build** lapot:

    ![Az IoT-alkalmazásoldal létrehozása](media/quick-deploy-iot-central/iotcentralcreate-new-application.png)

1. Válassza **az Egyéni alkalmazások lehetőséget,** és győződjön meg arról, hogy az Egyéni **alkalmazássablon** ki van jelölve.

1. Az Azure IoT Central automatikusan javaslatot tesz egy **alkalmazás név** re konndi a kiválasztott alkalmazássablon alapján. Használhatja ezt a nevet, vagy adja meg saját rövid alkalmazás nevét.

1. Az Azure IoT Central is létrehoz egy egyedi **alkalmazás URL-előtagot** az alkalmazás neve alapján. Ezzel az URL-címmel érheti el az alkalmazást. Módosítsa ezt az URL-előtagot valami emlékezetesebbre, ha szeretné.

    ![Azure IoT Central Alkalmazáslap létrehozása](media/quick-deploy-iot-central/iotcentralcreate-custom.png)

    ![Az Azure IoT központi számlázási adatai](media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png)

    > [!NOTE]
    > Ha az előző lapon az **Egyéni alkalmazás** lehetőséget választotta, megjelenik egy **alkalmazássablon** legördülő legördülő menü. Itt válthat az egyéni és az örökölt sablonok között. Előfordulhat, hogy a szervezet számára elérhetővé tett egyéb sablonok is megjelennek.

1. Válassza ki, hogy hozza létre ezt az alkalmazást a 7 napos ingyenes próbadíj díjcsomag, vagy az egyik szabványos árképzési tervek:

    - Az *ingyenes* csomaggal létrehozott alkalmazások hét napig ingyenesek, és legfeljebb öt eszközt támogatnak. Bármikor átalakíthatja őket, hogy szabványos díjszabási csomagot használjanak, mielőtt azok lejárnának.
    - A *szabványos* csomag használatával létrehozott alkalmazások számlázása eszközönként történik, így **Standard 2** az első két eszköz ingyenesen **választhat.** Az ingyenes és standard díjszabási csomagokról az [Azure IoT Central díjszabási oldalán olvashat bővebben.](https://azure.microsoft.com/pricing/details/iot-central/) Ha egy alkalmazást szabványos díjcsomag használatával hoz létre, ki kell választania a *címtár,* az *Azure-előfizetés*és *a hely helyet:*
        - *A címtár* az az Azure Active Directory, amelyben létrehozza az alkalmazást. Az Azure Active Directory felhasználói identitásokat, hitelesítő adatokat és egyéb szervezeti adatokat tartalmaz. Ha nem rendelkezik Azure-alapú Active Directoryval, az Azure-előfizetés létrehozásakor létrejön egy.
        - Az *Azure-előfizetéssel* Azure-szolgáltatások példányait hozhatja létre. Az IoT Central erőforrásokat tartalmaz az előfizetésében. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet ingyenesen az [Azure regisztrációs oldalán.](https://aka.ms/createazuresubscription) Az Azure-előfizetés létrehozása után keresse vissza az **új alkalmazás** lapra. Az új előfizetés most megjelenik az **Azure-előfizetés** legördülő menüben.
        - *A hely* az a [földrajzi hely,](https://azure.microsoft.com/global-infrastructure/geographies/) ahol létre szeretné hozni az alkalmazást. Az optimális teljesítmény érdekében általában azt a helyet kell kiválasztania, amely fizikailag a legközelebb van az eszközökhöz. Miután kiválasztotta a helyet, később nem helyezheti át az alkalmazást egy másik helyre.

1. Tekintse át az általános szerződési feltételeket, és válassza a lap alján található **Létrehozás** lehetőséget. Néhány perc múlva az IoT Central alkalmazás készen áll a használatra:

    ![Azure IoT Central alkalmazás](media/quick-deploy-iot-central/iotcentral-application.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy IoT Central-alkalmazást. Az alábbiakban az IoT Central megismerésének folytatásához javasolt következő lépést javasoljuk:

> [!div class="nextstepaction"]
> [Szimulált eszköz hozzáadása az IoT Central alkalmazáshoz](./quick-create-simulated-device.md)

Ha Ön eszközfejlesztő, és szeretne belemerülni néhány kódba, a javasolt következő lépés a következő:
> [!div class="nextstepaction"]
> [Ügyfélalkalmazás létrehozása és csatlakoztatása az Azure IoT Central alkalmazáshoz](./tutorial-connect-device-nodejs.md)
