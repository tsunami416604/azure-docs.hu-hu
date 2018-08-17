---
title: Az Azure SQL Database felügyelt példány kapcsolati architektúra |} A Microsoft Docs
description: Ez a cikk az Azure SQL Database felügyelt példányába kommunikációs áttekintését, és kapcsolati architektúra, valamint ismerteti, hogy hogyan a különböző összetevők működéséhez a felügyelt példánynak forgalom.
keywords: ''
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.subservice: managed instance
ms.custom: ''
ms.date: 08/16/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.topic: conceptual
ms.openlocfilehash: 54917c6548c7f0bfacad6408732c5619e6346683
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "40177625"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Az Azure SQL Database felügyelt példány kapcsolati architektúra 

Ez a cikk az Azure SQL Database felügyelt példányába kommunikációs áttekintését, és kapcsolati architektúra, valamint ismerteti, hogy hogyan a különböző összetevők működéséhez a felügyelt példánynak forgalom.  

## <a name="communication-overview"></a>Kommunikáció – áttekintés 

Az alábbi ábrán látható, amelyhez csatlakozni a felügyelt példány, valamint a felügyelt példány megfelelő működéséhez bizalommal rendelkező erőforrások entitások. 

![kapcsolati architektúra entitások](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

A diagram alján megfelelőként jelenik-kommunikációt vevő alkalmazások és eszközök csatlakoztatása a felügyelt példány adatforrásként jelöli.  

Mivel a felügyelt példány-as-a-platformszolgáltatások (PaaS), amely a Microsoft kezeli ezt a szolgáltatást a telemetriai adatok Streamek alapján automatikus ügynököket (felügyeleti, üzembe helyezési és karbantartási) használ. Mivel a felügyelt példány felügyeleti kizárólag a Microsoft felelőssége, a felhasználókat, nem érhetik el a felügyelt példány fürt virtuális gépek RDP-Kapcsolaton keresztül. 

Néhány SQL Server-művelet a végfelhasználók számára vagy alkalmazások által kezdeményezett szükség lehet a felügyelt példány használatával kommunikálhat a platform. Egy felügyelt példány adatbázis - erőforrás a portal, PowerShell és az Azure CLI keresztül közzétett létrehozásának funkcióban. 

Felügyelt példány függ, hogy más Azure-szolgáltatásoktól (például a biztonsági másolatokat az Azure Storage, Azure Service Bus telemetriához, a hitelesítéshez, az Azure Key Vault TDE, és így tovább az Azure AD) a megfelelő működéséhez, és ennek megfelelően kezdeményezi a kapcsolatokat őket. 

Minden kommunikáció, a fenti, vannak titkosítva, és a regisztrált tanúsítványok használatával. Ügyeljen arra, hogy kommunikáló felek megbízható, a felügyelt példány folyamatosan ellenőrzi ezeket a tanúsítványokat kapcsolatba lép a hitelesítésszolgáltató. Ha a tanúsítványok visszavonódnak, vagy a felügyelt példány nem ellenőrizhető a őket, lezárja a kapcsolatokat, az adatok védelme érdekében. 

## <a name="high-level-connectivity-architecture"></a>Magas szintű kapcsolati architektúra 

Magas szinten felügyelt példány egy elkülönített virtuális gépek, amelyek az ügyfél virtuális alhálózaton belül futnak, és virtuális fürtöt külön készlete üzemeltetett szolgáltatás-összetevők. 

Több felügyelt példány egyetlen virtuális fürtként sikerült elkészíteni. A fürt automatikusan kibontva vagy szerződés alapján épített, ha szükséges, amikor az ügyfél megváltoztatja az alhálózat üzembe helyezett példányok száma. 

Ügyfélalkalmazások sikerült felügyelt példány, a lekérdezés és a frissítés adatbázisok kapcsolódni, csak akkor, ha azok futtatható a virtuális hálózat vagy a virtuális társhálózatban vagy a VPN / Expressroute csatlakoztatott hálózati végpont a magánhálózati IP-cím használatával.  

![kapcsolati architektúra ábrája](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

A Microsoft management és a központi telepítési szolgáltatások futtatása a virtuális hálózaton kívüli, így a felügyelt példány és a Microsoft-szolgáltatások közötti kapcsolat a nyilvános IP-címekkel rendelkező végpontok keresztül haladnak. Felügyelt példány kimenő kapcsolatot hoz létre, ha a fogadó úgy tűnik, a nyilvános IP-cím miatt hálózati címfordítás (NAT) érkezik. 

Felügyeleti forgalom az ügyfél virtuális hálózatán keresztül. Ez azt jelenti, hogy a virtuális hálózati infrastruktúra elemeinek érinti, és sikerült ezzel károkat felügyeleti forgalom esetleges példány hibás állapotba, és már nem érhető el. 

> [!IMPORTANT]
> Ügyfél expirience és szolgáltatás rendelkezésre állásának javítása érdekében a Microsoft Azure virtuális hálózati infrastruktúra elemeit, amelyek hatással lehetnek a felügyelt példány működése a hálózati házirend-leképezés vonatkozik. Ez a platform mechanizmussal transzparens módon hálózati követelményei a végfelhasználók számára, a fő célja, hogy megakadályozza a hibás hálózati beállítás, és győződjön meg, hogy a felügyelt példánya a normál működés kommunikációhoz. Felügyelt példány törlése esetén a hálózati házirend-leképezés is el lesz távolítva. 

## <a name="virtual-cluster-connectivity-architecture"></a>Virtuális fürt kapcsolati architektúra 

Vegyük részletesebben megismerni a felügyelt példány kapcsolati architektúra. Az alábbi ábrán látható a virtuális fürt fogalmi elrendezését. 

![kapcsolati architektúra diagramja virtuális fürt](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Ügyfelek csatlakozhatnak a felügyelt példányt, amely rendelkezik egy űrlap < mi_name > név használatával. <clusterid>. database.windows.net. Az állomásnév feloldása egy olyan magánhálózati IP-cím, bár regisztrálva van a nyilvános DNS-zóna és nyilvánosan feloldható. 

A magánhálózati IP-cím tartozik, hogy a felügyelt példány belső Load Balancer (ILB), amely irányítja a forgalmat, a felügyelt példány átjáró (GW). Több felügyelt példány sikerült potenciálisan futtatásakor ugyanazon a fürtön belül GW irányítsa át a forgalmat a megfelelő SQL-kezelő szolgáltatás felügyelt példány állomásnevet használja. 

Felügyeleti és a központi telepítési szolgáltatások csatlakozhat a felügyelt példányhoz az nyilvános végpontot, amely külső terheléselosztó. Az adatforgalmat a csomópontok csak akkor, ha a kapott az előre meghatározott kizárólag a felügyelt példány felügyeleti összetevői által használt portok csoportja. Felügyeleti összetevők és a felügyeleti sík közötti minden kommunikáció a kölcsönösen hitelesített tanúsítvány. 

## <a name="next-steps"></a>További lépések 

- Áttekintéséhez lásd: [mit jelent a felügyelt példány](sql-database-managed-instance.md) 
- Virtuális hálózati konfigurációval kapcsolatos további információkért lásd: [felügyelt példányok Vnetjének konfigurálásával](sql-database-managed-instance-vnet-configuration.md). 
- Ehhez a rövid útmutatóhoz lásd: a felügyelt példány létrehozása: 
  - az a [Azure Portalon](sql-database-managed-instance-create-tutorial-portal.md) 
  - használatával [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) 
  - használatával [Azure Resource Manager-sablon](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/) 
  - használatával [Azure Resource Manager-sablon (jumpbox az ssms-ben szereplő)](https://portal.azure.com/) 

