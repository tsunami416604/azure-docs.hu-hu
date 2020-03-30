---
title: A scorecard értelmezése | Microsoft dokumentumok
description: Az Azure Internet Analyzer gyakran feltett kérdések.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a05c704f62d93bdc110d8a5943dd266ca22eb5f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512885"
---
# <a name="interpreting-your-scorecard"></a>A stratégiai mutatószámrendszer értelmezése

A scorecard fül tartalmazza a tesztek összesített és elemzett eredményeit. Minden tesztnek saját scorecardja van. A scorecardok gyors és tartalmas összegzést nyújtanak a mérési eredményekről, hogy adatvezérelt eredményeket nyújtsanak a hálózati követelményeknek. Internet Analyzer gondoskodik az elemzés, amely lehetővé teszi, hogy összpontosítson a döntést.

A scorecard lap az Internet Analyzer erőforrás menüjében található. 


## <a name="filters"></a>Szűrők

* ***Vizsgálat:*** Válassza ki azt a tesztet, amelynek eredményeit meg szeretné tekinteni – minden tesztnek saját scorecardja van. A vizsgálati adatok akkor jelennek meg, ha elegendő adat áll az elemzés befejezéséhez – a legtöbb esetben ennek 24 órán belül kell lennie. 
* ***Időszak & befejezési dátum:*** Naponta három scorecard jön létre – minden scorecard más összesítési időszakot tükröz – az azt megelőző 24 órát (napot), az azt megelőző hét napot (hét), és az azt megelőző 30 napot (hónap). A "Záró dátum" szűrővel válassza ki a megtekinteni kívánt időszak utolsó napját. 
* ***Ország:*** Minden olyan országhoz, ahol végfelhasználók vannak, a rendszer scorecardot hoz létre. A globális szűrő az összes végfelhasználót tartalmazza.

## <a name="measurement-count"></a>Mérési szám

A mérések száma befolyásolja az elemzés megbízhatóságát. Minél magasabb a szám, annál pontosabb az eredmény. A vizsgálatoknak legalább napi 100 mérésre kell irányulniuk. Ha a mérési számok túl alacsonyak, konfigurálja a JavaScript-ügyfelet úgy, hogy gyakrabban hajtsa végre az alkalmazásban. Az A és B végpontok mérési számának nagyon hasonlónak kell lennie, bár kis különbségek várhatók és rendben vannak. Nagy különbségek esetén az eredményeket nem szabad megbízni.

## <a name="percentiles"></a>Percentiles

A késés ezredmásodpercben mérve népszerű mérőszám a forrás és a cél közötti sebesség mérésére az interneten. A késési adatok általában nem oszlanak el (azaz nem követik a "Bell-görbét"), mert nagy látenciaértékek "hosszú farkú" vannak, amelyek a statisztikák, például a számtani középhasználata során eredményeznek ferde eredményeket. Alternatívmegoldásként a percentilisek "disztribúciómentes" módot biztosítanak az adatok elemzésére. Például a medián, vagy50. A 75 százalékos érték azt jelenti, hogy nagyobb, mint az eloszlás ban lévő összes érték 75%-a. Az Internet Analyzer p50, P75 és P95 rövidítésben lévő percentiliseket nevezi.

Az Internet Analyzer percentilisek _mintamérők._ Ez ellentétben áll a valós _népesség metrikus_. Például a napi valós népesség idoszakának medián késleltetése a University of Southern California és a Microsoft hallgatói között az adott nap összes kérésének középértéke. A gyakorlatban az összes kérelem értékének mérése nem praktikus, ezért feltételezzük, hogy egy ésszerűen nagy minta reprezentatív a valódi populációra nézve.

Elemzési célokra a P50 (medián) hasznos a késés eloszlásának várható értékeként. A magasabb százalékos, például a P95-ös, hasznos annak megállapítására, hogy a legrosszabb esetben milyen magas a késés. Ha általánosságban szeretné megérteni az ügyfelek késését, a P50 a megfelelő mérőszám, amelyre összpontosítani szeretne. Ha ön érintett -val megértés előadás részére a a legrosszabbul teljesítő vásárlók, akkor P95 legyen a összpontosít. A P75 a kettő közötti egyensúly.


## <a name="deltas"></a>Delták

A delta az A és B végpontok metrikaértékeinek különbsége. A delta lehet abszolút (pl. 10 ezredmásodperc) vagy relatív (5%).

## <a name="confidence-interval"></a>Megbízhatósági intervallum 

A konfidenciaintervallumok (CI) olyan értékek tartományai, amelyek valószínűsége, hogy tartalmazzák a sokasági metriát, például medián, P75 vagy átlag. A 95%-os CI használatának általános statisztikai konvencióját követjük.

Az Internet Analyzer esetében egy szűk megbízhatósági intervallum jó, mert azt mutatja, hogy a mintametria valószínűleg nagyon közel van a tényleges népességmetriához. A széles megbízhatósági intervallum azt jelenti, hogy kevésbé biztos, hogy a minta metrika tükrözi a valós népesség metrika. A CI javításának legjobb módja a mérési szám növelése.

## <a name="time-series"></a>Idősorok 

Az idősorozat oka, hogy egy metrika hogyan változik az idő múlásával. Az interneten számos időbeli tényező befolyásolja a teljesítményt, például a csúcsforgalmi időszakokat, a hétköznapok on-hétvégés népességkülönbségeit és az ünnepeket.


## <a name="next-steps"></a>További lépések

További információért tekintse meg [az Internet Analyzer áttekintését.](internet-analyzer-overview.md)
