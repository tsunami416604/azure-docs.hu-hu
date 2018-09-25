---
title: Létrehozása és kezelése az Azure Machine Learning-munkaterületek
description: Ismerje meg, hogyan hozhat létre, megtekintése és törlése az Azure Machine Learning-munkaterületek az Azure Portalon.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.openlocfilehash: 7d01a2e3ebd46315966c82a43a17ffc5b329b829
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954347"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>Létrehozása és kezelése az Azure Machine Learning-munkaterületek

Ebben a cikkben azt fogjuk létrehozása, megtekintése és törlése [ **Azure Machine Learning-munkaterületek** ](concept-azure-machine-learning-architecture.md#workspace) számára az Azure Portalon [Azure Machine Learning szolgáltatás](overview-what-is-azure-ml.md).  Akkor is törölhetnek és hozhatnak létre munkaterületeket [a parancssori felületről](reference-azure-machine-learning-cli.md) vagy [a Python-kód](http://aka.ms/aml-sdk).

Munkaterület létrehozásához Azure-előfizetés szükséges. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-workspace"></a>Munkaterület létrehozása 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view-a-workspace"></a>Munkaterület megtekintése

1. A bal felső sarkában a portálon, válassza ki **minden szolgáltatás**. 

1. Az a **minden szolgáltatás** írja be a Szűrő mezőbe **Machine Learning-munkaterület**.  

   ![Keresse meg az Azure Machine Learning-munkaterületet](media/how-to-manage-workspace/allservices-search1.png)

1. Az eredmények szűréséhez válassza **Machine Learning-munkaterület** egy a-munkaterületek listájának megjelenítéséhez. 

   ![Keresse meg az Azure Machine Learning-munkaterületet](media/how-to-manage-workspace/allservices-search.PNG)

1. Nézze át a találhatók munkaterületek listáját. Szűrhet előfizetés alapján, erőforráscsoportok és helyek.  

   ![Az Azure Machine Learning-munkaterület listája](media/how-to-manage-workspace/allservices_view_workspace.PNG)

1. Válassza ki a munkaterületet, újonnan létrehozott a tulajdonságainak megjelenítéséhez.

   ![PNG](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Munkaterület törlése

Használja a Törlés gombra, hogy törölni kívánja a munkaterület tetején.

  ![PNG](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Kövesse a részletes oktatóanyag munkaterület használatával hozhat létre, betanítását és üzembe helyezése a modellek Azure Machine Learning szolgáltatással.

> [!div class="nextstepaction"]
> [Oktatóanyag: Vonaton-modellek](tutorial-train-models-with-aml.md)
