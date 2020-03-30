---
title: Az Azure működési biztonságának áttekintése| Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az Azure működési biztonságáról.
services: security
documentationcenter: na
author: unifycloud
manager: rkarlin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: tomsh
ms.openlocfilehash: bcaf45af4c02dad22e2cc611fa7ea5a32ad3a853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443886"
---
# <a name="azure-operational-security-overview"></a>Az Azure működési biztonságának áttekintése

[Az Azure működési biztonsága](/azure/security/fundamentals/operational-security) a felhasználók számára elérhető szolgáltatásokra, vezérlőkre és funkciókra vonatkozik adataik, alkalmazásaik és egyéb eszközeik védelmére a Microsoft Azure-ban. Ez egy olyan keretrendszer, amely a Microsoft számára egyedülálló képességek széles skálája révén szerzett ismereteket foglalja magában. Ezek a képességek közé tartozik a Microsoft Security Development Lifecycle (SDL), a Microsoft Security Response Center program, és a kiberbiztonsági fenyegetések mély ismertségét.

## <a name="azure-management-services"></a>Azure-kezelési szolgáltatások

Az informatikai műveleti csoport felelős az adatközpont-infrastruktúra, az alkalmazások és az adatok kezeléséért, beleértve ezeknek a rendszereknek a stabilitását és biztonságát. A növekvő összetett informatikai környezetekben szerzett biztonsági elemzések azonban gyakran megkövetelik a szervezetektől, hogy több biztonsági és felügyeleti rendszeradatait összekell, hogy terjeszd.

[A Microsoft Azure Monitor naplói](/azure/operations-management-suite/operations-management-suite-overview) egy felhőalapú informatikai felügyeleti megoldás, amely segít a helyszíni és a felhőalapú infrastruktúra kezelésében és védelmében. Alapvető funkcióit az Azure-ban futó következő szolgáltatások biztosítják. Az Azure több szolgáltatást is tartalmaz, amelyek segítenek a helyszíni és a felhőalapú infrastruktúra kezelésében és védelmében. Minden szolgáltatás egy adott felügyeleti funkciót biztosít. A szolgáltatások kombinálásával különböző felügyeleti forgatókönyveket érhet el. 

### <a name="azure-monitor"></a>Azure Monitor

[Az Azure Monitor](/azure/azure-monitor/overview) a felügyelt forrásokból származó adatokat gyűjti a központi adattárakba. Ezek az adatok eseményeket, teljesítményadatokat vagy az API-n keresztül biztosított egyéni adatokat tartalmazhatnak. Az adatok gyűjtése után riasztásra, elemzésre és exportálásra is elérhető.

A különböző forrásokból származó adatokat konszolidálhatja, és kombinálhatja az Azure-szolgáltatásokból származó adatokat a meglévő helyszíni környezettel. Az Azure Monitor naplók is egyértelműen elválasztja az adatok gyűjtését az adatok on végzett művelet, így minden művelet elérhető mindenféle adat.

### <a name="automation"></a>Automation

[Az Azure Automation](/azure/automation/automation-intro) lehetővé teszi a manuális, hosszú ideig futó, hibalehetőségeket rejtő és gyakran ismétlődő feladatok automatizálását, amelyeket általában felhő- és nagyvállalati környezetben hajtanak végre. Időt takarít meg, és növeli az adminisztratív feladatok megbízhatóságát. Még azt is ütemezi, hogy ezeket a feladatokat rendszeres időközönként automatikusan végre kell hajtani. A folyamatokat runbookok használatával automatizálhatja, vagy a kívánt állapotkonfiguráció használatával automatizálhatja a konfigurációkezelést.

### <a name="backup"></a>Backup

[Az Azure Backup](/azure/backup/backup-introduction-to-azure-backup) az Azure-alapú szolgáltatás, amely segítségével biztonsági másolatot készíthet (vagy megvédhet) és visszaállíthatja az adatokat a Microsoft Cloud ban. Az Azure Backup lecseréli a meglévő helyszíni vagy külső biztonsági mentési megoldást egy megbízható, biztonságos és költséghatékony felhőalapú megoldásra.

Az Azure Backup olyan összetevőket kínál, amelyeket a megfelelő számítógépen vagy kiszolgálón vagy a felhőben tölthet le és telepít. A telepítendő összetevő vagy ügynök attól függ, hogy mit szeretne megvédeni. Az Azure Backup összes összetevője (akár a helyszínen, akár a felhőben védi az adatokat) az Azure Recovery Services-tárolóba való biztonsági mentéséhez használható.

További információt az [Azure Backup összetevőitáblában](/azure/backup/backup-overview#what-can-i-back-up)talál.

### <a name="site-recovery"></a>Site Recovery

[Az Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) üzletmenet-folytonosságot biztosít a helyszíni virtuális és fizikai gépek Azure-ba vagy egy másodlagos helyre történő replikációjának vezénylésével. Ha az elsődleges hely nem érhető el, a felhasználók továbbra is dolgozhatnak a másodlagos helyre. Ha a rendszerek újra üzemképes üzemi sorrendben térnek vissza, visszaadja a feladatát. Az Azure Security Center segítségével intelligensebb és hatékonyabb fenyegetésészlelést hajthat végre.

## <a name="azure-active-directory"></a>Azure Active Directory

[Az Azure Active Directory (Azure AD)](/azure/active-directory/active-directory-enable-sso-scenario) egy átfogó identitáskezelési szolgáltatás, amely:

-   Lehetővé teszi az identitás- és hozzáférés-kezelést (IAM) felhőszolgáltatásként.
-   Központi hozzáférés-kezelést, egyszeri bejelentkezést (SSO) és jelentéskészítést biztosít.
-   Támogatja az integrált hozzáférés-kezelést [több ezer alkalmazás](https://azure.microsoft.com/marketplace/active-directory/) számára az Azure Marketplace-en, beleértve a Salesforce-ot, a Google Apps-t, a Box-ot és a Concur-t.

Az Azure AD az [identitáskezelési képességek](/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports)teljes készletét is tartalmazza, többek között a következőket:

- [Többtényezős hitelesítés](/azure/multi-factor-authentication/multi-factor-authentication)
- [Önkiszolgáló jelszókezelés](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Önkiszolgáló csoportkezelés](/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Kiemelt fiókkezelés](/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Szerepköralapú hozzáférés-vezérlés](/azure/role-based-access-control/overview)
- [Alkalmazáshasználat figyelése](/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Gazdag könyvvizsgálat](/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Biztonsági figyelés és riasztás](/azure/operations-management-suite/oms-security-responding-alerts)

Az Azure Active Directory használatával a partnerek és az ügyfelek (üzleti vagy fogyasztói) számára közzétett összes alkalmazás azonos identitás- és hozzáférés-kezelési képességekkel rendelkezik. Ez lehetővé teszi, hogy jelentősen csökkentse a működési költségeket.

## <a name="azure-security-center"></a>Azure Security Center

[Az Azure Security Center](/azure/security-center/security-center-intro) segítségével megelőzheti, észlelheti és reagálhat a fenyegetésekre, és nagyobb rálátással (és az azure-erőforrások biztonságának szabályozásával) biztosíthatja azokat. Integrált biztonsági figyelést és házirend-kezelést biztosít az előfizetések között. Segít észlelni azokat a fenyegetéseket, amelyek egyébként észrevétlenek maradnak, és a biztonsági megoldások széles ökoszisztémájával működik együtt.

[Védje meg a virtuális gép (VM) adatait](/azure/security-center/security-center-linux-virtual-machine) az Azure-ban azáltal, hogy betekintést nyújt a virtuális gép biztonsági beállításaiba, és figyeli a fenyegetéseket. A Security Center a következőket tudja megfigyelés alatt tartani a virtuális gépeken:

- Az operációs rendszer biztonsági beállításai az ajánlott konfigurációs szabályokkal.
- A rendszer biztonsága és a hiányzó kritikus frissítések.
- Végpontvédelmi javaslatok.
- Lemeztitkosítás érvényesítése.
- Hálózat-alapú támadások.

A Security Center [szerepköralapú hozzáférés-vezérlést (RBAC)](/azure/role-based-access-control/role-assignments-portal)használ. Az RBAC [beépített szerepköröket](../../role-based-access-control/built-in-roles.md) biztosít, amelyek az Azure-ban felhasználókhoz, csoportokhoz és szolgáltatásokhoz rendelhetők hozzá.

A Security Center felméri az erőforrások konfigurációját a biztonsági problémák és biztonsági rések azonosítására. A Biztonsági központban csak akkor láthatja az erőforráshoz kapcsolódó információkat, ha tulajdonosi, közreműködői vagy olvasói szerepkört kap ahhoz az előfizetéshez vagy erőforráscsoporthoz, amelyhez egy erőforrás tartozik.

>[!Note]
>Ha többet szeretne tudni a Biztonsági központban található szerepkörökről és engedélyezett műveletekről, olvassa el az Engedélyek az Azure Security Centerben című [témakört.](/azure/security-center/security-center-permissions)

A Security Center a Microsoft Monitoring Agent-et használja. Ez ugyanaz az ügynök, amelyet az Azure Monitor szolgáltatás használ. Az ügynöktől gyűjtött adatokat az Azure-előfizetéshez társított meglévő Log [Analytics-munkaterület](/azure/log-analytics/log-analytics-manage-access) vagy egy új munkaterület tárolja, figyelembe véve a virtuális gép földrajzi helyét.

## <a name="azure-monitor"></a>Azure Monitor

A felhőalapú alkalmazás teljesítményproblémái hatással lehetnek a vállalkozásra. Több összekapcsolt összetevő vel és gyakori felszabadulással a degradációk bármikor előfordulhatnak. Ha pedig egy alkalmazást fejleszt, a felhasználók általában olyan problémákat fedeznek fel, amelyeket nem talált a tesztelés során. Azonnal tudnia kell ezekről a problémákról, és eszközöket kell tudnia a problémák diagnosztizálására és rögzítésére.

[Az Azure Monitor](/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) az Azure-on futó szolgáltatások figyelésének alapvető eszköze. Infrastruktúra-szintű adatokat ad egy szolgáltatás átviteli sebességű és a környező környezetről. Ha az Azure-ban kezeli alkalmazásait, és eldönti, hogy felfelé vagy lefelé skálázhatja-e az erőforrásokat, az Azure Monitor az a hely, ahol elkezdheti.

A figyelési adatok segítségével is mély betekintést nyerhet az alkalmazásról. Ez a tudás segíthet az alkalmazások teljesítményének vagy karbantarthatóságának javításában, illetve az egyébként manuális beavatkozást igénylő műveletek automatizálásában.

Az Azure Monitor a következő összetevőket tartalmazza.

### <a name="azure-activity-log"></a>Azure-tevékenységnapló

Az [Azure-tevékenységnapló](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) betekintést nyújt az előfizetésben lévő erőforrásokon végrehajtott műveletekbe. Korábban "Napló" vagy "Működési napló" néven volt ismert, mert az előfizetések vezérlősíkeseményeit jelenti.

### <a name="azure-diagnostic-logs"></a>Azure diagnosztikai naplók

[Az Azure diagnosztikai naplók által](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) kibocsátott egy erőforrás, és gazdag, gyakori adatokat az adott erőforrás működéséről. Ezeknek a naplóknak a tartalma erőforrástípustól függően változik.

A Windows eseményrendszer-naplók a virtuális gépek diagnosztikai naplóinak egyik kategóriája. Blob, tábla és várólista naplók a storage-fiókok diagnosztikai naplóinak kategóriái.

A diagnosztikai naplók eltérnek a [tevékenységnaplótól.](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) A tevékenységnapló betekintést nyújt az előfizetésben lévő erőforrásokon végrehajtott műveletekbe. A diagnosztikai naplók betekintést nyújtanak az erőforrás által végrehajtott műveletekbe.

### <a name="metrics"></a>Mérőszámok

Az Azure Monitor telemetriai adatokat biztosít, amely betekintést nyújt az Azure-beli számítási feladatok teljesítményébe és állapotába. A legfontosabb típusú Azure telemetriai adatok a [metrikák](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) (más néven teljesítményszámlálók) által kibocsátott legtöbb Azure-erőforrások. Az Azure Monitor számos módszert kínál ezeknek a metrikáknak a figyeléséhez és a hibaelhárításhoz történő konfigurálására és felhasználására.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Az Azure Diagnostics lehetővé teszi a diagnosztikai adatok gyűjtését egy telepített alkalmazáson. A Diagnosztika bővítmény t különböző forrásokból használhatja. Jelenleg támogatottak [az Azure felhőszolgáltatási szerepkörei](/azure/vs-azure-tools-configure-roles-for-cloud-service), a Microsoft Windows rendszert futtató [Azure virtuális gépek](/azure/vs-azure-tools-configure-roles-for-cloud-service) és az Azure Service [Fabric.](/azure/monitoring-and-diagnostics/azure-diagnostics)

## <a name="azure-network-watcher"></a>Azure Network Watcher

Az ügyfelek teljes körű hálózatot építenek az Azure-ban az egyes hálózati erőforrások, például a virtuális hálózatok, az Azure ExpressRoute, az Azure Application Gateway és a terheléselosztók vezénylésével és írásával. A figyelés minden hálózati erőforráson elérhető.

A végpontok közötti hálózat összetett konfigurációkkal és erőforrások közötti interakciókkal rendelkezhet. Az eredmény olyan összetett forgatókönyvek, amelyek forgatókönyv-alapú figyelést igényelnek az [Azure Network Watcher segítségével.](/azure/network-watcher/network-watcher-monitoring-overview)

A Network Watcher leegyszerűsíti az Azure-hálózat figyelését és diagnosztizálását. A Hálózatfigyelő diagnosztikai és vizualizációs eszközeivel a következőket teheti:

- Távoli csomagrögzítéseket készíthet egy Azure-beli virtuális gépen.
- Folyamatnaplók használatával betekintést nyerhet a hálózati forgalomba.
- Diagnosztizálhatja az Azure VPN-átjárót és a kapcsolatokat.

A Network Watcher jelenleg a következő képességekkel rendelkezik:

- [Topológia](/azure/network-watcher/network-watcher-topology-overview): Az erőforráscsoport hálózati erőforrásai közötti különböző összeköttetéseket és társításokat ismerteti.
- [Változó csomagrögzítés](/azure/network-watcher/network-watcher-packet-capture-overview): Csomagadatok rögzítése a virtuális gépbe be- és ki. A fejlett szűrési lehetőségek és finomhangolt vezérlők, mint például az idő- és méretkorlátok megadásának képessége, sokoldalúságot biztosít. A csomagadatok tárolhatók egy blob tárolóban vagy a helyi lemezen .cap formátumban.
- [IP-folyamat ellenőrzése](/azure/network-watcher/network-watcher-ip-flow-verify-overview): Ellenőrzi, hogy egy csomag engedélyezett vagy engedélyezett-e az áramlási információk 5-ttűs csomagparaméterei alapján (cél IP, forrás IP, célport, forrásport és protokoll). Ha egy biztonsági csoport megtagadja a csomagot, a rendszer visszaadja azt a szabályt és csoportot, amely megtagadta a csomagot.
- [Következő ugrás:](/azure/network-watcher/network-watcher-next-hop-overview)Meghatározza a következő ugrás az Azure hálózati hálóban átirányított csomagok, így diagnosztizálhatja a helytelenül konfigurált felhasználó által definiált útvonalak.
- [Biztonsági csoport nézet:](/azure/network-watcher/network-watcher-security-group-view-overview)Leképezi a virtuális gépen alkalmazott hatékony és alkalmazott biztonsági szabályokat.
- [NSG-folyamatnaplók hálózati biztonsági csoportokhoz:](/azure/network-watcher/network-watcher-nsg-flow-logging-overview)Lehetővé teszi a csoport biztonsági szabályai által engedélyezett vagy elutasított forgalommal kapcsolatos naplók rögzítését. A folyamatot 5-s hangú információ határozza meg: forrás IP, cél IP, forrásport, célport és protokoll.
- [Virtuális hálózati átjáró és kapcsolat hibaelhárítása](/azure/network-watcher/network-watcher-troubleshoot-manage-rest): Lehetővé teszi a virtuális hálózati átjárók és kapcsolatok hibaelhárítását.
- [Hálózati előfizetési korlátok](/azure/network-watcher/network-watcher-monitoring-overview): Lehetővé teszi a hálózati erőforrás-használat korlátozás nélküli megtekintését.
- [Diagnosztikai naplók](/azure/network-watcher/network-watcher-monitoring-overview): Egyetlen ablaktáblát biztosít az erőforráscsoport hálózati erőforrásainak diagnosztikai naplóinak engedélyezéséhez vagy letiltásához.

További információt a [Hálózatfigyelő konfigurálása](/azure/network-watcher/network-watcher-create)című témakörben talál.

## <a name="cloud-service-provider-access-transparency"></a>Felhőszolgáltató– hozzáférés átláthatósága

[A Microsoft Azure ügyfélszéfje](customer-lockbox-overview.md) egy Azure Portalba integrált szolgáltatás, amely explicit vezérlést biztosít abban a ritka esetben, amikor egy Microsoft támogatási szakembernek szüksége lehet az adatokhoz való hozzáférésre a probléma megoldásához.
Nagyon kevés olyan eset van, például egy hibakeresési távelérési probléma, amikor a Microsoft támogatási szakemberének emelt szintű engedélyekre van szüksége a probléma megoldásához. Ilyen esetekben a Microsoft mérnökei just-in-time hozzáférési szolgáltatást használnak, amely korlátozott, időhöz kötött engedélyezést biztosít, és a szolgáltatáshoz csak a hozzáférésre van korlátozva.  
Bár a Microsoft mindig megszerezte az ügyfél beleegyezését a hozzáféréshez, az Ügyfélszéf most antól lehetővé teszi az ilyen kérelmek áttekintését és jóváhagyását vagy elutasítását az Azure Portalról. A Microsoft támogatási szakemberei nem kapnak hozzáférést, amíg ön jóvá nem hagyja a kérelmet.

## <a name="standardized-and-compliant-deployments"></a>Szabványosított és megfelelő telepítések

[Az Azure Blueprints](/azure/governance/blueprints/overview) lehetővé teszi a felhőalapú építészek és a központi informatikai csoportok számára, hogy olyan megismételhető Azure-erőforrásokat határozzanak meg, amelyek megvalósítják és megfelelnek a szervezet szabványainak, mintáinak és követelményeinek.  
Ez lehetővé teszi a DevOps-csapatok számára, hogy gyorsan felépítsenek és felálljanak az új környezetekben, és bízzanak abban, hogy olyan infrastruktúrával építik fel őket, amely fenntartja a szervezeti megfelelőséget.
A tervrajzok deklaratív módot biztosítanak a különböző erőforrássablonok és egyéb összetevők telepítésének vezénylésére, például:

- Szerepkör-hozzárendelések
- Szabályzat-hozzárendelések
- Azure Resource Manager-sablonok
- Erőforráscsoportok

## <a name="devops"></a>DevOps

A [Fejlesztői műveletek (DevOps)](https://www.visualstudio.com/learn/what-is-devops/) alkalmazásfejlesztése előtt a csapatok egy szoftverprogram üzleti követelményeinek összegyűjtéséért és a kód írásáért voltak felelősek. Ezután egy külön minőségbiztosítási csapat tesztelte a programot egy elszigetelt fejlesztési környezetben. Ha a követelmények teljesülnek, a minőségbiztosítási csapat kiadta a telepített műveletek kódját. A központi telepítési csapatok tovább töredezettek voltak csoportokra, például hálózatépítésre és adatbázisra. Minden alkalommal, amikor egy szoftver programot "dobtak át a falon" egy független csapat, ez hozzáadott szűk keresztmetszetek.

A DevOps lehetővé teszi a csapatok számára, hogy gyorsabban és olcsóbban nyújtsanak biztonságosabb, jobb minőségű megoldásokat. Az ügyfelek dinamikus és megbízható élményt várnak el a szoftverek és szolgáltatások fogyasztásakor. A csapatoknak gyorsan meg kell jelennie a szoftverfrissítésekről, és mérniük kell a frissítések hatását. Gyorsan kell reagálniuk új fejlesztési ismétlésekkel a problémák megoldására, vagy több értéket kell biztosítaniuk.  

A felhőalapú platformok, például a Microsoft Azure megszüntették a hagyományos szűk keresztmetszeteket, és segítettek az infrastruktúra áruba rakatálásában. Szoftver uralkodik minden üzleti, mint a legfontosabb differenciáló és tényező az üzleti eredmények. Egyetlen szervezet, fejlesztő vagy informatikai dolgozó sem kerülheti el vagy kerülheti el a DevOps-mozgalmat.

Az érett DevOps-szakemberek az alábbi eljárások közül többet is alkalmaznak. Ezek a gyakorlatok [magukban foglalják az embereket,](https://www.visualstudio.com/learn/what-is-devops-culture/) hogy az üzleti forgatókönyvek alapján stratégiákat alakítsanak ki. A szerszámozás segíthet a különböző gyakorlatok automatizálásában.

- [Az agilis tervezési és projektkezelési](https://www.visualstudio.com/learn/what-is-agile/) technikáksegítségével megtervezheti és elkülönítheti a munkát a sprintekbe, kezelheti a csapatkapacitást, és segít a csapatoknak a változó üzleti igényekhez való gyors alkalmazkodásban.
- [A verziókövetés, általában a Git segítségével,](https://www.visualstudio.com/learn/what-is-git/)lehetővé teszi a világ bármely pontján található csapatok számára, hogy megosszák a forrást, és integrálódjanak a szoftverfejlesztőeszközökkel a kiadási folyamat automatizálásához.
- [A folyamatos integráció](https://www.visualstudio.com/learn/what-is-continuous-integration/) a kód folyamatos egyesítését és tesztelését eredményezi, ami a hibák korai megtalálásához vezet.  Az egyéb előnyök közé tartozik a kevesebb idő, amelyet az egyesítési problémák elleni küzdelemre pazarolnak, és a fejlesztőcsapatok gyors visszajelzése.
- A szoftvermegoldások [folyamatos szállítása](https://www.visualstudio.com/learn/what-is-continuous-delivery/) éles és tesztelési környezetekben segít a szervezeteknek a hibák gyors javításában és a folyamatosan változó üzleti igények kielégítésében.
- A futó alkalmazások [figyelése](https://www.visualstudio.com/learn/what-is-monitoring/) – beleértve az alkalmazásállapothoz szükséges éles környezeteket, valamint az ügyfélhasználatot – segít a szervezeteknek egy hipotézis kialakításában, és gyorsan érvényesíteni vagy megcáfolni a stratégiákat.  A bővített adatok rögzítése és tárolása különböző naplózási formátumokban történik.
- [Az Infrastructure as Code (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) egy olyan gyakorlat, amely lehetővé teszi a hálózatok és virtuális gépek létrehozásának és lebontásának automatizálását és érvényesítését, hogy segítsen a biztonságos, stabil alkalmazásüzemeltetési platformok biztosításában.
- [A mikroszolgáltatások](https://www.visualstudio.com/learn/what-are-microservices/) architektúrája az üzleti használati esetek kis újrafelhasználható szolgáltatásokba való elkülönítésére szolgál.  Ez az architektúra lehetővé teszi a méretezhetőséget és a hatékonyságot.

## <a name="next-steps"></a>További lépések

A Biztonsági és naplózási megoldásról az alábbi cikkekben olvashat:

- [Biztonság és megfelelőség](https://azure.microsoft.com/overview/trusted-cloud/)
- [Azure Security Center](/azure/security-center/security-center-intro)
- [Azure Monitor](/azure/azure-monitor/overview)
