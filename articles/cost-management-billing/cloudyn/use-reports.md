---
title: Cloudyn-jelentések használata az Azure-ban | Microsoft Docs
description: Ez a cikk a Cloudyn-portálon található Cloudyn-jelentések célját ismerteti, amely segít a hatékony használatban.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 139ad67035a96fb67bd96c4abb14ce9e1f26bf4f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989058"
---
# <a name="reports-available-in-the-cloudyn-portal"></a>A Cloudyn-portálon elérhető jelentések

Ez a cikk a Cloudyn-portálon található Cloudyn-jelentések célját ismerteti. Azt is leírja, hogyan használhatja a jelentéseket hatékonyan. A legtöbb jelentés intuitív, és egységes megjelenéssel és működéssel rendelkezik. Az egyetlen jelentésben elvégezhető műveletek többsége más jelentésekben is elvégezhető. A Cloudyn-jelentések használatáról, beleértve a jelentések testreszabásának és mentésének módját, valamint a jelentések ütemtervének áttekintését lásd: a [Cost-jelentések ismertetése](understanding-cost-reports.md).

Az Azure Cost Management a Cloudynhez hasonló funkcionalitást kínál. Az Azure Cost Management egy natív Azure költségkezelő megoldás. Segít kezelni a költségvetéseket, exportálni az adatokat, valamint áttekinteni és végrehajtani az optimalizálási javaslatokat pénzmegtakarítás céljából. További információ: [Azure Cost Management](../cost-management-billing-overview.md).

## <a name="report-types"></a>Report types (Jelentéstípusok)

Három típusú Cloudyn-jelentés létezik:

- Időbeli jelentések. Például az idő múlásával kapcsolatos jelentés. Az idő múlásával a jelentések egy idősorozatot jelenítenek meg a kiválasztott intervallumban egy előre meghatározott felbontással, és heti felbontást mutatnak az elmúlt két hónapban. A Csoportosítás és a szűrés használatával különböző adatpontokra nagyíthat.
  - A határidős jelentések segítségével megtekintheti a trendeket, és észlelheti a tüskéket és a rendellenességeket.
- Elemzési jelentések. Például a Cost Analysis jelentés. Ezek a jelentések összesített adatokat jelenítenek meg egy meghatározott időszakban, és lehetővé teszik a csoportosítást és a szűrést az adatain.
  - Az elemzési jelentések segítségével megtekintheti a tüskéket, és meghatározhatja az anomáliák kiváltó okait, és megjelenítheti az adatai részletes lebontását.
- Táblázatos jelentések. A jelentéseket táblázatként is megtekintheti, de egyes jelentések csak táblázatként jelennek meg. Ezek a jelentések az elemek részletes listáját tartalmazzák.
  - A javaslatok táblázatos jelentések – nincsenek vizualizációk a javaslatokhoz. A javaslatok eredményeit azonban megjelenítheti. Például a megtakarítás az idő múlásával.
  - A táblázatos jelentések a műveletek listájaként vagy a további feldolgozás céljából történő adatexportálás során hasznosak. Például egy jóváírási jelentés.

A _Cost-jelentések_ a _tényleges_ vagy az elszámolt költségeket mutatják.

A tényleges Cost-jelentések megjelenítik a kiválasztott időkereten belül végrehajtott kifizetéseket. Például az összes egyszeri díj, például a fenntartott példány (RI) vásárlása a tényleges költségek jelentésekben, a költségek arányában jelenik meg.

Az elszámolt költségekkel kapcsolatos jelentések egy adott időszakra érvényes egyszeri díjakat mutatnak be. Például az RI-vásárlásokra vonatkozó egyszeri díjak a foglalási időszak alatt oszlanak meg, és nem jelennek meg tüskeként. Az elszámolt nézet az egyetlen módja annak, hogy valódi trendeket lásson, és a költségeket is felkészítse.

Bizonyos esetekben az amortizáció külön jelentésként jelenik meg. Ilyenek például a Cost Analysis és az elszámolt Cost Analysis jelentései. Más esetekben az amortizáció egy olyan jelentési házirend, mint például a Cost foglalási és a Cost Analysis-jelentések.

Az időszakos kézbesítésre vonatkozó jelentéseket is ütemezhet. A Cost-jelentések lehetővé teszik a küszöbérték beállítását, így azok hasznosak lehetnek a riasztásokhoz.

## <a name="cost-analysis-vs-cost-allocation"></a>Cost Analysis és Cost kiosztása

A _Cost Analysis_ -jelentések a felhőalapú szolgáltatók számlázási adatait jelenítik meg. A jelentések segítségével csoportosíthatja és részletezheti a számlázási fájl alapján részletezett különböző adatszegmenseket. A jelentések lehetővé teszik a Felhőbeli szállító nyers számlázási adataiban a részletes díjszabást.

Néhány _Cost Analysis_ -jelentés nem csoportosítja a költségeket az erőforrás-címkék alapján. A és a címkén alapuló számlázási adatok csak akkor jelennek meg a jelentésekben, ha a költségek lefoglalása után a Cost ( [költség) Kiosztásával 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

A Cost _foglalási_ jelentései akkor érhetők el, ha az 360-es [Cost kiosztásával](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs)létrehoz egy Cost-modellt. A Cloudyn feldolgozza a költségeket és a számlázási adatokat, és _megfelel_ az adatoknak a Felhőbeli fiókok használati és címkézési adatainak. Az adatok egyeztetéséhez a Cloudyn hozzáférésre van szüksége a használati adatokhoz. Ha vannak olyan fiókok, amelyek nem rendelkeznek hitelesítő adatokkal, akkor a rendszer _Kategorizálatlan erőforrásként_címkézi őket.

## <a name="dashboards"></a>Irányítópultok

A felhőben lévő irányítópultok magas szintű áttekintést nyújtanak a jelentésekről. Az irányítópultok widgetek alkotják, és az egyes widgetek lényegében egy jelentés miniatűrje. Amikor [testreszabja a jelentéseket](understanding-cost-reports.md#save-and-schedule-reports), azokat saját jelentésekba menti, és hozzáadja őket az irányítópulthoz. Az irányítópultokkal kapcsolatos további információkért tekintse [meg a Key Cost-mérőszámok irányítópultokkal történő megtekintését](dashboards.md)ismertető témakört.

## <a name="budget-information-in-reports"></a>Költségvetési információk a jelentésekben

Számos Cloudyn-jelentés a manuálisan létrehozott költségvetési információkat jeleníti meg. Így a jelentések nem jelenítik meg a költségvetési adatokat, amíg létre nem hozza a költségvetést. További információ: költségkeret- [kezelési beállítások](#budget-management-settings).

## <a name="reports-and-reporting-features"></a>Jelentések és jelentéskészítési funkciók

A Cloudyn a következő jelentéseket és jelentéskészítési szolgáltatásokat tartalmazza.

### <a name="cost-navigator-report"></a>Költségnavigátor jelentés

Az Költségnavigátor jelentés egy gyors módszer a számlázási felhasználás megtekintésére az irányítópult-nézet használatával. A szűrők és az alapszintű nézetek egy részhalmazával azonnal megjelenítheti a szervezet költségeinek összefoglaló nézetét. A költségek dátum szerint jelennek meg. Mivel a jelentés a költségek kezdeti nézetére szolgál, nem olyan rugalmas vagy átfogó, mint számos más jelentés vagy egyéni irányítópult, amelyet Ön hozott létre.

Alapértelmezés szerint a jelentés fő nézetei a következőket mutatják be:

- A munkaidő egy munkahetet ábrázoló diagram nézetét jeleníti meg. A dátumtartomány megváltoztatásával módosíthatja a dátumtartomány **oszlopdiagram-** diagramját.
- Kiadások szolgáltatás szerint, tortadiagram használatával.
- Erőforrások kategorizálása címkék szerint, tortadiagram használatával.
- Költség entitások szerinti kiadások kördiagram használatával.
- A teljes összeg, a lista nézetben.

### <a name="cost-analysis-report"></a>Költségelemzési jelentés

A Cost Analysis jelentés a szabályzat alapján a költséghelyi visszacsatolási és a jóváírási összeg kiszámítása. A megadott időkereten belül összesíti a Felhőbeli felhasználást, miután az összes kiosztási szabályt alkalmazta a saját költségeit. Például kiszámítja a költségeket címkék alapján, hozzárendeli a címkézetlen erőforrások költségét, és opcionálisan lefoglalja a fenntartott példányok kihasználtságát.

Az 360-es [Cost-foglalásban](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) beállított szabályzatok a Cost Analysis jelentésben szerepelnek, és az eredmények a felhőalapú gyártó nyers adataiból származó információkkal kombinálhatók.

Hogyan történik a jelentés kiszámítása? A Cloudyn szolgáltatás biztosítja az egyes csatolt fiókok integritásának megőrzését a _fiók affinitásának_alkalmazásával. Az affinitás gondoskodik arról, hogy egy adott szolgáltatást nem használó fiókhoz ne legyen kiosztva a szolgáltatás költségei. A fiókban felmerülő költségek ebben a fiókban maradnak, és nem számítanak ki a kiosztási szabályzatok alapján. Előfordulhat például, hogy öt csatolt fiókja van. Ha csak hárman használják a tárolási szolgáltatásokat, akkor a tárolási szolgáltatások díjait a rendszer csak a három fiókban található címkék között osztja ki.

A Cost Analysis jelentés használatával:

- Szervezeti jóváírás/költséghelyi visszacsatolási kiszámítása
- Az összes költség kategorizálása
- Megjeleníti a teljes központi telepítés összesített nézetét egy adott időkereten belül.
- A Cost modellben létrehozott házirendek alapján megtekintheti a költségeket kategóriánként.

A Cost Analysis jelentés használata:

1. Válasszon dátumtartományt.
2. Szükség szerint adjon hozzá címkéket.
3. Csoportok hozzáadása.
4. Válassza ki a korábban létrehozott Cost modellt.

### <a name="cost-over-time-report"></a>Időalapú költségek jelentése

Az idő múlásával jelentés a Cost foglalásának eredményeit jeleníti meg idősorozatként. Lehetővé teszi a trendek megfigyelését és a szabálytalanságok észlelését az üzemelő példányban. Alapvetően a meghatározott időszakban elosztott költségeket mutatja. A jelentés tartalmazza a főbb költségekkel járó közreműködőket, beleértve a folyamatos költségeket és az egyszeri fenntartott példányok díját, amelyeket a rendszer a kiválasztott időszakban töltött le. Ebben a jelentésben a 360-es [Cost-foglalásban](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) beállított szabályzatok használatosak.

A következő időpontra vonatkozó jelentés használata:

- A változások időbeli változásait láthatja, és ez hatással lehet egy nap (vagy dátumtartomány) változására a következőre.
- Egy adott példány költségeinek elemzése az idő múlásával.
- Megtudhatja, miért volt egy adott példányra vonatkozó költségmegtakarítás.

A díjszabási idő jelentés használata:

1. Válasszon dátumtartományt.
2. Szükség szerint adjon hozzá címkéket.
3. Csoportok hozzáadása.
4. Válassza ki a korábban létrehozott Cost modellt.
5. Válassza ki a tényleges költségeket vagy az elszámolt költségeket.
6. Adja meg, hogy szeretné-e alkalmazni a foglalási szabályokat a nyers számlázási adatnézet megtekintéséhez vagy az újraszámított Cost nézethez.

### <a name="actual-cost-analysis-report"></a>Tényleges Cost Analysis-jelentés

A tényleges költség-elemzési jelentés a szolgáltatói költségeket nem módosítva jeleníti meg. A legfontosabb közreműködőket, például a folyamatos költségeket és az egyszeri díjakat mutatja be.

A jelentés használatával megtekintheti az előfizetések költségadatok adatait. A jelentésben az Azure-előfizetések a **fiók neve** és a **Fiók száma mezőben**jelennek meg. A **társított fiókok** AWS-előfizetéseket mutatnak. Az előfizetések költségeinek megtekintéséhez az egyes fiókok részletezése csoportban **válassza**ki az előfizetés típusát.

A tényleges Cost Analysis-jelentés használatával:

- A megadott időkereten belül a nyers szolgáltató költségeinek elemzése és figyelése.
- Küszöbérték-riasztást ütemezhet.
- A fiókok és entitások által felmerült nem módosított költségek elemzése.

### <a name="actual-cost-over-time-report"></a>Tényleges bekerülési idő jelentés

A tényleges bekerülési idő jelentés egy standard Cost Analysis-jelentés, amely egy meghatározott időfelbontásra osztja fel a költségeket. A jelentés az idő múlásával megjeleníti a trendeket, és észleli a kiadási rendellenességeket. Ez a jelentés a főbb költségekkel járó közreműködőket tartalmazza, beleértve a folyamatos költségeket és az egyszeri fenntartott példányok díját, amelyeket a rendszer a kiválasztott időszakban töltött le.

Az idő múlásával jelentést a következőre használhatja:

- Tekintse meg a költséghatékonyságot az idő múlásával.
- A szabálytalanságok megkeresése a költségeket illetően.
- A felhőalapú szolgáltatókkal kapcsolatos összes kapcsolódó kérdés megkeresése.

### <a name="amortized-cost-reports"></a>Amortizációs Cost-jelentések

Az elszámolási költséggel kapcsolatos jelentések a lineárisan nem használatos szolgáltatási díjakat, vagy a költségek egyszeri megfizetését, valamint a költségeknek az élettartamuk során egyenletes elosztását mutatják be. Az egyszeri díjak például a következők lehetnek:

- Éves támogatási díjak
- Éves biztonsági összetevők díjai
- Fenntartott példányok beszerzési díjai
- Néhány Azure Marketplace-elem

A számlázási fájlban az egyszeri díjak akkor jelennek meg, ha a szolgáltatás felhasználásának kezdési és befejezési dátuma (timestamp) egyenlő értékkel rendelkezik. A Cloudyn szolgáltatás ezt követően az elszámolt egyszeri díjakat észleli. Az igény szerinti használati költségekkel rendelkező egyéb fogyasztáson alapuló szolgáltatásokat nem számoljuk el.

Az elszámolt költségadatok a következők:

- Amortizációs Cost Analysis
- Amortizációs díj az idő függvényében

### <a name="cost-analysis-report"></a>Költségelemzési jelentés

A Cost Analysis jelentés betekintést nyújt a Felhőbeli felhasználásra és a költségekre a kiválasztott időkereten belül. A Cost Analysis jelentésben a 360-es [foglalásban](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) beállított szabályzatok használatosak.

Hogyan számítja ki a Cloudyn a jelentést?

A Cloudyn biztosítja, hogy a foglalás megőrizze az egyes csatolt fiókok integritását a _fiók affinitásának_alkalmazásával. Az affinitás gondoskodik arról, hogy egy adott szolgáltatást nem használó fiókhoz ne legyen kiosztva a szolgáltatás költségei. A fiókban felmerülő költségek ebben a fiókban maradnak, és nem számítják ki a foglalási szabályzatok. Előfordulhat például, hogy öt csatolt fiókja van. Ha csak hárman használják a tárolási szolgáltatásokat, akkor a tárolási szolgáltatások díjait a rendszer csak a három fiókban található címkék között osztja ki.

A Cost Analysis jelentés használatával:

- Megjeleníti a teljes központi telepítés összesített nézetét egy adott időkereten belül.
- A Cost modellben létrehozott házirendek alapján megtekintheti a költségeket kategóriánként.

### <a name="cost-over-time-report"></a>Időalapú költségek jelentése

A költségek időbeli alakulása jelentés az idő múlásával megjeleníti a trendeket, és észleli az üzemelő példányok szabálytalanságait. Alapvetően a meghatározott időszakban elosztott költségeket mutatja. A jelentés tartalmazza a főbb költségekkel járó közreműködőket, beleértve a folyamatos költségeket és az egyszeri fenntartott példányok díját, amelyeket a rendszer a kiválasztott időszakban töltött le. Ebben a jelentésben a 360-es [Cost-foglalásban](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) beállított szabályzatok használatosak.

A következő időpontra vonatkozó jelentés használata:

- A változások időbeli változásait láthatja, és ez hatással lehet egy nap (vagy dátumtartomány) változására a következőre.
- Egy adott példány költségeinek elemzése az idő múlásával.
- Megtudhatja, miért volt egy adott példányra vonatkozó költségmegtakarítás.

### <a name="custom-charges-report"></a>Egyéni díjak jelentés

A vállalati és a CSP-felhasználók gyakran a saját Felhőbeli erőforrás-használat mellett további szolgáltatásokat biztosítanak a külső vagy belső ügyfelek számára. Egyéni díjakat határozhat meg a hozzáadott szolgáltatásokhoz, illetve az ügyfél számlázási vagy jóváírási jelentéseihez hozzáadott kedvezményekhez egyéni sorokként.

Az egyéni szolgáltatási díjak a számlán általában nem szereplő szolgáltatásokat tükrözik. A létrehozott egyéni díjak ezután megjelennek a Cost-jelentésekben.

Az *Egyéni díjak nem egyéni díjszabással*rendelkeznek. Az egyéni díjak listája nem jeleníti meg az esetlegesen megjelenő eltérő díjszabást. Például az AWS számlázási díjai ugyanúgy jelennek meg, mint a díjak.

Egyéni díj létrehozása:

1. Az **Egyéni költségek**területen kattintson az **új hozzáadása**lehetőségre. Megjelenik az _új egyéni díj hozzáadása_ párbeszédpanel.
2. A **szolgáltató neve**mezőben adja meg a szolgáltató nevét.
3. A **szolgáltatás neve**mezőben adja meg a szolgáltatás típusát.
4. A **Leírás**mezőben adja meg az egyéni díj leírását.
5. A **típus**mezőben adja meg a Select **százalék** elemet, majd a szolgáltatások legördülő menüben válassza ki azokat a szolgáltatásokat, amelyeket egyéni díjakként szeretne szerepeltetni a Cost-jelentésekben.
6. A **fizetés**lapon válassza ki, hogy a díj egyszeri díj vagy ismétlődő díj. Ha a díj ismétlődő díj, válassza az elszámolt lehetőséget, ha azt szeretné, hogy a díjat ki kell fizetni, és válassza ki a hónapok számát.
7. Ha egy egyszeri díj van kiválasztva, **a Date (** **tényleges dátum**) mezőben adja meg a díj kifizetésének dátumát. Ha az ismétlődő díj be van jelölve, adja meg a dátumtartományt, beleértve a kezdési dátumot és a díj befejezési dátumát.
8. Az **entitások fában**válassza ki azokat az entitásokat, amelyekre alkalmazni szeretné a díjat, majd **válassza a be lehetőséget.**

_Ha a díjakat egy entitáshoz rendeli, a felhasználók nem módosíthatják azokat. A rendszergazda által egy szülő entitáshoz hozzáadott díjak csak olvashatók._

Egyéni díjak megtekintése:

Az egyéni díjak a Cost-jelentésekben jelennek meg. Nyissa meg például a tényleges Cost Analysis jelentést, majd a **kiterjesztett szűrők**területen válassza az **önálló**lehetőséget. Ezután szűrje az **Egyéni díjak**megjelenítéséhez.

### <a name="cost-allocation-360"></a>Költségmegosztás 360

A Cost allokációs 360 használatával egyéni költség-lefoglalási modelleket hozhat létre a felhasznált Felhőbeli erőforrások költségeinek kiosztásához. Számos jelentés az egyéni Cost-modellekkel létrehozott egyéni Cost-modellekkel kapcsolatos információkat jeleníti meg. Egyes jelentések pedig csak azt követően jelenítik meg az adatokat, miután létrehozott egy egyéni Cost-modellt a Cost lefoglalásával.

Az egyéni Cost models létrehozásával kapcsolatos további információkért lásd [: oktatóanyag: költségek kezelése a Cloudyn használatával](tutorial-manage-costs.md).

### <a name="cost-vs-budget-over-time-report"></a>Költség és költségkeret időbeli alakulása jelentés

A költség és a költségkeret időbeli alakulása jelentés lehetővé teszi, hogy összehasonlítsa a fődíjas közreműködőket a költségvetésével szemben. A hozzárendelt költségvetés megjelenik a jelentésben, így az idő múlásával megtekintheti az (alatta/alatt/par) költségvetés-felhasználást. A jelentés tetején a mezők megjelenítése/elrejtése lehetőség kiválasztásával megtekintheti a költségeket, a költségvetést, a halmozott költségeket és a teljes költségvetést.

### <a name="current-month-projected-cost-report"></a>Aktuális havi tervezett Cost-jelentés

Az aktuális havi tervezett Cost-jelentés betekintést nyújt az aktuális havi díjszabási összefoglalóba. Ez a jelentés a hónap elejétől, az előző hónaptól és az aktuális hónap teljes tervezett költségével kapcsolatos költségeket jeleníti meg. Az aktuális havi tervezett költség a naprakész havi költség és a kivetítés összegét számítja ki az elmúlt 30 napban figyelt díjak alapján.

Az aktuális havi tervezett Cost-jelentés használata:

- Projekt havi költségei szolgáltatás szerint
- Projekt havi költségei fiók szerint

### <a name="annual-projected-cost-report"></a>Éves tervezett Cost-jelentés

Az éves tervezett költségek jelentés lehetővé teszi, hogy a korábbi kiadások trendjei alapján megtekintse az éves tervezett költségeket. A következő 12 hónapot mutatja a teljes előrejelzett költségek közül. A kivetítések a következő 12 hónap során kikövetkeztetett trend függvény használatával történnek, az utolsó 30 nap használati díja alapján.

### <a name="budget-management-settings"></a>Költségvetés-kezelési beállítások

A költségvetés-kezelés lehetővé teszi a pénzügyi év költségvetésének megadását.

Költségvetés hozzáadása egy entitáshoz:

1. A költségvetés kezelése lap **entitások**területén válassza ki azt az entitást, amelyben létre kívánja hozni a költségvetést.
2. A költségvetési évben válassza ki azt az évet, amelyben létre kívánja hozni a költségvetést.
3. Minden hónapban állítsa be a költségvetést, majd kattintson a **Save (Mentés**) gombra.

Fájl importálása az éves költségvetésbe:

1. A **műveletek**területen válassza az **Exportálás** lehetőséget egy üres CSV-sablon letöltéséhez, amelyet a költségvetés alapjaként kíván használni.
2. Töltse ki a CSV-fájlt a költségvetési bejegyzéseivel, és mentse helyileg.
3. A **műveletek**területen válassza az **Importálás**lehetőséget.
4. Válassza ki a mentett fájlt, majd kattintson **az OK**gombra.

Ha a kész költségvetést CSV-fájlként szeretné exportálni, a **műveletek**területen válassza az **Exportálás** lehetőséget a fájl letöltéséhez.

Ha elkészült, a költségkeret a Cost Analysis jelentéseiben és a költség és a költségvetés időbeli változása jelentésben jelenik meg. A jelentéseket a költségvetési küszöbértékek alapján is ütemezhet.

### <a name="azure-resource-explorer-report"></a>Azure Erőforrás-kezelő jelentés

A Azure Erőforrás-kezelő jelentés a Cloudyn elérhető Azure-erőforrások tömeges listáját jeleníti meg. A jelentés hatékony használatához az Azure-fiókoknak engedélyezve kell lennie a kiterjesztett mérőszámoknak. A kiterjesztett mérőszámok Cloudyn hozzáférést biztosítanak az Azure-beli virtuális gépekhez. További információ: [bővített mérőszámok hozzáadása az Azure Virtual Machines szolgáltatáshoz](azure-vm-extended-metrics.md).

### <a name="azure-resources-over-time-report"></a>Azure-erőforrások az idő múlásával jelentés

Az Azure-erőforrások az idő múlásával jelentés az adott időszakban futó összes erőforrás részletezését jeleníti meg. A jelentés hatékony használatához az Azure-fiókoknak engedélyezve kell lennie a kiterjesztett mérőszámoknak. A kiterjesztett mérőszámok Cloudyn hozzáférést biztosítanak az Azure-beli virtuális gépekhez. További információ: [bővített mérőszámok hozzáadása az Azure Virtual Machines szolgáltatáshoz](azure-vm-extended-metrics.md).

### <a name="instance-explorer-report"></a>Példány-kezelő jelentés

A példányok kezelője jelentés használatával megtekintheti a virtuális gépek eszközeinek különféle mérőszámait. Adott példányok részletezésével megtekintheti a következő információkat:
- Példány futó intervallumai
- Életciklus a kiválasztott időszakban
- CPU-kihasználtság
- Hálózati bemenet
- Kimeneti forgalom
- Aktív lemezek

A példány-kezelő jelentés a megadott dátumtartomány szerint gyűjti az összes futó intervallumot, és ennek megfelelően összesíti az adatokat. A dátumtartomány összes futó intervallumának megtekintéséhez bontsa ki a példányt. Az egyes példányok díjait a rendszer az AWS és az Azure-lista díjszabása alapján kiválasztott dátumtartomány szerint számítja ki. A rendszer nem alkalmaz kedvezményeket. További mezőket is hozzáadhat a jelentéshez a mezők megjelenítése/elrejtése mező használatával.

A példányok kezelője jelentés használata a következőhöz:

- A becsült költségek kiszámítása egy gépen.
- Hozzon létre egy teljes listát, beleértve az összesített órákat is, amelyek az adott időtartományban aktív összes gépről érvényesek.
- Hozzon létre egy listát a felhőalapú szolgáltató vagy fiók alapján.
- Megtekintheti az időtartományban létrehozott vagy leállított gépeket.
- Az összes jelenleg leállított gép megtekintése.
- Megtekintheti az egyes gépek címkéit.

### <a name="instances-over-time-report"></a>Példányok időbeli jelentés

A példányok az idő múlásával jelentés használatával megtekintheti a kijelölt időtartományban aktív gépek maximális számát. Ha a definiált felbontás hét vagy hónap, az eredmény az adott napon aktív gépek maximális száma az adott hónapban. Válasszon ki egy dátumtartományt a jelentésben megjeleníteni kívánt szűrők kiválasztásához.

### <a name="instance-utilization-over-time-report"></a>Példány kihasználtsága időbeli jelentéssel

Ez a jelentés a processzor vagy a memória részletezését jeleníti meg az összes példányra vonatkozóan az idő múlásával.

### <a name="compute-power-cost-over-time-report"></a>Számítási teljesítmény az idő függvényében jelentés

A számítási bekapcsolási idő jelentés a számítási teljesítmény részletezését jeleníti meg a megadott dátumtartomány szerint. Bár más jelentések a futó gépek számát vagy a futásidejű órákat mutatják, ez a jelentés az alapórákat, a számítási egység óráját és a GB RAM-ot jeleníti meg.

A jelentés használata:

- A számítási teljesítmény ellenőrzését a megadott dátumtartomány szerint.
- Számítási idő megtekintése a Cost foglalási modellek alapján.

Ez a jelentés a [Cost allokációs 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) -szabályzatokhoz van társítva, így az eredmények a kijelölt címkézés és szabályzatok alapján jelennek meg. Ha nem hozott létre szabályzatot, akkor az eredmények nem jelennek meg.

### <a name="compute-power-average-cost-over-time-report"></a>Számítási teljesítmény átlagos átlagköltsége – jelentés

Az idő függvényében a számítási teljesítmény átlagos díjait használva több, mint az egyes futó gépek költségeit tekintheti meg. A jelentésben az átlagos napi óradíj, a főóra, a számítási egység óra és a GB RAM óra érték látható. A jelentés betekintést nyújt az üzemelő példány hatékonyságára.

Ez a jelentés a [Cost allokációs 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) -szabályzatokhoz van csatolva, így az eredmények a megadott címkézés és szabályzatok alapján jelennek meg. Ha nem hozott létre szabályzatot, akkor az eredmények nem jelennek meg.

### <a name="s3-cost-over-time-report"></a>Az S3 költséghatékonysági jelentés

Az S3 költség az idő múlásával jelentés az Amazon Simple Storage Service (S3) költségeinek részletezését biztosítja a megadott időszakra vonatkozóan. A jelentés segítségével megtalálhatja azokat a gyűjtőket, amelyek a fő díjas illesztőprogramokat használják, és az S3-használat és a kiadások tendenciáit jeleníti meg.

### <a name="s3-distribution-of-cost-report"></a>A Cost-jelentés S3-eloszlása

A jelentés használatával elemezheti az előző hónapra vonatkozó S3-költségeket a gyűjtő és a tárolási osztály alapján. A tortadiagram nézetet a láthatósági küszöbérték beállítására használhatja. Vagy a táblázat nézetet is használhatja a részösszegek megjelenítéséhez.

### <a name="s3-bucket-properties-report"></a>Az S3-gyűjtő tulajdonságai jelentés

A jelentés használatával megtekintheti az S3-gyűjtő tulajdonságait. A tortadiagram nézetet a láthatósági küszöbérték beállítására használhatja. Vagy a táblázat nézetet is használhatja a részösszegek megjelenítéséhez.

### <a name="rds-instances-over-time-report"></a>RDS-példányok időbeli jelentés

A jelentés használatával megtekintheti az összes, a megadott időszak alatt futó, az összes Amazon-beli adatbázis-szolgáltatás (RDS) példányának részletezését.

### <a name="rds-active-instances-report"></a>RDS aktív példányok jelentése

A jelentés használatával elemezze az aktív RDS-példányokat. A jelentésben bontsa ki a sor elemet a további információk megtekintéséhez.

### <a name="azure-reserved-instances-report"></a>Azure Reserved instances-jelentés

Az Azure-beli fenntartott példányok jelentés az Azure-beli fenntartott példányok egyetlen nézetét jeleníti meg. Ez a jelentés az egyes vásárlásokat a saját soraként jeleníti meg. A jelentés a vásárlás részleteit is megjeleníti, például a megvásárolt fiókot, a vásárlás és a példány típusát, a hátralévő napokat és így tovább. Megjelenítheti vagy elrejtheti a jelentésekhez tartozó adatmezőket a mezők megjelenítése/elrejtése használatával.

Az Azure Reserved instances jelentés használatával megtekintheti a következőket:

- A foglalások listája a vásárlás dátuma szerint.
- Hátralévő idő, amíg az RI le nem jár.
- Egyszeri díj.
- Az a fiók, amely megvásárolta a RIs-t, és mikor.

### <a name="aws-reserved-instances-report"></a>AWS fenntartott példányok jelentése

Az AWS-beli fenntartott példányok jelentés egyetlen nézetet biztosít az AWS számára fenntartott példányokról. Ez a jelentés megjeleníti az egyes vásárlások saját sorát, valamint a vásárlás részleteit, például a megvásárolt fiókot, a vásárlás és a példány típusát, a hátralévő napokat és így tovább. Megjelenítheti vagy elrejtheti a jelentésekhez tartozó adatmezőket a mezők megjelenítése/elrejtése használatával.

Az AWS Reserved instances jelentés használatával megtekintheti a következőket:

- A foglalások listája a vásárlás dátuma szerint.
- Hátralévő idő, amíg az RI le nem jár.
- Egyszeri díj.
- Eredeti vásárlási azonosító (foglalási azonosító).
- A RIs-t és a-t megvásárló fiók.

### <a name="ec2-ri-buying-recommendations-report"></a>EC2 RI – vásárlási javaslatok jelentés

A Felhőbeli erőforrások felhasználásának alapja az igény szerinti modell, ahol az erőforrások csak a használat során járnak költséggel. Nincsenek előzetes kötelezettségvállalások – csak a ténylegesen használt funkciókért kell fizetnie.

Az AWS alternatív díjszabási modellt kínál a rugalmas számítási felhő (EC2) szolgáltatásaihoz, a fenntartott példányhoz (RI). Ez a díjszabási modell garantálja a felhasználók számára, hogy szükség esetén a kapacitást használják a RI időtartamra. Az RI jelentős árengedményt nyújt az igény szerinti díjszabáshoz képest. Cserébe a felhasználók előzetes kötelezettségvállalást tesznek a virtuális példányok használatára. A kötelezettségvállalás egy adott családra, méretre, rendelkezésre állási zónára (AZ) és az operációs rendszerre van kötve a kötelezettségvállalási időszakon (egy vagy három év). A RI lehetővé teszi, hogy az AWS hatékonyan tervezze meg a jövőbeli kapacitást, és megszerezze az ügyfelek elkötelezettségét a szolgáltatásai használatára.

Három fizetési lehetőség a RIs számára, amely mindenre kiterjed:

- Tömeges összeg a 0. napon, amely a legmagasabb kedvezményt kínálja
- Nincs előzetes verzió, amelyben a ri költségét havi telepítésekben kell fizetni, a legalacsonyabb árengedményt kínálva
- Részleges előzetes verzió, amelyben a díj 1/4-1/2, a többi pedig a havi telepítésekben, a teljes költség alacsonyabb, de lezárult, az összes kezdeti arányig

A Cloudyn az elmúlt 30 napban kiértékeli az egyes gépek üzemidőét. A Cloudyn javasolja a RIs megvásárlását, ha ez költséghatékonyabb, ha a gépet az aktuális üzemidő szintjén egy RI-vel szeretné futtatni.

A jelentés azt az indoklást mutatja be, hogy javaslatai alapján a legtöbb pénzt mentse az év során. A javaslatok az igény szerinti példányok a RIs szolgáltatással való cseréjét javasolták. A RIs-t közvetlenül a jelentésből is megvásárolhatja.

Minden lap teljes jelentésként nyílik meg. A lapok jelentős részei a következők:

- **EC2 ri – vásárlás hatása** – ez a szakasz az igény szerinti és fenntartott példányok közötti különbség szimulációját ismerteti. Kattintson a **Nagyítás**elemre, hogy megtekintse a javaslathoz már definiált szűrőket tartalmazó teljes EC2 ri vásárlási hatás jelentését. Ez a jelentés a lehetséges RI-vásárlások vásárlásának hatását mutatja. A EC2 fenntartott példányok megvásárlásakor megtekintheti a várható átlagos üzemidőt.

- **Elemzések mentése** – ez a szakasz az elért lehetséges megtakarításokat és a megtakarítások hónapját adja meg a Cloudyn ajánlásainak követése során. A tényleges megtakarítások és a mentett százalékok piros színnel vannak kiemelve.

- **EC2 ri-típus összehasonlítása** – ez a szakasz a Cloudyn javasolt központi telepítésének ROI-kiemeléseit emeli ki, beleértve az összes vonatkozó beállítást is. A jelentés eredményei feltételezik, hogy a gép 100%-os üzemidőt használ. Kattintson a **Nagyítás** gombra a részletes jelentés megnyitásához.

- **Példányok időbeli** megoszlása – ez a szakasz a javaslathoz, a OnDemand, a fenntartott példányokhoz és a helyhez társított összes példány részletezését jeleníti meg. Kattintson a **Nagyítás** gombra a részletes jelentés megnyitásához.
- **Ráfizetéses pontok** – ez a szakasz az összes lehetséges ajánlott telepítés és a ROI és a hónap közötti táblázatot jeleníti meg a ROI bekövetkeztekor. Kattintson a **Nagyítás** gombra a részletes jelentés megnyitásához.

### <a name="ec2-reservations-over-time-report"></a>EC2 foglalások időbeli jelentéssel

A EC2-foglalások időbeli jelentésével nyomon követheti a megvásárolt EC2-RIs-használat állapotát. Megadhatja a jelentés felbontását óra, nap vagy hét értékre.

A jelentés használata:

- A használatban lévő és nem használt foglalások megjelenítése.
- Ha óránként szeretné megtekinteni a felbontást, ellenőrizze az óra használatát.

### <a name="savings-over-time-report"></a>Megtakarítás az idő múlásával – jelentés

A megtakarítások időbeli jelentésének használatával megtekintheti a fenntartott példányok és a helyszíni példányok használatával elért megtakarításokat. A jelentés a RI-vásárlások eredményeként elért ROI-t jeleníti meg.

A RIs-megtakarítás megtekintéséhez csoportosítsa az eredményeket az **Ár modell** alapján, és válassza a **foglalás**lehetőséget. Egy adott fiók vagy példány típusával elért RI-megtakarítás megtekintéséhez vegye fel a megfelelő csoportosítást és szűrőt a fiók vagy a példány típusára.

Ha meg szeretné tekinteni a direktszínes példányok használatának megtakarításait, szűrje az **árat** a **helyszínen**. A jelentés alapértelmezett szűrője az RI és a Spot példányok.

### <a name="rds-ri-buying-recommendations-report"></a>Az RDS RI vásárlási javaslatainak jelentése

Az RDS RI vásárlási javaslatairól szóló jelentés azt ajánlja, hogy mikor használjon RDS-t az igény szerinti példányok helyett.

Minden lap teljes jelentésként nyílik meg. A lapok jelentős részei a következők:

- **RDS ri – vásárlási hatás** – ez a szakasz az igény szerinti és fenntartott példányok közötti különbség szimulációját tartalmazza. Kattintson a nagyítás lehetőségre, hogy a teljes RDS RI-vásárlási Impact **-** jelentés a javaslathoz már meghatározott szűrőkkel legyen látható. Ez a jelentés lehetővé teszi, hogy megtekintse a potenciális RI-vásárlások vásárlásának hatását.  Megváltoztathatja a várható átlagos üzemidőt, és megtekintheti a lehetséges mentést a RIs megvásárlásával.
- **Elemzés mentése** – ez a szakasz az elért lehetséges megtakarításokat és a megtakarítások hónapját adja meg a Cloudyn ajánlásainak követése során. A tényleges megtakarítások és a mentett százalékok piros színnel vannak kiemelve.

- **RDS ri-típusok összehasonlítása** – ez a szakasz a javasolt központi telepítés ROI-kiemeléseit emeli ki, beleértve az összes vonatkozó beállítást is. A jelentés eredményei feltételezik, hogy a gép 100%-os üzemidőt használ. Kattintson a **Nagyítás** gombra a kiválasztott gép részletes jelentésének megnyitásához.
- **Példányok az idő múlásával** – ez a szakasz a javaslathoz, a OnDemand, a fenntartott példányokhoz és a spothoz társított összes példány részletezését jeleníti meg. Kattintson a **Nagyítás** gombra a részletes jelentés megnyitásához.

- **Ráfizetéses-pontok** – ebben a szakaszban az összes lehetséges ajánlott telepítés és a ROI és a havi megtérülés táblázata látható. Kattintson a **Nagyítás** gombra a részletes jelentés megnyitásához.

### <a name="rds-reservations-over-time-report"></a>RDS-foglalások időbeli jelentéssel

Az RDS-foglalás időbeli jelentés használatával megtekintheti a felhasznált és a nem használt foglalások lebontását a megadott időszakban.

### <a name="reserved-instance-purchase-impact-report"></a>Fenntartott példányok vásárlásának hatása jelentés

A EC2 RI vásárlási hatása jelentés lehetővé teszi, hogy a fenntartott példányok költségeit és az igény szerinti költségeket is szimulálja az idő függvényében. A megoldással jobb vásárlási döntéseket hozhat. Állítsa be a szűrőket, például az átlagos futtatókörnyezetet, a kifejezést, a platformot és másokat, hogy megalapozott döntéseket hozzon az RI-vásárlások során.

### <a name="cost-effective-sizing-recommendations-report"></a>Költséghatékony méretezési javaslatok jelentés

A költséghatékony méretezési javaslatok jelentés az AWS és az Azure eredményeit biztosítja. Az AWS-felhasználók esetében az RI-vásárlásokat figyelembe kell venni, és az eredmények nem tartalmazzák a RI-ként futó gépeket. Ez a jelentés a kiépíthető kihasználatlan példányok listáját tartalmazza. A javaslatok az elmúlt 30 napban használt használati és teljesítményadatokat alapulnak. Minden javaslatban szerepelnek a jelöltek listája, amelyek az előállításhoz szükségesek, valamint egy hivatkozás, amely a példány teljes részleteit és teljesítmény-metrikáit jeleníti meg. Továbbá, ha a vonatkozó javaslatok az újabb generációs példányok típusaira való váltásra vonatkoznak.

Nem töltheti le a jelentésből az ajánlott példány-azonosítók listáját. A példány-azonosítók letöltéséhez használja a minden méretezési javaslat jelentést.

Vegye figyelembe a következő leépítési példát:

Hat m3. XLarge fut. A Cloudyn elemzése azt mutatja, hogy öt közülük kevés a CPU kihasználtsága. Érdemes lehet leépíteni őket.

A Cost Impact kiszámítja a költségeket. Ebben a példában a sor elem kibővítésével megtekintheti egy m3. XLarge-példány (Linux/Unix) díjait $0,266/óra és egy m3. nagyméretű példány (Linux/Unix) díja $0,133/óra. Így az éves díj $11 651 öt m3. a XLarge példányok 100%-os kihasználtsággal futnak. Az éves díj $5 825 5 m3. a nagy példányok 100%-os kihasználtsággal működnek. A lehetséges megtakarítás $5 825.

A költséghatékony méretezési indoklások megtekintéséhez kattintson a + elemre a vonal kibontásához. **Részletek**:

- Az **ajánlás indoklása** szakasz a jelenlegi központi telepítést és a javasolt példányok számát jeleníti meg.
- A **Cost Impact** szakasz a lehetséges megtakarítások meghatározására szolgáló számítást jeleníti meg.
- A **lehetséges éves megtakarítások** szakasz a Cloudyn javaslata során felhasználható éves megtakarításokat jeleníti meg.

### <a name="all-sizing-recommendations-report"></a>Az összes méretezési javaslat jelentés

Ez a jelentés a kiépíthető kihasználatlan példányok listáját tartalmazza. A javaslatok az elmúlt 30 napban használt használati és teljesítményadatokat alapulnak. Minden javaslatban megtekintheti a példány teljes részleteit és teljesítmény-mérőszámait.

Ha AWS fenntartott példányokat vásárolt, ez a jelentés az összes futó példány eredményét tartalmazza, beleértve a RIs-ként futó példányokat is.

A minden méretezési javaslat jelentés a következőre használható:

- Tekintse meg az összes olyan példány listáját, amely a felépítésre jelöltek.
- A példányok nevét és azonosítóit tartalmazó jelentések listájának exportálása.

Egy adott példányra vonatkozó javaslat részleteinek megtekintéséhez kattintson a **+** elemre a részletek kibontásához. A javaslat részletei szakasz áttekintést nyújt a javaslatról.

A **címkék** szakasz a kijelölt példányhoz tartozó címke kulcsainak és értékeinek listáját tartalmazza. A bal oldali panelen található címkék használatával szűrheti a szakaszt.

A **CPU-kihasználtság** szakasz a példány CPU-kihasználtságát adja meg az elmúlt hónapban, naponta.

Kattintson a diagramra a részletezéshez, majd nyissa meg a példány CPU-t az idő függvényében, és tekintse meg a példányok részletezését.

- Mezők **megjelenítése/elrejtése mezők** hozzáadásához és eltávolításához használja a következő mezőket: timestamp, AVG CPU, minimális CPU, Max CPU.
- Dátumtartomány **használata dátum** -és dátumtartomány megadásához, valamint részletezés egy adott InstanceId.
- **Kibővített szűrők** használata az összes vagy egy adott példány azonosítójának megjelenítéséhez
- A CPU-kihasználtsági jelentés megnyitásához kattintson a **Nagyítás** gombra.

Ha a példány 30 napig nem lett figyelve, a hiányos adatértékek jelennek meg.

A **memória kihasználtsága (GB)** szakasz a felhasznált memóriával kapcsolatos információkat tartalmazza. Az AWS-felhasználók esetében a memória metrikái nem lesznek automatikusan elérhetők, és az AWS-en keresztül hozzá kell őket adni egy példányhoz. Az AWS a EC2-példányok számára lehetővé teszi a memória metrikáinak engedélyezését.

A **memória kihasználtsága (%)** szakasz a felhasznált memória százalékos arányát jeleníti meg.

A **hálózati bemeneti forgalom** szakasz a kiválasztott példány hálózati forgalmának, átlagának és maximális számának időpontját jeleníti meg. Vigye a kurzort a sorok fölé, hogy megtekintse az adott idő dátumát és maximális forgalmát. Kattintson a **Nagyítás** gombra a hálózati bemeneti forgalom jelentésének megnyitásához.

A **hálózati kimeneti forgalom** szakasz a kiválasztott példány hálózati kimeneti forgalmának pillanatképét jeleníti meg. Vigye a kurzort a sorok fölé, hogy megtekintse az adott idő dátumát és maximális forgalmát. Kattintson a **Nagyítás** gombra a hálózati kimeneti forgalom jelentésének megnyitásához.

### <a name="instance-metrics-explorer-report"></a>Példány Metrikaböngésző jelentés

A példány Metrikaböngésző jelentés a több felhőből származó teljesítmény-metrikákat jeleníti meg. A jelentés használatával megtekintheti a CPU-, memória-és hálózati metrikai küszöbértékeken alapuló vagy alá felhasználható példányokat.

A Felhőbeli teljesítmény megtekinthető egy példányon:

1. A **dátumtartomány**mezőben válasszon ki egy dátumtartományt, amelynek a teljesítményét meg szeretné tekinteni.
2. A **címkék**területen válassza ki a megtekinteni kívánt címkéket.
3. A **szűrők**területen válassza ki a jelentésben megjeleníteni kívánt szűrőket.
4. A **kiterjesztett szűrők**területen módosítsa a jelentés küszöbértékeit a következőre:
    - Átlagos CPU
    - Maximális CPU
    - Átlagos memória
    - Maximális memória
5. A **kiterjesztett szűrők** **területen kattintson a Megjelenítés** elemre, majd válassza ki a megjelenítendő példányok típusát.

Egy adott példány metrikáinak időbeli megjelenítése:

- Nyissa meg a példány Metrikaböngésző jelentést, és kattintson az **+** elemre a részletek megtekintéséhez.

### <a name="rds-sizing-recommendations-report"></a>RDS-méretezési javaslatok jelentés

Az RDS-méretezési javaslatok jelentés az RDS méretezési javaslatokat nyújt a felhő használatának optimalizálásához. Megjeleníti a kihasználható példányok listáját. A Cloudyn javaslatok az elmúlt 30 nap használati és teljesítmény-adatain alapulnak. A javaslatokat a fiók neve, a régió, a példány típusa és az állapot alapján szűrheti.

### <a name="sizing-threshold-manager-report"></a>Méretezési küszöbérték-kezelő jelentés

A Cloudyn beépített méretezési javaslatai egy összetett algoritmus használatával lettek kiszámítva, hogy pontos méretezési javaslatokat nyújtsanak. Módosíthatja a küszöbértékeket a javaslatok lecsökkentése céljából.

A küszöbérték-méretezési javaslatok manuális módosítása:

1. A méretezési küszöb kezelőjében állítsa be a következő küszöbértékeket, ahogy szeretné:
    - Átlagos CPU-érték (%)
    - Maximális CPU-érték (%)
    - Átlagos memória (%)
    - Maximális memória (%)
3. A módosítások mentéséhez kattintson az **alkalmaz** gombra.
4. A módosítások azonnal érvényesek az összes javaslatra.

Az alapértelmezett küszöbértékek visszaállítása:

- A méretezési küszöb kezelőjében kattintson az **Alapértelmezések visszaállítása**elemre.

### <a name="compute-instance-types-report"></a>Számítási példányok típusai jelentés

A példányok típusai jelentés használatával:

- A példány típusait a szolgáltatás, a család, az API-név és a név alapján tekintheti meg.
- Megtekintheti a CPU, az ECU, a RAM és a hálózat adatait.

A **kereséssel** konkrét sorok is megkereshetők.

## <a name="next-steps"></a>Következő lépések

- További információ a jelentések használatáról, többek között a jelentések testreszabásáról és mentéséről, valamint az ütemezett jelentésekről: a [Cost-jelentések ismertetése](understanding-cost-reports.md).
- Ismerkedjen meg a Cloudyn-ben található irányítópultokkal, és arról, hogyan hozhat létre saját egyéni irányítópultokat, lásd: [Key Cost-mérőszámok megtekintése az irányítópultokkal](dashboards.md).
