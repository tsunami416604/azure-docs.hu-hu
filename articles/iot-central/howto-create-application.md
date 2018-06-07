---
title: Azure IoT központi alkalmazás létrehozása |} Microsoft Docs
description: Egy rendszergazdaként hogyan hozhat létre egy Azure IoT központi alkalmazást.
author: tbhagwat3
ms.author: tanmayb
ms.date: 03/20/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 39992a1cc36d00a64ee6430cad5f24af3e1e1157
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629286"
---
# <a name="create-your-azure-iot-central-application"></a>Az Azure IoT központi-alkalmazás létrehozása

A Microsoft Azure IoT központi alkalmazás létrehozása a [alkalmazás létrehozása](https://apps.microsoftiotcentral.com/create) lap. Azure IoT központi alkalmazás létrehozása, kell végrehajtani ezen az oldalon a mezőket, és válassza a **létrehozása**. Ez a cikk további információ az egyes mezőket tartalmaz.

![Alkalmazások lap létrehozása](media\howto-create-application\image1.png)

## <a name="payment-plan"></a>Támogatási terv

A próbaverzió vagy egy fizetős alkalmazást is létrehozhat. További információ a próba- és fizetős alkalmazások ezen a lapon.

## <a name="application-name"></a>Alkalmazás neve

Az alkalmazás neve jelenik meg a **alkalmazáskezelő** lap és minden Azure IoT központi alkalmazáson belül. Lehetősége van az Azure IoT központi alkalmazás bármely nevét. Válasszon egy nevet, amely Önnek és mások számára a szervezetében.

## <a name="application-url"></a>Alkalmazás URL-címe

Az alkalmazás URL-címe a hivatkozásra kattintva az alkalmazást. A böngészőben szeretné menteni a könyvjelzőt, vagy ossza meg másokkal.

Amikor az alkalmazás nevét, az alkalmazás URL-cím jön létre automatikusan. Tetszés szerint választhat egy másik URL-címet az alkalmazáshoz. Minden Azure IoT központi URL-Címének egyedinek kell lennie. Ha úgy dönt, az URL-cím már használatban van egy hibaüzenetet lát.

## <a name="directory"></a>Címtár

Csak a fizetős alkalmazások.

Válassza ki az Azure Active Directory-bérlő hozhat létre Azure IoT központi alkalmazást. Az Azure Active Directory-bérlő felhasználói identitások hitelesítő adatokat és más szervezeti adatait tartalmazza. Lehet, hogy több Azure-előfizetéssel társítva van egy egyetlen Azure Active Directory-bérlő.

Ha az Azure Active Directory-bérlő nem rendelkezik, egy jön létre az Ön Azure-előfizetés létrehozásakor.

További tudnivalókért lásd: [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Azure-előfizetés

Azure-előfizetés eszközzel hozhat létre Azure services példányát. Az Azure IoT központi automatikusan rendelkezik hozzáféréssel az összes Azure-előfizetések talál, és a legördülő listában megjeleníti azokat a **alkalmazás létrehozása** lap. Válassza ki az Azure-előfizetés hozhat létre egy új Azure IoT központi alkalmazást.

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy ezen a lapon. Miután létrehozta az Azure-előfizetés, lépjen vissza a **alkalmazás létrehozása** lap. Megjelenik az új előfizetés a **Azure-előfizetés** legördülő listán.

További tudnivalókért lásd: [Azure-előfizetések](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Régió

Csak a fizetős alkalmazások.

Válassza ki a régiót, hol szeretné létrehozni az Azure IoT központi alkalmazást. Válasszon általában a régió, amely fizikailag közelebb az eszközök az optimális teljesítmény eléréséhez.

További tudnivalókért lásd: [Azure-régiók](https://docs.microsoft.com/en-us/azure/guides/developer/azure-developer-guide#azure-regions).

Megtekintheti a régiókban, ahol Azure IoT központi érhető el a [régiónként rendelkezésre álló termékek](https://azure.microsoft.com/regions/services/) lap.

> [!Note]
> Ha úgy dönt, hogy a régió, később nem helyezhető az alkalmazás más régióban.

## <a name="application-template"></a>Alkalmazássablon

Az új Azure IoT központi alkalmazáshoz a rendelkezésre álló sablonok közül választhat. Alkalmazássablonok például az eszköz sablonok előre meghatározott elemeket tartalmazhat, és segítséget nyújtanak az irányítópultok első lépések:

| Alkalmazássablon | Leírás |
| -------------------- | ----------- |
| Egyéni alkalmazás   | Alkalmazást hoz létre üres ahhoz, hogy a saját eszköz sablonok és eszközök feltöltéséhez. |
| A minta Contoso       | Alkalmazást hoz létre, amely tartalmazza az eszköz sablont egy egyszerű csatlakoztatott eszközön. A sablon használatával megismerésére Azure IoT központi. |
| A minta Devkits       | Alkalmazást hoz létre az eszköz sablonok készen áll a egy MXChip vagy málna Pi eszköz csatlakozhat. Ha az eszköz a kód kísérletezés eszköz a fejlesztők a sablon használatához. |

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik megtudta, hogyan hozhat létre egy Azure IoT központi alkalmazást, ez a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az alkalmazás felügyelete](howto-administer.md)