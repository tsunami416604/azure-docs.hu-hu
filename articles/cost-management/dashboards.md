---
title: Alapvető metrikákat megtekintéséhez az Azure költség felügyeleti irányítópultokon |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan Azure költség felügyeleti tekintse meg az irányítópultok metrikáit.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: b1dc2e2eca900ca0ae72329c3c373b2d24f1b2e0
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303706"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>Nézet kulcs irányítópultok metrikák költsége

Irányítópultok Cloudyn a jelentések magas szintű áttekintést adjon meg. Irányítópultok engedélyezi, hogy a kulcs költség metrikák tekintse meg egyetlen nézetben. Ezenkívül tartalmaznak az üzleti trend kiemeli a fontos üzleti döntéseket nyújt segítséget.

Irányítópultok nézeteket hozhat létre különböző feladatokat, többek között lehet, hogy a szervezet számára is használhatók:

- Pénzügyi vezérlő
- Alkalmazás vagy a projekt tulajdonosa
- DevOps visszafejtés
- Azok a vezetők

Irányítópultok widgeteket épülnek fel, és minden widget lényegében egy jelentés miniatűr. Kattintson a jelentés megnyitásához widgetet. Jelentések testreszabásakor mentheti azokat a jelentések és azok van-e adva az irányítópulton.

Irányítópult-verziók kezelése (MSP), a vállalati és a prémium szintű Cloudyn felhasználók térnek el egymástól. A különbségek entitás hozzáférési szintek határozzák meg. Hozzáférési szintekkel kapcsolatos további információkért lásd: [entitás hozzáférési szintek](tutorial-user-access.md#entity-access-levels).

Irányítópult rendelkezésre állási függ a felhőbeli szolgáltató szolgáltatásfiók irányítópultok megjelenítésekor használt. Rendelkezésre álló és Cloudyn által összegyűjtött információ típusát irányítópultok jelentései hatással van. Például ha egy AWS fiók nem rendelkezik majd nem látja a S3 követő irányítópult. Hasonlóképpen ha nem engedélyezi az Azure Resource Manager hozzáférést Cloudyn majd nem látja optimalizáló widgetjeihez bármely Azure-specifikus adatait.

A kész irányítópultok bármelyikét használhatja, vagy a saját Irányítópult testre szabott jelentéseket hozhat létre. Ha nem ismeri a Cloudyn jelentések, lásd: [használata költség felügyeleti jelentések](use-reports.md).

## <a name="create-a-custom-dashboard"></a>Egyéni irányítópult létrehozása

Ha gyorsan az egyéni irányítópult, másolatot készít egy meglévő tulajdonságát használatára. Ezután úgy, hogy a saját igényeinek megfelelően módosíthatja. A másolni kívánt irányítópultján kattintson **Mentés másként**. Csak másolatot készít személyre szabott irányítópultokat – az irányítópultok Cloudyn mellékelt nem lehet ugyanaz.

Egyéni irányítópult létrehozása:

1. A kezdőlapon, kattintson a **új hozzáadása +**. Saját irányítópult-oldalon jelenik meg.  
    ![Saját irányítópult](./media/dashboards/my-dashboard.png)
2. Kattintson a **adja hozzá az új jelentés**. A jelentés hozzáadása a megjelenő.
3. Jelölje ki az irányítópult-widgethez hozzáadni kívánt jelentést. A widget hozzáadódik az irányítópulton.
4. Ismételje meg a fenti lépéseket, amíg be nem fejeződik az irányítópulton.
5. Az irányítópult nevének módosításához kattintson a nevére az irányítópult az irányítópult kezdőlapján, és írja be az új nevet.

## <a name="modify-a-custom-dashboard"></a>Egy egyéni irányítópult

Létrehoz egy egyéni irányítópultot, például az irányítópultok Cloudyn mellékelt nem módosíthatja. Egy egyéni irányítópult jelentés módosítása:

1. Az irányítópulton található a jelentés módosítása, és kattintson a kívánt **szerkesztése**. Megjeleníteni a jelentést.
2. A beállítások módosításait, amely a jelentést szeretne készíteni, és kattintson a **mentése**. A jelentés frissül, és megjeleníti a módosítását.

## <a name="share-a-custom-dashboard"></a>Egy egyéni irányítópult megosztása

Egy egyéni irányítópultot megoszthatja másokkal történő _nyilvános_ vagy _saját entitás_. Nyilvános megosztásakor minden felhasználók megtekinthetik az irányítópulton. Csak az aktuális entitás hozzáféréssel rendelkező felhasználók megtekinthetik az irányítópulton, saját entitásra megosztásakor. Az egyéni irányítópult megosztása a nyilvános és a saját entitás lépések hasonlóak.

Nyilvános egyéni irányítópult megosztása:

1. Kattintson az irányítópulton, **irányítópultjának beállításai**. Az irányítópult beállítások mezőben jelenik meg.  
    ![Irányítópult-beállítások](./media/dashboards/dashboard-options.png)
2. A irányítópult beállítások párbeszédpanelen kattintson a nyíl szimbólum majd **nyilvános**. A nyilvános irányítópult megerősítést kérő párbeszédpanel jelenik meg.
3. Kattintson a **Igen**. Az irányítópult már mások számára elérhető.

## <a name="delete-a-custom-dashboard-report"></a>Egy egyéni irányítópult jelentés törlése

Egy egyéni jelentés összetevő törölheti az irányítópultról. A jelentés az irányítópulton nem törlése a jelentés a jelentések listából. Ehelyett a jelentés törlésével eltávolítja a csak az irányítópulton.

Kattintson az irányítópult az összetevők, az irányítópult-összetevőjének törlése **törlése**. Kattintson a **törlése** azonnal törli az irányítópult-összetevő.

## <a name="share-a-dashboard-enterprise"></a>A (vállalati) irányítópult megosztása

Az összes felhasználóra szabott irányítópultokat a szervezetben, vagy az aktuális entitás felhasználókkal is megoszthatja. Irányítópult megosztása biztosíthat mások egy gyors áttekintést nyújt arról a KPI-t. Irányítópult megosztása esetén automatikusan replikált az irányítópult minden Cloudyn entitások/ügyfeleinek. A megosztott irányítópult módosításai automatikusan frissülnek.

Irányítópult megosztása minden felhasználó alentitások is:

1. Az irányítópult kezdőlapján kattintson **szerkesztése**.
2. Kattintson a **megosztás** majd **nyilvános**.
3. A globális nyilvános irányítópult megerősítése mezőben jelenik meg.
4. Kattintson a **Igen** az irányítópult globális nyilvános irányítópultként beállítani.

Irányítópult megosztása aktuális entitás összes felhasználója számára:

1. Az irányítópult kezdőlapján kattintson **szerkesztése**.
2. Kattintson a **megosztás** majd **saját entitás**.
3. Kattintson a **Igen** az irányítópult nyilvános irányítópultként beállítani.

## <a name="duplicate-a-custom-dashboard"></a>Duplikált egyéni irányítópult

Amikor létrehoz egy új irányítópult, előfordulhat, hogy használni kívánt egy meglévő irányítópultot hasonló tulajdonságait. Másolhatja az irányítópultot hozzon létre egy újat.

Egyéni irányítópultok is csak másolatot készít. Standard irányítópultok nem lehet ugyanaz.

A duplikált (Klónozás) egy egyéni irányítópult:

1. Az ismétlődő kívánt irányítópultján kattintson **Mentés másként**. Új irányítópult nyílik meg ugyanazt a nevet és egy számot.
2. A duplikált irányítópult átnevezése, és módosítsa azt kedve szerint elrendezheti.

– Vagy –

1. Az irányítópult-beállítások kattintson **Mentés másként** ismétlődő kívánt irányítópult a sorban.
2. A duplikált irányítópult megnyitása.
3. Az irányítópult átnevezése, és módosítsa azt kedve szerint elrendezheti.

## <a name="set-a-default-dashboard"></a>Állítsa be az alapértelmezett irányítópult

Az alapértelmezett beállíthat bármelyik irányítópult. Az alapértelmezett beállítás lehetővé teszi a bal szélső lapon, az irányítópult lapon listában jelennek meg. Az alapértelmezett irányítópulton jeleníti meg, ha a Cloudyn portál megnyitásához.

- Kattintson az alapértelmezett, majd kattintson a kívánt irányítópult fület **alapértelmezett** a jobb oldalon.

– Vagy –

1. Kattintson a **irányítópultjának beállításai** elérhető irányítópultok listáját, és válassza ki a kívánt az alapértelmezett irányítópultot.  
    ![Irányítópult-beállítások](./media/dashboards/dashboard-options.png)
2. Kattintson a **alapértelmezett** sorában az irányítópulton. Az alapértelmezett irányítópult megerősítése mezőben jelenik meg.
3. Kattintson a **Yes** (Igen) gombra. Az irányítópult az alapértelmezett érték.

## <a name="management-dashboard"></a>Kezelési irányítópult
A Management (vagy MSP irányítópult MSP felhasználók) irányítópult magában foglalja a főjelentés típusú emeli ki.  
![Kezelési irányítópult](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>(Csak Enterprise) költség entitás összegzése
Ez a widget foglalja össze a felügyelt költség entitások, beleértve az ügyfelek száma és entitások száma.
- Kattintson a widget a vállalati részletei jelentés megnyitásához.

### <a name="cost-over-time"></a>Idővel költsége
Ez a widget segítségével költség tendenciákat. Kiemeli a költségeket a legutóbbi napon, az elmúlt 30 napban trendjét alapján.
- Kattintson a tényleges költség keresztül idő a jelentés megtekintéséhez nyissa meg a widget és további részleteket szűréséhez.

### <a name="asset-controller"></a>Eszköz-vezérlő
Ezt a widgetet az előző nap, a használati trendje az elmúlt 30 napban felett futó példányait számát mutatja be.
- Kattintson a widgetet az eszköz vezérlő irányítópult megnyitásához.

### <a name="unused-ri-detector"></a>Nem használt RI érzékelő
Ez a widget kiemeli a Amazon EC2 száma nem használt foglalásokat.
- Kattintson a nyissa meg a jelenleg nem használt foglalások jelentést, ahol megtekintheti a nem használt widget foglalások módosíthatja.

### <a name="cost-by-service"></a>Szolgáltatás költsége
Ez a widget amortized költségek kiemeli a szolgáltatás az elmúlt 30 napban. A tortadiagram a költségek szolgáltatásonként megtekintéséhez mutasson.
- Kattintson a widget a tényleges költség elemző jelentés megnyitásához.

### <a name="potential-savings"></a>Lehetséges megtakarítások
Ezt a widgetet jeleníti meg az árképzési Amazon EC2 és Amazon RDS javaslatok sablonpéldány típusúvá
- Kattintson a widget nyissa meg a megtakarítások elemző jelentés. Felsorolja az esetleges megtakarítás példány típusok költségeit.

### <a name="compute-instances---daily-trend"></a>-Példányok számítási napokra bontott tendenciáit
Ez a widget alapján jeleníti meg az aktív példány típusa, az elmúlt 30 napban.
- Kattintson a widgetet való nyissa meg a példányok keresztül idő jelentést, ahol megtekintheti az elmúlt 30 napban futó összes példányát részletes információkat.

### <a name="storage-by-department"></a>Tárolási részleg
Ez a widget osztályok által használt tárolószolgáltatások jeleníti meg. A kördiagram részleg tárhelyhasználati megtekintéséhez mutasson.
- Kattintson a widgetet az S3 követő irányítópult megnyitásához.

## <a name="cost-controller-dashboard"></a>A tartományvezérlő irányítópult költsége
A vezérlő költség irányítópult jeleníti meg előre beállított költség foglalási emeli ki.  
![A tartományvezérlő irányítópult költsége](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Idővel költsége
Ez a widget költség tendenciákat nyújt segítséget. Kiemeli a költségeket a legutóbbi napon, az elmúlt 30 napban trendjét alapján.
- Kattintson a tényleges költség keresztül idő a jelentés megtekintéséhez nyissa meg a widget és további részleteket szűréséhez.

### <a name="monthly-cost-trends"></a>Havi költség trendek
Ezt a widgetet kiemeli a tervezett amortized kiadásokat, és a tényleges töltött hónap kezdete óta.
- Kattintson a widgetet megnyitása az aktuális hónap tervezett költség jelentést, amely garantálja a hónap dátumig összefoglaló.

Ez a jelentés tartalmazza a költség, a hónap, az előző hónap költségét és az aktuális hónap tervezett költség kezdetén. Az aktuális hónap tervezett a számítás a naprakész havi költségét és a leképezés hozzáadásával. A leképezés a költség, az elmúlt 30 napban figyelt alapul.

### <a name="12-month-planner"></a>12 hónapos Planner
Ez a widget költségeit a következő 12 hónap és az esetleges megtakarítás keresztül mutatja be.
- Kattintson a widgetet az éves tervezett költség jelentés megnyitásához.

### <a name="cost-by-service"></a>Szolgáltatás költsége
Ez a widget amortized költségek kiemeli a szolgáltatás az elmúlt 30 napban.
- A tortadiagram a költségek szolgáltatásonként megtekintéséhez mutasson.
- Kattintson a widget a tényleges költség elemző jelentés megnyitásához.

### <a name="cost-by-account"></a>Fiók költsége
Ez a widget mutatja be, az elmúlt 30 napban amortized költségek fiókkal.
- A tortadiagram a költségek fiókonként megtekintéséhez mutasson.
- Kattintson a widget a tényleges költség elemző jelentés megnyitásához.

### <a name="cost-trend-by-day"></a>Napi költség Trend
A widget az elmúlt 30 napban töltött emeli ki.
- Az oszlopdiagramot napi költségek megtekintéséhez mutasson.
- Kattintson a widget a tényleges költség keresztül idő jelentés megnyitásához.

### <a name="cost-trend-by-month---last-6-months"></a>Havonta - elmúlt 6 hónapban költség Trend

A widget keresztül az elmúlt hat hónapban töltött emeli ki.
- Az oszlopdiagramot a havi költségeket megtekintéséhez mutasson.
- Kattintson a widget a tényleges költség keresztül idő jelentés megnyitásához.

## <a name="asset-controller-dashboard"></a>Eszköz vezérlő irányítópult

Ezt az irányítópultot a futó példányok, elérhető és a használatban lévő lemezek, terjesztési példány típusok és tárolással kapcsolatos számát jeleníti meg.  
![Eszköz vezérlő irányítópult](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Számítási példányok
Ez a widget futó példányait a használati trendje alapján az elmúlt 30 napban számát jeleníti meg.
- Kattintson a widget a példányok keresztül idő jelentés megnyitásához.

### <a name="disks"></a>Lemezek
Ez a widget mutatja be, a teljes számát és a lemezek használatban és elérhető kötet.
- Kattintson a widgetet az aktív lemezek jelentés megnyitásához.

### <a name="instance-type-distribution"></a>Példány típus terjesztési
Ez a widget a példány típusok tortadiagram mutatja be.
- Kattintson a widget a példány terjesztési jelentés, amely az aktív példányok részletes információkat biztosít a kijelölt összesítési való megnyitásához.

### <a name="compute-instances---daily-trend"></a>-Példányok számítási napokra bontott tendenciáit
Ez a widget kiemeli a számítási példányokért (direkt, fenntartott, és igény szerinti) naponta az elmúlt 30 napban.
- Mutasson a diagramot úgy, hogy számítási példányokért naponta típusonkénti számát.
- Kattintson a widget a példányok keresztül idő jelentés megnyitásához.

### <a name="all-buckets-s3"></a>Minden gyűjtők (S3)
Ez a widget mutatja be, a teljes S3 tárolás és a tárolt objektumok száma.
- Kattintson a widgetet az S3 követő irányítópult megnyitásához. Az Irányítópult segítségével megkeresheti, elemzése, és megjeleníti az aktuális tárhely-használat és a trendeket.

### <a name="sql-db-instances-rds"></a>SQL Server adatbázis-példányok (RDS)
Ez a widget kiemeli a futó alakulása az elmúlt 30 napban a Amazon távoli asztali szolgáltatások példányainak száma.
- Kattintson a widget a távoli asztali szolgáltatások példány keresztül idő jelentés megnyitásához.

## <a name="optimizer-dashboard"></a>Optimalizáló irányítópult
Ez az irányítópult downsizing javaslatokat, a nem használt erőforrások és a potenciális megtakarítások jeleníti meg.  
![Optimalizáló irányítópult](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>RI Számológép
Ez a widget RI vásárlásának javaslatok számát jeleníti meg, és kiemeli a potenciális éves megtakarítások.
- Kattintson a fenntartott, ahol meghatározhatja, mikor érdemes használni, és igény példány Számológép megnyitásához widget díjszabások fenntartva.

### <a name="sizing"></a>Méretezés
Ezt a widgetet ajánlott méretezési és esetleges megtakarítás emeli ki, ha megvalósítva.
- Kattintson a widget a EC2 költség hatályos méretezési javaslatok jelentés megnyitásához.

### <a name="unused-ri-detector"></a>Nem használt RI érzékelő
Ez a widget kiemeli a Amazon EC2 száma nem használt foglalásokat.
- Kattintson a widget a jelenleg nem használt foglalások jelentés, ahol megtekintheti a nem használt lefoglalását, amelyek még módosíthatók való megnyitásához.

###  <a name="available-disks"></a>Rendelkezésre álló lemezek
Ez a widget a környezetben nem csatlakoztatott lemezek számát mutatja be.
- Kattintson a nem csatlakoztatott lemezek jelentés megnyitása a widgetet.

### <a name="rds-ri-calculator"></a>Távoli asztali szolgáltatások RI Számológép
Ez a widget az Amazon RDS-példányok és az esetleges megtakarítás foglalás javaslatok számát mutatja be.
- Kattintson a widgetet megnyitása a távoli asztali szolgáltatások RI vásárlás javaslatok jelentés, ahol fenntartott példányok használata helyett igény példányok Cloudyn javaslatok láthatók.

### <a name="rds-sizing"></a>Távoli asztali szolgáltatások méretezése
Ez a widget méretezési javaslatok és az esetleges megtakarítás számát mutatja.
- Kattintson a widget a távoli asztali szolgáltatások méretezése javaslatok jelentés, mely ajánlások méretezése Amazon távoli asztali szolgáltatások részletes való megnyitásához.

Az optimalizálás javaslatokat a használati és teljesítményadatokat adatokat az utolsó hónapban alapulnak.

## <a name="s3-tracker-dashboard"></a>S3 követő irányítópult
A S3 követő Irányítópult segítségével megkeresheti, elemzése és az aktuális tárhely-használat és a trendeket.  
![S3 követő irányítópult](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>Minden gyűjtők
Ezt a widgetet emel ki, hogy minden a gyűjtők, GB, és a gyűjtők objektumok teljes száma a teljes mérete.
- Kattintson a widget a S3 mérete terjesztési jelentés megnyitásához. A jelentés segítségével elemezheti a S3 gyűjtő, a legfelső szintű mappát, a tárolási osztály és a verziószámozási állapotát méretet.

### <a name="bucket-properties"></a>Gyűjtő tulajdonságai
Ez a widget kiemeli a tárolási gyűjtők száma.
- Kattintson a widget a S3 gyűjtő tulajdonságok jelentést szeretne megtekinteni.

### <a name="scan-status"></a>Ellenőrzési állapota
Ez a widget mutatja be, a legutóbbi S3 vizsgálat befejezése és a következő indításakor fogja.
- Kattintson a widget a S3 vizsgálata állapotáról készült jelentés megnyitásához.

### <a name="storage-by-bucket"></a>Tárolási gyűjtő által
Ez a widget minden gyűjtő tárolási osztály által használt százalékos mutatja be.
- Kattintson a widget a S3 mérete terjesztési jelentés megnyitásához. A jelentés segítségével elemezheti a S3 gyűjtő, a legfelső szintű mappát, a tárolási osztály és a verziószámozási állapotát méretet.

### <a name="number-of-objects-by-bucket"></a>Gyűjtő által objektumok száma
Ez a widget / tényleges számát és százalékát gyűjtő objektumok számát mutatja be. A gyűjtő a teljes objektumok megtekintéséhez mutasson.
- Kattintson a widgetet S3 mérete terjesztési jelentés (vizsgálat alapján) történő megnyitásához.

## <a name="cloud-comparison-dashboard"></a>Felhő összehasonlítás irányítópult
A felhő összehasonlítás Irányítópult segítségével költségek a díjszabás, a Processzor típusa és a RAM mérete alapján különböző szolgáltatók összehasonlíthatja.  
![Felhő összehasonlítás irányítópult](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>EC2 Az Azure példánytípus költség
Ez a widget az elmúlt 30 napban az igény szerinti díjszabás használati mutatja be. Az comares a költséget az aktuális Amazon EC2 költségeket, és a költségeket, az Azure-ban a lehetséges.
- Az összehasonlítandó költségek egy sablonpéldány típusúvá sávok mutasson.
- Kattintson a widgetet az eljárás a központi telepítés – költség elemző jelentés megnyitásához.

### <a name="ec2-cost-in-azure"></a>EC2 Az Azure-ban költség
Ez a widget jeleníti meg az aktuális Amazon EC2 költségeit, és összehasonlítja azokat az Azure-bA. Az összehasonlítás az elmúlt 30 napban az igény szerinti díjszabás használati alapul.
- Kattintson a widgetet az eljárás a központi telepítés – költség elemző jelentés megnyitásához.

### <a name="ec2azure-instance-type-mapping"></a>EC2 vagy az Azure-példány hozzárendelése
Ez a widget rugalmas számítási egység Amazon EC2 és az Azure közötti legjobb leképezése mutatja be.
- Kattintson a widget a példányok adattípus-hozzárendelése jelentés megnyitásához.
