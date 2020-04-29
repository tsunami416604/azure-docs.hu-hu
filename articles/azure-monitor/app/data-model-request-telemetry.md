---
title: Adatmodell a kérelem telemetria – Azure Application Insights
description: Application Insights adatmodell a kérelem telemetria
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: d8a28063bf6780c3cace4ead81e289779b95eb9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671902"
---
# <a name="request-telemetry-application-insights-data-model"></a>Kérelem telemetria: Application Insights adatmodell

A kérelem telemetria eleme ( [Application Insights](../../azure-monitor/app/app-insights-overview.md)) az alkalmazás külső kérelme által aktivált végrehajtás logikai sorozatot jelöli. Minden kérelem végrehajtását egyedi `ID` azonosítja, és `url` az összes végrehajtási paramétert tartalmazza. A kérelmeket logikusan `name` csoportosíthatja, és `source` megadhatja a kérést. A kód végrehajtása a `success` vagy `fail` a szolgáltatáshoz vezethet. `duration` A sikeres és a sikertelen végrehajtás is elvégezhető a `resultCode`további csoportosításával. A kérés telemetria megadott kezdési ideje.

A telemetria kérése támogatja a standard bővíthetőségi `properties` modellt `measurements`a Custom és a használatával.

## <a name="name"></a>Name (Név)

A kérelem neve a kérelem feldolgozásához a kód elérési útját jelöli. Alacsony kardinális érték a kérelmek jobb csoportosításának lehetővé tételéhez. HTTP-kérelmek esetén a a HTTP-metódust és az URL `GET /values/{id}` -cím elérési útját jelöli, mint a tényleges `id` érték nélkül.

Application Insights web SDK a "as is" nevű kérelem nevét küldi el a Letter eset tekintetében. A felhasználói felületen való csoportosítás megkülönbözteti a `GET /Home/Index` kis-és nagybetűket, ezért a rendszer attól `GET /home/INDEX` függetlenül számítja, hogy az adott vezérlő és a művelet végrehajtása gyakran azonos-e. Ennek az az oka, hogy az URL-címek általában [megkülönböztetik a kis-és nagybetűket](https://www.w3.org/TR/WD-html40-970708/htmlweb.html). Érdemes megtekinteni `404` , hogy az URL-címek nagybetűvel lettek-e beírva. A ASP.NET web SDK-val kapcsolatos további információk a kérelmek neve gyűjteményben olvashatók a [blogbejegyzésben](https://apmtips.com/blog/2015/02/23/request-name-and-url/).

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

Kérelem időtartama (formátum) `DD.HH:MM:SS.MMMMMM`:. Értéke csak pozitív és nem `1000` lehet nap. Erre a mezőre azért van szükség, mert a Request telemetria a művelet kezdetét és végét jelöli.

## <a name="response-code"></a>Válasz kódja

A kérelem végrehajtásának eredménye. A HTTP-kérelmek HTTP-állapotkódot. Más típusú kérelmek `HRESULT` esetében az érték vagy a kivétel típusa lehet.

Maximális hossz: 1024 karakter

## <a name="success"></a>Sikeres

A sikeres vagy sikertelen hívás megjelölése. A mező kitöltése kötelező. Ha nincs explicit módon beállítva a `false` – a kérelem sikeresnek tekintendő. Állítsa be ezt az `false` értéket arra az esetre, ha a művelet kivétel miatt megszakadt, vagy a hiba eredményének kódját adta vissza.

A webalkalmazások esetében Application Insights sikeres kérést adjon meg, ha a válasz kódja kisebb `400` vagy egyenlő. `401` Vannak azonban olyan esetek, amikor ez az alapértelmezett leképezés nem egyezik az alkalmazás szemantikai állapotával. A válasz `404` kódja a "No Records" kifejezésre utalhat, amely a normál folyamat része lehet. Emellett hibás hivatkozást is jelezhet. A hibás hivatkozások esetében még összetettebb logika is megvalósítható. A hibás hivatkozásokat csak akkor lehet hibákként megjelölni, ha a hivatkozások ugyanazon a helyen találhatók, a hivatkozó URL-cím elemzésével. Vagy megjelölheti őket meghibásodásként a vállalat mobil alkalmazásából való hozzáféréskor. Hasonlóképpen `301` , `302` és azt jelzi, hogy hiba történt az átirányítást nem támogató ügyféltől való hozzáféréskor.

A részlegesen `206` elfogadott tartalom egy általános kérelem meghibásodását jelezhetik. A Application Insights végpont például egyetlen kérelemként fogadja a telemetria elemek kötegét. A függvény `206` akkor adja vissza, ha a köteg egyes elemeit nem sikerült feldolgozni. A növekvő arány `206` a vizsgálandó problémára utal. Hasonló logika vonatkozik arra `207` a többszörös állapotra, ahol a siker lehet az eltérő válasz kódokhoz tartozó legrosszabb érték.

További információ: kérelem eredményének kódja és állapotkód a [blogbejegyzésben](https://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mérések

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>További lépések

- [Egyéni kérelem telemetria írása](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)
- Lásd: [adatmodell](data-model.md) Application Insights típusokhoz és adatmodellekhez.
- Megtudhatja, hogyan [konfigurálhat ASP.net Core](../../azure-monitor/app/asp-net.md) alkalmazást Application Insights használatával.
- Tekintse meg Application Insights által támogatott [platformokat](../../azure-monitor/app/platforms.md) .
