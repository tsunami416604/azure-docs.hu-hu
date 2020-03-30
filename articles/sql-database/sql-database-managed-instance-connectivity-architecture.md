---
title: Felügyelt példány kapcsolódási architektúrája
description: Ismerje meg az Azure SQL Database felügyelt példánykommunikációs és kapcsolódási architektúráját, valamint azt, hogy az összetevők hogyan irányítják a forgalmat a felügyelt példányhoz.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: f30ccd498b79c36c8892ae38a3e26d169249621a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481099"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Felügyelt példány csatlakoztatási architektúrája az Azure SQL Database-ben

Ez a cikk ismerteti a kommunikációt egy Azure SQL Database által felügyelt példányban. Azt is ismerteti, kapcsolatarchitektúra és hogyan az összetevők közvetlen forgalmat a felügyelt példány.  

Az SQL Database felügyelt példány a felügyelt példányok számára kijelölt alhálózaton belül helyezkedik el. Ez a központi telepítés a következőket biztosítja:

- Biztonságos privát IP-cím.
- A helyszíni hálózat egy felügyelt példányhoz való csatlakoztatásának képessége.
- Felügyelt példány csatlakoztatása csatolt kiszolgálóhoz vagy egy másik helyszíni adattárhoz.
- Felügyelt példány azure-erőforrásokhoz való csatlakoztatásának lehetősége.

## <a name="communication-overview"></a>Kommunikáció – áttekintés

Az alábbi ábrán olyan entitások láthatók, amelyek egy felügyelt példányhoz csatlakoznak. Azt is mutatja, hogy a felügyelt példányokkal kommunikálnira szükséges erőforrásokat. A diagram alján található kommunikációs folyamat az ügyfélalkalmazásokat és a felügyelt példányhoz adatforrásként kapcsolódó eszközöket jelöli.  

![Entitások a kapcsolódási architektúrában](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

A felügyelt példány egy platform szolgáltatásként (PaaS) kínál. A Microsoft automatizált ügynököket (felügyeleti, üzembe helyezési és karbantartási) használ a szolgáltatás telemetriai adatfolyamok alapján történő kezeléséhez. Mivel a Microsoft felelős a felügyeletért, az ügyfelek nem férhetnek hozzá a felügyelt példány virtuális fürtgépeihez a Távoli asztali protokoll (RDP) protokollon keresztül.

A végfelhasználók vagy alkalmazások által indított egyes SQL Server-műveletekhez felügyelt példányokra lehet szükség a platformmal való együttműködéshez. Az egyik eset egy felügyelt példány-adatbázis létrehozása. Ez az erőforrás az Azure Portalon, a PowerShellen, az Azure CLI-n és a REST API-n keresztül érhető el.

A felügyelt példányok az Azure-szolgáltatásoktól, például az Azure Storage for backups- tól, az Azure Event Hubs for telemetriai adatoktól, az Azure Active Directory a hitelesítéshez, az Azure Key Vault az átlátszó adattitkosításhoz (TDE) és néhány Azure platformszolgáltatást nyújtó Azure-szolgáltatástól függenek. biztonsági és támogatási funkciók. A felügyelt példányok kapcsolatot létesítenek ezekkel a szolgáltatásokkal.

Minden kommunikáció titkosítva van, és tanúsítványokkal van aláírva. A kommunikáló felek megbízhatóságának ellenőrzéséhez a felügyelt példányok folyamatosan ellenőrzik ezeket a tanúsítványokat a visszavont tanúsítványok listáin keresztül. Ha a tanúsítványokat visszavonják, a felügyelt példány bezárja a kapcsolatokat az adatok védelme érdekében.

## <a name="high-level-connectivity-architecture"></a>Magas szintű csatlakoztatási architektúra

Magas szinten a felügyelt példány szolgáltatás-összetevők készlete. Ezek az összetevők az ügyfél virtuális hálózati alhálózatán belül futó elkülönített virtuális gépek dedikált készletén találhatók. Ezek a gépek virtuális fürtöt alkotnak.

Egy virtuális fürt több felügyelt példányt is üzemeltethet. Szükség esetén a fürt automatikusan kibontja vagy összehúzódik, amikor az ügyfél módosítja az alhálózatban lévő kiépített példányok számát.

Az ügyfélalkalmazások csatlakozhatnak a felügyelt példányokhoz, és lekérdezhetik és frissíthetik az adatbázisokat a virtuális hálózaton, a társviszony-létesített virtuális hálózaton vagy a VPN-en vagy Az Azure ExpressRoute-on keresztül csatlakoztatott hálózaton belül. Ennek a hálózatnak végpontot és privát IP-címet kell használnia.  

![Kapcsolódási architektúra diagram](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

A Microsoft felügyeleti és telepítési szolgáltatásai a virtuális hálózaton kívül futnak. A felügyelt példány és a Microsoft-szolgáltatások nyilvános IP-címekkel rendelkező végpontokon keresztül csatlakoznak. Amikor egy felügyelt példány kimenő kapcsolatot hoz létre, a hálózati címfordítás (NAT) fogadásakor a kapcsolat úgy néz ki, mintha erről a nyilvános IP-címről érkezne.

A felügyeleti forgalom az ügyfél virtuális hálózatán keresztül áramlik. Ez azt jelenti, hogy a virtuális hálózat infrastruktúrájának elemei károsíthatják a felügyeleti forgalmat azáltal, hogy a példány meghibásodik, és elérhetetlenné válnak.

> [!IMPORTANT]
> Az ügyfélélmény és a szolgáltatás elérhetőségének javítása érdekében a Microsoft hálózati leképezési szabályzatot alkalmaz az Azure virtuális hálózati infrastruktúra elemeire. A házirend befolyásolhatja a felügyelt példány működését. Ez a platformmechanizmus transzparens módon közli a felhasználókkal a hálózati követelményeket. A házirend fő célja a hálózati helytelen konfiguráció megelőzése és a normál felügyelt példányműveletek biztosítása. Felügyelt példány törlésekor a hálózati leképezési házirend is törlődik.

## <a name="virtual-cluster-connectivity-architecture"></a>Virtuális fürt kapcsolati architektúrája

Vessünk egy mélyebb encika a felügyelt példányok kapcsolatarchitektúráját. Az alábbi ábra a virtuális fürt fogalmi elrendezését mutatja be.

![A virtuális fürt kapcsolati architektúrája](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Az ügyfelek az űrlappal `<mi_name>.<dns_zone>.database.windows.net`rendelkező állomásnévvel csatlakoznak egy felügyelt példányhoz. Ez az állomásnév privát IP-címre oldódik fel, bár egy nyilvános tartománynév-rendszer (DNS) zónában van regisztrálva, és nyilvánosan feloldható. A `zone-id` automatikusan létrejön a fürt létrehozásakor. Ha egy újonnan létrehozott fürt másodlagos felügyelt példányt üzemeltet, megosztja a zónaazonosítóját az elsődleges fürttel. További információ: [Az automatikus feladatátvételi csoportok használata több adatbázis transzparens és összehangolt feladatátvételének engedélyezéséhez című témakörben található.](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets)

Ez a privát IP-cím a felügyelt példány belső terheléselosztójához tartozik. A terheléselosztó a felügyelt példány átjárójára irányítja a forgalmat. Mivel több felügyelt példány is futtatható ugyanazon a fürtön belül, az átjáró a felügyelt példány állomásnevét használja a forgalom átirányításához a megfelelő SQL motorszolgáltatáshoz.

A felügyeleti és telepítési szolgáltatások egy felügyelt példányhoz csatlakoznak egy külső terheléselosztóhoz leképező [felügyeleti végpont](#management-endpoint) használatával. A forgalom csak akkor lesz átirányítva a csomópontokhoz, ha az olyan portok előre meghatározott készletén érkezik, amelyeket csak a felügyelt példány felügyeleti összetevői használnak. A csomópontokon beépített tűzfal van beállítva, hogy csak a Microsoft IP-tartományokból származó forgalmat engedélyezze. A tanúsítványok kölcsönösen hitelesítik a felügyeleti összetevők és a felügyeleti sík közötti kommunikációt.

## <a name="management-endpoint"></a>Felügyeleti végpont

A Microsoft felügyeleti végpont használatával kezeli a felügyelt példányt. Ez a végpont a példány virtuális fürtjének belsejében található. A felügyeleti végpontot a hálózati szinten beépített tűzfal védi. Az alkalmazás szintjén, ez védi a kölcsönös tanúsítványok ellenőrzése. A végpont IP-címének megkereséséhez [olvassa el A felügyeleti végpont IP-címének meghatározása](sql-database-managed-instance-find-management-endpoint-ip-address.md)című témakört.

Amikor a kapcsolatok a felügyelt példányon belül indulnak el (csakúgy, mint a biztonsági mentések és a naplónaplók), a forgalom a felügyeleti végpont nyilvános IP-címéből indul. Korlátozhatja a hozzáférést a felügyelt példányból a közszolgáltatásokhoz, ha tűzfalszabályokat állít be, hogy csak a felügyelt példány IP-címét engedélyezze. További információt [a Felügyelt példány beépített tűzfalának ellenőrzése című témakörben](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)talál.

> [!NOTE]
> A felügyelt példány régióján belüli Azure-szolgáltatásokhoz vezető forgalom optimalizálva van, és ezért nem nated felügyelt példánykezelési végpont nyilvános IP-cím. Ezért, ha IP-alapú tűzfalszabályokat kell használnia, leggyakrabban a tároláshoz, a szolgáltatásnak a felügyelt példánytól eltérő régióban kell lennie.

## <a name="service-aided-subnet-configuration"></a>Szolgáltatásáltal támogatott alhálózat-konfiguráció

Az ügyfelek biztonságának és a kezelhetőségi követelményeknek a kezelése érdekében a felügyelt példány manuálisal a szolgáltatás által támogatott alhálózati konfigurációra vált.

A szolgáltatás által támogatott alhálózati konfiguráció felhasználó teljes mértékben ellenőrzése alatt áll az adatok (TDS) forgalom, míg a felügyelt példány felelősséget vállal a felügyeleti forgalom megszakítás nélküli áramlásának biztosításáért az SLA teljesítése érdekében.

A szolgáltatás által támogatott alhálózati konfiguráció a virtuális hálózati [alhálózat delegálási](../virtual-network/subnet-delegation-overview.md) szolgáltatásán felül épül fel, hogy automatikus hálózati konfigurációkezelést biztosítson, és lehetővé tegye a szolgáltatásvégpontokat. A szolgáltatásvégpontok segítségével konfigurálható a virtuális hálózati tűzfal szabályok tárfiókok, amelyek biztonsági mentések / naplónaplók tárolására.

### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények 

Felügyelt példány telepítése a virtuális hálózaton belüli dedikált alhálózatban. Az alhálózatnak rendelkeznie kell a következő jellemzőkkel:

- **Dedikált alhálózat:** A felügyelt példány alhálózata nem tartalmazhat más, hozzá társított felhőszolgáltatást, és nem lehet átjáró-alhálózat. Az alhálózat nem tartalmazhat egyetlen erőforrást, csak a felügyelt példányt, és később nem adhat hozzá más típusú erőforrásokat az alhálózatban.
- **Alhálózati delegálás:** A felügyelt példány alhálózatát delegálni `Microsoft.Sql/managedInstances` kell az erőforrás-szolgáltatónak.
- **Hálózati biztonsági csoport (NSG):** NSG-t kell társozni a felügyelt példány alhálózatához. Az NSG segítségével szabályozhatja a felügyelt példány adatvégpontjához való hozzáférést az 1433-as és az 11000-11999-es portok forgalmának szűrésével, ha a felügyelt példány átirányítási kapcsolatokra van konfigurálva. A szolgáltatás automatikusan biztosítja és megtartja a felügyeleti forgalom folyamatos áramlásához szükséges aktuális [szabályokat.](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)
- **Felhasználó által definiált útvonaltábla (UDR) táblázat:** Egy UDR-táblát kell társozni a felügyelt példány alhálózatához. Bejegyzéseket adhat hozzá az útvonaltáblához a helyszíni magán IP-címtartományokkal rendelkező forgalom irányított irányításához a virtuális hálózati átjárón vagy a virtuális hálózati berendezésen (NVA) keresztül. A szolgáltatás automatikusan kiépíti és megtartja a felügyeleti forgalom megszakítás nélküli áramlásához szükséges aktuális [bejegyzéseket.](#user-defined-routes-with-service-aided-subnet-configuration)
- **Elegendő IP-cím:** A felügyelt példány alhálózatának legalább 16 IP-címmel kell rendelkeznie. Az ajánlott minimum 32 IP-cím. További információ: [A felügyelt példányok alhálózatának méretének meghatározása.](sql-database-managed-instance-determine-size-vnet-subnet.md) Felügyelt példányokat telepíthet [a meglévő hálózatban,](sql-database-managed-instance-configure-vnet-subnet.md) miután konfigurálta, hogy megfeleljen [a felügyelt példányok hálózati követelményeinek.](#network-requirements) Ellenkező esetben hozzon létre egy [új hálózatot és alhálózatot.](sql-database-managed-instance-create-vnet-subnet.md)

> [!IMPORTANT]
> Felügyelt példány létrehozásakor a rendszer hálózati leképezési házirendet alkalmaz az alhálózaton, hogy megakadályozza a nem megfelelő hálózati beállítások módosítását. Az utolsó példány alhálózatból való eltávolítása után a hálózati leképezési házirend is törlődik.

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Kötelező bejövő biztonsági szabályok a szolgáltatás által támogatott alhálózati konfigurációval 

| Név       |Port                        |Protocol (Protokoll)|Forrás           |Cél|Műveletek|
|------------|----------------------------|--------|-----------------|-----------|------|
|felügyelet  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |MI-alhálózat  |Engedélyezés |
|            |9000, 9003                  |TCP     |CorpnetSaw között       |MI-alhálózat  |Engedélyezés |
|            |9000, 9003                  |TCP     |CorpnetPublic    |MI-alhálózat  |Engedélyezés |
|mi_subnet   |Bármelyik                         |Bármelyik     |MI-alhálózat        |MI-alhálózat  |Engedélyezés |
|health_probe|Bármelyik                         |Bármelyik     |AzureLoadBalancer|MI-alhálózat  |Engedélyezés |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Kötelező kimenő biztonsági szabályok a szolgáltatás által támogatott alhálózati konfigurációval 

| Név       |Port          |Protocol (Protokoll)|Forrás           |Cél|Műveletek|
|------------|--------------|--------|-----------------|-----------|------|
|felügyelet  |443, 12000    |TCP     |MI-alhálózat        |AzureCloud |Engedélyezés |
|mi_subnet   |Bármelyik           |Bármelyik     |MI-alhálózat        |MI-alhálózat  |Engedélyezés |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Felhasználó által definiált útvonalak szolgáltatásáltal támogatott alhálózati konfigurációval 

|Név|Címelőtag|Következő ugrás|
|----|--------------|-------|
|alhálózat-vnetlocal|MI-alhálózat|Virtuális hálózat|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|Internet|
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
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
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
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

\*Az MI SUBNET az alhálózat IP-címtartományára hivatkozik az x.x.x.x/y formátumban. Ezeket az információkat az Azure Portalon, az alhálózati tulajdonságokban találja.

Emellett bejegyzéseket adhat hozzá az útvonaltáblához a helyszíni magán IP-címtartományokkal rendelkező forgalom irányításához a virtuális hálózati átjárón vagy a virtuális hálózati berendezésen (NVA) keresztül.

Ha a virtuális hálózat egyéni DNS-t tartalmaz, az egyéni DNS-kiszolgálónak képesnek kell lennie a nyilvános DNS-rekordok feloldására. További funkciók, például az Azure AD-hitelesítés használata további teljes tartománynna további feloldása szükséges lehet. További információt az [Egyéni DNS beállítása](sql-database-managed-instance-custom-dns.md)című témakörben talál.

### <a name="networking-constraints"></a>Hálózati megkötések

**A TLS 1.2 a kimenő kapcsolatokon van kényszerítve:** 2020 januárjában a Microsoft kikényszerítette a TLS 1.2-t az összes Azure-szolgáltatás szolgáltatáson belüli forgalmára. Az Azure SQL Database felügyelt példánya esetén ez azt eredményezte, hogy a TLS 1.2 a replikációhoz és az SQL Serverhez kapcsolódó kiszolgálói kapcsolatokhoz használt kimenő kapcsolatokon volt kényszerítve. Ha az SQL Server 2016-os verzióit felügyelt példánysal használja, győződjön meg arról, hogy a [TLS 1.2-specifikus frissítések lettek](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) alkalmazva.

A felügyelt példány jelenleg nem támogatja a következő virtuális hálózati szolgáltatásokat:
- **Microsoft társviszony-létesítés**: A [Microsoft társviszony-létesítésének](../expressroute/expressroute-faqs.md#microsoft-peering) engedélyezése az expressz útvonal-áramkörökön közvetlenül vagy tranzitívmódon a virtuális hálózattal, ahol a felügyelt példány található, befolyásolja a felügyelt példány összetevői közötti forgalmat a virtuális hálózaton belül és a szolgáltatások on attól függ, hogy rendelkezésre állási problémákat okoz. A felügyelt példányok virtuális hálózatra telepítése, ha a Microsoft társviszony-létesítése már engedélyezve van, várhatóan sikertelenek lesznek.
- **Globális virtuális hálózati társviszony-létesítés**: Az Azure-régiók közötti [virtuális hálózati társviszony-létesítési](../virtual-network/virtual-network-peering-overview.md) kapcsolat nem működik a felügyelt példány esetében [a dokumentált terheléselosztó-korlátozások](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)miatt.
- **AzurePlatformDNS**: Az AzurePlatformDNS [szolgáltatáscímke](../virtual-network/service-tags-overview.md) használata a platform DNS-felbontásának blokkolására elérhetetlenné teszi a felügyelt példányt. Bár a felügyelt példány támogatja az ügyfél által definiált DNS-dns-t a motoron belül, a platform DNS-től függ a platform műveletek.

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>[Elavult] Hálózati követelmények szolgáltatásáltal támogatott alhálózati konfiguráció nélkül

Felügyelt példány telepítése a virtuális hálózaton belüli dedikált alhálózatban. Az alhálózatnak rendelkeznie kell a következő jellemzőkkel:

- **Dedikált alhálózat:** A felügyelt példány alhálózata nem tartalmazhat más, hozzá társított felhőszolgáltatást, és nem lehet átjáró-alhálózat. Az alhálózat nem tartalmazhat egyetlen erőforrást, csak a felügyelt példányt, és később nem adhat hozzá más típusú erőforrásokat az alhálózatban.
- **Hálózati biztonsági csoport (NSG):** A virtuális hálózathoz társított [NSG-nek](#mandatory-inbound-security-rules) minden más szabály előtt meg kell határoznia a bejövő és a kimenő [biztonsági szabályokat.](#mandatory-outbound-security-rules) Az NSG segítségével szabályozhatja a felügyelt példány adatvégpontjához való hozzáférést az 1433-as és az 11000-11999-es portok forgalmának szűrésével, ha a felügyelt példány átirányítási kapcsolatokra van konfigurálva.
- **Felhasználó által definiált útvonaltábla (UDR) táblázat:** A virtuális hálózathoz társított UDR-táblának meghatározott bejegyzéseket kell [tartalmaznia.](#user-defined-routes)
- **Nincs enek szolgáltatásvégpont:** A felügyelt példány alhálózatához egyetlen szolgáltatásvégpontot sem szabad társválasztani. Győződjön meg arról, hogy a szolgáltatásvégpontok beállítás le van tiltva a virtuális hálózat létrehozásakor.
- **Elegendő IP-cím:** A felügyelt példány alhálózatának legalább 16 IP-címmel kell rendelkeznie. Az ajánlott minimum 32 IP-cím. További információ: [A felügyelt példányok alhálózatának méretének meghatározása.](sql-database-managed-instance-determine-size-vnet-subnet.md) Felügyelt példányokat telepíthet [a meglévő hálózatban,](sql-database-managed-instance-configure-vnet-subnet.md) miután konfigurálta, hogy megfeleljen [a felügyelt példányok hálózati követelményeinek.](#network-requirements) Ellenkező esetben hozzon létre egy [új hálózatot és alhálózatot.](sql-database-managed-instance-create-vnet-subnet.md)

> [!IMPORTANT]
> Nem telepíthet új felügyelt példányt, ha a célalhálózat nem rendelkezik ezekkel a jellemzőkkel. Felügyelt példány létrehozásakor a rendszer hálózati leképezési házirendet alkalmaz az alhálózaton, hogy megakadályozza a nem megfelelő hálózati beállítások módosítását. Az utolsó példány alhálózatból való eltávolítása után a hálózati leképezési házirend is törlődik.

### <a name="mandatory-inbound-security-rules"></a>Kötelező bejövő biztonsági szabályok

| Név       |Port                        |Protocol (Protokoll)|Forrás           |Cél|Műveletek|
|------------|----------------------------|--------|-----------------|-----------|------|
|felügyelet  |9000, 9003, 1438, 1440, 1452|TCP     |Bármelyik              |MI-alhálózat  |Engedélyezés |
|mi_subnet   |Bármelyik                         |Bármelyik     |MI-alhálózat        |MI-alhálózat  |Engedélyezés |
|health_probe|Bármelyik                         |Bármelyik     |AzureLoadBalancer|MI-alhálózat  |Engedélyezés |

### <a name="mandatory-outbound-security-rules"></a>Kötelező kimenő biztonsági szabályok

| Név       |Port          |Protocol (Protokoll)|Forrás           |Cél|Műveletek|
|------------|--------------|--------|-----------------|-----------|------|
|felügyelet  |443, 12000    |TCP     |MI-alhálózat        |AzureCloud |Engedélyezés |
|mi_subnet   |Bármelyik           |Bármelyik     |MI-alhálózat        |MI-alhálózat  |Engedélyezés |

> [!IMPORTANT]
> Győződjön meg arról, hogy a 9000, 9003, 1438, 1440, 1452 és egy kimenő szabály csak egy bejövő szabály van a 443-as, 12000-es portokra. Felügyelt példány kiépítése az Azure Resource Manager-telepítések en keresztül sikertelen lesz, ha a bejövő és kimenő szabályok külön-külön vannak konfigurálva az egyes portok. Ha ezek a portok külön szabályokban vannak, a telepítés hibakóddal sikertelen lesz`VnetSubnetConflictWithIntendedPolicy`

\*Az MI SUBNET az alhálózat IP-címtartományára hivatkozik az x.x.x.x/y formátumban. Ezeket az információkat az Azure Portalon, az alhálózati tulajdonságokban találja.

> [!IMPORTANT]
> Bár a szükséges bejövő biztonsági szabályok lehetővé teszik a forgalmat _bármilyen_ forrásból a 9000, 9003, 1438, 1440 és 1452 porton, ezeket a portokat beépített tűzfal védi. További információt [a Felügyeleti végpont címének meghatározása](sql-database-managed-instance-find-management-endpoint-ip-address.md)című témakörben talál.

> [!NOTE]
> Ha tranzakciós replikációt használ egy felügyelt példányban, és bármely példányadatbázist használ közzétevőként vagy forgalmazóként, nyissa meg a 445-ös portot (TCP kimenő) az alhálózat biztonsági szabályai között. Ez a port lehetővé teszi a hozzáférést az Azure-fájlmegosztáshoz.

### <a name="user-defined-routes"></a>Felhasználó által definiált útvonalak

|Név|Címelőtag|Következő ugrás|
|----|--------------|-------|
|subnet_to_vnetlocal|MI-alhálózat|Virtuális hálózat|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|Internet|
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
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
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
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

## <a name="next-steps"></a>További lépések

- Az áttekintést az [SQL Database speciális adatbiztonság című témakörében](sql-database-managed-instance.md)találja.
- Ismerje meg, hogyan [állíthat be egy új Azure virtuális hálózatot](sql-database-managed-instance-create-vnet-subnet.md) vagy egy meglévő Azure virtuális [hálózatot,](sql-database-managed-instance-configure-vnet-subnet.md) ahol felügyelt példányokat telepíthet.
- [Számítsa ki annak az alhálózatnak a méretét,](sql-database-managed-instance-determine-size-vnet-subnet.md) ahol a felügyelt példányokat telepíteni szeretné.
- További információ a felügyelt példányok létrehozásáról:
  - Az [Azure Portalon.](sql-database-managed-instance-get-started.md)
  - A [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)használatával .
  - Egy [Azure Resource Manager sablon](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)használatával.
  - Egy [Azure Resource Manager sablon használatával (a JumpBox használatával, az SSMS-rel együtt)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/). 
