---
title: Felügyelt virtuális hálózat az Azure szinapszis Analyticsben
description: Az Azure szinapszis Analytics felügyelt virtuális hálózatát ismertető cikk
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 162d96244b01f8c5e1acf224475aadb9508f0aa5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81423634"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Azure szinapszis Analytics által felügyelt Virtual Network (előzetes verzió)

Ez a cikk ismerteti a felügyelt Virtual Network az Azure szinapszis Analyticsben.

## <a name="managed-workspace-vnet"></a>Felügyelt munkaterület VNet

Az Azure szinapszis-munkaterületének létrehozásakor kiválaszthatja, hogy egy VNet rendelje hozzá. A munkaterülethez társított VNet az Azure szinapszis kezeli. Ezt a VNet *felügyelt munkaterület-VNet*nevezzük.

A felügyelt munkaterület VNet négyféle módon biztosít értéket:

- A felügyelt munkaterület VNet kiszervezheti a VNet az Azure Szinapszisba való kezelésével járó terheket.
- Nem kell konfigurálnia a bejövő NSG-szabályokat a saját virtuális hálózatok, hogy az Azure szinapszis-felügyeleti forgalom belépjen a VNet. Ezeknek a NSG-szabályoknak a helytelen konfigurációja miatt a szolgáltatás megszakad az ügyfelek számára.
- Nem kell létrehoznia alhálózatot a Spark-fürtökhöz a csúcsérték terhelése alapján.
- A felügyelt munkaterületek VNet és a felügyelt privát végpontok megvédik az adatkiszűrése. Felügyelt privát végpontokat csak olyan munkaterületen hozhat létre, amelyhez hozzá van rendelve egy felügyelt munkaterület-VNet.

Ha egy olyan munkaterületet hoz létre, amely hozzá van rendelve egy felügyelt munkaterület-VNet, biztosítja, hogy a munkaterület más munkaterületekről izolált hálózat legyen. Az Azure szinapszis különböző analitikai képességeket biztosít egy munkaterületen: Adatintegráció, Apache Spark, SQL-készlet és igény szerinti SQL-szolgáltatás.

Ha a munkaterület felügyelt munkaterület-VNet rendelkezik, a rendszer az adatintegrációt és a Spark-erőforrásokat is telepíti. A felügyelt munkaterület VNet felhasználói szintű elkülönítést is biztosít a Spark-tevékenységekhez, mivel mindegyik Spark-fürt a saját alhálózatában található.

Az SQL-készlet és az igény szerinti SQL-példány több-bérlős képesség, ezért a felügyelt munkaterület VNet kívül található. Az SQL-készleten belüli és az igény szerinti SQL-alapú kommunikáció az Azure-beli privát hivatkozások használatával. A rendszer automatikusan létrehozza ezeket a magánhálózati hivatkozásokat, amikor létrehoz egy olyan munkaterületet, amely hozzá van rendelve egy felügyelt munkaterület-VNet.

>[!IMPORTANT]
>Ez a munkaterület-konfiguráció a munkaterület létrehozása után nem módosítható. Nem konfigurálhat például olyan munkaterületet, amelyhez nincs hozzárendelve felügyelt munkaterület VNet, és társíthat hozzá VNet. Hasonlóképpen, nem lehet újrakonfigurálni egy olyan munkaterületet, amely hozzá van rendelve egy felügyelt munkaterület-VNet, és nem társítja a VNet.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-vnet"></a>Azure-beli szinapszis-munkaterület létrehozása felügyelt munkaterület-VNet

Ha olyan Azure-beli szinapszis-munkaterületet szeretne létrehozni, amelynek van társítva felügyelt munkaterület-VNet, válassza a **Biztonság + hálózatkezelés** fület Azure Portal, és jelölje be a **felügyelt virtuális hálózat engedélyezése** jelölőnégyzetet.

Ha a jelölőnégyzet nincs bejelölve, a munkaterülethez nem tartozik VNet társítva.

>[!IMPORTANT]
>Csak olyan munkaterületen használhat privát hivatkozásokat, amely felügyelt munkaterület-VNet rendelkezik.

![Felügyelt munkaterület VNet engedélyezése](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>A későbbiekben a felügyelt munkaterület VNet érkező összes kimenő forgalom le lesz tiltva. Javasoljuk, hogy minden adatforráshoz a felügyelt magánhálózati végpontok használatával kapcsolódjon.

Megtekintheti, hogy az Azure szinapszis-munkaterület felügyelt munkaterület VNet van-e társítva, ha a Azure Portal **Áttekintés** elemét választja.

![A munkaterület áttekintése Azure Portal](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>További lépések

[Azure szinapszis-munkaterület](../quickstart-create-workspace.md) létrehozása

További információ a [felügyelt privát végpontokról](./synapse-workspace-managed-private-endpoints.md)

[Felügyelt privát végpontok létrehozása az adatforrásokhoz](./how-to-create-managed-private-endpoints.md)
