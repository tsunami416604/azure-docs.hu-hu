---
title: Használati irányítópult a kereskedelmi piactér imitorásában a Partnerközpontban
description: Ismerje meg, hogyan érheti el az összes virtuális gép kínál használat és forgalmi díjas számlázási metrikák.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 33762540d14ea51e8325abe9a466007cd7cca748
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262179"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Használati irányítópult a kereskedelmi piactér elemzésében

Ez a cikk a Partnerközpont Használati irányítópultjáról nyújt tájékoztatást. Ez az irányítópult két külön lapon jeleníti meg az összes virtuális gépajánlat-használatot és a forgalmi díjas számlázási metrikákat: virtuális gép használat és forgalmi díjas számlázási használat.

A Használati irányítópult eléréséhez nyissa meg az **[Elemzés irányítópultot](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** a Kereskedelmi piactér csoportban.

>[!NOTE]
> Az elemzési terminológia részletes meghatározását a [Kereskedelmi piactér elemzésével kapcsolatos gyakori kérdések és terminológia](./faq-terminology.md)című témakörben található.

## <a name="usage-dashboard"></a>Használat irányítópult

A használati irányítópult az összes virtuális gép (VM) használatának és forgalmi díjas számlázási használatának metrikáit jelöli. Ezek két külön lapon találhatók: virtuális gép használata és forgalmi díjas számlázási használat.

A Virtuálisgép-használat lapon a következő elemek grafikus ábrázolásai találhatók:

- [Használat összegzése](#usage-summary)
- [Felhasználás földrajzi hely szerint](#usage-by-geography)
- [Az ajánlatok általi használat](#usage-by-offers)
- [Használati trend ajánlatok és suk-ok szerint](#usage-trend-by-offers-and-skus)
- [Használat ajánlattípus szerint](#usage-by-offer-type)
- [Használat virtuális gép mérete szerint](#usage-by-vm-size)
- [Felhasználás értékesítési csatorna szerint](#usage-by-sales-channel)
- [Részletes használati adatok](#detailed-usage-data)

> [!NOTE]
> Az Analytics-jelentések eltérően jelennek meg a Cloud Partner Portal (CPP) és a Partner Center alkalmazásban. A CPP **eladói elemzési adatai** nak van egy megrendelése és használati lapja, amely mind a használatalapú, mind a nem használatalapú ajánlatok adatait jeleníti meg. A Partnerközpontban a használati mutatók egy külön oldalon jelennek meg.

### <a name="usage-summary"></a>Használati adatok összegzése

A használati összegzés tábla az összes megvásárolt ajánlat vevői használati óráit jeleníti meg.

- Normalizált használati órák meghatározása: a használati órák normalizált száma vm magok ([virtuálisgép-magok száma] x [óra nyers használat]). A "SHAREDCORE" néven kijelölt virtuális gépek 1/6 (vagy 0.1666) a [virtuálisgép-magok száma] szorzót használnak.
- A nyers használati órák meghatározása az, hogy mennyi ideig futottak a virtuális gépek az órákban kifejezve.
- A százalékos érték a kiválasztott dátumtartomány ([utolsó havi használat – első havi használat]) használati növekedési változását jelöli.
- A felfelé mutató zöld háromszögek növekedésváltozást jeleznek.
- A lefelé mutató piros háromszög negatív növekedési változást jelez az előző hónaphoz képest.
- A mikrosávos grafikonok havi értékeket jelölnek. Az egyes hónapok értékét úgy jelenítheti meg, hogy a diagram oszlopai fölé viszi az egérmutatót.

### <a name="usage-by-geography"></a>Felhasználás földrajzi hely szerint

A földrajzi hőtérkép **szerinti normalizált használat** megjeleníti az ügyfélország szerint leképezett használati órákat. Az országszín-változás a normalizált használati koncentrációt jelöli. Térjen vissza az eredeti nézethez a **térképen** található otthoni gomb megnyomásával.

### <a name="usage-by-offers"></a>Az ajánlatok általi használat

- Az **ajánlatok kördiagramszerinti normalizált használata** a normalizált használati órák ajánlat szerinti bontását jeleníti meg a kiválasztott dátumtartomány szerint. Az első 5 ajánlat grafikonon jelenik meg, míg a többi a "pihenés mind" kategóriába van csoportosítva.
- A sávdiagram a kiválasztott dátumtartomány havi növekedési trendjét ábrázolja. A hónap oszlopok az adott hónap legmagasabb használati óráival rendelkező ajánlatok használati óráit jelölik. A vonaldiagram a másodlagos Y tengelyen ábrázolt növekedési százalékos trendet ábrázolja.
- A diagram tetején lévő csúszkával jobbról balra görgethet az x tengely mentén, és/vagy fókuszálhat bizonyos adatpontokra.

### <a name="usage-trend-by-offers-and-skus"></a>Használati trend ajánlatok és suk-ok szerint

Ez a diagram az ajánlat kiválasztott sk-jainak normalizált használatának trendjét jeleníti meg. Az ajánlat ranglistáján a legmagasabb 50 ajánlat jelenik meg a legmagasabb kihasználtsul, és használati órák szerint vannak rendezve. A Termékváltozat ranglistáján a kiválasztott ajánlathoz a legmagasabb 50 termékváltozat látható.

### <a name="usage-by-offer-type"></a>Használat ajánlattípus szerint

- Az **ajánlattípus kördiagram szerinti használat** az ajánlat típusának megfelelően rendezi a használatot.
- A legnépszerűbb ajánlatok a táblázatban jelennek meg, a többi ajánlat pedig "Rest All" néven van csoportosítva.
- A **trenddiagram** havi növekedési trendeket jelenít meg. A hónap oszlop az adott hónap ban a legnépszerűbb ajánlattípusok használatát jelöli.

### <a name="usage-by-vm-size"></a>Használat virtuális gép mérete szerint

Ez a diagram az összes ajánlat/sk-ek kiválasztott virtuálisgép-méretek (max. 5) használati trendjét mutatja. Az oszlopdiagram a kiválasztott virtuálisgép-méretek használati óráival van halmozva.

A ranglistán a legnagyobb 50 virtuális gép mérete látható a legmagasabb kihasználtsulva, és használati órák szerint rendezve.

### <a name="usage-by-sales-channel"></a>Felhasználás értékesítési csatorna szerint

- Az értékesítési csatorna kördiagramja szerint történő használat az értékesítési csatorna szerint rendezi a használatot
- A legmagasabb használatú felső értékesítési csatorna jelenik meg a diagramon, és az értékesítési csatorna többi része "Összes pihenés" csoportba van csoportosítva.
- A hónap oszlop az adott hónap legnépszerűbb értékesítési csatornája szerint történő felhasználást jelöli.
- A diagram jellemzői megegyeznek a "Használat ajánlatok" diagramével

### <a name="detailed-usage-data"></a>Részletes használati adatok

A **Használat részletei táblázat** a használat szerint rendezett 1000 legfontosabb használati rekord számozott listáját jeleníti meg.

- A rács minden oszlopa rendezhető.
- Az adatok csv-fájlba nyerhetők ki, ha a rekordok száma kevesebb, mint 1000.
- Ha a rekordok száma több mint 1000, az exportálási adatok aszinkron módon kerülnek egy letöltési oldalra, amely a következő 30 napban lesz elérhető.
- Szűrők et lehet alkalmazni a **részletes használati adatok megjelenítéséhez** csak az adatokat, hogy érdekli. Az adatok szűrhetők ország, értékesítési csatorna, piactéri licenctípus, használati típus, ajánlatnév, ajánlattípus, ingyenes próbaverziók, Piactér-előfizetés-azonosító, ügyfélazonosító és vállalatnév szerint.

> [!NOTE]
> Válassza ki a **Használat típust** az oldalszűrőben, ha az oldalon lévő diagramokat "Normalizált nézetben" vagy "Nyers nézetben" szeretné megtekinteni. Ezeknek a diagramoknak az alapértelmezett nézete a "Normalizált nézet".

A **Használat oldalszűrők** alkalmazása az oldal szintjén. Több szűrőt is kijelölhet a diagram megjelenítéséhez a megtekinteni kívánt feltételekhez, valamint a "részletes használati adatok" rácsban/exportálásban megjeleníteni kívánt adatokat. A szűrők a rendelési lap jobb felső sarkában kiválasztott adattartományhoz kinyert adatokra vonatkoznak.

- **Az ajánlattípusok** és **az ajánlatnevek** csak a kiválasztott dátumtartományban szerzett ajánlatokhoz tartoznak. A listában szereplő ajánlatnevek a listából kiválasztott ajánlattípusokhoz jelennek meg.
- Az alapértelmezett beállítás az "Összes" beállítás minden szűrőbeállításhoz, kivéve a **Használati típust.** A Használati **típus** alapértelmezett kiválasztása a normalizált használat. Ha meg szeretné jeleníteni a nyers felhasználást a diagramokban, válassza a "nyers használat" lehetőséget.
- Az alkalmazott szűrők a végrehajtott szűrőkijelölések számválasztásait jelenítik meg. Az alkalmazott szűrők nem jelennek meg az alapértelmezett beállításoknál.

> [!NOTE]
> A "részletes rendelési adatok" rácsban, az oldalszűrőkben és az összes lehetséges kijelölésben szereplő mezők részletes definícióját a GYIK és a [terminológiai](link needed) cikk adatszótár szakasza határozza meg.

A **Forgalmi díjas számlázási használat** lap az ajánlattípusok használati adatait mutatja be, ahol a használatot méterenkénti méretekkel mérik. SaaS ajánlat típusú túllépés jelenleg bemutatott. A lap a SaaS-forgalmi díjas számlázási használat túllépési trendek grafikus ábrázolásait mutatja be:

- **Túllépési trend mérőméret szerint:** Az ajánlat kiválasztott mérőméretének havi túllépési trendjét jeleníti meg. Az X tengely a hónapot, az Y tengely pedig a használati mennyiséget jelöli. Az egyéni mérő mértékegysége az Y tengelyen is megjelenik.
- **Túllépéstrend termékváltozatszerint:** A kiválasztott mérődimenzió használati mennyiségének trendjét jelöli termékváltozatok szerint. A megjelenített sin-ek a legelső 5 sk-t jelölik, és a kiválasztott ajánlathoz a legnagyobb mennyiségű használatot biztosítják.
- **A Felső 50 ügyfél túlterhelési trendje:** A legmagasabb 50 ajánlat a legmagasabb használati órával a ***ranglistán*** jelenik meg, és az egyéni mérő legmagasabb használata alapján van rangsorolva. Válasszon ki egy vevőt a ranglistán a kiválasztott mérőméret használati trendjének megtekintéséhez.
- **Túllépés trend felső ügyfelek**: Bemutatja a legnépszerűbb ügyfél percentilis (ek), amelyek hozzájárulnak a %, a teljes használat. A legfelső vevő percentilis etengely mentén jelenik meg, és a vevő használati mennyisége határozza meg. Az Y tengely megjeleníti a használati mennyiséget. A részleteket úgy jelenítheti meg, hogy a vonaldiagram mentén lévő pontokra mutat.

> [!NOTE]
> A használat részletei és az ezen az oldalon lévő összes diagram attól függően jelenik meg, amelyik mérőméretet választja az oldalszűrőhöz.

## <a name="next-steps"></a>Következő lépések

- A Partnerközpont kereskedelmi piacterén elérhető elemzési jelentések áttekintését [a Partnerközpont Kereskedelmi piacterületén található Analytics for the commercial marketplace című témakörben találja.](./analytics.md)
- Az ajánlat marketplace-tevékenységét összegző összesített adatok grafikonjait, trendjeit és értékeit lásd: [Összefoglaló irányítópult a kereskedelmi piactér-elemzésben.](./summary-dashboard.md)
- A megrendelésekgrafikus és letölthető formátumban című témakörben talál további információt [a Rendelések irányítópultja a kereskedelmi piactér elemzésében.](./orders-dashboard.md)
- Az ügyfelekkel kapcsolatos részletes információkért, beleértve a növekedési trendeket is, a [Vevői irányítópult a kereskedelmi piactér elemzésében](./customer-dashboard.md)című témakörben talál.
- Az elmúlt 30 nap letöltési kérelmeinek listáját a [Letöltések irányítópultja a kereskedelmi piactér-elemzésben című témakörben tartalmazza.](./downloads-dashboard.md)
- Az Azure Marketplace-en és az AppSource-on az ajánlatokra vonatkozó ügyfél-visszajelzések összevont nézetét a [Minősítések és véleményezések irányítópultja című témakörben tekintheti meg a kereskedelmi piactér-elemzésekben.](./ratings-reviews.md)
- A kereskedelmi piactér elemzésével kapcsolatos gyakori kérdésekről és az adatkifejezések átfogó szótáráról a [Kereskedelmi piactér elemzésével kapcsolatos gyakori kérdések és terminológiák](./faq-terminology.md)című témakörben található.
