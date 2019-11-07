---
title: Közzétételi régiók & végpontok – LUIS
titleSuffix: Azure Cognitive Services
description: A Azure Portalban megadott régió ugyanaz, ahol a LUIS-alkalmazást közzé fogja tenni, és egy végponti URL-cím jön létre ugyanahhoz a régióhoz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: diberry
ms.openlocfilehash: fe4436afe56e631ae308b70ae35b1fb4a7c32e11
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669190"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Régiók és a hozzájuk tartozó kulcsok létrehozása és közzététele

A megfelelő LUIS-portálok három szerzői régiót támogatnak. Egy LUIS-alkalmazás több régióba való közzétételéhez régiónként legalább egy kulcsra van szükség. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS authoring-régiók
A régió alapján három LUIS authoring portál található. Ugyanabban a régióban kell létrehoznia és közzétennie. 

|LUIS|Szerzői régió|Azure-régió neve|
|--|--|--|
|[www.luis.ai][www.luis.ai]|Az USA<br>nem Európa<br>nem Ausztrália| `westus`|
|[au.luis.ai][au.luis.ai]|Ausztrália| `australiaeast`|
|[eu.luis.ai][eu.luis.ai]|Európa|`westeurope`|

A szerzői régiók [párosított feladatátvételi régiókkal](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)rendelkeznek. 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Közzétételi régiók és Azure-erőforrások
Az alkalmazás közzé van téve a LUIS-portálon hozzáadott LUIS-erőforrásokhoz társított összes régióban. Ha például egy [www.Luis.ai][www.luis.ai]-on létrehozott alkalmazáshoz létrehoz egy Luis vagy kognitív szolgáltatási erőforrást a **westus** -ben, és [hozzáadja az alkalmazáshoz erőforrásként](luis-how-to-azure-subscription.md), akkor az alkalmazás az adott régióban lesz közzétéve. 

## <a name="public-apps"></a>Nyilvános alkalmazások
A nyilvános alkalmazások minden régióban közzé vannak téve, hogy a régió-alapú LUIS-erőforrás kulcsával rendelkező felhasználók hozzáférhessenek az alkalmazáshoz, amely bármelyik régióban hozzá van rendelve az erőforrás-kulcshoz.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>A közzétételi régiók a létrehozási régiókhoz vannak kötve

Az authoring region alkalmazást csak a megfelelő közzétételi régióban lehet közzétenni. Ha az alkalmazás jelenleg nem megfelelő authoring régióban van, exportálja az alkalmazást, és importálja a közzétételi régiójának megfelelő szerzői régióba. 

A https://www.luis.aion létrehozott LUIS-alkalmazások közzétehetők az összes végponton, kivéve az [Európai](#publishing-to-europe) és [ausztráliai](#publishing-to-australia) régiókat. 

## <a name="publishing-to-europe"></a>Közzététel Európába

Az európai régiókban való közzétételhez csak https://eu.luis.aion hozhat létre LUIS alkalmazásokat. Ha az Európa régió egyik kulcsával próbál meg máshol közzétenni, a LUIS figyelmeztető üzenetet jelenít meg. Ehelyett használja a https://eu.luis.ai. A [https://eu.luis.ai][eu.luis.ai] címen létrehozott Luis-alkalmazások nem települnek át automatikusan más régiókba. Exportálja, majd importálja a LUIS alkalmazást a Migrálás érdekében.

## <a name="europe-publishing-regions"></a>Európa közzétételi régiói

 Globális régió | API-régió készítése & authoring webhelye| & Lekérdezési régió közzététele<br>`API region name`   |  Végpont URL-formátuma   |
|-----|------|------|------|
| [Európa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Közép-Franciaország<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Európa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Észak-Európa<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Európa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Nyugat-Európa<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Európa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Egyesült Királyság déli régiója<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Közzététel Ausztráliában

Az ausztráliai régiókban való közzétételhez csak https://au.luis.aion hozhat létre LUIS alkalmazásokat. Ha egy, az ausztráliai régió egyik kulcsát használva próbál meg mást közzétenni, a LUIS figyelmeztető üzenetet jelenít meg. Ehelyett használja a https://au.luis.ai. A [https://au.luis.ai][au.luis.ai] címen létrehozott Luis-alkalmazások nem települnek át automatikusan más régiókba. Exportálja, majd importálja a LUIS alkalmazást a Migrálás érdekében.

## <a name="australia-publishing-regions"></a>Ausztráliai közzétételi régiók

 Globális régió | API-régió készítése & authoring webhelye| & Lekérdezési régió közzététele<br>`API region name`   |  Végpont URL-formátuma   |
|-----|------|------|------|
| [Ausztrália](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Kelet-Ausztrália<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Közzététel más régiókban

A többi régióban való közzétételhez csak [https://www.luis.aion](https://www.luis.ai) hozhat létre Luis alkalmazásokat. 

## <a name="other-publishing-regions"></a>Egyéb közzétételi régiók

 Globális régió | API-régió készítése & authoring webhelye| & Lekérdezési régió közzététele<br>`API region name`   |  Végpont URL-formátuma   |
|-----|------|------|------|
| Afrika | `westus`<br>[www.luis.ai][www.luis.ai]| Dél-Afrika északi régiója<br>`southafricanorth` |  https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Közép-India<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Kelet-Ázsia<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Kelet-Japán<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Nyugat-Japán<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Korea középső régiója<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Délkelet-Ázsia<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Közép-Kanada<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA középső régiója<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA keleti régiója<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA 2. keleti régiója<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA északi középső régiója<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Észak-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA déli középső régiója<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA nyugati középső régiója<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Észak-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA nyugati régiója<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA 2. nyugati régiója<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Dél-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Dél-Brazília<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Végpontok

A LUIS jelenleg 2 végponttal rendelkezik: egy az authoring és egy a lekérdezés-előrejelzési elemzéshez.

|Cél|URL-cím|
|--|--|
|Tartalomkészítés|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Szöveg elemzése (lekérdezés előrejelzése)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

Az alábbi táblázat az előző táblázatban szereplő, kapcsos zárójelekkel `{}`paramétereket ismerteti.

|Paraméter|Cél|
|--|--|
|régió|Azure-régió – a szerzői műveletek és a közzététel különböző régiókban|
|appID|Az URL Route-ben használt LUIS-alkalmazás azonosítója, amely megtalálható az alkalmazás irányítópultján|
|q|az ügyfélalkalmazás, például a csevegési bot által eljuttatott teljes szöveg|

## <a name="failover-regions"></a>Feladatátvételi régiók

Minden régió rendelkezik egy másodlagos régióval, amely feladatátvételt hajt végre. Európa az Egyesült Államokban és Ausztráliában is feladatátvételt hajt végre az Ausztráliában belül.

A szerzői régiók [párosított feladatátvételi régiókkal](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)rendelkeznek. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Előre elkészített entitások referenciája](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
