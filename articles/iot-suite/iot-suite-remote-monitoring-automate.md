---
title: "Eszközökkel kapcsolatos problémákat észleli a távoli felügyeleti megoldás - Azure |} Microsoft Docs"
description: "Az oktatóanyag bemutatja, hogyan szabályok és a műveletek segítségével automatikusan észleli a küszöbérték-alapú eszközökkel kapcsolatos problémákat a távoli felügyeleti megoldás."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/18/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 2f2b221f5739ac370e110d60ed7812ce9ea5e05f
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="detect-issues-using-threshold-based-rules"></a>Küszöbérték-alapú szabályok használatával kapcsolatos problémák észlelése

Ez az oktatóanyag bemutatja a szabálymotor képességeit a távoli felügyeleti megoldás. Ezek a képességek szemléltetésére az oktatóprogram egy olyan forgatókönyvet a Contoso IoT-alkalmazásban.

A Contoso egy szabályt, amely kritikus riasztást hoz létre, ha a terhelés által jelentett tartalmaz egy **hűtő** eszköz meghaladja 250 PSI. Kezelőként azonosítani szeretné **hűtő** eszközök, amelyek problematikus érzékelők megkeresésével kezdeti nyomás teljesítményt. Ezek az eszközök azonosításához, hozzon létre egy szabályt, amely egy figyelmeztetés generálása, ha a terhelés meghaladja 150 PSI.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

>[!div class="checklist"]
> * A megoldásban a szabályok megtekintése
> * Új szabály létrehozása
> * Meglévő szabály szerkesztése
> * Szabály törlése

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag van szükség a távoli felügyeleti megoldás telepített példányát az Azure-előfizetésben.

Ha még nem telepítette a távoli figyelési megoldást igényelnek, még el kell végeznie a [a távoli felügyeleti előkonfigurált megoldás üzembe helyezéséhez](iot-suite-remote-monitoring-deploy.md) oktatóanyag.

## <a name="view-the-rules-in-your-solution"></a>A megoldásban a szabályok megtekintése

A **szabályok & műveletek** lap a megoldás az aktuális szabályok listáját jeleníti meg:

![Szabályok és a Műveletek lap](media/iot-suite-remote-monitoring-automate/rulesactions.png)

Csak a szabályok megtekintése **hűtő** eszközök, szűrő:

![A szabályok listájának szűrése](media/iot-suite-remote-monitoring-automate/rulesactionsfilter.png)

További információkat szeretne egy szabályt, és módosítsa azt, ha ezt választja ki a listában:

![A szabály részleteinek megtekintése](media/iot-suite-remote-monitoring-automate/rulesactionsdetail.png)

Letiltása, engedélyezése vagy törlése egy vagy több szabályt, jelölje ki a listában több szabály:

![Válassza ki a több szabály](media/iot-suite-remote-monitoring-automate/rulesactionsmultiselect.png)

## <a name="create-a-new-rule"></a>Új szabály létrehozása

Hozzáadása egy új szabályt, amely egy figyelmeztetést hoz létre, amikor a nyomás egy **hűtő** eszköz hosszabb 150 PSI, válassza a **új szabály**:

![Szabály létrehozása](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule.png)

A szabály létrehozásához a következő értékeket használja:

| Beállítás          | Érték                                 |
| ---------------- | ------------------------------------- |
| Név             | Hűtő figyelmeztetés                       |
| Forrás           | **Hűtő** eszközcsoport              |
| Eseményindító mező    | nyomás                              |
| Eseményindító operátor | Nagyobb mint                          |
| Eseményindítási    | 150                                   |
| Súlyossági szint   | Figyelmeztetés                               |
| Riasztás eseményszöveg | Hűtő nyomás túllépte a 150 PSI |

Az új szabály mentéséhez válassza **alkalmaz**.

Megtekintheti a szabály a kiváltásakor a **szabályok & műveletek** lap vagy az a **irányítópult** lap.

## <a name="edit-an-existing-rule"></a>Meglévő szabály szerkesztése

Meglévő szabály lehet módosítani, válassza ki a szabályok listáját. Ezt követően a a **szabály részletes** panelen válassza ki **szerkesztési módban**.

![Szabály szerkesztése](media/iot-suite-remote-monitoring-automate/rulesactionsedit.png)

## <a name="disable-a-rule"></a>A szabálynak a letiltása

Átmenetileg váltson ki egy szabályt, hogy bármikor letilthatja azt a szabályok listáját. Válassza ki a szabályt, hogy tiltsa le, és válassza a **letiltása**. A **állapot** a listában a szabály jelzi, a szabály nem engedélyezett. Egy szabály, hogy korábban letiltott ugyanazzal az eljárással engedélyezheti újra.

![Szabály letiltása](media/iot-suite-remote-monitoring-automate/rulesactionsdisable.png)

Engedélyezi, és egyszerre több szabályok letiltása, ha több szabály a listában jelölje ki.

## <a name="delete-a-rule"></a>Szabály törlése

Véglegesen törli a szabály, válassza ki a szabály a szabályok listáját, és válassza a **törlése**.

Egyszerre több szabály törölheti, ha több szabály válassza ki a listában.

## <a name="next-steps"></a>Következő lépések

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