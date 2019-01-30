---
title: Az Azure SQL Database felügyelt példány kapcsolati architektúra |} A Microsoft Docs
description: Ez a cikk az Azure SQL Database felügyelt példányába kommunikációs áttekintését, és kapcsolati architektúra, valamint ismerteti, hogy hogyan a különböző összetevők működéséhez a felügyelt példánynak forgalom.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: b709bbacce23a89b8c60b77a524018b50ca1ca5e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245667"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Az Azure SQL Database felügyelt példány kapcsolati architektúra

Ez a cikk az Azure SQL Database felügyelt példányába kommunikációs áttekintését, és kapcsolati architektúra, valamint ismerteti, hogy hogyan a különböző összetevők működéséhez a felügyelt példánynak forgalom.  

Az Azure SQL Database felügyelt példánya kerül Azure virtuális hálózat és az alhálózaton belül a felügyelt példányok dedikált. A központi telepítés lehetővé teszi, hogy a következő esetekben: 
- Tegye biztonságossá a magánhálózati IP-címet.
- Csatlakozás egy felügyelt példányra közvetlenül a helyi hálózatról.
- Felügyelt példány csatlakozik a csatolt kiszolgáló vagy egy másik helyszíni adattár.
- Felügyelt példány csatlakoztatása Azure-erőforrások.

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
> Felhasználói élmény és a szolgáltatás rendelkezésre állása, a Microsoft Azure virtuális hálózati infrastruktúra elemeit, amelyek hatással lehetnek a felügyelt példány működése a hálózati házirend-leképezés vonatkozik. Ez a platform mechanizmussal transzparens módon hálózati követelményei a végfelhasználók számára, a fő célja, hogy megakadályozza a hibás hálózati beállítás, és győződjön meg, hogy a felügyelt példánya a normál működés kommunikációhoz. Felügyelt példány törlése esetén a hálózati házirend-leképezés is el lesz távolítva.

## <a name="virtual-cluster-connectivity-architecture"></a>Virtuális fürt kapcsolati architektúra

Vegyük részletesebben megismerni a felügyelt példány kapcsolati architektúra. Az alábbi ábrán látható a virtuális fürt fogalmi elrendezését.

![kapcsolati architektúra diagramja virtuális fürt](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Felügyelt példányhoz az állomásnevet, amely rendelkezik egy űrlapot az ügyfelek csatlakoznak `<mi_name>.<dns_zone>.database.windows.net`. Az állomásnév feloldása egy olyan magánhálózati IP-cím, bár regisztrálva van a nyilvános DNS-zóna és nyilvánosan feloldható. A `zone-id` automatikusan létrejön, amikor létrehozza a fürtöt. Egy újonnan létrehozott fürt másodlagos felügyelt példány üzemelteti, ha a zóna Azonosítójával megosztja az elsődleges fürt. További információkért lásd: [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets)

A magánhálózati IP-cím tartozik, hogy a felügyelt példány belső Load Balancer (ILB), amely irányítja a forgalmat, a felügyelt példány átjáró (GW). Több felügyelt példány sikerült potenciálisan futtatásakor ugyanazon a fürtön belül GW irányítsa át a forgalmat a megfelelő SQL-kezelő szolgáltatás felügyelt példány állomásnevet használja.

Felügyelet és a központi telepítési szolgáltatások csatlakozhat a felügyelt példány használatával [felügyeleti végpont](#management-endpoint) , amely leképezi a külső terheléselosztó. Forgalmat irányítja a rendszer a csomópontok csak akkor, ha kizárólag a felügyelt példány felügyeleti összetevői által használt portok előre meghatározott érkezett. A csomópontokon, beépített tűzfal van konfigurálva, hogy csak a megadott IP-címtartományok Microsoft érkező forgalmat. Felügyeleti összetevők és a felügyeleti sík közötti minden kommunikáció a kölcsönösen hitelesített tanúsítvány.

## <a name="management-endpoint"></a>Felügyeleti végpont

Az Azure SQL Database felügyelt példánya virtuális fürt, amely a felügyelt példány kezeléséhez használja a Microsoft felügyeleti végpontot tartalmaz. A hálózati szintű és kölcsönös tanúsítvány-ellenőrzés az alkalmazás szintjén beépített tűzfal védi a felügyeleti végponthoz. Is [felügyeleti végpont IP-címének](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Kapcsolat kezdeményezése a felügyelt példány (a biztonsági mentés, a napló) belül jelenik meg, hogy a forgalom származik az felügyeleti végpont nyilvános IP-címét. A felügyelt példány nyilvános szolgáltatásokhoz való hozzáférés sikerült korlátozza tűzfalszabályokat, hogy csak a felügyelt példány IP-cím engedélyezése beállítást. További információ segítségével módszert [ellenőrizze a felügyelt példány beépített tűzfal](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Ez nem vonatkozik a tűzfalszabályok az Azure-szolgáltatásokhoz, amelyek a felügyelt példány ugyanabban a régióban, mivel az Azure-platform-optimalizálást a szolgáltatásokat, amelyek közös elhelyezésű közötti forgalmat a beállítást.

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

Felügyelt példány (a felügyelt példány alhálózatára) dedikált alhálózatán, telepítheti a virtuális hálózatban, amely megfelel az alábbi követelményeknek:
- **Alhálózat dedikált**: A felügyelt példány alhálózatára nem tartalmazhat bármilyen hozzá társított más felhőalapú szolgáltatás, és nem lehet egy átjáró-alhálózatot. Nem lesz képes a felügyelt példány létrehozása egy felügyelt példányra naplóátvitelen kívüli egyéb erőforrásokra tartalmazó alhálózathoz, és nem adhatja később hozzá más erőforrásokhoz az alhálózat.
- **Kompatibilis hálózati biztonsági csoport (NSG)**: Felügyelt példány az alhálózatokhoz társított NSG-t tartalmaznia kell a szabályok az alábbi táblázatban (kötelező bejövő biztonsági szabályokat és kötelező kimenő biztonsági szabályok) található egyéb szabályok előtt látható. Hálózati biztonsági csoportok segítségével teljes mértékben ki férhet hozzá a felügyelt példány adatok végpont 1433-as porton a kimenő forgalmának szűrésével. 
- **Kompatibilis a felhasználó által definiált útvonaltábla (UDR)**: A felügyelt példány alhálózatára rendelkeznie kell egy felhasználó útválasztási táblázatot az **0.0.0.0/0 Internet következő ugrási típusú** , a hozzárendelt kötelező udr-t. Emellett egy udr-t, hogy irányítja a forgalmat, amely rendelkezik a cél virtuális hálózati átjáró vagy virtuális hálózati berendezésre (NVA) keresztül a helyszíni privát IP-címtartományok is hozzáadhat. 
- **Nem kötelező egyéni DNS**: Ha egy egyéni DNS a virtuális hálózaton van megadva, az Azure rekurzív feloldó IP-címet (például a 168.63.129.16) meg kell adni a listához. További információkért lásd: [egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md). Az egyéni DNS-kiszolgáló a következő tartományok és az altartományokra feloldásához képesnek kell lennie: *microsoft.com*, *windows.net*, *windows.com*, *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, és *microsoftonline-p.com*. 
- **Nincsenek Szolgáltatásvégpontok**: A felügyelt példány alhálózatára nem rendelkeznie kell egy hozzá társított végpontot. Győződjön meg arról, hogy szolgáltatás végpontok lehetőség le van tiltva a virtuális hálózat létrehozásakor.
- **Elegendő IP-címek**: A felügyelt példány alhálózatára rendelkeznie kell a legalább 16 IP-címek (javasolt a minimális érték 32 IP-címek). További információkért lásd: [alhálózat méretét határozza meg a felügyelt példányok](sql-database-managed-instance-determine-size-vnet-subnet.md). Telepíthet a felügyelt példányok [a meglévő hálózat](sql-database-managed-instance-configure-vnet-subnet.md) után konfigurálja, hogy megfelelnek [hálózati követelményeiben felügyelt példány](#network-requirements), vagy hozzon létre egy [új hálózatot és alhálózatot](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Nem helyezhet üzembe egy új felügyelt példány, ha a cél alhálózat nem kompatibilis az összes követelménynek. Felügyelt példány létrejön, amikor egy *hálózati házirend-leképezés* hálózati konfiguráció nem megfelelő módosítható az alhálózaton van alkalmazva. Az alhálózatról, az utolsó példány eltávolítása után a *hálózati házirend-leképezés* is el lesz távolítva

### <a name="mandatory-inbound-security-rules"></a>Kötelező bejövő biztonsági szabályok 

| Name (Név)       |Port                        |Protokoll|Forrás           |Cél|Műveletek|
|------------|----------------------------|--------|-----------------|-----------|------|
|felügyelet  |9000, 9003, 1438, 1440, 1452|TCP     |Bármelyik              |Bármelyik        |Engedélyezés |
|mi_subnet   |Bármelyik                         |Bármelyik     |MI ALHÁLÓZAT        |Bármelyik        |Engedélyezés |
|health_probe|Bármelyik                         |Bármelyik     |AzureLoadBalancer|Bármelyik        |Engedélyezés |

### <a name="mandatory-outbound-security-rules"></a>Kötelező kimenő biztonsági szabályok 

| Name (Név)       |Port          |Protokoll|Forrás           |Cél|Műveletek|
|------------|--------------|--------|-----------------|-----------|------|
|felügyelet  |80, 443, 12000|TCP     |Bármelyik              |Internet   |Engedélyezés |
|mi_subnet   |Bármelyik           |Bármelyik     |Bármelyik              |MI ALHÁLÓZAT  |Engedélyezés |

  > [!Note]
  > MI ALHÁLÓZAT az IP-címtartomány, az űrlap 10.x.x.x/y alhálózat hivatkozik. Ez az információ az Azure Portalon (keresztül alhálózat tulajdonságai) található.
  
  > [!Note]
  > Bár a kötelező bejövő biztonsági szabályok érkező adatforgalom engedélyezéséhez _bármely_ portokon 9000, 9003, forrás 1438, 1440, 1452 beépített tűzfal védi ezeket a portokat. Ez [cikk](sql-database-managed-instance-find-management-endpoint-ip-address.md) bemutatja, hogyan felderítése felügyeleti végpont IP-címét, és a tűzfalszabályok ellenőrzése. 
  
  > [!Note]
  > Ha a tranzakciós replikáció használata a felügyelt példány, és bármilyen adatbázishoz felügyelt példány szolgál közzétevőjeként vagy terjesztőjeként, 445-ös (TCP, kimenő) porton is kell lennie nyissa meg a biztonsági szabályok az alhálózat az Azure-fájlmegosztás eléréséhez.
  
## <a name="next-steps"></a>További lépések

- Áttekintéséhez lásd: [mit jelent a felügyelt példány](sql-database-managed-instance.md)
- Ismerje meg, hogyan [új virtuális hálózat konfigurálása](sql-database-managed-instance-create-vnet-subnet.md) vagy [konfigurálása meglévő VNet](sql-database-managed-instance-configure-vnet-subnet.md) ahol felügyelt példány is üzemeltethető.
- [Meg az alhálózat méretének kiszámítása](sql-database-managed-instance-determine-size-vnet-subnet.md) , telepíti a felügyelt példányok. 
- Ehhez a rövid útmutatóhoz lásd: a felügyelt példány létrehozása:
  - Az a [Azure Portalon](sql-database-managed-instance-get-started.md)
  - használatával [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)
  - használatával [Azure Resource Manager-sablon](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)
  - használatával [Azure Resource Manager-sablon (jumpbox az ssms-ben szereplő)](https://portal.azure.com/)
