---
title: Kezelt magánvégpontok
description: Az Azure Synapse Analytics felügyelt magánvégpontjait magyarázó cikk
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 08c6610541d987cddd7cf2aeb71c526cb2359598
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423683"
---
# <a name="synapse-managed-private-endpoints-preview"></a>Szinapszis által kezelt magánvégpontok (előzetes verzió)

Ez a cikk ismerteti a felügyelt privát végpontok az Azure Synapse Analytics.

## <a name="managed-private-endpoints"></a>Kezelt magánvégpontok

Felügyelt magán-végpontok a felügyelt munkaterület virtuális hálózat létre az Azure-erőforrásokhoz létrehozott privát végpontok. Az Azure Synapse kezeli ezeket a privát végpontokat az Ön nevében.

Az Azure Synapse támogatja a privát hivatkozásokat. A privát hivatkozás lehetővé teszi az Azure-szolgáltatások (például az Azure Storage, az Azure Cosmos DB és az Azure SQL Data Warehouse) és az Azure által üzemeltetett ügyfél-/partnerszolgáltatások elérését az Azure virtuális hálózatából.

Ha privát kapcsolatot használ, a virtuális hálózat és a munkaterület közötti forgalom teljes mértékben áthalad a Microsoft gerinchálózaton keresztül. A Private Link védelmet nyújt az adatok kiszivárgási kockázatai ellen. Az erőforráshoz való privát hivatkozást egy privát végpont létrehozásával hozlétre.

A privát végpont egy privát IP-címet használ a virtuális hálózatból, hogy hatékonyan hozza a szolgáltatást a virtuális hálózatba. A privát végpontok az Azure-ban egy adott erőforráshoz vannak leképezve, és nem a teljes szolgáltatásra. Az ügyfelek korlátozhatják a kapcsolatot a szervezet által jóváhagyott adott erőforrásra. További információ a [privát hivatkozásokról és a privát végpontokról.](https://docs.microsoft.com/azure/private-link/)

>[!IMPORTANT]
>Felügyelt privát végpontok csak az Azure Synapse munkaterületek felügyelt munkaterület virtuális hálózat tal támogatott.
>[!NOTE]
>Javasoljuk, hogy hozzon létre felügyelt magánvégpontok az összes Azure-adatforráshoz való csatlakozáshoz. A >felügyelt munkaterület virtuális hálózatról érkező összes kimenő forgalom a jövőben le lesz tiltva.

A privát végpont-kapcsolat jön létre egy "függőben" állapotban, amikor létrehoz egy felügyelt privát végpont ot az Azure Synapse. A jóváhagyási munkafolyamat elindul. A magánkapcsolati erőforrás tulajdonosa felelős a kapcsolat jóváhagyásáért vagy elutasításáért.

Ha a tulajdonos jóváhagyja a kapcsolatot, a privát kapcsolat létrejön. Ellenkező esetben a privát kapcsolat nem jön létre. Mindkét esetben a felügyelt magánvégpont frissül a kapcsolat állapotával.

Csak egy jóváhagyott állapotú felügyelt magánvégpont küldhet forgalmat egy adott magánkapcsolati erőforrásnak.

## <a name="managed-private-endpoints-for-sql-pool-and-sql-on-demand"></a>Felügyelt személyes végpontok SQL-készlethez és SQL igény szerinti

Az SQL-készlet és az igény szerinti SQL-funkciók az Azure Synapse-munkaterületen analitikus képességek. Ezek a képességek olyan több-bérlős infrastruktúrát használnak, amely nincs telepítve a [felügyelt munkaterület virtuális hálózatába.](./synapse-workspace-managed-vnet.md)

Munkaterület létrehozásakor az Azure Synapse két felügyelt magánvégpontot hoz létre az SQL-készlethez és az SQL igény szerinti igény szerinti hoz létre a munkaterületen. 

Ez a két felügyelt privát végpont az Azure Synapse Studio-ban található. Válassza a **Kezelés lehetőséget** a bal oldali navigációs sávon, majd a Felügyelt **virtuális hálózatok** lehetőséget a Studio-ban.

Az SQL-készletet megcélozó felügyelt privát végpont neve *szinapszis-ws-sql -\<munkaterületnév,\> * és az, amelyik az SQL-t az igény szerint célozza, *szinapszis-ws-sqlOnDemand--\<munkaterület-név.\>*
![Felügyelt személyes végpontok SQL-készlethez és SQL igény szerinti](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Ez a két felügyelt privát végpont automatikusan létrejön az Azure Synapse munkaterület létrehozásakor. A két felügyelt privát végpontért nem kell fizetnie.

## <a name="next-steps"></a>További lépések

[Felügyelt személyes végpontok létrehozása az adatforrásokhoz](./how-to-create-managed-private-endpoints.md)
