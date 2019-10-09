---
title: 'Oktatóanyag: Alkalmazások méretezése az Azure Spring Cloud-ban | Microsoft Docs'
description: Ebből az oktatóanyagból megtudhatja, hogyan méretezheti az alkalmazásokat az Azure Spring Cloud-on Azure Portal
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 10/06/2019
ms.openlocfilehash: bca88cac45e1ba8117eb4e10141e32d621434b86
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038908"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>Oktatóanyag: Alkalmazások méretezése az Azure Spring Cloud-ban

Ez az oktatóanyag bemutatja, hogyan méretezheti a Service-alkalmazásokat a Azure Portal Azure Spring Cloud irányítópultján. A virtuális processzorok (vCPU) számának és a memória mennyiségének módosításával felfelé és lefelé méretezheti az alkalmazást. Az alkalmazás-példányok számának módosításával a-ben és a-ben méretezheti az alkalmazást. Ha elkészült, megtudhatja, hogyan végezheti el a gyors manuális módosításokat a szolgáltatásban lévő alkalmazásokhoz. A skálázás másodpercek alatt lép érvénybe, és nincs szükség kód módosítására vagy újratelepítésre.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 
* Egy üzembe helyezett Azure Spring Cloud Service-példány.  [A kezdéshez kövesse a gyors](spring-cloud-quickstart-launch-app-cli.md) üzembe helyezési útmutatót.
* Legalább egy alkalmazás már létre van hozva a szolgáltatási példányban.


## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Navigáljon a méretezés lapra a Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Navigáljon az Azure Spring Cloud **Áttekintés** oldalára.

1. Nyissa meg az **alkalmazások** fület a **Beállítások** fejléc alatt a bal oldali menüben.

1. Válassza ki a méretezni kívánt alkalmazást. Ebben a példában a "Account-Service" nevű alkalmazást fogjuk méretezni. Ekkor el kell végeznie az alkalmazás **Áttekintés** lapját.

1. Lépjen a **skála** lapra a bal oldali menü **Beállítások** fejlécében. Egy olyan űrlapot kell látnia, amely tartalmazza a korábban említett skálázási attribútumok sorait.

## <a name="scale-your-application"></a>Az alkalmazás méretezése

Módosíthatja a skálázási attribútumokat. Tartsa szem előtt az alábbi megjegyzéseket.

* **Processzorok**: Az engedélyezett processzorok maximális száma 4/alkalmazás példány. Az alkalmazáshoz tartozó processzorok teljes száma az itt megadott érték, amely az alkalmazás példányainak számát szorozza meg.

* **Memória/GB**: Az engedélyezett memória maximális mérete 8 GB/Application példány.  Az alkalmazáshoz tartozó memória teljes mennyisége az itt megadott érték lesz, szorozva az alkalmazás példányainak számával.

* **Példányok száma**: A standard szinten akár 20 példányt is felhasználhat. Ez az érték megváltoztatja a Service-alkalmazás különböző futó példányainak számát.

Ügyeljen rá, hogy a méretezési beállítások alkalmazásához kattintson a **Mentés** gombra.

Néhány másodperc elteltével a végrehajtott módosítások az **Áttekintés** lapon jelennek meg, és további részleteket az **alkalmazás példányai** lapon talál. A skálázáshoz nincs szükség kód módosítására vagy újratelepítésre.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan méretezheti manuálisan az Azure Spring Cloud-alkalmazásait.  Az alkalmazás figyelésének megismeréséhez folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Útmutató az alkalmazás figyeléséhez](spring-cloud-tutorial-distributed-tracing.md)
