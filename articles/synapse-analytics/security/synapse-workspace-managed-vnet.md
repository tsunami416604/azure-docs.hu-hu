---
title: Felügyelt virtuális hálózat
description: Az Azure szinapszis Analytics felügyelt virtuális hálózatát ismertető cikk
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 06b535b25df19e5062d16184f4469d9e9253b9c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87042618"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Azure szinapszis Analytics által felügyelt Virtual Network (előzetes verzió)

Ez a cikk ismerteti a felügyelt Virtual Network az Azure szinapszis Analyticsben.

## <a name="managed-workspace-virtual-network"></a>Felügyelt munkaterület Virtual Network

Az Azure szinapszis-munkaterület létrehozásakor kiválaszthatja, hogy hozzárendelje-e egy Microsoft Azure Virtual Networkhoz. A munkaterülethez társított Virtual Network az Azure szinapszis kezeli. Ezt a Virtual Network *felügyelt munkaterület Virtual Network*nevezzük.

A felügyelt munkaterület Virtual Network négyféle módon biztosít értéket:

- Felügyelt munkaterülettel Virtual Network kiszervezheti a Virtual Network az Azure Szinapszisba való kezelésével járó terheket.
- Nem kell konfigurálnia a bejövő NSG-szabályokat a saját virtuális hálózatán, hogy az Azure szinapszis-felügyeleti forgalom belépjen a Virtual Networkba. Ezeknek a NSG-szabályoknak a helytelen konfigurációja miatt a szolgáltatás megszakad az ügyfelek számára.
- Nem kell létrehoznia alhálózatot a Spark-fürtökhöz a csúcsérték terhelése alapján.
- A felügyelt munkaterület Virtual Network és a felügyelt privát végpontok is megvédik az kiszűrése. Felügyelt privát végpontokat csak olyan munkaterületen hozhat létre, amelyhez felügyelt munkaterület Virtual Network társítva van.

Ha olyan munkaterületet hoz létre, amely felügyelt munkaterülettel Virtual Network társítva, akkor biztosítja, hogy a munkaterület más munkaterületekről izolált hálózat legyen. Az Azure szinapszis különböző analitikai képességeket biztosít egy munkaterületen: Adatintegráció, Apache Spark, SQL-készlet és igény szerinti SQL-szolgáltatás.

Ha a munkaterület felügyelt munkaterülettel rendelkezik Virtual Network, a rendszer az adatintegrációt és a Spark-erőforrásokat is telepíti. A felügyelt munkaterület Virtual Network a Spark-tevékenységek felhasználói szintű elkülönítését is lehetővé teszi, mivel mindegyik Spark-fürt a saját alhálózatán található.

Az SQL-készlet és az igény szerinti SQL-példány több-bérlős képesség, ezért a felügyelt munkaterület Virtual Networkján kívül található. Az SQL-készleten belüli és az igény szerinti SQL-alapú kommunikáció az Azure-beli privát hivatkozások használatával. A rendszer automatikusan létrehozza ezeket a magánhálózati hivatkozásokat, amikor létrehoz egy olyan munkaterületet, amelyhez felügyelt munkaterület Virtual Network társítva van.

>[!IMPORTANT]
>Ez a munkaterület-konfiguráció a munkaterület létrehozása után nem módosítható. Nem konfigurálhat például olyan munkaterületet, amelyhez nem tartozik felügyelt munkaterület Virtual Network társítva, és hozzá kell rendelnie egy Virtual Network. Hasonlóképpen, nem lehet újrakonfigurálni egy munkaterületet egy felügyelt munkaterülettel Virtual Network társítva, és nem rendeli hozzá a Virtual Network.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-virtual-network"></a>Azure szinapszis-munkaterület létrehozása felügyelt munkaterülettel Virtual Network

Ha még nem tette meg, regisztrálja a hálózati erőforrás-szolgáltatót. Az erőforrás-szolgáltató regisztrálása konfigurálja az előfizetést az erőforrás-szolgáltatóval való együttműködésre. A [regisztráláskor](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)válassza a *Microsoft. Network* elemet az erőforrás-szolgáltatók listájából.

Ha olyan Azure szinapszis-munkaterületet szeretne létrehozni, amelynek felügyelt munkaterülete Virtual Network társítva van, válassza a **Biztonság + hálózatkezelés** fület Azure Portal, és jelölje be a **felügyelt virtuális hálózat engedélyezése** jelölőnégyzetet.

Ha a jelölőnégyzet nincs bejelölve, a munkaterülethez nem tartozik Virtual Network társítva.

>[!IMPORTANT]
>Csak olyan munkaterületen használhat privát hivatkozásokat, amely felügyelt munkaterülettel rendelkezik Virtual Network.

![Felügyelt munkaterület engedélyezése Virtual Network](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>A felügyelt munkaterületről érkező összes kimenő forgalom Virtual Network a felügyelt privát végpontok kivételével a jövőben le lesz tiltva. Javasoljuk, hogy felügyelt magánhálózati végpontokat hozzon létre, amelyek a munkaterületen kívüli összes Azure-adatforráshoz csatlakoznak. 

Megtekintheti, hogy az Azure szinapszis-munkaterülete felügyelt munkaterülethez van-e társítva Virtual Network a Azure Portal **Áttekintés** lehetőség kiválasztásával.

![A munkaterület áttekintése Azure Portal](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>További lépések

[Azure szinapszis-munkaterület](../quickstart-create-workspace.md) létrehozása

További információ a [felügyelt privát végpontokról](./synapse-workspace-managed-private-endpoints.md)

[Felügyelt privát végpontok létrehozása az adatforrásokhoz](./how-to-create-managed-private-endpoints.md)
