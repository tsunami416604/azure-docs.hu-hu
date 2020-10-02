---
title: Metrikák Advisor – gyakori kérdések
titleSuffix: Azure Cognitive Services
description: A metrikák tanácsadó szolgáltatásával kapcsolatos gyakori kérdések.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: aahi
ms.openlocfilehash: e4a75bdd6147ee2189660c37062c5bec9d55d512
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631739"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>Metrikák Advisor – gyakori kérdések

### <a name="what-is-the-cost-of-my-instance"></a>Mi a példányom díja?

A példány használata jelenleg nem jár az előzetes verzióban.

### <a name="why-is-the-demo-website-readonly"></a>Miért írásvédett a bemutató webhelye?

A [bemutató webhelye](https://anomaly-detector.azurewebsites.net/) nyilvánosan elérhető. Ez a példány írásvédett, hogy megakadályozza az adatok véletlen feltöltését.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>Miért nem hozható létre az erőforrás? A "díjszabási szintje" nem érhető el, és azt mondja, hogy "már létrehozott 1 S0 ehhez az előfizetéshez"?

:::image type="content" source="media/pricing.png" alt-text="Üzenet, ha már létezik egy F0-erőforrás":::

A nyilvános előzetes verzióban a metrikai tanácsadó csak egy példánya hozható létre egy előfizetésben, egy régióban.

Ha már van olyan példánya, amely ugyanabban a régióban lett létrehozva ugyanazzal az előfizetéssel, akkor egy másik régiót vagy egy másik előfizetést is kipróbálhat egy új példány létrehozásához. Meglévő példányt is törölhet, hogy újat hozzon létre.

Ha már törölte a meglévő példányt, de a hiba továbbra is fennáll, várjon körülbelül 20 perccel az erőforrás törlése után, mielőtt új példányt hozna létre.

## <a name="basic-concepts"></a>Alapfogalmak

### <a name="what-is-multi-dimensional-time-series-data"></a>Mi az a többdimenziós idősoros adatsorozat?

Tekintse meg a [többdimenziós metrika](glossary.md#multi-dimensional-metric)  definícióját a szószedetben.

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>Mennyi adat szükséges ahhoz, hogy a metrikai tanácsadó elindítsa a anomáliák észlelését?

Legalább egy adatpontnál az anomáliák észlelése is aktiválható. Ez azonban nem teszi lehetővé a legjobb pontosságot. A szolgáltatás azt feltételezi, hogy az előző adatpontok ablaka az adatcsatorna létrehozása során "Kitöltés-hézag" szabályként megadott értéket használja.

Azt javasoljuk, hogy az észlelést megelőzően néhány adattal ellátott időbélyeget kelljen használni.
Az adatai részletessége alapján az ajánlott adatmennyiség az alábbiak szerint változik.

| Részletesség | Ajánlott adatmennyiség észleléshez |
| ----------- | ------------------------------------- |
| Kevesebb, mint 5 perc | 4 napos adatmennyiség |
| 5 perc – 1 nap | 28 nap |
| Több mint 1 nap, 31 nap | 4 év |
| 31 napnál hosszabb | 48 éves adatmennyiség |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>Miért nem észlelhetők a metrikák tanácsadói a múltbeli adatokból származó rendellenességek?

A metrikai tanácsadó az élő adatfolyam-adatok észlelésére szolgál. A szolgáltatás által visszaadott visszamenőleges adatmennyiség maximális hossza korlátozott, és a rendszer a anomáliák észlelését fogja futtatni. Ez azt jelenti, hogy csak egy adott legkorábbi időbélyegzőt követő adatpontok lesznek a anomáliák észlelési eredményei. A legkorábbi időbélyeg az adatok részletességének függvénye.

Az adatai részletessége alapján a rendellenességek észlelését eredményező korábbi adatmennyiségek hossza az alábbi.

| Részletesség | Az adatanomáliák észleléséhez tartozó korábbi adatmennyiség maximális hossza |
| ----------- | ------------------------------------- |
| Kevesebb, mint 5 perc | Bevezetési idő – 13 óra |
| 5 perc vagy kevesebb, mint 1 óra | Bevezetési idő – 4 nap  |
| 1 órával kevesebb, mint 1 nap | Bevezetési idő – 14 nap  |
| 1 nap | Bevezetési idő – 28 nap  |
| Több mint 1 nap, kevesebb mint 31 nap | Bevezetési idő – 2 év  |
| 31 napnál hosszabb | Bevezetési idő – 24 év   |

### <a name="more-concepts-and-technical-terms"></a>További fogalmak és technikai feltételek

További információkat a [szószedetben](glossary.md) talál.

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>Hogyan az adataik betöltéséhez érvényes lekérdezést írni?  

Ahhoz, hogy a metrikai tanácsadó adatokat tartalmazzon, létre kell hoznia egy lekérdezést, amely az adatok dimenzióit egyetlen időbélyegen adja vissza. A metrikai tanácsadó többször is futtatja ezt a lekérdezést az adatoknak az egyes időbélyegekről való lekéréséhez. 

Vegye figyelembe, hogy a lekérdezésnek az egyes dimenziók kombinációinak legfeljebb egy rekordjában kell visszaadnia egy adott időbélyegzőn. Minden visszaadott rekordnak ugyanazzal az időbélyegzővel kell rendelkeznie. A lekérdezés nem ad vissza duplikált rekordot.

Tegyük fel például, hogy az alábbi lekérdezést hozza létre napi metrika esetén: 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

Ügyeljen arra, hogy az idősorozat helyes részletességét használja. Óradíjas metrika esetén a következőt kell használnia: 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

Vegye figyelembe, hogy ezek a lekérdezések csak egyetlen időbélyegen adják vissza az adatokat, és tartalmazzák a metrikai tanácsadó által betöltött összes dimenzió kombinációt. 

:::image type="content" source="media/query-result.png" alt-text="Üzenet, ha már létezik egy F0-erőforrás" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>Hogyan észlelni a tüskéket & a dipsot rendellenességként?

Ha a rögzített küszöbértékek előre vannak megadva, akkor a "Hard küszöbérték" manuálisan állítható be a [anomáliák észlelési konfigurációjában](how-tos/configure-metrics.md#anomaly-detection-methods).
Ha nincsenek küszöbértékek, használhatja az AI által működtetett "intelligens észlelést". A részletekért tekintse meg [az észlelési konfiguráció finomhangolása](how-tos/configure-metrics.md#tune-the-detecting-configuration) című témakört.

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>Hogyan a rendszeres (szezonális) mintákkal való megfelelőség észlelését rendellenességekként?

Az "intelligens észlelés" képes megtanulni az Adatmintázatot, beleértve az idényjellegű mintákat is. Ezt követően észleli azokat az adatpontokat, amelyek nem felelnek meg a reguláris mintáknak rendellenességeknek. A részletekért tekintse meg [az észlelési konfiguráció finomhangolása](how-tos/configure-metrics.md#tune-the-detecting-configuration) című témakört.

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>Hogyan felderíti az egyszerű vonalakat?

Ha az adatai általában meglehetősen instabilak, és nagy mértékben ingadoznak, és ha riasztást szeretne kapni, ha túl stabil, vagy akár egy vonalra vált, akkor a "változási küszöb" beállítás beállítható úgy, hogy az ilyen adatpontok észlelése túl kicsi legyen.
A részletekért tekintse meg a [anomáliák észlelésének konfigurációit](how-tos/configure-metrics.md#anomaly-detection-methods) .

## <a name="next-steps"></a>Következő lépések
- [A metrikai tanácsadó áttekintése](overview.md)
- [A bemutató webhely kipróbálása](quickstarts/explore-demo.md)
- [A webes portál használata](quickstarts/web-portal.md)