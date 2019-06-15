---
title: Az Azure Data Factory-folyamat feltételes Split adatátalakítás leképezése
description: Az Azure Data Factory folyamat feltételes Split átalakítását
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: f9fd346d4c4eaed0797d564fe52dd44e9f0e240a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65795640"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Leképezés adatfolyam feltételes átalakítási felosztása

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![felosztott eszközkészlet feltételes](media/data-flow/conditionalsplit2.png "feltételes eszközkészlet felosztása")

A feltételes Split átalakítást is adatsor átirányítása a adatok tartalma függően különböző Streamek. A feltételes Split átalakítás megvalósítása nagyon hasonlít egy kis döntési struktúra programozási nyelven. Az átalakítás kiértékeli a kifejezések, és az eredmények alapján, arra utasítja a megadott streamet az adatok sor. Az átalakítás is biztosít egy alapértelmezett kimenet, úgy, hogy ha egy sor kifejezés nem egyezik az alapértelmezett kimenet irányítja.

![feltételes split](media/data-flow/conditionalsplit1.png "feltételes vágási lehetőségek")

## <a name="multiple-paths"></a>Több elérési út

További feltételek hozzáadása, konfigurációs alsó ablaktábláján válassza a "Stream hozzáadása", majd kattintson a Kifejezésszerkesztő szövegmezőben hozhat létre a kifejezés.

![többszörös felosztása feltételes](media/data-flow/conditionalsplit3.png "felosztása több feltételes")

## <a name="next-steps"></a>További lépések

Általános folyamat adatátalakítások feltételes split használják: [Csatlakozzon az átalakítási](data-flow-join.md), [Loopup átalakítási](data-flow-lookup.md), [átalakítási kiválasztása](data-flow-select.md)
