---
title: A betöltési és összeolvasztási szabályok közelgő változásai a Azure Time Series Insightsban | Microsoft Docs
description: Betöltési szabály módosításai
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: lyhughes
ms.openlocfilehash: 067244aa40256e3cc76239343790974bc3c06481
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85919034"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>A JSON-összeolvasztási és-Escape-szabályok közelgő módosításai az új környezetekhez

Ezek a módosítások csak az *új* Azure Time Series Insights utólagos elszámolású (TB) környezetekre lesznek alkalmazva. Ezek a módosítások nem vonatkoznak a standard (S) SKU-környezetekre.

A Azure Time Series Insights-környezet dinamikusan létrehozza a tárolási oszlopokat, az elnevezési konvenciók egy adott halmazát követve. Egy esemény betöltése esetén a rendszer a JSON-adattartalomra és a tulajdonságok nevére alkalmazza a szabályok halmazát. A JSON-adatkészletek és az azok tárolására szolgáló változások az új Azure Time Series Insights utólagos elszámolású környezetekben lépnek életbe. július 2020. Ez a változás a következő esetekben van hatással:

* Ha a JSON-adattartalom beágyazott objektumokat tartalmaz
*  Ha a JSON-adattartalom tömböket tartalmaz
*  Ha a következő négy speciális karakter valamelyikét használja egy JSON-tulajdonság nevében: [\. '
*  Ha egy vagy több terminálszolgáltatási azonosító tulajdonsága beágyazott objektumon belül van.

Ha új környezetet hoz létre, és egy vagy több fenti eset az eseményre vonatkozó adattartalomra vonatkozik, akkor az adatok összevonása és tárolása másképp történik. Az alábbiakban a változások összegzése látható:

| Aktuális szabály | Új szabály | Példa JSON-ra | Előző oszlop neve | Új oszlop neve
|---|---| ---| ---|  ---|
| A beágyazott JSON egy aláhúzással van lelapulva, mint a határoló |A beágyazott JSON-t a lehatárolóval eltelt idő alapján lapított  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| A speciális karakterek nincsenek megszökni | A speciális karaktereket tartalmazó JSON-tulajdonságok neve. [\ és "a (z) [' és '] használatával kerül megmenekülésre. A (z) ["és"] keretben további Escape-értékekkel és fordított perjelekkel rendelkezik. A rendszer egy idézőjelet fog írni, \' és egy fordított perjelet fog írni\\\  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` | 
| A primitívek tömbje sztringként van tárolva | Az egyszerű típusok tömbjét dinamikus típusként tárolja a rendszer  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Az objektumok tömbje mindig össze van egyengetve, így több esemény is létrehozható. | Ha a tömbben lévő objektumok nem rendelkeznek a terminálszolgáltatási AZONOSÍTÓval vagy az időbélyegző-vel (IES), az objektumok tömbje a teljes dinamikus típusként tárolódik. | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Új környezetek ajánlott módosításai

#### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Ha a terminálszolgáltatási azonosító és/vagy timestamp tulajdonság egy objektumon belül van beágyazva:

*  Minden új központi telepítésnek meg kell egyeznie az új betöltési szabályokkal. Ha például a TS-AZONOSÍTÓval rendelkezik, `telemetry_tagId` frissítenie kell az ARM-sablonokat, vagy automatikusan telepítenie kell `telemetry.tagId` a parancsfájlokat a környezethez tartozó terminálszolgáltatási azonosítóként való konfiguráláshoz. Ez a módosítás a beágyazott JSON-beli eseményforrás-időbélyegek esetében is szükséges.

 #### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Ha a hasznos adatok beágyazott JSON-vagy speciális karaktereket tartalmaznak, és automatizálja az [idősorozat-modell](.\time-series-insights-update-tsm.md) változó kifejezéseit.

*  Frissítse a [TypesBatchPut](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch#typesbatchput) végrehajtó ügyfelét az új betöltési szabályoknak megfelelően. Például egy korábbi [Idősorozat-kifejezést](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) frissíteni kell az `"value": {"tsx": "$event.series_value.Double"}` alábbi lehetőségek egyikére:
    * `"value": {"tsx": "$event.series.value.Double"}`
    * `"value": {"tsx": "$event['series']['value'].Double"}`



## <a name="next-steps"></a>További lépések

- [A hosszú adattípus támogatásának](./time-series-insights-long-data-type.md)beolvasása.

- Olvassa el [Azure Time Series Insights előnézeti tárolót és a bejövő](./time-series-insights-update-storage-ingress.md)forgalmat.

