---
title: Gyakori kérdések a Arcfelismerési API-szolgáltatáshoz |} Microsoft Docs
description: Az alábbiakban a Arcfelismerési API-szolgáltatás a legnépszerűbb kérdésekre adott válaszok.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: da2f75deef8a8beea3ba23b6a39eb6d2fe104b54
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346990"
---
# <a name="face-api-frequently-asked-questions"></a>Gyakori kérdések a arcfelismerési API
### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-face-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Ha ez a GYIK a kérdésekre adott válaszok nem található, próbálja meg a Arcfelismerési API-Közösség kérése a [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) , vagy forduljon a Súgó és támogatás [UserVoice](https://cognitive.uservoice.com/).

-----
**Kérdés**: Mi tényezők csökkentheti Arcfelismerési API pontossága felismerés, ellenőrző vagy hasonló?

**Válasz**: általában célszerű a azonos esetekben, amikor az emberek nehezen valaki beleértve; azonosító
* Legalább az egyik szem blokkolja akadályok
* Szélsőséges megvilágítási, pl. súlyos háttérvilágítás
* Haj stílus vagy és haj módosításai
* Miatti kora
* Szélsőséges arcfelismerést kifejezés (pl. screaming)

Arcfelismerési API-t gyakran ilyen esetekben kihívást sikeres, de pontossága csökkenthető. Felismerés robusztusabb, és ezek a kihívások cím, a fényképek, amely tartalmazza az a szög és megvilágítási sokféle rendelkező személy betanításához.

-----
**Kérdés**: I vagyok átadja a bináris adatok, de "érvénytelen arcfelismerési lemezképpel" hiba jelenik meg.

**Válasz**: Ez azt jelenti, hogy az algoritmus volt-e a kép elemzése hibát. Okok a következők:
* A bemeneti formátum támogatott JPEG, PNG, (az első keret) GIF, BMP tartalmazza.
* Kép fájlmérete lehet 4MB-nál nagyobb
* A észlelhető arcfelismerési mérete tartománya 36 x 36 a 4096 x 4096 képpontban megadva. A tartományon kívül lapok nem észlelhető
* Bizonyos lapok műszaki akadályok, például a nagyon nagy arcfelismerési szögek (head-jelentő), mert nem észlelhető nagy hangelnyelés. Elülső és közelében elülső lapok van a legjobb eredmények elérése érdekében

-----
