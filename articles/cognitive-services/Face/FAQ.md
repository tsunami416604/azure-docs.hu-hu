---
title: Gyakori kérdések – Face API
titlesuffix: Azure Cognitive Services
description: Az alábbiakban a Face API szolgáltatás legnépszerűbb kérdésekre adott válaszok.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: 47ce80e1b0cefc01752d2445b751ebe1c2d65d08
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351329"
---
# <a name="face-api-frequently-asked-questions"></a>A Face API – gyakori kérdések

> [!TIP]
> Ha nem találja a kérdésekre adott válaszok a gyakori kérdések, próbálja meg, forduljon a Face API közösségéhez a [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) , vagy forduljon a Súgó és támogatás a [UserVoice](https://cognitive.uservoice.com/).

-----
**Kérdés**: Milyen tényezők csökkentheti a Face API pontossága Recognition, ellenőrző vagy hasonló?

**válasz**: Általában célszerű a az azonos esetekben, amikor az emberek nehézség azonosítása valaki többek között:
* Akadályok egyik vagy mindkét szemet blokkolása
* Szélsőséges világítás (például súlyos háttérvilágítás)
* Stílus és videofelismerést, arc haj haj módosításai
* Módosítások miatt kora
* Szélsőséges arckifejezések (például screaming)

A Face API gyakran nagy kihívást jelentő esetekben, például a fenti sikeres, de pontossága csökkenthető. Elismerési robusztusabb és ezek a kihívások megoldása, a fényképeket, amelyek tartalmazzák a sokféle szögek és megvilágítási rendelkező személy betanításához.

-----
**Kérdés**:  E vagyok átadja a bináris adatok, de a "érvénytelen arcképét" hibaüzenetet kapok.

**válasz**:  Ez a hiba arra utal, hogy az algoritmus a lemezkép-elemzés hiba történt. Okok a következők:
* A bemeneti formátum támogatott JPEG, PNG, (az első képkocka) GIF és BMP tartalmazza.
* Kép fájlmérete kell nem lehet nagyobb, mint 4 MB
* Az észlelhető oldallal mérete tartománya 36 x 36, 4096 x 4096 képpont. Arcok a tartományon nem észlelhető
* Néhány arcok észlelése miatt kihívások, például a nagy face szög (head-testtartás), a nagyméretű hangelnyelés helytelen lehet. Elülső és a közel-elülső arcok rendelkezik a legjobb eredmények elérése érdekében

-----
