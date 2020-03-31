---
title: Rögzített hosszúságú szövegfájlok feldolgozása leképezési adatfolyamokkal az Azure Data Factoryban
description: Ismerje meg, hogyan dolgozhat fel rögzített hosszúságú szövegfájlokat az Azure Data Factory ban leképezési adatfolyamok használatával.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: d6885e9b30cc71bda822a29574c4d574f2b020a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72387050"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Rögzített hosszúságú szövegfájlok feldolgozása adatfeldolgozó adatfolyamok használatával

A Microsoft Azure Data Factory ban a leképezési adatfolyamok használatával átalakíthatja az adatokat rögzített szélességű szövegfájlokból. A következő feladatban egy határolójel nélküli szövegfájl adatkészletét határozunk meg, majd a sorpozíció alapján beállítjuk a karakterláncalatti felosztásokat.

## <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Új folyamat létrehozásához válassza az **+Új folyamat** lehetőséget.

2. Adjon hozzá egy adatfolyam-tevékenységet, amely et rögzített szélességű fájlok feldolgozására fog ják:

    ![Rögzített szélességű csővezeték](media/data-flow/fwpipe.png)

3. Az adatfolyam-tevékenységben válassza az **Új leképezési adatfolyam lehetőséget.**

4. Forrás, származtatott oszlop, kijelölés és elfogadó átalakítás hozzáadása:

    ![Rögzített szélességű adatfolyam](media/data-flow/fw2.png)

5. Konfigurálja úgy a Forrás átalakítást, hogy új adatkészletet használjon, amely a Tagolt szöveg típusú lesz.

6. Ne állítson be oszlophatárolót vagy fejlécet.

   Most a fájl tartalmának kezdőpontjait és hosszát állítjuk be:

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

7. A Forrás átalakítás **vetítése** lapján egy *Column_1*nevű karakterláncoszlopnak kell lennie.

8. A Származtatott oszlopban hozzon létre egy új oszlopot.

9. Adunk az oszlopok egyszerű nevek, mint *a col1*.

10. A kifejezésszerkesztőmezőbe írja be a következőt:

    ```substring(Column_1,1,4)```

    ![származtatott oszlop](media/data-flow/fwderivedcol1.png)

11. Ismételje meg a 10.

12. A **Vizsgálat** lapon megtekintheti a létrehozandó új oszlopokat:

    ![Ellenőrizni](media/data-flow/fwinspect.png)

13. Az Átalakítás kiválasztása segítségével távolítsa el azokat az oszlopokat, amelyekre nincs szüksége az átalakításhoz:

    ![átalakítás kijelölése](media/data-flow/fwselect.png)

14. A Fogadó segítségével adja ki az adatokat egy mappába:

    ![rögzített szélességű mosogató](media/data-flow/fwsink.png)

    Így néz ki a kimenet:

    ![rögzített szélességű kimenet](media/data-flow/fxdoutput.png)

  A rögzített szélességű adatok most felvannak osztva, négy karakterrel, és a Col1, Col2, Col3, Col4 és így tovább. Az előző példa alapján az adatok négy oszlopra vannak felosztva.

## <a name="next-steps"></a>További lépések

* Az adatfolyam-átalakítások leképezésével az [adatfolyam-átalakítások](concepts-data-flow-overview.md)leképezésével hozhatja létre az adatfolyam-logika többi részét.
