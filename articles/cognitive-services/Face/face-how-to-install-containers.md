---
title: Tárolók telepítése és futtatása
titlesuffix: Face - Azure Cognitive Services
description: Töltse le, telepítse, és futtathat tárolókat az Arcfelismerés az bemutató oktatóanyag.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: beb9818be05fbb9a9e9c958dccb2e375f7685bd0
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272803"
---
# <a name="install-and-run-face-containers"></a>Telepítse és futtassa a Face tárolók

Az Azure Cognitive Services Face szabványos Linux-tárolót biztosít a Docker, amely észleli az emberi arcok a képeken. Attribútumok, többek között például noses és szem, nemét, életkor és egyéb gép – előre meghatározott arcfelismerés arcrészek is azonosítja. Mellett észlelése a Face ellenőrizheti, ha a két arc ugyanazt a lemezképet vagy különböző képek megegyeznek egy megbízhatósági pontszám használatával. Arcfelismerés is összehasonlíthatja arcok, ha egy hasonló megjelenésű vagy azonos arc már létezik-e olyan adatbázison. Azt is is hasonló arcok csoportokba rendezheti közös visual tulajdonságok használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A Face API-tárolók használata előtt meg kell felelnie a következő előfeltételek vonatkoznak.

|Kötelező|Cél|
|--|--|
|Docker-motor| A Docker-motor telepítve kell lennie egy [gazdaszámítógép](#the-host-computer). A docker csomagokat biztosít, a Docker-környezet konfigurálása a [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), és [Linux](https://docs.docker.com/engine/installation/#supported-platforms). A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).<br><br> Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz. <br><br> A Windows a Docker kell úgy is, hogy Linux-tárolók támogatják.<br><br>|
|Docker-ismeretek | Szüksége lesz a Docker fő fogalmaira, például a beállításjegyzékek, az adattárak, a tárolók és a tárolórendszerképek alapvető ismeretekkel. Alapszintű ismerete is kell `docker` parancsokat.| 
|Azure `Cognitive Services` erőforrás |A tárolót használja kell rendelkeznie:<br><br>Az Azure Cognitive Services-erőforrás és a hozzá tartozó számlázási kulcs és a számlázási végpont URI azonosítója. Mindkét értéket érhetők el a **áttekintése** és **kulcsok** oldalak az erőforrás. Akkor kell a tárolót. Adja hozzá a `face/v1.0` a végpont URI-t, az Útválasztás a következő BILLING_ENDPOINT_URI példában látható módon: <br><br>**{BILLING_KEY}** : erőforrás-kulcs<br><br>**{BILLING_ENDPOINT_URI}** : végpont URI-példa `https://westus.api.cognitive.microsoft.com/face/v1.0`|

## <a name="request-access-to-the-private-container-registry"></a>A privát tárolóregisztrációs hozzáférés kérése

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>A számítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

A következő táblázat ismerteti a minimális és ajánlott processzormagot és memóriát lefoglalni az egyes Face API-tárolók.

| Tároló | Minimális | Ajánlott | Másodpercenkénti tranzakciók<br>(Minimális, maximális)|
|-----------|---------|-------------|--|
|Arcfelismerés | 1 mag, 2 GB memória | 1 mag, 4 GB memória |10, 20|

* Egyes maghoz kell lennie legalább 2,6 GHz-es vagy gyorsabb.
* Másodpercenkénti tranzakciók (TPS).

Core és a memória felel meg a `--cpus` és `--memory` beállítások, amelyek részeként használhatók a `docker run` parancsot.

## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképét a docker pull beolvasása

A Face API tárolórendszerképeket érhetők el. 

| Tároló | Tárház |
|-----------|------------|
| Arcfelismerés | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>A Face tároló docker pull

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>A tároló használata

Miután a tároló-e a a [gazdaszámítógép](#the-host-computer), a következő eljárás használható a tárolóval.

1. [A tároló futtatásához](#run-the-container-with-docker-run) a szükséges a számlázás a beállításokat. További [példák](./face-resource-container-config.md#example-docker-run-commands) , a `docker run` parancs érhetők el. 
1. [A tároló előrejelzési végpontja lekérdezése](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>A docker használatával futtassa a tároló futtatásához

Használja a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) parancs futtatása bármely három tárolóra. A parancs a következő paramétereket használja.

| Helyőrző | Érték |
|-------------|-------|
|{BILLING_KEY} | Ezt a kulcsot a tárolót szolgál, és az Azure-ban elérhető `Cognitive Services` **kulcsok** lapot. |
|{BILLING_ENDPOINT_URI} | A számlázási végpont URI érték érhető el az Azure-beli `Cognitive Services` **áttekintése** lapot. Például: `https://westus.api.cognitive.microsoft.com/face/v1.0`.|

Adja hozzá a `face/v1.0` a végpont URI-t, az Útválasztás az előző BILLING_ENDPOINT_URI példában látható módon. 

Cserélje le ezeket a paramétereket a saját értékeit a következő `docker run` parancs:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Ezzel a paranccsal:

* Face tárolóban futtatja a tároló rendszerképét.
* Több processzormaggal és 4 GB memóriát foglal le.
* Elérhetővé teszi az 5000-es TCP-porton, és a egy ál TTY lefoglalja a tároló.
* Után kilép, automatikusan eltávolítja a tárolót. A tároló rendszerképét az továbbra is elérhető az állomáson. 

További [példák](./face-resource-container-config.md#example-docker-run-commands) , a `docker run` parancs érhetők el. 

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához, vagy a tároló nem indul el. További információkért lásd: [számlázási](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontja lekérdezése

A tároló REST-alapú lekérdezési előrejelzési végpontot API-kat biztosít. 

Használja a gazdagép `https://localhost:5000`, API-k tároló.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Állítsa le a tároló

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

A kimenet futtatásakor a tároló [csatlakoztatási](./face-resource-container-config.md#mount-settings) és naplózás engedélyezve van, a tárolót hoz létre a naplófájlokat, amelyek hasznosak során elindításához, vagy futtassa a tároló kapcsolatos problémák elhárítása. 


## <a name="billing"></a>Számlázás

A Face API tárolók számlázási információk küldése az Azure-bA a Face API-erőforrást az Azure-fiók használatával. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta, fogalmak és letöltése, telepítése és a Face API-tárolókat futtathat munkafolyamatokat. Összegezve:

* A Face API három Linux-tárolókat biztosít a Docker, kulcsszókeresés, nyelvfelismerés és hangulatelemzés biztosító.
* Tárolórendszerképek az Azure Container Registry letöltődnek.
* Tárolórendszerképek futtatása a Docker.
* A REST API vagy az SDK-t is használható, ha a Face API tárolókban műveletek hívására adja meg a gazdagép a tároló URI-t.
* Ha egy tároló elindításához példányosítania meg kell adnia a számlázási adatokat.

> [!IMPORTANT]
> Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Ügyfelek engedélyeznie kell a tárolókat való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók adatai, például a lemezkép vagy a szöveg, amely elemezni, ne küldjön a Microsoftnak.

## <a name="next-steps"></a>További lépések

* A konfigurációs beállításokat, lásd: [tárolók konfigurálása](face-resource-container-config.md).
* Észlelheti és arcazonosítás kapcsolatos további tudnivalókért lásd: [Face áttekintése](Overview.md).
* Az a tároló által támogatott módszerekkel kapcsolatos további információkért lásd: a [Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* További Cognitive Services-tárolók használatához lásd: [Cognitive Services-tárolók](../cognitive-services-container-support.md).
