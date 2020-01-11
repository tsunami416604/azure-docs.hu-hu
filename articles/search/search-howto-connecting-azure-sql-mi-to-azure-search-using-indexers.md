---
title: Azure SQL felügyelt példányok közötti kapcsolatok a keresés indexeléséhez
titleSuffix: Azure Cognitive Search
description: Engedélyezze a nyilvános végpontot, hogy engedélyezze az SQL felügyelt példányokhoz való kapcsolódást az Azure Cognitive Search indexelő eszközéről.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0f91775e0175b4b4af9b57fa96e389c3a2a22564
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863121"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Azure Cognitive Search indexelő és SQL felügyelt példány közötti kapcsolatok konfigurálása

Ahogy azt a [Azure SQL Database az Azure-Cognitive Search az indexelő használatával való csatlakoztatásával](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)kapcsolatban feltettük, az azure-Cognitive Search a nyilvános végponton keresztül támogatja az indexelő SQL-alapú **felügyelt példányok** létrehozását.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Felügyelt Azure SQL-példány létrehozása nyilvános végponttal
Hozzon létre egy felügyelt SQL-példányt a **nyilvános végpont engedélyezése** lehetőség kiválasztásával.

   ![Nyilvános végpont engedélyezése](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Nyilvános végpont engedélyezése")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Az Azure SQL felügyelt példány nyilvános végpontjának engedélyezése
A nyilvános végpontot egy meglévő SQL felügyelt példányon is engedélyezheti a **biztonsági** > **virtuális hálózatban** > **nyilvános végpont** > **engedélyezése**lehetőségre.

   ![Nyilvános végpont engedélyezése](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Nyilvános végpont engedélyezése")

## <a name="verify-nsg-rules"></a>NSG-szabályok ellenőrzése
Győződjön meg arról, hogy a hálózati biztonsági csoport rendelkezik a megfelelő **bejövő biztonsági szabályokkal** , amelyek engedélyezik az Azure-szolgáltatásokból érkező kapcsolatokat.

   ![NSG – bejövő biztonsági szabály](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG – bejövő biztonsági szabály")

> [!NOTE]
> A felügyelt SQL-példányhoz való bejövő hozzáférésnél szigorúbb korlátozást is megadhat, ha az aktuális szabályt (`public_endpoint_inbound`) 2 szabállyal helyettesíti:
>
> * Bejövő hozzáférés engedélyezése a `AzureCognitiveSearch` [szolgáltatási címkéből](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) ("forrás" = `AzureCognitiveSearch`)
>
> * A keresési szolgáltatás IP-címéről érkező bejövő hozzáférés engedélyezése, amelyet a teljes tartománynév (például `<your-search-service-name>.search.windows.net`) pingelésével lehet beszerezni. ("Forrás" = `IP address`)

## <a name="get-public-endpoint-connection-string"></a>Nyilvános végponti kapcsolatok karakterláncának beolvasása
Győződjön meg arról, hogy a **nyilvános végpont** (3342-as port, nem a 1433-es port) csatlakozási karakterláncát használja.

   ![Nyilvános végponti kapcsolatok karakterlánca](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Nyilvános végponti kapcsolatok karakterlánca")

## <a name="next-steps"></a>Következő lépések
A konfigurációnak megfelelően mostantól megadhat egy SQL felügyelt példányt egy Azure Cognitive Search indexelő adatforrásként a portál vagy a REST API használatával. További információkért lásd: [Azure SQL Database csatlakoztatása az Azure Cognitive Searchhoz az indexelő használatával](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .
