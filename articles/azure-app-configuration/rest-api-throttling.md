---
title: Azure app Configuration REST API – szabályozás
description: Az Azure-alkalmazás konfigurációjának használatakor a szabályozás megértését ismertető lapok REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 26464138b7739bb476d9d0dd1078194473ecd23e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424271"
---
# <a name="throttling"></a>Throttling

A konfigurációs tárolók korlátozásokat tartalmaznak az általuk kiszolgált kérelmek esetében. A konfigurációs tároló számára kiosztott kvótát meghaladó kérések HTTP 429 (túl sok kérés) választ kapnak.

A szabályozás különböző kvóta-házirendekre oszlik:

- **Kérelmek összesen** – kérelmek teljes száma
- **Teljes sávszélesség** – kimenő adatmennyiség bájtban
- **Tárterület** – a felhasználói adatmennyiség teljes mérete bájtban megadva

## <a name="handling-throttled-responses"></a>A szabályozott válaszok feldolgozása

Ha elérte az adott kvótára vonatkozó díjszabási korlátot, a kiszolgáló egy _429_ állapotkódot választ az adott típusú további kérelmekre. Az _429_ -es válasz egy _Újrapróbálkozás utáni MS_ fejlécet tartalmaz, amely az ügyfelet egy javasolt várakozási idővel (ezredmásodpercben) adja meg, hogy a kérelem kvótája feltöltésre kerüljön.

```http
HTTP/1.1 429 (Too Many Requests)
retry-after-ms: 10
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/too-many-requests",
  "title": "Resource utilization has surpassed the assigned quota",
  "policy": "Total Requests",
  "status": 429
}
```

A fenti példában az ügyfél túllépte az engedélyezett kvótát, és a további kérések megkísérlése előtt azt javasoljuk, hogy várjon 10 ezredmásodpercet. Az ügyfeleknek érdemes megfontolniuk a progresszív leállítási is.

## <a name="other-retry"></a>Egyéb újrapróbálkozás

A szolgáltatás a szabályozástól eltérő, az ügyfél újrapróbálkozásához szükséges helyzeteket is azonosíthatja (például: a 503 szolgáltatás nem érhető el). Ebben az esetben a `retry-after-ms` Válasz fejléce lesz megadva. A megbízhatóság növeléséhez az ügyfélnek a javasolt intervallum követéséhez és az újrapróbálkozások elvégzéséhez javasolt.

```http
HTTP/1.1 503 Service Unavailable
retry-after-ms: 787
```
