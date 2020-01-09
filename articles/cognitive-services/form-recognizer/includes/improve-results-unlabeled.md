---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: f0761847c3677b324ef16c5987eb9a1561dbcbe0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379291"
---
Vizsgálja meg az egyes kulcs/érték eredmény `"confidence"` értékeit a `"pageResults"` csomópont alatt. Érdemes megtekinteni a `"readResults"` csomópontban található megbízhatósági pontszámokat is, amelyek a szöveges olvasási műveletnek felelnek meg. Az olvasási eredmények megbízhatósága nem befolyásolja a kulcs/érték kinyerési eredményeinek megbízhatóságát, ezért mindkettőt ellenőriznie kell.
* Ha az olvasási művelet megbízhatósági pontszáma alacsony, próbálja meg javítani a bemeneti dokumentumok minőségét (lásd a [bemeneti követelményeket](../overview.md#input-requirements)).
* Ha a kulcs/érték kibontási műveletének megbízhatósági pontszáma alacsony, ügyeljen arra, hogy az elemzett dokumentumok ugyanolyan típusúak legyenek, mint a betanítási készletben használt dokumentumok. Ha a betanítási készlet dokumentumaiban változások vannak kialakítva, érdemes lehet különböző mappákba bontani őket, és minden egyes változathoz egy modellt betanítani.

A megcélzott megbízhatósági pontszámok a használati esettől függenek, de általában jó gyakorlat a 80%-os vagy újabb pontszám megcélzására. Az olyan érzékeny esetekben, mint például az orvosi rekordok vagy a számlázási utasítások beolvasása, 100%-os pontszám ajánlott.