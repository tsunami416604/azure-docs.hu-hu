---
title: Virtuális hálózat létrehozása felügyelt példányhoz
description: Ez a cikk ismerteti, hogyan hozhat létre egy virtuális hálózatot, ahol az Azure SQL Database felügyelt példány üzembe helyezhető.
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
ms.openlocfilehash: 0ce88f9a61b8aa7c2588a6e077d694afa6fb8631
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878850"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Virtuális hálózat létrehozása az Azure SQL Database felügyelt példányához

Ez a cikk bemutatja, hogyan hozhat létre egy érvényes virtuális hálózatot és alhálózatot, ahol telepítheti az Azure SQL Database felügyelt példány.

Az Azure SQL Database felügyelt példányát egy Azure [virtuális hálózaton](../virtual-network/virtual-networks-overview.md)belül kell telepíteni. Ez a központi telepítés a következő eseteket teszi lehetővé:

- Magánjellegű IP-cím biztonságossá tétele
- Csatlakozás felügyelt példányhoz közvetlenül a helyszíni hálózatról
- Felügyelt példány csatlakoztatása csatolt kiszolgálóhoz vagy más helyszíni adattárhoz
- Felügyelt példány csatlakoztatása Azure-erőforrásokhoz  

> [!NOTE]
> Az első példány telepítése előtt meg kell [határoznia a felügyelt példány alhálózatának méretét.](sql-database-managed-instance-determine-size-vnet-subnet.md) Az alhálózat nem méretezhető át, miután az erőforrásokat behelyezte.
>
> Ha meglévő virtuális hálózatot kíván használni, módosítania kell a hálózati konfigurációt a felügyelt példány nak megfelelően. További információt a [Felügyelt példány meglévő virtuális hálózatának módosítása című témakörben talál.](sql-database-managed-instance-configure-vnet-subnet.md)
>
> Felügyelt példány létrehozása után a felügyelt példány vagy virtuális hálózat áthelyezése egy másik erőforráscsoportba vagy előfizetésbe nem támogatott.  A felügyelt példány áthelyezése egy másik alhálózatba szintén nem támogatott.
>

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

A virtuális hálózat létrehozásának és konfigurálásának legegyszerűbb módja egy Azure Resource Manager telepítési sablon használata.

1. Jelentkezzen be az Azure portálra.

2. Válassza a **Telepítés az Azure-ba** gombot:

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Ez a gomb megnyit egy űrlapot, amely segítségével konfigurálhatja a hálózati környezetet, ahol a felügyelt példány telepíthető.

   > [!Note]
   > Ez az Azure Resource Manager-sablon két alhálózattal rendelkező virtuális hálózatot telepít. Egy felügyelt **példánynak**nevezett alhálózat van fenntartva a felügyelt példány számára, és előre konfigurált útvonaltáblával rendelkezik. A másik alhálózat, az **alapértelmezett**, más erőforrásokhoz használható, amelyek nek hozzá kell férniük a felügyelt példányhoz (például az Azure virtuális gépekhez).

3. Konfigurálja a hálózati környezetet. A következő űrlapon konfigurálhatja a hálózati környezet paramétereit:

   ![Erőforrás-kezelő sablon az Azure-hálózat konfigurálásához](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   Módosíthatja a virtuális hálózat és az alhálózatok nevét, és módosíthatja a hálózati erőforrásokhoz társított IP-tartományokat. Miután kiválasztotta a **Vásárlás** gombot, ez az űrlap létrehozza és konfigurálja a környezetet. Ha nincs szüksége két alhálózatra, törölheti az alapértelmezettet.

## <a name="next-steps"></a>További lépések

- Az áttekintést a [Mi a felügyelt példány?](sql-database-managed-instance.md)
- Ismerje meg [a felügyelt példány kapcsolódási architektúráját.](sql-database-managed-instance-connectivity-architecture.md)
- További információ a [felügyelt példány meglévő virtuális hálózatának módosításáról.](sql-database-managed-instance-configure-vnet-subnet.md)
- A virtuális hálózat létrehozásáról, felügyelt példány létrehozásáról és az adatbázis adatbázis-biztonsági mentésből történő visszaállításáról az [Azure SQL Database Managed Instance létrehozása című](sql-database-managed-instance-get-started.md)témakörben található.
- A DNS-sel kapcsolatos problémákról az [Egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md)című témakörben van.
