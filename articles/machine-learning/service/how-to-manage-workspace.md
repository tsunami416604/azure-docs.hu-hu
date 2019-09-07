---
title: Azure ML-munkaterületek létrehozása a portálon
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan létrehozása, megtekintése és törlése az Azure Machine Learning szolgáltatás a munkaterületeket, az Azure Portalon.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: df015df3c20cdb99512167e3dc3e455da2762c52
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744786"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces-in-the-azure-portal"></a>Azure Machine Learning szolgáltatás-munkaterületek létrehozása és kezelése a Azure Portal

Ebben a cikkben azt fogjuk létrehozása, megtekintése és törlése [ **Azure Machine Learning szolgáltatás munkaterületek** ](concept-workspace.md) számára az Azure Portalon [Azure Machine Learning szolgáltatás](overview-what-is-azure-ml.md).  A portál a legegyszerűbb módszer a munkaterületek használatának megkezdésére, de az igények változásának vagy az automatizálási követelmények növelésének megkezdéséhez létrehozhat és törölhet munkaterületeket [a parancssori felület használatával](reference-azure-machine-learning-cli.md), [Python-kóddal](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) vagy [a vs Code bővítménnyel](how-to-vscode-tools.md#get-started-with-azure-machine-learning).

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Munkaterület létrehozásához Azure-előfizetés szükséges. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning Service ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="download-a-configuration-file"></a>Konfigurációs fájl letöltése

1. Ha [Jegyzetfüzet virtuális gépet](tutorial-1st-experiment-sdk-setup.md#azure)hoz létre, ugorja át ezt a lépést.

1. Ha azt tervezi, hogy a munkaterületre hivatkozó helyi környezet programkódját használja, válassza a munkaterület **Áttekintés** szakaszában található **config. JSON letöltése** lehetőséget.  

   ![A config. JSON fájl letöltése](./media/how-to-manage-workspace/configure.png)
   
   Helyezze a fájlt a címtár-struktúrába a Python-szkriptekkel vagy a Jupyter notebookokkal. Ez lehet ugyanabban a címtárban, egy *. azureml*nevű alkönyvtár vagy egy szülő könyvtárban. Jegyzetfüzet-alapú virtuális gép létrehozásakor a rendszer ezt a fájlt adja hozzá a virtuális gép megfelelő könyvtárába.


## <a name="view"></a>Munkaterület megtekintése

1. A bal felső sarkában a portálon, válassza ki **minden szolgáltatás**.

1. A **minden szolgáltatás** szűrő mezőbe írja be a **Machine learning szolgáltatás**értéket.  

1. Válassza ki **Machine learning szolgáltatási munkaterületeket**.

   ![Azure Machine Learning szolgáltatás munkaterületének keresése](media/how-to-manage-workspace/all-services.png)

1. Nézze át a találhatók munkaterületek listáját. Szűrhet előfizetés alapján, erőforráscsoportok és helyek.  

1. Válasszon ki egy munkaterületet a tulajdonságainak megjelenítéséhez.
   ![Munkaterület tulajdonságai](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Munkaterület törlése

Használja a Törlés gombra, hogy törölni kívánja a munkaterület tetején.

  ![Törlés gomb](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Kövesse a részletes oktatóanyag munkaterület használatával hozhat létre, betanítását és üzembe helyezése a modellek Azure Machine Learning szolgáltatással.

> [!div class="nextstepaction"]
> [Oktatóanyag: Modellek betanítása](tutorial-train-models-with-aml.md)
