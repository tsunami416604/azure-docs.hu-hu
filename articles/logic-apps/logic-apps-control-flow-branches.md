---
title: Létrehozása vagy csatlakoztatása a párhuzamos ágak - Azure Logic Apps |} Microsoft Docs
description: Tárfiókok létrehozása, vagy a munkafolyamatok párhuzamos ágak csatlakozzon az Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: cfowler
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 40af32b1618ee495cff82ed0a588ad620b759e96
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725547"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Hozzon létre vagy a munkafolyamat-műveleteket párhuzamos ágak csatlakozzon az Azure Logic Apps

Alapértelmezés szerint a műveletek a logic app munkafolyamatokban egymás után futnak. A műveletek független egyszerre hozhat létre [ágak párhuzamos](#parallel-branches), majd [ágakat csatlakozás](#join-branches) a folyamat későbbi. 

> [!TIP] 
> Ha rendelkezik egy eseményindító fogadó egy tömb, és szeretné futtatni a munkafolyamat minden tömb elemhez, *debatch* , hogy a tömb a [ **SplitOn** tulajdonság indítás](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Alapszintű ismerete [logic Apps alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-a-parallel-branch"></a>Párhuzamos ág hozzáadása

Független lépések futtatásával egyszerre, egy meglévő lépés melletti párhuzamos ágak is hozzáadhat. 

![Lépéseket párhuzamos futtatása](media/logic-apps-control-flow-branches/parallel.png)

A Logic Apps alkalmazást a munkafolyamat folytatásához befejezéséhez minden ág vár.
Párhuzamos ágak futtatása csak akkor, ha azok `runAfter` tulajdonság a szülő Kész lépés állapot megegyeznek. Például mindkét `branchAction1` és `branchAction2` futtatásához csak akkor, ha vannak beállítva a `parentAction` befejezi a `Succeded` állapotát.

> [!NOTE]
> Mielőtt elkezdené, a logikai alkalmazás már rendelkeznie kell egy lépés párhuzamos ágak adhat hozzá.

1. Az a <a href="https://portal.azure.com" target="_blank">Azure-portálon</a>, nyissa meg a Logic Apps alkalmazást a Logic App tervezőben.

2. Vigye az egérmutatót a lépés feletti nyílra fölé, ahol párhuzamos ágak hozzáadni kívánt.

3. Válassza ki a **plus** bejelentkezési (**+**), válasszon **hozzáadása egy párhuzamos ág**, és válassza ki a hozzáadni kívánt elemet.

   ![Párhuzamos ág hozzáadása](media/logic-apps-control-flow-branches/add-parallel-branch.png)

   A kijelölt elem megjelenik egy párhuzamos ágat.

4. Minden egyes párhuzamos ág adja hozzá a kívánt lépéseket. A szekvenciális művelet hozzáadása egy párhuzamos ágat, vigye az egérmutatót, a művelet alatt, ahol az egymást követő művelet hozzáadni kívánt. Válassza ki a **plus** (**+**) bejelentkezési és a lépést, amely a hozzá szeretné adni.

   ![Egymást követő lépés hozzáadása párhuzamos ág](media/logic-apps-control-flow-branches/add-sequential-action-parallel-branch.png)

5. Vissza ágak együtt, egyesíteni [párhuzamos ágak csatlakozás](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Párhuzamos ág definition (JSON)

Ha kódnézetben dolgozik, is meghatározhatja a párhuzamos struktúra a Logic Apps alkalmazást JSON-definícióból helyette, például:

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
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Párhuzamos ágak csatlakozás

Párhuzamos ágak együtt egyesítéséhez csak lépés hozzáadása a lap alján alatti ágat. Ez a lépés fut-e után a párhuzamos ágak Befejezés futtatása.

![Párhuzamos ágak csatlakozás](media/logic-apps-control-flow-branches/join.png)

1. Az a [Azure-portálon](https://portal.azure.com), található, és nyissa meg a Logic Apps alkalmazást a Logic App tervezőben. 

2. A párhuzamos ágak, amelyet szeretne csatlakozni adja hozzá a lépést, amely a szeretne végezni.

   ![Adjon hozzá egy lépést, amelyhez csatlakozik, párhuzamos ágak](media/logic-apps-control-flow-branches/join-steps.png)

   A párhuzamos ágak most egyesítődnek.

<a name="join-json"></a>

## <a name="join-definition-json"></a>Csatlakozás definition (JSON)

Ha kódnézetben dolgozik, is meghatározhatja az illesztés struktúra a Logic Apps alkalmazást JSON-definícióból helyette, például:

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
* Küldje el, vagy szavazna funkciók és javaslatok, látogasson el a [Azure Logic Apps felhasználói visszajelzési webhelyet](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* [A lépéseket (feltételes utasítások) feltétel alapján](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Futtatás (kapcsoló utasítások) eltérő értékek alapján](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Futtassa, és ismételje meg a (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Futtassa a csoportosított (hatókör) állapota alapján](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
