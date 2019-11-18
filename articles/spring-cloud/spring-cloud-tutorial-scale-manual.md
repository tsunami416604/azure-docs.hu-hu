---
title: 'Oktatóanyag: alkalmazások méretezése az Azure Spring Cloud-ban | Microsoft Docs'
description: Ebből az oktatóanyagból megtudhatja, hogyan méretezheti az alkalmazásokat az Azure Spring Cloud-on Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/06/2019
ms.openlocfilehash: cce7562c74577f6fd545bcaed3ee3e0968fd40b4
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132910"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>Oktatóanyag: alkalmazások méretezése az Azure Spring Cloud-ban

Ez az oktatóanyag bemutatja, hogyan méretezheti a Service-alkalmazásokat a Azure Portal Azure Spring Cloud irányítópultján. A virtuális processzorok (vCPU) számának és a memória mennyiségének módosításával felfelé és lefelé méretezheti az alkalmazást. Az alkalmazás-példányok számának módosításával a-ben és a-ben méretezheti az alkalmazást. Ha elkészült, megtudhatja, hogyan végezheti el a gyors manuális módosításokat a szolgáltatásban lévő alkalmazásokhoz. A skálázás másodpercek alatt lép érvénybe, és nincs szükség kód módosítására vagy újratelepítésre.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 
* Egy üzembe helyezett Azure Spring Cloud Service-példány.  [A kezdéshez kövesse a gyors](spring-cloud-quickstart-launch-app-cli.md) üzembe helyezési útmutatót.
* Legalább egy alkalmazás már létre van hozva a szolgáltatási példányban.


## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Navigáljon a méretezés lapra a Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Navigáljon az Azure Spring Cloud **Áttekintés** oldalára.

1. Válassza ki a szolgáltatást tartalmazó erőforráscsoportot.

1. Nyissa meg az **alkalmazások** fület a **Beállítások** fejléc alatt a bal oldali menüben.

1. Válassza ki a méretezni kívánt alkalmazást. Ebben a példában a "Account-Service" nevű alkalmazást fogjuk méretezni. Ekkor el kell végeznie az alkalmazás **Áttekintés** lapját.

1. Lépjen a **skála** lapra a bal oldali menü **Beállítások** fejlécében. Ekkor meg kell jelennie az alábbi szakaszban látható skálázási attribútumok lehetőségeinek.

## <a name="scale-your-application"></a>Az alkalmazás méretezése

Módosíthatja a skálázási attribútumokat. Tartsa szem előtt az alábbi megjegyzéseket.

* **Processzorok**: a processzorok maximálisan megengedett száma 4 az alkalmazás példányai esetében. Az alkalmazáshoz tartozó processzorok teljes száma az itt megadott érték, amely az alkalmazás példányainak számát szorozza meg.

* **Memória/GB**: az alkalmazás-példányok maximálisan megengedett mérete 8 GB.  Az alkalmazáshoz tartozó memória teljes mennyisége az itt megadott érték lesz, szorozva az alkalmazás példányainak számával.

* **Alkalmazás-példányok száma**: a standard szinten akár 20 példányt is felhasználhat. Ez az érték megváltoztatja a Service-alkalmazás különböző futó példányainak számát.

Ügyeljen rá, hogy a méretezési beállítások alkalmazásához kattintson a **Save (Mentés** ) gombra.

![Szolgáltatás méretezése Azure Portal](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Néhány másodperc elteltével a végrehajtott módosítások az **Áttekintés** lapon jelennek meg, az **alkalmazás példányai** lapon pedig további részletek érhetők el. a skálázás nem igényli a kód módosítását vagy az újratelepítést.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan méretezheti manuálisan az Azure Spring Cloud-alkalmazásait.  Az alkalmazás figyelésének megismeréséhez folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Útmutató az alkalmazás figyeléséhez](spring-cloud-tutorial-distributed-tracing.md)
