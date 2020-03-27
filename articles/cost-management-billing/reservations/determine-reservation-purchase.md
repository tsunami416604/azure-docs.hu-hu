---
title: A megvásárolni kívánt Azure-beli foglalás kiválasztása
description: Ez a cikk segít a megvásárolni kívánt Azure-beli foglalás kiválasztásában.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: banders
ms.openlocfilehash: 1b639da3494c0527141347ca61e77980d29a59ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80135555"
---
# <a name="determine-what-reservation-to-purchase"></a>A megvásárolni kívánt foglalás kiválasztása

Az Azure Databricksen kívül minden foglalási díj óraalapú. A foglalásokat az alapján érdemes megvásárolni, hogy általában milyen használat jellemzi a rendszerét. Számos módon meg lehet határozni, hogy milyen foglalás megvásárlása mellett érdemes dönteni, és ez a cikk pontosan ehhez nyújt segítséget.

Ha a jellemző használathoz képest nagyobb kapacitást vásárol, az kihasználatlan foglalást eredményez. A kihasználatlanságot mindig érdemes elkerülnie, ha módjában áll. A fel nem használt, lefoglalt kapacitás nem adódik hozzá a következő óra kapacitásához.  A lefoglalt mennyiséget meghaladó használatra drágább, használatalapú díjak vonatkoznak.

## <a name="analyze-usage-data"></a>A használati adatok elemzése

Az alábbi szakaszok segítségével elemezheti a napi használati adatokat, hogy meghatározza, milyen mértékű a jellemző használat, és milyen foglalást érdemes beszereznie.

### <a name="analyze-usage-for-a-vm-reserved-instance-purchase"></a>Használat elemzése a fenntartott virtuálisgép-példányok vásárlásához

Határozza meg, milyen méretű virtuális gépet érdemes megvásárolnia. Az ES sorozat virtuális gépeihez vásárolt foglalás például nem érvényes az E sorozat virtuális gépeire, és fordítva.

A promóciós sorozatú virtuális gépek nem kapnak foglalási kedvezményt, ezért távolítsa el őket az elemzésből.

A jogosult virtuálisgép-használatok szűkítéséhez a következő szűrőket alkalmazza a használati adatokra:

- A **MeterCategory** kategóriát szűrje a **Virtuális gépek** elemre.
- Az **AdditionalInfo** elemből nyerje ki a **ServiceType** információkat. Ez az információ ad iránymutatást a megfelelő virtuálisgép-méretre. Például: Standard E32.
- A **ResourceLocation** mező használatával határozza meg a használati adatközpontot.

Hagyja figyelmen kívül a napi 24 óránál kevesebb használati idővel rendelkező erőforrásokat.

Ha a példányméretet családi szinten szeretné elemezni, akkor a [https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv) oldalon elérheti a példányméret rugalmassági értékeit. Az elemzéshez vonja össze ezeket az értékeket az adataival. A példányok méretrugalmasságára vonatkozó további információkért tekintse meg [a Reserved VM Instances virtuális gépeinek méretrugalmasságát](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md) ismertető cikket.

### <a name="analyze-usage-for-a-sql-database-reserved-instance-purchase"></a>Használat elemzése a fenntartott SQL Database-példányok vásárlásához

A lefoglalt kapacitás az SQL Database-adatbázisok virtuális magjainak számítási díjszabására vonatkozik. Nem vonatkozik a DTU-alapú díjszabásra, az SQL-licencek költségeire, illetve a nem számítási költségekre.

A jogosult SQL-használat szűkítéséhez alkalmazza a következő szűrőket a használati adatokon:


- A **MeterCategory** kategóriát szűrje az **SQL Database** elemre.
- A **MeterName** nevet szűrje a **virtuális mag** elemre.
- A **MeterSubCategory** kategóriát szűrje minden olyan használati rekordra, amelynek a nevében szerepel a _Compute_ (számítási) szó.

Az **AdditionalInfo** mezőből nyerje ki a **virtuális magok** értékét. Ez elárulja, hány virtuális mag volt használatban. A mennyiséget a **virtuális magok** számának és az adatbázis órában megadott használati idejének szorzata adja meg.

Ez az adat az alábbiak általános használatáról biztosít információkat:

- Adatbázistípus kombinációja. Például felügyelt példány vagy önálló adatbázisonkénti rugalmas készlet.
- Szolgáltatásszint. Például általános célú vagy üzleti szempontból kritikus.
- Generáció. Például 5. generáció.
- Resource Location (Erőforrás helye)

### <a name="analysis-for-sql-data-warehouse"></a>Elemzés az SQL Data Warehouse-hoz

A fenntartott kapacitás az SQL Data Warehouse DWU-használatára vonatkozik, és 100 DWU-s növekményekben vásárolható meg. A jogosult SQL-használat szűkítéséhez alkalmazza a következő szűrőket a használati adatokon:

- A **MeterName** nevet szűrje a **100 DWU** elemre.
- A **MeterSubCategory** kategóriát szűrje a **Számításra optimalizált Gen2** elemre.

A **ResourceLocation** mezőben határozza meg az SQL DW használatát egy régión belül.

Az SQL Data Warehouse-használat vertikálisan fel- és leskálázódhat a nap folyamán. Vegye fel a kapcsolatot az SQL Data Warehouse-példányt felügyelő csapattal, hogy többet tudjon meg a jellemző használati mutatókról.

Látogasson el az Azure Portal Foglalások területére, és vásároljon annyiszor 100 DWU fenntartott SQL Data Warehouse-kapacitást, amennyire szüksége van.

## <a name="reservation-purchase-recommendations"></a>Foglalásvásárlási javaslatok

A foglalásvásárlási javaslatokat az elmúlt 7, 30, illetve 60 nap órákra bontott használati adatainak elemzéséből számítjuk ki. Az Azure kiszámítja, hogy ha lett volna, milyen költségekkel járt volna a foglalás, és összehasonlítja az érintett időtartamban aktuálisan fizetett használat alapú költségekkel. A számítás során az adott időkereten belül használt összes mennyiséget figyelembe veszi. Olyan mennyiséget javasol, amely a lehető legnagyobb mértékű megtakarítással jár. 

Előfordulhat például, hogy az idő nagy részében 500 virtuális gépet használ, de időnként ez a szám eléri a 700-at is. Ebben az esetben az Azure mind az 500, mind a 700 virtuális géphez kiszámítja a megtakarítást. Mivel 700 virtuális gép használatára csak időnként kerül sor, a javaslatszámítás az 500 virtuálisgép-foglalás megvásárlása esetén elérhető maximális megtakarításra tesz javaslatot.

Vegye figyelembe a következő szempontokat:

- A foglalási javaslatokat az Önre vonatkozó igény szerinti használati díjak alapján számítjuk ki.
- A javaslatok egyéni méretekre vannak kiszámítva, nem példánycsalád méretére.
- A hatókörre vonatkozó ajánlott mennyiség azon a napon csökken, amikor megvásárolja a hatókörhöz tartozó foglalásokat.
    - A hatókörökre vonatkozó ajánlott foglalási mennyiség frissítése akár 25 napot is igénybe vehet. Ha például a megosztott hatókörre vonatkozó javaslatok alapján vásárol, az egy előfizetési hatókörre vonatkozó javaslatok módosítása akár 25 napot is igénybe vehet.

## <a name="recommendations-in-the-azure-portal"></a>Javaslatok az Azure Portalon

A javaslattételi alrendszer által kiszámolt foglalásvásárlásokat az **Azure Portal** [Ajánlott](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/docs) lapján találja. Lássunk egy példát.

![Kép az ajánlásokról](./media/determine-reservation-purchase/select-product-ri.png)

## <a name="recommendations-in-the-cost-management-power-bi-app"></a>Javaslatok a Cost Management Power BI-alkalmazásban

A Nagyvállalati Szerződéssel és a Microsoft Ügyfélszerződéssel rendelkező ügyfeleink használhatják a virtuális gépek fenntartottpéldány-lefedettségi (VM RI-lefedettségi) jelentéseit a virtuális gépekhez és a vásárlási javaslatokhoz. A lefedettségi jelentések a teljes használatot és a fenntartott példányok által lefedett használatot mutatják be.

1. Szerezze be a [Cost Management alkalmazást](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
2. Nyissa meg a VM RI-lefedettségi jelentést, és válassza ki a megosztott vagy egyszerű hatókört, attól függően, hogy melyikhez szeretne vásárolni.
3. Válassza ki a régiót és a példányméretcsaládot, hogy megtekinthesse a kiválasztott szűrő által szűrt használati, RI-lefedettségi és vásárlásjavaslati adatokat.

## <a name="recommendations-in-azure-advisor"></a>Javaslatok az Azure Advisorban

Az [Azure Advisorben](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview) is elérhetők foglalásvásárlási javaslatok.

- Az Advisorban csak egyelőfizetéses hatókörre érvényes javaslatok érhetők el.
- Az Advisor-javaslatokat az előző 30 nap alapján számítjuk ki. A kivetített megtakarítások egy 3 éves foglalási időszakra vonatkoznak.
- Ha megosztott hatókörrel rendelkező foglalást vásárol, az Advisor foglalásvásárlási javaslatai legfeljebb 30 napig láthatók.

## <a name="recommendations-using-apis"></a>Javaslatok API-k használatával

A [Reservation Recommendations](/rest/api/consumption/reservationrecommendations/list) REST API-val programozott módon tekintheti meg a javaslatokat.

## <a name="next-steps"></a>További lépések

- [Az Azure Reservations kezelése](manage-reserved-vm-instance.md)
- [A foglalási használat ismertetése használatalapú fizetéses előfizetés esetén](understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)
- [A Reservations díjában nem szereplő Windows-szoftverköltségek](reserved-instance-windows-software-costs.md)