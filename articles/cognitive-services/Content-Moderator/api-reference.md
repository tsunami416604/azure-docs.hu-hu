---
title: API-hivatkozás – Content Moderator
titleSuffix: Azure Cognitive Services
description: Ismerkedjen meg a különböző tartalom-moderálási és felülvizsgálati API-kkal Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: sajagtap
ms.openlocfilehash: 3ad911a95dbe6209fcf55adcac3cf2937b06d1ff
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565624"
---
# <a name="content-moderator-api-reference"></a>Content Moderator API-referencia

Az Azure Content Moderator API-kkal az alábbi módokon kezdheti meg:

- A Azure Portal előfizethet [a Content MODERATOR API](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)-ra.
- A [Content moderator felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/)regisztrációját [a webes Content moderator kipróbálása](quick-start.md) című témakörben tekintheti meg.

## <a name="moderation-apis"></a>Moderálási API-k

A következő Content Moderator API-k segítségével állíthatja be a moderálás utáni munkafolyamatokat.

| Leírás | Hivatkozás |
| -------------------- |-------------|
| **Képmoderálási API**<br /><br />A képeket a címkék, a megbízhatósági pontszámok és az egyéb kinyert információk használatával vizsgálhatja meg, és felderítheti a potenciális felnőtteket <br /><br />Ezekkel az információkkal közzéteheti, elutasíthatja vagy áttekintheti a tartalmat a moderációs munkafolyamatban. <br /><br />| [Képek moderálási API] -referenciája (https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Képek moderálási API") -referenciája   |
| **Szöveges moderálási API**<br /><br />Szöveg tartalmának vizsgálata. A rendszer a káromkodási feltételeket és a személyes adatértékeket adja vissza. <br /><br />Ezekkel az információkkal közzéteheti, elutasíthatja vagy áttekintheti a tartalmat a moderációs munkafolyamatban.<br /><br /> | [Szöveges MODERÁLÁS API] -referenciája (https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Szöveges MODERÁLÁS API") -referenciája   |
| **Videó moderálási API**<br /><br />Videókat kereshet, és felderítheti a potenciális felnőtt és a zamatos tartalmakat. <br /><br />Ezekkel az információkkal közzéteheti, elutasíthatja vagy áttekintheti a tartalmat a moderációs munkafolyamatban.<br /><br /> | [Videó-moderálási API – áttekintés] (video-moderation-api.md "Videó-moderálási API – áttekintés")   |
| **Lista kezelése API**<br /><br />Egyéni kizárási vagy belefoglalási listát hozhat létre és kezelhet képekből és szövegből. Ha ez a beállítás  engedélyezve van, a képegyeztetés és a **szöveges képernyő** művelet a beküldött tartalomhoz tartozó, az egyéni listán szereplő adatokhoz tartozó zavaros egyezést tesz lehetővé. <br /><br />A hatékonyság érdekében kihagyhatja a Machine learning-alapú moderálás lépést.<br /><br /> | [Felügyeleti API] -referenciák listázása (https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "Felügyeleti API") -referenciák listázása   |

## <a name="review-apis"></a>API-k áttekintése

A felülvizsgálati API-k a következő összetevőket használják:

| Leírás | Hivatkozás |
| -------------------- |-------------|
| **Feladatok**<br /><br /> Vizsgálja meg a beolvasási és a-felülvizsgálati moderálási munkafolyamatokat mind a képek, mind a szöveges tartalmak esetében. A moderációs feladatok a képmoderálási API és a Text moderációs API használatával vizsgálják meg a tartalmakat. A moderálási feladatok a definiált és az alapértelmezett munkafolyamatokat használják a felülvizsgálatok létrehozásához. <br /><br />Miután egy emberi moderátor áttekintette az automatikusan hozzárendelt címkéket és az előrejelzési adatokat, és elküldte a tartalom moderálási döntését, a felülvizsgálati API minden információt elküld az API-végpontnak.<br /><br /> | [Feladatok leírása] (https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "Feladatok leírása")   |
| **Értékelés**<br /><br />A felülvizsgálati eszköz használatával közvetlenül hozhat létre képeket vagy szöveges felülvizsgálatokat az emberi moderátorok számára.<br /><br /> Miután egy emberi moderátor áttekintette az automatikusan hozzárendelt címkéket és az előrejelzési adatokat, és elküldte a tartalom moderálási döntését, a felülvizsgálati API minden információt elküld az API-végpontnak.<br /><br /> | [Hivatkozás áttekintése] (https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Hivatkozás áttekintése")   |
| **Munkafolyamatok**<br /><br />A csoport által létrehozott egyéni munkafolyamatok adatainak létrehozása, frissítése és beolvasása. A munkafolyamatokat a felülvizsgálati eszköz használatával határozhatja meg. <br /> <br />A munkafolyamatok jellemzően Content Moderator használnak, de más API-kat is használhatnak, amelyek összekötőként elérhetők a felülvizsgálati eszközben.<br /><br /> | [Munkafolyamat-hivatkozás] (https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "Munkafolyamat-hivatkozás")   |