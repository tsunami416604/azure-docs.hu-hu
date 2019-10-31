---
title: 'Oktatóanyag: Azure Time Series Insights előzetes verziójú környezet beállítása | Microsoft Docs'
description: Ismerje meg, hogyan állíthatja be a környezetet Azure Time Series Insights előzetes verzióban.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 10/22/2019
ms.custom: seodec18
ms.openlocfilehash: 840280eecce60ac74514404497b857cd5362c9a0
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171647"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Oktatóanyag: Azure Time Series Insights előzetes verziójú környezet beállítása

Ez az oktatóanyag végigvezeti a Azure Time Series Insights előzetes utólagos elszámolású (TB) környezet létrehozásának folyamatán. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Time Series Insights előnézeti környezet létrehozása.
> * Csatlakoztathatja a Azure Time Series Insights előnézet környezetet egy Azure-beli Event hub-Event Hubs.
> * Egy megoldás-gyorsító minta futtatásával továbbíthatja az adatátvitelt a Azure Time Series Insights előzetes verziójának környezetében.
> * Végezze el az adatok alapszintű elemzését.
> * Definiáljon egy idősorozat-modell típusát és hierarchiáját, és társítsa azt a példányokhoz.

>[!TIP]
> A [IoT megoldás-gyorsítók](https://www.azureiotsolutions.com/Accelerators) olyan nagyvállalati szintű előre konfigurált megoldásokat biztosítanak, amelyek segítségével felgyorsíthatja az egyéni IoT-megoldások fejlesztését.

Ha még nem rendelkezik ilyennel, regisztráljon egy [ingyenes Azure-előfizetésre](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

* Az Azure bejelentkezési fiókjának az előfizetés **tulajdonosi** szerepkörének is tagja kell lennie. További információ: [hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel és a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Eszközszimuláció létrehozása

Ebben a szakaszban három szimulált eszközt fog létrehozni, amelyek az Azure IoT Hub-példányra küldenek adatküldést.

1. Nyissa meg az [Azure IoT megoldás-gyorsítók lapot](https://www.azureiotsolutions.com/Accelerators). Az oldalon számos előre összeépített példa látható. Jelentkezzen be az Azure-fiókjával. Ezután válassza az **eszköz szimulálása**elemet.

   [![Azure IoT-megoldás gyorsító lapja](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Válassza a **kipróbálás most**lehetőséget.

1. Az **eszköz-szimulációs megoldás létrehozása** lapon adja meg a következő paramétereket:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Központi telepítés neve** | Adjon meg egyedi értéket egy új erőforráscsoport számára. A felsorolt Azure-erőforrások létrejönnek, és hozzá lesznek rendelve az erőforráscsoporthoz. |
    | **Azure-előfizetés** | Válassza ki azt az előfizetést, amelyben létre szeretné hozni a Time Series Insights-környezetét. |
    | **Azure-beli hely** | Válassza ki azt a régiót, amelyet a Time Series Insights környezet létrehozásához használt. |
    | **Üzembe helyezési beállítások** | Válassza az **új IoT hub kiépítése**lehetőséget. |
 
    Válassza a **megoldás létrehozása**lehetőséget. Akár 20 percet is igénybe vehet, amíg a megoldás befejezi a telepítést.

    [![eszköz-szimulációs megoldás oldalának létrehozása](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Előzetes verziójú TB-környezet létrehozása

Ez a szakasz azt ismerteti, hogyan hozhat létre Azure Time Series Insights előnézeti környezetet, és hogyan csatlakoztathatja a IoT-megoldás-gyorsító által létrehozott IoT hub-hoz a [Azure Portal](https://portal.azure.com/)használatával.

1. Jelentkezzen be a Azure Portalba az előfizetési fiók használatával.

1. Válassza **az erőforrás létrehozása**  > **eszközök internetes hálózata**  > **Time Series Insights**lehetőséget.

   [![válassza a eszközök internetes hálózata lehetőséget, majd válassza a Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. Az **Time Series Insights környezet létrehozása** ablaktábla **alapok** lapján adja meg a következő paramétereket:

    | Paraméter | Műveletek |
    | --- | ---|
    | **Környezet neve** | Adjon egyedi nevet a Azure Time Series Insights előnézeti környezetnek. |
    | **Előfizetés** | Adja meg azt az előfizetést, amelyben létre szeretné hozni a Azure Time Series Insights előnézeti környezetet. Az ajánlott eljárás az, hogy ugyanazt az előfizetést használja, mint az eszköz-szimulátor által létrehozott többi IoT-erőforrást. |
    | **Erőforráscsoport** | Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot a Azure Time Series Insights előnézet környezeti erőforráshoz. Az erőforráscsoport az Azure-erőforrások tárolója. Az ajánlott eljárás az, hogy ugyanazt az erőforráscsoportot használja, mint az eszköz-szimulátor által létrehozott többi IoT-erőforrást. |
    | **Hely** | Válasszon ki egy adatközpont-régiót a Azure Time Series Insights előzetes verziójának környezetéhez. A további késés elkerülése érdekében érdemes létrehozni a Azure Time Series Insights előnézeti környezetet ugyanabban a régióban, mint a többi IoT-erőforrást. |
    | **Tier** |  Válassza*a* **TB** (utólagos elszámolású) lehetőséget. Ez a Azure Time Series Insights előzetes termékhez tartozó SKU. |
    | **Tulajdonság azonosítója** | Adjon meg egy értéket, amely egyedileg azonosítja a Time Series-példányt. A **tulajdonság azonosítója** mezőben megadott érték nem változtatható, és később nem módosítható. Ebben az oktatóanyagban adja meg a **iothub--kapcsolatok-Device-ID**értéket. Az idősorozat-AZONOSÍTÓval kapcsolatos további információkért lásd: [ajánlott eljárások idősorozat-azonosító kiválasztásához](./time-series-insights-update-how-to-id.md). |
    | **Storage-fiók neve** | Adjon meg egy globálisan egyedi nevet a hűtőházi tárolóhoz tartozó új Storage-fiókhoz.|
    |**Storage-fiók replikálása**|Válassza ki a vállalkozása számára megfelelő replikációs stratégiát. A különböző replikációs stratégiákkal kapcsolatos további információkért lásd: [Azure Storage redundancia](../storage/common/storage-redundancy.md). |
    |**Meleg tároló engedélyezése**|Hozzon létre egy opcionálisan meleg tárolót. A szolgáltatás bármikor törölhető. A meleg áruház lehetővé teszi, hogy gyorsabb, korlátlan számú lekérdezést biztosítson a legutóbbi (akár az elmúlt 31 napban) adathoz.  |
    |**Adatmegőrzés (nap)**|Válassza ki az adatmegőrzést a meleg tárolóban. Az alapértelmezett érték 7 nap, és akár 31 nap meghosszabbítható |
   
   Válassza a Next (tovább) lehetőséget **: eseményforrás**.

   [Time Series Insights környezet létrehozásához ![panel](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Az **eseményforrás** lapon adja meg a következő paramétereket:

   | Paraméter | Műveletek |
   | --- | --- |
   | **Létrehoz egy eseményforrás?** | Válassza az **Igen** lehetőséget.|
   | **Name (Név)** | Adjon meg egyedi értéket az eseményforrás neveként. |
   | **Forrás típusa** | Válassza a **IoT hub**lehetőséget. |
   | **Válasszon hubot** | Válassza a **meglévő kiválasztása**lehetőséget. |
   | **Előfizetés** | Válassza ki az eszköz-szimulátorhoz használt előfizetést. |
   | **IoT Hub neve** | Válassza ki az eszköz-szimulátorhoz létrehozott IoT hub-nevet. |
   | **Hozzáférési szabályzat IoT Hub** | Válassza a **iothubowner**lehetőséget. |
   | **IoT Hub fogyasztói csoport** | Válassza az **új**lehetőséget, adjon meg egy egyedi nevet, majd kattintson a **Hozzáadás**gombra. A fogyasztói csoportnak egyedi értéknek kell lennie Azure Time Series Insights előzetes verzióban. |
   | **Timestamp tulajdonság** | Ezzel az értékkel azonosítható a bejövő telemetria adataiban található **timestamp** tulajdonság. Ebben az oktatóanyagban hagyja üresen ezt a mezőt. Ez a szimulátor a IoT Hub bejövő időbélyegét használja, amely Time Series Insights alapértelmezett értéke. |

   Válassza az **Áttekintés + létrehozás** lehetőséget.

   [Eseményforrás konfigurálása ![](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. A **felülvizsgálat + létrehozás** lapon tekintse át a beállításokat, majd válassza a **Létrehozás**lehetőséget.

    [![felülvizsgálat + Létrehozás lap létrehozás gombja](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Az üzemelő példány állapotát a következő helyen tekintheti meg:

    [![az üzembe helyezést elvégező értesítés](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Ha Ön a bérlő tulajdonosa, hozzáférhet a Azure Time Series Insights előnézeti környezetéhez. Győződjön meg arról, hogy rendelkezik hozzáféréssel a következőhöz:

   1. Keresse meg az erőforráscsoportot, majd válassza ki a Azure Time Series Insights előnézet környezetét:

      [![kiválasztott környezet](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. A Azure Time Series Insights előnézet oldalon válassza az **adatelérési házirendek**lehetőséget:

      [adat![adatelérési házirendek](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Ellenőrizze, hogy a hitelesítő adatok szerepelnek-e a listáján:

      [![felsorolt hitelesítő adatok](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Ha a hitelesítő adatai nem szerepelnek a felsorolásban, meg kell adnia magadnak a környezet eléréséhez szükséges engedélyt. Ha többet szeretne megtudni az engedélyek beállításáról, olvassa el az [adathozzáférés engedélyezése](./time-series-insights-data-access.md)című témakört.

## <a name="stream-data"></a>Adatok streamelése

Most, hogy üzembe helyezte a Time Series Insights-környezetet, adatfolyamként továbbítja azokat az elemzéshez.

1. Váltson vissza az [Azure IoT megoldás-gyorsítók lapjára](https://www.azureiotsolutions.com/Accelerators). Keresse meg a megoldást a megoldás-gyorsító irányítópultján. Ezután válassza az **Indítás**lehetőséget:

    [az eszköz-szimulációs megoldás ![elindítása](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. A rendszer átirányítja a **Microsoft Azure IoT-eszköz szimulációs** lapjára. Az oldal jobb felső sarkában válassza az **új szimuláció**lehetőséget.

    [![Azure IoT szimulációs oldal](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. A **Szimuláció beállítása** ablaktáblán állítsa be a következő paramétereket:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Name (Név)** | Adjon egyedi nevet a szimulátornak. |
    | **Leírás** | Adjon meg egy definíciót. |
    | **Szimuláció időtartama** | **Határozatlan ideig történő futtatásra**van beállítva. |
    | **Eszközmodell** | **Név**: írja be a **hűtőt**. <br />**Mennyiség**: írja be a **3**értéket. |
    | **Cél IoT Hub** | Az **előre kiépített IoT hub használatára**van beállítva. |

    [a beállítani kívánt paraméterek ![](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Válassza a **Szimuláció indítása**lehetőséget.

    Az eszköz szimulációjának irányítópultján jegyezze fel a másodpercenkénti **aktív eszközökhöz** és **üzenetekhez**megjelenített információkat.

    [![Azure IoT szimulációs irányítópult](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>Adatok elemzése

Ebben a szakaszban az idősorozat-adatokra vonatkozó alapszintű elemzéseket a [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md)használatával hajtja végre.

1. A [Azure Portal](https://portal.azure.com/)erőforrás oldaláról válassza ki az URL-címet a Azure Time Series Insights előnézeti Explorerben.

    [a Time Series Insights Preview Explorer URL-címének ![](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. Az Explorerben válassza ki az **Idősorozat-példányok** csomópontot a környezetben található összes Azure Time Series Insights-előnézeti példány megtekintéséhez.

    [nem szülő példányok ![listája](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Válassza ki az első idősorozat-példányt. Ezután válassza a **nyomás megjelenítése**lehetőséget.

    [az átlagos nyomás megjelenítéséhez ![kiválasztott idősorozat-példány a Menu paranccsal](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Ekkor megjelenik egy idősorozat-diagram. Módosítsa az **intervallumot** a **15s**értékre.

    [idősorozat-diagram ![](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Ismételje meg a 3. lépést a másik két idősorozat-példánnyal. Az összes idősorozat-példányt megtekintheti, ahogy az a következő ábrán is látható:

    [![diagram az összes idősorozathoz](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. **Az időintervallum beállításnál** módosítsa az időtartományt, hogy megtekintse a Time Series trendeket az elmúlt órában:

    [![az időtartomány beállítása egy órára](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Modell meghatározása és alkalmazása

Ebben a szakaszban egy modellt alkalmaz az adatai struktúrájára. A modell elvégzéséhez a típusokat, hierarchiákat és példányokat kell meghatároznia. Az adatmodellezéssel kapcsolatos további információkért lásd: [idősorozat-modell](./time-series-insights-update-tsm.md).

1. A Explorerben válassza a **modell** fület:

   [![Model (modell) lap a Explorerben](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Típus hozzáadásához válassza a **Hozzáadás** lehetőséget:

   [a Hozzáadás gomb ![típusokhoz](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Ezután három változót határozhat meg a következő típushoz: *nyomás*, *hőmérséklet*és *páratartalom*. A **típus hozzáadása** panelen állítsa be a következő paramétereket:

    | Paraméter | Műveletek |
    | --- | ---|
    | **Name (Név)** | Adja meg a **Chiller**értéket. |
    | **Leírás** | Adja meg a **Chiller típusának definícióját**. |

   * A *terhelés*meghatározásához a **változók**területen adja meg a következő paramétereket:

     | Paraméter | Műveletek |
     | --- | ---|
     | **Name (Név)** | Adja meg az **AVG nyomást**. |
     | **Érték** | Válassza a **nyomás (Double)** lehetőséget. Az **érték** automatikus kitöltése eltarthat néhány percig, miután Azure Time Series Insights előnézet megkezdi az események fogadását. |
     | **Összesítési művelet** | Válassza az **AVG**elemet. |

      [a nyomás meghatározásának ![kiválasztása](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      A következő változó hozzáadásához válassza a **változó hozzáadása**elemet.

   * *Hőmérséklet*meghatározása:

     | Paraméter | Műveletek |
     | --- | ---|
     | **Name (Név)** | Adja meg az **AVG hőmérsékletet**. |
     | **Érték** | Válassza a **hőmérséklet (Double)** lehetőséget. Az **érték** automatikus kitöltése eltarthat néhány percig, miután Azure Time Series Insights előnézet megkezdi az események fogadását. |
     | **Összesítési művelet** | Válassza az **AVG**elemet.|

      [a hőmérséklet definiálásának ![kiválasztása](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      A következő változó hozzáadásához válassza a **változó hozzáadása**elemet.

   * *Páratartalom*meghatározása:

      | | |
      | --- | ---|
      | **Name (Név)** | **Maximális páratartalom** megadása |
      | **Érték** | Válassza a **páratartalom (dupla)** lehetőséget. Az **érték** automatikus kitöltése eltarthat néhány percig, miután Azure Time Series Insights előnézet megkezdi az események fogadását. |
      | **Összesítési művelet** | Válassza a **Max**lehetőséget.|

      [a hőmérséklet definiálásának ![kiválasztása](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    Kattintson a **Létrehozás** gombra.

    A hozzáadott típust láthatja:

    [a hozzáadott típussal kapcsolatos ![](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. A következő lépés egy hierarchia hozzáadása. A **hierarchiák**területen válassza a **Hozzáadás**lehetőséget:

    [![hierarchiák lap Hozzáadás gomb](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. A **hierarchia szerkesztése** panelen állítsa be a következő paramétereket:

   | Paraméter | Műveletek |
   | --- | ---|
   | **Name (Név)** | Adja meg a **hely hierarchiáját**. |
   | **1. szint** | Adja meg az **országot**. |
   | **2. szint** | Adja meg a **várost**. |
   | **3. szint** | Adja meg az **építési**értéket. |

   Kattintson a **Mentés** gombra.

    [![hierarchia mezői a létrehozás gombbal](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   A létrehozott hierarchiát a következő helyen tekintheti meg:

    [a hierarchiával ![információk](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Válassza a **példányok**lehetőséget. Válassza ki az első példányt, majd válassza a **Szerkesztés**lehetőséget:

    [![egy példány Szerkesztés gombjára kattintva](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. A **példányok szerkesztése** panelen állítsa be a következő paramétereket:

    | Paraméter | Műveletek |
    | --- | --- |
    | **Típus** | Válassza a **Chiller**lehetőséget. |
    | **Leírás** | **A Chiller-01,1 példány**megadása. |
    | **Hierarchiák** | Válassza ki a **hely hierarchiáját**. |
    | **Ország** | Adja meg az **USA**-t. |
    | **Város** | Adja meg a **Seattle**nevet. |
    | **Létrehozása** | Adja meg a **Space tű**értéket. |

    [![példány mezői a Save (Mentés) gombbal](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   Kattintson a **Mentés** gombra.

1. Ismételje meg az előző lépést a többi érzékelőnél. Frissítse a következő értékeket:

   * A Chiller 01,2 esetén:

     | Paraméter | Műveletek |
     | --- | --- |
     | **Típus** | Válassza a **Chiller**lehetőséget. |
     | **Leírás** | **A Chiller-01,2 példány**megadása. |
     | **Hierarchiák** | Válassza ki a **hely hierarchiáját**. |
     | **Ország** | Adja meg az **USA**-t. |
     | **Város** | Adja meg a **Seattle**nevet. |
     | **Létrehozása** | Adja meg a **Pacific Science centert**. |

   * A Chiller 01,3 esetén:

     | Paraméter | Műveletek |
     | --- | --- |
     | **Típus** | Válassza a **Chiller**lehetőséget. |
     | **Leírás** | **A Chiller-01,3 példány**megadása. |
     | **Hierarchiák** | Válassza ki a **hely hierarchiáját**. |
     | **Ország** | Adja meg az **USA**-t. |
     | **Város** | Adja meg a **New York**-i értéket. |
     | **Létrehozása** | Adja meg a **Birodalom állapotának kiépítése**értéket. |

1. Válassza az **elemzés** fület, majd frissítse az oldalt. Az idősorozat-példányok megjelenítéséhez a **hely hierarchiája**alatt bontsa ki az összes hierarchia szintet:

   [Az elemzés lap ![](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Az idősorozat-példányok az elmúlt órában való megismeréséhez módosítsa a **gyors** időt az **elmúlt órában**:

    [![a gyors időpontok jelölőnégyzetet az utolsó óra kiválasztásával](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. A **Pacific Science Center**területen válassza ki az idősorozat-példányt, majd válassza a **maximális páratartalom megjelenítése**lehetőséget.

    [a kiválasztott idősorozat-példány és a maximális páratartalom megjelenítése menü kijelölése ![](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. A **maximális páratartalom** idősorozata **1 perces** intervallummal megnyitva. Egy tartomány szűréséhez válasszon ki egy régiót. Az időkeretben lévő események elemzéséhez kattintson a jobb gombbal a diagramra, majd válassza a **Nagyítás**lehetőséget:

   [a kijelölt tartomány ![a nagyítási paranccsal egy helyi menüben](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Az esemény részleteinek megtekintéséhez válasszon ki egy régiót, majd kattintson a jobb gombbal a diagramra:

   [![események részletes listája](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Most, hogy elvégezte az oktatóanyagot, törölje a létrehozott erőforrásokat:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **minden erőforrás**lehetőséget, keresse meg a Azure Time Series Insights erőforráscsoportot.
1. Törölje a teljes erőforráscsoportot (és az abban található összes erőforrást) úgy, hogy kiválasztja az egyes erőforrások **törlését** vagy eltávolítását.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:  

> [!div class="checklist"]
> * Eszköz-szimulációs gyorssegéd létrehozása és használata.
> * Hozzon létre egy Azure Time Series Insights előzetes verziójú TB-környezetet.
> * Csatlakoztathatja a Azure Time Series Insights előnézeti környezetet egy IOT hubhoz.
> * Egy megoldás-gyorsító minta futtatásával továbbíthatja az adatátvitelt a Azure Time Series Insights előnézeti környezetbe.
> * Végezze el az adatok alapszintű elemzését.
> * Definiáljon egy idősorozat-modell típusát és hierarchiáját, majd társítsa azokat a példányokhoz.

Most, hogy már tudja, hogyan hozhat létre saját Azure Time Series Insights előnézeti környezetet, további információt olvashat a Azure Time Series Insights főbb fogalmakról.

További információ a Azure Time Series Insights Storage-konfigurációról:

> [!div class="nextstepaction"]
> [Azure Time Series Insights előnézet tároló és bejövő forgalom](./time-series-insights-update-storage-ingress.md)

További információ a Time Series-modellekről:

> [!div class="nextstepaction"]
> [Azure Time Series Insights előzetes verziójú adatmodellezés](./time-series-insights-update-tsm.md)
