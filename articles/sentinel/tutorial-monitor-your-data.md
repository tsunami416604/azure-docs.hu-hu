---
title: Az Azure Sentinel Azure Monitor-munkafüzetei alapján az adatok megjelenítése irányítópultok használatával | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan jelenítheti meg az adatokat az Azure Sentinel ben található munkafüzeteken alapuló irányítópultok használatával.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/01/2020
ms.author: yelevin
ms.openlocfilehash: b4461ac43e9356536914b345ef28f5de62fc9f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585220"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Oktatóanyag: Az adatok megjelenítése és figyelése



Miután [csatlakoztatta az adatforrásokat](quickstart-onboard.md) az Azure Sentinelhez, az Azure Sentinel Azure Monitor-munkafüzetek használatával jelenítheti meg és figyelheti az adatokat, ami sokoldalúságot biztosít az egyéni irányítópultok létrehozásában. Bár a munkafüzetek eltérően jelennek meg az Azure Sentinelben, hasznos lehet, ha megszeretné tekinteni, hogyan [hozhat létre interaktív jelentéseket az Azure Monitor munkafüzetekkel.](../azure-monitor/app/usage-workbooks.md) Az Azure Sentinel lehetővé teszi, hogy egyéni munkafüzeteket hozzon létre az adatok között, és beépített munkafüzetsablonokat is tartalmaz, amelyek lehetővé teszik, hogy gyorsan betekintést nyerjen az adatokba, amint egy adatforrást csatlakoztat.


Ez az oktatóanyag segít az azure Sentinelben az adatok megjelenítésében.
> [!div class="checklist"]
> * Beépített munkafüzetek használata
> * Új munkafüzetek létrehozása

## <a name="prerequisites"></a>Előfeltételek

- Legalább munkafüzet-olvasóval vagy munkafüzet-közreműködői engedélyekkel kell rendelkeznie az Azure Sentinel-munkaterület erőforráscsoportjához.

> [!NOTE]
> Az Azure Sentinelben látható munkafüzetek az Azure Sentinel munkaterületi erőforráscsoportba kerülnek, és azok a munkaterület, amelyben létrehozták őket, címkézik őket.

## <a name="use-built-in-workbooks"></a>Beépített munkafüzetek használata

1. Lépjen a **munkafüzetek,** majd **a Sablonok** lehetőséget az Azure Sentinel beépített munkafüzetek teljes listájának megtekintéséhez. Ha meg szeretné tekinteni, hogy mely adattípusok hoz relevánsak, az egyes munkafüzetek **szükséges adattípusmezője** zöld pipa mellett jelenik meg, ha már releváns adatokat továbbít az Azure Sentinelbe.
  ![ugrás munkafüzetekre](./media/tutorial-monitor-data/access-workbooks.png)
1. Kattintson a **Munkafüzet megtekintése gombra** az adatokkal feltöltött sablon megtekintéséhez.
  
1. A munkafüzet szerkesztéséhez válassza a **Mentés**lehetőséget, majd válassza ki azt a helyet, ahová a sablon jsonfájlját menteni szeretné. 

   > [!NOTE]
   > Ez létrehoz egy Azure-erőforrást a megfelelő sablon alapján, és a sablonT, a Json-fájlt menti, és nem az adatokat.


1. Válassza **a Munkafüzet megtekintése**lehetőséget. Ezután kattintson **a** szerkesztés gombra a tetején. Most már szerkesztheti a munkafüzetet, és igény szerint testreszabhatja. A munkafüzet testreszabásáról további információt az [Azure Monitor-munkafüzetekkel rendelkező interaktív jelentések létrehozása című témakörben talál.](../azure-monitor/app/usage-workbooks.md)
![munkafüzetek megtekintése](./media/tutorial-monitor-data/workbook-graph.png)
1. A módosítások elvégzése után mentheti a munkafüzetet. 

1. Klónozhatja a munkafüzetet is: Válassza a **Szerkesztés,** majd **a Mentés másként**lehetőséget, ügyelve arra, hogy azt egy másik névvel, ugyanazzal az előfizetéssel és erőforráscsoporttal mentse. Ezek a munkafüzetek a **Saját munkafüzetek** lap alatt jelennek meg.


## <a name="create-new-workbook"></a>Új munkafüzet létrehozása

1. Nyissa meg **a Munkafüzetek lehetőséget,** és válassza **a Munkafüzet hozzáadása** lehetőséget, ha teljesen új munkafüzetet szeretne létrehozni.
  ![ugrás munkafüzetekre](./media/tutorial-monitor-data/create-workbook.png)

1. A munkafüzet szerkesztéséhez válassza a **Szerkesztés**lehetőséget, majd szükség szerint adjon hozzá szöveget, lekérdezéseket és paramétereket. A munkafüzet testreszabásáról további információt az [Azure Monitor-munkafüzetekkel rendelkező interaktív jelentések létrehozása című témakörben talál.](../azure-monitor/app/usage-workbooks.md) 

1. Lekérdezés létrehozásakor állítsa be az **adatforrás** **naplók**, az **erőforrás típus** van beállítva **Log Analytics,** majd válassza ki a megfelelő munkaterület(ek). 

1. A munkafüzet létrehozása után mentse a munkafüzetet, és győződjön meg arról, hogy az Azure Sentinel-munkaterület előfizetési és erőforrás-csoportjában menti.

1. Ha azt szeretné, hogy a szervezet többi tagja is használja a munkafüzetet, a Mentés csoportban válassza **a** **Megosztott jelentések**lehetőséget. Ha azt szeretné, hogy ez a munkafüzet csak Ön számára legyen elérhető, válassza a **Saját jelentések**lehetőséget.

1. Ha a munkaterületen lévő munkafüzetek között szeretne váltani, bármelyik munkafüzet felső](./media/tutorial-monitor-data/switch.png)ablaktáblájában **kiválaszthatja** ![a Munkafüzetek megnyitása lehetőséget. A jobb oldalon megnyíló ablakban váltson a munkafüzetek között.

   ![Munkafüzetek váltása](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>Munkafüzetek törlése

Törölheti az Azure Sentinel-sablonból létrehozott munkafüzeteket. 

Testreszabott munkafüzet törléséhez a Munkafüzetek lapon jelölje ki a törölni kívánt mentett munkafüzetet, és válassza a **Törlés**lehetőséget. Ezzel eltávolítja a mentett munkafüzetet.

> [!NOTE]
> Ezzel eltávolítja az erőforrást, valamint a sablonon végzett módosításokat. Az eredeti sablon elérhető marad.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan tekintheti meg az adatokat az Azure Sentinelben.

A fenyegetésekre adott válaszok automatizálásáról az [Automatikus fenyegetésre adott válaszok beállítása az Azure Sentinelben című témakörben](tutorial-respond-threats-playbook.md)olvashat.
