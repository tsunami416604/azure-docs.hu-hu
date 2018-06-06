---
title: Létrehozása és kezelése az Azure-adatbázis MySQL VNet Szolgáltatásvégpontok és szabályok az Azure portál használatával |} Microsoft Docs
description: Létrehozása és kezelése az Azure-adatbázis MySQL VNet Szolgáltatásvégpontok és szabályok az Azure portál használatával
services: mysql
author: mbolz
ms.author: mbolz
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 8e4a14eb796875848406b4fc4ff39b90b9a691e9
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736439"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Létrehozása és kezelése az Azure-adatbázis MySQL VNet Szolgáltatásvégpontok és a virtuális hálózat szabályok az Azure portál használatával
Virtuális hálózathoz (VNet) services végpontjainak és szabályok terjessze ki a virtuális hálózat a titkos címtér a MySQL-kiszolgálóhoz tartozó Azure-adatbázis. Az Azure-adatbázis MySQL VNet Szolgáltatásvégpontok korlátozások, beleértve az áttekintést lásd: [MySQL kiszolgáló virtuális hálózat szolgáltatás végpontok Azure adatbázis](concepts-data-access-and-security-vnet.md). Virtuális hálózat Szolgáltatásvégpontok érhetők el minden támogatott régióban nyilvános előzetes MySQL az Azure-adatbázis.

> [!NOTE]
> Virtuális hálózat Szolgáltatásvégpontok támogatása csak olyan általános célú és a Memóriaoptimalizált van.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Egy VNet szabály létrehozása és engedélyezése az Azure portálon végpontok

1. MySQL-kiszolgáló a beállítások lapon elemcsoportban kattintson **kapcsolatbiztonsági** a kapcsolat biztonsági ablak megnyitása az Azure Database a MySQL. Ezután kattintson a **+ meglévő virtuális hálózat hozzáadása**. Ha nem rendelkezik egy meglévő virtuális hálózatot, kattintson **+ új virtuális hálózat létrehozása** kattintva létrehozhat egyet. Lásd: [gyors üzembe helyezés: hozzon létre egy virtuális hálózatot az Azure portál használatával](../virtual-network/quick-create-portal.md)

   ![Azure portál – kattintson a kapcsolat biztonságát](./media/howto-manage-vnet-using-portal/1-connection-security.png)

2. Adja meg a VNet szabály nevét, válassza ki az előfizetés, a virtuális hálózati és alhálózati név, majd kattintson **engedélyezése**. Ez automatikusan VNet szolgáltatás végpontját az alhálózati használata lehetővé teszi a **Microsoft.SQL** címke szolgáltatás.

   ![Azure portál – virtuális hálózat konfigurálása](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   > [!IMPORTANT]
   > Erősen ajánlott a cikkből szolgáltatási végpont, valamint szempontok Szolgáltatásvégpontok konfigurálása előtt. **Virtuális hálózati szolgáltatási végpont:** A [virtuális hálózati szolgáltatási végpont](../virtual-network/virtual-network-service-endpoints-overview.md) egy alhálózat, amelyek a következők: egy vagy több hivatalos Azure-szolgáltatás neve. Virtuális hálózat services végpontjainak használja a szolgáltatás típusának neve **Microsoft.Sql**, amely hivatkozik az Azure-szolgáltatás SQL-adatbázis neve. A szolgáltatás címke az Azure SQL Database, PostgreSQL-és MySQL az Azure-adatbázis is vonatkozik. Fontos figyelembe venni, amikor alkalmazza a **Microsoft.Sql** címke szolgáltatás a virtuális hálózat szolgáltatásvégpont konfigurálja a szolgáltatási végpont forgalmat az összes Azure-adatbázis szolgáltatás, beleértve az Azure SQL Database, PostgreSQL az Azure-adatbázis és Az Azure-adatbázis a MySQL-kiszolgálók az alhálózaton. 
   > 

3. Kattintson az engedélyezés után **OK** és látni fogja, hogy a VNet Szolgáltatásvégpontok engedélyezve vannak egy VNet szabály együtt.

   ![Virtuális hálózat szolgáltatás végpontok engedélyezett és a VNet szabály létrehozása](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>További lépések
- Ehhez hasonlóan is, a parancsfájl [engedélyezése virtuális hálózat szolgáltatás végpontjait és VNET szabály létrehozása az Azure Database az Azure parancssori felület használatával MySQL](howto-manage-vnet-using-cli.md).
- A MySQL-kiszolgáló egy Azure-adatbázishoz szeretne csatlakozni, lásd: [adatkapcsolattárak MySQL az Azure-adatbázis](./concepts-connection-libraries.md)
