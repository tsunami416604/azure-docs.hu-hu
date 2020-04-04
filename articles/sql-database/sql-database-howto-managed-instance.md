---
title: Felügyelt példány konfigurálása
description: Ismerje meg, hogyan konfigurálhatja és kezelheti az Azure SQL Database felügyelt példányát.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: b56375388f6293d27bcd2f2548d8b20205a92b15
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80638034"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Felügyelt példány használata az Azure SQL Database-ben

Ebben a cikkben különböző útmutatókat, parancsfájlokat és magyarázatokat talál, amelyek segíthetnek a felügyelt példány kezelésében és konfigurálásában.

## <a name="migration"></a>Migrálás

- [Áttelepítés felügyelt példányra](sql-database-managed-instance-migrate.md) – Ismerje meg az ajánlott áttelepítési folyamatot és a felügyelt példányba való áttelepítéshez szükséges eszközöket.

- [TDE-tanúsítvány áttelepítése felügyelt példányba](sql-database-managed-instance-migrate-tde-certificate.md) – Ha az SQL Server-adatbázis átlátszó adattitkosítással (TDE) védett, akkor át kell telepítenie azokat a tanúsítványokat, amelyet egy felügyelt példány használhat az Azure-ban visszaállítani kívánt biztonsági másolat visszafejtéséhez.

## <a name="network-configuration"></a>Hálózati konfiguráció

- [A felügyelt példány alhálózatának méretét határozza meg](sql-database-managed-instance-determine-size-vnet-subnet.md) – A felügyelt példány olyan alhálózatba kerül, amely nem méretezhető át, miután hozzáadja a belső erőforrásokat. Ezért ki kell számítania, hogy milyen IP-címtartományra lenne szükség az alhálózathoz az alhálózatban telepíteni kívánt példányok számától és típusától függően.
- [Hozzon létre új virtuális hálózatot és alhálózatot egy felügyelt példányhoz](sql-database-managed-instance-create-vnet-subnet.md) – az Azure virtuális hálózatot és az alhálózatot, ahol a felügyelt példányokat telepíteni szeretné, az [itt leírt hálózati követelményeknek](sql-database-managed-instance-connectivity-architecture.md#network-requirements)megfelelően kell konfigurálni. Ebben az útmutatóban megtalálja az új virtuális hálózat és a felügyelt példányok megfelelően konfigurált alhálózatának létrehozásának legegyszerűbb módját.
- [Konfigurálja a meglévő virtuális hálózatot és alhálózatot egy felügyelt példányhoz](sql-database-managed-instance-configure-vnet-subnet.md) – ha a meglévő virtuális hálózatot és alhálózatot a felügyelt példányok belső telepítésére szeretné konfigurálni, itt megtalálhatja a [hálózati követelményeket](sql-database-managed-instance-connectivity-architecture.md#network-requirements) és az alhálózatot a követelményeknek megfelelően konfiguráló parancsfájlt.
- [Egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md) – egyéni DNS-t kell konfigurálnia, ha az egyéni tartományok külső erőforrásait a felügyelt példányból szeretné elérni a db mail profilok csatolt kiszolgálóján keresztül.
- [Hálózati konfiguráció szinkronizálása](sql-database-managed-instance-sync-network-configuration.md) – Előfordulhat, hogy bár [integrálta az alkalmazást egy Azure virtuális hálózattal,](../app-service/web-sites-integrate-with-vnet.md)&#39;nem hozhat létre kapcsolatot egy felügyelt példányhoz. Az egyik dolog, amit megpróbálhat, hogy frissítse a hálózati konfigurációt a szolgáltatási csomaghoz.
- [Felügyeleti végpont IP-cím keresése](sql-database-managed-instance-find-management-endpoint-ip-address.md) – A felügyelt példány nyilvános végpontot használ felügyeleti célokra. A felügyeleti végpont IP-címét az itt leírt parancsfájl segítségével határozhatja meg.
- [Ellenőrizze a beépített tűzfal védelme](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – A felügyelt példány védett beépített tűzfal, amely lehetővé teszi a forgalmat csak a szükséges portokon. A beépített tűzfalszabályokat az útmutatóban ismertetett parancsfájl segítségével ellenőrizheti és ellenőrizheti.
- [Alkalmazások csatlakoztatása](sql-database-managed-instance-connect-app.md) – A felügyelt példány a saját privát Azure-virtuális hálózatában van elhelyezve, privát IP-címmel. Ismerje meg a különböző minták at az alkalmazások csatlakoztatása a felügyelt példányhoz.

## <a name="feature-configuration"></a>Szolgáltatás konfigurációja

- [A tranzakciós replikáció](replication-with-sql-database-managed-instance.md) lehetővé teszi az adatok replikálását a felügyelt példányok között, vagy a helyszíni SQL Server rendszerből egy felügyelt példányba, és fordítva. A tranzakcióreplikáció használatáról és konfigurálásáról ebben az útmutatóban talál további információt.
- [Fenyegetésészlelés konfigurálása](sql-database-managed-instance-threat-detection.md) – [a fenyegetésészlelés](sql-database-threat-detection-overview.md) egy beépített Azure SQL Database-szolgáltatás, amely észleli a különböző potenciális támadásokat, például az SQL Injection vagy a gyanús helyekről való hozzáférést. Ebben az útmutatóban megtudhatja, hogyan engedélyezheti és konfigurálhatja a felügyelt példány [fenyegetésészlelését.](sql-database-threat-detection-overview.md)
- [Riasztások létrehozása](sql-database-managed-instance-alerts.md) lehetővé teszi, hogy riasztások beállítása a figyelt metrikák, például a CPU-kihasználtság, a tárhelyfelhasználás, IOPS és mások felügyelt példány. Ebben az útmutatóban megtudhatja, hogyan engedélyezheti és konfigurálhatja a felügyelt példány riasztásait.

## <a name="next-steps"></a>További lépések

- További információ az [egyes adatbázisok útmutatóiról](sql-database-howto-single-database.md)
