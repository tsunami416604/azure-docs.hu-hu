---
title: Eszközökkel kapcsolatos problémákat észleli a távoli felügyeleti megoldás - Azure |} Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan szabályok és a műveletek segítségével automatikusan észleli a küszöbérték-alapú eszközökkel kapcsolatos problémákat a távoli felügyeleti megoldás.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 5acf35ed19a5b6baa2885fd58cfb7fbbe1ac3cd8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="detect-issues-using-threshold-based-rules"></a>Küszöbérték-alapú szabályok használatával kapcsolatos problémák észlelése

Ez az oktatóanyag bemutatja a szabálymotor képességeit a távoli felügyeleti megoldás. Ezek a képességek szemléltetésére az oktatóprogram egy olyan forgatókönyvet a Contoso IoT-alkalmazásban.

A Contoso egy szabályt, amely kritikus riasztást hoz létre, ha a terhelés által jelentett tartalmaz egy **hűtő** eszköz meghaladja 250 PSI. Kezelőként azonosítani szeretné **hűtő** eszközök, amelyek problematikus érzékelők megkeresésével kezdeti nyomás teljesítményt. Ezek az eszközök azonosításához, hozzon létre egy szabályt, amely egy figyelmeztetés generálása, ha a terhelés meghaladja 150 PSI.

Akkor is kaptak, hogy a kritikus riasztások kell váltódik ki, mikor átlagos nedvességtartalma a **hűtő** az elmúlt 5 percben eszköz érték nagyobb, mint a 80 %-os és hőmérséklete a **hűtő** az eszköz a elmúlt 5 percben érték nagyobb, mint 75 fahrenheit fokban megadva.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

>[!div class="checklist"]
> * A megoldásban a szabályok megtekintése
> * Új szabály létrehozása
> * Több feltételt az új szabály létrehozása
> * Meglévő szabály szerkesztése
> * Szabály törlése

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag van szükség a távoli felügyeleti megoldás telepített példányát az Azure-előfizetésben.

Ha még nem telepítette a távoli figyelési megoldást igényelnek, még el kell végeznie a [telepíteni a távoli felügyeleti megoldásgyorsító](iot-suite-remote-monitoring-deploy.md) oktatóanyag.

## <a name="view-the-rules-in-your-solution"></a>A megoldásban a szabályok megtekintése

A **szabályok** lap a megoldás az aktuális szabályok listáját jeleníti meg:

![Szabályok és a Műveletek lap](media/iot-suite-remote-monitoring-automate/rulesactions_v2.png)

Csak a szabályok megtekintése **hűtő** eszközök, szűrő:

![A szabályok listájának szűrése](media/iot-suite-remote-monitoring-automate/rulesactionsfilter_v2.png)

További információkat szeretne egy szabályt, és módosítsa azt, ha ezt választja ki a listában:

![A szabály részleteinek megtekintése](media/iot-suite-remote-monitoring-automate/rulesactionsdetail_v2.png)

Letiltása, engedélyezése vagy törlése egy vagy több szabályt, jelölje ki a listában több szabály:

![Válassza ki a több szabály](media/iot-suite-remote-monitoring-automate/rulesactionsmultiselect_v2.png)

## <a name="create-a-new-rule"></a>Új szabály létrehozása

Hozzáadása egy új szabályt, amely egy figyelmeztetést hoz létre, amikor a nyomás egy **hűtő** eszköz hosszabb 150 PSI, válassza a **új szabály**:

![Szabály létrehozása](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule_v2.png)

A szabály létrehozásához a következő értékeket használja:

| Beállítás          | Érték                                 |
| ---------------- | ------------------------------------- |
| Szabály neve        | Hűtő figyelmeztetés                       |
| Leírás      | Hűtő nyomás túllépte a 150 PSI |
| Eszközcsoport     | **Hőmérsékletű** eszközcsoport             |
| Számítási      | Az azonnali                               |
| Feltétel 1 mező| pressure                              |
| Feltétel 1 operátor | Nagyobb mint                      |
| 1 értéket a feltétel    | 150                               |
| Serverity szint  | Figyelmeztetés                               |

Az új szabály mentéséhez válassza **alkalmaz**.

Megtekintheti a szabály a kiváltásakor a **szabályok** lap vagy az a **irányítópult** lap.

## <a name="create-a-new-rule-with-multiple-conditions"></a>Több feltételt az új szabály létrehozása

Új szabály létrehozása több feltételt, amely létrehozza a kritikus riasztás feltétele az átlagos nedvességtartalma a **hűtő** az elmúlt 5 percben eszköz érték nagyobb, mint a 80 %-os és hőmérséklete a **hűtő** az elmúlt 5 percben eszköz érték nagyobb, mint 75 fok fahrenheit, válassza a **új szabály**:

![Mult szabály létrehozása](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule_mult_v2.png)

A szabály létrehozásához a következő értékeket használja:

| Beállítás          | Érték                                 |
| ---------------- | ------------------------------------- |
| Szabály neve        | Hűtő páratartalom és kritikus temp    |
| Leírás      | Páratartalom és hőmérséklet számára kritikus |
| Eszközcsoport     | **Hőmérsékletű** eszközcsoport             |
| Számítási      | Átlag                               |
| Adott időszakban      | 5                                     |
| Feltétel 1 mező| nedvességtartalma                              |
| Feltétel 1 operátor | Nagyobb mint                      |
| 1 értéket a feltétel    | 80                               |
| Serverity szint  | Kritikus                              |

A második feltétel hozzáadásához kattintson a "+ feltétel hozzáadása".

![2-feltétel létrehozása](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2.png)

Az új feltétel a következő értékeket használja:

| Beállítás          | Érték                                 |
| ---------------- | ------------------------------------- |
| Feltétel 2 mező| Hőmérséklet                           |
| Feltétel 2 operátor | Nagyobb mint                      |
| Feltétel 2 érték    | 75                                |

Az új szabály mentéséhez válassza **alkalmaz**.

Megtekintheti a szabály a kiváltásakor a **szabályok** lap vagy az a **irányítópult** lap.

## <a name="edit-an-existing-rule"></a>Meglévő szabály szerkesztése

Meglévő szabály lehet módosítani, válassza ki a szabályok listáját.

![Szabály szerkesztése](media/iot-suite-remote-monitoring-automate/rulesactionsedit_v2.png)

<!--## Disable a rule

To temporarily switch off a rule, you can disable it in the list of rules. Choose the rule to disable, and then choose **Disable**. The **Status** of the rule in the list changes to indicate the rule is now disabled. You can re-enable a rule that you previously disabled using the same procedure.

![Disable rule](media/iot-suite-remote-monitoring-automate/rulesactionsdisable.png)

You can enable and disable multiple rules at the same time if you select multiple rules in the list.-->

<!--## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan számára:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * A megoldásban a szabályok megtekintése
> * Új szabály létrehozása
> * Meglévő szabály szerkesztése
> * Szabály törlése

Most, hogy rendelkezik megtudta, hogyan észleli küszöbérték-alapú szabályok alkalmazásával problémáit, a javasolt lépések megtudhatja, hogyan:

* [Kezelése és az eszközök](./iot-suite-remote-monitoring-manage.md).
* [Hibaelhárítás és szervizelheti azokat a eszközökkel kapcsolatos problémákat](./iot-suite-remote-monitoring-maintain.md).
* [A megoldás tesztelése szimulált eszközökkel](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->