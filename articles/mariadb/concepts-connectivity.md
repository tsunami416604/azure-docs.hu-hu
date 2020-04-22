---
title: Átmeneti kapcsolódási hibák – Azure-adatbázis a MariaDB-hez
description: Ismerje meg, hogyan kezelhetők az Azure Database for MariaDB átmeneti kapcsolódási hibái.
keywords: mysql-kapcsolat,kapcsolati karakterlánc,kapcsolódási problémák,átmeneti hiba,csatlakozási hiba
author: jasonwhowell
ms.author: jasonh
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2d162b5123cdaabe17859863c148f6483175d1e1
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770233"
---
# <a name="handling-of-transient-connectivity-errors-for-azure-database-for-mariadb"></a>Átmeneti kapcsolódási hibák kezelése a MariaDB Azure Database szolgáltatásában

Ez a cikk ismerteti, hogyan kezelhető konzisztitusos hibák az Azure Database for MariaDB.

## <a name="transient-errors"></a>Átmeneti hibák

Egy átmeneti hiba, más néven átmeneti hiba, egy hiba, amely megoldja magát. Ezek a hibák általában az adatbázis-kiszolgálóval való kapcsolatként jelennek meg. A kiszolgálóval létesített új kapcsolatok sem nyithatók meg. Átmeneti hibák fordulhatnak elő például hardver- vagy hálózati hiba esetén. Egy másik ok lehet egy új verziója a PaaS szolgáltatás, amely folyamatban van kivezetve. A legtöbb ilyen eseményt a rendszer kevesebb mint 60 másodperc alatt automatikusan enyhíti. A felhőben lévő alkalmazások tervezésére és fejlesztésére vonatkozó ajánlott eljárás az, ha átmeneti hibákra számíthat. Tegyük fel, hogy bármikor bármely összetevőben előfordulhatnak, és hogy megfelelő logikával rendelkeznek az ilyen helyzetek kezeléséhez.

## <a name="handling-transient-errors"></a>Átmeneti hibák kezelése

Az átmeneti hibákat újrapróbálkozási logikával kell kezelni. Figyelembe veendő helyzetek:

* Hiba történik a kapcsolat megnyitásakor
* A kiszolgáló oldalon megszakad az az idlés kapcsolat. Amikor megpróbál kiadni egy parancsot, az nem hajtható végre
* A rendszer elejt egy aktív kapcsolatot, amely jelenleg egy parancsot hajt végre.

Az első és a második esetben meglehetősen egyenesen kezelni. Próbálja meg újra megnyitni a kapcsolatot. Ha sikeres, a rendszer enyhítette az átmeneti hibát. Az Azure Database for MariaDB-t ismét használhatja. Javasoljuk, hogy várjon, mielőtt újra próbálkozna a kapcsolattal. Visszalépés, ha a kezdeti újrapróbálkozások sikertelenek lesznek. Így a rendszer minden rendelkezésre álló erőforrást felhasználhat a hibahelyzet leküzdésére. Egy jó minta, hogy kövesse a következő:

* Várjon 5 másodpercet az első újrapróbálkozás előtt.
* Minden következő újrapróbálkozás esetén a várakozás exponenciálisan, legfeljebb 60 másodpercig növekszik.
* Állítsa be az újrapróbálkozások maximális számát, amelynek során az alkalmazás a művelet sikertelennek tekinti.

Ha egy aktív tranzakcióval való kapcsolat sikertelen, nehezebb a helyreállítás megfelelő kezelése. Két eset van: Ha a tranzakció csak olvasható jellegű volt, biztonságos újra megnyitni a kapcsolatot, és újra megpróbálni a tranzakciót. Ha azonban a tranzakció is az adatbázisba írt, meg kell állapítania, hogy a tranzakció vissza lett-e állítva, vagy sikeres volt-e az átmeneti hiba bekövetkezése előtt. Ebben az esetben lehet, hogy nem kapta meg a véglegesítési nyugtát az adatbázis-kiszolgálótól.

Ennek egyik módja az, hogy létrehoz egy egyedi azonosítót az ügyfélen, amely az összes újrapróbálkozáshoz használatos. Ezt az egyedi azonosítót a tranzakció részeként adja át a kiszolgálónak, és egy egyedi megkötéssel rendelkező oszlopban tárolja. Így biztonságosan újra próbálkozhat a tranzakcióval. Sikeres lesz, ha az előző tranzakció vissza lett állítva, és az ügyfél által létrehozott egyedi azonosító még nem létezik a rendszerben. Sikertelen lesz a duplikált kulcs megsértése, ha az egyedi azonosítót korábban tárolták, mert az előző tranzakció sikeresen befejeződött.

Amikor a program harmadik féltől származó köztes szoftveren keresztül kommunikál a MariaDB-alapú Azure-adatbázissal, kérdezze meg a szállítótól, hogy a köztes szoftver tartalmaz-e átmeneti hibák újrapróbálkozási logikáját.

Győződjön meg róla, hogy tesztelje a logika újrapróbálkozását. Például próbálja meg végrehajtani a kódot, miközben felskálázás a számítási erőforrások at Azure Database for MariaDB-kiszolgáló. Az alkalmazásnak gond nélkül kezelnie kell a művelet során észlelt rövid állásidőt.

## <a name="next-steps"></a>További lépések

* [Az Azure Database for MariaDB-hez való csatlakozás hibáinak elhárítása](howto-troubleshoot-common-connection-issues.md)
