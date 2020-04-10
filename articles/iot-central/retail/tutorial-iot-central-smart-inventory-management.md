---
title: Az IoT Smart készletgazdálkodásának oktatóanyaga | Microsoft dokumentumok
description: Intelligens készletkezelési alkalmazássablon oktatóanyaga az IoT Central hoz
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 430f477422e040e0e7e28fd69a1cbc18bce7d656
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000545"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Oktatóanyag: Intelligens készletkezelési alkalmazássablon telepítése és végigvezeti azt.



Ez az oktatóanyag bemutatja, hogyan kezdheti el az IoT Central **intelligens készletfelügyeleti** alkalmazássablon üzembe helyezését. Megtudhatja, hogyan telepítheti a sablont, mi szerepel a dobozon kívül, és mit érdemes elvégezni.

Ebben az oktatóanyagban megtudhatja, hogyan kell, 
* intelligens készletkezelő alkalmazás létrehozása 
* végigaz alkalmazás 

## <a name="prerequisites"></a>Előfeltételek

* Az alkalmazás telepítéséhez nincs szükség konkrét előfeltételekre
* Ajánlott Azure-előfizetés, de akár anélkül is próbálkozhat

## <a name="create-smart-inventory-management-application-template"></a>Intelligens készletkezelési alkalmazássablon létrehozása

Az alkalmazást a következő lépésekkel hozhatja létre

1. Keresse meg az Azure IoT Central alkalmazáskezelő webhelyét. Válassza a bal oldali navigációs sáv **építés gombját,** majd kattintson a **Kiskereskedelem** fülre.

    > [!div class="mx-imgBorder"]
    > ![Intelligens készletkezelési irányítópult](./media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png)

2. Válassza a **Kiskereskedelmi** lap lehetőséget, és válassza az **Alkalmazás létrehozása** lehetőséget az **intelligens készletkezelés** csoportban.

3. **Az alkalmazás létrehozása** megnyílik az Új jelentkezési lap, és az alábbiak szerint töltse ki a kért adatokat.
   **Alkalmazásneve**: használhatja az alapértelmezett javasolt nevet, vagy megadhatja a rövid alkalmazás nevét.
   **URL**: használhatja a javasolt alapértelmezett URL-t, vagy megadhatja a barátságos, egyedi emlékezetes URL-t. Ezután az alapértelmezett beállítás ajánlott, ha már rendelkezik egy Azure-előfizetéssel. Tudod elkezd -val 7- nap szabad próba- árkialakítás tervez és választ -hoz megtérít -hoz egy mértékadó árkialakítás tervez bármikor előtt a szabad nyom lejár.
   **Számlázási adatok:** A címtár, az Azure-előfizetés és a régió adatait az erőforrások kiépítése szükséges.
   **Létrehozás:** Válassza a lap alján található létrehozás lehetőséget az alkalmazás üzembe helyezéséhez.

    > [!div class="mx-imgBorder"]
    > ![Intelligens készletkezelési irányítópult](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png)

    > [!div class="mx-imgBorder"]
    > ![Intelligens készletkezelés számlázási adatai](./media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>Az alkalmazás végigjárása 

### <a name="dashboard"></a>Irányítópult 

Az alkalmazássablon sikeres üzembe helyezése után az alapértelmezett irányítópult egy intelligens készletkezelési operátorra fókuszált portál. A Northwind Trader egy fiktív intelligens készletszolgáltató, amely alacsony bluetoothos (BLE) és rádiófrekvenciás azonosítással (RFID) rendelkező kiskereskedelmi üzletet kezel. Ezen az irányítópulton két különböző átjáró telemetriai adatokat, valamint a kapcsolódó parancsokat, feladatokat és műveleteket biztosít. Ez az irányítópult előre konfigurálva van, hogy bemutassa a kritikus intelligens készletkezelő eszköz műveletek tevékenységét.
Az irányítópult logikailag két különböző átjáróeszköz-kezelési művelet között oszlik meg, 
   * A raktár egy rögzített BLE átjáróval van telepítve, & BLE-címkéket helyeznek el a raklapokon, hogy nyomon kövessék & nyomon követési készletet egy nagyobb létesítményben
   * A kiskereskedelmi üzlet egy rögzített RFID átjáróval van megvalósítva, & RFID-címkéket egyedi tételszinten, hogy nyomon kövessék és nyomon követhessék az állományt egy raktárban
   * Az átjáró helyének, állapotának & kapcsolódó részleteinek megtekintése 

> [!div class="mx-imgBorder"]
> ![Intelligens készletkezelési irányítópult](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * Az átjárók, az aktív és az ismeretlen címkék teljes számát könnyedén nyomon követheti.
   * Eszközkezelési műveleteket hajthat végre, például a firmware frissítését, az érzékelő letiltását, az érzékelő engedélyezését, az érzékelő küszöbértékének frissítését, a telemetriai időközök frissítését & eszközszerviz-szerződések frissítését
   * Az átjáróeszközök teljes vagy növekményes vizsgálattal hajthatnak végre igény szerinti készletkezelést.

> [!div class="mx-imgBorder"]
> ![Intelligens készletkezelési irányítópult](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Eszközsablon
Kattintson az Eszközsablonok fülre, és látni fogja az átjáró képességmodelljét. A képességmodell két különböző felület köré **épül, az átjárótelemetria& tulajdonság** és **az átjáróparancsok köré épül**

**Átjáró telemetriai & tulajdonság** – Ez a felület az érzékelőkhöz, helyadatokhoz, eszközadatokhoz és az ikereszköz-tulajdonságképességekhez kapcsolódó összes telemetriai adatot, például az átjáróküszöbértékeket és a frissítési időközöket jelöli.

> [!div class="mx-imgBorder"]
> ![Intelligens készletkezelési irányítópult](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Átjáróparancsok** – Ez a felület rendezi az összes átjáróparancs-képességet

> [!div class="mx-imgBorder"]
> ![Intelligens készletkezelési irányítópult](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Szabályok
Válassza ki a szabályok lapot az alkalmazássablonban található két különböző szabály megtekintéséhez. Ezek a szabályok úgy vannak beállítva, hogy további vizsgálatok céljából e-mailben küldjék el az értesítéseket az operátoroknak.

**Átjáró offline :** Ez a szabály aktiválódik, ha az átjáró hosszabb ideig nem jelent a felhőnek. Az átjáró nem válaszol, mert az alacsony töltöttségi mód, a kapcsolat elvesztése, az eszköz állapota.

**Ismeretlen címkék:** Fontos, hogy nyomon kövesse az összes RFID & BLE-címkék társított egy eszköz. Ha az átjáró túl sok ismeretlen címkét észlel, az a címkebeszerzési alkalmazásokkal kapcsolatos szinkronizálási kihívások jele.

> [!div class="mx-imgBorder"]
> ![Intelligens készletkezelési irányítópult](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>Feladatok
A feladatok lapon öt különböző feladat jelenik meg, amelyek az alkalmazássablon részeként léteznek: A feladatok funkcióval egész megoldásszintű műveleteket hajthat végre. Itt a készletkezelési feladatok az eszközparancsokat és az ikerképességeket használják olyan feladatok elvégzésére, mint például:
   * letiltja az olvasókat az összes átjárón
   * a telemetriai küszöbérték módosítása a 
   * igény szerinti készletszkennelést végezhet a teljes megoldásban.

> [!div class="mx-imgBorder"]
> ![Intelligens készletkezelési irányítópult](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem fogja tovább használni ezt az alkalmazást, törölje az alkalmazássablont a **Felügyeleti** > **alkalmazás beállításai** hivatkozással, és kattintson a Törlés **gombra.**

> [!div class="mx-imgBorder"]
> ![Intelligens készletkezelési irányítópult](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>További lépések
* További információ az intelligens készletkezelés [intelligens készletkezelési koncepciójáról](./architecture-smart-inventory-management.md)
* További információ az [IoT Central egyéb kiskereskedelmi sablonjairól](./overview-iot-central-retail.md)
* Az IoT Centralról további információ az [IoT Central áttekintése című témakörben található.](../core/overview-iot-central.md)
