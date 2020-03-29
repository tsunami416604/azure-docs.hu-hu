---
title: Azure SQL felügyelt példánykapcsolat a keresési indexeléshez
titleSuffix: Azure Cognitive Search
description: Engedélyezze a nyilvános végpontot, hogy az Azure Cognitive Search egy indexelője kapcsolatot engedélyezhet az SQL felügyelt példányokhoz.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 65e483fd772e20daa73b465ea17dfa6ecde42233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964889"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Kapcsolat konfigurálása egy Azure Cognitive Search indexelőtől az SQL felügyelt példányhoz

Amint azt az [Azure SQL-adatbázis csatlakoztatása az Azure Cognitive Search indexelők használatával,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)az **Indexelők** az SQL felügyelt példányok által támogatott azure cognitive Search a nyilvános végponton keresztül.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Azure SQL felügyelt példány létrehozása nyilvános végponttal
SQL felügyelt példány létrehozása a **Nyilvános végpont engedélyezése** beállítással.

   ![Nyilvános végpont engedélyezése](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Nyilvános végpont engedélyezése")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Az Azure SQL felügyelt példány nyilvános végpontjának engedélyezése
A nyilvános végpontot egy meglévő SQL felügyelt példányon is engedélyezheti a **Biztonsági** > **virtuális hálózat** > nyilvános végpontja Enable (Biztonsági virtuális hálózat**nyilvános végpontengedélyezése)** > **csoportban.**

   ![Nyilvános végpont engedélyezése](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Nyilvános végpont engedélyezése")

## <a name="verify-nsg-rules"></a>NSG-szabályok ellenőrzése
Ellenőrizze, hogy a hálózati biztonsági csoport rendelkezik a megfelelő **bejövő biztonsági szabályokkal,** amelyek lehetővé teszik az Azure-szolgáltatásokból származó kapcsolatokat.

   ![NSG bejövő biztonsági szabály](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG bejövő biztonsági szabály")

> [!NOTE]
> Az indexelők továbbra is megkövetelik, hogy az SQL felügyelt példányt nyilvános végponttal konfigurálják az adatok olvasásához.
> Azonban korlátozhatja az adott nyilvános végponthoz való bejövő hozzáférést, ha`public_endpoint_inbound`az aktuális szabályt ( ) a következő 2 szabállyal helyettesíti:
>
> * Bejövő hozzáférés engedélyezése a `AzureCognitiveSearch` [szolgáltatáscímkéből](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) ("SOURCE" = `AzureCognitiveSearch`, "NAME" = `cognitive_search_inbound`)
>
> * Lehetővé teszi a bejövő hozzáférést az IP-címét a keresési szolgáltatás, amely lehet beszerezni `<your-search-service-name>.search.windows.net`pingelés a teljesen minősített domain név (pl., ). ("FORRÁS" `IP address`= , "NÉV" = `search_service_inbound`)
>
> A 2 szabály mindegyikére állítsa be `3342`a "PORT" = , "PROTOCOL" = `TCP`, "DESTINATION" = `Any`" "ACTION" =`Allow`

## <a name="get-public-endpoint-connection-string"></a>Nyilvános végpontkapcsolati karakterlánc beszereznie
Győződjön meg arról, hogy a kapcsolati karakterláncot használja a **nyilvános végponthoz** (a 3342-es porthoz, nem az 1433-as porthoz).

   ![Nyilvános végpontkapcsolati karakterlánc](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Nyilvános végpontkapcsolati karakterlánc")

## <a name="next-steps"></a>További lépések
A konfiguráció az útból, most már megadhat egy SQL felügyelt példány adatforrásaként egy Azure Cognitive Search indexelő segítségével a portál vagy a REST API-t. További [információ: Az Azure SQL Database csatlakoztatása az Azure Cognitive Search indexelők használatával](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) című témakörben olvashat bővebben.
