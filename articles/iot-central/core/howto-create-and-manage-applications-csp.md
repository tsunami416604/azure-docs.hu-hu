---
title: Azure IoT Central-alkalmazások létrehozása és kezelése a CSP-portálról | Microsoft Docs
description: CSP-ként, hogyan lehet Azure IoT Central alkalmazást létrehozni az ügyfél nevében.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 40c5f612b5b1571bb3d39f452d64a7005701f7c1
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023803"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Azure IoT Central-alkalmazás létrehozása és kezelése a CSP-portálról

A Microsoft Cloud Solution Provider (CSP) program egy Microsoft viszonteladói program. Célja, hogy a Microsoft kereskedelmi online szolgáltatásainak viszonteladása érdekében egy egyablakos program keretében biztosítjuk a Channel partnereit. További információ a [Cloud Solution Provider programról](https://partner.microsoft.com/cloud-solution-provider).

CSP-ként létrehozhat és kezelhet Microsoft Azure IoT Central-alkalmazásokat az ügyfelek nevében a [Microsoft partner Center](https://partnercenter.microsoft.com/partner/home)használatával. Ha az Azure IoT Central-alkalmazásokat a kriptográfiai szolgáltatók nevében hozza létre, ugyanúgy, mint más, a CSP által felügyelt Azure-szolgáltatásokhoz, a kriptográfiai szolgáltató kezeli az ügyfelek számlázását. Az Azure IoT Central díját a Microsoft partner Center teljes számláján fogja megjelenni.

Első lépésként jelentkezzen be a fiókjába a Microsoft partner portálon, és válassza ki azt az ügyfelet, akivel Azure IoT Central alkalmazást szeretne létrehozni. A bal oldali NAV szolgáltatásban navigáljon az ügyfélhez.

![Microsoft partner Center, ügyfél nézet](media/howto-create-and-manage-applications-csp/image1.png)

Az Azure IoT Central a felügyelethez elérhető szolgáltatásként van felsorolva. Válassza ki az Azure IoT Central hivatkozást az oldalon új alkalmazások létrehozásához vagy meglévő alkalmazások kezeléséhez ehhez az ügyfélhez.

![Az Azure IoT Central kezelhető](media/howto-create-and-manage-applications-csp/image2.png)

Az Azure IoT Central Application Manager oldalán landol. Az Azure IoT Central a Microsoft partner központjából származó kontextust tartja, és az adott ügyfelet felügyelheti. Ezt a nyugtát az Application Manager oldal fejlécében tekintheti meg. Innen olyan meglévő alkalmazást is megnyithat, amelyet korábban hozott létre az ügyfél számára, hogy kezelje az ügyfelet, vagy új alkalmazást hozzon létre az ügyfél számára.

![Kezelő létrehozása a CSP-hez](media/howto-create-and-manage-applications-csp/image3.png)

Azure IoT Central-alkalmazás létrehozásához kattintson a bal oldali menüben a **Létrehozás** elemre. Válasszon az egyik iparági sablon közül, vagy válassza az **örökölt alkalmazás** lehetőséget egy új alkalmazás létrehozásához. Ekkor betöltődik az alkalmazás-létrehozási oldal. Az összes mezőt el kell végeznie ezen a lapon, majd a **Létrehozás**lehetőséget kell választania. További információkat az alábbi mezőkben talál.

![Alkalmazás-oldal létrehozása a kriptográfiai szolgáltatásokhoz](media/howto-create-and-manage-applications-csp/image4.png)

![Alkalmazás-oldal létrehozása a kriptográfiai szolgáltatásokhoz](media/howto-create-and-manage-applications-csp/image4-1.png)

![Alkalmazás-oldal létrehozása a CSP számlázási adataihoz](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>Díjszabási csomag

A standard díjszabási csomagot használó alkalmazásokat csak CSP-ként lehet létrehozni. Ahhoz, hogy az Azure IoT Central bemutassa az ügyfelet, létrehozhat egy alkalmazást, amely az ingyenes díjszabási csomagot külön használja. Az ingyenes és standard díjszabási csomagokról az [Azure IoT Central díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/iot-central/)tájékozódhat.

A standard díjszabási csomagot használó alkalmazásokat csak CSP-ként lehet létrehozni. Ahhoz, hogy az Azure IoT Central bemutassa az ügyfelet, létrehozhat egy alkalmazást, amely az ingyenes díjszabási csomagot külön használja. Az ingyenes és standard díjszabási csomagokról az [Azure IoT Central díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/iot-central/)tájékozódhat.

## <a name="application-name"></a>Alkalmazásnév

Az alkalmazás neve megjelenik az **Application Manager** lapon és az egyes Azure IoT Central-alkalmazásokon belül. Bármelyik nevet kiválaszthatja az Azure IoT Central alkalmazás számára. Válasszon egy olyan nevet, amely az Ön és a szervezet többi tagja számára is logikus.

## <a name="application-url"></a>Alkalmazás URL-címe

Az alkalmazás URL-címe az alkalmazásra mutató hivatkozás. Menthet egy könyvjelzőt a böngészőjében, vagy megoszthatja másokkal.

Az alkalmazás nevének megadásakor az alkalmazás URL-címe automatikusan létrejön. Ha szeretné, választhat egy másik URL-címet is az alkalmazáshoz. Minden egyes Azure-IoT Central URL-címnek egyedinek kell lennie az Azure IoT Centralon belül. Hibaüzenet jelenik meg, ha a választott URL-cím már használatban van.

## <a name="directory"></a>Könyvtár

Mivel az Azure IoT Central környezetében a Microsoft partner portálon kiválasztott ügyfél felügyelhető, csak az adott ügyfél Azure Active Directory bérlője jelenik meg a címtár mezőben. 

Az Azure Active Directory bérlő felhasználói identitásokat, hitelesítő adatokat és egyéb szervezeti adatokat tartalmaz. Több Azure-előfizetés is társítható egyetlen Azure Active Directory Bérlővel.

További információ: [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Azure-előfizetés

Az Azure-előfizetések lehetővé teszik az Azure-szolgáltatások példányainak létrehozását. Az Azure IoT Central automatikusan megkeresi az ügyfél összes olyan Azure-előfizetését, amelyhez hozzáférése van, és megjeleníti őket az **alkalmazás létrehozása** lap legördülő menüjében. Válasszon egy Azure-előfizetést új Azure IoT Central-alkalmazás létrehozásához.

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet a Microsoft partner Centerben. Az Azure-előfizetés létrehozása után lépjen vissza az **alkalmazás-létrehozási** lapra. Az új előfizetése megjelenik az **Azure-előfizetések** legördülő menüjében.

További információt az Azure- [előfizetések](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)című témakörben talál.

## <a name="region"></a>Region (Régió)

Válassza ki azt a régiót vagy [földrajzot](https://azure.microsoft.com/global-infrastructure/geographies/) , ahol létre szeretné hozni az Azure IoT Central alkalmazást. Az optimális teljesítmény érdekében általában az eszközökhöz legközelebb eső régiót kell kiválasztania.

További információ: Azure- [régiók](https://azure.microsoft.com/global-infrastructure/regions/) és [Azure földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/)területek.

Megtekintheti azokat a régiókat, amelyekben az Azure IoT Central elérhető a régiók oldalon [elérhető termékek területen](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central) .

> [!Note]
> Ha kiválasztott egy régiót, később nem helyezheti át az alkalmazást egy másik régióba.

## <a name="application-template"></a>Alkalmazássablon

Válassza ki az alkalmazáshoz használni kívánt sablont.


## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan hozhat létre egy Azure IoT Central alkalmazást CSP-ként, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Az alkalmazás felügyelete](howto-administer.md)
