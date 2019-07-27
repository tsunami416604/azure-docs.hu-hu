---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 17dc32f8948387b90229d3c4c07102cff98e3018
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562682"
---
Az űrlap-felismerő a következő követelményeknek megfelelő bemeneti dokumentumokon működik:

* A formátumnak JPG, PNG vagy PDF formátumúnak kell lennie (szöveges vagy beolvasott). A Text-Embedded PDF-fájlok a legjobbak, mert nem lehetséges a hibák kinyerése és helye.
* Ha a PDF-fájlok jelszóval vannak zárolva, a küldés előtt el kell távolítania a zárolást.
* A fájlméretnek 4 MB-nál kisebbnek kell lennie.
* Képek esetén a méretek 50 x 50 képpont és 4200 x 4200 képpont közé kell, hogy legyenek.
* Ha papíralapú dokumentumokból olvas be dokumentumokat, az űrlapoknak kiváló minőségű vizsgálatokat kell ellátniuk.
* A szövegnek a latin ábécét (angol karakter) kell használnia.
* Az adatoknak kulcsokat és értékeket kell tartalmazniuk.
* A kulcsok megjelenhetnek a fent vagy az értékek bal oldalán, de nem lent vagy jobbra.

Az űrlap-felismerő jelenleg nem támogatja az ilyen típusú bemeneti adatokat:

* Összetett táblák (beágyazott táblák, egyesített fejlécek vagy cellák stb.).
* Jelölőnégyzetek és választógombok.
* A PDF-dokumentumok 50 oldalnál hosszabbak.