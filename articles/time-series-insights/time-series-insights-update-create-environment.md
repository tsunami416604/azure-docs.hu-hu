---
title: Az Azure Time Series Insights előzetes verziója beállítása – állítsa be az Azure Time Series Insights – előzetes környezet oktatóanyag |} A Microsoft Docs
description: Ismerje meg, hogyan állítsa be a környezetet az Azure Time Series Insights előzetes verziója.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 9ad957d6378b1279f1ca51939eb4802b0ce7d78f
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322618"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Oktatóanyag: Az Azure Time Series Insights – előzetes környezet beállítása

Ez az oktatóanyag végigvezeti egy Azure Time Series Insights használatalapú (PAYG) előzetes verziójú környezet létrehozásának folyamatán. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

* Az Azure Time Series Insights – előzetes környezetet hozhat létre.
* Csatlakozás az Azure Time Series Insights – előzetes környezet az Azure Event Hubs-eseményközpontba.
* A szél farm szimuláció futtatása az adatok streamelése az Azure Time Series Insights – előzetes környezetbe.
* Alapvető adatok elemzését, a.
* Egy Time Series modell típusa és a hierarchia megadása, és társíthatja azt az üzemelő példányok.

# <a name="create-a-device-simulation"></a>Eszközszimuláció létrehozása

Ebben a szakaszban fog létrehozni, amely adatokat küld az IoT Hub három szimulált eszközökhöz.

1. Nyissa meg a [Azure IoT-megoldás megoldásgyorsítók kezdőlap](https://www.azureiotsolutions.com/Accelerators). Az Azure IoT megoldás megoldásgyorsítók kezdőlapján számos előre elkészített példákat. Jelentkezzen be az Azure-fiókjával. Ezután válassza ki **Eszközszimuláció**.

   ![Az Azure IoT megoldás megoldásgyorsítók kezdőlapja][1]

   Végül kattintson a **próbálja ki most**.

1. Adja meg a szükséges paramétereket a a **létrehozása Eszközszimuláció** megoldást lapon:

   | Paraméter | Leírás |
   | --- | --- |
   | Megoldás neve |    Egy egyedi érték, amelyet a rendszer új erőforráscsoportok létrehozásához használ. A listán szereplő Azure-erőforrások | az erőforráscsoport hozzárendelt. |
   | Előfizetés | Adja meg a TSI-környezet létrehozásához használt ugyanahhoz az előfizetéshez |
   | Régió |   Adja meg a TSI a létrehozásához használt ugyanabban a régióban. |
   | Választható Azure-erőforrások üzembe helyezése    | Hagyja üresen az IoT Hub be van jelölve, ahogy a szimulált eszközök, segítségével adatokat a connect/stream. |

   Miután megadta a szükséges paramétereket, kattintson a **megoldás létrehozása**. Várjon, amíg az üzembe helyezni a megoldás körülbelül 10 – 15 perc.

   ![Eszköz szimulálása megoldás létrehozása][2]

1. Az a **megoldás gyorsító irányítópult**, kattintson a **indítsa el a** gombra:

   ![Az eszköz szimulálása megoldás indítása][3]

1. A rendszer átirányítja a **a Microsoft Azure IoT-eszköz szimulálása** lapot. Kattintson a **+ új szimuláció** , a képernyő jobb felső sarokban található.

   ![Az Azure IoT-szimuláció lap][4]

1.  Adja meg a szükséges paramétereket a következőképpen:

    ![Töltse ki a paramétereket][5]

    |||
    | --- | --- |
    | **Name (Név)** | Adjon meg egy egyedi nevet szimulátort |
    | **Leírás** | Adjon meg definíciót |
    | **Szimuláció időtartama** | Beállítása `Run indefinitely` |
    | **Eszköz modellje** | **Név**: Adja meg `Chiller` **összeg**: Adja meg `3` |
    | **Cél IoT Hub** | Beállítása `Use pre-provisioned IoT Hub` |

    A szükséges paramétereket a töltés után kattintson a **Start Simulation**.

1. Eszköz szimulálása irányítópultján látható a **aktív eszközök** és **üzenetek / másodperc**.

    ![Az Azure IoT-szimuláció irányítópult][6]

## <a name="list-device-simulation-properties"></a>Lista eszköz szimulálása tulajdonságai

Az Azure Time Series Insights-környezetet hoz létre, szüksége lesz az IoT Hub, az előfizetés és az erőforráscsoport neve nevei.

1. Nyissa meg a **megoldás gyorsító irányítópult** , és jelentkezzen be Azure-előfizetés ugyanazzal a fiókkal. Keresse meg az eszköz-szimuláció, amely az előző lépésekben létrehozott.

1. Kattintson a készülékszimulátort a, és kattintson a **indítsa el a**. Kattintson a **Azure felügyeleti portálján** hivatkozásra a jobb oldalon jelenik meg.

    ![Simulátor listaelemek][7]

1. Jegyezze fel az IoT Hub, az előfizetés és az erőforráscsoport-nevekkel.

    ![Azure Portal][8]

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Egy Time Series Insights előzetes PAYG környezet létrehozása

Ez a szakasz ismerteti, hogyan hozhat létre az Azure Time Series Insights – előzetes környezet használatával a [az Azure portal](https://portal.azure.com/).

1. Jelentkezzen be az Azure Portalra az előfizetéses fiókba.

1. Válassza ki **erőforrás létrehozása**.

1. Válassza ki a **IOT-** kategóriát, és válassza ki **Time Series Insights**.

   ![Hozzon létre egy erőforrást, majd válassza ki az IOT-, majd válassza ki és a Time Series Insights][9]

1. A mezők a lapon a következőképpen töltse ki:

   | | |
   | --- | ---|
   | **Környezet neve** | Válassza ki az Azure Time Series Insights – előzetes környezet egyedi nevét. |
   | **Előfizetés** | Adja meg az előfizetést, ahol szeretné létrehozni az Azure Time Series Insights – előzetes környezet. Akkor célszerű használni az IoT-erőforrások a készülékszimulátort által létrehozott többi ugyanahhoz az előfizetéshez. |
   | **Erőforráscsoport** | Az erőforráscsoport az Azure-erőforrások tárolója. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy új, az Azure Time Series Insights – előzetes környezet erőforrás. Fontos ajánlott eljárás az, mint a többi az IoT-erőforrások hozta létre a készülékszimulátort ugyanazt az erőforráscsoportot használja. |
   | **Hely** | Válassza ki az Azure Time Series Insights – előzetes környezet adatközponti régió. A hozzáadott sávszélességgel kapcsolatos költségek és a késés elkerülése érdekében a legjobb, ha az Azure Time Series Insights – előzetes környezet egyéb IoT-erőforrások ugyanabban a régióban. |
   | **Réteg** |  Válassza ki `PAYG` ami a használatalapú fizetés rövidítése. Ez az Azure Time Series Insights előzetes verziója a termékhez a Termékváltozat. |
   | **Tulajdonságazonosító** | A time series egyedileg azonosítja. Vegye figyelembe, hogy ez a mező nem módosítható, és később nem módosítható. Ebben az oktatóanyagban a mező beállítása `iothub-connection-device-id`. Time Series azonosító kapcsolatos további információkért olvassa el [kiválasztása a Time Series ID](./time-series-insights-update-how-to-id.md). |
   | **Tárfiók neve** | Adjon meg egy globális egyedi nevet létrehozni egy új tárfiókot. |

   Kattintson a fenti mezők kitöltése után **tovább: Eseményforrás**.

   ![Kattintson a Tovább gombra: Eseményforrás][10]

1. Lapon adja meg a következő mezőket:

   | | |
   | --- | --- |
   | **Eseményforrás létrehozása?** | Adja meg `Yes`|
   | **Name (Név)** | Itt egyedi értéket kell megadni, amely az eseményforrás neveként szolgál majd.|
   | **Adatforrás típusa** | Adja meg `IoT Hub` |
   | **Válassza ki a Hub?** | Adja meg `Select Existing` |
   | **Előfizetés** | Adja meg az előfizetés, készülékszimulátort is használ. |
   | **Az IoT Hub nevét** | Adja meg az IoT hub nevére, amelyet létrehozott készülékszimulátort. |
   | **Az IoT Hub-hozzáférési házirend** | Adja meg `iothubowner` |
   | **Az IOT Hub fogyasztói csoport** | Egy egyedi felhasználói csoport szükség van egy Azure Time Series Insights előzetes verziója. |
   | **Időbélyeg** | Ez a mező az időbélyeg-tulajdonság a beérkező telemetriai adatokat az azonosítására szolgál. Ebben az oktatóanyagban nem töltse ki a mezőt. A szimulátor használja az IoT Hub alapértelmezett értéke a Time Series Insights bejövő időbélyege.|

   Egy egyedi felhasználói csoport létrehozása:

   1. Kattintson a **új** mellett a **az IoT Hub fogyasztói csoport** mező:

      ![Kattintson a Tovább gombra: Eseményforrás][11]

   1. Adjon meg egy egyedi nevet a fogyasztói csoportot, és kattintson a **Hozzáadás**:

      ![Kattintson az Add (Hozzáadás) parancsra][12]

   Miután kitöltötte a fenti mezőkön, kattintson a **felülvizsgálat + létrehozása**.

      ![Áttekintés és létrehozás][13]

1. Tekintse át az Áttekintés lap található összes mezőhöz, és kattintson a **létrehozás**.

   ![Létrehozás][14]

1. Láthatja, hogy a központi telepítés állapotát.

   ![Üzembe helyezés kész][15]

1. A time series-környezetet hozzáférést kapni, ha a bérlő saját. Győződjön meg arról, hogy rendelkezik hozzáféréssel,:

   * Keresse meg az újonnan létrehozott Azure Time Series Insights – előzetes környezet. Ehhez az erőforráscsoport keresése. Ezt követően kattintson a time series-környezetet:

      ![Üzembe helyezés kész][16]

   * Lépjen az Azure Time Series Insights – előzetes oldalon **az adathozzáférési házirendek**.

     ![Adathozzáférési házirendek][17]

   * Győződjön meg arról, hogy szerepel-e a hitelesítő adatait.

     ![A hitelesítő adatok ellenőrzéséhez][18]

   Ha a hitelesítő adatok nem szerepelnek, akkor a környezet hozzáférési engedélyt adjon magának. Olvasási [adatok hozzáférés biztosítása](./time-series-insights-data-access.md) további információt az engedélyek beállításáról.

## <a name="analyze-data-in-your-environment"></a>A környezetben lévő adatok elemzése

Ebben a szakaszban alapszintű elemzési hajt végre az idősorozat-adatok használatával a [Azure Time Series Insights – előzetes explorer](./time-series-insights-update-explorer.md).

1. Az Azure Time Series Insights – előzetes explorer, az URL-cím (erőforrás) lapján kattintva nyissa meg a [az Azure portal](https://portal.azure.com/).

   ![A Time Series Insights explorer URL-címe][19]

1. A Explorerben válassza ki a **fölérendelt objektum nélküli példányok** csomópontját, így megtekintheti az összes az Azure Time Series Insights előzetes a környezetben.

   ![Fölérendelt objektum nélküli-példányok listájának][20]

1. Az idősor látható kattintson az első példánynál. Ezután kattintson a **megjelenítése átlagos nyomás**.

   ![Átlagos nyomás megjelenítése][21]

1. Time series diagram meg kell jelennie a jobb oldalon:

   ![Adatsorozat idődiagram][22]

1. Ismételje meg a **3. lépés** , a többi két időtartományra sorozat. Az összes idősorozat megtekinthető alább látható módon:

   ![Az összes adatsor idődiagram][23]

1. Módosítsa a **időtartomány** time series trendek megtekintéséhez az elmúlt órában. Válassza ki a **a** beállítás jelölését alább látható módon:

   ![A From lehetőség kiválasztása][24]

1. Belül idő módosítása a **a** lehetőség jelölését az elmúlt egy órában származó események megjelenítéséhez:

   ![A From lehetőség kiválasztása][25]

1. Az utolsó egy órára összes három eszközön ezután összehasonlíthatja nyomás:

   ![A From lehetőség kiválasztása][26]

## <a name="define-and-apply-a-model"></a>Definiálása és alkalmazása a modell

Ebben a szakaszban egy modellt az adatok szerkezetének, szeretné alkalmazni. A modell végrehajtásához határoz típusok, hierarchiákat és példányok. További információ az adatmodellezés, lépjen a [Time Series modellek](./time-series-insights-update-tsm.md).

1. A Explorerben válassza ki a **modell** lapon:

   ![Válassza ki a modell lap][27]

1. Ezután kattintson a **+ Hozzáadás** típus. A jobb oldalon a típus szerkesztője nyílik meg.

   ![Kattintson az Add (Hozzáadás) parancsra][28]

1. Ezután három változókat határozhat meg: Nyomás hőmérséklet és páratartalom-típus. Adja meg a következő mezőket:

   | | |
   | --- | ---|
   | **Name (Név)** | Adja meg `Chiller` |
   | **Leírás** | Adja meg `This is a type definition of Chiller` |

   * Most adja meg a nyomás három változókkal:

      | | |
      | --- | ---|
      | **Name (Név)** | Adja meg `Avg Pressure` |
      | **Érték** | Válassza ki **nyomás (kétirányú)**. Vegye figyelembe, eltarthat néhány percig, ez a mező feltöltése után az Azure Time Series Insights elkezdi fogadni a események |
      | **Összesítési műveletet** | A következők szerint válasszon: `AVG` |

      ![Változó hozzáadása][29]

      Kattintson a **+ változó** , adja hozzá a következő változót.

   * Most adja meg a hőmérséklet:

      | | |
      | --- | ---|
      | **Name (Név)** | Adja meg `Avg Temperature` |
      | **Érték** | Válassza ki **hőmérséklet (kétirányú)**. Vegye figyelembe, eltarthat néhány percig, ez a mező feltöltése után az Azure Time Series Insights elkezdi fogadni a események |
      | **Összesítési műveletet** | A következők szerint válasszon: `AVG`|

      ![Adja meg a hőmérséklet][30]

   * Most adja meg a páratartalommal:

      | | |
      | --- | ---|
      | **Name (Név)** | Adja meg `Max Humidity` |
      | **Érték** | Válassza ki **páratartalom (kétirányú)**. Vegye figyelembe, eltarthat néhány percig, ez a mező feltöltése után az Azure Time Series Insights elkezdi fogadni a események |
      | **Összesítési műveletet** | A következők szerint válasszon: `MAX`|

      ![Adja meg a hőmérséklet][31]

   Változók meghatározása, után kattintson a **létrehozás**.

1. Láthatja, hogy a hozzáadott típusa:

   ![Tekintse meg a hozzáadott típusa][32]

1. A következő lépés, hogy a hierarchia hozzáadása. Az a **hierarchiák** szakaszban jelölje be **+ Hozzáadás** egy új hierarchia:

   ![Hierarchia hozzáadása][33]

1. Adja meg a hierarchiában. Adja meg a mezők a következők szerint:

   | | |
   | --- | ---|
   | **Name (Név)** | Adja meg `Location Hierarchy` |
   | **1. szint** | Adja meg `Country` |
   | **2. szint** | Adja meg `City` |
   | **3. szint** | Adja meg `Building` |

   A fenti mezők kitöltése után kattintson a **létrehozás**.

   ![Egy hierarchia megadása][34]

1. A létrehozott hierarchiában tekintheti meg:

   ![Tekintse meg a hierarchiában][35]

1. Miután meghatározása a hierarchiában, kattintson a **példányok** a bal oldalon. A példányok jelennek meg, kattintson az első példánynál és kiválasztása után **szerkesztése**:

   ![Egy példány szerkesztése][36]

1. A jobb oldalon jelenik meg egy szövegszerkesztőben. Adja hozzá a következő mezőket:

   | | |
   | --- | --- |
   | **Típus** | A következők szerint válasszon: `Chiller` |
   | **Leírás** | Adja meg `Instance for Chiller-01.1` |
   | **Hierarchiák** | Engedélyezése `Location Hierarchy` |
   | **Ország** | Adja meg `USA` |
   | **Város** | Adja meg `Seattle` |
   | **Épület** | Adja meg `Space Needle` |

    Kattintson a fenti mezők kitöltése után **mentése**.

   ![Egy hűtő mentése][37]

1. Ismételje meg az előző lépést a többi érzékelő számára. Használja a következő mezőket:

   * A hűtő 01.2:

     | | |
     | --- | --- |
     | **Típus** | A következők szerint válasszon: `Chiller` |
     | **Leírás** | Adja meg `Instance for Chiller-01.2` |
     | **Hierarchiák** | Engedélyezése `Location Hierarchy` |
     | **Ország** | Adja meg `USA` |
     | **Város** | Adja meg `Seattle` |
     | **Épület** | Adja meg `Pacific Science Center` |

   * A hűtő 01.3:

     | | |
     | --- | --- |
     | **Típus** | A következők szerint válasszon: `Chiller` |
     | **Leírás** | Adja meg `Instance for Chiller-01.1` |
     | **Hierarchiák** | Engedélyezése `Location Hierarchy` |
     | **Ország** | Adja meg `USA` |
     | **Város** | Adja meg `New York` |
     | **Épület** | Adja meg `Empire State Building` |

1. Lépjen a **elemzés** lapra, és frissítse az oldalt. Bontsa ki az összes hierarchiaszintek idősorozatban található.

   ![Az elemzés lap megtekintése][38]

1. Ismerkedés a time series az elmúlt órában, módosítsa **Gyorshivatkozások** az elmúlt egy óra:

   ![Fedezze fel az elmúlt órában][39]

1. Kattintson az időpontok alapján adatsor **csendes-óceáni adatelemzési Center** kattintson **megjelenítése maximális páratartalom**.

   ![Maximális páratartalom megjelenítése][40]

1. Az idősor a **maximális páratartalom** időközzel mérete 1 perces nyílik meg. Egy régióban, széles szűrése bal kattintással. Ezután kattintson a jobb gombbal, és a Nagyítás események elemzése az időkereten belül:

   ![Megtekintheti, szűrheti és nagyítás][41]

   ![Megtekintheti, szűrheti és nagyítás][42]

1. Is bal kattintással egy régiót, és kattintson a jobb gombbal megtekintheti az esemény részleteit:

   ![Megtekintheti, szűrheti és nagyítás][43]

   ![Megtekintheti, szűrheti és nagyítás][44]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:  

* Létrehozhat és használhat egy eszköz szimulálása gyorsító.
* Az Azure Time Series Insights előzetes PAYG környezetet hozhat létre.
* Csatlakozás az Azure Time Series Insights – előzetes környezet egy eseményközpontba.
* A szél farm szimuláció futtatása az adatok streamelése az Azure Time Series Insights – előzetes környezetbe.
* Az adatok egy alapszintű elemzéseket végezhet.
* Egy Idősorozat-modell típusa és a hierarchia megadása, és rendelje azokat a példányokat.

Most, hogy tudja, hogyan hozhat létre a saját Azure Time Series Insights – előzetes környezet, tudjon meg többet az Azure Time Series Insightsban kapcsolatos főbb fogalmakat.

További információ az Azure Time Series Insights tárolási konfigurációt:

> [!div class="nextstepaction"]
> [Az Azure Time Series Insights előzetes verziója storage és a bejövő forgalom](./time-series-insights-update-storage-ingress.md)

További tudnivalók a Time Series modellek:

> [!div class="nextstepaction"]
> [Az Azure Time Series Insights előzetes verziója az adatmodellezés](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/device-one-accelerator.png
[2]: media/v2-update-provision/device-two-create.png
[3]: media/v2-update-provision/device-three-launch.png
[4]: media/v2-update-provision/device-four-iot-sim-page.png
[5]: media/v2-update-provision/device-five-params.png
[6]: media/v2-update-provision/device-six-listings.png
[7]: media/v2-update-provision/device-seven-dashboard.png
[8]: media/v2-update-provision/device-eight-portal.png

[9]: media/v2-update-provision/payg-one-azure.png
[10]: media/v2-update-provision/payg-two-create.png
[11]: media/v2-update-provision/payg-three-new.png
[12]: media/v2-update-provision/payg-four-add.png
[13]: media/v2-update-provision/payg-five-event-source.png
[14]: media/v2-update-provision/payg-six-review.png
[15]: media/v2-update-provision/payg-seven-deploy.png
[16]: media/v2-update-provision/payg-eight-environment.png
[17]: media/v2-update-provision/payg-nine-data-access.png
[18]: media/v2-update-provision/payg-ten-verify.png

[19]: media/v2-update-provision/analyze-one-portal.png
[20]: media/v2-update-provision/analyze-two-unparented.png
[21]: media/v2-update-provision/analyze-three-show-pressure.png
[22]: media/v2-update-provision/analyze-four-chart.png
[23]: media/v2-update-provision/analyze-five-chart.png
[24]: media/v2-update-provision/analyze-six-from.png
[25]: media/v2-update-provision/analyze-seven-change-from.png
[26]: media/v2-update-provision/analyze-eight-all.png

[27]: media/v2-update-provision/define-one-model.png
[28]: media/v2-update-provision/define-two-add.png
[29]: media/v2-update-provision/define-three-variable.png
[30]: media/v2-update-provision/define-four-avg.png
[31]: media/v2-update-provision/define-five-humidity.png
[32]: media/v2-update-provision/define-six-type.png
[33]: media/v2-update-provision/define-seven-hierarchy.png
[34]: media/v2-update-provision/define-eight-add-hierarchy.png
[35]: media/v2-update-provision/define-nine-created.png
[36]: media/v2-update-provision/define-ten-edit.png
[37]: media/v2-update-provision/define-eleven-chiller.png
[38]: media/v2-update-provision/define-twelve.png
[39]: media/v2-update-provision/define-thirteen-explore.png
[40]: media/v2-update-provision/define-fourteen-show-max.png
[41]: media/v2-update-provision/define-fifteen-filter.png
[42]: media/v2-update-provision/define-sixteen.png
[43]: media/v2-update-provision/define-seventeen.png
[44]: media/v2-update-provision/define-eighteen.png