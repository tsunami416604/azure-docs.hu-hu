---
title: 'Oktatóanyag: alkalmazások méretezése az Azure Spring Cloud-ban | Microsoft Docs'
description: Ismerje meg, hogyan méretezheti az alkalmazásokat az Azure Spring Cloudtel a Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.custom: devx-track-java
ms.openlocfilehash: 33d25edafa9249300406365699930ded1c8ec75b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906843"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>Oktatóanyag: alkalmazások méretezése az Azure Spring Cloud-ban

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

Ez az oktatóanyag bemutatja, hogyan méretezheti a Service-alkalmazásokat a Azure Portal Azure Spring Cloud irányítópultján.

A virtuális processzorok (vCPU) számának és a memória mennyiségének módosításával felfelé és lefelé méretezheti az alkalmazást. Az alkalmazás-példányok számának módosításával méretezheti az alkalmazást a és a szolgáltatásban.

A befejezést követően tudni fogja, hogyan végezheti el a gyors manuális módosításokat a szolgáltatásban lévő egyes alkalmazásokban. A skálázás másodpercek alatt lép érvénybe, és nincs szükség kód módosítására vagy újratelepítésre.

## <a name="prerequisites"></a>Előfeltételek

Az alábbi eljárások követéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* Egy üzembe helyezett Azure Spring Cloud Service-példány.  Az első lépésekhez kövesse az [alkalmazások Azure CLI-n keresztüli üzembe helyezésével](spring-cloud-quickstart.md) foglalkozó rövid útmutatót.
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

Néhány másodperc elteltével megjelenik az **áttekintő** lap az Áttekintés oldalon, és további részleteket tartalmaz az **alkalmazás példányai** lapon. A skálázáshoz nincs szükség kód módosítására vagy újratelepítésre.

## <a name="upgrade-to-the-standard-tier"></a>Frissítés a Standard szintre
Ha az alapszintű csomaggal rendelkezik, és egy vagy több ilyen [korláttal](spring-cloud-quotas.md)van korlátozva, a standard szintre válthat. Ehhez nyissa meg az árképzési szint menüt úgy, hogy először kiválasztja a standard szint oszlopot, és a **frissítés** gombra kattint.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan lehet manuálisan méretezni egy Azure Spring Cloud-alkalmazást. Ha meg szeretné tudni, hogyan figyelheti az alkalmazást riasztások beállításával, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Tudnivalók a riasztások beállításáról](spring-cloud-tutorial-alerts-action-groups.md)
