---
title: Konfigurálása az Azure SQL Database felügyelt példány |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja és kezelheti az Azure SQL Database felügyelt példányában.
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
ms.date: 01/25/2019
ms.openlocfilehash: 91a9f6c4b9763c3261307eb9512f0db9d6aefa89
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55477760"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Az Azure SQL Database felügyelt példány használatával

Ebben a cikkben különböző útmutatók, a parancsfájlokat és a magyarázat, amelyek segítségével kezelheti és konfigurálhatja a felügyelt példány is megtalálhatja.

## <a name="migration"></a>Migrálás

- [Migrálás felügyelt példányra](sql-database-managed-instance-migrate.md) – további információ a javasolt áttelepítési folyamat és az eszközök a migrálás felügyelt példányra.

- [Migrálás TDE cert egy felügyelt példányra](sql-database-managed-instance-migrate-tde-certificate.md) – Ha az SQL Server-adatbázis védelme transzparens adattitkosítás (TDE), a tanúsítványt, amelyet a felügyelt példány használatával visszafejtésére használt biztonsági másolatot vissza kívánja állítani az Azure migrate kell.

## <a name="network-configuration"></a>Hálózati konfiguráció

- [A felügyelt példány alhálózatára méretének meghatározásához](sql-database-managed-instance-determine-size-vnet-subnet.md) – a felügyelt példánynak el van helyezve a megjelenítésének szenteli alhálózatot, amelyet nem méretezhetők, miután belül az erőforrásokat. Ezért kell alapján számítja ki, milyen IP-címek tartományát az alhálózat számát és típusát, hogy az alhálózat végzi az üzembe helyezést kívánt példányok függően szükség lehet.
- [Hozzon létre új virtuális hálózatot és alhálózatot egy felügyelt példány](sql-database-managed-instance-create-vnet-subnet.md) – Azure virtuális hálózatot és alhálózatot, ahol a felügyelt példányok üzembe szeretné konfigurálni kell a következők szerint a [hálózati követelmények az itt leírtak szerint](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Ebben az útmutatóban talál a legegyszerűbben úgy, hogy az új virtuális hálózat és alhálózat megfelelően konfigurálva a felügyelt példány létrehozása.
- [Konfigurálja a meglévő virtuális hálózatot és alhálózatot egy felügyelt példány](sql-database-managed-instance-configure-vnet-subnet.md) – Ha meg szeretné konfigurálni a meglévő virtuális hálózatot és alhálózatot, belül a felügyelt példányok üzembe helyezéséhez, itt találja a parancsfájl, amely ellenőrzi a [hálózati követelmények](sql-database-managed-instance-connectivity-architecture.md#network-requirements) és ellenőrizze az alhálózat a követelményeinek megfelelően konfigurálja.
- [Egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md) – egyéni DNS konfigurálása, ha az egyéni tartományok a külső erőforrások elérését a felügyelt példány db e-mail profilok csatolt kiszolgálón keresztül kell.
- [Hálózati konfigurációja szinkronizálása](sql-database-managed-instance-sync-network-configuration.md) – Ez akkor fordulhat elő, hogy bár, [az alkalmazás integrálva van az Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md), is&#39;t felügyelt példány kapcsolatot létesíteni. Próbálja meg egyik dolog, hogy a szolgáltatáscsomag fürttaghálózati konfiguráció frissítése.
- [Felügyeleti végpont IP-címének](sql-database-managed-instance-find-management-endpoint-ip-address.md) – a felügyelt példánynak a felügyeleti célból – csak nyilvános végpontot használja. Megállapíthatja, hogy a felügyeleti végponthoz a szkripttel leírt itt IP-címét.
- [Beépített tűzfal általi védelem ellenőrzése](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – a felügyelt példánynak beépített, amely engedélyezi a forgalmat csak a szükséges portokat tűzfal védi. Ellenőrizze, és ellenőrizze a beépített tűzfalszabályok a jelen útmutatóban leírt parancsfájl használatával.
- [Alkalmazások csatlakoztatása](sql-database-managed-instance-connect-app.md) – a felügyelt példánynak a saját privát Azure magánhálózati IP-címmel rendelkező virtuális hálózat el van helyezve. További információ a különböző minták a felügyelt példányhoz az alkalmazások összekapcsolására.

## <a name="feature-configuration"></a>Szolgáltatás konfigurálása

- [Tranzakciós replikáció](replication-with-sql-database-managed-instance.md) lehetővé teszi az adatok replikálása a felügyelt példányok között, vagy a helyszíni SQL Serverről egy felügyelt példányra, és ez fordítva is igaz. További információ használja, és a tranzakciós replikáció konfigurálása ebben az útmutatóban találja.
- [Fenyegetésészlelés konfigurálása](sql-database-managed-instance-threat-detection.md) – [fenyegetésészlelés](sql-database-threat-detection-overview.md) beépített Azure SQL Database szolgáltatása, amely észleli a különféle potenciális támadások, például az SQL-injektálás vagy gyanús helyekről való hozzáférés. Ebben az útmutatóban azt is megtudhatja, hogyan engedélyezheti és konfigurálhatja [fenyegetésészlelés](sql-database-threat-detection-overview.md) a felügyelt példányhoz.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [útmutató végigvezeti az önálló adatbázisok](sql-database-howto-single-database.md)