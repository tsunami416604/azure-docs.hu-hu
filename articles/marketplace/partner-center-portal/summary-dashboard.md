---
title: A partner Center Analytics összegző irányítópultja a kereskedelmi piactéren
description: Megtudhatja, hogyan érheti el a Piactéri tevékenységeket összefoglaló összesített adatok gráfokat, trendeket és értékeket a partner Center összegző irányítópultján.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 86bce85c6ec273b4ab5f9f00cbae68fc054f53f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81262400"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Összefoglaló irányítópult a kereskedelmi piactér elemzésében

Ez a cikk a partner Center összegző irányítópultján tartalmaz információkat. Ez az irányítópult az ajánlatok számára elérhető Piactéri tevékenységet összefoglaló összesített adatok grafikonokat, trendeket és értékeket jeleníti meg.

Az összegző irányítópult eléréséhez nyissa meg az **[elemzés irányítópultot](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** a kereskedelmi piactéren.

>[!NOTE]
> Az analitikai terminológia részletes leírását lásd: [a kereskedelmi piactér Analytics szolgáltatással kapcsolatos gyakori kérdések és fogalmak](./faq-terminology.md).

## <a name="summary-dashboard"></a>Összegzés irányítópult

Az **összegzési** irányítópult az egyes ajánlatok típusán alapuló áttekintést jelenít meg. Az információk egy pillantással **mutatják be** a kritikus információkat, és átfogó képet nyújtanak az ajánlatok értékesítési tevékenységéről. Ezeket a jelentéseket az **összegző** irányítópult használatával jelenítheti meg. Ez a cikk további részleteket mutat be a következő elemek mindegyikével kapcsolatban:

- [Dátumtartomány](#date-range)
- [Összefoglalás szakasz](#summary-section)
- [Ügyfelek földrajz szerint](#customers-by-geography)
- [Növekedési trendek diagramjai](#growth-trend-charts)
- [Ügyfél ranglista](#customer-leaderboard)
- [Ülőhelyek száma](#seat-count-trend)
- [Ingyenes próbaverziók SaaS Orders trend](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>Az összegző irányítópult elemei

Az alábbi szakaszok azt ismertetik, hogyan használható az összegző irányítópult, és hogyan lehet beolvasni az adatelemzést.

### <a name="date-range"></a>Dátumtartomány

Az egyes lapok jobb felső sarkában megtalálhatja a dátumtartomány kijelölését. Szabja testre az **összegzési** oldal gráfok kimenetét egy dátumtartomány kiválasztásával az elmúlt 3, 6 vagy 12 hónap alapján, vagy válasszon ki egy olyan egyéni dátumtartományt, amelynek maximális időtartama 12 hónap. Az alapértelmezett dátumtartomány hat hónap.

![A partner Center elemzése irányítópult](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Összefoglalás szakasz

Az összefoglalás szakasz az összes létrehozott rendelés, a beszerzett ügyfelek és a kiválasztott dátumtartomány során jelentett használati adatok számát jeleníti meg. A részleges aktuális hónap ki lesz zárva a metrikák kiszámításával. Például: Ha a 6M-es időkeretet választotta, a használati idő az aktuális hónap előtti hat hónapra van kiszámítva. Ha egyéni dátumtartomány van kiválasztva, az aktuális hónapból származó részleges mennyiség ki lesz zárva a számításból.

![Növekedési trendek az összegző irányítópulton](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>Az összefoglalás szakasz olvasása

- **Megrendelések**: a megvásárolt összes megrendelés száma (kivéve a megszakított rendeléseket) az eddig közzétett ajánlatokhoz.
- **Ügyfelek**: azoknak az ügyfeleknek a száma, akik az ajánlatokat megvásárolták, és legalább egy nem törölt rendeléssel rendelkeznek.
- **Normalizált használati idő**: a virtuálisgép-magok számának ([virtuálisgép-magok száma] x [órányi nyers használat]) a fiókhoz normalizált használati órájának meghatározása. A "SHAREDCORE" jelölésű virtuális gépek a 1/6 (vagy 0,1666) értéket használják [virtuálisgép-magok száma] szorzóként.
- **Nyers használati**idő: a virtuális gépek mennyisége órákban megadva. Az **összes megrendelés**, a **teljes ügyfél**, a **normalizált használati idő**, a **nyers használati idő**, az **oldalak meglátogatása**és a **műveletek hívása** a kiválasztott dátumtartomány ([a múlt havi használat – az első havi használat])/az első havi használat növekedésének mértékét jelöli. A fentiekben leírtak szerint az aktuális hónap részleges mennyisége ki lesz zárva ebből a metrikából.
- **Növekedési trendek**: Ha a diagram oszlopai fölé viszi a kurzort, a sávdiagramok az egyes hónapok értékét jelenítik meg.
- **Felfelé mutató zöld háromszög**: pozitív növekedési trendet jelez.
- **Lefelé mutató piros háromszög**: az előző hónaphoz képest negatív növekedési trendet jelez.

### <a name="customers-by-geography"></a>Ügyfelek földrajz szerint

Az **ügyfelek földrajzi** hő megjelenítik az ügyfelek darabszámát a globális térképen.

![Ügyfelek földrajz szerint az összefoglaló irányítópulton](./media/summary-customers-by-geography.png)

- A térképet áthelyezve megtekintheti a pontos helyet.
- Egy adott helyre nagyíthat.
- A hő egy kiegészítő rácstal rendelkezik, amely az adott helyen megtekintheti az ügyfelek számát, a rendelések számát, a normalizált használati időt.
- Megkeresheti és kiválaszthatja a rács egyik országát, hogy a térképen lévő helyre nagyítson. A **Kezdőlap** gomb megnyomásával visszatérhet az eredeti nézethez.
- Egy **új** ügyfél első alkalommal vásárolta meg az ajánlatait a kiválasztott időtartományon belüli hónapban.

### <a name="growth-trend-charts"></a>Növekedési trendek diagramjai

Megtekintheti a trendeket a **megvásárolt megrendelések** (megszakított megrendelések belefoglalása), a vásárolt **ügyfelek** (az elveszett ügyfeleket is beleértve) és a jelentett **felhasználás** alapján, amelyek a kiválasztott dátumtartomány szerint havonta jelennek meg. Ezeket a trendeket tovább elemezheti a diagram alatti hivatkozások kiválasztásával, amelyekkel megnyithatja az adott **sorrend**, a **használati**, az **ügyfél**vagy a **Marketplace** -elemzés lapokat.

Az Azure Marketplace és a AppSource két lapon jeleníti meg a Piactéri ajánlat **oldalát, és meghívja az Action** trend-diagramokat.

![Oldal-látogatások és a műveletek trend-diagramok meghívása az összegző irányítópulton](./media/summary-page-visits-and-cta.png)

A **Orders by ajánlatok** diagram az ajánlat neve alapján rendezi a rendeléseket.

A **megrendelések az értékesítési csatorna** kördiagrama a kiválasztott dátumtartomány szerint rendezi a rendeléseket (beleértve az ügyfelek által megszakított rendeléseket is) az értékesítési csatorna alapján. Az értékesítési csatorna az ügyfelek által az Azure megvásárlására használt licencelési szerződés típusa, amely a felhőalapú megoldás-szolgáltató (CSP), a Enterprise, a Enterprise, a viszonteladó, a GTM és a fizetés.

A **Sales Channel** Pie-diagramok az **ajánlatok** és a használati díjak használatával mutatják be a legnépszerűbb ajánlatok és értékesítési csatornák használatának részletezését. A belső tortadiagram a nyers használatot, a külső tortadiagram pedig normalizált használatot képvisel.

A Piactéri licenc típusa és a **piactér licencelése típusú** kördiagramok által **megrendelések** a megfelelő licencelési típus szerint a megrendelések és a használat részletezését jelenítik meg. A licencek típusai a következők:

- **Számlázás az Azure**-on keresztül: a Microsoft számlázza az ügyfelet az Ön nevében, ha úgy dönt, hogy eladja az ajánlatot a Microsofttal ezzel a licenccel. A fizetési típusok közé tartozik a bankkártya vagy a nagyvállalati számlázás.
- **Saját licenc**használata: a Microsoft nem számlázza az ügyfeleket az ilyen típusú piactér-ajánlattal való használatért. Ez a használat **most már (ingyenes) jelenik meg** a piactéren.
- **Ingyenes**: a Microsoft nem számlázza az ügyfeleket az ilyen típusú Piactéri ajánlattal való használatért. Ez a használat **ingyenes próbaverzióként** szerepel a piactéren.
- **Microsoft as viszonteladó**: a Microsoft viszonteladói által a **Cloud Solution Provider (CSP) program**részeként eladott ajánlatokat képviseli.

### <a name="customer-leaderboard"></a>Ügyfél ranglista

A legmagasabb számú rendeléssel rendelkező legfelső szintű 50-ügyfeleket a *ranglistán*jeleníti meg a legmagasabb sorszám és a sorrend százaléka szerint rendezve.

- Válasszon ki egy ügyfelet a profiljuk részleteinek megtekintéséhez, az ajánlat által szervezett rendelésekhez, valamint az Azure-licenc típusa és a díjszabási csatorna által szervezett rendelésekhez.
- Az **ajánlatok by Orders** fánk diagram az első négy ajánlatot (megrendelés száma szerint), a fennmaradó ajánlatokat pedig "Rest all"-ként csoportosítva mutatja be.
- Az ajánlat fánk-diagramjának **normalizált használata** az első öt ajánlat használatát mutatja be.

> [!NOTE]
> Az ügyfél személyes adatai csak akkor jelennek meg, ha az ügyfél beleegyezett. Ezeket az információkat akkor tekintheti meg, ha **tulajdonosi** szerepkörre vonatkozó engedélyekkel jelentkezett be. A **közreműködő** szerepkörrel rendelkező felhasználók nem fogják tudni megtekinteni ezeket az információkat. [További információ a felhasználói szerepkörökről és engedélyekről](./manage-account.md#define-user-roles-and-permissions).

### <a name="seat-count-trend"></a>Ülőhelyek száma

A **Orders by munkaállomásonkénti/site** diagram alapján megjelenő rendelések a díjszabási modell szerint megvásárolt összes megrendelés részletezését jelenítik meg. Az **ülőhelyek trendjeinek** diagramja a munkaállomásonkénti szoftverek (SaaS) által kínált összes szolgáltatásra vonatkozóan megvásárolt ülőhelyeket és rendeléseket mutatja be.

### <a name="free-trials-saas-orders-trend"></a>Ingyenes próbaverziók SaaS Orders trend

Az **ingyenes próbaverziós SaaS Orders trend** diagram az ingyenes próbaverziós SaaS-ajánlatok megrendeléseinek trendjét mutatja be egy 30 napos próbaidőszakon belül.

## <a name="next-steps"></a>További lépések

- A partner Center kereskedelmi piactéren elérhető analitikai jelentések áttekintését lásd: [a partner Center kereskedelmi piacának elemzései](./analytics.md).
- További információ a megrendelésekről grafikus és letölthető formátumban: [Orders Dashboard a kereskedelmi piactér elemzésében](./orders-dashboard.md).
- A virtuális gép (VM) használati és mért számlázási mérőszámokat kínál, lásd [a használati irányítópultot a kereskedelmi piactér elemzésében](./usage-dashboard.md).
- Az ügyfelekkel kapcsolatos részletes információkért, beleértve a növekedési trendeket, lásd [a kereskedelmi piactér elemzésének ügyfél-irányítópultját](./customer-dashboard.md).
- Az elmúlt 30 napban a letöltési kérelmek listáját lásd: a [kereskedelmi piactér Analytics letöltések irányítópultja](./downloads-dashboard.md).
- Ha szeretné megtekinteni az Azure Marketplace-en és a AppSource található ajánlatokra vonatkozó felhasználói visszajelzések összevont nézetét, tekintse meg a [kereskedelmi piactér elemzésének minősítések és értékelések irányítópultját](./ratings-reviews.md)
- A kereskedelmi piactér elemzésével kapcsolatos gyakori kérdésekre, valamint az adatkifejezések átfogó szótárát lásd: [Gyakori kérdések és terminológia a kereskedelmi piactér elemzéséhez](./faq-terminology.md).
