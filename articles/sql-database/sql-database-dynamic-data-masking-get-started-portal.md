---
title: "Azure-portálon: SQL-adatbázis dinamikus adatmaszkolási |} Microsoft Docs"
description: "SQL-adatbázis dinamikus adatmaszkolási az Azure portálon az első lépések"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: 
ms.assetid: "2"
ms.service: sql-database
ms.custom: security
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 11/22/2016
ms.author: ronitr; ronmat
ms.openlocfilehash: 15184e14d4e1e23b56126bbf9f972c1619dcba80
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Ismerkedés az SQL-adatbázis dinamikus adatmaszkolási az Azure portállal

Ez a témakör bemutatja, hogyan megvalósításához [dinamikus adatmaszkolási](sql-database-dynamic-data-masking-get-started.md) az Azure portálon. Dinamikus adatok maszkolása használatával is megvalósíthatja [Azure SQL Database parancsmagok](https://msdn.microsoft.com/library/azure/mt574084.aspx) vagy a [REST API](https://msdn.microsoft.com/library/dn505719.aspx).


## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Az Azure portál használata az adatbázis dinamikus adatmaszkolási beállítása
1. Indítsa el az Azure portálon, a [https://portal.azure.com](https://portal.azure.com).
2. Nyissa meg a beállítások panelről a maszkolandó bizalmas adatokat tartalmazó adatbázis.
3. Kattintson a **dinamikus Adatmaszkolási** csempe, amely elindítja a **dinamikus Adatmaszkolási** konfigurációs panelen.
   
   * Azt is megteheti, hogy is görgessen le a **műveletek** szakaszt, és kattintson **dinamikus Adatmaszkolási**.
     
     ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>
4. Az a **dinamikus Adatmaszkolási** konfigurációs panelen láthatja maszkolási van megjelölve, a javaslatok motor adatbázis oszlopok. Ahhoz, hogy fogadja el a javaslatok, egyszerűen kattintson **maszk hozzáadása** egy vagy több oszlopot és egy maszk jön létre az oszlop alapértelmezett típusa alapján. A maszkolási függvényt a maszkolási szabályra kattintva, és a maszkolás szerkesztésével módosíthatja az Ön által választott más formátumba mező formátuma. Ügyeljen arra, hogy kattintson **mentése** a beállítások mentéséhez.
   
    ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>
5. Egy maszk bármelyik oszlop tetején, az adatbázis hozzáadása a **dinamikus Adatmaszkolási** konfigurációs panelen kattintson **maszk hozzáadása** megnyitásához a **maszkolás szabály hozzáadása** konfigurációs panel
   
    ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>
6. Válassza ki a **séma**, **tábla** és **oszlop** legyen maszkolva a kijelölt mező meghatározásához.
7. Válasszon egy **maszkolás mező formátuma** bizalmas adatok maszkolása kategóriák közül.
   
    ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>        
8. Kattintson a **mentése** a a adatmaszkolási szabály panelt, és a dinamikus adatmaszkolási házirend szabályai maszkolás frissítéséhez.
9. Írja be az SQL-felhasználók vagy AAD identitások, a maszkolásból ki kell zárni, és a maszkolás bizalmas adatokhoz való hozzáférést. Felhasználók pontosvesszővel elválasztott listája legyen. Ne feledje, hogy rendszergazdai jogokkal rendelkező felhasználók mindig az eredeti látható adatok eléréséhez.
   
    ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)
   
   > [!TIP]
   > Ahhoz, az alkalmazási rétegre megjeleníthető kiemelt alkalmazások felhasználók bizalmas adatait, adja hozzá az SQL-felhasználó vagy az adatbázis lekérdezésének használja az alkalmazás AAD-identitása. Erősen ajánlott, hogy a lista tartalmazza-e megfelelő jogosultsággal rendelkező felhasználók a bizalmas adatok felfedésnek minimalizálása érdekében a lehető legkevesebb.
   > 
   > 
10. Kattintson a **mentése** az adatok maszkolása konfigurációs panelt, és mentse az új vagy frissített maszkolás házirend.


## <a name="next-steps"></a>Következő lépések

* Dinamikus adatmaszkolási áttekintését lásd: [dinamikus adatmaszkolási](sql-database-dynamic-data-masking-get-started.md).
* Dinamikus adatok maszkolása használatával is megvalósíthatja [Azure SQL Database parancsmagok](https://msdn.microsoft.com/library/azure/mt574084.aspx) vagy a [REST API](https://msdn.microsoft.com/library/dn505719.aspx).