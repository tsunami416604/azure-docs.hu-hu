---
title: Az Azure Blockchain Workbench előzetes verziójú adatbázisának beszerzése – részletek
description: Ismerje meg, hogyan kérheti le az Azure Blockchain Workbench előzetes verziójú adatbázisát és az adatbázis-kiszolgáló adatait.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f4a4eaab9a03aeed27e29eb645b6e22a028b243b
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845081"
---
# <a name="get-information-about-your-azure-blockchain-workbench-preview-database"></a>Az Azure Blockchain Workbench előzetes verziójával kapcsolatos információk lekérése

Ez a cikk bemutatja, hogyan kérhet részletes információkat az Azure Blockchain Workbench előzetes verziójú adatbázisáról.

## <a name="overview"></a>Áttekintés

Az alkalmazások, a munkafolyamatok és az intelligens szerződések végrehajtásának adatait a Blockchain Workbench SQL-adatbázisa adatbázisnézetek formájában tárolja. A fejlesztők ezt az információt használhatják olyan eszközök, mint például a Microsoft Excel, a Power BI, a Visual Studio és a SQL Server Management Studio használatakor.

Ahhoz, hogy egy fejlesztő csatlakozni tudjon az adatbázishoz, a következőkre lesz szüksége:

* Külső ügyfelek általi elérés engedélyezése az adatbázis tűzfalában. Ez az adatbázistűzfalak konfigurálásáról szóló cikk ismerteti, hogy hogyan engedélyezheti a hozzáférést.
* Az adatbázis-kiszolgáló neve és az adatbázisnév.

## <a name="connect-to-the-blockchain-workbench-database"></a>Csatlakozás a Blockchain Workbench adatbázisához

Az adatbázishoz való csatlakozáshoz:

1. Jelentkezzen be a Azure Portalba egy olyan fiókkal , amely rendelkezik tulajdonosi engedélyekkel az Azure Blockchain Workbench erőforrásaihoz.
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