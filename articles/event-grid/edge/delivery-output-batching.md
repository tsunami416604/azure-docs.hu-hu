---
title: Kimeneti kötegelés az Azure Event Grid IoT Edge-ben | Microsoft dokumentumok
description: Kimeneti kötegelés az Event Gridben az IoT Edge-en.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a6f033af34088081090251f2e5e7cd4a07ce43cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841747"
---
# <a name="output-batching"></a>Kimenet kötegelése

Az Event Grid támogatja, hogy egyetlen kézbesítési kérelemben egynél több eseményt kézbesítsen. Ez a funkció lehetővé teszi a teljes kézbesítési átviteli szint növelését a HTTP kérelemenkénti általános költségeinek megfizetése nélkül. A kötegelés alapértelmezés szerint ki van kapcsolva, és előfizetésenként is bekapcsolható.

> [!WARNING]
> Az egyes kézbesítési kérelmek feldolgozásának maximálisan engedélyezett időtartama nem változik, még akkor sem, ha az előfizetői kódnak kötegelt kérelemenként több munkát kell végeznie. A szállítási időtúlérték alapértelmezés szerint 60 másodperc.

## <a name="batching-policy"></a>Kötegelési házirend

Az Event Grid kötegelési viselkedése előfizetőnként testreszabható a következő két beállítás módosításával:

* Események maximális százaléka kötegenként

  Ez a beállítás a kötegelt kézbesítési kérelemhez adható események számának felső korlátját határozza meg.

* Előnyben részesített kötegméret kilobájtban

  Ez a gomb a szállítási kérelemenként elküldhető kilobájtok maximális számának további szabályozására szolgál.

## <a name="batching-behavior"></a>Kötegelési viselkedés

* Minden vagy egyik sem

  Az Event Grid mindent vagy semmit szemantikával működik. Nem támogatja a kötegelt kézbesítés részleges sikerét. Az előfizetőknek ügyelve arra, hogy csak annyi eseményt kérjenek kötegenként, amennyit 60 másodperc alatt ésszerűen kezelni tudnak.

* Optimista kötegelés

  A kötegelési házirend-beállítások nem szigorú a kötegelési viselkedés, és a legjobb erőfeszítés alapján tiszteletben tartják. Alacsony eseményarány esetén gyakran megfigyeli, hogy a kötegméretek kisebbek, mint a kötegenkéntkért maximális események.

* Az alapértelmezett érték KI

  Alapértelmezés szerint az Event Grid minden kézbesítési kérelemhez csak egy eseményt ad hozzá. A kötegelés bekapcsolásával beállíthatja a JSON-előfizetés esemény-előfizetése korábban említett beállítások egyikét.

* Alapértelmezett értékek

  Esemény-előfizetés létrehozásakor nem szükséges megadni a beállításokat (A kötegenkénti események maximális száma és a köteghozzávemérete kilo bájtban). Ha csak egy beállítás van beállítva, az Event Grid (konfigurálható) alapértelmezett értékeket használ. Tekintse meg a következő szakaszokaz alapértelmezett értékeket, és hogyan lehet felülírni őket.

## <a name="turn-on-output-batching"></a>Kimeneti kötegelés bekapcsolása

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

## <a name="configuring-maximum-allowed-values"></a>A maximálisan megengedett értékek konfigurálása

A következő telepítési időbeállítások szabályozzák az esemény-előfizetés létrehozásakor engedélyezett maximális értéket.

| Tulajdonság neve | Leírás |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | A `PreferredBatchSizeInKilobytes` gomb maximális értéke megengedett. Az `1033`alapértelmezett érték .
| `api__deliveryPolicyLimits__maxEventsPerBatch` | A `MaxEventsPerBatch` gomb maximális értéke megengedett. Az `50`alapértelmezett érték .

## <a name="configuring-runtime-default-values"></a>Futásidejű alapértelmezett értékek konfigurálása

A következő telepítési időbeállítások szabályozzák az egyes gombok futásidejű alapértelmezett értékét, ha az nincs megadva az Esemény-előfizetésben. Megismételni, legalább egy gombot be kell állítani az Esemény-előfizetés bekapcsolása kötegelési viselkedés.

| Tulajdonság neve | Leírás |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | Maximális kézbesítési `MaxEventsPerBatch` kérelem mérete, ha csak meg van adva. Az `1_058_576`alapértelmezett érték .
| `broker__defaultMaxEventsPerBatch` | Köteghez hozzáadandó események maximális száma, ha csak `MaxBatchSizeInBytes` meg van adva. Az `10`alapértelmezett érték .
