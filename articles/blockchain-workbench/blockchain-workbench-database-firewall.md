---
title: Az Azure Blockchain munkaterület SQL-adatbázis a tűzfal konfigurálása
description: Útmutató az Azure Blockchain munkaterület SQL-adatbázis a tűzfalat.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: dc22f212c014ab1d6622eff3491d669b21ca6f47
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Az Azure Blockchain munkaterület-adatbázis tűzfal konfigurálása

Ez a cikk bemutatja, hogyan konfigurálhatja egy tűzfalszabályt, az Azure portál használatával. Tűzfalszabályok engedélyezheti, hogy külső ügyfelek vagy alkalmazások az Azure Blockchain munkaterület-adatbázis csatlakoznak.

## <a name="connect-to-the-blockchain-workbench-database"></a>Csatlakozás a Blockchain munkaterület-adatbázis

Adatbázishoz való kapcsolódáshoz a ahová lehetőségre olyan szabály konfigurálásához:

1. Jelentkezzen be egy olyan fiókkal, amely rendelkezik az Azure portál **tulajdonos** az Azure Blockchain munkaterület erőforrások engedélyeit.
2. A bal oldali navigációs ablakból válassza **erőforráscsoportok**.
3. Válassza ki a Blockchain munkaterület központi telepítés az erőforráscsoport nevét.
4. Válassza ki **típus** rendezheti az erőforrások listájához, és válassza ki a **SQL server**.
5. Az alábbi képernyőfelvételen erőforrás példa bemutatja a két adatbázis: *fő* és *lsgn-sdk*. A tűzfalszabály konfigurálnia *lsgn-sdk*.

![Lista Blockchain munkaterület erőforrások](media/blockchain-workbench-database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Egy adatbázishoz tartozó tűzfalszabály létrehozása

Egy tűzfalszabály létrehozása:

1. Válassza ki a "lsgn-sdk" adatbázis mutató hivatkozást.
2. A menüsávban válassza **kiszolgáló tűzfalának beállítása**.

   ![Kiszolgálói tűzfal beállítása](media/blockchain-workbench-database-firewall/configure-server-firewall.png)

3. A szervezet olyan szabály létrehozására:

   * Adjon meg egy **szabály neve**
   * Adja meg az IP-címet a **KEZDŐ IP-** címtartomány
   * Adja meg az IP-címet a **záró IP-Címnél** címtartomány

   ![Tűzfalszabály létrehozása](media/blockchain-workbench-database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Ha csak szeretné venni a számítógép IP-címét, válassza a **+ ügyfél IP-cím hozzáadása**.
        
1. A tűzfal-konfiguráció mentéséhez válassza **mentése**.
2. Tesztelje az IP-címtartományt, az alkalmazás vagy az eszköz csatlakozzon az adatbázis konfigurálva. Ha például SQL Server Management Studio.

## <a name="next-steps"></a>További lépések

* [Adatbázis nézetekhez Azure Blockchain munkaterület](blockchain-workbench-database-views.md)