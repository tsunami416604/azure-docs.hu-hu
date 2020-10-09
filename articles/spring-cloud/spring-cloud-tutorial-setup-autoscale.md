---
title: Oktatóanyag – a Service-alkalmazások autoskálázásának beállítása
description: Ez a cikk azt ismerteti, hogyan állíthatja be az alkalmazásaihoz az Microsoft Azure Portal vagy az Azure CLI használatával az alkalmazások autoskálázási beállításait.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/22/2020
ms.custom: devx-track-java
ms.openlocfilehash: 5ca0c498e9fd03ce6397824465f9e1e006eeccc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905384"
---
# <a name="tutorial-set-up-autoscale-for-microservice-applications"></a>Oktatóanyag: a Service-alkalmazások autoskálázásának beállítása

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

Az autoscale az Azure Spring Cloud beépített funkciója, amely segítséget nyújt a Service-alkalmazások számára a lehető legjobb megoldás megváltozásakor. Ez magában foglalja a virtuális CPU-, memória-és alkalmazás-példányok számának módosítását. Ez a cikk azt ismerteti, hogyan állíthatja be az alkalmazásaihoz az Microsoft Azure Portal vagy az Azure CLI használatával az alkalmazások autoskálázási beállításait.

## <a name="prerequisites"></a>Előfeltételek

Az alábbi eljárások követéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.
* Egy üzembe helyezett Azure Spring Cloud Service-példány. Az első lépésekhez kövesse az [alkalmazások Azure CLI-n keresztüli üzembe helyezésével](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) foglalkozó rövid útmutatót.
* Legalább egy alkalmazás már létre van hozva a szolgáltatási példányban.

## <a name="navigate-to-the-autoscale-page-in-the-azure-portal"></a>Navigáljon a Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg az Azure Spring Cloud **Áttekintés** oldalát.
3. Válassza ki a szolgáltatást tartalmazó erőforráscsoportot.
4. Válassza az **alkalmazások** fület a bal oldali navigációs ablaktábla menü **Beállítások** területén.
5. Válassza ki azt az alkalmazást, amelyhez be szeretné állítani az autoskálázást. Ebben a példában válassza ki a **bemutató**nevű alkalmazást. Ekkor megjelenik az alkalmazás **Áttekintés** lapja.
6. A bal oldali navigációs ablaktábla menü **Beállítások** területén válassza a **kibővítés** lapot.
7. Válassza ki azt az üzembe helyezést, amelyhez be szeretné állítani az autoskálázást. A következő szakaszban láthatók az autoskálázás lehetőségei.


![Autoscale menü](./media/spring-cloud-autoscale/autoscale-menu.png)

## <a name="set-up-autoscale-settings-for-your-application-in-the-azure-portal"></a>Az alkalmazáshoz tartozó autoskálázási beállítások beállítása a Azure Portal

Az igény szerinti felügyelethez két lehetőség áll rendelkezésre:

* Manuális méretezés: rögzített példányszámot tart fenn. A standard szinten legfeljebb 500 példányt lehet kibővíteni. Ez az érték megváltoztatja a Service-alkalmazás különböző futó példányainak számát.
* Egyéni autoscale: bármilyen ütemterv szerint méretezhető, bármilyen metrika alapján.

A Azure Portal válassza ki, hogyan szeretné méretezni a méretezést.  Az alábbi ábrán az **Egyéni autoskálázási** beállítás és mód beállításai láthatók.

![Egyéni méretezés](./media/spring-cloud-autoscale/custom-autoscale.png)

## <a name="set-up-autoscale-settings-for-your-application-in-azure-cli"></a>Az alkalmazáshoz tartozó autoskálázási beállítások beállítása az Azure CLI-ben
Az Azure CLI használatával is beállíthatja az autoskálázási módokat.  Az alábbi parancsok egy autoskálázási beállítást és egy autoskálázási szabályt hoznak létre.

* Autoskálázási beállítás létrehozása
  ```
  az monitor autoscale create -g demo-rg --resource /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourcegroups/demo-rg/providers/Microsoft.AppPlatform/Spring/autoscale/apps/demo/deployments/default --name demo-setting --min-count 1 --max-count 5 --count 1
  ```
* Autoskálázási szabály létrehozása
  ```
  az monitor autoscale rule create -g demo-rg --autoscale-name demo-setting --scale out 1 --cooldown 1 --condition "tomcat.global.request.total.count > 100 avg 1m where AppName == demo and Deployment == default"
  ```

## <a name="upgrade-to-the-standard-tier"></a>Frissítés a Standard szintre

Ha az alapszintű csomaggal rendelkezik, és egy vagy több ilyen korláttal van korlátozva, a standard szintre válthat. Ehhez nyissa meg az **árképzési** szint menüt úgy, hogy először kiválasztja a *standard* szintű oszlopot, és a **frissítés** gombra kattint.

## <a name="next-steps"></a>További lépések

* [Az autoskálázás áttekintése Microsoft Azure](https://docs.microsoft.com/azure/azure-monitor/platform/autoscale-overview)
* [Az Azure CLI figyelésének autoskálázása](https://docs.microsoft.com/cli/azure/monitor/autoscale?view=azure-cli-latest&preserve-view=true)