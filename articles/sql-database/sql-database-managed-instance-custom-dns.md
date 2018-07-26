---
title: Az Azure SQL Database felügyelt példány egyéni DNS |} A Microsoft Docs
description: Ez a témakör ismerteti egy egyéni DNS-ben az Azure SQL Database felügyelt példánya a konfigurációs beállításait.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: d5bb2f2f4b79c4b03e631fc844a712f76fc69109
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258167"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Egy egyéni DNS konfigurálása az Azure SQL Database felügyelt példánya

Egy Azure SQL Database felügyelt példány (előzetes verzió) kell üzembe helyezni egy Azure-ban [virtuális hálózat (VNet)](../virtual-network/virtual-networks-overview.md). Vannak, néhány alkalmazási helyzetek, csatolt kiszolgálók más SQL-példányok a felhőalapú vagy hibrid környezetben, igénylő privát állomásnevek fel kell oldani a felügyelt példányhoz az. Ebben az esetben, konfigurálnia kell egy egyéni DNS az Azure-on belül. Felügyelt példány az azonos DNS-t használ a belső működésével, mivel a virtuális hálózat DNS-konfigurációt kell lennie a felügyelt példány kompatibilis. 

Ahhoz, hogy kompatibilis felügyelt példányt az egyéni DNS-konfigurációval, a következő lépéseket kell: 
- Egyéni DNS-kérelmeket továbbítja az Azure DNS konfigurálása 
- A virtuális hálózathoz a Custom DNS elsődleges és másodlagos, az Azure DNS beállítása 
- Az egyéni DNS elsődlegesként és az Azure DNS regisztrálása, másodlagos

## <a name="configure-custom-dns-to-forward-requests-to-azure-dns"></a>Egyéni DNS-kérelmeket továbbítja az Azure DNS konfigurálása 

DNS-továbbító konfigurálása a Windows Server 2016-on, tegye a következőket: 

1. A **Kiszolgálókezelő**, kattintson a **eszközök**, és kattintson a **DNS**. 

   ![DNS](./media/sql-database-managed-instance-custom-dns/dns.png) 

2. Kattintson duplán a **továbbítók**.

   ![Továbbítók](./media/sql-database-managed-instance-custom-dns/forwarders.png) 

3. Kattintson a **Szerkesztés** gombra. 

   ![Továbbítók-list](./media/sql-database-managed-instance-custom-dns/forwarders-list.png) 

4. Adja meg az Azure rekurzív feloldók IP-címet, például a 168.63.129.16.

   ![A rekurzív feloldók IP-cím](./media/sql-database-managed-instance-custom-dns/recursive-resolvers-ip-address.png) 
 
## <a name="set-up-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Egyéni DNS elsődleges és másodlagos, az Azure DNS beállítása 
 
Az egy Azure virtuális hálózat DNS-konfiguráció szükséges, hogy adja meg az IP-címeket, így konfigurálása az Azure virtuális Gépen, amelyen a DNS-kiszolgáló a következő lépések segítségével statikus IP-cím: 

1. Az Azure Portalon nyissa meg az egyéni DNS virtuális hálózati adapter.

   ![network-interface](./media/sql-database-managed-instance-custom-dns/network-interface.png) 

2. IP-konfigurációk szakaszban. Válassza ki az IP-konfiguráció 

   ![IP-konfiguráció](./media/sql-database-managed-instance-custom-dns/ip-configuration.png) 


3. Statikus magánhálózati IP-cím beállítva. Jegyezze fel az IP-cím (ezen a képernyőfelvételen a 10.0.1.5) 

   ![statikus](./media/sql-database-managed-instance-custom-dns/static.png) 


## <a name="register-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Egyéni DNS elsődlegesként és az Azure DNS regisztrálása, a másodlagos 

1. Az Azure Portalon keresse meg az egyéni DNS-beállítás a virtuális hálózat számára.

   ![egyéni DNS-beállítás](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Egyéni váltson, és adja meg az egyéni DNS-kiszolgáló IP-címét, valamint az Azure rekurzív feloldók IP-címet, például a 168.63.129.16. 

   ![egyéni DNS-beállítás](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > Nem beállítása az Azure rekurzív feloldó DNS-ben okoz a felügyelt példány hibás állapotba. Utáni helyreállítás állapot megkövetelhetik, hogy hozzon létre új példányt a megfelelő hálózati szabályzatok rendelkező virtuális hálózaton, példány szintű adatok létrehozásához, és az adatbázisok visszaállítása. Lásd: [VNet-konfiguráció](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>További lépések

- Áttekintéséhez lásd: [mit jelent a felügyelt példány](sql-database-managed-instance.md)
- Bemutatja, hogyan hozzon létre egy új felügyelt példány oktatóanyagért lásd: [hozzon létre egy felügyelt példányt](sql-database-managed-instance-create-tutorial-portal.md).
- A felügyelt példányhoz egy virtuális hálózat konfigurálásával kapcsolatos további információkért lásd: [felügyelt példányok VNet-konfiguráció](sql-database-managed-instance-vnet-configuration.md)
