---
title: A Azure Data Lake Analytics U-SQL futásidejű hibáinak elhárítása
description: Ismerje meg, hogy miként lehet elhárítani az U-SQL futásidejű hibáit.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.openlocfilehash: 41b7c80c85331f288343351749e6b2e5292b30c6
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241607"
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
2. Válassza **az összes feladat megtekintése** lehetőséget. Megjelenik a fiók összes aktív és legutóbb befejezett feladatának listája.
3. A **szűrő** lehetőségre kattintva megkeresheti a feladatokat az **időtartomány**, a **feladat neve** és a **Szerző** értékei alapján.
4. Láthatja a befejezett feladatokban használt futtatókörnyezetet.

![A korábbi feladatok futtatókörnyezet-verziójának megjelenítése](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

Az elérhető futásidejű verziók változnak az idő múlásával. Az alapértelmezett futtatókörnyezetet mindig "default"-nek nevezzük, és legalább az előző futtatókörnyezetet megtartjuk egy ideig, és a speciális futtatókörnyezeteket számos okból elérhetővé tesszük. A explicit módon megnevezett futtatókörnyezetek általában a következő formátumot követik (a dőltek a változó részekhez használatosak, a [] pedig opcionális részeket jelez):

release_YYYYMMDD_adl_buildno [_modifier]

Például release_20190318_adl_3394512_2 azt jelenti, hogy a Build 3394512-es verziójának második, 18 2019-es és release_20190318_adl_3394512_private-es verzióját jelenti, és az ugyanazon kiadás privát buildje. Megjegyzés: a dátum ahhoz kapcsolódik, hogy mikor került sor az adott kiadás utolsó beadására, és nem feltétlenül a hivatalos kiadási dátumra.


## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Az U-SQL futásidejű verziójának hibáinak elhárítása

A futásidejű verziók két lehetséges problémája merülhet fel:

1. Egy parancsfájl vagy valamilyen felhasználói kód megváltoztatja az egyik kiadás viselkedését a következőre. Az ilyen jellegű feltörési változások általában a kibocsátási megjegyzések közzétételével kapcsolatos idő előtt kerülnek közlésre. Ha ilyen jellegű változást tapasztal, forduljon a Microsoft ügyfélszolgálatahoz, és jelentse ezt a feltörési viselkedést (ha még nincs dokumentálva), és küldje el a feladatokat a régebbi futtatókörnyezet-verzióra.

2. Nem alapértelmezett futtatókörnyezetet használ explicit módon vagy implicit módon, amikor a fiókjában rögzítette, és a futtatókörnyezetet némi idő múlva eltávolították. Ha hiányzó futtatókörnyezetekkel találkozik, frissítse a parancsfájlokat az aktuális alapértelmezett futtatókörnyezettel való futtatásra. Ha további időre van szüksége, lépjen kapcsolatba Microsoft ügyfélszolgálata

## <a name="known-issues"></a>Ismert problémák

* Ha egy USQL-parancsfájl 12.0.3 vagy újabb verziójára Newtonsoft.Jshivatkozik, akkor a következő fordítási hiba fog megjelenni:

    *"Sajnos a Data Lake Analytics-fiókban futó feladatok valószínűleg lassabban futnak, vagy a művelet nem fejeződik be. Egy váratlan probléma miatt nem tudjuk automatikusan visszaállítani a funkciót a Azure Data Lake Analytics-fiókjába. Azure Data Lake mérnököket felvettek a vizsgálatba. "*  

    A hívási verem a következőket fogja tartalmazni:  
    `System.IndexOutOfRangeException: Index was outside the bounds of the array.`  
    `at Roslyn.Compilers.MetadataReader.PEFile.CustomAttributeTableReader.get_Item(UInt32 rowId)`  
    `...`

    **Megoldás**: használja a Newtonsoft.Jsfájlt a v 12.0.2 vagy az alacsonyabb fájlnál.


## <a name="see-also"></a>Lásd még

- [Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
- [Azure Data Lake Analytics kezelése Azure Portal használatával](data-lake-analytics-manage-use-portal.md)
- [Feladatok figyelése Azure Data Lake Analytics a Azure Portal használatával](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
