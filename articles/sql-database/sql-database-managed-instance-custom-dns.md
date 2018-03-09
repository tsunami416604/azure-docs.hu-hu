---
title: "Az Azure SQL adatbázis felügyelt példány egyéni DNS |} Microsoft Docs"
description: "Ez a témakör ismerteti az Azure SQL adatbázis felügyelt példánya egy egyéni DNS-konfigurációs beállítások."
services: sql-database
author: srdjan-bozovic
manager: cguyer
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: e660174038aa46ef496f5de67ac616bcd41b1f81
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Példány egy egyéni DNS konfigurálása az Azure SQL Database felügyelete

Egy Azure SQL adatbázis felügyelt példány (előzetes verzió) telepítenie kell egy Azure-ban [virtuális hálózathoz (VNet)](../virtual-network/virtual-networks-overview.md). Van néhány forgatókönyvek, a felhőalapú vagy hibrid környezetben más SQL-példányokban kívánja csatolt kiszolgálók, igénylő titkos állomásnevek oldható fel a felügyelt példányból. Ebben az esetben kell egy egyéni DNS belül Azure konfigurálása. Felügyelt példány a belső működésével az azonos DNS-Kiszolgálókat használ, mert a virtuális hálózat DNS-konfiguráció kell lennie a felügyelt példány kompatibilis. 

Ahhoz, hogy egy egyéni DNS-konfiguráció felügyelt példány kompatibilis, kell végeznie az alábbi lépéseket: 
- A kérelem továbbítása az Azure DNS-egyéni DNS konfigurálása 
- A vnet az elsődleges egyéni DNS és az Azure DNS-ben, mint a másodlagos beállítása 
- Az egyéni DNS elsődleges regisztrálása és az Azure DNS-ben, mint a másodlagos

## <a name="configure-custom-dns-to-forward-requests-to-azure-dns"></a>A kérelem továbbítása az Azure DNS-egyéni DNS konfigurálása 

A Windows Server 2016-os DNS-továbbító konfigurálásához tegye a következőket: 

1. A **Kiszolgálókezelő**, kattintson a **eszközök**, és kattintson a **DNS**. 

   ![DNS](./media/sql-database-managed-instance-custom-dns/dns.png) 

2. Kattintson duplán a **továbbítók**.

   ![A továbbítók](./media/sql-database-managed-instance-custom-dns/forwarders.png) 

3. Kattintson a **Szerkesztés** gombra. 

   ![A továbbítók-lista](./media/sql-database-managed-instance-custom-dns/forwarders-list.png) 

4. Adja meg Azure rekurzív feloldókat IP-címet, például 168.63.129.16.

   ![Rekurzív feloldókat IP-cím](./media/sql-database-managed-instance-custom-dns/recursive-resolvers-ip-address.png) 
 
## <a name="set-up-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Elsődleges egyéni DNS és az Azure DNS-ben, mint a másodlagos beállítása 
 
Egy Azure virtuális hálózatot a DNS-konfiguráció szükséges, hogy adja meg az IP-címek, úgy konfigurálja az Azure virtuális Gépen, amelyen a DNS-kiszolgáló a következő lépések segítségével statikus IP-címmel: 

1. Az Azure portálon nyissa meg az egyéni DNS VM hálózati illesztőt.

   ![network-interface](./media/sql-database-managed-instance-custom-dns/network-interface.png) 

2. IP-konfigurációk szakaszban. Válassza ki az IP-konfiguráció 

   ![IP-konfiguráció](./media/sql-database-managed-instance-custom-dns/ip-configuration.png) 


3. Állítsa be statikus magánhálózati IP-címet. Jegyezze fel az IP-cím (10.0.1.5 meg ezen a képernyőfelvételen látható) 

   ![statikus](./media/sql-database-managed-instance-custom-dns/static.png) 


## <a name="register-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Elsődleges egyéni DNS és az Azure DNS-ben regisztrálja, a másodlagos 

1. Az Azure-portálon keresse meg a Vnethez tartozó egyéni DNS-beállítás.

   ![egyéni DNS-beállítás](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Az egyéni váltson, és adja meg az egyéni DNS-kiszolgáló IP-címét, valamint az Azure rekurzív feloldókat IP-címet, például 168.63.129.16. 

   ![egyéni DNS-beállítás](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > Nem beállítása az Azure rekurzív feloldó a DNS-lista után a kezelt példányt adja meg a meghibásodott. Végezze el a helyreállítást, hogy a állapot előfordulhat, hogy a megfelelő hálózati házirendek a Vneten belül új példányt létrehozni, hozzon létre példányok szintű adatait, és az adatbázisok visszaállítása. Lásd: [VNet konfigurációját](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>További lépések

- Megtudhatja, [Mi az, hogy a felügyelt példánya](sql-database-managed-instance.md)
- Például egy virtuális hálózat létrehozása, és ezután a felügyelt példány létrehozása, [felügyelt példány létrehozása](sql-database-managed-instance-tutorial-portal.md).
- A virtuális hálózat konfigurálása, lásd: [felügyelt példányok VNet konfigurációja](sql-database-managed-instance-vnet-configuration.md)
