---
title: Felügyelt privát végpontok
description: Az Azure szinapszis Analytics szolgáltatásban felügyelt privát végpontokat ismertető cikk
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 10/16/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 7eff63b36eb09036b188ac756ec55a5b1bf63718
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116517"
---
# <a name="synapse-managed-private-endpoints"></a>Szinapszis felügyelt privát végpontok

Ez a cikk ismerteti a felügyelt privát végpontokat az Azure szinapszis Analyticsben.

## <a name="managed-private-endpoints"></a>Felügyelt privát végpontok

A felügyelt privát végpontok a felügyelt munkaterületen létrehozott privát végpontok, Microsoft Azure Virtual Network az Azure-erőforrásokra mutató privát hivatkozás létrehozása. Az Azure szinapszis ezeket a privát végpontokat az Ön nevében kezeli.

Az Azure szinapszis támogatja a privát hivatkozásokat. A privát hivatkozás lehetővé teszi az Azure-szolgáltatások (például az Azure Storage és a Azure Cosmos DB) és az Azure-beli üzemeltetett ügyfél/partner szolgáltatások elérését az Azure-Virtual Network biztonságosan.

Privát hivatkozás használata esetén a Virtual Network és a munkaterület közötti forgalom teljes mértékben áthalad a Microsoft gerinc hálózatán. A privát hivatkozás védi az adatkiszűrése kockázatait. Privát végpont létrehozásával létrehozhat egy erőforrásra mutató privát hivatkozást.

A privát végpont a Virtual Network egy magánhálózati IP-címet használ, hogy hatékonyan hozza a szolgáltatást a Virtual Networkba. A magánhálózati végpontok egy adott erőforrásra vannak leképezve az Azure-ban, és nem a teljes szolgáltatás. Az ügyfelek korlátozhatják a szervezete által jóváhagyott adott erőforráshoz való kapcsolódást. 

További információ a [privát kapcsolatokról és a privát végpontokról](../../private-link/index.yml).

>[!IMPORTANT]
>A felügyelt magánhálózati végpontok csak felügyelt munkaterülettel rendelkező Azure szinapszis-munkaterületeken támogatottak Virtual Network.

>[!NOTE]
>A felügyelt munkaterületről érkező összes kimenő forgalom Virtual Network a felügyelt privát végpontok kivételével a jövőben le lesz tiltva. Javasoljuk, hogy felügyelt magánhálózati végpontokat hozzon létre, amelyek a munkaterületen kívüli összes Azure-adatforráshoz csatlakoznak. 

A privát végponti kapcsolatok "függő" állapotban jönnek létre, amikor felügyelt privát végpontot hoz létre az Azure Szinapszisban. A rendszer elindít egy jóváhagyási munkafolyamatot. A magánhálózati kapcsolat erőforrásának tulajdonosa a kapcsolat jóváhagyása vagy elutasítása.

Ha a tulajdonos jóváhagyja a kapcsolatot, a magánhálózati kapcsolat létrejött. Ha azonban a tulajdonos nem hagyja jóvá a kapcsolatot, a magánhálózati kapcsolat nem lesz létrehozva. Mindkét esetben a felügyelt magánhálózati végpont a kapcsolatok állapotával lesz frissítve.

Csak egy felügyelt magánhálózati végpont engedélyezett állapotban küldhet forgalmat egy adott privát kapcsolati erőforrásnak.

## <a name="managed-private-endpoints-for-dedicated-sql-pool-and-serverless-sql-pool"></a>Felügyelt privát végpontok dedikált SQL-készlethez és kiszolgáló nélküli SQL-készlethez

A dedikált SQL-készlet és a kiszolgáló nélküli SQL-készlet az Azure szinapszis-munkaterületének analitikai képességei. Ezek a képességek olyan több-bérlős infrastruktúrát használnak, amely nincs telepítve a [felügyelt munkaterületen Virtual Network](./synapse-workspace-managed-vnet.md).

Munkaterületek létrehozásakor az Azure szinapszis két felügyelt magánhálózati végpontot hoz létre a munkaterületen, egyet a dedikált SQL-készlethez, egy pedig a kiszolgáló nélküli SQL-készlethez. 

Ez a két felügyelt magánhálózati végpont szerepel a szinapszis Studióban. Válassza a **kezelés** lehetőséget a bal oldali navigációs sávon, majd válassza a **felügyelt privát végpontok** lehetőséget a Studióban való megjelenítéshez.

A felügyelt magánhálózati végpont, amely az SQL-készletet célozza, *szinapszis- \<workspacename\> ws-SQL--* nek nevezi, és a kiszolgáló nélküli SQL-készletet tároló egyik neve *szinapszis-ws-sqlOnDemand-- \<workspacename\>*.

![Felügyelt privát végpontok dedikált SQL-készlethez és kiszolgáló nélküli SQL-készlethez](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Ez a két felügyelt magánhálózati végpont automatikusan létrejön az Azure szinapszis-munkaterület létrehozásakor. Ez a két felügyelt magánhálózati végpont esetében nem számítunk fel díjat.

## <a name="next-steps"></a>Következő lépések

További információt a [felügyelt privát végpontok létrehozása az adatforrásokhoz](./how-to-create-managed-private-endpoints.md) című cikkben talál.