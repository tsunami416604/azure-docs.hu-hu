---
title: "Az Azure Application Insights Telemetriai adatokat a modell - Telemetriai kérelem |} Microsoft Docs"
description: "Application Insights – kéréstelemetria tartozó adatmodell"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: bwren
ms.openlocfilehash: 8e782e45b706cadec66e7404dd9abc2e01dea917
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="request-telemetry-application-insights-data-model"></a>Telemetriai kérelem: az Application Insights adatmodell

A kérelem telemetriai elemet (a [Application Insights](app-insights-overview.md)) az alkalmazás olyan külső kérés végrehajtási logikai sorozatát jelenti. Minden kérelem végrehajtása azonosítja egyedi `ID` és `url` tartalmazó összes végrehajtási paramétert. Logikai szerint csoportosíthatja kérelmek `name` , és adja meg a `source` a kérelem. Kód végrehajtása eredményezhet `success` vagy `fail` , és egy bizonyos `duration`. A sikeres és Sikertelen végrehajtások csoportosíthatók további korlátozásokat `resultCode`. Kezdő időpont a boríték szinten definiált – kéréstelemetria.

Telemetria támogatja a szabványos bővíthetőségi modell egyéni kérelem `properties` és `measurements`.

## <a name="name"></a>Név

A kérés neve kódútvonala. a kérelem feldolgozásának jelöli. Alacsony számossága értéket kérelmek jobban csoportosítása. A HTTP-kérelmek azt jelöli, a HTTP-metódus és URL-cím elérési út sablont, például `GET /values/{id}` nélkül a tényleges `id` érték.

Application Insights webes SDK kérelem neve "adott állapotban" elküldi a nagybetűk ben elérhető. A felhasználói felület a csoportosítás akkor kis-és nagybetűket, `GET /Home/Index` a külön-külön számolt `GET /home/INDEX` annak ellenére, hogy gyakran ennek eredményeképpen az ugyanazon vezérlő és a művelet végrehajtása. A, amelyek oka, hogy vannak-e általában URL-címek [kis-és nagybetűket](http://www.w3.org/TR/WD-html40-970708/htmlweb.html). Előfordulhat, hogy szeretné látni, ha az összes `404` történt az URL-adta-e írni. További a kérelem tenantnév-gyűjtemény által az ASP.Net webes SDK-t el tudja olvasni a [blogbejegyzés](http://apmtips.com/blog/2015/02/23/request-name-and-url/).

Maximális hossz: 1024 karakter hosszú lehet

## <a name="id"></a>ID (Azonosító)

A kérelem hívás példány azonosítója. Kérelem és egyéb telemetriai adatok közötti korreláció használatos. Kell, hogy globálisan egyedi azonosítója. További információkért lásd: [korrelációs](application-insights-correlation.md) lap.

Maximális hossz: 128 karakter hosszú lehet

## <a name="url"></a>URL-cím

Kérelem URL-CÍMÉT és az összes lekérdezési karakterlánc paramétert.

Maximális hossz: 2048 karakter

## <a name="source"></a>Forrás

A kérelem forrását. Többek között a rendszerállapot-kulcsot a hívó vagy a hívónak IP-címét. További információkért lásd: [korrelációs](application-insights-correlation.md) lap.

Maximális hossz: 1024 karakter hosszú lehet

## <a name="duration"></a>Időtartam

Időtartam formátumú kérelem: `DD.HH:MM:SS.MMMMMM`. Pozitív, és kisebbnek kell lennie mint `1000` nap. A mező kitöltése kötelező, mivel – kéréstelemetria jelenti. a művelet kezdete és vége.

## <a name="response-code"></a>Válaszkód

A kérelem végrehajtása eredményét. HTTP-állapotkód: a HTTP-kérelmekre. Lehet, hogy `HRESULT` más kéréstípusok értékre, vagy a kivétel típusát.

Maximális hossz: 1024 karakter hosszú lehet

## <a name="success"></a>Sikeres

Sikeres vagy sikertelen hívás megjelölése. A mező kitöltése kötelező. Ha nincs beállítva az explicit módon `false` -kérés sikeres legyen tekinthető. Ez az érték beállítása `false` Ha művelet kivétel miatt megszakadt vagy a eredmény hibakódot adott vissza.

A webes alkalmazásokhoz az Application Insights határozza meg kérelem sikertelen volt, amikor ez a válaszkód kisebb, mint a `400` vagy annál `401`. Azonban előfordulhatnak olyan esetek, amikor az alapértelmezett leképezés nem felel meg az alkalmazás a szemantikai. A válaszkód `404` jelezheti, hogy "nincs rekordok", amely rendszeres folyamat része lehet. Azt is jelezheti egy megszakadt hivatkozás. A nem működő hivatkozások még összetettebb logikát is létrehozható. Csak akkor, ha ezeket a hivatkozásokat URL-cím hivatkozó elemzésével a ugyanazon a helyen lévő hivatkozások is megjelölése hibák. Vagy azok megjelölése hibák, amikor a vállalat mobilalkalmazás érik el. Hasonlóképpen `301` és `302` az ügyfélről, amely nem támogatja az átirányítási elérésekor hibát jelez.

Részlegesen elfogadta a tartalom `206` utalhat egy általános kérelem sikertelen. Az Application Insights végpont például egyetlen kérelemként kapja meg a kötegelt telemetriai elemek. Azt adja vissza `206` Ha a köteg bizonyos elemek nem dolgozott sikeresen megtörtént. Növekvő mértékű `206` , amelyet akkor kell megvizsgálni hibáját jelzi. Hasonló logika vonatkozik `207` több ahol sikeres lehet, hogy külön válaszkódot legrosszabb állapota.

További a kérelem eredménye elolvashatja és az állapot kód a [blogbejegyzés](http://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mértékek

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Következő lépések

- [Egyéni – kéréstelemetria írása](app-insights-api-custom-events-metrics.md#trackrequest)
- Lásd: [adatmodell](application-insights-data-model.md) Application Insights-típusok és az adatok modell.
- Megtudhatja, hogyan [konfigurálása az ASP.NET Core](app-insights-asp-net.md) alkalmazás az Application insights szolgáltatással.
- Tekintse meg [platformok](app-insights-platforms.md) Application Insights által támogatott.
