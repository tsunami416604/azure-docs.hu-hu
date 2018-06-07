---
title: 'Azure-portálon: SQL-adatbázis dinamikus adatmaszkolási |} Microsoft Docs'
description: SQL-adatbázis dinamikus adatmaszkolási az Azure-portálon az első lépések
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: ronitr
ms.openlocfilehash: 9b47345278bd74449c16e4e17ed1748dba489f82
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645514"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Az SQL-adatbázis dinamikus adatmaszkolási az Azure-portálon az első lépései

Ez a cikk bemutatja, hogyan megvalósítása [dinamikus adatmaszkolási](sql-database-dynamic-data-masking-get-started.md) az Azure portálon. Dinamikus adatok maszkolása használatával is megvalósíthatja [Azure SQL Database parancsmagok](https://msdn.microsoft.com/library/azure/mt574084.aspx) vagy a [REST API](https://msdn.microsoft.com/library/dn505719.aspx).


## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Az Azure portál használata az adatbázis dinamikus adatmaszkolási beállítása
1. Indítsa el az Azure portálon, a [ https://portal.azure.com ](https://portal.azure.com).
2. Keresse meg az adatbázis, amely tartalmazza a bizalmas adatok maszkolandó beállításait tartalmazó oldalt.
3. Kattintson a **dinamikus Adatmaszkolási** csempe, amely elindítja a **dinamikus Adatmaszkolási** konfiguráció lapon.
   
   * Azt is megteheti, hogy is görgessen le a **műveletek** szakaszt, és kattintson **dinamikus Adatmaszkolási**.
     
     ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>
4. Az a **dinamikus Adatmaszkolási** konfigurációs lapon látni maszkolási van megjelölve, a javaslatok motor adatbázis oszlopok. Ahhoz, hogy fogadja el a javaslatok, egyszerűen kattintson **maszk hozzáadása** egy vagy több oszlopot és egy maszk Ez az oszlop alapértelmezett típusa alapján jön létre. A maszkolási függvényt a maszkolási szabályra kattintva, és a maszkolás szerkesztésével módosíthatja az Ön által választott más formátumba mező formátuma. Ügyeljen arra, hogy kattintson **mentése** a beállítások mentéséhez.
   
    ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>
5. Egy maszk bármelyik oszlop tetején, az adatbázis hozzáadása a **dinamikus Adatmaszkolási** konfigurációs lapján kattintson **maszk hozzáadása** megnyitásához a **maszkolás szabály hozzáadása** konfiguráció lap .
   
    ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>
6. Válassza ki a **séma**, **tábla** és **oszlop** a kijelölt mező maszkolási meghatározásához.
7. Válasszon egy **maszkolás mező formátuma** bizalmas adatok maszkolása kategóriák közül.
   
    ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>        
8. Kattintson a **mentése** a a adatmaszkolási szabály lapot a dinamikus adatmaszkolási házirend szabályai maszkolás frissítéséhez.
9. Írja be az SQL-felhasználók vagy AAD identitások, a maszkolásból ki kell zárni, és a maszkolás bizalmas adatokhoz való hozzáférést. Felhasználók pontosvesszővel elválasztott listája legyen. Rendszergazdai jogosultságokkal rendelkező felhasználók mindig hozzáférhetnek az eredeti látható adatok.
   
    ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)
   
   > [!TIP]
   > Ahhoz, az alkalmazási rétegre megjeleníthető kiemelt alkalmazások felhasználók bizalmas adatait, adja hozzá az SQL-felhasználó vagy az adatbázis lekérdezésének használja az alkalmazás AAD-identitása. Erősen ajánlott, hogy a lista tartalmazza-e megfelelő jogosultsággal rendelkező felhasználók a bizalmas adatok felfedésnek minimalizálása érdekében a lehető legkevesebb.
   > 
   > 
10. Kattintson a **mentése** az adatok maszkolása konfigurálólapját menti az új vagy frissített maszkolás házirend.


## <a name="next-steps"></a>További lépések

* Dinamikus adatmaszkolási áttekintését lásd: [dinamikus adatmaszkolási](sql-database-dynamic-data-masking-get-started.md).
* Dinamikus adatok maszkolása használatával is megvalósíthatja [Azure SQL Database parancsmagok](https://msdn.microsoft.com/library/azure/mt574084.aspx) vagy a [REST API](https://msdn.microsoft.com/library/dn505719.aspx).
