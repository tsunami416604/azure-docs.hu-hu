---
title: Hozzon létre, és az Azure Machine Learning szolgáltatás munkaterületek kezelése
description: Ismerje meg, hogyan létrehozása, megtekintése és törlése az Azure Machine Learning szolgáltatás a munkaterületeket, az Azure Portalon.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.openlocfilehash: 2d62e2688c6c17cc96798cb1a560808f6da9c6a9
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242704"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Hozzon létre, és az Azure Machine Learning szolgáltatás munkaterületek kezelése

Ebben a cikkben azt fogjuk létrehozása, megtekintése és törlése [ **Azure Machine Learning szolgáltatás munkaterületek** ](concept-azure-machine-learning-architecture.md#workspace) számára az Azure Portalon [Azure Machine Learning szolgáltatás](overview-what-is-azure-ml.md).  Akkor is törölhetnek és hozhatnak létre munkaterületeket [a parancssori felületről](reference-azure-machine-learning-cli.md) vagy [a Python-kód](https://aka.ms/aml-sdk).

Munkaterület létrehozásához Azure-előfizetés szükséges. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-workspace"></a>Munkaterület létrehozása 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view-a-workspace"></a>Munkaterület megtekintése

1. A bal felső sarkában a portálon, válassza ki **minden szolgáltatás**. 

1. Az a **minden szolgáltatás** írja be a Szűrő mezőbe **Machine Learning szolgáltatás munkaterület**.  

   ![Keresse meg az Azure Machine Learning szolgáltatás munkaterület](media/how-to-manage-workspace/allservices-search1.png)

1. Az eredmények szűréséhez válassza **Machine Learning szolgáltatás munkaterületén** egy a-munkaterületek listájának megjelenítéséhez. 

   ![Keresse meg az Azure Machine Learning szolgáltatás munkaterület](media/how-to-manage-workspace/allservices-search.PNG)

1. Nézze át a találhatók munkaterületek listáját. Szűrhet előfizetés alapján, erőforráscsoportok és helyek.  

   ![Az Azure Machine Learning szolgáltatás munkaterület listája](media/how-to-manage-workspace/allservices_view_workspace.PNG)

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
