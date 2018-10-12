---
title: Létrehozása vagy csatlakozás a párhuzamos ágak – Azure Logic Apps |} A Microsoft Docs
description: Tárfiókok létrehozása, vagy a párhuzamos ágak a munkafolyamatok csatlakozzon az Azure Logic Appsben
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 41823d697139e039703cd47e0bfe3380fd2d20d6
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116086"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Hozzon létre vagy a munkafolyamat-műveletek párhuzamos ágakat csatlakozzon az Azure Logic Appsben

Alapértelmezés szerint a műveletek a logikai alkalmazások munkafolyamataiba egymás után futnak. Független műveletek végrehajtásához egyszerre hozhat létre [párhuzamos ágakat](#parallel-branches), majd [ágakat csatlakozzon](#join-branches) a folyamat későbbi. 

> [!TIP] 
> Ha rendelkezik egy eseményindítót, amely egy tömböt kap, és szeretne futtatni egy munkafolyamatot a tömb mindegyik elemén, *debatch* a tömböt a [ **SplitOn** tulajdonság-trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Párhuzamos ág hozzáadása

Független lépések végrehajtásához egy időben, hozzáadhat egy meglévő lépés melletti párhuzamos ágakat. 

![Párhuzamos lépéseket futtatása](media/logic-apps-control-flow-branches/parallel.png)

A logikai alkalmazás megvárja az összes ág befejeződik a munkafolyamat folytatása előtt. Párhuzamos ágakat futtatása csak akkor, ha azok `runAfter` tulajdonság értékek megegyeznek a befejezett szülő lépés állapotát. Például mindkét `branchAction1` és `branchAction2` futtatásához csak akkor, ha vannak beállítva a `parentAction` befejezi a `Succeded` állapotát.

> [!NOTE]
> Mielőtt elkezdené, a logikai alkalmazás már rendelkeznie kell egy lépést, ahol hozzáadhat párhuzamos ágakat.

1. Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a>, nyissa meg a logikai alkalmazás a Logikaialkalmazás-tervezőben.

1. Helyezze az egérmutatót a nyíl a fenti kívánja párhuzamos ágak hozzáadása. Válassza ki a **plusz** jel (**+**), amely akkor jelenik meg, és válassza **párhuzamos ág hozzáadása**. 

   ![Párhuzamos ág hozzáadása](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. A keresőmezőbe keresse meg és válassza ki a kívánt műveletet.

   ![Keresse meg és válassza ki a kívánt műveletet](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   Most már a kiválasztott művelet jelenik meg a párhuzamos ág, például:

   ![Keresse meg és válassza ki a kívánt műveletet](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Az egyes párhuzamos ágakhoz, adja hozzá a kívánt lépéseket. Egyik ágára való leküldés egy másik művelet hozzáadása, helyezze az egérmutatót, a művelet alatt, ahol szeretné egymást követő művelet hozzáadása. Válassza ki a **plusz** (**+**), amely akkor jelenik meg, és válassza ki bejelentkezési **művelet hozzáadása**.

   ![A művelet a soros párhuzamos ág hozzáadása](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. A keresőmezőbe keresse meg és válassza ki a kívánt műveletet.

   ![Keresse meg és válassza ki az egymást követő művelet](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   Most már a kiválasztott művelet megjelenjen-e az aktuális fejlesztési ágban, például:

   ![Keresse meg és válassza ki az egymást követő művelet](media/logic-apps-control-flow-branches/added-sequential-action.png)

Vissza az ágak együttesen egyesíteni [csatlakozzon a párhuzamos ágak](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Párhuzamos ág definition (JSON)

Ha kód nézetben dolgozik, határozhat meg a párhuzamos struktúra JSON-definíciójában a logikai alkalmazás, például:

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

## <a name="join-parallel-branches"></a>Csatlakozzon a párhuzamos ágak

Párhuzamos ágakat együtt egyesítéséhez csak lépés hozzáadása alatt az összes ág alján. Ez a lépés fut-e után a futó párhuzamos ágakat a Befejezés.

![Csatlakozzon a párhuzamos ágak](media/logic-apps-control-flow-branches/join.png)

1. Az a [az Azure portal](https://portal.azure.com), keresse meg és nyissa meg a logikai alkalmazás a Logikaialkalmazás-tervezőben. 

1. A párhuzamos ágak csatlakozni szeretne, válassza ki **új lépés**. 

   ![Csatlakozás lépés hozzáadása](media/logic-apps-control-flow-branches/add-join-step.png)

1. A keresőmezőbe keresse meg és válassza ki a kívánt művelet, amely csatlakoztatja az ágak lépéseként.

   ![Keresse meg és válassza ki a műveletet, amely csatlakoztatja a párhuzamos ágak](media/logic-apps-control-flow-branches/join-steps.png)

   A párhuzamos ágak most összefésülése megtörténjen.

   ![Csatlakoztatott ágak](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Csatlakozás definition (JSON)

Ha kód nézetben dolgozik, meghatározhatja a join struktúra JSON-definíciójában a logikai alkalmazás ehelyett például:

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
* Küldje el, vagy szavazhat a funkciók és javaslatok, látogasson el a [Azure Logic Apps felhasználói visszajelzések oldalon](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* [Hajtsa végre a lépéseket, egy feltételt (feltételes kifejezések) alapján](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Hajtsa végre a lépéseket, a különböző értékek (switch-utasítások) alapján](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Futtassa, és ismételje meg a (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Hajtsa végre a lépéseket, a csoportosított műveleti állapota (hatóköröket) alapján](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
