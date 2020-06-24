---
title: A szükséges alhálózati méret meghatározása & tartományban
titleSuffix: Azure SQL Managed Instance
description: Ez a témakör azt ismerteti, hogyan számítható ki az az alhálózat, amelyben az Azure SQL felügyelt példánya telepítve lesz.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 8d1073dbcced9532390776a23dd17c1f572cce40
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84686681"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>A szükséges alhálózati méret meghatározása & az Azure SQL felügyelt példányának tartománya
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányát egy Azure-beli [virtuális hálózaton (VNet)](../../virtual-network/virtual-networks-overview.md)belül kell üzembe helyezni.

A VNet alhálózatában üzembe helyezhető felügyelt példányok száma az alhálózat méretétől függ (az alhálózat tartománya).

Felügyelt példány létrehozásakor az Azure számos virtuális gépet foglal le a kiépítés során kiválasztott szintjétől függően. Mivel ezek a virtuális gépek az alhálózathoz vannak társítva, IP-címeket igényelnek. A normál működés és a szolgáltatás karbantartása során a magas rendelkezésre állás biztosítása érdekében az Azure további virtuális gépeket foglalhat le. Ennek eredményeképpen a szükséges IP-címek száma nagyobb, mint az alhálózaton lévő felügyelt példányok száma.

Kialakítás szerint a felügyelt példányoknak legalább 16 IP-címnek kell lenniük egy alhálózatban, és akár 256 IP-címet is használhatnak. Ennek eredményeképpen az alhálózati IP-címtartományok definiálásakor a/28 és/24 alhálózati maszkok is használhatók. A hálózati maszk bit/28 (14 gazdagép/hálózat) megfelelő méretű egyetlen általános célú vagy üzleti szempontból kritikus fontosságú üzembe helyezéshez. A maszk bit/27 (a hálózaton 30 gazdagép) ideális választás több, az SQL által felügyelt példányok ugyanazon VNet belüli központi telepítéséhez. A/26 (62-es gazdagépek) és/24 (254 gazdagépek) bit-beállításai lehetővé teszik a további felügyelt példányok támogatását a VNet.

> [!IMPORTANT]
> A 16 IP-címmel rendelkező alhálózati méret a minimálisan korlátozott, ha a skálázási művelet, például a virtuális mag-méret változása nem támogatott. A prefix/27 vagy leghosszabb előtaggal rendelkező alhálózat kiválasztása kifejezetten ajánlott.

## <a name="determine-subnet-size"></a>Alhálózat méretének meghatározása

Ha több felügyelt példányt kíván üzembe helyezni az alhálózaton belül, és az alhálózatok méretének optimalizálására van szükség, használja ezeket a paramétereket a számítások létrehozásához:

- Az Azure öt IP-címet használ az alhálózaton a saját igényeinek megfelelően
- Az általános célú példányok két címet igényelnek
- Minden üzleti szempontból kritikus példányhoz négy cím szükséges

**Példa**: három általános célú és két üzleti szempontból kritikus fontosságú felügyelt példányt kell megterveznie. Ez azt jelenti, hogy 5 + 3 * 2 + 2 * 4 = 19 IP-címet kell használnia. Mivel az IP-címtartományok meghatározása 2, a 32-es (2 ^ 5) IP-cím IP-címére van szükség. Ezért le kell foglalni az alhálózatot a/27 alhálózati maszkkal.

> [!IMPORTANT]
> A fent megjelenő számítás a további fejlesztésekkel elavulttá válik.

## <a name="next-steps"></a>További lépések

- Az áttekintést lásd: [Mi az az Azure SQL felügyelt példánya?](sql-managed-instance-paas-overview.md).
- További információ az [SQL felügyelt példányának kapcsolati architektúráról](connectivity-architecture-overview.md).
- Tekintse meg, hogyan [hozhat létre olyan VNet, ahol telepíteni fogja az SQL felügyelt példányát](virtual-network-subnet-create-arm-template.md).
- DNS-problémák esetén tekintse meg az [Egyéni DNS konfigurálása](custom-dns-configure.md)című témakört.
