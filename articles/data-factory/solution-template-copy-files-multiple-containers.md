---
title: Fájlok másolása több tárolóból
description: Ismerje meg, hogyan használhat megoldássablont több tárolóból több tárolóból az Azure Data Factory használatával.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: 0c4c26ba163f83483b3eb48e51d91f9a919a887c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439752"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Fájlok másolása több tárolóból az Azure Data Factory segítségével

Ez a cikk egy megoldássablont ismertet, amelyekkel fájlokat másolhat több tárolóból a fájltárolók között. Például használhatja a data lake áttelepítésére a AWS S3 az Azure Data Lake Store. Vagy használhatja a sablont, hogy replikáljon mindent az egyik Azure Blob-tárfiókból a másikba.

> [!NOTE]
> Ha egyetlen tárolóból szeretne fájlokat másolni, hatékonyabb az [Adatmásolás eszközzel](copy-data-tool.md) egyetlen másolási tevékenységgel rendelkező folyamatot létrehozni. A sablon ebben a cikkben több, mint amire szüksége van az egyszerű forgatókönyv.

## <a name="about-this-solution-template"></a>A megoldássablon – kapcsolat

Ez a sablon számba veszi a tárolók a forrástárolóból. Ezután másolja ezeket a tárolókat a céltárolóba.

A sablon három tevékenységet tartalmaz:
- **A GetMetadata** megvizsgálja a forrástárolót, és leolvassa a tárolólistát.
- **ForEach** lekéri a tárolólistát a **GetMetadata** tevékenységből, majd iterates a listán, és továbbítja az egyes tárolók a másolási tevékenység.
- **Másolja** az egyes tárolókat a forrástárolóból a céltárolóba.

A sablon a következő paramétereket határozza meg:
- *A SourceFileFolder* az adatforrás-tároló mappaelérési útja, ahol beszerezheti a tárolók listáját. Az elérési út a gyökérkönyvtár, amely több tárolómappát tartalmaz. A paraméter alapértelmezett értéke `sourcefolder`a.
- *A SourceFileDirectory* az adatforrás-tároló gyökérkönyvtára alatti almappa elérési út. A paraméter alapértelmezett értéke `subfolder`a.
- *A DestinationFileFolder* az a mappaelérési út, ahová a fájlokat a céltárolóba másolja a program. A paraméter alapértelmezett értéke `destinationfolder`a.
- *A DestinationFileDirectory* az az almappa elérési útja, ahová a fájlokat a céltárolóba másolja a rendszer. A paraméter alapértelmezett értéke `subfolder`a.

## <a name="how-to-use-this-solution-template"></a>A megoldássablon használata

1. Nyissa meg a **Több fájltároló kontinamányának másolása a Fájltárolók** sablont. **Hozzon** létre egy új kapcsolatot a forrástárolóval. A forrástároló az a hely, ahonnan több tárolóból szeretne fájlokat másolni.

    ![Új kapcsolat létrehozása a forrással](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. **Hozzon** létre egy új kapcsolatot a céltárolóval.

    ![Új kapcsolat létrehozása a célhoz](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Válassza **a Sablon használata lehetőséget.**

    ![Sablon használata](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. A következő példában látható a folyamat:

    ![A folyamat megjelenítése](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Válassza **a Hibakeresés**lehetőséget, írja be a **Paraméterek**, majd a **Befejezés**lehetőséget.

    ![A folyamat futtatása](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Tekintse át az eredményt.

    ![Az eredmény áttekintése](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>További lépések

- [Tömeges másolás egy adatbázisból egy vezérlőtábla használatával az Azure Data Factory-val](solution-template-bulk-copy-with-control-table.md)

- [Fájlok másolása több tárolóból az Azure Data Factory segítségével](solution-template-copy-files-multiple-containers.md)
