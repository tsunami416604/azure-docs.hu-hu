---
title: Azure IoT Central-alkalmazás létrehozása | Microsoft Docs
description: Új Azure IoT Central-alkalmazás létrehozása. Próbaalkalmazás vagy használatalapú fizetéses alkalmazás létrehozása alkalmazássablonnal.
author: viv-liu
ms.author: viviali
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: ba131002df64f972aa921a0f002a6d26c58f21ff
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280622"
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

1. Válasszon olyan sablont, amely nem használja az előzetes verziójú funkciókat. Az alkalmazássablon előre megadott elemeket, például eszközsablonokat és irányítópultokat tartalmazhat, amelyek segítséget nyújtanak az első lépésekhez.

    | Alkalmazássablon | Leírás |
    | -------------------- | ----------- |
    | Minta: Contoso       | Létrehoz egy alkalmazást, amely már tartalmaz egy eszközsablont egy hűtött eladóautomatához. Használja ezt a sablont az Azure IoT Central megismerésének megkezdéséhez. |
    | Minta: Devkits       | Létrehoz egy eszközsablonokat tartalmazó alkalmazást, amelyekkel csatlakoztathat egy MXChip vagy Raspberry Pi eszközt. Ha Ön eszközfejlesztő, aki ezekkel az eszközökkel kísérletezik, használja ezt a sablont. |
    | Egyéni alkalmazás   | Létrehoz egy üres alkalmazást, amelybe a saját eszközsablonjait és eszközeit helyezheti el. |

1. Válassza ki a fizetési konstrukciót:
   - a **7 napos ingyenes próbaverziós** alkalmazások a lejárat előtt hét napig ingyenesek. A lejáratuk előtt bármikor elvégezhető a **fizetésre való váltás** . Ha létrehoz egy **próbaverziós** alkalmazást, meg kell adnia a kapcsolattartási adatait, és el kell döntenie, hogy szeretne-e információkat és tippeket kapni a Microsofttól.
   - Az **utólagos elszámolású alkalmazások** számlázása eszközönként történik, és az első öt eszköz díjmentes. Ha **utólagos elszámolású alkalmazást hoz** létre, ki kell választania a *címtárat*, az Azure- *előfizetést*és a *helyet*:
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
