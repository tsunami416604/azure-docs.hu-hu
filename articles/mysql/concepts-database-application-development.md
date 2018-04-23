---
title: Adatbázis alkalmazások fejlesztése MySQL az Azure-adatbázis – áttekintés
description: Bemutatja, hogy a fejlesztő kell követnie, MySQL Azure adatbázishoz való kapcsolódáshoz alkalmazáskód írásakor kialakítási szempontok
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 1a3f517221c7e22d87dec5d0fc6f11c1bed16505
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Alkalmazások fejlesztése MySQL az Azure-adatbázis – áttekintés 
A cikk ismerteti, hogy a fejlesztő kell követnie, MySQL Azure adatbázishoz való kapcsolódáshoz alkalmazáskód írásakor tervezési szempontokat. 

> [!TIP]
> Az oktatóanyag bemutatja, hogyan hozzon létre egy kiszolgálót, hozzon létre egy kiszolgáló-alapú tűzfal, kiszolgáló tulajdonságainak megtekintése, adatbázist, és csatlakozzon és munkaterület és mysql.exe lekérdezése, lásd: [MySQL-adatbázis az első Azure-adatbázis megtervezése](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Nyelv és platform
Különböző programozási nyelvekhez és platformokhoz érhetők el kódminták. A következő mintakódok hivatkozásait megtalálhatja: [MySQL Azure adatbázishoz való kapcsolódáshoz használt kapcsolódási függvénytárak](concepts-connection-libraries.md)

## <a name="tools"></a>Eszközök
Azure MySQL-adatbázis MySQL közösségi verzióját használja, MySQL munkaterület vagy MySQL segédprogramok mysql.exe, mint például az általános kezelőeszközöket kompatibilis [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql), stb. Az Azure portál, az Azure CLI és a REST API-k használatával is kommunikálni az adatbázis-szolgáltatás.

## <a name="resource-limitations"></a>Erőforrás-korlátozások
Azure MySQL-adatbázis két különböző mechanizmusok használatával kezeli a kiszolgáló számára elérhető erőforrások: 
- Erőforrások irányítás.
- A korlátozások érvényesítése.

## <a name="security"></a>Biztonság
Azure MySQL-adatbázis korlátozó hozzáférési, védelmet nyújtó adatok, konfigurálás felhasználók és szerepkörök és MySQL-adatbázis a figyelési tevékenységek forrásokat biztosít.

## <a name="authentication"></a>Hitelesítés
Azure MySQL-adatbázis támogatja a kiszolgáló hitelesítése a felhasználók és bejelentkezések.

## <a name="resiliency"></a>Resiliency
Amikor egy átmeneti hiba akkor fordul elő, amikor kapcsolódni próbált MySQL-adatbázis, a kódot kell próbálja meg újra a hívást. Javasoljuk, hogy a az újrapróbálkozási logika leállás logika használjon, így azt nem ne terhelje tovább az SQL-adatbázis az újrapróbálkozás egyszerre több ügyfélnek.

- Kódminták: mintakódok, mely újrapróbálkozási logika, lásd: a következő nyelvű-példák: [MySQL Azure adatbázishoz való kapcsolódáshoz használt kapcsolódási függvénytárak](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Kapcsolatok kezelése
Adatbázis-kapcsolatok egy korlátozott erőforrás, a jobb teljesítmény érdekében a MySQL-adatbázis elérésekor kapcsolat ésszerű használata javasolt.
- Kapcsolatkészletezést vagy állandó kapcsolat használatával érik el az adatbázist.
- Rövid kapcsolat élettartamát használatával érik el az adatbázist. 
- Használjon újrapróbálkozási logika az alkalmazásban való egyidejű kapcsolatok eredő hibák dolgozza fel a kapcsolódási kísérlet helyén elérte a megengedett. Az újrapróbálkozási logika be egy rövid ideig eltart, majd várjon véletlenszerű ideje előtt a további csatlakozási kísérletek.