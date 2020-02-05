---
title: A Micro-beteljesülő központ alkalmazás-sablonjának oktatóanyaga | Microsoft Docs
description: A IoT Centralhez készült Micro-beteljesülő központ alkalmazás sablonjának oktatóanyaga
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 01/09/2020
ms.openlocfilehash: 2ed6f37bc3b00397fa6331a501e1b16c8d622b5f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027789"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Oktatóanyag: üzembe helyezés és útmutató a Micro-teljesítési központ alkalmazás sablonja

Ebben az oktatóanyagban kihasználjuk az Azure IoT Central ***Micro-beteljesülő központ*** alkalmazás sablonját, amely bemutatja, hogyan hozhat létre kiskereskedelmi megoldást. Megtudhatja, hogyan helyezheti üzembe az MFC-sablont, mit tartalmaz a mező, és hogy mit szeretne tenni a következő lépésekkel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni: 
> [!div class="checklist"]
> * Kiskereskedelmi alkalmazás létrehozása az Azure IoT Central **Micro-beteljesülés központ** sablonnal
> * Az alkalmazás átjárása 

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag-Sorozat elvégzéséhez a következőkre lesz szüksége:
* Azure-előfizetés. Igény szerint ingyenes 7 napos próbaverziót is használhat. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs oldalán](https://aka.ms/createazuresubscription).

## <a name="create-an-application"></a>Alkalmazás létrehozása 
Ebben a szakaszban új Azure IoT Central alkalmazást hoz létre egy sablonból. Ezt az alkalmazást az oktatóanyag-sorozatban fogja használni teljes megoldás létrehozásához.

Új Azure IoT Central-alkalmazás létrehozása:

1. Navigáljon az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyére.
1. Ha rendelkezik Azure-előfizetéssel, jelentkezzen be az eléréséhez használt hitelesítő adatokkal, máskülönben jelentkezzen be Microsoft-fiók használatával:

   ![Lépjen a céges fiókjába](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. Egy új Azure IoT Central-alkalmazás létrehozásának megkezdéséhez válassza az **Új alkalmazás** lehetőséget.

1. Válassza a **kereskedelmi**lehetőséget.  A kiskereskedelmi oldalon számos kereskedelmi alkalmazás-sablon látható.

Az előzetes verziójú funkciókat használó új, Micro-beteljesülő központ alkalmazás létrehozása:  
1. Válassza ki a **Micro-beteljesülés központ** alkalmazás sablonját. Ez a sablon az oktatóanyagban használt összes eszközhöz tartalmaz sablonokat. A sablon egy operátori irányítópultot is biztosít a teljesítési központban található figyelési feltételekhez, valamint a robot-szolgáltatók feltételeit is. 

    > [!div class="mx-imgBorder"]
    > ![Micro-beteljesülés alkalmazás típusa](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-homepage-mfc.png)
    
1. Igény szerint válasszon egy felhasználóbarát **nevet**.  Az alkalmazás sablonja a kitalált vállalati Northwind kereskedőkön alapul. 

    > [!NOTE]
    > Ha felhasználóbarát **alkalmazás-nevet**használ, továbbra is egyedi értéket kell használnia az alkalmazás **URL-címéhez**.

1. Ha Azure-előfizetéssel rendelkezik, adja meg a *címtárat, az Azure-előfizetést és a régiót*. Ha nem rendelkezik előfizetéssel, engedélyezheti a **7 napos ingyenes próbaidőszakot** , és elvégezheti a szükséges kapcsolattartási adatokat.  

    A könyvtárakkal és előfizetésekkel kapcsolatban további információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](../preview/quick-deploy-iot-central.md).

1. Kattintson a **Létrehozás** gombra.

> [!div class="mx-imgBorder"]
> ![Micro-beteljesülés alkalmazás létrehozása](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>az alkalmazás végigvezeti 

### <a name="dashboard"></a>Irányítópult 

Az alkalmazás sablonjának sikeres üzembe helyezését követően először a **Northwind Traders Micro-beteljesülő központ irányítópultját kell kitöltenie**. A Northwind Trader olyan fiktív kiskereskedő, amely a IoT Central alkalmazásban felügyelt, Micro-teljesítési központtal rendelkezik. Ezen az operátor irányítópulton a sablonban található eszközök információit és telemetria láthatja, valamint parancsokat, feladatokat és műveleteket is végrehajthat. Az irányítópult logikailag két szakaszra oszlik (a bal és a jobb oldalon). A bal oldalon lehetősége van a környezeti feltételek figyelésére a teljesítési struktúrán belül, és a jobb oldalon nyomon követheti a robot-szolgáltatók állapotát a létesítményen belül.  

Az irányítópulton a következőket teheti:
   * Tekintse meg az eszköz telemetria, például a # of Picks, a feldolgozott megrendelések száma és a tulajdonságok (például a struktúrarendszer állapota stb.).  
   * Megtekintheti a beteljesülés struktúrán belüli robot-szolgáltatók **alapszintű tervét** és helyét.
   * Aktiváljon olyan parancsokat, mint például a vezérlőrendszer alaphelyzetbe állítása, a szolgáltató belső vezérlőprogram frissítése, a hálózat újrakonfigurálása stb.

> [!div class="mx-imgBorder"]
> ![Micro-teljesítési központ irányítópultja](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard1.png)
   * Tekintse meg az irányítópult azon példáját, amelyet az operátor felhasználhat a teljesítési központban lévő feltételek figyelésére. 
   * Figyelje az átjáró eszközön futó hasznos adatok állapotát a teljesítési központban.    

> [!div class="mx-imgBorder"]
> ![Micro-teljesítési központ irányítópultja](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard2.png)

## <a name="device-template"></a>Eszköz sablonja
Ha az eszközbeállítások lapra kattint, látni fogja, hogy a sablonhoz két különböző típusú eszköz tartozik: 
   * **Robot Carrier**: Ez a sablon a megfelelőségi struktúrában üzembe helyezett, működő robot-szolgáltató definícióját jelöli, és megfelelő tárolási és lekérési műveleteket végez. Ha a sablonra kattint, látni fogja, hogy a robot adatokat küld, például a hőmérsékletet, a tengely pozícióját és a tulajdonságokat, például a robot Carrier állapotát stb. 
   * **Struktúra állapotának figyelése**: ez az eszköz egy olyan gyűjteményt képvisel, amely lehetővé teszi a környezeti feltételek, valamint a különböző peremhálózati munkaterheléseket üzemeltető átjárók számára a teljesítési központ bekapcsolását. Az eszköz telemetria adatokat küld, például a hőmérsékletet, a kivételezések számát, a megrendelések számát, valamint a környezetben futó számítási feladatok állapotát és állapotát. 

> [!div class="mx-imgBorder"]
> ![Micro-beteljesülés Center-eszközök sablonjai](./media/tutorial-micro-fulfillment-center-app-pnp/device-templates.png)

Ha az eszközcsoport (eszközcsoport) lapra kattint, azt is láthatja, hogy ezekhez az eszközökhöz automatikusan vannak létrehozva az erőforráscsoportok.

## <a name="rules"></a>Szabályok
Amikor a szabályok lapra ugrik, megjelenik egy, az alkalmazás sablonjában található minta szabály, amely figyeli a robot-szolgáltató hőmérsékleti feltételeit. Ezt a szabályt használhatja a kezelő riasztására, ha a létesítmény egy adott robotja túlmelegszik, és offline állapotba kell helyezni a karbantartáshoz. 

Használja ki a minta szabályt úgy, hogy az üzleti funkciók számára megfelelőbb szabályokat határozzon meg.

   - **Robot Carrier túl meleg**: Ez a szabály akkor aktiválódik, ha a robot szolgáltató egy adott időszakban eléri a hőmérsékleti küszöbértéket. 

> [!div class="mx-imgBorder"]
> ![Micro-teljesítési központ szabályainak](./media/tutorial-micro-fulfillment-center-app-pnp/rules.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az alkalmazás sablonját az **adminisztráció** > **alkalmazás beállításai** között, és kattintson a **Törlés**gombra.

> [!div class="mx-imgBorder"]
> ![Micro-teljesítési központ alkalmazás-karbantartási](./media/tutorial-micro-fulfillment-center-app-pnp/delete.png)

## <a name="next-steps"></a>Következő lépések
* További információ a [Micro-teljesítési központ megoldási architektúráról](./architecture-micro-fulfillment-center-pnp.md)
* További információ a [IoT Central kiskereskedelmi sablonokról](./overview-iot-central-retail-pnp.md)
* További információ a IoT Centralról [IoT Central áttekintés](../preview/overview-iot-central.md)