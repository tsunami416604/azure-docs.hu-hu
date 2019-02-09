---
title: Fájlok másolása több tárolót az Azure Data Factoryvel |} A Microsoft Docs
description: Ismerje meg, hogyan több tárolót az Azure Data Factoryvel fájlok másolása megoldás sablon használatával.
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
ms.openlocfilehash: aa5f32594c295ab6a8e60af8359370f64f75a72d
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967266"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Fájlok másolása az Azure Data Factory használatával több tároló

A jelen cikkben ismertetett megoldássablon segít, hogy fájlokat másoljon több fájlok, tárolók vagy gyűjtők között fájl tárolja. Például esetleg szeretné áttelepíteni a data lake az AWS S3 az Azure Data Lake Store. Vagy egy másik Azure Blob Storage-fiók replikálása egy Azure Blob Storage-fiókból mindent szeretné. Ez a sablon célja az ezeket a használati esetek.

Ha azt szeretné, hogy fájlokat másoljon egy egyetlen tároló és a gyűjtő sokkal hatékonyabban, használatára a **Copy Data eszköz** egy másolási tevékenységgel rendelkező folyamat létrehozása. Ezzel a sablonnal a több mint meg kell ezt az egyszerű használati eset.

## <a name="about-this-solution-template"></a>Ez a megoldássablon kapcsolatban

Ez a sablon enumerálása a tárolók a forrás tárolási áruházból, és majd másolatot minden, a tárolók a forrás-storage-ból a cél tároló tárolja. 

A sablon a három tevékenységet tartalmaz:
-   A **GetMetadata** vizsgálata a forrásadattárba a storage és a tároló-lista lekérése tevékenység.
-   A **ForEach** tevékenységet, hogy a tároló-lista lekérése a **GetMetadata** tevékenység majd megismételheti a listában, és adja át az egyes tárolók a másolási tevékenységnek.
-   A **másolási** tevékenység, amely a cél a másolhatja az egyes tárolók a forrás tárolási áruházból.

A sablon meghatározza a két paramétert:
-   A paraméter *SourceFilePath* a forrásadattár, ahol a tárolók vagy gyűjtők listáját is megkapja az elérési útja. A legtöbb esetben az elérési út a gyökérkönyvtár, amely több tároló mappát tartalmaz. Az alapértelmezett érték a paraméter `/`.
-   A paraméter *DestinationFilePath* az elérési utat, ahol a fájlok lesznek másolva a cél tárolóban van. Az alapértelmezett érték a paraméter `/`.

## <a name="how-to-use-this-solution-template"></a>Ez a megoldássablon használata

1. Lépjen a sablon **több fájlokat tároló közötti adatáthelyezéshez fájl másolása**, és hozzon létre egy **új kapcsolat** a forrás storage tárolójában. A forrás tárolási tároló a helyre, ahol a fájlok másolása több tárolók vagy gyűjtőkbe.

    ![A forrás egy új kapcsolat létrehozása](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Hozzon létre egy **új kapcsolat** a rendeltetési tárolási tárolójában.

    ![Hozzon létre egy új kapcsolatot a célhelyre](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Kattintson a **ezzel a sablonnal**.

    ![Sablon használata](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. A folyamat a panelen érhető el az alábbi példában látható módon jelenik meg:

    ![A folyamat megjelenítése](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Kattintson a **Debug**, bemeneti **paraméterek** kattintson **Befejezés**.

    ![A folyamat futtatása](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Tekintse át az eredményt.

    ![Tekintse át az eredmény](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>További lépések

- [Az Azure Data Factory bemutatása](introduction.md)
