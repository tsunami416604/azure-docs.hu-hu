---
title: Új átjáróeszköztípus definiálása az Azure IoT Centralban | Microsoft dokumentumok
description: Ez az oktatóanyag bemutatja, mint egy építő, hogyan definiálhat egy új IoT-átjáró eszköztípus az Azure IoT Central-alkalmazásban.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 2411eab50cc921a09ba55780b3c6620744a78f3f
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758124"
---
# <a name="define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>Új IoT-átjáróeszköztípus definiálása az Azure IoT Central alkalmazásban

*Ez a cikk a megoldáskészítőkre és az eszközfejlesztőkre vonatkozik.*

Ez az oktatóanyag bemutatja, mint a megoldás készítője, hogyan használhat egy átjáróeszköz-sablont egy átjáróeszköz definiálásához az IoT Central alkalmazásban. Ezután konfigurálja az IoT Central-alkalmazáshoz az átjáróeszközön keresztül csatlakozó alsóbb rétegbeli eszközöket. 

Ebben az oktatóanyagban egy **Smart Building** átjáróeszköz-sablont hoz létre. A **Smart Building** átjáróeszköz kapcsolatban áll más alsóbb rétegbeli eszközökkel.

![Az átjáróeszköz és az alsóbb rétegbeli eszközök közötti kapcsolat diagramja](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

Az átjáróeszközök az IoT Central alkalmazással való kommunikáció lehetővé tétele mellett a következőket is megtehetik:

* Küldje el a saját telemetriai adatokat, például a hőmérsékletet.
* Válaszoljon az operátor által készített írható tulajdonságfrissítésekre. Például egy operátor módosíthatja a telemetriai küldési időközt.
* Válaszoljon a parancsokra, például az eszköz újraindítására.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez létre kell [hoznia egy Azure IoT Central alkalmazást.](./quick-deploy-iot-central.md)

## <a name="create-downstream-device-templates"></a>Alsóbb rétegbeli eszközsablonok létrehozása

Ez az oktatóanyag eszközsablonokat használ egy **S1 érzékelő** eszközhöz és egy **RS40-es kihasználtsági érzékelő** eszközt szimulált alsóbb rétegbeli eszközök létrehozásához.

Eszközsablon létrehozása **S1 érzékelőeszközhöz:**

1. A bal oldali ablaktáblában válassza az **Eszközsablonok lehetőséget.** Ezután **+** válassza a sablon hozzáadásának megkezdéséhez.

1. Görgessen lefelé, amíg meg nem jelenik az S1 érzékelő eszköz **csempéje.** Jelölje ki a csempét, majd a **Tovább: Testreszabás**lehetőséget.

1. A **Véleményezés** lapon válassza a **Létrehozás** lehetőséget, ha hozzá szeretné adni az eszközsablont az alkalmazáshoz. 

Eszközsablon létrehozása ***RS40-es kihasználtságérzékelő eszközhöz:**

1. A bal oldali ablaktáblában válassza az **Eszközsablonok lehetőséget.** Ezután **+** válassza a sablon hozzáadásának megkezdéséhez.

1. Görgessen lefelé, amíg meg nem jelenik a ***RS40 kihasználtságérzékelő eszköz csempéje.** Jelölje ki a csempét, majd a **Tovább: Testreszabás**lehetőséget.

1. A **Véleményezés** lapon válassza a **Létrehozás** lehetőséget, ha hozzá szeretné adni az eszközsablont az alkalmazáshoz. 

Most már rendelkezik eszközsablonokkal a két alsóbb rétegbeli eszköztípushoz:

![Eszközsablonok alsóbb rétegbeli eszközökhöz](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>Átjáró-eszközsablon létrehozása

Ebben az oktatóanyagban egy teljesen új átjáróeszköz eszközsablont hoz létre. Ezt a sablont később egy szimulált átjáróeszköz létrehozásához az alkalmazásban.

Új átjáróeszköz-sablon hozzáadása az alkalmazáshoz:

1. A bal oldali ablaktáblában válassza az **Eszközsablonok lehetőséget.** Ezután **+** válassza a sablon hozzáadásának megkezdéséhez.

1. A **Sablontípus kiválasztása** lapon jelölje ki az **IoT-eszköz csempét,** majd kattintson a **Tovább gombra: Testreszabás**.

1. Az **Eszköz testreszabása** lapon jelölje be az **Átjáróeszköz** jelölőnégyzetet.

1. A **Véleményezés** lapon válassza a **Létrehozás gombot.** 

1. Adja meg a **Smart Building átjáróeszköz** sablonnevét.

1. A **Képességmodell létrehozása** lapon válassza az **Egyéni** csempe lehetőséget.

1. Válassza **+** a felület hozzáadásához.  Válassza az **Eszközinformáció** szabványos felületet.

### <a name="add-relationships"></a>Kapcsolatok hozzáadása

Ezután kapcsolatokat ad hozzá az alsóbb rétegbeli eszközsablonok sablonjaihoz:

1. A **Smart Building átjáróeszközsablonban** válassza a **Kapcsolatok lehetőséget.**

1. Válassza **a + Kapcsolat hozzáadása**lehetőséget. Adja meg **a Környezeti érzékelő** nevet a megjelenítendő névként, és válassza az **S1 érzékelő t** a célként.

1. Ismét válassza **a + Kapcsolat hozzáadása lehetőséget.** A Megjelenítendő névként adja meg a **Kihasználtságérzékelő** t, és válassza az **RS40-es kihasználtsági érzékelőt** a célpontként.

1. Kattintson a **Mentés** gombra.

![Smart Building átjáróeszköz-sablon, amely megmutatja a kapcsolatokat](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>Felhőtulajdonságok hozzáadása

Az átjáróeszköz-sablon felhőalapú tulajdonságokat is tartalmazhat. A felhőtulajdonságok csak az IoT Central alkalmazásban léteznek, és soha nem küldik el vagy fogadják az eszközről.

Felhőtulajdonságok hozzáadása a **Smart Building átjáróeszköz-sablonhoz.**

1. A **Smart Building átjáróeszköz** sablonjában válassza a **Felhőtulajdonságok lehetőséget.**

1.  Az alábbi táblázatban található információk segítségével két felhőtulajdonságot adhat hozzá az átjáróeszköz-sablonhoz.

    | Megjelenített név      | Szemantikai típus | Séma |
    | ----------------- | ------------- | ------ |
    | Utolsó szervizelés dátuma | None          | Dátum   |
    | Vevő neve     | None          | Sztring |

2. Kattintson a **Mentés** gombra.

### <a name="create-views"></a>Nézetek létrehozása

Építőként testreszabhatja az alkalmazást, hogy a környezeti érzékelő eszközével kapcsolatos releváns információkat megjelenítse az üzemeltető számára. A testreszabások lehetővé teszik az üzemeltető számára az alkalmazáshoz csatlakoztatott környezetérzékelő eszközök kezelését. Kétféle nézetet hozhat létre az operátorok számára az eszközökkel való kommunikációhoz:

* Űrlapok az eszköz- és felhőtulajdonságok megtekintéséhez és szerkesztéséhez.
* Irányítópultok az eszközök megjelenítéséhez.

A **Smart Building átjáróeszközsablon** alapértelmezett nézeteinek létrehozása:

1. A **Smart Building átjáróeszközsablonban** válassza a **Nézetek**lehetőséget.

1. Válassza az Alapértelmezett nézetek létrehozása csempe **lehetőséget,** és győződjön meg arról, hogy az összes beállítás ki van jelölve.

1. Válassza **az Alapértelmezett irányítópult-nézet(ek) létrehozása lehetőséget.**

## <a name="publish-the-device-template"></a>Az eszközsablon közzététele

Mielőtt létrehozhatna egy szimulált átjáróeszközt, vagy csatlakoztathatna egy valódi átjáróeszközt, közzé kell tennie az eszközsablont.

Az átjáróeszköz-sablon közzététele:

1. Válassza ki a **Smart Building átjáró eszközsablont** az **Eszközsablonok** lapon.

2. Kattintson a **Publish** (Közzététel) elemre.

3. Az **Eszközsablon közzététele** párbeszédpanelen válassza a **Közzététel**lehetőséget.

Az eszközsablon közzététele után az **eszközök** lapon és az operátor számára is látható. Egy közzétett eszközsablonban nem szerkesztheti az eszközképességi modellt új verzió létrehozása nélkül. A felhőtulajdonságokat, a testreszabásokat és a nézeteket azonban egy közzétett eszközsablonban is módosíthatja. Ezek a frissítések nem eredményeznek új verziót. A módosítások elvégzése után válassza **a Közzététel** lehetőséget, ha a módosításokat ki szeretné adni a szolgáltatónak.

## <a name="create-the-simulated-devices"></a>A szimulált eszközök létrehozása

Ez az oktatóanyag szimulált alsóbb rétegbeli eszközöket és szimulált átjáróeszközt használ.

Szimulált átjáróeszköz létrehozása:

1. Az **Eszközök** lapon válassza a **Smart Building átjáróeszköz** lehetőséget az eszközsablonok listájában.

1. Új **+** eszköz hozzáadásának megkezdéséhez válassza ezt a lehetőséget.

1. Tartsa meg a létrehozott **eszközazonosítót** és **az eszköz nevét.** Győződjön meg arról, hogy a **Szimulált** kapcsoló be van **kapcsolva.** Kattintson a **Létrehozás** gombra.

Szimulált alsóbb rétegbeli eszközök létrehozása:

1. Az **Eszközök** lapon válassza az **RS40-es kihasználtsági érzékelő lehetőséget** az eszközsablonok listájában.

1. Új **+** eszköz hozzáadásának megkezdéséhez válassza ezt a lehetőséget.

1. Tartsa meg a létrehozott **eszközazonosítót** és **az eszköz nevét.** Győződjön meg arról, hogy a **Szimulált** kapcsoló be van **kapcsolva.** Kattintson a **Létrehozás** gombra.

1. Az **Eszközök** lapon válassza az **S1-érzékelő** lehetőséget az eszközsablonok listájában.

1. Új **+** eszköz hozzáadásának megkezdéséhez válassza ezt a lehetőséget.

1. Tartsa meg a létrehozott **eszközazonosítót** és **az eszköz nevét.** Győződjön meg arról, hogy a **Szimulált** kapcsoló be van **kapcsolva.** Kattintson a **Létrehozás** gombra.

![Szimulált eszközök az alkalmazásban](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>Alsóbb rétegbeli eszközkapcsolatok hozzáadása átjáróeszközhöz

Most, hogy már a szimulált eszközök az alkalmazásban, létrehozhatja a kapcsolatot az alsóbb rétegbeli eszközök és az átjáró eszköz:

1. Az **Eszközök** lapon válassza az **S1-érzékelő** lehetőséget az eszközsablonok listájában, majd válassza ki a szimulált **S1 érzékelő** eszközt.

1. Válassza **a Csatlakozás átjáróhoz**lehetőséget.

1. A **Csatlakozás átjáróhoz** párbeszédpanelen jelölje ki a **Smart Building átjáróeszköz-sablont,** majd válassza ki a korábban létrehozott szimulált példányt.

1. Válassza **a Csatlakozás**lehetőséget.

1. Az **Eszközök** lapon válassza az **RS40-es kihasználtsági érzékelő lehetőséget** az eszközsablonok listájában, majd válassza ki a szimulált **RS40-es kihasználtságérzékelő** eszközt.

1. Válassza **a Csatlakozás átjáróhoz**lehetőséget.

1. A **Csatlakozás átjáróhoz** párbeszédpanelen jelölje ki a **Smart Building átjáróeszköz-sablont,** majd válassza ki a korábban létrehozott szimulált példányt.

1. Válassza **a Csatlakozás**lehetőséget.

Mindkét szimulált alsóbb rétegbeli eszköz most csatlakozik a szimulált átjáróeszközhöz. Ha az átjáróeszköz **alsóbb rétegbeli eszközök** nézetére lép, láthatja a kapcsolódó alsóbb rétegbeli eszközöket:

![Alsóbb rétegbeli eszközök nézete](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

Jelöljön ki egy átjáróeszköz-sablont és átjáróeszköz-példányt, és válassza a **Csatlakozás**lehetőséget.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Hozzon létre egy új IoT-átjárót eszközsablonként.
* Hozzon létre felhőtulajdonságokat.
* Testreszabások létrehozása.
* Vizualizációdefiniálása az eszköz telemetriai adataihoz.
* Kapcsolatok hozzáadása.
* Tegye közzé az eszközsablont.

> [!NOTE]
> A VS code alapú kódgenerálás jelenleg nem támogatott az IoT Centralban modellezett átjáróeszközök esetében.

Ezután eszközfejlesztőként megtudhatja, hogyan teheti meg:

> [!div class="nextstepaction"]
> [Azure IoT Edge-eszköz hozzáadása az Azure IoT Central alkalmazáshoz](tutorial-add-edge-as-leaf-device.md)
