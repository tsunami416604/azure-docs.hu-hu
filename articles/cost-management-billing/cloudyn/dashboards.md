---
title: Cloudyn-irányítópultokkal rendelkező fő mérőszámok megtekintése az Azure-ban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan tekintheti meg az irányítópultokkal rendelkező fő mérőszámokat a Cloudyn-ben.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: c0651dd006585e4ed5b8b888d6e55974c5c45a1d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75990683"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>A legfontosabb díjszabási mérőszámok megtekintése az irányítópultokkal

A Cloudyn lévő irányítópultok magas szintű áttekintést nyújtanak a jelentésekről. Az irányítópultok segítségével egyetlen nézetben tekintheti meg a legfontosabb díjszabási metrikákat. Emellett üzleti trendeket is biztosítanak, amelyek segítenek a fontos üzleti döntések meghozatalában.

Az irányítópultok a szervezetében a különböző felelősségi körökhöz tartozó felhasználók nézeteinek létrehozására is használhatók, amelyek például a következők lehetnek:

- Pénzügyi vezérlő
- Alkalmazás vagy projekt tulajdonosa
- DevOps mérnök
- Vezetők

Az irányítópultok widgetek alkotják, és az egyes widgetek lényegében egy jelentés miniatűrje. Kattintson egy widgetre a jelentés megnyitásához. Amikor testreszabja a jelentéseket, azokat Saját jelentésekba menti, és hozzáadja őket az irányítópulthoz.

Az irányítópult-verziók különböznek a felügyeleti (MSP), a vállalati és a prémium szintű Cloudyn-felhasználóktól. A különbségeket az entitások hozzáférési szintjei határozzák meg. A hozzáférési szintekkel kapcsolatos további információkért lásd: [entitások hozzáférési szintjei](tutorial-user-access.md#entity-access-levels).

Az irányítópultok rendelkezésre állása az irányítópultok megtekintésekor használt felhőalapú szolgáltatói fiók típusától függ. A rendelkezésre álló és a Cloudyn által gyűjtött információk típusa hatással van az irányítópultokon lévő jelentésekre. Ha például nem rendelkezik AWS-fiókkal, akkor az S3 Tracker-irányítópult nem jelenik meg. Hasonlóképpen, ha nem engedélyezi Azure Resource Manager hozzáférését a Cloudyn, akkor az Azure-specifikus információk nem jelennek meg az optimalizáló irányítópult widgetekben.

Az előkészített irányítópultok bármelyikét használhatja, vagy létrehozhat egy testreszabott jelentéseket tartalmazó irányítópultot is. Ha nem ismeri a Cloudyn-jelentéseket, tekintse meg a [Cloudyn-jelentések használata](use-reports.md)című témakört.

## <a name="create-a-custom-dashboard"></a>Egyéni irányítópult létrehozása

Az egyéni irányítópultok használatának gyors megkezdéséhez egy meglévőt is duplikálhat a tulajdonságainak használatához. Ezt követően módosíthatja az igényeinek megfelelően. A másolni kívánt irányítópulton kattintson a **Mentés másként**lehetőségre. Csak a testreszabott irányítópultok duplikálása lehetséges – a Cloudyn-ben található irányítópultokat nem lehet duplikálni.

Egyéni irányítópult létrehozása:

1. A kezdőlapon kattintson az **új + hozzáadása**lehetőségre. Megjelenik a saját irányítópult lap.  
    ![saját irányítópult-oldalt, ahol új jelentéseket adhat hozzá](./media/dashboards/my-dashboard.png)
2. Kattintson az **új jelentés hozzáadása**lehetőségre. Megjelenik a jelentés hozzáadása mező.
3. Válassza ki az irányítópult-widgethez hozzáadni kívánt jelentést. A widget hozzá van adva az irányítópulthoz.
4. Ismételje meg az előző lépéseket, amíg az irányítópult be nem fejeződik.
5. Az irányítópult nevének módosításához kattintson az irányítópult nevére az irányítópult kezdőlapján, és írja be az új nevet.

## <a name="modify-a-custom-dashboard"></a>Egyéni irányítópult módosítása

Az egyéni irányítópultok létrehozásához hasonlóan a Cloudyn-ben található irányítópultok nem módosíthatók. Egyéni irányítópult-jelentés módosítása:

1. Keresse meg a módosítani kívánt jelentést az irányítópulton, majd kattintson a **Szerkesztés**gombra. Megjelenik a jelentés.
2. Hajtsa végre a jelentés kívánt módosításait, és kattintson a **Mentés**gombra. A jelentés frissül, és megjeleníti a módosításokat.

## <a name="share-a-custom-dashboard"></a>Egyéni irányítópult megosztása

Megoszthat egy egyéni irányítópultot másokkal a _nyilvános_ vagy _az entitásom_használatával. Nyilvános megosztás esetén minden felhasználó megtekintheti az irányítópultot. Csak az aktuális entitáshoz hozzáféréssel rendelkező felhasználók tekinthetik meg az irányítópultot, amikor megosztják az entitást. Az egyéni irányítópultok nyilvános és saját entitással való megosztásának lépései hasonlóak.

Egyéni irányítópult nyilvánosként való megosztása:

1. Az irányítópulton kattintson az **irányítópult-beállítások**elemre. Megjelenik az irányítópult-beállítások mező.  
    ![irányítópult-beállítások egy egyéni irányítópulton](./media/dashboards/dashboard-options.png)
2. Az irányítópult beállításai mezőben kattintson a nyíl szimbólumra, majd a **nyilvános**elemre. Megjelenik a nyilvános irányítópult megerősítő párbeszédpanele.
3. Kattintson az **Igen**gombra. Az irányítópult mostantól mások számára is elérhető.

## <a name="delete-a-custom-dashboard-report"></a>Egyéni irányítópult-jelentés törlése

Az irányítópultról törölheti az egyéni jelentések összetevőjét. Ha törli a jelentést az irányítópultról, a jelentés nem törlődik a jelentések listából. Ehelyett a jelentés törlése csak az irányítópultról törlődik.

Irányítópult-összetevő törléséhez kattintson a **Törlés**elemre az irányítópult összetevőben. A **Törlés** gombra kattintva azonnal törlődik az irányítópult összetevő.

## <a name="share-a-dashboard-enterprise"></a>Irányítópult megosztása (vállalati)

Az egyéni irányítópultok megoszthatók a szervezet összes felhasználójára vagy az aktuális entitás felhasználóival. Az irányítópult megosztása mások számára is lehetővé teheti a KPI gyors áttekintését. Amikor megoszt egy irányítópultot, az automatikusan replikálja az irányítópultot az összes Cloudyn-entitásra/ügyfélre. A megosztott irányítópult módosításait a rendszer automatikusan frissíti.

Irányítópult megosztása az összes felhasználóval, beleértve az alentitásokat is:

1. Az irányítópult kezdőlapján kattintson a **Szerkesztés**elemre.
2. Kattintson a **megosztás** , majd a **nyilvános**elemre.
3. Megjelenik a globális nyilvános irányítópult megerősítése ablak.
4. Kattintson az **Igen** gombra az irányítópult globális nyilvános irányítópultként való beállításához.

Irányítópult megosztása egy aktuális entitás összes felhasználójának használatával:

1. Az irányítópult kezdőlapján kattintson a **Szerkesztés**elemre.
2. Kattintson a **megosztás** , majd **a saját entitás**elemre.
3. Kattintson az **Igen** gombra az irányítópult nyilvános irányítópultként való beállításához.

## <a name="duplicate-a-custom-dashboard"></a>Egyéni irányítópult duplikálása

Új irányítópult létrehozásakor előfordulhat, hogy hasonló tulajdonságokat szeretne használni egy meglévő irányítópulton. Az irányítópultot duplikálhatja egy új létrehozásához.

Csak az egyéni irányítópultok duplikálása végezhető el. Szabványos irányítópultok nem duplikálható.

Egyéni irányítópult duplikálása (klónozása):

1. Az átmásolni kívánt irányítópulton kattintson a **Mentés másként**lehetőségre. Megnyílik egy új irányítópult ugyanazzal a névvel és számmal.
2. Nevezze át a duplikált irányítópultot, és módosítsa úgy, ahogy szeretné.

– Vagy –

1. Az irányítópult beállításai területen kattintson a **Mentés másként** elemre az irányítópult azon sorában, amelyet duplikálni kíván.
2. Megnyílik az ismétlődő irányítópult.
3. Nevezze át az irányítópultot, és módosítsa úgy, ahogy szeretné.

## <a name="set-a-default-dashboard"></a>Alapértelmezett irányítópult beállítása

Az irányítópultot alapértelmezettként is megadhatja. Az alapértelmezett beállítás megadásával megjelenik az irányítópult lap lista bal szélső lapja. Az alapértelmezett irányítópult a Cloudyn-portál megnyitásakor jelenik meg.

- Kattintson az irányítópult fülre, amelyet alapértelmezettként szeretne beállítani, majd kattintson a jobb oldalon az **alapértelmezett** elemre.

– Vagy –

1. Kattintson az **irányítópult beállításai** elemre az elérhető irányítópultok listájának megtekintéséhez, majd válassza ki az alapértelmezettként beállítani kívánt irányítópultot.  
    irányítópult-beállítások ![alapértelmezett irányítópulthoz](./media/dashboards/dashboard-options.png)
2. Az irányítópult sorában kattintson az **alapértelmezett** elemre. Megjelenik az irányítópult alapértelmezett megerősítő mezője.
3. Kattintson az **Igen** gombra. Az irányítópult alapértelmezett értékre van állítva.

## <a name="management-dashboard"></a>Kezelési irányítópult
A felügyeleti (vagy az MSP-irányítópult az MSP-felhasználók számára) irányítópulton a fő jelentési típusok Kiemelt jellemzői szerepelnek.  
![Felügyeleti irányítópult, amely különböző jelentéseket mutat be](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Cost entitás összegzése (csak vállalati verzió)
Ez a widget összefoglalja a felügyelt Cost-entitásokat, beleértve az entitások számát és a fiókok számát.
- Kattintson a widgetre a vállalati adatok jelentés megnyitásához.

### <a name="cost-over-time"></a>Időalapú költségek
Ez a widget segítséget nyújt a költségmegtakarítások kialakulásához. Az elmúlt 30 nap trendje alapján kiemeli az elmúlt nap költségeit.
- A widgetre kattintva megnyithatja az idő múlásával kapcsolatos tényleges költségeket a további részletek megtekintéséhez és szűréséhez.

### <a name="asset-controller"></a>Adategység-vezérlő
Ez a widget kiemeli az előző naptól számított futó példányok számát, az elmúlt 30 napban a használati trend felett.
- Kattintson a widgetre az adatkezelő irányítópultjának megnyitásához.

### <a name="unused-ri-detector"></a>Nem használt fenntartott példányok érzékelője
Ez a widget kiemeli a nem használt Amazon EC2-foglalások számát.
- Kattintson a widgetre a jelenleg nem használt foglalások jelentés megnyitásához, ahol megtekintheti a módosítható nem használt foglalásokat.

### <a name="cost-by-service"></a>Szolgáltatás díja
Ez a widget az elmúlt 30 nap során kiemeli az elszámolási költségeket a szolgáltatás alapján. Vigye a kurzort a tortadiagram fölé a szolgáltatások költségeinek megtekintéséhez.
- Kattintson a widgetre a tényleges Cost Analysis-jelentés megnyitásához.

### <a name="potential-savings"></a>Lehetséges megtakarítások
Ez a widget az Amazon EC2-hoz és az Amazon RDS-hez tartozó példányok típusának díjszabását mutatja be.
- Kattintson a widgetre, és nyissa meg a megtakarítási elemzési jelentést. A költségek a lehetséges megtakarításokkal rendelkező példányok típusai szerint szerepelnek.

### <a name="compute-instances---daily-trend"></a>Számítási példányok – Napi trend
Ez a widget az aktív példányok típusát jeleníti meg az elmúlt 30 napban.
- Kattintson a widgetre a példányok időbeli jelentésének megnyitásához, ahol megtekintheti az elmúlt 30 napban futó összes példány részletezését.

### <a name="storage-by-department"></a>Tárolás részleg szerint
Ez a widget a részlegek által használt tárolási szolgáltatásokat jeleníti meg. Vigye a kurzort a tortadiagram fölé, és tekintse meg a tárhely felhasználását részleg alapján.
- Kattintson a widgetre az S3 Tracker irányítópultjának megnyitásához.

## <a name="cost-controller-dashboard"></a>A Cost Controller irányítópultja
A költséghely irányítópulton megjelenik az előre beállított költségmegosztás Kiemelt jellemzői.  
![A Cost Controller irányítópultja, amely különböző jelentéseket mutat be](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Időalapú költségek
Ez a widget segítséget nyújt a költségmegtakarítások kialakulásához. Az elmúlt 30 nap trendje alapján kiemeli az elmúlt nap költségeit.
- A widgetre kattintva megnyithatja az idő múlásával kapcsolatos tényleges költségeket a további részletek megtekintéséhez és szűréséhez.

### <a name="monthly-cost-trends"></a>Havi költség tendenciái
Ez a widget kiemeli az elszámolt amortizációs kiadásokat és a hónap elejétől számított tényleges költségeket.
- A widgetre kattintva megnyithatja az aktuális havi tervezett Cost-jelentést, amely egy, a hónaptól a napra vonatkozó összegzést biztosít.

Ez a jelentés a hónap elejétől számított költségeket, az előző hónap költségeit és az aktuális havi előrejelzett költségeket mutatja. Az aktuális havi tervezett költség kiszámítása a naprakész havi költség és a kivetítés hozzáadásával történik. A kivetítés az elmúlt 30 napban figyelt díjakon alapul.

### <a name="12-month-planner"></a>12 hónapos Planner
Ez a widget a következő 12 hónapra emeli ki a tervezett költségeket és a lehetséges megtakarításokat.
- Kattintson a widgetre az éves tervezett Cost-jelentés megnyitásához.

### <a name="cost-by-service"></a>Szolgáltatás díja
Ez a widget az elmúlt 30 nap során kiemeli az elszámolási költségeket a szolgáltatás alapján.
- Vigye a kurzort a tortadiagram fölé a szolgáltatások költségeinek megtekintéséhez.
- Kattintson a widgetre a tényleges Cost Analysis-jelentés megnyitásához.

### <a name="cost-by-account"></a>Cost by Account
Ez a widget az elmúlt 30 nap során kiemeli az elszámolt költségeket.
- Vigye a kurzort a tortadiagram fölé a fiókok költségeinek megtekintéséhez.
- Kattintson a widgetre a tényleges Cost Analysis-jelentés megnyitásához.

### <a name="cost-trend-by-day"></a>Napi költségmegtakarítás
Ez a widget kiemeli az elmúlt 30 nap során eltöltött időt.
- Vigye a kurzort a oszlopdiagram fölé a napi költségek megtekintéséhez.
- Kattintson a widgetre a tényleges költségadatok jelentésének megnyitásához.

### <a name="cost-trend-by-month---last-6-months"></a>Ár trendje hónap szerint – az elmúlt 6 hónap

Ez a widget az elmúlt hat hónapban kiemeli az elköltést.
- Vigye a kurzort a oszlopdiagram fölé, hogy a költségek havonta megjelenjenek.
- Kattintson a widgetre a tényleges költségadatok jelentésének megnyitásához.

## <a name="asset-controller-dashboard"></a>Eszköz-vezérlő irányítópult

Ez az irányítópult a futó példányok számát, a rendelkezésre álló és a használatban lévő lemezeket, a példányok típusának elosztását és a tárolási adatokat jeleníti meg.  
![Az Eszközkezelő irányítópultja, amely különböző jelentéseket jelenít meg](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Számítási példányok
Ez a widget a futó példányok számát jeleníti meg a használati trend alapján az elmúlt 30 napban.
- Kattintson a widgetre a példányok időbeli jelentésének megnyitásához.

### <a name="disks"></a>Lemezek
Ez a widget kiemeli a használatban lévő és elérhető lemezek teljes számát és mennyiségét.
- Kattintson a widgetre az aktív lemezek jelentés megnyitásához.

### <a name="instance-type-distribution"></a>Példány típusának eloszlása
Ez a widget kiemeli a tortadiagram példányainak típusát.
- Kattintson a widgetre a példányok terjesztési jelentésének megnyitásához, amely az aktív példányok részletezését biztosítja a kiválasztott összesítéssel.

### <a name="compute-instances---daily-trend"></a>Számítási példányok – Napi trend
Ez a widget az elmúlt 30 napban naponta kiemeli a számítási példányokat (helyszíni, fenntartott és igény szerinti).
- Vigye a kurzort a gráf fölé, és tekintse meg a számítási példányok számát naponta.
- Kattintson a widgetre a példányok időbeli jelentésének megnyitásához.

### <a name="all-buckets-s3"></a>Összes gyűjtő (S3)
Ez a widget kiemeli a teljes S3 tárterületet és a tárolt objektumok számát.
- Kattintson a widgetre az S3 Tracker irányítópultjának megnyitásához. Az irányítópult segítségével megkeresheti, elemezheti és megjelenítheti az aktuális tárterület-használatot és trendeket.

### <a name="sql-db-instances-rds"></a>SQL DB-példányok (RDS)
Ez a widget az elmúlt 30 nap trendje alapján kiemeli a futó Amazon RDS-példányok számát.
- Kattintson a widgetre az RDS-példány időbeli jelentésének megnyitásához.

## <a name="optimizer-dashboard"></a>Optimalizáló irányítópult
Ez az irányítópult a javaslatok, a használaton kívüli erőforrások és a lehetséges megtakarítások leépítését jeleníti meg.  
![A különböző jelentéseket bemutató optimalizáló irányítópult](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>RI kalkulátor
Ez a widget megjeleníti a RI vásárlási javaslatok számát, és kiemeli a lehetséges éves megtakarításokat.
- Kattintson a widgetre a fenntartott példány-kalkulátor megnyitásához, ahol meghatározhatja, hogy mikor érdemes igény szerinti vagy fenntartott díjszabást használni.

### <a name="sizing"></a>Méretezés
Ez a widget kiemeli a méretezés javasolt és lehetséges megtakarítását, ha megvalósítják.
- Kattintson a widgetre a EC2 költséghatékony méretezési javaslatok jelentés megnyitásához.

### <a name="unused-ri-detector"></a>Nem használt fenntartott példányok érzékelője
Ez a widget kiemeli a nem használt Amazon EC2-foglalások számát.
- Kattintson a widgetre a jelenleg nem használt foglalások jelentés megnyitásához, ahol megtekintheti a módosítható nem használt foglalásokat.

###  <a name="available-disks"></a>Elérhető lemezek
Ez a widget kiemeli a telepítésben lévő nem csatolt lemezek számát.
- Kattintson a widgetre a nem csatolt lemezek jelentés megnyitásához.

### <a name="rds-ri-calculator"></a>RDS RI kalkulátor
Ez a widget kiemeli az Amazon RDS-példányok foglalási javaslatainak számát és a lehetséges megtakarításokat.
- Kattintson a widgetre az RDS RI vásárlási javaslatok jelentés megnyitásához, ahol megtekintheti az igény szerinti példányok helyett a fenntartott példányok használatára vonatkozó Cloudyn javaslatokat.

### <a name="rds-sizing"></a>RDS méretezés
Ez a widget a méretezési javaslatok és a lehetséges megtakarítások számát jeleníti meg.
- Kattintson a widgetre az RDS-méretezési javaslatok jelentés megnyitásához, amely részletes Amazon RDS-méretezési javaslatokat jelenít meg.

Az optimalizálási javaslatok az elmúlt hónapban megfigyelt használati és teljesítménybeli adatokon alapulnak.

## <a name="s3-tracker-dashboard"></a>S3 Tracker-irányítópult
Az S3 Tracker irányítópultjának segítségével megkeresheti, elemezheti és megjelenítheti az aktuális tárterület-használatot és trendeket.  
![S3 Tracker-irányítópult, amely különböző jelentéseket mutat be](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>Összes gyűjtő
Ez a widget kiemeli a gyűjtők teljes méretét GB-ban, valamint a gyűjtőben lévő objektumok teljes számát.
- Kattintson a widgetre az S3-méret jelentés terjesztésének megnyitásához. A jelentés segít az S3 méretének elemzésében a gyűjtő, a legfelső szintű mappa, a tárolási osztály és a verziószámozási állapot alapján.

### <a name="bucket-properties"></a>Gyűjtő tulajdonságai
Ez a widget kiemeli a tárolási gyűjtők teljes számát.
- Kattintson a widgetre az S3 Bucket Properties-jelentés megtekintéséhez.

### <a name="scan-status"></a>Vizsgálat állapota
Ez a widget kiemeli, hogy mikor történt az utolsó S3 vizsgálat, és amikor a következő elindul.
- Kattintson a widgetre az S3 vizsgálat állapotáról szóló jelentés megnyitásához.

### <a name="storage-by-bucket"></a>Tárolás gyűjtő szerint
Ez a widget kiemeli az egyes gyűjtők tárolási osztályának százalékos arányát.
- Kattintson a widgetre az S3-méret jelentés terjesztésének megnyitásához. A jelentés segít az S3 méretének elemzésében a gyűjtő, a legfelső szintű mappa, a tárolási osztály és a verziószámozási állapot alapján.

### <a name="number-of-objects-by-bucket"></a>Objektumok száma gyűjtő szerint
Ez a widget kiemeli a másodpercenkénti objektumok számát a tényleges szám és százalék értékben. Vigye az egérmutatót a gyűjtőre, és tekintse meg az összes objektumot.
- A widgetre kattintva megnyithatja az S3 méretű jelentés eloszlását (vizsgálat alapján).

## <a name="cloud-comparison-dashboard"></a>Felhő-összehasonlító irányítópult
A felhő-összehasonlító irányítópult segítségével összehasonlíthatja a különböző felhőalapú szolgáltatók költségeit a díjszabás, a processzor típusa és a RAM mérete alapján.  
![A felhő összehasonlító irányítópultja, amely különböző jelentéseket mutat be](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>EC2-díj az Azure-ban példány típusa szerint
Ez a widget kiemeli az elmúlt 30 nap használatot az igény szerinti díjszabásban. Összehasonlítja a költségeket a jelenlegi Amazon EC2 és az Azure-ban rejlő lehetséges díjakkal.
- Vigye a kurzort a sávok fölé a használati költségek összehasonlításához.
- A widgetre kattintva megnyithatja az üzembe helyezés – Cost Analysis-jelentés portját.

### <a name="ec2-cost-in-azure"></a>EC2-díj az Azure-ban
Ez a widget megjeleníti a jelenlegi Amazon-EC2 költségeit, és összehasonlítja őket az Azure-ban. Az összehasonlítás az igény szerinti díjszabásban az elmúlt 30 nap használatán alapul.
- Kattintson a widgetre az üzembe helyezés-Cost Analysis-jelentés portjának megnyitásához.

### <a name="ec2azure-instance-type-mapping"></a>EC2/Azure-példány típusának leképezése
Ez a widget kiemeli a rugalmas számítási egységek az Amazon EC2 és az Azure közötti legmegfelelőbb leképezését.
- Kattintson a widgetre a példányok típus-leképezési jelentésének megnyitásához.

## <a name="next-steps"></a>Következő lépések
- A jelentésekkel kapcsolatos további információkért olvassa el a [Cloudyn-jelentések használata](use-reports.md) című cikket.
