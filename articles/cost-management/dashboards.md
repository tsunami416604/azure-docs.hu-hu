---
title: A Cloudyn irányítópultjain fontos metrikákat jeleníthet meg az Azure-ban |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan irányítópultokkal fontos metrikákat jeleníthet meg a Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 1d2b4a0cd78f3d68a76bd9186be4174f2dd1d86c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999708"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>Költségeket az irányítópultok metrikák nézet kulcs

A Cloudyn irányítópultjain jelentések magas szintű áttekintést nyújt. Az irányítópultok lehetővé teszi kulcs költség metrikákat tekinthet meg egyetlen nézetben. Ezenkívül tartalmaznak üzleti trend kiemeli annak érdekében, hogy fontos üzleti döntéseket hozhasson.

Az irányítópultok is használják nézeteket hozhat létre a különböző személyek a szervezetben, többek között előfordulhat, hogy:

- Pénzügyi vezérlő
- Alkalmazás- vagy projekt tulajdonosa
- DevOps-mérnök
- A vezetők

Az irányítópultok widgetek épülnek fel, és minden widget lényegében egy jelentés miniatűr. Kattintson egy widget nyissa meg a jelentést. Jelentések testreszabása, mentse azokat a jelentések mappa, és a rendszer hozzáadja az irányítópultot.

Irányítópult-verziók kezelése (MSP), Enterprise és prémium szintű Cloudyn felhasználók esetében eltérőek. A különbségek entitások hozzáférési szintjeit határozzák meg. Hozzáférési szintekkel kapcsolatos további információkért lásd: [entitások hozzáférési szintjeit](tutorial-user-access.md#entity-access-levels).

Irányítópult rendelkezésre állási attól függ, hogy a cloud service provider használt fiók irányítópultok megtekintésekor típusát. Elérhető és a Cloudyn által összegyűjtött információ típusa hatással van a jelentések az irányítópultokkal. Például ha AWS-fiók nem rendelkezik majd meg nem jelenik meg az S3-követő irányítópultot. Ehhez hasonlóan ha nem engedélyezi az Azure Resource Manager hozzáférhetnek, majd meg nem jelenik meg az irányítópult widgetek optimalizáló bármely Azure-ra vonatkozó információkat.

Az előzetesen irányítópultok bármelyikét használhatja, vagy testre szabott jelentéseket hozhat létre saját irányítópultját. Ha még nem ismeri a Cloudyn-jelentések, [használja a Cloudyn-jelentések](use-reports.md).

## <a name="create-a-custom-dashboard"></a>Egyéni irányítópult létrehozása

A használatának gyors megkezdése egy egyéni irányítópult, duplikálhatja tulajdonságainak használata egy meglévő. Majd módosíthatja azt, hogy illeszkedjen az igényeihez. A másolni kívánt irányítópultján kattintson **Mentés másként**. Személyre szabott irányítópultok csak duplikálhatja – az irányítópultok, a Cloudyn, nem ismétlődő.

Egyéni irányítópult létrehozása:

1. A kezdőlapon kattintson **új hozzáadása +**. Saját irányítópult-oldalon jelenik meg.  
    ![Saját irányítópult](./media/dashboards/my-dashboard.png)
2. Kattintson a **adja hozzá az új jelentés**. A jelentés hozzáadása jelenik meg.
3. Válassza ki a jelentést, az irányítópult-widgethez hozzáadni kívánt. A widget hozzá van adva az irányítópulthoz.
4. Ismételje meg a fenti lépéseket, mindaddig, amíg befejeződik az irányítópulton.
5. Ha módosítani szeretné az irányítópult nevét, az az irányítópult nevét, az irányítópult kezdőlapján kattintson, és írja be az új nevet.

## <a name="modify-a-custom-dashboard"></a>Egyéni irányítópult módosítása

Egyéni irányítópult létrehozása, például a cloudyn foglalt irányítópultokon nem módosíthatja. Egyéni irányítópult jelentés módosítása:

1. Az irányítópulton, keresse meg a jelentés módosítása, és kattintson a kívánt **szerkesztése**. A jelentés jelenik meg.
2. Végezze el a jelentést szeretne készíteni, és kattintson a módosításokat **mentése**. A jelentés frissül, és megjeleníti a módosításokat.

## <a name="share-a-custom-dashboard"></a>Egyéni irányítópult megosztása

Egyéni irányítópult megoszthatja másokkal, _nyilvános_ vagy _saját entitás_. Nyilvánosan megosztott a minden felhasználó megtekintheti az irányítópulton. Csak az aktuális entitás hozzáféréssel rendelkező felhasználók megtekinthetik az irányítópultot saját entitáshoz megosztásakor. Egyéni irányítópult megosztása a nyilvános és a saját entitás lépései hasonlóak.

A nyilvános egyéni irányítópult megosztása:

1. Az irányítópulton kattintson **irányítópult beállításai**. Az irányítópult beállításai jelenik meg.  
    ![Irányítópult beállításai](./media/dashboards/dashboard-options.png)
2. Az irányítópult beállításai a boxban, kattintson a nyílra, és kattintson a **nyilvános**. A nyilvános irányítópult megerősítő párbeszédpanel jelenik meg.
3. Kattintson a **Igen**. Az irányítópult már másoknak is elérhető.

## <a name="delete-a-custom-dashboard-report"></a>Egyéni irányítópult jelentés törlése

Egy egyéni jelentés összetevő törölheti az irányítópultról. A jelentés az irányítópulton nem törlése a jelentés a jelentések listából. Ehelyett a jelentés törlésével eltávolítja azt az csak az irányítópulton.

Egy irányítópult-összetevő, az irányítópult összetevőre törléséhez kattintson **törlése**. Kattintson a **törlése** azonnal törli az irányítópult összetevő.

## <a name="share-a-dashboard-enterprise"></a>(Enterprise) irányítópult megosztása

Minden felhasználó egyéni irányítópultokat megoszthatja a munkahelyén vagy az aktuális entitás felhasználóival. Irányítópult megosztásának teheti másoknak a KPI gyors magas szintű áttekintést. Ha megoszt egy irányítópultot, azt automatikusan replikálja az irányítópult összes Cloudyn entitásokat és ügyfeleinek. A megosztott irányítópultra mutató változtatások automatikusan frissülnek.

Irányítópult megosztása minden felhasználó subentities is:

1. Az irányítópult kezdőlapján kattintson **szerkesztése**.
2. Kattintson a **megosztás** majd **nyilvános**.
3. A globális nyilvános irányítópult a megerősítést kérő párbeszédpanelen jelenik meg.
4. Kattintson a **Igen** beállítása az irányítópult egy globális nyilvános irányítópultként.

Aktuális entitás minden felhasználó megosztani Önnel egy irányítópultot:

1. Az irányítópult kezdőlapján kattintson **szerkesztése**.
2. Kattintson a **megosztás** majd **saját entitás**.
3. Kattintson a **Igen** nyilvános irányítópultként beállítani a az irányítópulton.

## <a name="duplicate-a-custom-dashboard"></a>Egyéni irányítópult megkettőzése

Amikor létrehoz egy új irányítópultot, érdemes használni egy meglévő irányítópultot hasonló tulajdonságait. Másolhatja az irányítópulton, hozzon létre egy újat.

Csak egyéni irányítópultokat készíthet másolatot. Standard szintű irányítópultok nem ismétlődő.

A (Klónozás) egyéni irányítópult megkettőzése:

1. Az ismétlődő kívánt irányítópultján kattintson **Mentés másként**. Ugyanazzal a névvel és a egy szám megnyílik egy új irányítópultot.
2. Az ismétlődő irányítópult átnevezése, és tetszés szerint módosíthatja azt.

– Vagy –

1. Kattintson az irányítópult-beállítások **Mentés másként** az irányítópult megkettőzése kívánt sorában.
2. Az ismétlődő irányítópult nyílik meg.
3. Átnevezheti az irányítópultot, és tetszés szerint módosíthatja azt.

## <a name="set-a-default-dashboard"></a>Alapértelmezett irányítópult beállítása

Az alapértelmezett állítható bármelyik irányítópult. Az alapértelmezett értékre állítaná teszi, hogy az irányítópult-lapok listája a bal szélső lapján jelennek meg. Az alapértelmezett irányítópulton jeleníti meg, ha a Cloudyn portál megnyitása.

- Kattintson az alapértelmezett, majd kattintson a kívánt irányítópult fület **alapértelmezett** a jobb oldalon.

– Vagy –

1. Kattintson a **irányítópult beállításai** elérhető irányítópultok listáját, és válassza ki az alapértelmezettként használni kívánt irányítópultot.  
    ![Irányítópult beállításai](./media/dashboards/dashboard-options.png)
2. Kattintson a **alapértelmezett** az irányítópult a sorban. Az alapértelmezett irányítópult a megerősítést kérő párbeszédpanelen jelenik meg.
3. Kattintson a **Yes** (Igen) gombra. Az Irányítópult alapértelmezett értéke.

## <a name="management-dashboard"></a>Kezelési irányítópult
A Management (vagy MSP-irányítópult MSP-felhasználók) irányítópultot tartalmazza a fő típusú emeli ki.  
![Kezelési irányítópult](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Költségelem összegzése (csak Enterprise)
A widget a felügyelt költség entitásokkal, többek között, az entitások és fiókok számát foglalja össze.
- Kattintson a widget nyissa meg a vállalati részletei jelentést.

### <a name="cost-over-time"></a>Költségek időbeli alakulása
A widget segíthetnek a költségek trendek felismerése. Kiemeli a költségek az utolsó napjára, a trend az elmúlt 30 nap alapján.
- Kattintson a widget nyissa meg a tényleges időalapú költségeket tartalmazó jelentést megtekintéséhez és szűrheti a további részleteket.

### <a name="asset-controller"></a>Adategység-vezérlő
A widget kiemeli a futó példányok az előző nap, a használati trend az elmúlt 30 napban fent száma.
- Kattintson a widget az adategység-vezérlő irányítópult megnyitásához.

### <a name="unused-ri-detector"></a>A fel nem használt fenntartott példányok detector használatával
A widget kiemeli az Amazon EC2-é száma nem használt foglalások.
- Kattintson a widget nyissa meg a jelenleg nem használt foglalások jelentést, ahol megtekintheti a fel nem használt foglalások módosíthatja.

### <a name="cost-by-service"></a>Díj szolgáltatás szerint
A widget amortizált költségek kiemeli a szolgáltatás az elmúlt 30 napra vonatkozóan. A tortadiagram a költségeket a szolgáltatás megtekintéséhez mutasson.
- Kattintson a widget nyissa meg a tényleges költségek elemzése jelentést.

### <a name="potential-savings"></a>Lehetséges megtakarítás
A widget díjszabása az Amazon EC2-é és az Amazon RDS javaslatok példány típusát jeleníti meg.
- Kattintson a widget nyissa meg a megtakarítások elemző jelentés. Felsorolja a példányok típusai lehetséges megtakarítás a költségeket.

### <a name="compute-instances---daily-trend"></a>Számítási példányok – napi Trend
A widget jeleníti meg az aktív példány típusát, az elmúlt 30 napra vonatkozóan.
- Kattintson a widget nyissa meg a példányok időalapú jelentést, ahol megtekintheti az elmúlt 30 napban példányai egy bontása.

### <a name="storage-by-department"></a>Tárterület részlegenként
A widget részlegek által használt tárolási szolgáltatások jeleníti meg. A tortadiagram tekintse meg a tárhelyhasználat részleg fölé.
- Kattintson a widget az S3-követő irányítópult megnyitásához.

## <a name="cost-controller-dashboard"></a>Vezérlő irányítópultja költség
A költségek vezérlő irányítópult jeleníti meg előre beállított költségek felosztási emeli ki.  
![Vezérlő irányítópultja költség](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Költségek időbeli alakulása
A widget segít a költségek trendek felismerése. Kiemeli a költségek az utolsó napjára, a trend az elmúlt 30 nap alapján.
- Kattintson a widget nyissa meg a tényleges időalapú költségeket tartalmazó jelentést megtekintéséhez és szűrheti a további részleteket.

### <a name="monthly-cost-trends"></a>Havi költség trendek
A widget kiemeli a tervezett amortizált összegű költségkeretet, és a tényleges csak a hónap kezdete óta.
- Kattintson a widget megnyitása az aktuális havi előre jelzett költségről szóló jelentés, amelyet összefoglaló hónap elejétől számított költséget biztosít.

Ez a jelentés tartalmazza a költség, hónap, a költség az előző hónap és az aktuális havi előre jelzett költség kezdetétől fogva. Az aktuális havi előre jelzett költséget a naprakész havi költség és leképezés hozzáadása számolható ki. A leképezés a költségek figyelemmel kísérni az elmúlt 30 napban alapul.

### <a name="12-month-planner"></a>12 hónapos Planner
A widget a következő 12 hónapban, és a lehetséges megtakarítás kiemeli a a tervezett költségeket.
- Kattintson a widget az éves előre jelzett költségről szóló jelentés megnyitásához.

### <a name="cost-by-service"></a>Díj szolgáltatás szerint
A widget amortizált költségek kiemeli a szolgáltatás az elmúlt 30 napra vonatkozóan.
- A tortadiagram a költségeket a szolgáltatás megtekintéséhez mutasson.
- Kattintson a widget nyissa meg a tényleges költségek elemzése jelentést.

### <a name="cost-by-account"></a>Költség-fiók használatával
A widget emeli ki, az elmúlt 30 napban az amortizált költségek fiók használatával.
- A tortadiagram a költségek fiókonként megtekintéséhez mutasson.
- Kattintson a widget nyissa meg a tényleges költségek elemzése jelentést.

### <a name="cost-trend-by-day"></a>Költségtrend naponta
A widget az elmúlt 30 napban költségek emeli ki.
- Az oszlopdiagram megtekintéséhez költség / nap fölé.
- Kattintson a widget nyissa meg a tényleges időalapú költségeket tartalmazó jelentést.

### <a name="cost-trend-by-month---last-6-months"></a>Költségtrend hónap – elmúlt 6 hónap szerint

A widget az elmúlt hat hónapban költségek emeli ki.
- A kurzort az oszlopdiagramot, tekintse meg a havi költségeket.
- Kattintson a widget nyissa meg a tényleges időalapú költségeket tartalmazó jelentést.

## <a name="asset-controller-dashboard"></a>Adategység-vezérlő irányítópultja

Ez az irányítópult futó példányok, elérhető és a használatban lévő lemezek, a példányok típusai és a tároló terjesztési számát jeleníti meg.  
![Adategység-vezérlő irányítópultja](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Számítási példányok
A widget az elmúlt 30 napban a használati trendek alapján futó számát jeleníti meg.
- Kattintson a widget nyissa meg a példányok időalapú jelentést.

### <a name="disks"></a>Lemezek
A widget emeli ki, a teljes számát és a lemezek használatban és elérhető kötet.
- Kattintson a widget az aktív lemezek jelentés megnyitásához.

### <a name="instance-type-distribution"></a>Példány információtípus terjesztése
A widget a tortadiagram a példányok típusai emeli ki.
- Kattintson a widgeten nyissa meg a példány terjesztési jelentést, amely az aktív üzemelő példányok bontásban a kiválasztott összesítési.

### <a name="compute-instances---daily-trend"></a>Számítási példányok – napi Trend
A widget kiemeli a számítási példányok (helyszíni, fenntartott, és igény szerinti) naponta az elmúlt 30 napra vonatkozóan.
- A kurzort a diagram a napi típus szerint a számítási példányok számának megtekintéséhez.
- Kattintson a widget nyissa meg a példányok időalapú jelentést.

### <a name="all-buckets-s3"></a>Az összes gyűjtők (S3 esetén)
A widget emeli ki, az S3 szintű tárterület teljes és a tárolt objektumok száma.
- Kattintson a widget az S3-követő irányítópult megnyitásához. Az Irányítópult segítségével keresse meg, elemzése és megjelenítése az aktuális storage használati és a trendeket.

### <a name="sql-db-instances-rds"></a>SQL DB-példányok (RDS)
A widget kiemeli a futó a trend az elmúlt 30 nap alapján Amazon RDS-példányok számát.
- Kattintson a widget nyissa meg a távoli asztali szolgáltatások példány időalapú jelentést.

## <a name="optimizer-dashboard"></a>Optimalizáló irányítópult
Ez az irányítópult downsizing javaslatokat, a fel nem használt erőforrások és a lehetséges megtakarítás jeleníti meg.  
![Optimalizáló irányítópult](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>A fenntartott példányok Kalkulátor
A widget fenntartott példány vásárlására vonatkozó javaslat számát jeleníti meg, és kiemeli az éves szinten lehetséges megtakarításokat.
- Kattintson a widget, nyissa meg a fenntartott példány Számológép, ahol megadhatja, hogy mikor érdemes használni az igény szerinti és fenntartott díjszabási csomagokat.

### <a name="sizing"></a>Méretezés
A widget az ajánlott méretezése és a lehetséges megtakarítás emeli ki, ha implementálva.
- Kattintson a widget az EC2-é költséghatékony méretezési javaslatokat tartalmazó jelentés megnyitásához.

### <a name="unused-ri-detector"></a>A fel nem használt fenntartott példányok detector használatával
A widget kiemeli az Amazon EC2-é száma nem használt foglalások.
- Kattintson a widget nyissa meg a jelenleg nem használt foglalások jelentést, ahol megtekintheti a nem használt foglalások, amelyek módosíthatók.

###  <a name="available-disks"></a>Rendelkezésre álló lemezek
A widget rávilágít arra, hogy a telepítés nem csatlakoztatott lemezek számát.
- Kattintson a widget nyissa meg a leválasztott lemezeket jelentést.

### <a name="rds-ri-calculator"></a>A távoli asztali szolgáltatások a fenntartott példányok Kalkulátor
A widget kiemeli az Amazon RDS-példány és a lehetséges megtakarítás foglalás javaslatok száma.
- Kattintson a widget nyissa meg a távoli asztali szolgáltatások fenntartott példány vásárlására vonatkozó javaslatok jelentést, ahol megtekintheti a Cloudyn javaslatok a fenntartott példányok használata helyett az igény szerinti példányok.

### <a name="rds-sizing"></a>A távoli asztali szolgáltatások méretezése
A widget méretezési javaslatok és a lehetséges megtakarítás számát jeleníti meg.
- Kattintson a widget nyissa meg a távoli asztali szolgáltatások méretezési javaslatok jelentést, részletes méretezési javaslatok Amazon RDS melyik jeleníti meg.

Az optimalizálási javaslatokat vannak adatok alapján történő használatának és teljesítményének figyelemmel kísérni az elmúlt hónapban.

## <a name="s3-tracker-dashboard"></a>S3-követő irányítópult
Az S3-követő Irányítópult segítségével keresse meg, elemzése és megjelenítése az aktuális storage használati és a trendek.  
![S3-követő irányítópult](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>Az összes gyűjtők
A widget rávilágít arra, hogy minden a gyűjtők GB-ban, és a gyűjtők lévő objektumok teljes száma a teljes méretét.
- Kattintson a widget az S3 mérete terjesztési jelentés megnyitásához. A jelentés segítségével elemezheti az S3 méretet gyűjtőbe, a legfelső szintű mappát, a tárolási osztály és a verziószámozási állapotát.

### <a name="bucket-properties"></a>Gyűjtő tulajdonságai
A widget kiemeli a storage gyűjtők száma összesen.
- A widget az S3 gyűjtő tulajdonságairól szóló jelentés megtekintése gombra.

### <a name="scan-status"></a>Vizsgálat állapota
A widget emeli ki, amikor az utolsó S3 vizsgálat megtörtént, és elindítja a következő.
- Kattintson a widget az S3 vizsgálat állapota jelentés megnyitásához.

### <a name="storage-by-bucket"></a>Storage-gyűjtő által
A widget egyes gyűjtőbe tárolási osztály által használt százalékos emeli ki.
- Kattintson a widget az S3 mérete terjesztési jelentés megnyitásához. A jelentés segítségével elemezheti az S3 méretet gyűjtőbe, a legfelső szintű mappát, a tárolási osztály és a verziószámozási állapotát.

### <a name="number-of-objects-by-bucket"></a>A gyűjtő objektumok száma
A widget kiemeli a gyűjtőhöz a tényleges száma és százalékos aránya szerint objektumok száma. A kurzort a gyűjtőhöz, a teljes objektumok megtekintéséhez.
- Kattintson a widget nyissa meg a S3 mérete terjesztési jelentést (vizsgálat alapján).

## <a name="cloud-comparison-dashboard"></a>A cloud összehasonlító irányítópultja
A felhő összehasonlító Irányítópult segítségével összehasonlíthatja a költségek a különböző felhőszolgáltatók, díjszabás, a Processzor típusa és a RAM mérete alapján.  
![A cloud összehasonlító irányítópultja](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>EC2 Az Azure-ban, a példány típusának költség
A widget kiemeli az elmúlt 30 nap használati az igény szerinti díjszabás. Összehasonlítja a költségek és a jelenlegi Amazon EC2-é cost vs az esetleges költségeket az Azure-ban.
- Költség / példánytípus összehasonlítani a sávok fölé.
- Kattintson a widget az portolása az üzembe helyezés – költségek elemzése – jelentés megnyitásához.

### <a name="ec2-cost-in-azure"></a>EC2 Az Azure-ban költség
A widget jeleníti meg az aktuális Amazon EC2-é költségekkel, és összehasonlítja azokat az Azure-bA. Az összehasonlítás az elmúlt 30 nap használati az igény szerinti díjszabás alapján történik.
- Kattintson a widget az portolása az üzembe helyezés – költségek elemzése – jelentés megnyitásához.

### <a name="ec2azure-instance-type-mapping"></a>Adattípus-leképezés EC2-vagy az Azure-példány
A widget rávilágít arra, hogy a legjobb leképezést, rugalmas számítási egységek Amazon EC2-é és az Azure között.
- Kattintson a widget nyissa meg a példányok adattípus-hozzárendelése jelentést.

## <a name="next-steps"></a>További lépések
- Olvassa el a [használja a Cloudyn-jelentések](use-reports.md) további jelentésekkel kapcsolatos cikket.
