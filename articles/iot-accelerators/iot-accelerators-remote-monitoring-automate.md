---
title: Eszközökkel kapcsolatos problémák észlelése egy Azure-alapú távoli monitorozási megoldásban | Microsoft Docs
description: Ez az oktatóprogram bemutatja, hogyan használja a szabályokat és műveleteket a küszöbérték-alapú eszközhibák azonosítására a távoli monitorozási megoldásban.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 1e3eaeec1d2eae3c36f285a3e4c536657504cbb8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098481"
---
# <a name="tutorial-detect-issues-with-devices-connected-to-your-monitoring-solution"></a>Oktatóanyag: A monitorozási megoldáshoz csatlakoztatott eszközök problémáinak észlelése

Ebben az oktatóanyagban a távoli monitorozási megoldásgyorsító konfigurálásával fogja észlelni a csatlakoztatott IoT-eszközökkel kapcsolatos problémákat. Az eszközhibák észleléséhez adjon hozzá olyan szabályokat, amelyek riasztásokat jelenítenek meg a megoldás irányítópultján.

A szabályok és riasztások bemutatásához az oktatóanyag szimulált hűtőeszközöket használ. A Contoso nevű vállalat által kezelt hűtő kapcsolódik a távoli monitorozási megoldásgyorsítóhoz. A Contoso már rendelkezik olyan szabállyal, amely kritikus riasztást hoz létre, ha a hűtőben 298 PSI fölé emelkedik a nyomás. A Contoso operátoraként a kezdeti nyomásnövekedések keresésével azonosítania kell az olyan hűtőeszközöket, amelyeknek az érzékelői hibásak lehetnek. Az ilyen eszközök azonosításához hozzáadhat egy szabályt, amely egy figyelmeztető riasztást hoz létre, ha a hűtőben a nyomás meghaladja a 150 PSI-t.

Továbbá az is a feladata, hogy létrehozzon egy kritikus figyelmeztetést a hűtőkhöz, amely akkor lép életbe, ha az utolsó öt perc során az eszközben az átlagos páratartalom meghaladja a 80%-ot, a hőmérséklet pedig magasabb, mint 75 Fahrenheit-fok.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * A megoldás szabályainak megtekintése
> * Szabály létrehozása
> * Több feltétellel rendelkező szabály létrehozása
> * Meglévő szabály szerkesztése
> * Szabályok be- vagy kikapcsolása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag követéséhez rendelkeznie kell a távoli monitorozási megoldásgyorsító üzembe helyezett példányával az Azure-előfizetésében.

Ha még nem helyezte üzembe a távoli monitorozási megoldásgyorsítót, végezze el [Távoli felhőalapú monitorozási megoldás üzembe helyezése](quickstart-remote-monitoring-deploy.md) rövid útmutatóban leírtakat.

## <a name="view-the-existing-rules"></a>Meglévő szabályok megtekintése

A megoldásgyorsító **Szabályok** lapja megjeleníti az összes jelenlegi szabály listáját:

[![Szabályok lap](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

Ha kizárólag a hűtőre vonatkozó szabályokat szeretné megtekinteni, alkalmazzon szűrőt:

[![Szabálylista szűrése](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2-expanded.png#lightbox)

A listából való kiválasztással részletes információkat kaphat egy adott szabályról, és szerkesztheti is:

[![Szabály részleteinek megtekintése](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

Egy vagy több szabály engedélyezéséhez vagy letiltásához válasszon ki egy vagy több szabályt a listából:

[![Több szabály kijelölése](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2-expanded.png#lightbox)

## <a name="create-a-rule"></a>Szabály létrehozása

Kattintson az **Új szabály** elemre egy olyan szabály létrehozásához, amely egy figyelmeztetést hoz létre, ha a hűtőeszközben a nyomás meghaladja a 150 PSI-t. A szabály létrehozásához használja a következő értékeket:

| Beállítás          | Érték                                 |
| ---------------- | ------------------------------------- |
| Szabály neve        | Hűtő figyelmeztetés                       |
| Leírás      | A nyomás a hűtőben túllépte a 150 PSI-t |
| Eszközcsoport     | **Hűtők** eszközcsoport             |
| Számítás      | Azonnali                               |
| 1. feltétel mezője| pressure                              |
| 1. feltétel operátora | Nagyobb, mint                      |
| 1. feltétel értéke    | 150                               |
| Súlyossági szint  | Figyelmeztetés                               |

[![Figyelmeztetési szabály létrehozása](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-expanded.png#lightbox)

Az új szabály mentéséhez kattintson az **Alkalmaz** gombra.

A szabály aktiválásának idejét a **Szabály** vagy az **Irányítópult** oldalon láthatja:

[![Figyelmeztetési szabály aktiválása](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-expanded.png#lightbox)

## <a name="create-a-rule-with-multiple-conditions"></a>Több feltétellel rendelkező szabály létrehozása

Kattintson az **Új szabály** elemre egy olyan többfeltételes szabály létrehozásához, amely kritikus riasztást hoz létre, ha az utolsó öt perc során az eszközben az átlagos páratartalom meghaladja a 80%-ot, a hőmérséklet pedig magasabb, mint 75 Fahrenheit-fok. A szabály létrehozásához használja a következő értékeket:

| Beállítás          | Érték                                 |
| ---------------- | ------------------------------------- |
| Szabály neve        | Kritikus páratartalom- és hőmérsékletszint a hűtőben    |
| Leírás      | A páratartalom és a hőmérséklet szintje kritikus |
| Eszközcsoport     | **Hűtők** eszközcsoport             |
| Számítás      | Átlag                               |
| Időtartam      | 5                                     |
| 1. feltétel mezője| páratartalom                              |
| 1. feltétel operátora | Nagyobb, mint                      |
| 1. feltétel értéke    | 80                                |
| Súlyossági szint  | Kritikus                              |

[![Többfeltételes szabály létrehozásának első része](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-expanded.png#lightbox)

A második feltétel hozzáadásához kattintson a „+ feltétel hozzáadása” elemre. Használja a következő értékeket az új feltételhez:

| Beállítás          | Érték                                 |
| ---------------- | ------------------------------------- |
| 2. feltétel mezője| hőmérséklet                           |
| 2. feltétel operátora | Nagyobb, mint                      |
| 2. feltétel értéke    | 75                                |

[![Többfeltételes szabály létrehozásának második része](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-expanded.png#lightbox)

Az új szabály mentéséhez kattintson az **Alkalmaz** gombra.

A szabály aktiválásának idejét a **Szabály** vagy az **Irányítópult** oldalon láthatja:

[![Többfeltételes szabály aktiválása](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-expanded.png#lightbox)

## <a name="edit-an-existing-rule"></a>Meglévő szabály szerkesztése

Egy meglévő szabály módosításához válassza ki a szabályt a listából, és kattintson a **Szerkesztés** elemre:

[![Szabály szerkesztése](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-expanded.png#lightbox)

## <a name="disable-a-rule"></a>Szabály letiltása

Ha ideiglenesen ki kíván kapcsolni egy szabályt, tiltsa le a szabálylistában. Válassza ki a letiltani kívánt szabályt, majd kattintson a **Letiltás** elemre. Ekkor a listában megváltozik a szabály **Állapota**, jelezve, hogy a szabály le van tiltva. A korábban letiltott szabályokat ugyanezzel az eljárással engedélyezheti.

[![Szabály letiltása](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-expanded.png#lightbox)

Egyszerre több szabályt is engedélyezhet vagy letilthat, ha kijelöli őket a listában.

<!-- ## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább kíván lépni a következő oktatóanyagra, ne kapcsolja ki a távoli monitorozási megoldásgyorsítót. Ha csökkenteni szeretné a megoldásgyorsító futtatásának költségeit, amíg nem használja, állítsa le a szimulált eszközöket a beállítások panelen:

[![Telemetria szüneteltetése](./media/iot-accelerators-remote-monitoring-automate/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-automate/togglesimulation-expanded.png#lightbox)

Ha készen áll a következő oktatóanyag megkezdésére, újraindíthatja a szimulált eszközöket.

Ha már nincs szüksége a megoldásgyorsítóra, törölje a [Kiépített megoldások](https://www.azureiotsolutions.com/Accelerators#dashboard) lapról:

![Megoldás törlése](media/iot-accelerators-remote-monitoring-automate/deletesolution.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan használhatja a távoli monitorozási megoldásgyorsító található **Szabályok** lapját a megoldásban riasztásokat aktiváló szabályok létrehozásához és kezeléséhez. A következő oktatóanyagból megtudhatja, hogyan használhatja a megoldásgyorsítót a csatlakoztatott eszközök kezelésére és konfigurálására.

> [!div class="nextstepaction"]
> [A monitorozási megoldáshoz csatlakoztatott eszközök konfigurálása és kezelése](iot-accelerators-remote-monitoring-manage.md)