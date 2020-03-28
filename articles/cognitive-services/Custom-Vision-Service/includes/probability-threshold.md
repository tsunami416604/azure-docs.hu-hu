---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 07e7cc991f127bf4bb4f466c0108962786e45bce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68423582"
---
Megjegyzés: A **Valószínűségi küszöbérték** csúszka a **Teljesítmény** lap bal oldali ablaktábláján. Ez az a megbízhatósági szint, amelyet az előrejelzésnek a helyesnek kell tekinteni (a pontosság és a visszahívás kiszámítása céljából). 

Ha nagy valószínűségi küszöbértékkel értelmezi az előrejelzési hívásokat, azok általában&mdash;nagy pontossággal adják vissza az eredményeket a visszahívás rovására az észlelt besorolások helyesek, de sokan észrevétlenek maradnak. Az alacsony valószínűségi küszöbérték&mdash;a tényleges besorolások nagy részét észleli, de több hamis pozitív van a készleten belül. Ezt szem előtt tartva, be kell állítania a valószínűségi küszöbértéket a projekt egyedi igényeinek megfelelően. Később, amikor előrejelzési eredményeket kap az ügyféloldalon, ugyanazt a valószínűségi küszöbértéket kell használnia, mint az itt használt.