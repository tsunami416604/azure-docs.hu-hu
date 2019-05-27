---
title: 'Oktatóanyag: Az Azure Time Series Insights – előzetes környezet beállítása |} A Microsoft Docs'
description: Ismerje meg, hogyan állítsa be a környezetet az Azure Time Series Insights előzetes verziója.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.custom: seodec18
ms.openlocfilehash: 77b7b90b63ffebc14498183fc179b9c8ae76a722
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237847"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Oktatóanyag: Az Azure Time Series Insights – előzetes környezet beállítása

Ez az oktatóanyag végigvezeti a használatalapú fizetés (Használatalapú) környezet Azure Time Series Insights – előzetes létrehozásának folyamatán. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

* Az Azure Time Series Insights – előzetes környezetet hozhat létre.
* Csatlakozás az Azure Time Series Insights – előzetes környezet az Azure Event Hubs-eseményközpontba.
* Egy megoldás gyorsító minta futtatása az adatok streamelése az Azure Time Series Insights – előzetes környezetbe.
* Alapvető adatok elemzését, a.
* Egy Idősorozat-modell típusa és a hierarchia definiálja, és társíthatja azt az üzemelő példányok.

## <a name="create-a-device-simulation"></a>Eszközszimuláció létrehozása

Ebben a szakaszban három szimulált eszközök, amelyek adatokat küldenek az Azure IoT Hub-példány létrehozása.

1. Nyissa meg a [Azure IoT-megoldás megoldásgyorsítók lapja](https://www.azureiotsolutions.com/Accelerators). Az oldal néhány előre létrehozott példa jeleníti meg. Jelentkezzen be az Azure-fiókjával. Ezután válassza ki **Eszközszimuláció**.

   [![Az Azure IoT-megoldás megoldásgyorsítók lapja](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Válassza ki **kipróbálása**.

1. Az a **Eszközszimuláció létrehozása megoldás** lapon, és állítsa a következő paraméterekkel:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Megoldás neve** | Adja meg egy egyedi értéket egy új erőforráscsoportot. A felsorolt Azure-erőforrások létrejönnek, és hozzá lesznek rendelve az erőforráscsoporthoz. |
    | **Előfizetés** | Válassza ki az előfizetést, amelyhez a Time Series Insights-környezet létrehozásához használt. |
    | **Régió** | Válassza ki a régiót, amelyben a Time Series Insights-környezet létrehozásához használt. |
    | **Választható Azure-erőforrások üzembe helyezése** | Hagyja a **az IoT Hub** jelölőnégyzet be van jelölve. A szimulált eszközök az IoT Hub használatával csatlakozhat, és adatokat. |
 
    Válassza ki **megoldás létrehozása**. Várjon 10 – 15 perc, a megoldás a telepítésre.

    [![Eszközszimuláció megoldás lap létrehozása](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. A megoldás gyorsító irányítópultján, válassza ki a **indítsa el a**:

    [![Az eszköz szimulálása megoldás indítása](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. A program átirányítja a **a Microsoft Azure IoT-eszköz szimulálása** lapot. Az oldal jobb felső sarkában válassza **új szimuláció**.

    [![Az Azure IoT-szimuláció lap](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. Az a **szimuláció telepítő** panelen adja meg a következő paraméterekkel:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Name (Név)** | Adjon meg egy egyedi nevet szimulátort. |
    | **Leírás** | Adjon meg definíciót. |
    | **Szimuláció időtartama** | Állítsa be **határozatlan idejű futásra**. |
    | **Eszközmodell** | **Név**: Adja meg **hűtő**. <br />**Összeg**: Adja meg **3**. |
    | **Cél IoT Hub** | Állítsa be **használja az előzetesen kiépített az IoT Hub**. |

    [![Paraméterek beállítása](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Válassza ki **szimuláció indítása**.

    Az eszköz szimulálása irányítópultján, vegye figyelembe a látható információk **aktív eszközök** és **üzenetek / másodperc**.

    [![Az Azure IoT-szimuláció irányítópult](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="list-device-simulation-properties"></a>Lista eszköz szimulálása tulajdonságai

Létrehoz egy Azure Time Series Insights-környezetet, mielőtt szüksége az IoT hub, az előfizetés és az erőforráscsoport nevét.

1. Nyissa meg a megoldás gyorsító irányítópultján. Azure-előfizetés ugyanazzal a fiókkal jelentkezzen be. Az eszközszimuláció, amelyet az előző szakaszban található.

1. A készülékszimulátort, majd válassza ki és **indítsa el a**. Az eszköz szimulátor megoldás gyorsító ablaktábláján a jobb oldalon, válassza ki a **Azure felügyeleti portálján** lehetőséget.

    [![Simulátor listaelemek](media/v2-update-provision/device-six-listings.png)](media/v2-update-provision/device-six-listings.png#lightbox)

1. Megjegyzés: az IoT hub, az előfizetés és erőforrás-csoport nevét.

    [![Az Azure portal eszköz szimulátor irányítópult részletei](media/v2-update-provision/device-eight-portal.png)](media/v2-update-provision/device-eight-portal.png#lightbox)

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Egy Time Series Insights előzetes PAYG környezet létrehozása

Ez a szakasz ismerteti, hogyan hozhat létre az Azure Time Series Insights – előzetes környezet használatával a [az Azure portal](https://portal.azure.com/).

1. Jelentkezzen be az Azure Portalra az előfizetéses fiókba.

1. Válassza ki **erőforrás létrehozása** > **IOT-**  > **Time Series Insights**.

   [![Válassza ki az eszközök internetes hálózatát, és válassza ki a Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. Az a **létrehozásához a Time Series Insights-környezet** ablaktáblán, a a **alapjai** lapra, és állítsa a következő paraméterekkel:

    | Paraméter | Műveletek |
    | --- | ---|
    | **Környezet neve** | Adja meg az Azure Time Series Insights – előzetes környezet egyedi nevét. |
    | **Előfizetés** | Adja meg az előfizetést, ahol szeretné létrehozni az Azure Time Series Insights – előzetes környezet. Ajánlott eljárás, hogy ugyanahhoz az előfizetéshez, az IoT-erőforrásokat a készülékszimulátort által létrehozott többi részétől. |
    | **Erőforráscsoport** | Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot, az Azure Time Series Insights – előzetes környezet erőforrás. Az erőforráscsoport az Azure-erőforrások tárolója. Ajánlott eljárás, hogy ugyanazt az erőforráscsoportot, a többi IoT erőforrást a készülékszimulátort által létrehozott. |
    | **Hely** | Válassza ki az Azure Time Series Insights – előzetes környezet egy adatközpont-régiót. Hozzáadott sávszélességgel kapcsolatos költségek és a késés elkerülése érdekében érdemes az Azure Time Series Insights – előzetes környezet létrehozásához és az egyéb IoT-erőforrások ugyanabban a régióban található. |
    | **Tier** |  Válassza ki **PAYG** (*utólagos elszámolású*). Ez a Termékváltozat az Azure Time Series Insights előzetes verziója a termékhez. |
    | **Tulajdonságazonosító** | Adjon meg egy értéket, amely egyedileg azonosítja a time series példány. Az érték azt adja meg a **Tulajdonságazonosító** mező nem módosítható. Később nem módosítható. A jelen oktatóanyag esetében írja be a **iothub-kapcsolat-eszközazonosító**. A Time Series azonosító kapcsolatos további információkért lásd: [ajánlott eljárások a Time Series ID kiválasztására vonatkozó](./time-series-insights-update-how-to-id.md). |
    | **Tárfiók neve** | Adjon meg egy új tárfiókot hozzon létre egy globálisan egyedi nevet. |
   
   Válassza ki **tovább: Eseményforrás**.

   [![Egy Time Series Insights-környezet létrehozása panel](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Az a **eseményforrás** lapra, és állítsa a következő paraméterekkel:

   | Paraméter | Műveletek |
   | --- | --- |
   | **Eseményforrás létrehozása?** | Válassza ki **Igen**.|
   | **Name (Név)** | Adja meg az eseményforrás nevének egy egyedi értéket. |
   | **Adatforrás típusa** | Válassza ki **az IoT Hub**. |
   | **Válassza ki a hub** | Válasszon **válasszon meglévő**. |
   | **Előfizetés** | Válassza ki az előfizetést, amelyhez az eszköz szimulátort használ. |
   | **Az IoT Hub nevét** | Válassza ki az IoT hub nevére, a készülékszimulátort létrehozott. |
   | **Az IoT Hub-hozzáférési házirend** | Válassza ki **iothubowner**. |
   | **Az IOT Hub fogyasztói csoport** | Válassza ki **új**, adjon meg egy egyedi nevet, és válassza **Hozzáadás**. A fogyasztói csoportot az Azure Time Series Insights – előzetes egyedi értéknek kell lennie. |
   | **Időbélyeg-tulajdonság** | Ez az érték azonosítására szolgál a **időbélyeg** a bejövő telemetria-adatok a tulajdonság. Ebben az oktatóanyagban hagyja üresen ezt a jelölőnégyzetet. A szimulátor használja az IoT hubról, alapértelmezett beállítása a Time Series Insights bejövő időbélyeg. |

   Válassza az **Áttekintés + létrehozás** lehetőséget.

   [![Eseményforrás konfigurálása](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Az a **felülvizsgálat + létrehozás** lapon tekintse át a beállításokat, és válassza ki **létrehozás**.

    [![Felülvizsgálat + létrehozás oldalon, a Létrehozás gombra](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    A központi telepítés állapotát tekintheti meg:

    [![Értesítés, hogy a telepítés befejeződött](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Ha a bérlő saját érheti el az Azure Time Series Insights – előzetes környezet. Győződjön meg arról, hogy rendelkezik hozzáféréssel,:

   1. Keresse meg az erőforráscsoportot, és válassza ki az Azure Time Series Insights – előzetes környezet:

      [![Kiválasztott környezet](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Az Azure Time Series Insights – előzetes oldalon válassza ki a **az adathozzáférési házirendek**:

      [![Az adathozzáférési házirendek](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Győződjön meg arról, hogy szerepel-e a hitelesítő adatait:

      [![Listán szereplő hitelesítő adatok](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Ha nem jelenik meg a hitelesítő adatait, engedélyt kell saját kezűleg a környezet eléréséhez. További információt az engedélyek beállításáról, olvassa el [adathozzáférés](./time-series-insights-data-access.md).

## <a name="analyze-data-in-your-environment"></a>A környezetben lévő adatok elemzése

Ebben a szakaszban alapszintű elemzési hajt végre az idősorozat-adatok használatával a [Azure Time Series Insights – előzetes explorer](./time-series-insights-update-explorer.md).

1. Az URL-címet az erőforrás-oldalon válassza ki az Azure Time Series Insights – előzetes Explorerben nyissa meg a [az Azure portal](https://portal.azure.com/).

    [![A Time Series Insights előzetes verziója explorer URL-címe](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. A Explorerben válassza ki a **idő sorozat példányai** csomópont a környezet az Azure Time Series Insights – előzetes példányok megtekintéséhez.

    [![Fölérendelt objektum nélküli-példányok listájának](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Válassza ki az első alkalommal sorozat. Ezután válassza ki **nyomás megjelenítése**.

    [![Time series példány átlagos nyomás megjeleníthető menü paranccsal kiválasztva](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Time series diagram jelenik meg. Módosítsa a **időköz** való **15 mp**.

    [![Adatsorozat idődiagram](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Ismételje meg a műveletet 3. lépés-a másik kettőt idő sorozat példányai. Minden alkalommal sorozat példányai, tekintheti meg ezen a diagramon látható módon:

    [![Az összes idősorozat diagram](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Az a **időkeretre** lehetőséget a box, a time series trendek megtekintéséhez az utolsó egy órára időtartomány módosítása:

    [![Az időtartomány beállítása egy órára](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definiálása és alkalmazása a modell

Ebben a szakaszban egy modellt az adatok szerkezetének vonatkoznak. Fejezze be a modell, típusok, hierarchiákat és példányok megadása. Adatmodellezés kapcsolatos további információkért lásd: [Idősorozat-modell](./time-series-insights-update-tsm.md).

1. A Explorerben válassza ki a **modell** lapon:

   [![Az Explorer modell lap](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Válassza ki **Hozzáadás** típus:

   [![A Hozzáadás gombra típusok](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Ezt követően adja meg a típus mindhárom változóval: *nyomás*, *hőmérséklet*, és *páratartalom*. Az a **-típus hozzáadása** panelen adja meg a következő paraméterekkel:

    | Paraméter | Műveletek |
    | --- | ---|
    | **Name (Név)** | Adja meg **hűtő**. |
    | **Leírás** | Adja meg **Ez a típus definíciójának hűtő**. |

   * Meghatározásához *nyomás*alatt **változók**, állítsa be a következő paraméterekkel:

     | Paraméter | Műveletek |
     | --- | ---|
     | **Name (Név)** | Adja meg **átlagos nyomás**. |
     | **Érték** | Válassza ki **nyomás (kétirányú)** . Néhány percet igénybe vehet **érték** az Azure Time Series Insights előzetes verziója a fogadott események elindulása után automatikusan kitöltve. |
     | **Összesítési műveletet** | Válassza ki **átlagos**. |

      [![Kijelölések nyomás definiálása](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Adja hozzá a következő változót, jelölje be **változó hozzáadása**.

   * Definiálása *hőmérséklet*:

     | Paraméter | Műveletek |
     | --- | ---|
     | **Name (Név)** | Adja meg **átlagos hőmérsékletét**. |
     | **Érték** | Válassza ki **hőmérséklet (kétirányú)** . Néhány percet igénybe vehet **érték** az Azure Time Series Insights előzetes verziója a fogadott események elindulása után automatikusan kitöltve. |
     | **Összesítési műveletet** | Válassza ki **átlagos**.|

      [![Kijelölések hőmérséklet definiálása](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      Adja hozzá a következő változót, jelölje be **változó hozzáadása**.

   * Definiálása *páratartalom*:

      | | |
      | --- | ---|
      | **Name (Név)** | Adja meg **maximális nedvességtartalma** |
      | **Érték** | Válassza ki **páratartalom (kétirányú)** . Néhány percet igénybe vehet **érték** az Azure Time Series Insights előzetes verziója a fogadott események elindulása után automatikusan kitöltve. |
      | **Összesítési műveletet** | Válassza ki **maximális**.|

      [![Kijelölések hőmérséklet definiálása](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    Kattintson a **Létrehozás** gombra.

    Láthatja, hogy a hozzáadott típusa:

    [![A hozzáadott típusára vonatkozó adatok](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. A következő lépés, hogy a hierarchia hozzáadása. A **hierarchiák**válassza **Hozzáadás**:

    [![Hierarchiák lap Hozzáadás gomb](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. Az a **hierarchia szerkesztése** panelen adja meg a következő paraméterekkel:

   | Paraméter | Műveletek |
   | --- | ---|
   | **Name (Név)** | Adja meg **hely hierarchiában**. |
   | **1. szint** | Adja meg **ország**. |
   | **2. szint** | Adja meg **Város**. |
   | **3. szint** | Adja meg **épület**. |

   Kattintson a **Mentés** gombra.

    [![A Létrehozás gombra hierarchia mezők](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   A hierarchia létrehozott tekintheti meg:

    [![A hierarchiára vonatkozó információk](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Válassza ki **példányok**. Az első példánynál, majd válassza ki és **szerkesztése**:

    [![A Szerkesztés gombra egy példány kiválasztása](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. Az a **példányok szerkesztése** panelen adja meg a következő paraméterekkel:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Típus** | Válassza ki **hűtő**. |
    | **Leírás** | Adja meg **hűtő-01.1-példány**. |
    | **Hierarchiák** | Válassza ki **hely hierarchiában**. |
    | **Ország** | Adja meg **USA**. |
    | **Város** | Adja meg **Seattle**. |
    | **Épület** | Adja meg **tű Címterét**. |

    [![A Mentés gombra a szolgáltatáspéldány-mezők](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   Kattintson a **Mentés** gombra.

1. Ismételje meg az előző lépést a többi érzékelő számára. Frissítse a következő értékeket:

   * A hűtő 01.2:

     | Paraméter | Műveletek |
     | --- | --- |
     | **Típus** | Válassza ki **hűtő**. |
     | **Leírás** | Adja meg **hűtő-01.2-példány**. |
     | **Hierarchiák** | Válassza ki **hely hierarchiában**. |
     | **Ország** | Adja meg **USA**. |
     | **Város** | Adja meg **Seattle**. |
     | **Épület** | Adja meg **csendes-óceáni adatelemzési Center**. |

   * A hűtő 01.3:

     | Paraméter | Műveletek |
     | --- | --- |
     | **Típus** | Válassza ki **hűtő**. |
     | **Leírás** | Adja meg **hűtő-01.3-példány**. |
     | **Hierarchiák** | Válassza ki **hely hierarchiában**. |
     | **Ország** | Adja meg **USA**. |
     | **Város** | Adja meg **New York-i**. |
     | **Épület** | Adja meg **Empire állapot épület**. |

1. Válassza ki a **elemzés** lapra, és ezután frissítse a lapot. A **hely hierarchiában**, bontsa ki a time series példányok megjeleníthető az összes hierarchiaszintek:

   [![Az elemzés lapon](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Ismerkedés a time series példányok az elmúlt órában, módosítsa **Gyorshivatkozások** való **utolsó óra**:

    [![Gyorshivatkozások mezőbe, a kiválasztott elmúlt óra](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. A **csendes-óceáni adatelemzési Center**, válassza ki a time series-példányt, és válassza **megjelenítése maximális páratartalom**.

    [![Kiválasztott idő sorozatú példányok és a maximális páratartalom Megjelenítés menü kiválasztása](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. Az idősor a **maximális páratartalom** egy időköz mérettel **1 perces** nyílik meg. Számos szűrési, válasszon ki egy régiót. Események az időkereten belül elemzése, kattintson a jobb gombbal a diagramra, és válassza **nagyítás**:

   [![Kijelölt tartomány nagyítás paranccsal a helyi menü](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Esemény részleteinek megtekintéséhez válassza ki a régiót, és kattintson a jobb gombbal a diagram:

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