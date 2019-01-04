---
title: Az Azure Database for MySQL Database alkalmazás-fejlesztésének áttekintése
description: Vezet be, hogy a fejlesztő kell követniük, csatlakozás az Azure Database for MySQL-hez való írásakor kialakításával kapcsolatos szempontok
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 946f7011c51b7c6844e023d03e01e4c2043d2578
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53544463"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Alkalmazás-fejlesztésének áttekintése az Azure Database for MySQL-hez 
Ez a cikk ismerteti a tervezési szempontoknak a fejlesztő kell követniük, csatlakozás az Azure Database for MySQL-hez való írásakor. 

> [!TIP]
> Bemutatja, hogyan hozzon létre egy kiszolgálót, kiszolgálóalapú tűzfalak létrehozásáról, kiszolgáló tulajdonságainak megtekintése, hozzon létre adatbázist, és a csatlakozás és lekérdezés a workbench és mysql.exe használatával foglalkozó oktatóanyagért lásd: [az első Azure Database for MySQL-adatbázis megtervezése](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Nyelv és platform
Különböző programozási nyelvekhez és platformokhoz érhetők el kódminták. A kódmintákra mutató hivatkozások a következő helyen találhatók: [Csatlakozás az Azure Database for MySQL-hez használt csatlakozási kódtárak](concepts-connection-libraries.md)

## <a name="tools"></a>Eszközök
Azure Database for MySQL-hez a MySQL közösségi telepített verzióját használja, kompatibilis a MySQL Workbench vagy MySQL segédprogramok mysql.exe, mint például az általános kezelőeszközöket [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql), és másokkal. Az Azure Portalon, az Azure CLI és REST API-k használatával is kezelheti az adatbázis-szolgáltatással.

## <a name="resource-limitations"></a>Erőforrás-korlátozások
Azure Database for MySQL-kiszolgáló számára elérhető erőforrások két különböző mechanizmus használatával kezeli: 
- Erőforrás-szabályozással.
- Kényszerítési korlátját.

## <a name="security"></a>Biztonság
Azure Database for MySQL korlátozó hozzáférési, adatok védelmére, konfigurálása felhasználók és szerepkörök és MySQL-adatbázis a figyelési tevékenységek forrásokat biztosít.

## <a name="authentication"></a>Hitelesítés
Azure Database for MySQL server-hitelesítés, bejelentkezések és felhasználók támogatja.

## <a name="resiliency"></a>Rugalmasság
Egy MySQL-adatbázishoz való kapcsolódás során egy átmeneti hiba akkor fordul elő, amikor a kódnak újra kell próbálkoznia a hívással. Azt javasoljuk, hogy az újrapróbálkozási logika használja visszatartási logikai úgy, hogy az ne terhelje túl az SQL-adatbázis a több ügyfél egyidejű.

- Kódminták: Kódmintákért az újrapróbálkozási logika, a nyelv, a választott mintákat: [Csatlakozás az Azure Database for MySQL-hez használt csatlakozási kódtárak](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Kapcsolatok kezelése
Adatbázis-kapcsolatok a korlátozott erőforrások, így jobb teljesítményt érhet el a MySQL-adatbázis elérésekor kapcsolatok késleltetésnél használatát javasoljuk.
- Az adatbázis eléréséhez kapcsolatkészletezést vagy állandó kapcsolat használatával.
- A kapcsolat rövid élettartamát az adatbázis eléréséhez. 
- Használat újrapróbálkozási logikát az alkalmazásban, a csatlakozási kísérlet hibák származó egyidejű kapcsolatokat olvasásra folyamatos elérte a megengedett. Az újrapróbálkozási logika állítsa be egy kis ideig várakozni, és ezután Várjon, amíg egy véletlenszerű időpont előtt a kapcsolat további kísérleteket.