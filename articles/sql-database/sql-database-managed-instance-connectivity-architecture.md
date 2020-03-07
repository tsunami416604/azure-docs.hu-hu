---
title: Felügyelt példány kapcsolati architektúrája
description: Ismerkedjen meg Azure SQL Database felügyelt példányok kommunikációs és kapcsolati architektúrával, valamint arról, hogy az összetevők hogyan irányítsák át a felügyelt példányra.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 04/16/2019
ms.openlocfilehash: ec1430e7dd79378473cce9dbb77bedecd14600c8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357743"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Felügyelt példány kapcsolati architektúrája Azure SQL Database

Ez a cikk egy Azure SQL Database felügyelt példányon folytatott kommunikációt ismerteti. Emellett leírja a kapcsolati architektúrát, valamint azt is, hogy az összetevők hogyan irányítsák át a felügyelt példányra.  

Az SQL Database felügyelt példány az Azure-beli virtuális hálózaton és a felügyelt példányokhoz dedikált alhálózaton belül helyezkedik el. Ez az üzembe helyezés a következőket biztosítja:

- Biztonságos magánhálózati IP-cím.
- A helyszíni hálózat felügyelt példányhoz való kapcsolódásának lehetősége.
- Felügyelt példányok csatolása egy csatolt kiszolgálóhoz vagy egy másik helyszíni adattárhoz.
- Felügyelt példányok Azure-erőforrásokhoz való összekapcsolásának lehetősége.

## <a name="communication-overview"></a>Kommunikáció áttekintése

A következő ábrán a felügyelt példányokhoz csatlakozó entitások láthatók. A felügyelt példánnyal való kommunikációhoz szükséges erőforrásokat is megjeleníti. A diagram alján található kommunikációs folyamat azokat az ügyfeleket és eszközöket jelöli, amelyek adatforrásként csatlakoznak a felügyelt példányhoz.  

![A kapcsolati architektúrában lévő entitások](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

A felügyelt példányok szolgáltatásként nyújtott platformként nyújtott szolgáltatások. A Microsoft automatizált ügynököket használ (felügyelet, üzembe helyezés és karbantartás) a szolgáltatás telemetria-adatfolyamok alapján történő kezeléséhez. Mivel a Microsoft felelős a felügyeletért, az ügyfelek nem férhetnek hozzá a felügyelt példányok virtuális fürtjéhez RDP protokoll (RDP) használatával.

Előfordulhat, hogy a végfelhasználók vagy az alkalmazások által indított SQL Server műveletek esetében a platformmal való interakcióhoz felügyelt példányokra lehet szükség. Az egyik eset a felügyelt példányok adatbázisának létrehozása. Ezt az erőforrást a Azure Portal, a PowerShell, az Azure CLI és a REST API teszi elérhetővé.

A felügyelt példányok olyan Azure-szolgáltatásoktól függenek, mint az Azure Storage biztonsági mentések, Azure-Event Hubs telemetria, Azure Active Directory a hitelesítéshez, a Azure Key Vault for transzparens adattitkosítás (TDE) és néhány Azure platform-szolgáltatás, amely biztosítja biztonsági és támogatási funkciók. A felügyelt példányok kapcsolatot létesít ezekkel a szolgáltatásokkal.

Minden kommunikáció titkosítva van, és a tanúsítványokkal van aláírva. A kommunikáló felek megbízhatóságának ellenőrzéséhez a felügyelt példányok folyamatosan ellenőrzik ezeket a tanúsítványokat a visszavont tanúsítványok listája alapján. Ha visszavonják a tanúsítványokat, a felügyelt példány bezárja a kapcsolatokat az adatvédelemhez.

## <a name="high-level-connectivity-architecture"></a>Magas szintű kapcsolati architektúra

A felügyelt példányok magas szinten a szolgáltatás-összetevők készletét jelentik. Ezek az összetevők az ügyfél virtuális hálózati alhálózatán belül futó elkülönített virtuális gépek dedikált készletén futnak. Ezek a gépek virtuális fürtöt alkotnak.

A virtuális fürtök több felügyelt példányt is tárolhatnak. Ha szükséges, a fürt automatikusan kibontja a vagy a szerződést, amikor az ügyfél megváltoztatja az alhálózat kiépített példányainak számát.

Az ügyfélalkalmazások csatlakozhatnak a felügyelt példányokhoz, és lekérhetik és frissíthetik az adatbázisokat a virtuális hálózaton, a kihelyezett virtuális hálózaton vagy a VPN-vagy az Azure-ExpressRoute csatlakoztatott hálózaton belül. A hálózatnak végpontot és magánhálózati IP-címet kell használnia.  

![Kapcsolati architektúra diagramja](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

A Microsoft felügyeleti és központi telepítési szolgáltatásai a virtuális hálózaton kívül futnak. Felügyelt példány és Microsoft-szolgáltatások csatlakoznak a nyilvános IP-címekkel rendelkező végpontokon. Ha egy felügyelt példány kimenő kapcsolatokat hoz létre, a hálózati címfordítás (NAT) fogadása során a rendszer a kapcsolódást a nyilvános IP-címről érkezőnek tekinti.

A felügyeleti forgalom az ügyfél virtuális hálózatán keresztül folyik. Ez azt jelenti, hogy a virtuális hálózat infrastruktúrájának elemei a példány meghibásodása és elérhetetlenné válása miatt kárt okozhatnak a felügyeleti forgalomban.

> [!IMPORTANT]
> A Microsoft a felhasználói élmény és a szolgáltatás rendelkezésre állásának javítása érdekében az Azure Virtual Network infrastruktúra elemeire vonatkozó hálózati leképezési szabályzatot alkalmaz. A házirend hatással lehet a felügyelt példány működésére. Ez a platform-mechanizmus transzparens módon kommunikál a felhasználók hálózati követelményeivel. A szabályzat fő célja, hogy megakadályozza a hálózati helytelen konfigurációt, és gondoskodjon a felügyelt példányok szokásos műveleteiről. Felügyelt példány törlésekor a hálózati leképezési házirend is törlődik.

## <a name="virtual-cluster-connectivity-architecture"></a>Virtuális fürt kapcsolati architektúrája

Ismerkedjen meg a felügyelt példányok kapcsolati architektúrájának mélyebb bevezetésével. Az alábbi ábrán a virtuális fürt fogalmi elrendezése látható.

![A virtuális fürt kapcsolati architektúrája](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Az ügyfelek egy felügyelt példányhoz csatlakoznak olyan állomásnév használatával, amely `<mi_name>.<dns_zone>.database.windows.net`űrlapot tartalmaz. Ez az állomásnév a magánhálózati IP-címekre lesz feloldva, bár egy nyilvános tartománynévrendszer-(DNS-) zónában van regisztrálva, és nyilvánosan feloldható. A rendszer automatikusan létrehozza a `zone-id` a fürt létrehozásakor. Ha egy újonnan létrehozott fürt egy másodlagos felügyelt példányt futtat, akkor a zóna AZONOSÍTÓját megosztja az elsődleges fürttel. További információ: [automatikus feladatátvételi csoportok használata több adatbázis átlátható és koordinált feladatátvételének engedélyezéséhez](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Ez a magánhálózati IP-cím a felügyelt példány belső terheléselosztó része. A terheléselosztó átirányítja a forgalmat a felügyelt példány átjárójának. Mivel több felügyelt példány futhat ugyanazon a fürtön belül, az átjáró a felügyelt példány állomásneve használatával irányítja át a forgalmat a megfelelő SQL Engine szolgáltatásba.

A felügyeleti és központi telepítési szolgáltatások egy felügyelt példányhoz csatlakoznak egy olyan [felügyeleti végpont](#management-endpoint) használatával, amely egy külső terheléselosztó számára van leképezve. A rendszer csak akkor irányítja a forgalmat a csomópontokhoz, ha egy előre meghatározott porton érkezik, amely csak a felügyelt példány felügyeleti összetevői által használt. A csomópontokon a beépített tűzfal úgy van beállítva, hogy csak a Microsoft IP-címtartományok forgalmát engedélyezze. A tanúsítványok kölcsönösen hitelesítik a felügyeleti összetevők és a felügyeleti sík közötti összes kommunikációt.

## <a name="management-endpoint"></a>Felügyeleti végpont

A Microsoft felügyeleti végpont használatával kezeli a felügyelt példányt. Ez a végpont a példány virtuális fürtjén belül van. A felügyeleti végpontot egy beépített tűzfal védi a hálózati szinten. Az alkalmazás szintjén a kölcsönös tanúsítvány-ellenőrzés védi. A végpont IP-címének megkereséséhez tekintse meg [a felügyeleti végpont IP-címének megállapítása](sql-database-managed-instance-find-management-endpoint-ip-address.md)című témakört.

Ha a kapcsolatok a felügyelt példányon belül kezdődnek (mint a biztonsági mentések és a naplók), a forgalom úgy tűnik, hogy a felügyeleti végpont nyilvános IP-címéről indul el. A felügyelt példányok nyilvános szolgáltatásaihoz való hozzáférést úgy is korlátozhatja, hogy a tűzfalszabályok beállításával csak a felügyelt példány IP-címét engedélyezze. További információ: [a felügyelt példány beépített tűzfalának ellenőrzése](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> A felügyelt példány régiójában található Azure-szolgáltatásokra áthaladó forgalom optimalizált, ezért a felügyelt példányok felügyeleti végpontjának nyilvános IP-címére nem. Ezért ha IP-alapú tűzfalszabályok használatát kell használnia, általában a tároláshoz, a szolgáltatásnak a felügyelt példánytól eltérő régióban kell lennie.

## <a name="service-aided-subnet-configuration"></a>Szolgáltatással segített alhálózat konfigurációja

Az ügyfél biztonsági és kezelhetőségi követelményeinek kezelése érdekében a felügyelt példányok a manuálisról a szolgáltatással támogatott alhálózat-konfigurációra váltanak.

A szolgáltatással segített alhálózat-konfigurációs felhasználó teljes körű hozzáférés-vezérlési (TDS) forgalomban van, míg a felügyelt példányok feladata a felügyeleti forgalom zavartalan áramlásának biztosítása az SLA teljesítése érdekében.

### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények 

Felügyelt példány üzembe helyezése egy dedikált alhálózaton a virtuális hálózaton belül. Az alhálózatnak a következő jellemzőkkel kell rendelkeznie:

- **Dedikált alhálózat:** A felügyelt példány alhálózata nem tartalmazhat olyan más felhőalapú szolgáltatást, amely hozzá van rendelve, és nem lehet átjáró-alhálózat. Az alhálózat nem tartalmazhat erőforrást, hanem a felügyelt példányt, és később nem adhat hozzá más típusú erőforrásokat az alhálózatban.
- **Alhálózat delegálása:** A felügyelt példány alhálózatát delegálni kell `Microsoft.Sql/managedInstances` erőforrás-szolgáltatóhoz.
- **Hálózati biztonsági csoport (NSG):** Egy NSG kell társítani a felügyelt példány alhálózatához. Az NSG segítségével szabályozhatja a felügyelt példány adatvégpontjának hozzáférését úgy, hogy az 1433-es és a 11000-11999-es porton lévő forgalmat szűri, ha felügyelt példány van konfigurálva az átirányítási kapcsolatokhoz. A szolgáltatás automatikusan hozzáadja a felügyeleti forgalom megszakítás nélküli áramlását engedélyező [szabályokat](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) .
- **Felhasználó által megadott útvonal (UDR) tábla:** UDR táblát kell társítani a felügyelt példány alhálózatához. Hozzáadhat bejegyzéseket az útválasztási táblázathoz, hogy a virtuális hálózati átjáró vagy a virtuális hálózati berendezés (NVA) használatával átirányítsa a helyszíni magánhálózati IP-tartományokkal rendelkező forgalmat. A szolgáltatás automatikusan hozzáadja a felügyeleti forgalom zavartalan áramlását lehetővé tevő [bejegyzéseket](#user-defined-routes-with-service-aided-subnet-configuration) .
- **Szolgáltatási végpontok:** A szolgáltatás-végpontok virtuális hálózati szabályok konfigurálására használhatók a biztonsági mentéseket és naplókat megőrző Storage-fiókok esetében.
- **Elegendő IP-cím:** A felügyelt példány alhálózatának legalább 16 IP-címmel kell rendelkeznie. Az ajánlott minimális érték 32 IP-cím. További információ: a [felügyelt példányok alhálózat méretének meghatározása](sql-database-managed-instance-determine-size-vnet-subnet.md). A felügyelt példányokat [a meglévő hálózatban](sql-database-managed-instance-configure-vnet-subnet.md) is telepítheti, miután konfigurálta a [felügyelt példányok hálózati követelményeinek](#network-requirements)kielégítéséhez. Ellenkező esetben hozzon létre egy [új hálózatot és alhálózatot](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Felügyelt példány létrehozásakor a rendszer egy hálózati leképezési házirendet alkalmaz az alhálózaton, hogy megakadályozza a nem megfelelő módosításokat a hálózatkezelés beállításában. Miután az utolsó példányt eltávolította az alhálózatból, a rendszer eltávolítja a hálózati leképezési házirendet is.

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Kötelező bejövő biztonsági szabályok a szolgáltatással segített alhálózat konfigurációjával 

| Name (Név)       |Port                        |Protokoll|Forrás           |Cél|Műveletek|
|------------|----------------------------|--------|-----------------|-----------|------|
|felügyelet  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |MI ALHÁLÓZAT  |Engedélyezés |
|            |9000, 9003                  |TCP     |CorpnetSaw       |MI ALHÁLÓZAT  |Engedélyezés |
|            |9000, 9003                  |TCP     |65.55.188.0/24, 167.220.0.0/16, 131.107.0.0/16, 94.245.87.0/24|MI ALHÁLÓZAT  |Engedélyezés |
|mi_subnet   |Bármelyik                         |Bármelyik     |MI ALHÁLÓZAT        |MI ALHÁLÓZAT  |Engedélyezés |
|health_probe|Bármelyik                         |Bármelyik     |AzureLoadBalancer|MI ALHÁLÓZAT  |Engedélyezés |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Kötelező kimenő biztonsági szabályok a szolgáltatással segített alhálózat konfigurációjával 

| Name (Név)       |Port          |Protokoll|Forrás           |Cél|Műveletek|
|------------|--------------|--------|-----------------|-----------|------|
|felügyelet  |443, 12000    |TCP     |MI ALHÁLÓZAT        |AzureCloud |Engedélyezés |
|mi_subnet   |Bármelyik           |Bármelyik     |MI ALHÁLÓZAT        |MI ALHÁLÓZAT  |Engedélyezés |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>A felhasználó által megadott útvonalak a szolgáltatással segített alhálózat konfigurációjával 

|Name (Név)|Címelőtag|Következő ugrás|
|----|--------------|-------|
|alhálózat – vnetlocal|MI ALHÁLÓZAT|Virtuális hálózat|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|Mi-20-34-15-nexthop-Internet|20.34.0.0/15|Internet|
|Mi-20-36-14-nexthop-Internet|20.36.0.0/14|Internet|
|Mi-20-40-13-nexthop-Internet|20.40.0.0/13|Internet|
|Mi-20-128-16-nexthop-Internet|20.128.0.0/16|Internet|
|Mi-20-140-15-nexthop-Internet|20.140.0.0/15|Internet|
|Mi-20-144-14-nexthop-Internet|20.144.0.0/14|Internet|
|Mi-20-150-15-nexthop-Internet|20.150.0.0/15|Internet|
|Mi-20-160-12-nexthop-Internet|20.160.0.0/12|Internet|
|Mi-20-176-14-nexthop-Internet|20.176.0.0/14|Internet|
|Mi-20-180-14-nexthop-Internet|20.180.0.0/14|Internet|
|Mi-20-184-13-nexthop-Internet|20.184.0.0/13|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|Mi-51-4-15-nexthop-Internet|51.4.0.0/15|Internet|
|Mi-51-8-16-nexthop-Internet|51.8.0.0/16|Internet|
|Mi-51-10-15-nexthop-Internet|51.10.0.0/15|Internet|
|Mi-51-12-15-nexthop-Internet|51.12.0.0/15|Internet|
|Mi-51-18-16-nexthop-Internet|51.18.0.0/16|Internet|
|Mi-51-51-16-nexthop-Internet|51.51.0.0/16|Internet|
|Mi-51-53-16-nexthop-Internet|51.53.0.0/16|Internet|
|Mi-51-103-16-nexthop-Internet|51.103.0.0/16|Internet|
|Mi-51-104-15-nexthop-Internet|51.104.0.0/15|Internet|
|Mi-51-107-16-nexthop-Internet|51.107.0.0/16|Internet|
|Mi-51-116-16-nexthop-Internet|51.116.0.0/16|Internet|
|Mi-51-120-16-nexthop-Internet|51.120.0.0/16|Internet|
|Mi-51-124-16-nexthop-Internet|51.124.0.0/16|Internet|
|Mi-51-132-16-nexthop-Internet|51.132.0.0/16|Internet|
|Mi-51-136-15-nexthop-Internet|51.136.0.0/15|Internet|
|Mi-51-138-16-nexthop-Internet|51.138.0.0/16|Internet|
|Mi-51-140-14-nexthop-Internet|51.140.0.0/14|Internet|
|Mi-51-144-15-nexthop-Internet|51.144.0.0/15|Internet|
|Mi-52-96-12-nexthop-Internet|52.96.0.0/12|Internet|
|Mi-52-112-14-nexthop-Internet|52.112.0.0/14|Internet|
|Mi-52-125-16-nexthop-Internet|52.125.0.0/16|Internet|
|Mi-52-126-15-nexthop-Internet|52.126.0.0/15|Internet|
|Mi-52-130-15-nexthop-Internet|52.130.0.0/15|Internet|
|Mi-52-132-14-nexthop-Internet|52.132.0.0/14|Internet|
|Mi-52-136-13-nexthop-Internet|52.136.0.0/13|Internet|
|Mi-52-145-16-nexthop-Internet|52.145.0.0/16|Internet|
|Mi-52-146-15-nexthop-Internet|52.146.0.0/15|Internet|
|Mi-52-148-14-nexthop-Internet|52.148.0.0/14|Internet|
|Mi-52-152-13-nexthop-Internet|52.152.0.0/13|Internet|
|Mi-52-160-11-nexthop-Internet|52.160.0.0/11|Internet|
|Mi-52-224-11-nexthop-Internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|Mi-103-25-156-24-nexthop-Internet|103.25.156.0/24|Internet|
|Mi-103-25-157-24-nexthop-Internet|103.25.157.0/24|Internet|
|Mi-103-25-158-23-nexthop-Internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|Mi-131-253-1-24-nexthop-Internet|131.253.1.0/24|Internet|
|Mi-131-253-3-24-nexthop-Internet|131.253.3.0/24|Internet|
|Mi-131-253-5-24-nexthop-Internet|131.253.5.0/24|Internet|
|Mi-131-253-6-24-nexthop-Internet|131.253.6.0/24|Internet|
|Mi-131-253-8-24-nexthop-Internet|131.253.8.0/24|Internet|
|Mi-131-253-12-22-nexthop-Internet|131.253.12.0/22|Internet|
|Mi-131-253-16-23-nexthop-Internet|131.253.16.0/23|Internet|
|Mi-131-253-18-24-nexthop-Internet|131.253.18.0/24|Internet|
|Mi-131-253-21-24-nexthop-Internet|131.253.21.0/24|Internet|
|Mi-131-253-22-23-nexthop-Internet|131.253.22.0/23|Internet|
|Mi-131-253-24-21-nexthop-Internet|131.253.24.0/21|Internet|
|Mi-131-253-32-20-nexthop-Internet|131.253.32.0/20|Internet|
|Mi-131-253-61-24-nexthop-Internet|131.253.61.0/24|Internet|
|Mi-131-253-62-23-nexthop-Internet|131.253.62.0/23|Internet|
|Mi-131-253-64-18-nexthop-Internet|131.253.64.0/18|Internet|
|Mi-131-253-128-17-nexthop-Internet|131.253.128.0/17|Internet|
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

\* a MI ALHÁLÓZAT az alhálózat IP-címtartományt jelöli x. x. x. x/y formátumban. Ezeket az információkat az alhálózati tulajdonságok Azure Portaljában találja.

Emellett hozzáadhat bejegyzéseket az útválasztási táblázathoz, hogy átirányítsa a helyszíni magánhálózati IP-tartományokat a virtuális hálózati átjáró vagy a virtuális hálózati berendezés (NVA) használatával.

Ha a virtuális hálózat egyéni DNS-t tartalmaz, az egyéni DNS-kiszolgálónak képesnek kell lennie a nyilvános DNS-rekordok feloldására. Az Azure AD-hitelesítéshez hasonló további funkciókkal további teljes tartománynevek feloldására lehet szükség. További információt az [Egyéni DNS beállítása](sql-database-managed-instance-custom-dns.md)című témakörben talál.

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>Elavult A hálózati követelmények Service-alapú alhálózat-konfiguráció nélkül

Felügyelt példány üzembe helyezése egy dedikált alhálózaton a virtuális hálózaton belül. Az alhálózatnak a következő jellemzőkkel kell rendelkeznie:

- **Dedikált alhálózat:** A felügyelt példány alhálózata nem tartalmazhat olyan más felhőalapú szolgáltatást, amely hozzá van rendelve, és nem lehet átjáró-alhálózat. Az alhálózat nem tartalmazhat erőforrást, hanem a felügyelt példányt, és később nem adhat hozzá más típusú erőforrásokat az alhálózatban.
- **Hálózati biztonsági csoport (NSG):** A virtuális hálózathoz társított NSG minden más szabály előtt meg kell határoznia a [bejövő biztonsági szabályokat](#mandatory-inbound-security-rules) és a [kimenő biztonsági szabályokat](#mandatory-outbound-security-rules) . Az NSG segítségével szabályozhatja a felügyelt példány adatvégpontjának hozzáférését úgy, hogy az 1433-es és a 11000-11999-es porton lévő forgalmat szűri, ha felügyelt példány van konfigurálva az átirányítási kapcsolatokhoz.
- **Felhasználó által megadott útvonal (UDR) tábla:** A virtuális hálózathoz társított UDR-táblának konkrét [bejegyzéseket](#user-defined-routes)kell tartalmaznia.
- **Nincsenek szolgáltatási végpontok:** Nincs hozzárendelve szolgáltatási végpont a felügyelt példány alhálózatához. Győződjön meg arról, hogy a szolgáltatás-végpontok beállítás le van tiltva a virtuális hálózat létrehozásakor.
- **Elegendő IP-cím:** A felügyelt példány alhálózatának legalább 16 IP-címmel kell rendelkeznie. Az ajánlott minimális érték 32 IP-cím. További információ: a [felügyelt példányok alhálózat méretének meghatározása](sql-database-managed-instance-determine-size-vnet-subnet.md). A felügyelt példányokat [a meglévő hálózatban](sql-database-managed-instance-configure-vnet-subnet.md) is telepítheti, miután konfigurálta a [felügyelt példányok hálózati követelményeinek](#network-requirements)kielégítéséhez. Ellenkező esetben hozzon létre egy [új hálózatot és alhálózatot](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Nem telepíthet új felügyelt példányt, ha a célként megadott alhálózat nem rendelkezik ezekkel a jellemzőkkel. Felügyelt példány létrehozásakor a rendszer egy hálózati leképezési házirendet alkalmaz az alhálózaton, hogy megakadályozza a nem megfelelő módosításokat a hálózatkezelés beállításában. Miután az utolsó példányt eltávolította az alhálózatból, a rendszer eltávolítja a hálózati leképezési házirendet is.

### <a name="mandatory-inbound-security-rules"></a>Kötelező bejövő biztonsági szabályok

| Name (Név)       |Port                        |Protokoll|Forrás           |Cél|Műveletek|
|------------|----------------------------|--------|-----------------|-----------|------|
|felügyelet  |9000, 9003, 1438, 1440, 1452|TCP     |Bármelyik              |MI ALHÁLÓZAT  |Engedélyezés |
|mi_subnet   |Bármelyik                         |Bármelyik     |MI ALHÁLÓZAT        |MI ALHÁLÓZAT  |Engedélyezés |
|health_probe|Bármelyik                         |Bármelyik     |AzureLoadBalancer|MI ALHÁLÓZAT  |Engedélyezés |

### <a name="mandatory-outbound-security-rules"></a>Kötelező kimenő biztonsági szabályok

| Name (Név)       |Port          |Protokoll|Forrás           |Cél|Műveletek|
|------------|--------------|--------|-----------------|-----------|------|
|felügyelet  |443, 12000    |TCP     |MI ALHÁLÓZAT        |AzureCloud |Engedélyezés |
|mi_subnet   |Bármelyik           |Bármelyik     |MI ALHÁLÓZAT        |MI ALHÁLÓZAT  |Engedélyezés |

> [!IMPORTANT]
> Győződjön meg arról, hogy csak egy bejövő szabály van a 9000, 9003, 1438, 1440, 1452 és egy kimenő szabály számára a következő portokhoz: 443 12000. A felügyelt példányok üzembe helyezése Azure Resource Manager központi telepítések esetén sikertelen lesz, ha a bejövő és a kimenő szabályok külön vannak konfigurálva az egyes portokhoz. Ha ezek a portok külön szabályokban vannak, a telepítés sikertelen lesz, hibakód: `VnetSubnetConflictWithIntendedPolicy`

\* a MI ALHÁLÓZAT az alhálózat IP-címtartományt jelöli x. x. x. x/y formátumban. Ezeket az információkat az alhálózati tulajdonságok Azure Portaljában találja.

> [!IMPORTANT]
> Habár a kötelező bejövő biztonsági szabályok engedélyezik a forgalmat a 9000, 9003, 1438, 1440 és 1452 portok _bármely_ forrásáról, ezeket a portokat egy beépített tűzfal védi. További információ: [a felügyeleti végponti címek meghatározása](sql-database-managed-instance-find-management-endpoint-ip-address.md).
> [!NOTE]
> Ha egy felügyelt példányban tranzakciós replikálást használ, és ha bármely példány-adatbázist közzétevőként vagy terjesztőként használ, nyissa meg az alhálózat biztonsági szabályaiban az 445 (TCP kimenő) portot. Ez a port lehetővé teszi az Azure-fájlmegosztás elérését.

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

## <a name="next-steps"></a>Következő lépések

- Az áttekintést lásd: [SQL Database speciális adatbiztonság](sql-database-managed-instance.md).
- Ismerje meg, hogyan [állíthat be egy új Azure-beli virtuális hálózatot](sql-database-managed-instance-create-vnet-subnet.md) vagy egy [meglévő Azure-beli virtuális hálózatot](sql-database-managed-instance-configure-vnet-subnet.md) , ahol felügyelt példányokat helyezhet üzembe.
- A felügyelt példányok üzembe helyezéséhez használandó [alhálózat méretének kiszámítása](sql-database-managed-instance-determine-size-vnet-subnet.md) .
- Ismerje meg, hogyan hozhat létre felügyelt példányt:
  - A [Azure Portal](sql-database-managed-instance-get-started.md).
  - A [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)használatával.
  - [Azure Resource Manager sablon](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)használatával.
  - [Egy Azure Resource Manager-sablon használatával (az JumpBox használatával, a SSMS tartalmazza)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/). 
