---
title: "Az Azure SQL Data Warehouse (Azure-portál) számítási teljesítményt kezelése |} Microsoft Docs"
description: "Az Azure portál feladatok kezelésére számítási teljesítményt. Skála dwu-k beállításával számítási erőforrásokat. Vagy, és sablonok felfüggesztése és folytatása a számítási erőforrásokat költségek csökkentése érdekében."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 233b0da5-4abd-4d1d-9586-4ccc5f50b071
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: f56e62576cae0c594f26bcddf44528032bd5ea69
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>Kezelheti a számítási teljesítményt az Azure SQL Data Warehouse (Azure-portál)
Skála számítási erőforrásokat az Azure SQL Data Warehouse az Azure portál használatával.

## <a name="scale-compute-power"></a>Skála számítási teljesítmény
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Számítási erőforrásokat módosítása:

1. Nyissa meg a [Azure-portálon][Azure portal], nyissa meg az adatbázishoz, és kattintson a **méretezési**.

    ![Kattintson a Scale][1]
2. A méretezése a panelen a csúszka jobbra vagy balra módosítása a DWU-beállítására állnak.

    ![Csúszka][2]
3. Kattintson a **Save** (Mentés) gombra. Egy megerősítő üzenet jelenik meg. Kattintson a **Igen** megerősítéséhez vagy **nem** megszakítja a műveletet.

    ![Kattintson a Save (Mentés) gombra.][3]


## <a name="next-steps"></a>További lépések
További információkért lásd: [kezelése-áttekintés][Management overview].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png


<!--Article references-->
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
