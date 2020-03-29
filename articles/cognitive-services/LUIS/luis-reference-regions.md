---
title: Végpontok & közzétételi területek – LUIS
description: Az Azure Portalon megadott régió ugyanaz, ahol közzé fogja tenni a LUIS alkalmazást, és ugyanebben a régióban létrejön egy végpont URL-címe.
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: a7709d15a51637772d7a4775cd71c307dc21c52b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292088"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Szerkesztési és közzétételi régiók és a kapcsolódó kulcsok

Három szerzői régiók támogatja a megfelelő LUIS portálok. Egy LUIS-alkalmazás több régióban való közzétételéhez régiónként legalább egy kulcsra van szükség.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS authoring régiók
Három LUIS szerzői portálok, régió alapján. A létrehozást és a közzétételt ugyanabban a régióban kell elvégezni.

|LUIS|Szerzői régió|Az Azure régió neve|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[preview.luis.ai](https://preview.luis.ai)|Egyesült Államok<br>nem Európa<br>nem Ausztrália| `westus`|
|[au.luis.ai][au.luis.ai] <br>[preview.au.luis.ai](https://preview.au.luis.ai)|Ausztrália| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] <br>[preview.eu.luis.ai](https://preview.eu.luis.ai)|Európa|`westeurope`|

A szerzői régiók [párosították a feladatátvételi régiókat.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Közzétételi régiók és Azure-erőforrások
Az alkalmazás a LUIS-portálon hozzáadott LUIS-erőforrásokhoz társított összes régióban megjelenik. Ha például egy [www.luis.ai][www.luis.ai]létrehozott alkalmazás esetén luis- vagy Cognitive Service-erőforrást hoz létre **a Westusban,** és [erőforrásként adja hozzá az alkalmazáshoz,](luis-how-to-azure-subscription.md)az alkalmazás az adott régióban közzétételre kerül.

## <a name="public-apps"></a>Nyilvános alkalmazások
Egy nyilvános alkalmazás minden régióban közzé van téve, így a régióalapú LUIS erőforráskulccsal rendelkező felhasználó hozzáférhet az alkalmazáshoz abban a régióban, amely az erőforráskulcsához van társítva.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>A közzétételi régiók szerzői régiókhoz vannak kötve

A szerzői régió alkalmazás csak egy megfelelő közzétételi régióban tehető közzé. Ha az alkalmazás jelenleg nem a megfelelő szerzői területen található, exportálja az alkalmazást, és importálja a közzétételi régiónak megfelelő szerzői régióba.

Luis-alkalmazások https://www.luis.ai létrehozott közzé tehető az összes végponton, kivéve az [európai](#publishing-to-europe) és [ausztrál](#publishing-to-australia) régiókban.

## <a name="publishing-to-europe"></a>Kiadói könyv Európában

Az európai régiókban való közzétételhez https://eu.luis.ai csak LUIS-alkalmazásokat hozhat létre. Ha az európai régióban egy kulccsal máshol próbál közzétenni, a LUIS figyelmeztető üzenetet jelenít meg. Ehelyett használja https://eu.luis.aia használatát. Luis-alkalmazások [https://eu.luis.ai][eu.luis.ai] létrehozott nem automatikusan áttérni más régiókba. Exportálja, majd importálja a LUIS alkalmazást az áttelepítéshez.

## <a name="europe-publishing-regions"></a>Európa kiadói régiói

 Globális régió | Szerzői API-régió & szerzői webhely| Közzétételi & lekérdezési terület<br>`API region name`   |  Végpont URL-formátuma   |
|-----|------|------|------|
| [Európa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Közép-Franciaország<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Európa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Észak-Európa<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Európa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Nyugat-Európa<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Európa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Az Egyesült Királyság déli régiója<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Közzététel Ausztráliában

Az ausztrál régiókban való közzétételhez https://au.luis.ai csak LUIS-alkalmazásokat hozhat létre. Ha az ausztrál régióban egy kulccsal máshol próbál közzétenni, a LUIS figyelmeztető üzenetet jelenít meg. Ehelyett használja https://au.luis.aia használatát. Luis-alkalmazások [https://au.luis.ai][au.luis.ai] létrehozott nem automatikusan áttérni más régiókba. Exportálja, majd importálja a LUIS alkalmazást az áttelepítéshez.

## <a name="australia-publishing-regions"></a>Ausztrália kiadói régiói

 Globális régió | Szerzői API-régió & szerzői webhely| Közzétételi & lekérdezési terület<br>`API region name`   |  Végpont URL-formátuma   |
|-----|------|------|------|
| [Ausztrália](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Kelet-Ausztrália<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-other-regions"></a>Közzététel más régiókban

A többi régióban való közzétételhez [https://www.luis.ai](https://www.luis.ai) csak luis-alkalmazásokat hozhat létre.

## <a name="other-publishing-regions"></a>Egyéb kiadói területek

 Globális régió | Szerzői API-régió & szerzői webhely| Közzétételi & lekérdezési terület<br>`API region name`   |  Végpont URL-formátuma   |
|-----|------|------|------|
| Afrika | `westus`<br>[www.luis.ai][www.luis.ai]| Dél-Afrika Észak-Afrika<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Közép-India<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Kelet-Ázsia<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Kelet-Japán<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Nyugat-Japán<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Dél-Korea középső régiója<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ázsia | `westus`<br>[www.luis.ai][www.luis.ai]| Délkelet-Ázsia<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Közép-Kanada<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA középső régiója<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA keleti régiója<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Észak-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA 2. keleti régiója<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Észak-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA északi középső régiója<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Észak-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA déli középső régiója<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA nyugati középső régiója<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Észak-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA nyugati régiója<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Észak-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA nyugati régiója, 2.<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Dél-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Dél-Brazília<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="endpoints"></a>Végpontok

További információ a [szerzői és előrejelzési végpontokról.](developer-reference-resource.md)

## <a name="failover-regions"></a>Feladatátvételi régiók

Minden régiónak van egy másodlagos régiója, amelynek feladatátvételre van átadására. Európa európa,Ausztrália pedig Ausztrálián belül.

A szerzői régiók [párosították a feladatátvételi régiókat.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Előre összeállított entitások hivatkozása](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
