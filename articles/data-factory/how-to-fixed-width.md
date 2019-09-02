---
title: Rögzített hosszúságú szövegfájlok feldolgozása a leképezési adatfolyamatokkal Azure Data Factory
description: Megtudhatja, hogyan dolgozhat fel Azure Data Factory rögzített hosszúságú szövegfájlokat a leképezési adatfolyamatok használatával.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: e1ba09f459152616941071c23f7a6545ec2a8b73
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210675"
---
# <a name="process-fixed-length-text-files-using-data-factory-mapping-data-flows"></a>Rögzített hosszúságú szövegfájlok feldolgozása Data Factory leképezési adatfolyamatok használatával

Data Factory leképezési adatfolyamatok támogatják a rögzített szélességű szövegfájlokból származó átalakítási adatok használatát. Megadhat egy adatkészletet egy szöveges fájlhoz elválasztó nélkül, majd beállíthatja az alsztringek felosztását a sorszám alapján.

## <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Új folyamat elindításához nyissa meg az **+ új** folyamatot

2. Rögzített szélességű fájlok feldolgozásához használt adatfolyam-tevékenység hozzáadása

  ![Rögzített szélességű folyamat](media/data-flow/fwpipe.png)

3. Az adatfolyam tevékenységben válassza az új leképezési adatfolyam lehetőséget.

4. Forrás-átalakítás, származtatott oszlop, kijelölés és fogadó átalakítás hozzáadása

  ![Rögzített szélességű adatfolyam](media/data-flow/fw2.png)

5. Állítsa be úgy a forrás-átalakítást, hogy egy olyan új adatkészletet használjon, amelynek típusa tagolt szöveg lesz.

6. Nem állítható be az oszlop elválasztója, és nincsenek fejlécek

Ehhez a fájl tartalmához egyszerűen be kell állítani a mező kezdőpontját és hosszát:

```
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
```

7. A forrás-átalakítás leképezés lapján megjelenik egy "Column_1" nevű karakterlánc-oszlop.

8. A származtatott oszlopban most hozzon létre egy új oszlopot

9. Az oszlopokhoz egyszerű neveket adunk, például col1

10. Ezután a Kifejezésszerkesztő mezőbe írja be a következőt:

  ```substring(Column_1,1,4)```

  ![származtatott oszlop](media/data-flow/fwderivedcol1.png)

10. Ismételje meg ezt az összes elemezni kívánt oszlop esetében.

12. Kattintson a vizsgálat lapra a létrehozandó új oszlopok megtekintéséhez.

  ![vizsgálata](media/data-flow/fwinspect.png)

13. Az átalakítás kijelölése lehetőséggel távolítsa el azokat az oszlopokat, amelyekre nem lesz szüksége az átalakításhoz

  ![átalakítás kiválasztása](media/data-flow/fwselect.png)

14. Végül a fogadó használatával adja ki az adatokat egy mappába:

  ![rögzített szélességű fogadó](media/data-flow/fwsink.png)

  A kimenet a következőképpen fog kinézni:

  ![rögzített szélességű kimenet](media/data-flow/fxdoutput.png)

  A rögzített szélességű adat most négy karakterből áll, amelyek mindegyike Col1, Col2, Col3, Col4,... A fenti példa alapján 4 oszlopra oszthatom az adatmegosztást

## <a name="next-steps"></a>További lépések

* Az adatáramlási logikák kiépítése a leképezési adatfolyam [](concepts-data-flow-overview.md) -átalakítások használatával
