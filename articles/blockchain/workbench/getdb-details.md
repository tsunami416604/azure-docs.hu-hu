---
title: Az Azure Blockchain Workbench-adatbázis adatainak lekérése
description: Megtudhatja, hogy hogyan kérheti le az Azure Blockchain Workbench adatbázisának és adatbáziskiszolgálójának adatait.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/09/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 42d119acd8880458eadc1760a7cb9713f91e3f6f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65509981"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Az Azure Blockchain Workbench-adatbázis adatainak lekérése

Ebből a témakörből megtudhatja, hogy hogyan kérheti le az Azure Blockchain Workbench adatbázisának részletes adatait.

## <a name="overview"></a>Áttekintés

Az alkalmazások, a munkafolyamatok és az intelligens szerződések végrehajtásának adatait a Blockchain Workbench SQL-adatbázisa adatbázisnézetek formájában tárolja. Amikor az eszközök, például a Microsoft Excel, a Power BI, a Visual Studio és az SQL Server Management Studio használatával a fejlesztők a ezt az információt.

Ahhoz, hogy egy fejlesztő csatlakozni tudjon az adatbázishoz, a következőkre lesz szüksége:

* Külső ügyfelek általi elérés engedélyezése az adatbázis tűzfalában. Ez az adatbázistűzfalak konfigurálásáról szóló cikk ismerteti, hogy hogyan engedélyezheti a hozzáférést.
* Az adatbázis-kiszolgáló neve és az adatbázisnév.

## <a name="connect-to-the-blockchain-workbench-database"></a>Csatlakozás a Blockchain Workbench adatbázisához

Az adatbázishoz való csatlakozáshoz:

1. Jelentkezzen be egy olyan fiókkal, amely rendelkezik az Azure Portalon **tulajdonos** az Azure Blockchain Workbench erőforrások engedélyeit.
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