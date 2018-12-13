---
title: Hozzon létre és munkaterületek kezelése
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan létrehozása, megtekintése és törlése az Azure Machine Learning szolgáltatás a munkaterületeket, az Azure Portalon.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 73a47929fa26ae0729943e17d0f6c9a054f4f6bb
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276706"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Hozzon létre, és az Azure Machine Learning szolgáltatás munkaterületek kezelése

Ebben a cikkben azt fogjuk létrehozása, megtekintése és törlése [ **Azure Machine Learning szolgáltatás munkaterületek** ](concept-azure-machine-learning-architecture.md#workspace) számára az Azure Portalon [Azure Machine Learning szolgáltatás](overview-what-is-azure-ml.md).  Akkor is törölhetnek és hozhatnak létre munkaterületeket [a parancssori felületről](reference-azure-machine-learning-cli.md) vagy [a Python-kód](https://aka.ms/aml-sdk).

## <a name="create-a-workspace"></a>Munkaterület létrehozása 

Munkaterület létrehozásához Azure-előfizetés szükséges. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](http://aka.ms/AMLFree) még ma.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view-a-workspace"></a>Munkaterület megtekintése

1. A bal felső sarkában a portálon, válassza ki **minden szolgáltatás**. 

1. Az a **minden szolgáltatás** írja be a Szűrő mezőbe **Machine Learning szolgáltatás munkaterület**.  

   ![Keresse meg az Azure Machine Learning szolgáltatás munkaterület](media/how-to-manage-workspace/allservices-search1.png)

1. Az eredmények szűréséhez válassza **Machine Learning szolgáltatás munkaterületén** egy a-munkaterületek listájának megjelenítéséhez. 

   ![Az Azure Machine Learning szolgáltatás munkaterületek listája](media/how-to-manage-workspace/allservices-search.PNG)

1. Nézze át a találhatók munkaterületek listáját. Szűrhet előfizetés alapján, erőforráscsoportok és helyek.  

   ![Munkaterület megtekintése](media/how-to-manage-workspace/allservices_view_workspace.PNG)

1. Válassza ki a munkaterületet, újonnan létrehozott a tulajdonságainak megjelenítéséhez.

   ![Munkaterület tulajdonságai](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Munkaterület törlése

Használja a Törlés gombra, hogy törölni kívánja a munkaterület tetején.

  ![Törlés gomb](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Kövesse a részletes oktatóanyag munkaterület használatával hozhat létre, betanítását és üzembe helyezése a modellek Azure Machine Learning szolgáltatással.

> [!div class="nextstepaction"]
> [Oktatóanyag: Modellek](tutorial-train-models-with-aml.md)
