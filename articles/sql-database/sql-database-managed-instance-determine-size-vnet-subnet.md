---
title: Az Azure SQL Database felügyelt példánya határozza meg a virtuális hálózat/alhálózat méretét |} A Microsoft Docs
description: Ez a témakör ismerteti, hogyan számítja ki az alhálózatot, amelyen telepíteni fogja az Azure SQL Database felügyelt példányain a méretét.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 02/22/2019
ms.openlocfilehash: cdd9f4eed30744f0eb65f8890eb1d7149f39736c
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750242"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Határozza meg a virtuális hálózat alhálózat méretét az Azure SQL Database felügyelt példánya

Az Azure SQL Database felügyelt példányain telepíteni kell egy Azure-ban [virtuális hálózat (VNet)](../virtual-network/virtual-networks-overview.md).

A virtuális hálózat alhálózatában helyezhető felügyelt példányok száma attól függ, hogy az alhálózat (alhálózati címtartomány) méretét.

Felügyelt példány létrehozásakor az Azure virtuális gépek kiépítésekor kiválasztott csomagtól függően számos foglal le. Mivel ezek a virtuális gépek társítva az alhálózat, IP-címek igényelnek. Magas rendelkezésre állásának biztosításához a normál működést és a szolgáltatás karbantartás alatt, az Azure további virtuális gépeket is kioszthat. Ennek eredményeképpen az alhálózat szükséges IP-címek száma, az alhálózat által felügyelt példányok száma nagyobb.

A kialakításból fakadóan felügyelt példány legalább 16, az alhálózat IP-címet kell, és előfordulhat, hogy legfeljebb 256 IP-cím használata. Ennek eredményeképpen egy/28-as és /24 közötti alhálózati maszkok használhatja fel, az alhálózati IP-címtartományok meghatározásakor. Egy hálózati maszk bit/28-as (hálózati / 14 gazdagép) egy megfelelő méretű egyetlen általános célú vagy üzleti szempontból kritikus fontosságú központi telepítés. A maszk bit/27 (30 gazdagép hálózatonként) ideális ugyanazon a Vneten belül több felügyelt példány üzembe helyezés. Maszk bit beállításai/26-os (62 gazdagép) és a /24 (254 gazdagép) lehetővé teszi, hogy a virtuális hálózat támogatásához további felügyelt példányok horizontális felskálázása további.

> [!IMPORTANT]
> 16 IP-címet az alhálózat mérete korlátozott lehetséges a további felügyelt példány horizontális felskálázás az operációs rendszer nélküli helyreállításra minimális. Erősen ajánlott lehetőséget választva alhálózati előtagot/27-eset vagy alá.

## <a name="determine-subnet-size"></a>Határozza meg az alhálózat mérete

Ha azt tervezi, az alhálózaton belül több felügyelt példány üzembe helyezése és az alhálózat méretét optimalizálására, a számítás használni ezeket a paramétereket:

- Az Azure az alhálózat öt IP-címet használ a saját igényei szerint
- Egyes általános célú példányok van szüksége a két cím
- Egyes üzletileg kritikus példányok kell négy címét

**Példa**: Azt tervezi, hogy három általános célú és a két üzleti az kritikus fontosságú felügyelt példányok. That means you need 5 + 3 * 2 + 2 * 4 = 19 IP addresses. IP-címtartományok 2 hatványa határozzák meg, mint az IP-címtartományt 32 kell (2 ^ 5) IP-címeket. Ezért kell lefoglalni az alhálózat/27-eset a alhálózati maszkkal.

> [!IMPORTANT]
> További fejlesztések fog elavulnak számítási fent látható.

## <a name="next-steps"></a>További lépések

- Áttekintéséhez lásd: [mit jelent a felügyelt példány](sql-database-managed-instance.md).
- Tudjon meg többet [felügyelt példány kapcsolati architektúra](sql-database-managed-instance-connectivity-architecture.md).
- Lásd: hogyan [hozzon létre virtuális hálózatot, amelyen telepíti a felügyelt példányok](sql-database-managed-instance-create-vnet-subnet.md)
- DNS-problémák esetén lásd: [egy egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md)
