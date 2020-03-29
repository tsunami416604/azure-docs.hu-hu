---
title: Konténerek telepítése és futtatása - Face
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan tölthetle le, telepíthet és futtathat a Face tárolóit ebben a forgatókönyv-oktatóanyagban.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: e467b195ab1e2124286bfef74d7d1b71a4d99dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76165978"
---
# <a name="install-and-run-face-containers-preview"></a>Face-tárolók telepítése és futtatása (előzetes verzió)

Az Azure Cognitive Services Face egy szabványosított Linux-tárolót biztosít a Docker számára, amely észleli az emberi arcokat a képeken. Azt is azonosítja attribútumok, amelyek magukban foglalják arc tereptárgyak, mint az orr és a szem, nem, kor, és egyéb gép-előre jelzett arcvonások. Az észlelés mellett a Face ellenőrizheti, hogy ugyanazon a képen vagy különböző képeken lévő két arc azonos-e megbízhatósági pontszám használatával. Face is lehet összehasonlítani arcok egy adatbázis, hogy ha egy hasonló kinézetű vagy azonos arc már létezik. A hasonló arcokat csoportokba is rendezheti a megosztott vizuális tulajdonságok használatával.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A Face szolgáltatástárolók használata előtt meg kell felelnie az alábbi előfeltételeknek.

|Kötelező|Cél|
|--|--|
|Docker-motor| A Docker-motort [gazdaszámítógépre](#the-host-computer)kell telepíteni. A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszereken. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).<br><br> A Docker-t úgy kell konfigurálni, hogy a tárolók csatlakozhassanak az Azure-hoz, és számlázási adatokat küldjenek az Azure-ba. <br><br> Windows rendszeren a Dockert is be kell állítani linuxos tárolók támogatására.<br><br>|
|A Docker ismerete | A Docker-fogalmak, például a nyilvántartások, az adattárak, a tárolók és a tárolórendszerképek alapvető ismeretekre van szüksége. Az alapvető `docker` parancsok ismeretére is szükség van.| 
|Face erőforrás |A tároló használatához a következőkre van szüksége:<br><br>Egy Azure **Face-erőforrás** és a társított API-kulcs és a végpont URI.An Azure Face resource and the associated API key and the endpoint URI. Mindkét érték elérhető az **erőforrás Áttekintés és** **Kulcsok** lapján. El kell indítaniuk a konténert.<br><br>**{API_KEY}**: A **Kulcsok** lapon elérhető két erőforráskulcs egyike<br><br>**{ENDPOINT_URI}**: Az **Áttekintés** lapon megadott végpont

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>Hozzáférés kérése a privát tároló beállításjegyzékéhez

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>A tárolóra vonatkozó követelmények és ajánlások

Az alábbi táblázat ismerteti a minimális és ajánlott CPU-magok és a memória lefoglalni az egyes Face szolgáltatás tárolók.

| Tároló | Minimális | Ajánlott | Tranzakciók másodpercenként<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|Arcfelismerés | 1 mag, 2 GB memória | 1 mag, 4 GB memória |10, 20|

* Minden magnak legalább 2,6 GHz-nek vagy gyorsabbnak kell lennie.
* Tranzakciók másodpercenként (TPS).

A mag és `--cpus` a `--memory` memória megfelel nek a `docker run` és a beállításoknak, amelyek a parancs részeként használatosak.

## <a name="get-the-container-image-with-docker-pull"></a>A tárolórendszerkép bekérése docker-lekéréses sel

A Face szolgáltatás tárolórendszerképei elérhetők. 

| Tároló | Adattár |
|-----------|------------|
| Arcfelismerés | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Docker-lekérése a Face tárolóhoz

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>A tároló használata

Miután a tároló a [gazdaszámítógépen](#the-host-computer)van, a következő eljárással dolgozzon a tárolóval.

1. [Futtassa a tárolót](#run-the-container-with-docker-run) a szükséges számlázási beállításokkal. További [példák](./face-resource-container-config.md#example-docker-run-commands) a `docker run` parancs állnak rendelkezésre. 
1. [Kérdezze meg a tároló előrejelzési végpontját.](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Futtassa a tárolót docker-futtatással

Használja a [docker run](https://docs.docker.com/engine/reference/commandline/run/) parancsot a tároló futtatásához. Tekintse meg [a szükséges paraméterek összegyűjtését](#gathering-required-parameters) `{API_KEY}` a be- és értékek `{ENDPOINT_URI}` begyűjtésének részleteiről.

[Példák](face-resource-container-config.md#example-docker-run-commands) `docker run` a parancs állnak rendelkezésre.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Egy arctárolót futtat a tárolólemezképből.
* Egy processzormag és 4 GB memória lefoglalása.
* Az 5000-es TCP-portot teszi elérhetővé, és egy pszeudo TTY-t rendel a tárolóhoz.
* A tároló automatikus eltávolítása kilépés után. A tárolórendszerkép továbbra is elérhető a gazdaszámítógépen. 

További [példák](./face-resource-container-config.md#example-docker-run-commands) a `docker run` parancs állnak rendelkezésre. 

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` és a beállításokat meg kell adni a tároló futtatásához, különben a tároló nem indul el. További információ: [Billing](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat biztosít. 

Használja az `http://localhost:5000`állomás, , tároló API-k.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót kimeneti [csatlakoztatással](./face-resource-container-config.md#mount-settings) futtatja, és a naplózás engedélyezve van, a tároló naplófájlokat hoz létre, amelyek hasznosak a tároló indításavagy futtatása során felmerülő problémák elhárításához.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

A Face szolgáltatástárolók számlázási adatokat küldenek az Azure-ba egy Face-erőforrás használatával az Azure-fiókjában. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezekről a beállításokról a [Tárolók konfigurálása](./face-resource-container-config.md)című témakörben talál további információt.

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összefoglalás

Ebben a cikkben ismertetheti a Face szolgáltatástárolók letöltésével, telepítésével és futtatásával kapcsolatos fogalmakat és munkafolyamatokat. Összegezve:

* A tárolórendszerképek az Azure Container Registry szolgáltatásból töltődnek le.
* A tárolórendszerképek a Dockerben futnak.
* A REST API vagy az SDK használatával a Face szolgáltatástárolókban műveleteket hívhat meg a tároló gazdaURI-jának megadásával.
* A tároló példányosításakor meg kell adnia a számlázási adatokat.

> [!IMPORTANT]
> A Cognitive Services-tárolók nem rendelkeznek licenccel anélkül, hogy az Azure-hoz csatlakoznának a méréshez. Az ügyfeleknek lehetővé kell tenniük, hogy a tárolók mindig kommunikálják a számlázási adatokat a mérési szolgáltatással. A Cognitive Services-tárolók nem küldenek ügyféladatokat, például az elemzett képet vagy szöveget a Microsoftnak.

## <a name="next-steps"></a>További lépések

* A konfigurációs beállításokról a [Tárolók konfigurálása](face-resource-container-config.md)című témakörben olvashat.
* Ha többet szeretne tudni az arcok észleléséről és azonosításáról, olvassa el az [Arc áttekintése című témakört.](Overview.md)
* A tároló által támogatott módszerekről a [Face API című témakörben](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)talál további információt.
* További Cognitive Services-tárolók használatához lásd: [Cognitive Services-tárolók.](../cognitive-services-container-support.md)
