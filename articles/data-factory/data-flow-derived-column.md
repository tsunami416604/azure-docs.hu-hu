---
title: Az Azure Data Factory adatfolyam származtatott oszlop átalakítása
description: Az Azure Data Factory adatfolyam származtatott oszlop átalakítása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aee005aed52563e214e24148af2563fd7869de76
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271808"
---
# <a name="azure-data-factory-data-flow-derived-column-transformation"></a>Az Azure Data Factory adatfolyam származtatott oszlop átalakítása

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A származtatott oszlop átalakítása használja, az adatfolyam létrehozni az új oszlopok vagy a meglévő mezők módosítása.

![oszlop célosztályából](media/data-flow/dc1.png "származtatott oszlop")

Több származtatott oszlopot művelet egyetlen származtatott oszlopot átalakítás végrehajthatja. Kattintson az "Oszlop hozzáadása" átalakítja a 1-nél több oszlopra az átalakítást egyetlen lépésben.

Az oszlop mezőben válassza ki egy létező oszlop felülírása az új származtatott érték, vagy kattintson a "Hozzon létre új oszlop" egy az újonnan származtatott értéket tartalmazó új oszlop létrehozása.

A kifejezés szövegmező megnyílik a Kifejezésszerkesztő, ahol hozhat létre a kifejezés a származtatott oszlopok kifejezés függvények használatával.
