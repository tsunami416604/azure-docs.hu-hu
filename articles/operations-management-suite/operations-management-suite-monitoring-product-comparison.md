---
title: "A Microsoft monitoring termék összehasonlítása |} Microsoft Docs"
description: "A Microsoft Operations Management Suite (OMS), a Microsoft felhő alapú informatikai felügyeleti megoldás, amely segít a kezelése és védelme a helyszíni és felhőalapú infrastruktúra.  Ez a cikk azonosítja az OMS különböző szolgáltatásait, és a részletes tartalmukra mutató hivatkozásokat tartalmaz."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: a63ca0ad-61f8-425d-a48c-d87ba518c104
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren
ms.openlocfilehash: b4201f105a87b0a41059c061eb37fb35d4514e02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-monitoring-product-comparison"></a>A Microsoft figyelési termék összehasonlítása
Ez a cikk ismerteti a System Center Operations Manager (SCOM) és az Operations Management Suite (OMS) szolgáltatáshoz összehasonlítása architektúráját, logikája hogyan azok megfigyelje az erőforrásokat, és hogyan működnek a gyűjtött adatok elemzése .  Ez az Önnek egy alapvető ismeretekkel rendelkezik a relatív szintjeiről és különbségeket.  

## <a name="basic-architecture"></a>Alapszintű architektúrája
### <a name="system-center-operations-manager"></a>System Center Operations Manager
Minden SCOM ezeket az összetevőket az Adatközpont.  [Van telepített ügynök](http://technet.microsoft.com/library/hh551142.aspx) SCOM által kezelt Windows és Linux rendszerű gépekre.  Ügynökök csatlakozni [felügyeleti kiszolgálók](https://technet.microsoft.com/library/hh301922.aspx) amely kommunikálni az SCOM-adatbázis és az adatraktárról.  Ügynökök felügyeleti kiszolgálókhoz való kapcsolódáshoz tartományi hitelesítéshez támaszkodnak.  Azokat a megbízható tartományon kívüli tanúsítványt hitelesítést végezni, vagy csatlakozzon egy [átjárókiszolgáló](https://technet.microsoft.com/library/hh212823.aspx).

SCOM két SQL-adatbázis, a működési adatok egyet, majd egy másik az adatraktár jelentéskészítésre és adatelemzésre támogatásához szükséges.  A [jelentéskészítő kiszolgáló](https://technet.microsoft.com/library/hh298611.aspx) fut az SQL Reporting Services jelentés az adatokat az adatraktárból. 

SCOM figyelheti a felhőalapú erőforrásokat, például a felügyeleti csomagok használatát termékek [Azure](https://www.microsoft.com/download/details.aspx?id=38414), [Office 365](https://www.microsoft.com/download/details.aspx?id=43708), és [AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html).  Ezek a felügyeleti csomagok egy vagy több helyi ügynök használata a teljesítmény és rendelkezésre állási a proxyk felderítő felhőalapú erőforrásokat és a futó munkafolyamatra.  Proxy ügynökök megtalálhatók használt [hálózati eszközök figyelése](https://technet.microsoft.com/library/hh212935.aspx) és az egyéb külső erőforrásokhoz.

Az operatív konzol egy Windows-alkalmazás, amely a felügyeleti kiszolgálóhoz csatlakozik, és lehetővé teszi, hogy a rendszergazda megtekintése és elemzése a gyűjtött adatokat és az SCOM-környezet konfigurálása.  Egy webalapú konzol minden olyan IIS-kiszolgálón az alábbiakon tárolható, és böngészőn keresztül adatelemzés biztosít.

![SCOM-architektúra](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Log Analytics
A legtöbb OMS összetevői Azure felhőben, telepítheti és minimális költségek és erőfeszítések felügyeleti kezeli azt.  A Naplóelemzési által gyűjtött összes adat az OMS-tárház tárolja.

A Naplóelemzési három forrásokból származó gyűjthet adatokat:

* Fizikai és a Windows rendszerű virtuális gépek és a [Microsoft Monitoring Agent (MMA)](https://technet.microsoft.com/library/mt484108.aspx) vagy a Linux és a [Operations Management Suite-ügynök Linux](https://technet.microsoft.com/library/mt622052.aspx).  Ezek a gépek lehetnek a helyszíni vagy a virtuális gépek az Azure rendszerben vagy egy másik felhőben.
* Az Azure Storage-fiók [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) Azure feldolgozói szerepkörét, a webes szerepkör vagy a virtuális gép által összegyűjtött adatok.
* [A SCOM felügyeleti csoport kapcsolata](https://technet.microsoft.com/library/mt484104.aspx).  Ebben a konfigurációban az ügynökök kommunikálni az SCOM felügyeleti kiszolgálóhoz, amely az adatok biztosításához az SCOM-adatbázisba, ahol, majd kézbesítve lesz az OMS-adattár.
  A rendszergazdák összegyűjtött adatok elemzése és Naplóelemzési konfigurálása az OMS-portállal, amely az Azure szolgáltatásban üzemeltetett, és minden böngészőből elérhető.  Ezek az adatok eléréséhez mobilalkalmazások a szabványos platformokhoz érhetők el.

![Napló Analytics architektúrája](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>SCOM és Naplóelemzési integrációja
Ha SCOM adatforrásaként használja a Naplóelemzési kihasználható a a két terméknek egy hibrid környezet figyelését.  Beállíthatja a meglévő SCOM ügynökök mellett az SCOM felügyeleti csomagok futtatásának folytatását OMS-ben, kezelje az operatív konzolon keresztül.  
Egy csatlakoztatott SCOM felügyeleti csoport adatait a rendszer Naplóelemzési négy módszerek egyikének használatával:

* Az ügynök által gyűjtött és az SCOM-i események és teljesítményadatok.  Felügyeleti kiszolgálók scom adja át az adatokat szolgáltatáshoz.
* Egyes események, például az IIS-napló és a biztonsági események továbbra is kézbesítendő közvetlenül Naplóelemzési az ügynöktől.
* Néhány további szoftvereket telepíthet az ügynök, vagy a további adatok gyűjtéséért felelős ügyfélfeladatot szoftver telepítését igényli.  Ezeket az adatokat általában kapnak közvetlenül Naplóelemzési.
* Néhány megoldás közvetlenül az SCOM felügyeleti kiszolgálókat, amelyek nem származik a ügynök gyűjtenek adatokat.  Például a [Riasztáskezelési megoldás](https://technet.microsoft.com/library/mt484092.aspx) riasztásokat gyűjtő az SCOM való létrehozás után.

## <a name="monitoring-logic"></a>Figyelési logika
SCOM és Naplóelemzési hasonló, az ügynökök gyűjtött adatok, de hogyan azok határozza meg, és valósítja meg az adatgyűjtésre a programot, és hogyan azok elemzése a gyűjtött adatok alapvető eltérések vannak.

### <a name="operations-manager"></a>Operations Manager
Figyelési logika SCOM meg van valósítva a [felügyeleti csomagok](https://technet.microsoft.com/library/hh457558.aspx) való feltérképezéséhez, figyeléséhez, összetevők programot tartalmazó, a állapotának ezen összetevők, valamint elemezheti adatait.  Figyelési adatok más dolga, mint az esemény- vagy teljesítményszámláló gyűjtése, vagy azt egy parancsfájl megvalósított komplex logikai használhat.  Felügyeleti csomagok, amelyek tartalmaznak teljes figyelési érhetők el a különböző [Microsoft és harmadik féltől származó alkalmazások](http://go.microsoft.com/fwlink/?LinkId=82105) hardver- és hálózati eszközök mellett.  Is [saját felügyeleti csomagok szerzői](http://aka.ms/mpauthor) egyéni alkalmazások.

Felügyeleti csomagok tartalmazhatnak több munkafolyamatot, hogy minden egyes például teljesítményszámláló mintavételi, a szolgáltatás állapotának ellenőrzése vagy parancsprogram futtatása különböző figyelési funkciót hajt végre.  Minden munkafolyamat függetlenül fut, és határozza meg, melyik adatbázis például azt fogja írni a saját eredmények és, hogy riasztást hoz létre. 

Ha szeretné felülbírálni az munkafolyamat például a gyakoriság futnak, a küszöbérték, ahol ítélik hiba részletes adatait, és azok hoznak létre a riasztás súlyossága.  További funkciók is megadhatja a saját munkafolyamatokat hozzáadásával.

![Felülbírálások](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

Felügyeleti csomagok vannak telepítve az Operations Manager adatbázisában, és automatikusan terjesztve ügynökök felügyeleti kiszolgálók.  Minden ügynök automatikusan töltse le a felügyeleti csomagok és munkafolyamatok megfelelő a telepített alkalmazások betöltése.  Az ügynök által gyűjtött adatokat a rendszer a felügyeleti kiszolgálóra való az SCOM-adatbázis és az adatraktárról.  Az operatív konzol lehetővé teszi, hogy megtekintheti és egyéni nézetek, irányítópultokat és jelentéseket a felügyeleti csomag tartalmazza az adatok elemzéséhez.

A felügyeleti csomagok eloszlását mutatja be az alábbi ábrán.

![Felügyeleti csomag folyamata](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Log Analytics
#### <a name="event-and-performance-collection"></a>Esemény- és teljesítménynaplózás
A Naplóelemzési gyűjti össze az események és teljesítményszámlálók ügynök rendszerekről adatforrások, például Windows-Eseménynapló, az IIS-napló és a Syslog.  Megadhatja a feltételeket, amelynek adatok gyűjtése a Log Analytics-portálon keresztül és az összegyűjtött adatok elemzésére majd napló lekérdezéseket.  Szabványos feltételek csoportja van definiálva az OMS-munkaterület létrehozásakor, és bizonyos alkalmazásokhoz további adatokat adhat meg. 

![A Naplóelemzési Eseménynapló meghatározása](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

SCOM munkafolyamatai sok részletes általában az adatok és a válaszban végrehajtandó művelet meghatározott feltételek megadása, amíg a Naplóelemzési rendelkezik adatgyűjtés az általános feltételek.  Napló lekérdezések és a megoldások adjon meg több célként megadott feltételek elemzése és adott adatok a felhőben működő után van.

#### <a name="solutions"></a>Megoldások
Megoldás további logikát biztosítja az adatok gyűjtése és elemzése.  Az OMS-előfizetés hozzáadása a megoldás gyűjteményből megoldások választhatja ki.

![Megoldások gyűjteménye](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

Megoldások elsősorban az események és teljesítményszámlálók gyűjtése az OMS-tárházban adatelemzési lehetőségeket biztosít a felhőben fut.  Akkor is határozhatnak meg további adatokat gyűjteni, amelyek a napló lekérdezésekkel vagy az OMS-irányítópulton a megoldás által biztosított további felhasználói felület elemzése. 

Például a [változáskövetési megoldás](https://technet.microsoft.com/library/mt484099.aspx) konfigurációs ügynök rendszereken változik, és írja az eseményeket, amelyek több grafikus nézetek, hogy összesítse a elemzése OMS-tárházba talált változást észlel.  Részletezve be a részletes, a megoldás által gyűjtött adatokat megjelenítő napló lekérdezések összesített nézetből.

Kiválaszthatja, hogy milyen megoldások hozzáadása az előfizetéshez, amíg képes létrehozni a saját megoldások jelenleg nem rendelkezik.  Kiválaszthatja az események és teljesítményszámlálók gyűjtése és a saját napló lekérdezések alapuló egyéni nézetek létrehozása.

A figyelési logikát a Log Analyticshez összegzése az alábbi diagram szemlélteti.

![Napló elemzési megoldások folyamata](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>Állapotfigyelés
### <a name="operations-manager"></a>Operations Manager
SCOM minta az alkalmazás összetevői, és adjon meg egy valós idejű állapotát az egyes.  Ez lehetővé teszi nem csak a nézet észlelt hibákat, és a teljesítmény adott idő alatt, hanem az adott időpontban egy alkalmazás vagy a rendszer aktuális állapotát, és annak összetevői érvényesítéséhez.  Az időszakot, amely egy adott alkalmazás támogatja, mert a scom állapotának motor is támogatja a szolgáltatási szint szerződés (SLA) elemzése és a jelentés az adott idő alatt az alkalmazás rendelkezésre állásának.

Az alábbi nézetben például SQL-adatbázis motorok SCOM által figyelt valós idejű állapotát jeleníti meg.  Az adatbázis-értelmezők száma legalább egy adatbázis állapotának jelenik meg a alsó fele a nézetet.

![Állapot nézet](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

Egy, az adatbázis-értelmezők száma az állapotkezelő a figyelők, amelyek általános állapota határozza meg az alább látható.  Ezek a figyelők az SQL felügyeleti csomagban definiált, és minden SQL adatbázis motor SCOM által felderített futtatni.

![Az állapotkezelő](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

Több rendszer-összetevőket mérését egy elosztott alkalmazás állapotának egyesíthetők.  Ez különösen hasznos, ha több elosztott összetevőket tartalmazó üzleti alkalmazások lehet.  Az alkalmazás rendelkezésre állásának a modell, amely az egyes összetevők állapotát méri, hogy az összesítő hozhat létre.

Az Active Directory több felügyeleti csomagot, amely az elosztott összetevőinek elemzése modell példája.  A minta az alábbi ábrán a környezetben, és erdők, tartományok és a tartományvezérlők közötti kapcsolat állapotát jeleníti meg.  Alösszetevők és a fenti SQL példához hasonló több monitor ezeket az összetevőket tartalmaz.

![SCOM-diagram nézet](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Log Analytics
OMS nem tartalmazza a modell alkalmazások közös motort, és mérheti a valós idejű állapotát.  Egyes megoldások is fel a gyűjtött adatok alapján adott szolgáltatások általános állapotát, és előfordulhat, hogy valós idejű elemzés végrehajtásához az ügynök telepítése egyéni logika.  Az megoldások futnak a hozzáférés a felhőben, az OMS-tárházba, azok gyakran megadhatják mélyebb elemzés, mint a felügyeleti csomagok jellemzően történik. 

Például a [AD értékelési és SQL-értékelési megoldás](https://technet.microsoft.com/library/mt484102.aspx) összegyűjtött adatok elemzése, és adja meg a különböző szempontjairól a környezet besorolását.  Ez magában foglalja a javaslatok javításai, beállíthatja, hogy a rendelkezésre állás és a környezet teljesítményének javítása érdekében.

![AD-értékelési megoldás](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>Adatok elemzése
SCOM és a Naplóelemzési minden gyűjtött adatok elemzésére különböző funkciók biztosítására.  SCOM rendelkezik nézeteket és irányítópultokat formátumokat és az adatokat az adatraktárból táblázatos formában jelentések számos legutóbbi adatok elemzése az operatív konzolon.  A Naplóelemzési teljes napló lekérdezési nyelv és felületet biztosít az OMS-tárházban található adatok elemzéséhez.  SCOM Naplóelemzési használja forrásként, ha a tárház tartalmazza az adatokat, SCOM által gyűjtött, hogy a Naplóelemzési eszközök is használható legyen, mindkét rendszer adatok elemzését.

### <a name="operations-manager"></a>Operations Manager
#### <a name="views"></a>Nézetek
Nézetek az operatív konzolon lehetővé teszi, hogy különböző adattípusokkal SCOM által összegyűjtött események, a riasztások és állapotadatok, általában táblázatos különböző formátumokban és vonaldiagramok teljesítményadatokhoz.  Nézetek minimális elemző vagy az adatok összevonása, de engedélyezi, hogy bizonyos feltételek szerint szűrheti. 

![Nézetek](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

Felügyeleti csomagok jellemzően ad meg az alkalmazás vagy az általa figyelt system támogató több nézetet.  Ide tartozhat a különböző objektumok, amely felderíti a felügyeleti csomag, riasztás az észlelt problémákat, és nézeteket teljesítmény számlálók állapotnézetek.

Nézetek olyan alkalmas elemzése a jelenlegi állapotában a környezet, beleértve a nyitott riasztások és a figyelt rendszerek és objektumok állapotát.  Részletes esemény vagy egy adott riasztás támogató ahhoz, hogy az alapvető ok diagnosztizálása teljesítményadatokat is lebontva. Hasonlóképpen megtekintheti a teljesítmény- és állapot felmérheti az aktuális állapotát az alkalmazások különböző összetevők.

#### <a name="dashboards"></a>Irányítópultok
Az operatív konzolon irányítópultok elsősorban ilyen módon működik nézetek azonban több testre szabhatók, és részletesebb képi megjelenítések is tartalmazhat.  Standard irányítópultok készlete érhetők el, hogy könnyen teste szabhatja a saját célokra.  Egy PowerShell-lekérdezés által visszaadott adatokat megjelenítő widgetet PowerShell is használható.

![Irányítópult](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

A fejlesztők is képes egyéni összetevők hozzáadása irányítópultok tartoznak azok a felügyeleti csomagokban.  Ezek lehetséges, hogy lehet magas kifejezetten egy adott alkalmazásra, például az irányítópulton a lent látható módon SQL felügyeleti csomagban.  Ezt az irányítópultot is használható sablonként egyéni verzióihoz.

![SQL-irányítópult](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>Jelentések
Jelentéseket a SCOM elemezheti az adatokat az adatraktárból táblázatos formában.  Ugyanakkor nyomtatott és automatizált PDF, a fürt megosztott kötetei szolgáltatás és a Word különböző fájlformátumokban ütemezve.  Jelentések működnek az adatraktár adataival, különösen akkor megfelelőek a hosszú távú folyamatok elemzése.

Felügyeleti csomagok egyéni jelentések általában biztosít egy adott alkalmazás.  A szalagtár általános jelentések testre szabható a saját alkalmazásai vagy alkalmi elemzések végrehajtását is választhat.

Az alábbiakban látható egy minta rendszerteljesítmény-jelentés, az Active Directory felügyeleti csomag által gyűjtött adatok jelennek meg.

![Jelentés](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Log Analytics
A Naplóelemzési rendelkezik egy [lekérdezési nyelv](https://technet.microsoft.com/library/mt484120.aspx) használható keresztül nélkül kell létrehozni egy egyéni nézet vagy a jelentés több alkalmazás adatait elemzés végrehajtásához.  OMS meg van valósítva a felhőben, mert a lekérdezések és adatelemzés bármely hardver korlátozásai nem lépnek, és gyorsan elemezheti a lekérdezések, beleértve a több millió rekordot. 

A Naplóelemzési lekérdezések is egyéb funkciók alapját képezi.  Lekérdezés mentése, az eredmények exportálása az Excelbe vagy automatikusan futtatása rendszeres időközönként, és az eredményeket az adott feltételeknek megfelelő riasztást hoz létre.  

![Napló lekérdezés folyamata](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

Az alábbiakban van például Log Analytics-lekérdezés.  Ebben a példában a "started" a név összes eseményt adott vissza, és esemény szerint csoportosítva azonosítóját.  A felhasználó egyszerűen a lekérdezés és biztosít Naplóelemzési dinamikusan állít elő, a felhasználói felület az elemzés végrehajtásához.  Bármely elem listából történő kiválasztásakor az események részletes adatokat ad vissza.

![Napló-lekérdezés](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

Alkalmi adatelemzési lehetőségeket biztosít, valamint a Naplóelemzési lekérdezések későbbi felhasználás céljából mentve és szintén hozzáadott a [OMS irányítópult](http://technet.microsoft.com/library/mt484090.aspx) a következő példában látható módon.

![OMS irányítópult](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>Következő lépések
* Telepítése [a System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh205987.aspx).
* Regisztráljon [Naplóelemzési](https://azure.microsoft.com/documentation/services/log-analytics).  

