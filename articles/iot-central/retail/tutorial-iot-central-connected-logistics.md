---
title: A IoT kapcsolódó logisztikai útmutatója | Microsoft Docs
description: A IoT Central kapcsolódó logisztikai alkalmazás sablonjának oktatóanyaga
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: eac43ae68b10436b3e45452c6b1d03bec3ae4c9c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81000559"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Oktatóanyag: üzembe helyezés és útmutató a csatlakoztatott logisztikai alkalmazási sablonhoz



Ez az oktatóanyag bemutatja, hogyan kezdheti el az első lépéseket egy IoT Central **csatlakoztatott logisztikai** alkalmazás-sablon üzembe helyezésével. Megtudhatja, hogyan helyezheti üzembe a sablont, mit tartalmaz a mező, és hogy mit szeretne tenni a következő lépésekkel.

Ebből az oktatóanyagból megtudhatja, hogyan,

* csatlakoztatott logisztikai alkalmazás létrehozása
* az alkalmazás végigvezeti 

## <a name="prerequisites"></a>Előfeltételek

* Az alkalmazás üzembe helyezéséhez nem szükségesek konkrét előfeltételek
* Ajánlott az Azure-előfizetés használata, de anélkül is próbálkozhat

## <a name="create-connected-logistics-application-template"></a>Csatlakoztatott logisztikai alkalmazás sablonjának létrehozása

Az alábbi lépések végrehajtásával hozhat létre alkalmazást

1. Navigáljon az Azure IoT Central Application Manager webhelyére. Válassza a **Létrehozás** lehetőséget a bal oldali navigációs sávon, majd kattintson a **kereskedelmi** fülre.

    > [!div class="mx-imgBorder"]
    > ![Csatlakoztatott logisztikai irányítópult](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

2. Válassza az **alkalmazás létrehozása** a **csatlakoztatott logisztikai alkalmazásban** lehetőséget.

3. Az alkalmazás **létrehozásakor** megnyílik az új alkalmazás űrlapja, és az alább látható módon töltse ki a kért adatokat.
   * **Alkalmazás neve**: az alapértelmezett javasolt nevet használhatja, vagy megadhatja a felhasználóbarát alkalmazás nevét.
   * **URL**: használhatja a javasolt alapértelmezett URL-címet, vagy megadhatja a felhasználóbarát egyedi emlékezetes URL-címét. Ezt követően az alapértelmezett beállítás akkor ajánlott, ha már rendelkezik Azure-előfizetéssel. Elkezdheti a 7 napos ingyenes próbaverzió díjszabását, és úgy is dönthet, hogy az ingyenes nyomvonal lejárta előtt bármikor standard díjszabási csomagra vált.
   * **Számlázási információ**: a címtár, az Azure-előfizetés és a régió részletei szükségesek az erőforrások kiépítéséhez.
   * **Létrehozás**: válassza a létrehozás lehetőséget az oldal alján az alkalmazás üzembe helyezéséhez.

    > [!div class="mx-imgBorder"]
    > ![Csatlakoztatott logisztikai irányítópult](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

    > [!div class="mx-imgBorder"]
    > ![Kapcsolódó logisztikai számlázási információk](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>Az alkalmazás végigvezeti 

## <a name="dashboard"></a>Irányítópult

Az alkalmazás sablonjának sikeres üzembe helyezését követően az alapértelmezett irányítópult egy kapcsolódó logisztikai szolgáltatói portál. A Northwind Trader egy fiktív logisztikai szolgáltató, amely az óceánban és a szárazföldön található áruszállítási flottát kezeli. Ezen az irányítópulton két különböző átjáró jelenik meg, amelyek a telemetria, valamint a hozzájuk tartozó parancsokat, feladatokat és műveleteket is biztosítják. Ez az irányítópult előre konfigurálva van a kritikus logisztikai eszközök működési tevékenységének bemutatására.
Az irányítópult logikailag oszlik meg két különböző átjáró-eszköz felügyeleti művelet között. 
   * A tehergépkocsi-szállítás logisztikai útvonala és a tengeri szállítás helye részletesen az összes multimodális szállítás
   * Az átjáró állapotának megtekintése & vonatkozó információk 

> [!div class="mx-imgBorder"]
> ![Csatlakoztatott logisztikai irányítópult](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png)

   * Egyszerűen nyomon követheti az átjárók, az aktív és az ismeretlen címkék teljes számát.
   * Megteheti az Eszközkezelő műveleteit, például a belső vezérlőprogram frissítését, az érzékelő letiltását, az érzékelő engedélyezését, az érzékelő küszöbértékének frissítését, a frissítési telemetria intervallumait, & Update Service-szerződések
   * Eszköz akkumulátor-felhasználásának megtekintése

> [!div class="mx-imgBorder"]
> ![Csatlakoztatott logisztikai irányítópult](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png)

## <a name="device-template"></a>Eszköz sablonja

Kattintson az eszközök sablonjai lapra, és látni fogja az átjáró képességének modelljét. A képesség modell két különböző interfész átjáró telemetria van strukturálva **& tulajdonság** -és **átjáró-parancsok**

**Átjáró telemetria & tulajdonsága** – ez a felület az érzékelőkkel, a hellyel és az eszköz adataival kapcsolatos összes telemetria, valamint az eszköz Twin tulajdonságának funkcióit jelöli, például az érzékelő küszöbértékeit & frissítési időközökben.

> [!div class="mx-imgBorder"]
> ![Csatlakoztatott logisztikai irányítópult](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png)

**Átjáró parancsai** – ez az illesztő az összes átjáró-parancs funkcióját rendszerezi

> [!div class="mx-imgBorder"]
> ![Csatlakoztatott logisztikai irányítópult](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png)

## <a name="rules"></a>Szabályok
Válassza a szabályok lapot az alkalmazás sablonjában található két különböző szabály megjelenítéséhez. Ezek a szabályok úgy vannak konfigurálva, hogy e-mail-értesítéseket küldjön a kezelőknek további vizsgálatokra.
 
**Átjáró-lopás riasztása**: ezt a szabályt akkor indítja el a rendszer, ha az érzékelők váratlan fényt észlelnek az utazás közben. A potenciális lopás kivizsgálásához az operátoroknak MIHAMARABB értesítést kell kapniuk.
 
Nem **válaszoló átjáró**: Ez a szabály akkor aktiválódik, ha az átjáró hosszabb ideig nem jelent jelentést a felhőnek. Az átjáró nem válaszol, mert kevés az akkumulátor mód, a kapcsolat elvesztése, az Eszközállapot.

> [!div class="mx-imgBorder"]
> ![Csatlakoztatott logisztikai irányítópult](./media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png)

## <a name="jobs"></a>Feladatok
Válassza a feladatok fület az alkalmazás sablonjának részeként öt különböző feladat megjelenítéséhez:

> [!div class="mx-imgBorder"]
> ![Csatlakoztatott logisztikai irányítópult](./media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png)

A feladatok szolgáltatással az egész megoldásra kiterjedő műveleteket végezhet. Itt a feladatok az eszköz parancsai és a Twin képesség használatával olyan feladatokat végezhetnek el, mint például bizonyos érzékelők letiltása az összes átjárón, vagy az érzékelő küszöbértékének módosítása a szállítás módjától és útvonaltól függően. 
   * Ez egy standard művelet, amellyel letilthatja a sokk-érzékelőket az óceáni szállítás során, hogy megőrizze az akkumulátort, vagy csökkentse a hőmérséklet küszöbértékét a hideg láncú szállítás során 
 
   * A feladatok lehetővé teszik a rendszerszintű műveletek, például a belső vezérlőprogram frissítését az átjárón vagy a szolgáltatási szerződés frissítését, hogy naprakészek maradjanak a karbantartási tevékenységekben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha nem folytatja az alkalmazás használatát, törölje az alkalmazás sablonját a **felügyeleti** > **alkalmazás beállításainak** meglátogatásával, és kattintson a **Törlés**gombra.

> [!div class="mx-imgBorder"]
> ![Csatlakoztatott logisztikai irányítópult](./media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png)

## <a name="next-steps"></a>További lépések
* További információ a [csatlakoztatott logisztikai koncepcióról](./architecture-connected-logistics.md)
* További információ a [IoT Central kiskereskedelmi sablonokról](./overview-iot-central-retail.md)
* További információ az [IoT Central áttekintése](../core/overview-iot-central.md)
