---
title: Hozzon létre és kezelhető az Azure Database for MySQL virtuális hálózati Szolgáltatásvégpontok és szabályok az Azure portal használatával |} A Microsoft Docs
description: Hozzon létre és kezelhető az Azure Database for MySQL virtuális hálózati Szolgáltatásvégpontok és szabályok az Azure portal használatával
author: mbolz
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 9aac664ae78fa0995f39677a362f1b23b9c4af35
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53535301"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Hozzon létre és kezelhető az Azure Database for MySQL virtuális hálózati Szolgáltatásvégpontok és a VNet-szabályok az Azure portal használatával
Virtuális hálózat (VNet) szolgáltatások végpontok és a szabályok egy virtuális hálózat magáncímterét kiterjesztheti az Azure Database for MySQL-kiszolgáló. Azure Database MySQL virtuális hálózati Szolgáltatásvégpontok, korlátai, beleértve az áttekintést lásd: [, Azure Database for MySQL-kiszolgáló virtuális hálózati Szolgáltatásvégpontok](concepts-data-access-and-security-vnet.md). Virtuális hálózati Szolgáltatásvégpontok az Azure Database for MySQL minden támogatott régióban érhetők el.

> [!NOTE]
> Virtuális hálózati Szolgáltatásvégpontok támogatása csak az általános célú és memóriahasználatra optimalizált kiszolgálók esetében érhető el.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Hozzon létre egy virtuális hálózati szabályt, és engedélyezze a szolgáltatásvégpontokat az Azure Portalon

1. A MySQL server a beállítások lapon szakaszban kattintson **kapcsolatbiztonság** nyissa meg a kapcsolat biztonsági ablaktáblát az Azure Database for MySQL-hez. Ezután kattintson a **+ meglévő virtuális hálózat hozzáadása**. Ha nem rendelkezik egy meglévő Vnetet **+ új virtuális hálózat létrehozása** hozhat létre egyet. Lásd: [a rövid útmutató: Hozzon létre egy virtuális hálózatot az Azure portal használatával](../virtual-network/quick-create-portal.md)

   ![Az Azure portal – kapcsolatbiztonság kattintson](./media/howto-manage-vnet-using-portal/1-connection-security.png)

2. Adja meg a virtuális hálózati szabály nevét, válassza ki az előfizetést, virtuális hálózat és alhálózat neve, majd kattintson **engedélyezése**. Ez automatikusan lehetővé teszi a virtuális hálózati Szolgáltatásvégpontok az alhálózaton használatával a **Microsoft.SQL** szolgáltatáscímke.

   ![Az Azure portal – virtuális hálózat konfigurálása](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   A fióknak rendelkeznie kell egy virtuális hálózat és egy szolgáltatásvégpont létrehozásához szükséges engedélyekkel.

   A Szolgáltatásvégpontok a virtuális hálózatokon külön konfigurálhatók, a virtuális hálózathoz írási jogosultsággal rendelkező felhasználó által.
    
   Biztonságos Azure-szolgáltatási erőforrások virtuális hálózathoz, a felhasználó hozzáadott alhálózatokra vonatkozó "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" engedéllyel kell rendelkeznie. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.
    
   További információk a [beépített szerepkörökről](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) és a bizonyos engedélyek [egyéni szerepkörökhöz](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) való hozzárendeléséről.
    
   A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak egyazon előfizetéshez, vagy különböző előfizetésekhez is. Ha a virtuális hálózat és az Azure-szolgáltatási erőforrások különböző előfizetésekhez tartoznak, az erőforrások ugyanahhoz az Active Directory (AD) bérlőhöz kell tartozniuk.

   > [!IMPORTANT]
   > Ez a cikk szolgáltatáskonfiguráció endpoint és megfontolandó szempontok a Szolgáltatásvégpontok konfigurálása előtt erősen ajánlott. **Virtuális hálózati szolgáltatásvégpont:** A [virtuális hálózati szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) egy alhálózat, amelynek a következők: egy vagy több hivatalos Azure-szolgáltatás nevét. Virtuális hálózat services végpontjainak használja a szolgáltatás neve **Microsoft.Sql**, amely hivatkozik az Azure-szolgáltatás SQL-adatbázis neve. Ez a szolgáltatás címke az Azure SQL Database, Azure Database for PostgreSQL és MySQL-szolgáltatásokra is vonatkozik. Fontos, hogy alkalmazása esetén vegye figyelembe a **Microsoft.Sql** szolgáltatáscímke egy szolgáltatásvégpont, konfigurálja a szolgáltatás végpontja forgalmat az összes Azure-adatbázis szolgáltatás, így az Azure SQL Database, Azure Database for PostgreSQL és Azure Database for MySQL-kiszolgálók az alhálózaton. 
   > 

3. Kattintson az engedélyezés után a **OK** és látni fogja, hogy a virtuális hálózati Szolgáltatásvégpontok engedélyezve vannak és a egy virtuális hálózati szabályt.

   ![Virtuális hálózati Szolgáltatásvégpontok engedélyezve van, és a VNet szabály létrehozása](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>További lépések
- Hasonló módon is, a parancsfájl [engedélyezése a virtuális hálózat szolgáltatásvégpontokat, és a VNET szabály létrehozása az Azure Database for MySQL-hez az Azure CLI-vel](howto-manage-vnet-using-cli.md).
- Egy Azure Database for MySQL-kiszolgáló csatlakozik a kapcsolatos útmutatásért lásd: [adatkapcsolattárak az Azure Database for MySQL-hez](./concepts-connection-libraries.md)
