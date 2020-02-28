---
title: Adatmodell a kérelem telemetria – Azure Application Insights
description: Application Insights adatmodell a kérelem telemetria
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: d8a28063bf6780c3cace4ead81e289779b95eb9a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671902"
---
# <a name="request-telemetry-application-insights-data-model"></a>Kérelem telemetria: Application Insights adatmodell

A kérelem telemetria eleme ( [Application Insights](../../azure-monitor/app/app-insights-overview.md)) az alkalmazás külső kérelme által aktivált végrehajtás logikai sorozatot jelöli. Minden kérelem végrehajtását egyedi `ID` és az összes végrehajtási paramétert tartalmazó `url` azonosítja. A kérelmeket logikai `name` alapján csoportosíthatja, és megadhatja a kérelem `source`ét. A kód végrehajtása `success` vagy `fail` eredményezhet, és egy bizonyos `duration`. A sikeres és a sikertelen végrehajtás is a `resultCode`szerint csoportosítható. A kérés telemetria megadott kezdési ideje.

A telemetria kérése az egyéni `properties` és `measurements`használatával támogatja a standard bővíthetőségi modellt.

## <a name="name"></a>Name (Név)

A kérelem neve a kérelem feldolgozásához a kód elérési útját jelöli. Alacsony kardinális érték a kérelmek jobb csoportosításának lehetővé tételéhez. HTTP-kérelmek esetén a a HTTP-metódust és az URL-cím elérési útját adja meg, például `GET /values/{id}` a tényleges `id` érték nélkül.

Application Insights web SDK a "as is" nevű kérelem nevét küldi el a Letter eset tekintetében. A felhasználói felületen való csoportosítás megkülönbözteti a kis-és nagybetűket, így a `GET /Home/Index` a `GET /home/INDEX`tól függetlenül számít, bár gyakran ugyanazt a vezérlőt és műveletet hajtják végre. Ennek az az oka, hogy az URL-címek általában [megkülönböztetik a kis-és nagybetűket](https://www.w3.org/TR/WD-html40-970708/htmlweb.html). Érdemes megtekinteni, hogy az összes `404` a nagybetűvel beírt URL-címeknél történt-e. A ASP.NET web SDK-val kapcsolatos további információk a kérelmek neve gyűjteményben olvashatók a [blogbejegyzésben](https://apmtips.com/blog/2015/02/23/request-name-and-url/).

Maximális hossz: 1024 karakter

## <a name="id"></a>ID (Azonosító)

Egy kérelem hívási példányának azonosítója. A kérelem és az egyéb telemetria elemek közötti korrelációhoz használatos. Az AZONOSÍTÓnak globálisan egyedinek kell lennie. További információ: [korrelációs](../../azure-monitor/app/correlation.md) oldal.

Maximális hossz: 128 karakter

## <a name="url"></a>URL-cím

Kérelem URL-címe az összes lekérdezési karakterlánc paraméterrel.

Maximális hossz: 2048 karakter

## <a name="source"></a>Forrás

A kérelem forrása. Ilyenek például a hívó rendszerállapot-kulcsa vagy a hívó IP-címe. További információ: [korrelációs](../../azure-monitor/app/correlation.md) oldal.

Maximális hossz: 1024 karakter

## <a name="duration"></a>Időtartam

Kérelem időtartama (formátum: `DD.HH:MM:SS.MMMMMM`). Nem lehet pozitív, és `1000` napnál rövidebbnek kell lennie. Erre a mezőre azért van szükség, mert a Request telemetria a művelet kezdetét és végét jelöli.

## <a name="response-code"></a>Válasz kódja

A kérelem végrehajtásának eredménye. A HTTP-kérelmek HTTP-állapotkódot. Előfordulhat, hogy más típusú kérelmek esetében `HRESULT` érték vagy kivétel típusú.

Maximális hossz: 1024 karakter

## <a name="success"></a>Sikeres

A sikeres vagy sikertelen hívás megjelölése. A mező kitöltése kötelező. Ha nincs explicit módon beállítva a `false`re, a rendszer sikeresnek tekinti a kérelmet. Állítsa be ezt az értéket úgy, hogy `false`, ha a művelet kivétel miatt megszakadt, vagy a hiba eredményének kódját adta vissza.

A webalkalmazások esetében Application Insights sikeres kérést adjon meg, ha a válasz kódja kisebb, mint `400` vagy egyenlő `401`. Vannak azonban olyan esetek, amikor ez az alapértelmezett leképezés nem egyezik az alkalmazás szemantikai állapotával. A válasz kódja `404` a "No Records" kifejezésre utalhat, amely a normál folyamat része lehet. Emellett hibás hivatkozást is jelezhet. A hibás hivatkozások esetében még összetettebb logika is megvalósítható. A hibás hivatkozásokat csak akkor lehet hibákként megjelölni, ha a hivatkozások ugyanazon a helyen találhatók, a hivatkozó URL-cím elemzésével. Vagy megjelölheti őket meghibásodásként a vállalat mobil alkalmazásából való hozzáféréskor. Hasonlóképpen `301` és `302` az átirányítást nem támogató ügyféltől való hozzáférés hibáját jelzi.

A részben elfogadott tartalom `206` a teljes kérelem meghibásodását jelezhetik. A Application Insights végpont például egyetlen kérelemként fogadja a telemetria elemek kötegét. `206` ad vissza, ha a köteg egyes elemeit nem sikerült feldolgozni. A `206` növekvő aránya azt a problémát jelzi, amelyet meg kell vizsgálni. A hasonló logika `207` többállapotú, ahol a siker lehet a különböző válasz-kódokhoz tartozó legrosszabb.

További információ: kérelem eredményének kódja és állapotkód a [blogbejegyzésben](https://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mérések

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Következő lépések

- [Egyéni kérelem telemetria írása](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)
- Lásd: [adatmodell](data-model.md) Application Insights típusokhoz és adatmodellekhez.
- Megtudhatja, hogyan [konfigurálhat ASP.net Core](../../azure-monitor/app/asp-net.md) alkalmazást Application Insights használatával.
- Tekintse meg Application Insights által támogatott [platformokat](../../azure-monitor/app/platforms.md) .
