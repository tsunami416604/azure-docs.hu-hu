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
ms.openlocfilehash: 9d242c48068e96498a811f52dbc599abd32bc936
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383003"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Azure IoT Central-alkalmazás létrehozása (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre olyan Azure IoT Central-alkalmazást, amely előzetes verziójú funkciókat (például IoT Plug and Play) használ.

> [!WARNING]
> Az Azure IoT Central IoT Plug and Play képességei jelenleg nyilvános előzetes verzióban érhetők el. Ne használjon IoT Plug and Play engedélyezett IoT Central alkalmazást az éles számítási feladatokhoz. Éles környezetekhez használjon egy aktuális, általánosan elérhető IoT-alapú központi alkalmazást.

## <a name="create-an-application"></a>Alkalmazás létrehozása

Navigáljon az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyére. Ezután jelentkezzen be a Microsoft személyes vagy munkahelyi vagy iskolai fiókjával.

Egy új Azure IoT Central-alkalmazás létrehozásának megkezdéséhez válassza az **Új alkalmazás** lehetőséget. Ez a hivatkozás az **alkalmazás létrehozása** lapra kerül.

![Azure IoT Central alkalmazás létrehozása lap](media/quick-deploy-iot-central-pnp/iotcentralcreate.png)

Új Azure IoT Central-alkalmazás létrehozása, amely tartalmazza az előzetes verziójú funkciókat, például a IoT Plug and Play:

1. Válassza ki a fizetési konstrukciót:
   - A **próbaverziós** alkalmazások érvényessége hét napig ingyenes. A lejárat előtt bármikor átalakíthatók használatalapú fizetéses alkalmazássá. Ha létrehoz egy **próbaverziós** alkalmazást, meg kell adnia a kapcsolattartási adatait, és el kell döntenie, hogy szeretne-e információkat és tippeket kapni a Microsofttól.
   - Az **utólagos elszámolású alkalmazások** számlázása eszközönként történik, és az első öt eszköz díjmentes. Ha **utólagos elszámolású alkalmazást hoz** létre, ki kell választania a *címtárat*, az Azure- *előfizetést*és a *régiót*:
      - Az alkalmazás létrehozásához a *könyvtár* a Azure Active Directory (ad). Felhasználói identitásokat, hitelesítő adatokat és egyéb szervezeti információkat tartalmaz. Ha még nem rendelkezik Azure AD-vel, akkor létrejön egy Azure-előfizetés létrehozásakor.
      - Az *Azure-előfizetéssel* Azure-szolgáltatások példányait hozhatja létre. Az előfizetésben lévő erőforrásokat az IoT Central osztja ki. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs lapján](https://aka.ms/createazuresubscription). Az Azure-előfizetés létrehozása után lépjen vissza az **alkalmazás-létrehozási** lapra. Az új előfizetése megjelenik az **Azure-előfizetések** legördülő menüjében.
      - A *régió* az a fizikai hely, ahol létre szeretné hozni az alkalmazást. A nyilvános előzetes verzióban az **előzetes verziójú alkalmazások** egyetlen elérhető régiói **Észak-Európa** és az **USA középső**régiója.

      További információk a díjszabásról az [Azure IoT Central díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/iot-central/) találhatók.

1. Válasszon egy rövid alkalmazásnevet, például a **Contoso IoT** nevet. Az Azure IoT Central létrehoz egy egyéni URL-előtagot. Ezt az URL-előtagot egy könnyebben megjegyezhető előtagra módosíthatja.

1. Válassza ki az **előnézeti alkalmazás** sablonját. Az alkalmazássablon előre megadott elemeket, például eszközsablonokat és irányítópultokat tartalmazhat, amelyek segítséget nyújtanak az első lépésekhez.

1. A lap alján kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy IoT Central alkalmazást, amely az előzetes verziójú funkciókat használja. A javasolt következő lépés:

> [!div class="nextstepaction"]
> [Új eszköz típusának meghatározása az Azure IoT Central alkalmazásban](./tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
