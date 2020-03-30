---
title: Alkalmazásfejlesztés – Azure Database for MySQL
description: Olyan tervezési szempontokat mutat be, amelyeket a fejlesztőknek követniük kell az Azure Database for MySQL-hez való csatlakozáshoz való alkalmazáskód írásakor
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 02ce6b00b6555f849d162b9f3b381c0ab358d712
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532841"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Alkalmazásfejlesztési áttekintés az Azure Database for MySQL-hez 
Ez a cikk ismerteti a tervezési szempontokat, amelyeket a fejlesztőnek követnie kell az Azure Database for MySQL-hez való csatlakozáshoz való csatlakozáshoz. 

> [!TIP]
> A kiszolgáló létrehozásáról, a kiszolgálóalapú tűzfal létrehozásáról, a kiszolgáló tulajdonságainak megtekintéséről, az adatbázis létrehozásáról, valamint a munkaterület és a mysql.exe használatával történő csatlakozásról és lekérdezésről a Következő témakörben található: [Tervezés az első Azure-adatbázis A MySQL-adatbázishoz](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Nyelv és platform
Különböző programozási nyelvekhez és platformokhoz érhetők el kódminták. A kódmintákra mutató hivatkozásokat a következő helyen találja: [A MySQL-hez készült Azure Database-hez való csatlakozáshoz használt kapcsolódási könyvtárak](concepts-connection-libraries.md)

## <a name="tools"></a>Eszközök
Az Azure Database for MySQL a MySQL közösségi verzióját használja, amely kompatibilis a MySQL közös felügyeleti eszközeivel, például a Workbench vagy a MySQL segédprogramokkal, például a mysql.exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql)és mások. Az Azure Portal, az Azure CLI és a REST API-k is használhatja az adatbázis-szolgáltatással való interakciót.

## <a name="resource-limitations"></a>Erőforrás-korlátozások
Az Azure Database for MySQL két különböző mechanizmus használatával kezeli a kiszolgáló számára elérhető erőforrásokat: 
- Erőforrások irányítása.
- A határértékek betartatása.

## <a name="security"></a>Biztonság
Az Azure Database for MySQL erőforrásokat biztosít a hozzáférés korlátozására, az adatok védelmére, a felhasználók és szerepkörök konfigurálására, valamint a MySQL-adatbázis tevékenységeinek figyelésére.

## <a name="authentication"></a>Hitelesítés
Az Azure Database for MySQL támogatja a felhasználók kiszolgálói hitelesítését és a bejelentkezéseket.

## <a name="resiliency"></a>Rugalmasság
Ha egy Tranziens hiba történik, miközben egy MySQL-adatbázishoz csatlakozik, a kód nak újra meg kell próbálnia a hívást. Azt javasoljuk, hogy az újrapróbálkozási logika használja vissza a logikát, hogy ne terhelje el az SQL-adatbázis több ügyfél újrapróbálkozás egyidejűleg.

- Kódminták: Az újrapróbálkozási logikát szemléltető kódmintákat lásd az Ön által választott nyelvről a következő helyen: [A MySQL-hez készült Azure Database-hez való csatlakozáshoz használt kapcsolattárak](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Kapcsolatok kezelése
Az adatbázis-kapcsolatok korlátozott erőforrást jelentenek, ezért a jobb teljesítmény elérése érdekében javasoljuk a kapcsolatok ésszerű használatát a MySQL-adatbázis elérésekor.
- Az adatbázis elérése kapcsolatkészletezéssel vagy állandó kapcsolatokkal.
- Az adatbázis elérése rövid kapcsolati élettartam használatával. 
- Használja az újrapróbálkozási logikát az alkalmazásban a csatlakozási kísérlet azon pontján, hogy az egyidejű kapcsolatokból eredő hibák at a megengedett maximális értéket érjék el. Az újrapróbálkozási logikában állítson be egy rövid késleltetést, majd várjon egy véletlenszerű időt a további csatlakozási kísérletek előtt.