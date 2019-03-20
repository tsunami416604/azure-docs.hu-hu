---
title: Az Azure Container Registry webhookok adatbázisséma hivatkozása
description: Webhook kérés JSON hasznos referencia az Azure Container Registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/05/2019
ms.author: danlep
ms.openlocfilehash: 4c0845b9cf5194ecbd0ab813997e17e070840f44
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099899"
---
# <a name="azure-container-registry-webhook-reference"></a>Az Azure Container Registry webhookok leírása

Is [webhookok konfigurálása](container-registry-webhook.md) a tároló-beállításjegyzékhez, amelyek létrehozzák az eseményeket, amikor bizonyos műveleteket alapján történik. Például engedélyezze a webhookok, amelyek jelzik, ha egy tárolórendszerképet, vagy a Helm-diagram leküldte a tárolójegyzékbe, vagy törölni. Webhook akkor aktiválódik, amikor az Azure Container Registry bocsát ki, adja meg a végpont az eseménnyel kapcsolatos adatokat tartalmazó HTTP vagy HTTPS-kérést. A végpont ezután a webhook feldolgozni, és azoknak megfelelő cselekvést.

Az alábbi szakaszok a séma támogatott események által webhook-kérelmet. Az esemény szakaszok az esemény típusa, egy példa kérelem hasznos adatai, és aktiválja a webhookot egy vagy több Példaparancsok tartalom sémáját.

Az Azure container registry webhookok konfigurálásával kapcsolatos további információkért lásd: [használata Azure Container Registry webhookokat](container-registry-webhook.md).

## <a name="webhook-requests"></a>Webhook-kérelmek

### <a name="http-request"></a>HTTP-kérelem

Aktivált webhook lehetővé teszi egy olyan HTTP `POST` kérése az URL-végpontot, a webhook konfigurálásakor megadott.

### <a name="http-headers"></a>HTTP-fejlécek

Webhook kérések tartalmazzák egy `Content-Type` , `application/json` Ha nem adott meg egy `Content-Type` a webhook egyéni fejlécet.

A kérelem ezek egyéni fejlécek, előfordulhat, hogy a megadott a webhook mellett egyéb fejlécek kerülnek.

## <a name="push-event"></a>Leküldéses esemény

A Webhook aktiválódik, ha egy adattár át lett helyezve egy tárolórendszerképet.

### <a name="push-event-payload"></a>Leküldéses eseménytartalom

|Elem|Typo|Leírás|
|-------------|----------|-----------|
|`id`|String|A webhook-esemény azonosítója.|
|`timestamp`|DateTime|Az idő, amikor a webhook-esemény lett elindítva.|
|`action`|String|A művelet, amely kiváltotta a webhook-esemény.|
|[Cél](#target)|Komplex típus|A webhook-esemény kiváltó esemény célját.|
|[Kérelem](#request)|Komplex típus|A kérelem, ami a webhook-esemény jön létre.|

### <a name="target"></a>Cél

|Elem|Typo|Leírás|
|------------------|----------|-----------|
|`mediaType`|String|A hivatkozott objektum MIME-típusát.|
|`size`|Int32|A tartalom bájtok száma. Ugyanaz, mint a Hossz mezőben.|
|`digest`|String|A tartalom, ahogyan a beállításjegyzék V2 HTTP API-specifikációnak a kivonat.|
|`length`|Int32|A tartalom bájtok száma. Ugyanaz, mint mérete mező.|
|`repository`|String|A tárház nevét.|
|`tag`|String|A kép címke neve.|

### <a name="request"></a>Kérelem

|Elem|Typo|Leírás|
|------------------|----------|-----------|
|`id`|String|Az esemény által kezdeményezett kérelem azonosítója.|
|`host`|String|A kívülről elérhető-példányának gazdagépnevét a beállításjegyzék, a bejövő kérelem HTTP-állomásfejlécet által megadott.|
|`method`|String|A kérelmi metódust, ami az esemény jön létre.|
|`useragent`|String|A felhasználói ügynök fejléc a kérelem.|

### <a name="payload-example-image-push-event"></a>Adattartalom-példa: kép leküldéses esemény

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Példa [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) parancsot, amely elindítja a lemezkép **leküldéses** event webhook:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Diagram leküldéses esemény

A Webhook aktiválódik, ha egy tárház egy Helm-diagramot a rendszer továbbítja.

### <a name="chart-push-event-payload"></a>Diagram leküldéses eseménytartalom

|Elem|Typo|Leírás|
|-------------|----------|-----------|
|`id`|String|A webhook-esemény azonosítója.|
|`timestamp`|DateTime|Az idő, amikor a webhook-esemény lett elindítva.|
|`action`|String|A művelet, amely kiváltotta a webhook-esemény.|
|[Cél](#helm_target)|Komplex típus|A webhook-esemény kiváltó esemény célját.|

### <a name="helm_target"></a>Cél

|Elem|Typo|Leírás|
|------------------|----------|-----------|
|`mediaType`|String|A hivatkozott objektum MIME-típusát.|
|`size`|Int32|A tartalom bájtok száma.|
|`digest`|String|A tartalom, ahogyan a beállításjegyzék V2 HTTP API-specifikációnak a kivonat.|
|`repository`|String|A tárház nevét.|
|`tag`|String|A diagram címke neve.|
|`name`|String|A diagram neve.|
|`version`|String|A diagram verziója.|

### <a name="payload-example-chart-push-event"></a>Adattartalom-példa: diagram leküldéses esemény

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Példa [Azure CLI-vel](/cli/azure/acr) parancsot, amely elindítja a **chart_push** event webhook:

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Esemény törlése

Webhook által aktivált, amikor egy lemezképtárban, vagy a jegyzékfájl törlődik. Nem aktiválódik, ha egy címke törlése.

### <a name="delete-event-payload"></a>Eseménytartalom törlése

|Elem|Typo|Leírás|
|-------------|----------|-----------|
|`id`|String|A webhook-esemény azonosítója.|
|`timestamp`|DateTime|Az idő, amikor a webhook-esemény lett elindítva.|
|`action`|String|A művelet, amely kiváltotta a webhook-esemény.|
|[Cél](#delete_target)|Komplex típus|A webhook-esemény kiváltó esemény célját.|
|[Kérelem](#delete_request)|Komplex típus|A kérelem, ami a webhook-esemény jön létre.|

### <a name="delete_target"></a> Cél

|Elem|Typo|Leírás|
|------------------|----------|-----------|
|`mediaType`|String|A hivatkozott objektum MIME-típusát.|
|`digest`|String|A tartalom, ahogyan a beállításjegyzék V2 HTTP API-specifikációnak a kivonat.|
|`repository`|String|A tárház nevét.|

### <a name="delete_request"></a> Kérelem

|Elem|Typo|Leírás|
|------------------|----------|-----------|
|`id`|String|Az esemény által kezdeményezett kérelem azonosítója.|
|`host`|String|A kívülről elérhető-példányának gazdagépnevét a beállításjegyzék, a bejövő kérelem HTTP-állomásfejlécet által megadott.|
|`method`|String|A kérelmi metódust, ami az esemény jön létre.|
|`useragent`|String|A felhasználói ügynök fejléc a kérelem.|

### <a name="payload-example-image-delete-event"></a>Adattartalom-példa: lemezkép törlése esemény

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Példa [Azure CLI-vel](/cli/azure/acr) parancsok, az eseményindító egy **törlése** event webhook:

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Diagram törlése esemény

Webhook által aktivált, amikor egy Helm-diagram vagy a tárház törlődik. 

### <a name="chart-delete-event-payload"></a>Diagram törlése eseménytartalom

|Elem|Typo|Leírás|
|-------------|----------|-----------|
|`id`|String|A webhook-esemény azonosítója.|
|`timestamp`|DateTime|Az idő, amikor a webhook-esemény lett elindítva.|
|`action`|String|A művelet, amely kiváltotta a webhook-esemény.|
|[Cél](#chart_delete_target)|Komplex típus|A webhook-esemény kiváltó esemény célját.|

### <a name="chart_delete_target"></a> Cél

|Elem|Typo|Leírás|
|------------------|----------|-----------|
|`mediaType`|String|A hivatkozott objektum MIME-típusát.|
|`size`|Int32|A tartalom bájtok száma.|
|`digest`|String|A tartalom, ahogyan a beállításjegyzék V2 HTTP API-specifikációnak a kivonat.|
|`repository`|String|A tárház nevét.|
|`tag`|String|A diagram címke neve.|
|`name`|String|A diagram neve.|
|`version`|String|A diagram verziója.|

### <a name="payload-example-chart-delete-event"></a>Adattartalom-példa: diagram törlése esemény

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Példa [Azure CLI-vel](/cli/azure/acr) parancsot, amely elindítja a **chart_delete** event webhook:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>További lépések

[Azure Container Registry webhookok használata](container-registry-webhook.md)