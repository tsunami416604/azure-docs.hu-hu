---
title: Méretezési számítás a szinapszis SQL-készlethez (Azure Portal)
description: A Azure Portal használatával a szinapszis SQL-készlet (adatraktár) számítási felskálázását végezheti el.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: df0b21e98812faf99b6e67f262cec6e9c29db2f1
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79130260"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Gyors útmutató: a szinapszis SQL-készletre vonatkozó számítási méretezés a Azure Portal

A Azure Portal használatával a szinapszis SQL-készlet (adatraktár) számítási felskálázását végezheti el. [Bővítéssel](sql-data-warehouse-manage-compute-overview.md) a számítások teljesítménye növelhető, szűkítéssel a költségek csökkenthetők. 

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Előkészületek

A már meglévő SQL-készleteket méretezheti, vagy a gyors üzembe helyezési útmutató [: létrehozás és összekapcsolás-Portal](create-data-warehouse-portal.md) használatával hozhat létre egy **mySampleDataWarehouse**nevű SQL-készletet. A jelen gyors útmutató a **mySampleDataWarehouse** nevű adattárház méretezését mutatja be.

>[!IMPORTANT] 
>Az SQL-készletnek online állapotban kell lennie a skálázáshoz. 

## <a name="scale-compute"></a>Számítások méretezése

Az SQL-készlet számítási erőforrásai méretezhetők az adatraktár-egységek növelésével vagy csökkentésével. A [Create and összekapcsolás-Portal] rövid útmutató (create-adatraktár-portal.md) létrehozta a **mySampleDataWarehouse** , és inicializálta azt 400 DWU. Az alábbi lépésekkel módosíthatja a **mySampleDataWarehouse** adattárházban az adattárházegységek számát.

Az adattárházegységek számának módosításához:

1. Az Azure Portal bal oldalán kattintson az **Azure szinapszis Analytics (korábban SQL DW)** elemre.
2. Válassza a **mySampleDataWarehouse** lehetőséget az **Azure szinapszis Analytics (korábban SQL DW)** lapon. Megnyílik az SQL-készlet.
3. Kattintson **Méretezés** lehetőségre.

    ![Méretezés lehetőség választása](media/quickstart-scale-compute-portal/click-scale.png)

2. A Méretezés panelen húzza a csúszkát balra vagy jobbra az adattárházegységek (DWU-k) beállításának módosításához. Ezután válassza a skála lehetőséget.

    ![Csúszka mozgatása](media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni az SQL-készletről, folytassa a [betöltési adatait az SQL-készletbe](load-data-from-azure-blob-storage-using-polybase.md) című oktatóanyagban. 
