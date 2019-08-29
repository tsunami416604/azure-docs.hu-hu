---
title: Verziókövetés integrációja | Microsoft Docs
description: Nagyvállalati szintű adatbázis-DevOps az Azure Repos (git és GitHub) használatával történő natív verziókövetés-integrációs SQL Data Warehousehoz.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 10a10882efe05ef9e6bb86e54fcfcf8c5d73d225
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098741"
---
# <a name="source-control-integration-for-azure-sql-data-warehouse"></a>Verziókövetés integrációja Azure SQL Data Warehouse

Ez az oktatóanyag azt ismerteti, hogyan integrálható a SQL Server Adateszköz-(SSDT-) adatbázis-projekt a verziókövetés használatával.  A verziókövetés integrációja az első lépés a folyamatos integrációs és üzembe helyezési folyamat kiépítéséhez SQL Data Warehousesal. 

## <a name="before-you-begin"></a>Előkészületek

- Regisztráció [Azure DevOps](https://azure.microsoft.com/services/devops/) -szervezetre
- A [Létrehozás és összekapcsolás](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal) oktatóanyag átugrása
-  [A Visual Studio 2019 telepítése](https://visualstudio.microsoft.com/vs/older-downloads/) 

## <a name="set-up-and-connect-to-azure-devops"></a>Az Azure DevOps beállítása és kapcsolódás

1. Az Azure DevOps-szervezetben hozzon létre egy projektet, amely a SSDT adatbázis-projektet egy Azure-tárház adattárán keresztül fogja tárolni

   ![Projekt létrehozása](media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Projekt létrehozása")

2. Nyissa meg a Visual studiót, és kapcsolódjon az 1. lépésben az Azure DevOps-szervezethez és-projekthez a kapcsolatok kezelése lehetőség kiválasztásával

   ![Kapcsolatok kezelése](media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Kapcsolatok kezelése")

   ![Csatlakozási kapcsolat](media/sql-data-warehouse-source-control-integration/3-connect.png "Csatlakozási kapcsolat")

3. Az Azure-tárház klónozása a projektből a helyi gépre

   ![Klónozási] tárház (media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Klónozási") tárház

## <a name="create-and-connect-your-project"></a>Projekt létrehozása és összekapcsolása

1. A Visual Studióban hozzon létre egy új SQL Server adatbázis-projektet a **helyi klónozott tárházban** a címtárral és a helyi git-tárházmal

   ![Új projekt létrehozása](media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Új projekt létrehozása")  

2. Kattintson a jobb gombbal az üres sqlproject, és importálja az adattárházat az adatbázis-projektbe

   ![Projekt importálása](media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Projekt importálása")  

3. A Visual Studióban a Team Explorerben véglegesítse az összes módosítást a helyi git-tárházban 

   ![Véglegesítés](media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Véglegesítés")  

4. Most, hogy már elvégezte a módosításokat a klónozott tárházban helyileg, szinkronizálja és leküldheti a módosításokat az Azure-beli DevOps-projektben.

   ![Szinkronizálás és leküldéses előkészítés](media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Szinkronizálás és leküldéses előkészítés")

   ![Szinkronizálás és] leküldés (media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Szinkronizálás és") leküldés  

## <a name="validation"></a>Érvényesítés

1. Ellenőrizze, hogy a módosítások az Azure-tárházba lettek-e küldve az adatbázis-projektben lévő Table oszlop frissítésével a Visual Studio SQL Server Data Tools (SSDT) használatával

   ![Frissítési oszlop érvényesítése](media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Frissítési oszlop érvényesítése")

2. Véglegesítse és küldje le a változást a helyi adattárból az Azure-tárházba

   ![Leküldéses módosítások](media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Leküldéses módosítások")

3. Ellenőrizze, hogy a változást leküldte-e az Azure-tárház adattárában

   ![Ellenőrzés](media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Módosítások ellenőrzése")

4. (Nem**kötelező**) Séma összevetése és a cél adattárház változásainak frissítése a SSDT használatával annak biztosítása érdekében, hogy az Azure-tárházban és a helyi adattárban lévő objektumok definíciói az adattárházat tükrözzék

## <a name="next-steps"></a>További lépések

- [Fejlesztés Azure SQL Data Warehouse](sql-data-warehouse-overview-develop.md)

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->

