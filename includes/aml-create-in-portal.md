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
ms.date: 11/04/2019
ms.openlocfilehash: 8ccd3e6129f4a061eacf83a1f4e70174c697480f
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73633673"
---
1. Az Azure-előfizetéséhez tartozó hitelesítő adatokkal jelentkezzen be [Azure Portalba](https://portal.azure.com/) .

1. A Azure Portal bal felső sarkában válassza az **+ erőforrás létrehozása**lehetőséget.

    ![Új erőforrás létrehozása](media/aml-create-in-portal/create-workspace.gif)

1. A keresősáv használatával megkeresheti **Machine learning**.

1. Válassza a **Machine learning**lehetőséget.

1. A **Machine learning** ablaktáblán kattintson a **Létrehozás** elemre a kezdéshez.

1. Adja meg az alábbi adatokat az új munkaterület konfigurálásához:

   Mező|Leírás 
   ---|---
   Munkaterület neve |Adjon meg egy egyedi nevet, amely azonosítja a munkaterületet. Ebben a példában a **docs-ws-** t használjuk. A névnek egyedinek kell lennie az erőforráscsoport között. Használjon könnyen felhívható nevet, és a mások által létrehozott munkaterületek megkülönböztetését.  
   Előfizetés |Válassza ki a használni kívánt Azure-előfizetést.
   Erőforráscsoport | Használjon egy meglévő erőforráscsoportot az előfizetésben, vagy adjon meg egy nevet egy új erőforráscsoport létrehozásához. Egy erőforráscsoport kapcsolódó erőforrásokat tárol egy Azure-megoldáshoz. Ebben a példában a **docs-pénzmosást**használjuk. 
   Hely | Válassza ki a felhasználókhoz legközelebb eső helyet, valamint az adatforrásokat a munkaterület létrehozásához.
   Munkaterület kiadása | Válassza az **alapszintű** lehetőséget az oktatóanyag munkaterület-típusaként. A munkaterület típusa (alapszintű & Enterprise) meghatározza azokat a szolgáltatásokat, amelyekhez hozzáférésre és díjszabásra van szükség. Az oktatóanyagban szereplő összes adat egy alapszintű vagy egy vállalati munkaterülettel végezhető el.

1. Miután befejezte a munkaterület konfigurálását, válassza a **felülvizsgálat + létrehozás**lehetőséget. 

   > [!Warning] 
   > Több percet is igénybe vehet, hogy a munkaterületet a felhőben hozza létre.

   Ha a folyamat elkészült, megjelenik egy központi telepítés sikerességét jelző üzenet. 
 
 1. Az új munkaterület megtekintéséhez válassza az **Ugrás erőforráshoz**lehetőséget.

