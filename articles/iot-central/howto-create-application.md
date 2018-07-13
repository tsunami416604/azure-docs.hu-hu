---
title: Azure IoT Central-alkalmazás létrehozása | Microsoft Docs
description: Egy rendszergazdaként hogyan hozhat létre az Azure IoT Central alkalmazáshoz.
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 07/09/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 1fbe3ea142e1dd738cd341f57d2b8f48b539ac75
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003766"
---
# <a name="create-your-azure-iot-central-application"></a>Azure IoT Central-alkalmazás létrehozása

A Microsoft Azure IoT központi alkalmazás létrehozása a [alkalmazás létrehozása](https://apps.microsoftiotcentral.com/create) lapot. Hozzon létre egy Azure IoT Central alkalmazáshoz, kell ezen a lapon az összes mezőt, és válassza a **létrehozás**. További információt az alábbi mezők mindegyike rendelkezik találja.

![Alkalmazás-lap létrehozása](media\howto-create-application\image1.png)

## <a name="payment-plan"></a>Fizetési lehetőség

Létrehozhat egy próba vagy a fizetős alkalmazásra. A próbaverziós és fizetős alkalmazások többet [Azure IoT Central díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/iot-central/)...

## <a name="application-name"></a>Alkalmazás neve

Az alkalmazás neve jelenik meg a **alkalmazáskezelő** lapot, és minden egyes Azure IoT Central alkalmazáshoz. Kiválaszthatja, hogy bármilyen nevet az Azure IoT Central alkalmazáshoz. Válasszon egy nevet, amely logikus Ön és mások számára a szervezetben.

## <a name="application-url"></a>Alkalmazás URL-címe

Az alkalmazás URL-je a hivatkozásra kattintva az alkalmazás. A böngészőben szeretné menteni a könyvjelzőt, vagy ossza meg másokkal.

Ad meg a nevet az alkalmazásnak, amikor az alkalmazás URL-címe, automatikusan létrehozott. Igény szerint választhat egy másik URL-címet az alkalmazás. Minden egyes Azure IoT Central URL-cím az Azure IoT Central belül egyedinek kell lennie. Akkor egy hibaüzenet jelenik meg, ha úgy dönt, az URL-cím már foglalt.

## <a name="directory"></a>Címtár

Csak a fizetős alkalmazások.

Válassza ki az Azure Active Directory-bérlő, hozhat létre Azure IoT Central-alkalmazást. Az Azure Active Directory-bérlő tartalmazza a felhasználói identitásokat, hitelesítő adatok és más szervezeti adatokat. Több Azure-előfizetéssel is társítható egy egyetlen Azure Active Directory-bérlő.

Az Azure Active Directory-bérlő nem rendelkezik, egy létrejön, amikor az Azure-előfizetés létrehozása.

További tudnivalókért lásd: [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Azure-előfizetés

Azure-előfizetés lehetővé teszi a példányok az Azure-szolgáltatások létrehozásához. Az Azure IoT Central automatikusan megkeresi a hozzáfér az összes Azure-előfizetések, és megjeleníti őket a legördülő listában a **alkalmazás létrehozása** lapot. Válassza ki az új Azure IoT központi alkalmazás létrehozása az Azure-előfizetés.

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet a a [oldala az Azure-előfizetési](https://aka.ms/createazuresubscription). Miután létrehozta az Azure-előfizetést, lépjen vissza a **alkalmazás létrehozása** lapot. Az új előfizetés megjelenik a **Azure-előfizetés** listából.

További tudnivalókért lásd: [Azure-előfizetések](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Régió

Csak a fizetős alkalmazások.

Válassza ki a régiót, ahol szeretné az Azure IoT központi alkalmazás létrehozása. Általában a fizikailag az optimális teljesítmény eléréséhez az eszközök legközelebb eső régiót kell választania.

További tudnivalókért lásd: [Azure-régiók](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#azure-regions).

Láthatja, hogy a régiók, amelyek az Azure IoT Central érhető el a [elérhető termékek régiók szerint](https://azure.microsoft.com/regions/services/) lapot.

> [!Note]
> Ha úgy dönt, a régiót, később nem lehet áthelyezni az alkalmazás egy másik régióba.

## <a name="application-template"></a>Alkalmazás sablon

A rendelkezésre álló sablonok közül választhat az új Azure IoT Central alkalmazáshoz. Alkalmazássablonok tartalmazhat például eszközsablonok előre meghatározott elemek, és az irányítópultok segítségével első lépései.

| Alkalmazás sablon | Leírás |
| -------------------- | ----------- |
| Egyéni alkalmazás   | Létrehoz egy üres alkalmazást, hogy adja meg a saját eszköz sablonokat és eszközöket. |
| Minta Contoso       | Létrehoz egy alkalmazást, amely tartalmaz egy egyszerű csatlakoztatott eszköz eszköz-sablont. Ez a sablon használatával első lépései az Azure IoT Central feltárása. |
| Minta Devkits       | Létrehoz egy alkalmazás eszközsablonok készen áll, hogy egy MXChip vagy a Raspberry Pi-eszköz kapcsolódjon. Sablon használata, ha az eszköz a kódra kísérletezés eszköz a fejlesztők. |

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan hozhat létre az Azure IoT Central alkalmazáshoz, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az alkalmazás felügyelete](howto-administer.md)