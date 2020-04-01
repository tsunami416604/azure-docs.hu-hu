---
title: Oktatóanyag – Az operátori irányítópult testreszabása az Azure IoT Centralban
description: Ez az oktatói bemutató bemutatja, hogyan szabhatja testre az üzemeltetői irányítópultot egy IoT Central-alkalmazásban, és hogyan kezelheti az eszközöket.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: fea3440dd7c017b85d6fffa2ec0fe5d26cd98b97
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77022171"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>Oktatóanyag: Az operátori irányítópult testreszabása és eszközök kezelése az Azure IoT Centralban


Ebben az oktatóanyagban, mint egy építő megtudhatja, hogyan szabhatja testre az üzemeltetői irányítópultot az Azure IoT Central üzleten belüli elemzési alkalmazásban. Az alkalmazás-üzemeltetők a testreszabott irányítópult segítségével futtathatják az alkalmazást, és kezelhetik a csatlakoztatott eszközöket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Az irányítópult nevének módosítása
> * Képcsempék testreszabása az irányítópulton
> * Csempék elrendezése az elrendezés módosításához
> * Telemetriai csempék hozzáadása a feltételek megjelenítéséhez
> * Tulajdonságcsempék hozzáadása az eszköz részleteinek megjelenítéséhez
> * Parancscsempék hozzáadása parancsok futtatásához

## <a name="prerequisites"></a>Előfeltételek

Az oktatónak el kell végeznie az oktatóanyagot az Azure IoT Central store elemzési alkalmazás ának létrehozásához és az eszközök hozzáadásához:

* [Üzleten belüli elemzési alkalmazás létrehozása az Azure IoT Centralban](./tutorial-in-store-analytics-create-app-pnp.md) (kötelező)

## <a name="change-the-dashboard-name"></a>Az irányítópult nevének módosítása
Az operátori irányítópult testreszabásához az alkalmazás alapértelmezett irányítópultját kell testreszabnia. További új irányítópultokat is létrehozhat. Az alkalmazás irányítópultjának testreszabása első lépésa a név módosítása.

1. Keresse meg az [Azure IoT Central alkalmazáskezelő](https://aka.ms/iotcentral) webhelyét.

1. Nyissa meg a feltételfigyelő alkalmazást, amelyet létrehozott a [Hozzon létre egy in-store analytics alkalmazás az Azure IoT Central oktatóanyagban.](./tutorial-in-store-analytics-create-app-pnp.md)

1. Válassza a **Szerkesztés gombot** az irányítópult eszköztárán. Szerkesztési módban testreszabhatja az irányítópult megjelenését, elrendezését és tartalmát.

    ![Azure IoT Központi szerkesztési irányítópult](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-edit.png)

1. Szükség esetén elrejtheti a bal oldali ablaktáblát. A bal oldali ablaktábla elrejtésével nagyobb munkaterületet biztosít az irányítópult szerkesztéséhez.

1. Adja meg az irányítópult rövid nevét az **Irányítópult nevében.** Ez az oktatóanyag egy Contoso nevű fiktív vállalatot használ, és a példa irányítópult neve *Contoso irányítópult.* 

1. Kattintson a **Mentés** gombra. A módosítások az irányítópultra kerülnek, és a szerkesztési mód le van tiltva.

    ![Az Azure IoT Central módosítja az irányítópult nevét](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>Képcsempék testreszabása az irányítópulton
Az Azure IoT Central alkalmazás irányítópultja egy vagy több csempéből áll. A csempe egy téglalap alakú tároló, amely tartalmat jelenít meg az irányítópulton. Különböző típusú tartalmakat társít a csempékhez, és az irányítópult-elrendezés testreszabásához húzással, húzással és átméretezéssel szabhatja testre az irányítópult-elrendezést. A tartalom megjelenítéséhez többféle csempe létezik. A képcsempék képeket tartalmaznak, és hozzáadhat egy URL-címet, amely lehetővé teszi a felhasználók számára, hogy a képre kattintsanak. A címkecsempék egyszerű szöveget jelenítenek meg. A Markdown csempék formázott tartalmat tartalmaznak, és lehetővé teszik egy HTML-ként megjelenő kép, URL, cím és markdown kód beállítását. Telemetriai, tulajdonság- vagy parancscsempék eszközspecifikus adatokat jelenítenek meg. 

Ebben a szakaszban megtudhatja, hogyan szabhatja testre a képcsempéket az irányítópulton.

A márkaképet megjelenítő képcsempe testreszabása az irányítópulton:

1. Válassza a **Szerkesztés gombot** az irányítópult eszköztárán. 

1. Válassza a **Beállítás** lehetőséget a Northwind márkaképet megjelenítő képcsempén. 

    ![Az Azure IoT Central márkaképének szerkesztése](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-edit.png)

1. A **cím**módosítása . A cím akkor jelenik meg, amikor a felhasználó a kép fölé viszi az egérmutatót.

1. Válassza a **Kép**lehetőséget . Megnyílik egy párbeszédpanel, amely lehetővé teszi egyéni lemezkép feltöltését. 

1. Tetszés szerint adjon meg egy URL-címet a képhez.

1. Válassza **a Konfiguráció frissítése**lehetőséget. A **Konfiguráció frissítése** gomb menti az irányítópult módosításait, és engedélyezi a szerkesztési módot.

    ![Az Azure IoT Central márkaképének mentése](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-save.png)

1. Szükség esetén válassza a **Konfigurálás** lehetőséget a **Dokumentáció**című csempén, és adjon meg egy URL-címet a támogatási tartalomhoz. 

A boltban lévő érzékelőzónák térképét megjelenítő képcsempe testreszabása:

1. Válassza a Beállítás lehetőséget az alapértelmezett tárolózóna-leképezést megjelenítő képcsempe **Konfigurálás** parancsát. 

1. Válassza a **Kép**lehetőséget, és a párbeszédpanelen töltsön fel egy áruházi zónatérkép egyéni képét. 

1. Válassza **a Konfiguráció frissítése**lehetőséget.

    ![Azure IoT Central tárolótérkép](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-map-save.png)

    A példa Contoso áruház térkép négy zónát jelenít meg: két pénztári zónát, egy ruházati és testápolási zónát, valamint egy élelmiszer- és csemegeterületet. Ebben az oktatóanyagban az érzékelők et ezekkel a zónákkal társítja a telemetriai adatok biztosításához.

    ![Azure IoT központi áruházi zónák](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-zones.png)

1. Kattintson a **Mentés** gombra. 

## <a name="arrange-tiles-to-modify-the-layout"></a>Csempék elrendezése az elrendezés módosításához
Az irányítópult testreszabásának egyik kulcslépése a csempék átrendezése egy hasznos nézet létrehozásához. Az alkalmazás-operátorok az irányítópulton az eszköztelemetria megjelenítésére, az eszközök kezelésére és az áruházban lévő körülmények figyelésére használják. Az Azure IoT Central leegyszerűsíti az irányítópult létrehozásának alkalmazásszerkesztői feladatát. Az irányítópult szerkesztési módja lehetővé teszi csempék gyors hozzáadását, áthelyezését, átméretezését és törlését. Az **in-store analytics - pénztár** alkalmazás sablon is leegyszerűsíti az irányítópult létrehozásának feladatát. Ez biztosítja a működő műszerfal elrendezés, érzékelők csatlakoztatva, és csempe, hogy a kijelző pénztár vonal számít, és a környezeti feltételeket.

Ebben a szakaszban átrendezheti az irányítópultot az **in-store analytics - pénztár** alkalmazássablonban, hogy egyéni elrendezést hozzon létre.

Az alkalmazásban nem használni kívánt csempék eltávolítása:

1. Válassza a **Szerkesztés gombot** az irányítópult eszköztárán. 

1. Válassza **az X delete** lehetőséget a következő **csempék eltávolításához: Vissza**az összes zónába , Az üzlet **irányítópultjának megtekintése**, **Várakozási idő**és a **3**pénztárhoz társított mindhárom csempe. A Contoso áruház irányítópultja nem használja ezeket a csempéket. 

    ![Az Azure IoT Központi törlési csempéi](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles.png)

1. Görgessen a többi irányítópult-csempének a képbe hozásához.

1. Válassza az **X Delete** lehetőséget a következő csempék eltávolításához: **Bemelegítő pénztárzóna**, **Lehűtési pénztárzóna**, **Kihasználtságérzékelő beállításai**, **Termosztátérzékelő beállításai**és Környezeti **körülmények**. 

   ![Az Azure IoT Central törli a fennmaradó csempéket](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles-2.png)

1. Kattintson a **Mentés** gombra. A nem használt csempék eltávolítása helyet szabadít fel a szerkesztési lapon, és leegyszerűsíti az operátorok irányítópult-nézetét.

1. Az irányítópult módosításai megtekintése.

   ![Azure IoT Central csempék törlése után](./media/tutorial-in-store-analytics-customize-dashboard-pnp/after-delete-tiles.png)

A nem használt csempék eltávolítása után rendezze át a fennmaradó csempéket, hogy rendezett elrendezést hozzon létre. Az új elrendezés helyet biztosít a későbbi ekben hozzáadott csempék számára.

A többi csempe átrendezése:

1. Válassza a **Szerkesztés** elemet.

1. Jelölje ki az **Occupcy firmware** csempét, és húzza azt az **Occupancy akkumulátorcsempétől jobbra.**

1. Jelölje ki **a termosztát belső vezérlőprogram** csempéjét, és húzza a **termosztát** akkumulátorcsempéjének jobb oldalán.

1. Kattintson a **Mentés** gombra.

1. Megtekintheti az elrendezésváltozásait. 

    ![Az Azure IoT Central belső vezérlőprogramjának akkumulátorát csempe](./media/tutorial-in-store-analytics-customize-dashboard-pnp/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>Telemetriai csempék hozzáadása a feltételek megjelenítéséhez
Miután testreszabta az irányítópult elrendezését, készen áll csempék hozzáadására a telemetriai adatok megjelenítéséhez. Telemetriai csempe létrehozásához jelöljön ki egy eszközsablont és eszközpéldányt, majd válassza ki a csempén megjelenítendő eszközspecifikus telemetriai adatokat. Az **in-store analytics – pénztár** alkalmazássablon több telemetriai csempét tartalmaz az irányítópulton. A két pénztárzóna négy csempéje a szimulált foglaltságérzékelő telemetriáját jeleníti meg. A **Személyek forgalom** csempe a két pénztárzónában mutatja a számokat. 

Ebben a szakaszban további két telemetriai csempét ad hozzá a környezeti telemetriai adatok megjelenítéséhez a RuuviTag-érzékelők, amelyeket az Azure IoT Central oktatóanyag ban adott hozzá a [Készleten belüli elemzési alkalmazás létrehozása.](./tutorial-in-store-analytics-create-app-pnp.md) 

Csempék hozzáadása a RuuviTag érzékelők környezeti adatainak megjelenítéséhez:

1. Válassza a **Szerkesztés** elemet.

1. Válassza `RuuviTag` ki az **Eszközsablon** listában. 

1. Válassza ki a két RuuviTag érzékelő egyikének **eszközpéldányát.** A példa Contoso-tárolóban válassza ki, hogy `Zone 1 Ruuvi` hozzon létre egy telemetriai csempe zóna 1. 

1. Válassza `Relative humidity` `temperature` ki, és a **Telemetriai** lista. Ezek azok a telemetriai elemek, amelyek a csempe egyes zónáihoz jelennek meg.

1. Válassza **az Egyesítés**lehetőséget. 

    ![Az Azure IoT Central ruuviTag csempét ad hozzá 1](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-zone1-ruuvi.png)

    Egy új csempe jelenik meg a kiválasztott érzékelő kombinált páratartalom és hőmérséklet-telemetriai adatainak megjelenítéséhez. 

1. Válassza a RuuviTag érzékelő új csempéjének **Konfigurálás** lehetőséget. 

1. Módosítsa a **címet** *1.* 

1. Válassza **a Konfiguráció frissítése**lehetőséget.

1. Ismételje meg az előző lépéseket a második érzékelőpéldány csempéjének létrehozásához. Állítsa a **Cím 2** *zónás környezetet,* majd válassza **a Konfiguráció frissítése lehetőséget.**

1. Húzza a **Zone 2 nevű csempét** a **termosztát belső vezérlőprogram csempéje** alá. 

1. Húzza az **1.** **People traffic** 

1. Kattintson a **Mentés** gombra. Az irányítópult zónatelemetriai adatokat jelenít meg a két új csempén.

    ![Azure IoT Central minden RuuviTag csempe](./media/tutorial-in-store-analytics-customize-dashboard-pnp/all-ruuvitag-tiles.png)

A Kapcsolatok **forgalom** csempe szerkesztése csak két kivételi zóna telemetriai adatainak megjelenítéséhez:

1. Válassza a **Szerkesztés** elemet. 

1. Válassza **Configure** a **Beállítás** lehetőséget a Személyek forgalom csempén.

1. A **Telemetriai** területen válassza **az 1,** **a 2-es**és **a 3-as számot.** 

1. Válassza **a Konfiguráció frissítése**lehetőséget. Törli a csempe meglévő konfigurációját. 

1. Válassza a **Beállítások** ismét lehetőséget a **Személyek forgalom** csempén.

1. A **Telemetriai** területen válassza **az 1-es számot,** a **2-es számot pedig.** 

1. Válassza **a Konfiguráció frissítése**lehetőséget. 

1. Kattintson a **Mentés** gombra.  A frissített irányítópult csak a két pénztári zónát jeleníti meg, amelyek a szimulált foglaltságérzékelőn alapulnak.

    ![Az Azure IoT Central két sávot forgalmaz](./media/tutorial-in-store-analytics-customize-dashboard-pnp/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>Tulajdonságcsempék hozzáadása az eszköz részleteinek megjelenítéséhez
Az alkalmazás-operátorok az irányítópultsegítségével kezelik az eszközöket, és figyelik az állapotot. Adjon hozzá egy csempét minden RuuviTag-hoz, hogy az operátorok megtekinthessék a szoftververziót. 

Tulajdonságcsempe hozzáadása minden Egyes RuuviTag-hoz:

1. Válassza a **Szerkesztés** elemet.

1. Válassza `RuuviTag` ki az **Eszközsablon** listában. 

1. Válassza ki a két RuuviTag érzékelő egyikének **eszközpéldányát.** A példa Contoso-tárolóban válassza ki, hogy `Zone 1 Ruuvi` hozzon létre egy telemetriai csempe zóna 1. 

1. Válassza **a Tulajdonságok > szoftver verziószáma**lehetőséget.

1. Válassza **az Egyesítés**lehetőséget. 

1. Válassza a **Konfigurálás** lehetőséget a **Szoftververzió**című újonnan létrehozott csempén. 

1. Változtassa meg a **cím** *Ruuvi 1 szoftver verzió*.

1. Válassza **a Konfiguráció frissítése**lehetőséget. 

1. Húzza a **Ruuv 1 szoftververzió** című csempét a **Zone 1 környezeti** csempe alá.

1. Ismételje meg az előző lépéseket a második RuuviTag szoftververziótulajdonság-csempéjének létrehozásához. 

1. Kattintson a **Mentés** gombra.  

    ![Az Azure IoT Central RuuviTag tulajdonságcsempéi](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>Parancscsempék hozzáadása parancsok futtatásához
Az alkalmazás-üzemeltetők az irányítópulton is kezelhetik az eszközöket parancsok futtatásával. Az irányítópulthoz olyan parancscsempéket adhat, amelyek előre definiált parancsokat hajtanak végre az eszközön. Ebben a szakaszban egy parancscsempét ad hozzá, amely lehetővé teszi az operátorok számára a Rigado-átjáró újraindítását. 

Az átjáró újraindításához parancscsempe hozzáadása:

1. Válassza a **Szerkesztés** elemet. 

1. Válassza `C500` ki az **Eszközsablon** listában. Ez a Rigado C500 átjáró sablonja. 

1. Jelölje ki az átjárópéldányt az **Eszközpéldányban.**

1. Válassza **a Command > Reboot** parancsot, és húzza az áruházi térkép melletti irányítópultra. 

1. Kattintson a **Mentés** gombra. 

1. Tekintse meg a contoso-irányítópultot. 

    ![Az Azure IoT Central teljes irányítópult-testreszabása](./media/tutorial-in-store-analytics-customize-dashboard-pnp/completed-dashboard.png)

1. Szükség esetén válassza ki az **Újraindítás** csempét az újraindítási parancs futtatásához az átjárón.

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Az irányítópult nevének módosítása
* Képcsempék testreszabása az irányítópulton
* Csempék elrendezése az elrendezés módosításához
* Telemetriai csempék hozzáadása a feltételek megjelenítéséhez
* Tulajdonságcsempék hozzáadása az eszköz részleteinek megjelenítéséhez
* Parancscsempék hozzáadása parancsok futtatásához

Most, hogy testre szabta az irányítópultot az Azure IoT Central üzleten belüli elemzési alkalmazásában, az alábbiakat javasolta:

> [!div class="nextstepaction"]
> [Adatok exportálása és elemzések megjelenítése](./tutorial-in-store-analytics-export-data-visualize-insights-pnp.md)
