---
title: Tárolók telepítése és futtatása
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Hogyan letöltése, telepítése és -tárolókat futtathat szövegelemzési az bemutató oktatóanyag.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 07fbf51f584d691b08d94f68fefa8c3f9348227f
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55294828"
---
# <a name="install-and-run-text-analytics-containers"></a>Telepítse és futtassa a Text Analytics tárolók

A Text Analytics tárolók biztosítják a természetes nyelvi feldolgozás speciális a nyers szöveg fölé, és magában foglal három fő funkciót: hangulatelemzést, kulcsszókeresést és nyelvfelismerést. Entitáskapcsolás jelenleg nem támogatott egy tárolóban. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Bármely tárolóra a Szövegelemzés futtatásához az alábbiakkal kell rendelkeznie:

## <a name="preparation"></a>Előkészítés

Szövegelemzés tárolók használata előtt a következő előfeltételeknek kell megfelelnie:

|Szükséges|Cél|
|--|--|
|Docker-motor| A Docker-motor telepítve van szüksége egy [gazdaszámítógép](#the-host-computer). A docker csomagokat biztosít, a Docker-környezet konfigurálása a [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), és [Linux](https://docs.docker.com/engine/installation/#supported-platforms). A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).<br><br> Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz. <br><br> **A Windows**, a Docker Linux-tárolók támogatása is kell konfigurálni.<br><br>|
|Docker-ismeretek | A Docker fő fogalmaira, például a beállításjegyzékek, adattárak, tárolók, és tárolórendszerképeket, valamint alapszintű ismerete alapvető ismeretekkel kell `docker` parancsokat.| 
|Text Analytics-erőforrás |A tároló használatához rendelkeznie kell:<br><br>A [ _Szövegelemzés_ ](text-analytics-how-to-access-key.md) Azure-erőforráshoz tartozó számlázási kulcs és számlázási végpont URI azonosítója. Mindkét értéket az Azure Portalon Text Analytics áttekintése és a kulcsok oldalon érhető el, és a tároló indításához szükséges.<br><br>**{BILLING_KEY}** : erőforrás-kulcs<br><br>**{BILLING_ENDPOINT_URI}** : végpont URI-példa: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

### <a name="the-host-computer"></a>A számítógép

[!INCLUDE [Request access to private preview](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

A következő táblázat ismerteti a minimális és ajánlott, processzormagot legalább 2.6-os gigahertz (GHz) vagy gyorsabb, és a memória (gigabájtban), minden egyes Szövegelemzés tároló lefoglalása.

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
|Kulcskifejezések kinyerése | 1 mag, 2 GB memória | 1 mag, 4 GB memória |
|Nyelvfelismerés | 1 mag, 2 GB memória | 1 mag, 4 GB memória |
|Véleményelemzés | 1 mag, 2 GB memória | 1 mag, 4 GB memória |

Core és a memória felel meg a `--cpus` és `--memory` részeként használt beállítások a `docker run` parancsot.

## <a name="get-the-container-image-with-docker-pull"></a>A tárolórendszerkép beolvasása `docker pull`

Tárolórendszerképek szövegelemzési Microsoft Tárolóregisztrációs adatbázisból érhetők el. 

| Tároló | Tárház |
|-----------|------------|
|Kulcskifejezések kinyerése | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Nyelvfelismerés | `mcr.microsoft.com/azure-cognitive-services/language` |
|Véleményelemzés | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Használja a [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal töltse le a tárolórendszerkép Microsoft Tárolóregisztrációs adatbázisból...

A Text Analytics tárolók rendelkezésre álló címkék teljes leírását lásd a következő tárolókat a Docker hub:

* [Kulcskifejezések kinyerése](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Nyelvfelismerés](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Hangulatelemzés](https://go.microsoft.com/fwlink/?linkid=2018654)

Használja a [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal letöltheti egy tárolórendszerképet.


### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>A kulcs kifejezés kinyerése tároló docker pull

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>A nyelv észlelése tároló docker pull

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-container"></a>A róluk szóló véleményeket tároló docker pull

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>A tároló használata

Ha a tároló a [gazdaszámítógép](#the-host-computer), a következő eljárás használható a tárolóval.

1. [A tároló futtatásához](#run-the-container-with-docker-run), a szükséges a számlázás a beállításokat. További [példák](../text-analytics-resource-container-config.md#example-docker-run-commands) , a `docker run` parancs érhetők el. 
1. [A tároló előrejelzési végpontja lekérdezése](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>A tároló futtatásához `docker run`

Használja a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) parancs futtatása bármely három tárolóra. A parancs paraméterei a következők:

| Helyőrző | Érték |
|-------------|-------|
|{BILLING_KEY} | Ezt a kulcsot szolgál a tárolót, és az Azure Portalon Text Analytics kulcsok lapján található.  |
|{BILLING_ENDPOINT_URI} | A számlázási végpont URI azonosítóját az Azure Portalon Text Analytics áttekintése lapon érhető el.|

Cserélje le ezeket a paramétereket a következő példában a saját értékeire `docker run` parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Ezzel a paranccsal:

* A tároló rendszerképét kulcskifejezések tárolóban fut
* Foglalja le egy processzormagot és memóriát 4 gigabájt (GB)
* Elérhetővé teszi az 5000-es TCP-porton és a egy pszeudo-TTY lefoglalja a tároló
* Után kilép, automatikusan eltávolítja a tárolót. A tároló rendszerképét az továbbra is elérhető az állomáson. 

További [példák](../text-analytics-resource-container-config.md#example-docker-run-commands) , a `docker run` parancs érhetők el. 

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontja lekérdezése

A tároló REST-alapú lekérdezési előrejelzési végpontot API-kat biztosít. 

Használja a gazdagép https://localhost:5000, API-k tároló.

## <a name="stop-the-container"></a>Állítsa le a tároló

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

A kimenet futtatásakor a tároló [csatlakoztatási](../text-analytics-resource-container-config.md#mount-settings) és naplózás engedélyezve van, a tárolót hoz létre a naplófájlokat, amelyek hasznosak a hibaelhárítás indítása, vagy a tároló futtatása közben történik. 

## <a name="containers-api-documentation"></a>Tároló API-dokumentáció

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>Számlázás

A Text Analytics tárolók küldés számlázási adatokat az Azure-ba, a használatával egy _Text Analytics_ erőforrást az Azure-fiókjával. 

Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók nem vásárlói adatokat küldeni a Microsoftnak. 

A `docker run` parancsot használja a következő argumentumok számlázás szempontjából:

| Beállítás | Leírás |
|--------|-------------|
| `ApiKey` | Az API-kulcsot a _Szövegelemzés_ erőforrás számlázási adatok nyomon követésére szolgál. |
| `Billing` | Az a végpont a _Text Analytics_ erőforrás számlázási adatok nyomon követésére szolgál.|
| `Eula` | Azt jelzi, hogy Ön már elfogadta a tároló licencét.<br/>Ez a beállítás értékét állítsa `accept`. |

> [!IMPORTANT]
> Mindhárom meg kell adni az érvényes értékek, vagy a tároló nem indul el.

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](../text-analytics-resource-container-config.md).

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
* Tekintse meg [– gyakori kérdések (GYIK)](../text-analytics-resource-faq.md) funkció kapcsolatos problémák megoldásához.

