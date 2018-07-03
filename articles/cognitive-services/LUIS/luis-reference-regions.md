---
title: Language Understanding (LUIS) régiók |} A Microsoft Docs
titleSuffix: Azure
description: Ez a cikk a LUIS-régiók, a LUIS webhely, az Azure-előfizetések és a régiók listáját tartalmazza.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: d81fbc03689788066fb9275523a5e96647117c58
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346452"
---
# <a name="regions-and-keys"></a>Régiók és a kulcsok

A régió, amelyben a LUIS-alkalmazás közzététele a régiónak vagy az Azure Portalon egy Azure LUIS végponti kulcs létrehozásakor megadott felel meg. Ha Ön [alkalmazások közzététele](./luis-how-to-publish-app.md), LUIS automatikusan előállítja a kulcsot az ahhoz tartozó régiót-végpont URL-címe. A LUIS alkalmazás közzététele több régióban, régiónként legalább egy kulcs szükséges. 

## <a name="luis-website"></a>A LUIS-webhely
Nincsenek három LUIS webhelyek, régió alapján. Hozhat létre és közzététele ugyanabban a régióban kell. 

|LUIS|Régió|
|--|--|
|[www.Luis.ai][www.luis.ai]|USA<br>nem Európa<br>nem Ausztrália|
|[AU.Luis.ai][au.luis.ai]|Ausztrália|
|[EU.Luis.ai][eu.luis.ai]|Európa|


## <a name="publishing-regions"></a>Közzétételi régiók

A létrehozott LUIS-alkalmazások https://www.luis.ai kivételével az összes végpontok teheti közzé a [Európai](#publishing-to-europe) és [ausztrál](#publishing-to-australia) régióban. 

A szerzői műveletek terület app csak közzétehető egy megfelelő régió közzététele. Ha az alkalmazás jelenleg a megfelelő szerzői régióban, alkalmazást, és importálja azt a megfelelő szerzői régió a közzétételi régiója. 

 Globális régió | Szerzői műveletek terület | Közzétételi & régió lekérdezése   |   A LUIS-webhely | Végpont URL-formátum   |
|-----|------|------|------|------|
| Ázsia | USA nyugati régiója| Kelet-Ázsia     | [www.Luis.ai][www.luis.ai] |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ázsia | USA nyugati régiója| Délkelet-Ázsia     | [www.Luis.ai][www.luis.ai] |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Ausztrália](#publishing-to-australia) | Kelet-Ausztrália| Kelet-Ausztrália     |   [AU.Luis.ai][au.luis.ai] | https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Európa](#publishing-to-europe)| Nyugat-Európa| Észak-Európa     | [EU.Luis.ai][eu.luis.ai]|  https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Európa](#publishing-to-europe) | Nyugat-Európa| Nyugat-Európa     | [EU.Luis.ai][eu.luis.ai]|  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Észak-Amerika | USA nyugati régiója | USA keleti régiója      |[www.Luis.ai][www.luis.ai] |   https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika | USA nyugati régiója | USA 2. keleti régiója     | [www.Luis.ai][www.luis.ai] |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika | USA nyugati régiója | USA déli középső régiója     | [www.Luis.ai][www.luis.ai] |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Észak-Amerika | USA nyugati régiója | USA nyugati középső régiója     |[www.Luis.ai][www.luis.ai] |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika | USA nyugati régiója | USA nyugati régiója |  [www.Luis.ai][www.luis.ai] | https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Észak-Amerika | USA nyugati régiója | USA nyugati régiója, 2.    | [www.Luis.ai][www.luis.ai] |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Dél-Amerika | USA nyugati régiója | Dél-Brazília     | [www.Luis.ai][www.luis.ai] |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-europe"></a>Közzététel az Európa

Tesz közzé, az Európai régiókban, létre kell hozni a LUIS Apps alkalmazások https://eu.luis.ai csak. Ha közzé bárhol ellenkező esetben az Európai régióban a kulcs használatával kísérli meg, LUIS figyelmeztető üzenetet jeleníti meg. Ehelyett használjon https://eu.luis.ai. Hozza létre a LUIS-alkalmazások [ https://eu.luis.ai ] [ eu.luis.ai] nem automatikusan át más régiókban. Exportálja és importálja a LUIS-alkalmazásokon ahhoz, hogy telepítse át.

## <a name="publishing-to-australia"></a>Közzététel az Ausztrália

Az ausztráliai régiókban való közzétételéhez, LUIS alkalmazások létrehozása, https://au.luis.ai csak. Ha közzé bárhol ellenkező esetben az ausztráliai régióban a kulcs használatával kísérli meg, LUIS figyelmeztető üzenetet jeleníti meg. Ehelyett használjon https://au.luis.ai. Hozza létre a LUIS-alkalmazások [ https://au.luis.ai ] [ au.luis.ai] nem automatikusan át más régiókban. Exportálja és importálja a LUIS-alkalmazásokon ahhoz, hogy telepítse át.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Előre összeállított entitások referenciája](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai