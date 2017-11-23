---
title: "Az Azure Operational biztonsági |} Microsoft Docs"
description: "További tudnivalók a Microsoft Operations Management Suite (OMS), a szolgáltatások és annak működéséről."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 8528eeb4eac8397beaa3d0dca37ce9eb33167b32
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="azure-operational-security"></a>Az Azure Operational biztonsági
## <a name="introduction"></a>Bevezetés

### <a name="overview"></a>Áttekintés
Tudjuk, hogy biztonsági-e a feladat a felhőben, és hogy mennyire fontos, hogy található-e az Azure biztonsági pontos és időben információt. A legjobb okai az alkalmazások és szolgáltatások Azure használandó, a széles köréről biztonsági eszközöket és lehetőségeinek kihasználásához. Ezekkel az eszközökkel és képességek segítségével történő biztonságos megoldásokat keres a biztonságos Azure platformon. Windows Azure biztosítása átlátszó elszámolási kötelezettségéről szóló biztosítania kell titkosítás, integritás és felhasználói adatok rendelkezésre állását.

Jobb megértése érdekében Microsoft működési szempontok, ez a dokumentum, "Azure működési biztonság" nevével, amely, és mind az ügyfél a Microsoft Azure belül végrehajtani a biztonsági beállítások a tömb ügyfelek biztosít olyan átfogó Tekintse meg a működési biztonság érhető el a Windows Azure-ban.

### <a name="azure-platform"></a>Az Azure Platform
Azure egy nyilvános felhőszolgáltatási platform, amely támogatja az operációs rendszerek, programozási nyelvek, keretrendszerek, eszközök, adatbázisok és eszközök széles körű kijelölés. Linux-tárolók futtatható a Docker integrációja; alkalmazások, JavaScript, Python, .NET, PHP, Java és Node.js; build vissza-végpontok iOS, Android és Windows eszközökhöz. Azure Cloud service támogatja a fejlesztők milliói technológiát és informatikai szakemberek számára már alapulnak, és megbízható.

Létrehozása vagy áttelepítése informatikai eszközök, egy nyilvános felhőszolgáltatóhoz meg az adott szervezet képességek az alkalmazások és a szolgáltatások és a vezérlők adatok védelme érdekében a megbízható függő biztosítanak a biztonság, a felhő alapú eszközök kezeléséhez.

Azure infrastruktúrája alkalmazások felhasználók millióit egyidejűleg üzemeltetéséhez a létesítmény a készült, és biztosít egy megbízható foundation, amelyre vállalatok megfelel a biztonsági követelményeknek. Emellett Azure biztosít konfigurálható biztonsági beállításai és képes azokat, hogy testre szabhatja a szervezet központi telepítések egyedi követelményeinek biztonsági széles köréről. Ez a dokumentum fog segít megérteni az Azure biztonsági képességei segíthetnek ezek a követelmények teljesítéséhez.

### <a name="abstract"></a>Absztrakt
Az Azure Operational biztonsági hivatkozik a szolgáltatások, a vezérlők és a felhasználók számára elérhető szolgáltatások védelmére az adatok, alkalmazások és egyéb eszközök a Microsoft Azure-ban. Az Azure Operational biztonsági egy keretrendszer, amely magában foglalja a különböző képességeket, amelyek a Microsoftnak, beleértve a Microsoft biztonsági fejlesztési életciklus (SDL), a Microsoft Security Response Center program egyedi keresztül tapasztalatok épül és részletes tájékoztatást nyújthatnak a a számítógépes fenyegetésekről alkotott képet.

Ez a dokumentum belül a Microsoft Azure cloud platform a Microsoft Azure operatív biztonsági megközelítést ismerteti, és a következő szolgáltatásokat tartalmazza:
1.  [Az Azure Operations Management Suite szolgáltatásban](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

2.  [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

3.  [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

4.  [Az Azure hálózati figyelőt](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

5.  [Az Azure Storage analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Az Azure Active directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)


## <a name="microsoft-operations-management-suite"></a>A Microsoft Operations Management Suite szolgáltatásban

A Microsoft Operations Management Suite (OMS) az informatikai felügyeleti megoldás a hibrid felhő. Önmagában vagy a meglévő System Center telepítési kiterjesztéséhez OMS lehetővé teszi a maximális rugalmasságot és a felhő alapú felügyeleti infrastruktúra.

![A Microsoft Operations Management Suite szolgáltatásban](./media/azure-operational-security/azure-operational-security-fig1.png)

Az OMS-ben a felhő, beleértve a helyszíni, Azure, AWS, Windows Server, Linux, VMware és OpenStack, mint versenyképes megoldások alacsonyabb költségekkel szereplő bármely példány kezelheti. A felhő-első globális készült, OMS biztosít egy új megközelítés a vállalat, amely a lehető leggyorsabb és legköltséghatékonyabb módon új üzleti kihívást és olyan új munkaterhelések, alkalmazások és a felhő környezeteiben.

### <a name="oms-services"></a>OMS-szolgáltatások

Az OMS legfontosabb funkcióit az Azure-ban futó szolgáltatások biztosítják. Mindegyik szolgáltatás egy adott felügyeleti funkciót biztosít, és a szolgáltatások kombinálásával különféle felügyeleti forgatókönyvek valósíthatók meg.

| Szolgáltatás  | Leírás|
| :------------- | :-------------|
| Log Analytics | Figyeli és elemzi a különféle erőforrások, köztük a fizikai és virtuális gépek rendelkezésre állását és teljesítményét. |
|Automatizálás | Automatizálja a manuális folyamatokat, és érvényesíti a fizikai és virtuális gépekre vonatkozóan megadott konfigurációkat. |
| Biztonsági mentés | Készítsen biztonsági másolatot, és a fontos adatok helyreállítását. |
| Site Recovery | Biztosítja a kritikus fontosságú alkalmazások magas rendelkezésre állását. |

### <a name="log-analytics"></a>Log Analytics

A [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) figyelési szolgáltatásokat biztosít az OMS számára a felügyelt erőforrások adatainak egy központi tárházba gyűjtésével. Ezek az adatok lehetnek események, teljesítményadatok vagy az API segítségével biztosított egyéni adatok. Az összegyűjtésüket követően az adatok használhatók riasztáshoz, elemzéshez vagy exportáláshoz.


Ez a módszer lehetővé teszi különböző forrásokból származó adatokat, így kombinálhatja az Azure-szolgáltatások és a meglévő adatokat a helyszíni környezet. Továbbá egyértelműen elkülöníti az adatok gyűjtését az adatokon végzett műveletektől, így az összes művelet végrehajtható a különféle adatokon.


![Log Analytics](./media/azure-operational-security/azure-operational-security-fig2.png)

A Naplóelemzési szolgáltatás felhőalapú adatait biztonságosan kezeli az alábbi módszerekkel:
-   Az adatok elkülönítése
-   az adatok megőrzése
-   Fizikai biztonság
-   Incidenskezelés
-   Megfelelőségi
-   biztonsági szabványok tanúsítványok

### <a name="azure-backup"></a>Azure Backup

[Azure biztonsági mentés](http://azure.microsoft.com/documentation/services/backup) biztosítja az adatok biztonsági mentése és visszaállítása a szolgáltatások és termékek és szolgáltatások az OMS-csomag része.
Védelmet biztosít az alkalmazásadatok számára, valamint évekig megőrzi őket minden tőkebefektetés nélkül és minimális működési költségek mellett. Az adatok biztonsági másolatát is a fizikai és virtuális Windows Server kiszolgálókról például SQL Server és a SharePoint alkalmazások és szolgáltatások mellett. Azt is használhatja [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) a védett adatok replikálása Azure-bA redundancia és a hosszú távú tároláshoz.


Az Azure Backup védett adatainak tárolása egy meghatározott földrajzi régióban elhelyezkedő biztonságimásolat-tárolóban történik. Az adatok replikálódik a régión belül, és a tároló típusától függően előfordulhat, hogy is replikálni kell a további rugalmasságot egy másik régióban.

### <a name="management-solutions"></a>Felügyeleti megoldások
[A Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) a Microsoft felhőalapú informatikai felügyeleti megoldás, amely segít a kezelése és védelme a helyszíni és felhőalapú infrastruktúra.


[Megoldások](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) logics, amelyek megvalósítják az egy adott felügyeleti forgatókönyv egy vagy több OMS szolgáltatással előre csomagolt csoportja. A Microsoft és partnerei által kínált különböző megoldások egyszerűen hozzáadhatók az Azure-előfizetéshez, és így növelhető az OMS-sel kapcsolatos befektetéseinek értéke. Partnerként a saját megoldások támogatják az alkalmazások és szolgáltatások és azok a felhasználók számára az Azure piactér vagy a gyors üzembe helyezési sablonokat is létrehozhat.


![Felügyeleti megoldások](./media/azure-operational-security/azure-operational-security-fig4.png)

Egy jó példa a megoldás, amellyel több szolgáltatás további funkciókat biztosítja a [frissítés felügyeleti megoldás](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management). Ez a megoldás használ a [Naplóelemzési](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) ügynök információkat gyűjthet a Windows és Linux rendszerekhez szükséges frissítések ügynököket. Az adatokat beírja a Log Analytics-tárházba, ahol azok egy belső irányítópult segítségével elemezhetők.

Amikor létrehoz egy központi telepítési forgatókönyve [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szükséges frissítések szükségesek. A teljes folyamatot a portálon felügyelheti, és nem kell foglalkoznia a mögöttes részletekkel.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center segítségével, az Azure-erőforrások védelme. Biztosít integrált biztonsági monitorozást és az Azure-előfizetések. A szolgáltatáson belül meghatározhatják áll házirendek nem csak az Azure-előfizetések ellen is elleni [erőforráscsoportok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups), így pontosabban is lehet.

### <a name="security-policies-and-recommendations"></a>Biztonsági szabályzatok és javaslatok

A biztonsági szabályzat határozza meg azoknak a vezérlőelemeknek a körét, amelyeket a rendszer az adott előfizetésen vagy erőforráscsoporton belüli erőforrásokhoz javasol.

A Security Centerben a vállalat biztonsági elvárásainak és az alkalmazások típusának vagy az adatok érzékenységének megfelelően állíthatja be a szabályzatokat.

![Biztonsági szabályzatok és javaslatok](./media/azure-operational-security/azure-operational-security-fig5.png)


A diagram jobb oldalán látható az előfizetésen belüli összes erőforráscsoport propagálódik házirendeket, amelyek engedélyezve vannak az előfizetési automatikusan:


### <a name="data-collection"></a>Adatgyűjtés

A Security Center adatokat gyűjt a virtuális gépekről a biztonsági állapotuk értékeléséhez, a biztonsági javaslatok létrehozásához és a fenyegetésekkel kapcsolatos riasztásokhoz. Ha a Security Center adatgyűjtés első hozzáférés engedélyezve van az előfizetésében szereplő összes virtuális. Az adatgyűjtés ugyan ajánlott, de le is állíthatja azt a Security Center szabályzatában található adatgyűjtési lehetőség kikapcsolásával.

### <a name="data-sources"></a>Adatforrások

- Az Azure Security Center az alábbi forrásokból kapott adatok elemzésével biztosítja a biztonsági állapot áttekinthetőségét, azonosítja a biztonsági réseket, javításokat javasolj, valamint észleli az aktív fenyegetéseket:

-   Azure Services: az adott erőforrás-szolgáltatóval kommunikálva üzembe helyezett Azure-szolgáltatások konfigurációjával kapcsolatos információkat használja.

- Hálózati forgalom: a hálózati forgalom metaadataiból vett mintát használja a Microsoft infrastruktúrájából, például a forrás és a cél IP-címét és portját, a csomagméretet és a hálózati protokollt.

-   Partnermegoldások: Az integrált partnermegoldásoktól, például tűzfalaktól és kártevőirtó megoldásoktól érkező biztonsági riasztásokat használja.

-   Saját virtuális gépek: Konfigurációs és biztonsági eseményekkel kapcsolatos információkat használ (például Windows-esemény- és vizsgálati naplókat, IIS-naplókat, rendszernapló-üzeneteket és a virtuális gépekről származó összeomlási memóriaképeket).

### <a name="data-protection"></a>Adatvédelem

Az Azure Security Center biztonsággal kapcsolatos adatokat gyűjt és dolgoz fel (például konfigurációs információkat, metaadatokat, eseménynaplókat, összeomlási memóriaképeket és még sok mást), hogy segítsen az ügyfeleknek a fenyegetések megelőzésében, észlelésében és elhárításában. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig.

-   **Az adatok elkülönítése**: az adatok logikailag elkülönítve vannak tárolva a szolgáltatás egyes összetevőiben. Az összes adat szervezet szerint van megcímkézve. Ez a címkézés megmarad az adatok teljes életciklusa alatt, és a szolgáltatás minden rétegében érvényes.

-   **Adat-hozzáférési**: Adja meg a biztonsági javaslatok, és vizsgálja meg az esetleges biztonsági fenyegetéseket jelezhetnek, Microsoft személyzete gyűjtött adatok eléréséhez, vagy Azure-szolgáltatásokkal, beleértve a összeomlási memóriaképek, által elemzett feldolgozni a folyamatlétrehozási eseményeket, a Virtuálisgép-lemez a pillanatképek és összetevők, többek között lehet, hogy véletlenül ügyféladatok vagy a virtuális gépek személyes adatait. Mérvadónak a [Microsoft Online Services feltételei és adatvédelmi nyilatkozata](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), mely állapotban, amely a Microsoft nem használja, ügyféladatok, vagy származnia hirdetési vagy hasonló kereskedelmi célú származó információkat.

-   **Az adatok felhasználása**: a Microsoft a különböző bérlőknél észlelt mintákat és fenyegetésre vonatkozó intelligenciát használ a megelőzési és észlelési funkcióihoz, és ezt az [adatvédelmi nyilatkozatában](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx) ismertetett adatvédelmi kötelezettségeinek megfelelően teszi.

### <a name="data-location"></a>Az adatok helye

Az Azure Security Center ideiglenes másolatokat gyűjt az összeomlási memóriaképek fájljairól, és elemzi ezeket a biztonsági réseket kihasználó támadások és a sikeres feltörések nyomai után kutatva. Az Azure Security Center az elemzéseket ugyanazon a földrajzi helyen végzi el, ahol a munkaterület található, és az elemzés után törli az ideiglenes másolatokat. A gépösszetevők tárolása központilag történik, ugyanabban a régióban, ahol a virtuális gép is található.

-   **A Storage-fiókok**: tárfiók van megadva, minden egyes régió, ahol a virtuális gépek is működnek. Ez lehetővé teszi, hogy abban a régióban tárolja az adatait, amelyben az a virtuális gép található, amelyről az adatok gyűjtése történik.

-   **Az Azure Security Center tárhelye**: A biztonsági riasztásokkal (beleértve a partnerriasztásokat), javaslatokkal és biztonsági állapottal kapcsolatos információk tárolása jelenleg az Egyesült Államokban, központilag történik. Ez az információ magában foglalhat az Ön virtuális gépeiről összegyűjtött kapcsolódó konfigurációs adatokat és biztonsági eseményeket szükség szerint annak érdekében, hogy megjeleníthessük Önnek a biztonsági riasztásokat, a javaslatokat vagy a biztonsági állapot adatait.


## <a name="azure-monitor"></a>Azure Monitor

A [OMS biztonsági](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) és naplózási megoldás lehetővé teszi, hogy informatikai aktív figyelését az összes erőforrást, amelyek segíthetnek a biztonsági incidens hatás minimalizálása érdekében. OMS biztonsági és naplózási rendelkezik biztonsági tartományok erőforrások figyeléséhez használható. A biztonsági tartományi beállítások gyors hozzáférést biztosít, a biztonsági figyelés a következő tartományokkal további részleteket ismertetnek:

-   kártevő szoftver értékelése
-   Frissítések felmérése
-   Identitások és hozzáférések.

Azure figyelő erőforrások adott típusú mutatókat biztosít információkat biztosít. A képi megjelenítés, lekérdezés, útválasztási, riasztások, automatikus méretezési, és kínál automation adatokhoz az Azure-infrastruktúra (tevékenységnapló) és minden egyes Azure-erőforrás (diagnosztikai naplók) a.

![Azure Monitor](./media/azure-operational-security/azure-operational-security-fig6.png)


Sok áthelyezése alkotórészek összetettek a felhőalapú alkalmazásokhoz. Győződjön meg arról, hogy az alkalmazás marad be adatokat, és megfelelő állapotban fut figyelés nyújt. Emellett segít, hogy ki a lehetséges problémák stave és a múltbeli kiépítettektől eltérő hibakeresést.

Figyelési adatok segítségével emellett az alkalmazással kapcsolatos átfogó megismerésében. Ezt az információt nyújt segítséget az alkalmazások teljesítményének vagy karbantartási követelmények, vagy, amelyek egyébként kézi beavatkozás műveletek automatizálására.

### <a name="azure-activity-log"></a>Azure tevékenységnapló


A napló, amely a erőforrást az előfizetésében végrehajtott műveletek betekintést nyújt. A műveletnapló korábban hívták "Naplófájlok" vagy "Működési Logs", mivel azt jelenti, hogy az előfizetések vezérlő-vezérlősík eseményeket.

![Azure tevékenységnapló](./media/azure-operational-security/azure-operational-security-fig7.png)

A tevékenység-naplót használó, meghatározhatja a "mi, ki, és mikor" az esetleges írási műveleteket (PUT, POST, Törlés) végzett az erőforrást az előfizetésében. A művelet és az egyéb kapcsolódó tulajdonságainak állapotának értelmezni is lehet. A műveletnapló nem tartalmaz (GET) olvasási műveletek vagy a klasszikus modellt használó erőforrásokra vonatkozó műveleteket.

### <a name="azure-diagnostic-logs"></a>Az Azure diagnosztikai naplók

Ezek a naplók az erőforrás által kibocsátott, és adja meg az erőforrás a művelet részletes, gyakori adatait. Ezek a naplók tartalmának erőforrástípusok szerint változik.

Például Windows rendszer-eseménynaplói diagnosztikai napló a virtuális gépek és a blob, a tábla egy kategóriát, és várólista naplók diagnosztikai naplók kategóriáinak storage-fiókok.

Diagnosztikai naplók eltérnek a [tevékenységnapló (korábbi nevén napló tartalmát, vagy a műveleti napló)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). A műveletnapló erőforrást az előfizetésében a végrehajtott műveletek betekintést nyújt. Diagnosztikai naplók Észreveheti az olyan műveletek, hogy az erőforrás végre magát.

### <a name="metrics"></a>Mérőszámok

Azure figyelő címinfrastruktúra megjelenítési lehetőségeinek a teljesítmény- és a munkaterhelések Azure állapot telemetriai adatok felhasználását teszi lehetővé. A legfontosabb Azure telemetriai adatok típus (más néven teljesítményszámlálók) vagy az Azure erőforrások által kibocsátott metrikákat. Az Azure figyelő többféleképpen is konfigurálhatja, és felhasználhatják ezeket [metrikák](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) figyelés és hibaelhárítás céljából. Metrikák egy értékes telemetriai adatok forrását, és lehetővé teszik a következő feladatokat:

-   **A teljesítmény nyomon** az erőforrás (például egy virtuális gép, a webhely vagy a logikai alkalmazást) a metrikák a portál diagramon ábrázolásához és, hogy a diagramot irányítópulton való rögzítéshez.

-   **Probléma értesítés** , amely teljesítményére hatással van az erőforrás metrika ebbe a meghatározott küszöbérték.

-   **Konfigurálja az automatikus műveleteket**, például az automatikus skálázás egy erőforrás vagy runbook kiváltó metrika ebbe a meghatározott küszöbérték.

-   **Hajtsa végre a speciális elemzés** vagy a teljesítmény vagy a használati trendeket a erőforrás jelentés.

-   **Archív** az erőforrást a megfelelőségi és naplózási célokra teljesítmény vagy a rendszerállapot előzményeit.

### <a name="azure-diagnostics"></a>Azure Diagnostics

A funkció, amely lehetővé teszi a telepített alkalmazás diagnosztikai adatok gyűjtésére Azure belül. Használja a diagnosztika bővítményét a különböző forrásokból. A rendszer jelenleg támogatott [Azure Cloud Service webes és feldolgozói szerepkörök](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [Azure virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/overview) Microsoft Windows rendszerű és [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics). Más Azure-szolgáltatásokkal rendelkezik saját külön diagnosztika.

## <a name="azure-network-watcher"></a>Az Azure hálózati figyelőt

A hálózati biztonsági naplózás létfontosságú a hálózati biztonsági rések észlelése és az IT-biztonsági és szabályozási irányítás modell betartását. Biztonsági csoport nézet a konfigurált hálózati biztonsági csoport és a biztonsági szabályok és a hatékony biztonsági szabályok kérheti le. A szabályok alkalmazása listáját azt is meghatározhatja a portok nyitva, és a hálózati biztonsági rések értékelése.

[Hálózati figyelő](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) regionális szolgáltatás, amely lehetővé teszi figyelése és diagnosztizálása feltételek hálózati szintű az, hogy és az Azure-ból. Hálózati diagnosztika és a képi megjelenítés eszközök is elérhetők a hálózati figyelőt segítenek megérteni, diagnosztizálása és információt kaphat a hálózathoz, az Azure-ban. A szolgáltatás része a csomagrögzítéssel, a következő ugrás, az IP-adatfolyam győződjön meg arról, biztonsági csoport megtekintése, NSG folyamata naplókat. Forgatókönyv szintű figyelési jeleníti meg egy végpontok közötti hálózati erőforrások ellentétben egyes hálózati erőforrás-figyelése.

![Az Azure hálózati figyelőt](./media/azure-operational-security/azure-operational-security-fig8.png)

Hálózati figyelőt jelenleg a következő képességekkel rendelkezik:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">A naplók</a>**-hálózatok konfigurációjának részeként műveleteket a rendszer naplózza. Ezek a naplók megtekinthetők az Azure portálon, vagy visszavonni a Microsoft eszközök, például a Power BI és a külső eszközök használatával. Naplókat a portálon, a PowerShell, a CLI és a Rest API-n keresztül érhetők el. A naplókat további információkért tekintse meg a naplózási műveletek a Resource Manager. Naplók az összes hálózati erőforrás végzett műveletek érhetők el.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">IP-folyamata ellenőrzi </a>**  -ellenőrzést, ha egy csomag engedélyezett vagy megtagadott folyamata adatokat 5 rekordos csomag paraméterek (cél IP-címe, forrás IP-címe, Célport, Forrásport és protokoll) alapján. Ha a csomag megtagadta a hálózati biztonsági csoport, a szabály és a hálózati biztonsági csoport, amely a csomag megtagadva ad vissza.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Következő Ugrás</a>**  -határozza meg a következő ugrás a csomagok továbbítása az Azure hálózati háló, így lehetővé teszi bármely diagnosztizálása nincs megfelelően konfigurálva a felhasználó által definiált útvonalak.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Biztonsági csoport megtekintése</a>**  -a hatékony és alkalmazott biztonsági szabályokat, amelyek érvényesek a virtuális gép lekérdezi.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">NSG Flow naplózási</a>**  -folyamat naplókat a hálózati biztonsági csoportok lehetővé teszik a engedélyezett vagy megtagadott a csoport biztonsági szabályai forgalmi naplók rögzítése. A folyamat egy 5 rekordos információkat – a forrás IP-cím, a cél IP-cím, a forrásport, a célport és a protokoll határozzák meg.

## <a name="azure-storage-analytics"></a>Azure Storage Analytics

[Tárolási analitika](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) tárolhatja, amely tartalmazza az összevont tranzakció statisztikák és kapacitás adatok kérelmekkel kapcsolatos egy társzolgáltatás metrikákat. Tranzakciók jelentett szinten mindkét API művelet és a tárolás szolgáltatás szintjén, és a kapacitás jelenti a tárolás szolgáltatás szintjén. Metrikai adatok használható tárolási szolgáltatás használati elemzése, eseményadatokat az által, a tárolás szolgáltatás ellen, valamint egy szolgáltatást használó alkalmazások teljesítményének javítása érdekében.

[Az Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) elvégzi a naplózási, és adja meg a tárfiók metrikák adatait. Ezekkel az adatokkal kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálni lehet a tárfiókokkal kapcsolatos problémákat. Storage Analytics naplózási érhető el a [Blob, Queue és Table szolgáltatások](https://docs.microsoft.com/azure/storage/storage-introduction). Storage Analytics egy társzolgáltatás sikeres és sikertelen kérelmekkel kapcsolatos részletes információkat naplózza.

Ezeket az információkat egyes kérelmeket a figyelheti és egy tárolási szolgáltatással kapcsolatos problémák diagnosztizálásához használható. Kérelmek is be vannak jelentkezve a legjobb alapul. Naplóbejegyzéseket jönnek létre, csak ha nincs a szolgáltatási végpont ellen. A példa-e a tárfiók tevékenység a Blob végpontja, de nem a tábla- vagy várólista végpont csak naplózza a Blob szolgáltatáshoz tartozó jön létre.

A tárolási analitika használatához engedélyeznie kell azt egyedileg minden egyes figyelni kívánt szolgáltatás. Engedélyezheti a a [Azure-portálon](https://portal.azure.com/); további információkért lásd: [figyelése az Azure portálon tárfiók](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Tárolási analitika programozott módon a REST API vagy ügyfélkódtár keresztül is engedélyezheti. A szolgáltatás tulajdonságainak beállítása művelet segítségével tárolási analitika külön-külön engedélyezni az egyes szolgáltatásokhoz.

Az összesített adatok tárolását a jól ismert blob (naplózás), és jól ismert táblában (a metrika), előfordulhat, hogy elérhetők, a Blob és Table szolgáltatások API-k használatával.

Tárolási analitika rendelkezzen, amely független a tárfiók teljes korlátját tárolt adatok mennyisége a 20-TB-os korlátot. Minden naplója [blokkblobokat](https://docs.microsoft.com/azure/storage/storage-analytics) $logs nevű tároló, amely jönnek létre automatikusan tárolási analitika engedélyezve van a tárfiók.

Az alábbi tárolási analitika által végrehajtott műveletek a következők számlázható:

-   A naplózás blobok létrehozására irányuló kéréseket
-   Kérelmek táblaentitásokat metrikáihoz létrehozásához.

> [!Note]
> A számlázással és az adatmegőrzési házirendek további információkért lásd: [tárolási analitika és számlázási](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> Az optimális teljesítmény érdekében szeretné korlátozni a erősen túlterhelt lehetséges szabályozás elkerülése érdekében a virtuális géphez csatolt lemezek számát. Az összes lemez nem kívánt magas használhatók egyszerre, ha a tárfiók egy nagyobb számú lemezt támogat.

> [!Note]
> A tárfiókok korlátai további információkért lásd: [Azure Storage méretezhetőségi és teljesítménycéloknak](https://docs.microsoft.com/azure/storage/storage-scalability-targets).


A következő típusú hitelesített vagy névtelen kérelmeket a rendszer naplózza.

| Hitelesített  | Névtelen|
| :------------- | :-------------|
| Sikeres kérelmei | Sikeres kérelmei |
|Sikertelen kérelmek, beleértve az időtúllépés, a sávszélesség-szabályozás, hálózati, engedélyezési és egyéb hibák | Egy közös hozzáférésű Jogosultságkód (SAS), például és a sikertelen kérelmek használatával |
| Egy közös hozzáférésű Jogosultságkód (SAS), például és a sikertelen kérelmek használatával |Az ügyfél és kiszolgáló egyaránt időtúllépési hibák |
|   Az analitikai adatok kérelmek |    Sikertelen GET kérelmek 304 (nem módosított). Hibakód: |
| Tárolási analitika, például a napló létrehozásakor vagy törlésekor, kérelmét a rendszer nem naplózza. A naplózott adatok teljes listáját részletes ismertetését lásd: a [Storage Analytics naplózott műveletekkel és az állapotüzenetek](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) és [Storage Analytics naplóformátumban](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) témaköröket. | Minden más sikertelen névtelen kérelmek nem naplózza a rendszer. A naplózott adatok teljes listáját részletes ismertetését lásd: a [Storage Analytics naplózott műveletekkel és az állapotüzenetek](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) és [Storage Analytics naplóformátumban](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |
## <a name="azure-active-directory"></a>Azure Active Directory

Az Azure AD is tartalmaz, beleértve a többtényezős hitelesítést, eszközregisztráció, önkiszolgáló jelszókezelés, önkiszolgáló csoportkezelési, magas jogosultsági szintű fiók felügyeleti, szerepkörön alapuló hozzáférés identitáskezelési képességeit teljes készlete vezérlő, alkalmazás-használat figyelését, részletes naplózás, és a biztonsági figyelés és riasztás.

-   Alkalmazás biztonságának fokozása a többtényezős hitelesítést az Azure AD és a feltételes hozzáférés.

-   Megfigyelésére és az üzleti biztonsági jelentéskészítési és figyelés speciális fenyegetésekkel szembeni hatékony védelme.

Az Azure Active Directory (Azure AD) biztonsági, naplózási és tevékenységjelentéseket biztosít a címtárához. [Az Azure Active Directory naplózási jelentés](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) segítségével az ügyfelek azonosításához, amely az Azure Active Directoryban történt a privilegizált műveletekhez. A privilegizált műveletekhez tartalmazza az illetéktelen módosítások (például a szerepkör létrehozása vagy a jelszó alaphelyzetbe állítása), változó Csoportházirend konfigurálásának (például jelszóházirendek) és (például tartomány-összevonási beállításai módosításának) directory konfigurációjának módosításai.

A jelentések az esemény nevét, a szereplő ki hajtotta végre a műveletet, a cél erőforráson hatással a módosítás dátuma és időpontja (UTC) adja meg a naplórekordot. Az ügyfelek képesek naplózási események listájának beolvasása az Azure Active Directory keresztül a [Azure-portálon](https://portal.azure.com/)leírtak szerint [megtekintése a vizsgálati naplókban](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). A benne foglalt jelentések listája:

| Biztonsági jelentések  | Tevékenységjelentések| Naplózási jelentések |
| :------------- | :-------------| :-------------|
|Bejelentkezések ismeretlen forrásokról | Alkalmazáshasználat: összegzés | Címtárnaplózási jelentés |
|Több hibát követő bejelentkezések | Alkalmazáshasználat: részletes |   |
|Bejelentkezések különböző földrajzi régiókból | Alkalmazás irányítópultja |  |
|Bejelentkezések gyanús tevékenységeket mutató IP-címekkel |Alkalmazás-kiépítési hibák |  |
|Rendszertelen bejelentkezési tevékenység |Egyéni felhasználói eszközök |  |
|Bejelentkezések potenciálisan fertőzött eszközökről |Egyéni felhasználói tevékenység |   |
|Rendellenes bejelentkezési tevékenységet mutató felhasználók |Csoporttevékenység-jelentés |   |
| |Jelszó-visszaállítási regisztrációs tevékenységjelentés |   |
| |Jelszó-visszaállítási tevékenység |   | |



Ezeket az adatokat az alkalmazások, például a SIEM rendszerek, naplózási és üzleti intelligencia eszközök hasznos lehet. Az Azure AD jelentéskészítési API [API-k](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) a REST-alapú API-készlet keresztül programozott hozzáférést nyújtani. Ezen API-k különböző programozási nyelveket és eszközök hívása.

Az Azure AD naplózási jelentés események kerülnek 180 napig tart.

> [!Note]
> A jelentésekre megőrzési kapcsolatos további információkért lásd: [Azure Active Directory jelentés adatmegőrzési szabályai](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Az ügyfelek tárolása iránt érdeklődik a [naplózása](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) hosszabb megőrzési ideig, a Reporting API segítségével rendszeresen lekéréses külön adattárba események naplózása.

## <a name="summary"></a>Összefoglalás

Ez a cikk ezekkel az Ön adatainak védelmében, és miközben nagy szoftverek és -szolgáltatások, amelyek segítenek az adatok védelme a szervezet informatikai infrastruktúrájának kezelése. Microsoft felismeri, hogy azok megbízni másoknak adataikat, amikor adott megbízhatósági szigorú biztonsági igényel. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig. Biztonságossá tétele és az adatok védelmének a Microsoft a legnagyobb prioritással.

Ez a cikk ismerteti

-   Milyen adatok gyűjtése, feldolgozása, és az Operations Management Suite (OMS) védett.

-   Az események gyors elemzése akár több adatforrásban. A biztonsági incidensek okozta károk minimalizálása érdekében azonosítani kell a biztonsági kockázatokat és fel kell mérni a fenyegetések és támadások terjedelmét és hatásait.

-   Támadási minták azonosítása a kimenő rosszindulatú IP-forgalom és a rosszindulatú fenyegetéstípusok vizualizációjával. Ismerje meg, a biztonságot a teljes környezet platformtól függetlenül.

-   A megfelelőségi és biztonsági naplózási szükséges összes naplóját és a esemény adatok rögzítéséhez. Az idő és a biztonsági naplózást egy teljes, kereshető és exportálható napló és a eseményadat-készlet adja meg a szükséges erőforrásokat perjel.

<ul>
<li>Biztonsági események gyűjtése, naplózási és biztonsági szabályok megsértésére elemzések lezárása tartsa szem a eszközöket:</li>
<ul>
<li>Biztonsági állapotát</li>
<li>Figyelmet a jelentősebb probléma</li>
<li>Ezekkel az fenyegetések</li>
</ul>
</ul>

## <a name="next-steps"></a>Következő lépések

- [Tervezési és a működési biztonság](https://www.microsoft.com/trustcenter/security/designopsecurity)

A Microsoft a szolgáltatások és szoftverek kialakítást a biztonságot szem előtt gondoskodjon arról, hogy a felhő-infrastruktúra rugalmas és védeni támadások elősegítésére.

- [Az Operations Management Suite |} Biztonsági és megfelelőségi](https://www.microsoft.com/cloud-platform/security-and-compliance)

Több intelligens és hatékony fenyegetések észlelése végrehajtásához használja a Microsoft biztonsági és elemzése.

- [Azure Security Center tervezéséhez és műveletek](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide) lépéseket és feladatokat, amelyek követésével optimalizálja a Security Centerben a vállalat biztonsági igényeinek és felhőfelügyeleti modelljének alapján a.

