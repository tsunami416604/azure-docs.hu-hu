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
ms.openlocfilehash: eb6759d95ab0fb7afd3b6179babf052dfb029ff2
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873445"
---
# <a name="create-an-azure-iot-central-application"></a>Azure IoT Central-alkalmazás létrehozása

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

_Szerkesztőként_ az Azure IoT Central használatával határozza meg a Microsoft Azure IoT Central-alkalmazást. Ez a rövid útmutató a bemutatja, hogyan hozhat létre egy _eszközsablonmintát_ és szimulált _eszközöket_ tartalmazó Azure IoT Central-alkalmazást.

## <a name="create-an-application"></a>Alkalmazás létrehozása

Navigáljon az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyére. Be kell jelentkeznie személyes, munkahelyi vagy iskolai Microsoft-fiókjával.

Egy új Azure IoT Central-alkalmazás létrehozásának megkezdéséhez válassza az **Új alkalmazás** lehetőséget. Ekkor megjelenik az **Alkalmazás létrehozása** oldal.

![Azure IoT Central Alkalmazás létrehozása oldal](media/quick-deploy-iot-central/iotcentralcreate.png)

Új Azure IoT Central-alkalmazás létrehozása:

1. Válassza ki a fizetési konstrukciót:
   - A **próbaalkalmazások** 7 napig ingyenesek, azután lejárnak. A lejárat előtt bármikor átalakíthatók használatalapú fizetéses alkalmazássá. Ha létrehoz egy **próbaverziós** alkalmazást, meg kell adnia a kapcsolattartási adatait, és el kell döntenie, hogy szeretne-e információkat és tippeket kapni a Microsofttól.
   - A **használatalapú fizetéses** alkalmazások díjának felszámítása eszközönként történik. Az első 5 eszköz ingyenes. Ha **utólagos elszámolású alkalmazást hoz** létre, ki kell választania a *címtárat*, az Azure- *előfizetést*és a *régiót*:
      - Az alkalmazás létrehozásához a *könyvtár* a Azure Active Directory (ad). Felhasználói identitásokat, hitelesítő adatokat és egyéb szervezeti információkat tartalmaz. Ha még nem rendelkezik Azure AD-vel, akkor létrejön egy Azure-előfizetés létrehozásakor.
      - Az *Azure-előfizetéssel* Azure-szolgáltatások példányait hozhatja létre. Az előfizetésben lévő erőforrásokat az IoT Central osztja ki. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs lapján](https://aka.ms/createazuresubscription). Az Azure-előfizetés létrehozása után lépjen vissza az **alkalmazás-létrehozási** lapra. Az új előfizetése megjelenik az **Azure-előfizetések** legördülő menüjében.
      - A *régió* a fizikai hely vagy [Földrajz](https://azure.microsoft.com/global-infrastructure/geographies/) , ahol létre szeretné hozni az alkalmazást. Az optimális teljesítmény érdekében általában az eszközökhöz legközelebb eső régiót kell kiválasztania. Megtekintheti azokat a régiókat, amelyekben az Azure IoT Central elérhető a régiók oldalon [elérhető termékek területen](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central) . Miután kiválasztott egy régiót, később nem helyezheti át az alkalmazást egy másik régióba.

      További információk a díjszabásról az [Azure IoT Central díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/iot-central/) találhatók.

1. Válasszon ki egy alkalmazássablont. Az alkalmazássablon előre megadott elemeket, például eszközsablonokat és irányítópultokat tartalmazhat, amelyek segítséget nyújtanak az első lépésekhez.

    | Alkalmazássablon | Leírás |
    | -------------------- | ----------- |
    | Contoso-példa       | Létrehoz egy alkalmazást, amely már tartalmaz egy eszközsablont egy hűtött eladóautomatához. Használja ezt a sablont az Azure IoT Central megismerésének megkezdéséhez. |
    | Minta: Devkits       | Létrehoz egy eszközsablonokat tartalmazó alkalmazást, amelyekkel csatlakoztathat egy MXChip vagy Raspberry Pi eszközt. Ha Ön eszközfejlesztő, aki ezekkel az eszközökkel kísérletezik, használja ezt a sablont. |
    | Egyéni alkalmazás   | Létrehoz egy üres alkalmazást, amelybe a saját eszközsablonjait és eszközeit helyezheti el. |

1. Adjon meg egy felhasználóbarát nevet, például **contoso IoT**. Az Azure IoT Central létrehoz egy egyéni URL-előtagot. Ezt az URL-előtagot egy könnyebben megjegyezhető előtagra módosíthatja.

1. Kattintson a **Create** (Létrehozás) gombra.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy IoT Central-alkalmazást. A következő javasolt lépés:

> [!div class="nextstepaction"]
> [Új eszköz típusának meghatározása az Azure IoT Central alkalmazásban](./tutorial-define-device-type.md)
