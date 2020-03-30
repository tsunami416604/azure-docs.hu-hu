---
title: Felügyelt példány határozza meg a virtuális hálózat/alhálózat méretét
description: Ez a témakör azt ismerteti, hogyan számíthatja ki az alhálózat méretét, ahol az Azure SQL Database felügyelt példányok lesznek telepítve.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 7f0ef26343284b7b668e71676114586f4bec8b9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825759"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Virtuálishálózat-alhálózat méretének meghatározása az Azure SQL-adatbázis felügyelt példányához

Az Azure SQL Database felügyelt példányát egy Azure [virtuális hálózaton (VNet)](../virtual-network/virtual-networks-overview.md)kell telepíteni.

A virtuális hálózat alhálózatában telepíthető felügyelt példányok száma az alhálózat (alhálózati tartomány) méretétől függ.

Felügyelt példány létrehozásakor az Azure a kiépítés során kiválasztott szinttől függően számos virtuális gépet foglal le. Mivel ezek a virtuális gépek az alhálózathoz vannak társítva, IP-címeket igényelnek. A rendszeres üzemeltetés és a szolgáltatáskarbantartás során rendelkezésre álló magas rendelkezésre állás biztosítása érdekében az Azure további virtuális gépeket rendelhet ki. Ennek eredményeképpen az alhálózatban szükséges IP-címek száma nagyobb, mint az adott alhálózatban lévő felügyelt példányok száma.

A felügyelt példánynak legalább 16 IP-címre van szüksége egy alhálózatban, és legfeljebb 256 IP-címet használhat. Ennek eredményeképpen a /28 és /24 közötti alhálózati maszkokat használhatja az alhálózati IP-tartományok meghatározásakor. A /28 hálózati maszkbit (hálózatonként 14 állomás) jó méret egyetlen általános célú vagy üzleti szempontból kritikus telepítéshez. A /27 maszkbit (hálózatonként 30 állomás) ideális egy több felügyelt példány központi telepítéséhez ugyanazon a virtuális hálózaton belül. A /26 (62 állomás) és a /24 (254 állomás) maszkbitbeállításai további felügyelt példányok támogatásához lehetővé teszik a virtuális hálózat további horizontális kibővítését.

> [!IMPORTANT]
> A 16 IP-című alhálózati méret a minimális, korlátozott potenciállal rendelkező, ahol a skálázási művelet, például a virtuálismag méretének módosítása nem támogatott. Erősen ajánlott a /27 előtaggal vagy a leghosszabb előtaggal rendelkező alhálózat kiválasztása.

## <a name="determine-subnet-size"></a>Alhálózat méretének meghatározása

Ha több felügyelt példányt kíván telepíteni az alhálózaton belül, és optimalizálnia kell az alhálózat méretét, használja ezeket a paramétereket számításhoz:

- Az Azure öt IP-címet használ az alhálózatban a saját igényeinek megfelelően
- Minden általános célú példánynak két címre van szüksége
- Minden üzleti legkritikusabb példánynak négy címre van szüksége

**Példa:** Három általános célú és két kritikus fontosságú üzleti felügyelt példányt szeretne használni. Ez azt jelenti, hogy 5 + 3 * 2 + 2 * 4 = 19 IP-címre van szüksége. Mivel az IP-tartományok 2-es teljesítményben vannak definiálva, 32 (2^5) IP-cím IP-tartományra van szükség. Ezért le kell foglalnia az alhálózatot /27 alhálózati maszkkal.

> [!IMPORTANT]
> A fenti számítások további fejlesztésekkel elavulttá válnak.

## <a name="next-steps"></a>További lépések

- A Mi a felügyelt példány című témakörben talál [áttekintést.](sql-database-managed-instance.md)
- További információ [a Felügyelt példány kapcsolatarchitektúrájáról.](sql-database-managed-instance-connectivity-architecture.md)
- Tekintse meg, hogyan [hozhat létre virtuális hálózatot, ahol felügyelt példányokat telepíthet](sql-database-managed-instance-create-vnet-subnet.md)
- A DNS-sel kapcsolatos problémákról az [Egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md) című témakörben van.
