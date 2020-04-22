---
title: Biztonságos felügyelt példány nyilvános végpontjai
description: Nyilvános végpontok biztonságos használata az Azure-ban felügyelt példányokkal
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/08/2019
ms.openlocfilehash: f11c19ba33ee2fbae0fef265371bedad2fe29cb7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684957"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Azure SQL Database felügyelt példányának biztonságos használata nyilvános végpontokkal

Az Azure SQL Database által felügyelt példányok felhasználói kapcsolatot biztosíthatnak [a nyilvános végpontokon](../virtual-network/virtual-network-service-endpoints-overview.md)keresztül. Ez a cikk bemutatja, hogyan teheti ezt a konfigurációt biztonságosabbá.

## <a name="scenarios"></a>Forgatókönyvek

Az SQL Database felügyelt példánya egy privát végpontot biztosít a virtuális hálózaton belüli kapcsolat engedélyezéséhez. Az alapértelmezett beállítás a maximális elkülönítés biztosítása. Vannak azonban olyan esetek, ahol nyilvános végponti kapcsolatot kell biztosítania:

- A felügyelt példánynak integrálnia kell a több-bérlős platform-szolgáltatásként (PaaS) ajánlatok.
- Az adatcsere nagyobb átviteli igényére van szüksége, mint amennyi VPN használata esetén lehetséges.
- A vállalati irányelvek tiltják a PaaS-t a vállalati hálózatokon belül.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Felügyelt példány telepítése nyilvános végpont-hozzáféréshez

Bár nem kötelező, a nyilvános végpont-hozzáféréssel rendelkező felügyelt példány közös központi telepítési modellje a példány dedikált elkülönített virtuális hálózatban történő létrehozása. Ebben a konfigurációban a virtuális hálózat csak a virtuális fürt elkülönítésére szolgál. Nem számít, hogy a felügyelt példány IP-címterülete átfedésben van-e a vállalati hálózat IP-címterével.

## <a name="secure-data-in-motion"></a>Biztonságos mozgásban lévő adatok

A felügyelt példány adatforgalma mindig titkosítva van, ha az ügyfél-illesztőprogram támogatja a titkosítást. A felügyelt példány és más Azure-virtuális gépek vagy Azure-szolgáltatások között küldött adatok soha nem hagyja el az Azure gerincét. Ha kapcsolat van a felügyelt példány és a helyszíni hálózat között, javasoljuk, hogy használja az Azure ExpressRoute-ot. Az ExpressRoute segítségével elkerülheti az adatok nyilvános interneten keresztüli áthelyezését. Felügyelt példány privát kapcsolat, csak privát társviszony-létesítés használható.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Bejövő és kimenő kapcsolatok zárolása

Az alábbi ábra az ajánlott biztonsági konfigurációkat mutatja be:

![A bejövő és kimenő kapcsolatok zárolására szolgáló biztonsági konfigurációk](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

A felügyelt példány [dedikált nyilvános végpontcímmel rendelkezik.](sql-database-managed-instance-find-management-endpoint-ip-address.md) Az ügyféloldali kimenő tűzfalban és a hálózati biztonsági csoport szabályaiban állítsa be ezt a nyilvános végpont IP-címét a kimenő kapcsolat korlátozására.

Annak érdekében, hogy a felügyelt példány hoz való forgalom megbízható forrásokból származzon, javasoljuk, hogy jól ismert IP-címekkel rendelkező forrásokból csatlakozzon. Használjon hálózati biztonsági csoportot a 3342-es porton lévő felügyelt példány nyilvános végpontjához való hozzáférés korlátozására.

Ha az ügyfeleknek kapcsolatot kell kezdeményezniük egy helyszíni hálózatról, győződjön meg arról, hogy az eredeti cím egy jól ismert IP-címkészletre van lefordítva. Ha ezt nem tudja megtenni (például egy mobil munkaerő egy tipikus forgatókönyv), javasoljuk, [hogy használjon pont-hely VPN-kapcsolatokat és egy privát végpontot.](sql-database-managed-instance-configure-p2s.md)

Ha a kapcsolatok az Azure-ból indulnak, azt javasoljuk, hogy a forgalom egy jól ismert [virtuális IP-címről](../virtual-network/virtual-networks-reserved-public-ip.md) (például egy virtuális gépről) származik. A virtuális IP-címek (VIP) kezelésének megkönnyítése érdekében érdemes [lehet nyilvános IP-címelőtagokat](../virtual-network/public-ip-address-prefix.md)használni.

## <a name="next-steps"></a>További lépések

- A nyilvános végpont konfigurálása a példányok kezeléséhez: [Nyilvános végpont konfigurálása](sql-database-managed-instance-public-endpoint-configure.md)
