---
title: Alkalmazások létrehozásához és kezeléséhez az Azure IoT Central egy CSP-hez mint |} A Microsoft Docs
description: A kriptográfiai Szolgáltató, hogyan hozhat létre az ügyfél nevében egy Azure IoT Central alkalmazáshoz.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/03/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 8c73c55b059b4d3ca7136889122c11ab332b3f76
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270506"
---
# <a name="as-a-csp-create-and-manage-an-azure-iot-central-application-on-behalf-of-your-customer"></a>Egy CSP-hez hozzon létre, és az ügyfél nevében egy Azure IoT központi alkalmazás kezelése 

A Microsoft Cloud Solution Provider (CSP) program Microsoft Reseller is. A csatorna partnereivel, egyablakos program által az összes Microsoft kereskedelmi Online szolgáltatások, annak szándékát szolgál. Tudjon meg többet a [Cloud Solution Provider program](https://partner.microsoft.com/cloud-solution-provider).

Egy CSP-hez, létrehozása és kezelése a Microsoft Azure IoT Central alkalmazások keresztül az ügyfelek részére a [Microsoft Partner Centeren](https://partnercenter.microsoft.com/partner/home). Ha az Azure IoT Central alkalmazások ügyfeleik nevében által létrehozott CSP-k, csakúgy, mint más CSP által felügyelt Azure-szolgáltatások, a CSP-k kezelése az ügyfelek. Az Azure IoT Central díj a Microsoft Partner Centeren a számla összege jelenik meg.

Első lépésként jelentkezzen be a fiókját a Microsoft Partner portálra, és válassza ki egy ügyfelet, amely az Azure IoT központi alkalmazás létrehozása kívánja. Keresse meg a bal oldali navigációs ablaktáblán a Service management az ügyfél

![Microsoft Partner Centeren ügyfél megtekintése](media/howto-create-application-asCSP/image1.png)

Az Azure IoT Central felügyeletére a szolgáltatásként jelenik meg. Válassza ki az Azure IoT Central hivatkozása a lap új alkalmazások létrehozása vagy meglévő alkalmazások az ügyfélhez kezelése.

![Segítségével kezelheti az Azure IoT Central](media/howto-create-application-asCSP/image2.png)

Az Azure IoT központi alkalmazás-kezelő lap megjelenni. Az Azure IoT Central tartja a környezetet, hogy honnan származnak a Microsoft Partner Centeren és honnan vevőt kezeléséhez. Ez arra vonatkozik, az alkalmazás-kezelő lap fejlécében megjelenik. Itt akár navigálhat egy korábban az ügyfélhez kezeléséhez, vagy hozzon létre egy új alkalmazást az ügyfél korábban létrehozott meglévő alkalmazáshoz.

![Hozzon létre a CSP-kezelője](media/howto-create-application-asCSP/image3.png)

Hozzon létre egy Azure IoT Central alkalmazáshoz, jelölje be a **új alkalmazás** csempére. Ez az alkalmazás létrehozása oldal betöltődik. Kell ezen a lapon az összes mezőt, és válassza a **létrehozás**. További információ az egyes alábbi mezők találja.

![Hozzon létre az alkalmazás oldalán a CSP-k](media/howto-create-application-asCSP/image4.png)

![Hozzon létre az alkalmazás oldalán a CSP-k](media/howto-create-application-asCSP/image4-1.png)

## <a name="payment-plan"></a>Fizetési lehetőség

Csak egy CSP-hez mint használatalapú fizetés alkalmazásokat hozhat létre. Az ügyfelek az Azure IoT Central bemutatására külön létrehozhat egy próbaverziós alkalmazás. A próbaverzió utólagosan, és alkalmazások tájékozódhat a [Azure IoT Central díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Alkalmazásnév

Az alkalmazás neve jelenik meg a **alkalmazáskezelő** lapot, és minden egyes Azure IoT Central alkalmazáshoz. Kiválaszthatja, hogy bármilyen nevet az Azure IoT Central alkalmazáshoz. Válasszon egy nevet, amely logikus Ön és mások számára a szervezetben.

## <a name="application-url"></a>Alkalmazás URL-címe

Az alkalmazás URL-je a hivatkozásra kattintva az alkalmazás. A böngészőben szeretné menteni a könyvjelzőt, vagy ossza meg másokkal.

Adja meg a nevet az alkalmazásnak az alkalmazás URL-címe esetén automatikusan létrehozott. Igény szerint választhat egy másik URL-címet az alkalmazás. Minden egyes Azure IoT Central URL-cím az Azure IoT Central belül egyedinek kell lennie. Akkor egy hibaüzenet jelenik meg, ha úgy dönt, az URL-cím már foglalt.

## <a name="directory"></a>Címtár

Mivel az Azure IoT Central kezelheti a kiválasztott a Microsoft Partner portál ügyfél tartalomcsomagokból környezet rendelkezik, csak az Azure Active Directory-bérlő a Directory mezőben adott ügyfélhez tartozó láthatja. 

Az Azure Active Directory-bérlő tartalmazza a felhasználói identitásokat, hitelesítő adatok és más szervezeti adatokat. Több Azure-előfizetéssel is társítható egy egyetlen Azure Active Directory-bérlő.

További tudnivalókért lásd: [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Azure-előfizetés

Azure-előfizetés lehetővé teszi a példányok az Azure-szolgáltatások létrehozásához. Az Azure IoT Central automatikusan megkeresi az ügyfél, amely rendelkezik az összes Azure-előfizetések hozzáférést, és megjeleníti őket a legördülő listában a **alkalmazás létrehozása** lapot. Válassza ki az új Azure IoT központi alkalmazás létrehozása az Azure-előfizetés.

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet a Microsoft Partner Centeren. Az Azure-előfizetés létrehozása után lépjen vissza az **alkalmazás-létrehozási** lapra. Az új előfizetése megjelenik az **Azure-előfizetések** legördülő menüjében.

További tudnivalókért lásd: [Azure-előfizetések](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Régió

Válassza ki a régiót, ahol szeretné az Azure IoT központi alkalmazás létrehozása. Általában a fizikailag az optimális teljesítmény eléréséhez az eszközök legközelebb eső régiót kell választania.

További tudnivalókért lásd: [Azure-régiók](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#azure-regions).

Láthatja, hogy a régiók, amelyek az Azure IoT Central érhető el a [elérhető termékek régiók szerint](https://azure.microsoft.com/regions/services/) lapot.

> [!Note]
> Ha kiválasztott egy régiót, később nem helyezheti át az alkalmazást egy másik régióba.

## <a name="application-template"></a>Alkalmazássablon

A rendelkezésre álló sablonok közül választhat az új Azure IoT Central alkalmazáshoz. Az alkalmazássablon előre megadott elemeket, például eszközsablonokat és irányítópultokat tartalmazhat, amelyek segítséget nyújtanak az első lépésekhez.

| Alkalmazássablon | Leírás |
| -------------------- | ----------- |
| Egyéni alkalmazás   | Létrehoz egy üres alkalmazást, amelybe a saját eszközsablonjait és eszközeit helyezheti el. |
| Minta: Contoso       | Létrehoz egy alkalmazást, amely tartalmaz egy egyszerű csatlakoztatott eszköz eszköz-sablont. Használja ezt a sablont az Azure IoT Central megismerésének megkezdéséhez. |
| Minta: Devkits       | Létrehoz egy eszközsablonokat tartalmazó alkalmazást, amelyekkel csatlakoztathat egy MXChip vagy Raspberry Pi eszközt. Sablon használata, ha az eszköz a kódra kísérletezés eszköz a fejlesztők. |

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan hozhat létre egy Azure IoT Central alkalmazáshoz, mint egy CSP-hez, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Alkalmazás felügyelése](howto-administer.md)