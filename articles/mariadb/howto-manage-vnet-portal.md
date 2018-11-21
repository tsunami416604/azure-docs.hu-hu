---
title: Hozzon létre és kezelhető az Azure Database for MariaDB virtuális hálózati Szolgáltatásvégpontok és szabályok az Azure portal használatával |} A Microsoft Docs
description: Hozzon létre és kezelhető az Azure Database for MariaDB virtuális hálózati Szolgáltatásvégpontok és szabályok az Azure portal használatával
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: b94c797bc2351706badb98075514e5324d90a249
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52277301"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Hozzon létre és kezelhető az Azure Database for MariaDB virtuális hálózati Szolgáltatásvégpontok és a VNet-szabályok az Azure portal használatával

> [!IMPORTANT]
> A virtuális hálózatok szolgáltatásvégponti funkciója jelenleg nyilvános előzetes verzióban.

Virtuális hálózat (VNet) szolgáltatások végpontok és a szabályok egy virtuális hálózat magáncímterét kiterjesztheti az Azure Database for MariaDB-kiszolgáló. Azure database for MariaDB virtuális hálózati Szolgáltatásvégpontok, korlátai, beleértve lásd [, Azure Database for MariaDB-kiszolgáló virtuális hálózati Szolgáltatásvégpontok](concepts-data-access-security-vnet.md). Virtuális hálózati Szolgáltatásvégpontok az Azure Database for MariaDB minden támogatott régióban érhetők el.

> [!NOTE]
> Virtuális hálózati Szolgáltatásvégpontok támogatása csak az általános célú és memóriahasználatra optimalizált kiszolgálók esetében érhető el.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Hozzon létre egy virtuális hálózati szabályt, és engedélyezze a szolgáltatásvégpontokat

1. A MariaDB kiszolgálót a beállítások lapon kattintson a fejléc **kapcsolatbiztonság** nyissa meg a kapcsolat biztonsági ablaktáblát az Azure Database for MariaDB. Ezután kattintson a **+ meglévő virtuális hálózat hozzáadása**. Ha nem rendelkezik egy meglévő Vnetet **+ új virtuális hálózat létrehozása** hozhat létre egyet. Lásd: [a rövid útmutató: hozzon létre egy virtuális hálózatot az Azure portal használatával](../virtual-network/quick-create-portal.md)

   ![Az Azure portal – kapcsolatbiztonság kattintson](./media/howto-manage-vnet-portal/1-connection-security.png)

2. Adja meg a virtuális hálózati szabály nevét, válassza ki az előfizetést, virtuális hálózat és alhálózat neve, majd kattintson **engedélyezése**. Ez automatikusan lehetővé teszi a virtuális hálózati Szolgáltatásvégpontok az alhálózaton használatával a **Microsoft.SQL** szolgáltatáscímke.

   ![Az Azure portal – virtuális hálózat konfigurálása](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   A fióknak rendelkeznie kell egy virtuális hálózat és egy szolgáltatásvégpont létrehozásához szükséges engedélyekkel.

   A Szolgáltatásvégpontok a virtuális hálózatokon külön konfigurálhatók, a virtuális hálózathoz írási jogosultsággal rendelkező felhasználó által.
    
   Biztonságos Azure-szolgáltatási erőforrások virtuális hálózathoz, a felhasználó hozzáadott alhálózatokra vonatkozó "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" engedéllyel kell rendelkeznie. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.
    
   További információk a [beépített szerepkörökről](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) és a bizonyos engedélyek [egyéni szerepkörökhöz](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) való hozzárendeléséről.
    
   A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak egyazon előfizetéshez, vagy különböző előfizetésekhez is. Ha a virtuális hálózat és az Azure-szolgáltatási erőforrások különböző előfizetésekhez tartoznak, az erőforrások ugyanahhoz az Active Directory (AD) bérlőhöz kell tartozniuk.

   > [!IMPORTANT]
   > Ez a cikk szolgáltatáskonfiguráció endpoint és megfontolandó szempontok a Szolgáltatásvégpontok konfigurálása előtt erősen ajánlott. **Virtuális hálózati szolgáltatásvégpont:** A [virtuális hálózati szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) egy alhálózat, amelynek a következők: egy vagy több hivatalos Azure-szolgáltatás nevét. Virtuális hálózat services végpontjainak használja a szolgáltatás neve **Microsoft.Sql**, amely hivatkozik az Azure-szolgáltatás SQL-adatbázis neve. Ez a szolgáltatás címke az Azure SQL Database, Azure Database for MariaDB, PostgreSQL és MySQL-szolgáltatásokra is vonatkozik. Fontos, hogy alkalmazása esetén vegye figyelembe a **Microsoft.Sql** szolgáltatáscímke egy szolgáltatásvégpont, konfigurálja a szolgáltatás végpontja forgalmat az összes Azure-adatbázis szolgáltatás, így az Azure SQL Database, Azure Database for PostgreSQL, Azure Database for MariaDB, és az Azure Database for MySQL-kiszolgálók az alhálózaton.
   > 

3. Kattintson az engedélyezés után a **OK** és látni fogja, hogy a virtuális hálózati Szolgáltatásvégpontok engedélyezve vannak és a egy virtuális hálózati szabályt.

   ![Virtuális hálózati Szolgáltatásvégpontok engedélyezve van, és a VNet szabály létrehozása](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [SSL konfigurálása az Azure Database for MySQL-hez](howto-configure-ssl.md)

<!--
- Similarly, you can script to [Enable VNet service endpoints and create a VNET rule for Azure Database for MySQL using Azure CLI](howto-manage-vnet-using-cli.md).
- For help in connecting to an Azure Database for MySQL server, see [Connection libraries for Azure Database for MySQL](./concepts-connection-libraries.md)
-->
