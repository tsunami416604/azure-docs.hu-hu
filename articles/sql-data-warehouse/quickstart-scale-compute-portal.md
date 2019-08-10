---
title: 'Gyors útmutató: Számítási felskálázás a Azure SQL Data Warehouse-Azure Portalban | Microsoft Docs'
description: Azure SQL Data Warehouse-beli számítási bővítés az Azure Portalon. Felskálázással a számítások teljesítménye növelhető, leskálázással a költségek csökkenthetők.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: de91b2d681037ae67e14b16934cf72441ef9fd82
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935154"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>Gyors útmutató: Számítások méretezése Azure SQL Data Warehouseban a Azure Portal

Azure SQL Data Warehouse-beli számítási bővítés az Azure Portalon. [Felskálázással](sql-data-warehouse-manage-compute-overview.md) a számítások teljesítménye növelhető, leskálázással a költségek csökkenthetők. 

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="before-you-begin"></a>Előkészületek

Átméretezheti a már meglévő adattárházat, vagy a [Létrehozás és összekapcsolás-portál](create-data-warehouse-portal.md) használatával létrehozhat egy **mySampleDataWarehouse**nevű adattárházat.  A jelen gyors útmutató a **mySampleDataWarehouse** nevű adattárház méretezését mutatja be.

>[!Note]
>Az adattárháznak online állapotban kell lennie a skálázáshoz. 

## <a name="scale-compute"></a>Számítások méretezése

SQL Data Warehouse számítási erőforrások méretezése az adatraktár-egységek növelésével vagy csökkentésével lehetséges. A [Create and összekapcsolás-Portal] rövid útmutató (create-adatraktár-portal.md) létrehozta a **mySampleDataWarehouse** , és inicializálta azt 400 DWU. Az alábbi lépésekkel módosíthatja a **mySampleDataWarehouse** adattárházban az adattárházegységek számát.

Az adattárházegységek számának módosításához:

1. Az Azure Portal bal oldali panelén kattintson az **SQL Data Warehouse-adattárházak** elemre.
2. Az **SQL adattárház** lapon jelölje ki a **mySampleDataWarehouse** elemet. Ekkor megnyílik az adattárház.
3. Kattintson **Méretezés** lehetőségre.

    ![Méretezés lehetőség választása](media/quickstart-scale-compute-portal/click-scale.png)

2. A Méretezés panelen húzza a csúszkát balra vagy jobbra az adattárházegységek (DWU-k) beállításának módosításához.

    ![Csúszka mozgatása](media/quickstart-scale-compute-portal/scale-dwu.png)

3. Kattintson a **Save** (Mentés) gombra. Megjelenik egy megerősítő üzenet. Kattintson az **igen** gombra a megerősítéshez vagy a **nem** gombra az elvetéshez.

    ![Kattintson a Save (Mentés) gombra.](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>További lépések
Most már megtanulta az adattárház számítási felskálázását. Ha bővebb információra van szüksége az Azure SQL Data Warehouse-zal kapcsolatban, folytassa az adatok betöltésével foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
>[Adatok betöltése az SQL Data Warehouse-okba](load-data-from-azure-blob-storage-using-polybase.md)
