---
title: Az Azure Data Lake Analytics U-SQL futásidejű hibáinak hibaelhárítása
description: Ismerje meg, hogyan háríthatja el az U-SQL futásidejű hibák at.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 1e3fb218e6cda5619bfa1a0936e07d6731a9cc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648453"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>További információ az U-SQL futásidejű hibák futásidejű módosítások miatti hibáinak elhárításáról

Az Azure Data Lake U-SQL futásidejű, beleértve a fordító, optimalizáló és a feladatkezelő, az, amit feldolgozza az U-SQL-kódot.

## <a name="choosing-your-u-sql-runtime-version"></a>Az U-SQL futásidejű verzió kiválasztása

Amikor u-SQL-feladatokat küld a Visual Studio, az ADL SDK vagy az Azure Data Lake Analytics portálról, a feladat a jelenleg elérhető alapértelmezett futásidőt fogja használni. Az U-SQL futásidejű új verziói rendszeresen megjelennek, és kisebb frissítéseket és biztonsági javításokat is tartalmaznak.

Egyéni futásidejű verziót is választhat; vagy azért, mert ki szeretne próbálni egy új frissítést, a futásidejű ek régebbi verzióján kell maradnia, vagy egy olyan jelentett probléma gyorsjavítását kapta, ahol nem várhat a szokásos új frissítésre.

> [!CAUTION]
> Az alapértelmezetttől eltérő futásidejű kiválasztása megszakíthatja az U-SQL-feladatokat. Ezeket a többi verziót csak tesztelésre használja.

Ritkán előfordulhat, hogy a Microsoft támogatási szolgálata a futásidejű ek egy másik verzióját tűzi ki alapértelmezettként a fiókjához. Ügyeljen arra, hogy a lehető leghamarabb visszaadja ezt a gombostűt. Ha továbbra is rögzítve marad az adott verzióhoz, az egy későbbi időpontban lejár.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>A feladatok figyelése U-SQL futásidejű verzió

A Visual Studio feladatböngészőjében vagy az Azure Portal feladatelőzményein keresztül megtekintheti, hogy a korábbi feladatok mely futásidejű verziót használták a fiók feladatelőzményeiközött.

1. Az Azure Portalon nyissa meg a Data Lake Analytics-fiókját.
2. Válassza **az Összes feladat megtekintése**lehetőséget. Megjelenik a fiókban lévő összes aktív és legutóbb befejezett feladat listája.
3. Szükség esetén a **Szűrő** gombra kattintva megkeresheti a feladatokat **az Időtartomány,** **a Feladat neve**és a **Szerző** értéke szerint.
4. Láthatja a befejezett feladatokban használt futásidejű.

![Korábbi feladat futásidejű verziójának megjelenítése](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

A rendelkezésre álló futásidejű verziók idővel változnak. Az alapértelmezett futásidejű mindig az úgynevezett "alapértelmezett", és tartjuk legalább az előző futásidejű elérhető egy ideig, valamint a speciális futásidejű elérhetőak a különböző okok miatt. A nyíltan elnevezett futásidők általában a következő formátumot követik (dőlt betűs számok at használnak változó alkatrészekhez, és a [] a választható részeket jelöli):

release_YYYYMMDD_adl_buildno[_modifier]

Például release_20190318_adl_3394512_2 a 2019. március 18-i futásidejű kiadás 3394512-es verziójának második verzióját jelenti, release_20190318_adl_3394512_private pedig ugyanannak a kiadásnak a privát buildje. Megjegyzés: A dátum arra vonatkozik, hogy az utolsó bejelentkezés mikor történt az adott kiadáshoz, és nem feltétlenül a hivatalos megjelenési dátum.

A jelenleg elérhető futásidejű verziók az alábbiakban láthatók.

- release_20190318_adl_3394512
- release_20190318_adl_5832669 az aktuális alapértelmezett
- release_20190703_adl_4713356

## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Az U-SQL futásidejű verzióval kapcsolatos problémák elhárítása

Két lehetséges futásidejű verzióproblémák léphetnek fel:

1. Egy parancsfájl vagy valamilyen felhasználói kód megváltoztatja a viselkedést egyik kiadásról a másikra. Az ilyen törési változásokat általában előre közlik a kibocsátási megjegyzések közzétételével. Ha ilyen törési változással találkozik, lépjen kapcsolatba a Microsoft támogatási szolgálatával, és jelentse ezt a törési viselkedést (ha még nem dokumentálták), és küldje el a feladatokat a régebbi futásidejű verzióval szemben.

2. Nem alapértelmezett futásidejűt használt explicit vagy implicit módon, ha a fiókjához rögzítették, és a futásidejűt egy idő után eltávolították. Ha hiányzó futási idővel találkozik, frissítse a parancsfájlokat az aktuális alapértelmezett futásidővel való futtatáshoz. Ha további időre van szüksége, forduljon a Microsoft támogatási szolgálatához

## <a name="see-also"></a>Lásd még

- [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
- [Az Azure Data Lake Analytics kezelése az Azure Portal használatával](data-lake-analytics-manage-use-portal.md)
- [Feladatok figyelése az Azure Data Lake Analytics szolgáltatásban az Azure Portalhasználatával](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
