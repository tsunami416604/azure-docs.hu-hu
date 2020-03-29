---
title: Beszédtárolók konfigurálása
titleSuffix: Azure Cognitive Services
description: A beszédszolgáltatás minden tárolószámára egy közös konfigurációs keretrendszert biztosít, így könnyen konfigurálhatja és kezelheti a tárolók tárolási, naplózási és telemetriai adatait, valamint a tárolók biztonsági beállításait.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: dd5a531e4a979cba9c2a766c7774762a0427ad02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037329"
---
# <a name="configure-speech-service-containers"></a>Beszédfelismerési szolgáltatástárolók konfigurálása

A beszédtárolók lehetővé teszik az ügyfelek számára, hogy egy beszédfelismerési alkalmazásarchitektúrát építsenek, amely a robusztus felhőképességek és a peremhálózati helykihasználás előnyeinek kihasználására van optimalizálva. A négy beszédtároló, amelyet most támogatunk, a **szöveggé,** **az egyéni beszéd-szöveg,** **a szövegfelolvasás**és **az egyéni szövegfelolvasó**.

A **beszédtároló** futásidejű környezete `docker run` a parancsargumentumok használatával van konfigurálva. Ez a tároló számos szükséges beállítással rendelkezik, valamint néhány választható beállítással. A parancsra több [példa](#example-docker-run-commands) is elérhető. A tároló-specifikus beállítások a számlázási beállításokat.

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) és a beállítások együtt vannak használva, és mindháromhoz érvényes értékeket kell megadnia; ellenkező esetben a tartály nem indul el. A tároló konstiniformatika érdekében ezeknek a konfigurációs beállításoknak a használatával kapcsolatos további [tudnivalókért olvassa el a Számlázás című témakört.](speech-container-howto.md#billing)

## <a name="apikey-configuration-setting"></a>ApiKey konfigurációs beállítása

A `ApiKey` beállítás megadja az Azure erőforráskulcs a tároló számlázási adatainak nyomon követéséhez. Meg kell adnia egy értéket az ApiKey-hez, és az értéknek [`Billing`](#billing-configuration-setting) érvényes kulcsnak kell lennie a konfigurációs beállításhoz megadott _beszédfelismerési_ erőforráshoz.

Ez a beállítás a következő helyen található:

- Azure portal: **A beszéd** erőforrás-kezelése a **Kulcsok** csoportban

## <a name="applicationinsights-setting"></a>ApplicationInsights beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfiguráció beállítása

A `Billing` beállítás határozza meg a végpont URI-t a _beszédfelismerési_ erőforrás az Azure-ban a tároló számlázási adatainak mérésére használt. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végpontURI-nak kell lennie egy _beszédfelismerési_ erőforráshoz az Azure-ban. A tároló 10–15 percenként jelenti a használatot.

Ez a beállítás a következő helyen található:

- Azure Portal: **Beszéd áttekintése,** címkével`Endpoint`

| Kötelező | Név | Adattípus | Leírás |
| -------- | ---- | --------- | ----------- |
| Igen | `Billing` | Sztring | Számlázási végpont URI. A számlázási URI beszerzésével kapcsolatos további információkért [lásd: A szükséges paraméterek összegyűjtése.](speech-container-howto.md#gathering-required-parameters) További információt és a regionális végpontok teljes listáját a [Cognitive Services egyéni altartománynevei című témakörben talál.](../cognitive-services-custom-subdomains.md) |

## <a name="eula-setting"></a>Eula beállítás

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Gördülékeny beállítások

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxy hitelesítő adatainak beállításai

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Csatlakoztatási beállítások

A kötéscsatlakoztatások segítségével adatokat olvashat és írhat a tárolóba és a tárolóból. Megadhatja a bemeneti csatlakoztatás vagy `--mount` kimeneti csatlakoztatás a beállítás megadásával a [docker futtatási](https://docs.docker.com/engine/reference/commandline/run/) parancs.

A standard beszédtárolók nem használnak bemeneti vagy kimeneti csatlakoztatásokat a betanítási vagy szolgáltatásadatok tárolására. Az egyéni beszédtárolók azonban kötetcsatolókra támaszkodnak.

A gazdagép csatlakoztatási helyének pontos szintaxisa a gazdaoperációs rendszertől függően változik. Emellett előfordulhat, hogy a [gazdaszámítógép](speech-container-howto.md#the-host-computer)csatlakoztatási helye nem érhető el a docker-szolgáltatásfiók által használt engedélyek és a gazdagép csatlakoztatási helyének engedélyei közötti ütközés miatt.

| Optional | Név | Adattípus | Leírás |
| -------- | ---- | --------- | ----------- |
| Nem engedélyezett | `Input` | Sztring | A normál beszédtárolók nem használják ezt. Az egyéni beszédtárolók [kötetcsatlakoztatásokat](#volume-mount-settings)használnak.                                                                                    |
| Optional | `Output` | Sztring | A kimeneti csatlakoztatás célja. Az alapértelmezett érték `/output`. Ez a rönkök helye. Ez magában foglalja a tárolónaplókat is. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Kötetcsatlakoztatási beállítások

Az egyéni beszédtárolók [kötetcsatolók](https://docs.docker.com/storage/volumes/) használatával egyéni modellek megőrzése. A kötetcsatlakoztatás megadásához `-v` adja `--volume`hozzá a (vagy ) opciót a [docker futtatási](https://docs.docker.com/engine/reference/commandline/run/) parancshoz.

Egyéni modellek letöltődnek az első alkalommal, amikor egy új modell az egyéni beszédtároló docker futtatási parancs részeként kerül letöltésre. Az egyéni beszédtárolók azonos `ModelId` szekvenciális futtatásai a korábban letöltött modellt fogják használni. Ha a kötetcsatlakoztatás nincs megadva, az egyéni modellek nem maradhatnak meg.

A kötetcsatlakoztatás beállítása három `:` színtől elválasztott mezőből áll:

1. Az első mező a gazdagép kötetének neve, például _C:\input_.
2. A második mező a tárolókönyvtár, például _a /usr/local/models_.
3. A harmadik mező (nem kötelező) a lehetőségek vesszővel tagolt listája, további információért [lásd: Use volumes](https://docs.docker.com/storage/volumes/).

### <a name="volume-mount-example"></a>Példa kötetcsatlakoztatásra

```bash
-v C:\input:/usr/local/models
```

Ez a parancs a _C:\bemeneti_ könyvtárat a _/usr/local/models_ könyvtárhoz csatlakoztatja.

> [!IMPORTANT]
> A kötetcsatlakoztatási beállítások csak **az egyéni beszédfelismerési és** az egyéni **szövegfelolvasó** tárolókra vonatkoznak. A szabványos **beszéd-szöveg** és **a szövegfelolvasó** tárolók nem használnak kötettartókat.

## <a name="example-docker-run-commands"></a>Példa a docker-futtatási parancsokra

Az alábbi példák a konfigurációs beállításoksegítségével `docker run` szemléltetik a parancsok írását és használatát. Futás után a tároló addig fut, amíg le nem [állítja.](speech-container-howto.md#stop-the-container)

- **Sor-folytatás karakter**: A Docker-parancsok a következő `\`szakaszokban a fordított perjelet használják, vonalfolytatási karakterként. Cserélje le vagy távolítsa el ezt a gazdaoperációs rendszer követelményei nek megfelelően.
- **Argumentum sorrendje**: Ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.

Cserélje le {_argument_name_} -t a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | Az erőforrás végpontkulcsa az `Speech` `Speech` Azure Keys lapon.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | A számlázási végpont értéke elérhető `Speech` az Azure Áttekintés oldalon. | Lásd: [a szükséges paraméterek összegyűjtése](speech-container-howto.md#gathering-required-parameters) explicit példákat. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` a lehetőséget és a beállításokat meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el. További információ: [Billing](#billing-configuration-setting).
> Az ApiKey érték a **kulcs** az Azure beszédfelismerési erőforrás-kulcsok lapról.

## <a name="speech-container-docker-examples"></a>Példa a Docker-tárolóra

Az alábbi Docker-példák a beszédtároló.

## <a name="speech-to-text"></a>[Beszédfelismerés](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Alapvető példa a beszédfelismerési szöveghez

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Példa a beszédfelismerési szövegre

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-text"></a>[Egyéni beszédfelismerési szöveg](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Egyszerű példa az egyéni beszédfelismerési szövegképhez

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Példa egyéni beszédfelismerési szövegre

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="text-to-speech"></a>[Szövegfelolvasás](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Alapvető példa a szövegfelolvasásra

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Példa a szövegfelolvasás naplózására

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speech"></a>[Egyéni szövegfelolvasás](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Egyszerű példa az egyéni szövegfelolvasásra

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Példa egyéni szövegfelolvasásnaplózására

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>További lépések

- Tekintse át [a tárolók telepítésének és futtatásának áttekintését](speech-container-howto.md)
