---
title: 'Oktatóanyag: alkalmazások méretezése az Azure Spring Cloud-ban | Microsoft Docs'
description: Ebből az oktatóanyagból megtudhatja, hogyan méretezheti az alkalmazásokat az Azure Spring Cloudtel a Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.openlocfilehash: f08a3b5d0a03b0e898457bbb783dd5031c4b0f27
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "76277476"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Alkalmazások méretezése az Azure Spring Cloud-ban

Ez az oktatóanyag bemutatja, hogyan méretezheti a Service-alkalmazásokat a Azure Portal Azure Spring Cloud irányítópultján.

A virtuális processzorok (vCPU) számának és a memória mennyiségének módosításával felfelé és lefelé méretezheti az alkalmazást. Az alkalmazás-példányok számának módosításával méretezheti az alkalmazást a és a szolgáltatásban.

A befejezést követően tudni fogja, hogyan végezheti el a gyors manuális módosításokat a szolgáltatásban lévő egyes alkalmazásokban. A skálázás másodpercek alatt lép érvénybe, és nincs szükség kód módosítására vagy újratelepítésre.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) . 
* Egy üzembe helyezett Azure Spring Cloud Service-példány.  Az első lépésekhez kövesse [az alkalmazások Azure CLI-n keresztüli üzembe helyezését ismertető](spring-cloud-quickstart-launch-app-cli.md) útmutatót.
* Legalább egy alkalmazás már létre van hozva a szolgáltatási példányban.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Navigáljon a méretezés lapra a Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Nyissa meg az Azure Spring Cloud **Áttekintés** oldalát.

1. Válassza ki a szolgáltatást tartalmazó erőforráscsoportot.

1. Válassza az **alkalmazások** fület a lap bal oldalán található menü **Beállítások** területén.

1. Válassza ki a méretezni kívánt alkalmazást. Ebben a példában válassza ki a **Account-Service**nevű alkalmazást. Ekkor megjelenik az alkalmazás **Áttekintés** lapja.

1. Lépjen a **skála** lapra a lap bal oldalán lévő menü **Beállítások** területén. A következő szakaszban látható attribútumok méretezéséhez meg kell jelennie a beállításoknak.

## <a name="scale-your-application"></a>Az alkalmazás méretezése

Ha módosítja a skálázási attribútumokat, tartsa szem előtt a következő megjegyzéseket:

* **Processzorok**: az alkalmazás példányain a processzorok maximális száma négy. Az alkalmazáshoz tartozó processzorok teljes számát az itt megadott érték szorozza az alkalmazás példányainak számával.

* **Memória/GB**: az alkalmazás-példányok maximális mérete 8 GB. Az alkalmazáshoz tartozó memória teljes mennyisége az itt megadott érték, az alkalmazás példányainak száma szorozva.

* **Alkalmazás-példányok száma**: a standard szinten legfeljebb 20 példányra lehet méretezni. Ez az érték megváltoztatja a Service-alkalmazás különböző futó példányainak számát.

A skálázási beállítások alkalmazásához válassza a **Mentés** lehetőséget.

![A Azure Portal méretezési szolgáltatása](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Néhány másodperc elteltével megjelenik az **áttekintő** oldal az Áttekintés oldalon, és további részleteket tartalmaz az **alkalmazás példányai** lapon. a skálázás nem igényli a kód módosítását vagy az újratelepítést.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan méretezheti manuálisan az Azure Spring Cloud-alkalmazásait. Az alkalmazás figyelésének megismeréséhez folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Útmutató az alkalmazások figyeléséhez](spring-cloud-tutorial-distributed-tracing.md)
