---
title: Gyakori kérdések – Face API
titlesuffix: Azure Cognitive Services
description: Az alábbiakban a Face API szolgáltatás legnépszerűbb kérdésekre adott válaszok.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: b4b2c09ef608da7c52d415d5f1f2215ddc31c41a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223323"
---
# <a name="face-api-frequently-asked-questions"></a>A Face API – gyakori kérdések

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-face-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Ha nem találja a kérdésekre adott válaszok a gyakori kérdések, próbálja meg, forduljon a Face API közösségéhez a [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) , vagy forduljon a Súgó és támogatás a [UserVoice](https://cognitive.uservoice.com/).

-----
**Kérdés**: Milyen tényezők csökkentheti a Face API pontossága Recognition, ellenőrző vagy hasonló?

**válasz**: Általában az azonos esetekben, amikor az emberek azonosítása valaki; például nehéz legyen.
* Akadályok egyik vagy mindkét szemet blokkolása
* Háttérvilágítás szélsőséges világító, például: súlyos
* Stílus és videofelismerést, arc haj haj módosításai
* Módosítások miatt kora
* Szélsőséges arckifejezések (pl. screaming)

A Face API gyakran ilyen esetekben kihívást sikeres, de pontossága csökkenthető. Elismerési robusztusabb és ezek a kihívások megoldása, a fényképeket, amelyek tartalmazzák a sokféle szögek és megvilágítási rendelkező személy betanításához.

-----
**Kérdés**:  E vagyok átadja a bináris adatok, de a "érvénytelen arcképét" hibaüzenetet kapok.

**válasz**:  Ez azt jelenti, hogy az algoritmus a lemezkép-elemzés hiba történt. Okok a következők:
* A bemeneti formátum támogatott JPEG, PNG, (az első képkocka) GIF és BMP tartalmazza.
* Kép fájlmérete kell nem lehet nagyobb, mint 4MB
* Az észlelhető oldallal mérete tartománya 36 x 36, 4096 x 4096 képpont. A tartományon arcok észlelése nem történik meg
* Néhány arcok észlelése helytelen lehet kihívások, például a nagyon nagy face szög (head-testtartás), mert nagy hangelnyelés. Elülső és a közel-elülső arcok rendelkezik a legjobb eredmények elérése érdekében

-----
