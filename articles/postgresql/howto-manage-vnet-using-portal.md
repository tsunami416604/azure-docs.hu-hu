---
title: Hozzon létre és kezelhető az Azure Database for PostgreSQL virtuális hálózati Szolgáltatásvégpontok és szabályok az Azure portal használatával |} A Microsoft Docs
description: Hozzon létre és kezelhető az Azure Database for PostgreSQL virtuális hálózati Szolgáltatásvégpontok és szabályok az Azure portal használatával
services: postgresql
author: mbolz
ms.author: mbolz
ms.reviewer: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/15/2018
ms.openlocfilehash: 3884b594c903c234ce83c0752f92ddc34b24cd5a
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239319"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Hozzon létre és kezelhető az Azure Database for PostgreSQL virtuális hálózati Szolgáltatásvégpontok és a VNet-szabályok az Azure portal használatával
Virtuális hálózat (VNet) szolgáltatások végpontok és a szabályok egy virtuális hálózat magáncímterét kiterjesztheti az Azure Database for PostgreSQL-kiszolgáló. Azure database for PostgreSQL virtuális hálózati Szolgáltatásvégpontok, többek között a korlátozásokat, áttekintést lásd: [, Azure Database for PostgreSQL-kiszolgáló virtuális hálózati Szolgáltatásvégpontok](concepts-data-access-and-security-vnet.md). Virtuális hálózati Szolgáltatásvégpontok az Azure Database for PostgreSQL minden támogatott régióban érhetők el.

> [!NOTE]
> Virtuális hálózati Szolgáltatásvégpontok támogatása csak az általános célú és memóriahasználatra optimalizált kiszolgálók esetében érhető el.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Hozzon létre egy virtuális hálózati szabályt, és engedélyezze a szolgáltatásvégpontokat az Azure Portalon

1. A PostgreSQL kiszolgáló a beállítások lapon szakaszban kattintson **kapcsolatbiztonság** nyissa meg a kapcsolat biztonsági ablaktáblát az Azure Database for postgresql-hez. Ezután kattintson a **+ meglévő virtuális hálózat hozzáadása**. Ha nem rendelkezik egy meglévő Vnetet **+ új virtuális hálózat létrehozása** hozhat létre egyet. Lásd: [a rövid útmutató: hozzon létre egy virtuális hálózatot az Azure portal használatával](../virtual-network/quick-create-portal.md)

   ![Az Azure portal – kapcsolatbiztonság kattintson](./media/howto-manage-vnet-using-portal/1-connection-security.png)

2. Adja meg a virtuális hálózati szabály nevét, válassza ki az előfizetést, virtuális hálózat és alhálózat neve, majd kattintson **engedélyezése**. Ez automatikusan lehetővé teszi a virtuális hálózati Szolgáltatásvégpontok az alhálózaton használatával a **Microsoft.SQL** szolgáltatáscímke.

   ![Az Azure portal – virtuális hálózat konfigurálása](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   > [!IMPORTANT]
   > Ez a cikk szolgáltatáskonfiguráció endpoint és megfontolandó szempontok a Szolgáltatásvégpontok konfigurálása előtt erősen ajánlott. **Virtuális hálózati szolgáltatásvégpont:** A [virtuális hálózati szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) egy alhálózat, amelynek a következők: egy vagy több hivatalos Azure-szolgáltatás nevét. Virtuális hálózat services végpontjainak használja a szolgáltatás neve **Microsoft.Sql**, amely hivatkozik az Azure-szolgáltatás SQL-adatbázis neve. Ez a szolgáltatás címke az Azure SQL Database, Azure Database for PostgreSQL és MySQL-szolgáltatásokra is vonatkozik. Fontos, hogy alkalmazása esetén vegye figyelembe a **Microsoft.Sql** szolgáltatáscímke egy szolgáltatásvégpont, konfigurálja a szolgáltatás végpontja forgalmat az összes Azure-adatbázis szolgáltatás, így az Azure SQL Database, Azure Database for PostgreSQL és Azure Database for MySQL-kiszolgálók az alhálózaton. 
   > 

3. Kattintson az engedélyezés után a **OK** és látni fogja, hogy a virtuális hálózati Szolgáltatásvégpontok engedélyezve vannak és a egy virtuális hálózati szabályt.

   ![Virtuális hálózati Szolgáltatásvégpontok engedélyezve van, és a VNet szabály létrehozása](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>További lépések
- Hasonló módon is, a parancsfájl [szolgáltatásvégpontokat engedélyezése a virtuális hálózat és a VNET szabály létrehozása az Azure Database for postgresql-hez az Azure CLI-vel](howto-manage-vnet-using-cli.md).
- Segítségre van szüksége az Azure Database for PostgreSQL-kiszolgálóhoz csatlakozik, lásd: [az Azure Database for PostgreSQL csatlakozási kódtárak](./concepts-connection-libraries.md)
