---
title: Érzelemfelismerési API – gyakori kérdések |} Microsoft Docs
description: A Érzelemfelismerési API-val kapcsolatban gyakran feltett kérdésekre válaszok kognitív szolgáltatásban.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/26/2017
ms.author: anroth
ms.openlocfilehash: 8532d7c00fd8d7b01d84b5e55cb9bbc60241789c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347002"
---
# <a name="emotion-api-frequently-asked-questions"></a>Gyakori kérdések a érzelemfelismerési API
 
> [!IMPORTANT]
> Villámnézet API a 2017. október 30 véget ér. Kipróbálhatja az új [videó indexelő API előnézete](https://azure.microsoft.com/services/cognitive-services/video-indexer/) insights könnyen kibontani videók, és tartalom felderítési lép, például a keresési eredmények, növelje a szóbeli szavakat, a lapok, a karakterek és a érzelmek észlelésével. [További információk](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Ha ez a GYIK a kérdésekre adott válaszok nem található, próbálja meg a Érzelemfelismerési API-Közösség kérése a [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) , vagy forduljon a Súgó és támogatás [UserVoice](https://cognitive.uservoice.com/).  

-----

**Kérdés**: *Érzelemfelismerési API-n milyen típusú képek beolvasása a legjobb eredmények?*

**Válasz**: szabad, teljes elülső arcfelismerést képek használjon a legjobb eredmények elérése érdekében. Megbízhatóság csökkenti a részleges elülső lapokat, és Érzelemfelismerési API nem ismeri fel a képek, ahol maga az elforgatott 45 fok, vagy több érzelmek.

-----

**Kérdés**: *hány érzelmek azonosíthatja Érzelemfelismerési API-t?*

**Válasz**: Érzelemfelismerési API felismeri nyolc különböző általánosan elfogadható érzelmek: 
* Boldogság
* Szomorúság
* Meglepettség
* Düh
* Félelem
* Megvetés
* Undor 
* Semleges 

-----

**Kérdés**: *valamilyen módon egy élő video-adatfolyammá alakítja át az API-t, és az eredmény egyidejűleg?*

**Válasz**: használja a Képalapú érzelemfelismerési API és minden kereten neki, vagy hagyja ki a teljesítmény keretek.  Video a keret-keret elemzése minták érhetők el.

-----

**Kérdés**: *I vagyok átadja a bináris adatok, de biztosít me: "érvénytelen arcfelismerési lemezképpel.**

**Válasz**: Ez azt jelenti, hogy az algoritmus volt-e a kép elemzése hibát.  
* A bemeneti formátum támogatott JPEG, PNG, (az első keret) GIF, BMP tartalmazza. 
* Kép fájlmérete lehet 4MB-nál nagyobb
* A észlelhető arcfelismerési mérete tartománya 36 x 36 a 4096 x 4096 képpontban megadva. A tartományon kívül lapok nem észlelhető
* Bizonyos lapok műszaki akadályok, például a nagyon nagy arcfelismerési szögek (head-jelentő), mert nem észlelhető nagy hangelnyelés. Elülső és közelében elülső lapok van a legjobb eredmények elérése érdekében

-----
