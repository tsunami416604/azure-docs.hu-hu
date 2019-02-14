---
title: Gyakori kérdések – Emotion API
titlesuffix: Azure Cognitive Services
description: Az Emotion API-val kapcsolatos gyakori kérdésekre adott válaszok.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 3654d315867cfe60c277806d4246469adf0a791f
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238266"
---
# <a name="emotion-api-frequently-asked-questions"></a>Az Emotion API – gyakori kérdések

> [!IMPORTANT]
> Az Emotion API 2019. február 15-ével elavulttá válik. Az érzelemfelismerési képesség mostantól a [Face API](https://docs.microsoft.com/azure/cognitive-services/face/) részeként általánosan elérhető.

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Ha nem találja a kérdésekre adott válaszok a gyakori kérdések, próbálkozzon az Emotion API közösségi kéri a [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) , vagy forduljon a Súgó és támogatás a [UserVoice](https://cognitive.uservoice.com/).  

-----

**Kérdés**: *Milyen típusú rendszerképek lekérése a legjobb eredmények elérése érdekében Emotion API-ból?*

**válasz**: Akadálymentes, teljes elülső arcfelismerési képeket használ a legjobb eredmények elérése érdekében. Megbízhatóság csökkenti a részleges elülső arcok, és az Emotion API előfordulhat, hogy nem érzelemfelismerés képeken, a face rotálásakor legfeljebb 45 fok.

-----

**Kérdés**: *Hány érzelmek azonosíthatja az Emotion API-t?*

**válasz**: Az Emotion API felismeri a nyolc különböző általánosan elfogadott érzelmek:
* Boldogság
* Szomorúság
* Meglepettség
* Düh
* Félelem
* Megvetés
* Undor
* Semleges

-----

**Kérdés**: *Van bármilyen módon élő video-adatfolyamot át az API-t és az eredmény egy időben?*

**válasz**: Használja a kép az emotion API-alapú, és azt hívja meg az egyes keret, vagy hagyja ki a keretek a teljesítmény.  Videó a keret-keret elemzési minták érhetők el.

-----

**Kérdés**: *E vagyok átadja a bináris adatok, de me biztosítja: "Érvénytelen arcképét.**

**válasz**: Ez az üzenet azt jelenti, hogy az algoritmus a lemezkép-elemzés hiba történt.  
* A bemeneti formátum támogatott közé tartozik, JPEG, PNG, (az első képkocka) GIF és BMP
* Kép fájlmérete kell nem lehet nagyobb, mint 4 MB
* Az észlelhető oldallal mérete tartománya 36 x 36, 4096 x 4096 képpont. Arcok a tartományon nem észlelhető
* Néhány arcok észlelése miatt kihívások, például a nagy face szög (head-testtartás), a nagyméretű hangelnyelés helytelen lehet. Elülső és a közel-elülső arcok rendelkezik a legjobb eredmények elérése érdekében

-----
