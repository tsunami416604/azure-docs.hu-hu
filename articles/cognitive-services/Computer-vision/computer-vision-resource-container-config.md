---
title: Tárolók konfigurálása - Computer Vision
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan konfigurálható a számítógép-látásban a Szövegfelismerés tárolók szükséges és választható beállításai.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 3be302019c712c13bd29d7ed3781151a1648e847
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879309"
---
# <a name="configure-computer-vision-docker-containers"></a>A Computer Vision Docker-tárolók konfigurálása

A Computer Vision tároló futásidejű környezetét `docker run` a parancsargumentumok használatával konfigurálhatja. Ez a tároló számos szükséges beállítással rendelkezik, valamint néhány választható beállítással. A parancsra több [példa](#example-docker-run-commands) is elérhető. A tároló-specifikus beállítások a számlázási beállításokat. 

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) és a beállítások együtt vannak használva, és mindháromhoz érvényes értékeket kell megadnia; ellenkező esetben a tartály nem indul el. A tároló konstiniformatika érdekében ezeknek a konfigurációs beállításoknak a használatával kapcsolatos további [tudnivalókért olvassa el a Számlázás című témakört.](computer-vision-how-to-install-containers.md)

## <a name="apikey-configuration-setting"></a>ApiKey konfigurációs beállítása

A `ApiKey` beállítás megadja `Cognitive Services` az Azure erőforráskulcs a tároló számlázási adatainak nyomon követéséhez. Meg kell adnia egy értéket az ApiKey és az érték érvényes kulcsnak [`Billing`](#billing-configuration-setting) kell lennie a konfigurációs beállításhoz megadott Cognitive Services-erőforráshoz. _Cognitive Services_

Ez a beállítás a következő helyen található:

* Azure portal: **Cognitive Services** Erőforrás-kezelés, **a Kulcsok** csoportban

## <a name="applicationinsights-setting"></a>ApplicationInsights beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfiguráció beállítása

A `Billing` beállítás adja meg a végpont URI a _Cognitive Services-erőforrás_ az Azure-ban a tároló számlázási adatainak mérésére használt. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végpontURI-nak kell lennie az _Azure-beli Cognitive_ Services-erőforráshoz. A tároló 10–15 percenként jelenti a használatot.

Ez a beállítás a következő helyen található:

* Azure portal: **Kognitív szolgáltatások** áttekintése, címkével`Endpoint`

Ne felejtse `vision/v1.0` el hozzáadni az útválasztást a végpont URI-jához az alábbi táblázatban látható módon. 

|Kötelező| Név | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Sztring | Számlázási végpont URI-ja<br><br>Példa:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Eula beállítás

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Gördülékeny beállítások

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxy hitelesítő adatainak beállításai

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Csatlakoztatási beállítások

A kötéscsatlakoztatások segítségével adatokat olvashat és írhat a tárolóba és a tárolóból. Megadhatja a bemeneti csatlakoztatás vagy `--mount` kimeneti csatlakoztatás a beállítás megadásával a [docker futtatási](https://docs.docker.com/engine/reference/commandline/run/) parancs.

A Computer Vision tárolók nem használnak bemeneti vagy kimeneti csatlakoztatásokat a betanítási vagy szolgáltatási adatok tárolására. 

A gazdagép csatlakoztatási helyének pontos szintaxisa a gazdaoperációs rendszertől függően változik. Emellett előfordulhat, hogy a [gazdaszámítógép](computer-vision-how-to-install-containers.md#the-host-computer)csatlakoztatási helye nem érhető el a Docker-szolgáltatásfiók által használt engedélyek és a gazdagép csatlakoztatási helyének engedélyei közötti ütközés miatt. 

|Optional| Név | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | Sztring | A Computer Vision tárolók nem használják ezt.|
|Optional| `Output` | Sztring | A kimeneti csatlakoztatás célja. Az alapértelmezett érték `/output`. Ez a rönkök helye. Ez magában foglalja a tárolónaplókat is. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa a docker-futtatási parancsokra

Az alábbi példák a konfigurációs beállításoksegítségével `docker run` szemléltetik a parancsok írását és használatát.  Futás után a tároló addig fut, amíg le nem [állítja.](computer-vision-how-to-install-containers.md#stop-the-container)

* **Sor-folytatás karakter**: A Docker-parancsok a következő `\`szakaszokban a fordított perjelet használják, vonalfolytatási karakterként. Cserélje le vagy távolítsa el ezt a gazdaoperációs rendszer követelményei nek megfelelően. 
* **Argumentum sorrendje**: Ne módosítsa az argumentumok sorrendjét, hacsak nem nagyon ismeri a Docker-tárolókat.

Cserélje le {_argument_name_} -t a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
| **{API_KEY}** | Az erőforrás végpontkulcsa az `Computer Vision` `Computer Vision` Azure Keys lapon. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | A számlázási végpont értéke elérhető `Computer Vision` az Azure Áttekintés oldalon.| Lásd: [a szükséges paraméterek összegyűjtése](computer-vision-how-to-install-containers.md#gathering-required-parameters) explicit példákat. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` a lehetőséget és a beállításokat meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információ: [Billing](computer-vision-how-to-install-containers.md#billing).
> Az ApiKey érték **Key** a kulcs `Cognitive Services` az Azure Resource keys lapról.

## <a name="container-docker-examples"></a>A Tároló Docker-példái

Az alábbi Docker-példák a read tároló.

### <a name="basic-example"></a>Alapszintű példa

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Példa naplózásra 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>További lépések

* Tekintse át [a tárolók telepítésének és futtatásának áttekintését.](computer-vision-how-to-install-containers.md)
