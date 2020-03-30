---
title: Azure működési biztonsága | Microsoft dokumentumok
description: Ismerje meg a Microsoft Azure Monitor naplóit, szolgáltatásait és működését.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 34c0c52945abc6e0ab74b1cb180581c76464bee8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749956"
---
# <a name="azure-operational-security"></a>Az Azure működési biztonsága
## <a name="introduction"></a>Bevezetés

### <a name="overview"></a>Áttekintés
Tudjuk, hogy a biztonság az első feladat a felhőben, és mennyire fontos, hogy pontos és időszerű információkat találjon az Azure biztonságáról. Az Azure alkalmazásaihoz és szolgáltatásaihoz való használatának egyik legjobb oka a rendelkezésre álló biztonsági eszközök és képességek széles skálájának kihasználása. Ezek az eszközök és képességek lehetővé teszik biztonságos megoldások létrehozását a biztonságos Azure platformon. A Windows Azure-nak biztosítania kell az ügyféladatok titkosságát, integritását és elérhetőségét, ugyanakkor lehetővé kell tennie az átlátható elszámoltathatóságot.

Annak érdekében, hogy az ügyfelek jobban megértsék a Microsoft Azure-ban az ügyfél és a Microsoft működési perspektíváiból megvalósított biztonsági vezérlők tömbjét, ez az "Azure Operational Security" című tanulmány íródott, amely átfogó tekintse meg a Windows Azure-ban elérhető működési biztonságot.

### <a name="azure-platform"></a>Azure Platform
Az Azure egy nyilvános felhőszolgáltatási platform, amely az operációs rendszerek, programozási nyelvek, keretrendszerek, eszközök, adatbázisok és eszközök széles választékát támogatja. Linux-tárolókat futtathat a Docker-integrációval; alkalmazások készítése JavaScript, Python, .NET, PHP, Java és Node.js nyelven; háttérrendszereket készíthet iOS, Android és Windows rendszerű eszközökre. Az Azure Cloud szolgáltatás ugyanazokat a technológiákat támogatja, amelyekben már több millió fejlesztő és informatikai szakember támaszkodik és bízik.

Amikor egy nyilvános felhőszolgáltatóra épít, vagy informatikai eszközöket telepít át, akkor az adott szervezet azon képességére támaszkodik, amely az alkalmazások és az adatok védelmére szolgál a felhőalapú eszközök biztonságának kezeléséhez biztosított szolgáltatásokkal és vezérlőkkel.

Az Azure infrastruktúráját a létesítménytől az ügyfelek millióinak egyidejű befogadására szolgáló alkalmazásokig tervezték, és megbízható alapot biztosít, amelyre a vállalkozások megfelelhetnek biztonsági követelményeiknek. Emellett az Azure a konfigurálható biztonsági beállítások széles skáláját biztosítja, és lehetővé teszi azok vezérlését, hogy testre szabhassa a biztonságot, hogy megfeleljen a szervezet központi telepítései egyedi követelményeinek. Ez a dokumentum segít megérteni, hogy az Azure biztonsági képességei hogyan segíthetnek ezeknek a követelményeknek a teljesítésében.

### <a name="abstract"></a>Abstract
Az Azure Operational Security a felhasználók számára elérhető szolgáltatásokra, vezérlőkre és funkciókra vonatkozik adataik, alkalmazásaik és egyéb eszközeik védelmére a Microsoft Azure-ban. Az Azure Operational Security egy olyan keretrendszerre épül, amely a Microsoft számára egyedi különböző képességek révén szerzett ismereteket tartalmazza, beleértve a Microsoft Security Development Lifecycle (SDL), a Microsoft Security Response Center programot, és a kiberbiztonsági fenyegetési környezet mély tudatosítása.

Ez a tanulmány ismerteti a Microsoft megközelítését az Azure működési biztonságával kapcsolatosan a Microsoft Azure felhőplatformján belül, és a következő szolgáltatásokat ismerteti:
1.  [Azure Monitor](../../azure-monitor/index.yml)

2.  [Azure Security Center](../../security-center/security-center-intro.md)

3.  [Azure Monitor](../../azure-monitor/overview.md)

4.  [Az Azure Network figyelője](../../network-watcher/network-watcher-monitoring-overview.md)

5.  [Azure Storage-elemzés](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active címtár](../../active-directory/fundamentals/active-directory-whatis.md)


## <a name="microsoft-azure-monitor-logs"></a>A Microsoft Azure Figyelő naplói

A Microsoft Azure Monitor naplói a hibrid felhő informatikai felügyeleti megoldása. Az Azure Monitor-naplók a meglévő System Center-telepítés kiterjesztéséhez vagy a meglévő System Center-telepítés kiterjesztéséhez maximális rugalmasságot és felügyeletet biztosítanak az infrastruktúra felhőalapú felügyeletéhez.

![Azure Monitor-naplók](./media/operational-security/azure-operational-security-fig1.png)

Az Azure Monitor naplóival bármilyen felhőben kezelheti bármely példányát, beleértve a helyszíni, az Azure, az AWS, a Windows Server, a Linux, a VMware és az OpenStack példányokat, alacsonyabb költséggel, mint a versenyképes megoldások. A felhőalapú világszámára épített Azure Monitor-naplók új megközelítést kínálnak a vállalat kezeléséhez, amely a leggyorsabb és leginkább költséghatékony módja az új üzleti kihívásoknak való megfeleltetésnek, valamint az új számítási feladatok, alkalmazások és felhőkörnyezetek kezelésére.

### <a name="azure-monitor-services"></a>Az Azure Monitor szolgáltatásai

Az Azure Monitor-naplók alapvető funkcióit az Azure-ban futó szolgáltatások készlete biztosítja. Mindegyik szolgáltatás egy adott felügyeleti funkciót biztosít, és a szolgáltatások kombinálásával különféle felügyeleti forgatókönyvek valósíthatók meg.

| Szolgáltatás  | Leírás|
| :------------- | :-------------|
| Azure Monitor-naplók | Figyeli és elemzi a különféle erőforrások, köztük a fizikai és virtuális gépek rendelkezésre állását és teljesítményét. |
|Automation | Automatizálja a manuális folyamatokat, és érvényesíti a fizikai és virtuális gépekre vonatkozóan megadott konfigurációkat. |
| Backup | Biztonsági másolatot és visszaállítást a kritikus adatokról. |
| Site Recovery | Biztosítja a kritikus fontosságú alkalmazások magas rendelkezésre állását. |

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók

[Az Azure Monitor naplók figyelési](https://azure.microsoft.com/documentation/services/log-analytics) szolgáltatásokat biztosít a felügyelt erőforrásokból származó adatok központi tárházba gyűjtésével. Ezek az adatok lehetnek események, teljesítményadatok vagy az API segítségével biztosított egyéni adatok. Az összegyűjtésüket követően az adatok használhatók riasztáshoz, elemzéshez vagy exportáláshoz.


Ez a módszer lehetővé teszi a különböző forrásokból származó adatok konszolidálását, így kombinálhatja az Azure-szolgáltatásokból származó adatokat a meglévő helyszíni környezettel. Továbbá egyértelműen elkülöníti az adatok gyűjtését az adatokon végzett műveletektől, így az összes művelet végrehajtható a különféle adatokon.


![Azure Monitor-naplók](./media/operational-security/azure-operational-security-fig2.png)

Az Azure Monitor szolgáltatás biztonságosan kezeli a felhőalapú adatokat a következő módszerekkel:
-   adatszegregáció
-   adatmegőrzés
-   fizikai biztonság
-   incidenskezelés
-   Megfelelés
-   biztonsági szabványok tanúsításai

### <a name="azure-backup"></a>Azure Backup

[Az Azure Backup](https://azure.microsoft.com/documentation/services/backup) adatbiztonsági mentési és visszaállítási szolgáltatásokat nyújt, és az Azure Monitor termék- és szolgáltatáscsomag része.
Védelmet biztosít az alkalmazásadatok számára, valamint évekig megőrzi őket minden tőkebefektetés nélkül és minimális működési költségek mellett. Az alkalmazás-munkaterhelések, például az SQL Server és a SharePoint mellett a fizikai és a virtuális Windows-kiszolgálókadatairól is biztonsági másolatot tud kapni. A System Center [Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) is használhatja a védett adatok Replikálására az Azure-ba redundancia és hosszú távú tárolás érdekében.


Az Azure Backup védett adatainak tárolása egy meghatározott földrajzi régióban elhelyezkedő biztonságimásolat-tárolóban történik. Az adatok replikálása ugyanabban a régióban, és a ttól függően, hogy a tároló típusa, is replikálható egy másik régióba a további rugalmasság érdekében.

### <a name="management-solutions"></a>Felügyeleti megoldások
[Az Azure Monitor](../../security-center/security-center-intro.md) a Microsoft felhőalapú informatikai felügyeleti megoldása, amely segít a helyszíni és a felhőalapú infrastruktúra kezelésében és védelmében.


[A felügyeleti megoldások](../../monitoring/monitoring-solutions.md) olyan előre csomagolt logikai készletek, amelyek egy adott felügyeleti forgatókönyvet valósítanak meg egy vagy több Azure Monitor-szolgáltatás használatával. Különböző megoldások érhetők el a Microsofttól és a partnerektől, amelyeket könnyedén hozzáadhat Azure-előfizetéséhez, hogy növelje befektetése értékét az Azure Monitorban. Partnerként létrehozhat saját megoldásokat az alkalmazások és szolgáltatások támogatásához, és az Azure Piactéren vagy a rövid útmutató sablonokon keresztül biztosíthatja azokat a felhasználóknak.


![Felügyeleti megoldások](./media/operational-security/azure-operational-security-fig4.png)

Egy jó példa egy olyan megoldásra, amely több szolgáltatást használ további funkciók biztosításához, az [Update Management megoldás.](../../automation/automation-update-management.md) Ez a megoldás az Azure Monitor windowsos és Linuxos [naplóügynökét](../../log-analytics/log-analytics-queries.md) használja az egyes ügynökök szükséges frissítéseivel kapcsolatos információk gyűjtéséhez. Ezeket az adatokat az Azure Monitor naplók tárházba, ahol elemezheti azt egy mellékelt irányítópulton.

Központi telepítés létrehozásakor az [Azure Automation](../../automation/automation-intro.md) runbookok a szükséges frissítések telepítéséhez használatosak. A teljes folyamatot a portálon felügyelheti, és nem kell foglalkoznia a mögöttes részletekkel.

## <a name="azure-security-center"></a>Azure Security Center

Az Azure Security Center segít megvédeni az Azure-erőforrásokat. Integrált biztonsági figyelést és házirend-kezelést biztosít az Azure-előfizetésekben. A szolgáltatáson belül nem csak az Azure-előfizetések, hanem [az erőforráscsoportok](../../azure-resource-manager/management/overview.md#resource-groups)ellen is definiálhat házirendeket, így részletesebb lehet.

### <a name="security-policies-and-recommendations"></a>Biztonsági szabályzatok és javaslatok

A biztonsági szabályzat határozza meg azoknak a vezérlőelemeknek a körét, amelyeket a rendszer az adott előfizetésen vagy erőforráscsoporton belüli erőforrásokhoz javasol.

A Security Centerben a vállalat biztonsági elvárásainak és az alkalmazások típusának vagy az adatok érzékenységének megfelelően állíthatja be a szabályzatokat.

![Biztonsági szabályzatok és javaslatok](./media/operational-security/azure-operational-security-fig5.png)


Az előfizetési szinten engedélyezett házirendek automatikusan továbbítanak az előfizetésen belüli összes erőforráscsoportra, ahogy az a jobb oldali ábrán látható:


### <a name="data-collection"></a>Adatgyűjtés

A Security Center adatokat gyűjt a virtuális gépekről a biztonsági állapotuk értékeléséhez, a biztonsági javaslatok létrehozásához és a fenyegetésekkel kapcsolatos riasztásokhoz. Amikor az első hozzáférés Security Center, adatgyűjtés engedélyezve van az összes virtuális gép az előfizetésben. Az adatgyűjtés ugyan ajánlott, de le is állíthatja azt a Security Center szabályzatában található adatgyűjtési lehetőség kikapcsolásával.

### <a name="data-sources"></a>Adatforrások

- Az Azure Security Center az alábbi forrásokból kapott adatok elemzésével biztosítja a biztonsági állapot áttekinthetőségét, azonosítja a biztonsági réseket, javításokat javasolj, valamint észleli az aktív fenyegetéseket:

-   Azure Services: az adott erőforrás-szolgáltatóval kommunikálva üzembe helyezett Azure-szolgáltatások konfigurációjával kapcsolatos információkat használja.

- Hálózati forgalom: a hálózati forgalom metaadataiból vett mintát használja a Microsoft infrastruktúrájából, például a forrás és a cél IP-címét és portját, a csomagméretet és a hálózati protokollt.

-   Partnermegoldások: Az integrált partnermegoldásoktól, például tűzfalaktól és kártevőirtó megoldásoktól érkező biztonsági riasztásokat használja.

-   Saját virtuális gépek: Konfigurációs és biztonsági eseményekkel kapcsolatos információkat használ (például Windows-esemény- és vizsgálati naplókat, IIS-naplókat, rendszernapló-üzeneteket és a virtuális gépekről származó összeomlási memóriaképeket).

### <a name="data-protection"></a>Adatvédelem

Az Azure Security Center biztonsággal kapcsolatos adatokat gyűjt és dolgoz fel (például konfigurációs információkat, metaadatokat, eseménynaplókat, összeomlási memóriaképeket és még sok mást), hogy segítsen az ügyfeleknek a fenyegetések megelőzésében, észlelésében és elhárításában. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig.

-   **Az adatok elkülönítése**: az adatok logikailag elkülönítve vannak tárolva a szolgáltatás egyes összetevőiben. Az összes adat szervezet szerint van megcímkézve. Ez a címkézés megmarad az adatok teljes életciklusa alatt, és a szolgáltatás minden rétegében érvényes.

-   **Adathozzáférés:** A biztonsági javaslatok biztosítása és a potenciális biztonsági fenyegetések vizsgálata érdekében a Microsoft munkatársai hozzáférhetnek az Azure-szolgáltatások által gyűjtött vagy elemzett információkhoz, beleértve az összeomlási memóriaképfájlokat, a folyamatlétrehozási eseményeket, a virtuális gép lemezpillanatképeit és az összetevőket, amelyek véletlenül ügyféladatokat vagy a virtuális gépekről származó személyes adatokat tartalmazhatnak. Betartjuk a [Microsoft Online Services általános szerződési feltételeit és adatvédelmi nyilatkozatát,](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)amely kijelenti, hogy a Microsoft nem használja fel az Ügyféladatokat, és nem származtat belőle adatokat hirdetési vagy hasonló kereskedelmi célokra.

-   **Az adatok felhasználása**: a Microsoft a különböző bérlőknél észlelt mintákat és fenyegetésre vonatkozó intelligenciát használ a megelőzési és észlelési funkcióihoz, és ezt az [adatvédelmi nyilatkozatában](https://www.microsoft.com/en-us/privacystatement/OnlineServices/) ismertetett adatvédelmi kötelezettségeinek megfelelően teszi.

### <a name="data-location"></a>Az adatok helye

Az Azure Security Center ideiglenes másolatokat gyűjt az összeomlási memóriaképek fájljairól, és elemzi ezeket a biztonsági réseket kihasználó támadások és a sikeres feltörések nyomai után kutatva. Az Azure Security Center az elemzéseket ugyanazon a földrajzi helyen végzi el, ahol a munkaterület található, és az elemzés után törli az ideiglenes másolatokat. A gépösszetevők tárolása központilag történik, ugyanabban a régióban, ahol a virtuális gép is található.

-   **A tárfiókok:** A tárfiók van megadva minden régióban, ahol a virtuális gépek futnak. Ez lehetővé teszi, hogy abban a régióban tárolja az adatait, amelyben az a virtuális gép található, amelyről az adatok gyűjtése történik.

-   **Az Azure Security Center tárhelye**: A biztonsági riasztásokkal (beleértve a partnerriasztásokat), javaslatokkal és biztonsági állapottal kapcsolatos információk tárolása jelenleg az Egyesült Államokban, központilag történik. Ez az információ magában foglalhat az Ön virtuális gépeiről összegyűjtött kapcsolódó konfigurációs adatokat és biztonsági eseményeket szükség szerint annak érdekében, hogy megjeleníthessük Önnek a biztonsági riasztásokat, a javaslatokat vagy a biztonsági állapot adatait.


## <a name="azure-monitor"></a>Azure Monitor

Az [Azure Monitor naplózza a biztonság](../../security-center/security-center-monitoring.md) és a naplózás i megoldás lehetővé teszi, hogy az informatikai aktívan figyelje az összes erőforrást, amely segít minimalizálni a biztonsági incidensek hatását. Az Azure Monitor naplói biztonság és naplózás a biztonsági tartományok, amelyek az erőforrások figyelésére használható. A biztonsági tartomány gyors hozzáférést biztosít a beállításokhoz, a biztonsági figyeléshez a következő tartományokat részletesebben ismerteti:

-   Kártevőfelmérés
-   Frissítések felmérése
-   Identitás és hozzáférés.

Az Azure Monitor az erőforrások adott típusaira vonatkozó információkra mutató mutatókat biztosít. Vizualizációt, lekérdezést, útválasztást, riasztást, automatikus méretezést és automatizálást kínál az Azure-infrastruktúrából (activity log) és az egyes Azure-erőforrásokból (diagnosztikai naplók) származó adatokon.

![Azure Monitor](./media/operational-security/azure-operational-security-fig6.png)


A felhőalapú alkalmazások összetettek, sok mozgó alkatrészrel. Figyelés adatokat biztosít annak érdekében, hogy az alkalmazás kifogástalan állapotban marad. Ez is segít, hogy elhárít a lehetséges problémákat, vagy hibaelhárítási múlt is.

Emellett a figyelési adatok segítségével mély betekintést nyerhet az alkalmazásról. Ez a tudás segíthet az alkalmazások teljesítményének vagy karbantarthatóságának javításában, illetve az egyébként manuális beavatkozást igénylő műveletek automatizálásában.

### <a name="azure-activity-log"></a>Azure-tevékenységnapló


Ez egy napló, amely betekintést nyújt az előfizetésben lévő erőforrásokon végrehajtott műveletekbe. A tevékenységnaplót korábban "Naplónaplók" vagy "Működési naplók" néven ismerték, mivel az előfizetések vezérlősíkeseményeit jelenti.

![Azure-tevékenységnapló](./media/operational-security/azure-operational-security-fig7.png)

A tevékenységnapló segítségével meghatározhatja az előfizetés erőforrásaira felvett írási műveletek (PUT, POST, DELETE) "mit, ki és mikor" című szöveget. Azt is megismerheti a művelet állapotát és más releváns tulajdonságokat. A tevékenységnapló nem tartalmazza a klasszikus modellt használó erőforrások olvasási (GET) műveleteit vagy műveleteit.

### <a name="azure-diagnostic-logs"></a>Azure diagnosztikai naplók

Ezeket a naplókat egy erőforrás bocsátja ki, és gazdag, gyakori adatokat szolgáltat az adott erőforrás működéséről. Ezeknek a naplóknak a tartalma erőforrástípustól függően változik.

A Windows eseményrendszer-naplói például a virtuális gépek diagnosztikai naplójának egyik kategóriája, a tábla- és várólistanaplók a storage-fiókok diagnosztikai naplóinak kategóriái.

A diagnosztikai naplók eltérnek a [tevékenységnaplótól (korábbi nevén napló vagy működési napló).](../../azure-monitor/platform/platform-logs-overview.md) A tevékenységnapló betekintést nyújt az előfizetésben lévő erőforrásokon végrehajtott műveletekbe. A diagnosztikai naplókban az erőforrás által végrehajtott műveletekkel kapcsolatos információk találhatók meg.

### <a name="metrics"></a>Mérőszámok

Az Azure Monitor lehetővé teszi, hogy telemetriát használjon, hogy betekintést nyerjen az Azure-beli számítási feladatok teljesítményébe és állapotába. A legfontosabb típusú Azure telemetriai adatok a metrikák (más néven teljesítményszámlálók) által kibocsátott legtöbb Azure-erőforrások. Az Azure Monitor számos módszert kínál ezeknek a [metrikáknak](../../monitoring/monitoring-data-collection.md) a figyeléséhez és a hibaelhárításhoz történő konfigurálására és felhasználására. A metrikák a telemetria értékes forrásai, és lehetővé teszik a következő feladatok elvégzését:

-   Az erőforrás (például egy virtuális gép, webhely vagy logikai alkalmazás) **teljesítményét úgy követheti nyomon,** hogy a mérőszámokat egy portáldiagramon ábrázolja, és a diagramot egy irányítópultra tűzi.

-   **Értesítést kaphat egy olyan problémáról,** amely hatással van az erőforrás teljesítményére, amikor egy metrika átlép egy bizonyos küszöbértéket.

-   **Konfigurálja az automatikus műveleteket,** például egy erőforrás automatikus méretezése vagy egy runbook kilövése, ha egy metrika átlép egy bizonyos küszöbértéket.

-   **Speciális elemzéseket** vagy jelentéseket végezhet az erőforrás teljesítményéről vagy használati tendenciáiról.

-   **Archiválja** az erőforrás teljesítményét vagy állapotát megfelelőségi vagy naplózási célokra.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Az Azure-on belüli képesség teszi lehetővé a diagnosztikai adatok gyűjtését egy telepített alkalmazáson. Használhatja a diagnosztikai bővítmény különböző forrásokból. Jelenleg támogatott az [Azure Cloud Service Web és a Worker Roles](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service), a Microsoft Windows rendszert futtató Azure virtuális [gépek](../../virtual-machines/windows/overview.md) és a [Service Fabric.](../../azure-monitor/platform/diagnostics-extension-overview.md) Más Azure-szolgáltatások saját külön diagnosztikával rendelkeznek.

## <a name="azure-network-watcher"></a>Azure Network Watcher

A hálózati biztonság naplózása létfontosságú a hálózati biztonsági rések észleléséhez, valamint az informatikai biztonsági és szabályozási irányítási modellnek való megfelelés biztosításához. A Biztonsági csoport nézetben lekérheti a konfigurált hálózati biztonsági csoportot és biztonsági szabályokat, valamint a hatályos biztonsági szabályokat. Az alkalmazott szabályok listájával meghatározhatja a megnyitott portokat, és felmérheti a hálózati biztonsági rést.

[A Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) egy regionális szolgáltatás, amely lehetővé teszi a feltételek figyelése és diagnosztizálása hálózati szinten az Azure-ban, az Azure-ban és az Azure-ból. A Network Watcher segítségével a Network Watcher segítségével megismerheti, diagnosztizálhatja és betekintést nyerhet a hálózatába az Azure-ban. Ez a szolgáltatás tartalmazza a csomagrögzítést, a következő ugrást, az IP-folyamat ellenőrzését, a biztonsági csoport nézetét, az NSG folyamatnaplóit. A forgatókönyvszint-figyelés a hálózati erőforrások teljes nézetét biztosítja, ellentétben az egyes hálózati erőforrások figyelésével.

![Azure Network Watcher](./media/operational-security/azure-operational-security-fig8.png)

A Network Watcher jelenleg a következő képességekkel rendelkezik:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Naplónaplók</a>**– a hálózatok konfigurációjának részeként végrehajtott műveletek et naplózza a rendszer. Ezek a naplók megtekinthetők az Azure Portalon, vagy leolvashatók a Microsoft eszközeivel, például a Power BI vagy a külső eszközök használatával. A naplózási naplók a portálon, a PowerShellen, a CLI-n és a Rest API-n keresztül érhetők el. A naplókról további információt az Erőforrás-kezelővel végzett naplózási műveletek című témakörben talál. A naplónaplók az összes hálózati erőforráson végzett műveletekhez érhetők el.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">Az IP-folyamat ellenőrzése</a>** – Ellenőrzi, hogy egy csomag engedélyezett vagy elutasított-e az 5-ttűs csomagparaméterek (cél IP, Forrás IP, célport, forrásport és protokoll) alapján. Ha egy hálózati biztonsági csoport megtagadja a csomagot, a rendszer visszaadja a csomagot megtagadó szabályt és hálózati biztonsági csoportot.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Következő ugrás</a>** – Meghatározza a következő ugrás az Azure Network Fabric-ben irányított csomagok, amely lehetővé teszi a helytelenül konfigurált felhasználó által definiált útvonalak diagnosztizálását.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Biztonsági csoport nézet</a>** – Leszögezi a virtuális gépen alkalmazott hatékony és alkalmazott biztonsági szabályokat.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">NSG-folyamatnaplózás</a>** – A hálózati biztonsági csoportok folyamatnaplói lehetővé teszik a csoport biztonsági szabályai által engedélyezett vagy elutasított forgalommal kapcsolatos naplók rögzítését. A folyamatot egy 5-tétes információ határozza meg – forrás IP, cél IP, forrásport, célport és protokoll.

## <a name="azure-storage-analytics"></a>Azure Storage-statisztika

[A Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) olyan metrikákat tárolhat, amelyek összesített tranzakcióstatisztikákat és kapacitásadatokat tartalmaznak a storage-szolgáltatásoknak küldött kérelmekről. A tranzakciók at az API-művelet szintjén és a tárolási szolgáltatás szintjén is jelentik, a kapacitást pedig a tárolási szolgáltatás szintjén. Metrikák adatok segítségével elemezheti a tárolási szolgáltatás használatát, diagnosztizálhatja a rendszerkérésekkel kapcsolatos problémákat a tárolási szolgáltatás, és a szolgáltatás thasználó alkalmazások teljesítményének javítása.

[Az Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) naplózást hajt végre, és metrikaadatokat biztosít egy tárfiókhoz. Ezeket az adatokat a kérelmek nyomon követésére, a használati trendek elemzésére és a tárfiókkal kapcsolatos problémák diagnosztizálására használhatja. A Storage Analytics naplózása a [Blob, a Várólista és](../../storage/common/storage-introduction.md)a Table szolgáltatásokhoz érhető el. A Storage Analytics naplózza a tárolási szolgáltatásoknak elküldött sikeres és sikertelen kérelmek részletes információit.

Ezekkel az információkkal monitorozhatók az egyes kérelmek és diagnosztizálhatók a tárolási szolgáltatások problémái. A kérelmek naplózása a legjobb erőfeszítés alapján történik. A naplóbejegyzések csak akkor jönnek létre, ha a szolgáltatás végpontjának kérései vannak. Például ha egy tárfiók tevékenységet a Blob végpontjában, de nem a tábla vagy a várólista végpontok, csak a Blob szolgáltatáshoz tartozó naplók jönnek létre.

A Storage Analytics használatához külön-külön kell engedélyeznie minden egyes figyelni kívánt szolgáltatáshoz. Az [Azure Portalon](https://portal.azure.com/)engedélyezheti; További információt a [Tárfiók figyelése az Azure Portalon című témakörben talál.](../../storage/common/storage-monitor-storage-account.md) A Storage Analytics programozott módon is engedélyezhető a REST API-n vagy az ügyfélkódtáron keresztül. A Szolgáltatás tulajdonságainak beállítása művelettel engedélyezheti a Storage Analytics-et egyenként az egyes szolgáltatásokhoz.

Az összesített adatok egy jól ismert blobban (naplózás) és jól ismert táblákban (metrikák) tárolódnak, amelyek a Blob szolgáltatás és a Table service API-k használatával érhetők el.

A Storage Analytics 20 TB-os korláttal rendelkezik a tárfiók teljes korlátáttól független tárolt adatok mennyiségére vonatkozóan. Az összes napló [tanéven](../../storage/common/storage-analytics.md) ként $logs tárolóban tárolóban tárolóba kerül, amely automatikusan létrejön, ha a Storage Analytics engedélyezve van egy tárfiókhoz.

A Storage Analytics által végrehajtott alábbi műveletek számlázhatók:

-   Blobok naplózáshoz való létrehozására irányuló kérelmek
-   A metrikák táblaentitások létrehozására vonatkozó kérések.

> [!Note]
> A számlázási és adatmegőrzési szabályzatokkal kapcsolatos további információkért lásd: [Storage Analytics and Billing](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> Az optimális teljesítmény érdekében korlátozni szeretné a virtuális géphez csatlakoztatott nagy mértékben kihasznált lemezek számát, hogy elkerülje a szabályozást. Ha az összes lemez nem nagy mértékben kihasznált egy időben, a tárfiók támogatja a nagyobb számú lemezt.

> [!Note]
> A tárfiókok korlátairól a [szabványos tárfiókok méretezhetőségi céljai](../../storage/common/scalability-targets-standard-account.md)című témakörben talál további információt.


A rendszer a következő típusú hitelesített és névtelen kérelmeket naplózza.

| Hitelesített  | Névtelen|
| :------------- | :-------------|
| Sikeres kérelmek | Sikeres kérelmek |
|Sikertelen kérések, köztük az időtúllépések, torlódások, valamint a hálózati, hitelesítési és egyéb hibák | Kérelmek megosztott hozzáférésű aláírást (SAS) használó, beleértve a sikertelen és sikeres kérelmeket |
| Kérelmek megosztott hozzáférésű aláírást (SAS) használó, beleértve a sikertelen és sikeres kérelmeket |Ügyfél- és kiszolgálóoldali időtúllépési hibák |
|   Elemzési adatokhoz kapcsolódó kérelmek |    304-es (Nincs módosítva) hibakóddal ellátott sikertelen GET-kérések |
| A Storage Analytics által tett kérelmek, például a napló létrehozása vagy törlése, nem kerülnek naplózásra. A naplózott adatok teljes listáját a [Storage Analytics naplózott műveletek és állapotüzenetek és](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) a Storage Analytics log [format](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) témakörök dokumentálják. | Az összes többi sikertelen névtelen kérelem nem kerül naplózásra. A naplózott adatok teljes listáját a [Storage Analytics naplózott műveletek és állapotüzenetek és](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) a Storage Analytics log format [dokumentálja.](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) |

## <a name="azure-active-directory"></a>Azure Active Directory

Az Azure AD az identitáskezelési képességek teljes csomagját is tartalmazza, beleértve a többtényezős hitelesítést, az eszközregisztrációt, az önkiszolgáló jelszókezelést, az önkiszolgáló csoportkezelést, a kiemelt fiókkezelést, a szerepköralapú hozzáférést ellenőrzés, alkalmazáshasználat-figyelés, bővített naplózás, valamint biztonsági figyelés és riasztás.

-   Az Azure AD többtényezős hitelesítéssel és feltételes hozzáféréssel javíthatja az alkalmazások biztonságát.

-   A biztonsági jelentésekkel és figyeléssel figyelheti az alkalmazások használatát, és megvédheti vállalkozását a speciális fenyegetésekkel szemben.

Az Azure Active Directory (Azure AD) biztonsági, naplózási és tevékenységjelentéseket biztosít a címtárához. [Az Azure Active Directory naplózási jelentés](../../active-directory/active-directory-reporting-azure-portal.md) segít az ügyfeleknek az Azure Active Directoryban előforduló kiemelt műveletek azonosításában. A kiemelt műveletek közé tartoznak a jogosultságszint-emelésmódosításai (például a szerepkör létrehozása vagy a jelszó visszaállítása), a házirend-konfigurációk módosítása (például jelszóházirendek) vagy a címtárkonfiguráció módosításai (például a tartományösszevonási beállítások módosításai).

A jelentések az esemény nevének, a műveletet végző szereplőnek, a módosítás által érintett célerőforrásnak, valamint a dátumnak és az időnek a naplózási rekordját adják (UTC-ben). Az ügyfelek az Azure Active Directory naplózási eseményeinek listáját az [Azure Portalon](https://portal.azure.com/)keresztül kérhetik le, a [naplónaplók megtekintése](../../active-directory/reports-monitoring/overview-reports.md)című dokumentumban leírtak szerint. A benne foglalt jelentések listája:

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
| |Jelszó-visszaállítási tevékenység |   |



Ezeknek a jelentéseknek az adatai hasznosak lehetnek az alkalmazások számára, például a SIEM-rendszerek, a naplózás és az üzletiintelligencia-eszközök számára. Az Azure AD [jelentéskészítési API-k](../../active-directory/active-directory-reporting-api-getting-started-azure-portal.md) biztosít programozott hozzáférést az adatokhoz egy sor REST-alapú API-k. Ezeket az API-kat különböző programozási nyelvekből és eszközökből hívhatja.

Az Azure AD-naplózási jelentésben lévő események 180 napig megmaradnak.

> [!Note]
> A jelentések megőrzéséről az [Azure Active Directory jelentésmegőrzési házirendjei](../../active-directory/reports-monitoring/reference-reports-data-retention.md)című témakörben talál további információt.

A [naplózási események](../../active-directory/active-directory-reporting-activity-audit-logs.md) hosszabb megőrzési időszakokra történő tárolása iránt érdeklődő ügyfelek számára a Reporting API segítségével rendszeresen lehívhat naplózási eseményeket egy külön adattárba.

## <a name="summary"></a>Összefoglalás

Ez a cikk összefoglalja az adatvédelem és az adatok védelme, miközben olyan szoftverek és szolgáltatások, amelyek segítenek kezelni az informatikai infrastruktúra a szervezet. A Microsoft elismeri, hogy amikor az adataikat másokra bízzák, ez a bizalom szigorú biztonságot igényel. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig. Az adatok védelme és védelme a Microsoft egyik legfontosabb prioritása.

Ez a cikk

-   Hogyan gyűjti, dolgozza fel és biztosítja az adatokat az Azure Monitor csomagban.

-   Gyorsan elemezheti az eseményeket több adatforrásban. Azonosítsa a biztonsági kockázatokat, és ismerje meg a fenyegetések és támadások hatókörét és hatását a biztonsági rés okozta károk csökkentése érdekében.

-   Azonosítsa a támadási mintákat a kimenő rosszindulatú IP-forgalom és a rosszindulatú fenyegetéstípusok megjelenítésével. Ismerje meg a teljes környezet biztonsági állapotát platformtól függetlenül.

-   Rögzítse a biztonsági vagy megfelelőségi naplózáshoz szükséges összes napló- és eseményadatot. Csökkentse a biztonsági naplózás teljes, kereshető és exportálható napló- és eseményadatkészlettel való ellátásához szükséges időt és erőforrásokat.

<ul>
<li>Gyűjtse össze a biztonsággal kapcsolatos eseményeket, naplózza és elemezheti a szabálysértéseket, hogy közelről szemmel tarthassa eszközeit:</li>
<ul>
<li>Biztonsági testtartás</li>
<li>Figyelemre méltó kérdés</li>
<li>Összefoglalása fenyegetések</li>
</ul>
</ul>

## <a name="next-steps"></a>Következő lépések

- [Tervezés és üzembiztonság](https://www.microsoft.com/trustcenter/security/designopsecurity)

A Microsoft a szolgáltatásokat és a szoftvereket a biztonság szem előtt tartásával tervezi meg, hogy a felhőalapú infrastruktúra rugalmas legyen, és védve legyen a támadásokkal szemben.

- [Azure Monitor naplók | Biztonsági & megfelelőség](https://www.microsoft.com/cloud-platform/security-and-compliance)

A Microsoft biztonsági adatainak és elemzésének segítségével intelligensebb és hatékonyabb fenyegetésészlelést végezhet.

- [Az Azure Security Center tervezése és műveletei](../../security-center/security-center-planning-and-operations-guide.md) A Biztonsági központ használatának optimalizálásához a szervezet biztonsági követelményei és a felhőkezelési modell alapján követheti a lépéseket és feladatokat.

