---
title: 'Azure Portal: dinamikus adatmaszkolás'
description: A Azure SQL Database dinamikus adatmaszkolásának első lépései a Azure Portal
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/28/2020
ms.openlocfilehash: 12a187b29737ba9388d9b33ac8bcfedf1339c059
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253783"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Ismerkedjen meg SQL Database dinamikus adatmaszkolással a Azure Portal
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk bemutatja, hogyan valósítható meg a [dinamikus adatmaszkolás](dynamic-data-masking-overview.md) a Azure Portal. A dinamikus adatmaszkolást [Azure SQL Database parancsmagok](https://docs.microsoft.com/powershell/module/az.sql/) vagy a [REST API](https://docs.microsoft.com/rest/api/sql/)használatával is megvalósíthatja.

> [!NOTE]
> Ez a funkció nem állítható be az Azure szinapszis portál használatával (PowerShell vagy REST API használata) vagy az SQL felügyelt példányával. További információ: [dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Dinamikus adatmaszkolás beállítása az adatbázishoz a Azure Portal használatával

1. Indítsa el a Azure Portal a következő helyen: [https://portal.azure.com](https://portal.azure.com) .
2. Navigáljon a maszkoláshoz használni kívánt bizalmas adatokat tartalmazó adatbázis beállítások lapjára.
3. Kattintson a **dinamikus adatmaszkolás** panelre az adatbázis **Biztonság** szakaszában.

   ![Navigációs panel](./media/dynamic-data-masking-configure-portal/4_ddm_settings_tile.png)

4. A **dinamikus adatmaszkolás** konfigurálása lapon előfordulhat, hogy egyes adatbázis-oszlopok láthatók, amelyeket a javaslatok motorja megjelöl a maszkoláshoz. A javaslatok elfogadásához egyszerűen kattintson a **maszk hozzáadása** elemre egy vagy több oszlophoz, és az oszlop alapértelmezett típusa alapján létrehoz egy maszkot. A maszkolási függvényt úgy módosíthatja, ha a maszkolási szabályra kattint, és a maszkolási mező formátumát más formátumra szerkeszti. A beállítások mentéséhez kattintson a **Mentés** gombra.

    ![Navigációs panel](./media/dynamic-data-masking-configure-portal/5_ddm_recommendations.png)

5. Ha az adatbázis bármely oszlopához hozzá szeretne adni egy maszkot, a **dinamikus adatmaszkolás** konfigurálása lap tetején kattintson a **maszk hozzáadása** elemre a **maszkolási szabály hozzáadása** konfiguráció lap megnyitásához.

    ![Navigációs panel](./media/dynamic-data-masking-configure-portal/6_ddm_add_mask.png)

6. Válassza ki a **sémát**, a **táblát** és az **oszlopot** a maszkoláshoz kijelölt mező definiálásához.
7. **Válassza ki, hogyan legyen maszk** a bizalmas adatmaszkolási kategóriák listájából.

    ![Navigációs panel](./media/dynamic-data-masking-configure-portal/7_ddm_mask_field_format.png)

8. Kattintson a **Hozzáadás** elemre az adatmaszkolási szabály lapon a maszkolási szabályok a dinamikus adatmaszkolási házirendben való frissítéséhez.
9. Írja be azokat az SQL-felhasználókat vagy Azure Active Directory (Azure AD) identitásokat, amelyeket ki szeretne zárni a maszkolásból, és hozzáférhessen a nem maszkolt bizalmas adatokhoz. Ennek a felhasználóknak pontosvesszővel tagolt listának kell lennie. A rendszergazdai jogosultsággal rendelkező felhasználók mindig hozzáférhetnek az eredeti, kitakaratlan adatbázisokhoz.

    ![Navigációs panel](./media/dynamic-data-masking-configure-portal/8_ddm_excluded_users.png)

    > [!TIP]
    > Annak érdekében, hogy az alkalmazás rétege bizalmas adatokat jelenítsen meg az alkalmazás privilegizált felhasználói számára, adja hozzá az SQL-felhasználót vagy az Azure AD-identitást, amelyet az alkalmazás az adatbázis lekérdezéséhez használ. Erősen ajánlott, hogy ez a lista minimális számú jogosultsággal rendelkező felhasználót tartalmazzon a bizalmas adatok expozíciójának minimalizálása érdekében.

10. Az új vagy frissített maszkolási szabályzat mentéséhez kattintson a **Mentés** gombra az adatmaszkolás konfigurálása lapon.

## <a name="next-steps"></a>További lépések

- A dinamikus adatmaszkolás áttekintését lásd: [dinamikus adatmaszkolás](dynamic-data-masking-overview.md).
- A dinamikus adatmaszkolást [Azure SQL Database parancsmagok](https://docs.microsoft.com/powershell/module/az.sql/) vagy a [REST API](https://docs.microsoft.com/rest/api/sql/)használatával is megvalósíthatja.
