---
title: 'Oszlopok kiválasztása az adatkészlet: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan adatkészletet modul az Azure Machine Learning szolgáltatás a Select-Columns használatával válassza ki az alsóbb rétegbeli műveletek használandó oszlopok egy része.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: e7407f62bd3401411d56076b298bd8cd134ece62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028095"
---
# <a name="select-columns-in-dataset-module"></a>Oszlopok kiválasztása az adathalmaz-modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul az alsóbb rétegbeli műveletek használandó oszlopok kiválasztásához használható. A modul nem fizikailag törli az oszlopot a forrásadatkészlet; Ehelyett hozza létre oszlopok, hasonlóan egy adatbázis *nézet* vagy *leképezése*.

Ez a modul akkor hasznos, amikor szüksége van egy alsóbb rétegbeli művelet oszlopainak korlátozásához, vagy ha a szükségtelen oszlopok eltávolításával az adatkészlet méretének csökkentése érdekében.

Az oszlopok az adatkészlet kimeneti ugyanabban a sorrendben, mint az eredeti adatok, még akkor is, ha megadja ezeket különböző sorrendben.

## <a name="how-to-use"></a>A használat módja

Ez a modul nem tartozik paraméter. Az Oszlopválasztó használatával válassza ki a bevonhat vagy kizárhat oszlopokat.

### <a name="choose-columns-by-name"></a>Oszlopok kiválasztása név szerint

A modul neve szerint oszlopok kiválasztására vonatkozó több lehetőség áll rendelkezésre: 

+ Szűrés és keresés

    Kattintson a **név szerint** lehetőséget.

    Ha csatlakozott egy adatkészletet, amely már fel van töltve, elérhető oszlopok listájának kell megjelennie. Ha nincsenek oszlopok jelennek meg, szüksége lehet a felsőbb rétegbeli modulok oszlop listájának megjelenítéséhez futtassa.

    A lista szűréséhez írja be a keresőmezőbe. Például, ha beírja a levél `w` a keresőmezőbe, a szűrt lista megjelenítése az oszlopok neveit, amelyek tartalmazzák a levél `w`.

    Válassza ki az oszlopot, és kattintson a jobbra mutató nyíl gombra áthelyezi a kijelölt oszlopokat a jobb oldali panelen található listában.

    + Az oszlopnevek folyamatos tartomány kijelöléséhez nyomja le a **Shift + kattintás**.
    + Az egyes oszlopok hozzáadása a kijelölt, nyomja le a **Ctrl + kattintással**.

    Kattintson a pipa gombra kattintva mentse és zárja be.

+ Nevek használata az egyéb szabályokkal

    Kattintson a **a szabályok** lehetőséget.
    
    Válasszon egy szabályt, például egy adott típusú oszlopok megjelenítése.

    Ezután kattintson az adott típusú az egyes oszlopok neve, a kiválasztási listában való hozzáadásakor.

+ Írja vagy illessze be az oszlopok neveit vesszővel elválasztott listája

    Ha az adatkészlet széles, indexek használata könnyebb, vagy külön-külön létrehozott oszlopok kiválasztásával, hanem nevek listája. Feltéve, hogy előkészítette a lista előre:

    1. Kattintson a **a szabályok** lehetőséget. 
    2. Válassza ki **nem szerepelnek oszlopok a**válassza **Belefoglalás**, majd kattintson a szövegdobozba, és a piros felkiáltójel. 
    3. Illessze be a, vagy írja be a korábban ellenőrzött oszlopnevek vesszővel tagolt listája. Nem lehet menteni a modul Ha bármely oszlop neve érvénytelen, ezért mindenképpen előre nevek ellenőrzése.
    
    Ez a módszer használatával adja meg az oszlop az index értékek listáját. 

### <a name="choose-by-type"></a>Válassza ki a típus szerint

Használatakor a **a szabályok** beállítást, az oszlop ablakbon több feltételt is alkalmazhat. Szüksége lehet például egy numerikus adattípusú csak a szolgáltatás oszlopok.

A **megkezdése a** beállítás meghatározza, hogy a kiindulási pont, és az eredmények megértéséhez fontos. 

+ Ha a **minden oszlop** beállítást, minden oszlopokat ad hozzá a listához. Ezt követően kell használnia a **kizárása** beállítást *eltávolítása* oszlopokat, amelyek megfelelnek bizonyos feltételeknek. 

    Például előfordulhat, hogy indítsa el az összes oszlopot tartalmazó, és távolítsa el oszlopok neve vagy típusa szerint.

+ Ha a **nem oszlopok** beállítást, az oszlopok listájának elindításakor üresen. Majd adja meg a feltételek *hozzáadása* oszlopot a listához. 

    Ha több szabályt, minden egyes feltétel van **additív**. Tegyük fel például, hogy nem szerepelnek oszlopok a kezdődhet, és egy szabályt, amely az összes numerikus oszlopok adja. Az adatkészletben Automobile price, amely 16 ilyen oszlop eredményez. Ezután kattintson a **+** új feltétel hozzáadása, és válassza a bejelentkezés **többek között az összes**. Az eredményül kapott adatkészletet tartalmaz, a numerikus oszlopok, valamint az összes funkció oszlopot, beleértve néhány funkció karakterlánc típusú oszlopokra.

### <a name="choose-by-column-index"></a>Az oszlopindex alapján

Az oszlopindex abban az oszlop belül az eredeti adatkészletre hivatkozik.

+ Oszlopok számozása 1-gyel egymás után kezdődik.  
+ Az oszlopok számos, amelyet egy kötőjel. 
+ Például nyílt specifikációk `1-` vagy `-3` nem engedélyezettek.
+ Ismétlődő index értékek (vagy oszlopneveket) használata nem engedélyezett, és hibát eredményezhet.

Például feltéve, hogy az adatkészlet legalább nyolc oszlopot tartalmaz, akkor sikerült illessze be a valamelyik a következő példákat, hogy több nem egymás melletti oszlopot ad vissza: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

az utolsó példában nem eredményez hibát; azonban adja vissza egy példánya oszlop `4`.



### <a name="change-order-of-columns"></a>Az oszlopok sorrendjének módosítása

A beállítás **engedélyezi az ismétlődő elemeket, és őrizze meg a kijelölt oszlopok sorrendjét** üres lista kezdődik, és felveszi az oszlopok megadott neve vagy indexe. Ellentétben más beállításokat, amelyek mindig vissza oszlopokat "természetes sorrend", ezt a beállítást kimenete az oszlopokat a sorrendben, név vagy őket. 

Például az oszlopok Col1, Col2, Col3 és Col4 adatkészletek, sikerült az oszlopok sorrendjét, és hagyja ki 2. oszlop, adja meg a következő listák valamelyikét:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 