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
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 1b09c0e31b217d7d67f936aefe9045d190241389
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633113"
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

   ![Az Azure IoT-megoldás megoldásgyorsítók lapja][1]

   Válassza ki **kipróbálása**.

1. Adja meg a szükséges paramétereket az **Eszközszimulációs megoldás létrehozása** oldalon:

   | Paraméter | Leírás |
   | --- | --- |
   | **Megoldás neve** |    Adjon meg egy egyedi értéket, egy új erőforráscsoport létrehozásához. A felsorolt Azure-erőforrások létrejönnek, és hozzá lesznek rendelve az erőforráscsoporthoz. |
   | **Előfizetés** | Adja meg a Time Series Insights-környezet létrehozásához használt ugyanahhoz az előfizetéshez. |
   | **Régió** |   Adja meg a Time Series Insights-környezet létrehozásakor használható ugyanabban a régióban. |
   | **Választható Azure-erőforrások üzembe helyezése**    | Hagyja kiválasztva, az IoT Hub, mert csatlakoztatása és adatok streamelése a szimulált eszközök fogja használni. |

   Ezután válassza ki **megoldás létrehozása**. Várjon 10 – 15 perc, a megoldás a telepítésre.

   ![Eszközszimuláció megoldás lap létrehozása][2]

1. A megoldás gyorsító irányítópultján, válassza ki a **indítsa el a** gombra:

   ![Az eszköz szimulálása megoldás indítása][3]

1. A program átirányítja a **a Microsoft Azure IoT-eszköz szimulálása** lapot. Válassza ki **+ új szimuláció** , az oldal jobb felső.

   ![Az Azure IoT-szimuláció lap][4]

1.  Adja meg a szükséges paramétereket a következőképpen:

    ![Töltse ki a paramétereket][5]

    |||
    | --- | --- |
    | **Name (Név)** | Adjon meg egy egyedi nevet szimulátort. |
    | **Leírás** | Adjon meg definíciót. |
    | **Szimuláció időtartama** | Állítsa be **határozatlan idejű futásra**. |
    | **Eszközmodell** | **Név**: Adja meg **hűtő**. </br>**Összeg**: Adja meg **3**. |
    | **Cél IoT Hub** | Állítsa be **használja az előzetesen kiépített az IoT Hub**. |

    Ezután válassza ki **szimuláció indítása**.

1. Eszköz szimulálása irányítópultján látható **aktív eszközök** és **üzenetek / másodperc**.

    ![Az Azure IoT-szimuláció irányítópult][6]

## <a name="list-device-simulation-properties"></a>Lista eszköz szimulálása tulajdonságai

Létrehoz egy Azure Time Series Insights-környezetet, mielőtt szüksége az IoT hub, az előfizetés és az erőforráscsoport nevét.

1. Nyissa meg a megoldás gyorsító irányítópultján, és jelentkezzen be Azure-előfizetés ugyanazzal a fiókkal. Keresse meg az eszköz-szimuláció, amely az előző lépésekben létrehozott.

1. Válassza ki a készülékszimulátort, majd **indítsa el a**. Válassza ki a **Azure felügyeleti portálján** hivatkozásra a jobb oldalon.

    ![Simulátor listaelemek][7]

1. Jegyezze fel az IoT hub, az előfizetés és erőforráscsoport-nevekkel.

    ![Azure Portal][8]

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Egy Time Series Insights előzetes PAYG környezet létrehozása

Ez a szakasz ismerteti, hogyan hozhat létre az Azure Time Series Insights – előzetes környezet használatával a [az Azure portal](https://portal.azure.com/).

1. Jelentkezzen be az Azure Portalra az előfizetéses fiókba.

1. Válassza ki **erőforrás létrehozása**.

1. Válassza ki a **IOT-** kategóriát, és válassza ki **Time Series Insights**.

   ![Válassza ki az eszközök internetes hálózatát, és válassza ki a Time Series Insights][9]

1. Töltse ki a mezőket a lapon a következő:

   | | |
   | --- | ---|
   | **Környezet neve** | Válassza ki az Azure Time Series Insights – előzetes környezet egyedi nevét. |
   | **Előfizetés** | Adja meg az előfizetést, ahol szeretné létrehozni az Azure Time Series Insights – előzetes környezet. Akkor célszerű használni az IoT-erőforrások a készülékszimulátort által létrehozott többi ugyanahhoz az előfizetéshez. |
   | **Erőforráscsoport** | Az erőforráscsoport az Azure-erőforrások tárolója. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy új, az Azure Time Series Insights – előzetes környezet erőforrás. Fontos ajánlott eljárás az, mint a többi az IoT-erőforrások hozta létre a készülékszimulátort ugyanazt az erőforráscsoportot használja. |
   | **Hely** | Válassza ki az Azure Time Series Insights – előzetes környezet egy adatközpont-régiót. A hozzáadott sávszélességgel kapcsolatos költségek és a késés elkerülése érdekében a legjobb, ha az Azure Time Series Insights – előzetes környezet egyéb IoT-erőforrások ugyanabban a régióban. |
   | **Réteg** |  Válassza ki **PAYG**, amely a használatalapú fizetés rövidítése. Ez a Termékváltozat az Azure Time Series Insights előzetes verziója a termékhez. |
   | **Tulajdonságazonosító** | Adja meg, amely egyedileg azonosítja a time series valami. Vegye figyelembe, hogy ez a mező nem módosítható, és később nem módosítható. A jelen oktatóanyag esetében használja **iothub-kapcsolat-eszközazonosító**. A Time Series azonosító kapcsolatos további információkért olvassa el [kiválasztása a Time Series ID](./time-series-insights-update-how-to-id.md). |
   | **Tárfiók neve** | Adjon meg egy globálisan egyedi nevet a létrehozandó új storage-fiókot. |

   Ezután válassza ki **tovább: Eseményforrás**.

   ![Egy Time Series Insights-környezet létrehozására szolgáló oldal][10]

1. A lapon az esemény forrását a következőképpen töltse ki a mezőket:

   | | |
   | --- | --- |
   | **Eseményforrás létrehozása?** | Adja meg **Igen**.|
   | **Name (Név)** | Adjon meg egy egyedi érték, amellyel az eseményforrás neve.|
   | **Adatforrás típusa** | Adja meg **az IoT Hub**. |
   | **Válassza ki a hub?** | Adja meg **válasszon meglévő**. |
   | **Előfizetés** | Adja meg az előfizetést, amelyhez az eszköz szimulátort használ. |
   | **Az IoT Hub nevét** | Adja meg az IoT hub nevére, amelyet a készülékszimulátort létrehozott. |
   | **Az IoT Hub-hozzáférési házirend** | Adja meg **iothubowner**. |
   | **Az IOT Hub fogyasztói csoport** | Az Azure Time Series Insights előzetes verziója egy egyedi felhasználói csoport van szükség. Válassza ki **új**, adjon meg egy egyedi nevet, és válassza **Hozzáadás**. |
   | **Időbélyeg-tulajdonság** | Ez a mező az időbélyeg-tulajdonság a beérkező telemetriai adatokat az azonosítására szolgál. A jelen oktatóanyag esetében ne adja meg a mezőben. A szimulátor használja az IoT hubról, alapértelmezett beállítása a Time Series Insights bejövő időbélyeg.|

   Ezután válassza ki **felülvizsgálat + létrehozása**.

   ![Eseményforrás beállítására szolgáló oldal][13]

1. Tekintse át az Áttekintés lap összes mezőt, és válassza ki **létrehozás**.

   ![Felülvizsgálat + létrehozás oldalon, a Létrehozás gombra][14]

1. Láthatja, hogy a központi telepítés állapotát.

   ![Értesítés, hogy a telepítés befejeződött][15]

1. Hozzáférést kell kapnia az Azure Time Series Insights – előzetes környezethez, ha a bérlő saját. Győződjön meg arról, hogy rendelkezik hozzáféréssel,:

   a. Keresse meg az erőforráscsoport, és válassza ki azt az Azure Time Series Insights – előzetes környezetet:

      ![Kiválasztott környezet][16]

   b. Lépjen az Azure Time Series Insights – előzetes oldalon **az adathozzáférési házirendek**.

     ![Adathozzáférési házirendek][17]

   c. Győződjön meg arról, hogy szerepel-e a hitelesítő adatait.

     ![Listán szereplő hitelesítő adatok][18]

   Ha nem jelennek meg a hitelesítő adatait, hogy a környezet hozzáférési engedélyt adjon magának. További információt az engedélyek beállításáról, olvassa el [adathozzáférés](./time-series-insights-data-access.md).

## <a name="analyze-data-in-your-environment"></a>A környezetben lévő adatok elemzése

Ebben a szakaszban alapszintű elemzési hajt végre az idősorozat-adatok használatával a [Azure Time Series Insights – előzetes explorer](./time-series-insights-update-explorer.md).

1. Az URL-címet az erőforrás-oldalon válassza ki az Azure Time Series Insights – előzetes Explorerben nyissa meg a [az Azure portal](https://portal.azure.com/).

   ![A Time Series Insights előzetes verziója explorer URL-címe][19]

1. A Explorerben válassza ki a **fölérendelt objektum nélküli példányok** csomópont a környezet az Azure Time Series Insights – előzetes példányok megtekintéséhez.

   ![Fölérendelt objektum nélküli-példányok listájának][20]

1. Az idősor látható válassza ki az első példánynál. Ezután válassza ki **megjelenítése átlagos nyomás**.

   ![Az átlagos nyomás megjeleníthető menü paranccsal kiválasztott példány][21]

   Time series diagram meg kell jelennie a jobb oldalon:

   ![Adatsorozat idődiagram][22]

1. Ismételje meg a műveletet 3. lépés a másik kettő a time series. Az összes idősorozat, majd tekintheti meg ezen a diagramon látható módon:

   ![Az összes idősorozat diagram][23]

1. Módosítsa a time series trendek megtekintéséhez az elmúlt órában az időtartományt. 

   a. Válassza ki a **a** beállítás ki:

      ![A From beállítás][24]

   b. A mező kiválasztásával megjelenítheti az események az elmúlt órában az idő módosítása:

      ![Idő módosítása][25]

1. Az utolsó egy órára összes három eszközön ezután összehasonlíthatja nyomás:

   ![Három eszközön összehasonlítása][26]

## <a name="define-and-apply-a-model"></a>Definiálása és alkalmazása a modell

Ebben a szakaszban egy modellt az adatok szerkezetének vonatkoznak. Fejezze be a modell, típusok, hierarchiákat és példányok megadása. További információ az adatmodellezés, lépjen a [Idősorozat-modell](./time-series-insights-update-tsm.md).

1. A Explorerben válassza ki a **modell** lapon:

   ![Az Explorer modell lap][27]

1. Válassza ki **+ Hozzáadás** típus. A jobb oldalon megnyílik egy típus szerkesztő.

   ![A Hozzáadás gombra típusok][28]

1. A típus három változókat határozhat meg: nyomás hőmérsékleti és páratartalom. Adja meg a következő információkat:

   | | |
   | --- | ---|
   | **Name (Név)** | Adja meg **hűtő**. |
   | **Leírás** | Adja meg **Ez a típus definíciójának hűtő**. |

   * Adja meg a nyomás három változókkal:

      | | |
      | --- | ---|
      | **Name (Név)** | Adja meg **átlagos nyomás**. |
      | **Érték** | Válassza ki **nyomás (kétirányú)**. Vegye figyelembe, hogy ezt a mezőt kell feltöltenie az Azure Time Series Insights előzetes verziója a fogadott események indítása után néhány percet is igénybe vehet. |
      | **Összesítési műveletet** | Válassza ki **átlagos**. |

      ![Kijelölések nyomás definiálása][29]

      Válassza ki **+ változó hozzáadása** , adja hozzá a következő változót.

   * Adja meg a hőmérséklet:

      | | |
      | --- | ---|
      | **Name (Név)** | Adja meg **átlagos hőmérsékletét**. |
      | **Érték** | Válassza ki **hőmérséklet (kétirányú)**. Vegye figyelembe, hogy ezt a mezőt kell feltöltenie az Azure Time Series Insights előzetes verziója a fogadott események indítása után néhány percet is igénybe vehet. |
      | **Összesítési műveletet** | Válassza ki **átlagos**.|

      ![Kijelölések hőmérséklet definiálása][30]

   * Adja meg a páratartalommal:

      | | |
      | --- | ---|
      | **Name (Név)** | Adja meg **maximális páratartalom**. |
      | **Érték** | Válassza ki **páratartalom (kétirányú)**. Vegye figyelembe, hogy ezt a mezőt kell feltöltenie az Azure Time Series Insights előzetes verziója a fogadott események indítása után néhány percet is igénybe vehet. |
      | **Összesítési műveletet** | Válassza ki **maximális**.|

      ![Kijelölések hőmérséklet definiálása][31]

   Ezután kattintson a **Létrehozás** elemre.

1. Láthatja, hogy a hozzáadott típusa:

   ![A hozzáadott típusára vonatkozó adatok][32]

1. A következő lépés, hogy a hierarchia hozzáadása. Az a **hierarchiák** szakaszban jelölje be **+ Hozzáadás**:

   ![Hierarchiák lap Hozzáadás gomb][33]

1. Adja meg a hierarchiában. Töltse ki a mezőket a következő:

   | | |
   | --- | ---|
   | **Name (Név)** | Adja meg **hely hierarchiában**. |
   | **1. szint** | Adja meg **ország**. |
   | **2. szint** | Adja meg **Város**. |
   | **3. szint** | Adja meg **épület**. |

   Ezután kattintson a **Létrehozás** elemre.

   ![A Létrehozás gombra hierarchia mezők][34]

1. A hierarchia létrehozott tekintheti meg:

   ![A hierarchiára vonatkozó információk][35]

1. Válassza ki **példányok** a bal oldalon. Miután a példányok jelennek meg, az első példánynál, majd válassza ki és **szerkesztése**:

   ![A Szerkesztés gombra egy példány kiválasztása][36]

1. A jobb oldalon jelenik meg egy szövegszerkesztőben. Adja meg a következő információkat:

   | | |
   | --- | --- |
   | **Típus** | Válassza ki **hűtő**. |
   | **Leírás** | Adja meg **hűtő-01.1-példány**. |
   | **Hierarchiák** | Engedélyezése **hely hierarchiában**. |
   | **Ország** | Adja meg **USA**. |
   | **Város** | Adja meg **Seattle**. |
   | **Épület** | Adja meg **tű Címterét**. |

    Ezt követően válassza a **Mentés** lehetőséget.

   ![Szolgáltatáspéldány-mezők a Mentés gombra][37]

1. Ismételje meg az előző lépést a többi érzékelő számára. Használja a következő mezőket:

   * A hűtő 01.2:

     | | |
     | --- | --- |
     | **Típus** | Válassza ki **hűtő**. |
     | **Leírás** | Adja meg **hűtő-01.2-példány**. |
     | **Hierarchiák** | Engedélyezése **hely hierarchiában**. |
     | **Ország** | Adja meg **USA**. |
     | **Város** | Adja meg **Seattle**. |
     | **Épület** | Adja meg **csendes-óceáni adatelemzési Center**. |

   * A hűtő 01.3:

     | | |
     | --- | --- |
     | **Típus** | Válassza ki **hűtő**. |
     | **Leírás** | Adja meg **hűtő-01.1-példány**. |
     | **Hierarchiák** | Engedélyezése **hely hierarchiában**. |
     | **Ország** | Adja meg **USA**. |
     | **Város** | Adja meg **New York-i**. |
     | **Épület** | Adja meg **Empire állapot épület**. |

1. Nyissa meg a **elemzés** lapra, és frissítse az oldalt. Bontsa ki az összes hierarchiaszintek idősorozatban található.

   ![Az elemzés lapon][38]

1. Ismerkedés a time series az elmúlt órában, módosítsa **Gyorshivatkozások** való **utolsó óra**:

   ![Gyorshivatkozások mezőbe, a kiválasztott elmúlt óra][39]

1. Válassza ki az idősor alatt **csendes-óceáni adatelemzési Center** , és válassza ki **megjelenítése maximális páratartalom**.

   ![Kiválasztott idősorozat és maximális páratartalom Megjelenítés menü kiválasztása][40]

1. Az idősor a **maximális páratartalom** időközzel mérete 1 perces nyílik meg. Válassza ki a régiót, számos szűréséhez. Ezután kattintson a jobb gombbal, és válassza ki **nagyítás** elemezheti a időkereten belül események:

   ![Kijelölt tartomány nagyítás paranccsal a helyi menü][41]

1. Is válassza ki a régiót, és kattintson a jobb gombbal megtekintheti az esemény részleteit:

   ![Események részletes listája][44]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:  

* Létrehozhat és használhat egy eszköz szimulálása gyorsító.
* Az Azure Time Series Insights előzetes PAYG környezetet hozhat létre.
* Csatlakozás az Azure Time Series Insights – előzetes környezet egy eseményközpontba.
* A megoldás gyorsító minta futtatása az adatok streamelése az Azure Time Series Insights – előzetes környezetbe.
* Az adatok egy alapszintű elemzéseket végezhet.
* Egy Idősorozat-modell típusa és a hierarchia definiálja, és rendelje azokat a példányokat.

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
[6]: media/v2-update-provision/device-seven-dashboard.png
[7]: media/v2-update-provision/device-six-listings.png
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