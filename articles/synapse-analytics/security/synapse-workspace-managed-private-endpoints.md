---
title: Felügyelt privát végpontok
description: Az Azure szinapszis Analytics szolgáltatásban felügyelt privát végpontokat ismertető cikk
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 08c6610541d987cddd7cf2aeb71c526cb2359598
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81423683"
---
# <a name="synapse-managed-private-endpoints-preview"></a>Szinapszis felügyelt privát végpontok (előzetes verzió)

Ez a cikk ismerteti a felügyelt privát végpontokat az Azure szinapszis Analyticsben.

## <a name="managed-private-endpoints"></a>Felügyelt privát végpontok

A felügyelt privát végpontok a felügyelt munkaterület VNet létrehozott privát végpontok, amelyek az Azure-erőforrásokra mutató privát hivatkozást hoznak létre. Az Azure szinapszis ezeket a privát végpontokat az Ön nevében kezeli.

Az Azure szinapszis támogatja a privát hivatkozásokat. A privát hivatkozás lehetővé teszi az Azure-szolgáltatások (például az Azure Storage, a Azure Cosmos DB és a Azure SQL Data Warehouse) és az Azure-beli üzemeltetett ügyfelek/partnerek szolgáltatásainak biztonságos elérését az Azure-VNet.

Ha privát hivatkozást használ, a VNet és a munkaterület közötti forgalom teljes egészében a Microsoft gerinc hálózatán halad át. A privát hivatkozás védi az adatkiszűrése kockázatait. Privát végpont létrehozásával létrehozhat egy erőforrásra mutató privát hivatkozást.

A privát végpont egy magánhálózati IP-címet használ a VNet, hogy hatékonyan hozza a szolgáltatást a VNet. A magánhálózati végpontok egy adott erőforrásra vannak leképezve az Azure-ban, és nem a teljes szolgáltatás. Az ügyfelek korlátozhatják a szervezete által jóváhagyott adott erőforráshoz való kapcsolódást. További információ a [privát kapcsolatokról és a privát végpontokról](https://docs.microsoft.com/azure/private-link/).

>[!IMPORTANT]
>A felügyelt magánhálózati végpontok csak a felügyelt munkaterület VNet rendelkező Azure szinapszis-munkaterületeken támogatottak.
>[!NOTE]
>Javasoljuk, hogy felügyelt privát végpontokat hozzon létre az összes Azure-adatforráshoz való kapcsolódáshoz. A >felügyelt munkaterület VNet érkező összes kimenő forgalom a jövőben le lesz tiltva.

A privát végponti kapcsolatok "függő" állapotban jönnek létre, amikor felügyelt privát végpontot hoz létre az Azure Szinapszisban. A rendszer elindít egy jóváhagyási munkafolyamatot. A magánhálózati kapcsolat erőforrásának tulajdonosa a kapcsolat jóváhagyása vagy elutasítása.

Ha a tulajdonos jóváhagyja a kapcsolatot, a magánhálózati kapcsolat létrejött. Ellenkező esetben a magánhálózati kapcsolat nem lesz létrehozva. Mindkét esetben a felügyelt magánhálózati végpont a kapcsolatok állapotával lesz frissítve.

Csak egy felügyelt magánhálózati végpont engedélyezett állapotban küldhet forgalmat egy adott privát kapcsolati erőforrásnak.

## <a name="managed-private-endpoints-for-sql-pool-and-sql-on-demand"></a>Felügyelt magánhálózati végpontok az SQL-készlethez és az igény szerinti SQL-hez

Az SQL-készlet és az SQL on-demand analitikus funkciók az Azure szinapszis-munkaterületen. Ezek a képességek olyan több-bérlős infrastruktúrát használnak, amely nincs telepítve a [felügyelt munkaterület VNet](./synapse-workspace-managed-vnet.md).

Munkaterületek létrehozásakor az Azure szinapszis két felügyelt magánhálózati végpontot hoz létre az SQL-készletbe, és igény szerint az SQL-t a munkaterületen. 

Ez a két felügyelt magánhálózati végpont az Azure szinapszis Studióban szerepel. Válassza a **kezelés** lehetőséget a bal oldali navigációs sávon, majd válassza a **felügyelt virtuális hálózatok** lehetőséget a Studióban való megjelenítéshez.

A felügyelt magánhálózati végpont, amely az SQL-készletet célozza, *szinapszis-\<ws\> -SQL-workspacename* , és az SQL on-demand célpontjának neve *szinapszis-ws-\<sqlOnDemand\>--workspacename*.
![Felügyelt magánhálózati végpontok az SQL-készlethez és az igény szerinti SQL-hez](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Ez a két felügyelt magánhálózati végpont automatikusan létrejön az Azure szinapszis-munkaterület létrehozásakor. Ez a két felügyelt magánhálózati végpont esetében nem számítunk fel díjat.

## <a name="next-steps"></a>További lépések

[Felügyelt privát végpontok létrehozása az adatforrásokhoz](./how-to-create-managed-private-endpoints.md)
