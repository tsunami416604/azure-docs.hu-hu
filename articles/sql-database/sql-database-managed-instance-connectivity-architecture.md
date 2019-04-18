---
title: Az Azure SQL Database felügyelt példány kapcsolati architektúra |} A Microsoft Docs
description: Tudnivalók Azure SQL Database felügyelt példány kommunikációs és kapcsolati architektúra, valamint, hogy hogyan az összetevők közvetlen forgalom a felügyelt példányhoz.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: 82b533f7293e00469a5b92b02e8d58967379a585
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59497066"
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

Felügyelt példány függ, például az Azure Storage biztonsági mentésekhez, telemetriai adatokat az Azure Event Hubs, Azure Active Directory-hitelesítéshez, az Azure Key Vault transzparens adattitkosítási (TDE) az Azure-szolgáltatások, valamint néhány biztosító Azure platformszolgáltatások kereslettől és a biztonsági funkciók. A felügyelt példányok lehetővé teszi, hogy ezek a szolgáltatások kapcsolatokat.

Minden kommunikáció titkosított, és a regisztrált tanúsítványok használatával. Ellenőrizze a megbízhatósága kommunikáció feleket, felügyelt példányok folyamatosan ellenőrizze-e tanúsítvány-visszavonási listák révén ezek a tanúsítványok. Ha a tanúsítványok visszavonódnak, a következő felügyelt példányt bezárja a kapcsolatokat, az adatok védelme érdekében.

## <a name="high-level-connectivity-architecture"></a>Magas szintű kapcsolati architektúra

Magas szinten a felügyelt példány egy szolgáltatás-összetevők. Ezek az összetevők egy dedikált csoportján belül az ügyfél virtuális hálózatának alhálózatához futtató elkülönített virtuális gépeket üzemelnek. Ezek a gépek virtuális fürtöt alkotnak.

Virtuális fürt több felügyelt példányt is üzemeltethet. Szükség esetén a fürt automatikusan bontja ki, vagy csökken, amikor az ügyfél megváltoztatja az alhálózat üzembe helyezett példányok száma.

Ügyfélalkalmazások csatlakozhat a felügyelt példányok és is lekérdezése és a virtuális hálózat társviszonyban lévő virtuális hálózaton belüli adatbázisok frissítése vagy VPN- vagy Azure ExpressRoute által csatlakoztatott hálózat. Ezt a hálózatot a végpont és a egy magánhálózati IP-címet kell használnia.  

![kapcsolati architektúra ábrája](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

A Microsoft management és a központi telepítési szolgáltatások futnak a virtuális hálózaton kívülről. Felügyelt példány és a Microsoft-szolgáltatások a végpontok, amelyeken a nyilvános IP-címek keresztül csatlakozhatnak. Ha egy felügyelt példányt hoz létre egy kimenő kapcsolatot fogadó hálózati címfordítás (NAT) révén, mint a kapcsolat tekintse meg a nyilvános IP-cím származik.

Felügyeleti forgalom az ügyfél virtuális hálózatán keresztül. Ez azt jelenti, hogy a virtuális hálózati infrastruktúra elemeinek károsíthatják felügyeleti forgalom azáltal, hogy a példány sikertelen lesz, és már nem érhető el.

> [!IMPORTANT]
> Felhasználói élményét és szolgáltatás rendelkezésre állása, a Microsoft Azure virtuális hálózati infrastruktúra-elemeket a szándék hálózati házirend vonatkozik. A szabályzat hatással lehet a felügyelt példány működését. Ez a platform mechanizmus a hálózati követelményei a felhasználók számára transzparens módon kommunikál. A Csoportházirend fő célja, hogy a hibás hálózati beállítás és, hogy a normál felügyelt példány. Ha töröl egy felügyelt példányt, a hálózati szándék házirend is törlődik.

## <a name="virtual-cluster-connectivity-architecture"></a>Virtuális fürt kapcsolati architektúra

Vegyük részletesebben megismerni, felügyelt példányok kapcsolati architektúra. Az alábbi ábrán látható a virtuális fürt fogalmi elrendezését.

![A virtuális fürt kapcsolati architektúra](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Az ügyfelek egy állomásnevet, amely rendelkezik az űrlap használatával csatlakozhat a felügyelt példány `<mi_name>.<dns_zone>.database.windows.net`. Az állomásnév feloldása egy magánhálózati IP-cím, bár azt a nyilvános tartomány neve tartománynévrendszer (DNS) zónában lévő regisztrálva van, és nyilvánosan feloldható. A `zone-id` automatikusan létrejön, amikor a fürt létrehozásához. Ha egy újonnan létrehozott fürt másodlagos felügyelt példány üzemelteti, a zóna Azonosítójával megosztja az elsődleges fürt. További információkért lásd: [automatikus feladatátvételi csoportok használatával engedélyezhető az átlátható és koordinált több adatbázis feladatátvételét](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

A magánhálózati IP-cím tartozik, a felügyelt példány belső terheléselosztót. A load balancer irányítja a forgalmat a felügyelt példány átjáróhoz. Több felügyelt példány futtathatja ugyanazon a fürtön belül, mert az átjáró forgalom átirányítása a megfelelő SQL-adatbázismotor szolgáltatás használja a felügyelt példány állomás neve.

Felügyeleti és a központi telepítési szolgáltatások használatával csatlakozhat a felügyelt példány egy [felügyeleti végpont](#management-endpoint) maps külső terheléselosztó. Forgalmat irányítja a rendszer a csomópontok csak akkor, ha azt csak a felügyelt példány felügyeleti összetevők által használt portok előre meghatározott érkezik. A csomópontok beépített tűzfal van beállítva, hogy csak a Microsoft IP-címtartományok érkező forgalmat. Tanúsítványok kölcsönös hitelesítésére az összetevőket és a felügyeleti sík közötti minden kommunikáció.

## <a name="management-endpoint"></a>Felügyeleti végpont

A Microsoft egy felügyeleti végpont használatával kezeli a következő felügyelt példányt. Ez a végpont a példány virtuális fürt belül van. A felügyeleti végponthoz a hálózati szinten beépített tűzfal védi. Az alkalmazás szintjén a védi kölcsönös tanúsítvány-ellenőrzés. A végpont IP-cím megkereséséhez lásd: [határozza meg a felügyeleti végpont IP-cím](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Ha a kapcsolatok indítsa el a következő felügyelt példányt belül (a biztonsági mentések és a vizsgálati naplók), forgalom jelenik meg, a felügyeleti végponthoz nyilvános IP-címről indított el. Hozzáférés korlátozható nyilvános szolgáltatásokhoz a felügyelt példány csak a felügyelt példány IP-cím tűzfalszabályokban beállításával. További információkért lásd: [beépített tűzfal a felügyelt példány ellenőrzése](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Traffice, amely az Azure-szolgáltatások, amelyek a felügyelt példány-régión belül van optimalizálva, és ehhez az okból nem gelt manged példány felügyeleti végpont nyilvános IP-címre. Éppen ezért, ha szeretné használni az IP-alapú tűzfalszabályokat, leggyakrabban a storage, service kell lennie a felügyelt példány egy másik régióban.

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

A virtuális hálózaton belüli kijelölt alhálózatot a felügyelt példány üzembe helyezése. Az alhálózat a következő jellemzőkkel kell rendelkeznie:

- **Kijelölt alhálózatot:** A felügyelt példány alhálózatára bármely más felhőalapú szolgáltatás, amely rendelkezik társított nem tartalmazhat, és nem lehet egy átjáró-alhálózatot. Az alhálózat nem tartalmazhat bármilyen erőforrás, de a következő felügyelt példányt, és később az alhálózat nem adható hozzá erőforrásokat.
- **Hálózati biztonsági csoport (NSG):** A virtuális hálózathoz társított NSG-t meg kell határoznia [bejövő biztonsági szabályok](#mandatory-inbound-security-rules) és [kimenő biztonsági szabályok](#mandatory-outbound-security-rules) előtt egyéb szabályok. Az NSG-KET segítségével férhet hozzá a felügyelt példány adatok végpont 1433-as porton a kimenő forgalmának szűrésével, és portok 11000-11999, ha a felügyelt példány van konfigurálva átirányítja a kapcsolatokat.
- **Felhasználó által megadott útvonal (UDR) tábla:** Adott tartalmaznia kell a virtuális hálózathoz társított UDR tábla [bejegyzések](#user-defined-routes).
- **Nincsenek Szolgáltatásvégpontok:** Nem service-végpont a felügyelt példány alhálózatára társítva kell lennie. Győződjön meg arról, hogy a szolgáltatás-végpontok lehetőség le van tiltva a virtuális hálózat létrehozásakor.
- **Elegendő IP-címek:** A felügyelt példány alhálózatára rendelkeznie kell legalább 16 IP-címet. Az ajánlott minimális érték 32 IP-címeket. További információkért lásd: [határozza meg a felügyelt példányok alhálózat méretét](sql-database-managed-instance-determine-size-vnet-subnet.md). Telepíthet a felügyelt példányok [a meglévő hálózat](sql-database-managed-instance-configure-vnet-subnet.md) után konfigurálja, hogy eleget [a hálózati követelmények a felügyelt példányokhoz](#network-requirements). Máskülönben hozzon létre egy [új hálózatot és alhálózatot](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Ha a cél alhálózat nem rendelkezik a következő jellemzőkkel nem telepíthet egy új felügyelt példányát. Amikor létrehoz egy felügyelt példány, egy hálózati szándék szabályzat érvényes az alhálózat hálózati beállítása nem megfelelő módosításának megakadályozása. Az utolsó példány az alhálózatról eltávolítása után a hálózati szándék házirend is törlődik.

### <a name="mandatory-inbound-security-rules"></a>Kötelező bejövő biztonsági szabályok

| Name (Név)       |Port                        |Protokoll|Forrás           |Cél|Műveletek|
|------------|----------------------------|--------|-----------------|-----------|------|
|felügyelet  |9000, 9003, 1438, 1440, 1452|TCP     |Bármelyik              |MI ALHÁLÓZAT  |Engedélyezés |
|mi_subnet   |Bármelyik                         |Bármelyik     |MI ALHÁLÓZAT        |MI ALHÁLÓZAT  |Engedélyezés |
|health_probe|Bármelyik                         |Bármelyik     |AzureLoadBalancer|MI ALHÁLÓZAT  |Engedélyezés |

### <a name="mandatory-outbound-security-rules"></a>Kötelező kimenő biztonsági szabályok

| Name (Név)       |Port          |Protokoll|Forrás           |Cél|Műveletek|
|------------|--------------|--------|-----------------|-----------|------|
|felügyelet  |80, 443, 12000|TCP     |MI ALHÁLÓZAT        |AzureCloud |Engedélyezés |
|mi_subnet   |Bármelyik           |Bármelyik     |MI ALHÁLÓZAT        |MI ALHÁLÓZAT  |Engedélyezés |

> [!IMPORTANT]
> Győződjön meg arról, 9003, csak egy bejövő szabály a portok 9000, nincs 1438, 1440, 1452 és a egy kimenő szabály, a 80-as, 443-as, 12000 portokat. A felügyelt példány kiépítése az Azure Resource Manager üzembe helyezések meghiúsulnak, ha a bemeneti és kimeneti szabályok a az egyes portok külön-külön vannak konfigurálva. Ha ezek a portok külön szabályokat, a telepítés meghiúsul, hibakód: `VnetSubnetConflictWithIntendedPolicy`

\* MI ALHÁLÓZAT az IP-címtartományt az űrlap 10.x.x.x/y alhálózat hivatkozik. Ezt az információt találja az Azure Portalon, az alhálózat tulajdonságait.

> [!IMPORTANT]
> Bár a szükséges bejövő biztonsági szabályok érkező adatforgalom engedélyezéséhez _bármely_ portokon forrás 9000, 9003, 1438, 1440 és 1452, ezeket a portokat a beépített tűzfal védi. További információkért lásd: [határozza meg a felügyeleti végpont címe](sql-database-managed-instance-find-management-endpoint-ip-address.md).
> [!NOTE]
> Tranzakciós replikáció használata a felügyelt példány, és minden olyan példány adatbázis közzétevő vagy forgalmazó használ, nyissa meg az alhálózat biztonsági szabályok a 445-ös (TCP, kimenő) porton. Ez a port lehetővé teszi a hozzáférést az Azure-fájlmegosztást.

### <a name="user-defined-routes"></a>Felhasználó által megadott útvonalak

|Name (Név)|Címelőtag|Következő ugrás|
|----|--------------|-------|
|subnet_to_vnetlocal|MI ALHÁLÓZAT|Virtuális hálózat|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-96-13-nexthop-internet|13.96.0.0/13|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-8-nexthop-internet|20.0.0.0/8|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-8-nexthop-internet|51.0.0.0/8|Internet|
|mi-52-8-nexthop-internet|52.0.0.0/8|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-22-nexthop-internet|103.25.156.0/22|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-253-16-nexthop-internet|131.253.0.0/16|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-100-102-24-nexthop-internet|192.100.102.0/24|Internet|
|mi-192-100-103-24-nexthop-internet|192.100.103.0/24|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
||||

Emellett bejegyzést adhat hozzá az útvonaltáblát irányíthatja a forgalmat, amely rendelkezik a helyszíni privát IP-címtartományok a célhelyet a virtuális hálózati átjáró vagy a virtuális hálózati berendezésre (NVA) keresztül.

Ha a virtuális hálózat egy egyéni DNS tartalmaz, az egyéni DNS-kiszolgáló kell tudni oldania az állomásneveket \*. core.windows.net zóna. További funkciók használatával, mint az Azure AD-hitelesítés lehet szükség további teljes tartománynevek feloldását. További információkért lásd: [beállítása egy egyéni DNS](sql-database-managed-instance-custom-dns.md).

## <a name="next-steps"></a>További lépések

- Áttekintéséhez lásd: [SQL Database által nyújtott adatbiztonság speciális](sql-database-managed-instance.md).
- Ismerje meg, hogyan [egy új Azure virtuális hálózat beállítása](sql-database-managed-instance-create-vnet-subnet.md) vagy egy [meglévő Azure virtuális hálózat](sql-database-managed-instance-configure-vnet-subnet.md) ahol a felügyelt példány is üzemeltethető.
- [Az alhálózat méretének kiszámítása](sql-database-managed-instance-determine-size-vnet-subnet.md) kívánja telepíteni a felügyelt példányok.
- Ismerje meg, hogyan hozhat létre egy felügyelt példányt:
  - Az a [az Azure portal](sql-database-managed-instance-get-started.md).
  - Használatával [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Használatával [Azure Resource Manager-sablon](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Használatával [egy Azure Resource Manager-sablon (használatával a jumpboxba, vagyis az ssms-ben szereplő)](https://portal.azure.com/). 
