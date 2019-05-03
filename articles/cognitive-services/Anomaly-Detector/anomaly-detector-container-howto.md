---
title: Telepítse és futtassa a tárolók – Anomáliadetektálási detector használatával
titleSuffix: Azure Cognitive Services
description: Az Anomáliadetektálási detector használatával API korszerű algoritmusokat segítségével azonosíthatja a rendellenességeket az idősoros adatokat.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/07/2019
ms.author: aahi
ms.openlocfilehash: 5dcec0d5f313b1c746c0674d0f9bf4d30ed19e5c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026312"
---
# <a name="install-and-run-anomaly-detector-containers"></a>Telepítse és futtassa a tárolók Anomáliadetektálási detector használatával

Az Anomáliadetektálási detector használatával a következő tárolórendszerkép rendelkezik: 

|Függvény|Szolgáltatások|
|-|-|
|Anomáliadetektálási detector használatával| <li> A valós idejű előforduló észleli a rendellenességeket. <li> Észleli a rendellenességeket a készlet egész kötegként. <li> Kikövetkezteti a várt normál tartományon az adatokat. <li> Támogatja anomáliadetektálási észlelés érzékenysége korrekciós jobban illeszkednek az adatok. |

Az API-k kapcsolatos részletes információkért tekintse meg:
* [További tudnivalók a Anomáliadetektálási detector használatával API-szolgáltatás](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Anomáliadetektálási detector használatával tárolók használata előtt a következő előfeltételeknek kell megfelelnie:

|Szükséges|Cél|
|--|--|
|Docker-motor| A Docker-motor telepítve van szüksége egy [gazdaszámítógép](#the-host-computer). A docker csomagokat biztosít, a Docker-környezet konfigurálása a [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), és [Linux](https://docs.docker.com/engine/installation/#supported-platforms). A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).<br><br> Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz. <br><br> **A Windows**, a Docker Linux-tárolók támogatása is kell konfigurálni.<br><br>|
|Docker-ismeretek | A Docker fő fogalmaira, például a beállításjegyzékek, adattárak, tárolók, és tárolórendszerképeket, valamint alapszintű ismerete alapvető ismeretekkel kell `docker` parancsokat.| 
|Anomáliadetektálási detector használatával erőforrás |Ezek a tárolók használatához rendelkeznie kell:<br><br>Egy _Anomáliadetektálási detector használatával_ Azure-erőforráshoz tartozó számlázási kulcs és számlázási végpont URI azonosítója. Mindkét értéket az Azure Portalon Anomáliadetektálási detector használatával áttekintése és a kulcsok oldalon érhető el, és a szükséges, hogy a tárolót.<br><br>**{BILLING_KEY}** : erőforrás-kulcs<br><br>**{BILLING_ENDPOINT_URI}** : végpont URI-példa: `https://westus2.api.cognitive.microsoft.com`|

## <a name="request-access-to-the-container-registry"></a>A tároló-beállításjegyzék hozzáférés kérése

Először végezze el, és küldje el a [Anomáliadetektálási detector használatával tároló kérésűrlapra](https://aka.ms/adcontainer) hozzáférés kéréséhez a tárolóhoz.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>A számítógép

A **gazdagép** van a számítógépen, amelyen a docker-tárolót. A helyszíni vagy a docker-üzemeltetési szolgáltatás az Azure például egy számítógép lehet:

* [Azure Kubernetes Service](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)
* [Azure Container Instances](https://docs.microsoft.com/container-instances/index.yml)
* [Kubernetes](https://kubernetes.io/) fürtben telepített [Azure Stack](https://docs.microsoft.com/azure-stack/index.yml). További információkért lásd: [Kubernetes üzembe helyezése az Azure Stackhez](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md).
<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

A következő táblázat ismerteti a minimális és ajánlott processzormagot és memóriát lefoglalni az Anomáliadetektálási detector használatával tároló.

| QPS (a másodpercenkénti lekérdezések) | Minimális | Ajánlott |
|-----------|---------|-------------|
| 10 QPS | 4 mag, 1GB memória | 8 mag 2GB memória |
| 20 QPS | 8 mag, 2GB memória | 16 mag, 4GB memória |

Egyes maghoz kell lennie legalább 2.6-os gigahertz (GHz) vagy gyorsabb.

Core és a memória felel meg a `--cpus` és `--memory` beállítások, amelyek részeként használhatók a `docker run` parancsot.

## <a name="get-the-container-image-with-docker-pull"></a>A tárolórendszerkép beolvasása `docker pull`

Használja a [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal letöltheti egy tárolórendszerképet.

| Tároló | Adattár |
|-----------|------------|
| cognitive-services-anomaly-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-anomaly-detector-container"></a>Docker pull tároló Anomáliadetektálási detector használatával

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>A tároló használata

Ha a tároló a [gazdaszámítógép](#the-host-computer), a következő eljárás használható a tárolóval.

1. [A tároló futtatásához](#run-the-container-with-docker-run), a szükséges a számlázás a beállításokat. További [példák](anomaly-detector-container-configuration.md#example-docker-run-commands) , a `docker run` parancs érhetők el. 
1. [A tároló előrejelzési végpontja lekérdezése](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>A tároló futtatásához `docker run`

Használja a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) parancs futtatása bármely három tárolóra. A parancs paraméterei a következők:

| Helyőrző | Érték |
|-------------|-------|
|{BILLING_KEY} | Ezt a kulcsot szolgál a tárolót, és az Azure Portalon Anomáliadetektálási detector használatával kulcsok lapján található.  |
|{BILLING_ENDPOINT_URI} | A számlázási végpont URI azonosítóját az Azure Portalon Anomáliadetektálási detector használatával – Áttekintés lapon érhető el.|

Cserélje le ezeket a paramétereket a következő példában a saját értékeire `docker run` parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Ezzel a paranccsal:

* A tároló rendszerképét az Anomáliadetektálási detector használatával tároló fut
* Több processzormaggal és 4 gigabájt (GB) memóriát foglal le
* Elérhetővé teszi az 5000-es TCP-porton és a egy pszeudo-TTY lefoglalja a tároló
* Után kilép, automatikusan eltávolítja a tárolót. A tároló rendszerképét az továbbra is elérhető az állomáson. 

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Több tároló futtatása ugyanazon a gazdagépen

Ha a futtatni kívánt több tároló elérhetővé tett porttal, győződjön meg arról, egy másik portot a tárolók futtatásához. Például futtassa az első tároló 5000-es és a második tároló-as porton 5001.

Cserélje le a `<container-registry>` és `<container-name>` az értékeket a tárolók használata. Ezeket nem kell ugyanazt a tárolót kell. Az Anomáliadetektálási detector használatával tároló és a LUIS-tároló a gazdagépen futó együtt használhat, vagy beállíthatja, hogy a futtató több Anomáliadetektálási detector használatával tárolókat. 

Futtassa az első tároló 5000-es porton. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Futtassa a második tárolót 5001 porton.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Minden ezt követő tároló lehet egy másik porton. 

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontja lekérdezése

A tároló REST-alapú lekérdezési előrejelzési végpontot API-kat biztosít. 

Használja a gazdagép https://localhost:5000, API-k tároló.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Állítsa le a tároló

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

A kimenet futtatásakor a tároló [csatlakoztatási](anomaly-detector-container-configuration.md#mount-settings) és naplózás engedélyezve van, a tárolót hoz létre a naplófájlokat, amelyek hasznosak a hibaelhárítás indítása, vagy a tároló futtatása közben történik. 

## <a name="billing"></a>Számlázás

Anomáliadetektálási detector használatával tárolók Küldés a számlázási adatokat az Azure-ba, a használatával egy _Anomáliadetektálási detector használatával_ erőforrást az Azure-fiókjával. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](anomaly-detector-container-configuration.md).

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és a munkafolyamat letöltése, telepítése és a futó tárolók Anomáliadetektálási detector használatával. Összegezve:

* Anomáliadetektálási detector használatával biztosít egy Linux-tárolót a Docker, anomáliadetektálás beágyazása a várt tartományon következtetésekhez, és az érzékenységi hangolása folyamatos átviteli batch vs.
* Tárolórendszerképek letöltődnek egy privát Azure Container Registry dedikált tárolók előzetes verzióra.
* Tárolórendszerképek futtatása a Docker.
* Használhatja a REST API vagy SDK-val Anomáliadetektálási detector használatával tárolókban műveletek hívására adja meg a gazdagép a tároló URI-t.
* Számlázási adatokat adjon meg egy tároló hárítható el.

> [!IMPORTANT]
> Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók nem ügyfél (pl. az idő sorozat az elemezni kívánt) adatokat küldeni a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [tárolók konfigurálása](anomaly-detector-container-configuration.md) a konfigurációs beállítások
* [További tudnivalók a Anomáliadetektálási detector használatával API-szolgáltatás](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
