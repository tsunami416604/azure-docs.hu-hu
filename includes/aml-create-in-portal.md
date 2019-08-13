---
title: fájl belefoglalása
description: fájl belefoglalása
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 07/31/2019
ms.openlocfilehash: 0a497ce506e1aa3b0f9afc47bf1ab8382c9c0405
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68951429"
---
1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a használt Azure-előfizetés hitelesítő adataival. 

1. Azure Portal bal felső sarkában válassza az **erőforrás létrehozása**lehetőséget.

1. A keresősáv használatával megkeresheti **Machine learning szolgáltatás**munkaterületét.

1. Válassza **Machine learning szolgáltatás**munkaterületet.

1. A **ml szolgáltatás** munkaterülete ablaktáblán kattintson a **Létrehozás** elemre a kezdéshez.

1. Konfigurálja az új munkaterületet úgy, hogy megadja a munkaterület nevét, az előfizetést, az erőforráscsoportot és a helyet.

    ![Munkaterület létrehozása](./media/aml-create-in-portal/workspace-create-main-tab.png)

   Mező|Leírás 
   ---|---
   Munkaterület neve |Adjon meg egy egyedi nevet, amely azonosítja a munkaterületet. Ebben a példában a **docs-ws-** t használjuk. A névnek egyedinek kell lennie az erőforráscsoport között. Használjon könnyen felhívható nevet, és a mások által létrehozott munkaterületek megkülönböztetését.  
   Subscription |Válassza ki a használni kívánt Azure-előfizetést.
   Resource group | Használjon egy meglévő erőforráscsoportot az előfizetésben, vagy adjon meg egy nevet egy új erőforráscsoport létrehozásához. Egy erőforráscsoport kapcsolódó erőforrásokat tárol egy Azure-megoldáshoz. Ebben a példában a **docs-pénzmosást**használjuk. 
   Location | Válassza ki a felhasználókhoz legközelebb eső helyet, valamint az adatforrásokat a munkaterület létrehozásához.

1. Miután befejezte a munkaterület konfigurálását, válassza a **Létrehozás**lehetőséget. 

   A munkaterület létrehozása néhány percet is igénybe vehet.

   Ha a folyamat elkészült, megjelenik egy központi telepítés sikerességét jelző üzenet. Az értesítések szakaszban is megtalálható. Az új munkaterület megtekintéséhez válassza az **Ugrás erőforráshoz**lehetőséget.

   ![Munkaterület-létrehozási állapot](./media/aml-create-in-portal/notifications.png)

1. Ha a kód a helyi környezetből erre a munkaterületre hivatkozik, válassza a munkaterület **Áttekintés** szakaszában a **config. JSON letöltése** lehetőséget.  

   ![A config. JSON fájl letöltése](./media/aml-create-in-portal/configure.png)

    Helyezze ezt a JSON-fájlt a Python-szkripteket vagy Jupyter-jegyzetfüzeteket tartalmazó címtár-struktúrába. Ez lehet ugyanabban a címtárban, egy *. azureml*nevű alkönyvtár vagy egy szülő könyvtárban.

   Ha [notebookos virtuális gépet](../articles/machine-learning/service/tutorial-1st-experiment-sdk-setup.md#azure) használ, nincs szükség a fájl letöltésére, a rendszer hozzáadja a virtuális gép megfelelő könyvtárához.
