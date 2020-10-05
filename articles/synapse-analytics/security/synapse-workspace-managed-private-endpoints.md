---
title: Felügyelt privát végpontok
description: Az Azure szinapszis Analytics szolgáltatásban felügyelt privát végpontokat ismertető cikk
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: e592159777f3b533bc447bb109e9b1308af7ecff
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91249502"
---
# <a name="synapse-managed-private-endpoints-preview"></a>Szinapszis felügyelt privát végpontok (előzetes verzió)

Ez a cikk ismerteti a felügyelt privát végpontokat az Azure szinapszis Analyticsben.

## <a name="managed-private-endpoints"></a>Felügyelt privát végpontok

A felügyelt privát végpontok a felügyelt munkaterületen létrehozott privát végpontok, Microsoft Azure Virtual Network az Azure-erőforrásokra mutató privát hivatkozás létrehozása. Az Azure szinapszis ezeket a privát végpontokat az Ön nevében kezeli.

Az Azure szinapszis támogatja a privát hivatkozásokat. A privát hivatkozás lehetővé teszi az Azure-szolgáltatások (például az Azure Storage és a Azure Cosmos DB) és az Azure-beli üzemeltetett ügyfél/partner szolgáltatások elérését az Azure-Virtual Network biztonságosan.

Privát hivatkozás használata esetén a Virtual Network és a munkaterület közötti forgalom teljes mértékben áthalad a Microsoft gerinc hálózatán. A privát hivatkozás védi az adatkiszűrése kockázatait. Privát végpont létrehozásával létrehozhat egy erőforrásra mutató privát hivatkozást.

A privát végpont a Virtual Network egy magánhálózati IP-címet használ, hogy hatékonyan hozza a szolgáltatást a Virtual Networkba. A magánhálózati végpontok egy adott erőforrásra vannak leképezve az Azure-ban, és nem a teljes szolgáltatás. Az ügyfelek korlátozhatják a szervezete által jóváhagyott adott erőforráshoz való kapcsolódást. 

További információ a [privát kapcsolatokról és a privát végpontokról](https://docs.microsoft.com/azure/private-link/).

>[!IMPORTANT]
>A felügyelt magánhálózati végpontok csak felügyelt munkaterülettel rendelkező Azure szinapszis-munkaterületeken támogatottak Virtual Network.

>[!NOTE]
>A felügyelt munkaterületről érkező összes kimenő forgalom Virtual Network a felügyelt privát végpontok kivételével a jövőben le lesz tiltva. Javasoljuk, hogy felügyelt magánhálózati végpontokat hozzon létre, amelyek a munkaterületen kívüli összes Azure-adatforráshoz csatlakoznak. 

A privát végponti kapcsolatok "függő" állapotban jönnek létre, amikor felügyelt privát végpontot hoz létre az Azure Szinapszisban. A rendszer elindít egy jóváhagyási munkafolyamatot. A magánhálózati kapcsolat erőforrásának tulajdonosa a kapcsolat jóváhagyása vagy elutasítása.

Ha a tulajdonos jóváhagyja a kapcsolatot, a magánhálózati kapcsolat létrejött. Ha azonban a tulajdonos nem hagyja jóvá a kapcsolatot, a magánhálózati kapcsolat nem lesz létrehozva. Mindkét esetben a felügyelt magánhálózati végpont a kapcsolatok állapotával lesz frissítve.

Csak egy felügyelt magánhálózati végpont engedélyezett állapotban küldhet forgalmat egy adott privát kapcsolati erőforrásnak.

## <a name="managed-private-endpoints-for-sql-pool-and-sql-on-demand"></a>Felügyelt magánhálózati végpontok az SQL-készlethez és az igény szerinti SQL-hez

Az SQL-készlet és az SQL on-demand analitikus funkciók az Azure szinapszis-munkaterületen. Ezek a képességek olyan több-bérlős infrastruktúrát használnak, amely nincs telepítve a [felügyelt munkaterületen Virtual Network](./synapse-workspace-managed-vnet.md).

Munkaterületek létrehozásakor az Azure szinapszis két felügyelt magánhálózati végpontot hoz létre az SQL-készletbe, és igény szerint az SQL-t a munkaterületen. 

Ez a két felügyelt magánhálózati végpont az Azure szinapszis Studióban szerepel. Válassza a **kezelés** lehetőséget a bal oldali navigációs sávon, majd válassza a **felügyelt virtuális hálózatok** lehetőséget a Studióban való megjelenítéshez.

A felügyelt magánhálózati végpont, amely az SQL-készletet célozza, *szinapszis- \<workspacename\> ws-SQL--* nek nevezi, és az SQL on-demand célpontjának neve *szinapszis-ws-sqlOnDemand-- \<workspacename\> *.
![Felügyelt magánhálózati végpontok az SQL-készlethez és az igény szerinti SQL-hez](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Ez a két felügyelt magánhálózati végpont automatikusan létrejön az Azure szinapszis-munkaterület létrehozásakor. Ez a két felügyelt magánhálózati végpont esetében nem számítunk fel díjat.

## <a name="next-steps"></a>További lépések

További információt a [felügyelt privát végpontok létrehozása az adatforrásokhoz](./how-to-create-managed-private-endpoints.md) című cikkben talál.
