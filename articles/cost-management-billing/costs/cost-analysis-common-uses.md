---
title: Gyakran használt költségelemzési megoldások az Azure Cost Managementben
description: Ez a cikk bemutatja, hogyan kérhetők le az eredmények a gyakori költségelemzési feladatok esetében az Azure Cost Managementben.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: c03bf5e52ae7f6b259c7b744e6033d760af23dd0
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683674"
---
# <a name="common-cost-analysis-uses"></a>Gyakran használt költségelemzési megoldások

Az Azure Cost Management felhasználói gyakran keresnek válaszokat olyan kérdésekre, amelyeket sok más felhasználó is feltesz. Ez a cikk bemutatja, hogyan kérhetők le az eredmények a gyakori költségelemzési feladatok esetében a Cost Managementben.

## <a name="view-forecasted-costs"></a>Előrejelzett költségek megtekintése

Az előrejelzett költségek költségelemzési területeken jelennek meg a terület és halmozott oszlop nézetekben. Az előrejelzés az erőforrás-használati előzményeken alapul. Az erőforrás módosításai az előrejelzett költségekre is kihatnak.

Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.

Az alapértelmezett nézetben a felső diagramon található egy tényleges/amortizált költség és egy előrejelzett költség szakasz. A diagramon a sötétebb színű terület mutatja a tényleges/amortizált költséget. A világosabb terület az előrejelzett költséget mutatja.

[![Előrejelzett költségek](./media/cost-analysis-common-uses/enrollment-forecast.png)](./media/cost-analysis-common-uses/enrollment-forecast.png#lightbox)

## <a name="view-forecasted-costs-grouped-by-service"></a>Előrejelzett költségek megtekintése szolgáltatások szerint csoportosítva

Az alapértelmezett nézet nem jeleníti meg a szolgáltatás szerint csoportosított előrejelzett költségeket, ezért kijelöléssel kell hozzáadnia a csoportot.

Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.

Válassza a **Csoportosítás** > **Szolgáltatásnév** lehetőséget.

A nézetben a költségek az egyes szolgáltatások szerint csoportosítva jelennek meg. Az egyes szolgáltatások előrejelzett költségeit nem számítja ki a rendszer. Az összes szolgáltatás **Teljes** várható költsége jelenik meg.

[![Csoportosított előrejelzett költségek](./media/cost-analysis-common-uses/forecast-group-by-service.png)](./media/cost-analysis-common-uses/forecast-group-by-service.png#lightbox)

## <a name="view-forecasted-costs-for-a-service"></a>Egy szolgáltatás előrejelzett költségeinek megtekintése

Az előrejelzett költségeket akár egyetlen szolgáltatásra leszűkítve is megjelenítheti. Például előfordulhat, hogy csak a virtuális gépek előrejelzett költségeit szeretné megtekinteni.

1. Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.
1. Válassza a **Szűrő hozzáadása**, majd a **Szolgáltatás neve** elemet.
1. A **kiválasztási** listában válasszon ki egy szolgáltatást. Ebben a példában válassza a **Virtuális gépek** lehetőséget.

Tekintse át a kiválasztott szolgáltatás tényleges és előrejelzett költségeit.

A nézeten további testreszabást is végezhet.

1. Adjon hozzá egy második szűrőt (**Mérő**), és válasszon ki egy adott mérőtípushoz tartozó értéket, amelyre szűrni kíván a kiválasztott szolgáltatásnév alatt.
1. **Erőforrás** szerint csoportosítva áttekintheti a költségekkel járó erőforrásokat. Az egyes szolgáltatások előrejelzett költségeit nem számítja ki a rendszer. Az összes erőforrás **Teljes** várható költsége jelenik meg.

[![Egy szolgáltatás előrejelzett költsége](./media/cost-analysis-common-uses/forecast-by-service.png)](./media/cost-analysis-common-uses/forecast-by-service.png#lightbox)

## <a name="view-your-azure-and-aws-costs-together"></a>Az Azure és az AWS költségeinek együttes megjelenítése  

Az Azure és az AWS költségeinek együttes megjelenítéséhez használja az Azure-beli felügyeleti csoportok hatóköreit.

1. Hozzon létre egy felügyeleti csoportot, vagy válasszon ki egy meglévőt.
1. Rendelje hozzá a kívánt meglévő Azure-előfizetéseket a felügyeleti csoporthoz.
1. Rendelje hozzá *ugyanezt* a felügyeleti csoportot az összekötő csatolt fiókjához.
1. Lépjen a Költségelemzés területre, és válassza az **Összesített költségek** elemet.
1. Válassza a **Csoportosítás** - **Szolgáltató** lehetőséget.

## <a name="view-cost-breakdown-by-azure-service"></a>Költséglebontás megtekintése Azure-szolgáltatások szerint

A költségek Azure-szolgáltatások szerinti megjelenítése révén jobban átláthatja infrastruktúrájának azon részeit, amelyek a legmagasabb költségekkel járnak. Előfordulhat például, hogy a virtuális gépekhez kapcsolódó számítási költségek alacsonyak. A virtuális gépekből származó információmennyiség miatt azonban jelentős hálózati költségek merülhetnek fel. Az Azure-szolgáltatások elsődleges költségtényezőinek megértése elengedhetetlen ahhoz, hogy igény szerint módosíthassa saját szolgáltatáshasználatát.

1. Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.
1. Válassza a **Díj szolgáltatás szerint** elemet, majd végezze el a csoportosítást **Szolgáltatásszint** szerint.
1. Módosítsa a nézetet a következőre: **Táblázat**.

[![Költségek lebontása Azure-szolgáltatások szerint](./media/cost-analysis-common-uses/breakdown-by-service.png)](./media/cost-analysis-common-uses/breakdown-by-service.png#lightbox)

## <a name="review-invoiced-charges-in-cost-analysis"></a>A számlázott díjtételek áttekintése a Költségelemzésben

A számlaadatok Azure Portalon való megtekintéséhez keresse meg a költségelemzést ahhoz a hatókörhöz, amely az elemezni kívánt számlához kapcsolódik. Válassza a **Számla részletei** nézetet. A száma részletei úgy jelenítik meg a díjtételeket, ahogy a számlán szerepelnek.

[![A számla részleteit bemutató példa](./media/cost-analysis-common-uses/invoice-details.png)](./media/cost-analysis-common-uses/invoice-details.png#lightbox)

A számlázási adatok megtekintésével azonosíthatja, hogy melyik szolgáltatás okozott váratlan költségeket, és megállapíthatja, hogy melyik erőforrások kapcsolódnak közvetlenül ehhez az erőforráshoz a Költségelemzésben. Ha például engedélyezni kívánja a Virtual Machines szolgáltatás díjait, navigáljon a **Halmozott költség** nézetre. A részletességet ezután állítsa **Napi** értékre, a díjakat szűréséhez adja meg a **Szolgáltatásnév: Virtual Machines** értéket, majd csoportosítsa a díjakat **Erőforrás** szerint.

[![A virtuális gépek halmozott költségeit bemutató példa](./media/cost-analysis-common-uses/virtual-machines.png)](./media/cost-analysis-common-uses/virtual-machines.png#lightbox)

## <a name="view-cost-breakdown-by-azure-resource"></a>Költséglebontás megtekintése Azure-erőforrások szerint

A szolgáltatások létrehozása Azure-erőforrásokkal történik. A költségek erőforrás-alapú áttekintése segítséget nyújthat az elsődleges költségelemek gyors azonosításában. Ha egy szolgáltatás túlságosan drága erőforrásokkal rendelkezik, érdemes lehet módosításokat eszközölni a költségek csökkentése érdekében.

1. Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.
1. Válassza a **Díjak erőforrások szerint** elemet.
1. Módosítsa a nézetet a következőre: **Táblázat**.

[![Költséglebontás megtekintése Azure-erőforrások szerint](./media/cost-analysis-common-uses/cost-by-resource.png)](./media/cost-analysis-common-uses/cost-by-resource.png#lightbox)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Költséglebontás megtekintése a kiválasztott dimenziók szerint

A dimenziók lehetővé teszik, hogy költségeit a díjtételekben szereplő különböző metaadatértékek alapján rendszerezhesse. Csoportosíthatja például a költségeit hely szerint.

1. Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.
1. Válassza a **Csoportosítási szempont** szűrőt.  
    [![Csoport kiválasztása tétel szerint](./media/cost-analysis-common-uses/group-by.png)](./media/cost-analysis-common-uses/group-by.png#lightbox)
1. Igény szerint mentheti a nézetet későbbi felhasználásra.
1. A részletesebb adatok megtekintéséhez kattintson a grafikon alatti tortadiagramra.  
    [![Költséglebontás megtekintése a kiválasztott dimenziók szerint](./media/cost-analysis-common-uses/drill-down.png)](./media/cost-analysis-common-uses/drill-down.png#lightbox)

## <a name="view-costs-per-day-or-by-month"></a>Napi vagy havi költségek megtekintése

A napi és havi költségek megtekintése révén jobban megismerheti, hogy van-e olyan időszak a hét vagy év során, amikor a költségei magasabbak. Ha nagyobb ügyfélforgalma van egy adott munkaszüneti időszakban, Azure-költségei annak megfelelően nőnek? A péntek drágább nap, mint a hétfő?

1. Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.
1. A **Részletesség** értéke legyen **Havi** vagy **Napi**.

[![Napi költségek megtekintése](./media/cost-analysis-common-uses/daily-granularity.png)](./media/cost-analysis-common-uses/daily-granularity.png#lightbox)


## <a name="view-your-spot-vm-charges"></a>A Spot virtuális géphez kapcsolódó díjak megtekintése

A Spot virtuális gépek nagy költségmegtakarítást biztosítanak a megszakításokat kezelő számítási feladatok esetében. A számítási feladatok a használaton kívüli Azure-kapacitáson futnak. Mivel bármikor kizárhatók, a Spot virtuális gépek jelentős kedvezményt kapnak. A Spot virtuális géphez kapcsolódó díjak megtekintéséhez alkalmazza a következő lépéseket.

1. Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például így: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.
2. Adjon hozzá egy szűrőt a **Spot díjszabási modellhez**.

![Példa a Spot virtuális gép szűrőjére](./media/cost-analysis-common-uses/spot-vm-filter.png)

A díjszabási modell dimenziója az igény szerinti és a foglalási díjak megtekintésére is használatos.

## <a name="view-your-reservation-charges"></a>A foglalási díjak áttekintése

A fenntartott példányok lehetőséget biztosítanak Önnek arra, hogy pénzt takaríthasson meg az Azure-ral. A foglalásokkal előre meghatározott idő alatt költ pénzt meghatározott számú erőforrásra. A költségelemzés a számlán megjelenő díjakat jeleníti meg. A díjak tényleges vagy amortizált költségként jelennek meg a foglalási időszakra vonatkozóan.

1. Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például így: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.
1. Adjon hozzá egy szűrőt a **Foglalás díjszabási modellhez**.
1. A **Hatókör** és a megjelenő ár mellett kattintson a lefelé mutató nyíl szimbólumra, és válassza ki a **Tényleges költség** vagy az **Amortizált költség** metrikát.

![Költségmetrika kiválasztása](./media/cost-analysis-common-uses/metric-cost.png)

Az egyes metrikák hatással vannak arra, hogyan jelennek meg a foglalási díjakhoz tartozó adatok.

**Tényleges költség** – Úgy jeleníti meg a vásárlást, ahogy az megjelenik az Ön számláján. Ha például januárban vásárolt egy egy évre szóló foglalást 1200 USD értékben, a költségelemzés a foglalás 1200 USD-s díját január hónaphoz rendeli. Az év többi hónapjához nem rendel foglalási díjat. Ha a tényleges költségeket a virtuális gép szerint csoportosítja, akkor egy adott hónapban a foglalási kedvezménnyel rendelkező virtuális gépek esetén a hónap költsége nulla.

**Amortizált költség** – A foglalásvásárlási díjakat egyenletesen elosztva mutatja a foglalási időszak teljes tartamára. A fenti példát használva a költségelemzés az év minden hónapjára vonatkozóan egy 100 dolláros díjat jelenít meg, ha januárban éves foglalást vásárolt 1200 USD értékben. Ha ebben a példában a költségeket a virtuális gépek szerint csoportosítja, akkor látni fogja az egyes, foglalási kedvezménnyel rendelkező virtuális gépekhez hozzárendelt költségeket.

## <a name="view-your-reservation-utilization"></a>A foglalás kihasználtságának megtekintése

A foglalás megvásárlása után fontos a kihasználtság nyomon követése, hogy a használat arányos legyen a kifizetett összeggel. Ha például 10 virtuális gépet vásárolt egy évig, és csak ötöt használ, akkor lényegében a vásárlás fele kárba veszett. A kihasználtság kiértékelésének két különböző módja van:

### <a name="view-unused-ri-costs-in-cost-analysis"></a>A nem használt fenntartott példányok költségeinek áttekintése a költségelemzéssel

Az alábbi lépésekkel meghatározhatja, hogy mennyi foglalással kapcsolatos költség vész kárba az egyes hónapokban.

1. Az Azure Portalon keresse meg a költségelemzést ahhoz a hatókörhöz, amelyben a foglalást alkalmazza. Például így: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.
1. Adjon hozzá egy szűrőt a **Foglalás díjszabási modellhez**.
1. Válassza az **Amortizált költség** nézetet.
1. A részletesség értéke legyen **Havi**.
1. Állítsa be az időszakot az aktuális évre vagy a foglalási időszakra.
1. A diagram típusa **Oszlop (halmozott)** legyen.
1. Csoportosítsa a díjakat a **Díj típusa** szerint.
1. Tekintse át az `unusedreservation` értékekhez tartozó eredményeket.

[![A foglalás kihasználtságát mutató példa](./media/cost-analysis-common-uses/view-reservation-cost.png)](./media/cost-analysis-common-uses/view-reservation-cost.png#lightbox)

### <a name="view-utilization-in-reservations"></a>A foglalások kihasználtságának áttekintése

Részletes információkért tekintse meg a [foglalás kihasználtságának optimalizálását](../reservations/manage-reserved-vm-instance.md#optimize-reservation-use) ismertető szakaszt.

## <a name="view-costs-for-a-specific-tag"></a>Adott címkéhez tartozó költségek megtekintése

A díjtételek hatékonyabb kategorizálásához számos Azure-felhasználó alkalmaz címkéket az erőforrásai esetében, ami lehet például költséghely vagy (éles és tesztelési) fejlesztési környezet. A címkék dimenziókként jelennek meg a költségelemzésben. A dimenziók segítségével a felhasználó betekintést nyerhet saját egyéni címkézési kategóriáiba.

A címkék támogatása a címke erőforráson való alkalmazása *utáni* jelentett használatra vonatkozik. A címkék nem jelennek meg visszamenőlegesen a költségösszesítések esetében.

1. Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.
1. Válassza ki a címkéhez tartozó **Csoportosítás szempont** elemet.

[![Adott címkéhez tartozó költségek megtekintése](./media/cost-analysis-common-uses/tag.png)](./media/cost-analysis-common-uses/tag.png#lightbox)

## <a name="download-your-usage-details"></a>Használati adatok letöltése

A használati adatokat tartalmazó, CSV formátumú jelentésfájl tartalmazza az adott számlán szereplő összes díjtétel lebontását. A jobb megértés érdekében a jelentést összehasonlíthatja a számlájával. A számlán szereplő díjtételek mindegyike egy-egy lebontott díjtételnek felel meg a használati jelentésben.

1. Az Azure Portalon keresse meg a számlázási fiókhoz vagy előfizetéshez tartozó **Használat és díjak** lapot. Például: **Költségkezelés + Számlázás** > **Számlázás** > **Felhasználás + díjak**.
1. Válassza ki a letöltési forrásként használni kívánt elemet, majd kattintson a letöltési szimbólumra.  
    [![Használati adatok és díjak letöltése](./media/cost-analysis-common-uses/download1.png)](./media/cost-analysis-common-uses/download1.png#lightbox)
1.  Válassza ki a letölteni kívánt használatiadat-fájlt.  
    ![Letölteni kívánt használatiadat-fájl kiválasztása](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Havi EA-költséglebontás megtekintése

EA-regisztrációja a teljes szervezet költségeit magában foglalja. A költségek felhalmozódásának és időbeli számlázásának megértése segít a megfelelő érdekelt felek bevonásában a költségek felelősségteljes kezelésének biztosításához.

Csak az aktív regisztrációhoz tartozó költségek láthatók. Ha egy (inaktív) regisztrációt egy új (aktív) regisztrációba vitt át, az előző regisztráció költségei nem jelennek meg a Cost Managementben.


1. Az Azure Portalon lépjen a **Költségkezelés + Számlázás** > **Áttekintése** területre.
1. Kattintson az aktuális hónaphoz tartozó **Lebontás** elemre, és tekintse meg a pénzügyi kerete részleteit.  
    [![EA-költségek áttekintése – a költséglebontás összefoglalása](./media/cost-analysis-common-uses/breakdown1.png)](./media/cost-analysis-common-uses/breakdown1.png#lightbox)
1.  Kattintson a **Használat és díjak** lapra, és tekintse meg az előző hónap költséglebontását a kiválasztott időtartományra vonatkozóan.  
    [![Használat és díjak lap](./media/cost-analysis-common-uses/breakdown2.png)](./media/cost-analysis-common-uses/breakdown2.png#lightbox)

## <a name="view-enrollment-monthly-cost-by-term"></a>Havi regisztrációs költség megtekintése időszakonként

A regisztráció havi költségeinek grafikus nézete segítségével megismerheti az adott időszakra vonatkozó költségtrendeket és kiszámlázott összegeket.

1. Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.
1. Válassza ki a regisztrációt, és állítsa be a regisztrációs időszakot.
1. Állítsa be a részletességet havi értékre, majd állítsa be a nézetet a következőre: **Oszlop (halmozott)** .

A részletesebb elemzéshez csoportosíthatja és szűrheti is az adatait.

[![Havi regisztrációs költség időszakonként](./media/cost-analysis-common-uses/enrollment-term1.png)](./media/cost-analysis-common-uses/enrollment-term1.png#lightbox)

## <a name="view-ea-enrollment-accumulated-costs"></a>Az EA-regisztráció halmozott költségeinek megtekintése

Tekintse meg az idővel felhalmozott nettó díjtételeket, hogy átfogó képet kaphasson a szervezete kiadásairól az adott időszakra vonatkozóan.

1. Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.
1. Válassza ki a regisztrációt, majd tekintse meg az aktuális halmozott költségeket.

[![Halmozott regisztrációs költségek](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)](./media/cost-analysis-common-uses/cost-analysis-enrollment.png#lightbox)

## <a name="next-steps"></a>További lépések
- Ha még nem végezte el a Cost Management (Költségkezelés) első gyorsútmutatóját, itt megtekintheti: [Költségelemzés indítása](quick-acm-cost-analysis.md).
- Olvassa el a [Cost Management dokumentációját](../index.yml).
