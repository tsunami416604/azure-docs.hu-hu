---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 816fa4b0df2fab77bcde261f30d8bad584257fd7
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592615"
---
Űrlap felismerő működik a fenti követelményeknek megfelelő bemeneti dokumentum:

* Formátumban kell lennie, JPG, PNG vagy PDF-fájl (szöveges vagy beolvasott). Beágyazott szöveg PDF-fájlok nincsenek ajánlott, mert nem fordulhat elő hiba karakterfelismerés és helyen.
* Fájl mérete kisebb, mint 4 megabájt (MB) kell lennie.
* Képek a dimenziók 50 x 50 képpont méretű és 4200 x 4200 képpont között kell lennie.
* Nyomtatott dokumentumok gépről beolvasott, űrlapok magas színvonalú vizsgálatok kell lennie.
* Szöveg kell használnia a Latin ábécében (angol nyelvű karakter).
* Adatok (nem kézzel írott) kell nyomtatni.
* Kulcsokat és értékeket kell tartalmaznia.
* Kulcsokkal fölött, vagy az értékek, de nem alábbi balra vagy jobbra is megjelenhetnek.

Űrlap felismerő jelenleg nem támogatja az ilyen típusú bemeneti adatok:

* Összetett táblázatok (beágyazott táblákat, egyesített fejlécek vagy cellák és így tovább).
* Jelölőnégyzetek vagy választógomb gombot.
* PDF-dokumentumok hosszabb, mint az 50.