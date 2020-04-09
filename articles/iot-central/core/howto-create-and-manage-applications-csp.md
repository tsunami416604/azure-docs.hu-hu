---
title: Azure IoT Central-alkalmazások létrehozása és kezelése a kriptaportálról | Microsoft dokumentumok
description: KRipti-szolgáltatóként hogyan hozhat létre egy Azure IoT Central-alkalmazást az ügyfél nevében.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 02481d5dcbaba15c9b17a27348207d9af64f3355
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982038"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Azure IoT Central alkalmazás létrehozása és kezelése a CSP-portálról

A Microsoft Cloud Solution Provider (CSP) program a Microsoft viszonteladói programja. Célja, hogy csatornapartnereinknek egyablakos programot biztosítson a Microsoft kereskedelmi online szolgáltatásainak viszonteladására. További információ a [Felhőszolgáltató programról.](https://partner.microsoft.com/cloud-solution-provider)

KRIPtaként a Microsoft Partner [Center](https://partnercenter.microsoft.com/partner/home)segítségével hozhat létre és kezelhet Microsoft Azure IoT Central alkalmazásokat ügyfelei nevében. Amikor az Azure IoT Central-alkalmazásokat az ügyfelek nevében a kript.c.-k hoznak létre, csakúgy, mint más kripta-felügyelt Azure-szolgáltatások, a kripta-k kezelik az ügyfelek számlázását. Az Azure IoT Central díja megjelenik a teljes számlán a Microsoft Partner Centerben.

Első lépésekhez jelentkezzen be fiókjába a Microsoft Partner Portalon, és válasszon ki egy ügyfelet, akinek létre szeretne hozni egy Azure IoT Central alkalmazást. Keresse meg az ügyfél szolgáltatáskezelése szolgáltatáskezelő eszközét a bal oldali navigációs eszközről.

![Microsoft PartnerCenter, ügyfélnézet](media/howto-create-and-manage-applications-csp/image1.png)

Az Azure IoT Central felügyeleti szolgáltatásként szerepel. Válassza ki az Azure IoT Central hivatkozást a lapon új alkalmazások létrehozásához vagy az ügyfél meglévő alkalmazásai kezeléséhez.

![Az Azure IoT Central kezelhető](media/howto-create-and-manage-applications-csp/image2.png)

Az Azure IoT Central Application Manager lapon jelenik meg. Az Azure IoT Central megőrzi a microsoftos partnerközpontból származó környezetet, és azt, hogy ön az adott ügyfél kezelésére érkezett. Ezt az Alkalmazáskezelő lap fejlécében nyugtázta. Itt vagy egy korábban létrehozott alkalmazásra navigálhat, amelyet az ügyfél számára az ügyfél számára kezelt, vagy létrehozhat egy új alkalmazást az ügyfél számára.

![Menedzser létrehozása kripta-k számára](media/howto-create-and-manage-applications-csp/image3.png)

Azure IoT Central alkalmazás létrehozásához válassza a **Build parancsot** a bal oldali menüben. Válasszon egyet az iparági sablonok közül, vagy válassza az **Egyéni alkalmazás** lehetőséget egy teljesen új alkalmazás létrehozásához. Ezzel betölti az Alkalmazás-létrehozás lapot. Ki kell töltenie a lap összes mezőjét, majd a **Létrehozás gombot kell választania.** Az alábbi mezőkről további információt talál.

![Alkalmazáslap létrehozása kriptip-ek számára](media/howto-create-and-manage-applications-csp/image4.png)

![Alkalmazáslap létrehozása kriptip-ek számára](media/howto-create-and-manage-applications-csp/image4-1.png)

![Alkalmazáslap létrehozása a kriptip-ek számlázási adataihoz](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>Árképzési terv

Csak olyan alkalmazásokat hozhat létre, amelyek kriptaként szabványos díjszabási sémát használnak. Az Azure IoT Central bemutatása az ügyfél számára, létrehozhat egy alkalmazást, amely az ingyenes díjszabási csomagot külön-külön használja. Az ingyenes és standard díjszabási csomagokról az [Azure IoT Central díjszabási oldalán olvashat bővebben.](https://azure.microsoft.com/pricing/details/iot-central/)

Csak olyan alkalmazásokat hozhat létre, amelyek kriptaként szabványos díjszabási sémát használnak. Az Azure IoT Central bemutatása az ügyfél számára, létrehozhat egy alkalmazást, amely az ingyenes díjszabási csomagot külön-külön használja. Az ingyenes és standard díjszabási csomagokról az [Azure IoT Central díjszabási oldalán olvashat bővebben.](https://azure.microsoft.com/pricing/details/iot-central/)

## <a name="application-name"></a>Alkalmazásnév

Az alkalmazás neve megjelenik az **Application Manager** lapon és az egyes Azure IoT Central-alkalmazásokban. Az Azure IoT Central alkalmazáshoz tetszőleges nevet választhat. Olyan nevet válasszon, amely nek és a szervezet többi tagja számára is van értelme.

## <a name="application-url"></a>Alkalmazás URL-címe

Az alkalmazás URL-címe az alkalmazásra mutató hivatkozás. A könyvjelzőt mentheti a böngészőben, vagy megoszthatja másokkal.

Amikor megadja az alkalmazás nevét, az alkalmazás URL-címe automatikusan létrejön. Ha szeretné, választhat egy másik URL-címet az alkalmazáshoz. Minden Azure IoT Central URL-címnek egyedinek kell lennie az Azure IoT Central on belül. Hibaüzenet jelenik meg, ha a választott URL-cím már megtörtént.

## <a name="directory"></a>Címtár

Mivel az Azure IoT Central környezetben, hogy a Microsoft Partner Portal on kiválasztott ügyfél kezelésére érkezett, csak az Azure Active Directory-bérlő az adott ügyfél számára a Címtár mezőben jelenik meg. 

Az Azure Active Directory-bérlő felhasználói identitásokat, hitelesítő adatokat és egyéb szervezeti adatokat tartalmaz. Egyetlen Azure-előfizetésegyetlen Azure Active Directory-bérlőhöz társítható.

További információ: [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Azure-előfizetés

Az Azure-előfizetéssel Azure-szolgáltatások példányait hozhatja létre. Az Azure IoT Central automatikusan megkeresi annak az ügyfélnek az összes Azure-előfizetését, amelyhez hozzáférése van, és megjeleníti **azokat** egy legördülő menüben az Alkalmazás létrehozása lapon. Válasszon egy Azure-előfizetést egy új Azure IoT Central alkalmazás létrehozásához.

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet a Microsoft Partner Centerben. Az Azure-előfizetés létrehozása után lépjen vissza az **alkalmazás-létrehozási** lapra. Az új előfizetése megjelenik az **Azure-előfizetések** legördülő menüjében.

További információ: [Azure-előfizetések](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="location"></a>Hely

**A hely** az a [földrajzi hely,](https://azure.microsoft.com/global-infrastructure/geographies/) ahol létre szeretné hozni az alkalmazást. Az optimális teljesítmény érdekében általában azt a helyet kell kiválasztania, amely fizikailag a legközelebb van az eszközökhöz. Jelenleg létrehozhat egy IoT Central alkalmazást **Az Ausztrália,** **Ázsia csendes-óceáni,** **Európa,** **Egyesült Államok**, Egyesült **Királyság**és **Japán** földrajzi. Miután kiválasztotta a helyet, később nem helyezheti át az alkalmazást egy másik helyre.

## <a name="application-template"></a>Alkalmazássablon

Válassza ki az alkalmazáshoz használni kívánt alkalmazássablont.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan hozhat létre egy Azure IoT Central alkalmazást kriptaként, itt látható a következő javasolt lépés:

> [!div class="nextstepaction"]
> [Alkalmazás felügyelése](howto-administer.md)
