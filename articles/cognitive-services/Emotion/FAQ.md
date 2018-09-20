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
ms.openlocfilehash: 56b3f2899f1e77c1a2b840285e2c69bdb8987ff4
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363088"
---
# <a name="emotion-api-frequently-asked-questions"></a>Az Emotion API – gyakori kérdések
 
> [!IMPORTANT]
> Video API előzetes verziója 2017. október 30-én megszűnik. Próbálja ki az új [Video Indexer API előzetes verzióját](https://azure.microsoft.com/services/cognitive-services/video-indexer/) könnyedén kiértékelheti a videókkal és javíthatja a tartalomkeresés élményét, például a keresési eredmények között, mely képes észlelni a videóban elhangzó szövegre keressenek, arcok, karakterek és érzelmeket. [További információk](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Ha nem találja a kérdésekre adott válaszok a gyakori kérdések, próbálkozzon az Emotion API közösségi kéri a [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) , vagy forduljon a Súgó és támogatás a [UserVoice](https://cognitive.uservoice.com/).  

-----

**Kérdés**: *milyen típusú rendszerképek lekérése a legjobb eredmények Emotion API-ból?*

**Válasz**: akadálymentes, teljes elülső arcfelismerési képeket használ a legjobb eredmények elérése érdekében. Megbízhatóság csökkenti a részleges elülső arcok, és Emotion API előfordulhat, hogy nem érzelemfelismerés képeken, ahol az arcfelismerés az elforgatott 45 fok vagy több.

-----

**Kérdés**: *hány érzelmek azonosíthatja az Emotion API-t?*

**Válasz**: Emotion API felismeri a általánosan elfogadott nyolc különböző érzelmek: 
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

**Válasz**: Ez azt jelenti, hogy az algoritmus a lemezkép-elemzés hiba történt.  
* A bemeneti formátum támogatott JPEG, PNG, (az első képkocka) GIF és BMP tartalmazza. 
* Kép fájlmérete kell nem lehet nagyobb, mint 4MB
* Az észlelhető oldallal mérete tartománya 36 x 36, 4096 x 4096 képpont. A tartományon arcok észlelése nem történik meg
* Néhány arcok észlelése helytelen lehet kihívások, például a nagyon nagy face szög (head-testtartás), mert nagy hangelnyelés. Elülső és a közel-elülső arcok rendelkezik a legjobb eredmények elérése érdekében

-----
