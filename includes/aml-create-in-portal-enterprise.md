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
ms.date: 10/04/2019
ms.openlocfilehash: b44d624db419919823ec85bcb599007fb75c92b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73929628"
---
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-előfizetés hitelesítő adatainak használatával. 

1. Az Azure Portal bal felső sarkában válassza a **+ Erőforrás létrehozása**lehetőséget.

      ![Új erőforrás létrehozása](media/aml-create-in-portal/create-workspace.gif)

1. A keresősáv segítségével keresse meg a **Machine Learning et.**

1. Válassza a **Gépi tanulás lehetőséget.**

1. A **Machine Learning** ablaktáblán válassza a **Létrehozás** gombot a kezdéshez.

1. Adja meg a következő információkat az új munkaterület konfigurálásához:

   Mező|Leírás 
   ---|---
   Munkaterület neve |Adjon meg egy egyedi nevet, amely azonosítja a munkaterületet. Ebben a példában **docs-ws**. A neveknek egyedinek kell lenniük az erőforráscsoportban. Használjon könnyen visszahívható nevet, és különböztetse meg a mások által létrehozott munkaterületeket. 
   Előfizetés |Válassza ki a használni kívánt Azure-előfizetést.
   Erőforráscsoport | Használjon egy meglévő erőforráscsoportot az előfizetésben, vagy adjon meg egy nevet egy új erőforráscsoport létrehozásához. Egy erőforráscsoport egy Azure-megoldás kapcsolódó erőforrásait tartalmazza. Ebben a példában **docs-aml**. 
   Hely | Válassza ki a felhasználókhoz legközelebb eső helyet és a munkaterület létrehozásához szükséges adaterőforrásokat.
   Munkaterületi kiadás | Válassza a **Nagyvállalati lehetőséget.** Ez az oktatóanyag az Enterprise kiadás használatát igényli. Az Enterprise kiadás előzetes verzióban érhető el, és jelenleg nem ad többletköltséget. 

1. Miután befejezte a munkaterület konfigurálását, válassza a **Létrehozás gombot.** 

   > [!WARNING] 
   > A munkaterület felhőbeli létrehozása több percig is eltarthat.

   Amikor a folyamat befejeződött, megjelenik egy sikeres telepítési üzenet. 
 
 1. Az új munkaterület megtekintéséhez válassza az **Ugrás az erőforrásra**lehetőséget.

