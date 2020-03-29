---
title: Párhuzamos elágazások létrehozása vagy egyesítése a munkafolyamatokban végrehajtott műveletekhez
description: Megtudhatja, hogyan hozhat létre és egyesíthet párhuzamos futó ágakat független munkafolyamat-műveletekhez az Azure Logic Apps alkalmazásban
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: c0b1519992ba930382a1987aed185ef3c92eded4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453438"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Párhuzamos ágak létrehozása vagy egyesítése munkafolyamat-műveletekhez az Azure Logic Apps alkalmazásban

Alapértelmezés szerint a logikai alkalmazás munkafolyamataiban végrehajtott műveletek egymás után futnak. Ha egyszerre szeretne független műveleteket végrehajtani, [párhuzamos ágakat](#parallel-branches)hozhat létre, majd ezeket az [ágakat](#join-branches) később csatlakozhat a folyamathoz. 

> [!TIP] 
> Ha olyan eseményindítóval rendelkezik, amely tömböt fogad, és munkafolyamatot szeretne futtatni minden tömbelemhez, akkor a [ **SplitOn** eseményindító tulajdonsággal](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) *felbonthatja* a tömböt.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Párhuzamos elágazás hozzáadása

Ha egyszerre szeretne független lépéseket futtatni, párhuzamos ágakat adhat hozzá egy meglévő lépés mellé. 

![Lépések párhuzamos futtatása](media/logic-apps-control-flow-branches/parallel.png)

A logikai alkalmazás megvárja, amíg az összes ág befejeződik, mielőtt folytatná a munkafolyamatot. A párhuzamos elágazások csak akkor futnak, ha tulajdonságértékeik `runAfter` megegyeznek a befejezett szülőlépés állapotával. Mindkettő, `branchAction1` és `branchAction2` csak akkor fut, `parentAction` ha a `Succeeded` teljes állapot.

> [!NOTE]
> Mielőtt elkezdené, a logikai alkalmazás már rendelkezik egy lépéssel, ahol párhuzamos ágakat adhat hozzá.

1. Az <a href="https://portal.azure.com" target="_blank">Azure Portalon</a>nyissa meg a logikai alkalmazást a Logic App Designerben.

1. Vigye az egérmutatót a párhuzamos ágak hozzáadására lévő lépés feletti nyíl fölé. Válassza ki a**+** megjelenő **pluszjelet** ( ), majd válassza **a Párhuzamos ág hozzáadása**lehetőséget. 

   ![Párhuzamos elágazás hozzáadása](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. A keresőmezőben keresse meg és jelölje ki a kívánt műveletet.

   ![A kívánt művelet megkeresése és kijelölése](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   A kiválasztott művelet most megjelenik a párhuzamos ágban, például:

   ![A kívánt művelet megkeresése és kijelölése](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Most minden párhuzamos ágban adja hozzá a kívánt lépéseket. Ha egy másik műveletet szeretne hozzáadni egy ághoz, vigye a mutatót a művelet alá, ahol szekvenciális műveletet szeretne hozzáadni. Válassza **plus** ki**+** a megjelenő plusz ( ) jelet, majd válassza **a Művelet hozzáadása**lehetőséget.

   ![Szekvenciális művelet hozzáadása párhuzamos elágazáshoz](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. A keresőmezőben keresse meg és jelölje ki a kívánt műveletet.

   ![Szekvenciális művelet keresése és kijelölése](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   A kiválasztott művelet most antól az aktuális ágon belül jelenik meg, például:

   ![Szekvenciális művelet keresése és kijelölése](media/logic-apps-control-flow-branches/added-sequential-action.png)

Az ágak egyesítéséhez [csatlakozzon a párhuzamos ágakhoz.](#join-branches) 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Párhuzamos ág definíciója (JSON)

Ha kódnézetben dolgozik, a párhuzamos struktúrát a logikai alkalmazás JSON-definíciójában határozhatja meg, például:

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

## <a name="join-parallel-branches"></a>Párhuzamos ágak összefűzése

A párhuzamos ágak egyesítéséhez csak adjon hozzá egy lépést az alján az összes ág alatt. Ez a lépés akkor fut, amikor az összes párhuzamos ág futása befejeződik.

![Párhuzamos ágak összefűzése](media/logic-apps-control-flow-branches/join.png)

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és nyissa meg a logikai alkalmazást a Logic App Designerben. 

1. A csatlakozni kívánt párhuzamos ágak alatt válassza az **Új lépés lehetőséget.** 

   ![Lépés hozzáadása az illesztéshez](media/logic-apps-control-flow-branches/add-join-step.png)

1. A keresőmezőben keresse meg és jelölje ki a kívánt műveletet az ágakat egyesítő lépésként.

   ![Párhuzamos ágakat egyesítő művelet megkeresése és kijelölése](media/logic-apps-control-flow-branches/join-steps.png)

   A párhuzamos ágak most egyesülnek.

   ![Egyesített ágak](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Csatlakozás definíciója (JSON)

Ha kódnézetben dolgozik, a logikai alkalmazás JSON-definíciójában definiálhatja az illesztési struktúrát, például:

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
* A funkciók és javaslatok elküldéséhez vagy az azokra való szavazáshoz látogasson el az [Azure Logic Apps felhasználói visszajelzési webhelyére.](https://aka.ms/logicapps-wish)

## <a name="next-steps"></a>További lépések

* [Feltételek (feltételes utasítások) alapján lépések futtatása](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Lépések futtatása különböző értékek alapján (kapcsolóutasítások)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Lépések futtatása és ismétlése (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Lépések futtatása csoportosított műveletállapot (hatókörök) alapján](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
