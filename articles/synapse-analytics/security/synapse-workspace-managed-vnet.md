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
ms.openlocfilehash: b605c2c9f3c7468d0fe1f2cccd8c3602c556714b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501073"
---
# <a name="azure-synapse-analytics-managed-virtual-network"></a>Azure szinapszis Analytics által felügyelt Virtual Network

Ez a cikk ismerteti a felügyelt Virtual Network az Azure szinapszis Analyticsben.

## <a name="managed-workspace-virtual-network"></a>Felügyelt munkaterület Virtual Network

Az Azure szinapszis-munkaterület létrehozásakor kiválaszthatja, hogy hozzárendelje-e egy Microsoft Azure Virtual Networkhoz. A munkaterülethez társított Virtual Network az Azure szinapszis kezeli. Ezt a Virtual Network *felügyelt munkaterület Virtual Network* nevezzük.

A felügyelt munkaterület Virtual Network négyféle módon biztosít értéket:

- Felügyelt munkaterülettel Virtual Network kiszervezheti a Virtual Network az Azure Szinapszisba való kezelésével járó terheket.
- Nem kell konfigurálnia a bejövő NSG-szabályokat a saját virtuális hálózatán, hogy az Azure szinapszis-felügyeleti forgalom belépjen a Virtual Networkba. Ezeknek a NSG-szabályoknak a helytelen konfigurációja miatt a szolgáltatás megszakad az ügyfelek számára.
- Nem kell létrehoznia alhálózatot a Spark-fürtökhöz a csúcsérték terhelése alapján.
- A felügyelt munkaterület Virtual Network és a felügyelt privát végpontok is megvédik az kiszűrése. Felügyelt privát végpontokat csak olyan munkaterületen hozhat létre, amelyhez felügyelt munkaterület Virtual Network társítva van.

Ha olyan munkaterületet hoz létre, amely felügyelt munkaterülettel Virtual Network társítva, akkor biztosítja, hogy a munkaterület más munkaterületekről izolált hálózat legyen. Az Azure szinapszis különböző analitikai képességeket biztosít egy munkaterületen: Adatintegráció, kiszolgáló nélküli Apache Spark készlet, dedikált SQL-készlet és kiszolgáló nélküli SQL-készlet.

Ha a munkaterület felügyelt munkaterülettel rendelkezik Virtual Network, a rendszer az adatintegrációt és a Spark-erőforrásokat is telepíti. A felügyelt munkaterület Virtual Network a Spark-tevékenységek felhasználói szintű elkülönítését is lehetővé teszi, mivel mindegyik Spark-fürt a saját alhálózatán található.

A dedikált SQL-készlet és a kiszolgáló nélküli SQL-készlet több-bérlős képesség, ezért a felügyelt munkaterület Virtual Networkján kívül található. A dedikált SQL-készletbe és a kiszolgáló nélküli SQL-készletbe való munkaterület-kommunikáció az Azure privát hivatkozásait használja. A rendszer automatikusan létrehozza ezeket a magánhálózati hivatkozásokat, amikor létrehoz egy olyan munkaterületet, amelyhez felügyelt munkaterület Virtual Network társítva van.

>[!IMPORTANT]
>Ez a munkaterület-konfiguráció a munkaterület létrehozása után nem módosítható. Nem konfigurálhat például olyan munkaterületet, amelyhez nem tartozik felügyelt munkaterület Virtual Network társítva, és hozzá kell rendelnie egy Virtual Network. Hasonlóképpen, nem lehet újrakonfigurálni egy munkaterületet egy felügyelt munkaterülettel Virtual Network társítva, és nem rendeli hozzá a Virtual Network.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-virtual-network"></a>Azure szinapszis-munkaterület létrehozása felügyelt munkaterülettel Virtual Network

Ha még nem tette meg, regisztrálja a hálózati erőforrás-szolgáltatót. Az erőforrás-szolgáltató regisztrálása konfigurálja az előfizetést az erőforrás-szolgáltatóval való együttműködésre. A [regisztráláskor](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)válassza a *Microsoft. Network* elemet az erőforrás-szolgáltatók listájából.

Olyan Azure-beli szinapszis-munkaterület létrehozásához, amelyhez felügyelt munkaterület Virtual Network társítva van, válassza a **hálózatkezelés** fület a Azure Portal, és jelölje be a **felügyelt virtuális hálózat engedélyezése** jelölőnégyzetet.

Ha a jelölőnégyzet nincs bejelölve, a munkaterülethez nem tartozik Virtual Network társítva.

>[!IMPORTANT]
>Csak olyan munkaterületen használhat privát hivatkozásokat, amely felügyelt munkaterülettel rendelkezik Virtual Network.

![Felügyelt munkaterület engedélyezése Virtual Network](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)


Megtekintheti, hogy az Azure szinapszis-munkaterülete felügyelt munkaterülethez van-e társítva Virtual Network a Azure Portal **Áttekintés** lehetőség kiválasztásával.

![A munkaterület áttekintése Azure Portal](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>További lépések

[Azure szinapszis-munkaterület](../quickstart-create-workspace.md) létrehozása

További információ a [felügyelt privát végpontokról](./synapse-workspace-managed-private-endpoints.md)

[Felügyelt privát végpontok létrehozása az adatforrásokhoz](./how-to-create-managed-private-endpoints.md)
