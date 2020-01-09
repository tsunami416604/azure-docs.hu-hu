---
title: Azure IoT Central-alkalmazás létrehozása | Microsoft Docs
description: Új Azure IoT Central-alkalmazás létrehozása. Próbaalkalmazás vagy használatalapú fizetéses alkalmazás létrehozása alkalmazássablonnal.
author: lmasieri
ms.author: lmasieri
ms.date: 12/18/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: bbb287805ac0351153778191d484bae2ef319705
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434797"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Azure IoT Central-alkalmazás létrehozása (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ez a rövid útmutató bemutatja, hogyan hozhat létre olyan Azure IoT Central-alkalmazást, amely előzetes verziójú funkciókat (például IoT Plug and Play) tartalmaz.

> [!WARNING]
> Az Azure IoT Central IoT Plug and Play képességei jelenleg nyilvános előzetes verzióban érhetők el. Ne használjon IoT Plug and Play engedélyezett IoT Central alkalmazást az éles számítási feladatokhoz. Éles környezetekhez használjon egy aktuális, általánosan elérhető IoT-alapú központi alkalmazást.

## <a name="create-an-application"></a>Alkalmazás létrehozása

Navigáljon az [Azure IoT Central Build](https://aka.ms/iotcentral) webhelyére. Ezután jelentkezzen be a Microsoft személyes, munkahelyi vagy iskolai fiókjával.

Hozzon létre egy új alkalmazást az iparághoz kapcsolódó IoT Central sablonok listájából, amely segítséget nyújt a gyors kezdéshez, vagy az **egyéni alkalmazás** sablonjának használatával.

![Azure IoT Central alkalmazás létrehozása lap](media/quick-deploy-iot-central/iotcentralcreate-templates-pnp.png)

Új Azure IoT Central-alkalmazás létrehozása:

1. Ha új Azure IoT Central alkalmazást szeretne létrehozni egy *iparági sablonból*, válasszon ki egy alkalmazást az egyik iparágban elérhető sablonok listájából. Az *egyéni alkalmazás*lehetőség kiválasztásával is elindíthatja az előzményeket.
1. Az Azure IoT Central automatikusan javaslatot tesz az **alkalmazás nevére** a kiválasztott alkalmazás sablonja alapján. Használhatja ezt a nevet, vagy megadhatja a saját felhasználóbarát alkalmazásának nevét.
1. Az Azure IoT Central az alkalmazás neve alapján egyedi **URL-** előtagot is létrehoz az Ön számára. Ezt az URL-címet használja az alkalmazás eléréséhez. Ha szeretné, megváltoztathatja ezt az URL-előtagot valami emlékezetre.

    ![Azure IoT Central alkalmazás létrehozása lap](media/quick-deploy-iot-central/iotcentralcreate-industry-pnp.png)

    > [!NOTE]
    > Ha az egyéni alkalmazás sablont használja, megjelenik egy **alkalmazás-sablon** legördülő mezője. Innen válthat az előzetes verzió és az általánosan elérhető sablonok között. Előfordulhat, hogy a szervezete számára elérhetővé tett egyéb sablonokat is láthat.

1. Válassza ki, hogy az alkalmazást 7 napos ingyenes próbaverzióval kívánja-e létrehozni, vagy az utólagos elszámolású előfizetést használja.
    - A **próbaverziós** alkalmazások hét napig ingyenesen használhatók, és legfeljebb öt eszközt támogatnak. A lejárat előtt bármikor átalakíthatók használatalapú fizetéses alkalmazássá. Ha létrehoz egy próbaverziós alkalmazást, meg kell adnia a kapcsolattartási adatait, és el kell döntenie, hogy szeretne-e információkat és tippeket kapni a Microsofttól.
    - Az utólagos elszámolású alkalmazások számlázása eszközönkénti alapon történik, és az első két eszköz ingyenesen **elérhető** . További információ a [IoT Central díjszabásáról](https://aka.ms/iotcentral-pricing). Ha utólagos elszámolású alkalmazást hoz létre, ki kell választania a *címtárat*, az *Azure-előfizetést*és a *helyet*:
        - A *könyvtár* a Azure Active Directory (HRE), amelyben létre fogja hozni az alkalmazást. Az Azure AD felhasználói identitásokat, hitelesítő adatokat és egyéb szervezeti adatokat tartalmaz. Ha még nem rendelkezik Azure AD-vel, akkor létrejön egy Azure-előfizetés létrehozásakor.
        - Az *Azure-előfizetéssel* Azure-szolgáltatások példányait hozhatja létre. IoT Central az előfizetéshez tartozó erőforrásokat. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs lapján](https://aka.ms/createazuresubscription). Az Azure-előfizetés létrehozása után váltson vissza az **alkalmazás létrehozása** lapra. Az új előfizetés megjelenik az Azure- **előfizetés** legördülő listájában.
        - A hely az a [földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/) *hely* , ahol létre szeretné hozni az alkalmazást. Az optimális teljesítmény érdekében általában ki kell választania az eszközökhöz legközelebb eső helyet. Az Azure IoT Central nyilvános előzetes verziója jelenleg a **Egyesült Államokban**vagy **Európában**érhető el. A hely kiválasztása után később nem helyezheti át az alkalmazást egy másik helyre.

        > [!NOTE]
        > A nyilvános előzetes verzióban a csak az **előzetes verziójú alkalmazások** számára elérhető helyszínek **Európa** és **Egyesült Államok**.

1. Tekintse át a használati feltételeket, majd válassza a **Létrehozás** elemet az oldal alján.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy IoT Central-alkalmazást. A következő javasolt lépés:

> [!div class="nextstepaction"]
> [Szimulált eszköz hozzáadása a IoT Central alkalmazáshoz](./quick-create-pnp-device.md)
