---
title: Azure SQL-adatbázis vizsgálata tűzfal mögött
description: Ismerje meg, hogyan vizsgálhatja meg a tűzfal mögötti erőforrásokat az Azure hatáskörébe portál használatával.
author: sudhandkumar
ms.author: kumarsud
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/16/2020
ms.openlocfilehash: 4e1e76aaae2951e6d65fd1ebaaa5798ff417daf9
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552595"
---
# <a name="scan-azure-sql-db-behind-a-firewall-in-azure-purview"></a>Azure SQL-adatbázis vizsgálata tűzfal mögött az Azure hatáskörébe

Ebből a cikkből megtudhatja, hogyan végezheti el a tűzfal mögötti erőforrások vizsgálatát az Azure hatáskörébe.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Saját [Azure Active Directory bérlője](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="set-up-sql-storage-behind-a-firewall"></a>SQL-tároló beállítása tűzfal mögött

Első lépésként adja hozzá a katalógus MSI-t egy Azure SQL-ADATBÁZIShoz a [regisztrálás és a vizsgálat egy Azure SQL Database](register-scan-azure-sql-database.md)használatával.

## <a name="scan-sql-db-from-purview"></a>SQL-adatbázis ellenőrzése a hatáskörébe

1. Tallózással keresse meg a hatáskörébe tartozó kezdőlapot.

1. A bal oldali navigációs sávon válassza a **felügyeleti központ** lehetőséget.

1. Válassza ki **az adatforrásokat** a **források és a vizsgálat** alatt.

1. Válassza az **+ új** lehetőséget az adatforrás hozzáadásához.

1. Válassza a **Azure SQL Database** lehetőséget.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-source.png" alt-text="Képernyőkép az SQL Server kiválasztásához.":::

1. Adja meg az új adatforrás nevét, válasszon az **Azure-előfizetésből**, és válassza ki az előfizetését és a kiszolgáló nevét a legördülő listából.

   A regisztráció befejezéséhez válassza a **Befejezés** lehetőséget. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-register.png" alt-text="Képernyőkép a kijelölés befejezéséhez":::

1. Válassza ki a tűzfal mögötti tárterület és a kapcsolatok **tesztelése beállítást.** A csatlakoztatás azt jelzi, hogy a művelet sikeres. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-setscan.png" alt-text="Képernyőfelvétel: a kiválasztási T0 beállítása a vizsgálathoz.":::

1. Állítsa be a vizsgálat gyakoriságát, és válassza a **Folytatás** lehetőséget.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-once.png" alt-text="Képernyőkép a kijelölésről az SQL-vizsgálat elindításához.":::

1.  A vizsgálat befejeződik, és a rendszer frissíti az állapotot az adatforrások listájában.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-success.png" alt-text="Az üzenet vizsgálatának képernyőképe befejeződött":::
   
## <a name="next-steps"></a>Következő lépések

Ezután beállíthatja a beolvasási szabályt úgy, hogy egy [ellenőrzési szabályt hozzon létre a csoportok vizsgálatához](create-a-scan-rule-set.md) .
