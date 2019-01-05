---
title: Virtuális hálózat létrehozása az Azure SQL Database felügyelt példány |} A Microsoft Docs
description: Ez a témakör ismerteti, hogyan hozhat létre egy virtuális hálózat (VNet), ahol telepíthet egy Azure SQL Database felügyelt példányain.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: a8000fb26ce5496a9c62ba475b862f8f80adf6b7
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041764"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Virtuális hálózat konfigurálása az Azure SQL Database felügyelt példány

Ez a témakör azt ismerteti, hogyan hozhat létre egy érvényes virtuális hálózatot és alhálózatot, ahol telepítheti az Azure SQL Database felügyelt példányain.

Az Azure SQL Database felügyelt példányain telepíteni kell egy Azure-ban [virtuális hálózat (VNet)](../virtual-network/virtual-networks-overview.md). A központi telepítés lehetővé teszi, hogy a következő esetekben:

- Tegye biztonságossá a magánhálózati IP-címet.
- Felügyelt példány csatlakozik közvetlenül egy a helyszíni hálózatból
- Felügyelt példány csatlakozik a csatolt kiszolgáló vagy egy másik helyszíni adattár
- Felügyelt példány csatlakozik az Azure-erőforrások  

  > [!Note]
  > Érdemes [alhálózat méretét határozza meg a felügyelt példány](sql-database-managed-instance-determine-size-vnet-subnet.md) előtt első példány üzembe helyezéséhez, mert a sunet nem méretezhetők, ha belül az erőforrásokat.
  > Ha azt tervezi, használja a meglévő virtuális hálózattal, módosítania, hogy a felügyelt példány megfeleljen a hálózati konfigurációt. További információkért lásd: [meglévő virtuális hálózat módosítása a felügyelt példány](sql-database-managed-instance-configure-vnet-subnet.md).

## <a name="create-a-new-virtual-network"></a>Új virtuális hálózat létrehozása

A létrehozása és konfigurálása virtuális hálózat legegyszerűbben az Azure Resource Manager üzembe helyezési sablon használata.

1. Jelentkezzen be az Azure portálra.

2. Használat **üzembe helyezés az Azure** üzembe helyezése az Azure-felhőben lévő virtuális hálózati gombra:

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Ez a gomb megnyílik egy űrlapot, amely segítségével konfigurálhatja a hálózati környezet ahol telepíthet a felügyelt példány.

   > [!Note]
   > Az Azure Resource Manager-sablont fog üzembe helyezni a virtuális hálózat két alhálózattal. Egy alhálózat nevű **ManagedInstances** felügyelt példányok számára fenntartva, és rendelkezik előre konfigurált útválasztási táblázatot, míg a másik alhálózat nevű **alapértelmezett** szolgál, amely hozzá kell férnie a felügyelt források A példány (például az Azure virtuális gépeken). Eltávolíthatja **alapértelmezett** Ha már nincs szüksége alhálózatra.

3. Konfigurálja a hálózati környezet. A következő képernyőn konfigurálhatja a hálózati környezet paraméterei:

![Azure-hálózat konfigurálása](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

Előfordulhat, hogy módosítsa a virtuális hálózatok és alhálózatok nevét, és állítsa be a hálózati erőforrásokhoz tartozó IP-címtartományok. Után a "Vásárlás" gombra az űrlap létrehozása, és konfigurálja a környezetet. Ha már nincs szüksége a két alhálózattal, törölheti az alapértelmezett értéktől.

## <a name="next-steps"></a>További lépések

- Áttekintéséhez lásd: [mit jelent a felügyelt példány](sql-database-managed-instance.md).
- Ismerje meg [a felügyelt példány kapcsolati architektúra](sql-database-managed-instance-connectivity-architecture.md).
- Rendezés törlése hogyan [meglévő virtuális hálózat módosítása a felügyelt példány](sql-database-managed-instance-configure-vnet-subnet.md)
- Hozzon létre egy Vnetet, létrehoz egy felügyelt példányt, és egy adatbázist egy adatbázis biztonsági másolatból történő bemutató oktatóanyagot, tekintse meg a [létrehozása egy Azure SQL Database felügyelt példányába](sql-database-managed-instance-get-started.md).
- DNS-problémák esetén lásd: [egy egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md)
