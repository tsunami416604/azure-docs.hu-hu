---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: fdcb5bb57afe145b57cb1223242d4d5625a2dfb7
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400770"
---
Beszédfelismerési konfigurációs objektum létrehozásához a Speech erőforrás-előfizetés kulcsa és régiója szükséges. A beszédfelismerési objektum létrehozásához a konfigurációs objektum szükséges.

A felismerő példány több módszert tesz elérhetővé a beszéd felismeréséhez. Ebben a példában a beszédfelismerést egyszer ismeri fel a rendszer. Ez a funkció lehetővé teszi, hogy a beszédfelismerési szolgáltatás biztos legyen abban, hogy egyetlen kifejezést küld az elismeréshez, és hogy ha a kifejezést azonosította a beszédfelismerés felismerésének leállításához. Az eredmény megírása után a kód a konzolra írja az elismerés okát.

> [!TIP]
> A beszédfelismerési SDK alapértelmezés szerint a nyelv használatával `en-us` ismeri fel a nyelvet, a forrás nyelvének kiválasztásával kapcsolatos információkért lásd: a [beszéd nyelvének megadása szöveghez](../../../how-to-specify-source-language.md) .