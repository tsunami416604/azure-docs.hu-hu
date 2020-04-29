---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 11783f8fac147e6fb2cf371ee1a588cf318a5e9c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75379432"
---
Az űrlap-felismerő a következő követelményeknek megfelelő bemeneti dokumentumokon működik:

* A formátumnak JPG, PNG, PDF (Text vagy beolvasott) vagy TIFF formátumúnak kell lennie. A Text-Embedded PDF-fájlok a legjobbak, mert nem lehetséges a hibák kinyerése és helye.
* Ha a PDF-fájlok jelszóval vannak zárolva, a küldés előtt el kell távolítania a zárolást.
* A PDF-és a TIFF-dokumentumnak 200 vagy annál kisebbnek kell lennie, és a betanítási adatkészlet teljes méretének 500 vagy annál kisebbnek kell lennie.
* Képek esetén a méretek 600 x 100 képpont és 4200 x 4200 képpont közé kell, hogy legyenek.
* Ha papíralapú dokumentumokból olvas be dokumentumokat, az űrlapoknak kiváló minőségű vizsgálatokat kell ellátniuk.
* A szövegnek a latin ábécét (angol karakter) kell használnia.
* A nem felügyelt tanulás (címkézett adatok nélkül) esetében az adatoknak kulcsokat és értékeket kell tartalmazniuk.
* A nem felügyelt tanulás (címkézett adatok nélkül) esetében a kulcsoknak az értékek felett vagy balra kell megjelenniük; nem jelennek meg a jobb oldalon.

Az űrlap-felismerő jelenleg nem támogatja az ilyen típusú bemeneti adatokat:

* Összetett táblák (beágyazott táblák, egyesített fejlécek vagy cellák stb.).
* Jelölőnégyzetek és választógombok.
