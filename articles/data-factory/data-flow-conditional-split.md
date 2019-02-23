---
title: Az Azure Data Factory-folyamat feltételes Split adatátalakítás leképezése
description: Az Azure Data Factory folyamat feltételes Split átalakítását
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: a4ea79e05165dfae4f79aa6473a07151ba7c00fc
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727808"
---
# <a name="azure-data-factory-mapping-data-flow-conditional-split-transformation"></a>Az Azure Data Factory-folyamat feltételes Split adatátalakítás leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A feltételes Split átalakítást is adatsor átirányítása a adatok tartalma függően különböző Streamek. A feltételes Split átalakítás megvalósítása nagyon hasonlít egy kis döntési struktúra programozási nyelven. Az átalakítás kiértékeli a kifejezések, és az eredmények alapján, arra utasítja a megadott streamet az adatok sor. Az átalakítás is biztosít egy alapértelmezett kimenet, úgy, hogy ha egy sor kifejezés nem egyezik az alapértelmezett kimenet irányítja.

![feltételes split](media/data-flow/cd1.png "feltételes felosztása")

További feltételek hozzáadása, konfigurációs alsó ablaktábláján válassza a "Stream hozzáadása", majd kattintson a Kifejezésszerkesztő szövegmezőben hozhat létre a kifejezés.
