---
title: Az Azure Data Factory-folyamatot helyettes kulcs átalakítását leképezése
description: Az Azure Data Factory-folyamatot helyettes kulcs átalakítását leképezése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 62e9879f6bc75f042669ecb931ca7459d1317cc7
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271850"
---
# <a name="azure-data-factory-mapping-data-flow-surrogate-key-transformation"></a>Az Azure Data Factory-folyamatot helyettes kulcs átalakítását leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A helyettes kulcs átalakítási használatával adja hozzá az adatokat a folyamat sorkészlet növekvő nem üzleti tetszőleges kulcs értéket. Ez akkor hasznos, dimenziótáblák csillag sémában analitikai adatmodellt ahol a dimenziótáblák minden tagjának rendelkeznie kell egy egyedi kulcsot Kimball DW módszertan nem üzleti kulcs részét képező tervezésekor.

![Kulcs átalakító helyettesítő](media/data-flow/surrogate.png "helyettesítő kulcs átalakítása")

"Kulcsoszlop" Ez a név meg kell adnia az új helyettes kulcs oszlopra.

A növekményes érték kezdőpontját "Kezdeti érték".
