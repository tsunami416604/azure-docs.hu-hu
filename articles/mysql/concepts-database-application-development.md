---
title: Alkalmazásfejlesztés – Azure Database for MySQL
description: Bevezeti azokat a tervezési szempontokat, amelyeket a fejlesztőnek követnie kell az alkalmazás kódjának a Azure Database for MySQLhoz való csatlakozásakor
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 20c6841bb596c7f4a32ef689423515a8da59a945
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541555"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Az alkalmazások fejlesztésének áttekintése Azure Database for MySQL 
Ez a cikk azokat a tervezési szempontokat ismerteti, amelyeket a fejlesztőnek követnie kell az alkalmazás kódjának Azure Database for MySQLhoz való csatlakozásakor. 

> [!TIP]
> A kiszolgálók létrehozását, kiszolgáló-alapú tűzfal létrehozását, a kiszolgáló tulajdonságainak megtekintését, az adatbázis létrehozását, valamint a Workbench és a mysql.exe használatával történő kapcsolódást és lekérdezést bemutató oktatóanyagért lásd: [az első Azure Database for MySQL-adatbázis megtervezése](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Nyelv és platform
Különböző programozási nyelvekhez és platformokhoz érhetők el kódminták. A kód mintáinak hivatkozásait itt találja: a [Azure Database for MySQLhoz való csatlakozáshoz használt kapcsolati kódtárak](concepts-connection-libraries.md)

## <a name="tools"></a>Eszközök
Azure Database for MySQL a MySQL-Közösség verzióját használja, amely kompatibilis a MySQL általános felügyeleti eszközeivel, például a Workbench vagy a MySQL segédprogramokkal, például a mysql.exe, a [phpMyAdmin](https://www.phpmyadmin.net/), a [Navicat](https://www.navicat.com/products/navicat-for-mysql)és másokkal. Használhatja a Azure Portal, az Azure CLI és a REST API-kat is az adatbázis-szolgáltatással való kommunikációhoz.

## <a name="resource-limitations"></a>Erőforrás-korlátozások
A Azure Database for MySQL két különböző mechanizmus használatával kezeli a kiszolgálók számára elérhető erőforrásokat: 
- Erőforrások irányítása.
- Korlátok betartatása.

## <a name="security"></a>Biztonság
A Azure Database for MySQL erőforrásokat biztosít a hozzáférés korlátozásához, az adatok védelméhez, a felhasználók és a szerepkörök konfigurálásához, valamint a figyelési tevékenységek létrehozásához egy MySQL-adatbázisban.

## <a name="authentication"></a>Hitelesítés
Azure Database for MySQL támogatja a felhasználók és a bejelentkezések kiszolgálói hitelesítését.

## <a name="resiliency"></a>Rugalmasság
Ha átmeneti hiba történik egy MySQL-adatbázishoz való kapcsolódáskor, a kódnak újra kell próbálkoznia a hívással. Azt javasoljuk, hogy az újrapróbálkozási logika ne használja ki az újrapróbálkozási logikát, hogy az ne haladja meg az SQL-adatbázist, és egyszerre több ügyfél próbálkozzon újra.

- Mintakód: az újrapróbálkozási logikát szemléltető kód minták esetében lásd: minták a választott nyelvhez a következő helyen: [Azure Database for MySQLhoz való csatlakozáshoz használt kapcsolati kódtárak](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Kapcsolatok kezelése
Az adatbázis-kapcsolatok korlátozott erőforrások, ezért javasoljuk a kapcsolatok ésszerű használatát, amikor a MySQL-adatbázishoz fér hozzá a jobb teljesítmény érdekében.
- Hozzáférés az adatbázishoz kapcsolati készletezés vagy állandó kapcsolatok használatával.
- Hozzáférés az adatbázishoz rövid kapcsolati élettartam használatával. 
- Használja az újrapróbálkozási logikát az alkalmazásban a kapcsolódási kísérlet során az egyidejű kapcsolatok miatti hibák észlelése érdekében, elérte a maximálisan megengedett értéket. Az újrapróbálkozási logikában állítson be egy rövid késleltetést, majd várjon egy véletlenszerű időt a további csatlakozási kísérletek előtt.