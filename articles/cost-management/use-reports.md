---
title: A Cloudyn-jelentések használata az Azure-ban |} A Microsoft Docs
description: Ez a cikk ismerteti a Cloudyn-jelentések, amelyek szerepelnek a Cloudyn portál segítségével hatékonyan használja őket célját.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: f838091f4b4cdcb0535f58926a724074ef080463
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74218857"
---
# <a name="reports-available-in-the-cloudyn-portal"></a>A Cloudyn portálon elérhető jelentések

Ez a cikk ismerteti a Cloudyn-jelentések, amelyek szerepelnek a Cloudyn portálon célját. Azt is ismerteti, hogyan hatékonyan használhatja a jelentéseket. A legtöbb jelentések intuitív, és rendelkezik egy egységes megjelenés és működés. A legtöbb művelet elvégezhető egy jelentésben, műveletek végezhetők az egyéb jelentésekben. A Cloudyn-jelentések használatáról, beleértve a jelentések testreszabásának és mentésének módját, valamint a jelentések ütemtervének áttekintését lásd: a [Cost-jelentések ismertetése](understanding-cost-reports.md).

Az Azure Cost Management hasonló funkciókat kínál, mint a Cloudyn. Az Azure Cost Management egy natív Azure költségkezelő megoldás. Segít kezelni a költségvetéseket, exportálni az adatokat, valamint áttekinteni és végrehajtani az optimalizálási javaslatokat pénzmegtakarítás céljából. További információ: [Azure Cost Management](overview-cost-mgt.md).

## <a name="report-types"></a>Jelentéstípusok

A Cloudyn jelentésekben három típusa van:

- Többlet idő jelentéseket. Ha például az időalapú költségeket tartalmazó jelentést. Többlet idő a jelentések egy idősorozat-adatok megjelenítése egy kiválasztott időszakban előre meghatározott megoldást, és heti felbontása megjelenítése az utolsó két hónapig. Csoportosítás és szűrés segítségével különböző adatpontok nagyítás.
  - Többlet idő jelentések segítségével megtekinthessék a szalagfelhasználási tendenciákat, és a csúcsok és a rendellenességek észlelését.
- Problémaelemző jelentések. Például a Cost Analysis jelentés. Ezek a jelentések összesített adatok megjelenítése a időszakban határozza meg, és a csoportosítási és az adatok szűrésének engedélyezése.
  - Elemzési jelentés segítségével megtekintheti az adatforgalmi csúcsokhoz és döntse el, anomáliadetektálási kiváltó okokat, és jelenítse meg egy részletes break ki az adatokat.
- Táblázatos jelentéseket. Megtekintheti a jelentést egy táblát, de egyes jelentések csak táblaként tekinthetők meg. Ezek a jelentések adja meg a részletes elemek listája.
  - Az ajánlások táblázatos jelentések – nincsenek javaslatok egyetlen vizualizációja sincs. Azonban jelenítheti meg az ajánlás eredményeket. Ha például megtakarítások idővel.
  - Táblázatos jelentések hasznosak, listák, műveletek, illetve az adatok exportálása további feldolgozás céljából. Ha például a jóváírási jelentések.

A _Cost-jelentések_ a _tényleges_ vagy az elszámolt költségeket mutatják.

Tényleges költségek jelentéseiben jelennek meg a kifizetések a kijelölt időszakban. Például minden egyszeri díjak, mint például (RI). a fenntartott példányok vásárlása jelennek meg a tényleges költségek jelentéseiben, költség kiugrások.

Amortizált költségek jelentései egyszeri díjak, amelyekre érvényes keresztül terjednek. Például egyszeri díjak fenntartott példány vásárlására oszlik el a Foglalás időtartama és ugrásszerű nem jelennek meg. Az amortizált nézet az egyetlen módja igaz trendeket és költség leképezések.

Bizonyos esetekben a törlesztési külön jelentésként jelennek meg. Ilyenek például a költségek elemzése és az amortizált költségek elemzése jelentéseket. Más esetekben a törlesztési egy olyan jelentés szabályzat, például a Költséglefoglalási és költségelemzés jelentéseket.

Bármely jelentés rendszeres kézbesítésre ütemezheti. Költségek jelentései lehetővé teszik egy küszöbértéket beállítását úgy is hasznos, ha a riasztásokat.

## <a name="cost-analysis-vs-cost-allocation"></a>A költséglefoglalási és költségelemzés

A _Cost Analysis_ -jelentések a felhőalapú szolgáltatók számlázási adatait jelenítik meg. A jelentések segítségével, csoport és a számlázási fájlból részletezett különböző adatok szegmensek részletesen. A jelentések lehetővé a pontos költségeket a felhő gyártója által biztosított nyers számlázási adatok.

Néhány _Cost Analysis_ -jelentés nem csoportosítja a költségeket az erőforrás-címkék alapján. A és a címkén alapuló számlázási adatok csak akkor jelennek meg a jelentésekben, ha a költségek lefoglalása után a Cost ( [költség) Kiosztásával 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

A Cost _foglalási_ jelentései akkor érhetők el, ha az 360-es [Cost kiosztásával](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs)létrehoz egy Cost-modellt. A Cloudyn feldolgozza a költségeket és a számlázási adatokat, és _megfelel_ az adatoknak a Felhőbeli fiókok használati és címkézési adatainak. Az adatok megfelelő, a Cloudyn a használati adatok hozzáférésre van szüksége. Ha vannak olyan fiókok, amelyek nem rendelkeznek hitelesítő adatokkal, akkor a rendszer _Kategorizálatlan erőforrásként_címkézi őket.

## <a name="dashboards"></a>Irányítópultok

Irányítópultok Cloudy a jelentések magas szintű áttekintést biztosítanak. Az irányítópultok widgetek épülnek fel, és minden widget lényegében egy jelentés miniatűr. Amikor [testreszabja a jelentéseket](understanding-cost-reports.md#save-and-schedule-reports), azokat saját jelentésekba menti, és hozzáadja őket az irányítópulthoz. Az irányítópultokkal kapcsolatos további információkért tekintse [meg a Key Cost-mérőszámok irányítópultokkal történő megtekintését](dashboards.md)ismertető témakört.

## <a name="budget-information-in-reports"></a>A jelentésekben költségvetési információk

Cloudyn-jelentések megjelenítése a költségvetési információk után manuálisan létrehozott egy. Így a jelentések nem jelennek meg a költségvetési információk költségvetési létrehozásáig. További információ: költségkeret- [kezelési beállítások](#budget-management-settings).

## <a name="reports-and-reporting-features"></a>Jelentések és a jelentéskészítési szolgáltatások

A Cloudyn a következő jelentéseket és jelentéskészítési szolgáltatásokat tartalmazza.

### <a name="cost-navigator-report"></a>A kezelő jelentést költség

A költségeket a kezelő jelentést gyors módja egy irányítópult-nézet használata a számlázási fogyasztás megtekintéséhez. Szűrők és a szervezet költségeinek összesített nézetét mutatja azonnal az alapszintű nézetek egy része van. Költségek dátum szerint jelennek meg. A jelentés kezdeti képet a költségeket jelent, mert nincs olyan rugalmas, vagy, átfogó, mint sok más jelentések vagy egyéni irányítópultokat, Ön hozza létre.

Alapértelmezés szerint a jelentés főbb nézetek megjelenítése:

- Egy munkahét sáv diagram nézet megjelenítése időalapú költségek. A dátumtartomány megváltoztatásával módosíthatja a dátumtartomány **oszlopdiagram-** diagramját.
- Szolgáltatás használata a kördiagram kiadásai.
- Erőforrás kategorizálási címkék, kördiagram alapján.
- Költség költségelemek kördiagram szerint.
- Költség összesen dátum szerint a lista nézetben.

### <a name="cost-analysis-report"></a>Költségelemzési jelentés

A Költségelemzési jelentést egy számítási, költséghelyi visszacsatolási és költséghelyi elszámolási, a házirend alapján. Gyűjti a kiválasztott időtartam alatt, a költség az összes lefoglalási szabályokat alkalmazását követően a felhőbeli fogyasztást. Például, számítja ki a költségeket címkék alapján, ismét kiosztja a címkézetlen erőforrások költségeit és szükség esetén osztja ki a fenntartott példányok kihasználtságát.

Az 360-es [Cost-foglalásban](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) beállított szabályzatok a Cost Analysis jelentésben szerepelnek, és az eredmények a felhőalapú gyártó nyers adataiból származó információkkal kombinálhatók.

Hogyan számítják az ebben a jelentésben? A Cloudyn szolgáltatás biztosítja az egyes csatolt fiókok integritásának megőrzését a _fiók affinitásának_alkalmazásával. Affinitás biztosítja, hogy egy fiókot, amely nem használja egy adott szolgáltatás nem rendelkezik a szolgáltatás számára lefoglalt költségek. A költségek abban, hogy a fiók továbbra is az adott fiókban, és nem számítja ki a foglalási szabályzatok által elhatárolt. Előfordulhat például, hogy öt összekapcsolt fiókok. Ha csak a tárolási szolgáltatások használatához három a majd csak a tárolási szolgáltatások költsége van lefoglalva a három fiókot a címkék között.

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

Idő a jelentés felett költségeit a költséglefoglalási eredmények a time series jeleníti meg. Lehetővé teszi, hogy látni fogja a trendeket, és a szabálytalanságok észleléséhez a központi telepítésben. Lényegében egy meghatározott időszakban elosztott költségeit jeleníti meg. A jelentés tartalmazza a fő közreműködőkkel, beleértve a folyamatos költségeket és a egy kiválasztott időszakban fordított fenntartott példány egyszeri díjak. Ebben a jelentésben a 360-es [Cost-foglalásban](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) beállított szabályzatok használatosak.

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

A jelentés segítségével az előfizetések költségadatok megtekintéséhez. A jelentésben az Azure-előfizetések a **fiók neve** és a **Fiók száma mezőben**jelennek meg. A **társított fiókok** AWS-előfizetéseket mutatnak. Az előfizetések költségeinek megtekintéséhez az egyes fiókok részletezése csoportban **válassza**ki az előfizetés típusát.

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

A számlázási fájlban egyszeri díjak jellemző, ha a szolgáltatás használatalapú kezdő és záró dátum (timestamp) egyenlő értékek találhatók. A Cloudyn szolgáltatás majd felismeri, hogy a rendszer amortizált például egyszeri díjak. Egyéb fogyasztásalapú szolgáltatások igény szerinti használat költségei nem amortizált vannak.

Amortizált költségek jelentései a következők:

- Amortizált költségek elemzése
- Amortizált költségek időbeli alakulása

### <a name="cost-analysis-report"></a>Költségelemzési jelentés

A Költségelemzési jelentést biztosít a felhőbeli fogyasztást és a egy kiválasztott időszakban költségeit. A Cost Analysis jelentésben a 360-es [foglalásban](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) beállított szabályzatok használatosak.

Hogyan Cloudyn Ez a jelentés kiszámítása?

A Cloudyn biztosítja, hogy a foglalás megőrizze az egyes csatolt fiókok integritását a _fiók affinitásának_alkalmazásával. Affinitás biztosítja, hogy egy fiókot, amely egy adott szolgáltatás nem használja még nem rendelkezik a szolgáltatás számára lefoglalt költségek. A költségek azt, hogy a fiók továbbra is az adott fiókban a foglalási szabályzatok nem számítja ki. Előfordulhat például, hogy öt összekapcsolt fiókok. Ha csak a tárolási szolgáltatások használatához három a majd csak a tárolási szolgáltatások költsége van lefoglalva a három fiókot a címkék között.

A költségelemzés jelentésben használja:

- Jeleníti meg a teljes telepítés a egy megadott időkereten összesített nézetet.
- A költségmodell létrehozott szabályzatok alapján címke kategóriák nézet költségeket.

### <a name="cost-over-time-report"></a>Időalapú költségek jelentése

Az időalapú költségeket tartalmazó jelentést a költségkeret-beállítási idővel felismerése érdekében trendek és a szabálytalanságok figyelje meg a központi telepítésben jeleníti meg. Lényegében egy meghatározott időszakban elosztott költségeit jeleníti meg. A jelentés tartalmazza a fő közreműködőkkel, beleértve a folyamatos költségeket és a egy kiválasztott időszakban fordított fenntartott példány egyszeri díjak. Ebben a jelentésben a 360-es [Cost-foglalásban](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) beállított szabályzatok használatosak.

Az időalapú költségeket tartalmazó jelentés használja:

- Módosítások megtekintéséhez az idő függvényében, és milyen hatású módosítja egy napig (vagy dátumtartomány) a következő.
- Költségek elemzése egy adott példányt az idő függvényében.
- Megismerheti miért volt egy adott példányt egy költség növelését.

### <a name="custom-charges-report"></a>Egyéni díjak jelentés

Vállalati és a CSP-felhasználók gyakran tapasztalhatja magukat a hozzáadott szolgáltatásokat biztosító külső vagy belső ügyfeleiknek, a saját felhő erőforrás-használat mellett. Megadhat új szolgáltatások és az esetleges kedvezményeket, az ügyfél számlázási vagy költséghelyi elszámolási jelentések egyéni sorként hozzáadott egyéni kell fizetni.

Egyéni szolgáltatási díjak szolgáltatásokat, amelyek normál esetben nem jelennek meg, szerepel a számlán jelenik meg. Az Ön által létrehozott egyéni díjak ezután költségjelentésben megjelennek.

Az *Egyéni díjak nem egyéni díjszabással*rendelkeznek. Egyéni díjak listája nem jelenik meg a különböző díjszabás, akkor előfordulhat, hogy lehet díjszabási. Az AWS díjfizetést például jelennek meg ugyanúgy, mint azok díjat számítunk fel.

Egyéni díj létrehozása:

1. Az **Egyéni költségek**területen kattintson az **új hozzáadása**lehetőségre. Megjelenik az _új egyéni díj hozzáadása_ párbeszédpanel.
2. A **szolgáltató neve**mezőben adja meg a szolgáltató nevét.
3. A **szolgáltatás neve**mezőben adja meg a szolgáltatás típusát.
4. A **Leírás**mezőben adja meg az egyéni díj leírását.
5. A **típus**mezőben adja meg a Select **százalék** elemet, majd a szolgáltatások legördülő menüben válassza ki azokat a szolgáltatásokat, amelyeket egyéni díjakként szeretne szerepeltetni a Cost-jelentésekben.
6. A **fizetés**lapon válassza ki, hogy a díj egyszeri díj vagy ismétlődő díj. Egy ismétlődő díjat kell fizetni a díjat, ha Amortized válassza, ha azt szeretné, hogy a díj amortizált kell, és válassza ki a hónapok száma.
7. Ha egy egyszeri díj van kiválasztva, **a Date (** **tényleges dátum**) mezőben adja meg a díj kifizetésének dátumát. Ha díjból ismétlődő be van jelölve, adja meg a dátumtartományt, beleértve a kezdő dátum és záró dátumát a díjat.
8. Az **entitások fában**válassza ki azokat az entitásokat, amelyekre alkalmazni szeretné a díjat, majd **válassza a be lehetőséget.**

_Ha a díjakat egy entitáshoz rendeli, a felhasználók nem módosíthatják azokat. A rendszergazda által egy szülő entitáshoz hozzáadott díjak csak olvashatók._

Egyéni díjak megtekintése:

Egyéni díjak költségjelentésben megjelennek. Nyissa meg például a tényleges Cost Analysis jelentést, majd a **kiterjesztett szűrők**területen válassza az **önálló**lehetőséget. Ezután szűrje az **Egyéni díjak**megjelenítéséhez.

### <a name="cost-allocation-360"></a>A Cost Allocation 360

Cost Allocation 360 használatával egyéni foglalási modelljeinek költségek hozzárendelése felhasznált felhőalapú erőforrásokhoz. Számos jelentést az egyéni modellek létrehozott egyéni modellek adatainak megjelenítése. És egyes jelentések csak adatok megjelenítése egyéni költségmodell a költséglefoglalási a létrehozása után.

Az egyéni Cost models létrehozásával kapcsolatos további információkért lásd [: oktatóanyag: költségek kezelése a Cloudyn használatával](tutorial-manage-costs.md).

### <a name="cost-vs-budget-over-time-report"></a>Költség és költségkeret időbeli alakulása jelentés

A költség és a költségkeret időbeli alakulása jelentés lehetővé teszi, hogy összehasonlítsa a fődíjas közreműködőket a költségvetésével szemben. A hozzárendelt költségvetés jelenik meg a jelentésben, hogy a költségvetés (felett vagy alatt/par) használat idővel tekintheti meg. Megjelenítése/elrejtése mezővel, amely a jelentés tetején választhat nézet költség, a költségvetés, a összesített költségeit és a teljes fennmaradó.

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

1. A költségvetés kezelése lap **entitások**területén válassza ki azt az entitást, amelyben létre kívánja hozni a költségvetést.
2. A költségvetési évben válassza ki az év, ahol szeretné létrehozni a költségvetést.
3. Minden hónapban állítsa be a költségvetést, majd kattintson a **Save (Mentés**) gombra.

Az éves költségvetés-fájl importálása:

1. A **műveletek**területen válassza az **Exportálás** lehetőséget egy üres CSV-sablon letöltéséhez, amelyet a költségvetés alapjaként kíván használni.
2. Töltse ki a költségvetés és a CSV-fájlt, és mentse helyileg.
3. A **műveletek**területen válassza az **Importálás**lehetőséget.
4. Válassza ki a mentett fájlt, majd kattintson **az OK**gombra.

Ha a kész költségvetést CSV-fájlként szeretné exportálni, a **műveletek**területen válassza az **Exportálás** lehetőséget a fájl letöltéséhez.

Ha elkészült, a költségkeret a Cost Analysis jelentéseiben és a költség és a költségvetés időbeli változása jelentésben jelenik meg. Költségvetés küszöbértékek alapján jelentéseket is ütemezheti.

### <a name="azure-resource-explorer-report"></a>Az Azure erőforrás-kezelő jelentés

Az Azure Resource Explorer jelentés az Azure-erőforrások rendelkezésre a Cloudyn tömeges listáját jeleníti meg. A hatékony használatot a jelentést, az Azure-fiókokhoz kell bővítette a metrikák engedélyezve. Kiterjesztett metrikák Cloudyn-hozzáférést biztosít az Azure-beli virtuális gépek. További információ: [bővített mérőszámok hozzáadása az Azure Virtual Machines szolgáltatáshoz](azure-vm-extended-metrics.md).

### <a name="azure-resources-over-time-report"></a>Az Azure erőforrások időalapú jelentés

Az Azure erőforrások időalapú jelentés egy adott időszak alatt futó összes erőforrás bontását mutatja. A hatékony használatot a jelentést, az Azure-fiókokhoz kell bővítette a metrikák engedélyezve. Kiterjesztett metrikák Cloudyn-hozzáférést biztosít az Azure-beli virtuális gépek. További információ: [bővített mérőszámok hozzáadása az Azure Virtual Machines szolgáltatáshoz](azure-vm-extended-metrics.md).

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

Ez a jelentés a [Cost allokációs 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) -szabályzatokhoz van társítva, így az eredmények a kijelölt címkézés és szabályzatok alapján jelennek meg. Ha nem rendelkezik egy létrehozott házirend, majd eredmények nem jelennek meg.

### <a name="compute-power-average-cost-over-time-report"></a>Számítási teljesítmény átlagos időalapú költségek jelentése

Több mint csak az egyes futó gépek költsége megtekintéséhez használhatja a számítási teljesítmény átlagos időalapú költségeket tartalmazó jelentést. A jelentésben a átlagköltség óra. példány, a processzor-üzemóra, a számítási üzemideje (óra) és a GB RAM-MAL óra. A jelentés a hatékonyság érdekében az üzembe helyezés betekintést nyújt.

Ez a jelentés a [Cost allokációs 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) -szabályzatokhoz van csatolva, így az eredmények a megadott címkézés és szabályzatok alapján jelennek meg. Ha nem rendelkezik egy létrehozott házirend, majd eredmények nem jelennek meg.

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

A Cloudyn hasznos üzemidejének az egyes gépek kiértékeli az elmúlt 30 napra vonatkozóan. A Cloudyn azt javasolja, hogy a fenntartott példányok vásárlása, ha a gép futtatásához a Hónapjánál az aktuális szinten üzemidő helyett.

A jelentés megjeleníti a javaslatokat, a legtöbb pénzt takaríthat meg az évben indoklásának a következőt tartalmazza. A javaslatok javasolni, és cserélje le a fenntartott példányok igény szerinti példányok. A fenntartott példányok közvetlenül a jelentés vásárolhat.

Minden lapon teljes jelentésként nyitja meg. Lapok jelentős szakaszai a következők:

- **EC2 ri – vásárlás hatása** – ez a szakasz az igény szerinti és fenntartott példányok közötti különbség szimulációját ismerteti. Kattintson a **Nagyítás**elemre, hogy megtekintse a javaslathoz már definiált szűrőket tartalmazó teljes EC2 ri vásárlási hatás jelentését. Ez a jelentés tartalmazza az összes lehetséges a fenntartott példányok vásárlási beszerzési hatását. Módosíthatja a várható átlagos üzemidő megtekintéséhez a mentése való EC2 fenntartott példány megvásárlása lehetséges.

- **Elemzések mentése** – ez a szakasz az elért lehetséges megtakarításokat és a megtakarítások hónapját adja meg a Cloudyn ajánlásainak követése során. A tényleges megtakarítások és a százalékérték mentett pirossal ki vannak emelve.

- **EC2 ri-típus összehasonlítása** – ez a szakasz a Cloudyn javasolt központi telepítésének ROI-kiemeléseit emeli ki, beleértve az összes vonatkozó beállítást is. Ebben a jelentésben az eredmények azt feltételezik, hogy a gép fut-e a 100 %-os üzemidő. Kattintson a **Nagyítás** gombra a részletes jelentés megnyitásához.

- **Példányok időbeli** megoszlása – ez a szakasz a javaslathoz, a OnDemand, a fenntartott példányokhoz és a helyhez társított összes példány részletezését jeleníti meg. Kattintson a **Nagyítás** gombra a részletes jelentés megnyitásához.
- **Ráfizetéses pontok** – ez a szakasz az összes lehetséges ajánlott telepítés és a ROI és a hónap közötti táblázatot jeleníti meg a ROI bekövetkeztekor. Kattintson a **Nagyítás** gombra a részletes jelentés megnyitásához.

### <a name="ec2-reservations-over-time-report"></a>Időalapú foglalások EC2-jelentés

A foglalások EC2-é az időalapú jelentés a megvásárolt fenntartott EC2-példányok használatát az állapotát követi nyomon. Beállíthatja a jelentés a feloldás óra, nap vagy hét.

A jelentés használja:

- Vásárolt használt és nem használt foglalások jelennek meg.
- Jelenítse meg a megoldás a fenntartott példányok használatának óránként megtekintéséhez óra.

### <a name="savings-over-time-report"></a>Megtakarítás időalapú jelentés

A megtakarítások időalapú jelentés segítségével megtekintheti a fenntartott példányok, valamint a helyszíni példányok használatával elért megtakarítások. A jelentésben a megtérülési RÁTÁRA, a fenntartott példányok vásárlási eredő idővel érhető el.

A RIs-megtakarítás megtekintéséhez csoportosítsa az eredményeket az **Ár modell** alapján, és válassza a **foglalás**lehetőséget. A fenntartott példányok megtakarítás érhető el, hogy egy adott partner vagy példánytípus megtekintéséhez adja hozzá a megfelelő csoportosítási és a szűrő a fiók vagy a példány típusát.

Ha meg szeretné tekinteni a direktszínes példányok használatának megtakarításait, szűrje az **árat** a **helyszínen**. A jelentés az alapértelmezett szűrő, a fenntartott példányok és kihasználatlan példányoknak.

### <a name="rds-ri-buying-recommendations-report"></a>A távoli asztali szolgáltatások a fenntartott példányok vásárlási javaslatok jelentésében

A távoli asztali szolgáltatások a fenntartott példányok vásárlási javaslatok jelentésében a távoli asztali szolgáltatások fenntartott példányok használata helyett az igény szerinti példányok használatát javasolja.

Minden lapon teljes jelentésként nyitja meg. Lapok jelentős szakaszai a következők:

- **RDS ri – vásárlási hatás** – ez a szakasz az igény szerinti és fenntartott példányok közötti különbség szimulációját tartalmazza. Kattintson a nagyítás lehetőségre, hogy a teljes RDS RI-vásárlási Impact **-** jelentés a javaslathoz már meghatározott szűrőkkel legyen látható. Ez a jelentés lehetővé teszi, hogy az összes lehetséges a fenntartott példányok vásárlási vásárlásának hatását ismertető témakörben talál.  Állítsa be a várt átlagos rendelkezésre állását, és tekintse meg a fenntartott példányok vásárlásával mentése lehetséges.
- **Elemzés mentése** – ez a szakasz az elért lehetséges megtakarításokat és a megtakarítások hónapját adja meg a Cloudyn ajánlásainak követése során. A tényleges megtakarítások és a százalékérték mentett pirossal ki vannak emelve.

- **RDS ri-típusok összehasonlítása** – ez a szakasz a javasolt központi telepítés ROI-kiemeléseit emeli ki, beleértve az összes vonatkozó beállítást is. Ebben a jelentésben az eredmények azt feltételezik, hogy a gép fut-e a 100 %-os üzemidő. Kattintson a **Nagyítás** gombra a kiválasztott gép részletes jelentésének megnyitásához.
- **Példányok az idő múlásával** – ez a szakasz a javaslathoz, a OnDemand, a fenntartott példányokhoz és a spothoz társított összes példány részletezését jeleníti meg. Kattintson a **Nagyítás** gombra a részletes jelentés megnyitásához.

- **Ráfizetéses-pontok** – ebben a szakaszban az összes lehetséges ajánlott telepítés és a ROI és a havi megtérülés táblázata látható. Kattintson a **Nagyítás** gombra a részletes jelentés megnyitásához.

### <a name="rds-reservations-over-time-report"></a>A távoli asztali szolgáltatások foglalások időalapú jelentés

A távoli asztali szolgáltatások foglalás időalapú jelentés segítségével a használt és a fel nem használt foglalások egy bontása megtekintheti a megadott időszakban.

### <a name="reserved-instance-purchase-impact-report"></a>Fenntartott példányok vásárlásának hatását ismertető jelentés

Az EC2-beli fenntartott példányok vásárlásának hatását ismertető jelentés szimulálása a fenntartott példányok költségei és igény szerinti költségek időbeli alakulása teszi lehetővé. Ez segíthet a beszerzési jobb döntéseket hozhat. Állítsa be a szűrőket, például átlagos futtatási ideje, időszak, platform és másokat, hogy tájékozott döntéseket hozhat a fenntartott példányok vásárlási meghatározásakor.

### <a name="cost-effective-sizing-recommendations-report"></a>Költséghatékony méretezési javaslatokat tartalmazó jelentés

A Futtatás költséghatékony méretezési javaslatokat tartalmazó jelentés eredményét mutatja az AWS és az Azure. Az AWS-felhasználók esetén a fenntartott példányok vásárlási kell figyelembe venni, és az eredmények nem tartalmazzák a fenntartott példány futtató gépek. Ez a jelentés azokat az alacsony kihasználtságú érdemes becsléseim-példányok listáját tartalmazza. Az elmúlt 30 nap használatának és teljesítményének adatait ajánlásokat alapul. Az egyes javaslatok becsléseim a jelöltek az indoklást becsléseim, és megtekintheti a részleteket mutató hivatkozást listáját és a teljesítmény-mérőszámokat a példány van. És amikor vonatkozó javaslatok javasolja újabb generációs példánytípusok módosításával.

Példány azonosítóját, amely ebből a jelentésből becsléseim javasolt listája nem tölthető le. A Példányazonosítók letöltéséhez használja az összes méretezési javaslatokat tartalmazó jelentés.

Vegye figyelembe a következő downsizing példa:

Hogy hat m3.xlarge futó példányát tekintve. A Cloudyn elemzés azt mutatja, hogy azokat az öt CPU-kihasználtság alacsony. Javasolt csökkenteni őket.

A költségekre gyakorolt hatás a költségekre gyakorolt hatás számítja. Ebben a példában a vonal elem kibontásával láthatja az aktuális egy m3.xlarge példány (Linux/Unix) díjszabása a költségeket az óránkénti egységszám és a egy m3.large példány (Linux/Unix) költségek 0.266 $0.133 $ / óra. Tehát éves költsége, 100 %-ot futtató öt m3.xlarge példányok 11,651 $. Az éves költség, 100 %-ot futtató öt m3.large példányok 5,825 $. A lehetséges megtakarítás $5,825.

A költséghatékony méretezési a indoklások megtekintéséhez kattintson a + bontsa ki a sortételt. **Részletek**:

- Az **ajánlás indoklása** szakasz a jelenlegi központi telepítést és a javasolt példányok számát jeleníti meg.
- A **Cost Impact** szakasz a lehetséges megtakarítások meghatározására szolgáló számítást jeleníti meg.
- A **lehetséges éves megtakarítások** szakasz a Cloudyn javaslata során felhasználható éves megtakarításokat jeleníti meg.

### <a name="all-sizing-recommendations-report"></a>Az összes méretezési javaslatokat tartalmazó jelentés

Ez a jelentés azokat az alacsony kihasználtságú érdemes becsléseim-példányok listáját tartalmazza. A javaslatok az elmúlt 30 nap használatának és teljesítményének adatait alapulnak. Az egyes javaslatok megtekintheti a részleteket és a példány teljesítmény-mérőszámait.

Ha AWS lefoglalt példányokat vásárolt, ez a jelentés tartalmazza az összes futó példányát, többek között a fenntartott példányok futtató példányok eredményeket.

Az összes méretezési javaslatok jelentés használja:

- A deduplikációra becsléseim összes példány listája.
- Exportálja a példány nevét és azonosítóját tartalmazó jelentés listáját.

Egy adott példányra vonatkozó javaslat részleteinek megtekintéséhez kattintson a **+** elemre a részletek kibontásához. A javaslat részletei szakaszban kínál a javaslat áttekintése.

A **címkék** szakasz a kijelölt példányhoz tartozó címke kulcsainak és értékeinek listáját tartalmazza. A bal oldali panelen címkék segítségével szűrheti a szakaszt.

A **CPU-kihasználtság** szakasz a példány CPU-kihasználtságát adja meg az elmúlt hónapban, naponta.

Kattintson a diagramra a részletes elemzést, és nyissa meg a példány CPU keresztül idő a jelentés a példányok kibontásának megtekintéséhez.

- Mezők **megjelenítése/elrejtése mezők** hozzáadásához és eltávolításához használja a következő mezőket: timestamp, AVG CPU, minimális CPU, Max CPU.
- Dátumtartomány **használata dátum** -és dátumtartomány megadásához, valamint részletezés egy adott InstanceId.
- **Kibővített szűrők** használata az összes vagy egy adott példány azonosítójának megjelenítéséhez
- A CPU-kihasználtsági jelentés megnyitásához kattintson a **Nagyítás** gombra.

Ha a példány nem lett figyelt 30 napig, hiányos adatok jelenik meg.

A **memória kihasználtsága (GB)** szakasz a felhasznált memóriával kapcsolatos információkat tartalmazza. AWS – a felhasználóknak memória metrikák nem érhetők el automatikusan, és hozzá kell adni egy példány AWS keresztül. Az AWS EC2-példányok memória metrikáinak engedélyezheti költségek.

A **memória kihasználtsága (%)** szakasz a felhasznált memória százalékos arányát jeleníti meg.

A **hálózati bemeneti forgalom** szakasz a kiválasztott példány hálózati forgalmának, átlagának és maximális számának időpontját jeleníti meg. Tekintse meg a dátumot és a maximális forgalom arra az időpontra a sorok fölé. Kattintson a **Nagyítás** gombra a hálózati bemeneti forgalom jelentésének megnyitásához.

A **hálózati kimeneti forgalom** szakasz a kiválasztott példány hálózati kimeneti forgalmának pillanatképét jeleníti meg. Tekintse meg a dátumot és a maximális forgalom arra az időpontra a sorok fölé. Kattintson a **Nagyítás** gombra a hálózati kimeneti forgalom jelentésének megnyitásához.

### <a name="instance-metrics-explorer-report"></a>Példány Metrikaböngésző jelentés

A példány Metrikaböngésző jelentés a több felhőre kiterjedő teljesítmény metrikákat példányonként jeleníti meg. Használja a jelentés megtekintéséhez, amelyek vagy kevésbé használt Processzor, memória és hálózati mérőszám küszöbértékén alapuló.

Egy példány a több felhőre kiterjedő teljesítmény megtekintése:

1. A **dátumtartomány**mezőben válasszon ki egy dátumtartományt, amelynek a teljesítményét meg szeretné tekinteni.
2. A **címkék**területen válassza ki a megtekinteni kívánt címkéket.
3. A **szűrők**területen válassza ki a jelentésben megjeleníteni kívánt szűrőket.
4. A **kiterjesztett szűrők**területen módosítsa a jelentés küszöbértékeit a következőre:
    - Átlagos CPU
    - CPU maximális száma
    - Átlagos memória
    - Maximális memória
5. A **kiterjesztett szűrők** **területen kattintson a Megjelenítés** elemre, majd válassza ki a megjelenítendő példányok típusát.

Egy adott példányt metrikák megtekintése idővel:

- Nyissa meg a példány Metrikaböngésző jelentést, és kattintson az **+** elemre a részletek megtekintéséhez.

### <a name="rds-sizing-recommendations-report"></a>A távoli asztali szolgáltatások méretezési javaslatokat tartalmazó jelentés

A távoli asztali szolgáltatások méretezési javaslatokat tartalmazó jelentés a távoli asztali szolgáltatások méretezésének javaslatokkal optimalizálhatja a felhőbeli használati biztosít. Azt a deduplikációra becsléseim kihasználatlan példányok listáját jeleníti meg. A Cloudyn, ajánlások az elmúlt 30 napban használatának és teljesítményének adatait alapulnak. Javaslatok fióknév, régió, példánytípus és állapot szerint szűrheti.

### <a name="sizing-threshold-manager-report"></a>Méretezési küszöbérték Manager-jelentés

A Cloudyn beépített méretezési javaslatok pontos méretezési javaslatokat biztosít egy összetett algoritmus használatával számítjuk ki. Módosíthatja a küszöbértékek downsizing javaslatokat.

Manuálisan módosíthatja a küszöbérték méretezési javaslatok:

1. A méretezési küszöbértéket kezelő állítsa be az alábbi küszöbértékek, tetszés szerint:
    - Átlagos CPU %
    - Maximális százalékos processzorhasználatról
    - Átlagos memória %-át
    - Maximális memória %-át
3. A módosítások mentéséhez kattintson az **alkalmaz** gombra.
4. Módosítások azonnal érvénybe lépnek az összes javaslatot.

Visszaállítása az alapértelmezett küszöbértékek:

- A méretezési küszöb kezelőjében kattintson az **Alapértelmezések visszaállítása**elemre.

### <a name="compute-instance-types-report"></a>Számítási példányok típusai jelentés

A példányok típusai jelentés használja:

- Példány nézettípusok szolgáltatás, család, API-név és nevét.
- Megtekintheti például a Processzor, a ECU, RAM, és a hálózati.

A **kereséssel** konkrét sorok is megkereshetők.

## <a name="next-steps"></a>Következő lépések

- További információ a jelentések használatáról, többek között a jelentések testreszabásáról és mentéséről, valamint az ütemezett jelentésekről: a [Cost-jelentések ismertetése](understanding-cost-reports.md).
- Ismerkedjen meg a Cloudyn-ben található irányítópultokkal, és arról, hogyan hozhat létre saját egyéni irányítópultokat, lásd: [Key Cost-mérőszámok megtekintése az irányítópultokkal](dashboards.md).
