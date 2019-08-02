---
title: Azure SQL Database felügyelt példány határozza meg a VNet/alhálózat méretét | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan számítható ki az alhálózat mérete, ahol a Azure SQL Database felügyelt példányok lesznek telepítve.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 4b627b13fb79cd5105a95d9161d9239f28f2e062
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567500"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Azure SQL Database felügyelt példány VNet-alhálózati méretének meghatározása

Azure SQL Database felügyelt példányt egy Azure [-beli virtuális hálózaton (VNet)](../virtual-network/virtual-networks-overview.md)belül kell üzembe helyezni.

A VNet alhálózatán üzembe helyezhető felügyelt példányok száma az alhálózat méretétől (alhálózat-tartomány) függ.

Felügyelt példány létrehozásakor az Azure számos virtuális gépet foglal le a kiépítés során kiválasztott szintjétől függően. Mivel ezek a virtuális gépek az alhálózathoz vannak társítva, IP-címeket igényelnek. A normál működés és a szolgáltatás karbantartása során a magas rendelkezésre állás biztosítása érdekében az Azure további virtuális gépeket foglalhat le. Ennek eredményeképpen a szükséges IP-címek száma nagyobb, mint az alhálózaton lévő felügyelt példányok száma.

Kialakítás szerint a felügyelt példányoknak legalább 16 IP-címnek kell lenniük egy alhálózatban, és akár 256 IP-címet is használhatnak. Ennek eredményeképpen az alhálózati IP-címtartományok definiálásakor a/28 és/24 alhálózati maszkok is használhatók. A hálózati maszk bit/28 (14 gazdagép/hálózat) jó méret egyetlen általános célú vagy üzleti szempontból kritikus fontosságú üzembe helyezéshez. A Mask bit of/27 (hálózatonként 30 gazdagép) ideális a több felügyelt példányok ugyanazon VNet belüli üzembe helyezéséhez. A/26 (62-es gazdagépek) és/24 (254 gazdagépek) bit-beállításai lehetővé teszik a további felügyelt példányok támogatását a VNet.

> [!IMPORTANT]
> Az alhálózati méret 16 IP-címmel, amely a további felügyelt példányok vertikális felskálázásának korlátozott lehetősége. Az alhálózat kiválasztása a következő előtaggal:/27 vagy alacsonyabb.

## <a name="determine-subnet-size"></a>Alhálózat méretének meghatározása

Ha több felügyelt példányt kíván üzembe helyezni az alhálózaton belül, és az alhálózatok méretének optimalizálására van szükség, használja ezeket a paramétereket a számítások létrehozásához:

- Az Azure öt IP-címet használ az alhálózaton a saját igényeinek megfelelően
- Minden általános célú-példánynak két címnek kell lennie
- Minden üzletileg kritikus-példányhoz négy cím szükséges

**Példa**: Azt tervezi, hogy három általános célú és két üzletileg kritikus felügyelt példánya van. Ez azt jelenti, hogy 5 + 3 * 2 + 2 * 4 = 19 IP-címet kell használnia. Mivel az IP-címtartományok a 2. Powerben vannak meghatározva, a 32-es (2 ^ 5) IP-címekre vonatkozó IP-címtartomány szükséges. Ezért le kell foglalni az alhálózatot a/27 alhálózati maszkkal.

> [!IMPORTANT]
> A fent megjelenő számítás a további fejlesztésekkel elavulttá válik.

## <a name="next-steps"></a>További lépések

- Az áttekintést lásd: [Mi az a felügyelt példány](sql-database-managed-instance.md).
- További információ a [felügyelt példány kapcsolati architektúráról](sql-database-managed-instance-connectivity-architecture.md).
- Tekintse meg, hogyan [hozhat létre VNet, ahol felügyelt példányokat fog telepíteni](sql-database-managed-instance-create-vnet-subnet.md)
- DNS-problémák esetén tekintse meg az [Egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md) című témakört.
