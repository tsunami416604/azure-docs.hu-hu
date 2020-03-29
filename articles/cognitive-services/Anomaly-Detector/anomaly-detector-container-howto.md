---
title: Az Anomaly Detector API-hoz használt tárolók telepítése és futtatása
titleSuffix: Azure Cognitive Services
description: Az Anomáliadetektor API fejlett algoritmusai segítségével azonosíthatja az idősorozat-adatok anomáliáit.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 45abd904ea95cf8e68583ba5630a485af59479ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220346"
---
# <a name="install-and-run-anomaly-detector-containers-preview"></a>Anomáliadetektor-tárolók telepítése és futtatása (előzetes verzió)

Az anomáliadetektor a következő tárolófunkcióval rendelkezik:

| Függvény | Szolgáltatások |
|--|--|
| Anomáliadetektor | <li> Észleli az anomáliákat, amint azok valós időben fordulnak elő. <li> Észleli az anomáliákat az adatkészletben kötegként. <li> Az adatok várható normál tartományára következtet. <li> Támogatja az anomáliadetektálásérzékenység-korrekciót, hogy jobban illeszkedjen az adatokhoz. |

Az API-król az:
* [További információ az Anomáliadetektor API-szolgáltatásról](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Anomáliadetektor-tárolók használata előtt meg kell felelnie az alábbi előfeltételeknek:

|Kötelező|Cél|
|--|--|
|Docker-motor| A Docker-motort egy [gazdaszámítógépen](#the-host-computer)kell telepíteni. A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszereken. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).<br><br> A Docker-t úgy kell konfigurálni, hogy a tárolók csatlakozhassanak az Azure-hoz, és számlázási adatokat küldjenek az Azure-ba. <br><br> **Windows**rendszeren a Docker-t is be kell állítani linuxos tárolók támogatására.<br><br>|
|A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a nyilvántartásokról, az adattárakról, a tárolókról és a tárolórendszerképekről, valamint az alapvető `docker` parancsok ismeretéről.| 
|Anomáliadetektor erőforrás |A tárolók használatához a következőkre van szüksége:<br><br>Egy Azure _Anomália-detektor_ erőforrás a társított API-kulcs és végpont URI bekéseléséhez. Mindkét érték elérhető az Azure Portal **anomáliaérzékelő** áttekintése és a kulcsok lapok, és a tároló elindításához szükséges.<br><br>**{API_KEY}**: A **Kulcsok** lapon elérhető két erőforráskulcs egyike<br><br>**{ENDPOINT_URI}**: Az **Áttekintés** lapon megadott végpont|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>Hozzáférés kérése a tároló beállításjegyzékéhez

Először ki kell töltenie, és el kell küldenie az [Anomália-detektor tárolókérelem űrlapot](https://aka.ms/adcontainer) a tárolóhoz való hozzáférés kéréséhez.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>A tárolóra vonatkozó követelmények és ajánlások

Az alábbi táblázat az Anomaly Detector tároló számára lefoglalandó minimális és ajánlott processzormagokat és memóriát ismerteti.

| QPS(Lekérdezések másodpercenként) | Minimális | Ajánlott |
|-----------|---------|-------------|
| 10 QPS | 4 mag, 1 GB memória | 8 magos 2 GB memória |
| 20 QPS | 8 mag, 2 GB memória | 16 magos 4 GB-os memória |

Minden magnak legalább 2,6 gigahertzesnek (GHz) vagy gyorsabbnak kell lennie.

A mag és `--cpus` a `--memory` memória megfelel nek a `docker run` és a beállításoknak, amelyek a parancs részeként használatosak.

## <a name="get-the-container-image-with-docker-pull"></a>A tárolókép beszerezése`docker pull`

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal töltsön le egy tárolólemezképet.

| Tároló | Adattár |
|-----------|------------|
| kognitív-szolgáltatások-anomália-detektor | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Docker-lekérése az Anomáliadetektor tárolóhoz

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a [gazdaszámítógépen](#the-host-computer)van, használja a következő eljárást a tárolóval való munkához.

1. [Futtassa a tárolót](#run-the-container-with-docker-run)a szükséges számlázási beállításokkal. További [példák](anomaly-detector-container-configuration.md#example-docker-run-commands) a `docker run` parancs állnak rendelkezésre.
1. [Kérdezze meg a tároló előrejelzési végpontját.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Futtassa a tartályt`docker run`

Használja a [docker run](https://docs.docker.com/engine/reference/commandline/run/) parancsot a tároló futtatásához. Tekintse meg [a szükséges paraméterek összegyűjtését](#gathering-required-parameters) `{API_KEY}` a be- és értékek `{ENDPOINT_URI}` begyűjtésének részleteiről.

[Példák](anomaly-detector-container-configuration.md#example-docker-run-commands) `docker run` a parancs állnak rendelkezésre.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Anomáliadetektor-tároló futtatása a tárolórendszerképből
* Egy PROCESSZORmag és 4 gigabájt (GB) memória lefoglalása
* Kiteszi az 5000-es TCP-portot, és pszeudo-TTY-t rendel a tárolóhoz
* A tároló automatikus eltávolítása kilépés után. A tárolórendszerkép továbbra is elérhető a gazdaszámítógépen. 

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` a lehetőséget és a beállításokat meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információ: [Billing](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Több tároló futtatása ugyanazon a gazdagépen

Ha több tárolót kíván futtatni a kitett portokkal, győződjön meg arról, hogy minden tárolót más porttal futtat. Futtassa például az első tárolót az 5000-es porton, a másodikat pedig az 5001-es porton.

Cserélje `<container-registry>` ki `<container-name>` a és a használt tárolók értékeit. Ezeknek nem kell egyforma tárolónak lenniük. Az Anomáliadetektor-tároló és a LUIS-tároló együtt fut, vagy több anomáliadetektor-tároló is futtatható. 

Futtassa az első tárolót az 5000-es porton. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Futtassa a második tárolót az 5001-es porton.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Minden további tárolónak egy másik porton kell lennie. 

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat biztosít. 

Használja az http://localhost:5000állomás, , tároló API-k.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót kimeneti [csatlakoztatással](anomaly-detector-container-configuration.md#mount-settings) és naplózással futtatja, a tároló naplófájlokat hoz létre, amelyek hasznosak a tároló indításakor vagy futtatásakor felmerülő problémák elhárításához.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

Az Anomáliadetektor-tárolók számlázási adatokat küldenek az Azure-ba egy _Anomália-detektor_ erőforrás használatával az Azure-fiókjában. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezekről a beállításokról a [Tárolók konfigurálása](anomaly-detector-container-configuration.md)című témakörben talál további információt.

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta az Anomáliadetektor-tárolók letöltésére, telepítésére és futtatására vonatkozó fogalmakat és munkafolyamatokat. Összegezve:

* Anomáliadetektor egy Linux-tárolót biztosít a Docker számára, amely az anomáliadetektálást kötegelt és folyamatos átvitellel, a várt tartománykövetkeztetéssel és az érzékenység hangolásával foglalja magában.
* A tárolórendszerképek egy privát Azure Container Registry tárolók előzetes verziójára dedikált.
* A tárolórendszerképek a Dockerben futnak.
* A REST API vagy az SDK használatával az Anomaly Detector tárolókban műveleteket hívhat meg a tároló gazdaURI-jának megadásával.
* A tároló példányosításakor meg kell adnia a számlázási adatokat.

> [!IMPORTANT]
> A Cognitive Services-tárolók nem rendelkeznek licenccel anélkül, hogy az Azure-hoz csatlakoznának a méréshez. Az ügyfeleknek lehetővé kell tenni, hogy a tárolók mindig kommunikálják a számlázási adatokat a mérési szolgáltatással. A Cognitive Services-tárolók nem küldenek ügyféladatokat (például az elemzett idősorozat-adatokat) a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Tekintse át [a tárolók konfigurálása](anomaly-detector-container-configuration.md) a konfigurációs beállításokat
* [Anomália-érzékelő tároló üzembe helyezése az Azure Container-példányokba](how-to/deploy-anomaly-detection-on-container-instances.md)
* [További információ az Anomáliadetektor API-szolgáltatásról](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
