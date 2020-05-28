---
title: Virtuális hálózat létrehozása
titleSuffix: Azure SQL Managed Instance
description: Ez a cikk azt ismerteti, hogyan hozható létre olyan virtuális hálózat, amely támogatja egy felügyelt Azure SQL-példány üzembe helyezését.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 09/12/2019
ms.openlocfilehash: 923f8b447b1103284b2c999a981826ef19a1c7d8
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050750"
---
# <a name="create-a-virtual-network-for-azure-sql-managed-instance"></a>Virtuális hálózat létrehozása az Azure SQL felügyelt példányaihoz
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk azt ismerteti, hogyan hozhat létre egy érvényes virtuális hálózatot és alhálózatot, ahol üzembe helyezheti az Azure SQL felügyelt példányát.

Az Azure SQL felügyelt példányát egy Azure-beli [virtuális hálózaton](../../virtual-network/virtual-networks-overview.md)belül kell üzembe helyezni. Ez a központi telepítés a következő forgatókönyveket teszi lehetővé:

- Biztonságos magánhálózati IP-cím
- Csatlakozás SQL felügyelt példányhoz közvetlenül egy helyszíni hálózatról
- SQL felügyelt példány csatlakoztatása egy csatolt kiszolgálóhoz vagy egy másik helyszíni adattárhoz
- SQL felügyelt példány csatlakoztatása Azure-erőforrásokhoz  

> [!NOTE]
> Az első példány üzembe helyezése előtt meg kell [határoznia az alhálózat méretét az SQL által felügyelt példányhoz](vnet-subnet-determine-size.md) . Az alhálózatot az erőforrások belső elhelyezése után nem lehet átméretezni.
>
> Ha meglévő virtuális hálózat használatát tervezi, a hálózati konfigurációt úgy kell módosítania, hogy az megfeleljen az SQL felügyelt példányának. További információ: [meglévő virtuális hálózat módosítása SQL felügyelt példányhoz](vnet-existing-add-subnet.md).
>
> A felügyelt SQL-példányok létrehozása után az SQL felügyelt példányának vagy VNet másik erőforráscsoporthoz vagy előfizetésbe való áthelyezése nem támogatott.  Az SQL felügyelt példányának másik alhálózatra való áthelyezése nem támogatott.
>

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

A virtuális hálózat létrehozásának és konfigurálásának legegyszerűbb módja egy Azure Resource Manager telepítési sablon használata.

1. Jelentkezzen be az Azure portálra.

2. Válassza az **üzembe helyezés az Azure-** ban gombot:

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Ezzel a gombbal megnyithatja a hálózati környezet konfigurálásához használható űrlapot, amelyen üzembe helyezheti az SQL felügyelt példányát.

   > [!Note]
   > Ez a Azure Resource Manager-sablon két alhálózattal rendelkező virtuális hálózatot fog telepíteni. Egy **ManagedInstances**nevű alhálózat a felügyelt SQL-példány számára van fenntartva, és előre konfigurált útválasztási táblázattal rendelkezik. A másik, **alapértelmezett**nevű alhálózat más erőforrásokhoz is használatos, amelyeknek hozzá kell férniük az SQL felügyelt példányához (például Azure Virtual machines).

3. Konfigurálja a hálózati környezetet. A következő űrlapon konfigurálhatja a hálózati környezet paramétereit:

   ![Resource Manager-sablon az Azure-hálózat konfigurálásához](./media/virtual-network-subnet-create-arm-template/create-mi-network-arm.png)

   Módosíthatja a virtuális hálózat és az alhálózatok nevét, és módosíthatja a hálózati erőforrásokhoz társított IP-tartományokat. Miután kiválasztotta a **vásárlás** gombot, az űrlap létrehozza és konfigurálja a környezetet. Ha nincs szüksége két alhálózatra, törölheti az alapértelmezettet.

## <a name="next-steps"></a>További lépések

- Az áttekintést lásd: [Mi az SQL felügyelt példány?](sql-managed-instance-paas-overview.md).
- Ismerje meg a [kapcsolati architektúrát az SQL felügyelt példányában](connectivity-architecture-overview.md).
- Megtudhatja, hogyan [módosíthat egy meglévő virtuális hálózatot az SQL felügyelt példányain](vnet-existing-add-subnet.md).
- A virtuális hálózatok létrehozásával, a felügyelt SQL-példányok létrehozásával és az adatbázis biztonsági másolatból való visszaállításával kapcsolatos oktatóanyagért tekintse meg [Az Azure SQL felügyelt példányának létrehozása](instance-create-quickstart.md)című témakört.
- DNS-problémák esetén tekintse meg az [Egyéni DNS konfigurálása](custom-dns-configure.md)című témakört.
