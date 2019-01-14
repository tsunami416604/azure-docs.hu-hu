---
title: Közzétételi régiók és végpontok
titleSuffix: Azure Cognitive Services
description: A régió, amelyben a LUIS-alkalmazás közzététele a régiónak vagy az Azure Portalon egy Azure LUIS végponti kulcs létrehozásakor megadott felel meg. Amikor közzétesz egy alkalmazást, a LUIS automatikusan létrehozza a kulcsot az ahhoz tartozó régiót-végpont URL-címe.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 01/10/2019
ms.author: diberry
ms.openlocfilehash: 655a2c0e2a7ee483dccbf129f36611d0b36df61f
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246313"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Szerzői és régiók és a hozzá tartozó kulcsok közzététele

A régió, amelyben a LUIS-alkalmazás közzététele a régiónak vagy az Azure Portalon egy Azure LUIS végponti kulcs létrehozásakor megadott felel meg. Ha Ön [alkalmazások közzététele](./luis-how-to-publish-app.md), LUIS automatikusan előállítja a kulcsot az ahhoz tartozó régiót-végpont URL-címe. A LUIS alkalmazás közzététele több régióban, régiónként legalább egy kulcs szükséges. 

## <a name="luis-website"></a>A LUIS-webhely
Nincsenek három LUIS webhelyek, régió alapján. Hozhat létre és közzététele ugyanabban a régióban kell. 

|LUIS|Régió|
|--|--|
|[www.Luis.ai][www.luis.ai]|USA<br>nem Európa<br>nem Ausztrália|
|[AU.Luis.ai][au.luis.ai]|Ausztrália|
|[EU.Luis.ai][eu.luis.ai]|Európa|

## <a name="regions-and-azure-resources"></a>Régiók és az Azure-erőforrások
Az alkalmazás közzé van téve a LUIS erőforrások a LUIS portálon hozzá társított összes régióba. Például a létrehozott alkalmazás [www.luis.ai][www.luis.ai], ha a LUIS erőforrás létrehozása **westus** , és adja hozzá az alkalmazást egy erőforrást, az alkalmazás közzé van téve az adott régióban. 

## <a name="public-apps"></a>Nyilvános alkalmazások
A nyilvános alkalmazás közzé van téve az összes régióban, hogy egy felhasználó egy LUIS-erőforrás régió-alapú kulccsal hozzáférhet az alkalmazás bármelyik régióban az erőforrás-kulcs társítva.

## <a name="publishing-regions"></a>Közzétételi régiók

A létrehozott LUIS-alkalmazások https://www.luis.ai kivételével az összes végpontok teheti közzé a [Európai](#publishing-to-europe) és [ausztrál](#publishing-to-australia) régióban. 

A szerzői műveletek terület app csak közzétehető egy megfelelő régió közzététele. Ha az alkalmazás jelenleg a megfelelő szerzői régióban, alkalmazást, és importálja azt a megfelelő szerzői régió a közzétételi régiója. 

 Globális régió | Szerzői API régióban, és a webhely készítése| Közzétételi & régió lekérdezése<br>`API region name`   |  Végpont URL-formátum   |
|-----|------|------|------|
| Ázsia | `westus`<br>[www.Luis.ai][www.luis.ai]| Közép-India<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ázsia | `westus`<br>[www.Luis.ai][www.luis.ai]| Kelet-Ázsia<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ázsia | `westus`<br>[www.Luis.ai][www.luis.ai]| Kelet-Japán<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ázsia | `westus`<br>[www.Luis.ai][www.luis.ai]| Nyugat-Japán<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ázsia | `westus`<br>[www.Luis.ai][www.luis.ai]| Korea középső régiója<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ázsia | `westus`<br>[www.Luis.ai][www.luis.ai]| Délkelet-Ázsia<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Ausztrália](#publishing-to-australia) | `australiaeast`<br>[AU.Luis.ai][au.luis.ai]| Kelet-Ausztrália<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Európa](#publishing-to-europe)| `westeurope`<br>[EU.Luis.ai][eu.luis.ai]| Közép-Franciaország<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Európa](#publishing-to-europe)| `westeurope`<br>[EU.Luis.ai][eu.luis.ai]| Észak-Európa<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Európa](#publishing-to-europe) | `westeurope`<br>[EU.Luis.ai][eu.luis.ai]| Nyugat-Európa<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Európa](#publishing-to-europe) | `westeurope`<br>[EU.Luis.ai][eu.luis.ai]| Az Egyesült Királyság déli régiója<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Észak-Amerika |`westus`<br>[www.Luis.ai][www.luis.ai] | Közép-Kanada<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika |`westus`<br>[www.Luis.ai][www.luis.ai] | USA középső régiója<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika |`westus`<br>[www.Luis.ai][www.luis.ai] | USA keleti régiója<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika | `westus`<br>[www.Luis.ai][www.luis.ai] | USA 2. keleti régiója<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika | `westus`<br>[www.Luis.ai][www.luis.ai] | USA északi középső régiója<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Észak-Amerika | `westus`<br>[www.Luis.ai][www.luis.ai] | USA déli középső régiója<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Észak-Amerika |`westus`<br>[www.Luis.ai][www.luis.ai] | USA nyugati középső régiója<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika | `westus`<br>[www.Luis.ai][www.luis.ai] | USA nyugati régiója<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Észak-Amerika |`westus`<br>[www.Luis.ai][www.luis.ai] | USA nyugati régiója, 2.<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Dél-Amerika | `westus`<br>[www.Luis.ai][www.luis.ai] | Dél-Brazília<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |




## <a name="publishing-to-europe"></a>Közzététel az Európa

Tesz közzé, az Európai régiókban, létre kell hozni a LUIS Apps alkalmazások https://eu.luis.ai csak. Ha közzé bárhol ellenkező esetben az Európai régióban a kulcs használatával kísérli meg, LUIS figyelmeztető üzenetet jeleníti meg. Ehelyett használjon https://eu.luis.ai. Hozza létre a LUIS-alkalmazások [ https://eu.luis.ai ] [ eu.luis.ai] nem automatikusan át más régiókban. Exportálja és importálja a LUIS-alkalmazásokon ahhoz, hogy telepítse át.

## <a name="publishing-to-australia"></a>Közzététel az Ausztrália

Az ausztráliai régiókban való közzétételéhez, LUIS alkalmazások létrehozása, https://au.luis.ai csak. Ha közzé bárhol ellenkező esetben az ausztráliai régióban a kulcs használatával kísérli meg, LUIS figyelmeztető üzenetet jeleníti meg. Ehelyett használjon https://au.luis.ai. Hozza létre a LUIS-alkalmazások [ https://au.luis.ai ] [ au.luis.ai] nem automatikusan át más régiókban. Exportálja és importálja a LUIS-alkalmazásokon ahhoz, hogy telepítse át.

## <a name="endpoints"></a>Végpontok

A LUIS jelenleg rendelkezik 2 végpontok: egyet az szerzői és egyet a szövegelemzés.

|Cél|URL-cím|
|--|--|
|Szerzői műveletek|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Szövegelemzés (lekérdezés előrejelzés)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

A következő táblázat ismerteti a paraméterek, a kapcsos zárójelek szintben `{}`, az előző táblázatban.

|Paraméter|Cél|
|--|--|
|régió|Azure-régió - létrehozási és közzétételi rendelkezik különböző régiókban|
|appID|A LUIS alkalmazás azonosító URL-CÍMÉT az útvonalban használt és az alkalmazás irányítópulton található|
|válaszok|például csevegőrobot ügyfélalkalmazástól utterance (kifejezés) szövegét|


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Előre összeállított entitások referenciája](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai