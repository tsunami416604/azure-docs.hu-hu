---
title: Personalizer működése
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan futtathat a Personalizer tárolót a docker, futtassa a parancsot a konfigurációs beállításokat.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 85344d432c169ce8c4adffd059059660672df02e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158552"
---
# <a name="configure-personalizer-containers"></a>Personalizer tárolók konfigurálása

A **Personalizer** tároló-futtatókörnyezet használatával lett konfigurálva a `docker run` parancssori argumentumokat. Ez a tároló rendelkezik néhány választható beállítások mellett számos szükséges beállításokat. Több [példák](#example-docker-run-commands) a parancs érhetők el. A tároló-specifikus beállítások a számlázási beállításokat is. 

## <a name="configuration-settings"></a>Konfigurációs beállítások

Ez a tároló a következő konfigurációs beállításokkal rendelkezik:

|Szükséges|Beállítás|Cél|
|--|--|--|
|Igen|[ApiKey](#apikey-configuration-setting)|Számlázási adatok nyomon követésére szolgál.|
|Nem|[ApplicationInsights](#applicationinsights-setting)|Lehetővé teszi, hogy [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetriai támogatása a tárolóba.|
|Igen|[Billing](#billing-configuration-setting)|Az Azure-ban található szolgáltatás-erőforrás végponti URI-ját adja meg.|
|Igen|[Eula](#eula-setting)| Azt jelzi, hogy Ön már elfogadta a tároló licencét.|
|Nem|[Fluentd](#fluentd-settings)|Napló írási és opcionálisan metrikaadatok Fluentd kiszolgálóhoz.|
|Nem|[Http Proxy](#http-proxy-credentials-settings)|Egy HTTP-proxy konfigurálása, hogy a kimenő kérelmek.|
|Nem|[Logging](#logging-settings)|Biztosítja a naplózás a tároló támogatása ASP.NET Core. |
|Nem|[Fluentd](#mount-settings)|Napló- és opcionálisan metrikaadatok írása egy Fluentd-kiszolgálóra.|

> [!IMPORTANT]
> A [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), és [ `Eula` ](#eula-setting) beállítások együtt használja, és meg kell adnia az érvényes értékek mindhárom azokat; egyéb a tároló nem indul el. Egy tároló példányosítása a konfigurációs beállítások használatával kapcsolatos további információkért lásd: [számlázási](personalizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurációs beállítás apikey tulajdonsággal végzett tesztelése

A `ApiKey` beállítás határozza meg a számlázási adatokat tároló nyomon követésére használt Azure-erőforrás kulcs. Meg kell adnia egy értéket a apikey tulajdonsággal végzett tesztelése és az értéknek kell lennie egy érvényes kulcsot a _Personalizer_ megadott erőforrás a [ `Billing` ](#billing-configuration-setting) konfigurációs beállítás.

Ez a beállítás a következő helyen található:

* Az Azure Portalon: **A personalizer** erőforrás-kezelés alatt **kulcsok**

## <a name="applicationinsights-setting"></a>Applicationinsights – beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás határozza meg a végpont URI-t, a _Personalizer_ erőforrást az Azure-ban használt mérni a tároló számlázási adatokat. Meg kell adnia egy értéket a konfigurációs beállítás, és az értéknek kell lennie egy érvényes végpont URI-t a egy _Personalizer_ erőforrást az Azure-ban.

Ez a beállítás a következő helyen található:

* Az Azure Portalon: **A personalizer** áttekintése, címkével `Endpoint`

|Szükséges| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Karakterlánc | A számlázás végpont URI azonosítója<br><br>Példa:<br>`Billing=https://westus.api.cognitive.microsoft.com/sts/v1.0` |

## <a name="eula-setting"></a>Licencfeltételek beállítása

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd beállításai

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxybeállításai hitelesítő adatok

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Csatlakoztatási beállítások

Kötés használatát csatlakoztatja az adatok olvasását és írását, és a tárolóból. Megadhat egy bemeneti csatlakoztatási vagy csatlakoztatási kimeneti megadásával a `--mount` beállítást a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) parancsot.

A tárolók nem bemeneti vagy kimeneti csatlakoztatja képzési vagy szolgáltatás adatok tárolására. 

A gazdagép csatlakoztatási helye a pontos szintaxisa a gazdagép operációs rendszere függően változik. Ezenkívül a [gazdaszámítógép](personalizer-container-howto.md#the-host-computer)a csatlakoztatási helye nem lehet elérni a docker szolgáltatás fiókja által használt engedélyek közötti ütközés miatt, és a gazdagép csatlakoztatásához hely engedélyeket. 

|Optional| Name (Név) | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | String | Personalizer tárolók ez nem használható.|
|Optional| `Output` | String | A kimeneti csatlakoztatási célját. Az alapértelmezett érték `/output`. Ez az a hely a naplófájlok. Ez magában foglalja a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa docker-parancsok futtatása 

Az alábbi példák bemutatják, hogyan írhat, és használja a konfigurációs beállítások segítségével `docker run` parancsokat.  Ha fut, a tároló továbbra is fut, amíg ki nem [leállítása](personalizer-container-howto.md#stop-the-container) azt.

* **Vonal-folytatási karakter**: Az alábbi szakaszok a Docker-parancsokat használhatja a fordított perjel `\`, a bash-rendszerhéjból sor folytatási karakterként. Cserélje le, vagy távolítsa el ezt a gazdagép operációs rendszerre vonatkozó követelmények alapján. Például a folytatási jelző karakter Windows-e a kalap `^`. Cserélje le a fordított perjel jel. 
* **Argument sorrend**: Ne módosítsa az argumentumok sorrendje, kivéve, ha nagyon ismeri a Docker-tárolókat.

Cserélje le a zárójelben lévő `{}`, a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
|{BILLING_KEY} | A végpont kulcs Personalizer erőforrás. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | A számlázási végpontértéknek többek között a régióban.|`https://westus2.api.cognitive.microsoft.com/`|

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](personalizer-container-howto.md#billing).
> Apikey tulajdonsággal végzett tesztelése értéke a **kulcs** Azure Personalizer erőforrás kulcsok lapjáról. 

## <a name="personalizer-container-docker-examples"></a>Personalizer tároló Docker-példák

Az alábbi Docker-példák a Personalizer tároló is. 

### <a name="basic-example"></a>Alapszintű példa 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/[container-name] \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Parancssori argumentumok naplózását példa

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/[container-name] \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>További lépések

[Megtudhatja, hogyan használja a Docker-tároló](personalizer-container-howto.md)