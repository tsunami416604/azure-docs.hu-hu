---
title: "Oszlopok használata az Azure Machine Learning-munkaterület példa átalakítással egyesítése"
description: "A \"Oszlopok egyesítése példa\" transzformáció a referenciadokumentum"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 528d7ff5f74948fd1ae7b829d4b2fc38fa2f1109
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="combine-columns-by-example-transformation"></a>Példa átalakítással oszlopok egyesítése
Ez a transzformáció lehetővé teszi a felhasználó számára egy olyan új oszlop hozzáadása kombinálásával több oszlop értékeit. Felhasználó adjon meg egy elválasztó és példákkal kombinált értékek ehhez a transzformációhoz végrehajtásához. Amikor a felhasználó kombinációja példákat tartalmaz, az átalakítás kezeli azonos **-példa** motor, amely használatban van a **Célosztályából oszlop példa alapján** átalakító.

## <a name="how-to-perform-this-transformation"></a>Ez a transzformáció végrehajtása

A transzformáció végrehajtásához kövesse az alábbi lépéseket:
1. Jelölje ki két vagy több, egyesítheti a egyoszlopos kívánt oszlopokat. 
2. Válassza ki **példa oszlopok egyesítése** a a **átalakítja** menü. Vagy kattintson jobb gombbal a kijelölt oszlopok, és válassza ki a fejlécében a **példa oszlopok egyesítése** a helyi menüből. Megnyílik a átalakítási szerkesztő, és egy olyan új oszlop ad hozzá a megfelelő a kiválasztott oszlop mellett. Az új oszlop alapértelmezett elválasztó elválasztott kombinált értékeket tartalmazza. A kijelölt oszlopok segítségével azonosítható, az oszlopfejlécek a jelölőnégyzeteket. Hozzáadása és eltávolítása a kijelölt oszlopokat végezhető jelölőnégyzetek segítségével.
3. Az újonnan létrehozott oszlop kombinált értéke frissítheti. A frissített értéket az átalakítás további példaként szolgál.
4. Kattintson a **OK** az átalakítás fogadásához.

### <a name="transform-editor-advanced-mode"></a>Átalakítás szerkesztő: speciális módban

Speciális módban történő oszlopok egyesítésével gazdagabb élményt nyújt. 

Kiválasztása **elválasztó** alatt **egyesítése oszlopok** lehetővé teszi, hogy a felhasználó megadhatja a karakterláncok a **elválasztó** szövegmezőben. A kimenő lapon a **elválasztó** tekintse meg az eredményeket az adatok gird szolgáló szövegmező. Nyomja le az **OK** véglegesíti a transzformáció.

Kiválasztása **példák** alatt **egyesítése oszlopok** lehetővé teszi a felhasználónak meg kell adnia néhány lehetséges kombinált érték. A példa egy sor támogatására kattintson duplán a rács sorai. Adja meg a várt kimeneti a szövegmezőben az előléptetett sor ellen. A kimenő lapon a **elválasztó** tekintse meg az eredményeket az adatok gird szolgáló szövegmező. Nyomja le az **OK** véglegesíti a transzformáció. 

Felhasználó válthat a **alapvető mód** és a **Speciális üzemmód** átalakítási-szerkesztőben hivatkozásokra kattintva.

### <a name="transform-editor-send-feedback"></a>Átalakítás szerkesztő: visszajelzés küldése

Kattintson a a **visszajelzés küldése** hivatkozás megnyílik a **visszajelzés** nyújtott előre feltöltve a példák felhasználói Megjegyzések mezőbe párbeszédpanel megnyitása. Felhasználói tekintse át a Megjegyzések mezőbe tartalmát és további részleteket, és segítsen megérteni a problémát. Ha a felhasználó nem szeretné adatok megosztása a Microsofttal, felhasználói való kattintás előtt törölje a előfeltöltött példaadatokat a **visszajelzés küldése** gombra. 

### <a name="editing-existing-transformation"></a>Meglévő átalakítása szerkesztése

A felhasználó már meglévő **oszlop egyesítése minta alapján** kiválasztásával átalakítási **szerkesztése** átalakítása lépés lehetőséget. Kattintson a **szerkesztése** a átalakítási megnyitása a **alapvető mód**. Felhasználó adhat meg a **Speciális üzemmód** a fejlécben szereplő hivatkozásra kattintva. A példák, amelyeket az átalakítás létrehozása során is megjelennek.

## <a name="example-using-separators"></a>Példa elválasztók használatával

Vesszőt és egy szóközt egyesítése ebben a példában elválasztójelként szolgál a *utca*, *Város*, *állapot*, és *ZIP-* oszlopok.

|Utca|Város|Állapot|ZIP|Oszlop|
|:----|:----|:----|:----|:----|
|16011 N.E. 36th módja|REDMOND|WA|98052|16011 N.E. 36th módja, REDMOND, WA, 98052|
|16021 N.E. 36th módja|REDMOND|WA|98052|16021 N.E. 36th módja, REDMOND, WA, 98052|
|16031 N.E. 36th módja|REDMOND|WA|98052|16031 N.E. 36th módja, REDMOND, WA, 98052|
|16041 N.E. 36th módja|REDMOND|WA|98052|16041 N.E. 36th módja, REDMOND, WA, 98052|
|16051 N.E. 36th módja|REDMOND|WA|98052|16051 N.E. 36th módja, REDMOND, WA, 98052|
|16061 N.E. 36th módja|REDMOND|WA|98052|16061 N.E. 36th módja, REDMOND, WA, 98052|
|3460 157th erőfeszítések Üzenetnek|REDMOND|WA|98052|3460 157th erőfeszítések Üzenetnek, REDMOND, WA, 98052|
|3350 157th Ave N.E.|REDMOND|WA|98052|3350 157th Ave N.E., REDMOND, WA, 98052|
|3240 157th erőfeszítések N.E.|REDMOND|WA|98052|3240 157th erőfeszítések N.E., REDMOND, WA, 98052|

## <a name="example-using-by-example"></a>Példa a használatával – példa

Az érték a **félkövér** példaként lett megadva.

|Dátum|Hónap|év|Óra|Perc|Második|Összetett oszlop|
|:----|:----|:----|:----|:----|:----|:----|
|13|okt.|2016|15|01|23|**13-Oct-2016 15:01:23 PDT**|
|16|okt.|2016|16|22|33|16-Oct-2016 15:01:33 CET|
|17|okt.|2016|12|43|12|17-Oct-2016 15:01:12 PDT|
|12|nov.|2016|14|22|44|12-Nov-2016 15:01:44 PDT|
|23|nov.|2016|01|52|45|23-Nov-2016 15:01:45 PDT|
|16|jan.|2017|22|34|56|16-Jan-2016 15:01:56 CET|
|23|már.|2017|01|55|25|23-Mar-2016 15:01:25 PDT|
|16|ápr.|2017|11|34|36|16-6%/4!a-2016 15:01:36 CET|

