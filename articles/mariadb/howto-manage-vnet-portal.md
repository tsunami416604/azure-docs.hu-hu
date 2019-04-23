---
title: Hozzon létre és kezelhető az Azure Database for MariaDB virtuális hálózati Szolgáltatásvégpontok és szabályok az Azure portal használatával |} A Microsoft Docs
description: Hozzon létre és kezelhető az Azure Database for MariaDB virtuális hálózati Szolgáltatásvégpontok és szabályok az Azure portal használatával
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 6644b6ae3a9482a1bd3f840a814d3bb6361517fc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790059"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Hozzon létre és kezelhető az Azure Database for MariaDB virtuális hálózati Szolgáltatásvégpontok és a VNet-szabályok az Azure portal használatával

Virtuális hálózat (VNet) szolgáltatások végpontok és a szabályok egy virtuális hálózat magáncímterét kiterjesztheti az Azure Database for MariaDB-kiszolgáló. Azure database for MariaDB virtuális hálózati Szolgáltatásvégpontok, korlátai, beleértve lásd [, Azure Database for MariaDB-kiszolgáló virtuális hálózati Szolgáltatásvégpontok](concepts-data-access-security-vnet.md). Virtuális hálózati Szolgáltatásvégpontok az Azure Database for MariaDB minden támogatott régióban érhetők el.

> [!NOTE]
> Virtuális hálózati Szolgáltatásvégpontok támogatása csak az általános célú és memóriahasználatra optimalizált kiszolgálók esetében érhető el.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Hozzon létre egy virtuális hálózati szabályt, és engedélyezze a szolgáltatásvégpontokat

1. A MariaDB kiszolgálót a beállítások lapon kattintson a fejléc **kapcsolatbiztonság** nyissa meg a kapcsolat biztonsági ablaktáblát az Azure Database for MariaDB.

2. Győződjön meg arról, hogy a hozzáférés engedélyezése az Azure-szolgáltatások vezérlő értéke **OFF**.

> [!Important]
> ON értékre állítsa be, ha az Azure MariaDB adatbázis-kiszolgáló minden olyan alhálózat-kommunikációt fogad el. Hagyja a vezérlőt, állítsa be, előfordulhat, hogy egy biztonsági szempontból túlzott mértékű hozzáférést. A Microsoft Azure Virtual Network szolgáltatás végpont szolgáltatás együttes a virtuális hálózati szabály funkció az Azure Database for MariaDB, együtt csökkentheti a biztonsági támadási.

3. Ezután kattintson a **+ meglévő virtuális hálózat hozzáadása**. Ha nem rendelkezik egy meglévő Vnetet **+ új virtuális hálózat létrehozása** hozhat létre egyet. Lásd: [a rövid útmutató: Hozzon létre egy virtuális hálózatot az Azure portal használatával](../virtual-network/quick-create-portal.md)

   ![Az Azure portal – kapcsolatbiztonság kattintson](./media/howto-manage-vnet-portal/1-connection-security.png)

4. Adja meg a virtuális hálózati szabály nevét, válassza ki az előfizetést, virtuális hálózat és alhálózat neve, majd kattintson **engedélyezése**. Ez automatikusan lehetővé teszi a virtuális hálózati Szolgáltatásvégpontok az alhálózaton használatával a **Microsoft.SQL** szolgáltatáscímke.

   ![Az Azure portal – virtuális hálózat konfigurálása](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   A fióknak rendelkeznie kell egy virtuális hálózat és egy szolgáltatásvégpont létrehozásához szükséges engedélyekkel.

   A Szolgáltatásvégpontok a virtuális hálózatokon külön konfigurálhatók, a virtuális hálózathoz írási jogosultsággal rendelkező felhasználó által.
    
   Biztonságos Azure-szolgáltatási erőforrások virtuális hálózathoz, a felhasználó hozzáadott alhálózatokra vonatkozó "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" engedéllyel kell rendelkeznie. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.
    
   További információk a [beépített szerepkörökről](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) és a bizonyos engedélyek [egyéni szerepkörökhöz](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) való hozzárendeléséről.
    
   A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak egyazon előfizetéshez, vagy különböző előfizetésekhez is. Ha a virtuális hálózat és az Azure-szolgáltatási erőforrások különböző előfizetésekhez tartoznak, az erőforrások ugyanahhoz az Active Directory (AD) bérlőhöz kell tartozniuk.

   > [!IMPORTANT]
   > Ez a cikk szolgáltatáskonfiguráció endpoint és megfontolandó szempontok a Szolgáltatásvégpontok konfigurálása előtt erősen ajánlott. **Virtuális hálózati szolgáltatásvégpont:** A [virtuális hálózati szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) egy alhálózat, amelynek a következők: egy vagy több hivatalos Azure-szolgáltatás nevét. Virtuális hálózat services végpontjainak használja a szolgáltatás neve **Microsoft.Sql**, amely hivatkozik az Azure-szolgáltatás SQL-adatbázis neve. Ez a szolgáltatás címke az Azure SQL Database, Azure Database for MariaDB, PostgreSQL és MySQL-szolgáltatásokra is vonatkozik. Fontos, hogy alkalmazása esetén vegye figyelembe a **Microsoft.Sql** szolgáltatáscímke egy szolgáltatásvégpont, konfigurálja a szolgáltatás végpontja forgalmat az összes Azure-adatbázis szolgáltatás, így az Azure SQL Database, Azure Database for PostgreSQL, Azure Database for MariaDB, és az Azure Database for MySQL-kiszolgálók az alhálózaton.
   > 

5. Kattintson az engedélyezés után a **OK** és látni fogja, hogy a virtuális hálózati Szolgáltatásvégpontok engedélyezve vannak és a egy virtuális hálózati szabályt.

   ![Virtuális hálózati Szolgáltatásvégpontok engedélyezve van, és a VNet szabály létrehozása](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [SSL konfigurálása az Azure Database for MariaDB](howto-configure-ssl.md)
- Hasonló módon is, a parancsfájl [szolgáltatásvégpontokat engedélyezése a virtuális hálózat és a VNET szabály létrehozása az Azure Database for MariaDB Azure CLI-vel](howto-manage-vnet-cli.md).
