---
title: Azure Blockchain munkaterület-adatbázis részletek
description: 'Útmutató: Azure Blockchain munkaterület-adatbázis és adatbázis-kiszolgáló adatainak beolvasása.'
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: bf7cc85e823e6630dbd3278bc91fba85f404059f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Az Azure Blockchain munkaterület-adatbázis adatainak beolvasása

Ez a cikk bemutatja, hogyan szerezhet az Azure Blockchain munkaterület-adatbázis részletes információkat.

## <a name="overview"></a>Áttekintés

Alkalmazások, a munkafolyamatok és az intelligens szerződés végrehajtási információt a Blockchain munkaterület SQL DB adatbázis nézeteinek használata. A fejlesztők használhatja ezeket az információkat, amikor eszközöket, például a Microsoft Excel, a Power BI, a Visual Studio és az SQL Server Management Studio használatával.

Egy fejlesztő tud kapcsolódni az adatbázishoz, mielőtt van szükségük:

* Az adatbázis tűzfal engedélyezett külső ügyfélhozzáférés. Ez a cikk egy adatbázis tűzfal cikk konfigurálásával kapcsolatos való hozzáférés engedélyezése módját ismerteti.
* Az adatbázis-kiszolgáló nevét és az adatbázis nevét.

## <a name="connect-to-the-blockchain-workbench-database"></a>Csatlakozás a Blockchain munkaterület-adatbázis

Csatlakozni az adatbázishoz:

1. Jelentkezzen be egy olyan fiókkal, amely rendelkezik az Azure portál **tulajdonos** az Azure Blockchain munkaterület erőforrások engedélyeit.
2. A bal oldali navigációs ablakból válassza **erőforráscsoportok**.
3. Válassza ki a Blockchain munkaterület központi telepítés az erőforráscsoport nevét.
4. Válassza ki **típus** rendezheti az erőforrás-listát, és válassza ki a **SQL server**. A következő képernyőfelvételen rendezett lista megjeleníti a két SQL-adatbázis, "fő" és "lhgn" használnak a **erőforrás előtag**.

   ![Rendezett Blockchain munkaterület erőforrások listája](media/blockchain-workbench-getdb-details/sorted-workbench-resource-list.png)

5. A Blockchain munkaterület-adatbázis kapcsolatos részletes információk megtekintéséhez jelölje ki az adatbázis hivatkozást a **erőforrás előtag** megadott Blockchain munkaterület központi telepítéséhez.

   ![Az adatbázis adatainak megadása](media/blockchain-workbench-getdb-details/workbench-db-details.png)

Az adatbázis-kiszolgáló nevét és az adatbázis nevét lehetővé teszik, hogy a Blockchain munkaterület-adatbázis a fejlesztési használatával, vagy a jelentéskészítő eszköz csatlakozni.

## <a name="next-steps"></a>További lépések

* [Adatbázis nézetekhez Azure Blockchain munkaterület](blockchain-workbench-database-views.md)