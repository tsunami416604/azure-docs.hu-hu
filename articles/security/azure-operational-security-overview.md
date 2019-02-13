---
title: Az Azure működési biztonság – áttekintés |} A Microsoft Docs
description: Ez a cikk az Azure operational security áttekintést nyújt.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: tomsh
ms.openlocfilehash: da58737314012255f26d344f279d855244f0c1ba
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116473"
---
# <a name="azure-operational-security-overview"></a>Az Azure működési biztonság – áttekintés

[Az Azure operational security](https://docs.microsoft.com/azure/security/azure-operational-security) hivatkozik a szolgáltatások, a vezérlők és a felhasználók számára elérhető szolgáltatások védelme érdekében az adatokat, alkalmazásokat és egyéb eszközök, Microsoft Azure-ban. Olyan keretrendszer, amely magában foglalja a különböző képességeket, amelyek a Microsoft egyedülálló szerzett ismeretek. Ezek közé tartozik a Microsoft biztonsági fejlesztési életciklus (SDL), a Microsoft Security Response Center programhoz és a kiberbiztonsági fenyegetések világának.

## <a name="azure-management-services"></a>Az Azure felügyeleti szolgáltatások

Az informatikai üzemeltetési csapat felelős az Adatközpont-infrastruktúrába, alkalmazások és adatok, beleértve a stabilitás és ezek a rendszerek biztonságát kezelése. Biztonsági következtetéseket között összetett IT-környezetek gyakran növelése azonban szükség van a szervezetek számára, hogy cobble együtt több biztonsági és felügyeleti rendszerek adatait.

[A Microsoft Log Analytics](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) egy felhőalapú, informatikai felügyeleti megoldás, amely segít a kezelése és védelme a helyszíni és felhőalapú infrastruktúrára. A következő szolgáltatásokat futtató Azure-ban a legfontosabb funkcióit biztosítja. Az Azure több tartalmaz szolgáltatások, amelyek segítségével kezelése és védelme a helyszíni és felhőalapú infrastruktúrára. Mindegyik szolgáltatás egy adott felügyeleti funkciót biztosít. Különféle felügyeleti forgatókönyvek valósíthatók szolgáltatások kombinálásával. 

### <a name="azure-monitor"></a>Azure Monitor

[Az Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) gyűjti az adatokat a felügyelt források részéről történő központi adattárakban. Ezek az adatok lehetnek események, teljesítményadatok vagy az API segítségével biztosított egyéni adatok. Az adatok begyűjtését követően érhető el a riasztások, elemzéshez vagy exportáláshoz. 

Különböző forrásokból származó adatok egyesítése, és az Azure-szolgáltatásokból származó adatokat kombinálva a meglévő helyszíni környezetben. A log Analytics, hogy az összes művelet is elérhető a különféle adatokon továbbá egyértelműen elkülöníti az adatok gyűjtését az adatok, végrehajtott műveletet.

### <a name="automation"></a>Automation

[Az Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) lehetővé teszi, hogy automatizálhatják a manuális, hosszan tartó, sok hibalehetőséget rejtő és gyakran ismételt feladatokat, amelyeket gyakran hajtanak végre a felhőalapú és vállalati környezetben. Ez a időt takaríthat meg, és növeli az adminisztratív feladatok megbízhatóságát. Azt is ezeket a feladatokat kell elvégezni, rendszeres időközönként automatikusan ütemezi. Folyamatok automatizálása runbookok használatával, vagy a konfigurációkezelés automatizálása a Desired State Configuration használatával.

### <a name="backup"></a>Backup

[Az Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) az Azure-alapú szolgáltatás, amely segítségével biztonsági mentése (vagy védelme) és állítsa vissza az adatokat a Microsoft Cloud szolgáltatásban. Az Azure Backup megbízható, biztonságos és költséghatékony felhőalapú megoldással váltja fel a meglévő helyszíni vagy telephelyen kívüli biztonsági mentési megoldását. 

Az Azure Backup kínál összetevőket, letöltheti és telepítheti a megfelelő számítógépre vagy kiszolgálóra, vagy a felhőben. A telepítendő összetevő vagy ügynök attól függ, hogy mit szeretne megvédeni. Minden Azure Backup-összetevővel (legyen szó akár a helyi védelméről és a felhőben) használható az adatok biztonsági mentésére az Azure Recovery Services-tárolót az Azure-ban. 

További információkért lásd: a [Azure Backup-összetevők táblázatában](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use).

### <a name="site-recovery"></a>Site Recovery

[Az Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) biztosítja az üzletmenet folytonosságát a helyszíni virtuális és fizikai gépek Azure-bA vagy egy másodlagos helyre replikálásával. Az elsődleges hely nem érhető el, ha átadja a feladatokat a másodlagos hely úgy, hogy a felhasználók is dolgozhat tovább. Vissza amikor a rendszer visszaveszi a feladatokat. Hajtsa végre több intelligens és hatékony fenyegetések észlelése a Azure Security Center.

## <a name="azure-active-directory"></a>Azure Active Directory

[Az Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) van olyan átfogó identitás-szolgáltatás, amely:

-   Lehetővé teszi az identitás és hozzáférés-kezelés (IAM) felhőszolgáltatás formájában.
-   Itt a központi felügyeletet, egyszeri bejelentkezés (SSO) és jelentéskészítés.
-   Támogatja az integrált hozzáférés-kezelés [ezer alkalmazáshoz](https://azure.microsoft.com/marketplace/active-directory/) az Azure Marketplace-en, beleértve a Salesforce, a Google Apps, a Box és a beleértve.

Azure ad-ben is magában foglalja a teljes körű [identitáskezelési képességeket](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports), beleértve a következőket:

- [Többtényezős hitelesítés](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)
- [Önkiszolgáló jelszókezelés](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Önkiszolgáló csoportkezelés](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Kiemelt jogosultságú fiókok kezelése](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Alkalmazáshasználat monitorozását](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Részletes naplózás](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Biztonsági monitorozási és riasztási](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts)

Az Azure Active Directoryval a partnerek és ügyfelek (üzleti és fogyasztói) közzétett összes alkalmazás legyen ugyanaz az identitásuk, és hozzáférés-kezelési képességeket. Ez lehetővé teszi, hogy jelentősen csökkentheti a működési költségeket.

## <a name="azure-security-center"></a>Azure Security Center

[Az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) megelőzése, észlelése, és háríthatja el a fenyegetéseket átláthatóbbá és (és szabályozhatóbbá) segít az Azure-erőforrások biztonságát. Integrált biztonsági monitorozást és felügyeletet biztosít az előfizetésekben. Ez segít egyébként észrevétlenül maradó fenyegetések észlelésére, és a biztonsági megoldások átfogó ökoszisztémájának működik.

[A virtuális gépek (VM) adatainak védelme érdekében](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) az Azure-ban azáltal, hogy a virtuális gép biztonsági beállításaiba betekintést és a fenyegetéseket. A Security Center a következőket tudja megfigyelés alatt tartani a virtuális gépeken:

- Operációs rendszer biztonsági beállításai ajánlott konfigurációs szabályokkal.
- Rendszer biztonsági és kritikus frissítések hiányoznak.
- Az Endpoint protection javaslatai.
- Lemeztitkosítás ellenőrzése.
- Hálózatalapú támadások.

A Security Center használ [szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). Az RBAC biztosít [beépített szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) , hogy a felhasználók, csoportok és Azure-szolgáltatások rendelhetők.

A Security Center a biztonsági problémák és biztonsági rések azonosításához erőforrásán konfiguráció értékeli. A Security Centerben lásd: csak akkor, ha a szerepkör tulajdonos, közreműködő vagy olvasó az előfizetés vagy erőforráscsoportot, amely egy erőforrás tartozik, a kapott egy erőforráshoz kapcsolódó információkat.

>[!Note]
>További információ a szerepkörökről, és láthatja a műveletek a Security Centerben engedélyezett [engedélyek az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-permissions).

A Security Center a Microsoft Monitoring Agentet használja. Ez a ugyanaz az ügynök a Log Analytics szolgáltatás által használt. Ettől az ügynöktől gyűjtött adatok tárolva van egy meglévő Log Analytics [munkaterület](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) társított Azure-előfizetését, vagy egy új munkaterületet, az a virtuális gép földrajzi helyének figyelembevételével.

## <a name="azure-monitor"></a>Azure Monitor

A felhőalkalmazások teljesítményével kapcsolatos problémákat befolyásolhatja az üzleti. Több egymáshoz kapcsolódó összetevők és a gyakori kiadások romlását fordulhat elő, tetszőleges időpontban. És ha egy alkalmazást fejleszt, a felhasználók általában problémákat, amelyek nem találta meg a tesztelés. Ha tisztában van az ezekkel kapcsolatos azonnal, és rendelkeznie kell az eszközök felderítésére és a problémák elhárítására.

[Az Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) alapszintű eszköz figyelése az Azure-on futó szolgáltatásokat. Biztosít egy szolgáltatás és a környezete adatforgalmáról infrastruktúra-szintű adatokat. Az alkalmazások az összes Azure-ban, és eldönti, hogy-vagy leskálázhatja az erőforrások kezelése, Azure Monitor-e a hely indítása.

Monitorozási adatok segítségével mélyebb betekintést az alkalmazással kapcsolatos. A Tudásbázis segítségével javíthatja az alkalmazás teljesítménye vagy Karbantarthatóság, vagy, amelyek egyébként manuális beavatkozásra műveletek automatizálása. 

Az Azure Monitor a következő összetevőket tartalmazza.

### <a name="azure-activity-log"></a>Azure-tevékenységnapló

A [Azure-tevékenységnapló](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) az előfizetésében erőforrásokon végrehajtott műveletek betekintést nyújt. Ez nevezték "Napló" vagy "Műveleti napló,", mert a vezérlősík események az előfizetésekre vonatkozó jelentést készít.

### <a name="azure-diagnostic-logs"></a>Azure diagnosztikai naplók

[Az Azure diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) az erőforrás által kibocsátott vannak, és adja meg a műveletet az erőforrás gazdag, gyakori adatait. Ezek a naplók a tartalom erőforrás típusa szerint változó.

Windows rendszer-eseménynaplói olyan virtuális gépek számára a diagnosztikai naplók egy kategóriáját. BLOB, tábla és üzenetsor-naplók kategóriába sorolhatók a diagnosztikai naplók tárfiókok esetében.

Diagnosztikai naplók különböznek a [tevékenységnapló](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). A tevékenységnaplóban a az előfizetésében erőforrásokon végrehajtott műveletekkel betekintést nyújt. Diagnosztikai naplók műveletekkel kapcsolatos információk az erőforrás által végrehajtott magát adja meg.

### <a name="metrics"></a>Mérőszámok

Az Azure Monitor biztosítja a telemetriai adatokból, hogy az Azure-ban a teljesítmény és a számítási feladatok állapotát betekintést nyújt. A legfontosabb Azure telemetriai adatok típusa a [metrikák](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) a legtöbb Azure-erőforrások által kibocsátott (is nevezik teljesítményszámlálók). Az Azure Monitor konfigurálása és figyelése és hibaelhárítása a metrikák felhasználásához több módszert is biztosít.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Az Azure diagnosztikai szolgáltatása lehetővé teszi, a központilag telepített alkalmazás a diagnosztikai adatok gyűjtését. A diagnosztikai bővítmény különböző forrásokból is használhatja. Jelenleg csak [Azure cloud service szerepkörök](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [Azure-beli virtuális gépek](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) fut a Microsoft Windows, és [Azure Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).

## <a name="azure-network-watcher"></a>Azure Network Watcher

Az ügyfelek előkészítését és az egyes hálózati erőforrások, például a virtuális hálózatok, az Azure ExpressRoute, az Azure Application Gateway létrehozása az Azure-ban egy végpontok közötti hálózati készítését és terheléselosztók. Figyelés a hálózati erőforrások mindegyike érhető el.

A végpontok közötti hálózati összetett konfigurációkkal és erőforrások közötti interakciók rendelkezhet. Az eredmény az összetett forgatókönyvek, forgatókönyv-alapú figyelés keresztül igénylő [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

A Network Watcher leegyszerűsíti, figyelése és diagnosztizálása az Azure-hálózat. A Network Watchert a diagnosztikai és vizualizációs eszközökkel is használhatja:

- Távoli csomagrögzítés igénybe egy Azure virtuális gépen.
- A hálózati forgalomról Folyamatnaplók használatával elemezheti.
- Az Azure VPN Gateway és kapcsolatok diagnosztizálása

A Network Watcher jelenleg a következő képességekkel rendelkezik:

- [Topológia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): A különböző csatlakozás és a egy erőforráscsoportba tartozó hálózati erőforrások egymáshoz rendelését nézetét jeleníti meg.
- [Változó csomagrögzítés](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): Rögzíti a csomagadatok adataikkal egy virtuális gépet. A fejlett szűrési lehetőségek és finomhangolt vezérlők, mint például az idő- és méretkorlátok megadásának képessége, sokoldalúságot biztosít. A csomag adatok tárolhatók a blob-tárolóban, vagy a helyi lemezen .cap formátumban.
- [IP-folyamat ellenőrzésével](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): Ellenőrzi, hogy egy csomag engedélyezett vagy megtagadott forgalmi adatok (cél IP-cím, forrásoldali IP-cím, Célport, forrásoldali portszám és protokoll) 5-ször több csomag paraméterei alapján. Ha egy biztonsági csoportot a csomag megtagadja, a szabály és a csoportot, amely a csomagot adja vissza.
- [A következő Ugrás](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Meghatározza, hogy a csomagok irányítása az Azure-beli hálózati hálóban, így bármely helytelenül konfigurált felhasználó által megadott útvonalak diagnosztizálhatja a következő ugrás.
- [Biztonsági csoport nézet](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): A hatékony és alkalmazott biztonsági szabályok, amelyek érvényesek a virtuális gép beolvasása.
- [Hálózati biztonsági csoportok folyamatnaplóit](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview): Engedélyezze a forgalmat, engedélyezett vagy tiltott a biztonsági szabályok a csoportban található naplók rögzítését. Határozza meg a folyamat 5-ször több információt: forrás IP, cél IP-cím, forrásoldali portszám, céloldali port és protokoll.
- [Virtuális hálózati átjáró és a kapcsolat hibaelhárítási](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): Lehetővé teszi a virtuális hálózati átjárók és kapcsolatok hibaelhárítása.
- [Hálózati előfizetési korlátozásait](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Lehetővé teszi, hogy a hálózati erőforrás-használati korlátozások megtekintése.
- [Diagnosztikai naplók](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Egytáblás, engedélyezheti vagy tilthatja le a hálózati erőforrásokat egy erőforráscsoportba tartozó diagnosztikai naplókat.

További információkért lásd: [Network Watcher konfigurálása](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="cloud-service-provider-access-transparency"></a>Cloud Service Provider hozzáférés Transparency

[A Microsoft Azure-ügyfél kulcstároló](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/) szolgáltatás integrálva van az Azure portál, amelyen explicit vezérlőelem a ritka esetben amikor a Microsoft támogatási Szakértőnk szükség lehet a probléma megoldásához szükséges adatokhoz való hozzáférést. Nincsenek nagyon kevés instancesről, például a hibakeresési távelérési probléma, ahol a Microsoft támogatási Szakértőnk a probléma megoldásához emelt szintű engedélyekkel kell rendelkeznie. Ezekben az esetekben a Microsoft mérnökei just-in-time adatelérési szolgáltatás, amely a szolgáltatás korlátozott hozzáférést biztosít a korlátozott, korlátozott idejű engedélyezési használja.  
A Microsoft mindig kapott hozzáférést az ügyfél beleegyezése, miközben ügyfél kulcstároló most teszi lehetővé, tekintse át és hagyja jóvá vagy az Azure Portalról az ilyen kérések elutasítása. A Microsoft támogatási szakemberei nem kapnak hozzáférést, amíg a kérelem jóváhagyása.

## <a name="standardized-and-compliant-deployments"></a>Szabványos és a megfelelő központi telepítések

[Az Azure tervezetek](../governance/blueprints/overview.md) engedélyezése a felhőben dolgozó tervezők és a központi technológia csoportok segítségével meghatározhatja egy megismételhető Azure-erőforrások, amelyek végrehajtása és a szervezet szabványok, minták és követelményeket tartunk.  
Ez lehetővé teszi a fejlesztési és üzemeltetési csapatok gyorsan hozhat létre, és új környezetek üzembe helyezését és megbízható, hogy azok készít őket, amely fenntartja a szervezeti megfelelőségi infrastruktúrával. Tervezetek történő központi telepítését a különböző erőforrássablonok és más összetevőket, például a deklaratív módszert biztosítanak: 

- Szerepkör-hozzárendelések
- Szabályzat-hozzárendelések
- Azure Resource Manager-sablonok
- Erőforráscsoportok

## <a name="devops"></a>DevOps

Mielőtt [fejlesztői műveletek (DevOps)](https://www.visualstudio.com/learn/what-is-devops/) fejlesztés, a csapatok is egy adott szoftverprogramot tartozó üzleti követelmények összegyűjtése és kód írása. Majd egy külön QA csapat tesztelte a program egy elkülönített fejlesztési környezetben. Ha követelmények is teljesülnek, a QA csapat kiadott a kód üzembe helyezéséhez műveletekhez. A telepítési csoport további csoportokra, például a hálózat és az adatbázis is töredezett. Minden alkalommal, amikor egy adott szoftverprogramot "lépett fel a fali keresztül" egy független csapatának, az felkerül a szűk keresztmetszeteket.

Fejlesztési és üzemeltetési csapatok így biztonságosabb, jobb minőségű megoldásokat, gyorsabban és olcsóbban lehetővé teszi. Ügyfelek várható a dinamikus és megbízható környezetet, szoftverek és szolgáltatások használatakor. Csapatok kell gyors ismételt futtatásával szoftverfrissítéseket, és mérheti a frissítések a hatását. Ezeket a kell az új fejlesztési ismétlések problémák gyorsan reagálhat, vagy további értéket adjon meg.  

A Microsoft Azure felhőplatformon eltávolítva a hagyományos szűk keresztmetszeteket és commoditize infrastruktúra segítségével. Szoftver reigns minden üzleti különbséget jelent és tényező az üzleti eredményeket. Nincs szervezet, fejlesztői vagy informatikai dolgozó is, vagy lehetőleg ne a fejlesztési és üzemeltetési áthelyezését.

Érett fejlesztési és üzemeltetési szakemberek fogadja el az alábbi eljárások közül. Ezek az eljárások [személyek között](https://www.visualstudio.com/learn/what-is-devops-culture/) az űrlap stratégiák az üzleti forgatókönyvek alapján. Eszközök segítségével automatizálhatja a különböző eljárások.

- [Agilis tervezés és projektmenedzsment](https://www.visualstudio.com/learn/what-is-agile/) technikák megtervezése és a sprintekben munkát elkülönítése, a csapat kapacitás kezelése és a segítségével gyorsan alkalmazkodhat a változó üzleti igények csapatok szolgálnak.
- [Általában a Git a verziókövetéshez](https://www.visualstudio.com/learn/what-is-git/), lehetővé teszi a csapatok, bárhol a világon, megoszthatja a forrás és a szoftver fejlesztői eszközökkel automatizálhatja a kiadási folyamathoz.
- [Folyamatos integráció](https://www.visualstudio.com/learn/what-is-continuous-integration/) meghajtók, a folyamatban lévő egyesítése és kódot, ami hibák kimutatásához korai vizsgálatára.  Egyéb előnyök kevesebb idő alatt az egyesítési problémák és a fejlesztői csapatok gyors visszajelzés elleni adattisztítást.
- [Folyamatos készregyártás](https://www.visualstudio.com/learn/what-is-continuous-delivery/) szoftvermegoldások termelési és tesztelési környezetek segítséget nyújt a szervezetek gyorsan hibajavítás és reagálni a folyamatosan változó üzleti követelmények.
- [Figyelés](https://www.visualstudio.com/learn/what-is-monitoring/) futó alkalmazások – beleértve az éles környezetek esetében az alkalmazás állapotáról, valamint az ügyfelek általi használatot – segítségével a szervezetek egy változat űrlap gyors ellenőrzése vagy disprove stratégiák.  Rögzített és különböző naplózási formátum tárolja a részletes adatokat.
- [Infrastruktúra-Code (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) eljárás, amely lehetővé teszi az automation és az ellenőrzés a létrehozástól és lebontás hálózatok és virtuális gépek biztonságos, stabil alkalmazást üzemeltető platformok továbbítása érdekében.
- [Mikroszolgáltatások](https://www.visualstudio.com/learn/what-are-microservices/) architektúra segítségével elkülönítése üzleti alkalmazási esetek kis újrafelhasználható szolgáltatásba.  Ez az architektúra lehetővé teszi a méretezhetőségi és a hatékonyság.

## <a name="next-steps"></a>További lépések

A biztonsági és auditálási megoldás kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Biztonság és megfelelőség](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)
