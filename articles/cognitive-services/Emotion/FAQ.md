---
title: Gyakori kérdések – Emotion API
titlesuffix: Azure Cognitive Services
description: Az Emotion API-val kapcsolatos gyakori kérdésekre adott válaszok.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 6c1c4b8e5c2701f3c419a58bc3fdc33f7e629bbd
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238536"
---
# <a name="emotion-api-frequently-asked-questions"></a>Az Emotion API – gyakori kérdések

> [!IMPORTANT]
> Az Emotion API, a 2019. február 15 elavulttá válik. Mostantól általánosan elérhető az érzelemfelismerési képességeket része a [Face API](https://docs.microsoft.com/azure/cognitive-services/face/).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Ha nem találja a kérdésekre adott válaszok a gyakori kérdések, próbálkozzon az Emotion API közösségi kéri a [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) , vagy forduljon a Súgó és támogatás a [UserVoice](https://cognitive.uservoice.com/).  

-----

**Kérdés**: *milyen típusú rendszerképek lekérése a legjobb eredmények Emotion API-ból?*

**Válasz**: akadálymentes, teljes elülső arcfelismerési képeket használ a legjobb eredmények elérése érdekében. Megbízhatóság csökkenti a részleges elülső arcok, és az Emotion API előfordulhat, hogy nem érzelemfelismerés képeken, a face rotálásakor legfeljebb 45 fok.

-----

**Kérdés**: *hány érzelmek azonosíthatja az Emotion API-t?*

**Válasz**: Emotion API felismeri a nyolc különböző általánosan elfogadott érzelmek:
* Boldogság
* Szomorúság
* Meglepettség
* Düh
* Félelem
* Megvetés
* Undor
* Semleges

-----

**Kérdés**: *valamilyen módon, élő video-adatfolyamot át az API-t és az eredmény egy időben?*

**Válasz**: a Képalapú emotion API használata és az egyes keret meghívására, vagy hagyja ki a keretek a teljesítmény.  Videó a keret-keret elemzési minták érhetők el.

-----

**Kérdés**: *I vagyok átadja a bináris adatok, de biztosít me: "érvénytelen arcképét.**

**Válasz**: Ez az üzenet azt jelenti, hogy az algoritmus a lemezkép-elemzés hiba történt.  
* A bemeneti formátum támogatott közé tartozik, JPEG, PNG, (az első képkocka) GIF és BMP
* Kép fájlmérete kell nem lehet nagyobb, mint 4 MB
* Az észlelhető oldallal mérete tartománya 36 x 36, 4096 x 4096 képpont. Arcok a tartományon nem észlelhető
* Néhány arcok észlelése miatt kihívások, például a nagy face szög (head-testtartás), a nagyméretű hangelnyelés helytelen lehet. Elülső és a közel-elülső arcok rendelkezik a legjobb eredmények elérése érdekében

-----
