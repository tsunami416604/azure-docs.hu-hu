---
title: Az Azure Application Insights Telemetriai adatokat modell - Telemetriai kérelem |} A Microsoft Docs
description: Application Insights-adatmodell – kéréstelemetria
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: d39ec71315bec98c35ac7fb76ed9a88a094817ca
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117146"
---
# <a name="request-telemetry-application-insights-data-model"></a>Kérelmek telemetriai adatai: Application Insights-adatmodell

A kérelemben szereplő telemetriai elem (a [Application Insights](../../azure-monitor/app/app-insights-overview.md)) az alkalmazás külső kérelem által kiváltott végrehajtás logikai sorrendben jelöli. Minden kérelem végrehajtásának azonosítja egyedi `ID` és `url` tartalmazó összes végrehajtás paramétert. Kérelmek logikai szerint csoportosíthatók `name` , és meghatározhatja a `source` , ezt a kérelmet. Végrehajtás eredményezhet `success` vagy `fail` és a egy bizonyos rendelkezik `duration`. Mind a sikeres és Sikertelen végrehajtások csoportosíthatók további korlátozásokat `resultCode`. Kezdési ideje a kérelmek telemetriai adatai, meghatározott boríték szintjén.

Telemetria támogatja a standard szintű bővíthetőségi modell segítségével egyéni kérelem `properties` és `measurements`.

## <a name="name"></a>Name (Név)

A kérés neve a kérés feldolgozásához igénybe vett kódelérési út jelöli. Alacsony cardinality értéke, hogy jobban kérelmek csoportosítása. A HTTP-kérések azt jelöli, a HTTP-metódus és egy URL-cím elérési út sablont, például `GET /values/{id}` nélkül a tényleges `id` értéket.

Application Insights webes SDK kérelem neve "adott állapotában" küldi a nagybetűk meg. A felhasználói felület a csoportosítás akkor kis-és nagybetűket, `GET /Home/Index` külön számít a `GET /home/INDEX` annak ellenére, hogy milyen gyakran eredményeznek ugyanazon vezérlő és a művelet végrehajtását. Amelyek oka, hogy vannak-e az általános URL-címek [kis-és nagybetűket](https://www.w3.org/TR/WD-html40-970708/htmlweb.html). Tekintse meg, ha az összes érdemes `404` történt kisbetűvel írt URL-címek esetében. Több a kérés a tenantnév-gyűjtemény által az ASP.Net Web SDK-olvashat a [blogbejegyzés](https://apmtips.com/blog/2015/02/23/request-name-and-url/).

Maximális hossz: 1024 karakternél

## <a name="id"></a>ID (Azonosító)

Egy kérelem hívás példány azonosítója. Kérelem és egyéb telemetriát elemek között használt. Lehet, hogy globálisan egyedi azonosítója. További információkért lásd: [korrelációs](../../azure-monitor/app/correlation.md) lapot.

Maximális hossz: 128 karakter hosszú lehet

## <a name="url"></a>URL-cím

Kérelem URL-címe és az összes lekérdezési karakterlánc paramétert.

Maximális hossz: 2048 karakternél

## <a name="source"></a>Forrás

A kérelem forrását. Példák a kialakítási kulcsot a hívó vagy a hívó ip-címét. További információkért lásd: [korrelációs](../../azure-monitor/app/correlation.md) lapot.

Maximális hossz: 1024 karakternél

## <a name="duration"></a>Időtartam

Kérelem időtartama a formátumban: `DD.HH:MM:SS.MMMMMM`. Pozitív, és kisebbnek kell lennie, mint `1000` nap. Ez a mező megadása kötelező, a kérelmek telemetriai adatai jelenti. a művelet kezdete és vége.

## <a name="response-code"></a>Válaszkód

Egy kérelem végrehajtásának eredménye. HTTP-állapotkódot a HTTP-kéréseket. Ez lehet `HRESULT` más kéréstípusok érték vagy a kivétel típusát.

Maximális hossz: 1024 karakternél

## <a name="success"></a>Sikeres

A sikeres vagy sikertelen hívás megjelölése. Ezt a mezőt kötelező kitölteni. Ha nincs beállítva az explicit módon `false` -kérelem sikeres számít. Ez az érték `false` Ha művelet kivétel által megszakított vagy a következő eredmény hibakódot adta vissza.

A webalkalmazások esetén az Application Insights meghatározni egy kérelmet, a sikeres a válaszkód esetén kevesebb mint `400` vagy egyenlő `401`. Azonban előfordulhatnak olyan esetek, amikor az alapértelmezett leképezés nem egyezik a kérelem a szemantikai. Válaszkód `404` utalhat a "nincs rekordok" rendszeres folyamat része lehet. Azt is jelentheti egy megszakadt hivatkozás. A hibás hivatkozást talál még akkor is Megvalósíthat az összetettebb logika. Csak akkor, ha a hivatkozások URL-cím hivatkozó elemzésével ugyanazon a helyen található kódhibáiként jelölheti meg hibás hivatkozást talál. Vagy hibák során érhető el, amely a vállalat mobilalkalmazás megjelölni. Hasonlóképpen `301` és `302` azt jelzi, hogy a hiba, amikor az ügyfél, amely nem támogatja az átirányítási érik el.

Részlegesen elfogadta a tartalom `206` előfordulhat, hogy egy átfogó kérelem sikertelenségét jelzik. Például az Application Insights-végpont telemetriai kötegelt egyetlen kérést kap. Adja vissza, `206` mikor a kötegben lévő egyes elemek nem sikerült feldolgozni. Növekvő mértékű `206` , meg kell vizsgálni kapcsolatos problémát jelez. Hasonló a logika vonatkozik `207` több állapota, ahol sikeres lehet, hogy külön válaszkódok legrosszabb.

Tudjon meg több a kérés eredménye kód és az állapotkódot az [blogbejegyzés](https://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mérések

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>További lépések

- [A kérelem egyéni telemetriát írhat](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)
- Lásd: [adatmodell](data-model.md) Application Insights és modellhez.
- Ismerje meg, hogyan [konfigurálhatja az ASP.NET Core](../../azure-monitor/app/asp-net.md) alkalmazáshoz az Application insights segítségével.
- Tekintse meg [platformok](../../azure-monitor/app/platforms.md) Application Insights által támogatott.
