---
title: Kapcsolati architektúra
titleSuffix: Azure SQL Managed Instance
description: Ismerje meg az Azure SQL felügyelt példányának kommunikációs és kapcsolati architektúráját, valamint azt, hogy az összetevők hogyan irányítsák át az SQL felügyelt példány forgalmát.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: e0a16ac8b52907f5ce27d0d186172725e8536423
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045185"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Az Azure SQL felügyelt példányának kapcsolati architektúrája
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk az Azure SQL felügyelt példányain folytatott kommunikációt ismerteti. Emellett leírja a kapcsolati architektúrát, valamint azt is, hogy az összetevők hogyan irányítják át a forgalmat az SQL felügyelt példányára.  

Az SQL felügyelt példánya az Azure-beli virtuális hálózaton belül található, és az SQL felügyelt példányaihoz dedikált alhálózat. Ez az üzembe helyezés a következőket biztosítja:

- Biztonságos magánhálózati IP-cím.
- Helyszíni hálózat egy SQL-felügyelt példányhoz való összekapcsolásának lehetősége.
- SQL felügyelt példány csatolása egy csatolt kiszolgálóhoz vagy egy másik helyszíni adattárhoz.
- SQL felügyelt példányok Azure-erőforrásokhoz való összekapcsolásának lehetősége.

## <a name="communication-overview"></a>Kommunikáció áttekintése

A következő ábrán a felügyelt SQL-példányokhoz kapcsolódó entitások láthatók. Emellett az SQL felügyelt példánnyal való kommunikációhoz szükséges erőforrásokat is megjeleníti. A diagram alján található kommunikációs folyamat azokat az ügyfeleket és eszközöket jelöli, amelyek adatforrásként csatlakoznak az SQL felügyelt példányához.  

![A kapcsolati architektúrában lévő entitások](./media/connectivity-architecture-overview/connectivityarch001.png)

A felügyelt SQL-példányok szolgáltatásként nyújtott platformként nyújtott szolgáltatások. Az Azure a szolgáltatás telemetria-adatfolyamok alapján történő kezeléséhez automatizált ügynököket (felügyelet, üzembe helyezés és karbantartás) használ. Mivel az Azure felelős a felügyeletért, az ügyfelek nem férhetnek hozzá az SQL felügyelt példányok virtuális fürtjéhez RDP protokoll (RDP) használatával.

A végfelhasználók vagy alkalmazások által indított egyes műveletekhez szükség lehet az SQL felügyelt példányaira a platformmal való interakcióhoz. Az egyik esetben egy SQL felügyelt példány-adatbázis létrehozása. Ezt az erőforrást a Azure Portal, a PowerShell, az Azure CLI és a REST API teszi elérhetővé.

Az SQL felügyelt példányai olyan Azure-szolgáltatásoktól függenek, mint az Azure Storage biztonsági mentések, Azure-Event Hubs telemetria, Azure Active Directory a hitelesítéshez, a Azure Key Vault for transzparens adattitkosítás (TDE) és néhány Azure platform-szolgáltatás, amely biztonsági és támogatási funkciókat biztosít. Az SQL felügyelt példányai kapcsolatot létesít ezekkel a szolgáltatásokkal.

Minden kommunikáció titkosítva van, és a tanúsítványokkal van aláírva. A kommunikáló felek megbízhatóságának ellenőrzéséhez az SQL felügyelt példányai folyamatosan ellenőrzik ezeket a tanúsítványokat a visszavont tanúsítványok listájáról. Ha visszavonják a tanúsítványokat, az SQL felügyelt példánya bezárja a kapcsolatokat az adatvédelemhez.

## <a name="high-level-connectivity-architecture"></a>Magas szintű kapcsolati architektúra

A felügyelt SQL-példányok magas szinten a szolgáltatás-összetevők készletét jelentik. Ezek az összetevők az ügyfél virtuális hálózati alhálózatán belül futó elkülönített virtuális gépek dedikált készletén futnak. Ezek a gépek virtuális fürtöt alkotnak.

A virtuális fürtök több SQL felügyelt példányt is tárolhatnak. Ha szükséges, a fürt automatikusan kibontja a vagy a szerződést, amikor az ügyfél megváltoztatja az alhálózat kiépített példányainak számát.

Az ügyfélalkalmazások csatlakozhatnak az SQL felügyelt példányaihoz, és lekérhetik és frissíthetik az adatbázisokat a virtuális hálózaton, a kihelyezett virtuális hálózaton vagy a VPN-vagy Azure-ExpressRoute csatlakoztatott hálózaton belül. A hálózatnak végpontot és magánhálózati IP-címet kell használnia.  

![Kapcsolati architektúra diagramja](./media/connectivity-architecture-overview/connectivityarch002.png)

Az Azure felügyeleti és központi telepítési szolgáltatásai a virtuális hálózaton kívül futnak. A felügyelt SQL-példányok és az Azure-szolgáltatások a nyilvános IP-címekkel rendelkező végpontokon csatlakoznak. Ha egy felügyelt SQL-példány kimenő kapcsolatokat hoz létre, a hálózati címfordítás (NAT) fogadásakor a rendszer a kapcsolódást a nyilvános IP-címről érkezőnek tekinti.

A felügyeleti forgalom az ügyfél virtuális hálózatán keresztül folyik. Ez azt jelenti, hogy a virtuális hálózat infrastruktúrájának elemei a példány meghibásodása és elérhetetlenné válása miatt kárt okozhatnak a felügyeleti forgalomban.

> [!IMPORTANT]
> A felhasználói élmény és a szolgáltatás rendelkezésre állásának javítása érdekében az Azure hálózati leképezési házirendet alkalmaz az Azure Virtual Network infrastruktúra elemeire. A házirend hatással lehet az SQL felügyelt példányának működésére. Ez a platform-mechanizmus transzparens módon kommunikál a felhasználók hálózati követelményeivel. A szabályzat fő célja, hogy megakadályozza a hálózati helytelen konfigurálást, és gondoskodjon az SQL felügyelt példányok szokásos műveleteiről. Ha töröl egy SQL-felügyelt példányt, a rendszer eltávolítja a hálózati leképezési házirendet is.

## <a name="virtual-cluster-connectivity-architecture"></a>Virtuális fürt kapcsolati architektúrája

Ismerkedjen meg a kapcsolati architektúrával a felügyelt SQL-példányok esetében. Az alábbi ábrán a virtuális fürt fogalmi elrendezése látható.

![A virtuális fürt kapcsolati architektúrája](./media/connectivity-architecture-overview/connectivityarch003.png)

Az ügyfelek egy SQL-alapú felügyelt példányhoz csatlakoznak az űrlapot tartalmazó állomásnév használatával `<mi_name>.<dns_zone>.database.windows.net` . Ez az állomásnév a magánhálózati IP-címekre lesz feloldva, bár egy nyilvános tartománynévrendszer-(DNS-) zónában van regisztrálva, és nyilvánosan feloldható. A `zone-id` automatikusan létrejön a fürt létrehozásakor. Ha egy újonnan létrehozott fürt egy másodlagos SQL felügyelt példányt futtat, akkor a zóna AZONOSÍTÓját megosztja az elsődleges fürttel. További információ: [automatikus feladatátvételi csoportok használata több adatbázis átlátható és koordinált feladatátvételének engedélyezéséhez](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Ez a magánhálózati IP-cím az SQL felügyelt példányának belső terheléselosztó része. A terheléselosztó átirányítja a forgalmat az SQL felügyelt példány átjárójának. Mivel több SQL felügyelt példány futhat ugyanazon a fürtön belül, az átjáró a felügyelt SQL-példány állomásneve használatával irányítja át a forgalmat a megfelelő SQL Engine szolgáltatásba.

A felügyeleti és központi telepítési szolgáltatások egy felügyelt SQL-példányhoz csatlakoznak [, amely egy](#management-endpoint) külső terheléselosztó számára van leképezve. A rendszer csak akkor irányítja a forgalmat a csomópontokhoz, ha egy előre meghatározott porton érkezik, amely csak az SQL felügyelt példány felügyeleti összetevői által használt. A csomópontokon a beépített tűzfal úgy van beállítva, hogy csak a Microsoft IP-címtartományok forgalmát engedélyezze. A tanúsítványok kölcsönösen hitelesítik a felügyeleti összetevők és a felügyeleti sík közötti összes kommunikációt.

## <a name="management-endpoint"></a>Felügyeleti végpont

Az Azure felügyeleti végpont használatával kezeli az SQL felügyelt példányát. Ez a végpont a példány virtuális fürtjén belül van. A felügyeleti végpontot egy beépített tűzfal védi a hálózati szinten. Az alkalmazás szintjén a kölcsönös tanúsítvány-ellenőrzés védi. A végpont IP-címének megkereséséhez tekintse meg [a felügyeleti végpont IP-címének megállapítása](management-endpoint-find-ip-address.md)című témakört.

Ha a kapcsolatok a felügyelt SQL-példányon belül kezdődnek (mint a biztonsági mentések és a naplók), a forgalom úgy tűnik, hogy a felügyeleti végpont nyilvános IP-címéről indul el. A nyilvános szolgáltatásokhoz való hozzáférést egy felügyelt SQL-példányon korlátozhatja, ha a tűzfalszabályok beállításával csak az SQL felügyelt példány IP-címét engedélyezi. További információ: [az SQL felügyelt példány beépített tűzfalának ellenőrzése](management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Az SQL felügyelt példány régiójában található Azure-szolgáltatásokra áthaladó forgalom optimalizált, ezért a felügyeleti végpont nyilvános IP-címére nem. Ezért ha IP-alapú tűzfalszabályok használatát kell használnia, általában a tároláshoz, a szolgáltatásnak a felügyelt SQL-példánytól eltérő régióban kell lennie.

## <a name="service-aided-subnet-configuration"></a>Szolgáltatással segített alhálózat-konfiguráció

Az ügyfél biztonsági és kezelhetőségi követelményeinek kezeléséhez az SQL felügyelt példánya a manuálistól a szolgáltatással támogatott alhálózat-konfigurációra vált.

A szolgáltatással segített alhálózat-konfiguráció felhasználója teljes mértékben felügyeli az adatok (TDS) forgalmát, miközben az SQL felügyelt példánya felelősséget vállal a felügyeleti forgalom zavartalan áramlásának biztosításához az SLA teljesítése érdekében.

A szolgáltatással segített alhálózat-konfiguráció a virtuális hálózat [alhálózati delegálás](../../virtual-network/subnet-delegation-overview.md) funkciójának segítségével biztosítja az automatikus hálózati konfiguráció felügyeletét és a szolgáltatásvégpontok engedélyezését. A szolgáltatásvégpontok használhatók a biztonsági mentéseket/auditnaplókat tároló tárfiókok virtuális hálózati tűzfalszabályainak konfigurálására.

### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

Helyezzen üzembe egy SQL felügyelt példányt egy dedikált alhálózatban a virtuális hálózaton belül. Az alhálózatnak a következő jellemzőkkel kell rendelkeznie:

- **Dedikált alhálózat:** Az SQL felügyelt példány alhálózata nem tartalmazhat más, hozzá társított felhőalapú szolgáltatást, és nem lehet átjáró-alhálózat. Az alhálózat nem tartalmazhat erőforrásokat, de az SQL felügyelt példányát, és később nem adhat hozzá más típusú erőforrásokat az alhálózatban.
- **Alhálózat delegálása:** Az SQL felügyelt példány alhálózatát delegálni kell az `Microsoft.Sql/managedInstances` erőforrás-szolgáltatóhoz.
- **Hálózati biztonsági csoport (NSG):** Egy NSG kell társítani az SQL felügyelt példány alhálózatához. A NSG használatával szabályozhatja az SQL felügyelt példány adatvégpontjának hozzáférését úgy, hogy az 1433-es és a 11000-11999-es porton lévő forgalmat szűri, ha az SQL felügyelt példánya átirányítási kapcsolatok használatára van konfigurálva. A szolgáltatás automatikusan kiépíti és megtartja a jelenlegi, a felügyeleti forgalom zavartalan áramlását lehetővé tevő [szabályokat](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration).
- **Felhasználó által megadott útvonal (UDR) tábla:** UDR táblát kell társítani az SQL felügyelt példány alhálózatához. Hozzáadhat bejegyzéseket az útválasztási táblához, hogy a virtuális hálózati átjárón vagy a virtuális hálózati berendezésen (NVA) keresztül átirányítsa a helyszíni magánhálózati IP-tartományokkal rendelkező forgalmat. A szolgáltatás automatikusan kiépíti és megtartja a jelenlegi, a felügyeleti forgalom zavartalan áramlását lehetővé tevő [bejegyzéseket](#user-defined-routes-with-service-aided-subnet-configuration).
- **Elegendő IP-cím:** Az SQL felügyelt példány alhálózatának legalább 16 IP-címmel kell rendelkeznie. A javasolt minimális mennyiség 32 IP-cím. További információ: az [alhálózat méretének meghatározása az SQL felügyelt példányaihoz](vnet-subnet-determine-size.md). Az SQL felügyelt példányai [a meglévő hálózatban](vnet-existing-add-subnet.md) telepíthetők az [SQL-felügyelt példányok hálózati követelményeinek](#network-requirements)kielégítése érdekében. Egyéb esetben hozzon létre egy [új virtuális hálózatot és alhálózatot](virtual-network-subnet-create-arm-template.md).

> [!IMPORTANT]
> SQL felügyelt példány létrehozásakor a rendszer egy hálózati leképezési házirendet alkalmaz az alhálózaton, hogy megakadályozza a nem megfelelő módosításokat a hálózatkezelés beállításában. Miután az utolsó példányt eltávolította az alhálózatból, a rendszer eltávolítja a hálózati leképezési házirendet is.

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Kötelező bejövő biztonsági szabályok a szolgáltatással segített alhálózat konfigurációjával

| Name       |Port                        |Protokoll|Forrás           |Cél|Műveletek|
|------------|----------------------------|--------|-----------------|-----------|------|
|felügyelet  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |MI ALHÁLÓZAT  |Engedélyezés |
|            |9000, 9003                  |TCP     |CorpnetSaw       |MI ALHÁLÓZAT  |Engedélyezés |
|            |9000, 9003                  |TCP     |CorpnetPublic    |MI ALHÁLÓZAT  |Engedélyezés |
|mi_subnet   |Bármelyik                         |Bármelyik     |MI ALHÁLÓZAT        |MI ALHÁLÓZAT  |Engedélyezés |
|health_probe|Bármelyik                         |Bármelyik     |AzureLoadBalancer|MI ALHÁLÓZAT  |Engedélyezés |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Kötelező kimenő biztonsági szabályok a szolgáltatással segített alhálózat konfigurációjával

| Name       |Port          |Protokoll|Forrás           |Cél|Műveletek|
|------------|--------------|--------|-----------------|-----------|------|
|felügyelet  |443, 12000    |TCP     |MI ALHÁLÓZAT        |AzureCloud |Engedélyezés |
|mi_subnet   |Bármelyik           |Bármelyik     |MI ALHÁLÓZAT        |MI ALHÁLÓZAT  |Engedélyezés |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>A felhasználó által megadott útvonalak a szolgáltatással segített alhálózat konfigurációjával

|Name|Címelőtag|Következő ugrás|
|----|--------------|-------|
|alhálózat – vnetlocal|MI ALHÁLÓZAT|Virtuális hálózat|
|Mi-13-64-11-nexthop-Internet|13.64.0.0/11|Internet|
|Mi-13-104-14-nexthop-Internet|13.104.0.0/14|Internet|
|Mi-20-33-16-nexthop-Internet|20.33.0.0/16|Internet|
|Mi-20-34-15-nexthop-Internet|20.34.0.0/15|Internet|
|Mi-20-36-14-nexthop-Internet|20.36.0.0/14|Internet|
|Mi-20-40-13-nexthop-Internet|20.40.0.0/13|Internet|
|Mi-20-48-12-nexthop-Internet|20.48.0.0/12|Internet|
|Mi-20-64-10-nexthop-Internet|20.64.0.0/10|Internet|
|Mi-20-128-16-nexthop-Internet|20.128.0.0/16|Internet|
|Mi-20-135-16-nexthop-Internet|20.135.0.0/16|Internet|
|Mi-20-136-16-nexthop-Internet|20.136.0.0/16|Internet|
|Mi-20-140-15-nexthop-Internet|20.140.0.0/15|Internet|
|Mi-20-143-16-nexthop-Internet|20.143.0.0/16|Internet|
|Mi-20-144-14-nexthop-Internet|20.144.0.0/14|Internet|
|Mi-20-150-15-nexthop-Internet|20.150.0.0/15|Internet|
|Mi-20-160-12-nexthop-Internet|20.160.0.0/12|Internet|
|Mi-20-176-14-nexthop-Internet|20.176.0.0/14|Internet|
|Mi-20-180-14-nexthop-Internet|20.180.0.0/14|Internet|
|Mi-20-184-13-nexthop-Internet|20.184.0.0/13|Internet|
|Mi-20-192-10-nexthop-Internet|20.192.0.0/10|Internet|
|Mi-40-64-10-nexthop-Internet|40.64.0.0/10|Internet|
|Mi-51-4-15-nexthop-Internet|51.4.0.0/15|Internet|
|Mi-51-8-16-nexthop-Internet|51.8.0.0/16|Internet|
|Mi-51-10-15-nexthop-Internet|51.10.0.0/15|Internet|
|Mi-51-18-16-nexthop-Internet|51.18.0.0/16|Internet|
|Mi-51-51-16-nexthop-Internet|51.51.0.0/16|Internet|
|Mi-51-53-16-nexthop-Internet|51.53.0.0/16|Internet|
|Mi-51-103-16-nexthop-Internet|51.103.0.0/16|Internet|
|Mi-51-104-15-nexthop-Internet|51.104.0.0/15|Internet|
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
|Mi-64-4-18-nexthop-Internet|64.4.0.0/18|Internet|
|Mi-65-52-14-nexthop-Internet|65.52.0.0/14|Internet|
|Mi-66-119-144-20-nexthop-Internet|66.119.144.0/20|Internet|
|Mi-70-37-17-nexthop-Internet|70.37.0.0/17|Internet|
|Mi-70-37-128-18-nexthop-Internet|70.37.128.0/18|Internet|
|Mi-91-190-216-21-nexthop-Internet|91.190.216.0/21|Internet|
|Mi-94-245-64-18-nexthop-Internet|94.245.64.0/18|Internet|
|Mi-103-9-8-22-nexthop-Internet|103.9.8.0/22|Internet|
|Mi-103-25-156-24-nexthop-Internet|103.25.156.0/24|Internet|
|Mi-103-25-157-24-nexthop-Internet|103.25.157.0/24|Internet|
|Mi-103-25-158-23-nexthop-Internet|103.25.158.0/23|Internet|
|Mi-103-36-96-22-nexthop-Internet|103.36.96.0/22|Internet|
|Mi-103-255-140-22-nexthop-Internet|103.255.140.0/22|Internet|
|Mi-104-40-13-nexthop-Internet|104.40.0.0/13|Internet|
|Mi-104-146-15-nexthop-Internet|104.146.0.0/15|Internet|
|Mi-104-208-13-nexthop-Internet|104.208.0.0/13|Internet|
|Mi-111-221-16-20-nexthop-Internet|111.221.16.0/20|Internet|
|Mi-111-221-64-18-nexthop-Internet|111.221.64.0/18|Internet|
|Mi-129-75-16-nexthop-Internet|129.75.0.0/16|Internet|
|Mi-131-107-16-nexthop-Internet|131.107.0.0/16|Internet|
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
|Mi-132-245-16-nexthop-Internet|132.245.0.0/16|Internet|
|Mi-134-170-16-nexthop-Internet|134.170.0.0/16|Internet|
|Mi-134-177-16-nexthop-Internet|134.177.0.0/16|Internet|
|Mi-137-116-15-nexthop-Internet|137.116.0.0/15|Internet|
|Mi-137-135-16-nexthop-Internet|137.135.0.0/16|Internet|
|Mi-138-91-16-nexthop-Internet|138.91.0.0/16|Internet|
|Mi-138-196-16-nexthop-Internet|138.196.0.0/16|Internet|
|Mi-139-217-16-nexthop-Internet|139.217.0.0/16|Internet|
|Mi-139-219-16-nexthop-Internet|139.219.0.0/16|Internet|
|Mi-141-251-16-nexthop-Internet|141.251.0.0/16|Internet|
|Mi-146-147-16-nexthop-Internet|146.147.0.0/16|Internet|
|Mi-147-243-16-nexthop-Internet|147.243.0.0/16|Internet|
|Mi-150-171-16-nexthop-Internet|150.171.0.0/16|Internet|
|Mi-150-242-48-22-nexthop-Internet|150.242.48.0/22|Internet|
|Mi-157-54-15-nexthop-Internet|157.54.0.0/15|Internet|
|Mi-157-56-14-nexthop-Internet|157.56.0.0/14|Internet|
|Mi-157-60-16-nexthop-Internet|157.60.0.0/16|Internet|
|Mi-167-105-16-nexthop-Internet|167.105.0.0/16|Internet|
|Mi-167-220-16-nexthop-Internet|167.220.0.0/16|Internet|
|Mi-168-61-16-nexthop-Internet|168.61.0.0/16|Internet|
|Mi-168-62-15-nexthop-Internet|168.62.0.0/15|Internet|
|Mi-191-232-13-nexthop-Internet|191.232.0.0/13|Internet|
|Mi-192-32-16-nexthop-Internet|192.32.0.0/16|Internet|
|Mi-192-48-225-24-nexthop-Internet|192.48.225.0/24|Internet|
|Mi-192-84-159-24-nexthop-Internet|192.84.159.0/24|Internet|
|Mi-192-84-160-23-nexthop-Internet|192.84.160.0/23|Internet|
|Mi-192-197-157-24-nexthop-Internet|192.197.157.0/24|Internet|
|Mi-193-149-64-19-nexthop-Internet|193.149.64.0/19|Internet|
|Mi-193-221-113-24-nexthop-Internet|193.221.113.0/24|Internet|
|Mi-194-69-96-19-nexthop-Internet|194.69.96.0/19|Internet|
|Mi-194-110-197-24-nexthop-Internet|194.110.197.0/24|Internet|
|Mi-198-105-232-22-nexthop-Internet|198.105.232.0/22|Internet|
|Mi-198-200-130-24-nexthop-Internet|198.200.130.0/24|Internet|
|Mi-198-206-164-24-nexthop-Internet|198.206.164.0/24|Internet|
|Mi-199-60-28-24-nexthop-Internet|199.60.28.0/24|Internet|
|Mi-199-74-210-24-nexthop-Internet|199.74.210.0/24|Internet|
|Mi-199-103-90-23-nexthop-Internet|199.103.90.0/23|Internet|
|Mi-199-103-122-24-nexthop-Internet|199.103.122.0/24|Internet|
|Mi-199-242-32-20-nexthop-Internet|199.242.32.0/20|Internet|
|Mi-199-242-48-21-nexthop-Internet|199.242.48.0/21|Internet|
|Mi-202-89-224-20-nexthop-Internet|202.89.224.0/20|Internet|
|Mi-204-13-120-21-nexthop-Internet|204.13.120.0/21|Internet|
|Mi-204-14-180-22-nexthop-Internet|204.14.180.0/22|Internet|
|Mi-204-79-135-24-nexthop-Internet|204.79.135.0/24|Internet|
|Mi-204-79-179-24-nexthop-Internet|204.79.179.0/24|Internet|
|Mi-204-79-181-24-nexthop-Internet|204.79.181.0/24|Internet|
|Mi-204-79-188-24-nexthop-Internet|204.79.188.0/24|Internet|
|Mi-204-79-195-24-nexthop-Internet|204.79.195.0/24|Internet|
|Mi-204-79-196-23-nexthop-Internet|204.79.196.0/23|Internet|
|Mi-204-79-252-24-nexthop-Internet|204.79.252.0/24|Internet|
|Mi-204-152-18-23-nexthop-Internet|204.152.18.0/23|Internet|
|Mi-204-152-140-23-nexthop-Internet|204.152.140.0/23|Internet|
|Mi-204-231-192-24-nexthop-Internet|204.231.192.0/24|Internet|
|Mi-204-231-194-23-nexthop-Internet|204.231.194.0/23|Internet|
|Mi-204-231-197-24-nexthop-Internet|204.231.197.0/24|Internet|
|Mi-204-231-198-23-nexthop-Internet|204.231.198.0/23|Internet|
|Mi-204-231-200-21-nexthop-Internet|204.231.200.0/21|Internet|
|Mi-204-231-208-20-nexthop-Internet|204.231.208.0/20|Internet|
|Mi-204-231-236-24-nexthop-Internet|204.231.236.0/24|Internet|
|Mi-205-174-224-20-nexthop-Internet|205.174.224.0/20|Internet|
|Mi-206-138-168-21-nexthop-Internet|206.138.168.0/21|Internet|
|Mi-206-191-224-19-nexthop-Internet|206.191.224.0/19|Internet|
|Mi-207-46-16-nexthop-Internet|207.46.0.0/16|Internet|
|Mi-207-68-128-18-nexthop-Internet|207.68.128.0/18|Internet|
|Mi-208-68-136-21-nexthop-Internet|208.68.136.0/21|Internet|
|Mi-208-76-44-22-nexthop-Internet|208.76.44.0/22|Internet|
|Mi-208-84-21-nexthop-Internet|208.84.0.0/21|Internet|
|Mi-209-240-192-19-nexthop-Internet|209.240.192.0/19|Internet|
|Mi-213-199-128-18-nexthop-Internet|213.199.128.0/18|Internet|
|Mi-216-32-180-22-nexthop-Internet|216.32.180.0/22|Internet|
|Mi-216-220-208-20-nexthop-Internet|216.220.208.0/20|Internet|
|mi-23-96-13-nexthop-Internet|23.96.0.0/13|Internet|
|Mi-42-159-16-nexthop-Internet|42.159.0.0/16|Internet|
|Mi-51-13-17-nexthop-Internet|51.13.0.0/17|Internet|
|Mi-51-107-16-nexthop-Internet|51.107.0.0/16|Internet|
|Mi-51-116-16-nexthop-Internet|51.116.0.0/16|Internet|
|Mi-51-120-16-nexthop-Internet|51.120.0.0/16|Internet|
|Mi-51-120-128-17-nexthop-Internet|51.120.128.0/17|Internet|
|Mi-51-124-16-nexthop-Internet|51.124.0.0/16|Internet|
|Mi-102-37-18-nexthop-Internet|102.37.0.0/18|Internet|
|Mi-102-133-16-nexthop-Internet|102.133.0.0/16|Internet|
|Mi-199-30-16-20-nexthop-Internet|199.30.16.0/20|Internet|
|Mi-204-79-180-24-nexthop-Internet|204.79.180.0/24|Internet|
||||

\*A MI ALHÁLÓZAT az alhálózat IP-címtartományt jelöli x. x. x. x/y formátumban. Ezeket az információkat az alhálózati tulajdonságok Azure Portaljában találja.

Emellett hozzáadhat bejegyzéseket az útválasztási táblázathoz, hogy átirányítsa a helyszíni magánhálózati IP-tartományokat a virtuális hálózati átjáró vagy a virtuális hálózati berendezés (NVA) használatával.

Ha a virtuális hálózat egyéni DNS-t tartalmaz, az egyéni DNS-kiszolgálónak képesnek kell lennie a nyilvános DNS-rekordok feloldására. Az Azure AD-hitelesítéshez hasonló további funkciókkal további teljes tartománynevek feloldására lehet szükség. További információt az [Egyéni DNS beállítása](custom-dns-configure.md)című témakörben talál.

### <a name="networking-constraints"></a>Hálózati megkötések

**A tls 1,2 a kimenő kapcsolatokon van kikényszerítve**: a január 2020 Microsoft által kényszerített TLS 1,2 az összes Azure-szolgáltatáson belüli forgalomhoz. Az Azure SQL felügyelt példányai esetében ez a TLS 1,2-t eredményezte a replikáláshoz használt kimenő kapcsolatokon és a SQL Serverhoz csatolt kiszolgáló kapcsolatain. Ha a SQL Server 2016 régebbi verzióit használja, és az SQL felügyelt példánnyal rendelkezik, győződjön meg arról, hogy a [TLS 1,2-specifikus frissítések](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) lettek alkalmazva.

A következő virtuális hálózati funkciók jelenleg nem támogatottak az SQL felügyelt példányain:

- **Microsoft-partneri**kapcsolat: a [Microsoft-társak](../../expressroute/expressroute-faqs.md#microsoft-peering) Express Route-áramkörökhöz való közvetlen vagy tranzitívnak való engedélyezése a virtuális hálózattal, ahol az SQL felügyelt példánya a virtuális hálózaton és a szolgáltatásokon belüli SQL felügyelt példány-összetevők közötti forgalmat befolyásolja, a rendelkezésre állási problémáktól függ. A felügyelt példányok virtuális hálózatra való központi telepítése már engedélyezve van a Microsoft társközi szolgáltatásával.
- **Globális virtuális hálózati**társítás: az Azure-régiók közötti [virtuális hálózati](../../virtual-network/virtual-network-peering-overview.md) társítási kapcsolat nem működik az SQL felügyelt példánya esetében, mert [dokumentált](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)terheléselosztó-korlátozások vannak érvényben.
- **AzurePlatformDNS**: a AzurePlatformDNS [szolgáltatás címkéjének](../../virtual-network/service-tags-overview.md) használata a platform DNS-feloldásának letiltásához az SQL felügyelt példánya nem érhető el. Habár a felügyelt SQL-példány támogatja az ügyfél által meghatározott DNS-t a DNS-feloldáshoz a motoron belül, a platformhoz tartozó DNS-függőség függ.
- **NAT-átjáró**: [Virtual Network NAT](../../virtual-network/nat-overview.md) használata az adott nyilvános IP-címmel rendelkező kimenő kapcsolatok vezérléséhez az SQL felügyelt példánya nem érhető el. A felügyelt SQL-példányok szolgáltatás jelenleg olyan alapszintű terheléselosztó használatára korlátozódik, amely nem biztosítja a bejövő és kimenő adatforgalom Virtual Network NAT-vel való egymással való létezését.

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>Elavult A hálózati követelmények Service-alapú alhálózat-konfiguráció nélkül

Helyezzen üzembe egy SQL felügyelt példányt egy dedikált alhálózatban a virtuális hálózaton belül. Az alhálózatnak a következő jellemzőkkel kell rendelkeznie:

- **Dedikált alhálózat:** Az SQL felügyelt példány alhálózata nem tartalmazhat más, hozzá társított felhőalapú szolgáltatást, és nem lehet átjáró-alhálózat. Az alhálózat nem tartalmazhat erőforrásokat, de az SQL felügyelt példányát, és később nem adhat hozzá más típusú erőforrásokat az alhálózatban.
- **Hálózati biztonsági csoport (NSG):** A virtuális hálózathoz társított NSG minden más szabály előtt meg kell határoznia a [bejövő biztonsági szabályokat](#mandatory-inbound-security-rules) és a [kimenő biztonsági szabályokat](#mandatory-outbound-security-rules) . A NSG használatával szabályozhatja az SQL felügyelt példány adatvégpontjának hozzáférését úgy, hogy az 1433-es és a 11000-11999-es porton lévő forgalmat szűri, ha az SQL felügyelt példánya átirányítási kapcsolatok használatára van konfigurálva.
- **Felhasználó által megadott útvonal (UDR) tábla:** A virtuális hálózathoz társított UDR-táblának konkrét [bejegyzéseket](#user-defined-routes)kell tartalmaznia.
- **Nincsenek szolgáltatási végpontok:** Nincs hozzárendelve szolgáltatási végpont az SQL felügyelt példány alhálózatához. Győződjön meg arról, hogy a szolgáltatás-végpontok beállítás le van tiltva a virtuális hálózat létrehozásakor.
- **Elegendő IP-cím:** Az SQL felügyelt példány alhálózatának legalább 16 IP-címmel kell rendelkeznie. A javasolt minimális mennyiség 32 IP-cím. További információ: az [alhálózat méretének meghatározása az SQL felügyelt példányaihoz](vnet-subnet-determine-size.md). Az SQL felügyelt példányai [a meglévő hálózatban](vnet-existing-add-subnet.md) telepíthetők az [SQL-felügyelt példányok hálózati követelményeinek](#network-requirements)kielégítése érdekében. Egyéb esetben hozzon létre egy [új virtuális hálózatot és alhálózatot](virtual-network-subnet-create-arm-template.md).

> [!IMPORTANT]
> Nem telepíthet új SQL felügyelt példányt, ha a célként megadott alhálózat nem rendelkezik ezekkel a jellemzőkkel. SQL felügyelt példány létrehozásakor a rendszer egy hálózati leképezési házirendet alkalmaz az alhálózaton, hogy megakadályozza a nem megfelelő módosításokat a hálózatkezelés beállításában. Miután az utolsó példányt eltávolította az alhálózatból, a rendszer eltávolítja a hálózati leképezési házirendet is.

### <a name="mandatory-inbound-security-rules"></a>Kötelező bejövő biztonsági szabályok

| Name       |Port                        |Protokoll|Forrás           |Cél|Műveletek|
|------------|----------------------------|--------|-----------------|-----------|------|
|felügyelet  |9000, 9003, 1438, 1440, 1452|TCP     |Bármelyik              |MI ALHÁLÓZAT  |Engedélyezés |
|mi_subnet   |Bármelyik                         |Bármelyik     |MI ALHÁLÓZAT        |MI ALHÁLÓZAT  |Engedélyezés |
|health_probe|Bármelyik                         |Bármelyik     |AzureLoadBalancer|MI ALHÁLÓZAT  |Engedélyezés |

### <a name="mandatory-outbound-security-rules"></a>Kötelező kimenő biztonsági szabályok

| Name       |Port          |Protokoll|Forrás           |Cél|Műveletek|
|------------|--------------|--------|-----------------|-----------|------|
|felügyelet  |443, 12000    |TCP     |MI ALHÁLÓZAT        |AzureCloud |Engedélyezés |
|mi_subnet   |Bármelyik           |Bármelyik     |MI ALHÁLÓZAT        |MI ALHÁLÓZAT  |Engedélyezés |

> [!IMPORTANT]
> Győződjön meg arról, hogy csak egy bejövő szabály van a 9000, 9003, 1438, 1440, 1452 és egy kimenő szabály számára a következő portokhoz: 443 12000. A felügyelt SQL-példányok Azure Resource Manager központi telepítéseken keresztüli kiépítése meghiúsul, ha a bejövő és a kimenő szabályok külön vannak konfigurálva az egyes portokhoz. Ha ezek a portok külön szabályokban vannak, a telepítés sikertelen lesz, hibakód:`VnetSubnetConflictWithIntendedPolicy`

\*A MI ALHÁLÓZAT az alhálózat IP-címtartományt jelöli x. x. x. x/y formátumban. Ezeket az információkat az alhálózati tulajdonságok Azure Portaljában találja.

> [!IMPORTANT]
> Habár a kötelező bejövő biztonsági szabályok engedélyezik a forgalmat a 9000, 9003, 1438, 1440 és 1452 portok _bármely_ forrásáról, ezeket a portokat egy beépített tűzfal védi. További információ: [a felügyeleti végponti címek meghatározása](management-endpoint-find-ip-address.md).

> [!NOTE]
> Ha tranzakciós replikálást használ egy SQL felügyelt példányban, és ha bármely példány-adatbázist közzétevőként vagy terjesztőként használ, nyissa meg az alhálózat biztonsági szabályaiban az 445 (TCP kimenő) portot. Ez a port lehetővé teszi az Azure-fájlmegosztás elérését.

### <a name="user-defined-routes"></a>Felhasználó által megadott útvonalak

|Name|Címelőtag|Következő ugrás|
|----|--------------|-------|
|subnet_to_vnetlocal|MI ALHÁLÓZAT|Virtuális hálózat|
|Mi-13-64-11-nexthop-Internet|13.64.0.0/11|Internet|
|Mi-13-104-14-nexthop-Internet|13.104.0.0/14|Internet|
|Mi-20-33-16-nexthop-Internet|20.33.0.0/16|Internet|
|Mi-20-34-15-nexthop-Internet|20.34.0.0/15|Internet|
|Mi-20-36-14-nexthop-Internet|20.36.0.0/14|Internet|
|Mi-20-40-13-nexthop-Internet|20.40.0.0/13|Internet|
|Mi-20-48-12-nexthop-Internet|20.48.0.0/12|Internet|
|Mi-20-64-10-nexthop-Internet|20.64.0.0/10|Internet|
|Mi-20-128-16-nexthop-Internet|20.128.0.0/16|Internet|
|Mi-20-135-16-nexthop-Internet|20.135.0.0/16|Internet|
|Mi-20-136-16-nexthop-Internet|20.136.0.0/16|Internet|
|Mi-20-140-15-nexthop-Internet|20.140.0.0/15|Internet|
|Mi-20-143-16-nexthop-Internet|20.143.0.0/16|Internet|
|Mi-20-144-14-nexthop-Internet|20.144.0.0/14|Internet|
|Mi-20-150-15-nexthop-Internet|20.150.0.0/15|Internet|
|Mi-20-160-12-nexthop-Internet|20.160.0.0/12|Internet|
|Mi-20-176-14-nexthop-Internet|20.176.0.0/14|Internet|
|Mi-20-180-14-nexthop-Internet|20.180.0.0/14|Internet|
|Mi-20-184-13-nexthop-Internet|20.184.0.0/13|Internet|
|Mi-20-192-10-nexthop-Internet|20.192.0.0/10|Internet|
|Mi-40-64-10-nexthop-Internet|40.64.0.0/10|Internet|
|Mi-51-4-15-nexthop-Internet|51.4.0.0/15|Internet|
|Mi-51-8-16-nexthop-Internet|51.8.0.0/16|Internet|
|Mi-51-10-15-nexthop-Internet|51.10.0.0/15|Internet|
|Mi-51-18-16-nexthop-Internet|51.18.0.0/16|Internet|
|Mi-51-51-16-nexthop-Internet|51.51.0.0/16|Internet|
|Mi-51-53-16-nexthop-Internet|51.53.0.0/16|Internet|
|Mi-51-103-16-nexthop-Internet|51.103.0.0/16|Internet|
|Mi-51-104-15-nexthop-Internet|51.104.0.0/15|Internet|
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
|Mi-64-4-18-nexthop-Internet|64.4.0.0/18|Internet|
|Mi-65-52-14-nexthop-Internet|65.52.0.0/14|Internet|
|Mi-66-119-144-20-nexthop-Internet|66.119.144.0/20|Internet|
|Mi-70-37-17-nexthop-Internet|70.37.0.0/17|Internet|
|Mi-70-37-128-18-nexthop-Internet|70.37.128.0/18|Internet|
|Mi-91-190-216-21-nexthop-Internet|91.190.216.0/21|Internet|
|Mi-94-245-64-18-nexthop-Internet|94.245.64.0/18|Internet|
|Mi-103-9-8-22-nexthop-Internet|103.9.8.0/22|Internet|
|Mi-103-25-156-24-nexthop-Internet|103.25.156.0/24|Internet|
|Mi-103-25-157-24-nexthop-Internet|103.25.157.0/24|Internet|
|Mi-103-25-158-23-nexthop-Internet|103.25.158.0/23|Internet|
|Mi-103-36-96-22-nexthop-Internet|103.36.96.0/22|Internet|
|Mi-103-255-140-22-nexthop-Internet|103.255.140.0/22|Internet|
|Mi-104-40-13-nexthop-Internet|104.40.0.0/13|Internet|
|Mi-104-146-15-nexthop-Internet|104.146.0.0/15|Internet|
|Mi-104-208-13-nexthop-Internet|104.208.0.0/13|Internet|
|Mi-111-221-16-20-nexthop-Internet|111.221.16.0/20|Internet|
|Mi-111-221-64-18-nexthop-Internet|111.221.64.0/18|Internet|
|Mi-129-75-16-nexthop-Internet|129.75.0.0/16|Internet|
|Mi-131-107-16-nexthop-Internet|131.107.0.0/16|Internet|
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
|Mi-132-245-16-nexthop-Internet|132.245.0.0/16|Internet|
|Mi-134-170-16-nexthop-Internet|134.170.0.0/16|Internet|
|Mi-134-177-16-nexthop-Internet|134.177.0.0/16|Internet|
|Mi-137-116-15-nexthop-Internet|137.116.0.0/15|Internet|
|Mi-137-135-16-nexthop-Internet|137.135.0.0/16|Internet|
|Mi-138-91-16-nexthop-Internet|138.91.0.0/16|Internet|
|Mi-138-196-16-nexthop-Internet|138.196.0.0/16|Internet|
|Mi-139-217-16-nexthop-Internet|139.217.0.0/16|Internet|
|Mi-139-219-16-nexthop-Internet|139.219.0.0/16|Internet|
|Mi-141-251-16-nexthop-Internet|141.251.0.0/16|Internet|
|Mi-146-147-16-nexthop-Internet|146.147.0.0/16|Internet|
|Mi-147-243-16-nexthop-Internet|147.243.0.0/16|Internet|
|Mi-150-171-16-nexthop-Internet|150.171.0.0/16|Internet|
|Mi-150-242-48-22-nexthop-Internet|150.242.48.0/22|Internet|
|Mi-157-54-15-nexthop-Internet|157.54.0.0/15|Internet|
|Mi-157-56-14-nexthop-Internet|157.56.0.0/14|Internet|
|Mi-157-60-16-nexthop-Internet|157.60.0.0/16|Internet|
|Mi-167-105-16-nexthop-Internet|167.105.0.0/16|Internet|
|Mi-167-220-16-nexthop-Internet|167.220.0.0/16|Internet|
|Mi-168-61-16-nexthop-Internet|168.61.0.0/16|Internet|
|Mi-168-62-15-nexthop-Internet|168.62.0.0/15|Internet|
|Mi-191-232-13-nexthop-Internet|191.232.0.0/13|Internet|
|Mi-192-32-16-nexthop-Internet|192.32.0.0/16|Internet|
|Mi-192-48-225-24-nexthop-Internet|192.48.225.0/24|Internet|
|Mi-192-84-159-24-nexthop-Internet|192.84.159.0/24|Internet|
|Mi-192-84-160-23-nexthop-Internet|192.84.160.0/23|Internet|
|Mi-192-197-157-24-nexthop-Internet|192.197.157.0/24|Internet|
|Mi-193-149-64-19-nexthop-Internet|193.149.64.0/19|Internet|
|Mi-193-221-113-24-nexthop-Internet|193.221.113.0/24|Internet|
|Mi-194-69-96-19-nexthop-Internet|194.69.96.0/19|Internet|
|Mi-194-110-197-24-nexthop-Internet|194.110.197.0/24|Internet|
|Mi-198-105-232-22-nexthop-Internet|198.105.232.0/22|Internet|
|Mi-198-200-130-24-nexthop-Internet|198.200.130.0/24|Internet|
|Mi-198-206-164-24-nexthop-Internet|198.206.164.0/24|Internet|
|Mi-199-60-28-24-nexthop-Internet|199.60.28.0/24|Internet|
|Mi-199-74-210-24-nexthop-Internet|199.74.210.0/24|Internet|
|Mi-199-103-90-23-nexthop-Internet|199.103.90.0/23|Internet|
|Mi-199-103-122-24-nexthop-Internet|199.103.122.0/24|Internet|
|Mi-199-242-32-20-nexthop-Internet|199.242.32.0/20|Internet|
|Mi-199-242-48-21-nexthop-Internet|199.242.48.0/21|Internet|
|Mi-202-89-224-20-nexthop-Internet|202.89.224.0/20|Internet|
|Mi-204-13-120-21-nexthop-Internet|204.13.120.0/21|Internet|
|Mi-204-14-180-22-nexthop-Internet|204.14.180.0/22|Internet|
|Mi-204-79-135-24-nexthop-Internet|204.79.135.0/24|Internet|
|Mi-204-79-179-24-nexthop-Internet|204.79.179.0/24|Internet|
|Mi-204-79-181-24-nexthop-Internet|204.79.181.0/24|Internet|
|Mi-204-79-188-24-nexthop-Internet|204.79.188.0/24|Internet|
|Mi-204-79-195-24-nexthop-Internet|204.79.195.0/24|Internet|
|Mi-204-79-196-23-nexthop-Internet|204.79.196.0/23|Internet|
|Mi-204-79-252-24-nexthop-Internet|204.79.252.0/24|Internet|
|Mi-204-152-18-23-nexthop-Internet|204.152.18.0/23|Internet|
|Mi-204-152-140-23-nexthop-Internet|204.152.140.0/23|Internet|
|Mi-204-231-192-24-nexthop-Internet|204.231.192.0/24|Internet|
|Mi-204-231-194-23-nexthop-Internet|204.231.194.0/23|Internet|
|Mi-204-231-197-24-nexthop-Internet|204.231.197.0/24|Internet|
|Mi-204-231-198-23-nexthop-Internet|204.231.198.0/23|Internet|
|Mi-204-231-200-21-nexthop-Internet|204.231.200.0/21|Internet|
|Mi-204-231-208-20-nexthop-Internet|204.231.208.0/20|Internet|
|Mi-204-231-236-24-nexthop-Internet|204.231.236.0/24|Internet|
|Mi-205-174-224-20-nexthop-Internet|205.174.224.0/20|Internet|
|Mi-206-138-168-21-nexthop-Internet|206.138.168.0/21|Internet|
|Mi-206-191-224-19-nexthop-Internet|206.191.224.0/19|Internet|
|Mi-207-46-16-nexthop-Internet|207.46.0.0/16|Internet|
|Mi-207-68-128-18-nexthop-Internet|207.68.128.0/18|Internet|
|Mi-208-68-136-21-nexthop-Internet|208.68.136.0/21|Internet|
|Mi-208-76-44-22-nexthop-Internet|208.76.44.0/22|Internet|
|Mi-208-84-21-nexthop-Internet|208.84.0.0/21|Internet|
|Mi-209-240-192-19-nexthop-Internet|209.240.192.0/19|Internet|
|Mi-213-199-128-18-nexthop-Internet|213.199.128.0/18|Internet|
|Mi-216-32-180-22-nexthop-Internet|216.32.180.0/22|Internet|
|Mi-216-220-208-20-nexthop-Internet|216.220.208.0/20|Internet|
|mi-23-96-13-nexthop-Internet|23.96.0.0/13|Internet|
|Mi-42-159-16-nexthop-Internet|42.159.0.0/16|Internet|
|Mi-51-13-17-nexthop-Internet|51.13.0.0/17|Internet|
|Mi-51-107-16-nexthop-Internet|51.107.0.0/16|Internet|
|Mi-51-116-16-nexthop-Internet|51.116.0.0/16|Internet|
|Mi-51-120-16-nexthop-Internet|51.120.0.0/16|Internet|
|Mi-51-120-128-17-nexthop-Internet|51.120.128.0/17|Internet|
|Mi-51-124-16-nexthop-Internet|51.124.0.0/16|Internet|
|Mi-102-37-18-nexthop-Internet|102.37.0.0/18|Internet|
|Mi-102-133-16-nexthop-Internet|102.133.0.0/16|Internet|
|Mi-199-30-16-20-nexthop-Internet|199.30.16.0/20|Internet|
|Mi-204-79-180-24-nexthop-Internet|204.79.180.0/24|Internet|
||||

## <a name="next-steps"></a>További lépések

- Az áttekintést lásd: [Mi az az Azure SQL felügyelt példánya?](sql-managed-instance-paas-overview.md).
- Ismerje meg, hogyan [állíthat be egy új Azure-beli virtuális hálózatot](virtual-network-subnet-create-arm-template.md) vagy egy [meglévő Azure-beli virtuális hálózatot](vnet-existing-add-subnet.md) , AMELYEN telepítheti az SQL felügyelt példányait.
- A felügyelt SQL-példányok üzembe helyezéséhez használandó [alhálózat méretének kiszámítása](vnet-subnet-determine-size.md) .
- Ismerje meg, hogyan hozhat létre SQL-alapú felügyelt példányt:
  - A [Azure Portal](instance-create-quickstart.md).
  - A [PowerShell](scripts/create-configure-managed-instance-powershell.md)használatával.
  - [Azure Resource Manager sablon](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)használatával.
  - [Egy Azure Resource Manager-sablon használatával (az JumpBox használatával, a SSMS tartalmazza)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/).
