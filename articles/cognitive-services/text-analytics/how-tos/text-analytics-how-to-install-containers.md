---
title: Tárolók telepítése és futtatása – Text Analytics
titleSuffix: Azure Cognitive Services
description: Hogyan letöltése, telepítése és -tárolókat futtathat szövegelemzési az bemutató oktatóanyag.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dapine
ms.openlocfilehash: ff0be2e9dada758cce96ba7c5eebbf03b00f56c6
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971447"
---
# <a name="install-and-run-text-analytics-containers"></a>Text Analytics tárolók telepítése és futtatása

A tárolók lehetővé teszik a szöveges elemzési API-k futtatását a saját környezetében, és kiválóan használhatók az adott biztonsági és adatirányítási követelményekhez. A Text Analytics tárolók fejlett természetes nyelvi feldolgozást biztosítanak a nyers szövegeken, és három fő funkciót tartalmaznak: a hangulat elemzését, a kulcsfontosságú kifejezés kinyerését és a nyelvfelismerés. Az entitások csatolása jelenleg nem támogatott egy tárolóban.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A Text Analytics tárolók futtatásához rendelkeznie kell a gazdagép és a tároló környezetével.

## <a name="preparation"></a>Előkészítés

Szövegelemzés tárolók használata előtt a következő előfeltételeknek kell megfelelnie:

|Kötelező|Cél|
|--|--|
|Docker-motor| A Docker-motornak telepítve kell lennie a [gazdagépen](#the-host-computer). A Docker csomagokat biztosít a Docker-környezet konfigurálásához [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)és [Linux](https://docs.docker.com/engine/installation/#supported-platforms)rendszereken. A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).<br><br> Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz. <br><br> **Windows rendszeren a**Docker-t is konfigurálni kell a Linux-tárolók támogatásához.<br><br>|
|A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a kibocsátásiegység-forgalmi jegyzékekről, a adattárakról, a tárolók és a `docker` tárolók lemezképéről, valamint az alapszintű parancsokról.| 
|Erőforrás Text Analytics |A tároló használatához a következőket kell tennie:<br><br>Egy Azure [text Analytics erőforrás](../../cognitive-services-apis-create-account.md) a társított API-kulcs és végpont URI-azonosító lekéréséhez. Mindkét érték elérhető a Azure Portal Text Analytics áttekintés és kulcsok oldalain, és a tároló indításához szükséges.<br><br>**{API_KEY}** : A **kulcsok** oldalon található két elérhető erőforrás-kulcs egyike<br><br>**{ENDPOINT_URI}** : Az **Áttekintés** oldalon megadott végpont|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

A következő táblázat ismerteti a minimális és ajánlott, processzormagot legalább 2.6-os gigahertz (GHz) vagy gyorsabb, és a memória (gigabájtban), minden egyes Szövegelemzés tároló lefoglalása.

| Tároló | Minimális | Ajánlott | TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|Kulcskifejezések kinyerése | 1 mag, 2 GB memória | 1 mag, 4 GB memória |15, 30|
|Nyelvfelismerés | 1 mag, 2 GB memória | 1 mag, 4 GB memória |15, 30|
|Véleményelemzés | 1 mag, 2 GB memória | 1 mag, 4 GB memória |15, 30|

* Minden mag legalább 2,6 gigahertz (GHz) vagy gyorsabb lehet.
* TPS – tranzakció/másodperc

Az alap és a memória a `--cpus` `docker run` parancs `--memory` részeként használt és beállításoknak felel meg.

## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének beolvasása a`docker pull`

Tárolórendszerképek szövegelemzési Microsoft Tárolóregisztrációs adatbázisból érhetők el.

| Tároló | Tárház |
|-----------|------------|
|Kulcskifejezések kinyerése | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Nyelvfelismerés | `mcr.microsoft.com/azure-cognitive-services/language` |
|Véleményelemzés| `mcr.microsoft.com/azure-cognitive-services/sentiment` |

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal tölthet le egy tároló rendszerképet a Microsoft Container Registryból.

A Text Analytics tárolók rendelkezésre álló címkék teljes leírását lásd a következő tárolókat a Docker hub:

* [Kulcskifejezések kinyerése](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Nyelvfelismerés](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Hangulatelemzés](https://go.microsoft.com/fwlink/?linkid=2018654)

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) parancs használatával töltse le a tárolók rendszerképét.

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>A Docker lekéri a Key kifejezés kinyerésének tárolóját

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>Docker-lekérés a nyelvfelismerés tárolóhoz

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-container"></a>Docker-lekérés az érzelmek tárolója számára

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a gazdagépen [](#the-host-computer)található, a következő eljárással dolgozhat a tárolóval.

1. [Futtassa a tárolót](#run-the-container-with-docker-run)a kötelező számlázási beállításokkal. További [példák](../text-analytics-resource-container-config.md#example-docker-run-commands) a `docker run` parancsra.
1. [A tároló előrejelzési végpontjának lekérdezése](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>A tároló futtatása a`docker run`

A három tároló bármelyikének futtatásához használja a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsot. A`{Endpoint_URI}` és`{API_Key}` értékek beszerzésével kapcsolatos részletekért tekintse meg a [szükséges paraméterek](#gathering-required-parameters) összegyűjtését ismertető témakört.

[](../text-analytics-resource-container-config.md#example-docker-run-commands) A`docker run` parancs például elérhető.

### <a name="run-container-example-of-docker-run-command"></a>Tároló futtatása példa a Docker Run parancsra

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Ez a parancs:

* Egy Key mondat tárolót futtat a tároló rendszerképből.
* Egy CPU mag és 4 gigabájt (GB) memóriát foglal le
* Elérhetővé teszi az 5000-es TCP-porton és a egy pszeudo-TTY lefoglalja a tároló
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.


> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat biztosít.

A tároló API `http://localhost:5000`-k esetében használja a gazdagépet.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót egy kimeneti csatlakoztatással [](../text-analytics-resource-container-config.md#mount-settings) futtatja, és a naplózás engedélyezve van, a tároló olyan naplófájlokat hoz létre, amelyek hasznosak a tároló indításakor vagy futtatásakor felmerülő problémák elhárításához.

## <a name="billing"></a>Számlázás

A Text Analytics tárolók számlázási adatokat küldenek az Azure-nak az Azure-fiókjában lévő _text Analytics_ -erőforrás használatával. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és letöltése, telepítése és futtatása a Text Analytics tárolók munkafolyamatokat. Összegezve:

* Szövegelemzés biztosít három Linux-tárolók Docker, kulcsszókeresés, nyelvfelismerés és hangulatelemzés fejlécbe foglalja.
* Tárolórendszerképek letöltődnek az a Microsoft Container Registry (MCR) az Azure-ban.
* Tárolórendszerképek futtatása a Docker.
* Használhatja a REST API vagy SDK-val a Text Analytics tárolókban műveletek hívására adja meg a gazdagép a tároló URI-t.
* Számlázási adatokat adjon meg egy tároló hárítható el.

> [!IMPORTANT]
> Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók nem (például a lemezkép vagy az elemezni kívánt szöveget) a vásárlói adatokat küldeni a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [tárolók konfigurálása](../text-analytics-resource-container-config.md) a konfigurációs beállítások
* A funkciókkal kapcsolatos problémák megoldásához tekintse meg a [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md) című témakört.
