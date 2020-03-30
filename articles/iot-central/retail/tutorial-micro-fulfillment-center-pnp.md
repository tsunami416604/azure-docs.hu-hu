---
title: Micro-fulfillment center app sablon bemutató | Microsoft dokumentumok
description: Oktatóanyag az Azure IoT Central mikro-teljesítési központ alkalmazássablonjáról
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 01/09/2020
ms.openlocfilehash: 93906f582f1edc351088f8b4c453bf9ebda54f83
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77369821"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Oktatóanyag: A mikro-teljesítési központ alkalmazássablon telepítése és végigvezeti

Ebben az oktatóanyagban az Azure IoT Central mikro-teljesítési központ alkalmazássablon használatával kiskereskedelmi megoldást hozhat létre. Megtudhatja, hogyan telepítheti a sablont, mi szerepel benne, és mit szeretne tenni a következő lépésekkel.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag-sorozat befejezéséhez azure-előfizetésre van szükség. Az ingyenes 7 napos próbaverziót igény szerint használhatja. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs oldalán.](https://aka.ms/createazuresubscription)

## <a name="create-an-application"></a>Alkalmazás létrehozása 
Ebben a szakaszban hozzon létre egy új Azure IoT Central-alkalmazást egy sablonból. Ezt az alkalmazást fogja használni az oktatóanyag-sorozatban a teljes megoldás létrehozásához.

Új Azure IoT Central-alkalmazás létrehozása:

1. Nyissa meg az [Azure IoT Central alkalmazáskezelő](https://aka.ms/iotcentral) webhelyét.
1. Ha rendelkezik Azure-előfizetéssel, jelentkezzen be a hozzáféréshez használt hitelesítő adatokkal. Ellenkező esetben jelentkezzen be Microsoft-fiókkal:

   ![Képernyőkép a Microsoft-fiókbejelentkezés immára](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. Egy új Azure IoT Central-alkalmazás létrehozásának megkezdéséhez válassza az **Új alkalmazás** lehetőséget.

1. Válassza a **Kiskereskedelem**lehetőséget.  A kiskereskedelmi oldalon több kiskereskedelmi alkalmazássablon jelenik meg.

Új mikro-teljesítési központ alkalmazás létrehozása, amely előnézeti funkciókat használ:  
1. Válassza ki a **Mikro-teljesítési központ** alkalmazássablont. Ez a sablon eszközsablonokat tartalmaz az oktatóanyagban használt összes eszközhöz. A sablon egy kezelői irányítópultot is biztosít a teljesítési központon belüli monitorozási feltételekhez, valamint a robotszolgáltatók feltételeihez. 

    ![Képernyőkép az Azure IoT Centralról az IoT-alkalmazás lap létrehozása](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-homepage-mfc.png)
    
1. Tetszés szerint válasszon egy rövid **alkalmazásnevet**. Az alkalmazás sablon alapja a fiktív cég Northwind kereskedők. 

    >[!NOTE]
    >Ha rövid alkalmazásnevet használ, akkor is egyedi értéket kell használnia az alkalmazás URL-címéhez.

1. Ha Rendelkezik Azure-előfizetéssel, adja meg a címtárat, az Azure-előfizetést és a régiót. Ha nem rendelkezik előfizetéssel, engedélyezheti a 7 napos ingyenes próbaverziót, és kivégezheti a szükséges kapcsolattartási adatokat.  

    A könyvtárakról és az előfizetésekről további információt az [Alkalmazás létrehozása](../preview/quick-deploy-iot-central.md) rövid útmutató című témakörben talál.

1. Kattintson a **Létrehozás** gombra.

    ![Képernyőkép az Azure IoT Central Új alkalmazáslapjáról](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>Az alkalmazás végigjárása 

Az alkalmazássablon sikeres üzembe helyezése után megjelenik a **Northwind Traders mikro-teljesítési központ irányítópultja.** A Northwind Traders egy fiktív kiskereskedő, amelynek mikro-teljesítési központja van ebben az Azure IoT Central alkalmazásban. Ezen az operátori irányítópulton a sablonban található eszközökre vonatkozó információk és telemetriai adatok, valamint a végrehajtható parancsok, feladatok és műveletek láthatók. Az irányítópult logikusan két részre van osztva. A bal oldalon figyelemmel kísérheti a környezeti feltételeket a teljesítési struktúrán belül, és a jobb oldalon figyelemmel kísérheti egy robothordozó állapotát a létesítményen belül.  

Az irányítópultról a következőket teheti:
   * Tekintse meg az eszköz telemetriai adatai, például a kiválasztások száma, a feldolgozott rendelések száma és a tulajdonságok, például a struktúrarendszer állapota.  
   * Tekintse meg a robothordozók alaprajzát és helyét a teljesítési struktúrán belül.
   * Kiváltó parancsok, például a vezérlőrendszer alaphelyzetbe állítása, a szolgáltató belső vezérlőprogramjának frissítése és a hálózat újrakonfigurálása.

     ![Képernyőkép a Northwind Traders mikrobeteljesülési központ irányítópultjáról](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard1.png)
   * Tekintsen meg egy példát az irányítópultra, amelyet az operátor a teljesítési központon belüli feltételek figyelésére használhat. 
   * Az átjáróeszközön a teljesítési központban futó hasznos terhelések állapotának figyelése.    

     ![Képernyőkép a Northwind Traders mikrobeteljesülési központ irányítópultjáról](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard2.png)

## <a name="device-template"></a>Eszközsablon
Ha az eszközsablonok lapot választja, láthatja, hogy a sablon két különböző eszköztípus része: 
   * **Robotkaroshordozó**: Ez az eszközsablon egy működő robothordozó definícióját jelöli, amely a teljesítési struktúrában lett telepítve, és megfelelő tárolási és lekérési műveleteket hajt végre. Ha kiválasztja a sablont, láthatja, hogy a robot eszközadatokat küld, például a hőmérsékletet és a tengely pozíciót, valamint az olyan tulajdonságokat, mint például a robothordozó állapota. 
   * **Struktúraállapot-figyelés:** Ez az eszközsablon egy eszközgyűjteményt jelöl, amely lehetővé teszi a környezeti feltételek figyelését, valamint a különböző peremhálózati számítási feladatokat tároló átjáróeszközt a teljesítési központ működtetéséhez. Az eszköz telemetriai adatokat küld, például a hőmérsékletet, a kiválasztások számát és a rendelések számát. Emellett a környezetben futó számítási számítási feladatok állapotát és állapotát is elküldi. 

     ![Mikroteljesítési központ eszközsablonjai](./media/tutorial-micro-fulfillment-center-app-pnp/device-templates.png)

Ha az eszközcsoportok lapot választja, azt is láthatja, hogy ezek az eszközsablonok automatikusan eszközcsoportokat hoznak létre számukra.

## <a name="rules"></a>Szabályok
A **Szabályok** lapon egy mintaszabály jelenik meg, amely az alkalmazássablonban található a robotkaros hordozó hőmérsékleti feltételeinek figyelésére. Ezzel a szabállyal figyelmeztetheti az üzemeltetőt, ha a létesítményben egy adott robot túlmelegszik, és a szervizeléshez offline állapotba kell helyezni. 

A mintaszabály segítségével határozza meg az üzleti funkcióknak megfelelő szabályokat.

![Képernyőkép a Szabályok lapról](./media/tutorial-micro-fulfillment-center-app-pnp/rules.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem fogja tovább használni ezt az alkalmazást, törölje az alkalmazássablont. Nyissa meg a **Felügyeleti** > **alkalmazás beállításai**t, és válassza a **Törlés**lehetőséget.

![Képernyőkép a Mikroteljesítési központ alkalmazásbeállítások lapjáról](./media/tutorial-micro-fulfillment-center-app-pnp/delete.png)

## <a name="next-steps"></a>További lépések
* További információ a [mikro-teljesítési központ megoldásarchitektúrájáról.](./architecture-micro-fulfillment-center-pnp.md)
* További információ az [Azure IoT Central egyéb kiskereskedelmi sablonjairól.](./overview-iot-central-retail-pnp.md)
* Olvassa el az [Azure IoT Central áttekintését.](../preview/overview-iot-central.md)
