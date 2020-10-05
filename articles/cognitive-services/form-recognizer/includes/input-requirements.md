---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 5de121a1a905a58f8b5eaf8e60c1f8da71ee8cd6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91276942"
---
Az űrlap-felismerő a következő követelményeknek megfelelő bemeneti dokumentumokon működik:

* A formátumnak JPG, PNG, PDF (Text vagy beolvasott) vagy TIFF formátumúnak kell lennie. A Text-Embedded PDF-fájlok a legjobbak, mert nem lehetséges a hibák kinyerése és helye.
* A fájlméretnek 20 MB-nál kisebbnek kell lennie.
* A képdimenziónak 50 x 50 képpont és 10000 x 10000 képpont közé kell esnie.
* A PDF-dimenzióknak legfeljebb 17 x 17 hüvelyknek kell lenniük, amely a jogi vagy az A3-as papírméretnek felel meg, és kisebb.
* A PDF és a TIFF esetében csak az első 200 oldal lesz feldolgozva (ingyenes rétegbeli előfizetéssel csak az első két oldal feldolgozása történik).
* A betanítási adathalmaz teljes méretének 500 vagy annál kisebbnek kell lennie.
* Ha a PDF-fájlok jelszóval vannak zárolva, a küldés előtt el kell távolítania a zárolást.
* Ha papíralapú dokumentumokból olvas be dokumentumokat, az űrlapoknak kiváló minőségű vizsgálatokat kell ellátniuk.
* A szövegnek a latin ábécét (angol karakter) kell használnia.
* A nem felügyelt tanulás (címkézett adatok nélkül) esetében az adatoknak kulcsokat és értékeket kell tartalmazniuk.
* A nem felügyelt tanulás (címkézett adatok nélkül) esetében a kulcsoknak az értékek felett vagy balra kell megjelenniük; nem jelennek meg a jobb oldalon.

Az űrlap-felismerő jelenleg nem támogatja az ilyen típusú bemeneti adatokat:

* Összetett táblák (beágyazott táblák, egyesített fejlécek vagy cellák stb.).
* Jelölőnégyzetek és választógombok.
