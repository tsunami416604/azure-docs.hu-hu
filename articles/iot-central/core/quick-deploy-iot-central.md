---
title: Azure IoT Central-alkalmazás létrehozása | Microsoft Docs
description: Új Azure IoT Central-alkalmazás létrehozása. Hozza létre az alkalmazást az ingyenes díjszabási csomag vagy az egyik standard díjszabási csomag használatával.
author: viv-liu
ms.author: viviali
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 5268f5438c005033f9c6ecf74657dc1a01d3b673
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989396"
---
# <a name="create-an-azure-iot-central-application"></a>Azure IoT Central-alkalmazás létrehozása

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

_Szerkesztőként_ az Azure IoT Central használatával határozza meg a Microsoft Azure IoT Central-alkalmazást. Ez a rövid útmutató bemutatja, hogyan hozhat létre olyan Azure IoT Central alkalmazást, amely egy minta- _eszköz sablont_tartalmaz. A létrehozott alkalmazás nem használ előzetes verziójú funkciókat.

## <a name="create-an-application"></a>Alkalmazás létrehozása

Navigáljon az [Azure IoT Central Build](https://aka.ms/iotcentral) webhelyére. Ezután jelentkezzen be a Microsoft személyes, munkahelyi vagy iskolai fiókjával.

Ha olyan Azure IoT Central alkalmazást szeretne létrehozni, amelyen nincs engedélyezve az előzetes verziójú funkció, válassza a **Létrehozás**lehetőséget. Ez a hivatkozás a IoT- **alkalmazás összeállítására** szolgáló oldalt veszi fel.

![Azure IoT Central-Build oldal](media/quick-deploy-iot-central/iotcentralcreate.png)

Ezután válassza az **egyéni alkalmazás**lehetőséget.

Új Azure IoT Central-alkalmazás létrehozása:

1. Az Azure IoT Central automatikusan javaslatot tesz az alkalmazás nevére a kiválasztott alkalmazás sablonja alapján. Ezt a nevet elfogadhatja, vagy megadhatja a saját felhasználóbarát alkalmazásának nevét, például a **contoso IoT**is. Az Azure IoT Central egy egyedi URL-címet is létrehoz az Ön számára az alkalmazás neve alapján. Ha szeretné, megváltoztathatja ezt az URL-előtagot valami emlékezetre.

1. Válassza ki azt az **örökölt alkalmazást** , amely nem használja az előzetes verziójú funkciókat.

    | Alkalmazássablon | Leírás |
    | -------------------- | ----------- |
    | Örökölt alkalmazás   | Létrehoz egy üres alkalmazást, amelybe a saját eszközsablonjait és eszközeit helyezheti el. |

    ![Azure IoT Central új alkalmazás](media/quick-deploy-iot-central/newapplication.png)

    ![Azure IoT Central számlázási adatok](media/quick-deploy-iot-central/billinginfo.png)

1. Válassza ezt az alkalmazást a 7 napos ingyenes próbaverzió díjszabási csomagjának használatával, vagy a standard díjszabási csomagok valamelyikével:
   - Az *ingyenes* csomag használatával létrehozott alkalmazások hét napig ingyenesen használhatók, és legfeljebb öt eszközt támogatnak. Egy standard díjszabási csomag használatára a lejárat előtt bármikor átalakítható.
   - A *standard* csomag használatával létrehozott alkalmazások számlázása eszközönként történik, a **standard 1** vagy **Standard 2** díjszabási csomaggal pedig az első két eszköz ingyenesen használható. Az ingyenes és standard díjszabási csomagokról az [Azure IoT Central díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/iot-central/)tájékozódhat. Ha létrehoz egy szabványos alkalmazást, ki kell választania a *címtárat*, az *Azure-előfizetést*és a *helyet*:
        - Az alkalmazás létrehozásához a *könyvtár* a Azure Active Directory (ad). Felhasználói identitásokat, hitelesítő adatokat és egyéb szervezeti információkat tartalmaz. Ha még nem rendelkezik Azure AD-vel, akkor létrejön egy Azure-előfizetés létrehozásakor.
        - Az *Azure-előfizetéssel* Azure-szolgáltatások példányait hozhatja létre. IoT Central az előfizetéshez tartozó erőforrásokat. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs lapján](https://aka.ms/createazuresubscription). Az Azure-előfizetés létrehozása után váltson vissza az **alkalmazás létrehozása** lapra. Az új előfizetése megjelenik az **Azure-előfizetések** legördülő menüjében.
        - A hely az a [földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/) *hely* , ahol létre szeretné hozni az alkalmazást. Az optimális teljesítmény érdekében általában ki kell választania az eszközökhöz legközelebb eső helyet. Az Azure IoT Central jelenleg a **Egyesült Államok**, **Ausztráliában**, **Ázsia és a csendes-óceáni térség**vagy **Európában**érhető el.  A hely kiválasztása után később nem helyezheti át az alkalmazást egy másik helyre.
        További információk a díjszabásról az [Azure IoT Central díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/iot-central/) találhatók.

1. Töltse ki a korábban kiválasztott fizetési csomaghoz szükséges további információkat az 1. lépésben.

1. A lap alján kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy IoT Central-alkalmazást. A következő javasolt lépés:

> [!div class="nextstepaction"]
> [Új eszköz típusának meghatározása az Azure IoT Central alkalmazásban](./tutorial-define-device-type.md)
