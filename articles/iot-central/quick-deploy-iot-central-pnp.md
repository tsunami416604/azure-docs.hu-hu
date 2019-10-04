---
title: Azure IoT Central-alkalmazás létrehozása | Microsoft Docs
description: Új Azure IoT Central-alkalmazás létrehozása. Próbaalkalmazás vagy használatalapú fizetéses alkalmazás létrehozása alkalmazássablonnal.
author: viv-liu
ms.author: viviali
ms.date: 06/07/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 72d02cecf37c631e6f8097b220848425c5e1fe9c
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719125"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Azure IoT Central-alkalmazás létrehozása (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre olyan Azure IoT Central-alkalmazást, amely előzetes verziójú funkciókat (például IoT Plug and Play) használ.

> [!WARNING]
> Az Azure IoT Central IoT Plug and Play képességei jelenleg nyilvános előzetes verzióban érhetők el. Ne használjon IoT Plug and Play engedélyezett IoT Central alkalmazást az éles számítási feladatokhoz. Éles környezetekhez használjon egy aktuális, általánosan elérhető IoT-alapú központi alkalmazást.

## <a name="create-an-application"></a>Alkalmazás létrehozása

Navigáljon az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyére. Ezután jelentkezzen be a Microsoft személyes, munkahelyi vagy iskolai fiókjával.

Egy új Azure IoT Central-alkalmazás létrehozásának megkezdéséhez válassza az **Új alkalmazás** lehetőséget. Ez a hivatkozás az **alkalmazás létrehozása** lapra kerül.

![Azure IoT Central alkalmazás létrehozása lap](media/quick-deploy-iot-central-pnp/iotcentralcreate-pnp.png)

Új Azure IoT Central-alkalmazás létrehozása, amely tartalmazza az előzetes verziójú funkciókat, például a IoT Plug and Play:

1. Válassza ki a fizetési konstrukciót:
   - A **próbaverziós** alkalmazások érvényessége hét napig ingyenes. A lejáratuk előtt bármikor elvégezhető a **fizetésre való váltás** . Ha létrehoz egy **próbaverziós** alkalmazást, meg kell adnia a kapcsolattartási adatait, és ki kell választania, hogy szeretne-e információkat és tippeket kapni a Microsofttól.
   - Az **utólagos elszámolású alkalmazások** számlázása eszközönként történik, és az első öt eszköz díjmentes. Ha **utólagos elszámolású alkalmazást hoz** létre, ki kell választania a *címtárat*, az *Azure-előfizetést*és a *régiót*:
        - Az alkalmazás létrehozásához a *könyvtár* a Azure Active Directory (ad). Felhasználói identitásokat, hitelesítő adatokat és egyéb szervezeti információkat tartalmaz. Ha még nem rendelkezik Azure AD-vel, akkor létrejön egy Azure-előfizetés létrehozásakor.
        - Az *Azure-előfizetéssel* Azure-szolgáltatások példányait hozhatja létre. Az előfizetésben lévő erőforrásokat az IoT Central osztja ki. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs lapján](https://aka.ms/createazuresubscription). Az Azure-előfizetés létrehozása után váltson vissza az **alkalmazás létrehozása** lapra. Az új előfizetése megjelenik az **Azure-előfizetések** legördülő menüjében.
        - A *régió* az a fizikai hely, ahol létre szeretné hozni az alkalmazást. Az optimális teljesítmény érdekében általában az eszközökhöz legközelebb eső régiót kell kiválasztania. A nyilvános előzetes verzióban az **előzetes verziójú alkalmazások** egyetlen elérhető régiói **Észak-Európa** és az **USA középső**régiója. A régió kiválasztása után később nem helyezheti át az alkalmazást egy másik régióba.

        További információk a díjszabásról az [Azure IoT Central díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/iot-central/) találhatók.

1. Válassza ki az **előnézeti alkalmazás** sablonját. Az alkalmazássablon előre megadott elemeket, például eszközsablonokat és irányítópultokat tartalmazhat, amelyek segítséget nyújtanak az első lépésekhez.

1. Az Azure IoT Central automatikusan javaslatot tesz az alkalmazás nevére a kiválasztott alkalmazás sablonja alapján. Ezt a nevet elfogadhatja, vagy megadhatja a saját felhasználóbarát alkalmazásának nevét, például a **contoso IoT**is. Az Azure IoT Central egy egyedi URL-előtagot is létrehoz az Ön számára az alkalmazás neve alapján. Ha szeretné, megváltoztathatja ezt az URL-előtagot valami emlékezetre.

1. Töltse ki a korábban kiválasztott fizetési csomaghoz szükséges további információkat az 1. lépésben.

1. A lap alján kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy IoT Central alkalmazást, amely az előzetes verziójú funkciókat használja. A következő javasolt lépés:

> [!div class="nextstepaction"]
> [Új eszköz típusának meghatározása az Azure IoT Central alkalmazásban](./tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
