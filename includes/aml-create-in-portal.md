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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73633673"
---
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-előfizetés hitelesítő adataival.

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
   Erőforráscsoport | Az előfizetés valamelyik meglévő erőforráscsoportját használja, vagy adjon meg egy nevet új erőforráscsoport létrehozásához. Egy erőforráscsoport egy Azure-megoldás kapcsolódó erőforrásait tartalmazza. Ebben a példában **docs-aml**. 
   Hely | Válassza ki a felhasználókhoz legközelebb eső helyet és a munkaterület létrehozásához szükséges adaterőforrásokat.
   Munkaterületi kiadás | Válassza az **Alapszintű** munkaterület-típusként az oktatóanyaghoz. A munkaterület típusa (Alapszintű & Enterprise) határozza meg azokat a funkciókat, amelyekhez hozzáféréssel és árképzéssel rendelkezik. Az oktatóanyag minden használható alapszintű vagy nagyvállalati munkaterülettel.

1. Miután befejezte a munkaterület konfigurálását, válassza a **Véleményezés + Létrehozás**lehetőséget. 

   > [!Warning] 
   > A munkaterület felhőbeli létrehozása több percig is eltarthat.

   Amikor a folyamat befejeződött, megjelenik egy sikeres telepítési üzenet. 
 
 1. Az új munkaterület megtekintéséhez válassza az **Ugrás az erőforrásra**lehetőséget.

