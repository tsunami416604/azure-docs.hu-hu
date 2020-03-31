---
title: Rendszerleíró adatbázis webhook-sémahivatkozása
description: Hivatkozás a JSON-tartalomhoz az Azure-tároló beállításjegyzékében lévő webhook-kérelmekhez, amelyek akkor jönnek létre, amikor a webhookok engedélyezve vannak a műtermék leküldéses vagy törlési eseményekhez
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 8354ef9db24d5825238155ac567d5d829f9b0d7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455975"
---
# <a name="azure-container-registry-webhook-reference"></a>Az Azure Container Registry webhook-hivatkozása

[Webhookokat konfigurálhat](container-registry-webhook.md) a tároló beállításjegyzékéhez, amelyek bizonyos műveletek végrehajtásakor eseményeket hoznak létre. Például engedélyezze azokat a webhookokat, amelyek akkor aktiválódnak, ha egy tárolórendszerképet vagy helmdiagramot egy beállításjegyzékbe tolnak vagy törölnek. Amikor egy webhook aktiválódik, az Azure Container Registry kiad egy HTTP- vagy HTTPS-kérelmet, amely az eseményre vonatkozó információkat tartalmaz egy megadott végpontnak. A végpont ezután feldolgozhatja a webhookot, és ennek megfelelően cselekedhet.

A következő szakaszok részletesen ismertetik a támogatott események által létrehozott webhook-kérelmek sémáját. Az eseményszakaszok tartalmazzák az eseménytípus hasznos sémáját, egy példakérelem-hasznos adatát és egy vagy több példaparancsot, amelyek a webhookot aktiválják.

A webhookok Azure container registry beállításjegyzékbe történő konfigurálásáról az [Azure Container Registry webhooks használata című témakörben](container-registry-webhook.md)talál további információt.

## <a name="webhook-requests"></a>Webhook-kérelmek

### <a name="http-request"></a>HTTP-kérelem

Egy aktivált webhook http-kérelmet `POST` küld a webhook konfigurálásakor megadott URL-végpontra.

### <a name="http-headers"></a>HTTP-fejlécek

Webhook-kérelmek `Content-Type` közé `application/json` tartozik egy, ha `Content-Type` nem adott meg egyéni fejlécet a webhook.

A webhookhoz megadott egyéni fejléceken kívül nincs más fejléc a kérelemhez.

## <a name="push-event"></a>Leküldéses esemény

Webhook akkor aktiválódik, amikor egy tárolórendszerkép leküldéses egy tárházba.

### <a name="push-event-payload"></a>Leküldéses esemény hasznos adat

|Elem|Típus|Leírás|
|-------------|----------|-----------|
|`id`|Sztring|A webhook-esemény azonosítója.|
|`timestamp`|DateTime|A webhook-esemény aktiválásának időpontja.|
|`action`|Sztring|A webhook-eseményt aktiváló művelet.|
|[Cél](#target)|Összetett típus|A webhook-eseményt kiváltó esemény célja.|
|[Kérés](#request)|Összetett típus|A webhook-eseményt létrehozó kérelem.|

### <a name="target"></a><a name="target"></a>Cél

|Elem|Típus|Leírás|
|------------------|----------|-----------|
|`mediaType`|Sztring|A hivatkozott objektum MIME-típusa.|
|`size`|Int32|A tartalom bájtjainak száma. Megegyezik a Hossz mezővel.|
|`digest`|Sztring|A tartalom kivonatolása a Rendszerleíróadatbázis V2 HTTP API specifikációja szerint.|
|`length`|Int32|A tartalom bájtjainak száma. Megegyezik a Méret mezővel.|
|`repository`|Sztring|A tárház neve.|
|`tag`|Sztring|A képcímke neve.|

### <a name="request"></a><a name="request"></a>Kérés

|Elem|Típus|Leírás|
|------------------|----------|-----------|
|`id`|Sztring|Az eseményt kezdeményező kérelem azonosítója.|
|`host`|Sztring|A beállításjegyzék-példány külsőleg elérhető állomásneve, ahogy azt a HTTP-állomás fejléce a bejövő kérelmeknél meghatározta.|
|`method`|Sztring|Az eseményt létrehozó kérelemmetódus.|
|`useragent`|Sztring|A kérelem felhasználói ügynökfejléce.|

### <a name="payload-example-image-push-event"></a>Hasznos adat példa: image push esemény

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

Példa [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) parancs, amely elindítja a rendszerkép **leküldéses** esemény webhook:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Diagram leküldéses eseménye

Webhook akkor aktiválódik, amikor egy Helm-diagram leküldéses egy tárházba.

### <a name="chart-push-event-payload"></a>Diagram leküldéses esemény hasznos terhelése

|Elem|Típus|Leírás|
|-------------|----------|-----------|
|`id`|Sztring|A webhook-esemény azonosítója.|
|`timestamp`|DateTime|A webhook-esemény aktiválásának időpontja.|
|`action`|Sztring|A webhook-eseményt aktiváló művelet.|
|[Cél](#helm_target)|Összetett típus|A webhook-eseményt kiváltó esemény célja.|

### <a name="target"></a><a name="helm_target"></a>Cél

|Elem|Típus|Leírás|
|------------------|----------|-----------|
|`mediaType`|Sztring|A hivatkozott objektum MIME-típusa.|
|`size`|Int32|A tartalom bájtjainak száma.|
|`digest`|Sztring|A tartalom kivonatolása a Rendszerleíróadatbázis V2 HTTP API specifikációja szerint.|
|`repository`|Sztring|A tárház neve.|
|`tag`|Sztring|A diagramcímke neve.|
|`name`|Sztring|A diagram neve.|
|`version`|Sztring|A diagram verziója.|

### <a name="payload-example-chart-push-event"></a>Hasznos adat példa: diagramleküldéses esemény

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

Példa [az Azure CLI](/cli/azure/acr) parancsra, amely elindítja a **chart_push** esemény webhookját:

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Esemény törlése

Webhook akkor aktiválódik, ha egy képtár vagy jegyzékfájl törlődik. A címke törlésekor nem aktiválódik.

### <a name="delete-event-payload"></a>Esemény hasznos adatának törlése

|Elem|Típus|Leírás|
|-------------|----------|-----------|
|`id`|Sztring|A webhook-esemény azonosítója.|
|`timestamp`|DateTime|A webhook-esemény aktiválásának időpontja.|
|`action`|Sztring|A webhook-eseményt aktiváló művelet.|
|[Cél](#delete_target)|Összetett típus|A webhook-eseményt kiváltó esemény célja.|
|[Kérés](#delete_request)|Összetett típus|A webhook-eseményt létrehozó kérelem.|

### <a name="target"></a><a name="delete_target"></a>Cél

|Elem|Típus|Leírás|
|------------------|----------|-----------|
|`mediaType`|Sztring|A hivatkozott objektum MIME-típusa.|
|`digest`|Sztring|A tartalom kivonatolása a Rendszerleíróadatbázis V2 HTTP API specifikációja szerint.|
|`repository`|Sztring|A tárház neve.|

### <a name="request"></a><a name="delete_request"></a>Kérés

|Elem|Típus|Leírás|
|------------------|----------|-----------|
|`id`|Sztring|Az eseményt kezdeményező kérelem azonosítója.|
|`host`|Sztring|A beállításjegyzék-példány külsőleg elérhető állomásneve, ahogy azt a HTTP-állomás fejléce a bejövő kérelmeknél meghatározta.|
|`method`|Sztring|Az eseményt létrehozó kérelemmetódus.|
|`useragent`|Sztring|A kérelem felhasználói ügynökfejléce.|

### <a name="payload-example-image-delete-event"></a>Hasznos adatpélda: képtörlési esemény

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

Példa [az Azure CLI-parancsokra,](/cli/azure/acr) amelyek törlési esemény webhookot váltanak ki: **delete**

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Diagramtörlési esemény

A Helm-diagram vagy tárház törlésekor kiváltott webhook. 

### <a name="chart-delete-event-payload"></a>Diagramtörlési esemény hasznos terhelése

|Elem|Típus|Leírás|
|-------------|----------|-----------|
|`id`|Sztring|A webhook-esemény azonosítója.|
|`timestamp`|DateTime|A webhook-esemény aktiválásának időpontja.|
|`action`|Sztring|A webhook-eseményt aktiváló művelet.|
|[Cél](#chart_delete_target)|Összetett típus|A webhook-eseményt kiváltó esemény célja.|

### <a name="target"></a><a name="chart_delete_target"></a>Cél

|Elem|Típus|Leírás|
|------------------|----------|-----------|
|`mediaType`|Sztring|A hivatkozott objektum MIME-típusa.|
|`size`|Int32|A tartalom bájtjainak száma.|
|`digest`|Sztring|A tartalom kivonatolása a Rendszerleíróadatbázis V2 HTTP API specifikációja szerint.|
|`repository`|Sztring|A tárház neve.|
|`tag`|Sztring|A diagramcímke neve.|
|`name`|Sztring|A diagram neve.|
|`version`|Sztring|A diagram verziója.|

### <a name="payload-example-chart-delete-event"></a>Hasznos adat példa: diagramtörlési esemény

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

Példa [az Azure CLI](/cli/azure/acr) parancsra, amely elindítja a **chart_delete** esemény webhookját:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>További lépések

[Az Azure Container Registry webhookjainak használata](container-registry-webhook.md)