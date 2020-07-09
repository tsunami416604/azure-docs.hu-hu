---
title: Azure Event Grid IoT Edge kimenete Microsoft Docs
description: A Event Grid IoT Edge-on való bekapcsolásának kimenete.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a6f033af34088081090251f2e5e7cd4a07ce43cc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76841747"
---
# <a name="output-batching"></a>Kimenet kötegelése

Event Grid több esemény egyetlen kézbesítési kérelemben való továbbítását támogatja. Ez a funkció lehetővé teszi a teljes kézbesítési sebesség növelését anélkül, hogy a HTTP per-Request típusú terhelést kellene fizetnie. A Batch szolgáltatás alapértelmezés szerint ki van kapcsolva, és előfizetésre is kapcsolható.

> [!WARNING]
> Az egyes kézbesítési kérések feldolgozásához megengedett maximális időtartam nem változik, bár az előfizetői kódnak több munkát kell végeznie a kötegelt kérések esetében. A kézbesítés időtúllépése alapértelmezett értéke 60 másodperc.

## <a name="batching-policy"></a>Kötegelt házirend

A Event Grid batching viselkedése előfizetőként testreszabható, az alábbi két beállítás csípésével:

* Események maximális száma kötegben

  Ezzel a beállítással állítható be a kötegelt kézbesítési kérelembe felvehető események száma felső korlátja.

* Előnyben részesített köteg mérete (kilobájtban)

  Ez a gomb a kézbesítési kérelemben elküldhető kilobájtok maximális számának szabályozására szolgál.

## <a name="batching-behavior"></a>Kötegelt működés

* Összes vagy nincs

  A Event Grid minden-vagy-none szemantikagal működik. Nem támogatja a Batch-kézbesítések részleges sikerességét. Az előfizetőknek körültekintően kell lenniük arra, hogy csak annyi eseményt kérjenek a Batch szolgáltatásban, amennyit 60 másodperc alatt ésszerűen kezelhetnek.

* Optimista kötegelt feldolgozás

  A batching házirend-beállításai nem szigorú korlátokat mutatnak a kötegek viselkedésére vonatkozóan, és a legjobb erőfeszítést figyelembe veszik. Az alacsony események díjainál gyakran megfigyelheti, hogy a köteg mérete kisebb, mint a másodpercenként kért maximális események száma.

* Az alapértelmezett beállítás a kikapcsolva értékre van állítva

  Alapértelmezés szerint a Event Grid csak egy eseményt hoz létre az egyes kézbesítési kérelmekhez. A kötegelt feldolgozás bekapcsolásának módja az esemény-előfizetés JSON-fájljában korábban említett beállítások valamelyikének beállítása.

* Alapértelmezett értékek

  Egy esemény-előfizetés létrehozásakor nem szükséges megadni a beállításokat (a kötegek maximális számát, valamint a kötegelt méretet kilogramm bájtban). Ha csak egy beállítás van beállítva, Event Grid a (konfigurálható) alapértelmezett értékeket használja. Tekintse meg a következő részeket az alapértelmezett értékekről, valamint a felülbírálásuk módját.

## <a name="turn-on-output-batching"></a>A kimeneti köteg bekapcsolása

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="configuring-maximum-allowed-values"></a>Maximálisan engedélyezett értékek konfigurálása

A következő üzembe helyezési idő beállítások az esemény-előfizetés létrehozásakor engedélyezett maximális értéket vezérlik.

| Tulajdonság neve | Description |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | A forgatógomb számára engedélyezett maximális érték `PreferredBatchSizeInKilobytes` Alapértelmezett érték `1033` .
| `api__deliveryPolicyLimits__maxEventsPerBatch` | A forgatógomb számára engedélyezett maximális érték `MaxEventsPerBatch` Alapértelmezett érték `50` .

## <a name="configuring-runtime-default-values"></a>Futásidejű alapértelmezett értékek konfigurálása

A következő üzembe helyezési idő beállításai vezérlik az egyes gombok futásidejű alapértelmezett értékét, ha az esemény-előfizetésben nincs megadva. Az ismételt próbálkozáshoz be kell állítani legalább egy gombot az esemény-előfizetésre, hogy bekapcsolja a kötegelt működést.

| Tulajdonság neve | Description |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | A kézbesítési kérelmek maximális mérete, ha csak meg `MaxEventsPerBatch` van adva. Alapértelmezett érték `1_058_576` .
| `broker__defaultMaxEventsPerBatch` | A köteghez hozzáadandó események maximális száma, ha csak meg `MaxBatchSizeInBytes` van adva. Alapértelmezett érték `10` .
