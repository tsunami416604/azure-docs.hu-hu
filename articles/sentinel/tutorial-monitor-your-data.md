---
title: Az Azure Sentinel Azure Monitor-munkafüzetek használatával jelenítheti meg az adatait | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan jelenítheti meg az adatait az Azure Sentinel-munkafüzetek használatával.
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
ms.date: 05/04/2020
ms.author: yelevin
ms.openlocfilehash: 8d8f1343d92f66dc464ab7064949bbabb813268e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83798536"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Oktatóanyag: az adatai megjelenítése és figyelése



Miután [csatlakoztatta az adatforrásokat](quickstart-onboard.md)az   Azure sentinelhez, az Azure monitor-munkafüzetek Azure Sentinel-bevezetésével megjelenítheti és figyelheti az adatait, ami sokoldalúságot biztosít az egyéni irányítópultok létrehozásához. Míg a munkafüzetek eltérően jelennek meg az Azure Sentinelben, hasznos lehet megtekinteni, hogyan [hozhat létre interaktív jelentéseket Azure monitor-munkafüzetek](../azure-monitor/platform/workbooks-overview.md)használatával. Az Azure Sentinel lehetővé teszi, hogy egyéni munkafüzeteket hozzon létre az adatai között, és a beépített munkafüzet-sablonokkal is lehetővé teszi, hogy az adatforrások összekapcsolását követően gyorsan szerezzen információt az adatelemzésről.


Ez az oktatóanyag segítséget nyújt az Azure Sentinelben tárolt adatai megjelenítéséhez.
> [!div class="checklist"]
> * Beépített munkafüzetek használata
> * Új munkafüzetek létrehozása

## <a name="prerequisites"></a>Előfeltételek

- Az Azure Sentinel munkaterület erőforráscsoporthoz legalább munkafüzet-olvasó vagy munkafüzet közreműködői engedélyekkel kell rendelkeznie.

> [!NOTE]
> Az Azure Sentinelben megtekinthető munkafüzetek az Azure Sentinel-munkaterület erőforráscsoport-csoportjában lesznek mentve, és azokat a munkaterületet címkézik, amelyben létrehozták őket.

## <a name="use-built-in-workbooks"></a>Beépített munkafüzetek használata

1. Nyissa meg a **munkafüzetek** elemet, majd válassza a **sablonok** lehetőséget az Azure Sentinel beépített munkafüzetek teljes listájának megtekintéséhez. Ha szeretné megtekinteni, hogy mely információkra van szüksége a csatlakoztatott adattípusokhoz, az egyes munkafüzetek **kötelező adattípusok** mezője a zöld pipa melletti adattípust sorolja fel, ha már továbbít releváns adatokat az Azure Sentinel szolgáltatásnak.
  ![Ugrás a munkafüzetekhez](./media/tutorial-monitor-data/access-workbooks.png)
1. Kattintson a **munkafüzet megtekintése** elemre az adatokkal feltöltött sablon megtekintéséhez.
  
1. A munkafüzet szerkesztéséhez válassza a **Mentés**lehetőséget, majd válassza ki azt a helyet, ahová menteni szeretné a sablon JSON-fájlját. 

   > [!NOTE]
   > Ez létrehoz egy Azure-erőforrást a megfelelő sablon alapján, és maga menti a sablon JSON-fájlját, és nem az adatokat.


1. Válassza a **munkafüzet megtekintése**lehetőséget. Ezután kattintson a felül található **Szerkesztés** gombra. Most már szerkesztheti a munkafüzetet, és igényei szerint testre is szabhatja. A munkafüzet testreszabásával kapcsolatos további információkért lásd: [interaktív jelentések létrehozása Azure monitor-munkafüzetek](../azure-monitor/platform/workbooks-overview.md)használatával.
![munkafüzetek megtekintése](./media/tutorial-monitor-data/workbook-graph.png)
1. A módosítások elvégzése után mentheti a munkafüzetet. 

1. A munkafüzet klónozása is megtehető: válassza a **Szerkesztés** , majd a **Mentés másként**lehetőséget, hogy egy másik névvel mentse azt az előfizetéshez és az erőforráscsoporthoz. Ezek a munkafüzetek a **saját munkafüzetek** lapon jelennek meg.


## <a name="create-new-workbook"></a>Új munkafüzet létrehozása

1. Nyissa meg a **munkafüzetek** elemet, majd válassza a **munkafüzet hozzáadása** lehetőséget, hogy új munkafüzetet hozzon létre a semmiből.
  ![Ugrás a munkafüzetekhez](./media/tutorial-monitor-data/create-workbook.png)

1. A munkafüzet szerkesztéséhez válassza a **Szerkesztés**lehetőséget, majd szükség szerint adja hozzá a szöveget, a lekérdezéseket és a paramétereket. A munkafüzet testreszabásával kapcsolatos további információkért lásd: [interaktív jelentések létrehozása Azure monitor-munkafüzetek](../azure-monitor/platform/workbooks-overview.md)használatával. 

1. Lekérdezés létrehozásakor állítsa be az **adatforrás** **naplókat**, az **Erőforrás típusa** pedig **log Analytics** , majd válassza ki a megfelelő munkaterületet (ka) t. 

1. Miután létrehozta a munkafüzetet, mentse a munkafüzetet, és mentse azt az Azure Sentinel munkaterület előfizetés és erőforráscsoport területén.

1. Ha azt szeretné, hogy a szervezeten belül mások is használhassák a munkafüzetet, a **Mentés** lehetőség alatt válassza a **megosztott jelentések**lehetőséget. Ha azt szeretné, hogy ez a munkafüzet csak Ön számára legyen elérhető, válassza **a saját jelentések**lehetőséget.

1. A munkaterületen lévő munkafüzetek **közötti váltáshoz** ![ a munkafüzetek felső ablaktábláján válassza a váltás a munkafüzetben lehetőséget ](./media/tutorial-monitor-data/switch.png) . A jobb oldalon megnyíló ablakban váltson a munkafüzetek között.

   ![Munkafüzetek váltása](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>Munkafüzetek törlése

Az Azure Sentinel-sablonból létrehozott munkafüzetek törölhetők. 

Testreszabott munkafüzet törléséhez a munkafüzetek lapon válassza ki a törölni kívánt mentett munkafüzetet, és válassza a **Törlés**lehetőséget. Ezzel törli a mentett munkafüzetet.

> [!NOTE]
> Ezzel eltávolítja az erőforrást, valamint a sablonon végrehajtott módosításokat is. Az eredeti sablon továbbra is elérhető marad.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan tekintheti meg adatait az Azure Sentinelben.

A fenyegetésekre adott válaszok automatizálásával kapcsolatos további információkért lásd: [automatizált veszélyforrásokkal kapcsolatos válaszok beállítása az Azure sentinelben](tutorial-respond-threats-playbook.md).
