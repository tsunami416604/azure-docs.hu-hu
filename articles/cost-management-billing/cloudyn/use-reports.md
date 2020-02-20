---
title: Cloudyn-jelentések használata az Azure-ban | Microsoft Docs
description: Ez a cikk a Cloudyn portálon elérhető Cloudyn-jelentések célját ismerteti a hatékony használat érdekében.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ms.openlocfilehash: b3563e08963b0b32542dcbece2529c350981f557
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200010"
---
# <a name="reports-available-in-the-cloudyn-portal"></a>A Cloudyn portálon elérhető jelentések

Ez a cikk a Cloudyn portálon elérhető Cloudyn-jelentések célját ismerteti. A cikk a jelentések hatékony használatát is ismerteti. A legtöbb jelentés intuitívan használható, valamint egységes megjelenéssel és működéssel rendelkezik. Az egy adott jelentésben elvégezhető műveletek nagy részét más jelentésekben is elvégezheti. A Cloudyn-jelentések áttekintéséért és használatáért (beleértve a jelentések testreszabását, mentését és ütemezését) lásd a [költségjelentések ismertetését](understanding-cost-reports.md).

Az Azure Cost Management a Cloudynhez hasonló funkcionalitást kínál. Az Azure Cost Management egy natív Azure költségkezelő megoldás. Segít kezelni a költségvetéseket, exportálni az adatokat, valamint áttekinteni és végrehajtani az optimalizálási javaslatokat pénzmegtakarítás céljából. További információ: [Azure Cost Management](../cost-management-billing-overview.md).

## <a name="report-types"></a>Jelentéstípusok

A Cloudyn-jelentéseknek három típusa van:

- Időalapú jelentések. Ilyen például az Időalapú költségek jelentés. Az időalapú jelentések adatok időbeli sorozatát jelenítik meg egy előre meghatározott részletességgel rendelkező kiválasztott időszakban, és heti részlegességgel ismertetik az utolsó két hónapot. A csoportosítás és a szűrés használatával ráközelíthet különböző adatpontokra.
  - Az időalapú jelentésekkel megtekintheti a trendeket, valamint észlelheti a kiugró csúcsokat és anomáliákat.
- Elemzési jelentések. Ilyen például a Költségelemzési jelentés. Ezek a jelentések egy Ön által meghatározott időszak összesített adatait mutatják, valamint lehetővé teszik az adatok csoportosítását és szűrését.
  - Az elemzési jelentések segítségével megtekintheti a kiugró csúcsokat, meghatározhatja az anomáliák kiváltó okait, valamint megjelenítheti az adatok részletes lebontását.
- Táblázatos jelentések. Bármely jelentést megtekinthet táblázatként, bizonyos jelentések azonban csak táblázatként tekinthetők meg. Ezek a jelentések részletes elemlistákkal szolgálnak.
  - A javaslatok táblázatos jelentések, tehát a javaslatok nincsenek vizualizálva. A javaslatok eredményeit azonban vizualizálhatja (például egy adott időszak megtakarításait).
  - A táblázatos jelentések műveleti listaként vagy adatok további feldolgozáshoz való exportálásához hasznos (például költséghelyi elszámolási jelentésként).

A költségjelentések _tényleges_ vagy _amortizált_ költségeket jelenítenek meg.

A tényleges költségek jelentései egy kiválasztott időkereten belül történt kifizetéseket mutatnak. A tényleges költségek jelentéseiben az egyszeri díjak (például a fenntartott példányok vásárlásai) kiugró költségként jelennek meg.

Az amortizált költségek elosztják az egyszeri költségeket arra a vonatkozó időszakra. A fenntartott példányok vásárlásának egyszeri díjai például eloszlanak a foglalási időszakon belül, és nem kiugró csúcsként jelennek meg. A tényleges trendek megtekintése és a költség-előrejelzések létrehozása csak amortizált nézetben lehetséges.

Bizonyos esetekben az amortizáció külön jelentésként jelenik meg. Ilyen például a Költségelemzési és az Amortizált költségek elemzése jelentés. Más esetekben az amortizáció egy jelentési szabályzat, mint például a Költségfelosztási vagy a Költségelemzési jelentés.

Minden jelentéshez ütemezhet időszakos kézbesítést. A költségjelentésekkel beállíthat egy küszöbértéket, így ezek a jelentések hasznosak riasztásokhoz.

## <a name="cost-analysis-vs-cost-allocation"></a>Költségelemzés és költségfelosztás

A _Költségelemzési_ jelentések a felhőszolgáltatóktól származó számlázási adatokat jelenítik meg. A jelentésekkel csoportosíthatja és részletesen elemezheti a számlázási fájlban tételekre bontott különböző adatszegmenseket. A jelentések lehetővé teszik a felhőszolgáltató nyers számlázási adataiban történő részletes költségelemzést.

Bizonyos _költségelemzési_ jelentések nem csoportosítják a költségeket erőforráscímkék szerint. A címkealapú számlázási információk csak azután jelennek meg a jelentésekben, hogy felosztotta a költségeket a költségek [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) használatával történő létrehozása által.

A _Költségfelosztási_ jelentések azután érhetők el, hogy létrehozott egy költségmodellt a [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) használatával. A Cloudyn feldolgozza a költség- és számlázási adatokat, és _megfelelteti_ az adatokat a felhőfiókok használati és címkeadatainak. Az adatok megfeleltetéséhez a Cloudynnek hozzá kell férnie a használati adatokhoz. Ha rendelkezik olyan fiókokkal, amelyekből hiányoznak a hitelesítő adatok, a Cloudyn _nem kategorizált erőforrásként_ címkézi fel őket.

## <a name="dashboards"></a>Irányítópultok

A Cloudyn irányítópultjai magas szintű áttekintést nyújtanak a jelentésekhez. Az irányítópultokat widgetek alkotják, és az egyes widgetek tulajdonképpen a jelentések miniatűrjei. A jelentések [testreszabását](understanding-cost-reports.md#save-and-schedule-reports) követően a My Reports (Saját jelentések) közé mentheti a jelentéseket, és a rendszer hozzáadja őket az irányítópulthoz. Az irányítópultokkal kapcsolatos további információkért lásd a [fő költségmetrikák irányítópultokkal való megtekintését](dashboards.md).

## <a name="budget-information-in-reports"></a>Költségvetési információk a jelentésekben

Számos Cloudyn-jelentés csak azután jelenít meg költségvetési információkat, hogy manuálisan létrehozott egy költségvetést. Ezért a jelentések nem jelenítenek meg költségvetési információkat, amíg létre nem hoz egy költségvetést. További információkért lásd a [költségvetés-kezelési beállításokat](#budget-management-settings).

## <a name="reports-and-reporting-features"></a>Jelentések és jelentéskészítési funkciók

A Cloudyn az alábbi jelentéseket és jelentéskészítési funkciókat tartalmazza.

### <a name="cost-navigator-report"></a>Költségnavigátor jelentés

A Költségnavigátor jelentéssel gyorsan megtekintheti a számlázási használatát egy irányítópult-nézet segítségével. Ez a jelentés számos szűrővel és alapszintű nézettel rendelkezik, amelyekkel azonnal megjelenítheti a szervezete költségeinek összesített nézetét. A költségek megjelenítése dátum alapján történik. Mivel a jelentés a költségek kezdeti nézeteként szolgál, nem olyan rugalmas vagy átfogó, mint sok más jelentés vagy egyéni irányítópult, amelyet Ön hoz létre.

Alapértelmezés szerint a jelentés nagyobb nézetei az alábbiakat jelenítik meg:

- Időalapú költségek egy munkahetet megjelenítő sávdiagramnézetben. A **Date Range** (Dátumtartomány) módosításával módosíthatja a sávdiagram dátumtartományát.
- Kiadások szolgáltatás szerint, tortadiagram használatával.
- Erőforrások kategorizálása címkék szerint, tortadiagram használatával.
- Kiadások költségentitások szerint, tortadiagram használatával.
- Teljes költség dátum szerint, listanézetben.

### <a name="cost-analysis-report"></a>Költségelemzési jelentés

A Költségelemzési jelentés egy költséghelyi visszacsatolási és költséghelyi elszámolási számítás, amely az Ön szabályzatán alapul. A jelentés összesíti a kiválasztott időkereten belüli felhőhasználatot, miután az összes felosztási szabályt alkalmazta a költségre. Például kiszámítja a költségeket címkék szerint, újból hozzárendeli a címkézetlen erőforrások költségeit, és opcionálisan lefoglalja a fenntartott példányok használatát.

A rendszer alkalmazza a [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs)-ban beállított szabályzatokat a Költségelemzési jelentésben, majd ötvözi az eredményeket a felhőszolgáltató nyers adataiból származó információkkal.

Hogyan történik a jelentés kiszámítása? A _fiókaffinitás_ alkalmazásával a Cloudyn szolgáltatás biztosítja, hogy a felosztás megőrizze az egyes összekapcsolt fiókok integritását. Az affinitás biztosítja, hogy a konkrét szolgáltatást nem használó fiókokhoz ne legyenek hozzárendelve a szolgáltatás költségei. A fiókban keletkező költségek az adott fiókban maradnak, és a kiszámításuk nem a felosztási szabályzatok szerint történik. Például öt összekapcsolt fiókkal rendelkezik. Ha közülük csak három használ tárolási szolgáltatásokat, akkor a tárolási szolgáltatások költsége csak a három fiók címkéi között van felosztva.

A Költségelemzési jelentéssel:

- Kiszámíthatja a szervezete költséghelyi elszámolását és költséghelyi visszacsatolását
- Kategorizálhatja a költségeit
- Megjelenítheti a teljes környezet adott időkeretre vonatkozó összesített nézetét
- Megtekintheti a költségeket a költségmodellben létrehozott szabályzatokon alapuló címkekategóriák szerint.

A Költségelemzési jelentés használata:

1. Válasszon ki egy dátumtartományt.
2. Igény szerint adjon hozzá címkéket.
3. Adjon hozzá csoportokat.
4. Válasszon ki egy korábban létrehozott költségmodellt.

### <a name="cost-over-time-report"></a>Időalapú költségek jelentése

Az Időalapú költségek jelentés idősorozatokként jeleníti meg a költségfelosztási eredményeket. Ez a jelentés lehetővé teszi, hogy megfigyelje a trendeket, és észlelje a rendellenességeket a környezetben. Tulajdonképpen a költségek adott időszakban történő elosztását jeleníti meg. A jelentés tartalmazza a kiválasztott időkereten belül elköltött főbb költségelemeket, például a folyamatos költségeket és a fenntartott példányok egyszeri díjait. Ebben a jelentésben a rendszer alkalmazza a [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs)-ban beállított szabályzatokat.

Az Időalapú költségek jelentéssel:

- Megtekintheti az időalapú változásokat, valamint azt, hogy mely hatások változnak az egyik napról (vagy dátumtartományról) a másikra.
- Elemezheti egy adott példány időalapú költségeit.
- Megismerheti, miért növekedtek egy adott példány költségei.

Az Időalapú költségek jelentés használatához:

1. Válasszon ki egy dátumtartományt.
2. Igény szerint adjon hozzá címkéket.
3. Adjon hozzá csoportokat.
4. Válasszon ki egy korábban létrehozott költségmodellt.
5. Válassza ki a tényleges költségeket vagy az amortizált költségeket.
6. Adja meg, hogy a felosztási szabályokat a nyers számlázási adatok nézetének vagy az újraszámított költségek nézetének megtekintéséhez szeretné alkalmazni.

### <a name="actual-cost-analysis-report"></a>Tényleges költségelemzési jelentés

A Tényleges költségelemzési jelentés módosítások nélkül jeleníti meg a szolgáltatói költségeket. Ez a jelentés a főbb költségelemeket jeleníti meg, például a folyamatos költségeket és az egyszeri díjakat.

A jelentéssel megtekintheti az előfizetéseivel kapcsolatos költséginformációkat. A jelentésben az Azure-előfizetések **fióknévként** és **fiókszámként** jelennek meg. A **Linked accounts** (Összekapcsolt fiókok) az AWS-előfizetéseket jelenítik meg. Az előfizetésenkénti költségek és a fiókonkénti lebontás megtekintéséhez válassza ki az előfizetése típusát a **Groups** (Csoportok) területen.

A Tényleges költségelemzési jelentéssel:

- Elemezheti és monitorozhatja az adott időkereten belül elköltött nyers szolgáltatói költségeket.
- Ütemezhet egy küszöbérték-riasztást.
- Elemezheti a fiókok és az entitások által előidézett módosítatlan költségeket.

### <a name="actual-cost-over-time-report"></a>Tényleges időalapú költségek jelentés

A Tényleges időalapú költségek jelentés egy standard költségelemzési jelentés, amely meghatározott időbeli részletességgel osztja el a költségeket. A jelentés időalapú költést jelenít meg, így lehetővé teszi, hogy megfigyelje a trendeket, és észlelje a költési rendellenességeket. Ez a jelentés a kiválasztott időkereten belül elköltött főbb költségelemeket jeleníti meg, például a folyamatos költségeket és a fenntartott példányok egyszeri díjat.

A Tényleges időalapú költségek jelentéssel:

- Megtekintheti az adott időszakra vonatkozó trendeket.
- Rendellenességeket kereshet a költségekben.
- Megtekintheti a felhőszolgáltatókkal kapcsolatos költségekre vonatkozó kérdéseket.

### <a name="amortized-cost-reports"></a>Amortizált költségjelentések

Az amortizált költségek ezen készlete linearizált, nem használatalapú szolgáltatási díjakat vagy egyszer kifizetendő költségeket jelenít meg, és egyenletesen elosztja a költségeket időben az élettartamuk során. Az egyszeri díjak például az alábbiakat tartalmazzák:

- Éves támogatási díjak
- Biztonsági összetevők éves díjai
- Fenntartott példányok beszerzési illetékei
- Egyes Azure Marketplace-elemek

A számlázási fájlban az egyszeri díjak karakterizálása akkor történik meg, amikor a szolgáltatáshasználat kezdő és záró dátuma (időbélyege) azonos értékkel rendelkezik. A Cloudyn szolgáltatás ezt követően amortizált egyszeri díjakként ismeri fel őket. Az egyéb, igény szerinti használattal rendelkező használatalapú szolgáltatások költségei nincsenek amortizálva.

Az Amortizált költségjelentések az alábbiakat tartalmazzák:

- Amortizált költségek elemzése
- Időalapú amortizált költségek

### <a name="cost-analysis-report"></a>Költségelemzési jelentés

A Költségelemzési jelentés betekintést nyújt a felhőhasználatba és a kiadásokba egy kiválasztott időkereten belül. A rendszer alkalmazza a [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs)-ban beállított szabályzatokat a Költségelemzési jelentésben.

Hogyan számítja ki a Cloudyn ezt a jelentést?

A _fiókaffinitás_ alkalmazásával a Cloudyn biztosítja, hogy a felosztás megőrizze az egyes összekapcsolt fiókok integritását. Az affinitás biztosítja, hogy a konkrét szolgáltatást nem használó fiókokhoz ne is legyenek hozzárendelve a szolgáltatás költségei. A fiókban keletkező költségek az adott fiókban maradnak, és a kiszámításuk nem a felosztási szabályzatok szerint történik. Például öt összekapcsolt fiókkal rendelkezik. Ha közülük csak három használ tárolási szolgáltatásokat, akkor a tárolási szolgáltatások költsége csak a három fiók címkéi között van felosztva.

A Költségelemzési jelentéssel:

- Megjelenítheti a teljes környezet adott időkeretre vonatkozó összesített nézetét
- Megtekintheti a költségeket a költségmodellben létrehozott szabályzatokon alapuló címkekategóriák szerint.

### <a name="cost-over-time-report"></a>Időalapú költségek jelentése

Az Időalapú költségek jelentés megjeleníti az időalapú kiadásokat, így észlelheti a trendeket és a rendellenességeket az üzemelő példányban. Tulajdonképpen a költségek adott időszakban történő elosztását jeleníti meg. A jelentés tartalmazza a kiválasztott időkereten belül elköltött főbb költségelemeket, például a folyamatos költségeket és a fenntartott példányok egyszeri díjait. Ebben a jelentésben a rendszer alkalmazza a [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs)-ban beállított szabályzatokat.

Az Időalapú költségek jelentéssel:

- Megtekintheti az időalapú változásokat, valamint azt, hogy mely hatások változnak az egyik napról (vagy dátumtartományról) a másikra.
- Elemezheti egy adott példány időalapú költségeit.
- Megismerheti, miért növekedtek egy adott példány költségei.

### <a name="custom-charges-report"></a>Egyéni díjakkal kapcsolatos jelentés

A vállalati és CSP-felhasználók a saját felhőerőforrás-használatuk mellett gyakran további szolgáltatásokat biztosítanak külső vagy belső ügyfeleik számára. A hozzáadott szolgáltatások egyéni díjait vagy az ügyfél számlázási vagy költséghelyi elszámolási jelentéseihez hozzáadott kedvezményeket egyéni sorelemekként határozza meg.

Az egyéni szolgáltatási díjak azokat a szolgáltatásokat jelzik, amelyek általában nem jelennek meg a számlákon. A létrehozott egyéni díjak ezután megjelennek a Költségjelentésekben.

*Az egyéni díjak díjszabása nem egyéni*. Az egyéni díjak listája nem jeleníti meg az esetlegesen kiszámlázott különféle díjakat. Az AWS számlázási díjai például csak a kiszámlázáskor jelennek meg.

Egyéni díj létrehozása:

1. A **Custom Charges** (Egyéni díjak) területen kattintson az **Add New** (Új hozzáadása) elemre. Megjelenik az _Add New Custom Charge_ (Új egyéni díj hozzáadása) párbeszédpanel.
2. A **Provider Name** (Szolgáltató neve) mezőben adja meg a szolgáltató nevét.
3. A **Service Name** (Szolgáltatásnév) mezőben adja meg a szolgáltatás típusát.
4. A **Description** (Leírás) mezőben adja meg az egyéni díj leírását.
5. A **Type** (Típus) mezőben adja meg a kiválasztott **Percentage** (Százalék) értéket, majd a Services (Szolgáltatások) legördülő listából válassza ki azokat a szolgáltatásokat, amelyeket bele szeretne foglalni a költségjelentésekbe egyéni díjakként.
6. A **Payment** (Fizetés) mezőben válassza ki, hogy a díj One-Time Fee (Egyszeri díj) vagy Recurring Fee (Ismétlődő díj). Ha a díj ismétlődő díj, és azt szeretné, hogy a díj amortizált legyen, válassza az Amortized (Amortizált) lehetőséget, és adja meg a hónapok számát.
7. Ha egyszeri díj van kiválasztva, a **Date** (Dátum) terület **Effective Date** (Hatályba lépés dátuma) mezőjében adja meg a díj kifizetésének dátumát. Ha a Recurring Fee (Ismétlődő díj) lehetőség van kiválasztva, adja meg a dátumtartományt a díj kezdő és záró dátumával együtt.
8. Az **Entities tree** (Entitásfa) területen válassza ki az entitásokat, amelyekre alkalmazni szeretné a díjat, majd válassza az **On** (Be) lehetőséget.

_Ha a díjakat egy entitáshoz rendeli, a felhasználók nem módosíthatják azokat. Azok a díjak, amelyeket egy rendszergazda adott hozzá egy fölérendelt entitáshoz, írásvédettek._

Az egyéni költségek megtekintése:

Az egyéni díjak a Költségjelentéseknél jelennek meg. Például nyissa meg a Tényleges költségelemzési jelentést, majd az **Extended Filters** (Kiterjesztett szűrők) területen válassza a **Standalone** (Különálló) lehetőséget. Ezután szűréssel jelenítse meg az **egyéni díjakat**.

### <a name="cost-allocation-360"></a>Cost Allocation 360

A Cost Allocation 360 használatával létrehozhat egyéni költségfelosztási modelleket a költségek felhasznált felhőalapú erőforrásokhoz való hozzárendeléséhez. Számos jelentés olyan egyéni költségmodellek információit jeleníti meg, amelyeket egyéni költségmodellekkel hozott létre. Emellett egyes jelentések csak akkor jelenítenek meg információkat, ha létrehozott egy költségfelosztást biztosító egyéni költségmodellt.

Az egyéni költségmodellek létrehozásával kapcsolatos további információkért lásd: [Oktatóanyag: Költségek kezelése a Cloudyn használatával](tutorial-manage-costs.md).

### <a name="cost-vs-budget-over-time-report"></a>Költségek és időalapú költségvetés jelentés

A Költségek és időalapú költségvetési jelentéssel összehasonlíthatja a költségvetésbe beleszámító fő költségelemeket. A hozzárendelt költségvetés megjelenik a jelentésben, így megtekintheti a költségvetés időalapú használatát (túlhasználtságát, kihasználatlanságát vagy megfelelő használatát). A jelentés tetején lévő Show/Hide Fields (Mezők megjelenítése/elrejtése) lehetőség használatával kiválaszthatja, hogy a költségeket, a költségvetést, a halmozott költségeket vagy a teljes költségvetést szeretné megtekinteni.

### <a name="current-month-projected-cost-report"></a>Aktuális havi előre jelzett költség jelentés

Az Aktuális havi előre jelzett költség jelentés betekintést nyújt az aktuális hónap költségeinek összegzésébe. Ez a jelentés a hónap elejétől számított költségeket, az előző havi költségeket és az aktuális hónap teljes előre jelzett költségeit mutatja. Az aktuális havi előre jelzett költség kiszámítása a naprakész havi költségek összegeként és az elmúlt 30 napban monitorozott költségek előrejelzéseként történik.

Az Aktuális havi előre jelzett költség jelentéssel:

- Előre jelezheti a költségeket szolgáltatás szerint
- Előre jelezheti a költségeket fiók szerint

### <a name="annual-projected-cost-report"></a>Éves előre jelzett költségről szóló jelentés

Az Éves előre jelzett költségről szóló jelentéssel megtekintheti az éves előre jelzett költségeket a korábbi kiadási trendek alapján. Ez a jelentés a következő 12 hónap összes előrejelzett költségét mutatja. Az előrejelzések létrehozása egy, a következő 12 hónapra extrapolált trendfunkció használatával történik az elmúlt 30 nap használatához kapcsolódó költségek alapján.

### <a name="budget-management-settings"></a>Költségvetés-kezelési beállítások

A Költségvetés-kezelés segítségével beállíthat egy költségkeretet az adott pénzügyi évre.

Költségvetés entitáshoz való hozzáadása:

1. A Budget Management (Költségvetés-kezelés) oldal **Entities** (Entitások) területén válassza ki az entitást, amelyben a költségvetést létre szeretné hozni.
2. A költségvetési évben válassza ki az évet, amelyhez létre szeretné hozni a költségvetést.
3. Minden hónapban állítsa be a költségvetést, majd kattintson a **Save** (Mentés) parancsra.

Az éves költségvetés fájljának importálása:

1. Az **Actions** (Műveletek) területen válassza az **Export** (Exportálás) lehetőséget egy üres CSV-sablon letöltéséhez, amelyet a költségvetés alapjaként használhat.
2. Töltse ki a CVS-fájlt a költségvetés elemeivel, és mentse a fájlt helyileg.
3. Az **Actions** (Műveletek) területen válassza az **Import** (Importálás) elemet.
4. Válassza ki a mentett fájlt, majd kattintson az **OK** gombra.

A kész költségvetés CVS-fájlként történő exportálásához az **Actions** (Műveletek) területen válassza az **Export** (Exportálás) lehetőséget, és töltse le a fájlt.

Ha a költségvetés elkészült, megjelenik a Költségelemzési jelentések között, valamint a Költségek és időalapú költségvetés jelentésben. Emellett jelentéseket is ütemezhet a költségvetési küszöbértékek alapján.

### <a name="azure-resource-explorer-report"></a>Azure Resource Explorer-jelentés

Az Azure Resource Explorer-jelentés a Cloudynban elérhető Azure-erőforrások tömeges listáját mutatja. A jelentés hatékony használatához az Azure-fiókokban engedélyezni kell a kiterjesztett metrikákat. A kiterjesztett metrikák hozzáférést biztosítanak a Cloudyn számára az Azure-beli virtuális gépekhez. További információkért lásd a [kiterjesztett metrikák Azure-beli virtuális gépekhez történő hozzáadását](azure-vm-extended-metrics.md) ismertető szakaszt.

### <a name="azure-resources-over-time-report"></a>Azure-erőforrások adott időszakban jelentés

Az Azure-erőforrások adott időszakban jelentés az összes futó jelentés lebontását mutatja egy adott időszakban. A jelentés hatékony használatához az Azure-fiókokban engedélyezni kell a kiterjesztett metrikákat. A kiterjesztett metrikák hozzáférést biztosítanak a Cloudyn számára az Azure-beli virtuális gépekhez. További információkért lásd a [kiterjesztett metrikák Azure-beli virtuális gépekhez történő hozzáadását](azure-vm-extended-metrics.md) ismertető szakaszt.

### <a name="instance-explorer-report"></a>Instance Explorer-jelentés

Az Instance Explorer-jelentés a virtuális gépek objektumaihoz tartozó különböző metrikák megtekintésére szolgál. Elemezhet konkrét példányokat az olyan információk megtekintéséhez, mint például az alábbiak:
- Példányfuttatási időközök
- Életciklus a kiválasztott időszakban
- Processzorhasználat
- Hálózati bemenet
- Kimenő forgalom
- Aktív lemezek

Az Instance Explorer-jelentés összegyűjti a meghatározott dátumtartományban található összes futási intervallumot, és ennek megfelelően összesíti az adatokat. A dátumtartományban található futási intervallumok megtekintéséhez terjessze ki a példányt. A rendszer az egyes példányok költségét az AWS- és az Azure-listaárak alapján a kiválasztott dátumtartományhoz számítja ki. Kedvezmények nélkül. A Show/Hide Fields (Mezők megjelenítése/elrejtése) lehetőség használatával további mezőket adhat hozzá a jelentéshez.

Az Instance Explorer-jelentéssel:

- Kiszámíthatja a gépenkénti becsült költséget.
- Létrehozhatja az adott időtartományon belül aktív gépek teljes listáját, beleértve az összesített futási órákat.
- Létrehozhat listákat felhőszolgáltató vagy fiók szerint.
- Megtekintheti az adott időtartományon belül létrehozott vagy megszüntetett gépeket.
- Megtekintheti az összes aktuálisan leállított gépet.
- Megtekintheti az egyes gépek címkéit.

### <a name="instances-over-time-report"></a>Példányok adott időszakban jelentés

A Példányok adott időszakban jelentés használatával megtekintheti a kiválasztott időtartományon belül aktív gépek maximális számát. Ha heti vagy havi részletesség van megadva, az eredmények az adott hónap adott napján aktív gépek maximális számára vonatkoznak. Válasszon ki egy dátumtartományt a jelentésben megjeleníteni kívánt szűrők kiválasztásához.

### <a name="instance-utilization-over-time-report"></a>Időalapú példányhasználat jelentés

Ez a jelentés az összes példány processzor- vagy memóriahasználatának időalapú lebontását mutatja.

### <a name="compute-power-cost-over-time-report"></a>Számítási teljesítmény időalapú költsége jelentés

A Számítási teljesítmény időalapú költsége jelentés a megadott dátumtartományra vonatkozó számítási teljesítmény lebontását mutatja. Bár más jelentések a futó gépek vagy a futásidő óráinak számát jelenítik meg, ez a jelentés alapóraszámot, a számítási egységhez tartozó óraszámot vagy a GB RAM-hoz tartozó óraszámot jeleníti meg.

A jelentéssel:

- Ellenőrizheti a számítási teljesítményt egy adott dátumtartományon belül.
- Megtekintheti a számítási időket a költségfelosztási modellek alapján.

Ez a jelentés kapcsolódik a [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs)-szabályzatokhoz, így az eredmények a kiválasztott költségszabályzatban meghatározott címkézés és szabályzatok alapján jelennek meg. Ha nem hozott létre szabályzatot, az eredmények nem jelennek meg.

### <a name="compute-power-average-cost-over-time-report"></a>Számítási teljesítmény átlagos időalapú költsége jelentés

A Számítási teljesítmény átlagos időalapú költsége jelentéssel az egyes futó gépek költségén kívül más adatok is megtekinthetők. A jelentés a példányóránkénti átlagos költséget, az alapóraszámot, a számítási egységhez tartozó óraszámot és a GB RAM-hoz tartozó óraszámot is megjeleníti. A jelentés betekintést nyújt a környezet hatékonyságába.

Ez a jelentés kapcsolódik a [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs)-szabályzatokhoz, így az eredmények a kiválasztott költségszabályzatban meghatározott címkézés és szabályzatok alapján jelennek meg. Ha nem hozott létre szabályzatot, az eredmények nem jelennek meg.

### <a name="s3-cost-over-time-report"></a>S3-beli időalapú költségek jelentés

Az S3-beli időalapú költségek jelentés az Amazon Simple Storage Service (S3) költségeinek időalapú, adott időkereten belüli, gyűjtőkre való lebontásával szolgál. A jelentés segít megtalálni a fő költségtényezőkként szolgáló gyűjtőket, és megmutatja az S3-használat és -kiadások trendjeit.

### <a name="s3-distribution-of-cost-report"></a>S3-költségeloszlás jelentés

Ezzel a jelentéssel gyűjtő és tárolási osztály alapján elemezheti az előző havi S3-költségeket. A tortadiagram nézettel beállíthatja a láthatósági küszöbértéket. A táblázatnézet használatával pedig megtekintheti a részösszegeket.

### <a name="s3-bucket-properties-report"></a>S3-gyűjtőtulajdonságok jelentés

Ezzel a jelentéssel megtekintheti az S3-gyűjtők tulajdonságait. A tortadiagram nézettel beállíthatja a láthatósági küszöbértéket. A táblázatnézet használatával pedig megtekintheti a részösszegeket.

### <a name="rds-instances-over-time-report"></a>RDS-példányok adott időszakban jelentés

Ezzel a jelentéssel megtekintheti a megadott időszakban futó Amazon Relational Database Service- (RDS-) példányok lebontását.

### <a name="rds-active-instances-report"></a>Aktív RDS-példányok jelentés

Ezzel a jelentéssel elemezheti az aktív RDS-példányokat. A további információk megtekintéséhez bontsa ki a sorelemet a jelentésben.

### <a name="azure-reserved-instances-report"></a>Azure-beli fenntartott példányok jelentés

Az Azure-beli fenntartott példányok jelentésben egyszerre tekintheti meg az Azure-beli fenntartott példányok mindegyikét. Ez a jelentés külön sortételként jeleníti meg az egyes vásárlásokat. A jelentés a vásárlások részleteit is megjeleníti, például a vásárlást kezdeményező fiókot, a vásárlás típusát és a példánytípust, a hátralévő napokat és az egyéb információkat. A jelentésadatokat a Show/Hide Fields (Mezők megjelenítése/elrejtése) elemmel jelenítheti meg vagy rejtheti el.

Az Azure Reserved Instances (Azure-beli fenntartott példányok) jelentéssel az alábbiakat tekintheti meg:

- Az összes foglalás listáját a vásárlás dátuma szerint.
- A fenntartott példány lejáratáig fennmaradó időt.
- Az egyszeri díjakat.
- A fenntartott példányokat megvásárló fiókot és a vásárlás időpontját.

### <a name="aws-reserved-instances-report"></a>AWS Reserved Instances (AWS-beli fenntartott példányok) jelentés

Az AWS Reserved Instances (AWS-beli fenntartott példányok) jelentésben egyszerre tekintheti meg az AWS-beli fenntartott példányok mindegyikét. Ez a jelentés külön sortételként jeleníti meg az egyes vásárlásokat, emellett a vásárlások részleteit is megjeleníti, például a vásárlást kezdeményező fiókot, a vásárlás típusát és a példánytípust, a hátralévő napokat és az egyéb információkat. A jelentésadatokat a Show/Hide Fields (Mezők megjelenítése/elrejtése) elemmel jelenítheti meg vagy rejtheti el.

Az AWS Reserved Instances (AWS-beli fenntartott példányok) jelentéssel az alábbiakat tekintheti meg:

- Az összes foglalás listáját a vásárlás dátuma szerint.
- A fenntartott példány lejáratáig fennmaradó időt.
- Az egyszeri díjakat.
- Eredeti vásárlás azonosítója (foglalásazonosító).
- A fenntartott példányokat megvásárló fiókot és a vásárlás időpontját.

### <a name="ec2-ri-buying-recommendations-report"></a>EC2 RI Buying Recommendations (Fenntartott EC2-példány vásárlására vonatkozó javaslatok) jelentés

A felhőbeli erőforrások felhasználása az igény szerinti modellen alapul, amelyben az erőforrások csak a használatuk esetén járnak költséggel. Nincs előzetes kötelezettségvállalás, csak a valóban használt funkciókért kell fizetnie, és csak akkor, amikor használja őket.

Az AWS alternatív árképzési modellt kínál az Elastic Compute Cloud- (EC2-) szolgáltatásaira vonatkozóan, a fenntartott példányt. Ez az árképzési modell a fenntartott példány időtartama során garantálja a felhasználóknak a kellő kapacitást, amikor csak szükségük van rá. A fenntartott példány jelentős árkedvezményeket kínál az igény szerinti árképzéshez képest. A felhasználóknak cserébe előzetes kötelezettségvállalást kell tenniük a virtuális példány használatára vonatkozóan. A kötelezettségvállalás egy adott termékcsaládhoz, mérethez, rendelkezésreállási zónához és operációs rendszerhez kötött a kötelezettségvállalás időtartamára (egy vagy három év). A fenntartott példány lehetővé teszi az AWS számára a jövőbeli kapacitás hatékony tervezését, valamint az ügyfelek elkötelezettségének megnyerését a szolgáltatásai használatára.

A fenntartott példányok vásárlásához három fizetési lehetőség érhető el:

- Teljes összeg a 0. napon, amely a legmagasabb kedvezményt kínálja.
- Nincs előzetes fizetés, amely esetén a fenntartott példány költsége havi részletekben fizetendő a fenntartott példány időtartama során. Ez a lehetőség kínálja a legkisebb kedvezményt.
- Részleges előzetes fizetés, amely esetén az ár ¼–½-e előre fizetendő, a fennmaradó összeg pedig havi részletekben. A kedvezmény mértéke viszonylag alacsony, de közelebb áll a teljes összeg előre fizetését lehetővé tevő lehetőséghez.

A Cloudyn értékeli az egyes gépek üzemidejét az elmúlt 30 napra vonatkozóan. A Cloudyn fenntartott példányok vásárlását javasolja, ha az aktuális üzemidőszint mellett a gépet fenntartott példánnyal költséghatékonyabb futtatni.

A jelentés megjeleníti az év során a lehető legtöbb pénz megtakarítására irányuló javaslatainak indoklását. A javaslatok az igény szerinti példányok fenntartott példányokra való lecserélését ajánlják. A fenntartott példányokat közvetlenül a jelentésből is megvásárolhatja.

Mindegyik lap teljes jelentésként nyílik meg. A lapok fontos részei az alábbiak:

- **EC2 RI Purchase Impact** (Fenntartott EC2-példány vásárlásának hatása) – Ez a szakasz az igény szerinti és a fenntartott példányok közötti különbség szimulációját biztosítja. A **Zoom in** (Nagyítás) elemre kattintva megtekintheti a teljes EC2 RI Purchase Impact (Fenntartott EC2-példány vásárlásának hatása) jelentést, a javaslatra vonatkozóan előre meghatározott szűrőkkel. Ez a jelentés a különféle fenntartott példányok esetleges megvásárlásával járó hatást jeleníti meg. A várt átlagos üzemidő módosításával megtekintheti a fenntartott EC2-példányok vásárlásával járó lehetséges megtakarítást.

- **Saving Analysis** (Megtakarításelemzés) – Ez a szakasz az elért lehetséges megtakarítást és azt a hónapot jeleníti meg, amikor a megtakarítás realizálódik a Cloudyn javaslatainak követése esetén. A tényleges megtakarítást és a megtakarított százalékot piros színnel emeli ki a rendszer.

- **EC2 RI Type Comparison** (Fenntartott EC2-példánytípusok összehasonlítása) – Ez a szakasz a Cloudyn által ajánlott üzemi környezettel járó megtérüléssel kapcsolatos főbb információkat jeleníti meg, beleértve a kapcsolódó lehetőségeket is. A jelentés eredményei feltételezik, hogy a gép 100%-os üzemidő mellett fut. A **Zoom In** (Nagyítás) elemre kattintva nyithatja meg a részletes jelentést.

- **Instances Over Time** (Példányok egy adott időszakban) – Ez a szakasz a javaslathoz kapcsolódó összes példány részletezését jeleníti meg, OnDemand (Igény szerinti), Reserved Instances (Fenntartott példányok) és Spot (Kihasználatlan) szerint lebontva. A **Zoom In** (Nagyítás) elemre kattintva nyithatja meg a részletes jelentést.
- **Breakeven Points** (Kiegyenlítődési pontok) – Ez a szakasz egy táblázatot jelenít meg az összes lehetséges, javasolt üzemi környezettel, a megtérüléssel, valamint a megtérülés realizálódásának hónapjával. A **Zoom In** (Nagyítás) elemre kattintva nyithatja meg a részletes jelentést.

### <a name="ec2-reservations-over-time-report"></a>EC2 Reservations Over Time (EC2-foglalások adott időszakban) jelentés

Az EC2 Reservations Over Time (EC2-foglalások adott időszakban) jelentés a megvásárolt fenntartott EC2-példányok használatának állapotát követi nyomon. A jelentés részletességét beállíthatja órára, napra vagy hétre.

A jelentéssel:

- Megjelenítheti, hogy a megvásárolt foglalások közül melyiket használják és melyiket nem.
- Óra szintig részletezhet a fenntartott példány órára lebontott használatának megtekintéséhez.

### <a name="savings-over-time-report"></a>Savings Over Time (Megtakarítás adott időszakban) jelentés

A Savings Over Time (Megtakarítás adott időszakban) jelentéssel a fenntartott példányokkal és a kihasználatlan példányokkal elért megtakarítást tekintheti meg. A jelentés megjeleníti az adott időszakban a fenntartott példányok megvásárlásával járó megtérülést.

A fenntartott példányoknak köszönhető megtakarítás megtekintéséhez csoportosítsa az eredményeket **Price Model** (Ármodell) alapján, majd válassza a **Reservation** (Foglalás) elemet. Egy adott fiók vagy példánytípus által a fenntartott példányokkal elért megtakarítás megtekintéséhez adja meg a megfelelő csoportosítást, és szűrjön rá a fiókra vagy példánytípusra.

A kihasználatlan példányok használatából eredő megtakarítás megtekintéséhez a **Price Model** (Ármodell) szűrőt állítsa **Spot** (Kihasználatlan) értékűre. A jelentés alapértelmezett szűrője az RI (Fenntartott példány) és Spot Instances (Kihasználatlan példányok).

### <a name="rds-ri-buying-recommendations-report"></a>RDS RI Buying Recommendations (Fenntartott RDS-példány vásárlására vonatkozó javaslatok) jelentés

Az RDS RI Buying Recommendations (Fenntartott RDS-példány vásárlására vonatkozó javaslatok) jelentés javaslatokat ad arra vonatkozóan, hogy mikor érdemes fenntartott RDS-példányokat használni az igény szerinti példányok helyett.

Mindegyik lap teljes jelentésként nyílik meg. A lapok fontos részei az alábbiak:

- **RDS RI Purchase Impact** (Fenntartott RDS-példány vásárlásának hatása) – Ez a szakasz az igény szerinti és a fenntartott példányok közötti különbség szimulációját biztosítja. A **Zoom in** (Nagyítás) elemre kattintva megtekintheti a teljes RDS RI Purchase Impact (Fenntartott RDS-példány vásárlásának hatása) jelentést, a javaslatra vonatkozóan előre meghatározott szűrőkkel. A jelentéssel a különféle fenntartott példányok esetleges megvásárlásával járó hatást is megtekintheti.  A várt átlagos üzemidő módosításával megtekintheti a fenntartott példányok vásárlásával járó lehetséges megtakarítást.
- **Saving Analysis** (Megtakarításelemzés) – Ez a szakasz az elért lehetséges megtakarítást és azt a hónapot jeleníti meg, amikor a megtakarítás realizálódik a Cloudyn javaslatainak követése esetén. A tényleges megtakarítást és a megtakarított százalékot piros színnel emeli ki a rendszer.

- **RDS RI Type Comparison** (Fenntartott RDS-példánytípusok összehasonlítása) – Ez a szakasz az ajánlott üzemi környezettel járó megtérüléssel kapcsolatos főbb információkat jeleníti meg, beleértve a kapcsolódó lehetőségeket is. A jelentés eredményei feltételezik, hogy a gép 100%-os üzemidő mellett fut. A **Zoom In** (Nagyítás) elemre kattintva nyithatja meg a kiválasztott gépre vonatkozó részletes jelentést.
- **Instances Over Time** (Példányok egy adott időszakban) – Ez a szakasz a javaslathoz kapcsolódó összes példány részletezését jeleníti meg, OnDemand (Igény szerinti), Reserved Instances (Fenntartott példányok) és Spot (Kihasználatlan) szerint lebontva. A **Zoom In** (Nagyítás) elemre kattintva nyithatja meg a részletes jelentést.

- **Breakeven Points** (Kiegyenlítődési pontok) – Ez a szakasz egy táblázatot jelenít meg az összes lehetséges, javasolt üzemi környezettel, a megtérüléssel, valamint a megtérülés realizálódásának hónapjával. A **Zoom In** (Nagyítás) elemre kattintva nyithatja meg a részletes jelentést.

### <a name="rds-reservations-over-time-report"></a>RDS Reservations Over Time (RDS-foglalások adott időszakban) jelentés

Az RDS Reservations Over Time (RDS-foglalások adott időszakban) jelentéssel megtekintheti a megadott időtartam során használt és nem használt foglalások részletezését.

### <a name="reserved-instance-purchase-impact-report"></a>Reserved Instance Purchase Impact (Fenntartott példányok vásárlásának hatása) jelentés

Az EC2 RI Purchase Impact (Fenntartott EC2-példány vásárlásának hatása) jelentés lehetővé teszi a fenntartott példányokkal, illetve az igény szerinti példányokkal járó költségek szimulálását egy adott időszakra vonatkozóan. A jelentés segítségével jobb döntéseket hozhat a vásárlás tekintetében. Az olyan tényezőkre, mint az átlagos üzemidőre, időtartamra, platformra és másokra vonatkozó szűrők módosításával megalapozott döntéseket hozhat, amikor fenntartott példányok vásárlását fontolgatja.

### <a name="cost-effective-sizing-recommendations-report"></a>Cost-Effective Sizing Recommendations (Költséghatékony méretezésre vonatkozó javaslatok) jelentés

A Cost-Effective Sizing Recommendations (Költséghatékony méretezésre vonatkozó javaslatok) jelentés az AWS-hez és az Azure-hoz kapcsolódóan biztosít eredményeket. AWS-felhasználók esetén a rendszer a megvásárolt fenntartott példányokat veszi figyelembe, és az eredmények nem foglalják magukban a fenntartott példányként futó gépeket. A jelentés azon alacsony kihasználtságú példányok listáját biztosítja, amelyeknél a rendszer a méret csökkentését javasolja. A javaslatok az elmúlt 30 nap használati és teljesítményadatain alapulnak. Az egyes javaslatok magukban foglalják a méretcsökkentésre javasolt példányok listáját, a méretcsökkentés indoklását, valamint egy hivatkozást, amelyet követve megtekinthetők az adott példányok részletes adatai és teljesítménymetrikái. Az is látható továbbá, hogy a kapcsolódó javaslatok ajánlják-e az újabb generációs példánytípusokra való váltást.

A jelentésből nem tölthető le az azokat a példányazonosítókat tartalmazó lista, amelyek esetén a rendszer méretcsökkentést javasol. A példányazonosítók letöltéséhez használja az All Sizing Recommendations (Minden méretezési javaslat) jelentést.

Tekintse meg a méretcsökkentésre vonatkozó alábbi példát:

Hat, futó m3.xlarge-példánnyal rendelkezik. A Cloudyn elemzése kimutatta, hogy közülük ötnél alacsony a processzor kihasználtsága. Vegye fontolóra a méretük csökkentését.

A Cost Impact (Költségre gyakorolt hatás) szakaszban a költségre gyakorolt hatást számítja ki a rendszer. Ebben a példában a sortétel kibontásával láthatja, hogy egy m3.xlarge-példány (Linux/Unix) költsége 0,266 dollár/óra, egy m3.large-példány (Linux/Unix) költsége pedig 0,133 dollár/óra. A 100%-os kihasználtsággal futó öt m3.xlarge-példány éves költsége így 11 651 dollár. A 100%-os kihasználtsággal futó öt m3.large-példány éves költsége 5825 dollár. A lehetséges megtakarítás 5825 dollár.

A költséghatékony méretezésre vonatkozó javaslatok indoklásának megtekintéséhez kattintson a + elemre a sortétel kibontásához. A **Details** (Részletek) lapon:

- A **Recommendation Justification** (Javaslat indoklása) szakasz az aktuális üzemi környezetet és a méretcsökkentésre javasolt példányok számát jeleníti meg.
- A **Cost Impact** (Költségre gyakorolt hatás) a lehetséges megtakarítás meghatározásához használt számítást jeleníti meg.
- A **Potential Annual Savings** (Lehetséges éves megtakarítás) szakasz a lehetséges éves megtakarítást jeleníti meg, ha a méretcsökkentés végrehajtása a Cloudyn javaslata szerint történik.

### <a name="all-sizing-recommendations-report"></a>All Sizing Recommendations (Minden méretezési javaslat) jelentés

A jelentés azon alacsony kihasználtságú példányok listáját biztosítja, amelyeknél a rendszer a méret csökkentését javasolja. A javaslatok az elmúlt 30 nap használati és teljesítményadatain alapulnak. Az egyes javaslatokban megtekintheti az adott példányok részletes adatait és teljesítménymetrikáit.

Ha fenntartott AWS-példányokat vásárolt, ez a jelentés az összes futó példányra vonatkozóan tartalmaz eredményeket, beleértve a fenntartott példányként futó példányokat is.

Az All Sizing Recommendations (Minden méretezési javaslat) jelentéssel:

- Megtekintheti azon példányok listáját, amelyeknél a rendszer a méret csökkentését javasolja.
- Exportálhatja a példányneveket és -azonosítókat tartalmazó jelentéslistát.

Egy adott példányra vonatkozó javaslat részleteinek megtekintéséhez kattintson a **+** jelre a részletek kibontásához. A Recommendation Details (Javaslat részletei) szakasz biztosítja a javaslat áttekintését.

A **Tags** (Címkék) szakasz a kiválasztott példány címkekulcsainak és -értékeinek listáját biztosítja. A bal oldali panelen található Tags (Címkék) elemmel szűrheti a szakaszt.

A **CPU Utilization** (CPU-használat) szakaszban a példány elmúlt havi CPU-használata látható napra lebontva.

A grafikonra kattintva részletes elemzést indíthat, és megnyithatja az Instance CPU Over Time (Példányok CPU-használata adott időszakban) jelentést a példányok részletezésének megtekintéséhez.

- A **Show/Hide Fields** (Mezők megjelenítése/elrejtése) elemmel a következő mezőket veheti fel vagy távolíthatja el: Timestamp (Időbélyeg), Avg CPU (Átl. CPU), Min CPU (Min. CPU), Max CPU (Max. CPU).
- A **Date Range** (Dátumtartomány) használatával egy dátumot vagy dátumtartományt adhat meg, és egy adott példányazonosítót elemezhet.
- Az **Extended Filters** (Bővített szűrők) használatával megjelenítheti az összes vagy csak egy adott példányazonosítót.
- A **Zoom In** (Nagyítás) elemre kattintva nyithatja meg a CPU Utilization (CPU-használat) jelentést.

Ha a példány 30 napig nem volt monitorozva, hiányos adatok láthatók.

A **Memory Utilization (GB)** (Memóriahasználat (GB)) szakasz a felhasznált memóriával kapcsolatban biztosít információt. AWS-felhasználók esetén a memóriametrikák nem érhetők el automatikusan, és minden példányhoz külön hozzá kell adni őket az AWS segítségével. Az AWS díjat számít fel az EC2-példányokra vonatkozó memóriametrikák engedélyezéséhez.

A **Memory Utilization (%)** (Memóriahasználat (%)) szakasz a felhasznált memória százalékos arányát jeleníti meg.

A **Network Input Traffic** (Bejövő hálózati forgalom) szakasz a hálózati forgalom átlagos és maximális értékeinek adott időszakra vonatkozó pillanatképét jeleníti meg a kiválasztott példányra vonatkozóan. Az egérmutatót a vonalakra helyezve tekintheti meg a dátumot, illetve az adott időpont maximális hálózati forgalmát. A **Zoom In** (Nagyítás) elemre kattintva nyithatja meg a Network Input Traffic (Bejövő hálózati forgalom) jelentést.

A **Network Output Traffic** (Kimenő hálózati forgalom) szakasz a kimenő hálózati forgalom pillanatképét jeleníti meg a kiválasztott példányra vonatkozóan. Az egérmutatót a vonalakra helyezve tekintheti meg a dátumot, illetve az adott időpont maximális hálózati forgalmát. A **Zoom In** (Nagyítás) elemre kattintva nyithatja meg a Network Output Traffic (Kimenő hálózati forgalom) jelentést.

### <a name="instance-metrics-explorer-report"></a>Instance Metrics Explorer (Példánymetrika-böngésző) jelentés

Az Instance Metrics Explorer (Példánymetrika-böngésző) jelentés az egyes példányok több felhőre vonatkozó teljesítménymetrikáit jeleníti meg. A jelentéssel azokat a példányokat tekintheti meg, amelyek kihasználtsága túl magas vagy túl alacsony a CPU-, a memória- és hálózatmetrikák küszöbértékei alapján.

Az egyes példányok több felhőre vonatkozó teljesítményének megtekintése:

1. A **Date Range** (Dátumtartomány) szakaszban válassza ki azt a dátumtartományt, amelyre vonatkozóan látni szeretné a teljesítményt.
2. A **Tags** (Címkék) szakaszban válassza ki a megtekinteni kívánt címkéket.
3. A **Filters** (Szűrők) szakaszban válassza ki a jelentésben megjeleníteni kívánt szűrőket.
4. Az **Extended Filters** (Bővített szűrők) szakaszban módosíthatja a jelentés küszöbértékeit az alábbiakra vonatkozóan:
    - Avg CPU (Átl. CPU)
    - Max CPU (Max. CPU)
    - Avg Memory (Átl. memória)
    - Max Memory (Max. memória)
5. Kattintson az **Extended Filters** (Bővített szűrők) szakasz **Show** (Megjelenítés) elemére, majd válassza ki a megjeleníteni kívánt példányok típusát.

Egy adott példány adott időszakra vonatkozó metrikáinak megtekintése:

- Nyissa meg az Instance Metrics Explorer (Példánymetrika-böngésző) jelentést, és kattintson a **+** jelre a részletek megtekintéséhez.

### <a name="rds-sizing-recommendations-report"></a>RDS Sizing Recommendations (RDS-méretezési javaslatok) jelentés

Az RDS Sizing Recommendations (RDS-méretezési javaslatok) jelentés az RDS-sel kapcsolatos méretezési javaslatokat biztosít a felhőhasználat optimalizálásához. Azon alacsony kihasználtságú példányok listáját biztosítja, amelyeknél a rendszer a méret csökkentését javasolja. A Cloudyn javaslatai az elmúlt 30 nap használati és teljesítményadatain alapulnak. A javaslatokat Account Name (Fióknév), Region (Régió), Instance Type (Példánytípus) és Status (Állapot) alapján szűrheti.

### <a name="sizing-threshold-manager-report"></a>Sizing Threshold Manager (Méretezési küszöbértékek kezelője) jelentés

A Cloudyn beépített méretezési javaslatainak számítása egy összetett algoritmussal történik a pontos méretezési javaslatok biztosítása érdekében. A méretcsökkentésre vonatkozó javaslatok küszöbértékeit azonban módosíthatja.

Méretezési javaslatok küszöbértékeinek manuális módosítása:

1. A Sizing Threshold Manager (Méretezési küszöbértékek kezelője) jelentésben módosítsa tetszés szerint az alábbi küszöbértékeket:
    - Average CPU % (Átlagos CPU-használat %-ban)
    - Maximum CPU % (Maximális CPU-használat %-ban)
    - Average Memory % (Átlagos memóriahasználat %-ban)
    - Maximum Memory % (Maximális memóriahasználat %-ban)
3. Kattintson az **Apply** (Alkalmaz) elemre a módosítások mentéséhez.
4. A módosítások azonnal érvénybe lépnek a javaslatok mindegyikére vonatkozóan.

Az alapértelmezett küszöbértékek visszaállítása:

- Kattintson a Sizing Threshold Manager (Méretezési küszöbértékek kezelője) jelentés **Restore Defaults** (Alapértelmezett értékek visszaállítása) elemére.

### <a name="compute-instance-types-report"></a>Compute Instance Types (Számításipéldány-típusok) jelentés

A példánytípusokra vonatkozó jelentéssel:

- Megtekintheti a példánytípusokat Service (Szolgáltatás), Family (Család), API Name (API-név) és Name (Név) alapján.
- Olyan részleteket tekinthet meg, mint a CPU, ECU, RAM és Network (Hálózat).

A **Search** (Keresés) használatával adott sortételeket kereshet.

## <a name="next-steps"></a>További lépések

- A [költségjelentéseket ismertető](understanding-cost-reports.md) cikkben elsajátíthatja a jelentések használatát, beleértve a testre szabásukat, mentésüket és ütemezésüket is.
- A [fő költségmetrikák irányítópultokkal történő megtekintését](dashboards.md) ismertető cikkben megismerkedhet a Cloudyn irányítópultjaival, és megtudhatja, hogyan hozhat létre saját, egyéni irányítópultokat.
