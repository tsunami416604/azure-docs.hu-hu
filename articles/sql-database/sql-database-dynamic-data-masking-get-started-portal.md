---
title: 'Azure Portal: dinamikus adatmaszkolás'
description: A SQL Database dinamikus adatmaszkolásának első lépései a Azure Portal
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
ms.date: 03/04/2018
ms.openlocfilehash: 0053958c0681e98ee225232249b050583d4e13b2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826566"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Ismerkedjen meg SQL Database dinamikus adatmaszkolással a Azure Portal

Ez a cikk bemutatja, hogyan valósítható meg a [dinamikus adatmaszkolás](sql-database-dynamic-data-masking-get-started.md) a Azure Portal. A dinamikus adatmaszkolást [Azure SQL Database parancsmagok](https://docs.microsoft.com/powershell/module/az.sql/) vagy a [REST API](https://docs.microsoft.com/rest/api/sql/)használatával is megvalósíthatja.

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Dinamikus adatmaszkolás beállítása az adatbázishoz a Azure Portal használatával

1. Indítsa el a Azure Portalt a [https://portal.azure.com](https://portal.azure.com).
2. Navigáljon a maszkoláshoz használni kívánt bizalmas adatokat tartalmazó adatbázis beállítások lapjára.
3. Kattintson a **dinamikus Adatmaszkolási** csempére, amely elindítja a **dinamikus adatmaszkolás** konfigurációs lapját.

   * Azt is megteheti, hogy legörgeti a **műveletek** szakaszt, majd a **dinamikus adatmaszkolás**lehetőségre kattint.

     ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. A **dinamikus adatmaszkolás** konfigurálása lapon előfordulhat, hogy egyes adatbázis-oszlopok láthatók, amelyeket a javaslatok motorja megjelöl a maszkoláshoz. A javaslatok elfogadásához egyszerűen kattintson a **maszk hozzáadása** elemre egy vagy több oszlophoz, és az oszlop alapértelmezett típusa alapján létrehoz egy maszkot. A maszkolási függvényt úgy módosíthatja, ha a maszkolási szabályra kattint, és a maszkolási mező formátumát más formátumra szerkeszti. A beállítások mentéséhez kattintson a **Mentés** gombra.

    ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. Ha az adatbázis bármely oszlopához hozzá szeretne adni egy maszkot, a **dinamikus adatmaszkolás** konfigurálása lap tetején kattintson a **maszk hozzáadása** elemre a **maszkolási szabály hozzáadása** konfiguráció lap megnyitásához.

    ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. Válassza ki a **sémát**, a **táblát** és az **oszlopot** a maszkoláshoz kijelölt mező definiálásához.
7. A bizalmas adatmaszkolási kategóriák listájából válassza ki a **maszkolási mező formátumát** .

    ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. Az adatmaszkolási szabály lapon a **Mentés** gombra kattintva frissítheti a maszkolási szabályok készletét a dinamikus adatmaszkolási házirendben.
9. Írja be azokat az SQL-felhasználókat vagy HRE identitásokat, amelyeket ki szeretne zárni a maszkolásból, és hogy hozzáférhessen a nem maszkolt bizalmas adatokhoz. Ennek a felhasználóknak pontosvesszővel tagolt listának kell lennie. A rendszergazdai jogosultsággal rendelkező felhasználók mindig hozzáférhetnek az eredeti, kitakaratlan adatbázisokhoz.

    ![Navigációs ablaktábla](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > Annak érdekében, hogy az alkalmazás rétege bizalmas adatokat jelenítsen meg az alkalmazás privilegizált felhasználói számára, adja hozzá azt az SQL-felhasználót vagy HRE-identitást, amelyet az alkalmazás az adatbázis lekérdezéséhez használ. Erősen ajánlott, hogy ez a lista minimális számú jogosultsággal rendelkező felhasználót tartalmazzon a bizalmas adatok expozíciójának minimalizálása érdekében.

10. Az új vagy frissített maszkolási szabályzat mentéséhez kattintson a **Mentés** gombra az adatmaszkolás konfigurálása lapon.

## <a name="next-steps"></a>További lépések

* A dinamikus adatmaszkolás áttekintését lásd: [dinamikus adatmaszkolás](sql-database-dynamic-data-masking-get-started.md).
* A dinamikus adatmaszkolást [Azure SQL Database parancsmagok](https://docs.microsoft.com/powershell/module/az.sql/) vagy a [REST API](https://docs.microsoft.com/rest/api/sql/)használatával is megvalósíthatja.
