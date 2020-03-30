---
title: A Synapse SQL-készlet számítási méretezése (Azure portal)
description: A Synapse SQL-készlet (adattárház) számítási méretezése az Azure Portal használatával.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f92152658b9db83740ffc2de2dc6956003849e06
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350818"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Rövid útmutató: A Synapse SQL-készlet számításainak méretezése az Azure Portalon

A Synapse SQL-készlet (adattárház) számítási méretezése az Azure Portal használatával. [Felskálázással](sql-data-warehouse-manage-compute-overview.md) a számítások teljesítménye növelhető, leskálázással a költségek csökkenthetők. 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="before-you-begin"></a>Előkészületek

A már meglévő SQL-készletet méretezheti, vagy [használhatja a rövid útmutatót: létrehozés és csatlakozás - portál](create-data-warehouse-portal.md) a **mySampleDataWarehouse**nevű SQL-készlet létrehozásához. A jelen gyors útmutató a **mySampleDataWarehouse** nevű adattárház méretezését mutatja be.

>[!IMPORTANT] 
>Az SQL-készletnek online állapotúnak kell lennie a méretezéshez. 

## <a name="scale-compute"></a>Számítások méretezése

SQL készlet számítási erőforrások méretezhető növelésével vagy csökkentésével adattárház egységek. A [create and connect - portal] rövidútmutató(create-data-warehouse-portal.md) létrehozta **a mySampleDataWarehouse-t,** és 400 DWUs-szal inicializálta. Az alábbi lépésekkel módosíthatja a **mySampleDataWarehouse** adattárházban az adattárházegységek számát.

Az adattárházegységek számának módosításához:

1. Kattintson az **Azure Synapse Analytics (korábbi SQL DW)** elemre az Azure Portal bal oldalán.
2. Válassza ki a **mySampleDataWarehouse** az **Azure Synapse Analytics (korábban SQL DW)** lap. Megnyílik az SQL-készlet.
3. Kattintson **Méretezés** lehetőségre.

    ![Méretezés lehetőség választása](./media/quickstart-scale-compute-portal/click-scale.png)

2. A Méretezés panelen húzza a csúszkát balra vagy jobbra az adattárházegységek (DWU-k) beállításának módosításához. Ezután válassza a léptéket.

    ![Csúszka mozgatása](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni az SQL-készletről, folytassa az [Adatok betöltése az SQL-készletbe](load-data-from-azure-blob-storage-using-polybase.md) oktatóanyaggal. 
