---
title: Azure Machine Learning-munkaterületek létrehozása a portálon
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre, tekinthet meg és törölhet Azure Machine Learning munkaterületeket a Azure Portalban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: 4f31c5583671ed3441e1a3f0f53c8518e550080a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355214"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Azure Machine Learning-munkaterületek létrehozása és kezelése az Azure Portalon
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben [**Azure Machine learning munkaterületeket**](concept-workspace.md) hoz létre, tekinthet meg és törölhet a [Azure Machine learning](overview-what-is-azure-ml.md)Azure Portal.  A portál a legegyszerűbb módszer a munkaterületek használatának megkezdésére, de az igények változásának vagy az automatizálási követelmények növelésének megkezdéséhez létrehozhat és törölhet munkaterületeket [a parancssori felület használatával](reference-azure-machine-learning-cli.md), [Python-kóddal](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) vagy [a vs Code bővítménnyel](tutorial-setup-vscode-extension.md).

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Munkaterület létrehozásához Azure-előfizetés szükséges. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) az Azure-előfizetéséhez tartozó hitelesítő adatok használatával. 

1. A Azure Portal bal felső sarkában válassza az **+ erőforrás létrehozása**lehetőséget.

      ![Új erőforrás létrehozása](./media/how-to-manage-workspace/create-workspace.gif)

1. A keresősáv használatával megkeresheti **Machine learning**.

1. Válassza a **Machine learning**lehetőséget.

1. A **Machine learning** ablaktáblán kattintson a **Létrehozás** elemre a kezdéshez.

1. Adja meg az alábbi adatokat az új munkaterület konfigurálásához:

   Mező|Leírás 
   ---|---
   Munkaterület neve |Adjon meg egy egyedi nevet, amely azonosítja a munkaterületet. Ebben a példában a **docs-ws-** t használjuk. A névnek egyedinek kell lennie az erőforráscsoport között. Használjon könnyen felhívható nevet, és a mások által létrehozott munkaterületek megkülönböztetését. A munkaterület neve megkülönbözteti a kis-és nagybetűket.
   Előfizetést |Válassza ki a használni kívánt Azure-előfizetést.
   Erőforráscsoport | Használjon egy meglévő erőforráscsoportot az előfizetésben, vagy adjon meg egy nevet egy új erőforráscsoport létrehozásához. Egy erőforráscsoport kapcsolódó erőforrásokat tárol egy Azure-megoldáshoz. Ebben a példában a **docs-pénzmosást**használjuk. 
   Hely | Válassza ki a felhasználókhoz legközelebb eső helyet, valamint az adatforrásokat a munkaterület létrehozásához.
   Munkaterület kiadása | Válassza az **Alap** vagy a **vállalat**lehetőséget.  Ez a munkaterület-kiadás határozza meg azokat a szolgáltatásokat, amelyekhez hozzáférésre és díjszabásra van szüksége. További információ az [alapszintű és a nagyvállalati kiadási ajánlatokról](overview-what-is-azure-ml.md#sku). 

    ![Munkaterület konfigurálása](./media/how-to-manage-workspace/select-edition.png)

1. Miután befejezte a munkaterület konfigurálását, válassza a **Létrehozás**lehetőséget. 

   > [!Warning] 
   > Több percet is igénybe vehet, hogy a munkaterületet a felhőben hozza létre.

   Ha a folyamat elkészült, megjelenik egy központi telepítés sikerességét jelző üzenet. 
 
 1. Az új munkaterület megtekintéséhez válassza az **Ugrás erőforráshoz**lehetőséget.

### <a name="download-a-configuration-file"></a>Konfigurációs fájl letöltése

1. Ha [számítási példányt](tutorial-1st-experiment-sdk-setup.md#azure)fog létrehozni, hagyja ki ezt a lépést.

1. Ha azt tervezi, hogy a munkaterületre hivatkozó helyi környezet programkódját használja, válassza a munkaterület **Áttekintés** szakaszában található **config. JSON letöltése** lehetőséget.  

   ![A config. JSON fájl letöltése](./media/how-to-manage-workspace/configure.png)
   
   Helyezze a fájlt a címtár-struktúrába a Python-szkriptekkel vagy a Jupyter notebookokkal. Ez lehet ugyanabban a címtárban, egy *. azureml*nevű alkönyvtár vagy egy szülő könyvtárban. Számítási példány létrehozásakor a rendszer hozzáadja ezt a fájlt a virtuális gép megfelelő könyvtárába.

## <a name="upgrade"></a>Frissítés a Enterprise Edition verzióra

A munkaterületet az alapszintű kiadásról a nagyvállalati kiadásra frissítheti, így kihasználhatja a fejlett funkciók előnyeit, például az alacsony kódú élményt és a fokozott biztonsági funkciókat.

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com).

1. Válassza ki a frissíteni kívánt munkaterületet.

1. Kattintson a lap jobb felső részén található **További információ** lehetőségre.

   [munkaterület ![frissítése](./media/how-to-manage-workspace/upgrade.png)](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. Válassza a **frissítés** lehetőséget a megjelenő ablakban.


> [!IMPORTANT]
> Az Enterprise Edition-munkaterületeket nem lehet alapszintű kiadási munkaterületre visszaminősíteni. 

## <a name="view"></a>Munkaterület keresése

1. A felső Keresés mezőbe írja be a következőt: **Machine learning**.  

1. Válassza a **Machine learning**lehetőséget.

   ![Azure Machine Learning munkaterület keresése](./media/how-to-manage-workspace/find-workspaces.png)

1. Nézze át a találhatók munkaterületek listáját. Szűrhet előfizetés alapján, erőforráscsoportok és helyek.  

1. Válasszon ki egy munkaterületet a tulajdonságainak megjelenítéséhez.

## <a name="delete-a-workspace"></a>Munkaterület törlése

Használja a Törlés gombra, hogy törölni kívánja a munkaterület tetején.

  ![Törlés gomb](./media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Hibakeresés

### <a name="resource-provider-errors"></a>Erőforrás-szolgáltatói hibák

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>A munkaterület áthelyezése

> [!WARNING]
> Ha áthelyezi a Azure Machine Learning munkaterületet egy másik előfizetésbe, vagy áthelyezi a tulajdonosi előfizetést egy új bérlőre, nem támogatott. Ez hibákhoz vezethet.

### <a name="deleting-the-azure-container-registry"></a>A Azure Container Registry törlése

A Azure Machine Learning munkaterület egyes műveletekhez Azure Container Registry (ACR) használ. Automatikusan létrehoz egy ACR-példányt, amikor először szüksége lesz rá.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Következő lépések

Kövesse a teljes hosszúságú oktatóanyagot, amelyből megtudhatja, hogyan hozhat létre, taníthat és helyezhet üzembe modelleket Azure Machine Learning használatával a munkaterületen.

> [!div class="nextstepaction"]
> [Oktatóanyag: modellek betanítása](tutorial-train-models-with-aml.md)
