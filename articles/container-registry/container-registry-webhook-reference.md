---
title: Az Azure tároló beállításjegyzék webhook adatbázisséma hivatkozása
description: Webhook kérelem JSON hasznos referencia az Azure-tároló beállításjegyzék.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: marsma
ms.openlocfilehash: f62477a4c68abf1617d9689047913fd820ee5461
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-container-registry-webhook-reference"></a>Az Azure tároló beállításjegyzék webhook referencia

Is [konfigurálása webhookokkal](container-registry-webhook.md) a tároló rendszerleíró, amelyek bizonyos végrehajt rajta eseményeket. Például engedélyezheti a tároló rendszerképre által kiváltott webhookok `push` és `delete` műveletek. A webhook aktiválása esetén az Azure tároló beállításjegyzék az esemény akkor adja meg a végpont kapcsolatos információkat tartalmazó HTTP vagy HTTPS-kérelmet ad ki. A végpont a webhook feldolgozni, majd ennek megfelelően működjön.

A következő részek a séma támogatott események által webhook kérelmek. Az esemény szakaszok tartalmaznak az eseménytípus, egy példa-kérések forgalma és egy vagy több indítsa el a webhook Példaparancsok hasznos sémáját.

További információ a webhookok beállításáról az Azure-tárolót rendszerleíró: [Azure tároló beállításjegyzék használatával webhookok](container-registry-webhook.md).

## <a name="webhook-requests"></a>Webhook kérelmek

### <a name="http-request"></a>HTTP-kérelem

Egy kiváltott webhook HTTP teszi `POST` kérése az URL-végpontjának a webhook konfigurálásakor megadott.

### <a name="http-headers"></a>HTTP-fejlécek

Webhook kérelmek közé tartozik egy `Content-Type` a `application/json` Ha nem adott meg egy `Content-Type` a webhook egyéni fejlécet.

A kérelem túl ezen egyéni fejlécek esetleg lett megadva a webhook más fejlécek nem lettek hozzáadva.

## <a name="push-event"></a>Leküldéses esemény

A tároló-lemezkép a rendszer előkészítésre továbbít egy tárház által elindított Webhook.

### <a name="push-event-payload"></a>Leküldéses eseménytartalom

|Elem|Típus|Leírás|
|-------------|----------|-----------|
|`id`|Karakterlánc|A webhook esemény azonosítója.|
|`timestamp`|DateTime|Az az idő, ahol a webhook esemény lett elindítva.|
|`action`|Karakterlánc|A webhook eseményt kiváltó művelet.|
|[cél](#target)|Összetett típus|A webhook eseményt kiváltó esemény célját.|
|[Kérelem](#request)|Összetett típus|A kérelem a webhook eseményt létrehozó.|

### <a name="target"></a>cél

|Elem|Típus|Leírás|
|------------------|----------|-----------|
|`mediaType`|Karakterlánc|A hivatkozott objektum MIME-típusát.|
|`size`|Int32|A tartalom bájtok száma. Ugyanaz, mint hosszúság mezője.|
|`digest`|Karakterlánc|A kivonatoló a tartalom határozzák meg a beállításjegyzék V2 HTTP API-specifikációnak.|
|`length`|Int32|A tartalom bájtok száma. Ugyanaz, mint mérete mező.|
|`repository`|Karakterlánc|A tárház nevét.|
|`tag`|Karakterlánc|A kép címke neve.|

### <a name="request"></a>Kérelem

|Elem|Típus|Leírás|
|------------------|----------|-----------|
|`id`|Karakterlánc|A kérelem, az esemény kezdeményezett azonosítója.|
|`host`|Karakterlánc|A beállításjegyzék-példányban leírtak szerint a HTTP állomásfejléc bejövő kérelmek kívülről elérhető állomásnevét.|
|`method`|Karakterlánc|A kérelem metódus az eseményt létrehozó.|
|`useragent`|Karakterlánc|A felhasználói ügynök a kérelem fejléce.|

### <a name="payload-example-push-event"></a>Hasznos példa: leküldéses esemény

```JSON
{
  "id": "cb8c3971-9adc-488b-bdd8-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-86cd-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Példa [Docker parancssori felületén](https://docs.docker.com/engine/reference/commandline/cli/) parancs, amely elindítja a **leküldéses** esemény webhook:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="delete-event"></a>Esemény törlése

A Webhook kiváltva, ha a tárház vagy jegyzékfájl törlődik. Nem indulnak el, egy címke törlésekor.

### <a name="delete-event-payload"></a>Eseménytartalom törlése

|Elem|Típus|Leírás|
|-------------|----------|-----------|
|`id`|Karakterlánc|A webhook esemény azonosítója.|
|`timestamp`|DateTime|Az az idő, ahol a webhook esemény lett elindítva.|
|`action`|Karakterlánc|A webhook eseményt kiváltó művelet.|
|[cél](#delete_target)|Összetett típus|A webhook eseményt kiváltó esemény célját.|
|[Kérelem](#delete_request)|Összetett típus|A kérelem a webhook eseményt létrehozó.|

### <a name="delete_target"></a> cél

|Elem|Típus|Leírás|
|------------------|----------|-----------|
|`mediaType`|Karakterlánc|A hivatkozott objektum MIME-típusát.|
|`digest`|Karakterlánc|A kivonatoló a tartalom határozzák meg a beállításjegyzék V2 HTTP API-specifikációnak.|
|`repository`|Karakterlánc|A tárház nevét.|

### <a name="delete_request"></a> Kérelem

|Elem|Típus|Leírás|
|------------------|----------|-----------|
|`id`|Karakterlánc|A kérelem, az esemény kezdeményezett azonosítója.|
|`host`|Karakterlánc|A beállításjegyzék-példányban leírtak szerint a HTTP állomásfejléc bejövő kérelmek kívülről elérhető állomásnevét.|
|`method`|Karakterlánc|A kérelem metódus az eseményt létrehozó.|
|`useragent`|Karakterlánc|A felhasználói ügynök a kérelem fejléce.|

### <a name="payload-example-delete-event"></a>Hasznos példa: az esemény törlése

```JSON
{
    "id": "afc359ce-df7f-4e32-bdde-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-807f-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Példa [Azure CLI 2.0](/cli/azure/acr) parancsok, amely egy **törlése** esemény webhook:

```azurecli
# Delete repository
az acr repository delete -n MyRegistry --repository MyRepository

# Delete manifest
az acr repository delete -n MyRegistry --repository MyRepository --tag MyTag --manifest
```

## <a name="next-steps"></a>További lépések

[Azure-tároló beállításjegyzék webhookok használatával](container-registry-webhook.md)