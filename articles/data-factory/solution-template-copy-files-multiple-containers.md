---
title: Több tároló fájlok másolása az Azure Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan több tárolót az Azure Data Factory használatával fájlok másolása megoldás sablon használatával.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/1/2018
ms.openlocfilehash: a52729adf8d6df3f4e44e561b45b854db433628c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57543423"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Fájlok másolása az Azure Data Factory használatával több tároló

Ez a cikk ismerteti, amelyek segítségével több tároló között fájl tárolja a fájlok másolása megoldás sablon. Például használhatja azt a data lake áttelepítése az AWS S3-ból az Azure Data Lake Store. Vagy használhatja a sablont egy másikra egy Azure Blob storage-fiókból minden replikálni.

> [!NOTE]
> Ha azt szeretné, hogy fájlokat másoljon egy egyetlen tároló, a hatékonyabb, ha használja a [Copy Data eszköz](copy-data-tool.md) egy másolási tevékenységgel rendelkező folyamat létrehozása. Ez a cikk a sablon több mint egyszerű forgatókönyvhöz szükséges.

## <a name="about-this-solution-template"></a>Ez a megoldássablon kapcsolatban

Ez a sablon a tárolók a forrás tárolási áruházból enumerálása. Ezt követően lemásolja készregyártásának a cél-tárolójában.

A sablon a három tevékenységet tartalmaz:
- **GetMetadata** megvizsgálja a forrásadattárba a storage és a tároló listájának beolvasása.
- **ForEach** a tároló listájának beolvasása a **GetMetadata** tevékenység a lista ismétel és a tárolók átadja a másolási tevékenységnek.
- **Másolás** egyes tárolók másol a forrásadattárba a storage tárolási célhelye.

A sablon meghatározza a két paramétert:
- *SourceFilePath* a forrásadattár, ahol a tárolók listáját is megkapja az elérési útja. A legtöbb esetben az elérési út a gyökérkönyvtár, amely több tároló mappát tartalmaz. Az alapértelmezett érték a paraméter `/`.
- *DestinationFilePath* ahol a fájlok lesznek másolva az a cél tároló elérési útja. Az alapértelmezett érték a paraméter `/`.

## <a name="how-to-use-this-solution-template"></a>Ez a megoldássablon használata

1. Nyissa meg a **több fájlokat tároló közötti adatáthelyezéshez fájl másolása** sablont. Hozzon létre egy **új** a forrásadattárba a tárolási kapcsolat. A forrás tárolási tároló, ahová fájlokat másoljon a több tároló.

    ![A forrás egy új kapcsolat létrehozása](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Hozzon létre egy **új** a céltár tárolási kapcsolat.

    ![Hozzon létre egy új kapcsolatot a célhelyre](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Válassza ki **ezzel a sablonnal**.

    ![Sablon használata](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. A folyamat a következő példához hasonlóan jelenik meg:

    ![A folyamat megjelenítése](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Válassza ki **Debug**, adja meg a **paraméterek**, majd válassza ki **Befejezés**.

    ![A folyamat futtatása](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Tekintse át az eredményt.

    ![Tekintse át az eredmény](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>További lépések

- [Tömegesen másolni egy adatbázisból vezérlő táblázat az Azure Data Factory használatával](solution-template-bulk-copy-with-control-table.md)

- [Fájlok másolása az Azure Data Factory használatával több tároló](solution-template-copy-files-multiple-containers.md)