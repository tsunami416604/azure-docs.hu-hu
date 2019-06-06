---
title: Az Azure Event Griddel Container Registry eseménysémája
description: Ismerteti a tulajdonságait, amelyet az Azure Event Griddel Container Registry-események
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 03/12/2019
ms.author: spelluru
ms.openlocfilehash: c5998ff428c4b6f4c1f7a4087c6ccb27d93773eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60345464"
---
# <a name="azure-event-grid-event-schema-for-container-registry"></a>Tároló-beállításjegyzék Azure Event Grid eseménysémája

Ez a cikk a séma és a Container Registry-események. Eseménysémák szeretné megismerni, lásd: [Azure Event Grid-esemény séma](event-schema.md).

## <a name="available-event-types"></a>Rendelkezésre álló események típusai

Az Azure Container Registry a következő esemény típusú bocsát ki:

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Jelenik meg, ha a kép leküldésekor. |
| Microsoft.ContainerRegistry.ImageDeleted | Következik be, amikor egy lemezkép törlődik. |
| Microsoft.ContainerRegistry.ChartPushed | Következik be, amikor egy Helm-diagram leküldésekor. |
| Microsoft.ContainerRegistry.ChartDeleted | Jön létre, ha törölnek egy Helm-diagramot. |

## <a name="example-event"></a>Példa esemény

Az alábbi példa bemutatja egy esemény leküldött rendszerkép sémája: 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "eventType": "ImagePushed",
  "eventTime": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Egy törölt kép esemény sémája hasonlít:

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "eventType": "ImageDeleted",
  "eventTime": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

A séma, esemény leküldött diagram egy lemezkép-alapú leküldött esemény sémája a hasonló, de nem tartalmazza a kérés objektum:

```json
[{
  "id": "ea3a9c28-5b17-40f6-a500-3f02b6829277",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartPushed",
  "eventTime": "2019-03-12T22:16:31.5164086Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:16:31.0087496+00:00",
    "action":"chart_push",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Egy törölt diagram esemény sémája a sémában az leképezett törölt esemény hasonló, de nem tartalmazza a kérés objektum:

```json
[{
  "id": "39136b3a-1a7e-416f-a09e-5c85d5402fca",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartDeleted",
  "eventTime": "019-03-12T22:42:08.7034064Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:42:08.3783775+00:00",
    "action":"chart_delete",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény a következő legfelső szintű adatokat tartalmaz:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| topic | string | A forrás teljes erőforrás elérési útja. Ez a mező nem írható. Event Grid biztosítja ezt az értéket. |
| subject | string | Az esemény tárgya közzétevő által megadott elérési útja. |
| eventType | string | Ehhez eseményre adatforráshoz regisztrált esemény típusok egyikét. |
| eventTime | string | Az esemény akkor jön létre az idő alapján a szolgáltató UTC idő. |
| id | string | Az esemény egyedi azonosítója. |
| data | objektum | A BLOB storage-eseményadatok. |
| dataVersion | string | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | string | Az esemény-metaadatok sémaverziója. Event Grid sémáját, a legfelső szintű tulajdonságait határozza meg. Event Grid biztosítja ezt az értéket. |

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| id | string | Az esemény azonosítója. |
| timestamp | string | Az az időpont, amikor az esemény történt. |
| action | string | A művelet, amely magában foglalja a megadott esemény. |
| target | objektum | A cél az esemény. |
| request | objektum | A kérelem, ami az esemény jön létre. |

A célobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| mediaType | string | A hivatkozott objektum MIME-típusát. |
| size | egész szám | A tartalom bájtok száma. Ugyanaz, mint a Hossz mezőben. |
| digest | string | A tartalom, ahogyan a beállításjegyzék V2 HTTP API-specifikációnak a kivonat. |
| length | egész szám | A tartalom bájtok száma. Ugyanaz, mint mérete mező. |
| repository | string | A tárház nevét. |
| tag | string | A címke neve. |
| name | string | A diagram neve. |
| version | string | A diagram verziója. |

A kérelem objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| id | string | Az esemény által kezdeményezett kérelem azonosítója. |
| addr | string | Az IP vagy állomásnév, esetleg az ügyfél kapcsolata az eseményt inicializáló portjával. Ez az érték a szabványos http-kérelem a RemoteAddr. |
| host | string | A kívülről elérhető-példányának gazdagépnevét a beállításjegyzék, a bejövő kérelem http-állomásfejlécet által megadott. |
| method | string | A kérelmi metódust, ami az esemény jön létre. |
| useragent | string | A felhasználói ügynök fejléc a kérelem. |

## <a name="next-steps"></a>További lépések

* Azure Event Grid bemutatása, lásd: [Mi az Event Grid?](overview.md)
* Az Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid-előfizetés séma](subscription-creation-schema.md).
