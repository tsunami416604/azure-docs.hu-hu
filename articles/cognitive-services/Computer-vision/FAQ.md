---
title: A Computer Vision API vonatkozó gyakori kérdések
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
ms.openlocfilehash: 55b474d0edbb8dc01b9f35d4f8799e53e37862df
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166372"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Computer Vision API – gyakori kérdések

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-computer-vision-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Ha nem találja a kérdésekre adott válaszok a gyakori kérdések, próbálja meg, forduljon a Computer Vision API közösségéhez a [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) , vagy forduljon [Súgó és támogatás a uservoice-ról](https://cognitive.uservoice.com/)

-----

**Kérdés**: *betaníthatja I Computer Vision API egyéni címkék használata?  Például szeretném hírcsatorna a képek cat fajtájú "be" a mesterséges Intelligencia, majd a fajta értéke egy AI-kérést kap.*

**Válasz**: Ez a funkció jelenleg nem érhető el. Azonban a mérnökeinknek a Computer Vision megoldás a létrehozási funkció működik.

-----

**Kérdés**: *is számítógépes Látástechnológiai helyileg használható internetkapcsolat nélkül?*

**Válasz**: jelenleg nem biztosítunk egy helyszíni vagy a helyi megoldás.

-----

**Kérdés**: *támogatott nyelvek a Computer Vision?*

**Válasz**: támogatott nyelvek:

| | | Támogatott nyelvek | | |
|---------------- |------------------ |------------------ |--------------------------- |--------------------
| Dán (da-DK)  | Holland (Hollandia, NL)     | Angol           | Finn (fi-FI)            |Francia (fr-FR)
| Német (de-DE)  | Görög (el-GR)     | Magyar (hu-HU) | Olasz (it-IT)            | Japán (ja-JP)
| Koreai (ko-KR)  | Norvég (nb-NO) | Lengyel (pl-PL)    | Portugál (pt-BR) (pt-PT) | Orosz (ru-RU)
| Spanyol (es-ES)   | Svéd (sv-SV)     | Török (tr-TR)   |                            |

-----

**Kérdés**: *is számítógépes Látástechnológiai licenc lemezeket kiolvasásához használandó?*

**Válasz**: A Látástechnológiai API lehetővé teszi a helyes szöveg-észlelési OCR-rel, de jelenleg nem optimalizált licenc lemezeket. Folyamatosan keressük szolgáltatásaink javításához és a hozzáadott OCR automatikus licenc blonból elismerési funkciókra vonatkozó kérések listáját.

-----

**Kérdés:** *támogatott nyelvek a kézírás-felismerés?*

**Válasz**: jelenleg csak angol nyelven támogatott.

-----

**Kérdés**: *milyen típusú Surface-eszközök írása a kézírás-felismerés támogatottak?*

**Válasz**: A technológia működését, különböző típusú Surface-eszközök, például táblaképek, tanulmány és sárga jegyzetlapok.

-----

**Kérdés**: *mennyi a kézírás-felismerés művelet tart?*

**Válasz**: az, hogy mennyi ideig tart a szöveg hosszúsága függ. A hosszabb szövegeket, akár is igénybe vehet néhány másodpercig. Ezért a kézzel írott szöveget ismeri fel a művelet befejezése után szükség lehet kell várni, mielőtt az eredményeket az első kézzel írott szöveget művelet eredményének művelettel lehet lekérdezni.

-----

**Kérdés**: *hogyan történik a kézírás-felismerés felismerés technológia leíró szöveg, amelyben egy sor közepén kalap beillesztett?*

**Válasz**: ilyen szöveget adja vissza, külön sorban a kézírás-felismerés műveletet.

-----

**Kérdés**: *hogyan a kézírás-felismerés technológia kezeli áthúzott szavakat vagy sorok?*

**Válasz**: a szavakat átlépésekor több sorokkal felismerhetetlen megjelenítése, ha a kézírás-felismerés művelet nem importálja azokat. Azonban a szavakat felszabadíthatják egyetlen sor használatával, ha a metsző zaj számít, és szavak továbbra is első dolgozza fel a kézírás-felismerés műveletet.

-----

**Kérdés**: *milyen szöveg tájolása a kézírás-felismerés technológia támogatottak?*

**Válasz**: szöveg szögben körülbelül 30 fok legfeljebb 40 értéket orientált előfordulhat, hogy első dolgozza fel a kézírás-felismerés műveletet.

-----
