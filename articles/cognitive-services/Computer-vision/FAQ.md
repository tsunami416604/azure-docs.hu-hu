---
title: Gyakori kérdések – Computer Vision
titlesuffix: Azure Cognitive Services
description: A Computer Vision API – gyakori kérdések válaszok az Azure Cognitive Servicesben.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 3146650280af80923a4ed9cbb78a5ce6d43e5bce
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53584275"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Computer Vision API – gyakori kérdések

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-computer-vision-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Ha nem találja a kérdésekre adott válaszok a gyakori kérdések, próbálja meg, forduljon a Computer Vision API közösségéhez a [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) , vagy forduljon [Súgó és támogatás a uservoice-ról](https://cognitive.uservoice.com/)

-----

**Kérdés**: *Betaníthatja a Computer Vision API egyéni címkék használata?  Például szeretném hírcsatorna a képek cat fajtájú "be" a mesterséges Intelligencia, majd a fajta értéke egy AI-kérést kap.*

**válasz**: Ez a funkció jelenleg nem érhető el. Azonban a mérnökeinknek a Computer Vision megoldás a létrehozási funkció működik.

-----

**Kérdés**: *Számítógépes Látástechnológia használható helyi internetkapcsolat nélkül?*

**válasz**: Jelenleg nem biztosítunk egy helyszíni vagy a helyi megoldás.

-----

**Kérdés**: *Támogatott nyelvek a Computer Vision?*

**válasz**: A támogatott nyelvek közé tartoznak a következők:

| | | Támogatott nyelvek | | |
|---------------- |------------------ |------------------ |--------------------------- |--------------------
| Dán (da-DK)  | Holland (Hollandia, NL)     | Angol           | Finn (fi-FI)            |Francia (fr-FR)
| Német (de-DE)  | Görög (el-GR)     | Magyar (hu-HU) | Olasz (it-IT)            | Japán (ja-JP)
| Koreai (ko-KR)  | Norvég (nb-NO) | Lengyel (pl-PL)    | Portugál (pt-BR) (pt-PT) | Orosz (ru-RU)
| Spanyol (es-ES)   | Svéd (sv-SV)     | Török (tr-TR)   |                            |

-----

**Kérdés**: *Számítógépes Látástechnológia használható licenc lemezeket olvasni?*

**válasz**: A Vision API-t kínál a helyes szöveg-észlelési OCR-rel, de jelenleg nem optimalizált licenc lemezeket. Folyamatosan keressük szolgáltatásaink javításához és a hozzáadott OCR automatikus licenc blonból elismerési funkciókra vonatkozó kérések listáját.

-----

**Kérdés:** *A kézírás-felismerés támogatott nyelvek?*

**válasz**: Jelenleg csak angol nyelven támogatott.

-----

**Kérdés**: *Milyen típusú Surface-eszközök írása a kézírás-felismerés támogatottak?*

**válasz**: A technológia működését, különböző típusú Surface-eszközök, például táblaképek, tanulmány és sárga jegyzetlapok.

-----

**Kérdés**: *Mennyi időt vesz a kézírás-felismerés műveletet igénybe?*

**válasz**: Az, hogy mennyi ideig tart a szöveg hosszúsága függ. A hosszabb szövegeket, akár is igénybe vehet néhány másodpercig. Ezért a kézzel írott szöveget ismeri fel a művelet befejezése után szükség lehet kell várni, mielőtt az eredményeket az első kézzel írott szöveget művelet eredményének művelettel lehet lekérdezni.

-----

**Kérdés**: *Hogyan kezeli a kézírás-felismerés technológia használatával egy sor közepén kalap beszúrt szöveg?*

**válasz**: Az ilyen szöveget adja vissza, külön sorban a kézírás-felismerés műveletet.

-----

**Kérdés**: *Hogyan kezeli a a kézírás-felismerés technológia a áthúzott szavakat vagy sorok?*

**válasz**: A szavak felismerhetetlen megjelenítése több sorokkal átlépésekor, ha a kézírás-felismerés művelet nem importálja azokat. Azonban a szavakat felszabadíthatják egyetlen sor használatával, ha a metsző zaj számít, és szavak továbbra is első dolgozza fel a kézírás-felismerés műveletet.

-----

**Kérdés**: *A kézírás-felismerés technológia milyen szöveg tájolása támogatottak?*

**válasz**: Szöveg szögben körülbelül 30 fok legfeljebb 40 értéket orientált előfordulhat, hogy első dolgozza fel a kézírás-felismerés műveletet.

-----
