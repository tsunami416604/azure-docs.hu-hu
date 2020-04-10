---
title: Az IoT Connected logisztika bemutatója | Microsoft dokumentumok
description: Az IoT Central csatlakoztatott logisztikai alkalmazássablonjának oktatóanyaga
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: eac43ae68b10436b3e45452c6b1d03bec3ae4c9c
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000559"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Oktatóanyag: Egy csatlakoztatott logisztikai alkalmazássablon telepítése és végigjárása



Ez az oktatóanyag bemutatja, hogyan kezdheti el az IoT Central **csatlakoztatott logisztikai alkalmazássablon** üzembe helyezését. Megtudhatja, hogyan telepítheti a sablont, mi szerepel a dobozon kívül, és mit érdemes elvégezni.

Ebben a bemutatóban megtudhatja, hogyan kell,

* csatlakoztatott logisztikai alkalmazás létrehozása
* végigaz alkalmazás 

## <a name="prerequisites"></a>Előfeltételek

* Az alkalmazás telepítéséhez nincs szükség konkrét előfeltételekre
* Ajánlott Azure-előfizetés, de akár anélkül is próbálkozhat

## <a name="create-connected-logistics-application-template"></a>Csatlakoztatott logisztikai alkalmazássablon létrehozása

Az alkalmazást a következő lépésekkel hozhatja létre

1. Keresse meg az Azure IoT Central alkalmazáskezelő webhelyét. Válassza a bal oldali navigációs sáv **építés gombját,** majd kattintson a **Kiskereskedelem** fülre.

    > [!div class="mx-imgBorder"]
    > ![Csatlakoztatott logisztikai irányítópult](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

2. Válassza **az Alkalmazás létrehozása lehetőséget** a Csatlakoztatott logisztikai alkalmazás **csoportban.**

3. **Az alkalmazás létrehozása** megnyílik az Új jelentkezési lap, és az alábbiak szerint töltse ki a kért adatokat.
   * **Alkalmazásneve**: használhatja az alapértelmezett javasolt nevet, vagy megadhatja a rövid alkalmazás nevét.
   * **URL**: használhatja a javasolt alapértelmezett URL-t, vagy megadhatja a barátságos, egyedi emlékezetes URL-t. Ezután az alapértelmezett beállítás ajánlott, ha már rendelkezik egy Azure-előfizetéssel. Tudod elkezd -val 7- nap szabad próba- árkialakítás tervez és választ -hoz megtérít -hoz egy mértékadó árkialakítás tervez bármikor előtt a szabad nyom lejár.
   * **Számlázási adatok:** A címtár, az Azure-előfizetés és a régió adatait az erőforrások kiépítése szükséges.
   * **Létrehozás:** Válassza a lap alján található létrehozás lehetőséget az alkalmazás üzembe helyezéséhez.

    > [!div class="mx-imgBorder"]
    > ![Csatlakoztatott logisztikai irányítópult](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

    > [!div class="mx-imgBorder"]
    > ![Csatlakoztatott logisztikai számlázási adatok](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>Az alkalmazás végigjárása 

## <a name="dashboard"></a>Irányítópult

Az alkalmazássablon sikeres üzembe helyezése után az alapértelmezett irányítópult egy csatlakoztatott logisztikai operátorra fókuszált portál. A Northwind Trader egy fiktív logisztikai szolgáltató, amely az óceánban és a szárazföldön szállít jattot. Ezen az irányítópulton két különböző átjáró telemetriai adatokat, valamint a kapcsolódó parancsokat, feladatokat és műveleteket biztosít. Ez az irányítópult előre konfigurálva van, hogy bemutassa a kritikus logisztikai eszközműveletek tevékenységét.
Az irányítópult logikailag két különböző átjáróeszköz-kezelési művelet között oszlik meg, 
   * Logisztikai útvonal a teherautó-szállításhoz és az óceánszállítás helyadatai alapvető fontosságúak a multimodális szállításhoz
   * Az átjáró állapotának megtekintése & releváns információk 

> [!div class="mx-imgBorder"]
> ![Csatlakoztatott logisztikai irányítópult](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png)

   * Az átjárók, az aktív és az ismeretlen címkék teljes számát könnyedén nyomon követheti.
   * Eszközkezelési műveleteket, például a firmware frissítését, az érzékelő letiltását, az érzékelő frissítését, az érzékelő küszöbértékének frissítését, a telemetriai időközök frissítését, & eszközszolgáltatási szerződések frissítésének művelete.
   * Eszköz energiafogyasztásának megtekintése

> [!div class="mx-imgBorder"]
> ![Csatlakoztatott logisztikai irányítópult](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png)

## <a name="device-template"></a>Eszközsablon

Kattintson az Eszközsablonok fülre, és látni fogja az átjáró képességmodelljét. A képességmodell két különböző felület köré **épül, az átjárótelemetria& tulajdonság** és **az átjáróparancsok köré épül**

**Átjáró telemetriai & tulajdonság** – Ez a felület az érzékelőkhöz, helyadatokhoz és eszközadatokhoz kapcsolódó összes telemetriai adatot, valamint az ikereszköz-tulajdonságképességeket, például az érzékelőküszöbértékeket & frissítési időközöket jelöli.

> [!div class="mx-imgBorder"]
> ![Csatlakoztatott logisztikai irányítópult](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png)

**Átjáróparancsok** – Ez a felület rendezi az összes átjáróparancs-képességet

> [!div class="mx-imgBorder"]
> ![Csatlakoztatott logisztikai irányítópult](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png)

## <a name="rules"></a>Szabályok
Válassza ki a szabályok lapot az alkalmazássablonban található két különböző szabály megtekintéséhez. Ezek a szabályok úgy vannak beállítva, hogy további vizsgálatok céljából e-mailben küldjék el az értesítéseket az operátoroknak.
 
**Átjárólopási riasztás:** Ez a szabály akkor aktiválódik, ha az érzékelők az utazás során váratlan fényérzékelést észlelnek. Az üzemeltetőket azonnal értesíteni kell, hogy kivizsgálják a potenciális lopást.
 
**Nem válaszoló átjáró:** Ez a szabály aktiválódik, ha az átjáró hosszabb ideig nem jelent a felhőnek. Az átjáró nem válaszol, mert az alacsony töltöttségi mód, a kapcsolat elvesztése, az eszköz állapota.

> [!div class="mx-imgBorder"]
> ![Csatlakoztatott logisztikai irányítópult](./media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png)

## <a name="jobs"></a>Feladatok
Az alkalmazássablon részeként létező öt különböző feladat megtekintéséhez válassza a Feladatok lapot:

> [!div class="mx-imgBorder"]
> ![Csatlakoztatott logisztikai irányítópult](./media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png)

A feladatok funkcióval megoldásszintű műveleteket tehet le. Itt a feladatok az eszközparancsokat és az ikerképességeket használják olyan feladatok elvégzésére, mint például bizonyos érzékelők letiltása az összes átjárón, vagy az érzékelő küszöbértékének módosítása a szállítási módtól és az útvonaltól függően. 
   * Ez egy szabványos művelet, hogy tiltsa le a sokk érzékelők során óceán szállítás megőrzése akkumulátor vagy csökkenti a hőmérsékleti küszöböt a hideg lánc szállítás. 
 
   * A feladatok lehetővé teszik, hogy rendszerszintű műveleteket, például az átjárók belső vezérlőprogramjának frissítése vagy a szolgáltatási szerződés frissítése, hogy naprakész maradjon a karbantartási tevékenységek.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha nem fogja tovább használni ezt az alkalmazást, törölje az alkalmazássablont a **Felügyeleti** > **alkalmazás beállításai** hivatkozással, és kattintson a Törlés **gombra.**

> [!div class="mx-imgBorder"]
> ![Csatlakoztatott logisztikai irányítópult](./media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png)

## <a name="next-steps"></a>További lépések
* További információ a [csatlakoztatott logisztikai koncepcióról](./architecture-connected-logistics.md)
* További információ az [IoT Central egyéb kiskereskedelmi sablonjairól](./overview-iot-central-retail.md)
* További információ az [IoT Central áttekintéséről](../core/overview-iot-central.md)
