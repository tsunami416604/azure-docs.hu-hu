---
title: A fő metrikák megtekintése Cloudyn-irányítópultokkal az Azure-ban
description: Ez a cikk azt ismerteti, hogyan tekintheti meg a fő metrikákat a Cloudyn irányítópultjaival.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: vitavor
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 03d5e3959b85a2b0bd9ae30744051895ff026b77
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691224"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>A fő költségmetrikák megtekintése irányítópultokkal

A Cloudyn irányítópultjain a jelentések áttekintése látható. Az irányítópultokkal egyetlen nézetben tekintheti meg a fő költségmetrikákat. Az irányítópultok az üzleti trendekkel kapcsolatos fontos információkat is biztosítanak, megkönnyítve ezzel a fontos üzleti döntések meghozatalát.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

Az irányítópultokkal emellett nézetek is létrehozhatók a szervezet különböző felelősségi köröket ellátó munkatársainak, akik például az alábbiak lehetnek:

- pénzügyi ellenőr,
- alkalmazás- vagy projekttulajdonos,
- DevOps-mérnök,
- felső vezetők.

Az irányítópultokat widgetek alkotják, és az egyes widgetek tulajdonképpen a jelentések miniatűrjei. A widgetekre kattintva nyithatja meg a hozzájuk tartozó jelentést. A jelentések testreszabását követően a My Reports (Saját jelentések) közé mentheti a jelentéseket, és a rendszer hozzáadja őket az irányítópulthoz.

Az irányítópult-verziók különböznek a felügyeleti (MSP), a vállalati és a prémium szintű Cloudyn-felhasználók esetében. A különbségeket az entitás-hozzáférési szintek határozzák meg. Az entitás-hozzáférési szintekkel kapcsolatban az [entitás-hozzáférési szinteket](tutorial-user-access.md#entity-access-levels) ismertető fejezetben talál további információt.

Az irányítópultok elérhetősége az irányítópultok megtekintésekor használt felhőszolgáltatói fiók típusától függ. A rendelkezésre álló és a Cloudyn által gyűjtött információk típusa hatással van az irányítópultokon található jelentésekre. Nem láthatja például az S3 Tracker (S3-követő) irányítópultot, ha nem rendelkezik AWS-fiókkal. Hasonlóképpen, ha nem engedélyezi az Azure Resource Managernek a Cloudyn elérését, akkor az Azure-ra vonatkozó információk nem jelennek meg az Optimizer (Optimalizáló) irányítópult widgetjeiben.

Használhatja az előre elkészített irányítópultokat, vagy létrehozhat saját, egyéni jelentésekkel rendelkező irányítópultokat is. Ha korábban még nem használta a Cloudyn jelentéseit, olvassa el a [Cloudyn-jelentések használatát](use-reports.md) ismertető cikket.

## <a name="create-a-custom-dashboard"></a>Egyéni irányítópult létrehozása

Az egyéni irányítópult gyors használatának megkezdéséhez duplikálhat egy meglévő irányítópultot, és felhasználja annak tulajdonságait. Az irányítópultot után az igényei szerint módosíthatja. Kattintson a **Save as** (Mentés másként) parancsra a másolni kívánt irányítópulton. Csak a testre szabott irányítópultokat duplikálhatja, a Cloudyn saját irányítópultjait nem.

Egyéni irányítópult létrehozása:

1. Kattintson az **Add New +** (Új hozzáadása +) parancsra a kezdőlapon. Megjelenik a My Dashboard (Saját irányítópult) lap.  
    ![A My Dashboard (Saját irányítópult) lap, amelyen új jelentéseket vehet fel](./media/dashboards/my-dashboard.png)
2. Kattintson az **Add New Report** (Új jelentés felvétele) elemre. Megjelenik az Add Report (Jelentés felvétele) párbeszédpanel.
3. Válassza ki az irányítópult-widgethez hozzáadni kívánt jelentést. A rendszer hozzáadja a widgetet az irányítópulthoz.
4. Ismételje meg az előző lépéseket, amíg el nem készül az irányítópulttal.
5. Az irányítópult nevének módosításához kattintson a nevére az irányítópult kezdőlapján, majd írja az új nevet.

## <a name="modify-a-custom-dashboard"></a>Egyéni irányítópult módosítása

Az egyéni irányítópultok létrehozásához hasonlóan a Cloudyn saját irányítópultjai nem módosíthatók. Egyéni irányítópult-jelentés módosítása:

1. Keresse meg az irányítópulton a módosítani kívánt jelentést, majd kattintson az **Edit** (Szerkesztés) elemre. Megjelenik a jelentés.
2. Hajtsa végre a kívánt módosításokat a jelentésben, majd kattintson a **Save** (Mentés) parancsra. A rendszer frissíti jelentést, amely megjeleníti a módosításokat.

## <a name="share-a-custom-dashboard"></a>Egyéni irányítópult megosztása

Egyéni irányítópultokat a _Public_ (Nyilvános) vagy a _My Entity_ (Saját entitás) beállítással oszthat meg. A Public (Nyilvános) beállítással történő megosztás esetén az összes felhasználó megtekintheti az irányítópultot. A My Entity (Saját entitás) beállítással történő megosztás esetén csak az aktuális entitáshoz hozzáféréssel rendelkező felhasználók tekinthetik meg az irányítópultot. Az egyéni irányítópultok Public (Nyilvános) vagy My Entity (Saját entitás) beállítással történő megosztásának lépései hasonlók.

Egyéni irányítópult megosztása a Public (Nyilvános) beállítással:

1. Kattintson az irányítópult **Dashboard Settings** (Irányítópult beállításai) elemére. Megjelenik a Dashboard Settings (Irányítópult beállításai) párbeszédpanel.  
    ![egyéni irányítópult irányítópult-beállításai](./media/dashboards/dashboard-options.png)
2. A Dashboard Settings (Irányítópult beállításai) párbeszédpanelen kattintson a nyíl szimbólumra, majd a **Public** (Nyilvános) elemre. Megjelenik a megerősítést kérő Public Dashboard (Nyilvános irányítópult) párbeszédpanel.
3. Kattintson a **Yes** (Igen) gombra. Az irányítópult elérhetővé vált mások számára.

## <a name="delete-a-custom-dashboard-report"></a>Egyéni irányítópult-jelentés törlése

Törölhet egyéni jelentés-összetevőket az irányítópultról. A jelentés irányítópultról való törlése nem törli a jelentést a jelentések listájából. A törlés csak az irányítópultról távolítja el a jelentést.

Irányítópult-összetevő törléséhez kattintson az irányítópult-összetevő **Delete** (Törlés) elemére. A **Delete** (Törlés) elemre kattintva azonnal törli az irányítópult-összetevőt.

## <a name="share-a-dashboard-enterprise"></a>Irányítópult megosztása (vállalat)

Az egyéni irányítópultokat megoszthatja a szervezet összes felhasználójával, vagy csak az aktuális entitás felhasználóival. Az irányítópultok megosztásával mások számára is áttekintést biztosíthat a KPI-kről. Amikor megoszt egy irányítópultot, azzal automatikusan replikálja az összes Cloudyn-entitás/-ügyfél számára. A megosztott irányítópult módosításait a rendszer automatikusan frissíti.

Irányítópult megosztása az összes felhasználóval, az alentitásokat is beleértve:

1. Kattintson az irányítópult kezdőlapjának **Edit** (Szerkesztés) elemére.
2. Kattintson a **Share** (Megosztás) parancsra, és válassza a **Public** (Nyilvános) lehetőséget.
3. Megjelenik a megerősítést kérő Global Public Dashboard (Globálisan nyilvános irányítópult) párbeszédpanel.
4. Kattintson a **Yes** (Igen) gombra az irányítópult globálisan nyilvános irányítópultként történő beállításához.

Irányítópult megosztása az aktuális entitás összes felhasználójával:

1. Kattintson az irányítópult kezdőlapjának **Edit** (Szerkesztés) elemére.
2. Kattintson a **Share** (Megosztás) parancsra, és válassza a **My Entity** (Saját entitás) lehetőséget.
3. Kattintson a **Yes** (Igen) gombra az irányítópult nyilvános irányítópultként történő beállításához.

## <a name="duplicate-a-custom-dashboard"></a>Egyéni irányítópult duplikálása

Új irányítópult létrehozásakor érdemes lehet felhasználni egy már meglévő irányítópult hasonló tulajdonságait. A meglévő irányítópult duplikálásával létrehozhat egy új irányítópultot.

Csak egyéni irányítópultokat duplikálhat. A szabványos irányítópultok nem duplikálhatók.

Egyéni irányítópult duplikálása (klónozása):

1. Kattintson a **Save As** (Mentés másként) parancsra a duplikálni kívánt irányítópulton. Megnyílik egy új irányítópult ugyanazzal a névvel és egy számmal.
2. Nevezze át a duplikált irányítópultot, majd hajtsa végre a kívánt módosításokat.

– Vagy –

1. A Dashboard Settings (Irányítópult beállításai) között kattintson a **Save As** (Mentés másként) elemre annak az irányítópultnak a sorában, amelyet duplikálni kíván.
2. Megnyílik a duplikált irányítópult.
3. Nevezze át az irányítópultot, majd hajtsa végre a kívánt módosításokat.

## <a name="set-a-default-dashboard"></a>Alapértelmezett irányítópult beállítása

Bármelyik irányítópultot beállíthatja alapértelmezettként. Az alapértelmezettként beállított irányítópult a legszélső bal oldali lapként jelenik meg az irányítópultlapok listájában. Az alapértelmezett irányítópult nyílik meg a Cloudyn portál megnyitásakor.

- Kattintson arra az irányítópultlapra, amelyet alapértelmezettnek szeretne beállítani, majd kattintson a **Default** (Alapértelmezett) elemre a jobb oldalon.

– Vagy –

1. Az elérhető irányítópultok listájának megtekintéséhet kattintson a **Dashboard Settings** (Irányítópult beállításai) elemre, majd válassza ki azt az irányítópultot, amelyet alapértelmezettként szeretne beállítani.  
    ![irányítópult-beállítások alapértelmezett irányítópult beállításához](./media/dashboards/dashboard-options.png)
2. Kattintson a **Default** (Alapértelmezett) elemre az irányítópult sorában. Megjelenik a megerősítést kérő Default Dashboard (Alapértelmezett irányítópult) párbeszédpanel.
3. Kattintson a **Yes** (Igen) gombra. Az irányítópultot a rendszer alapértelmezettként állítja be.

## <a name="management-dashboard"></a>Kezelési irányítópult
A Management (Felügyelet) (vagy MSP-felhasználók esetén MSP) irányítópult a fő jelentéstípusokkal kapcsolatos fontos információkat jeleníti meg.  
![A különféle jelentéseket megjelenítő Management (Felügyelet) irányítópult](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Cost Entity Summary (Költségelem összegzése) (csak vállalat)
Ez a widget a felügyelt költségentitásokat összegzi, beleértve az entitások és a fiókok számát.
- A widgetre kattintva az Enterprise Details (Vállalati adatok) jelentést nyithatja meg.

### <a name="cost-over-time"></a>Cost Over Time (Költségek az adott időszakban)
Ez a widget a költségtrendek felméréséhez nyújthat segítséget. A legutóbbi nap költségét jeleníti meg az elmúlt 30 nap trendje alapján.
- A widgetre kattintva az Actual Cost Over Time (Tényleges költségek az adott időszakban) jelentést nyithatja meg, amelyben további részleteket tekinthet meg és szűrhet.

### <a name="asset-controller"></a>Asset Controller (Eszközkontroller)
Ez a widget az előző nap óta futó példányok számát jeleníti meg az elmúlt 30 nap használati trendje felett.
- A widgetre kattintva az Asset Controller (Eszközkontroller) irányítópultot nyithatja meg.

### <a name="unused-ri-detector"></a>Unused RI Detector (Nem használt fenntartott példányok érzékelője)
Ez a widget a nem használt Amazon EC2-foglalásokat jeleníti meg.
- A widgetre kattintva a Currently Unused Reservations (Jelenleg nem használt foglalások) jelentést nyithatja meg, amelyben a módosítható, nem használt foglalásokat tekintheti meg.

### <a name="cost-by-service"></a>Cost by Service (Díj szolgáltatások szerint)
Ez a widget az amortizált költségeket jeleníti meg szolgáltatások szerint az elmúlt 30 napra vonatkozóan. A szolgáltatásonkénti költségek megtekintéséhez helyezze a mutatót a tortadiagram fölé.
- A widgetre kattintva az Actual Cost Analysis (Tényleges költség elemzése) jelentést nyithatja meg.

### <a name="potential-savings"></a>Potential savings (Lehetséges megtakarítás)
Ez a widget a példánytípusok díjszabásához kapcsolódó javaslatokat jelenít meg az Amazon EC2-re és az Amazon RDS-re vonatkozóan.
- A widgetre kattintva a Savings Analysis (Megtakarításelemzés) jelentést nyithatja meg. A jelentés példánytípusok szerint sorolja fel a költségeket a lehetséges megtakarítással együtt.

### <a name="compute-instances---daily-trend"></a>Compute Instances - Daily Trend (Számítási példányok – Napi trend)
Ez a widget az aktív példányokat jeleníti meg típus szerint az elmúlt 30 napra vonatkozóan.
- A widgetre kattintva az Instances Over Time (Példányok az adott időszakban) jelentést nyithatja meg, amelyben megtekintheti az elmúlt 30 napban futott összes példány részletezését.

### <a name="storage-by-department"></a>Storage by department (Tárterület részlegenként)
Ez a widget a használt tárolási szolgáltatásokat jeleníti meg részlegek szerint. A tárterület-használat részlegek szerinti megtekintéséhez helyezze a mutatót a tortadiagram fölé.
- A widgetre kattintva az S3 Tracker (S3-követő) irányítópultot nyithatja meg.

## <a name="cost-controller-dashboard"></a>Cost Controller (Költségkontroller) irányítópult
A Cost Controller (Költségkontroller) irányítópult az előre beállított költséglefoglalással kapcsolatos fő információkat jeleníti meg.  
![A különféle jelentéseket megjelenítő Cost Controller (Költségkontroller) irányítópult](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Cost Over Time (Költségek az adott időszakban)
Ez a widget a költségtrendek felméréséhez nyújt segítséget. A legutóbbi nap költségét jeleníti meg az elmúlt 30 nap trendje alapján.
- A widgetre kattintva az Actual Cost Over Time (Tényleges költségek az adott időszakban) jelentést nyithatja meg, amelyben további részleteket tekinthet meg és szűrhet.

### <a name="monthly-cost-trends"></a>Monthly Cost Trends (Havi költségtrendek)
Ez a widget a várható amortizált kiadást és a hónap kezdetétől számított tényleges kiadást jeleníti meg.
- A widgetre kattintva a Current Month Projected Cost (Aktuális havi előre jelzett költség) jelentést nyithatja meg, amely a hónap adott napjáig tartó költségösszefoglalót biztosít.

Ez a jelentés a hónap elejétől felmerült költséget, az előző havi költséget, valamint az aktuális havi előre jelzett költséget jeleníti meg. Az aktuális havi előre jelzett költség számítása az adott napig felmerült költség és az előrejelzés összeadásával történik. Az előrejelzés az elmúlt 30 napban monitorozott költségen alapul.

### <a name="12-month-planner"></a>12 Month Planner (12 hónapos tervező)
Ez a widget a következő 12 hónapban várható költséget és a lehetséges megtakarítást jeleníti meg.
- A widgetre kattintva az Annual Projected Cost (Éves előre jelzett költség) jelentést nyithatja meg.

### <a name="cost-by-service"></a>Cost by Service (Díj szolgáltatások szerint)
Ez a widget az amortizált költségeket jeleníti meg szolgáltatások szerint az elmúlt 30 napra vonatkozóan.
- A szolgáltatásonkénti költségek megtekintéséhez helyezze a mutatót a tortadiagram fölé.
- A widgetre kattintva az Actual Cost Analysis (Tényleges költség elemzése) jelentést nyithatja meg.

### <a name="cost-by-account"></a>Cost by Account (Költség fiókok szerint)
Ez a widget az amortizált költségeket jeleníti meg fiókok szerint az elmúlt 30 napra vonatkozóan.
- A fiókonkénti költségek megtekintéséhez helyezze a mutatót a tortadiagram fölé.
- A widgetre kattintva az Actual Cost Analysis (Tényleges költség elemzése) jelentést nyithatja meg.

### <a name="cost-trend-by-day"></a>Cost Trend by Day (Költségtrend napok szerint)
Ez a widget a kiadást jelenti meg az elmúlt 30 napra vonatkozóan.
- A naponkénti költségek megtekintéséhez helyezze a mutatót a sávdiagram fölé.
- A widgetre kattintva az Actual Cost Over Time (Tényleges költségek az adott időszakban) jelentést nyithatja meg.

### <a name="cost-trend-by-month---last-6-months"></a>Cost Trend by Month - Last 6 months (Költségtrend hónapok szerint – Elmúlt 6 hónap)

Ez a widget az elmúlt hat havi kiadást jeleníti meg.
- A havonkénti költségek megtekintéséhez helyezze a mutatót a sávdiagram fölé.
- A widgetre kattintva az Actual Cost Over Time (Tényleges költségek az adott időszakban) jelentést nyithatja meg.

## <a name="asset-controller-dashboard"></a>Asset Controller (Eszközkontroller) irányítópult

Ez az irányítópult a futó példányok számát, a rendelkezésre álló és használatban lévő lemezeket, a példánytípusok eloszlását, valamint a tárterülettel kapcsolatos információkat jeleníti meg.  
![A különféle jelentéseket megjelenítő Asset Controller (Eszközkontroller) irányítópult](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Compute Instances (Számítási példányok)
Ez a widget a futó példányok számát jeleníti meg az elmúlt 30 nap használati trendje alapján.
- A widgetre kattintva az Instances Over Time (Példányok az adott időszakban) jelentést nyithatja meg.

### <a name="disks"></a>Lemezek
Ez a widget a használatban lévő és rendelkezésre álló lemezek teljes számát és köteteit jeleníti meg.
- A widgetre kattintva az Active Disks (Aktív lemezek) jelentést nyithatja meg.

### <a name="instance-type-distribution"></a>Instance Type Distribution (Példánytípus-eloszlás)
Ez a widget a példánytípusokat jeleníti meg egy tortadiagramban.
- A widgetre kattintva az Instance Distribution (Példányeloszlás) jelentést nyithatja meg, amely az aktív példányok összesítését biztosítja a kiválasztott összesítés szerint.

### <a name="compute-instances---daily-trend"></a>Compute Instances - Daily Trend (Számítási példányok – Napi trend)
Ez a widget a számítási példányokat (kihasználatlan, lefoglalt és igény szerinti) jeleníti meg napi bontásban az elmúlt 30 napra vonatkozóan.
- A számítási példányok típusonkénti és naponkénti számának megtekintéséhez helyezze a mutatót a diagram fölé.
- A widgetre kattintva az Instances Over Time (Példányok az adott időszakban) jelentést nyithatja meg.

### <a name="all-buckets-s3"></a>All Buckets (S3) (Az összes gyűjtő (S3))
Ez a widget a teljes S3-tárterületet és a tárolt objektumok számát jeleníti meg.
- A widgetre kattintva az S3 Tracker (S3-követő) irányítópultot nyithatja meg. Az irányítópult segítségével megkeresheti, elemezheti és megjelenítheti a tárterület aktuális használatát és használati trendjeit.

### <a name="sql-db-instances-rds"></a>SQL DB Instances (RDS) (SQL DB-példányok (RDS))
Ez a widget a futó Amazon RDS-példányok számát jeleníti meg az elmúlt 30 nap trendje alapján.
- A widgetre kattintva az RDS Instance Over Time (RDS-példányok az adott időszakban) jelentést nyithatja meg.

## <a name="optimizer-dashboard"></a>Optimizer (Optimalizáló) irányítópult
Ez az irányítópult a méretcsökkentéssel kapcsolatos javaslatokat, a nem használt erőforrásokat és a lehetséges megtakarítást jeleníti meg.  
![A különféle jelentéseket megjelenítő Optimizer (Optimalizáló) irányítópult](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>RI Calculator (Fenntartottpéldány-kalkulátor)
Ez a widget a fenntartott példányok vásárlására vonatkozó javaslatokat, valamint a lehetséges éves megtakarítást jeleníti meg.
- A widgetre kattintva a Reserved Instance Calculator (Fenntartottpéldány-kalkulátor) eszközt nyithatja meg, amelyben meghatározhatja, hogy mikor érdemes igény szerinti vagy foglaláson alapuló díjszabást alkalmazni.

### <a name="sizing"></a>Méretezés
Ez a widget a javasolt méreteket, illetve a méretek implementálása esetén elérhető megtakarítást jeleníti meg.
- A widgetre kattintva az EC2 Cost Effective Sizing Recommendations (Költséghatékony EC2-méretezésre vonatkozó javaslatok) jelentést nyithatja meg.

### <a name="unused-ri-detector"></a>Unused RI Detector (Nem használt fenntartott példányok érzékelője)
Ez a widget a nem használt Amazon EC2-foglalásokat jeleníti meg.
- A widgetre kattintva a Currently Unused Reservations (Jelenleg nem használt foglalások) jelentést nyithatja meg, amelyben a nem használt, módosítható foglalásokat tekintheti meg.

###  <a name="available-disks"></a>Available Disks (Rendelkezésre álló lemezek)
Ez a widget az üzemi környezet nem csatolt lemezeinek a számát jeleníti meg.
- A widgetre kattintva az Unattached Disks (Nem csatolt lemezek) jelentést nyithatja meg.

### <a name="rds-ri-calculator"></a>RDS RI Calculator (Fenntartott RDS-példányok kalkulátora)
Ez a widget az Amazon RDS-példányokra vonatkozó foglalási javaslatok számát, valamint a lehetséges megtakarítást jeleníti meg.
- A widgetre kattintva az RDS RI Buying Recommendations (Fenntartott RDS-példány vásárlására vonatkozó javaslatok) jelentést nyithatja meg, amelyben az igény szerinti példányok helyett fenntartott példányok használatára vonatkozó Cloudyn-javaslatokat tekintheti meg.

### <a name="rds-sizing"></a>RDS Sizing (RDS-méretezés)
Ez a widget a méretezési javaslatok számát, valamint a lehetséges megtakarítást jeleníti meg.
- A widgetre kattintva a RDS Sizing Recommendations (RDS-méretezési javaslatok) jelentést nyithatja meg, amely az Amazon RDS méretezésére vonatkozó részletes javaslatokat jelenít meg.

Az optimalizálási javaslatok az elmúlt hónapban monitorozott használati és teljesítményadatokon alapulnak.

## <a name="s3-tracker-dashboard"></a>S3 Tracker (S3-követő) irányítópult
Az S3 Tracker (S3-követő) irányítópult segítségével megkeresheti, elemezheti és megjelenítheti a tárterület aktuális használatát és használati trendjeit.  
![A különféle jelentéseket megjelenítő S3 Tracker (S3-követő) irányítópult](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>All Buckets (Az összes gyűjtő)
Ez a widget az összes gyűjtő teljes méretét (GB-ban) jeleníti meg a gyűjtőkben tárolt objektumok teljes száma mellett.
- A widgetre kattintva a Distribution of S3 Size (S3-méret eloszlása) jelentést nyithatja meg. A jelentés segítségével gyűjtő, felső szintű mappa, tárolási osztály és verziószámozási állapot alapján elemezheti az S3-méretet.

### <a name="bucket-properties"></a>Bucket Properties (Gyűjtőtulajdonságok)
Ez a widget a tárgyűjtők teljes számát jeleníti meg.
- A widgetre kattintva az S3 Bucket Properties (S3-gyűjtőtulajdonságok) jelentést tekintheti meg.

### <a name="scan-status"></a>Scan Status (Vizsgálat állapota)
Ez a widget a legutóbbi S3-vizsgálat végrehajtásának és a következő vizsgálat indításának az idejét jeleníti meg.
- A widgetre kattintva a S3 Scan Status (S3-vizsgálat állapota) jelentést nyithatja meg.

### <a name="storage-by-bucket"></a>Storage by Bucket (Tárterület gyűjtők szerint)
Ez a widget az egyes gyűjtőtároló-osztályok által használt százalékos arányt jeleníti meg.
- A widgetre kattintva a Distribution of S3 Size (S3-méret eloszlása) jelentést nyithatja meg. A jelentés segítségével gyűjtő, felső szintű mappa, tárolási osztály és verziószámozási állapot alapján elemezheti az S3-méretet.

### <a name="number-of-objects-by-bucket"></a>Number of Objects by Bucket (Objektumok száma gyűjtők szerint)
Ez a widget az objektumok gyűjtőnkénti tényleges számát és százalékos arányát jeleníti meg. A mutatót az egyes gyűjtők fölé helyezve tekintheti meg az objektumok teljes számát.
- A widgetre kattintva a Distribution of S3 Size (S3-méret eloszlása) jelentést (vizsgálatalapú) nyithatja meg.

## <a name="cloud-comparison-dashboard"></a>Cloud Comparison (Felhőalapú összehasonlítás) irányítópult
A Cloud Comparison (Felhőalapú összehasonlítás) irányítópulttal összehasonlíthatja a különböző felhőszolgáltatók költségeit a díjszabás, CPU-típus és RAM-méret alapján.  
![A különféle jelentéseket megjelenítő Cloud Comparison (Felhőalapú összehasonlítás) irányítópult](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>EC2 Cost in Azure by Instance Type (EC2-költség az Azure-ban példánytípusok szerint)
Ez a widget az elmúlt 30 napi használattal járó igény szerinti árakat jeleníti meg. Összehasonlítja a költséget az aktuális Amazon EC2-költséggel és az Azure-ban felmerülő lehetséges költséggel.
- A példánytípusonkénti költségek megtekintéséhez helyezze a mutatót az oszlopok fölé.
- A widgetre kattintva a Porting Your Deployment – Cost Analysis (Az üzemi környezet áthelyezése – Költségelemzés) jelentést nyithatja meg.

### <a name="ec2-cost-in-azure"></a>EC2 Cost in Azure (EC2-költség az Azure-ban)
Ez a widget az aktuális Amazon EC2-költségeket jeleníti meg, és összehasonlítja őket az Azure-ral. Az összehasonlítás az elmúlt 30 napi használattal járó igény szerinti árakon alapul.
- A widgetre kattintva a Porting Your Deployment - Cost Analysis jelentést (Az üzemi környezet áthelyezése – Költségelemzés) jelentést nyithatja meg.

### <a name="ec2azure-instance-type-mapping"></a>EC2/Azure Instance Type Mapping (EC2-/Azure-példánytípusok megfeleltetése)
Ez a widget a rugalmas számítási egységeknek az Amazon EC2 és az Azure közötti legjobb megfeleltetését jeleníti meg.
- A widgetre kattintva az Instances Type Mappig (Példánytípusok megfeleltetése) jelentést nyithatja meg.

## <a name="next-steps"></a>További lépések
- Ha szeretne többet megtudni a jelentésekről, olvassa el a [Cloudyn-jelentések használatát](use-reports.md) ismertető cikket.
