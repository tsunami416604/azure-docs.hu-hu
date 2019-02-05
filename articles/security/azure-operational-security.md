---
title: Az Azure Operational Security |} A Microsoft Docs
description: Ismerje meg a Microsoft Azure Log Analytics, a szolgáltatások és működését.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: aafa1acdf6cca58ea0cfbc968f73ce13eb65bf91
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700502"
---
# <a name="azure-operational-security"></a>Az Azure működési biztonság
## <a name="introduction"></a>Bevezetés

### <a name="overview"></a>Áttekintés
Tudjuk, hogy biztonsági-e a feladatot a felhőben, és hogy mennyire fontos, hogy látja-e az Azure security pontos és időben információt. A legjobb okok igénybe az Azure az alkalmazások és szolgáltatások egyik biztonsági eszközökkel és a rendelkezésre álló lehetőségek széles választékának előnyeinek kihasználása érdekében. A következő eszközök és képességek segítségével biztonságos megoldásokat hozhat létre a biztonságos Azure-platformon lehetővé teszik, hogy. Windows Azure kell megadnia bizalmas, integritás és rendelkezésre állását a vásárlói adatokat, miközben transzparens accountability is.

Segítségével jobban megismerheti, mind az ügyfél a Microsoft Azure-ban végrehajtott biztonsági intézkedés a tömbben, és a Microsoft operational perspektívák, ez a tanulmány, "Az Azure Operational Security", írása, amely ügyfeleknek nyújt átfogó Tekintse meg a működési biztonság érhető el a Windows Azure-ral.

### <a name="azure-platform"></a>Azure Platform
Az Azure egy nyilvános felhőszolgáltatás-platform, amely támogatja az operációs rendszerek, programozási nyelvek, keretrendszerek, eszközök, adatbázisok és eszközök széles skálájából el. Azt is Linux-tárolókat futtathat Docker-integrációval; a JavaScript, Python, .NET, PHP, Java és Node.js-alkalmazások készítéséhez háttérrendszereket készíthet iOS, Android és Windows eszközökhöz. Az Azure Cloud service támogatja a technológiákat a fejlesztők és informatikai szakemberek számára már elnyerték.

Amikor épülnek, vagy át az IT-eszközeivel, nyilvános felhőszolgáltató védelme érdekében az alkalmazások és adatok a szolgáltatások és a vezérlők annak a szervezetnek képességekhez hagyatkoznia nyújtanak kezelheti a felhőalapú eszközeinek biztonságát.

Az Azure infrastruktúráját úgy alakítottuk kiszolgáló alkalmazásokat üzemeltetni a képes legyen ügyfelek millióit egyidejűleg, és olyan megbízható alapot, amely megfelel a vállalatok a biztonsági követelményeket biztosít. Emellett az Azure biztosít számos konfigurálható biztonsági beállítások és irányítása őket, hogy a szervezet üzemelő példányok egyedi követelményeinek megfelelő biztonsági testre szabhatja. Ez a dokumentum lesz a segítségével megismerheti az Azure biztonsági funkciók segítségével ezek a követelmények teljesítéséhez.

### <a name="abstract"></a>Absztrakt
Az Azure Operational Security hivatkozik a szolgáltatások, a vezérlők és a felhasználók számára elérhető szolgáltatások védelme érdekében az adatokat, alkalmazásokat és egyéb eszközök, Microsoft Azure-ban. Az Azure Operational Security olyan keretrendszer, amely magában foglalja a különböző képességeket, amelyek a egyedülálló rendszereiből, például a Microsoft biztonsági fejlesztési életciklus (SDL), a Microsoft Security Response Center program keresztül szerzett ismeretek épül és a kiberbiztonsági fenyegetések világának.

Ez a tanulmány belül a Microsoft Azure felhőalapú platform a Microsoft Azure Operational Security megközelítését ismerteti, és a következő szolgáltatásokat tartalmazza:
1.  [Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

2.  [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

3.  [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

4.  [Az Azure Network watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

5.  [Az Azure Storage analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)


## <a name="microsoft-azure-log-analytics"></a>A Microsoft Azure Naplóelemzés

A Microsoft Azure Log Analytics az IT-felügyeleti megoldást kínál a hibrid felhő áll. Önállóan használva vagy kiterjesztése a System Center-telepítéssel, Log Analytics a legnagyobb rugalmasságot és vezérlést biztosít az infrastruktúra felhőalapú kezelését.

![Log Analytics](./media/azure-operational-security/azure-operational-security-fig1.png)

A Log Analytics-szel a felhőhöz, beleértve a helyszíni, Azure, az AWS, a Windows Server, Linux, VMware és OpenStack, mint az adatbázisszinten megoldások alacsonyabb költséggel lévő bármely példány segítségével kezelheti. A felhő-és felhőközpontú világ számára készült, a Log Analytics új megközelítést kínál az a vállalat, amely a leggyorsabb és leginkább költséghatékony megoldást új üzleti kihívásokat, valamint új terheléseket, alkalmazások és a felhőalapú környezetek kezeléséhez.

### <a name="log-analytics-services"></a>Log Analytics-szolgáltatások

A Log Analytics központi funkcióit az Azure-ban futó szolgáltatások biztosítják. Mindegyik szolgáltatás egy adott felügyeleti funkciót biztosít, és a szolgáltatások kombinálásával különféle felügyeleti forgatókönyvek valósíthatók meg.

| Szolgáltatás  | Leírás|
| :------------- | :-------------|
| Log Analytics | Figyeli és elemzi a különféle erőforrások, köztük a fizikai és virtuális gépek rendelkezésre állását és teljesítményét. |
|Automation | Automatizálja a manuális folyamatokat, és érvényesíti a fizikai és virtuális gépekre vonatkozóan megadott konfigurációkat. |
| Backup | Készítsen biztonsági másolatot, és a kritikus fontosságú adatok helyreállítását. |
| Site Recovery | Biztosítja a kritikus fontosságú alkalmazások magas rendelkezésre állását. |

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics) adatokat gyűjt a felügyelt erőforrások egy központi tárházba figyelési szolgáltatásokat biztosít. Ezek az adatok lehetnek események, teljesítményadatok vagy az API segítségével biztosított egyéni adatok. Az összegyűjtésüket követően az adatok használhatók riasztáshoz, elemzéshez vagy exportáláshoz.


Ezzel a módszerrel konszolidálhatja a különféle forrásokból származó adatokat, így kombinálhatja az Azure-szolgáltatásokat a meglévő adatokat a helyszíni környezetben. Továbbá egyértelműen elkülöníti az adatok gyűjtését az adatokon végzett műveletektől, így az összes művelet végrehajtható a különféle adatokon.


![Log Analytics](./media/azure-operational-security/azure-operational-security-fig2.png)

A Log Analytics szolgáltatás a felhőbeli adatok biztonságosan kezeli a következő módszerekkel:
-   az adatok elkülönítése
-   Adatmegőrzés
-   fizikai biztonság
-   incidenskezelés
-   megfelelőség
-   biztonsági szabványok tanúsítványok

### <a name="azure-backup"></a>Azure Backup

[Az Azure Backup](https://azure.microsoft.com/documentation/services/backup) biztosít az adatok biztonsági mentését és helyreállítását végző szolgáltatásokat és a termékek és szolgáltatások a Log Analytics-csomag része.
Védelmet biztosít az alkalmazásadatok számára, valamint évekig megőrzi őket minden tőkebefektetés nélkül és minimális működési költségek mellett. A fizikai és virtuális Windows kiszolgálókról, valamint az alkalmazások és szolgáltatások, például az SQL Server és SharePoint biztonsági mentés adatokat. Azt is használhatja [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) védett adatok replikálása az Azure-bA a redundancia és a hosszú távú tároláshoz.


Az Azure Backup védett adatainak tárolása egy meghatározott földrajzi régióban elhelyezkedő biztonságimásolat-tárolóban történik. Az adatok ugyanazon a régión belül replikálódnak, és a tároló típusától függően előfordulhat, hogy is lehet egy másik régióba replikálja további rugalmasságot.

### <a name="management-solutions"></a>Felügyeleti megoldások
[Log Analytics](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) van a Microsoft felhőalapú informatikai felügyeleti megoldása, amely segít a kezelése és védelme a helyszíni és felhőalapú infrastruktúrára.


[Felügyeleti megoldások](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) vannak, amelyek egy vagy több Log Analytics szolgáltatás használatával adott felügyeleti forgatókönyvet valósítanak meg logics összeállított logikakészletek. A Microsoft és partnerei, hogy könnyen adhat az Azure-előfizetés növelje befektetéseit a Log Analytics különböző megoldások érhetők el. Partnerként létrehozhat saját megoldásokat az alkalmazások és szolgáltatások támogatására, és adja meg azokat a felhasználók számára az Azure Marketplace piactéren vagy a gyors üzembe helyezési sablonokat.


![Felügyeleti megoldások](./media/azure-operational-security/azure-operational-security-fig4.png)

Egy jó példa olyan megoldás, amely további funkciókat több szolgáltatás használ a [Update Management megoldás](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management). Ez a megoldás használ a [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) információkat gyűjthet a Windows és Linux ügynök-frissítések minden ügynök szükséges. Az adatokat beírja a Log Analytics-tárházba, ahol azok egy belső irányítópult segítségével elemezhetők.

Amikor létrehoz egy központi telepítés, a runbookok [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szükséges frissítések telepítéséhez szükségesek. A teljes folyamatot a portálon felügyelheti, és nem kell foglalkoznia a mögöttes részletekkel.

## <a name="azure-security-center"></a>Azure Security Center

Az Azure Security Center segít az Azure-erőforrások védelme. Integrált biztonsági monitorozást és felügyeletet biztosít az Azure-előfizetések között. A szolgáltatáson belül is tudja definiálása házirendek nem csak az Azure-előfizetései ellen is elleni [erőforráscsoportok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups), ezért a részletesebb lehet.

### <a name="security-policies-and-recommendations"></a>Biztonsági szabályzatok és javaslatok

A biztonsági szabályzat határozza meg azoknak a vezérlőelemeknek a körét, amelyeket a rendszer az adott előfizetésen vagy erőforráscsoporton belüli erőforrásokhoz javasol.

A Security Centerben a vállalat biztonsági elvárásainak és az alkalmazások típusának vagy az adatok érzékenységének megfelelően állíthatja be a szabályzatokat.

![Biztonsági szabályzatok és javaslatok](./media/azure-operational-security/azure-operational-security-fig5.png)


Szabályzatokat a rendszer az előfizetési csomagnak automatikusan propagálja a jobb oldali ábrán látható módon az előfizetésen belüli összes erőforráscsoport számára:


### <a name="data-collection"></a>Adatgyűjtés

A Security Center adatokat gyűjt a virtuális gépekről a biztonsági állapotuk értékeléséhez, a biztonsági javaslatok létrehozásához és a fenyegetésekkel kapcsolatos riasztásokhoz. Ha az első hozzáférés a Security Center, az adatgyűjtés engedélyezve van az előfizetés minden virtuális gépen. Az adatgyűjtés ugyan ajánlott, de le is állíthatja azt a Security Center szabályzatában található adatgyűjtési lehetőség kikapcsolásával.

### <a name="data-sources"></a>Adatforrások

- Az Azure Security Center az alábbi forrásokból kapott adatok elemzésével biztosítja a biztonsági állapot áttekinthetőségét, azonosítja a biztonsági réseket, javításokat javasolj, valamint észleli az aktív fenyegetéseket:

-   Azure Services: Használ, adott erőforrás-szolgáltatóval kommunikálva üzembe helyezett Azure-szolgáltatások konfigurációjával kapcsolatos információkat.

- Hálózati forgalom: Használja a hálózati forgalom metaadataiból a Microsoft, például a forrás és cél IP-portot, a csomagméretet és a hálózati protokoll mintát venni.

-   Partneri megoldások: Integrált partneri megoldások, például tűzfalaktól és kártevőirtó megoldásoktól érkező biztonsági riasztásokat használja.

-   A virtuális gépek: Konfigurációs és biztonsági eseményekkel, például a Windows esemény- és vizsgálati naplókat, IIS-naplók, syslog-üzeneteket és a virtuális gépekről származó összeomlási memóriaképeket kapcsolatos információkat használ.

### <a name="data-protection"></a>Adatvédelem

Az Azure Security Center biztonsággal kapcsolatos adatokat gyűjt és dolgoz fel (például konfigurációs információkat, metaadatokat, eseménynaplókat, összeomlási memóriaképeket és még sok mást), hogy segítsen az ügyfeleknek a fenyegetések megelőzésében, észlelésében és elhárításában. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig.

-   **Az adatok elkülönítése**: Adatok van logikailag elkülönítve vannak tárolva a szolgáltatás egyes összetevőiben. Az összes adat szervezet szerint van megcímkézve. Ez a címkézés megmarad az adatok teljes életciklusa alatt, és a szolgáltatás minden rétegében érvényes.

-   **Adatelérési**: Biztonsági javaslatok és a lehetséges biztonsági fenyegetések kivizsgálása, a Microsoft ezért felelős munkatársai gyűjtött információk érhetik el vagy Azure-szolgáltatások által elemzett összeomlási memóriaképekhez feldolgozni-létrehozási események, Virtuálisgép-lemez pillanatképeihez és összetevőihez, amelyek véletlenül tartalmazhatnak ügyféladatokat vagy személyes adatokat a virtuális gépekről. Betartjuk a [Microsoft Online szolgáltatások használati feltételeit és adatvédelmi nyilatkozatát](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), mely állapot, amely a Microsoft nem használja az ügyféladatokat, vagy a nyer információt azokból hirdetési vagy hasonló kereskedelmi célú.

-   **Adatok használatával**: A Microsoft minták és több bérlő különböző bérlőknél észlelt fenyegetésekkel kapcsolatos Tudásbázis alkalmaz a használatával javíthatja a megelőzési és észlelési funkcióihoz; ismertetett adatvédelmi kötelezettségeinek megfelelően tesszük a [adatvédelmi nyilatkozat](https://www.microsoft.com/en-us/privacystatement/OnlineServices/).

### <a name="data-location"></a>Az adatok helye

Az Azure Security Center ideiglenes másolatokat gyűjt az összeomlási memóriaképek fájljairól, és elemzi ezeket a biztonsági réseket kihasználó támadások és a sikeres feltörések nyomai után kutatva. Az Azure Security Center az elemzéseket ugyanazon a földrajzi helyen végzi el, ahol a munkaterület található, és az elemzés után törli az ideiglenes másolatokat. A gépösszetevők tárolása központilag történik, ugyanabban a régióban, ahol a virtuális gép is található.

-   **A Tárfiókok**: Egy tárfiók van megadva minden régióhoz, ahol a virtuális gépek futnak. Ez lehetővé teszi, hogy abban a régióban tárolja az adatait, amelyben az a virtuális gép található, amelyről az adatok gyűjtése történik.

-   **Azure Security Center Storage**: Biztonsági riasztások, beleértve a partnerriasztásokat, javaslatok és biztonsági állapottal kapcsolatos információk tárolása központilag, jelenleg az Amerikai Egyesült Államokban. Ez az információ magában foglalhat az Ön virtuális gépeiről összegyűjtött kapcsolódó konfigurációs adatokat és biztonsági eseményeket szükség szerint annak érdekében, hogy megjeleníthessük Önnek a biztonsági riasztásokat, a javaslatokat vagy a biztonsági állapot adatait.


## <a name="azure-monitor"></a>Azure Monitor

A [Log Analytics biztonsági](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) és naplózási megoldás révén az informatikai aktívan figyeljük az összes erőforrást, amely megkönnyíti a biztonsági incidens hatásának minimalizálása érdekében. Log Analytics biztonsági és auditálási rendelkezik az erőforrások figyeléséhez használt biztonsági tartományok. A biztonsági tartományához beállítások gyors hozzáférést biztosít, a biztonság monitorozására vonatkozó további részleteket a következő tartományok ismertetnek:

-   Kártevőfelmérés
-   Frissítések felmérése
-   Identitás és hozzáférés.

Az Azure Monitor mutatókat tartalmaznak információkat biztosít az adott típusú erőforrás. Vizualizáció, lekérdezés, útválasztás, riasztási, automatikus méretezés és mind az Azure-infrastruktúra (tevékenységnapló) és minden egyes Azure-erőforrás (diagnosztikai naplók) származó adatokon automation kínál.

![Azure Monitor](./media/azure-operational-security/azure-operational-security-fig6.png)


A felhőalapú alkalmazások összetettek a részek. Győződjön meg arról, hogy az alkalmazás mindig elérhető fel az adatokat és kifogástalan állapotban fut figyelést biztosít. Segít, hogy ki a lehetséges problémák stave vagy korábbi kiépítettektől hibaelhárítása.

Monitorozási adatok segítségével emellett részletes elemzéseket kaphat az alkalmazásról. A Tudásbázis segítségével javíthatja az alkalmazás teljesítménye vagy Karbantarthatóság, vagy, amelyek egyébként manuális beavatkozásra műveletek automatizálása.

### <a name="azure-activity-log"></a>Azure-tevékenységnapló


Egy naplót, amelyek támpontul szolgálnak az előfizetésében erőforrásokon végrehajtott műveletekkel. A tevékenységnapló nevezték "Naplófájlok" vagy "Műveleti naplók," mivel a vezérlősík események az előfizetésekre vonatkozó jelentést készít.

![Azure-tevékenységnapló](./media/azure-operational-security/azure-operational-security-fig7.png)

A tevékenységnapló használatával megadhatja, hogy a "mit, ki, és mikor" írási műveletek (PUT, POST, DELETE) tett erőforrásokra az előfizetésben. A művelet és az egyéb releváns tulajdonságok állapotát is ismernie is. A tevékenységnapló nem tartalmaz olvasási (GET) műveleteket, illetve az erőforrások a klasszikus modellt használó műveleteket.

### <a name="azure-diagnostic-logs"></a>Az Azure diagnosztikai naplók

Ezeket a naplókat az erőforrás által kibocsátott vannak, és adja meg a műveletet az erőforrás gazdag, gyakori adatait. Ezek a naplók a tartalom erőforrás típusa szerint változó.

Például Windows rendszer-eseménynaplói egy kategóriába tartozó virtuális gépek és a blob, tábla diagnosztikai naplót, és üzenetsor-naplók kategóriába sorolhatók a diagnosztikai naplók tárfiókok esetében.

Diagnosztikai naplók különböznek a [Activity Log (korábbi nevén napló vagy működési naplófájlokat)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). A tevékenységnaplóban a az előfizetésében erőforrásokon végrehajtott műveletekkel betekintést nyújt. A diagnosztikai naplókban az erőforrás által végrehajtott műveletekkel kapcsolatos információk találhatók meg.

### <a name="metrics"></a>Mérőszámok

Az Azure Monitor lehetővé teszi, hogy értékes információkhoz juthat a teljesítmény és az Azure-ban a számítási feladatok állapotát a telemetriai adatok felhasználásához. Az Azure telemetriai adatokat a legfontosabb típus a legtöbb Azure-erőforrások által kibocsátott (más néven teljesítményszámlálók) metrikák. Az Azure Monitor többféleképpen is konfigurálhatja, és ezek használata [metrikák](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) figyeléséhez és hibaelhárításához. Metrikák telemetria értékes forrását, és lehetővé teszi a következő feladatokat végezheti el:

-   **A teljesítmény** az erőforrás (például egy virtuális Gépet, a webhely vagy a logikai alkalmazás) küldik az ábrázolást a portál diagramon a metrikák és a rögzítés az irányítópulton a diagram.

-   **Értesítés küldése egy probléma** , amely hatással van az erőforrás a teljesítmény, ha egy metrika átlépi egy bizonyos küszöbértéket.

-   **Automatikus műveletek beállítása**, például az automatikus skálázás egy erőforrást vagy aktiválja a runbookot, ha egy metrika átlépi egy bizonyos küszöbértéket.

-   **Bővített analitika** vagy a teljesítmény vagy a használati trendeket az erőforrás jelent.

-   **Archív** az erőforrást, megfelelőségi és naplózási célokra teljesítmény vagy egészségügyi előzményeit.

### <a name="azure-diagnostics"></a>Azure Diagnostics

A funkció, amely lehetővé teszi az üzembe helyezett alkalmazás diagnosztikai adatgyűjtés Azure-on belül. A diagnosztikai bővítmény különböző forrásokból is használhatja. Jelenleg csak [Azure Cloud Service webes és feldolgozói szerepkörök](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/overview) fut a Microsoft Windows, és [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics). Más Azure-szolgáltatásokkal rendelkezik saját külön diagnosztika.

## <a name="azure-network-watcher"></a>Azure Network Watcher

A hálózati biztonsági naplózás létfontosságú a hálózati biztonsági rések és az IT-biztonsági és jogszabályi cégirányítási modell betartásáért. Biztonsági csoport nézet kérheti le a konfigurált hálózati biztonsági csoport és a biztonsági szabályok és az érvényben lévő biztonsági szabályokat. A alkalmazni szabályok listáját, és megadhatja, hogy a portok nyitva, és a hálózati biztonsági rések felmérése.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) egy regionális szolgáltatás, amely lehetővé teszi a figyelési és diagnosztizálási tevékenységet végezhet hálózati szinten lévő, a és az Azure-ból. A hálózati diagnosztikai és vizualizációs eszközök a Network Watcherrel elérhető segítenek megérteni, diagnosztizálása és betekintést nyerhet az Azure-ban a hálózati. A szolgáltatás része, csomagrögzítés, következő ugrási, IP-folyamat ellenőrzése, a biztonsági csoport nézet NSG-Folyamatnaplók. Forgatókönyv szintű monitorozása biztosítja a teljes körű képet szakembereket egyes hálózati erőforrások monitorozása a hálózati erőforrásokhoz.

![Azure Network Watcher](./media/azure-operational-security/azure-operational-security-fig8.png)

A Network Watcher jelenleg a következő képességekkel rendelkezik:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Auditnaplók</a>**-hálózatok konfigurációjának részeként végrehajtott műveleteket a rendszer naplózza. Ezek a naplók tekinthetnek meg az Azure Portalon, vagy használja a Microsoft eszközök, például a Power bi-ban vagy harmadik féltől származó eszközökkel. Auditnaplók – a portal, PowerShell, CLI és Rest API-val érhetők el. A naplók riasztásaihoz további információkért tekintse meg a naplózási műveletek a Resource Managerben. Auditnaplók minden hálózati erőforrásokon végzett műveletek érhetők el.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">IP-folyamat ellenőrzése </a>**  – ellenőrzi, hogy egy csomag engedélyezett vagy megtagadott (cél IP-cím, forrásoldali IP-cím, Célport, forrásoldali portszám és protokoll) folyamat információk 5-ször több csomag paraméterei alapján. Ha a csomag a hálózati biztonsági csoport van, a szabály és a hálózati biztonsági csoport, amely a csomagot adja vissza.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Következő Ugrás</a>**  – meghatározza, hogy a következő ugrás a csomagok irányítása az Azure hálózati háló, lehetővé téve a bármely diagnosztizálhatja a helytelenül van konfigurálva, felhasználó által megadott útvonalakat.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Biztonsági csoport nézet</a>**  – lekérdezi a hatékony és alkalmazott biztonsági szabályok, amelyek érvényesek a virtuális gép.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">Hálózati biztonsági csoportok naplózásának</a>**  – a hálózati biztonsági csoportok folyamatnaplóit engedélyezi a forgalmat, amely engedélyezi vagy megtagadja a csoport biztonsági szabályai szerint naplók rögzítését. A folyamat egy 5-ször több információt – forrás IP-címe, cél IP-cím, forrásoldali portszám, céloldali Port és protokoll határozza meg.

## <a name="azure-storage-analytics"></a>Azure Storage Analytics

[A Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) is mérőszámokat tárol, amelyek összesített és kapacitási adatait tartalmazzák érkező kérések egy tárolási szolgáltatásba. Tranzakciók jelenti az API művelet szinten és a storage szolgáltatás szintjén, és a storage szolgáltatás szintjén jelentett kapacitás. Metrikaadatok is használható, elemezheti a storage szolgáltatás használatát, a storage szolgáltatás kérelmekre kapcsolatos problémák diagnosztizálása és a egy szolgáltatást használó alkalmazások teljesítményének javítása érdekében.

[Az Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) naplózhatja és mérőszámadatokat biztosít egy tárfiókot. Ezekkel az adatokkal kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálni lehet a tárfiókokkal kapcsolatos problémákat. A Storage Analytics naplózási érhető el a [Blob, Queue és Table szolgáltatások](https://docs.microsoft.com/azure/storage/storage-introduction). A Storage Analytics naplók sikeres és sikertelen kérések kapcsolatos részletes információk egy tárolási szolgáltatásba.

Ezt az információt a figyelheti az egyes kérések és a egy storage szolgáltatással kapcsolatos problémák diagnosztizálásához használható. Kérelmek van bejelentkezve a képességeinkhez mérten biztosítjuk. Naplóbejegyzések jönnek létre, csak ha a szolgáltatásvégpont kérelmekre. Például ha a tárfiók a Blob végpontja, de nem a tábla vagy üzenetsor végpontjait, csak-naplókat a Blob szolgáltatáshoz tartozó jön létre.

A Storage Analytics használatához minden egyes figyelni kívánt szolgáltatás külön-külön kell engedélyeznie. Engedélyezheti a a [az Azure portal](https://portal.azure.com/); további információkért lásd: [monitorozása az Azure Portal tárfiók](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). A Storage Analytics automatizáltan a REST API-t vagy az ügyféloldali kódtár is engedélyezheti. A szolgáltatás tulajdonságainak beállítása műveletet használja ahhoz, hogy a Storage Analytics külön-külön az egyes szolgáltatások.

Az összesített adatokat tárolja egy jól ismert blob (naplózás) és a jól ismert táblák (a metrikákat), amely a Blob és Table szolgáltatások API-k használatával is elérhetők.

A Storage Analytics esetében a 20 TB-ig, amely független a tárfiók teljes maximális tárolt adatok mennyisége. Az összes napló vannak tárolva [blokkblobok](https://docs.microsoft.com/azure/storage/storage-analytics) $logs nevű tárolóban, amely automatikusan létrejönnek, amikor a Storage Analytics engedélyezve van a storage-fiók.

A következő műveleteket végzi a Storage Analytics számlázhatók:

-   Kérelmek létrehozásához a blobok a naplózáshoz
-   Kérés létrehozása metrikákhoz táblaentitások.

> [!Note]
> A számlázással és az adatmegőrzési házirendek további információkért lásd: [Storage Analytics és a számlázás](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> Az optimális teljesítmény érdekében érdemes korlátozni a lehetséges szabályozás elkerülése érdekében a virtuális géphez csatlakoztatott nagy kihasználtságú lemezek számát. Az összes lemez nem folyamatban magas használhatók egy időben, a storage-fiók támogatják-e nagyobb számú lemez.

> [!Note]
> További információ a tárfiókok korlátai: [Azure Storage méretezhetőségi és Teljesítménycéljai](https://docs.microsoft.com/azure/storage/storage-scalability-targets).


A következő típusú hitelesített és névtelen kérelmeket a rendszer naplózza.

| Hitelesített  | Névtelen|
| :------------- | :-------------|
| Sikeres kérelmek | Sikeres kérelmek |
|Sikertelen kérelmek, beleértve az időkorlát, a szabályozás, hálózati, engedélyezési és egyéb hibák | -Kérések egy közös hozzáférésű Jogosultságkód (SAS), beleértve a sikeres és sikertelen kérelmek |
| -Kérések egy közös hozzáférésű Jogosultságkód (SAS), beleértve a sikeres és sikertelen kérelmek |Az ügyfél és a kiszolgáló időkorlátja hibák |
|   Elemzési adatok kérelmeket |    Sikertelen GET kérelmek 304 (nem módosított). Hibakód: |
| Kérést hoz létre a Storage Analytics, például a napló a létrehozás vagy törlés, a program nem naplózza. A naplózott adatok teljes listáját leírása itt található a [Storage Analytics naplózott műveletek és az állapotüzenetek](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) és [Storage Analytics naplóformátum](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) témaköröket. | Az összes többi sikertelen névtelen kérelmek nem jelentkezett. A naplózott adatok teljes listáját leírása itt található a [Storage Analytics naplózott műveletek és az állapotüzenetek](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) és [Storage Analytics naplóformátum](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |
## <a name="azure-active-directory"></a>Azure Active Directory

Azure ad-ben is magában foglalja a teljes körű identitáskezelési képességeket, beleértve a multi-factor authentication, az eszköz regisztrálása, önkiszolgáló jelszókezelés, önkiszolgáló csoportfelügyelet, kiemelt jogosultságú fiókok kezelése, szerepköralapú hozzáférés vezérlő, Alkalmazáshasználat monitorozását, részletes naplózást, és biztonsági figyelés és riasztások.

-   Az Azure ad-ben a többtényezős hitelesítés és feltételes hozzáférés alkalmazás biztonságának javítása.

-   Alkalmazáshasználat figyelése és az üzleti jelentéskészítés és a monitorozás biztonsági veszélyforrások elleni védelme.

Az Azure Active Directory (Azure AD) biztonsági, naplózási és tevékenységjelentéseket biztosít a címtárához. [Az Azure Active Directory naplózási jelentés](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) segítségével az ügyfelek, amelyek az Azure Active Directory privilegizált műveletek azonosításához. Privilegizált műveletek közé tartozik a jogosultságszint-emelés (például a szerepkör létrehozása vagy a jelszó alaphelyzetbe állítása), változó szabályzatkonfiguráció (például jelszó-szabályzatokat), és változásainak könyvtár-konfiguráció (például tartomány összevonási beállításainak módosítása).

A jelentések az esemény nevét, az aktor a célként megadott erőforrás érinti a változás és dátuma és időpontja (UTC) műveletet végrehajtó adja meg a naplórekordot. Ügyfelek képesek naplóesemények listájának beolvasása az Azure Active Directory-n keresztül a [az Azure portal](https://portal.azure.com/)leírtak szerint [az Auditnaplók megtekintése](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). A benne foglalt jelentések listája:

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



Az ezen jelentések adatai, például a SIEM rendszerekhez, naplózási és üzletiintelligencia-eszközök az alkalmazások hasznos lehet. Az Azure AD-jelentéskészítés [API-k](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) programozott hozzáférést biztosítanak a REST-alapú API-kon keresztül az adatok. Ezen API-k hívása különböző programozási nyelveken és eszközökkel.

Az Azure AD naplózási jelentés eseményei 180 napig maradnak meg.

> [!Note]
> A jelentések adatmegőrzési kapcsolatos további információkért lásd: [Azure Active Directory jelentés adatmegőrzési szabályai](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Tárolja az ügyfeleknek saját [események naplózása](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) hosszabb megőrzési időszakok, a Reporting API-val használható rendszeresen lekérni a naplózási események egy külön tárolóba.

## <a name="summary"></a>Összegzés

Ez a cikk összesítések, a személyes adatok védelme és biztonságossá tétele az adatokat, miközben nagy szoftverek, amelyek segítenek a szervezet informatikai infrastruktúrájának kezelésére. Microsoft tisztában van vele, hogy azok entrust-másoknak adataikat, amikor a megbízhatósági szigorú biztonsági igényel. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig. Biztonságossá tétele és az adatok védelme a Microsoft prioritást jelent.

Ez a cikk ismerteti

-   Adatok gyűjtése, feldolgozása, és a Log Analytics Suite részeként biztosított.

-   Az események gyors elemzése akár több adatforrásban. A biztonsági incidensek okozta károk minimalizálása érdekében azonosítani kell a biztonsági kockázatokat és fel kell mérni a fenyegetések és támadások terjedelmét és hatásait.

-   Támadási minták azonosítása a kimenő rosszindulatú IP-forgalom és a rosszindulatú fenyegetéstípusok vizualizációjával. Ismerje meg, a platformtól függetlenül a teljes környezet biztonsági állapotát.

-   A biztonsági vagy megfelelőségi ellenőrzésekhez szükséges összes napló- és esemény adatot rögzítheti. Idő és a biztonsági naplózást és a egy teljes, kereshető és exportálható napló- és eseményadat-készlet adja meg a szükséges erőforrások perjel.

<ul>
<li>Gyűjtse össze a biztonsággal kapcsolatos eseményeket, naplózási és illetéktelen behatolás elemzést monitoringeszközeivel figyelhető az eszközök:</li>
<ul>
<li>Biztonsági rendszer kialakításához</li>
<li>Jelentős probléma</li>
<li>Összesítések fenyegetések</li>
</ul>
</ul>

## <a name="next-steps"></a>További lépések

- [Biztonságközpontú tervezés és üzemeltetés](https://www.microsoft.com/trustcenter/security/designopsecurity)

A Microsoft a szolgáltatásokat és szoftvereket tervez a biztonságot szem érdekében győződjön meg arról, hogy a felhőalapú infrastruktúra rugalmas és támadások védeni.

- [Log Analytics |} A biztonság és megfelelőség](https://www.microsoft.com/cloud-platform/security-and-compliance)

A Microsoft biztonsági adat- és elemzési Több intelligens és hatékony fenyegetések észlelése végrehajtásához használható.

- [Az Azure Security Center tervezéséhez és](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide) lépéseket és feladatokat, is optimalizálhatja a Security Centerben a vállalat biztonsági igényeinek és felhőfelügyeleti modelljének alapú használatát.

