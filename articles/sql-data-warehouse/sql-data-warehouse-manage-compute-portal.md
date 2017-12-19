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
ms.openlocfilehash: 63888d5dd103b585cf18e4787d3e779810163e3d
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>Kezelheti a számítási teljesítményt az Azure SQL Data Warehouse (Azure-portál)
> [!div class="op_single_selector"]
> * [Áttekintés](sql-data-warehouse-manage-compute-overview.md)
> * [Portál](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>


## <a name="scale-compute-power"></a>Skála számítási teljesítmény
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Számítási erőforrásokat módosítása:

1. Nyissa meg a [Azure-portálon][Azure portal], nyissa meg az adatbázishoz, és kattintson a **méretezési**.

    ![Kattintson a Scale][1]
2. A méretezése a panelen a csúszka jobbra vagy balra módosítása a DWU-beállítására állnak.

    ![Csúszka][2]
3. Kattintson a **Save** (Mentés) gombra. Egy megerősítő üzenet jelenik meg. Kattintson a **Igen** megerősítéséhez vagy **nem** megszakítja a műveletet.

    ![Kattintson a Save (Mentés) gombra.][3]

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Felfüggesztés számítási
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Egy adatbázis felfüggesztése:

1. Nyissa meg a [Azure-portálon] [ Azure portal] , és nyissa meg az adatbázis. Figyelje meg, hogy az állapot **Online**.

    ![Online állapota][6]
2. Számítási és memória-erőforrások felfüggesztéséhez kattintson **szüneteltetése**, majd egy megerősítő üzenet megjelenik -e. Kattintson a **Igen** megerősítéséhez vagy **nem** megszakítja a műveletet.

    ![Erősítse meg a felfüggesztés][7]
3. Az SQL Data Warehouse megkezdi az adatbázisban, amíg az állapot értéke **felfüggesztése**.
4. Ha az állapot értéke **felfüggesztve**, a szüneteltetési művelete történik, és van már nem kívánt szó, a dwu-k.

    ![Felfüggesztés állapota][4]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Folytatás számítási
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Egy adatbázis folytatása:

1. Nyissa meg a [Azure-portálon] [ Azure portal] , és nyissa meg az adatbázis. Figyelje meg, hogy az állapot **felfüggesztve**.

    ![Felfüggesztés adatbázis][4]
2. Kattintson a adatbázis folytatása **Start**, majd egy megerősítő üzenet megjelenik -e. Kattintson a **Igen** megerősítéséhez vagy **nem** megszakítja a műveletet.

    ![Erősítse meg a folytatási][5]
3. Az SQL Data Warehouse megkezdi az adatbázisban, amíg az állapot értéke "Folytatása".
4. Ha az állapot értéke **online**, az adatbázis készen áll.

    ![Online állapota][6]

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Következő lépések
További információkért lásd: [kezelése-áttekintés][Management overview].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
