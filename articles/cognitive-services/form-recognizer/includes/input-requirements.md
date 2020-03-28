---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 11783f8fac147e6fb2cf371ee1a588cf318a5e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75379432"
---
Az űrlapfelismerő az alábbi követelményeknek megfelelő bemeneti dokumentumokon dolgozik:

* A formátumnak JPG, PNG, PDF (szöveg vagy beolvasott) vagy TIFF formátumnak kell lennie. A szövegbe ágyazott PDF-dokumentumok azért a legjobbak, mert a karakterek kinyerése és helye nem tartalmaz hibát.
* Ha a PDF-fájlok jelszóval zárolva vannak, a beküldés előtt el kell távolítania a zárolást.
* A PDF- és TIFF-dokumentumoknak legkisebbméretűnek kell lenniük, és a betanítási adatkészlet teljes méretének legkisebbnek kell lennie.
* Képek esetén a méreteknek 600 x 100 és 4200 x 4200 képpont között kell lenniük.
* Ha papíralapú dokumentumokból olvassa be, a formanyomtatványok kiváló minőségű vizsgálatoknak kell lenniük.
* A szövegnek a latin ábécét (angol karaktereket) kell használnia.
* Felügyelet nélküli tanulás (címkézett adatok nélkül) az adatoknak kulcsokat és értékeket kell tartalmazniuk.
* A felügyelet nélküli tanuláshoz (címkézett adatok nélkül) a kulcsoknak az értékek felett vagy a bal oldalán kell megjelenniük; nem jelenhetnek meg alatta vagy jobbra.

Az Űrlapfelismerő jelenleg nem támogatja az ilyen típusú bemeneti adatokat:

* Összetett táblázatok (beágyazott táblázatok, egyesített fejlécek vagy cellák, és így tovább).
* Jelölőnégyzetek vagy választógombok.
