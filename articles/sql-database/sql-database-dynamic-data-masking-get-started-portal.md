---
title: 'Azure portal: Dinamikus adatmaszkolás'
description: Az SQL Database dinamikus adatmaszkolásának első lépései az Azure Portalon
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/04/2018
ms.openlocfilehash: a8098b31c6b389b640fc03e756da44c70d9f3a70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722118"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Ismerkedés az SQL Database dinamikus adatmaszkolásával az Azure Portalon

Ez a cikk bemutatja, hogyan valósíthatja meg a [dinamikus adatmaszkolás](sql-database-dynamic-data-masking-get-started.md) az Azure Portalon. Dinamikus adatmaszkolást is megvalósíthat [az Azure SQL Database parancsmagjaival](https://docs.microsoft.com/powershell/module/az.sql/) vagy a REST [API-val.](https://docs.microsoft.com/rest/api/sql/)

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Dinamikus adatmaszkolás beállítása az adatbázishoz az Azure Portal használatával

1. Indítsa el az [https://portal.azure.com](https://portal.azure.com)Azure Portalt a helyen.
2. Nyissa meg az adatbázis azon beállítási lapját, amely tartalmazza a maszkolni kívánt bizalmas adatokat.
3. Kattintson a **Dinamikus adatmaszkolás** csempére, amely elindítja a **Dinamikus adatmaszkolás** konfigurációs lapot.

   * Másik lehetőségként görgessen le a **Műveletek** szakaszhoz, és kattintson a **Dinamikus adatok maszkolása parancsra.**

     ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. A **dinamikus adatok maszkolása** konfigurációs lapon előfordulhat, hogy néhány adatbázis-oszlopok, amelyek a javaslatok motor megjelölt maszkolás. A javaslatok elfogadásához kattintson a **Maszk hozzáadása** egy vagy több oszlophoz elemre, és az oszlop alapértelmezett típusa alapján maszk jön létre. A maszkolási funkciót a maszkolási szabályra kattintva módosíthatja, és a maszkolási mező formátumát más formátumra szerkesztheti. A beállítások mentéséhez kattintson a **Mentés** gombra.

    ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. Ha maszkot szeretne hozzáadni az adatbázis bármely oszlopához, a **Dinamikus adatmaszkolás** konfigurációs lap tetején kattintson a **Maszk hozzáadása** gombra a Maszk **hozzáadása konfigurációs** lap megnyitásához.

    ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. A **maszkoláshoz**kijelölt mező definiálásához válassza a Séma , **tábla** és **oszlop** lehetőséget.
7. Válasszon **maszkolómező-formátumot** a bizalmas adatmaszkolási kategóriák listájából.

    ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. Kattintson a **Mentés gombra** az adatmaszkolási szabály lapon a dinamikus adatmaszkolási házirend maszkolási szabályainak frissítéséhez.
9. Írja be az SQL-felhasználók vagy AAD-identitások, amelyeket ki kell zárni a maszkolás, és hozzáférhet nek a maszkolatlan bizalmas adatokat. Ennek a felhasználók pontosvesszővel elválasztott listájának kell lennie. A rendszergazdai jogosultságokkal rendelkező felhasználók mindig hozzáférhetnek az eredeti maszk nélküli adatokhoz.

    ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > Annak érdekében, hogy az alkalmazásréteg bizalmas adatokat jeleníthessen meg az alkalmazás kiemelt felhasználói számára, adja hozzá az alkalmazás által az adatbázis lekérdezéséhez használt SQL-felhasználó- vagy AAD-identitást. Erősen ajánlott, hogy ez a lista tartalmazza a minimális számú kiemelt felhasználók a bizalmas adatok expozíciójának minimalizálása érdekében.

10. Az új vagy frissített maszkolási házirend mentéséhez kattintson a **Mentés** gombra az adatmaszkolási konfigurációs lapon.

## <a name="next-steps"></a>További lépések

* A dinamikus adatmaszkolás áttekintését a [dinamikus adatmaszkolás](sql-database-dynamic-data-masking-get-started.md)című témakörben találja.
* Dinamikus adatmaszkolást is megvalósíthat [az Azure SQL Database parancsmagjaival](https://docs.microsoft.com/powershell/module/az.sql/) vagy a REST [API-val.](https://docs.microsoft.com/rest/api/sql/)
