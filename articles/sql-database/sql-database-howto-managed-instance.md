---
title: Az Azure SQL Database felügyelt példányain konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja és kezelheti az Azure SQL Database felügyelt példányain.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: 10a33ac09b5cfa588bef88e6c1587d67036b1aef
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53440491"
---
# <a name="how-to-use-managed-instance"></a>Felügyelt példány használatával

Ebben a szakaszban található különböző útmutatók, a parancsfájlokat és a magyarázat, amelyek segítségével kezelheti és konfigurálhatja az Azure SQL Database – felügyelt példány.

## <a name="migration"></a>Migrálás

- [Migrálás felügyelt példányra](sql-database-managed-instance-migrate.md) – további információ a javasolt áttelepítési folyamat és az eszközök felügyelt példányokra való áttelepítéshez.

- [TDE cert át a felügyelt példánynak](sql-database-managed-instance-migrate-tde-certificate.md) – Ha az SQL Server-adatbázis védelme transzparens adattitkosítás (TDE), a tanúsítványt, amelyet a felügyelt példány használatával visszafejtésére használt biztonsági másolatot vissza kívánja állítani az Azure migrate kell.

## <a name="network-configuration"></a>Hálózati konfiguráció

- [Felügyelt példány alhálózatára méretének meghatározásához](sql-database-managed-instance-determine-size-vnet-subnet.md) – felügyelt példány kerül a megjelenítésének szenteli alhálózatot, amelyet nem méretezhetők, miután belül az erőforrásokat. Ezért kell alapján számítja ki, milyen IP-címek tartományát az alhálózat számát és típusát, hogy az alhálózat végzi az üzembe helyezést kívánt példányok függően szükség lehet.
- [Felügyelt példány létrehozása új virtuális hálózatot és alhálózatot](sql-database-managed-instance-create-vnet-subnet.md) – Azure virtuális hálózatot és alhálózatot, ahol a felügyelt példányok üzembe szeretné konfigurálni kell a következők szerint a [hálózati követelmények az itt leírtak szerint](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Ebben az útmutatóban talál a legegyszerűbben úgy, hogy az új virtuális hálózat és alhálózat megfelelően konfigurálva a felügyelt példány létrehozása.
- [Meglévő virtuális hálózatot és alhálózatot konfigurálása a felügyelt példány](sql-database-managed-instance-configure-vnet-subnet.md) – Ha meg szeretné konfigurálni a meglévő virtuális hálózatot és alhálózatot, belül a felügyelt példányok üzembe helyezéséhez, itt találja a parancsfájl, amely ellenőrzi a [hálózati követelmények](sql-database-managed-instance-connectivity-architecture.md#network-requirements) és Ellenőrizze az alhálózat az igényeknek megfelelően konfigurálja.
- [Egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md) – egyéni DNS konfigurálása, ha az egyéni tartományok a külső erőforrások elérését a felügyelt példány db e-mail profilok csatolt kiszolgálón keresztül kell.
- [Hálózati konfigurációja szinkronizálása](sql-database-managed-instance-sync-network-configuration.md) – Ez akkor fordulhat elő, hogy bár, [az alkalmazás integrálva van az Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md), is&#39;t felügyelt példány kapcsolatot létesíteni. Próbálja meg egyik dolog, hogy a szolgáltatáscsomag fürttaghálózati konfiguráció frissítése.
- [Felügyeleti végpont IP-címének](sql-database-managed-instance-find-management-endpoint-ip-address.md) – felügyelt példány felügyeleti célból – csak használja a nyilvános végponthoz. Megállapíthatja, hogy a felügyeleti végponthoz a szkripttel leírt itt IP-címét.
- [Beépített tűzfal általi védelem ellenőrzése](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – felügyelt példány beépített, amely engedélyezi a forgalmat csak a szükséges portokat tűzfal védi. Ellenőrizze, és ellenőrizze a beépített tűzfalszabályok a jelen útmutatóban leírt parancsfájl használatával.
- [Alkalmazások csatlakoztatása](sql-database-managed-instance-connect-app.md) – felügyelt példány a saját privát Azure magánhálózati IP-címmel rendelkező virtuális hálózat el van helyezve. További információ a különböző minták biztosíthat a felügyelt példány az alkalmazások összekapcsolására.

## <a name="feature-configuration"></a>Szolgáltatás konfigurálása

- [Tranzakciós replikáció](replication-with-sql-database-managed-instance.md) lehetővé teszi az adatok replikálása a felügyelt példányok között, vagy a helyszíni SQL Serverről egy felügyelt példányra, és ez fordítva is igaz. További információ használja, és a tranzakciós replikáció konfigurálása ebben az útmutatóban találja.
- [Fenyegetésészlelés konfigurálása](sql-database-managed-instance-threat-detection.md) – [fenyegetésészlelés](sql-database-threat-detection-overview.md) beépített Azure SQL Database szolgáltatása, amely észleli a különféle potenciális támadások, például az SQL-injektálás vagy gyanús helyekről való hozzáférés. Ebben az útmutatóban azt is megtudhatja, hogyan engedélyezheti és konfigurálhatja [fenyegetésészlelés](sql-database-threat-detection-overview.md) felügyelt példány számára.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [útmutató végigvezeti az önálló adatbázis](sql-database-howto-single-database.md)