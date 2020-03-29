---
title: API-hivatkozás - Tartalommoderátor
titleSuffix: Azure Cognitive Services
description: Ismerje meg a különböző tartalommoderálást, és tekintse át a tartalommoderátor API-jait.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 7fc46d06b68dca074da060b4866186a6242ffad2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757379"
---
# <a name="content-moderator-api-reference"></a>Tartalommoderátor API-hivatkozása

Az Azure Content Moderator API-k használata az alábbi módokon ismerkedhet meg:

- Az Azure Portalon [iratkozzon fel a tartalommoderátor API-ra.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- Lásd: [Tartalommoderátor kipróbálása az interneten,](quick-start.md) hogy regisztrálhass a [Tartalommoderátor-ellenőrző eszközzel.](https://contentmoderator.cognitive.microsoft.com/)

## <a name="moderation-apis"></a>Moderálási API-k

A következő tartalommoderátor-API-k segítségével állíthatja be a moderálás utáni munkafolyamatokat.

| Leírás | Referencia |
| -------------------- |-------------|
| **Image Moderation API**<br /><br />Címkék, megbízhatósági pontszámok és egyéb kinyert információk használatával képeket szíthat, és észlelheti a potenciális felnőtt és pikáns tartalmakat. <br /><br />Ezen információk segítségével közzéteheti, elutasíthatja vagy áttekintheti a moderálás utáni munkafolyamat tartalmát. <br /><br />| [Képmoderálás API-hivatkozása](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Képmoderálás API-hivatkozása")   |
| **Text Moderation API**<br /><br />Szövegtartalom beszkamárla. Káromkodási feltételek és személyes adatok visszaadása. <br /><br />Ezen információk segítségével közzéteheti, elutasíthatja vagy áttekintheti a moderálás utáni munkafolyamat tartalmát.<br /><br /> | [Szövegmoderálás API-hivatkozása](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Szövegmoderálás API-hivatkozása")   |
| **Videómoderálásapi-t**<br /><br />Szidja a videókat, és észlelheti a potenciális felnőtt és pikáns tartalmakat. <br /><br />Ezen információk segítségével közzéteheti, elutasíthatja vagy áttekintheti a moderálás utáni munkafolyamat tartalmát.<br /><br /> | [Videomoderálás api – áttekintés](video-moderation-api.md "Videomoderálás api – áttekintés")   |
| **Listafelügyeleti API**<br /><br />Képek és szövegek egyéni kizárási vagy belefoglalási listáinak létrehozása és kezelése. Ha engedélyezve van, a **Kép – Egyezés** és **szöveg – Képernyő** műveletek nem fuzzy megfelel a beküldött tartalom az egyéni listákat. <br /><br />A hatékonyság érdekében kihagyhatja a gépi tanuláson alapuló moderálási lépést.<br /><br /> | [Listafelügyeleti API-hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "Listafelügyeleti API-hivatkozás")   |

## <a name="review-apis"></a>API-k áttekintése

A véleményezési API-k a következő összetevőkkel rendelkeznek:

| Leírás | Referencia |
| -------------------- |-------------|
| **Feladatok**<br /><br /> Kezdeményezze a bevizsgálat és az ellenőrzés moderálási munkafolyamatait mind a kép-, mind a szöveges tartalomhoz. A moderálási feladat a Képmoderálás API és a Szövegmoderálás API használatával ellenőrzi a tartalmat. A moderálási feladatok a definiált és az alapértelmezett munkafolyamatokat használják az értékelések létrehozásához. <br /><br />Miután egy emberi moderátor áttekintette az automatikusan hozzárendelt címkéket és előrejelzési adatokat, és elküldte a tartalommoderálási döntést, a Felülvizsgálati API elküldi az összes információt az API-végpontba.<br /><br /> | [Feladat hivatkozása](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "Feladat hivatkozása")   |
| **Felülvizsgálatok**<br /><br />A Véleményezés eszközzel közvetlenül hozhat létre kép- vagy szöveges értékeléseket az emberi moderátorok számára.<br /><br /> Miután egy emberi moderátor áttekintette az automatikusan hozzárendelt címkéket és előrejelzési adatokat, és elküldte a tartalommoderálási döntést, a Felülvizsgálati API elküldi az összes információt az API-végpontba.<br /><br /> | [Hivatkozás áttekintése](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Hivatkozás áttekintése")   |
| **Munkafolyamatok**<br /><br />A csapat által létrehozott egyéni munkafolyamatok létrehozása, frissítése és megismerése. A munkafolyamatokat a Véleményezés eszközzel határozhatja meg. <br /> <br />A munkafolyamatok általában tartalommoderátort használnak, de használhatnak bizonyos más API-kat is, amelyek összekötőként érhetők el a Véleményezés eszközben.<br /><br /> | [Munkafolyamat-útmutató](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "Munkafolyamat-útmutató")   |