---
title: Az Azure Data Factory leképezési adatok folyamat Kifejezésszerkesztő
description: Az Azure Data Factory-térképadatokat a Kifejezésszerkesztő folyamatok
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: a65ba66b91b89cca7a41b7276a0ee4790088d5a1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213472"
---
# <a name="azure-data-factory-mapping-data-flow-expression-builder"></a>Az Azure Data Factory leképezési adatok folyamat Kifejezésszerkesztő

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Az Azure Data Factory leképezési adatfolyam látni fogja, amelyen megadhatja a kifejezéseket adatátalakítás kifejezés mezőkbe. Ezek a mezők oszlopok, mezők, változókat, paraméterek, az adatfolyam funkciókat használja. Kifejezés összeállítása, használja a Kifejezésszerkesztő, amely indítja el a kifejezés szövegmezőbe az átalakítás belül kattintva. Oszlopok átalakítása kiválasztásakor "Számított oszlop" beállítások néha is megjelenik. Gombra, amely elindítja a Kifejezésszerkesztő is látni fogja.

![Expression Builder](media/data-flow/expression.png "Expression Builder")

A Kifejezésszerkesztő eszköz az alapértelmezett érték a szöveges szerkesztő lehetőséget. az automatikus kiegészítés funkció szintaxissal ellenőrzése és a kiemelés olvassa be a teljes Azure Data Factory adatfolyam hálózatiobjektum-modellt.

![Automatikus kitöltés Kifejezésszerkesztő](media/data-flow/expb1.png "Kifejezésszerkesztő automatikus kiegészítés")

## <a name="currently-working-on-field"></a>A mező jelenleg működik

![Kifejezésszerkesztő](media/data-flow/exp3.png "éppen dolgozik")

At bal felső részén a kifejezés a jelentéskészítő felhasználói felületén, látni fogja, amely jelenleg dolgozunk a mező neve "Jelenleg működik a" nevű mező. Csak a jelenleg működő mező, a kifejezés, amely a felhasználói felületen fejleszt lépnek érvénybe. Ha szeretne egy másik mező átalakítása, az adatok aktuális mentése és a legördülő jelöljön ki egy másik mezőt, és a többi mező kifejezésének felépítéséhez.

## <a name="data-preview-in-debug-mode"></a>Adatelőnézet hibakeresési módban

![Kifejezésszerkesztő](media/data-flow/exp4b.png "kifejezés Adatelőnézet")

Ha éppen dolgozik a kifejezések, igény szerint válthat hibakeresési módban az Azure Data Factory adatfolyam tervezési felületéhez a kifejezés, amely hoz létre az adatok eredményeit a folyamatban lévő élő előnézetének engedélyezése. Valós idejű élő hibakeresés engedélyezve van a kifejezéseket.

![Hibakeresési mód](media/data-flow/debugbutton.png "gomb hibakeresése")


![Kifejezésszerkesztő](media/data-flow/exp5.png "kifejezés Adatelőnézet")

## <a name="comments"></a>Megjegyzések

Megjegyzések hozzáadása a kifejezések egyetlen sor és többsoros megjegyzés szintaxisa:

![Megjegyzések](media/data-flow/comments.png "megjegyzések")

## <a name="regular-expressions"></a>Reguláris kifejezések

Az Azure Data Factory adatfolyam kifejezésnyelveket [teljes leírása itt található dokumentáció](http://aka.ms/dataflowexpressions), lehetővé teszi, hogy az funkciók, amelyek tartalmazzák a reguláris kifejezések szintaxisát. Reguláriskifejezés-függvényeket használatakor a Kifejezésszerkesztő megpróbálja értelmezése fordított perjel (\) , az escape-karaktersorozat. Ha fordított perjelet használ a reguláris kifejezés, vagy tegye a teljes regex órajel során végbemenő ` ` vagy használjon egy dupla fordított perjelet.

Példa órajel során végbemenő használatával

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

vagy kettős perjel használatával

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Tömb indexek-címzés

A tömbök visszaadó kifejezés funkciók szögletes zárójeleket [] használatával oldja meg a visszaadott tömb objektumában lévő egyetlen egyedi indexeket. A tömb a meglévőket-alapú.

![Kifejezés Builder tömb](media/data-flow/expb2.png "kifejezés Adatelőnézet")
