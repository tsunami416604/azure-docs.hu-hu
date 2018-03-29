---
title: Támogatott verziók a MySQL az Azure-adatbázis
description: A témakör ismerteti a támogatott verziók Azure adatbázis MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/22/2018
ms.openlocfilehash: cfebdbe7485f0ffaa15828803d72c2a3f97c118d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Támogatott Azure-adatbázis a MySQL server-verziók
A MySQL adatbázis Azure identitáskezelési [MySQL Community Edition](https://www.mysql.com/products/community/), a InnoDB használata.  Azure MySQL-adatbázis jelenleg az alábbi verzióit támogatja:

## <a name="mysql-version-5638"></a>MySQL-verzió 5.6.38
Tekintse meg a MySQL [dokumentáció](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-38.html) további fejlesztések és MySQL 5.6.38 javításaival kapcsolatos.

## <a name="mysql-version-5720"></a>MySQL-verzió 5.7.20
Tekintse meg a MySQL [dokumentáció](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-20.htmll) fejlesztései és javításaival kapcsolatos további a MySQL 5.7.20.

> [!NOTE]
> A szolgáltatás egy átjáró használatával a kapcsolatok átirányítása server-példányokat. A kapcsolat létrejötte után a MySQL-ügyfél verziószáma MySQL állítsa be az átjáró, a tényleges verzió a MySQL server-példányon futnak. A MySQL kiszolgálópéldány verziójának meghatározásához használja a `SELECT VERSION();` parancsot a MySQL-parancssorba. 

## <a name="managing-updates-and-upgrades"></a>Kezelése frissítések és verziófrissítések
A szolgáltatás automatikusan kezeli a alverzió frissítések javítását. Főverzió frissítések nem támogatottak (például) MySQL 5.6 verzióra történő frissítése MySQL 5.7).

## <a name="next-steps"></a>További lépések

Adott erőforrásokra vonatkozó információkból tájékoódhat kvótái és korlátai alapján a **szolgáltatásréteg**, lásd: [Szolgáltatásszinteken](./concepts-pricing-tiers.md)
