---
title: A Azure Data Lake Analytics U-SQL futásidejű hibáinak elhárítása
description: Ismerje meg, hogy miként lehet elhárítani az U-SQL futásidejű hibáit.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 1e3fb218e6cda5619bfa1a0936e07d6731a9cc93
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "73648453"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Ismerje meg, hogy miként lehet elhárítani a futásidejű változások miatti U-SQL futásidejű hibákat

A Azure Data Lake u-SQL-futtatókörnyezet, beleértve a fordítót, az optimalizáló és a Feladatkezelőt, a u-SQL-kódot dolgozza fel.

## <a name="choosing-your-u-sql-runtime-version"></a>A U-SQL futtatókörnyezet-verziójának kiválasztása

Ha a Visual studióból, az ADL SDK-ból vagy a Azure Data Lake Analytics-portálról küld el U-SQL-feladatokat, a feladat az aktuálisan elérhető alapértelmezett futtatókörnyezetet fogja használni. Az U-SQL Runtime új verziói rendszeresen jelennek meg, és a másodlagos frissítéseket és a biztonsági javításokat is tartalmazzák.

Választhat egyéni futtatókörnyezet-verziót is; vagy azért, mert egy új frissítést szeretne kipróbálni, a futtatókörnyezet egy régebbi verziójában kell maradnia, vagy a jelentett probléma gyorsjavításával lett ellátva, amely nem várja meg a rendszeres új frissítést.

> [!CAUTION]
> Az alapértelmezetttől eltérő futtatókörnyezet kiválasztásával megszakíthatja a U-SQL-feladatokat. Ezeket a más verziókat csak tesztelésre használhatja.

Ritka esetekben a Microsoft ügyfélszolgálata a futtatókörnyezet egy másik verzióját is rögzítheti alapértelmezettként a fiókjához. Győződjön meg róla, hogy a PIN-kódot a lehető leghamarabb visszavonja. Ha továbbra is az adott verzióra van rögzítve, akkor később lejár.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>Feladatok figyelése U-SQL futtatókörnyezet-verzió

Megtekintheti, hogy a korábbi feladatok melyik futásidejű verzióját használták a fiókjában a Visual Studio böngésző vagy a Azure Portal feladat előzményein keresztül.

1. A Azure Portal lépjen a Data Lake Analytics-fiókra.
2. Válassza **az összes feladat megtekintése**lehetőséget. Megjelenik a fiók összes aktív és legutóbb befejezett feladatának listája.
3. A **szűrő** lehetőségre kattintva megkeresheti a feladatokat az **időtartomány**, a **feladat neve**és a **Szerző** értékei alapján.
4. Láthatja a befejezett feladatokban használt futtatókörnyezetet.

![A korábbi feladatok futtatókörnyezet-verziójának megjelenítése](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

Az elérhető futásidejű verziók változnak az idő múlásával. Az alapértelmezett futtatókörnyezetet mindig "default"-nek nevezzük, és legalább az előző futtatókörnyezetet megtartjuk egy ideig, és a speciális futtatókörnyezeteket számos okból elérhetővé tesszük. A explicit módon megnevezett futtatókörnyezetek általában a következő formátumot követik (a dőltek a változó részekhez használatosak, a [] pedig opcionális részeket jelez):

release_YYYYMMDD_adl_buildno [_modifier]

Például release_20190318_adl_3394512_2 azt jelenti, hogy a Build 3394512-es verziójának második, 18 2019-es és release_20190318_adl_3394512_private-es verzióját jelenti, és az ugyanazon kiadás privát buildje. Megjegyzés: a dátum ahhoz kapcsolódik, hogy mikor került sor az adott kiadás utolsó beadására, és nem feltétlenül a hivatalos kiadási dátumra.

A jelenleg elérhető futásidejű verziók a következők.

- release_20190318_adl_3394512
- a jelenlegi alapértelmezett release_20190318_adl_5832669
- release_20190703_adl_4713356

## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Az U-SQL futásidejű verziójának hibáinak elhárítása

A futásidejű verziók két lehetséges problémája merülhet fel:

1. Egy parancsfájl vagy valamilyen felhasználói kód megváltoztatja az egyik kiadás viselkedését a következőre. Az ilyen jellegű feltörési változások általában a kibocsátási megjegyzések közzétételével kapcsolatos idő előtt kerülnek közlésre. Ha ilyen jellegű változást tapasztal, forduljon a Microsoft ügyfélszolgálatahoz, és jelentse ezt a feltörési viselkedést (ha még nincs dokumentálva), és küldje el a feladatokat a régebbi futtatókörnyezet-verzióra.

2. Nem alapértelmezett futtatókörnyezetet használ explicit módon vagy implicit módon, amikor a fiókjában rögzítette, és a futtatókörnyezetet némi idő múlva eltávolították. Ha hiányzó futtatókörnyezetekkel találkozik, frissítse a parancsfájlokat az aktuális alapértelmezett futtatókörnyezettel való futtatáshoz. Ha további időre van szüksége, vegye fel a kapcsolatot Microsoft ügyfélszolgálata

## <a name="see-also"></a>Lásd még

- [Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
- [Azure Data Lake Analytics kezelése Azure Portal használatával](data-lake-analytics-manage-use-portal.md)
- [Feladatok figyelése Azure Data Lake Analytics a Azure Portal használatával](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
