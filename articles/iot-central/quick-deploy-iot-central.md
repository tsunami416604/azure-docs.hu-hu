---
title: Azure IoT Central-alkalmazás létrehozása | Microsoft Docs
description: Új Azure IoT Central-alkalmazás létrehozása. Próbaalkalmazás vagy használatalapú fizetéses alkalmazás létrehozása alkalmazássablonnal.
author: viv-liu
ms.author: viviali
ms.date: 10/31/2018
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: f0e5b9d6e873cad1a997bda2ee286c92ad3818d3
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959460"
---
# <a name="create-an-azure-iot-central-application"></a>Azure IoT Central-alkalmazás létrehozása

_Szerkesztőként_ az Azure IoT Central használatával határozza meg a Microsoft Azure IoT Central-alkalmazást. Ez a rövid útmutató a bemutatja, hogyan hozhat létre egy _eszközsablonmintát_ és szimulált _eszközöket_ tartalmazó Azure IoT Central-alkalmazást.

Lépjen az Azure IoT Central [Alkalmazáskezelő](https://aka.ms/iotcentral) oldalára. Be kell jelentkeznie személyes, munkahelyi vagy iskolai Microsoft-fiókjával.

Egy új Azure IoT Central-alkalmazás létrehozásának megkezdéséhez válassza az **Új alkalmazás** lehetőséget. Ekkor megjelenik az **Alkalmazás létrehozása** oldal.

![Azure IoT Central Alkalmazás létrehozása oldal](media/quick-deploy-iot-central/iotcentralcreate.png)

Új Azure IoT Central-alkalmazás létrehozása:

1. Válassza ki a fizetési konstrukciót:
    - A **próbaalkalmazások** 7 napig ingyenesek, azután lejárnak. A lejárat előtt bármikor átalakíthatók használatalapú fizetéses alkalmazássá.
    - A **használatalapú fizetéses** alkalmazások díjának felszámítása eszközönként történik. Az első 5 eszköz ingyenes.

    További információk a díjszabásról az [Azure IoT Central díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/iot-central/) találhatók.

1. Válasszon egy rövid alkalmazásnevet, például a **Contoso IoT** nevet. Az Azure IoT Central létrehoz egy egyéni URL-előtagot. Ezt az URL-előtagot egy könnyebben megjegyezhető előtagra módosíthatja.

1. Válasszon ki egy alkalmazássablont. Az alkalmazássablon előre megadott elemeket, például eszközsablonokat és irányítópultokat tartalmazhat, amelyek segítséget nyújtanak az első lépésekhez.
    | Alkalmazássablon | Leírás |
    | -------------------- | ----------- |
    | Minta: Contoso       | Létrehoz egy alkalmazást, amely már tartalmaz egy eszközsablont egy hűtött eladóautomatához. Használja ezt a sablont az Azure IoT Central megismerésének megkezdéséhez. |
    | Minta: Devkits       | Létrehoz egy eszközsablonokat tartalmazó alkalmazást, amelyekkel csatlakoztathat egy MXChip vagy Raspberry Pi eszközt. Ha Ön eszközfejlesztő, aki ezekkel az eszközökkel kísérletezik, használja ezt a sablont. |
    | Egyéni alkalmazás   | Létrehoz egy üres alkalmazást, amelybe a saját eszközsablonjait és eszközeit helyezheti el. |

1. Ha **használatalapú fizetéses** alkalmazást hoz létre, ki kell választania a *címtárat*, az *Azure-előfizetést* és a *régiót*. 
    - A *címtár* az az Azure Active Directory, amelyben az alkalmazás létrejön. Felhasználói identitásokat, hitelesítő adatokat és egyéb szervezeti információkat tartalmaz. Ha még nem rendelkezik AAD-vel, létrehozunk egyet Önnek az Azure-előfizetés létrehozásakor.

    - Az *Azure-előfizetéssel* Azure-szolgáltatások példányait hozhatja létre. Az előfizetésben lévő erőforrásokat az IoT Central osztja ki. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs lapján](https://aka.ms/createazuresubscription). Az Azure-előfizetés létrehozása után lépjen vissza az **alkalmazás-létrehozási** lapra. Az új előfizetése megjelenik az **Azure-előfizetések** legördülő menüjében.

    - A *régió* az a fizikai hely, ahol létre szeretné hozni az alkalmazást. Általában olyan régiót érdemes választani, amely fizikailag a legközelebb található az eszközökhöz, hogy azok optimális teljesítményt nyújtsanak. Azon régiók listája, amelyekben elérhető az Azure IoT Central a [Régiónként elérhető termékek](https://azure.microsoft.com/regions/services/) oldalon található.

    > [!Note]
    > Ha kiválasztott egy régiót, később nem helyezheti át az alkalmazást egy másik régióba.

1. Kattintson a **Create** (Létrehozás) gombra.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy IoT Central-alkalmazást. A következő javasolt lépés:

> [!div class="nextstepaction"]
> [Ismerkedés az IoT Centrallal](#overview-iot-central-tour)
