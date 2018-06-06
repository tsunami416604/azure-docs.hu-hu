---
title: Létrehozása és kezelése az Azure-adatbázis PostgreSQL VNet Szolgáltatásvégpontok és szabályok az Azure portál használatával |} Microsoft Docs
description: Létrehozása és kezelése az Azure-adatbázis PostgreSQL VNet Szolgáltatásvégpontok és szabályok az Azure portál használatával
services: postgresql
author: mbolz
ms.author: mbolz
manager: kfile
editor: jasonwhowell
ms.service: postgresql-database
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 8199bd98b9ae091ff2b27efa8334e8e763879359
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757365"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Létrehozása és kezelése az Azure-adatbázis PostgreSQL VNet Szolgáltatásvégpontok és a virtuális hálózat szabályok az Azure portál használatával
Virtuális hálózathoz (VNet) services végpontjainak és szabályok terjed ki a saját címtartomány a virtuális hálózat az Azure-adatbázis PostgreSQL-kiszolgáló. Az Azure-adatbázis PostgreSQL VNet Szolgáltatásvégpontok korlátozások, beleértve az áttekintést lásd: [PostgreSQL Server VNet szolgáltatás végpontok Azure adatbázis](concepts-data-access-and-security-vnet.md). Virtuális hálózat Szolgáltatásvégpontok érhetők el minden támogatott régióban nyilvános előzetes PostgreSQL az Azure-adatbázis.

> [!NOTE]
> Virtuális hálózat Szolgáltatásvégpontok támogatása csak olyan általános célú és a Memóriaoptimalizált van.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Egy VNet szabály létrehozása és engedélyezése az Azure portálon végpontok

1. PostgreSQL-kiszolgáló a beállítások lapon elemcsoportban kattintson **kapcsolatbiztonsági** Azure-adatbázis a kapcsolat biztonsági ablaktábla PostgreSQL megnyitásáról. Ezután kattintson a **+ meglévő virtuális hálózat hozzáadása**. Ha nem rendelkezik egy meglévő virtuális hálózatot, kattintson **+ új virtuális hálózat létrehozása** kattintva létrehozhat egyet. Lásd: [gyors üzembe helyezés: hozzon létre egy virtuális hálózatot az Azure portál használatával](../virtual-network/quick-create-portal.md)

   ![Azure portál – kattintson a kapcsolat biztonságát](./media/howto-manage-vnet-using-portal/1-connection-security.png)

2. Adja meg a VNet szabály nevét, válassza ki az előfizetés, a virtuális hálózati és alhálózati név, majd kattintson **engedélyezése**. Ez automatikusan VNet szolgáltatás végpontját az alhálózati használata lehetővé teszi a **Microsoft.SQL** címke szolgáltatás.

   ![Azure portál – virtuális hálózat konfigurálása](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   > [!IMPORTANT]
   > Erősen ajánlott a cikkből szolgáltatási végpont, valamint szempontok Szolgáltatásvégpontok konfigurálása előtt. **Virtuális hálózati szolgáltatási végpont:** A [virtuális hálózati szolgáltatási végpont](../virtual-network/virtual-network-service-endpoints-overview.md) egy alhálózat, amelyek a következők: egy vagy több hivatalos Azure-szolgáltatás neve. Virtuális hálózat services végpontjainak használja a szolgáltatás típusának neve **Microsoft.Sql**, amely hivatkozik az Azure-szolgáltatás SQL-adatbázis neve. A szolgáltatás címke az Azure SQL Database, PostgreSQL-és MySQL az Azure-adatbázis is vonatkozik. Fontos figyelembe venni, amikor alkalmazza a **Microsoft.Sql** címke szolgáltatás a virtuális hálózat szolgáltatásvégpont konfigurálja a szolgáltatási végpont forgalmat az összes Azure-adatbázis szolgáltatás, beleértve az Azure SQL Database, PostgreSQL az Azure-adatbázis és Az Azure-adatbázis a MySQL-kiszolgálók az alhálózaton. 
   > 

3. Kattintson az engedélyezés után **OK** és látni fogja, hogy a VNet Szolgáltatásvégpontok engedélyezve vannak egy VNet szabály együtt.

   ![Virtuális hálózat szolgáltatás végpontok engedélyezett és a VNet szabály létrehozása](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>További lépések
- Ehhez hasonlóan is, a parancsfájl [engedélyezése virtuális hálózat szolgáltatás végpontjait és VNET szabály létrehozása az Azure Database az Azure parancssori felület használatával PostgreSQL](howto-manage-vnet-using-cli.md).
- Egy PostgreSQL-kiszolgáló Azure-adatbázishoz szeretne csatlakozni a témakörben talál segítséget [adatkapcsolattárak PostgreSQL Azure-adatbázis](./concepts-connection-libraries.md)
