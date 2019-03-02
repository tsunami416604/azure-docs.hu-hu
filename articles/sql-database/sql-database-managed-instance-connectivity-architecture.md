---
title: Az Azure SQL Database felügyelt példány kapcsolati architektúra |} A Microsoft Docs
description: Tudnivalók Azure SQL Database felügyelt példány kommunikációs és kapcsolati architektúra, valamint, hogy hogyan az összetevők közvetlen forgalom a felügyelt példányhoz.
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
ms.date: 02/26/2019
ms.openlocfilehash: 6ef020ff1054416e2b9af5af824b9aa27f0b1e64
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247239"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Az Azure SQL Database felügyelt példány kapcsolati architektúra 

Ez a cikk bemutatja egy Azure SQL Database felügyelt példány a kommunikáció. Kapcsolati architektúra, és hogyan az összetevők irányítani a forgalmat a következő felügyelt példányt is ismerteti.  

Az SQL Database felügyelt példány az Azure virtuális hálózat és az alhálózatot, amelyet a felügyelt példányok dedikált helyezik. A központi telepítés biztosítja:

- Biztonságos magánhálózati IP-cím.
- A helyszíni hálózat csatlakoztatása egy felügyelt példányra lehetővé teszi.
- Felügyelt példány csatlakozni a csatolt kiszolgáló vagy egy másik lehetővé teszi a helyszíni adattárban.
- Lehetővé teszi a felügyelt példány az Azure erőforrások eléréséhez.

## <a name="communication-overview"></a>Kommunikáció – áttekintés

Az alábbi ábrán látható entitások, amelyek a felügyelt példány csatlakoznak. Azt is bemutatja a felügyelt példány folytatott kommunikációhoz szükséges erőforrásokat. A diagram alján a kommunikációs folyamat vevő alkalmazások és eszközök, amelyek adatforrásként szolgálnak a felügyelt példány csatlakoznak jelöli.  

![Kapcsolati architektúra entitások](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Felügyelt példány az platform szolgáltatásajánlatként (PaaS). A Microsoft az automatizált ügynökökkel (felügyeleti, üzembe helyezés és a karbantartási) kezelheti ezt a szolgáltatást a telemetriai adatok Streamek alapján. Mivel a Microsoft management felelős, ügyfelek nem fér hozzá a felügyelt példány fürt virtuális gépek keresztül a távoli asztal protokoll (RDP).

Bizonyos felhasználók vagy alkalmazások által indított tevékenységek szükség lehet az SQL Server-példányt a identitásplatformmal felügyelt. Egy eset létrehozása egy felügyelt példány adatbázisa. Ezt az erőforrást az Azure portal, PowerShell, az Azure CLI és a REST API használatával van közzétéve.

Felügyelt példányok Azure-szolgáltatások például a biztonsági másolatokat az Azure Storage, Azure Service Bus telemetriához, Azure Active Directory hitelesítést és az Azure Key Vault függ a transzparens adattitkosítás (TDE). A felügyelt példányok kapcsolat ezeket a szolgáltatásokat.

Minden kommunikáció titkosítási és aláírási tanúsítványokat használja. Ellenőrizze a megbízhatósága kommunikáció feleket, felügyelt példányok folyamatosan ellenőrizze ezeket a tanúsítványokat kapcsolatba lép a hitelesítésszolgáltató. Ha a tanúsítványok visszavonódnak, vagy nem lehet ellenőrizni, a következő felügyelt példányt bezárja a kapcsolatokat, az adatok védelme érdekében.

## <a name="high-level-connectivity-architecture"></a>Magas szintű kapcsolati architektúra

Magas szinten a felügyelt példány egy szolgáltatás-összetevők. Ezek az összetevők egy dedikált csoportján belül az ügyfél virtuális hálózatának alhálózatához futtató elkülönített virtuális gépeket üzemelnek. Ezek a gépek virtuális fürtöt alkotnak.

Virtuális fürt több felügyelt példányt is üzemeltethet. Szükség esetén a fürt automatikusan bontja ki, vagy csökken, amikor az ügyfél megváltoztatja az alhálózat üzembe helyezett példányok száma.

Ügyfélalkalmazások csatlakozhat a felügyelt példány, és lekérdezheti, és a frissítés adatbázisok csak akkor, ha a virtuális hálózaton belül futnak társviszonyban álló virtuális hálózati vagy VPN- vagy Azure ExpressRoute által csatlakoztatott hálózat. Ezt a hálózatot a végpont és a egy magánhálózati IP-címet kell használnia.  

![kapcsolati architektúra ábrája](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

A Microsoft management és a központi telepítési szolgáltatások futnak a virtuális hálózaton kívülről. Felügyelt példány és a Microsoft-szolgáltatások a végpontok, amelyeken a nyilvános IP-címek keresztül csatlakozhatnak. Ha egy felügyelt példányt hoz létre egy kimenő kapcsolatot fogadó hálózati címfordítás (NAT) révén, mint a kapcsolat tekintse meg a nyilvános IP-cím származik.

Felügyeleti forgalom az ügyfél virtuális hálózatán keresztül. Ez azt jelenti, hogy a virtuális hálózati infrastruktúra elemeinek károsíthatják felügyeleti forgalom azáltal, hogy a példány sikertelen lesz, és már nem érhető el.

> [!IMPORTANT]
> Felhasználói élményét és szolgáltatás rendelkezésre állása, a Microsoft Azure virtuális hálózati infrastruktúra-elemeket a szándék hálózati házirend vonatkozik. A szabályzat hatással lehet a felügyelt példány működését. Ez a platform mechanizmus a hálózati követelményei a felhasználók számára transzparens módon kommunikál. A Csoportházirend fő célja, hogy a hibás hálózati beállítás és, hogy a normál felügyelt példány. Ha töröl egy felügyelt példányt, a hálózati szándék házirend is törlődik.

## <a name="virtual-cluster-connectivity-architecture"></a>Virtuális fürt kapcsolati architektúra

Vegyük részletesebben megismerni, felügyelt példányok kapcsolati architektúra. Az alábbi ábrán látható a virtuális fürt fogalmi elrendezését.

![A virtuális fürt kapcsolati architektúra](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Az ügyfelek egy állomásnevet, amely rendelkezik az űrlap használatával csatlakozhat a felügyelt példány `<mi_name>.<dns_zone>.database.windows.net`. Az állomásnév feloldása egy magánhálózati IP-cím, bár azt a nyilvános tartomány neve tartománynévrendszer (DNS) zónában lévő regisztrálva van, és nyilvánosan feloldható. A `zone-id` automatikusan létrejön, amikor a fürt létrehozásához. Ha egy újonnan létrehozott fürt másodlagos felügyelt példány üzemelteti, a zóna Azonosítójával megosztja az elsődleges fürt. További információkért lásd: [autofailover csoportok használatával engedélyezhető az átlátható és koordinált több adatbázis feladatátvételét](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

A magánhálózati IP-cím tartozik, a felügyelt példány belső terheléselosztót. A load balancer irányítja a forgalmat a felügyelt példány átjáróhoz. Több felügyelt példány futtathatja ugyanazon a fürtön belül, mert az átjáró forgalom átirányítása a megfelelő SQL-adatbázismotor szolgáltatás használja a felügyelt példány állomás neve.

Felügyeleti és a központi telepítési szolgáltatások használatával csatlakozhat a felügyelt példány egy [felügyeleti végpont](#management-endpoint) maps külső terheléselosztó. Forgalmat irányítja a rendszer a csomópontok csak akkor, ha azt csak a felügyelt példány felügyeleti összetevők által használt portok előre meghatározott érkezik. A csomópontok beépített tűzfal van beállítva, hogy csak a Microsoft IP-címtartományok érkező forgalmat. Tanúsítványok kölcsönös hitelesítésére az összetevőket és a felügyeleti sík közötti minden kommunikáció.

## <a name="management-endpoint"></a>Felügyeleti végpont

A Microsoft egy felügyeleti végpont használatával kezeli a következő felügyelt példányt. Ez a végpont a példány virtuális fürt belül van. A felügyeleti végponthoz a hálózati szinten beépített tűzfal védi. Az alkalmazás szintjén a védi kölcsönös tanúsítvány-ellenőrzés. A végpont IP-cím megkereséséhez lásd: [határozza meg a felügyeleti végpont IP-cím](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Ha a kapcsolatok indítsa el a következő felügyelt példányt belül (a biztonsági mentések és a vizsgálati naplók), forgalom jelenik meg, a felügyeleti végponthoz nyilvános IP-címről indított el. Hozzáférés korlátozható nyilvános szolgáltatásokhoz a felügyelt példány csak a felügyelt példány IP-cím tűzfalszabályokban beállításával. További információkért lásd: [beépített tűzfal a felügyelt példány ellenőrzése](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Ellentétben a tűzfalon belül a felügyelt példány induló kapcsolatok az Azure-szolgáltatások, amelyek a felügyelt példány-régión belül van egy tűzfal, amely a forgalmat, amely a szolgáltatás közötti van optimalizálva.

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

A virtuális hálózaton belüli kijelölt alhálózatot a felügyelt példány üzembe helyezése. Az alhálózat a következő jellemzőkkel kell rendelkeznie:

- **Kijelölt alhálózatot:** A felügyelt példány alhálózatára bármely más felhőalapú szolgáltatás, amely rendelkezik társított nem tartalmazhat, és nem lehet egy átjáró-alhálózatot. Az alhálózat nem tartalmazhat bármilyen erőforrás, de a következő felügyelt példányt, és később az alhálózat nem adható hozzá erőforrásokat.
- **Hálózati biztonsági csoport (NSG):** A virtuális hálózathoz társított NSG-t meg kell határoznia [bejövő biztonsági szabályok](#mandatory-inbound-security-rules) és [kimenő biztonsági szabályok](#mandatory-outbound-security-rules) előtt egyéb szabályok. Az NSG-KET segítségével férhet hozzá a felügyelt példány adatok végpont 1433-as porton a kimenő forgalmának szűrésével.
- **Felhasználó által megadott útvonal (UDR) tábla:** Adott tartalmaznia kell a virtuális hálózathoz társított UDR tábla [bejegyzések](#user-defined-routes).
- **Nincsenek Szolgáltatásvégpontok:** Nem service-végpont a felügyelt példány alhálózatára társítva kell lennie. Győződjön meg arról, hogy a szolgáltatás-végpontok lehetőség le van tiltva a virtuális hálózat létrehozásakor.
- **Elegendő IP-címek:** A felügyelt példány alhálózatára rendelkeznie kell legalább 16 IP-címet. Az ajánlott minimális érték 32 IP-címeket. További információkért lásd: [határozza meg a felügyelt példányok alhálózat méretét](sql-database-managed-instance-determine-size-vnet-subnet.md). Telepíthet a felügyelt példányok [a meglévő hálózat](sql-database-managed-instance-configure-vnet-subnet.md) után konfigurálja, hogy eleget [a hálózati követelmények a felügyelt példányokhoz](#network-requirements). Máskülönben hozzon létre egy [új hálózatot és alhálózatot](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Ha a cél alhálózat nem rendelkezik a következő jellemzőkkel nem telepíthet egy új felügyelt példányát. Amikor létrehoz egy felügyelt példány, egy hálózati szándék szabályzat érvényes az alhálózat hálózati beállítása nem megfelelő módosításának megakadályozása. Az utolsó példány az alhálózatról eltávolítása után a hálózati szándék házirend is törlődik.

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
|mi_subnet   |Bármelyik           |Bármelyik     |Bármelyik              |MI ALHÁLÓZATI *  |Engedélyezés |

\* MI ALHÁLÓZAT az IP-címtartományt az űrlap 10.x.x.x/y alhálózat hivatkozik. Ezt az információt találja az Azure Portalon, az alhálózat tulajdonságait.

> [!IMPORTANT]
> Bár a szükséges bejövő biztonsági szabályok érkező adatforgalom engedélyezéséhez _bármely_ portokon forrás 9000, 9003, 1438, 1440 és 1452, ezeket a portokat a beépített tűzfal védi. További információkért lásd: [határozza meg a felügyeleti végpont címe](sql-database-managed-instance-find-management-endpoint-ip-address.md).

> [!NOTE]
> Tranzakciós replikáció használata a felügyelt példány, és minden olyan példány adatbázis közzétevő vagy forgalmazó használ, nyissa meg az alhálózat biztonsági szabályok a 445-ös (TCP, kimenő) porton. Ez a port lehetővé teszi a hozzáférést az Azure-fájlmegosztást.

### <a name="user-defined-routes"></a>Felhasználó által megadott útvonalak

|Name (Név)|Címelőtag|Következő ugrás|
|----|--------------|-------|
|subnet_to_vnetlocal|[mi_subnet]|Virtuális hálózat|
|mi-0-5-next-hop-internet|0.0.0.0/5|Internet|
|mi-11-8-nexthop-internet|11.0.0.0/8|Internet|
|mi-12-6-nexthop-internet|12.0.0.0/6|Internet|
|mi-128-3-nexthop-internet|128.0.0.0/3|Internet|
|mi-16-4-nexthop-internet|16.0.0.0/4|Internet|
|mi-160-5-nexthop-internet|160.0.0.0/5|Internet|
|mi-168-6-nexthop-internet|168.0.0.0/6|Internet|
|mi-172-12-nexthop-internet|172.0.0.0/12|Internet|
|mi-172-128-9-nexthop-internet|172.128.0.0/9|Internet|
|mi-172-32-11-nexthop-internet|172.32.0.0/11|Internet|
|mi-172-64-10-nexthop-internet|172.64.0.0/10|Internet|
|mi-173-8-nexthop-internet|173.0.0.0/8|Internet|
|mi-174-7-nexthop-internet|174.0.0.0/7|Internet|
|mi-176-4-nexthop-internet|176.0.0.0/4|Internet|
|mi-192-128-11-nexthop-internet|192.128.0.0/11|Internet|
|mi-192-160-13-nexthop-internet|192.160.0.0/13|Internet|
|mi-192-169-16-nexthop-internet|192.169.0.0/16|Internet|
|mi-192-170-15-nexthop-internet|192.170.0.0/15|Internet|
|mi-192-172-14-nexthop-internet|192.172.0.0/14|Internet|
|mi-192-176-12-nexthop-internet|192.176.0.0/12|Internet|
|mi-192-192-10-nexthop-internet|192.192.0.0/10|Internet|
|mi-192-9-nexthop-internet|192.0.0.0/9|Internet|
|mi-193-8-nexthop-internet|193.0.0.0/8|Internet|
|mi-194-7-nexthop-internet|194.0.0.0/7|Internet|
|mi-196-6-nexthop-internet|196.0.0.0/6|Internet|
|mi-200-5-nexthop-internet|200.0.0.0/5|Internet|
|mi-208-4-nexthop-internet|208.0.0.0/4|Internet|
|mi-224-3-nexthop-internet|224.0.0.0/3|Internet|
|mi-32-3-nexthop-internet|32.0.0.0/3|Internet|
|mi-64-2-nexthop-internet|64.0.0.0/2|Internet|
|mi-8-7-nexthop-internet|8.0.0.0/7|Internet|
||||

Emellett bejegyzést adhat hozzá az útvonaltáblát irányíthatja a forgalmat, amely rendelkezik a helyszíni privát IP-címtartományok a célhelyet a virtuális hálózati átjáró vagy a virtuális hálózati berendezésre (NVA) keresztül.

Ha a virtuális hálózat egy egyéni DNS tartalmaz, adja hozzá az Azure rekurzív feloldó IP-címet (például a 168.63.129.16) egy bejegyzés. További információkért lásd: [beállítása egy egyéni DNS](sql-database-managed-instance-custom-dns.md). Az egyéni DNS-kiszolgáló a gazdagépnevekhez ezekből a tartományokból és azok altartományok képesnek kell lennie: *microsoft.com*, *windows.net*, *windows.com*,  *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, és *microsoftonline-p.com*.

## <a name="next-steps"></a>További lépések

- Áttekintéséhez lásd: [SQL Database által nyújtott adatbiztonság speciális](sql-database-managed-instance.md).
- Ismerje meg, hogyan [egy új Azure virtuális hálózat beállítása](sql-database-managed-instance-create-vnet-subnet.md) vagy egy [meglévő Azure virtuális hálózat](sql-database-managed-instance-configure-vnet-subnet.md) ahol a felügyelt példány is üzemeltethető.
- [Az alhálózat méretének kiszámítása](sql-database-managed-instance-determine-size-vnet-subnet.md) kívánja telepíteni a felügyelt példányok.
- Ismerje meg, hogyan hozhat létre egy felügyelt példányt:
  - Az a [az Azure portal](sql-database-managed-instance-get-started.md).
  - Használatával [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/).
  - Használatával [Azure Resource Manager-sablon](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Használatával [egy Azure Resource Manager-sablon (használatával a jumpboxba, vagyis az ssms-ben szereplő)](https://portal.azure.com/).
