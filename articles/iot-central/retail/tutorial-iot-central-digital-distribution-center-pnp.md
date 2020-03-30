---
title: Az IoT Digital Distribution Center bemutatója | Microsoft dokumentumok
description: Az IoT Central digitális elosztóközpont alkalmazássablonjának oktatóanyaga
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 93a77d73b5cc249c39609f98f055a7b1927dd6ed
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77025486"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Oktatóanyag: Digitális elosztóközpont-alkalmazássablon telepítése és végigjárása



Ez az oktatóanyag bemutatja, hogyan kezdheti el az IoT Central **digitális terjesztési központ** alkalmazássablonüzembe helyezését. Megtudhatja, hogyan telepítheti a sablont, mi szerepel a dobozon kívül, és mit érdemes elvégezni.

Ebben az oktatóanyagban megtudhatja, hogyan kell, 
* Digitális elosztóközpont-alkalmazás létrehozása 
* Az alkalmazás végigjárása 

## <a name="prerequisites"></a>Előfeltételek
* Az alkalmazás telepítéséhez nincs szükség konkrét előfeltételekre
* Ajánlott Azure-előfizetés, de akár anélkül is próbálkozhat

## <a name="create-digital-distribution-center-application-template"></a>Digitális elosztóközpont alkalmazássablon létrehozása

Az alkalmazást a következő lépésekkel hozhatja létre

1. Keresse meg az Azure IoT Central alkalmazáskezelő webhelyét. Válassza a bal oldali navigációs sáv **építés gombját,** majd kattintson a **Kiskereskedelem** fülre.

    > [!div class="mx-imgBorder"]
    > ![Digitális elosztóközpont](./media/tutorial-iot-central-ddc/iotc-retail-homepage.png)

2. Válassza a **Kiskereskedelmi** lap lehetőséget, és válassza az **Alkalmazás létrehozása** lehetőséget a **digitális elosztóközpont-alkalmazás alatt.**

3. **Az alkalmazás létrehozása** megnyílik az Új jelentkezési lap, és az alábbiak szerint töltse ki a kért adatokat.
   **Alkalmazásneve**: használhatja az alapértelmezett javasolt nevet, vagy megadhatja a rövid alkalmazás nevét.
   **URL**: használhatja a javasolt alapértelmezett URL-t, vagy megadhatja a barátságos, egyedi emlékezetes URL-t. Ezután az alapértelmezett beállítás ajánlott, ha már rendelkezik egy Azure-előfizetéssel. Tudod elkezd -val 7- nap szabad próba- árkialakítás tervez és választ -hoz megtérít -hoz egy mértékadó árkialakítás tervez bármikor előtt a szabad nyom lejár.
   **Számlázási adatok:** A címtár, az Azure-előfizetés és a régió adatait az erőforrások kiépítése szükséges.
   **Létrehozás:** Válassza a lap alján található létrehozás lehetőséget az alkalmazás üzembe helyezéséhez.

    > [!div class="mx-imgBorder"]
    > ![Digitális elosztóközpont](./media/tutorial-iot-central-ddc/ddc-create.png)

    > [!div class="mx-imgBorder"]
    > ![Digitális terjesztési számlázási adatok](./media/tutorial-iot-central-ddc/ddc-create-billinginfo.png)

## <a name="walk-through-the-application-dashboard"></a>Az alkalmazás irányítópultjának végigjárása 

Az alkalmazássablon sikeres üzembe helyezése után az alapértelmezett irányítópult egy elosztóközpont-üzemeltetőközpontú portál. A Northwind Trader egy fiktív elosztóközpont-megoldásszolgáltató, amely szállítószalag-rendszereket kezel. 

Ezen az irányítópulton egy átjáró és egy kamera fog iot-eszközként eljárni. Az átjáró telemetriai adatokat biztosít a csomagok, például az érvényes, érvénytelen, azonosítatlan és a kapcsolódó eszköz iker tulajdonságokat. Az összes alsóbb rétegbeli parancs végrehajtása IoT-eszközökön, például egy kamerán történik. Ez az irányítópult előre konfigurálva van, hogy bemutassa a kritikus elosztóközpont-eszközműveletek et.

Az irányítópult logikusan van rendszerezve az Azure IoT-átjáró és az IoT-eszköz eszközkezelési képességeinek megjelenítéséhez.  
   * Az átjáróparancs-& vezérlőfeladatokat elvégezhet
   * Kezelje a megoldás részét szolgáló összes kamerát. 

> [!div class="mx-imgBorder"]
> ![Digitális elosztóközpont](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Eszközsablon

Kattintson az Eszközsablonok fülre, és látni fogja az átjáró képességmodelljét. A képességmodell két különböző felület köré épül **a Fényképezőgép** és a Digitális **Terjesztési Átjáró**

> [!div class="mx-imgBorder"]
> ![Digitális elosztóközpont](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Kamera** - Ez a felület szervezi az összes kamera-specifikus parancs képességek 

> [!div class="mx-imgBorder"]
> ![Digitális elosztóközpont](./media/tutorial-iot-central-ddc/ddc-camera.png)

**Digitális terjesztési átjáró** – Ez a felület a kamera, a felhőalapú ikereszközök tulajdonságaiból és az átjáróadatokból érkező összes telemetriai adatot képviseli.

> [!div class="mx-imgBorder"]
> ![Digitális elosztóközpont](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>Átjáróparancsok
Ez az illesztő rendszerezi az összes átjáróparancs-képességet

> [!div class="mx-imgBorder"]
> ![Digitális elosztóközpont](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Szabályok
Válassza ki a szabályok lapot az alkalmazássablonban található két különböző szabály megtekintéséhez. Ezek a szabályok úgy vannak beállítva, hogy további vizsgálatok céljából e-mailben küldjék el az értesítéseket az operátoroknak.

 **Túl sok érvénytelen csomagriasztás** – Ez a szabály akkor aktiválódik, ha a fényképezőgép nagy számú érvénytelen csomagot észlel, amelyek a szállítószalag-rendszeren keresztül áramlanak.
 
**Nagy csomag** - Ez a szabály akkor aktiválódik, ha a fényképezőgép hatalmas csomagot észlel, amely nem ellenőrizhető a minőség szempontjából. 

> [!div class="mx-imgBorder"]
> ![Digitális elosztóközpont](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Feladatok
Válassza ki a feladatok lapon, hogy öt különböző feladatok, amelyek az alkalmazás sablon részeként létezik: A feladatok funkció valameddig a megoldás szintű műveleteket. Itt a digitális elosztóközpont-feladatok az eszközparancsokat használják, & ikerképességgel olyan feladatok at, mint például a
   * kamera kalibrálása a csomagészlelés megkezdése előtt 
   * rendszeres időközönként frissíti a kamera firmware-ét
   * a telemetriai időköz módosítása az adatfeltöltés kezeléséhez

> [!div class="mx-imgBorder"]
> ![Digitális elosztóközpont](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha nem fogja tovább használni ezt az alkalmazást, törölje az alkalmazássablont a **Felügyeleti** > **alkalmazás beállításai** hivatkozással, és kattintson a Törlés **gombra.**

> [!div class="mx-imgBorder"]
> ![Digitális elosztóközpont](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>További lépések
* További információ a digitális elosztóközpont megoldásarchitektúra [digitális elosztóközpontjának koncepciójáról](./architecture-digital-distribution-center-pnp.md)
* További információ az [IoT Central egyéb kiskereskedelmi sablonjairól](./overview-iot-central-retail-pnp.md)
* Az IoT Centralról további információ az [IoT Central áttekintése című témakörben található.](../core/overview-iot-central.md)
