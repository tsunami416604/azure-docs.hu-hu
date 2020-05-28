---
title: A szükséges alhálózati méret meghatározása & tartományban
titleSuffix: Azure SQL Managed Instance
description: Ez a témakör azt ismerteti, hogyan számítható ki az az alhálózat, amelyben az Azure SQL felügyelt példányai lesznek telepítve.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 5eebde1fc95cb50f8ff7c13b6cbc411484ddf943
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050932"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>A szükséges alhálózati méret meghatározása & az Azure SQL felügyelt példányának tartománya
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányát egy Azure-beli [virtuális hálózaton (VNet)](../../virtual-network/virtual-networks-overview.md)belül kell üzembe helyezni.

A VNet alhálózatán üzembe helyezhető SQL felügyelt példányok száma az alhálózat méretétől (alhálózat-tartomány) függ.

SQL felügyelt példány létrehozásakor az Azure számos virtuális gépet foglal le a kiépítés során kiválasztott szintjétől függően. Mivel ezek a virtuális gépek az alhálózathoz vannak társítva, IP-címeket igényelnek. A normál működés és a szolgáltatás karbantartása során a magas rendelkezésre állás biztosítása érdekében az Azure további virtuális gépeket foglalhat le. Ennek eredményeképpen az alhálózat kötelező IP-címeinek száma nagyobb, mint az adott alhálózat SQL felügyelt példányainak száma.

A tervezés szerint egy SQL felügyelt példánynak legalább 16 IP-címnek kell lennie egy alhálózatban, és akár 256 IP-címet is használhat. Ennek eredményeképpen az alhálózati IP-címtartományok definiálásakor a/28 és/24 alhálózati maszkok is használhatók. A hálózati maszk bit/28 (14 gazdagép/hálózat) jó méret egyetlen általános célú vagy üzleti szempontból kritikus fontosságú üzembe helyezéshez. A maszk bit/27 (a hálózaton 30 gazdagép) ideális megoldás egy több SQL-alapú felügyelt példányhoz ugyanazon a VNet belül. A/26 (62-es gazdagépek) és/24 (254 gazdagépek) bit-beállításai lehetővé teszik a VNet további skálázását a további SQL felügyelt példányok támogatásához.

> [!IMPORTANT]
> A 16 IP-címmel rendelkező alhálózati méret a minimálisan korlátozott, ha a skálázási művelet, például a virtuális mag-méret változása nem támogatott. A prefix/27 vagy leghosszabb előtaggal rendelkező alhálózat kiválasztása kifejezetten ajánlott.

## <a name="determine-subnet-size"></a>Alhálózat méretének meghatározása

Ha több SQL felügyelt példányt szeretne üzembe helyezni az alhálózaton belül, és az alhálózatok méretének optimalizálására van szükség, használja ezeket a paramétereket a számítások létrehozásához:

- Az Azure öt IP-címet használ az alhálózaton a saját igényeinek megfelelően
- Minden általános célú-példánynak két címnek kell lennie
- Minden üzletileg kritikus-példányhoz négy cím szükséges

**Példa**: azt tervezi, hogy három általános célú és két üzletileg kritikus SQL felügyelt példánya van. Ez azt jelenti, hogy 5 + 3 * 2 + 2 * 4 = 19 IP-címet kell használnia. Mivel az IP-címtartományok a 2. Powerben vannak meghatározva, a 32-es (2 ^ 5) IP-címekre vonatkozó IP-címtartomány szükséges. Ezért le kell foglalni az alhálózatot a/27 alhálózati maszkkal.

> [!IMPORTANT]
> A fent megjelenő számítás a további fejlesztésekkel elavulttá válik.

## <a name="next-steps"></a>További lépések

- Az áttekintést lásd: [Mi az SQL felügyelt példánya](sql-managed-instance-paas-overview.md).
- További információ az [SQL felügyelt példányának kapcsolati architektúráról](connectivity-architecture-overview.md).
- Tekintse meg, hogyan [hozhat létre olyan VNet, ahol telepíteni fogja az SQL felügyelt példányokat](virtual-network-subnet-create-arm-template.md)
- DNS-problémák esetén tekintse meg az [Egyéni DNS konfigurálása](custom-dns-configure.md) című témakört.
