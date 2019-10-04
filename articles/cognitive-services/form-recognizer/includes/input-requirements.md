---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 4079b0cab0126a9750bbb91ca7046aa18dbe9ece
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657797"
---
Az űrlap-felismerő a következő követelményeknek megfelelő bemeneti dokumentumokon működik:

* A formátumnak JPG, PNG vagy PDF formátumúnak kell lennie (szöveges vagy beolvasott). A Text-Embedded PDF-fájlok a legjobbak, mert nem lehetséges a hibák kinyerése és helye.
* Ha a PDF-fájlok jelszóval vannak zárolva, a küldés előtt el kell távolítania a zárolást.
* A fájlméretnek 4 MB-nál kisebbnek kell lennie.
* Képek esetén a méretek 600 x 100 képpont és 4200 x 4200 képpont közé kell, hogy legyenek.
* Ha papíralapú dokumentumokból olvas be dokumentumokat, az űrlapoknak kiváló minőségű vizsgálatokat kell ellátniuk.
* A szövegnek a latin ábécét (angol karakter) kell használnia.
* Az adatoknak kulcsokat és értékeket kell tartalmazniuk.
* A kulcsok megjelenhetnek a fent vagy az értékek bal oldalán, de nem lent vagy jobbra.

Az űrlap-felismerő jelenleg nem támogatja az ilyen típusú bemeneti adatokat:

* Összetett táblák (beágyazott táblák, egyesített fejlécek vagy cellák stb.).
* Jelölőnégyzetek és választógombok.
* A PDF-dokumentumok 50 oldalnál hosszabbak.
