---
title: Tárolók telepítése és futtatása – Text Analytics
titleSuffix: Azure Cognitive Services
description: A Text Analytics tárolóinak letöltése, telepítése és futtatása az útmutató oktatóanyagában.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2d44df1bb828140e662b06ffbe5fb14f207f68e0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877088"
---
# <a name="install-and-run-text-analytics-containers"></a>A Text Analytics-tárolók telepítése és futtatása

A tárolók lehetővé teszik a szöveges elemzési API-k futtatását a saját környezetében, és kiválóan használhatók az adott biztonsági és adatirányítási követelményekhez. A Text Analytics tárolók fejlett természetes nyelvi feldolgozást biztosítanak a nyers szövegeken, és három fő funkciót tartalmaznak: a hangulat elemzését, a kulcsfontosságú kifejezés kinyerését és a nyelvfelismerés. Az entitások csatolása jelenleg nem támogatott egy tárolóban.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

> [!IMPORTANT]
> Az ingyenes fiók havonta 5 000 tranzakcióra van korlátozva, és csak az **ingyenes** és a **standard** <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">szintű <span class="docon docon-navigate-external x-hidden-focus"></span> díjszabás</a> érvényes a tárolók esetében. A tranzakciós kérelmek díjszabásával kapcsolatos további információkért lásd az [Adatkorlátokat](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits)ismertető témakört.

## <a name="prerequisites"></a>Előfeltételek

A Text Analytics tárolók futtatásához rendelkeznie kell a gazdagép és a tároló környezetével.

## <a name="preparation"></a>Előkészítés

Text Analytics tárolók használata előtt meg kell felelnie a következő előfeltételeknek:

|Kötelező|Cél|
|--|--|
|Docker-motor| A Docker-motornak telepítve kell lennie a [gazdagépen](#the-host-computer). A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszereken. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).<br><br> A Docker-t úgy kell konfigurálni, hogy lehetővé tegye a tárolók számára az Azure-ba való kapcsolódást és a számlázási információk küldését. <br><br> **Windows rendszeren a**Docker-t is konfigurálni kell a Linux-tárolók támogatásához.<br><br>|
|A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a kibocsátásiegység-forgalmi jegyzékekről, a adattárakról, a tárolók és a `docker` tárolók lemezképéről, valamint az alapszintű parancsokról.| 
|Erőforrás Text Analytics |A tároló használatához a következőket kell tennie:<br><br>Egy Azure [text Analytics erőforrás](../../cognitive-services-apis-create-account.md) a társított API-kulcs és végpont URI-azonosító lekéréséhez. Mindkét érték elérhető a Azure Portal Text Analytics áttekintés és kulcsok oldalain, és a tároló indításához szükséges.<br><br>**{API_KEY}**: a **kulcsok** oldalon található két elérhető erőforrás-kulcs egyike<br><br>**{ENDPOINT_URI}**: az **Áttekintés** lapon megadott végpont|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>A tárolóra vonatkozó követelmények és javaslatok

Az alábbi táblázat a minimális és ajánlott CPU-magot, legalább 2,6 gigahertzes (GHz) vagy gyorsabb, valamint a memóriát (GB) tartalmazza az egyes Text Analytics-tárolók lefoglalásához.

# <a name="key-phrase-extraction"></a>[Kulcskifejezések kinyerése](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-requirements](../includes/key-phrase-extraction-container-requirements.md)]

# <a name="language-detection"></a>[Nyelvfelismerés](#tab/language)

[!INCLUDE [language-detection-container-requirements](../includes/language-detection-container-requirements.md)]

# <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-requirements](../includes/sentiment-analysis-container-requirements.md)]

***

* Minden mag legalább 2,6 gigahertz (GHz) vagy gyorsabb lehet.
* TPS – tranzakció/másodperc

Az alap és a memória a `--cpus` `docker run` parancs `--memory` részeként használt és beállításoknak felel meg.

## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének beolvasása a`docker pull`

A Text Analytics tároló lemezképei a Microsoft Container Registry érhetők el.

# <a name="key-phrase-extraction"></a>[Kulcskifejezések kinyerése](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-repository](../includes/key-phrase-extraction-container-repository.md)]

# <a name="language-detection"></a>[Nyelvfelismerés](#tab/language)

[!INCLUDE [language-detection-container-repository](../includes/language-detection-container-repository.md)]

# <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-repository](../includes/sentiment-analysis-container-repository.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-text-analytics-containers"></a>Docker-lekérés a Text Analytics tárolók számára

# <a name="key-phrase-extraction"></a>[Kulcskifejezések kinyerése](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Nyelvfelismerés](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

***

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a [gazdagépen](#the-host-computer)található, a következő eljárással dolgozhat a tárolóval.

1. [Futtassa a tárolót](#run-the-container-with-docker-run)a kötelező számlázási beállításokkal. További [példák](../text-analytics-resource-container-config.md#example-docker-run-commands) a `docker run` parancsra.
1. [A tároló előrejelzési végpontjának lekérdezése](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>A tároló futtatása a`docker run`

A három tároló bármelyikének futtatásához használja a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsot. A `{ENDPOINT_URI}` és `{API_KEY}` értékek beszerzésével kapcsolatos részletekért tekintse meg a [szükséges paraméterek összegyűjtését](#gathering-required-parameters) ismertető témakört.

[Examples](../text-analytics-resource-container-config.md#example-docker-run-commands) A `docker run` parancs például elérhető.

# <a name="key-phrase-extraction"></a>[Kulcskifejezések kinyerése](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Nyelvfelismerés](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

***

> [!IMPORTANT]
> A `Eula`tároló `Billing`futtatásához `ApiKey` meg kell adni a, a és a beállításokat. Ellenkező esetben a tároló nem indul el.  További információ: [számlázás](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat biztosít.

A tároló API `http://localhost:5000`-k esetében használja a gazdagépet.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót egy kimeneti [csatlakoztatással](../text-analytics-resource-container-config.md#mount-settings) futtatja, és a naplózás engedélyezve van, a tároló olyan naplófájlokat hoz létre, amelyek hasznosak a tároló indításakor vagy futtatásakor felmerülő problémák elhárításához.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

A Text Analytics tárolók számlázási adatokat küldenek az Azure-nak az Azure-fiókjában lévő _text Analytics_ -erőforrás használatával. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

További információ ezekről a beállításokról: [tárolók konfigurálása](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta Text Analytics tárolók letöltésére, telepítésére és futtatására vonatkozó fogalmakat és munkafolyamatokat. Összegezve:

* A Text Analytics három Linux-tárolót biztosít a Docker számára, és különböző képességeket ágyaz be:
   * *Kulcskifejezések kinyerése*
   * *Nyelvfelismerés*
   * *Hangulatelemzés*
* A rendszer letölti a tároló lemezképeit a Microsoft Container Registry (MCR) szolgáltatásból az Azure-ban.
* A tároló lemezképei a Docker-ben futnak.
* A REST API vagy az SDK használatával meghívhatja a műveleteket Text Analytics tárolókban a tároló gazdagép URI azonosítójának megadásával.
* A tárolók létrehozásakor számlázási adatokat kell megadnia.

> [!IMPORTANT]
> Cognitive Services tárolók nem futtathatók az Azure-hoz való csatlakozás nélkül. Az ügyfeleknek engedélyeznie kell, hogy a tárolók a számlázási adatokat mindig a mérési szolgáltatással kommunikáljanak. Cognitive Services tárolók nem küldenek ügyféladatokat (például az elemzett képet vagy szöveget) a Microsoftnak.

## <a name="next-steps"></a>További lépések

* A [tárolók konfigurálásának](../text-analytics-resource-container-config.md) áttekintése konfigurációs beállításokhoz
* A funkciókkal kapcsolatos problémák megoldásához tekintse meg a [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md) című témakört.
