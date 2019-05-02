---
title: 'Oktatóanyag: Az Azure Time Series Insights – előzetes környezet beállítása |} A Microsoft Docs'
description: Ismerje meg, hogyan állítsa be a környezetet az Azure Time Series Insights előzetes verziója.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.custom: seodec18
ms.openlocfilehash: f83063a88207f51f9d481447923fd8a8498692a2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713908"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Oktatóanyag: Az Azure Time Series Insights – előzetes környezet beállítása

Ez az oktatóanyag végigvezeti a használatalapú fizetés (Használatalapú) környezet Azure Time Series Insights – előzetes létrehozásának folyamatán. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

* Az Azure Time Series Insights – előzetes környezetet hozhat létre.
* Csatlakozás az Azure Time Series Insights – előzetes környezet az Azure Event Hubs-eseményközpontba.
* Egy megoldás gyorsító minta futtatása az adatok streamelése az Azure Time Series Insights – előzetes környezetbe.
* Alapvető adatok elemzését, a.
* Egy Idősorozat-modell típusa és a hierarchia definiálja, és társíthatja azt az üzemelő példányok.

# <a name="create-a-device-simulation"></a>Eszközszimuláció létrehozása

Ebben a szakaszban hoz létre, amely adatokat küld az IoT hub három szimulált eszközökhöz.

1. Nyissa meg a [Azure IoT-megoldás megoldásgyorsítók lapja](https://www.azureiotsolutions.com/Accelerators). Az oldal néhány előre létrehozott példa jeleníti meg. Jelentkezzen be az Azure-fiókjával. Ezután válassza ki **Eszközszimuláció**.

   [![Az Azure IoT-megoldás megoldásgyorsítók lapja](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Válassza ki **kipróbálása**.

1. Adja meg a szükséges paramétereket az **Eszközszimulációs megoldás létrehozása** oldalon:

   | Paraméter | Leírás |
   | --- | --- |
   | **Megoldás neve** |    Adjon meg egy egyedi értéket, egy új erőforráscsoport létrehozásához. A felsorolt Azure-erőforrások létrejönnek, és hozzá lesznek rendelve az erőforráscsoporthoz. |
   | **Előfizetés** | Adja meg a Time Series Insights-környezet létrehozásához használt ugyanahhoz az előfizetéshez. |
   | **Régió** |   Adja meg a Time Series Insights-környezet létrehozásakor használható ugyanabban a régióban. |
   | **Választható Azure-erőforrások üzembe helyezése**    | Hagyja kiválasztva, az IoT Hub, mert csatlakoztatása és adatok streamelése a szimulált eszközök fogja használni. |

   Ezután válassza ki **megoldás létrehozása**. Várjon 10 – 15 perc, a megoldás a telepítésre.

   [![Eszközszimuláció megoldás lap létrehozása](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. A megoldás gyorsító irányítópultján, válassza ki a **indítsa el a** gombra:

   [![Az eszköz szimulálása megoldás indítása](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. A program átirányítja a **a Microsoft Azure IoT-eszköz szimulálása** lapot. Válassza ki **+ új szimuláció** , az oldal jobb felső.

   [![Az Azure IoT-szimuláció lap](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. Adja meg a szükséges paramétereket a következőképpen:

    [![Töltse ki a paramétereket](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    |||
    | --- | --- |
    | **Name (Név)** | Adjon meg egy egyedi nevet szimulátort. |
    | **Leírás** | Adjon meg definíciót. |
    | **Szimuláció időtartama** | Állítsa be **határozatlan idejű futásra**. |
    | **Eszközmodell** | **Név**: Írja be a `Chiller` (igen) kifejezést. </br>**Összeg**: Írja be a `3` (igen) kifejezést. |
    | **Cél IoT Hub** | Állítsa be **használja az előzetesen kiépített az IoT Hub**. |

    Ezután válassza ki **szimuláció indítása**.

1. Eszköz szimulálása irányítópultján látható **aktív eszközök** és **üzenetek / másodperc**.

    [![Az Azure IoT-szimuláció irányítópult](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="list-device-simulation-properties"></a>Lista eszköz szimulálása tulajdonságai

Létrehoz egy Azure Time Series Insights-környezetet, mielőtt szüksége az IoT hub, az előfizetés és az erőforráscsoport nevét.

1. Nyissa meg a megoldás gyorsító irányítópultján, és jelentkezzen be Azure-előfizetés ugyanazzal a fiókkal. Keresse meg az eszköz-szimuláció, amely az előző lépésekben létrehozott.

1. Válassza ki a készülékszimulátort, majd **indítsa el a**. Válassza ki a **Azure felügyeleti portálján** hivatkozásra a jobb oldalon.

    [![Simulátor listaelemek](media/v2-update-provision/device-six-listings.png)](media/v2-update-provision/device-six-listings.png#lightbox)

1. Jegyezze fel az IoT hub, az előfizetés és erőforráscsoport-nevekkel.

    [![Az Azure Portalon](media/v2-update-provision/device-eight-portal.png)](media/v2-update-provision/device-eight-portal.png#lightbox)

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Egy Time Series Insights előzetes PAYG környezet létrehozása

Ez a szakasz ismerteti, hogyan hozhat létre az Azure Time Series Insights – előzetes környezet használatával a [az Azure portal](https://portal.azure.com/).

1. Jelentkezzen be az Azure Portalra az előfizetéses fiókba.

1. Válassza ki **erőforrás létrehozása**.

1. Válassza ki a **IOT-** kategóriát, és válassza ki **Time Series Insights**.

   [![Válassza ki az eszközök internetes hálózatát, és válassza ki a Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. Töltse ki a mezőket a lapon a következő:

   | | |
   | --- | ---|
   | **Környezet neve** | Válassza ki az Azure Time Series Insights – előzetes környezet egyedi nevét. |
   | **Előfizetés** | Adja meg az előfizetést, ahol szeretné létrehozni az Azure Time Series Insights – előzetes környezet. Akkor célszerű használni az IoT-erőforrások a készülékszimulátort által létrehozott többi ugyanahhoz az előfizetéshez. |
   | **Erőforráscsoport** | Az erőforráscsoport az Azure-erőforrások tárolója. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy új, az Azure Time Series Insights – előzetes környezet erőforrás. Fontos ajánlott eljárás az, mint a többi az IoT-erőforrások hozta létre a készülékszimulátort ugyanazt az erőforráscsoportot használja. |
   | **Hely** | Válassza ki az Azure Time Series Insights – előzetes környezet egy adatközpont-régiót. A hozzáadott sávszélességgel kapcsolatos költségek és a késés elkerülése érdekében a legjobb, ha az Azure Time Series Insights – előzetes környezet egyéb IoT-erőforrások ugyanabban a régióban. |
   | **Tier** |  Válassza ki **PAYG**, amely a használatalapú fizetés rövidítése. Ez a Termékváltozat az Azure Time Series Insights előzetes verziója a termékhez. |
   | **Tulajdonságazonosító** | Adja meg, amely egyedileg azonosítja a time series valami. Vegye figyelembe, hogy ez a mező nem módosítható, és később nem módosítható. A jelen oktatóanyag esetében használja `iothub-connection-device-id`. A Time Series azonosító kapcsolatos további információkért olvassa el [kiválasztása a Time Series ID](./time-series-insights-update-how-to-id.md). |
   | **Tárfiók neve** | Adjon meg egy globálisan egyedi nevet a létrehozandó új storage-fiókot. |

   Ezután válassza ki **tovább: Eseményforrás**.

   [![Egy Time Series Insights-környezet létrehozására szolgáló oldal](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. A lapon az esemény forrását a következőképpen töltse ki a mezőket:

   | | |
   | --- | --- |
   | **Eseményforrás létrehozása?** | Írja be a `Yes` (igen) kifejezést.|
   | **Name (Név)** | Adjon meg egy egyedi érték, amellyel az eseményforrás neve.|
   | **Adatforrás típusa** | Válassza ki **az IoT Hub**. |
   | **Válassza ki a hub?** | Válassza ki **válasszon meglévő**. |
   | **Előfizetés** | Válassza ki az előfizetést, az eszköz szimulátort használ. |
   | **Az IoT Hub nevét** | Válassza ki az IoT hub nevére, a készülékszimulátort létrehozott. |
   | **Az IoT Hub-hozzáférési házirend** | Válassza ki **iothubowner**. |
   | **Az IOT Hub fogyasztói csoport** | Az Azure Time Series Insights előzetes verziója egy egyedi felhasználói csoport van szükség. Válassza ki **új**, adjon meg egy egyedi nevet, és válassza **Hozzáadás**. |
   | **Időbélyeg-tulajdonság** | Ez a mező az időbélyeg-tulajdonság a beérkező telemetriai adatokat az azonosítására szolgál. A jelen oktatóanyag esetében ne adja meg a mezőben. A szimulátor használja az IoT hubról, alapértelmezett beállítása a Time Series Insights bejövő időbélyeg.|

   Ezután válassza ki **felülvizsgálat + létrehozása**.

   [![Eseményforrás konfigurálása](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Tekintse át az Áttekintés lap összes mezőt, és válassza ki **létrehozás**.

   [![Felülvizsgálat + létrehozás oldalon, a Létrehozás gombra](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

1. Láthatja, hogy a központi telepítés állapotát.

   [![Értesítés, hogy a telepítés befejeződött](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Hozzáférést kell kapnia az Azure Time Series Insights – előzetes környezethez, ha a bérlő saját. Győződjön meg arról, hogy rendelkezik hozzáféréssel,:

   a. Keresse meg az erőforráscsoport, és válassza ki azt az Azure Time Series Insights – előzetes környezetet:

      [![Kiválasztott környezet](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   b. Lépjen az Azure Time Series Insights – előzetes oldalon **az adathozzáférési házirendek**.

     [![Az adathozzáférési házirendek](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   c. Győződjön meg arról, hogy szerepel-e a hitelesítő adatait.

     [![Listán szereplő hitelesítő adatok](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Ha nem jelennek meg a hitelesítő adatait, hogy a környezet hozzáférési engedélyt adjon magának. További információt az engedélyek beállításáról, olvassa el [adathozzáférés](./time-series-insights-data-access.md).

## <a name="analyze-data-in-your-environment"></a>A környezetben lévő adatok elemzése

Ebben a szakaszban alapszintű elemzési hajt végre az idősorozat-adatok használatával a [Azure Time Series Insights – előzetes explorer](./time-series-insights-update-explorer.md).

1. Az URL-címet az erőforrás-oldalon válassza ki az Azure Time Series Insights – előzetes Explorerben nyissa meg a [az Azure portal](https://portal.azure.com/).

   [![A Time Series Insights előzetes verziója explorer URL-címe](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. A Explorerben válassza ki a **idő sorozat példányai** csomópont a környezet az Azure Time Series Insights – előzetes példányok megtekintéséhez.

   [![Fölérendelt objektum nélküli-példányok listájának](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Az idősor látható válassza ki az első példánynál. Ezután válassza ki **megjelenítése átlagos nyomás**.

   [![Az átlagos nyomás megjeleníthető menü paranccsal kiválasztott példány](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

   Time series diagram meg kell jelennie a jobb oldalon. Módosítsa a **időköz** való `15s`.

   [![Adatsorozat idődiagram](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Ismételje meg a **3. lépés** , a többi két időtartományra sorozat. Az összes idősorozat, majd tekintheti meg ezen a diagramon látható módon:

   [![Az összes idősorozat diagram](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Módosítsa a time series trendek megtekintéséhez az elmúlt órában az időtartományt.

   a. Válassza ki a **időkeretre** beállítás ki:

      [![Az időtartomány beállítása egy órára](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definiálása és alkalmazása a modell

Ebben a szakaszban egy modellt az adatok szerkezetének vonatkoznak. Fejezze be a modell, típusok, hierarchiákat és példányok megadása. További információ az adatmodellezés, lépjen a [Idősorozat-modell](./time-series-insights-update-tsm.md).

1. A Explorerben válassza ki a **modell** lapon:

   [![Az Explorer modell lap](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Válassza ki **+ Hozzáadás** típus. A jobb oldalon megnyílik egy típus szerkesztő.

   [![A Hozzáadás gombra típusok](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. A típus három változókat határozhat meg: nyomás hőmérsékleti és páratartalom. Adja meg a következő információkat:

   | | |
   | --- | ---|
   | **Name (Név)** | Írja be a `Chiller` (igen) kifejezést. |
   | **Leírás** | Írja be a `This is a type definition of Chiller` (igen) kifejezést. |

   * Adja meg a nyomás három változókkal:

      | | |
      | --- | ---|
      | **Name (Név)** | Írja be a `Avg Pressure` (igen) kifejezést. |
      | **Érték** | Válassza ki **nyomás (kétirányú)**. Vegye figyelembe, hogy ezt a mezőt kell feltöltenie az Azure Time Series Insights előzetes verziója a fogadott események indítása után néhány percet is igénybe vehet. |
      | **Összesítési műveletet** | Válassza ki **átlagos**. |

      [![Kijelölések nyomás definiálása](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Válassza ki **+ változó hozzáadása** , adja hozzá a következő változót.

   * Adja meg a hőmérséklet:

      | | |
      | --- | ---|
      | **Name (Név)** | Írja be a `Avg Temperature` (igen) kifejezést. |
      | **Érték** | Válassza ki **hőmérséklet (kétirányú)**. Vegye figyelembe, hogy ezt a mezőt kell feltöltenie az Azure Time Series Insights előzetes verziója a fogadott események indítása után néhány percet is igénybe vehet. |
      | **Összesítési műveletet** | Válassza ki **átlagos**.|

      [![Kijelölések hőmérséklet definiálása](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

   * Adja meg a páratartalommal:

      | | |
      | --- | ---|
      | **Name (Név)** | Adja meg `Max Humidity` |
      | **Érték** | Válassza ki **páratartalom (kétirányú)**. Vegye figyelembe, hogy ezt a mezőt kell feltöltenie az Azure Time Series Insights előzetes verziója a fogadott események indítása után néhány percet is igénybe vehet. |
      | **Összesítési műveletet** | Válassza ki **maximális**.|

      [![Kijelölések hőmérséklet definiálása](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

   Ezután kattintson a **Létrehozás** elemre.

1. Láthatja, hogy a hozzáadott típusa:

   [![A hozzáadott típusára vonatkozó adatok](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. A következő lépés, hogy a hierarchia hozzáadása. Az a **hierarchiák** szakaszban jelölje be **+ Hozzáadás**:

   [![Hierarchiák lap Hozzáadás gomb](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. Adja meg a hierarchiában. Töltse ki a mezőket a következő:

   | | |
   | --- | ---|
   | **Name (Név)** | Írja be a `Location Hierarchy` (igen) kifejezést. |
   | **1. szint** | Írja be a `Country` (igen) kifejezést. |
   | **2. szint** | Írja be a `City` (igen) kifejezést. |
   | **3. szint** | Írja be a `Building` (igen) kifejezést. |

   Ezután kattintson a **Létrehozás** elemre.

   [![A Létrehozás gombra hierarchia mezők](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

1. A hierarchia létrehozott tekintheti meg:

   [![A hierarchiára vonatkozó információk](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Válassza ki **példányok** a bal oldalon. Miután a példányok jelennek meg, az első példánynál, majd válassza ki és **szerkesztése**:

   [![A Szerkesztés gombra egy példány kiválasztása](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. A jobb oldalon jelenik meg egy szövegszerkesztőben. Adja meg a következő információkat:

   | | |
   | --- | --- |
   | **Típus** | Válassza ki **hűtő**. |
   | **Leírás** | Írja be a `Instance for Chiller-01.1` (igen) kifejezést. |
   | **Hierarchiák** | Válassza ki **hely hierarchiában**. |
   | **Ország** | Írja be a `USA` (igen) kifejezést. |
   | **Város** | Írja be a `Seattle` (igen) kifejezést. |
   | **Épület** | Írja be a `Space Needle` (igen) kifejezést. |

    Ezt követően válassza a **Mentés** lehetőséget.

   [![Szolgáltatáspéldány-mezők a Mentés gombra](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

1. Ismételje meg az előző lépést a többi érzékelő számára. Használja a következő mezőket:

   * A hűtő 01.2:

     | | |
     | --- | --- |
     | **Típus** | Válassza ki **hűtő**. |
     | **Leírás** | Írja be a `Instance for Chiller-01.2` (igen) kifejezést. |
     | **Hierarchiák** | Válassza ki **hely hierarchiában**. |
     | **Ország** | Írja be a `USA` (igen) kifejezést. |
     | **Város** | Írja be a `Seattle` (igen) kifejezést. |
     | **Épület** | Írja be a `Pacific Science Center` (igen) kifejezést. |

   * A hűtő 01.3:

     | | |
     | --- | --- |
     | **Típus** | Válassza ki **hűtő**. |
     | **Leírás** | Írja be a `Instance for Chiller-01.3` (igen) kifejezést. |
     | **Hierarchiák** | Válassza ki **hely hierarchiában**. |
     | **Ország** | Írja be a `USA` (igen) kifejezést. |
     | **Város** | Írja be a `New York` (igen) kifejezést. |
     | **Épület** | Írja be a `Empire State Building` (igen) kifejezést. |

1. Nyissa meg a **elemzés** lapra, és frissítse az oldalt. Bontsa ki az összes hierarchiaszintek idősorozatban található.

   [![Az elemzés lapon](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Ismerkedés a time series az elmúlt órában, módosítsa **Gyorshivatkozások** való **utolsó óra**:

    [![Gyorshivatkozások mezőbe, a kiválasztott elmúlt óra](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. Válassza ki az idősor alatt **csendes-óceáni adatelemzési Center** , és válassza ki **megjelenítése maximális páratartalom**.

    [![Kiválasztott idősorozat és maximális páratartalom Megjelenítés menü kiválasztása](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. Az idősor a **maximális páratartalom** egy időköz mérettel **1 perces** nyílik meg. Válassza ki a régiót, számos szűréséhez. Ezután kattintson a jobb gombbal, és válassza ki **nagyítás** elemezheti a időkereten belül események:

   [![Kijelölt tartomány nagyítás paranccsal a helyi menü](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Is válassza ki a régiót, és kattintson a jobb gombbal megtekintheti az esemény részleteit:

   [![Események részletes listája](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:  

> [!div class="checklist"]
> * Létrehozhat és használhat egy eszköz szimulálása gyorsító.
> * Az Azure Time Series Insights előzetes PAYG környezetet hozhat létre.
> * Csatlakozás az Azure Time Series Insights – előzetes környezet egy eseményközpontba.
> * A megoldás gyorsító minta futtatása az adatok streamelése az Azure Time Series Insights – előzetes környezetbe.
> * Az adatok egy alapszintű elemzéseket végezhet.
> * Egy Idősorozat-modell típusa és a hierarchia definiálja, és rendelje azokat a példányokat.

Most, hogy tudja, hogyan hozhat létre a saját Azure Time Series Insights – előzetes környezet, tudjon meg többet az Azure Time Series Insightsban kapcsolatos főbb fogalmakat.

További információ az Azure Time Series Insights tárolási konfigurációt:

> [!div class="nextstepaction"]
> [Az Azure Time Series Insights előzetes verziója storage és a bejövő forgalom](./time-series-insights-update-storage-ingress.md)

További tudnivalók a Time Series modellek:

> [!div class="nextstepaction"]
> [Az Azure Time Series Insights előzetes verziója az adatmodellezés](./time-series-insights-update-tsm.md)