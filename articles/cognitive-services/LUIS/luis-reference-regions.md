---
title: Nyelvi ismertetése (LUIS) régiók |} Microsoft Docs
titleSuffix: Azure
description: Ez a cikk a LUIS webhely, az Azure-előfizetések és a globális régiókban LUIS régiók listáját tartalmazza.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 86a20770178707f72cf2991ca08b6b98eaeaf0cf
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36237115"
---
# <a name="regions-and-keys"></a>Régiók és kulcsok

A régió, ahol a LUIS alkalmazás közzététele a régió vagy az Azure-portálon az Azure LUIS végpontkulcs létrehozásakor megadott felel meg. Ha Ön [alkalmazások közzététele](./PublishApp.md), LUIS automatikusan létrehozza a kulcshoz tartozó régió egy végponti URL-címe. Egy LUIS alkalmazás közzététele egynél több régióban, régiónként legalább egy kulcs szükséges. 

## <a name="luis-website"></a>LUIS webhely
Nincsenek három LUIS webhelyek, régió alapján. Szerzői kell, és ugyanabban a régióban közzététele. 

|LUIS|Régió|
|--|--|
|[www.Luis.ai][www.luis.ai]|USA<br>nem Európa<br>nem Ausztrália|
|[AU.Luis.ai][au.luis.ai]|Ausztrália|
|[EU.Luis.ai][eu.luis.ai]|Európa|


## <a name="publishing-regions"></a>Közzétételi régiók

A létrehozott LUIS alkalmazások https://www.luis.ai kivételével minden végpontok teheti közzé a [Európai](#publishing-to-europe) és [ausztrál](#publishing-to-australia) régiók. 

A szerzői műveletek terület-alkalmazás csak a megfelelő tehetők közzé régió közzététele. Ha az alkalmazás jelenleg nem megfelelő a szerzői műveletekhez régióban, az alkalmazás exportálása, és importálja a fájlt a közzétételi régió megfelelő szerzői műveletek terület. 

 Globális régió | Szerzői műveletek terület | Közzététel & régió lekérdezése   |   LUIS webhely | Végpont URL-formátum   |
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

Az Európai régiók közzétételéhez hoz létre LUIS alkalmazások https://eu.luis.ai csak. Ha úgy próbálja közzétenni bárhol más kulccsal Európa régióban, LUIS figyelmeztető üzenetet jeleníti meg. Ehelyett használjon https://eu.luis.ai. Létrehozás dátuma: LUIS alkalmazások [ https://eu.luis.ai ] [ eu.luis.ai] nem automatikusan át más régiókban. Exportálja és importálja a LUIS app ahhoz, hogy telepítse át.

## <a name="publishing-to-australia"></a>Közzététel az Ausztrália

Az ausztrál régiók közzétételéhez hoz létre LUIS alkalmazások https://au.luis.ai csak. Ha úgy próbálja közzétenni bárhol általi használata a kulcs az ausztrál régióban, LUIS figyelmeztető üzenetet jeleníti meg. Ehelyett használjon https://au.luis.ai. Létrehozás dátuma: LUIS alkalmazások [ https://au.luis.ai ] [ au.luis.ai] nem automatikusan át más régiókban. Exportálja és importálja a LUIS app ahhoz, hogy telepítse át.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Előre elkészített szervezetek-referencia](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai