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
ms.date: 08/14/2019
ms.openlocfilehash: 11287ee5ca0df3e51bca20b39c35eb5ce33b88bd
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231064"
---
1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a használt Azure-előfizetés hitelesítő adataival. 

1. A Azure Portal bal felső sarkában válassza az **+ erőforrás létrehozása**lehetőséget.

      ![Új erőforrás létrehozása](media/aml-create-in-portal/portal-create-resource.png)

1. A keresősáv használatával megkeresheti **Machine learning szolgáltatás**munkaterületét.

1. Válassza **Machine learning szolgáltatás**munkaterületet.

1. A **Machine learning szolgáltatás** munkaterülete ablaktáblán kattintson a **Létrehozás** elemre a kezdéshez.

1. Adja meg az alábbi adatokat az új munkaterület konfigurálásához:

   Mező|Leírás 
   ---|---
   Munkaterület neve |Adjon meg egy egyedi nevet, amely azonosítja a munkaterületet. Ebben a példában a **docs-ws-** t használjuk. A névnek egyedinek kell lennie az erőforráscsoport között. Használjon könnyen felhívható nevet, és a mások által létrehozott munkaterületek megkülönböztetését.  
   Subscription |Válassza ki a használni kívánt Azure-előfizetést.
   Resource group | Használjon egy meglévő erőforráscsoportot az előfizetésben, vagy adjon meg egy nevet egy új erőforráscsoport létrehozásához. Egy erőforráscsoport kapcsolódó erőforrásokat tárol egy Azure-megoldáshoz. Ebben a példában a **docs-pénzmosást**használjuk. 
   Location | Válassza ki a felhasználókhoz legközelebb eső helyet, valamint az adatforrásokat a munkaterület létrehozásához.

1. Miután befejezte a munkaterület konfigurálását, válassza a **Létrehozás**lehetőséget. 

   > [!Warning] 
   > Több percet is igénybe vehet, hogy a munkaterületet a felhőben hozza létre.

   Ha a folyamat elkészült, megjelenik egy központi telepítés sikerességét jelző üzenet. 
 
 1. Az új munkaterület megtekintéséhez válassza az **Ugrás erőforráshoz**lehetőséget.

