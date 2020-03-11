---
title: Felügyelt példány konfigurálása
description: Megtudhatja, hogyan konfigurálhatja és felügyelheti Azure SQL Database felügyelt példányát.
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
ms.openlocfilehash: 906ae2a970ce1d5b82302d0277ca45bd93c23011
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357620"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Felügyelt példány használata Azure SQL Database

Ebben a cikkben különböző útmutatókat, parancsfájlokat és magyarázatokat talál, amelyek segíthetnek a felügyelt példányok kezelésében és konfigurálásában.

## <a name="migration"></a>Migrálás

- [Migrálás felügyelt példányra](sql-database-managed-instance-migrate.md) – az ajánlott áttelepítési folyamat és a felügyelt példányra való áttelepítéshez szükséges eszközök megismerése.

- [TDE-tanúsítvány migrálása felügyelt példányra](sql-database-managed-instance-migrate-tde-certificate.md) – ha a SQL Server-adatbázist transzparens adattitkosítással (TDE) védik, át kell telepítenie azt a tanúsítványt, amelyet a felügyelt példányok használhatnak az Azure-ban visszaállítani kívánt biztonsági mentés visszafejtéséhez.

## <a name="network-configuration"></a>Hálózati konfiguráció

- [Felügyelt példányok alhálózat méretének meghatározása](sql-database-managed-instance-determine-size-vnet-subnet.md) – a felügyelt példány olyan alhálózatot helyez el, amely nem méretezhető, ha a benne lévő erőforrásokat hozzáadja. Ezért érdemes kiszámítani, hogy milyen IP-címtartományt kell megadni az alhálózatnak az alhálózatban telepíteni kívánt példányok számától és típusaitól függően.
- [Hozzon létre új VNet és alhálózatot egy felügyelt példányhoz](sql-database-managed-instance-create-vnet-subnet.md) – az Azure VNet és az alhálózatot, ahol telepíteni kívánja a felügyelt példányokat, az [itt ismertetett hálózati követelmények](sql-database-managed-instance-connectivity-architecture.md#network-requirements)szerint kell konfigurálni. Ebből az útmutatóból megtudhatja, hogyan hozhatja létre az új VNet és az alhálózatot megfelelően konfigurált felügyelt példányok számára.
- [Meglévő VNet és alhálózat konfigurálása felügyelt példányhoz](sql-database-managed-instance-configure-vnet-subnet.md) – ha a meglévő VNet és alhálózatot úgy szeretné konfigurálni, hogy a felügyelt példányok telepítve legyenek, itt találhatja meg a [hálózati követelményeket](sql-database-managed-instance-connectivity-architecture.md#network-requirements) ellenőrző parancsfájlt, és a követelmények szerint konfigurálja az alhálózatot.
- [Egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md) – egyéni DNS-t kell konfigurálnia, ha a felügyelt példányban lévő külső erőforrásokhoz szeretne hozzáférni az adatbázisbeli levelezési profilok csatolt kiszolgálóján keresztül.
- [Hálózati konfiguráció szinkronizálása](sql-database-managed-instance-sync-network-configuration.md) – előfordulhat, hogy bár az [alkalmazást Azure-Virtual Network integrálta](../app-service/web-sites-integrate-with-vnet.md), a felügyelt&#39;példányokhoz való kapcsolódást nem lehet létrehozni. Az egyik lehetőség, hogy megpróbáljuk frissíteni a szolgáltatási csomag hálózati konfigurációját.
- [Felügyeleti VÉGPONT IP-címének keresése](sql-database-managed-instance-find-management-endpoint-ip-address.md) – a felügyelt példány nyilvános végpontot használ felügyeleti célokra. A felügyeleti végpont IP-címét az itt ismertetett parancsfájl használatával határozhatja meg.
- A [beépített tűzfalbeállítások ellenőrzése](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – a felügyelt példány olyan beépített tűzfallal védett, amely csak a szükséges portokon engedélyezi a forgalmat. Az útmutatóban ismertetett parancsfájllal ellenőrizheti és ellenőrizheti a beépített tűzfalszabályok szabályait.
- Az [alkalmazások összekötése](sql-database-managed-instance-connect-app.md) – a felügyelt példány a saját privát Azure-VNet kerül, privát IP-címmel. Ismerje meg az alkalmazások felügyelt példányhoz való csatlakoztatásának különböző mintáit.

## <a name="feature-configuration"></a>Szolgáltatás konfigurációja

- A [tranzakciós replikáció](replication-with-sql-database-managed-instance.md) lehetővé teszi, hogy az adatait felügyelt példányok között, vagy a helyszíni SQL Server egy felügyelt példányra replikálja, és fordítva. További információ a tranzakciós replikáció használatáról és konfigurálásáról ebben az útmutatóban.
- A [fenyegetések észlelésének konfigurálása](sql-database-managed-instance-threat-detection.md) – a [fenyegetések észlelése](sql-database-threat-detection-overview.md) egy beépített Azure SQL Database funkció, amely különböző lehetséges támadásokat, például SQL-befecskendezést vagy gyanús helyekről való hozzáférést észlel. Ebből az útmutatóból megtudhatja, hogyan engedélyezheti és konfigurálhatja a [fenyegetések észlelését](sql-database-threat-detection-overview.md) felügyelt példányok esetén.

## <a name="next-steps"></a>Következő lépések

- További információ [az önálló adatbázisok](sql-database-howto-single-database.md) útmutatóinak megismeréséről