---
title: Virtuális hálózat létrehozása Azure SQL Database felügyelt példányhoz
description: Ez a cikk azt ismerteti, hogyan hozhat létre olyan virtuális hálózatot, amelyen Azure SQL Database felügyelt példányt telepíthet.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 09/12/2019
ms.openlocfilehash: ae18671000a2bfd3bedef7805ba5db964fe0cf95
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688173"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Virtuális hálózat létrehozása Azure SQL Database felügyelt példányhoz

Ez a cikk azt ismerteti, hogyan lehet létrehozni egy érvényes virtuális hálózatot és alhálózatot, ahol Azure SQL Database felügyelt példányt telepíthet.

Azure SQL Database felügyelt példányt egy Azure-beli [virtuális hálózaton](../virtual-network/virtual-networks-overview.md)belül kell üzembe helyezni. Ez a központi telepítés a következő forgatókönyveket teszi lehetővé:

- Biztonságos magánhálózati IP-cím
- Csatlakozás felügyelt példányhoz közvetlenül egy helyszíni hálózatról
- Felügyelt példány csatlakoztatása csatolt kiszolgálóhoz vagy egy másik helyszíni adattárhoz
- Felügyelt példány csatlakoztatása az Azure-erőforrásokhoz  

> [!Note]
> Az első példány üzembe helyezése előtt meg kell [határoznia a felügyelt példány alhálózatának méretét](sql-database-managed-instance-determine-size-vnet-subnet.md) . Az alhálózatot az erőforrások belső elhelyezése után nem lehet átméretezni.
>
> Ha meglévő virtuális hálózat használatát tervezi, akkor módosítania kell a hálózati konfigurációt, hogy az megfeleljen a felügyelt példánynak. További információ: [meglévő virtuális hálózat módosítása felügyelt példányhoz](sql-database-managed-instance-configure-vnet-subnet.md).
>
> Felügyelt példány létrehozása után a felügyelt példány vagy VNet másik erőforráscsoporthoz vagy előfizetésbe való áthelyezése nem támogatott.


## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

A virtuális hálózat létrehozásának és konfigurálásának legegyszerűbb módja egy Azure Resource Manager telepítési sablon használata.

1. Jelentkezzen be az Azure portálra.

2. Válassza az **üzembe helyezés az Azure-** ban gombot:

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Ezzel a gombbal megnyithatja a hálózati környezet konfigurálásához használható űrlapot, ahol felügyelt példányt telepíthet.

   > [!Note]
   > Ez a Azure Resource Manager-sablon két alhálózattal rendelkező virtuális hálózatot fog telepíteni. Egy **ManagedInstances**nevű alhálózat felügyelt példány számára van fenntartva, és előre konfigurált útválasztási táblázattal rendelkezik. A másik, **alapértelmezett**nevű alhálózat más erőforrásokhoz is használatos, amelyeknek hozzáférést kell biztosítania a felügyelt példányhoz (például Azure Virtual machines).

3. Konfigurálja a hálózati környezetet. A következő űrlapon konfigurálhatja a hálózati környezet paramétereit:

   ![Resource Manager-sablon az Azure-hálózat konfigurálásához](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   Módosíthatja a virtuális hálózat és az alhálózatok nevét, és módosíthatja a hálózati erőforrásokhoz társított IP-tartományokat. Miután kiválasztotta a **vásárlás** gombot, az űrlap létrehozza és konfigurálja a környezetet. Ha nincs szüksége két alhálózatra, törölheti az alapértelmezettet.

## <a name="next-steps"></a>További lépések

- Tekintse át a [Mi az a felügyelt példány?](sql-database-managed-instance.md)című témakört.
- Ismerje meg a [felügyelt példány kapcsolati architektúráját](sql-database-managed-instance-connectivity-architecture.md).
- Megtudhatja, hogyan [módosíthat egy meglévő virtuális hálózatot a felügyelt példányok számára](sql-database-managed-instance-configure-vnet-subnet.md).
- A virtuális hálózatok létrehozásával, felügyelt példány létrehozásával és adatbázis biztonsági másolatból való visszaállításával kapcsolatos oktatóanyagért lásd: [Azure SQL Database felügyelt példány létrehozása](sql-database-managed-instance-get-started.md).
- DNS-problémák esetén tekintse meg az [Egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md)című témakört.
