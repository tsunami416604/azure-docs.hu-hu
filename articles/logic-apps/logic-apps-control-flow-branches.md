---
title: Párhuzamos ágak létrehozása vagy csatlakoztatása
description: Párhuzamos ágak létrehozása vagy csatlakoztatása munkafolyamatokhoz Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: bb1c615852fb2bf810abec97e3faeb5775c4a57a
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791453"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Párhuzamos ágak létrehozása vagy csatlakoztatása a munkafolyamat-műveletekhez Azure Logic Apps

Alapértelmezés szerint a logikai alkalmazás munkafolyamatainak műveletei egymás után futnak. Ha egyazon időben szeretne független műveleteket végezni, [párhuzamos ágakat](#parallel-branches)is létrehozhat, majd később is [csatlakozhat](#join-branches) a folyamathoz. 

> [!TIP] 
> Ha olyan triggerrel rendelkezik, amely egy tömböt kap, és minden tömb elemhez szeretne futtatni egy munkafolyamatot, akkor a tömböt a [ **SplitOn** trigger tulajdonsággal](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) *végezheti el.*

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Párhuzamos ág hozzáadása

A független lépések egyidejű futtatásához hozzáadhat párhuzamos ágakat egy meglévő lépés mellett. 

![A lépések párhuzamos futtatása](media/logic-apps-control-flow-branches/parallel.png)

A logikai alkalmazás a munkafolyamat folytatása előtt megvárja, amíg az összes ág be nem fejeződik. A párhuzamos ágak csak akkor futnak, ha a `runAfter` tulajdonság értéke megegyezik a befejezett fölérendelt lépés állapotával. A `branchAction1` és az `branchAction2` például csak akkor van beállítva, ha a `parentAction` `Succeeded` állapottal fejeződött be.

> [!NOTE]
> A művelet elkezdése előtt a logikai alkalmazásnak már rendelkeznie kell egy lépéssel, ahol párhuzamos ágakat adhat hozzá.

1. A <a href="https://portal.azure.com" target="_blank">Azure Portalban</a>nyissa meg a logikai alkalmazást a Logic app Designerben.

1. Vigye az egérmutatót a lépés fölötti nyíl fölé, ahol párhuzamos ágakat kíván hozzáadni. Válassza ki a megjelenő **pluszjelet** ( **+** ), majd válassza a **párhuzamos ág hozzáadása**lehetőséget. 

   ![Párhuzamos ág hozzáadása](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. A keresőmezőbe keresse meg és válassza ki a kívánt műveletet.

   ![Keresse meg és válassza ki a kívánt műveletet](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   A kiválasztott művelet most megjelenik a párhuzamos ágban, például:

   ![Keresse meg és válassza ki a kívánt műveletet](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Most minden párhuzamos ág esetében adja hozzá a kívánt lépéseket. Egy másik művelet egy ágra való hozzáadásához vigye a mutatót azon művelet alá, amelyhez egy szekvenciális műveletet szeretne hozzáadni. Válassza ki a megjelenő **pluszjelet** ( **+** ), majd válassza a **művelet hozzáadása**lehetőséget.

   ![Szekvenciális művelet hozzáadása párhuzamos ág](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. A keresőmezőbe keresse meg és válassza ki a kívánt műveletet.

   ![Szekvenciális művelet keresése és kiválasztása](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   A kiválasztott művelet most megjelenik az aktuális ág között, például:

   ![Szekvenciális művelet keresése és kiválasztása](media/logic-apps-control-flow-branches/added-sequential-action.png)

Ha össze szeretné vonni az ágakat, [csatlakoztassa a párhuzamos ágakat](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Párhuzamos ág definíciója (JSON)

Ha kód nézetben dolgozik, megadhatja a párhuzamos struktúrát a logikai alkalmazás JSON-definíciójában, például:

``` json
{
  "triggers": {
    "myTrigger": {}
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Párhuzamos ágak csatlakoztatása

A párhuzamos ágak egyesítéséhez egyszerűen vegyen fel egy lépést az összes ág alján. Ez a lépés akkor fut le, ha az összes párhuzamos ág futtatása befejeződött.

![Párhuzamos ágak csatlakoztatása](media/logic-apps-control-flow-branches/join.png)

1. A [Azure Portal](https://portal.azure.com)a Logic app Designerben keresse meg és nyissa meg a logikai alkalmazást. 

1. Válassza az **új lépés**lehetőséget a csatlakoztatni kívánt párhuzamos ágak alatt. 

   ![Lépés hozzáadása a csatlakozáshoz](media/logic-apps-control-flow-branches/add-join-step.png)

1. A keresőmezőbe keresse meg és válassza ki az ágakat összekapcsoló lépésként használni kívánt műveletet.

   ![A párhuzamos ágakat összekapcsoló művelet megkeresése és kiválasztása](media/logic-apps-control-flow-branches/join-steps.png)

   A párhuzamos ágak egyesítése már megtörtént.

   ![Csatlakoztatott ágak](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Join Definition (JSON)

Ha kód nézetben dolgozik, megadhatja az illesztési struktúrát a logikai alkalmazás JSON-definíciójában, például:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A szolgáltatásokról és javaslatokról a [Azure Logic apps felhasználói visszajelzéseket ismertető webhelyről](https://aka.ms/logicapps-wish)küldhet vagy szavazhat.

## <a name="next-steps"></a>Következő lépések

* [Lépések futtatása feltételek alapján (feltételes utasítások)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Lépések futtatása különböző értékek alapján (switch utasítások)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Futtatási és ismétlési lépések (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Lépések futtatása csoportosított műveleti állapot alapján (hatókörök)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
