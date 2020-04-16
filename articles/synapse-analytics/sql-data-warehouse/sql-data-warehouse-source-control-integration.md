---
title: Verziókövetés integrálása
description: Nagyvállalati szintű Database DevOps-élmény az SQL-készlet natív forrásvezérlési integrációval az Azure Repos (Git és GitHub) használatával.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 3ec52c5274891619cf7976e99b5241bfc67a4076
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415083"
---
# <a name="source-control-integration-for-sql-pool"></a>Forrásvezérlés integrációja AZ SQL-készlethez

Ez az oktatóanyag bemutatja, hogyan integrálható az SQL Server Data tools (SSDT) adatbázis-projekt a forrás-ellenőrzéssel.  A forrásvezérlés integrációja az első lépés a folyamatos integrációs és üzembe helyezési folyamat létrehozása az Azure Synapse Analytics SQL készleterőforrásával.

## <a name="before-you-begin"></a>Előkészületek

- Regisztráció egy [Azure DevOps-szervezetre](https://azure.microsoft.com/services/devops/)
- A [Létrehozás és csatlakoztatás](create-data-warehouse-portal.md) oktatóanyag végighaladva
- [A Visual Studio 2019 telepítése](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Az Azure DevOps beállítása és csatlakozás

1. Az Azure DevOps Organization ben hozzon létre egy olyan projektet, amely az SSDT-adatbázis-projektet egy Azure Repo-tárházon keresztül üzemelteti

   ![Projekt létrehozása](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Projekt létrehozása")

2. Nyissa meg a Visual Studio alkalmazást, és csatlakozzon az Azure DevOps-szervezethez és projekthez az 1.

   ![Kapcsolatok kezelése](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Kapcsolatok kezelése")

   ![Kapcsolódás](./media/sql-data-warehouse-source-control-integration/3-connect.png "Kapcsolódás")

3. Az Azure Repo-tárház klónozása a projektből a helyi számítógépre

   ![Klón tárhét](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Klón tárhét")

## <a name="create-and-connect-your-project"></a>A projekt létrehozása és csatlakoztatása

1. A Visual Studióban hozzon létre egy új SQL Server adatbázis-projektet egy könyvtárral és egy helyi Git tárházzal a **helyi klónozott tárházban**

   ![Új projekt létrehozása](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Új projekt létrehozása")  

2. Kattintson a jobb gombbal az üres sqlprojectre, és importálja az adatraktárt az adatbázisprojektbe

   ![Projekt importálása](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Projekt importálása")  

3. A Visual Studio csapatkezelőjében az összes módosítást véglegesítse a helyi Git-tárházban

   ![Véglegesítés](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Véglegesítés")  

4. Most, hogy a módosításokat helyileg véglegesítették a klónozott tárházban, szinkronizálja és leküldéses a módosításokat az Azure Repo tárház az Azure DevOps-projektben.

   ![Szinkronizálás és leküldéses - előkészítés](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Szinkronizálás és leküldéses - előkészítés")

   ![Szinkronizálás és leküldéses](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Szinkronizálás és leküldéses")  

## <a name="validation"></a>Ellenőrzés

1. Ellenőrizze, hogy a módosítások lelettek-e rendelve az Azure-tárházban azáltal, hogy frissítette az adatbázis-projekt egy táblaoszlopát a Visual Studio SQL Server Data Tools (SSDT) rendszerből.

   ![Frissítési oszlop ellenőrzése](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Frissítési oszlop ellenőrzése")

2. A módosítás véglegesítése és leküldése a helyi tárházból az Azure Repo-ba

   ![Módosítások leküldése](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Módosítások leküldése")

3. Ellenőrizze, hogy a módosítás lelett-e küldése az Azure Repo-tárházban

   ![Ellenőrzés](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Módosítások ellenőrzése")

4. (**Nem kötelező**) A Schema Compare és a changes to your target data warehouse ssdt használatával használatával biztosíthatja, hogy az Azure Repo-tárházban és a helyi tárházban lévő objektumdefiníciók tükrözzék az adatraktárt

## <a name="next-steps"></a>További lépések

- [Fejlesztés az SQL-készlet fejlesztéséhez](sql-data-warehouse-overview-develop.md)
