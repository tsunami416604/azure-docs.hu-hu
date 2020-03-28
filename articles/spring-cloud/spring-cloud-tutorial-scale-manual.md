---
title: 'Oktatóanyag: Alkalmazás méretezése az Azure Spring Cloud ban | Microsoft dokumentumok'
description: Ebben az oktatóanyagban megtudhatja, hogyan skálázhat egy alkalmazást az Azure Spring Cloud szolgáltatással az Azure Portalon
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.openlocfilehash: f08a3b5d0a03b0e898457bbb783dd5031c4b0f27
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277476"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Alkalmazás méretezése az Azure Spring Cloudban

Ez az oktatóanyag bemutatja, hogyan skálázhatja a mikroszolgáltatás-alkalmazásokat az Azure Spring Cloud irányítópultjával az Azure Portalon.

Az alkalmazás fel-le méretezése a virtuális processzorok (vCPU-k) és a memória mennyiségének módosításával. Az alkalmazás be- és kiméretezése az alkalmazáspéldányok számának módosításával.

Miután befejezte, tudni fogja, hogyan végezhet gyors manuális módosításokat a szolgáltatás egyes alkalmazásain. A skálázás másodpercek alatt lép érvénybe, és nem igényel kódmódosításokat vagy újratelepítést.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. 
* Üzembe helyezett Azure Spring Cloud szolgáltatáspéldány.  Kövesse [rövid útmutatónkat egy alkalmazás üzembe helyezéséről az Azure CLI-n keresztül az](spring-cloud-quickstart-launch-app-cli.md) első lépésekhez.
* Legalább egy alkalmazás már létrehozott a szolgáltatáspéldányban.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Keresse meg a Méretezés lapot az Azure Portalon

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Nyissa meg az Azure Spring Cloud **Áttekintés lapját.**

1. Válassza ki a szolgáltatást tartalmazó erőforráscsoportot.

1. Válassza az **Alkalmazások** lapot a lap bal oldalán található menü **Beállítások** csoportjában.

1. Jelölje ki a méretezni kívánt alkalmazást. Ebben a példában válassza ki a **fiókszolgáltatás**nevű alkalmazást. Ekkor megjelenik az alkalmazás **áttekintése** oldalon.

1. Nyissa meg a Lap bal oldalán található **Beállítások** lap **Méretezés** lapján található A következő szakaszban láthatók az attribútumok méretezésének beállításai.

## <a name="scale-your-application"></a>Az alkalmazás méretezése

Ha módosítja a méretezési attribútumokat, tartsa szem előtt a következő megjegyzéseket:

* **PROCESSZOROK**: A processzorok maximális száma alkalmazáspéldányonként négy. Egy alkalmazás processzorainak teljes száma az itt beállított érték és az alkalmazáspéldányok számának szorzata.

* **Memória/GB**: Az alkalmazáspéldányonkénti maximális memóriamennyiség 8 GB. Az alkalmazás teljes memóriamennyisége az itt beállított érték és az alkalmazáspéldányok számának szorzata.

* **Alkalmazáspéldányok száma**: A standard szinten legfeljebb 20 példányra skálázható. Ez az érték módosítja a mikroszolgáltatás-alkalmazás külön futó példányainak számát.

Ügyeljen arra, hogy a **Mentés** lehetőséget válassza a méretezési beállítások alkalmazásához.

![A Méretezés szolgáltatás az Azure Portalon](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Néhány másodperc múlva a végrehajtott módosítások megjelennek az **Áttekintés** lapon, további részletek érhetők el az **Alkalmazáspéldányok** lapon.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan skálázhatja manuálisan az Azure Spring Cloud-alkalmazásokat. Ha meg szeretné tudni, hogyan figyelheti az alkalmazást, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Útmutató egy alkalmazás figyeléséhez](spring-cloud-tutorial-distributed-tracing.md)
