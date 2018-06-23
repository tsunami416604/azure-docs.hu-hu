---
title: Az Azure Content moderátor API-referencia |} Microsoft Docs
description: További tudnivalók a tartalom moderálás, és tekintse át a tartalmat moderátor API-k.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 94de9b91cc242e8b7e5479cacab8380adac551f6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346994"
---
# <a name="content-moderator-api-reference"></a>Content Moderator API-referencia

Első lépések az Azure Content moderátor API-k az alábbi módokon: (lásd még: [hitelesítő adatok kezelése](review-tool-user-guide/credentials.md).)

- Az Azure-portálon [előfizetni a tartalom moderátor API-k](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Regisztrálás a [tartalom moderátor felülvizsgálati eszköz](http://contentmoderator.cognitive.microsoft.com/). Lásd: [gyors üzembe helyezés](quick-start.md).

## <a name="moderation-apis"></a>Moderálási API-k

A következő tartalom moderátor API-k segítségével állítsa be a utáni moderálás munkafolyamatokat.

| Leírás | Leírások |
| -------------------- |-------------|
| **Kép moderálás API**<br /><br />Lemezképek vizsgálata, és lehetséges felnőtt és ellopható tartalom észlelése címkék, várható, pontszámokat és egyéb kinyert információkat. <br /><br />Ezen információk használatával közzététele, utasítsa el, vagy tekintse át a tartalmat a utáni moderálás munkafolyamat. <br /><br />| [Kép moderálás API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "kép moderálás API-referencia")   |
| **Szöveg moderálás API**<br /><br />Szöveges tartalom vizsgálata. Visszaadja a Profanitás feltételeket és a személyes azonosításra alkalmas adatokat (PII). <br /><br />Ezen információk használatával közzététele, utasítsa el, vagy tekintse át a tartalmat a utáni moderálás munkafolyamat.<br /><br /> | [Szöveg moderálás API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "szöveg moderálás API-referencia")   |
| **Videó moderálás API**<br /><br />Videók vizsgálata, és lehetséges felnőtt és ellopható tartalomtípusok. <br /><br />Ezen információk használatával közzététele, utasítsa el, vagy tekintse át a tartalmat a utáni moderálás munkafolyamat.<br /><br /> | [Áttekintő videó moderálás API](video-moderation-api.md "videó moderálás API – áttekintés")   |
| **Lista felügyeleti API**<br /><br />Hozzon létre, és a képek és szövegek egyéni kizárási és tartalmazási listák kezelése. Ha engedélyezve van, a **kép – megfelelő** és **szöveg - képernyő** műveletek tegye az elküldött tartalom az egyéni listájával intelligens megfelelő. <br /><br />A hatékonyság kihagyhatja a machine learning-alapú moderálás lépés.<br /><br /> | [Felügyeleti API-referencia listában](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "lista felügyeleti API-referencia")   |

## <a name="review-api"></a>Felülvizsgálati API

A felülvizsgálati API a következő részből áll:

| Leírás | Leírások |
| -------------------- |-------------|
| **Feladatok**<br /><br /> Indítsa el a vizsgálatot, és tekintse át moderálás munkafolyamatok kép és a szöveges tartalom. Egy moderálás feladat ellenőrzi a tartalom a kép moderálás API és a szöveg moderálás API használatával. Moderálás feladatok használata a definiált, és az alapértelmezett munkafolyamatok értékelést létrehozásához. <br /><br />Emberi Moderátorra tekintse át a az automatikusan hozzárendelt címkék és előrejelzési adatokat, és a tartalom moderálás döntési benyújtott, miután a felülvizsgálati API minden információt az API-végpont küldi el.<br /><br /> | [Sikertelen feladat-hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "feladat-hivatkozás")   |
| **Ellenőrzések**<br /><br />A felülvizsgálati eszközzel közvetlenül a lemezkép létrehozásához, vagy szöveges ellenőrzi, hogy az emberi moderátorok.<br /><br /> Emberi Moderátorra tekintse át a az automatikusan hozzárendelt címkék és előrejelzési adatokat, és a tartalom moderálás döntési benyújtott, miután a felülvizsgálati API minden információt az API-végpont küldi el.<br /><br /> | [Tekintse át a hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "tekintse át a hivatkozás")   |
| **Munkafolyamatok**<br /><br />Létrehozása, frissítése és részletes információkat szolgáltatva az egyéni munkafolyamatokat, amely a csoport-hoz. A munkafolyamatok a felülvizsgálati eszközzel határozza meg. <br /> <br />Munkafolyamatok általában tartalom moderátor használja, de bizonyos más API-k által biztosított összekötők a felülvizsgálati eszközben is használhatja.<br /><br /> | [Munkafolyamat-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "munkafolyamat-referencia")   |


