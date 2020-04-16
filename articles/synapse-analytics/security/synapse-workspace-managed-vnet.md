---
title: Felügyelt virtuális hálózat az Azure Synapse Analytics szolgáltatásban
description: A felügyelt virtuális hálózatot az Azure Synapse Analytics szolgáltatásban magyarázó cikk
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 162d96244b01f8c5e1acf224475aadb9508f0aa5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423634"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Azure Synapse Analytics felügyelt virtuális hálózat (előzetes verzió)

Ez a cikk ismerteti a felügyelt virtuális hálózat az Azure Synapse Analytics.

## <a name="managed-workspace-vnet"></a>Felügyelt munkaterület virtuális hálózat

Az Azure Synapse-munkaterület létrehozásakor választhatja, hogy társítja azt egy virtuális hálózathoz. A munkaterülethez társított virtuális hálózatot az Azure Synapse kezeli. Ezt a virtuális hálózatot *felügyelt munkaterületi virtuális hálózatnak nevezik.*

A felügyelt munkaterületi virtuális hálózat négy féleképpen biztosít értéket:

- Felügyelt munkaterület virtuális hálózat tal kiszervezheti a virtuális hálózat kezelésének terhét az Azure Synapse.
- Nem kell konfigurálni a bejövő NSG-szabályok a saját virtuális hálózatok, hogy az Azure Synapse felügyeleti forgalom adja meg a virtuális hálózat. Ezeknek az NSG-szabályoknak a helytelen konfigurálása szolgáltatáskimaradást okoz az ügyfelek számára.
- Nem kell létrehoznia egy alhálózatot a Spark-fürtök csúcsterhelés alapján.
- Felügyelt munkaterület virtuális hálózat és a felügyelt magánhálózati végpontok védelmet nyújt az adatok kiszivárgása ellen. Felügyelt végpontok csak olyan munkaterületen hozhat létre felügyelt munkaterületet, amelyhez felügyelt munkaterület virtuális hálózat van társítva.

Ha egy munkaterületet hoz létre egy hozzá társított felügyelt munkaterületi virtuális hálózattal, akkor a munkaterület hálózata más munkaterületektől van elkülönítve. Az Azure Synapse különböző analitikus lehetőségeket biztosít egy munkaterületen: adatintegráció, Apache Spark, SQL-készlet és az SQL igény szerinti.

Ha a munkaterület felügyelt munkaterületvirtuális hálózat, adatintegrációs és Spark-erőforrások üzembe helyezése. A felügyelt munkaterület virtuális hálózat is biztosít felhasználói szintű elkülönítése Spark-tevékenységek, mert minden Spark-fürt saját alhálózat.

SQL-készlet és az SQL igény szerinti több-bérlős képességek, és ezért a felügyelt munkaterület virtuális hálózaton kívül található. Az SQL-készlettel és az SQL-igény szerinti SQL-kapcsolatokkal való munkaterületen belüli kommunikáció azure-beli privát hivatkozásokat használ. Ezek a személyes hivatkozások automatikusan létre, amikor létrehoz egy munkaterületet felügyelt munkaterület virtuális hálózat társítva.

>[!IMPORTANT]
>A munkaterület létrehozása után ez a munkaterület-konfiguráció nem módosítható. Például nem konfigurálhat újra egy munkaterületet, amelyhez nincs felügyelt munkaterületi virtuális hálózat társítva, és nem társíthat hozzá virtuális hálózatot. Hasonlóképpen nem konfigurálhat újra egy munkaterületet felügyelt munkaterületi virtuális hálózattal, és nem társíthatja a virtuális hálózatot.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-vnet"></a>Azure Synapse-munkaterület létrehozása felügyelt munkaterületvirtuális hálózattal

Ha olyan Azure Synapse-munkaterületet szeretne létrehozni, amelyhez felügyelt munkaterületi virtuális hálózat tartozik társítva, jelölje be az Azure Portal **Biztonság + hálózat** lapot, és jelölje be a Felügyelt virtuális hálózat **engedélyezése** jelölőnégyzetet.

Ha nem jelöli be a jelölőnégyzetet, akkor a munkaterülethez nem lesz virtuális hálózat társítva.

>[!IMPORTANT]
>A magánhálózati hivatkozások csak felügyelt munkaterületi virtuális hálózatú munkaterületen használhatók.

![Felügyelt munkaterület virtuális hálózatának engedélyezése](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>A felügyelt munkaterület virtuális hálózatról érkező összes kimenő forgalom a jövőben le lesz tiltva. Javasoljuk, hogy az összes adatforráshoz csatlakozzon a felügyelt magánrendszerbeli végpontok használatával.

Az Azure Synapse-munkaterület egy felügyelt munkaterület virtuális hálózathoz való társításával ellenőrizheti az **Áttekintés lehetőséget** az Azure Portalon.

![Munkaterület – áttekintés az Azure Portalon](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>További lépések

Azure [Synapse-munkaterület](../quickstart-create-workspace.md) létrehozása

További információ a [felügyelt magánvégpontokról](./synapse-workspace-managed-private-endpoints.md)

[Felügyelt személyes végpontok létrehozása az adatforrásokhoz](./how-to-create-managed-private-endpoints.md)
