---
title: Az Azure Data Factory-folyamat feltételes Split adatátalakítás leképezése
description: Az Azure Data Factory folyamat feltételes Split átalakítását
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 9fe650465160ab837d8c8c191887d0f952976682
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271806"
---
# <a name="azure-data-factory-mapping-data-flow-conditional-split-transformation"></a>Az Azure Data Factory-folyamat feltételes Split adatátalakítás leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A feltételes Split átalakítást is adatsor átirányítása a adatok tartalma függően különböző Streamek. A feltételes Split átalakítás megvalósítása nagyon hasonlít egy kis döntési struktúra programozási nyelven. Az átalakítás kiértékeli a kifejezések, és az eredmények alapján, arra utasítja a megadott streamet az adatok sor. Az átalakítás is biztosít egy alapértelmezett kimenet, úgy, hogy ha egy sor kifejezés nem egyezik az alapértelmezett kimenet irányítja.

![feltételes split](media/data-flow/cd1.png "feltételes felosztása")

További feltételek hozzáadása, konfigurációs alsó ablaktábláján válassza a "Stream hozzáadása", majd kattintson a Kifejezésszerkesztő szövegmezőben hozhat létre a kifejezés.
