---
title: Az Azure Blockchain Workbench-adatbázis adatainak lekérése
description: Megtudhatja, hogyan szerezheti be az Azure Blockchain Workbench Preview adatbázis- és adatbázis-kiszolgálói adatait.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 2b3190a9d042be8ead1ff3d5ef48d4a2a19e8963
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324691"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Az Azure Blockchain Workbench-adatbázis adatainak lekérése

Ez a cikk bemutatja, hogyan kaphat részletes információkat az Azure Blockchain Workbench Preview-adatbázisról.

## <a name="overview"></a>Áttekintés

Az alkalmazások, a munkafolyamatok és az intelligens szerződések végrehajtásának adatait a Blockchain Workbench SQL-adatbázisa adatbázisnézetek formájában tárolja. A fejlesztők ezt az információt olyan eszközök használata esetén használhatják, mint a Microsoft Excel, a Power BI, a Visual Studio és az SQL Server Management Studio.

Ahhoz, hogy egy fejlesztő csatlakozni tudjon az adatbázishoz, a következőkre lesz szüksége:

* Külső ügyfelek általi elérés engedélyezése az adatbázis tűzfalában. Ez az adatbázistűzfalak konfigurálásáról szóló cikk ismerteti, hogy hogyan engedélyezheti a hozzáférést.
* Az adatbázis-kiszolgáló neve és az adatbázisnév.

## <a name="connect-to-the-blockchain-workbench-database"></a>Csatlakozás a Blockchain Workbench adatbázisához

Az adatbázishoz való csatlakozáshoz:

1. Jelentkezzen be az Azure Portalra egy olyan fiókkal, amely **tulajdonosi** engedélyekkel rendelkezik az Azure Blockchain Workbench-erőforrásokhoz.
2. A bal oldali navigációs panelen válassza az **Erőforráscsoportok** lehetőséget.
3. Válassza a Blockchain Workbench-környezete erőforráscsoportjának nevét.
4. Válassza a **Típus** lehetőséget az erőforráslista rendezéséhez, majd válassza az **SQL server** lehetőséget. Az alábbi képernyőképen szemléltetett rendezett listában két SQL-adatbázis látható, egy „master” nevű, illetve egy másik, mely az „lhgn” nevet használja **erőforrás-előtagként**.

   ![A Blockchain Workbench-erőforrások rendezett listája](./media/getdb-details/sorted-workbench-resource-list.png)

5. A Blockchain Workbench-adatbázis adatainak megtekintéséhez válassza annak az adatbázisnak a hivatkozását, amelyhez a Blockchain Workbench telepítéséhez megadott **Erőforrás-előtag** tartozik.

   ![Adatbázis adatai](./media/getdb-details/workbench-db-details.png)

Az adatbázis-kiszolgáló neve és az adatbázisnév használatával csatlakozhat a Blockchain Workbench-adatbázishoz a saját fejlesztési vagy jelentéskészítő eszközével.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Adatbázisnézetek az Azure Blockchain Workbenchben](database-views.md)