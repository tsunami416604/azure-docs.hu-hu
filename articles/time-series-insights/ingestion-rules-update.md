---
title: A betöltési és összeolvasztási szabályok közelgő változásai a Azure Time Series Insights Gen2 | Microsoft Docs
description: Betöltési szabály módosításai
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/12/2020
ms.custom: lyhughes
ms.openlocfilehash: cdf90614e1f766fc37e04a1416081bd35e19b74e
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168201"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>A JSON-összeolvasztási és-Escape-szabályok közelgő módosításai az új környezetekhez

**Ezek a módosítások csak az *újonnan létrehozott* Azure Time Series Insights Gen2-környezetekre lesznek alkalmazva. Ezek a módosítások nem vonatkoznak a Gen1-környezetekre.**

Azure Time Series Insights Gen2-környezete dinamikusan létrehozza a tárolási oszlopokat az elnevezési konvenciók egy adott halmazának követésével. Egy esemény betöltése esetén a rendszer a JSON-adattartalomra és a tulajdonságok nevére alkalmazza a szabályok halmazát. A JSON-adatkészletek és a tárolt elemek tárolásának változásai a 2020 júliusában új Azure Time Series Insights Gen2 környezetekben lépnek életbe. Ez a változás a következő esetekben van hatással:

* Ha a JSON-adattartalom beágyazott objektumokat tartalmaz
* Ha a JSON-adattartalom tömböket tartalmaz
* Ha a következő négy speciális karakter valamelyikét használja egy JSON-tulajdonság nevében: [\. '
* Ha egy vagy több terminálszolgáltatási azonosító tulajdonsága beágyazott objektumon belül van.

Ha új környezetet hoz létre, és egy vagy több fenti eset az eseményre vonatkozó adattartalomra vonatkozik, akkor az adatok összevonása és tárolása másképp történik. Az alábbiakban a változások összegzése látható:

| Aktuális szabály | Új szabály | Példa JSON-ra | Előző oszlop neve | Új oszlop neve
|---|---| ---| ---|  ---|
| A beágyazott JSON egy aláhúzással van lelapulva, mint a határoló |A beágyazott JSON-t a lehatárolóval eltelt idő alapján lapított  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| A speciális karakterek nincsenek megszökni | A speciális karaktereket tartalmazó JSON-tulajdonságok neve. [\ és "a (z) [' és '] használatával kerül megmenekülésre. A (z) ["és"] keretben további Escape-értékekkel és fordított perjelekkel rendelkezik. A rendszer egy idézőjelet fog írni, \' és egy fordított perjelet fog írni\\\  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| A primitívek tömbje sztringként van tárolva | Az egyszerű típusok tömbjét dinamikus típusként tárolja a rendszer  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Az objektumok tömbje mindig össze van egyengetve, így több esemény is létrehozható. | Ha a tömbben lévő objektumok nem rendelkeznek a terminálszolgáltatási AZONOSÍTÓval vagy az időbélyegző-vel (IES), az objektumok tömbje a teljes dinamikus típusként tárolódik. | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Új környezetek ajánlott módosításai

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Ha a terminálszolgáltatási azonosító és/vagy időbélyeg tulajdonsága egy objektumon belül van beágyazva

* Minden új központi telepítésnek meg kell egyeznie az új betöltési szabályokkal. Ha például a TS-AZONOSÍTÓval rendelkezik, `telemetry_tagId` frissítenie kell a Azure Resource Manager (ARM) sablonokat, vagy automatikusan telepítenie kell a parancsfájlokat, hogy `telemetry.tagId` a konfigurálja a környezetet a TS-azonosítóként. Ez a módosítás a beágyazott JSON-beli eseményforrás-időbélyegek esetében is szükséges.

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Ha a hasznos adatok beágyazott JSON-vagy speciális karaktereket tartalmaznak, és automatizálja az [idősorozat-modell](.\time-series-insights-update-tsm.md) változó kifejezéseit.

* Frissítse a [TypesBatchPut](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) végrehajtó ügyfelét az új betöltési szabályoknak megfelelően. Például egy korábbi [Idősorozat-kifejezést](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) frissíteni kell az `"value": {"tsx": "$event.series_value.Double"}` alábbi lehetőségek egyikére:
  * `"value": {"tsx": "$event.series.value.Double"}`
  * `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>További lépések

* Olvassa el [Azure Time Series Insights Gen2-tárolót és a bejövő](./time-series-insights-update-storage-ingress.md)forgalmat.

* További információ az adatlekérdezésről az [Idősorozat lekérdezési API-jai](./concepts-query-overview.md)segítségével.

* További információ az [új Idősorozat-kifejezés szintaxisáról](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax).
