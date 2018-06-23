---
title: Gyakori kérdések az a számítógép Látástechnológiai API |} Microsoft Docs
description: A számítógép Látástechnológiai API-val kapcsolatban gyakran feltett kérdésekre válaszok kognitív Microsoft-szolgáltatásokban.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 01/26/2017
ms.author: kefre
ms.openlocfilehash: 5c862dd2fb26a005f4e785673a4e9358ecf9286f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346979"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Gyakori kérdések a számítógép Látástechnológiai API
### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-computer-vision-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Ha ez a GYIK a kérdésekre adott válaszok nem található, próbálja meg a számítógép Látástechnológiai API-Közösség kérése a [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) , vagy forduljon [Súgó és támogatás a UserVoice](https://cognitive.uservoice.com/)

-----

**Kérdés**: *is szeretnék betanítása számítógép Látástechnológiai API egyéni címkék használata?  Például szeretnék hírcsatorna a képek cat fajtájú "vonat" a AI, majd a fajta érték egy AI kérelemre.*

**Válasz**: Ez a funkció jelenleg nem érhető el. Azonban a mérnökök arra törekednek, hogy ez a funkció számítógép stratégiai.

-----

**Kérdés**: *is számítógép stratégiai helyileg használható internetkapcsolat nélkül?*

**Válasz**: azonban jelenleg nem készül egy helyszíni vagy a helyi megoldás.

-----

**Kérdés**: *rendelkező számítógép stratégiai támogatott nyelvek?*

**Válasz**: támogatott nyelvek:

| | | Támogatott nyelvek | | |
|---------------- |------------------ |------------------ |--------------------------- |--------------------
| Dán (da-DK)  | Holland (nl-NL)     | Angol           | Finn (fi-FI)            |Francia (fr-FR)
| Német (de-DE)  | Görög (el-GR)     | Magyar (hu-HU) | Olasz (it-IT)            | Japán (ja-JP)
| Koreai (ko-KR)  | Norvég (nb-NO) | Lengyel (pl-PL)    | Portugál (pt-BR) (pt-PT) | Orosz (ru-RU)
| Spanyol (es-ES)   | Svéd (sv-SV)     | Török (tr-SO)   |                            |

-----

**Kérdés**: *számítógép stratégiai is használható beolvasásához licenc lemezeket?*

**Válasz**: A stratégiai API lehetővé teszi a helyes szöveg-észlelési OCR rendelkező, de jelenleg nem optimalizált licenc lemezeket. Folyamatosan keressük szolgáltatásaink javítására és hozzá vannak adva OCR automatikus licenc lemez felismeréséhez funkciókra vonatkozó kérések listáját.

-----

**Kérdés:** *a kézírás-felismerés támogatott nyelvek?*

**Válasz**: jelenleg csak angol nyelvű használata támogatott.

-----

**Kérdés**: *milyen típusú felületek írása a kézírás-felismerés támogatottak?*

**Válasz**: A technológia működik együtt a különböző típusú felületek, beleértve a tábla, dokumentáció és sárga jegyzeteket.

-----

**Kérdés**: *mennyi időt a kézírás-felismerési művelet igénybe?*

**Válasz**: A szükséges idő mértékét a szöveg hosszát. A hosszabb szövegeket azt is eltarthat néhány másodpercig. Ezért a kézzel szöveg felismerése művelet befejezése után szükség lehet eltelte után az eredményeket az beszerzése kézzel szöveg műveleti eredmény művelet használatával kérheti le.

-----

**Kérdés**: *hogyan működik a kézírás-felismerés felismerés technológia leíró szöveg egy sor közepén kalap beszúrt?*

**Válasz**: ilyen szöveget adja vissza, külön sorban a kézírás-felismerési műveletet.

-----

**Kérdés**: *hogyan nem a kézírás-felismerési technológia kezeli a áthúzott szavak vagy sorok?*

**Válasz**: a szavakat felismerhetetlen leképezése több sort tartalmazó túllépése, ha a kézírás-felismerési művelet nem importálja őket. Azonban a szavakat rendszer átlépte, egy sorba használatával, ha adott metsző zaj a rendszer, és a szavakat továbbra is beolvasása észlelnie a kézírás-felismerési műveletet.

-----

**Kérdés**: *milyen szöveg tájolása a kézírás-felismerési technológia támogatottak?*

**Válasz**: szögben körülbelül 30 fok legfeljebb 40 fokra célú szöveg előfordulhat, hogy első észlelnie a kézírás-felismerési műveletet.

-----
