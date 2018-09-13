---
title: Az Azure Machine Learning Workbench használatával például átalakítással oszlopok egyesítése
description: A referenciadokumentum az "Oszlopok egyesítése példa"-átalakításhoz
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 621601ad3576aad13f2f71062ee2351cf1a394c8
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645521"
---
# <a name="combine-columns-by-example-transformation"></a>Oszlopok egyesítése példa átalakítással
Az átalakítás lehetővé teszi, hogy a felhasználó egy olyan új oszlop hozzáadásához érték több oszlopból kombinálásával. Felhasználó megadása az elválasztó vagy az összesített értékek az átalakítás végrehajtásához példákat biztosítanak. Amikor a felhasználó kombináció példákat talál, az átalakítás kezeli azonos **– példa** motor, amely használatban van a **származtatása példa** átalakítása.

## <a name="how-to-perform-this-transformation"></a>Az átalakítás végrehajtása

Az átalakítás végrehajtásához kövesse az alábbi lépéseket:
1. Válassza ki a két vagy több oszlopot úgy, hogy egy oszlop összevonásával. 
2. Válassza ki **oszlopok egyesítése példa alapján** származó a **alakítja át az** menü. Vagy a jobb gombbal bármelyik a kijelölt oszlopokat, és válassza a fejléc a **oszlopok egyesítése példa alapján** a helyi menüből. Megnyílik a átalakítása szerkesztő, és a egy olyan új oszlop bekerül a megfelelő legtöbb kijelölt oszlop mellett. Az új oszlop alapértelmezett elválasztó elválasztva összesített értékeket tartalmazza. A kijelölt oszlopok az oszlopfejléceket, a jelölőnégyzetek segítségével azonosítható. Hozzáadása és eltávolítása a kijelölt oszlopokat teheti meg a jelölőnégyzetek használatával.
3. Az újonnan létrehozott oszlopot kombinált értéke frissítheti. A frissített értéket az átalakítás további példaként szolgál.
4. Kattintson a **OK** gombra az átalakítás elfogadásához.

### <a name="transform-editor-advanced-mode"></a>Editor átalakítása: speciális módban

Speciális mód oszlopok kombinálására gazdagabb élményt nyújt. 

Kiválasztásával **elválasztó** alatt **oszlopok egyesítése** lehetővé teszi, hogy a felhasználó meghatározza a karakterláncok a **elválasztó** szövegmezőben. Ki a lapon a **elválasztó** az előzetes verzióra az eredményeket az adatok gird a szövegmezőben. Nyomja meg **OK** véglegesíteni az átalakítás.

Kiválasztásával **példák** alatt **oszlopok egyesítése** lehetővé teszi a felhasználónak meg kell adnia néhány lehetséges érték kombinált. Például egy sor előléptetése, kattintson duplán a sorokat a rácsban. Írja be a várt kimenetet a szövegmezőbe, szemben az előléptetett sor. Ki a lapon a **elválasztó** az előzetes verzióra az eredményeket az adatok gird a szövegmezőben. Nyomja meg **OK** véglegesíteni az átalakítás. 

Felhasználói között válthat a **egyszerű módban** és a **speciális mód** átalakítása a szerkesztőben a hivatkozásokra kattintva.

### <a name="transform-editor-send-feedback"></a>Editor átalakítása: visszajelzés küldése

Kattintson a a **visszajelzés küldése** hivatkozás megnyílik az **visszajelzés** párbeszédpanelen hagyja a megjegyzéseket, előre feltöltve a példák felhasználóval van megadva. Felhasználó kell a Megjegyzések mező tartalmát, és segít nekünk a probléma, további részletekkel szolgálnak. Ha a felhasználó nem szeretné az adatok megosztása a Microsofttal, felhasználói gombra kattintás előtt törölje a előfeltöltött példaadatokat a **visszajelzés küldése** gombra. 

### <a name="editing-existing-transformation"></a>Meglévő átalakítás szerkesztése

A felhasználó szerkesztheti a meglévő **oszlop egyesítése példa alapján** kiválasztásával átalakítása **szerkesztése** átalakítási lépés lehetőséget. Kattintson a **szerkesztése** a átalakítása megnyitása a **egyszerű módban**. Felhasználó megadhatja a **speciális mód** a fejlécben szereplő hivatkozásra kattintva. A példákat, amelyeket az átalakítás létrehozása közben is megjelennek.

## <a name="example-using-separators"></a>Példa elválasztók használatával

Egy vesszőt és egy szóközt úgy, hogy ebben a példában elválasztására szolgál a *Street*, *Város*, *állapot*, és *ZIP* oszlopokat.

|Utca|Város|Állapot|ZIP|Oszlop|
|:----|:----|:----|:----|:----|
|16011 N.E. 36th módja|REDMOND|WA|98052|16011 N.E. 36th way, REDMOND, WA, 98052|
|16021 N.E. 36th módja|REDMOND|WA|98052|16021 N.E. 36th way, REDMOND, WA, 98052|
|16031 N.E. 36th módja|REDMOND|WA|98052|16031 N.E. 36th way, REDMOND, WA, 98052|
|16041 N.E. 36th módja|REDMOND|WA|98052|16041 N.E. 36th way, REDMOND, WA, 98052|
|16051 N.E. 36th módja|REDMOND|WA|98052|16051 N.E. 36th way, REDMOND, WA, 98052|
|16061 N.E. 36th módja|REDMOND|WA|98052|16061 N.E. 36th way, REDMOND, WA, 98052|
|3460 157th sérülésre ú|REDMOND|WA|98052|3460 157th sérülésre ú, REDMOND, WA, 98052|
|3350 157th Ave N.E.|REDMOND|WA|98052|3350 157th Ave N.E., REDMOND, WA, 98052|
|3240 157th sérülésre N.E.|REDMOND|WA|98052|3240 157th sérülésre N.E., REDMOND, WA, 98052|

## <a name="example-using-by-example"></a>Példa használatával példa alapján

Az érték a **félkövér** példaként lett megadva.

|Dátum|Hónap|Év|Óra|Perc|Másodperc|Egyesített oszlop|
|:----|:----|:----|:----|:----|:----|:----|
|13|Okt|2016|15|01|23|**13 – Oct-2016 15:01:23 CET**|
|16|Okt|2016|16|22|33|16-Oct-2016 15:01:33 CET|
|17|Okt|2016|12|43|12|17-Oct-2016 15:01:12 CET|
|12|Nov|2016|14|22|44|12 – november – 2016 15:01:44 CET|
|23|Nov|2016|01|52|45|23-november – 2016 15:01:45 CET|
|16|Jan|2017|22|34|56|16 – – 2016. január 15:01:56 CET|
|23|már.|2017|01|55|25|23-március – 2016 15:01:25-ös CET|
|16|ápr.|2017|11|34|36|16-Diagramhalmazban – 2016 15:01:36 CET|

