---
title: Verziókövetés integrálása
description: Nagyvállalati szintű adatbázis-DevOps az SQL-készlethez az Azure Repos (git és GitHub) használatával történő natív verziókövetés-integrációval.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: afb1108bacadd16007e1f53186107ea8458d96e9
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85205118"
---
# <a name="source-control-integration-for-sql-pool"></a>Verziókövetés integrálása az SQL-készletbe

Ez az oktatóanyag azt ismerteti, hogyan integrálható a SQL Server Adateszköz-(SSDT-) adatbázis-projekt a verziókövetés használatával.  A verziókövetés integrációja az első lépés a folyamatos integrációs és üzembe helyezési folyamat létrehozásához az SQL Pool-erőforrással az Azure szinapszis Analyticsben.

## <a name="before-you-begin"></a>Előkészületek

- Regisztráció [Azure DevOps-szervezetre](https://azure.microsoft.com/services/devops/)
- A [Létrehozás és összekapcsolás](create-data-warehouse-portal.md) oktatóanyag átugrása
- [A Visual Studio 2019 telepítése](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Az Azure DevOps beállítása és kapcsolódás

1. Az Azure DevOps-szervezetben hozzon létre egy projektet, amely a SSDT adatbázis-projektet egy Azure-tárház adattárán keresztül fogja tárolni

   ![Projekt létrehozása](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Projekt létrehozása")

2. Nyissa meg a Visual studiót, és kapcsolódjon az 1. lépésben az Azure DevOps-szervezethez és-projekthez a kapcsolatok kezelése lehetőség kiválasztásával

   ![Kapcsolatok kezelése](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Kapcsolatok kezelése")

   ![Kapcsolódás](./media/sql-data-warehouse-source-control-integration/3-connect.png "Kapcsolódás")

3. Az Azure-tárház klónozása a projektből a helyi gépre

   ![Klónozási tárház](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Klónozási tárház")

## <a name="create-and-connect-your-project"></a>Projekt létrehozása és összekapcsolása

1. A Visual Studióban hozzon létre egy új SQL Server adatbázis-projektet a **helyi klónozott tárházban** a címtárral és a helyi git-tárházmal

   ![Új projekt létrehozása](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Új projekt létrehozása")  

2. Kattintson a jobb gombbal az üres sqlproject, és importálja az adattárházat az adatbázis-projektbe

   ![Projekt importálása](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Projekt importálása")  

3. A Visual Studióban a Team Explorerben véglegesítse az összes módosítást a helyi git-tárházban

   ![Véglegesítés](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Véglegesítés")  

4. Most, hogy már elvégezte a módosításokat a klónozott tárházban helyileg, szinkronizálja és leküldheti a módosításokat az Azure-beli DevOps-projektben.

   ![Szinkronizálás és leküldéses előkészítés](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Szinkronizálás és leküldéses előkészítés")

   ![Szinkronizálás és leküldés](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Szinkronizálás és leküldés")  

## <a name="validation"></a>Ellenőrzés

1. Ellenőrizze, hogy a módosítások az Azure-tárházba lettek-e küldve az adatbázis-projektben lévő Table oszlop frissítésével a Visual Studio SQL Server Data Tools (SSDT) használatával

   ![Frissítési oszlop érvényesítése](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Frissítési oszlop érvényesítése")

2. Véglegesítse és küldje le a változást a helyi adattárból az Azure-tárházba

   ![Módosítások leküldése](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Módosítások leküldése")

3. Ellenőrizze, hogy a változást leküldte-e az Azure-tárház adattárában

   ![Ellenőrzés](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Módosítások ellenőrzése")

4. (Nem**kötelező**) Séma összevetése és a cél adattárház változásainak frissítése a SSDT használatával annak biztosítása érdekében, hogy az Azure-tárházban és a helyi adattárban lévő objektumok definíciói az adattárházat tükrözzék

## <a name="next-steps"></a>További lépések

- [SQL-készlet fejlesztése](sql-data-warehouse-overview-develop.md)
