---
title: Közzétételi régiók & végpontok – LUIS
titleSuffix: Azure Cognitive Services
description: 3 a számos közzétételi régiót a szerzői régiók és a portálok támogatják. A régió, amelyben a LUIS-alkalmazás közzététele a régiónak vagy az Azure Portalon egy Azure LUIS végponti kulcs létrehozásakor megadott felel meg. Amikor közzétesz egy alkalmazást, a LUIS automatikusan létrehozza a kulcsot az ahhoz tartozó régiót-végpont URL-címe.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 44742d2a3d545af6c3f141500ed4d41cabd3d548
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563353"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Régiók és a hozzájuk tartozó kulcsok létrehozása és közzététele

A megfelelő LUIS-portálok három szerzői régiót támogatnak. A LUIS alkalmazás közzététele több régióban, régiónként legalább egy kulcs szükséges. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS authoring-régiók
A régió alapján három LUIS authoring portál található. Hozhat létre és közzététele ugyanabban a régióban kell. 

|LUIS|Szerzői műveletek terület|Azure-régió neve|
|--|--|--|
|[www.luis.ai][www.luis.ai]|USA<br>nem Európa<br>nem Ausztrália| `westus`|
|[au.luis.ai][au.luis.ai]|Ausztrália| `australiaeast`|
|[eu.luis.ai][eu.luis.ai]|Európa|`westeurope`|

A szerzői régiók [párosított feladatátvételi régiókkal](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)rendelkeznek. 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Közzétételi régiók és Azure-erőforrások
Az alkalmazás közzé van téve a LUIS erőforrások a LUIS portálon hozzá társított összes régióba. Ha például egy [www.Luis.ai][www.luis.ai]-on létrehozott alkalmazáshoz létrehoz egy Luis vagy kognitív szolgáltatási erőforrást a **westus** -ben, és [hozzáadja az alkalmazáshoz erőforrásként](luis-how-to-azure-subscription.md), akkor az alkalmazás az adott régióban lesz közzétéve. 

## <a name="public-apps"></a>Nyilvános alkalmazások
A nyilvános alkalmazás közzé van téve az összes régióban, hogy egy felhasználó egy LUIS-erőforrás régió-alapú kulccsal hozzáférhet az alkalmazás bármelyik régióban az erőforrás-kulcs társítva.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>A közzétételi régiók a létrehozási régiókhoz vannak kötve

A szerzői műveletek terület app csak közzétehető egy megfelelő régió közzététele. Ha az alkalmazás jelenleg a megfelelő szerzői régióban, alkalmazást, és importálja azt a megfelelő szerzői régió a közzétételi régiója. 

A létrehozott LUIS-alkalmazások https://www.luis.ai kivételével az összes végpontok teheti közzé a [Európai](#publishing-to-europe) és [ausztrál](#publishing-to-australia) régióban. 

## <a name="publishing-to-europe"></a>Közzététel az Európa

Tesz közzé, az Európai régiókban, létre kell hozni a LUIS Apps alkalmazások https://eu.luis.ai csak. Ha közzé bárhol ellenkező esetben az Európai régióban a kulcs használatával kísérli meg, LUIS figyelmeztető üzenetet jeleníti meg. Ehelyett használjon https://eu.luis.ai. A [https://eu.luis.ai][eu.luis.ai] -ben létrehozott Luis-alkalmazások nem lesznek automatikusan migrálva más régiókba. Exportálja és importálja a LUIS-alkalmazásokon ahhoz, hogy telepítse át.

## <a name="europe-publishing-regions"></a>Európa közzétételi régiói

 Globális régió | API-régió készítése & authoring webhelye| Közzétételi & régió lekérdezése<br>`API region name`   |  Végpont URL-formátum   |
|-----|------|------|------|
| [Európa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Közép-Franciaország<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Európa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Észak-Európa<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Európa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Nyugat-Európa<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Európa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Az Egyesült Királyság déli régiója<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Közzététel az Ausztrália

Az ausztráliai régiókban való közzétételéhez, LUIS alkalmazások létrehozása, https://au.luis.ai csak. Ha közzé bárhol ellenkező esetben az ausztráliai régióban a kulcs használatával kísérli meg, LUIS figyelmeztető üzenetet jeleníti meg. Ehelyett használjon https://au.luis.ai. A [https://au.luis.ai][au.luis.ai] -ben létrehozott Luis-alkalmazások nem lesznek automatikusan migrálva más régiókba. Exportálja és importálja a LUIS-alkalmazásokon ahhoz, hogy telepítse át.

## <a name="australia-publishing-regions"></a>Ausztráliai közzétételi régiók

 Globális régió | API-régió készítése & authoring webhelye| Közzétételi & régió lekérdezése<br>`API region name`   |  Végpont URL-formátum   |
|-----|------|------|------|
| [Ausztrália](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Kelet-Ausztrália<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Közzététel más régiókban

A többi régióban való közzétételhez [https://www.luis.ai](https://www.luis.ai) csak a Luis-alkalmazásokat kell létrehoznia. 

## <a name="other-publishing-regions"></a>Egyéb közzétételi régiók

 Globális régió | API-régió készítése & authoring webhelye| Közzétételi & régió lekérdezése<br>`API region name`   |  Végpont URL-formátum   |
|-----|------|------|------|
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Közép-India<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Kelet-Ázsia<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Kelet-Japán<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Nyugat-Japán<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Korea középső régiója<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Délkelet-Ázsia<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Közép-Kanada<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA középső régiója<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | East US<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA 2. keleti régiója<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA északi középső régiója<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Észak-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA déli középső régiója<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA nyugati középső régiója<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA nyugati régiója<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA nyugati régiója, 2.<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Dél-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Dél-Brazília<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Végpontok

A LUIS jelenleg 2 végponttal rendelkezik: egy az authoring és egy a lekérdezés-előrejelzési elemzéshez.

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

## <a name="failover-regions"></a>Feladatátvételi régiók

Minden régió rendelkezik egy másodlagos régióval, amely feladatátvételt hajt végre. Európa az Egyesült Államokban és Ausztráliában is feladatátvételt hajt végre az Ausztráliában belül.

A szerzői régiók [párosított feladatátvételi régiókkal](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)rendelkeznek. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Előre összeállított entitások referenciája](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
