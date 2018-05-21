---
title: Az Azure operational biztonsági áttekintése |} Microsoft Docs
description: Ez a cikk áttekintést az Azure operational biztonsági.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: c0413678aad16105f732ef23fb60c61fddcdad45
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="azure-operational-security-overview"></a>Az Azure operational biztonsági áttekintése
[Az Azure operational biztonsági](https://docs.microsoft.com/azure/security/azure-operational-security) védelmére az adatok, alkalmazások és egyéb eszközök a Microsoft Azure-ban a szolgáltatások, a vezérlők és a felhasználók számára elérhető szolgáltatások hivatkozik. Egy keretrendszer, amely magában foglalja a tapasztalatok teszik a különböző képességeket, amelyeket a Microsoft egyedi keresztül. Ezek a képességek közé tartoznak a Microsoft biztonsági fejlesztési életciklus (SDL), a Microsoft Security Response Center program és a részletes tájékoztatást nyújthatnak a számítógépes veszélyforrásainak tükrében megfigyelhető a.

## <a name="operations-management-suite"></a>Operations Management Suite
Az informatikai műveletek csapat adatközpont infrastruktúrájában, alkalmazások és adatok, beleértve a stabilitás és az ilyen rendszerek biztonsági felügyeletéért felelős. Biztonság és ellenőrzött való növelésével gyakran összetett informatikai környezetben különböző azonban van szükség a szervezetek cobble egyszerre több biztonsági és felügyeleti rendszerek adatait.

[A Microsoft Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) felhőalapú informatikai felügyeleti megoldás, amely segít a kezelése és védelme a helyszíni és felhőalapú infrastruktúra. A fő funkciókat biztosítja az Azure-ban futó a következő szolgáltatások. Minden szolgáltatás egy adott felügyeleti funkciót biztosít. Kombinálhatja services különböző felügyeleti lehetőségeket eléréséhez. 

### <a name="log-analytics"></a>Log Analytics
[Az Azure Naplóelemzés](http://azure.microsoft.com/documentation/services/log-analytics) által kezelt erőforrások egy központi tárházban történő adatainak begyűjtése az Operations Management Suite figyelési szolgáltatásokat biztosít. Ezek az adatok tartalmazhatnak eseményeket, teljesítményadatokat vagy egyéni adatok az API-n keresztül megadott. Az adatok begyűjtését követően érhető el riasztási, elemzés és exportálása. 

Különböző forrásokból származó adatok egyesítése, és az Azure-szolgáltatások adatok egyesíthető a meglévő helyszíni környezetben. A Naplóelemzési, hogy az összes műveletek érhetők el az összes típusú adatok is egyértelműen elválasztja az adatok gyűjtését az adatok, végrehajtott műveletet.

### <a name="automation"></a>Automation
[Azure Automation szolgáltatásbeli](https://docs.microsoft.com/azure/automation/automation-intro) is biztosítja, hogy a felhőalapú és nagyvállalati környezetben általában végrehajtott manuális, hosszan futó, hibákhoz vezethet, és gyakran ismétlődő feladatok automatizálásához. Időt takaríthat meg, és növeli a felügyeleti feladatok megbízhatóságát. Akkor is ütemezi a rendszeres időközönként automatikusan elvégzendő feladatok. Folyamatok automatizálása a runbookok használatával, vagy konfigurációkezelés automatizálhatják a célállapot-konfiguráció használatával.

### <a name="backup"></a>Backup
[Azure biztonsági mentés](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) az Azure-alapú szolgáltatás, amely segítségével készítsen biztonsági másolatot (vagy védelme) és az adatokat a Microsoft Cloud visszaállítása. Azure biztonsági mentés lecseréli a meglévő helyszíni vagy a helyszínen biztonsági mentési megoldás egy felhőalapú megoldás, amely megbízható, biztonságos és költség-versenyképesség fenntartása érdekében. 

Azure biztonsági mentés kínál az összetevőkről, amelyek letöltése és telepítése a megfelelő számítógépre vagy kiszolgálóra, vagy a felhőben. A telepítendő összetevő vagy ügynök attól függ, hogy mit szeretne megvédeni. Minden Azure Backup szolgáltatás-összetevők (hogy számára kíván védelmet biztosítani a helyi vagy a felhőben) segítségével adatokról biztonsági másolatot az Azure Recovery Services-tároló az Azure-ban. 

További információkért lásd: a [Azure biztonsági mentés összetevők tábla](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use).

### <a name="site-recovery"></a>Site Recovery
[Az Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) az üzletmenet folytonossága biztosítja, mivel a helyszíni virtuális és fizikai gépek Azure-bA vagy másodlagos helyre replikálásával. Az elsődleges hely nem érhető el, ha a rendszer átadja a másodlagos helyre, hogy a felhasználók is tovább dolgozik. Ön nem vissza, ha a rendszerek működésének adja vissza. Használja az Azure Security Center több intelligens és hatékony fenyegetések észlelése végrehajtásához.

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) van egy átfogó szolgáltatott identitási szolgáltatáshoz:

-   Lehetővé teszi, hogy a felhő alapú szolgáltatásként identitások és hozzáférések felügyeletéhez (IAM).
-   Központi kezelés, az egyszeri bejelentkezés (SSO) és jelentéskészítési biztosít.
-   Támogatja az integrált kezelés [alkalmazások ezer](https://azure.microsoft.com/marketplace/active-directory/) az Azure piactéren, például a Salesforce-, Google Apps, be, és Concur.

Az Azure AD is tartalmazó teljes csomag [identitáskezelési funkciói](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports), beleértve a következőket:

- [Többtényezős hitelesítés](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)
- [Eszközregisztráció]( https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-overview)
- [Önkiszolgáló jelszókezelés](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Önkiszolgáló csoportkezelés](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Védett fiókok kezelése](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Alkalmazás használatának figyelése](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Részletes naplózás](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Biztonsági figyelés és riasztás](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts)

Az Azure Active Directoryval a partnerek és a felhasználók (üzleti vagy fogyasztó) közzétett összes alkalmazás ugyanazzal az identitással rendelkezik, és hozzáférés-kezelési képességeket. Ez lehetővé teszi, hogy jelentősen csökkenti a működési költségek.

## <a name="azure-security-center"></a>Azure Security Center
[Az Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) segítségével megakadályozása, észleli, és a láthatóság növelésével fenyegetések válaszolni (és vezérlése) az Azure-erőforrások biztonságának. Biztosít integrált biztonsági monitorozást és -előfizetése. Észleli a veszélyeket, amely szabályzatkezelést segít, és számos biztonsági megoldással együttműködik.

[Virtuális gépek (VM) adatainak védelme](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) lássák a virtuális gép biztonsági beállításainak megadása és a fenyegetések figyelése az Azure-ban. A Security Center a következőket tudja megfigyelés alatt tartani a virtuális gépeken:

-   Operációs rendszer biztonsági beállításait az ajánlott konfiguráció szabályait.
-   Rendszer biztonsági és kritikus frissítések hiányoznak.
-   Az Endpoint protection javaslatok.
-   Lemez titkosítási érvényesítése.
-   Hálózati támadásoktól.

A Security Center által használt [szerepköralapú hozzáférés-vezérlést (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). Biztosítja az RBAC [beépített szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) , hogy a felhasználók, csoportok és az Azure rendelhető.

A Security Center értékeli a konfigurációs az erőforrások biztonsági problémák és biztonsági rések azonosítása. A biztonsági központban tekintse meg egy erőforrás csak akkor, ha hozzá vannak rendelve a szerepkör tulajdonos, közreműködő vagy olvasó az előfizetés vagy az erőforráscsoportot, amelyhez tartozik egy erőforrás kapcsolatos adatokat.

>[!Note]
>További információ a szerepkörök és engedélyezett műveletek a biztonsági központban, [engedélyek az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-permissions).

A Security Center a Microsoft Monitoring Agent használja. Ez a ugyanaz az ügynök, amely az Operations Management Suite és a Log Analytics szolgáltatás használatára. Ez az ügynök gyűjtött adatokat tárolja egy meglévő Log Analyticshez [munkaterület](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) társított Azure-előfizetése vagy egy új munkaterületet, figyelembe véve a földrajzi hely, a virtuális gép.

## <a name="azure-monitor"></a>Azure Monitor
A cloud app teljesítményével kapcsolatos problémákat hatással lehet az üzleti. A több összekapcsolt összetevőkkel és gyakori kiadásokban degradations bármikor fordulhat elő. És ha az alkalmazást, a felhasználók általában felderítése a tesztjei során nem talált problémákat. Tudnia kell ezekről a problémákról azonnal, és az eszközök felderítésére és a problémák elhárítására vonatkozó rendelkeznie kell.

[Az Azure figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) alapvető eszköz Azure-on futó szolgáltatások figyelése. Biztosít egy szolgáltatás és a környezet átviteli infrastruktúra szintű adatait. Ha az összes Azure-ban és eldöntötte, hogy a méretezési felfelé vagy lefelé erőforrások alkalmazásokat felügyel, Azure figyelőjének értéke a kiindulási.

Figyelési adatok segítségével az alkalmazással kapcsolatos átfogó megismerésében. Ezt az információt nyújt segítséget az alkalmazások teljesítményének vagy karbantartási követelmények, vagy, amelyek egyébként kézi beavatkozás műveletek automatizálására. 

Az Azure a figyelő a következő összetevőket tartalmazza.

### <a name="azure-activity-log"></a>Azure tevékenységnapló
A [Azure tevékenységnapló](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) erőforrást az előfizetésében a végrehajtott műveletek betekintést nyújt. Azt korábban hívták "Napló" vagy "Műveleti napló", mert azt jelenti, hogy a vezérlő-vezérlősík események az előfizetésekhez.

### <a name="azure-diagnostic-logs"></a>Azure diagnosztikai naplók
[Az Azure diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) erőforrás által kibocsátott, és adja meg a műveletet az erőforrás gazdag, gyakori adatait. Ezek a naplók tartalmának erőforrástípusok szerint változik.

A Windows rendszer-eseménynaplói olyan virtuális gépek esetén a diagnosztikai naplók egy kategóriát. BLOB, table és a várólista naplók storage-fiókok diagnosztikai naplók kategóriájuk.

Diagnosztikai naplók eltérnek a [tevékenységnapló](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). A műveletnapló erőforrást az előfizetésében a végrehajtott műveletek betekintést nyújt. Diagnosztikai naplók Észreveheti az olyan műveletek, hogy az erőforrás végre magát.

### <a name="metrics"></a>Mérőszámok
Az Azure telemetriai adat, amely betekintést nyújt a teljesítmény és a munkaterhelések állapotát az Azure biztosít. A legfontosabb típusú Azure telemetriai adatok a [metrikák](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) az Azure erőforrások által kibocsátott (is hívott teljesítményszámlálók). Az Azure figyelő többféleképpen is konfigurálhatja, és felhasználhatják a figyeléshez és hibaelhárításhoz metrikákat.

### <a name="azure-diagnostics"></a>Azure Diagnostics
Az Azure Diagnostics lehetővé teszi, hogy a telepített alkalmazás diagnosztikai adatok gyűjtésére. A különböző forrásokból használja a diagnosztika bővítményét. Jelenleg támogatott még [Azure cloud service szerepkörök](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [Azure virtuális gépek](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) Microsoft Windows rendszerű és [Azure Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).


## <a name="azure-network-watcher"></a>Azure Network Watcher
Az ügyfelek hozhat létre egy Azure-végpontok közötti hálózati levezényli és az egyes hálózati erőforrások, például Azure ExpressRoute, Azure Application Gateway, virtuális hálózatok létrehozása, és terheléselosztókat. Figyelési érhető el minden olyan a hálózati erőforrásokhoz.

A végpontok közötti hálózati összetett konfigurációk és erőforrások közötti interakció rendelkezhet. Összetett forgatókönyvek esetében, amelyeket a forgatókönyv-alapú figyelési eredménye [Azure hálózati figyelőt](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

Hálózati figyelőt egyszerűbbé teszi a figyelése és diagnosztizálása az Azure-hálózat. A diagnosztikai és a képi megjelenítés eszközök a hálózati figyelőt használhatja:
- Egy Azure virtuális gép távoli csomag rögzítésekre igénybe.
- A hálózati forgalom folyamata naplók segítségével betekintést nyereség érdekében.
- Diagnosztizálja az Azure VPN Gateway és kapcsolatok.

Hálózati figyelőt jelenleg a következő képességekkel rendelkezik:

- [Topológia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): jeleníti meg a különböző csatlakozás és egy erőforráscsoportban található hálózati erőforrások egymáshoz rendelését.
-   [Változó csomagrögzítéssel](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): mindkét virtuális gép csomagadatok rögzíti. Speciális szűrési beállítások és finomhangolható vezérlők, például az idő beállítása és a méretezés korlátozások, adja meg az indít. A csomagadatok a blob-tárolóban, vagy a helyi lemezen .cap formátumban tárolható.
-   [IP-adatfolyam ellenőrizze](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): 5 rekordos csomag paraméterek folyamata információ (cél IP-címe, forrás IP-címe, Célport, Forrásport és protokoll) alapján ellenőrzi, ha egy csomag engedélyezett vagy megtagadott. Ha egy biztonsági csoportot a csomag megtagadja, a szabály és a csoportot, amely a csomag megtagadva adja vissza.
-   [Következő Ugrás](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): meghatározza, hogy a következő ugrás a csomagok továbbítása az Azure-hálózat hálóban, így felderítheti az esetleges nincs megfelelően konfigurálva a felhasználó által definiált útvonalak.
-   [Biztonsági csoport megtekintése](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): lekérdezi a hatékony és alkalmazott biztonsági szabályokat, amelyek érvényesek a virtuális gép.
-   [NSG folyamata naplókat a hálózati biztonsági csoportok](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview): lehetővé teszik a naplók engedélyezett vagy megtagadott a csoport biztonsági szabályai szerint forgalom rögzítése. A folyamat 5 rekordos adatokat határozzák meg: forrás IP-, a cél IP-cím, a forrásport, a célport és a protokoll.
-   [Virtuális hálózati átjáró és a kapcsolat hibaelhárítási](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): lehetővé teszi a virtuális hálózati átjárók és kapcsolatok hibáinak elhárítása.
-   [Előfizetési korlátozásait a hálózati](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): lehetővé teszi a hálózati erőforrás-használati korlátozások megtekintéséhez.
-   [Diagnosztikai naplók](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): engedélyezheti vagy tilthatja le a hálózati erőforrások az erőforráscsoportban diagnosztikai naplók egytáblás biztosít.

További információkért lásd: [konfigurálása hálózati figyelőt](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="devops"></a>DevOps
Mielőtt [fejlesztői műveletek (DevOps)](https://www.visualstudio.com/learn/what-is-devops/) alkalmazásfejlesztés, csapatok volt feladata üzleti követelmények szoftver adatgyűjtési és kódot. Egy külön csapat QA tesztelik a program elkülönített fejlesztői környezetet. Ha követelményeknek való megfelelés, a QA team kiadott műveletek telepíteni a kódot. A telepítési csoport további csoportok, például a hálózat és az adatbázis volt töredezett. Minden alkalommal, amikor egy program "dobott keresztül a fali" egy független csapathoz szűk keresztmetszetek hozzá.

DevOps lehetővé teszi, hogy a csoportok kínálja biztonságosabb, magasabb színvonalú gyorsabb és olcsóbban. Az ügyfelek a dinamikus és megbízható élmény várhatnak, amikor a szoftverek és -szolgáltatások fel. Csoportok gyorsan kell szoftverfrissítések többször és mérjük a hatását a frissítéseket. Azok a kell, gyorsan képesek reagálni az új fejlesztési ismétlési problémák, vagy további értéket adjon meg.  

Felhő platformokon, például a Microsoft Azure eltávolította a hagyományos szűk keresztmetszetei és infrastruktúra commoditize segített. Szoftver reigns minden üzleti a legfontosabb különbséget és tényező az üzleti tevékenységét. Nincs szervezet, fejlesztői vagy informatikai dolgozó is, vagy kerülje a DevOps mozgása.

Érett DevOps szakemberek az alábbi eljárásokkal számos fogad el. Ezeket a gyakorlatokat [tartalmaz, amely személyek](https://www.visualstudio.com/learn/what-is-devops-culture/) az űrlap stratégiák az üzleti forgatókönyvek alapján. Tooling segíthet a különböző eljárások automatizálására.

-   [Agilis tervezési és a projekt kezelése](https://www.visualstudio.com/learn/what-is-agile/) technikák segítségével tervezze meg és sprints való munka elkülönítése, team kapacitás kezelése, valamint gyorsan igazítja a üzleti igények változásaira csoportok segítségével.
-   [Verziókövetés, általában a Git](https://www.visualstudio.com/learn/what-is-git/), lehetővé teszi, hogy a csoportok bárhol a világon forrás megosztásához, és integrálása a fejlesztési szoftvereszközök a kiadási folyamat automatizálása.
-   [Folyamatos integrációt](https://www.visualstudio.com/learn/what-is-continuous-integration/) meghajtók, a folyamatban lévő egyesítése és a kódot, ami hibák keresése korai vizsgálatára.  Egyéb előnyök rövidebb idő alatt az egyesítési problémákat és a fejlesztési csapat gyors visszajelzése elleni kihasználatlan tartalmazza.
-   [Folyamatos kézbesítési](https://www.visualstudio.com/learn/what-is-continuous-delivery/) szoftveres megoldások éles és tesztelési környezetek segítséget nyújt a szervezetek gyorsan hibajavítás és üzleti követelmények folyamatosan változó válaszolni.
-   [Figyelési](https://www.visualstudio.com/learn/what-is-monitoring/) futó alkalmazások – beleértve az éles környezetekben az alkalmazás állapotának, valamint az ügyfél használati – segítségével a szervezetek alkotnak egy feltevése gyorsan érvényesítése és stratégiák disprove.  Részletes adatokat rögzíti, majd különböző naplózási formátumban tárolja.
-   [Szolgáltatott infrastruktúra (IaC) kód](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) eljárás, amely lehetővé teszi az automatizálás és létrehozásának érvényesítése és lebontás hálózatok és virtuális gépeket futtató rendszerek biztonságos, stabil alkalmazás továbbítása érdekében.
-   [Mikroszolgáltatások](https://www.visualstudio.com/learn/what-are-microservices/) architektúra elkülönítése üzleti alkalmazási esetek az kis újrafelhasználható szolgáltatásaiba szolgál.  Ez az architektúra lehetővé teszi a méretezhetőség és a hatékonyságot.

## <a name="next-steps"></a>További lépések
Az Operations Management Suite biztonsági és hitelesítési megoldás, lásd: a következő cikkeket:

- [Biztonsági és megfelelőségi](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Figyelés és válaszadás a biztonsági riasztásokat az Operations Management Suite biztonsági és hitelesítési megoldás](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts)
- [Az Operations Management Suite biztonsági és hitelesítési megoldás erőforrások figyelése](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources)
