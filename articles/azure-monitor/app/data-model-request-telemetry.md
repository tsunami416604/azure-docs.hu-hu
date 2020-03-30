---
title: Adatmodell a kérelemtelemetriához – Azure Application Insights
description: Application Insights-adatmodell a kérelem telemetriai adataihoz
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: d8a28063bf6780c3cace4ead81e289779b95eb9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671902"
---
# <a name="request-telemetry-application-insights-data-model"></a>Telemetriai adatok kérése: Application Insights-adatmodell

A kérelem telemetriai elem (az [Application Insights)](../../azure-monitor/app/app-insights-overview.md)az alkalmazás külső kérése által aktivált végrehajtási logikai sorrendet jelöli. Minden kérelem végrehajtása egyedi `ID` `url` azonosító, amely tartalmazza az összes végrehajtási paramétert. A kérelmeket logikai `name` szerint `source` csoportosíthatja, és meghatározhatja a kérelemet. A kódvégrehajtása `success` bizonyos `fail` . `duration` Mind a sikeres, mind a sikertelen `resultCode`végrehajtást tovább csoportosíthatja a. A borítékszinten definiált kérelemtelemetria ia.

Kérelem telemetriai támogatja a szabványos bővíthetőségi modell egyéni `properties` és. `measurements`

## <a name="name"></a>Név

A kérelem neve a kérelem feldolgozásához szükséges kódelérési utat jelöli. Alacsony számossági érték a kérelmek jobb csoportosításának lehetővé teszi. Http-kérelmek esetén a HTTP-metódust és `GET /values/{id}` az `id` URL-elérési út sablont jelöli, például a tényleges érték nélkül.

Application Insights web SDK küld kérelem neve "ahogy van" tekintetében a levél esetében. A felhasználói felületcsoportosítása megkülönbözteti `GET /Home/Index` a kis- `GET /home/INDEX` és nagybetűket, így a rendszer elkülönítve számolja, még akkor is, ha gyakran ugyanazt a vezérlőt és műveletvégrehajtást eredményezik. Ennek az az oka, hogy az URL-ek általában [a kis- és nagybetűket.](https://www.w3.org/TR/WD-html40-970708/htmlweb.html) Érdemes megnézni, hogy `404` minden történt-e a nagybetűvel beírt URL-eknél. A ASP.NET Web SDK által kért névgyűjtést a [blogbejegyzésben olvashatja](https://apmtips.com/blog/2015/02/23/request-name-and-url/)el.

Maximális hossz: 1024 karakter

## <a name="id"></a>ID (Azonosító)

A kérelemhívás-példány azonosítója. A kérelem és más telemetriai elemek közötti korrelációra szolgál. Az azonosítónak globálisan egyedinek kell lennie. További információ: [Korrelációs](../../azure-monitor/app/correlation.md) oldal.

Maximális hossz: 128 karakter

## <a name="url"></a>URL-cím

Kérelem URL-címe az összes lekérdezési karakterlánc paraméterrel.

Maximális hossz: 2048 karakter

## <a name="source"></a>Forrás

A kérelem forrása. Ilyenek például a hívó műszerezési kulcsa vagy a hívó IP-címe. További információ: [Korrelációs](../../azure-monitor/app/correlation.md) oldal.

Maximális hossz: 1024 karakter

## <a name="duration"></a>Időtartam

A kérelem időtartama `DD.HH:MM:SS.MMMMMM`formátumban: . . Pozitívnak és kevesebbnek kell lennie, mint `1000` a napok. Ez a mező szükséges, mivel a kérelem telemetriai adatok jelöli a műveletet az elején és a végén.

## <a name="response-code"></a>Válaszkód

A kérelem végrehajtásának eredménye. HTTP-állapotkód a HTTP-kérelmekhez. Más kérelemtípusok esetében lehet `HRESULT` érték- vagy kivételtípus.

Maximális hossz: 1024 karakter

## <a name="success"></a>Sikeres

A sikeres vagy sikertelen hívás jelzése. A mező kitöltése kötelező. Ha nincs kifejezetten `false` beállítva - a kérelem sikeresnek minősül. Állítsa ezt `false` az értéket, ha a műveletet kivétel szakította meg, vagy hibaeredmény-kódot adott vissza.

A webes alkalmazások esetében az Application Insights egy kérelmet `400` sikeresként `401`definiál, ha a válaszkód kisebb vagy egyenlő. Vannak azonban olyan esetek, amikor ez az alapértelmezett leképezés nem egyezik meg az alkalmazás szemantikai. A `404` válaszkód jelezheti a "nincs rekord",, amely része lehet a rendszeres áramlásnak. Azt is jelezheti, hogy egy törött link. A hibás hivatkozások, akkor is végre fejlettebb logika. A hibás hivatkozásokat csak akkor jelölheti meg hibaként, ha ezek a hivatkozások ugyanazon a webhelyen találhatók az url referrer elemzésével. Vagy jelölje meg őket hibaként, amikor a vállalat mobilalkalmazásából érik el őket. `301` Hasonlóképpen, `302` és azt jelzi, hiba, amikor az ügyfél, amely nem támogatja az átirányítás.

A részben `206` elfogadott tartalom egy általános kérelem sikertelenségére utalhat. Például az Application Insights-végpont egyetlen kérelemként telemetriai elemek kötegét kapja. Akkor `206` ad vissza, ha a köteg egyes cikkei feldolgozása nem sikerült. A növekvő `206` arány olyan problémát jelez, amelyet ki kell vizsgálni. Hasonló logika `207` vonatkozik a multi-status, ahol a siker lehet a legrosszabb a külön válaszkódok.

Elolvashatja tovább kérésre eredmény kód és állapotkód a [blogbejegyzést](https://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mérések

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>További lépések

- [Egyéni kérelem telemetria írása](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)
- Tekintse meg az Application Insights-típusok és adatmodell [adatmodelljét.](data-model.md)
- Ismerje meg, hogyan [konfigurálhatja ASP.NET Core](../../azure-monitor/app/asp-net.md) alkalmazás az Application Insights.
- Tekintse meg az Application Insights által támogatott [platformokat.](../../azure-monitor/app/platforms.md)
