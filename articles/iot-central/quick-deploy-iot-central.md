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
ms.openlocfilehash: 23ef0afbf3a3fd3e0d0db6e3b4130b45530916be
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001269"
---
# <a name="create-an-azure-iot-central-application"></a>Azure IoT Central-alkalmazás létrehozása

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

_Szerkesztőként_ az Azure IoT Central használatával határozza meg a Microsoft Azure IoT Central-alkalmazást. Ez a rövid útmutató bemutatja, hogyan hozhat létre olyan Azure IoT Central alkalmazást, amely egy minta- _eszköz sablont_tartalmaz.

## <a name="create-an-application"></a>Alkalmazás létrehozása

Navigáljon az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyére. Ezután jelentkezzen be a Microsoft személyes, munkahelyi vagy iskolai fiókjával.

Egy új Azure IoT Central-alkalmazás létrehozásának megkezdéséhez válassza az **Új alkalmazás** lehetőséget. Ez a hivatkozás az **alkalmazás létrehozása** lapra kerül.

![Azure IoT Central alkalmazás létrehozása lap](media/quick-deploy-iot-central/iotcentralcreate.png)

Új Azure IoT Central-alkalmazás létrehozása:

1. Válassza ki a fizetési konstrukciót:
   - A **próbaverziós** alkalmazások érvényessége hét napig ingyenes. A lejáratuk előtt bármikor elvégezhető a **fizetésre való váltás** . Ha létrehoz egy **próbaverziós** alkalmazást, meg kell adnia a kapcsolattartási adatait, és el kell döntenie, hogy szeretne-e információkat és tippeket kapni a Microsofttól.
   - Az **utólagos elszámolású alkalmazások** számlázása eszközönként történik, és az első öt eszköz díjmentes. Ha **utólagos elszámolású alkalmazást hoz** létre, ki kell választania a *címtárat*, az Azure- *előfizetést*és a *régiót*:
        - Az alkalmazás létrehozásához a *könyvtár* a Azure Active Directory (ad). Felhasználói identitásokat, hitelesítő adatokat és egyéb szervezeti információkat tartalmaz. Ha még nem rendelkezik Azure AD-vel, akkor létrejön egy Azure-előfizetés létrehozásakor.
        - Az *Azure-előfizetéssel* Azure-szolgáltatások példányait hozhatja létre. IoT Central az előfizetéshez tartozó erőforrásokat. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs lapján](https://aka.ms/createazuresubscription). Az Azure-előfizetés létrehozása után váltson vissza az **alkalmazás létrehozása** lapra. Az új előfizetése megjelenik az **Azure-előfizetések** legördülő menüjében.
        - A *régió* a fizikai hely vagy [Földrajz](https://azure.microsoft.com/global-infrastructure/geographies/) , ahol létre szeretné hozni az alkalmazást. Az optimális teljesítmény érdekében általában az eszközökhöz legközelebb eső régiót kell kiválasztania. Megtekintheti azokat a régiókat, amelyekben az Azure IoT Central elérhető a régiók oldalon [elérhető termékek területen](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central) . A régió kiválasztása után később nem helyezheti át az alkalmazást egy másik régióba.

        További információk a díjszabásról az [Azure IoT Central díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/iot-central/) találhatók.

1. Válassza ki az alkalmazás sablonját. Az alkalmazássablon előre megadott elemeket, például eszközsablonokat és irányítópultokat tartalmazhat, amelyek segítséget nyújtanak az első lépésekhez.

    | Alkalmazássablon | Leírás |
    | -------------------- | ----------- |
    | Contoso-minta       | Létrehoz egy alkalmazást, amely már tartalmaz egy eszközsablont egy hűtött eladóautomatához. Használja ezt a sablont az Azure IoT Central megismerésének megkezdéséhez. |
    | Minta: Devkits       | Létrehoz egy eszközsablonokat tartalmazó alkalmazást, amelyekkel csatlakoztathat egy MXChip vagy Raspberry Pi eszközt. Ha Ön eszközfejlesztő, aki ezekkel az eszközökkel kísérletezik, használja ezt a sablont. |
    | Egyéni alkalmazás   | Létrehoz egy üres alkalmazást, amelybe a saját eszközsablonjait és eszközeit helyezheti el. |

1. Az Azure IoT Central automatikusan javaslatot tesz az alkalmazás nevére a kiválasztott alkalmazás sablonja alapján. Ezt a nevet elfogadhatja, vagy megadhatja a saját felhasználóbarát alkalmazásának nevét, például a **contoso IoT**is. Az Azure IoT Central egy egyedi URL-előtagot is létrehoz az Ön számára az alkalmazás neve alapján. Ha szeretné, megváltoztathatja ezt az URL-előtagot valami emlékezetre.

1. Töltse ki a korábban kiválasztott fizetési csomaghoz szükséges további információkat az 1. lépésben.

1. A lap alján kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy IoT Central-alkalmazást. A következő javasolt lépés:

> [!div class="nextstepaction"]
> [Új eszköz típusának meghatározása az Azure IoT Central alkalmazásban](./tutorial-define-device-type.md)
