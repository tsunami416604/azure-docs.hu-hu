---
title: A Kereskedelmi piactér partnerközpont-elemzésének összefoglaló irányítópultja
description: Megtudhatja, hogy miként érheti el a marketplace-tevékenységeket összegző összesített adatok grafikonjait, trendjeit és értékeit a Partnerközpont Összefoglaló irányítópultjáról.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 3da3109bd813fc3b99a4f59e5a357fa351c75394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281374"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Összefoglaló irányítópult a Kereskedelmi Piactér elemzésében

Ez a cikk a Partnerközpont Összefoglaló irányítópultjáról nyújt tájékoztatást. Ez az irányítópult grafikonokat, trendeket és összesített adatokat jelenít meg, amelyek összegezik az ajánlatok piactéri tevékenységét.

Az Összefoglaló irányítópult eléréséhez nyissa meg az **[Elemzés irányítópultot](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** a Kereskedelmi piactér csoportban.

>[!NOTE]
> Az elemzési terminológia részletes meghatározását a [Kereskedelmi piactér elemzésével kapcsolatos gyakori kérdések és terminológiák](./faq-terminology.md)című témakörben található.

## <a name="summary-dashboard"></a>Összegzés irányítópult

Az **Összefoglaló** irányítópult áttekintést nyújt az egyes ajánlattípusok alapján. **Az elemzési** adatok egy pillantással áttekintve jelenítik meg a kritikus információkat, és széles képet adnak az ajánlatok értékesítési tevékenységéről. Ezeket a jelentéseket az Összefoglaló irányítópult segítségével **jelenítheti meg.** Ez a cikk további részleteket tartalmaz a következő elemekmindegyikéről:

- [Dátumtartomány](#date-range)
- [Összefoglaló szakasz](#summary-section)
- [Ügyfelek földrajzi elhelyezkedés szerint](#customers-by-geography)
- [Növekedési trendgrafikonok](#growth-trend-charts)
- [Ügyfél ranglista](#customer-leaderboard)
- [Ülőhelyek száma trend](#seat-count-trend)
- [Ingyenes vizsgálatok SaaS megrendelések trend](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>Az Összefoglaló irányítópult elemei

A következő szakaszok ismertetik, hogyan kell használni az összefoglaló irányítópultot, és hogyan kell olvasni az adatokat.

### <a name="date-range"></a>Dátumtartomány

A dátumtartomány-kijelölést az egyes oldalak jobb felső sarkában találja. **Az Összegző** oldalgrafikonok kimenetének testreszabásához válasszon az elmúlt 3, 6 vagy 12 hónap dátumtartományát, vagy válasszon egy legfeljebb 12 hónapos egyéni dátumtartományt. Az alapértelmezett dátumtartomány hat hónap.

![Partnerközpont elemzése irányítópult](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Összefoglaló szakasz

Az Összegzés szakasz megjeleníti a kiválasztott dátumtartományban beszerzett összes rendelés, beszerzett és a beszerzett rendelések számát. A részleges aktuális hónap ki lesz zárva ezeknek a mutatóknak a számításából. Például: Ha a 6 M-es időkeretet választotta, a használati órák at az aktuális hónapot megelőző hat hónapra számítja ki a rendszer. Ha egy egyéni dátumtartomány van kiválasztva, az aktuális hónap egy részleges összege ki lesz zárva a számításból.

![Növekedési trendek az Összefoglaló irányítópulton](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>Az összefoglaló szakasz elolvasása

- **Megrendelések**: Az eddig közzétett ajánlatokhoz vásárolt összes megrendelés megszámlálása (a törölt megrendelések kivételével).
- **Ügyfelek**: Megszámlálja azokat az ügyfeleket, akik megvásárolták az ajánlatokat, és legalább egy nem törölt rendelésük van.
- **Normalizált használati órák:** A virtuálisgép-magok ([virtuálisgép-magok száma] x [nyers használat órái] figyelembevételével normalizált használati órák meghatározásaként határozható meg. A "SHAREDCORE" néven kijelölt virtuális gépek 1/6 (vagy 0.1666) a [virtuálisgép-magok száma] szorzót használnak.
- **Nyers használati órák:** Az az idő, amíg a virtuális gépek órákban futnak. Az **összes rendelés,** **az összes vevő, a** **normalizált használati órák,** **a nyers használati órák,** **az oldallátogatások**és a **cselekvések hívása** melletti százalékos érték a kiválasztott dátumtartomány ([utolsó havi használat – első havi használat]) / első havi használat használati értékének mértékét jelöli. A fent leírtak szerint az aktuális hónap egy részleges összege ki lesz zárva ebből a mérőszámból.
- **Növekedési trendek**: Ha a diagram oszlopai fölé viszi az egérmutatót, a sávdiagramok az egyes havi értékeket jelenítik meg.
- **Felfelé mutató zöld háromszög**: Pozitív növekedési tendenciát jelez.
- **Lefelé mutató piros háromszög**: Az előző hónaphoz viszonyított negatív növekedési tendenciát jelez.

### <a name="customers-by-geography"></a>Ügyfelek földrajzi elhelyezkedés szerint

Az **Ügyfelek földrajzi hőtérkép szerint** az ügyfelek számát a világtérképen jeleníti meg.

![Ügyfelek földrajzi hely szerint az Összefoglaló irányítópulton](./media/summary-customers-by-geography.png)

- A térkép áthelyezésével megtekintheti a pontos helyet.
- Egy adott helyre nagyíthat.
- A hőtérkép egy kiegészítő rácsmal rendelkezik a vevőszám, a rendelések számának, az adott helyen normalizált használati órák részleteinek megtekintéséhez.
- Kereshet és kiválaszthat egy országot a rácsban a térképen lévő helyre való nagyításhoz. Visszatérés az eredeti nézethez a térkép **Enigombjának** megnyomásával.
- Egy **új** ügyfél a hónap során először vásárolta meg az egyik ajánlatot a kiválasztott dátumtartományon belül.

### <a name="growth-trend-charts"></a>Növekedési trendgrafikonok

Megtekintheti a **megvásárolt rendelések** (beleértve a visszavont rendeléseket is), a **megszerzett vevők** (beleértve az elveszett vevőket is) és a **jelentett használat** növekedésén alapuló trendeket, amelyek hónapról hónapra jelennek meg a kiválasztott dátumtartomány szerint. Ezeket a trendeket tovább elemezheti a diagram alatti hivatkozások kiválasztásával, amelyek a megfelelő **sorrendre**, **használatra**, **ügyfélre**vagy **Marketplace Insights-oldalakra** navigálnak.

A **Marketplace-en laplátogatásokat és cselekvésre való** felhívástrend-diagramokat kínálaz Azure piactéren és az AppSource-ban két lapon.

![Oldallátogatások és cselekvésre való felhívás trenddiagramjai az Összefoglaló irányítópulton](./media/summary-page-visits-and-cta.png)

Az ajánlatok szerinti **rendelések** diagram az Ajánlat neve szerint rendezi a rendeléseket.

A **rendelések értékesítési csatorna kördiagramja** a rendeléseket (beleértve azokat a rendeléseket is, amelyeket a vevők visszavontak) a kiválasztott dátumtartományban, értékesítési csatorna szerint rendezi. Az értékesítési csatorna az a licencszerződés, amelyet az ügyfelek az Azure megvásárlására használnak, amelyek a felhőszolgáltató (CSP), az Enterprise, a Nagyvállalati verzió viszonteladón keresztül, a GTM és az Ön használatból kifizetett út.

**Az ajánlatok** és **az értékesítési csatorna** kördiagramjai szerinti használat a használat felső ajánlatok és értékesítési csatornák szerinti bontását mutatja be. A belső kördiagram a nyers használatot, a külső kördiagram pedig a normalizált használatot jelöli.

A **rendelések piactérlicenc típusa** és **a piactéri licenctípus** kördiagramok szerint a rendelések és a használat megfelelő licenctípus szerinti bontását jelenítik meg. A licenctípusok a következők:

- **Az Azure-on keresztül számlázva:** A Microsoft az Ön nevében számlázaz ügyfeleknek, ha úgy dönt, hogy az ajánlatot a Microsofton keresztül értékesíti ezzel a licenctípussal. A fizetési típusok közé tartozik a felosztó-ki-fel fizetés hitelkártyával vagy vállalati számlázással.
- **Hozza magával a saját licencét: A**Microsoft nem számlázaz ügyfeleknek a használatukért az ilyen típusú piactéri ajánlattal. Ez a használat szerepel **a Get it now (Ingyenes)** a piacon.
- **Ingyenes**: A Microsoft nem számlázaz ügyfeleknek az ilyen típusú piactéri ajánlattal való használatukért. Ez a használat ingyenes **próbaverzióként** szerepel a piactéren.
- **Microsoft viszonteladóként**: A Microsoft viszonteladói által a **Felhőszolgáltató (CSP) program**részeként értékesített ajánlatokat képviseli.

### <a name="customer-leaderboard"></a>Ügyfél ranglista

A legtöbb rendeléssel rendelkező 50 ügyfél a *ranglistán*jelenik meg, a legmagasabb rendelésszám és a rendelési százalék szerint rendezve.

- Válasszon ki egy ügyfelet a profilrészleteik, az ajánlat szerinti rendelések vagy az Azure licenctípus és a díjszabási csatorna szerint rendezett rendelések megtekintéséhez.
- A **rendelések szerint ajánlatok** fánk diagram az első négy ajánlatot mutatja be (rendelések száma szerint), a fennmaradó ajánlatokat pedig "Rest All" néven csoportosítva.
- A **normalizált használat ajánlat** fánk diagram bemutatja az első öt kínál a használat.

> [!NOTE]
> Az ügyfél személyes adatait csak akkor mutatjuk be, ha az ügyfél ehhez hozzájárult. Ezeket az információkat akkor tekintheti meg, ha **tulajdonosi** szerepkör-engedélyekkel jelentkezett be. A **közreműködői** szerepkörrel rendelkező felhasználók nem tekinthetik meg ezeket az információkat. [További információ a felhasználói szerepkörökről és engedélyekről.](./manage-account.md#define-user-roles-and-permissions)

### <a name="seat-count-trend"></a>Ülőhelyszám trend

A **rendelések ülőhelyenként/ webhelydiagramonként** az összes megvásárolt rendelés ármodell szerinti bontását mutatja be. Az **ülőhelyszám trendtáblázat** a szolgáltatások (SaaS) ajánlataihoz vásárolt helyeket és megrendeléseket mutatja be az egy helyen ként szoftverhez.

### <a name="free-trials-saas-orders-trend"></a>Ingyenes vizsgálatok SaaS megrendelések trend

Az **ingyenes próbaverzió SaaS megrendelések trend** diagram bemutatja a trend a megrendelések ingyenes vizsgálatok SaaS kínál egy 30 napos próbaidőszak.

## <a name="next-steps"></a>További lépések

- A Partnerközpont kereskedelmi piactéren elérhető elemzési jelentéseinek áttekintését [a Partnerközpont Kereskedelmi piactér Analytics szolgáltatása című témakörben találja.](./analytics.md)
- A megrendelésekgrafikus és letölthető formátumban című témakörben talál további információt a [Rendelések irányítópultja a Kereskedelmi piactér elemzésében.](./orders-dashboard.md)
- A virtuális gép (VM) kínál használati és forgalmi díjas számlázási metrikák, lásd: [Használati irányítópult a Kereskedelmi Piactér analytics.](./usage-dashboard.md)
- Az ügyfelekkel kapcsolatos részletes információkért, beleértve a növekedési trendeket is, az [Ügyfél irányítópultja a Kereskedelmi piactér elemzésében](./customer-dashboard.md)című témakörben talál.
- Az elmúlt 30 nap letöltési kérelmeinek listáját a [Letöltések irányítópultja a Kereskedelmi piactér elemzésében című témakörben található.](./downloads-dashboard.md)
- Az Azure Marketplace-en és az AppSource-on az ajánlatokra vonatkozó ügyfél-visszajelzések összevont nézetét a [Kereskedelmi piactér elemzése minősítések és vélemények irányítópultja című témakörben tekintheti meg.](./ratings-reviews.md)
- A Kereskedelmi piactér elemzésével kapcsolatos gyakori kérdésekről és az adatkifejezések átfogó szótáráról a [Kereskedelmi piactér elemzésével kapcsolatos gyakori kérdések és terminológiák](./faq-terminology.md)című témakörben található.
