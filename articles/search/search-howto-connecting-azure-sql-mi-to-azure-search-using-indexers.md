---
title: Azure SQL felügyelt példányok közötti kapcsolatok a keresés indexeléséhez – Azure Search
description: Engedélyezze a nyilvános végpontot, hogy engedélyezze az SQL felügyelt példányokhoz való kapcsolódást egy Azure Search indexelő eszközéről.
author: vl8163264128
manager: briansmi
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: victliu
ms.openlocfilehash: ae947e85f600b3bc380898ad5820239aa823794d
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229127"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-managed-instance"></a>Azure Search indexelő és az SQL felügyelt példány közötti kapcsolatok konfigurálása
Ahogy azt a [Azure SQL Database összekapcsolása a Azure Search indexelő használatával](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)című részében leírtak szerint, a Azure Search a nyilvános végponton keresztül támogatja az indexek létrehozását az **SQL felügyelt példányain** .

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Felügyelt Azure SQL-példány létrehozása nyilvános végponttal
Hozzon létre egy felügyelt SQL-példányt a **nyilvános végpont engedélyezése** lehetőség kiválasztásával.

   ![Nyilvános végpont engedélyezése](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Nyilvános végpont engedélyezése")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Az Azure SQL felügyelt példány nyilvános végpontjának engedélyezése
Egy meglévő SQL felügyelt példányon is engedélyezheti a nyilvános végpontot a **biztonsági** > **virtuális hálózat** > **nyilvános végpontjának** > **engedélyezése**lehetőség alatt.

   ![Nyilvános végpont engedélyezése](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Nyilvános végpont engedélyezése")

## <a name="verify-nsg-rules"></a>NSG-szabályok ellenőrzése
Győződjön meg arról, hogy a hálózati biztonsági csoport rendelkezik a megfelelő **bejövő biztonsági szabályokkal** , amelyek engedélyezik az Azure-szolgáltatásokból érkező kapcsolatokat.

   ![NSG – bejövő biztonsági szabály](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG – bejövő biztonsági szabály")

## <a name="get-public-endpoint-connection-string"></a>Nyilvános végponti kapcsolatok karakterláncának beolvasása
Győződjön meg arról, hogy a **nyilvános végpont** (3342-as port, nem a 1433-es port) csatlakozási karakterláncát használja.

   ![Nyilvános végponti kapcsolatok karakterlánca](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Nyilvános végponti kapcsolatok karakterlánca")

## <a name="next-steps"></a>További lépések
A konfigurációnak megfelelően mostantól megadhat egy SQL felügyelt példányt egy Azure Search indexelő adatforrásként a portál vagy a REST API használatával. További információkért lásd: [Azure SQL Database csatlakoztatása Azure Search az indexelő használatával](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .
