---
title: Virtuálishálózati végpontok kezelése – Azure portal – Azure Database for MariaDB
description: Azure-adatbázis létrehozása és kezelése a MariaDB virtuálishálózat-szolgáltatás végpontjaihoz és szabályaihoz az Azure Portal használatával
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 61a8337536f55ceda9bef5b7eaa67a37644d2aca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530597"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Azure-adatbázis létrehozása és kezelése a MariaDB virtuálishálózat-szolgáltatás végpontjaihoz és virtuálishálózat-szabályaihoz az Azure Portal használatával

A virtuális hálózat (VNet) szolgáltatásvégpontjai és szabályai kiterjesztik a virtuális hálózat magánhálózati címtartományát az Azure Database for MariaDB-kiszolgálóra. Az Azure Database for MariaDB VNet szolgáltatásvégpontok áttekintését, beleértve a korlátozásokat, az [Azure Database for MariaDB Server VNet szolgáltatásvégpontjai című témakörben olvashat.](concepts-data-access-security-vnet.md) A VNet-szolgáltatás végpontjai a MariaDB-hez való Azure Database összes támogatott régiójában elérhetők.

> [!NOTE]
> A Virtuálishálózat-szolgáltatás végpontjainak támogatása csak általános célú és memóriaoptimalizált kiszolgálókhoz érhető el.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Virtuálishálózati szabály létrehozása és szolgáltatásvégpontok engedélyezése

1. A MariaDB kiszolgálólapján a Beállítások fejléc alatt kattintson a **Kapcsolatbiztonság** elemre a MariaDB Azure Database kapcsolatbiztonsági ablaktáblájának megnyitásához.

2. Győződjön meg arról, hogy az Azure-szolgáltatások hoz való hozzáférés engedélyezése vezérlő **je off -ra**van állítva.

> [!Important]
> Ha be állítja, az Azure MariaDB adatbázis-kiszolgáló elfogadja a kommunikációt bármely alhálózatból. Ha a vezérlőt be állítja BE-re, biztonsági szempontból túlzott hozzáférés lehet. A Microsoft Azure Virtual Network szolgáltatás végpontja, az Azure Database for MariaDB virtuális hálózati szabályszolgáltatásával együttműködve együttesen csökkentheti a biztonsági felületet.

3. Ezután kattintson a **+ Meglévő virtuális hálózat hozzáadása**elemre. Ha nem rendelkezik meglévő virtuális hálózattal, a **+ Új virtuális hálózat létrehozása** elemre kattintva hozhat létre egyet. Lásd: [Rövid útmutató: Virtuális hálózat létrehozása az Azure Portalhasználatával](../virtual-network/quick-create-portal.md)

   ![Azure Portal – kattintson a Kapcsolatbiztonság elemre](./media/howto-manage-vnet-portal/1-connection-security.png)

4. Adja meg a virtuális hálózat szabály nevét, jelölje ki az előfizetést, a virtuális hálózatot és az alhálózat nevét, majd kattintson az **Engedélyezés gombra.** Ez automatikusan engedélyezi a Virtuálishálózat-szolgáltatás végpontjait az alhálózaton a **Microsoft.SQL** szolgáltatáscímke használatával.

   ![Azure Portal - a virtuális hálózat konfigurálása](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   A fióknak rendelkeznie kell a megfelelő engedélyekkel egy virtuális hálózat és egy szolgáltatásvégpont létrehozásához.

   A szolgáltatásvégpontokat a virtuális hálózatokon egymástól függetlenül konfigurálhatja a virtuális hálózathoz írási hozzáféréssel rendelkező felhasználó.
    
   Az Azure-szolgáltatás erőforrásainak virtuális hálózathoz való biztonságossá tétele érdekében a felhasználónak engedéllyel kell rendelkeznie a "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" használatára a hozzáadott alhálózatok hoz. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.
    
   További információk a [beépített szerepkörökről](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) és a bizonyos engedélyek [egyéni szerepkörökhöz](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) való hozzárendeléséről.
    
   A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak egyazon előfizetéshez, vagy különböző előfizetésekhez is. Ha a virtuális hálózat és az Azure-szolgáltatás erőforrásai különböző előfizetésekben vannak, az erőforrásoknak ugyanahhoz az Active Directory (AD) bérlőhöz kell tartozniuk. Győződjön meg arról, hogy mindkét előfizetés rendelkezik a **Microsoft.Sql** erőforrás-szolgáltató regisztrálva. További információkért lásd [az erőforrás-kezelő-regisztrációt][resource-manager-portal]

   > [!IMPORTANT]
   > A szolgáltatásvégpontok konfigurálása előtt ajánlott elolvasni ezt a cikket a szolgáltatásvégpontok konfigurációjáról és szempontokról. **Virtuális hálózat szolgáltatás végpontja:** A [virtuális hálózati szolgáltatás végpontja](../virtual-network/virtual-network-service-endpoints-overview.md) egy alhálózat, amelynek tulajdonságértékei egy vagy több hivatalos Azure-szolgáltatástípus-neveket tartalmaznak. A VNet-szolgáltatások végpontjai a **Microsoft.Sql**szolgáltatástípust használják, amely az SQL Database nevű Azure-szolgáltatásra hivatkozik. Ez a szolgáltatáscímke az Azure SQL Database, az Azure Database for MariaDB, a PostgreSQL és a MySQL szolgáltatásokra is vonatkozik. Fontos megjegyezni, ha a **Microsoft.Sql** szolgáltatáscímke egy virtuális hálózat szolgáltatás végpontja konfigurálja a szolgáltatás végpontforgalmát az összes Azure Database-szolgáltatások, beleértve az Azure SQL-adatbázis, az Azure Database for PostgreSQL, az Azure Database for MariaDB és az Azure Database for MySQL-kiszolgálók az alhálózaton.
   > 

5. Ha engedélyezve van, kattintson **az OK** gombra, és látni fogja, hogy a virtuális hálózat szolgáltatás végpontjai engedélyezve vannak egy virtuális hálózati szabállyal együtt.

   ![Virtuális hálózat szolgáltatásvégpontjai engedélyezve vannak, és a virtuális hálózat szabálya létrehozva](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>További lépések
- További információ az [SSL konfigurálásáról a MariaDB Azure Database szolgáltatásában](howto-configure-ssl.md)
- Hasonlóképpen parancsfájlt is parancsfájlt használhat [a Virtuálishálózati szolgáltatás végpontjainak engedélyezéséhez, és létrehozhat egy VNET-szabályt az Azure Database for MariaDB számára az Azure CLI használatával.](howto-manage-vnet-cli.md)

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md