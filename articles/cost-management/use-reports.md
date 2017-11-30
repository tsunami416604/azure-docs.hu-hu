---
title: "Költség jelentések használata Azure költség Management |} Microsoft Docs"
description: "Ez a cikk ismerteti a különböző költség hálózatkezelési jelentések használata a Cloudyn portálon."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/29/2017
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 92bb4f2a6458057613bdbcb749026781111a778d
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="use-cost-management-reports"></a>Költség felügyeleti jelentések használata

Ez a cikk ismerteti a különböző költség hálózatkezelési jelentések használata a Cloudyn portálon. A legtöbb Cloudyn jelentések intuitív és egy egységes megjelenés és működés. Cloudyn jelentésekkel kapcsolatos áttekintéséért lásd: [ismertetése költség jelentések](understading-cost-reports.md). A cikk a különböző beállítások és a legtöbb jelentésekben használt mezőket is ismerteti.

## <a name="cost-analysis-reports"></a>Költség elemzési jelentések

Költség elemzési jelentések a szolgáltatók számlázási adatait fogja megjeleníteni. A jelentések használata, csoportnak, és elemezze a számlázási fájlban részletezett különböző adatszegmensek. A jelentések részletes költség navigációs engedélyezése a felhőalapú szállítók nyers számlázási adatok között.

Költség elemzési jelentések nem csoportos költségek címkék alapján. Címke-alapú jelentés érhető csak a költség jelentések beállítása költség-modellben költség foglalási 360 létrehozása után.

### <a name="actual-cost-analysis"></a>Tényleges költségelemzés

A tényleges költség elemző jelentés a fő közreműködők, beleértve a folyamatban lévő költségeket és egyszeri díjakat jeleníti meg.

 A tényleges költség elemzési jelentés használja:

- Elemezheti és figyelheti a megadott időtartományban töltött tényleges költségek
- Egy küszöbérték-értesítés ütemezése
- Visszajelzés és jóváírási költség

#### <a name="to-use-the-actual-cost-analysis-report"></a>A tényleges költség elemző jelentés használatával

Legalább a következő lépésekkel telepítheti. Egyéb beállítások és mezőket is használható.

1. Válassza ki azt a dátumtartományt.
2. Válasszon ki egy szűrőt.

Kattintson a jobb egérgombbal jelentés eredményeit azokat kínál, illetve további részletes információk megtekintése.

![Tényleges költség elemzés – példa](./media/use-reports/actual-cost-analysis.png)

### <a name="actual-cost-over-time"></a>Tényleges költség adott idő alatt

A tényleges költség keresztül idő a jelentés egy szabványos elemző jelentés költség terjesztése felett a meghatározott idő felbontása. A jelentés megjeleníti az adott idő alatt is láthatja a trendek és észlelje a költségkeret szabálytalanságok kiadásokat. Ez a jelentés tartalmazza a fő közreműködők, beleértve a folyamatban lévő költségeket és a kijelölt időtartományban fordított egyszeri fenntartott példány díjakat.

A tényleges költség keresztül idő jelentésben használja:

- Lásd: energiaköltségi trendjeinek időbeli.
- Szabálytalanságok található költség.
- Amazon Web Services kapcsolódó összes költség kapcsolatos kérdések található.

#### <a name="to-use-the-actual-cost-over-time-report"></a>A tényleges költség keresztül idő jelentés használata:

Legalább a következő lépésekkel telepítheti. Egyéb beállítások és mezőket is használható.

- Válassza ki azt a dátumtartományt.

Például kiválaszthatja csoportok időbeli költségét megtekintéséhez. Majd az eredmények szűkítéséhez szűrőket.

![Tényleges költség keresztül idő – példa](./media/use-reports/actual-cost-over-time.png)



### <a name="amortized-cost-reports"></a>Amortized költség-jelentések

A amortized költség jelentések megjelenítése linearizált nem-használat alapú szolgáltatás költség, vagy egyszeri fizetendő és azok költségét eloszlatva egyenletesen az élettartamot közben.

Az egyszeri díjak lehet például a következők:

- Éves támogatási díjak
- Éves biztonsági összetevő díjak
- Fenntartott példányok beszerzési díjak
- Bizonyos Azure piactéren elemek

Egyszeri díjak jellemző a számlázási fájlban, ha a szolgáltatás felhasználásához kezdő és záró dátumát vagy timestamp, értékűek egyenlő. Cloudyn majd felismeri az egyszeri díjak amortized is lehet. Nem lehet amortized egyéb fogyasztás alapján szolgáltatások igény szerinti használati költségek.

Amortized költségek mutatja be, tekintse át a következő példa kép tényleges költség keresztül idő jelentés. A példa azt mutatja be egy költség csúcs augusztus 23. Az anomáliadetektálási képest a szokásos napi költségtrend tűnhet. Legfelső szintű alapprobléma elemzéséhez és adatok navigációs az ehhez kapcsolódó költséget azonosítottak egy éves AWS szolgáltatás APN foglalást, amely egyszeri díjat vásárolt, és az adott napon számlázható. Hogyan van amortized az ehhez kapcsolódó költséget a következő szakaszban tekintheti meg.

![Tényleges költség keresztül idő – példa megjelenítő egyszeri költség](./media/use-reports/actual-amort-example.png)

#### <a name="to-use-the-amortized-cost-over-time-report"></a>A Amortized költség keresztül idő jelentés használata:

Legalább a következő lépésekkel telepítheti. Egyéb beállítások és mezőket is használható.

1. Válassza ki azt a dátumtartományt.
2. Válassza ki a szolgáltatás és a szolgáltató.

Az előző példában végrehajtsa-továbbító, hogy az egyszeri költség most amortized-e az alábbi ábrán látható:

![Amortized költség keresztül idő – példa](./media/use-reports/amort-cost-over-time.png)

A fenti kép időbeli változását ábrázolja az APN fenntartás költsége amortized költsége. Ez a jelentés tartalmazza a egyszeri díj sablon és a hozzáférési költség, egy éves foglalás beszerzési. APN költsége van egyenletesen naponta 1/365th a Foglalás kezdeti költség.

## <a name="cost-allocation-analysis-reports"></a>Foglalási elemzési jelentések költsége

Költség foglalási elemzési jelentések érhetők el egy költség-modellben költség foglalási 360 létrehozása után. Cloudyn költség/számlázási adatokat dolgozza fel, és megfelel az adatok a felhőben fiókok használati és címke adatokhoz. Az adatok kereséséhez Cloudyn a használati adatok hozzáférésre van szüksége. Fiókok, amelyekből hiányoznak a hitelesítő adatokat, amelyek címkézve Kategorizálatlan erőforrások.

### <a name="cost-analysis-report"></a>Költség Teljesítményelemzésének jelentése.

A költségek elemző jelentés a felhőben felhasználási és a kijelölt időtartományban költségeik betekintést nyújt. A házirendek beállítása az költség hozzárendelés-kezelő a költség elemző jelentés is használja.

Hogyan Cloudyn Ez a jelentés kiszámítja?

Cloudyn biztosítja, hogy foglalási megőrzi minden hivatkozott fiók sértetlenségének fiók affinitás alkalmazásával. Affinitás biztosítja, hogy egy megadott szolgáltatást nem használó fiók nem rendelkezik hozzárendelt szolgáltatás költségeket. Az, hogy a fiók fiókhoz tartozó maradnak, és a program az elosztási házirendek miatt felmerülő költségekkel. Előfordulhat például, öt csatolt fiókok. Ha csak a tárolási szolgáltatások használatához három a majd csak a tárolási szolgáltatások költsége van lefoglalva a három fiókok címkék között.

 Költség elemzési jelentés használja:

- Jeleníti meg a teljes telepítés vagy adott időszakon az összevont nézetet.
- Megtekintheti a költségeket a költség modell létrehozott házirendek alapján címke kategóriák szerint.

#### <a name="to-use-the-cost-analysis-report"></a>A költségek elemző jelentés használata:

1. Válassza ki azt a dátumtartományt.
2. Adja hozzá a címkéket, igény szerint.
3. Adja hozzá a csoportokat.
4. Válassza ki a korábban létrehozott költség modelljét.

Az alábbi ábrán egy példa költség elemző jelentés sunburst formátumban. A gyűrűk csoportok megjelenítése. A külső gyűrű jeleníti meg a szolgáltatás, és a belső kör egység jeleníti meg.

![Költség elemzés – példa](./media/use-reports/cost-analysis01.png)



Itt látható példa táblázat nézetben ugyanazokat az információkat.

![Költség elemzés – példa](./media/use-reports/cost-analysis02.png)



### <a name="cost-over-time-report"></a>Költség keresztül idő jelentés

A költség keresztül idő a jelentés jeleníti meg, ugyanis adott idő alatt, így láthatja a trendek és szabálytalanságok észleli a telepítésben. Lényegében egy meghatározott időszakban elosztott költségek jeleníti meg. A jelentés tartalmazza a fő közreműködők, beleértve a folyamatban lévő költségeket és a kijelölt időtartományban fordított egyszeri fenntartott példány díjakat. Ez a jelentés használható költség-kezelő 360° beállított szabályzatoknak.

Használja a költség keresztül idő jelentést:

- Idő, és milyen hatású módosítsa egy nap (vagy dátumtartományt) a következő keresztül láthatják a változtatásokat.
- Elemezze a költségek időbeli egy adott művelethez.
- Megismeréséhez ezért megnőtt költség egy adott művelethez.

#### <a name="to-use-the-cost-over-time-report"></a>A költségek keresztül idő jelentés használata:

1. Válassza ki azt a dátumtartományt.
2. Adja hozzá a címkéket, igény szerint.
3. Adja hozzá a csoportokat.
4. Válassza ki a korábban létrehozott költség modelljét.
5. Válassza ki a tényleges költségek és amortized költségeket.
6. Nyers adatok nézet számlázási nézetre felosztási szabályok vonatkoznak-e, vagy költség számolni Cloudyn nézetben.

Ez a jelentés egy példát.

![Költség keresztül idő – példa](./media/use-reports/cost-over-time.png)



## <a name="next-steps"></a>Következő lépések

- Ha még nem fejezte első oktatóanyaga, amely költség-kezelésre, olvassa el a [tekintse át a használati és költségek](tutorial-review-usage.md).
