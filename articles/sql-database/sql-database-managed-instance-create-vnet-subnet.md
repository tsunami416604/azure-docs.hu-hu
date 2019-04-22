---
title: Virtuális hálózat létrehozása az Azure SQL Database felügyelt példányába |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan hozhat létre egy virtuális hálózatot, ahol telepítheti az Azure SQL Database felügyelt példányain.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 01/15/2019
ms.openlocfilehash: 5e8b385d018482d281153f1cf80f9953cb8c7f06
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59782524"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Virtuális hálózat létrehozása az Azure SQL Database felügyelt példánya

Ez a cikk bemutatja, hogyan hozhat létre egy érvényes virtuális hálózatot és alhálózatot, ahol telepítheti az Azure SQL Database felügyelt példányain.

Az Azure SQL Database felügyelt példányain telepíteni kell egy Azure-ban [virtuális hálózat](../virtual-network/virtual-networks-overview.md). A központi telepítés lehetővé teszi, hogy a következő esetekben:

- Biztonságos magánhálózati IP-cím
- Felügyelt példány csatlakozik közvetlenül egy a helyszíni hálózatból
- Felügyelt példány csatlakozik a csatolt kiszolgáló vagy egy másik helyszíni adattár
- Felügyelt példány csatlakozik az Azure-erőforrások  

> [!Note]
> Érdemes [az alhálózat mérete határozza meg a felügyelt példány](sql-database-managed-instance-determine-size-vnet-subnet.md) az első példány üzembe helyezése előtt. Az alhálózat nem méretezhető, után nyilvántartani belül az erőforrásokat.
>
> Ha azt tervezi, használja a meglévő virtuális hálózattal, módosítania, hogy a felügyelt példány megfeleljen a hálózati konfigurációt. További információkért lásd: [módosíthatja egy meglévő virtuális hálózatot a felügyelt példány](sql-database-managed-instance-configure-vnet-subnet.md).

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

A legegyszerűbben úgy létrehozása és konfigurálása virtuális hálózat egy olyan Azure Resource Manager üzembe helyezési sablon használatát.

1. Jelentkezzen be az Azure portálra.

2. Válassza ki a **üzembe helyezés az Azure** gombra:

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Ezzel a gombbal megnyithatja, amelyek segítségével konfigurálhatja a hálózati környezet ahol telepíthet a felügyelt példány űrlap.

   > [!Note]
   > Az Azure Resource Manager-sablon üzembe helyezni egy virtuális hálózatot két alhálózattal. Egy alhálózat nevű **ManagedInstances**, felügyelt példány számára fenntartva, és a egy előre konfigurált útválasztási táblázatot tartalmaz. A más, nevű alhálózat **alapértelmezett**, más erőforrások, amelyek hozzá kell férnie a felügyelt példány (például az Azure Virtual Machines) használatos.

3. A hálózati környezet beállításához. A következő képernyőn konfigurálhatja a hálózati környezet paraméterei:

   ![Az Azure-hálózat konfigurálása a Resource Manager-sablon](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   Előfordulhat, hogy a módosítsa a virtuális hálózat és alhálózatok a neveket, és állítsa be a hálózati erőforrásokhoz rendelt IP-címtartományok. Kiválasztása után a **beszerzési** gombra, az űrlap pedig létrehozza és konfigurálja a környezetet. Ha már nincs szüksége a két alhálózattal, törölheti az alapértelmezett értéktől.

## <a name="next-steps"></a>További lépések

- Áttekintéséhez lásd: [mit jelent a felügyelt példány?](sql-database-managed-instance.md).
- Ismerje meg [a felügyelt példány kapcsolati architektúra](sql-database-managed-instance-connectivity-architecture.md).
- Ismerje meg, hogyan [módosíthatja egy meglévő virtuális hálózatot a felügyelt példány](sql-database-managed-instance-configure-vnet-subnet.md).
- Ez az oktatóanyag bemutatja, hogyan hozzon létre egy virtuális hálózatot, hozzon létre egy felügyelt példányt, és egy adatbázist egy adatbázis biztonsági másolatból, lásd: [hozzon létre egy Azure SQL Database felügyelt példányába](sql-database-managed-instance-get-started.md).
- DNS-problémák esetén lásd: [egy egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md).
