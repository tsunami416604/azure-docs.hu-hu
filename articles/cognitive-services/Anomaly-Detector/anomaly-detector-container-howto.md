---
title: Tárolók telepítése és futtatása anomália-detektor API-k használatával
titleSuffix: Azure Cognitive Services
description: A rendellenesség-Kiderítő API speciális algoritmusait használva azonosíthatja az idősorozat-adataiban mutatkozó rendellenességeket.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 45abd904ea95cf8e68583ba5630a485af59479ec
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327257"
---
# <a name="install-and-run-anomaly-detector-containers-preview"></a>Rendellenesség-Kiderítő tárolók telepítése és futtatása (előzetes verzió)

Az anomália detektor a következő tároló-funkciókkal rendelkezik:

| Függvény | Szolgáltatások |
|--|--|
| Anomália detektor | <li> Észleli a rendellenességeket, mivel azok valós időben történnek. <li> Az adatkészletben lévő összes adathalmazt a kötegben észleli. <li> Kikövetkezteti az adatai várható normál tartományát. <li> A anomáliák észlelésének érzékenységi beállítását támogatja, hogy jobban illeszkedjen az adataihoz. |

Az API-kkal kapcsolatos részletes információkért lásd:
* [További információ az anomália-érzékelő API szolgáltatásáról](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételeknek kell megfelelnie az anomália-Kiderítő tárolók használata előtt:

|Kötelező|Cél|
|--|--|
|Docker-motor| A Docker-motornak telepítve kell lennie a [gazdagépen](#the-host-computer). A Docker csomagokat biztosít a Docker-környezet konfigurálásához [MacOS](https://docs.docker.com/docker-for-mac/), Windows és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) [rendszereken](https://docs.docker.com/docker-for-windows/). A Docker és a Container alapjairól a [Docker áttekintésében](https://docs.docker.com/engine/docker-overview/)talál további információt.<br><br> Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz. <br><br> **Windows rendszeren a**Docker-t is konfigurálni kell a Linux-tárolók támogatásához.<br><br>|
|A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a kibocsátásiegység-forgalmi jegyzékekről, a adattárakról, a tárolók és a tárolók rendszerképeiről, valamint az alapszintű `docker` parancsok megismeréséről.| 
|Rendellenesség-Kiderítő erőforrás |A tárolók használatához a következőket kell tennie:<br><br>Egy Azure _anomália-detektor_ erőforrás a társított API-kulcs és végpont URI-azonosító lekéréséhez. Mindkét érték elérhető a Azure Portal **anomália-érzékelő** áttekintése és kulcsok oldalain, és a tároló indításához szükséges.<br><br>**{API_KEY}** : a **kulcsok** oldalon található két elérhető erőforrás-kulcs egyike<br><br>**{ENDPOINT_URI}** : az **Áttekintés** lapon megadott végpont|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>Hozzáférés kérése a tároló beállításjegyzékéhez

Először be kell fejeznie és el kell küldenie az [anomália-detektor tárolójának kérelmi űrlapját](https://aka.ms/adcontainer) , hogy hozzáférést Kérjen a tárolóhoz.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

A következő táblázat az anomáliák kiosztásához szükséges minimális és ajánlott CPU-magokat és memóriát ismerteti.

| QPS (lekérdezések másodpercenként) | Minimális | Ajánlott |
|-----------|---------|-------------|
| 10 QPS | 4 mag, 1 GB memória | 8 magos, 2 GB-os memória |
| 20 QPS | 8 mag, 2 GB memória | 16 magos, 4 GB-os memória |

Minden mag legalább 2,6 gigahertz (GHz) vagy gyorsabb lehet.

Az alap és a memória a `docker run` parancs részeként használt `--cpus` és `--memory` beállításoknak felel meg.

## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének beolvasása a `docker pull`

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) parancs használatával letöltheti a tárolók képét.

| Tároló | Tárház |
|-----------|------------|
| cognitive-services-anomaly-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Docker-lekérés az anomália detektor tárolóhoz

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a [gazdagépen](#the-host-computer)található, a következő eljárással dolgozhat a tárolóval.

1. [Futtassa a tárolót](#run-the-container-with-docker-run)a kötelező számlázási beállításokkal. További [példák](anomaly-detector-container-configuration.md#example-docker-run-commands) a `docker run` parancsra.
1. [A tároló előrejelzési végpontjának lekérdezése](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>A tároló futtatása `docker run`

A tároló futtatásához használja a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsot. A `{ENDPOINT_URI}` és `{API_KEY}` értékek beszerzésével kapcsolatos részletekért tekintse meg a [szükséges paraméterek összegyűjtését](#gathering-required-parameters) ismertető témakört.

A `docker run` parancs [példái](anomaly-detector-container-configuration.md#example-docker-run-commands) elérhetők.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Ez a parancs:

* Anomália-detektor tárolót futtat a tároló rendszerképből
* Egy CPU mag és 4 gigabájt (GB) memóriát foglal le
* Elérhetővé teszi az 5000-es TCP-porton és a egy pszeudo-TTY lefoglalja a tároló
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen. 

> [!IMPORTANT]
> A tároló futtatásához meg kell adni a `Eula`, `Billing`és `ApiKey` beállításokat. Ellenkező esetben a tároló nem indul el.  További információ: [számlázás](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Több tároló futtatása ugyanazon a gazdagépen

Ha több tárolót szeretne futtatni a kihelyezett portokkal, ügyeljen arra, hogy az egyes tárolókat egy másik porton futtassa. Futtassa például az első tárolót az 5000-as porton és a második tárolót a 5001-es porton.

Cserélje le a `<container-registry>` és a `<container-name>`t a használt tárolók értékeire. Ezeknek nem kell megegyezniük a tárolóval. Az anomália detektor tárolója és a GAZDAGÉPen futó LUIS-tároló együtt is lehet, de több anomália-detektor tároló is fut. 

Futtassa az első tárolót a 5000-es porton. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Futtassa a második tárolót az 5001-as porton.


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

A tároló API-khoz használja a gazdagépet (http://localhost:5000).

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót egy kimeneti [csatlakoztatással](anomaly-detector-container-configuration.md#mount-settings) futtatja, és a naplózás engedélyezve van, a tároló olyan naplófájlokat hoz létre, amelyek hasznosak a tároló indításakor vagy futtatásakor felmerülő problémák elhárításához.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

Az anomáliák Kiderítő tárolói számlázási adatokat küldenek az Azure-nak az Azure-fiókjában található _anomália-Kiderítő_ erőforrás használatával. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

További információ ezekről a beállításokról: [tárolók konfigurálása](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összefoglalás

Ebből a cikkből megtudhatta, milyen fogalmakat és munkafolyamatokat használ az anomáliák-detektor tárolóinak letöltéséhez, telepítéséhez és futtatásához. Összegezve:

* Az anomália detektor egy Linux-tárolót biztosít a Docker számára, valamint a Batch vs streaming, a várt Range és az érzékenység finomhangolását.
* A rendszer a tárolók lemezképeit a tárolók előzetes verziójára kijelölt privát Azure Container Registry tölti le.
* Tárolórendszerképek futtatása a Docker.
* Használhatja a REST API vagy az SDK-t, hogy a tároló gazdagép URI azonosítójának megadásával hívja meg a műveleteket a rendellenesség-Kiderítő tárolókban.
* Számlázási adatokat adjon meg egy tároló hárítható el.

> [!IMPORTANT]
> Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services tárolók nem küldenek ügyféladatokat (pl. az elemzett idősoros adatgyűjtési időt) a Microsoftnak.

## <a name="next-steps"></a>Következő lépések

* A [tárolók konfigurálásának](anomaly-detector-container-configuration.md) áttekintése konfigurációs beállításokhoz
* [Rendellenesség-Kiderítő tároló üzembe helyezése Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [További információ az anomália-érzékelő API szolgáltatásáról](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
