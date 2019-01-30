---
title: API-referencia - Content Moderator
titlesuffix: Azure Cognitive Services
description: További információ a különböző tartalom-jóváhagyás, és tekintse át a Content Moderator API-k.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 0620ee1d0969a57801fe524684d02e2ef9ef6ba6
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223382"
---
# <a name="content-moderator-api-reference"></a>Content Moderator API-referencia

A következő módokon Ismerkedés az Azure Content Moderator API-kat: (Lásd még: [hitelesítő adatok kezelése](review-tool-user-guide/credentials.md).)

- Az Azure Portalon [fizessen elő a Content Moderator API-k](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Regisztráljon a [Content Moderator felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/). Lásd: [próbálja ki a Content Moderator, a weben](quick-start.md).

## <a name="moderation-apis"></a>Moderálási API-k

A következő Content Moderator API-k segítségével a jóváhagyás utáni munkafolyamatokat állíthat be.

| Leírás | Leírások |
| -------------------- |-------------|
| **Képmoderálási API**<br /><br />Kiszűrhető, és észleli a potenciális felnőtt és szexuális tartalom címkék, megbízhatósági pontszámokat és egyéb kinyert információkat. <br /><br />Ezen információk használatával közzététele, elvetése, vagy tekintse át a tartalmat a jóváhagyás utáni munkafolyamat. <br /><br />| [Moderálási API-referencia a kép](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "kép moderálási API-referencia")   |
| **Szövegmoderálási API**<br /><br />Szöveges tartalom vizsgálata. Visszaadja a vulgáris feltételeket és a személyes azonosításra alkalmas adatokat (PII). <br /><br />Ezen információk használatával közzététele, elvetése, vagy tekintse át a tartalmat a jóváhagyás utáni munkafolyamat.<br /><br /> | [Szöveg moderálási API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "szöveg moderálási API-referencia")   |
| **Videomoderálási API**<br /><br />Videók vizsgálata, valamint észlelheti a potenciális felnőtt és szexuális tartalom. <br /><br />Ezen információk használatával közzététele, elvetése, vagy tekintse át a tartalmat a jóváhagyás utáni munkafolyamat.<br /><br /> | [Áttekintő videó moderálási API](video-moderation-api.md "videó moderálási API – áttekintés")   |
| **Lista felügyeleti API**<br /><br />Létrehozhat és kezelhet, képek és szöveg kizárási és belefoglalási egyedi listákkal. Ha engedélyezve van, a **kép – megfelelő** és **szöveg – a képernyő** operations tegye, hogy a beküldött tartalmat összeveti az egyedi listákkal intelligens megfelelő. <br /><br />A hatékonyság kihagyhatja a machine learning-alapú moderálás lépés.<br /><br /> | [Felügyeleti API-referencia listában](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "lista felügyeleti API-referencia")   |

## <a name="review-api"></a>Felülvizsgálati API

A felülvizsgálati API-t a következő összetevőket tartalmazza:

| Leírás | Leírások |
| -------------------- |-------------|
| **Feladatok**<br /><br /> Vizsgálat és felülvizsgálati jóváhagyás munkafolyamatokat a lemezkép és a szöveges tartalom kezdeményezni. Moderálás feladat megvizsgálja a tartalmat a lemezkép moderálási API-t és a moderálási API használatával. Moderálás feladatok használata a definiált, és az alapértelmezett létrehozni a felülvizsgálatok munkafolyamatokat. <br /><br />Miután egy emberi moderátor tekintse át az automatikusan hozzárendelt címkék és előrejelzési adatokat, és a tartalom-jóváhagyás döntési elküldött, a felülvizsgálati API az API-végpont összes információt küldi el.<br /><br /> | [Feladat-hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "feladat-referencia")   |
| **Értékelések**<br /><br />Használja a vizsgálóeszközt közvetlenül a lemezkép létrehozása vagy emberi moderátorok felülvizsgálatai szöveg.<br /><br /> Miután egy emberi moderátor tekintse át az automatikusan hozzárendelt címkék és előrejelzési adatokat, és a tartalom-jóváhagyás döntési elküldött, a felülvizsgálati API az API-végpont összes információt küldi el.<br /><br /> | [Tekintse át a hivatkozási](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "tekintse át a hivatkozást")   |
| **Munkafolyamatok**<br /><br />Hozzon létre, frissíthet és részletes információkat az egyéni munkafolyamatokat, amely a csapat hoz létre. A felülvizsgálati eszköz használatával meghatározhatja a munkafolyamatokat. <br /> <br />A munkafolyamatok általában használja a Content Moderator, de bizonyos más API-k érhetők el beépített összekötőkként, a felülvizsgálati eszközben is használhatja.<br /><br /> | [A munkafolyamat referencia](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "munkafolyamat-referencia")   |


