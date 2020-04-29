---
title: Fájlok másolása több tárolóból
description: Megtudhatja, hogyan másolhat több tárolóból származó fájlokat Azure Data Factory használatával a megoldási sablon használatával.
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
ms.openlocfilehash: 383b70bbb02e7a200c7ec0a994f7cf11e9b9520e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414822"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Több tárolóból származó fájlok másolása Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk egy megoldási sablont ismertet, amellyel több tárolóból is másolhat fájlokat a tárak között. A segítségével például áttelepítheti a adattavat az AWS S3-ból Azure Data Lake Storeba. Másik lehetőségként a sablon használatával replikálhatja az egyik Azure Blob Storage-fiókból a másikba.

> [!NOTE]
> Ha egyetlen tárolóból szeretné átmásolni a fájlokat, hatékonyabban használhatja a [adatok másolása eszközt](copy-data-tool.md) egyetlen másolási tevékenységgel rendelkező folyamat létrehozásához. Az ebben a cikkben szereplő sablon több, mint amire szüksége van az adott egyszerű forgatókönyvhöz.

## <a name="about-this-solution-template"></a>Tudnivalók a megoldási sablonról

Ez a sablon enumerálja a tárolókat a forrás Storage-tárolóból. Ezután átmásolja ezeket a tárolókat a célhelyre.

A sablon három tevékenységet tartalmaz:
- A **GetMetaData** megvizsgálja a forrásként szolgáló tárolót, és lekéri a tárolók listáját.
- A **foreach** lekéri a **GetMetaData** tevékenységből a tárolók listáját, majd megismétli a listát, és átadja az egyes tárolókat a másolási tevékenységnek.
- **Másolja** az egyes tárolókat a forrásként szolgáló tárolóból a célhelyre.

A sablon a következő paramétereket definiálja:
- A *SourceFileFolder* az adatforrás-tároló mappa elérési útja, ahol lekérheti a tárolók listáját. Az elérési út a gyökérkönyvtár, amely több tároló-mappát tartalmaz. A paraméter alapértelmezett értéke a következő: `sourcefolder`.
- A *SourceFileDirectory* az adatforrás-tároló gyökérkönyvtára alatti almappa elérési útja. A paraméter alapértelmezett értéke a következő: `subfolder`.
- A *DestinationFileFolder* az a mappa elérési útja, ahová a rendszer a fájlokat másolja a célhely-tárolóba. A paraméter alapértelmezett értéke a következő: `destinationfolder`.
- A *DestinationFileDirectory* az az almappa elérési útja, ahová a rendszer a fájlokat másolja a célhely-tárolóba. A paraméter alapértelmezett értéke a következő: `subfolder`.

## <a name="how-to-use-this-solution-template"></a>A megoldás sablonjának használata

1. Lépjen a **több fájl másolása tárolók között a fájlmegosztás** sablonba. Hozzon létre egy **új** kapcsolódást a forrás Storage-tárolóhoz. A forrásként szolgáló tár az a hely, ahol a fájlokat több tárolóból kívánja másolni.

    ![Új kapcsolódás létrehozása a forráshoz](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Hozzon létre egy **új** kapcsolódást a cél Storage-tárolóhoz.

    ![Új kapcsolódás létrehozása a célhoz](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Válassza **a sablon használata**lehetőséget.

    ![Sablon használata](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Ekkor megjelenik a folyamat, ahogy az alábbi példában is látható:

    ![A folyamat megjelenítése](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Válassza a **hibakeresés**lehetőséget, adja meg a **paramétereket**, majd kattintson a **Befejezés gombra**.

    ![A folyamat futtatása](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Tekintse át az eredményt.

    ![Az eredmény áttekintése](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>További lépések

- [Tömeges másolás adatbázisból egy Azure Data Factory tartalmazó vezérlőelem-táblázat használatával](solution-template-bulk-copy-with-control-table.md)

- [Több tárolóból származó fájlok másolása Azure Data Factory](solution-template-copy-files-multiple-containers.md)
