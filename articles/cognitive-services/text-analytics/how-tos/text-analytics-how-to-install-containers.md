---
title: Tárolók telepítése és futtatása – Szövegelemzés
titleSuffix: Azure Cognitive Services
description: A Szövegelemzés tárolóinak letöltése, telepítése és futtatása ebben a forgatókönyv-oktatóanyagban.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 65033f9b6599d690b1097b4b78aa01148a40fc39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037505"
---
# <a name="install-and-run-text-analytics-containers"></a>A Text Analytics-tárolók telepítése és futtatása

A tárolók lehetővé teszik a szöveges analitikus API-k futtatását a saját környezetében, és kiválóan megfelelnek az adott biztonsági és adatirányítási követelményeknek. A Text Analytics-tárolók fejlett természetes nyelvi feldolgozást biztosítanak a nyers szövegen keresztül, és három fő funkciót tartalmaznak: hangulatelemzés, kulcskifejezések kinyerése és nyelvfelismerés. Az entitáscsatolás jelenleg nem támogatott egy tárolóban.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

> [!IMPORTANT]
> Az ingyenes fiók havonta legfeljebb 5000 tranzakciót bonyolít le, és csak az **ingyenes** és **a standard** <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">tarifacsomagok <span class="docon docon-navigate-external x-hidden-focus"></span> </a> érvényesek a tárolókra. A tranzakciókérési díjakról az [Adatkorlátok](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits)című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

A Text Analytics-tárolók futtatásához rendelkeznie kell a gazdaszámítógép és a tároló környezetek futtatásához.

## <a name="preparation"></a>Előkészítés

A Text Analytics-tárolók használata előtt meg kell felelnie az alábbi előfeltételeknek:

|Kötelező|Cél|
|--|--|
|Docker-motor| A Docker-motort egy [gazdaszámítógépen](#the-host-computer)kell telepíteni. A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszereken. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).<br><br> A Docker-t úgy kell konfigurálni, hogy a tárolók csatlakozhassanak az Azure-hoz, és számlázási adatokat küldjenek az Azure-ba. <br><br> **Windows**rendszeren a Docker-t is be kell állítani linuxos tárolók támogatására.<br><br>|
|A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a nyilvántartásokról, az adattárakról, a tárolókról és a tárolórendszerképekről, valamint az alapvető `docker` parancsok ismeretéről.| 
|Szövegelemzési erőforrás |A tároló használatához a következőkre van szüksége:<br><br>Egy Azure [Text Analytics-erőforrás](../../cognitive-services-apis-create-account.md) a társított API-kulcs és végpont URI beszerezéséhez. Mindkét érték elérhető az Azure Portal text analytics áttekintése és a kulcsok oldalakon, és a tároló elindításához szükséges.<br><br>**{API_KEY}**: A **Kulcsok** lapon elérhető két erőforráskulcs egyike<br><br>**{ENDPOINT_URI}**: Az **Áttekintés** lapon megadott végpont|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>A tárolóra vonatkozó követelmények és ajánlások

Az alábbi táblázat ismerteti a minimális és ajánlott CPU-magok, legalább 2,6 gigahertzes (GHz) vagy gyorsabb, és a memória, gigabájtban (GB), lefoglalni az egyes Text Analytics-tárolók.

# <a name="key-phrase-extraction"></a>[Kulcskifejezések kinyerése](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-requirements](../includes/key-phrase-extraction-container-requirements.md)]

# <a name="language-detection"></a>[Nyelvfelismerés](#tab/language)

[!INCLUDE [language-detection-container-requirements](../includes/language-detection-container-requirements.md)]

# <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-requirements](../includes/sentiment-analysis-container-requirements.md)]

***

* Minden magnak legalább 2,6 gigahertzesnek (GHz) vagy gyorsabbnak kell lennie.
* TPS - tranzakciók másodpercenként

A mag és `--cpus` a `--memory` memória megfelel nek a `docker run` és a beállításoknak, amelyek a parancs részeként használatosak.

## <a name="get-the-container-image-with-docker-pull"></a>A tárolókép beszerezése`docker pull`

A Text Analytics tárolólemezképei a Microsoft Container Registry szolgáltatásban érhetők el.

# <a name="key-phrase-extraction"></a>[Kulcskifejezések kinyerése](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-repository](../includes/key-phrase-extraction-container-repository.md)]

# <a name="language-detection"></a>[Nyelvfelismerés](#tab/language)

[!INCLUDE [language-detection-container-repository](../includes/language-detection-container-repository.md)]

# <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-repository](../includes/sentiment-analysis-container-repository.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-text-analytics-containers"></a>Docker-lekérések a Text Analytics-tárolókhoz

# <a name="key-phrase-extraction"></a>[Kulcskifejezések kinyerése](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Nyelvfelismerés](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

***

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a [gazdaszámítógépen](#the-host-computer)van, használja a következő eljárást a tárolóval való munkához.

1. [Futtassa a tárolót](#run-the-container-with-docker-run)a szükséges számlázási beállításokkal. További [példák](../text-analytics-resource-container-config.md#example-docker-run-commands) a `docker run` parancs állnak rendelkezésre.
1. [Kérdezze meg a tároló előrejelzési végpontját.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Futtassa a tartályt`docker run`

A [docker run](https://docs.docker.com/engine/reference/commandline/run/) parancs futtatásához a három tároló bármelyikét futtatja. A [szükséges paraméterek összegyűjtése](#gathering-required-parameters) című dokumentumban `{ENDPOINT_URI}` részletesen tájékot megtudhatja, hogyan szerezheti be a és `{API_KEY}` az értékeket.

[Példák](../text-analytics-resource-container-config.md#example-docker-run-commands) `docker run` a parancs állnak rendelkezésre.

# <a name="key-phrase-extraction"></a>[Kulcskifejezések kinyerése](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Nyelvfelismerés](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

***

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` a lehetőséget és a beállításokat meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információ: [Billing](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat biztosít.

Használja az `http://localhost:5000`állomás, , tároló API-k.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót kimeneti [csatlakoztatással](../text-analytics-resource-container-config.md#mount-settings) és naplózással futtatja, a tároló naplófájlokat hoz létre, amelyek hasznosak a tároló indításakor vagy futtatásakor felmerülő problémák elhárításához.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

A Text Analytics-tárolók számlázási adatokat küldenek az Azure-ba az Azure-fiókjában _lévő Text Analytics-erőforrás_ használatával. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezekről a beállításokról a [Tárolók konfigurálása](../text-analytics-resource-container-config.md)című témakörben talál további információt.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összefoglalás

Ebben a cikkben ismertetheti a szövegelemzési tárolók letöltésére, telepítésére és futtatására vonatkozó fogalmakat és munkafolyamatokat. Összegezve:

* A Text Analytics három Linux-tárolót biztosít a Docker számára, amelyek különböző képességeket foglalnak magukban:
   * *Kulcskifejezések kinyerése*
   * *Nyelvfelismerés*
   * *Hangulatelemzés*
* A tárolórendszerképek az Azure-beli Microsoft Container Registry (MCR) rendszerből töltődnek le.
* A tárolórendszerképek a Dockerben futnak.
* A REST API vagy az SDK segítségével a Text Analytics-tárolókban műveleteket hívhat meg a tároló gazdaURI-jának megadásával.
* A tároló példányosításakor meg kell adnia a számlázási adatokat.

> [!IMPORTANT]
> A Cognitive Services-tárolók nem rendelkeznek licenccel anélkül, hogy az Azure-hoz csatlakoznának a méréshez. Az ügyfeleknek lehetővé kell tenni, hogy a tárolók mindig kommunikálják a számlázási adatokat a mérési szolgáltatással. A Cognitive Services-tárolók nem küldenek ügyféladatokat (például az elemzett képet vagy szöveget) a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Tekintse át [a tárolók konfigurálása](../text-analytics-resource-container-config.md) a konfigurációs beállításokat
* A működéssel kapcsolatos problémák megoldásához olvassa el a [gyakran ismételt kérdéseket.](../text-analytics-resource-faq.md)
