---
title: Virtuális hálózati szabályok használata - Azure portal – Azure Database for PostgreSQL - Single Server
description: Virtuálishálózati szolgáltatásvégpontok és szabályok létrehozása és kezelése az Azure Database for PostgreSQL - Single Server az Azure Portal használatával
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 413c3a7b6fdcda996d3db548fb53f358eb8c71e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978278"
---
# <a name="create-and-manage-vnet-service-endpoints-and-vnet-rules-in-azure-database-for-postgresql---single-server-by-using-the-azure-portal"></a>Virtuálishálózati szolgáltatásvégpontok és virtuálishálózati szabályok létrehozása és kezelése az Azure Database for PostgreSQL - Single Server szolgáltatásban az Azure Portal használatával
Virtuális hálózat (VNet) szolgáltatások végpontok és szabályok kiterjeszti a privát címtér egy virtuális hálózat az Azure Database for PostgreSQL-kiszolgáló. Az Azure Database for PostgreSQL vNet szolgáltatásvégpontok áttekintését, beleértve a korlátozásokat, a [PostgreSQL Server virtuálishálózat-végpontokhoz készült Azure Database című témakörben olvashat.](concepts-data-access-and-security-vnet.md) VNet szolgáltatás végpontok érhetők el az összes támogatott régiókban az Azure Database for PostgreSQL.

> [!NOTE]
> A Virtuálishálózat-szolgáltatás végpontjainak támogatása csak általános célú és memóriaoptimalizált kiszolgálókhoz érhető el.
> Virtuális hálózati társviszony-létesítés esetén, ha a forgalom egy szolgáltatás-végpontokkal rendelkező közös virtuális hálózati átjárón keresztül áramlik, és a társnak kell áramlania, hozzon létre egy ACL/VNet szabályt, amely lehetővé teszi, hogy az Azure virtuális gépek az átjáró virtuális hálózatában hozzáférjenek az Azure Database for PostgreSQL-kiszolgálóhoz.


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Virtuálishálózati szabály létrehozása és szolgáltatásvégpontok engedélyezése az Azure Portalon

1. A PostgreSQL kiszolgálólapján a Beállítások fejléc alatt kattintson a **Kapcsolatbiztonság** elemre az Azure Database for PostgreSQL Kapcsolatbiztonsági ablaktáblájának megnyitásához. 

2. Győződjön meg arról, hogy az Azure-szolgáltatások hoz való hozzáférés engedélyezése vezérlő **je off -ra**van állítva.

> [!Important]
> Ha a vezérlő t in-BE állapotban hagyja, az Azure PostgreSQL adatbázis-kiszolgáló bármely alhálózatból fogadja a kommunikációt. Ha a vezérlőt be állítja BE-re, biztonsági szempontból túlzott hozzáférés lehet. A Microsoft Azure Virtual Network szolgáltatás végpontja, az Azure Database for PostgreSQL virtuális hálózati szabályfunkciójával együttműködve együttesen csökkentheti a biztonsági felületet.

3. Ezután kattintson a **+ Meglévő virtuális hálózat hozzáadása**elemre. Ha nem rendelkezik meglévő virtuális hálózattal, a **+ Új virtuális hálózat létrehozása** elemre kattintva hozhat létre egyet. Lásd: [Rövid útmutató: Virtuális hálózat létrehozása az Azure Portalhasználatával](../virtual-network/quick-create-portal.md)

   ![Azure Portal – kattintson a Kapcsolatbiztonság elemre](./media/howto-manage-vnet-using-portal/1-connection-security.png)

4. Adja meg a virtuális hálózat szabály nevét, jelölje ki az előfizetést, a virtuális hálózatot és az alhálózat nevét, majd kattintson az **Engedélyezés gombra.** Ez automatikusan engedélyezi a Virtuálishálózat-szolgáltatás végpontjait az alhálózaton a **Microsoft.SQL** szolgáltatáscímke használatával.

   ![Azure Portal - a virtuális hálózat konfigurálása](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

    A fióknak rendelkeznie kell a megfelelő engedélyekkel egy virtuális hálózat és egy szolgáltatásvégpont létrehozásához.

    A szolgáltatásvégpontokat a virtuális hálózatokon egymástól függetlenül konfigurálhatja a virtuális hálózathoz írási hozzáféréssel rendelkező felhasználó.
    
    Az Azure-szolgáltatás erőforrásainak virtuális hálózathoz való biztonságossá tétele érdekében a felhasználónak engedéllyel kell rendelkeznie a "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" használatára a hozzáadott alhálózatok hoz. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.
    
    További információk a [beépített szerepkörökről](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) és a bizonyos engedélyek [egyéni szerepkörökhöz](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) való hozzárendeléséről.
    
    A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak egyazon előfizetéshez, vagy különböző előfizetésekhez is. Ha a virtuális hálózat és az Azure-szolgáltatás erőforrásai különböző előfizetésekben vannak, az erőforrásoknak ugyanahhoz az Active Directory (AD) bérlőhöz kell tartozniuk. Győződjön meg arról, hogy mindkét előfizetés rendelkezik a **Microsoft.Sql** erőforrás-szolgáltató regisztrálva. További információkért lásd [az erőforrás-kezelő-regisztrációt][resource-manager-portal]

   > [!IMPORTANT]
   > A szolgáltatásvégpontok konfigurálása előtt ajánlott elolvasni ezt a cikket a szolgáltatásvégpontok konfigurációjáról és szempontokról. **Virtuális hálózat szolgáltatás végpontja:** A [virtuális hálózati szolgáltatás végpontja](../virtual-network/virtual-network-service-endpoints-overview.md) egy alhálózat, amelynek tulajdonságértékei egy vagy több hivatalos Azure-szolgáltatástípus-neveket tartalmaznak. A VNet-szolgáltatások végpontjai a **Microsoft.Sql**szolgáltatástípust használják, amely az SQL Database nevű Azure-szolgáltatásra hivatkozik. Ez a szolgáltatáscímke az Azure SQL Database, az Azure Database for PostgreSQL és a MySQL-szolgáltatásokra is vonatkozik. Fontos megjegyezni, ha a **Microsoft.Sql** szolgáltatáscímke egy Virtuálishálózat-szolgáltatás végpontra alkalmazza a szolgáltatásvégpont-forgalmat az összes Azure Database-szolgáltatáshoz, beleértve az Azure SQL Database-t, az Azure Database for PostgreSQL-t és az Azure Database for MySQL-kiszolgálókat az alhálózaton. 
   > 

5. Ha engedélyezve van, kattintson **az OK** gombra, és látni fogja, hogy a virtuális hálózat szolgáltatás végpontjai engedélyezve vannak egy virtuális hálózati szabállyal együtt.

   ![Virtuális hálózat szolgáltatásvégpontjai engedélyezve vannak, és a virtuális hálózat szabálya létrehozva](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>További lépések
- Hasonlóképpen parancsfájlt is parancsfájlt használhat [a Virtuálishálózati szolgáltatás végpontjainak engedélyezéséhez, és létrehozhat egy VNET-szabályt a PostgreSQL-hez az Azure CLI használatával.](howto-manage-vnet-using-cli.md)
- Ha segítségre van szüksége a PostgreSQL-adatbázishoz való csatlakozáshoz, olvassa el [a Kapcsolattárak az Azure Database for PostgreSQL kapcsolattárak című témakört.](./concepts-connection-libraries.md)

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md