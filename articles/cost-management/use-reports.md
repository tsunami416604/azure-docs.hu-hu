---
title: Cost Management jelentéseinek használata az Azure Cost Managementben |} A Microsoft Docs
description: Ez a cikk ismerteti a különböző Cost Management jelentéseinek használata a Cloudyn portálon.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/06/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 055584bd27c978fb71e6c09995c28be7348acda8
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37908272"
---
# <a name="use-cost-management-reports"></a>Költségkezelési jelentések használata

Ez a cikk ismerteti a költségkezelési jelentések, amelyek szerepelnek a Cloudyn portálon célját. Azt is ismerteti, hogyan hatékonyan használhatja a jelentéseket. A legtöbb jelentések intuitív, és rendelkezik egy egységes megjelenés és működés. A legtöbb művelet elvégezhető egy jelentésben, műveletek végezhetők az egyéb jelentésekben. Költségkezelési jelentések, beleértve az testreszabásához, és mentse vagy ütemezett jelentéseket, használatáról lásd [ismertetése költségjelentéseket](understanding-cost-reports.md).

## <a name="report-types"></a>Jelentéstípusok

Költségkezelési jelentések három típusa van:

- Többlet idő jelentéseket. Ha például az időalapú költségeket tartalmazó jelentést. Többlet idő a jelentések egy idősorozat-adatok megjelenítése egy kiválasztott időszakban előre meghatározott megoldást, és heti felbontása megjelenítése az utolsó két hónapig. Csoportosítás és szűrés segítségével különböző adatpontok nagyítás.
  - Többlet idő jelentések segítségével megtekinthessék a szalagfelhasználási tendenciákat, és a csúcsok és a rendellenességek észlelését.
- Problémaelemző jelentések. Ha például a Cost Analysis felhasználónkénti jelentés. Ezek a jelentések összesített adatok megjelenítése a időszakban határozza meg, és a csoportosítási és az adatok szűrésének engedélyezése.
  - Elemzési jelentés segítségével megtekintheti az adatforgalmi csúcsokhoz és döntse el, anomáliadetektálási kiváltó okokat, és jelenítse meg egy részletes break ki az adatokat.
- Táblázatos jelentéseket. Megtekintheti a jelentést egy táblát, de egyes jelentések csak táblaként tekinthetők meg. Ezek a jelentések adja meg a részletes elemek listája.
  - Az ajánlások táblázatos jelentések – nincsenek javaslatok egyetlen vizualizációja sincs. Azonban jelenítheti meg az ajánlás eredményeket. Ha például megtakarítások idővel.
  - Táblázatos jelentések hasznosak, listák, műveletek, illetve az adatok exportálása további feldolgozás céljából. Ha például a jóváírási jelentések.

A Cost szóló jelentésekben megtekintheti, vagy _tényleges_ vagy _amortizált_ költségeket.

Tényleges költségek jelentéseiben jelennek meg a kifizetések a kijelölt időszakban. Például minden egyszeri díjak, mint például (RI). a fenntartott példányok vásárlása jelennek meg a tényleges költségek jelentéseiben, költség kiugrások.

Amortizált költségek jelentései egyszeri díjak, amelyekre érvényes keresztül terjednek. Például egyszeri díjak fenntartott példány vásárlására oszlik el a Foglalás időtartama és ugrásszerű nem jelennek meg. Az amortizált nézet az egyetlen módja igaz trendeket és költség leképezések.

Bizonyos esetekben a törlesztési külön jelentésként jelennek meg. Ilyenek például a költségek elemzése és az amortizált költségek elemzése jelentéseket. Más esetekben a törlesztési egy olyan jelentés szabályzat, például a Költséglefoglalási és költségelemzés jelentéseket.

Bármely jelentés rendszeres kézbesítésre ütemezheti. Költségek jelentései lehetővé teszik egy küszöbértéket beállítását úgy is hasznos, ha a riasztásokat.

## <a name="cost-analysis-vs-cost-allocation"></a>A költséglefoglalási és költségelemzés

_Elemzés_ jelentések jelenítik meg a felhőszolgáltatók számlázási adatokat. A jelentések segítségével, csoport és a számlázási fájlból részletezett különböző adatok szegmensek részletesen. A jelentések lehetővé a pontos költségeket a felhő gyártója által biztosított nyers számlázási adatok.

Néhány _költségelemzés_ jelentések költségeket az erőforráscímkék nem csoportosítás. És a számlázási adatokat címke-alapú csak akkor jelenik meg a jelentésekben, hozzon létre egy költség modell használatával költségek lefoglalása után [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

_Foglalási költség_ jelentés érhető el, miután létrehozott egy költség modell használatával [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs). Cost Management dolgozza fel a költségek és a számlázási adatokat, és _megegyezik_ cloud-fiókok használatát, és a címke adatait az adatokat. Az adatok megfelelő, a Cost Management a használati adatok hozzáférésre van szüksége. Ha fiókok, amelyekről frissítések hiányoznak a hitelesítő adatokat, azok van jelölve _Kategorizálatlan erőforrások_.

## <a name="dashboards"></a>Irányítópultok

Irányítópultok Cloudy a jelentések magas szintű áttekintést biztosítanak. Az irányítópultok widgetek épülnek fel, és minden widget lényegében egy jelentés miniatűr. Ha Ön [jelentések testreszabásával](understanding-cost-reports.md#save-and-schedule-reports), mentse azokat a jelentések mappa és a rendszer hozzáadja az irányítópultot. Az irányítópultok kapcsolatos további információkért lásd: [irányítópultokkal kulcs költség metrikákat tekinthet meg](dashboards.md).

## <a name="budget-information-in-reports"></a>A jelentésekben költségvetési információk

Felügyeleti jelentések megjelenítése költségvetési információk számos költség, manuálisan egy létrehozását követően. Így a jelentések nem jelennek meg a költségvetési információk költségvetési létrehozásáig. További információkért lásd: [költségvetés-beállítások](#budget-settings).

## <a name="reports-and-reporting-features"></a>Jelentések és a jelentéskészítési szolgáltatások

Clouydn az alábbi jelentések és jelentéskészítési funkciókat is tartalmaz.

### <a name="cost-navigator-report"></a>A kezelő jelentést költség

A költségeket a kezelő jelentést gyors módja egy irányítópult-nézet használata a számlázási fogyasztás megtekintéséhez. Szűrők és a szervezet költségeinek összesített nézetét mutatja azonnal az alapszintű nézetek egy része van. Költségek dátum szerint jelennek meg. A jelentés kezdeti képet a költségeket jelent, mert nincs olyan rugalmas, vagy, átfogó, mint sok más jelentések vagy egyéni irányítópultokat, Ön hozza létre.

Alapértelmezés szerint a jelentés főbb nézetek megjelenítése:

- Egy munkahét sáv diagram nézet megjelenítése időalapú költségek. Módosíthatja a **dátumtartomány** dátum tartomány sávdiagram módosításához.
- Szolgáltatás használata a kördiagram kiadásai.
- Erőforrás kategorizálási címkék, kördiagram alapján.
- Költség költségelemek kördiagram szerint.
- Költség összesen dátum szerint a lista nézetben.

### <a name="cost-analysis-report"></a>Költségelemzési jelentés

A Költségelemzési jelentést egy számítási, költséghelyi visszacsatolási és költséghelyi elszámolási, a házirend alapján. Gyűjti a kiválasztott időtartam alatt, a költség az összes lefoglalási szabályokat alkalmazását követően a felhőbeli fogyasztást. Például, számítja ki a költségeket címkék alapján, ismét kiosztja a címkézetlen erőforrások költségeit és szükség esetén osztja ki a fenntartott példányok kihasználtságát.

A szabályzatok beállítása [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) szolgálnak a költségek elemzése az jelentés és az eredmények majd mostantól a nyers adatoktól a felhő gyártója által biztosított információkkal.

Hogyan számítják az ebben a jelentésben? A Cost Management szolgáltatás biztosítja, hogy foglalási megőrzi minden társított fiók biztonságának alkalmazásával _affinitás fiók_. Affinitás biztosítja, hogy egy fiókot, amely nem használja egy adott szolgáltatás nem rendelkezik a szolgáltatás számára lefoglalt költségek. A költségek abban, hogy a fiók továbbra is az adott fiókban, és nem számítja ki a foglalási szabályzatok által elhatárolt. Előfordulhat például, hogy öt összekapcsolt fiókok. Ha csak a tárolási szolgáltatások használatához három a majd csak a tárolási szolgáltatások költsége van lefoglalva a három fiókot a címkék között.

A költségelemzés jelentésben használja:

- A szervezet jóváírás és költséghelyi visszacsatolási kiszámítása
- Az összes a költségek kategorizálása
- Jeleníti meg a teljes telepítés a egy megadott időkereten összesített nézetet.
- A költségmodell létrehozott szabályzatok alapján címke kategóriák nézet költségeket.

A Költségelemzési jelentést használata:

1. A dátumtartományok kijelölésével.
2. Adja hozzá a címkéket, igény szerint.
3. Csoportok hozzáadása.
4. Válassza ki a korábban létrehozott költségmodellt.

### <a name="cost-over-time-report"></a>Időalapú költségek jelentése

Idő a jelentés felett költségeit a költséglefoglalási eredmények a time series jeleníti meg. Lehetővé teszi, hogy látni fogja a trendeket, és a szabálytalanságok észleléséhez a központi telepítésben. Lényegében egy meghatározott időszakban elosztott költségeit jeleníti meg. A jelentés tartalmazza a fő közreműködőkkel, beleértve a folyamatos költségeket és a egy kiválasztott időszakban fordított fenntartott példány egyszeri díjak. Házirendek beállítása [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) ebben a jelentésben szerepel.

Az időalapú költségeket tartalmazó jelentés használja:

- Módosítások megtekintéséhez az idő függvényében, és milyen hatású módosítja egy napig (vagy dátumtartomány) a következő.
- Költségek elemzése egy adott példányt az idő függvényében.
- Megismerheti miért volt egy adott példányt egy költség növelését.

Az időalapú költségeket tartalmazó jelentés használata:

1. A dátumtartományok kijelölésével.
2. Adja hozzá a címkéket, igény szerint.
3. Csoportok hozzáadása.
4. Válassza ki a korábban létrehozott költségmodellt.
5. Válassza ki a tényleges költségek vagy az amortizált költségek.
6. Döntse el, hogy foglalási szabályok alkalmazása a nyers adatok nézete számlázási nézetre, vagy a újraszámítja a költségek nézet.

### <a name="actual-cost-analysis-report"></a>Tényleges költségek elemzése – jelentés

A tényleges költségelemzés jelentésben szolgáltató költségeket a nem módosított előfizetésekben. A fő közreműködőkkel, beleértve a folyamatos költségeket és egyszeri díjak jeleníti meg.

A jelentés segítségével az előfizetések költségadatok megtekintéséhez. A jelentésben, az Azure-előfizetések jelennek meg **fióknév** és **fiók száma**. **Kapcsolódó fiókok** AWS előfizetések megjelenítése. A nézetben per előfizetés költségeit, az egyes fiókok számára egy bontása a **csoportok**, válassza ki az előfizetést, amely rendelkezik típusát.

A tényleges költségelemzés jelentésben használja:

- Elemezheti és a egy megadott időszakban töltött nyers szolgáltató költségek figyelése.
- Egy küszöbérték-értesítés ütemezése.
- Elemezheti a fiókokat és entitásokat, módosítás nélküli költségeit.

### <a name="actual-cost-over-time-report"></a>Tényleges időalapú költségek jelentése

A tényleges időalapú költségeket tartalmazó jelentés egy standard szintű elemzési jelentés költség terjesztése egy meghatározott alkalommal feloldási keresztül. A jelentés megjeleníti a költségkeret-beállítási is látni fogja a trendeket, valamint a költségkeret szabálytalanságok észleléséhez az idő függvényében. Ez a jelentés tartalmazza a fő közreműködőkkel, beleértve a folyamatos költségeket és a egy kiválasztott időszakban fordított fenntartott példány egyszeri díjak.

A tényleges időalapú költségeket tartalmazó jelentés használja:

- Tekintse meg a energiaköltségi trendjeinek időbeli alakulása.
- Szabálytalanságok található költség.
- Keresse meg a felhőszolgáltatók kapcsolatos összes költséget kapcsolatos kérdések.

### <a name="amortized-cost-reports"></a>Amortizált költségek jelentései

Amortizált költség jelentések linearizált mutat be nem-használat egy készlete alapján, vagy szolgáltatási díjak egyszeri fizetendő költségeket, és azok költségei eloszlatva egyenletesen azok élettartama során. Ha például egyszeri díjak a következők lehetnek:

- Éves támogatási díjak
- Éves biztonsági összetevő díjak
- Fenntartott példányok vásárlására díjak
- Néhány Azure Marketplace-elemek

A számlázási fájlban egyszeri díjak jellemző, ha a szolgáltatás használatalapú kezdő és záró dátum (timestamp) egyenlő értékek találhatók. A Cost Management szolgáltatás majd felismeri, hogy a rendszer amortizált például egyszeri díjak. Egyéb fogyasztásalapú szolgáltatások igény szerinti használat költségei nem amortizált vannak.

Amortizált költségek jelentései a következők:

- Amortizált költségek elemzése
- Amortizált költségek időbeli alakulása

### <a name="cost-analysis-report"></a>Költségelemzési jelentés

A Költségelemzési jelentést biztosít a felhőbeli fogyasztást és a egy kiválasztott időszakban költségeit. A szabályzatok beállítása a [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) használatban vannak a Költségelemzési jelentést.

Hogyan Cost Managementben Ez a jelentés kiszámítása?

A Cost Management biztosítja, hogy a kiosztási integritását, egyes társított fiók megőrzi alkalmazásával _affinitás fiók_. Affinitás biztosítja, hogy egy fiókot, amely egy adott szolgáltatás nem használja még nem rendelkezik a szolgáltatás számára lefoglalt költségek. A költségek azt, hogy a fiók továbbra is az adott fiókban a foglalási szabályzatok nem számítja ki. Előfordulhat például, hogy öt összekapcsolt fiókok. Ha csak a tárolási szolgáltatások használatához három a majd csak a tárolási szolgáltatások költsége van lefoglalva a három fiókot a címkék között.

A költségelemzés jelentésben használja:

- Jeleníti meg a teljes telepítés a egy megadott időkereten összesített nézetet.
- A költségmodell létrehozott szabályzatok alapján címke kategóriák nézet költségeket.

### <a name="cost-over-time-report"></a>Időalapú költségek jelentése

Az időalapú költségeket tartalmazó jelentést a költségkeret-beállítási idővel felismerése érdekében trendek és a szabálytalanságok figyelje meg a központi telepítésben jeleníti meg. Lényegében egy meghatározott időszakban elosztott költségeit jeleníti meg. A jelentés tartalmazza a fő közreműködőkkel, beleértve a folyamatos költségeket és a egy kiválasztott időszakban fordított fenntartott példány egyszeri díjak. Házirendek beállítása [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) ebben a jelentésben szerepel.

Az időalapú költségeket tartalmazó jelentés használja:

- Módosítások megtekintéséhez az idő függvényében, és milyen hatású módosítja egy napig (vagy dátumtartomány) a következő.
- Költségek elemzése egy adott példányt az idő függvényében.
- Megismerheti miért volt egy adott példányt egy költség növelését.

### <a name="custom-charges-report"></a>Egyéni díjak jelentés

Vállalati és a CSP-felhasználók gyakran tapasztalhatja magukat a hozzáadott szolgáltatásokat biztosító külső vagy belső ügyfeleiknek, a saját felhő erőforrás-használat mellett. Megadhat új szolgáltatások és az esetleges kedvezményeket, az ügyfél számlázási vagy költséghelyi elszámolási jelentések egyéni sorként hozzáadott egyéni kell fizetni.

Egyéni szolgáltatási díjak szolgáltatásokat, amelyek normál esetben nem jelennek meg, szerepel a számlán jelenik meg. Az Ön által létrehozott egyéni díjak ezután költségjelentésben megjelennek.

*Egyéni díjak nem az egyéni díjszabás*. Egyéni díjak listája nem jelenik meg a különböző díjszabás, akkor előfordulhat, hogy lehet díjszabási. Az AWS díjfizetést például jelennek meg ugyanúgy, mint azok díjat számítunk fel.

Egyéni díj létrehozása:

1. A **egyéni díjak**, kattintson a **új hozzáadása**. A _adjon hozzá új egyéni díj_ párbeszédpanel jelenik meg.
2. A **szolgáltatónevet**, adja meg a szolgáltató nevét.
3. A **szolgáltatásnév**, írja be azt a szolgáltatás.
4. A **leírás**, adjon meg egy leírást a egyéni költség.
5. A **típus**, adja meg a select **százalékos** és szolgáltatások legördülő menüben válassza ki a szolgáltatások közé tartozik egy egyéni díjak a költségek jelentéseiben.
6. A **fizetési**válassza, ha egy egyszeri díjat vagy ismétlődő díj a díjat. Egy ismétlődő díjat kell fizetni a díjat, ha Amortized válassza, ha azt szeretné, hogy a díj amortizált kell, és válassza ki a hónapok száma.
7. A **dátumok**, ha egyszeri van kiválasztva, a **hatályba lépés dátuma**, adja meg a dátumot, a díjat. Ha díjból ismétlődő be van jelölve, adja meg a dátumtartományt, beleértve a kezdő dátum és záró dátumát a díjat.
8. Az a **entitások fa**, válassza ki az entitásokat, a díj a alkalmazni, és válassza ki a kívánt **a**.

_Egy entitás történő hozzárendelése esetén a díjak, felhasználók nem módosíthatják azokat. Egy rendszergazdának egy fölérendelt entitásba hozzáadott költségek írásvédettek._

Egyéni díjak megtekintése:

Egyéni díjak költségjelentésben megjelennek. A tényleges költségelemzés jelentésben például nyisson meg ezután alatt **bővített szűrők**válassza **önálló**. Ezután szűrjön azokra **egyéni díjak**.

### <a name="cost-allocation-360"></a>A Cost Allocation 360

Cost Allocation 360 használatával egyéni foglalási modelljeinek költségek hozzárendelése felhasznált felhőalapú erőforrásokhoz. Számos jelentést az egyéni modellek létrehozott egyéni modellek adatainak megjelenítése. És egyes jelentések csak adatok megjelenítése egyéni költségmodell a költséglefoglalási a létrehozása után.

Egyéni modellek létrehozásával kapcsolatos további információkért lásd: [oktatóanyag: költségek kezelése az Azure Cost managementtel](tutorial-manage-costs.md).

### <a name="cost-vs-budget-over-time-report"></a>A Cost vs. Költségvetés időalapú jelentés

A Cost vs. Költségvetés időalapú a jelentés lehetővé teszi a fő közreműködőkkel előirányzott költségvetéssel összehasonlítását. A hozzárendelt költségvetés jelenik meg a jelentésben, hogy a költségvetés (felett vagy alatt/par) használat idővel tekintheti meg. Megjelenítése/elrejtése mezővel, amely a jelentés tetején választhat nézet költség, a költségvetés, a összesített költségeit és a teljes fennmaradó.

### <a name="current-month-projected-cost-report"></a>Aktuális havi előre jelzett költségről szóló jelentés

Az aktuális havi előre jelzett költségről szóló jelentés az aktuális hónap elejétől számított költség összegzése betekintést nyújt. Ez a jelentés megjeleníti az előző hónap a hónap kezdetétől fogva a költségeket, és az összes az aktuális hónapra előre jelzett költsége. Az aktuális havi előre jelzett költség, hogy a naprakész havi díja, és a egy leképezés a költségek figyelemmel kísérni az elmúlt 30 napban alapján számítjuk.

Az aktuális havi előre jelzett költségről szóló jelentés használja:

- Projekt havi díjak szolgáltatás szerint
- Havi projektköltségek fiók használatával

### <a name="annual-projected-cost-report"></a>Éves előre jelzett költségről szóló jelentés

Az éves előre jelzett költségek jelentés éves előre jelzett költségek előző költési trendek alapján megtekintését teszi lehetővé. A következő 12 hónapra előre jelzett költségek átfogó jeleníti meg. A leképezések jönnek létre a következő 12 hónapban extrapolálni alapján a költségeket az elmúlt 30 nap használati trend függvény használatával.

### <a name="budget-management-settings"></a>Költségvetés felügyeleti beállítások

Költségvetés felügyeleti a pénzügyi év költségvetési megadását teszi lehetővé.

Egy entitás költségvetési hozzáadása:

1. A költségvetés felügyelet lapon alatt **entitások**, válassza ki az entitást, ahol szeretné létrehozni a költségvetést.
2. A költségvetési évben válassza ki az év, ahol szeretné létrehozni a költségvetést.
3. Minden hónapban a költségvetés beállítását majd **mentése**.

Az éves költségvetés-fájl importálása:

1. Alatt **műveletek**válassza **exportálása** letölteni egy üres CSV-sablont a költségvetés a alapjaként használni.
2. Töltse ki a költségvetés és a CSV-fájlt, és mentse helyileg.
3. A **műveletek**válassza **importálás**.
4. Válassza ki a mentett fájlt, és kattintson a **OK**.

Az elkészült költségvetés alatt exportálhatja egy CSV-fájl **műveletek**válassza **exportálása** letölteni a fájlt.

Amikor elkészült, költségelemzés jelentésekben és a költségek és az a költségvetés jelenik meg. Költségvetés időalapú jelentést. Költségvetés küszöbértékek alapján jelentéseket is ütemezheti.

### <a name="azure-resource-explorer-report"></a>Az Azure erőforrás-kezelő jelentés

Az Azure Resource Explorer jelentésben tömeges a az Azure Cost Management szolgáltatásban elérhető erőforrások listáját. A hatékony használatot a jelentést, az Azure-fiókokhoz kell bővítette a metrikák engedélyezve. Kiterjesztett metrikák Cost Management hozzáférést biztosítanak az Azure-beli virtuális gépek. További információkért lásd: [kiterjesztett metrikák az Azure-beli virtuális gépek hozzáadása](azure-vm-extended-metrics.md).

### <a name="azure-resources-over-time-report"></a>Az Azure erőforrások időalapú jelentés

Az Azure erőforrások időalapú jelentés egy adott időszak alatt futó összes erőforrás bontását mutatja. A hatékony használatot a jelentést, az Azure-fiókokhoz kell bővítette a metrikák engedélyezve. Kiterjesztett metrikák Cost Management hozzáférést biztosítanak az Azure-beli virtuális gépek. További információkért lásd: [kiterjesztett metrikák az Azure-beli virtuális gépek hozzáadása](azure-vm-extended-metrics.md).

### <a name="instance-explorer-report"></a>Példány Explorer jelentés

A példány Explorer jelentés segítségével az eszközök a virtuális gépek különböző metrikákat tekinthet meg. Feltárás konkrét példányok információk megtekintéséhez, mint például a következőket teheti:
- Példány időközönként fut
- A kiválasztott időszakban életciklusa
- Processzorkihasználtság
- Hálózati bemenet
- Kimenő adatforgalom
- Aktív lemezek

A példány Explorer jelentés a megadott dátumtartományban minden futó időközönként gyűjt, és ennek megfelelően összesíti az adatokat. Megtekintheti a futó időszakok dátumtartományban, bontsa ki a példány. Az egyes példányok költségének kiszámításához a tartomány alapján AWS és az Azure lista díjszabása kiválasztott dátum. Kedvezmények nem. További mezőket adhat hozzá a jelentés megjelenítése/elrejtése mezőkkel.

Példány Explorer jelentés használja:

- Gépenként becsült költségek kiszámításához.
- Hozzon létre teljes listáját, beleértve az összesített futó óra, minden olyan számítógép, amely során egy időtartományt volt aktív.
- Hozzon létre egy listát felhőszolgáltató vagy a fiók.
- Számítógépek megtekintése során egy időtartományt leállt, vagy hozza létre.
- Az összes jelenleg leállított gépek megtekintéséhez.
- A címkék az egyes gépek megtekintéséhez.

### <a name="instances-over-time-report"></a>Példányok időalapú jelentés

A példányok időalapú jelentés segítségével láthatja aktív gépek maximális száma minden, a kijelölt időtartományban. Ha a megadott feloldási week vagy month, az eredmények olyan túllépése bármelyik nap a hónapban aktív gépek maximális száma. Válassza ki a jelentésben megjeleníteni kívánt szűrőket a dátumtartományok kijelölésével.

### <a name="instance-utilization-over-time-report"></a>Példány időalapú felhasználási jelentés

Ez a jelentés az összes példány idővel CPU és memória használata egy bontását mutatja.

### <a name="compute-power-cost-over-time-report"></a>Számítási teljesítmény időalapú költségeket tartalmazó jelentést

A számítási teljesítmény Over Time jelentés alkalmazásonkénti bontásban számítási teljesítményt a megadott dátumtartományban. Más jelentések megjelenítése a gépek vagy a futásidejű órák számát, bár ez a jelentés tartalmazza a processzor-üzemóra, számítási egységek használati óráinak száma vagy GB RAM-MAL óra.

A jelentés használja:

- Ellenőrizze a számítási teljesítmény belül egy adott dátumtartományon belül.
- Nézet a számítási költségek felosztási modellek időpontokat.

Ez a jelentés a [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) a meghatározott címkézés és a házirendek a kiválasztott házirend alapján szabályzatokat, az eredmények jelennek meg. Ha nem rendelkezik egy létrehozott házirend, majd eredmények nem jelennek meg.

### <a name="compute-power-average-cost-over-time-report"></a>Számítási teljesítmény átlagos időalapú költségek jelentése

Több mint csak az egyes futó gépek költsége megtekintéséhez használhatja a számítási teljesítmény átlagos időalapú költségeket tartalmazó jelentést. A jelentésben a átlagköltség óra. példány, a processzor-üzemóra, a számítási üzemideje (óra) és a GB RAM-MAL óra. A jelentés a hatékonyság érdekében az üzembe helyezés betekintést nyújt.

Ez a jelentés a [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) a meghatározott címkézés és a házirendek a kiválasztott házirend alapján szabályzatokat, az eredmények jelennek meg. Ha nem rendelkezik egy létrehozott házirend, majd eredmények nem jelennek meg.

### <a name="s3-cost-over-time-report"></a>S3 időalapú költségeket tartalmazó jelentést

Az S3 időalapú költségeket tartalmazó jelentést bontásban megmutatja az Amazon Simple Storage Service (S3) költség / gyűjtőbe egy adott időszakban az idő függvényében. A jelentés segít a keresse meg a gyűjtőket, amelyek a fő költség-illesztőprogramokat, és megjeleníti a trendek az S3 használat és költségek.

### <a name="s3-distribution-of-cost-report"></a>S3 terjesztési szóló jelentés

A jelentés segítségével elemezheti a S3 költségeit az elmúlt hónapban a gyűjtőbe, valamint a tárolási osztály alapján. A diagram nézet segítségével állítsa be a láthatósági küszöbérték. Vagy használhatja a táblázatos nézetre részösszegek megtekintéséhez.

### <a name="s3-bucket-properties-report"></a>S3 gyűjtő tulajdonságairól szóló jelentés

A jelentés segítségével S3 gyűjtő tulajdonságainak megtekintése. A diagram nézet segítségével állítsa be a láthatósági küszöbérték. Vagy használhatja a táblázatos nézetre részösszegek megtekintéséhez.

### <a name="rds-instances-over-time-report"></a>A távoli asztali szolgáltatások példányok időalapú jelentés

A jelentés segítségével megtekintheti az összes Amazon relációs adatbázis-szolgáltatás (RDS) példányai a megadott időszakban egy bontása.

### <a name="rds-active-instances-report"></a>A távoli asztali szolgáltatások aktív példány jelentés

A jelentés segítségével elemezheti a távoli asztali szolgáltatások aktív példány. A jelentést bontsa ki a sortételt a további információk megjelenítéséhez.

### <a name="azure-reserved-instances-report"></a>Az Azure fenntartott példányok jelentés

Az Azure Reserved Instances jelentés nyújt minden Azure fenntartott példányok egyetlen nézetben. Ez a jelentés megjeleníti minden egyes, a saját sortételt. A jelentés az adott vásárlás, például a fiókot, amelyet vásárolta, a vásárlás és a példány típusa, és így tovább hátralévő napok száma típus adatait is mutatja. Megjelenítheti vagy elrejtheti a jelentés adatainak megjelenítése/elrejtése mezőkkel.

Használja az Azure Reserved Instances jelentés megtekintése:

- Beszerzés dátuma szerint minden foglalást listája.
- Amíg le nem jár a fenntartott példányok fennmaradó idő.
- Egyszeri díjak.
- A fiókot, amelyet a megvásárolt fenntartott példányokkal, és mikor.

### <a name="aws-reserved-instances-report"></a>Az AWS Reserved Instances jelentés

Az AWS Reserved Instances jelentésben egyetlen nézetben mutatja az összes AWS lefoglalt példányokat. Ez a jelentés megjeleníti minden egyes saját sortételt és az adott vásárlás, például a fiókot, amelyet vásárolta, a vásárlás és a példány típusa, és így tovább hátralévő napok száma típus részleteit. Megjelenítheti vagy elrejtheti a jelentés adatainak megjelenítése/elrejtése mezőkkel.

Használja az AWS Reserved Instances jelentés megtekintése:

- Beszerzés dátuma szerint minden foglalást listája.
- Amíg le nem jár a fenntartott példányok fennmaradó idő.
- Egyszeri díjak.
- Az eredeti vásárlás azonosítója (foglalás azonosítója).
- A fiókot, amelyet a megvásárolt fenntartott példányokkal és mikor.

### <a name="ec2-ri-buying-recommendations-report"></a>A jelentés EC2 fenntartott példány vásárlására vonatkozó javaslatok

Az erőforrás-felhasználás felhő alapját az igény szerinti modellje, ahol erőforrásokat csak akkor, ha a használt használatáért számítunk fel. Nincsenek előzetes kötelezettségvállalás – hogy csak azért fizet, amit ténylegesen használ, amikor ezzel.

Az AWS-díjszabási modell, a rugalmas számítási (EC2) felhőszolgáltatások alternatívát nyújt – a fenntartott példány (RI). Erről a díjszabási modellről garantálja a felhasználók a kapacitást, amikor szükségük van rá a fenntartott példányok időtartamára. A fenntartott példányok igény szerinti áraihoz képest jelentős kedvezményeket kínál. Cserébe felhasználók hajthatnak végre egy virtuális példány használatára vonatkozó előzetes kötelezettségvállalást. Az előzetes kötelezettségvállalást van kötve egy megadott termékcsalád, a mérete, a rendelkezésre állási zónában (AZ) és az operációs rendszer, a időszakban kötelezettségvállalás (egy vagy három év). A fenntartott példányok lehetővé teszi, hogy hatékonyan jövőbeli kapacitásának megtervezése, valamint parancs számára, így a szolgáltatások használatával kapcsolatos AWS.

A fenntartott példányok, amelyek minden előzetes három fizetési beállítások:

- Tömeges sum nap 0, a legnagyobb kedvezményeket kínál
- Nincs előre - található, amely a fenntartott példányok költségeinek fizetős havi részletben a fenntartott példányok időtartama alatt, a legalacsonyabb kedvezményeket kínál
- Részlegesen előre, mely ¼ –, akkor az ár ½ fizetni meghozni, és a többi havi részletben, és a kedvezményes díjszabás alacsonyabb, de zárja be, minden előzetes díjaival

A Cost Management hasznos üzemidejének az egyes gépek kiértékeli az elmúlt 30 napra vonatkozóan. A Cost Management használatát javasolja, hogy a fenntartott példányok vásárlása, ha a gép futtatásához a Hónapjánál az aktuális szinten üzemidő helyett.

A jelentés megjeleníti a javaslatokat, a legtöbb pénzt takaríthat meg az évben indoklásának a következőt tartalmazza. A javaslatok javasolni, és cserélje le a fenntartott példányok igény szerinti példányok. A fenntartott példányok közvetlenül a jelentés vásárolhat.

Minden lapon teljes jelentésként nyitja meg. Lapok jelentős szakaszai a következők:

- **EC2 fenntartott példányok vásárlásának hatását ismertető** – Ez a szakasz szimulálja a különbség a fenntartott példányok igény szerinti vs. Kattintson a **nagyítás**, a teljes EC2 fenntartott példányok vásárlásának hatását ismertető jelentés megtekintéséhez, a szűrők már meg van adva, a javaslat. Ez a jelentés tartalmazza az összes lehetséges a fenntartott példányok vásárlási beszerzési hatását. Módosíthatja a várható átlagos üzemidő megtekintéséhez a mentése való EC2 fenntartott példány megvásárlása lehetséges.

- **Elemzés mentése** – Ez a szakasz tartalmazza a lehetséges megtakarítás érhető el, és a hónap, a megtakarítások actualized vannak, amikor a Cost Management javaslatokat követve. A tényleges megtakarítások és a százalékérték mentett pirossal ki vannak emelve.

- **EC2 fenntartott példány típusok összehasonlítása** – Ez a szakasz a megtérülési ráta főbb újdonságai a Cost Management ajánlott üzembe helyezési, például az összes releváns lehetőségeit emeli ki. Ebben a jelentésben az eredmények azt feltételezik, hogy a gép fut-e a 100 %-os üzemidő. Kattintson a **nagyítás** nyissa meg a részletes jelentést.

- **Példányok időalapú** – ebben a szakaszban egy a javaslat, az OnDemand, a fenntartott példányok és a helyszíni társított összes példány áttekintését jeleníti meg. Kattintson a **nagyítás** nyissa meg a részletes jelentést.
- **Breakeven pontok** – Ez a szakasz az összes lehetséges ajánlott központi telepítések és a megtérülés és a hónap, a befektetési megtérülés bekövetkezésekor táblázatot jelenít meg. Kattintson a **nagyítás** nyissa meg a részletes jelentést.

### <a name="ec2-reservations-over-time-report"></a>Időalapú foglalások EC2-jelentés

A foglalások EC2-é az időalapú jelentés a megvásárolt fenntartott EC2-példányok használatát az állapotát követi nyomon. Beállíthatja a jelentés a feloldás óra, nap vagy hét.

A jelentés használja:

- Vásárolt használt és nem használt foglalások jelennek meg.
- Jelenítse meg a megoldás a fenntartott példányok használatának óránként megtekintéséhez óra.

### <a name="savings-over-time-report"></a>Megtakarítás időalapú jelentés

A megtakarítások időalapú jelentés segítségével megtekintheti a fenntartott példányok, valamint a helyszíni példányok használatával elért megtakarítások. A jelentésben a megtérülési RÁTÁRA, a fenntartott példányok vásárlási eredő idővel érhető el.

Szeretné megtekinteni a fenntartott példányok a megtakarítás mértéke, csoport szerint **díjszabási modell** válassza **foglalás**. A fenntartott példányok megtakarítás érhető el, hogy egy adott partner vagy példánytípus megtekintéséhez adja hozzá a megfelelő csoportosítási és a szűrő a fiók vagy a példány típusát.

Tekintse meg a helyszíni példányát használja a megtakarítás mértéke, a szűrőt a **díjszabási modell** való **helyszíni**. A jelentés az alapértelmezett szűrő, a fenntartott példányok és kihasználatlan példányoknak.

### <a name="rds-ri-buying-recommendations-report"></a>A távoli asztali szolgáltatások a fenntartott példányok vásárlási javaslatok jelentésében

A távoli asztali szolgáltatások a fenntartott példányok vásárlási javaslatok jelentésében a távoli asztali szolgáltatások fenntartott példányok használata helyett az igény szerinti példányok használatát javasolja.

Minden lapon teljes jelentésként nyitja meg. Lapok jelentős szakaszai a következők:

- **A távoli asztali szolgáltatások a fenntartott példányok vásárlásának hatását ismertető** – Ez a szakasz tartalmazza a szimuláció közötti különbség az igény szerinti és a fenntartott példányok. Kattintson a **nagyítás** a teljes a távoli asztali szolgáltatások a fenntartott példányok vásárlásának hatását ismertető jelentés megtekintéséhez, a szűrők már meg van adva, a javaslat. Ez a jelentés lehetővé teszi, hogy az összes lehetséges a fenntartott példányok vásárlási vásárlásának hatását ismertető témakörben talál.  Állítsa be a várt átlagos rendelkezésre állását, és tekintse meg a fenntartott példányok vásárlásával mentése lehetséges.
- **Elemzés mentése** – Ez a szakasz tartalmazza a lehetséges megtakarítás érhető el, és a hónap, a megtakarítások actualized vannak, amikor a Cost Management javaslatokat követve. A tényleges megtakarítások és a százalékérték mentett pirossal ki vannak emelve.

- **A távoli asztali szolgáltatások a fenntartott példányok típusok összehasonlítása** – Ez a szakasz a megtérülési ráta emeli ki, beleértve a vonatkozó beállítások az összes ajánlott üzembe helyezési emeli ki. Ebben a jelentésben az eredmények azt feltételezik, hogy a gép fut-e a 100 %-os üzemidő. Kattintson a **nagyítás** nyissa meg a részletes jelentést a kiválasztott gép.
- **Példányok időalapú** – ebben a szakaszban egy a javaslat, az OnDemand, a fenntartott példányok és a helyszíni társított összes példány áttekintését jeleníti meg. Kattintson a **nagyítás** nyissa meg a részletes jelentést.

- **Breakeven pontok** – Ez a szakasz az összes lehetséges ajánlott központi telepítések és a megtérülés és a hónap, a befektetési megtérülés bekövetkezésekor táblázatot jelenít meg. Kattintson a **nagyítás** nyissa meg a részletes jelentést.

### <a name="rds-reservations-over-time-report"></a>A távoli asztali szolgáltatások foglalások időalapú jelentés

A távoli asztali szolgáltatások foglalás időalapú jelentés segítségével a használt és a fel nem használt foglalások egy bontása megtekintheti a megadott időszakban.

### <a name="reserved-instance-purchase-impact-report"></a>Fenntartott példányok vásárlásának hatását ismertető jelentés

Az EC2-beli fenntartott példányok vásárlásának hatását ismertető jelentés szimulálása a fenntartott példányok költségei és igény szerinti költségek időbeli alakulása teszi lehetővé. Ez segíthet a beszerzési jobb döntéseket hozhat. Állítsa be a szűrőket, például átlagos futtatási ideje, időszak, platform és másokat, hogy tájékozott döntéseket hozhat a fenntartott példányok vásárlási meghatározásakor.

### <a name="cost-effective-sizing-recommendations-report"></a>Költséghatékony méretezési javaslatokat tartalmazó jelentés

A Futtatás költséghatékony méretezési javaslatokat tartalmazó jelentés eredményét mutatja az AWS és az Azure. Az AWS-felhasználók esetén a fenntartott példányok vásárlási kell figyelembe venni, és az eredmények nem tartalmazzák a fenntartott példány futtató gépek. Ez a jelentés azokat az alacsony kihasználtságú érdemes becsléseim-példányok listáját tartalmazza. Az elmúlt 30 nap használatának és teljesítményének adatait ajánlásokat alapul. Az egyes javaslatok becsléseim a jelöltek az indoklást becsléseim, és megtekintheti a részleteket mutató hivatkozást listáját és a teljesítmény-mérőszámokat a példány van. És amikor vonatkozó javaslatok javasolja újabb generációs példánytípusok módosításával.

Példány azonosítóját, amely ebből a jelentésből becsléseim javasolt listája nem tölthető le. A Példányazonosítók letöltéséhez használja az összes méretezési javaslatokat tartalmazó jelentés.

Vegye figyelembe a következő downsizing példa:

Hogy hat m3.xlarge futó példányát tekintve. A Cost Management elemzés azt mutatja, hogy azokat az öt CPU-kihasználtság alacsony. Javasolt csökkenteni őket.

A költségekre gyakorolt hatás a költségekre gyakorolt hatás számítja. Ebben a példában a vonal elem kibontásával láthatja az aktuális egy m3.xlarge példány (Linux/Unix) díjszabása a költségeket az óránkénti egységszám és a egy m3.large példány (Linux/Unix) költségek 0.266 $0.133 $ / óra. Tehát éves költsége, 100 %-ot futtató öt m3.xlarge példányok 11,651 $. Az éves költség, 100 %-ot futtató öt m3.large példányok 5,825 $. A lehetséges megtakarítás $5,825.

A költséghatékony méretezési a indoklások megtekintéséhez kattintson a + bontsa ki a sortételt. A **részletek**:

- A **javaslat indoklása** szakasz jelenít meg a jelenlegi üzemelő példány és ajánlott, hogy becsléseim példányainak számát.
- A **költségekre gyakorolt hatás** szakasz azt határozza meg, lehetséges megtakarítás kiszámítása jelenít meg.
- A **éves szinten lehetséges megtakarításokat** szakasz éves szinten lehetséges megtakarításokat jelenik meg, ha a Cost Management ajánlás szerint downsizing.

### <a name="all-sizing-recommendations-report"></a>Az összes méretezési javaslatokat tartalmazó jelentés

Ez a jelentés azokat az alacsony kihasználtságú érdemes becsléseim-példányok listáját tartalmazza. A javaslatok az elmúlt 30 nap használatának és teljesítményének adatait alapulnak. Az egyes javaslatok megtekintheti a részleteket és a példány teljesítmény-mérőszámait.

Ha AWS lefoglalt példányokat vásárolt, ez a jelentés tartalmazza az összes futó példányát, többek között a fenntartott példányok futtató példányok eredményeket.

Az összes méretezési javaslatok jelentés használja:

- A deduplikációra becsléseim összes példány listája.
- Exportálja a példány nevét és azonosítóját tartalmazó jelentés listáját.

Egy adott példányt a javaslat részleteinek megtekintéséhez kattintson a **+** részleteinek kibontásához. A javaslat részletei szakaszban kínál a javaslat áttekintése.

A **címkék** szakasz a címke kulcsok és értékek listáját jeleníti meg a kijelölt példányhoz. A bal oldali panelen címkék segítségével szűrheti a szakaszt.

A **CPU-kihasználtság** a témakör a Processzor kihasználtsága az elmúlt hónapban, a példány naponta.

Kattintson a diagramra a részletes elemzést, és nyissa meg a példány CPU keresztül idő a jelentés a példányok kibontásának megtekintéséhez.

- Használat **mezők megjelenítése/elrejtése** hozzáadni vagy eltávolítani mezőket: időbélyeg, az átlagos CPU, a minimális CPU, a maximális CPU.
- Használat **dátumtartomány** adjon meg egy dátum vagy dátumtartomány és a egy adott InstanceID részletesen.
- Használat **bővített szűrők** megjelenítése az összes vagy egy adott példány azonosítója
- Kattintson a **nagyítás** nyissa meg a CPU-kihasználtság jelentést

Ha a példány nem lett figyelt 30 napig, hiányos adatok jelenik meg.

A **memória kihasználtsága (GB)** szakasz ismerteti a felhasznált memória. AWS – a felhasználóknak memória metrikák nem érhetők el automatikusan, és hozzá kell adni egy példány AWS keresztül. Az AWS EC2-példányok memória metrikáinak engedélyezheti költségek.

A **memória kihasználtsága (%)** szakasz a használt memória százalékos jelenít meg.

A **bemeneti adatforgalmát** szakasz a kiválasztott példány a hálózati forgalmat, átlagos és maximális, idővel jelenít meg egy pillanatkép. Tekintse meg a dátumot és a maximális forgalom arra az időpontra a sorok fölé. Kattintson a **nagyítás** bemeneti a hálózati forgalom jelentés megnyitásához.

A **hálózati kimenő forgalom** szakasz a hálózati kimenő forgalom a kiválasztott példány pillanatképét jeleníti meg. Tekintse meg a dátumot és a maximális forgalom arra az időpontra a sorok fölé. Kattintson a **nagyítás** nyissa meg a hálózati kimenő forgalom jelentést.

### <a name="instance-metrics-explorer-report"></a>Példány Metrikaböngésző jelentés

A példány Metrikaböngésző jelentés a több felhőre kiterjedő teljesítmény metrikákat példányonként jeleníti meg. Használja a jelentés megtekintéséhez, amelyek vagy kevésbé használt Processzor, memória és hálózati mérőszám küszöbértékén alapuló.

Egy példány a több felhőre kiterjedő teljesítmény megtekintése:

1. A **dátumtartomány**, amelyekhez teljesítményét szeretné megtekinteni a dátumtartományok kijelölésével.
2. A **címkék**, válassza ki a megtekinteni kívánt címkéket.
3. A **szűrők**, válassza ki a jelentésben megjeleníteni kívánt szűrőket.
4. A **bővített szűrők**, módosítsa a jelentés küszöbértékeinek:
    - Átlagos CPU
    - CPU maximális száma
    - Átlagos memória
    - Maximális memória
5. A **bővített szűrők**, kattintson a **megjelenítése** majd válassza ki a példányok megjelenítéséhez.

Egy adott példányt metrikák megtekintése idővel:

- Nyissa meg a példány Metrikaböngésző, jelentést, és kattintson a **+** részletek megtekintéséhez.

### <a name="rds-sizing-recommendations-report"></a>A távoli asztali szolgáltatások méretezési javaslatokat tartalmazó jelentés

A távoli asztali szolgáltatások méretezési javaslatokat tartalmazó jelentés a távoli asztali szolgáltatások méretezésének javaslatokkal optimalizálhatja a felhőbeli használati biztosít. Azt a deduplikációra becsléseim kihasználatlan példányok listáját jeleníti meg. A Cost Management javaslatok az elmúlt 30 napban használatának és teljesítményének adatait alapulnak. Javaslatok fióknév, régió, példánytípus és állapot szerint szűrheti.

### <a name="sizing-threshold-manager-report"></a>Méretezési küszöbérték Manager-jelentés

A Cost Management beépített méretezési javaslatok pontos méretezési javaslatokat biztosít egy összetett algoritmus használatával számítjuk ki. Módosíthatja a küszöbértékek downsizing javaslatokat.

Manuálisan módosíthatja a küszöbérték méretezési javaslatok:

1. A méretezési küszöbértéket kezelő állítsa be az alábbi küszöbértékek, tetszés szerint:
    - Átlagos CPU %
    - Maximális százalékos processzorhasználatról
    - Átlagos memória %-át
    - Maximális memória %-át
3. Kattintson a **alkalmaz** módosítások mentéséhez.
4. Módosítások azonnal érvénybe lépnek az összes javaslatot.

Visszaállítása az alapértelmezett küszöbértékek:

- A méretezési küszöbérték Managerben, kattintson a **alapértelmezett értékek visszaállítása**.

### <a name="compute-instance-types-report"></a>Számítási példányok típusai jelentés

A példányok típusai jelentés használja:

- Példány nézettípusok szolgáltatás, család, API-név és nevét.
- Megtekintheti például a Processzor, a ECU, RAM, és a hálózati.

Használhat **keresési** konkrét elemek kereséséhez.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan kell használnia, beleértve a testreszabása vagy mentéséhez és a jelentések ütemezése, olvassa el a [ismertetése költségjelentéseket](understanding-cost-reports.md).
- Ismerje meg a Cloudyn foglalt irányítópultokon és a saját egyéni irányítópultokat hozhat létre, lásd: hogyan [irányítópultokkal kulcs költség metrikákat tekinthet meg](dashboards.md).
