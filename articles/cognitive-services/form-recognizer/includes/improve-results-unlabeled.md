---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: f0761847c3677b324ef16c5987eb9a1561dbcbe0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379291"
---
Vizsgálja `"confidence"` meg az egyes kulcs/érték `"pageResults"` eredmény értékeit a csomópont alatt. Azt is meg kell néznie `"readResults"` a megbízhatósági pontszámok a csomópont, amely megfelel a szöveg olvasási művelet. Az olvasási eredmények megbízhatósága nem befolyásolja a kulcs/érték kinyerési eredmények megbízhatóságát, ezért mindkettőt ellenőrizze.
* Ha az olvasási művelet megbízhatósági pontszámai alacsonyak, próbálja meg javítani a bemeneti dokumentumok minőségét [(lásd: Bemeneti követelmények).](../overview.md#input-requirements)
* Ha a kulcs/érték kinyerési művelet megbízhatósági pontszámai alacsonyak, győződjön meg arról, hogy az elemzett dokumentumok azonos típusúak, mint a betanítási készletben használt dokumentumok. Ha a betanítási készlet dokumentumai megjelenésük eltérő, fontolja meg a különböző mappákra való felosztásukat, és minden változathoz betanítási modellt.

A megcélzott megbízhatósági pontszámok a használati esettől függenek, de általában jó gyakorlat a 80%-os vagy annál magasabb pontszámot megcélozni. Érzékenyebb esetekben, például orvosi feljegyzések vagy számlakivonatok olvasása esetén 100%-os pontszám ajánlott.