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
ms.openlocfilehash: 20dfbd2c5c2c38abddf332dc6a20ddb271c95ace
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="azure-operational-security-overview"></a>Az Azure operational biztonsági áttekintése
Az Azure Operational biztonsági hivatkozik a szolgáltatások, a vezérlők és a felhasználók számára elérhető szolgáltatások védelmére az adatok, alkalmazások és egyéb eszközök a Microsoft Azure-ban. [Az Azure Operational biztonsági](https://docs.microsoft.com/azure/security/azure-operational-security) egy keretrendszer, amely magában foglalja a Tudásbázis a különböző képességeket, amelyeket a Microsoft, beleértve a Microsoft biztonsági fejlesztési életciklus (SDL), a Microsoft Security Response egyedi keresztül szerzett Center programot, és részletes tájékoztatást nyújthatnak a számítógépes biztonsági fenyegetésekről alkotott képet.

Ez a cikk Azure működési biztonsági áttekintése összpontosít a következő területeken:

- Az Azure Operations Management Suite szolgáltatásban
-   Azure Security Center
-   Azure Monitor
-   Az Azure hálózati figyelőt
-   Az Azure Storage analytics
-   Azure Active directory

## <a name="azure-operations-management-suite"></a>Az Azure Operations Management Suite szolgáltatásban
IT-üzemeltetők adatközpont infrastruktúrájában, alkalmazások és adatok, beleértve a stabilitás és az ilyen rendszerek biztonsági felügyeletéért felelős. Biztonság és ellenőrzött való növelésével gyakran összetett informatikai környezetben különböző azonban van szükség a szervezetek cobble egyszerre több biztonsági és felügyeleti rendszerek adatait.

[A Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) a Microsoft felhőalapú informatikai felügyeleti megoldás, amely segít a kezelése és védelme a helyszíni és felhőalapú infrastruktúra.

OMS egy olyan felhőalapú informatikai felügyeleti megoldás a sok ajánlatokat, informatikai automatizálási, biztonsági és megfelelőségi, Naplóelemzési, és a biztonsági mentés és helyreállítás. Egy tökéletes támogatás kezelésére és védelmére az informatikai infrastruktúrák ilyen – a helyszíni és a felhőben.

Az OMS legfontosabb funkcióit az Azure-ban futó szolgáltatások biztosítják. Mindegyik szolgáltatás egy adott felügyeleti funkciót biztosít, és a szolgáltatások kombinálásával különféle felügyeleti forgatókönyvek valósíthatók meg. Mert az tartalmazza:

-   Log Analytics
-   Automation
-   Backup
-   Site Recovery

### <a name="log-analytics"></a>Log Analytics
A [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) figyelési szolgáltatásokat biztosít az OMS számára a felügyelt erőforrások adatainak egy központi tárházba gyűjtésével. Ezek az adatok lehetnek események, teljesítményadatok vagy az API segítségével biztosított egyéni adatok. Az összegyűjtésüket követően az adatok használhatók riasztáshoz, elemzéshez vagy exportáláshoz. Ezzel a módszerrel konszolidálhatja a különféle forrásokból származó adatokat, így az Azure-szolgáltatásokból származó adatok egyesíthetők a meglévő helyszíni környezet adataival. Továbbá egyértelműen elkülöníti az adatok gyűjtését az adatokon végzett műveletektől, így az összes művelet végrehajtható a különféle adatokon.

### <a name="automation"></a>Automation
Microsoft [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) módot biztosít a felhasználók számára a felhőalapú és nagyvállalati környezetben általában végrehajtott manuális, hosszan futó, hibákhoz vezethet, és gyakran ismétlődő feladatok automatizálásához. Ezzel idő takarítható meg, továbbá nő a rendszeres adminisztratív feladatok megbízhatósága, valamint még ütemezhetők is a feladatok, hogy adott időközönként automatikusan végrehajtsa őket a rendszer. A folyamatokat automatizálhatja forgatókönyvek segítségével, vagy automatizálhat konfigurációkezelést a Célállapot-konfigurációval (DSC).

### <a name="backup"></a>Backup
[Azure biztonsági mentés](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) biztonsági mentése (vagy védelme) is használhatja az Azure-alapú szolgáltatás, és állítsa vissza az adatokat a Microsoft felhőben. Az Azure Backup megbízható, biztonságos és költséghatékony felhőalapú megoldással váltja fel a meglévő helyszíni vagy külső helyszínen lévő biztonsági mentési megoldást. Az Azure Backup több összetevőjét letöltheti és telepítheti a megfelelő számítógépre, kiszolgálóra vagy a felhőbe. A telepítendő összetevő vagy ügynök attól függ, hogy mit szeretne megvédeni. Minden Azure Backup-összetevővel (függetlenül attól, hogy helyszíni vagy a felhőben tárolt adatokat kíván védeni) készíthetők biztonsági másolatok az Azure Recovery Services-tárolójába. Tekintse meg a [Azure biztonsági mentés összetevők tábla](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use).

### <a name="site-recovery"></a>A Site recovery
Az [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) a helyszíni virtuális és fizikai gépeknek az Azure-ba vagy egy másodlagos helyre való replikálásának vezénylésével biztosítja az üzletmenet-folytonosságot. Ha az elsődleges hely nem érhető el, a rendszer feladatátvételt végez a másodlagos helyre, így a felhasználók tovább dolgozhatnak, majd amikor a rendszer újra működőképessé válik, az elsődleges hely visszaveszi a feladatokat. Használja az Azure Security Center több intelligens és hatékony fenyegetések észlelése végrehajtásához.

## <a name="azure-active-directory"></a>Azure Active Directory
[Az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) van a Microsoft átfogó szolgáltatott identitási (IDaaS) szolgáltatás megoldás, amely:

-   Lehetővé teszi a felhő alapú szolgáltatásként IAM
-   Központi hozzáférési kezelhető, egyszeri bejelentkezést (SSO) és a jelentéskészítési
-   Támogatja az integrált kezelés [alkalmazások ezer](https://azure.microsoft.com/marketplace/active-directory/) az alkalmazás katalógusában, például a Salesforce, Google Apps, mezőben, Concur és egyéb.

Az Azure AD is tartalmazó teljes csomag [identitáskezelési funkciói](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports) beleértve [a multi-factor authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), [eszközregisztráció]( https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-overview), [ önkiszolgáló jelszókezelés](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/), [önkiszolgáló csoportkezelési](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), [fiókkezelés privilegizált](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure), [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview), [alkalmazás-használat figyelését](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health), [részletes naplózás](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs), és [biztonsági figyelést és riasztást](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts).

Az Azure Active Directoryval minden alkalmazás közzététele a partnerek és ügyfelek (üzleti vagy fogyasztó) ugyanazzal az identitással rendelkezik, és hozzáférés-kezelési képességeket. Ez lehetővé teszi, hogy jelentősen csökkenti a működési költségek.

## <a name="azure-security-center"></a>Azure Security Center
[Az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started) az Azure-erőforrások biztonsági felügyeletének átláthatóbbá és szabályozhatóbbá tételével megkönnyíti a fenyegetések megelőzését, észlelését és elhárítását. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

[A Security Center](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) segítségével megakadályozhatja a virtuális gépek adatainak Azure lássák a virtuális gép biztonsági beállításainak megadása és a fenyegetések figyelése. A Security Center a következőket tudja megfigyelés alatt tartani a virtuális gépeken:

-   Az operációs rendszer (OS) biztonsági beállításai ajánlott konfigurációs szabályokkal
-   Rendszerbiztonság és a hiányzó kritikus frissítések
-   Az Endpoint Protection javaslatai
-   Lemeztitkosítás ellenőrzése
-   Hálózati támadás

Az Azure Security Center által használt [szerepköralapú hozzáférés-vezérlést (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), amely biztosítja [beépített szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) , hogy a felhasználók, csoportok és az Azure rendelhető.

A Security Center értékeli a konfigurációs az erőforrások biztonsági problémák és biztonsági rések azonosítása. A biztonsági központban csak akkor jelenik meg egy erőforrás tulajdonos, közreműködő vagy olvasó szerepkört az előfizetés vagy az erőforráscsoportot, amelyhez tartozik egy erőforrás hozzárendelése esetén kapcsolatos adatokat.

>[!Note]
>Lásd: [engedélyek az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-permissions) tudhat meg többet a szerepkörök és a Security Center engedélyezett műveletek.

A Security Center használja a Microsoft Monitoring Agent – Ez az Operations Management Suite és Naplóelemzés szolgáltatás által használt ugyanannak az ügynöknek. Ez az ügynök gyűjtött adatokat tárolja egy meglévő Log Analyticshez [munkaterület](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) társított Azure-előfizetése vagy egy új munkaterületek, figyelembe véve a földrajzi hely, a virtuális gép.

## <a name="azure-monitor"></a>Azure Monitor
A cloud app teljesítményével kapcsolatos problémákat hatással lehet az üzleti. A több összekapcsolt összetevőkkel és gyakori kiadásokban degradations bármikor fordulhat elő. És ha az alkalmazást, a felhasználók általában felderítése a tesztjei során nem talált problémákat. Meg kell ezekről a problémákról értesülnek azonnal, eszközöket, és diagnosztizálására és a problémák elhárításához.

[Az Azure figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) alapvető eszköz Azure-on futó szolgáltatások figyelése. Biztosít egy szolgáltatás és a környezet átviteli infrastruktúra szintű adatait. Kezeli az alkalmazások az összes Azure-ban, ha eldöntötte, hogy a méretezési felfelé vagy lefelé erőforrásokat, majd Azure a figyelő lehetővé teszi az szeretne használni.

Figyelési adatok segítségével emellett az alkalmazással kapcsolatos átfogó megismerésében. Ezt az információt nyújt segítséget az alkalmazások teljesítményének vagy karbantartási követelmények, vagy, amelyek egyébként kézi beavatkozás műveletek automatizálására. Ezek a következők:

-   Azure tevékenységnapló
-   Az Azure diagnosztikai naplók
-   Mérőszámok
-   Azure Diagnostics

### <a name="azure-activity-log"></a>Azure tevékenységnapló
A napló, amely a erőforrást az előfizetésében végrehajtott műveletek betekintést nyújt. A [tevékenységnapló](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) korábban hívták "Naplófájlok" vagy "Működési Logs", mivel azt jelenti, hogy az előfizetések vezérlő-vezérlősík eseményeket.

### <a name="azure-diagnostic-logs"></a>Az Azure diagnosztikai naplók
[Az Azure diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) erőforrás által kibocsátott, és adja meg a műveletet az erőforrás gazdag, gyakori adatait. Ezek a naplók tartalmának erőforrástípusok szerint változik.

Például Windows rendszer-eseménynaplói diagnosztikai napló a virtuális gépek és a blob, a tábla egy kategóriát, és várólista naplók diagnosztikai naplók kategóriáinak storage-fiókok.

Diagnosztikai naplók eltérnek a [tevékenységnapló (korábbi nevén napló tartalmát, vagy a műveleti napló)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). A műveletnapló erőforrást az előfizetésében a végrehajtott műveletek betekintést nyújt. A diagnosztikai naplókban az erőforrás által végrehajtott műveletekkel kapcsolatos információk találhatók meg.

### <a name="metrics"></a>Mérőszámok
Azure figyelő címinfrastruktúra megjelenítési lehetőségeinek a teljesítmény- és a munkaterhelések Azure állapot telemetriai adatok felhasználását teszi lehetővé. A legfontosabb Azure telemetriai adatok típus (más néven teljesítményszámlálók) vagy az Azure erőforrások által kibocsátott metrikákat. Az Azure figyelő többféleképpen is konfigurálhatja, és felhasználhatják ezeket [metrikák](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) figyelés és hibaelhárítás céljából.

### <a name="azure-diagnostics"></a>Azure Diagnostics
A funkció, amely lehetővé teszi a telepített alkalmazás diagnosztikai adatok gyűjtésére Azure belül. Használja a diagnosztika bővítményét a különböző forrásokból. A rendszer jelenleg támogatott [Azure Cloud Service webes és feldolgozói szerepkörök](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [Azure virtuális gépek](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) Microsoft Windows rendszerű és [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).


## <a name="network-watcher"></a>Network Watcher
Az ügyfelek egy Azure-végpontok közötti hálózati koordinálása, és különböző egyes hálózati erőforrások, például virtuális hálózaton, ExpressRoute, Alkalmazásátjáró, terheléselosztók, és több összeállítása hozhat létre. Figyelési érhető el minden olyan a hálózati erőforrásokhoz.

A végpontok közötti hálózati összetett konfigurációk és erőforrások létrehozása összetett forgatókönyvek esetében, forgatókönyv-alapú hálózati figyelőt figyeléshez igénylő közötti interakció rendelkezhet.

[Hálózati figyelő](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) fog egyszerűbbé teszi a figyelése és diagnosztizálása az Azure-hálózat. Diagnosztikai és a képi megjelenítés eszközök is elérhetők a hálózati figyelőt engedélyezése, hogy a távoli csomag rögzíti az Azure virtuális gép, jobb betekintést a hálózati forgalmat, folyamat-naplók segítségével és diagnosztizálhatják az RSS VPN Gateway és kapcsolatok.

Hálózati figyelőt jelenleg a következő képességekkel rendelkezik:

- [Topológia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) -a különböző csatlakozás és egy erőforráscsoportban található hálózati erőforrások egymáshoz rendelését hálózati szintű nézetét jeleníti meg.
-   [Változó csomagrögzítéssel](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) -csomagadatok mindkét virtuális gép rögzíti. Speciális szűrési beállítások, például fel idő beállítása és korlátozások méretezés finomhangolható vezérlők meg a indít. A csomagadatok a blob-tárolóban, vagy a helyi lemezen .cap formátumban tárolható.
-   [IP-adatfolyamok ellenőrizze](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) -ellenőrzést, ha egy csomag engedélyezett vagy megtagadott folyamata adatokat 5 rekordos csomag paraméterek (cél IP-címe, forrás IP-címe, Célport, Forrásport és protokoll) alapján. Ha a csomag megtagadta a biztonsági csoport, a szabály és a csoportot, amely a csomag megtagadva ad vissza.
-   [Következő Ugrás](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) -határozza meg a következő ugrás a csomagok továbbítása az Azure hálózati háló, így lehetővé teszi bármely diagnosztizálása nincs megfelelően konfigurálva a felhasználó által definiált útvonalak.
-   [Biztonsági csoport megtekintése](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) -a hatékony és alkalmazott biztonsági szabályokat, amelyek érvényesek a virtuális gép lekérdezi.
-   [NSG Flow naplózási](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) -folyamat naplókat a hálózati biztonsági csoportok lehetővé teszik a engedélyezett vagy megtagadott a csoport biztonsági szabályai forgalmi naplók rögzítése. A folyamat egy 5 rekordos információkat – a forrás IP-cím, a cél IP-cím, a forrásport, a célport és a protokoll határozzák meg.
-   [Virtuális hálózati átjáró és a kapcsolat hibaelhárítási](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) -lehetővé teszi a virtuális hálózati átjárók és kapcsolatok hibáinak elhárítása.
-   [Előfizetési korlátozásait a hálózati](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) -lehetővé teszi a hálózati erőforrás-használati korlátozások megtekintéséhez.
-   [Diagnosztikai naplófájl konfigurálása](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) – engedélyezheti vagy tilthatja le a diagnosztikai naplókat a hálózati erőforrások az erőforráscsoportban egytáblás biztosít.

Ha többet szeretne konfigurálásáról lásd: a hálózati figyelő, [konfigurálja a hálózati figyelőt](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="developer-operations-devops"></a>Fejlesztői műveletek (DevOps)
DevOps alkalmazásfejlesztés, mielőtt csoportok feladata üzleti követelmények szoftver adatgyűjtési és kódot volt. Majd egy külön csapat QA teszteli a program elkülönített fejlesztői környezetet, ha követelményeknek való megfelelés, és feloldja a telepítendő műveletek kódját. A telepítési csoport például a hálózat és az adatbázis siloed csoportokba további töredezett. Minden alkalommal, amikor egy program "fordul elő a fali keresztül" egy független csapat azt szűk keresztmetszetek hozzáadja.

[DevOps](https://www.visualstudio.com/learn/what-is-devops/) lehetővé teszi a nagyobb biztonságot nyújt, magasabb színvonalú megoldásokat gyorsabb és olcsóbb csoportokkal. Az ügyfelek a dinamikus és megbízható élmény várhatnak, amikor a szoftverek és -szolgáltatások fel.  Szoftverfrissítések, gyorsan kell csapatok többször mérjük a hatását a frissítéseket, és, gyorsan képesek reagálni az új fejlesztési ismétlési problémák, vagy adjon meg több érték.  Felhő platformokon, például a Microsoft Azure eltávolította a hagyományos szűk keresztmetszetei és infrastruktúra commoditize segített. Szoftver reigns minden üzleti a legfontosabb különbséget és tényező az üzleti tevékenységét. Nincs szervezet, fejlesztői vagy informatikai dolgozó is, vagy kerülje a DevOps mozgása.

Érett DevOps szakemberek az alábbi eljárásokkal számos fogad el. Ezeket a gyakorlatokat [tartalmaz, amely személyek](https://www.visualstudio.com/learn/what-is-devops-culture/) az űrlap stratégiák az üzleti forgatókönyvek alapján.  Tooling segítségével automatizálhatja a különböző eljárásokat:

-   [Agilis tervezési és a projekt kezelése](https://www.visualstudio.com/learn/what-is-agile/) technikák segítségével tervezze meg és sprints való munka elkülönítése, team kapacitás kezelése, valamint gyorsan igazítja a üzleti igények változásaira csoportok segítségével.
-   [Verziókövetés, általában a Git](https://www.visualstudio.com/learn/what-is-git/), lehetővé teszi, hogy a csoportok bárhol a világon forrás megosztásához, és integrálása a fejlesztési szoftvereszközök a kiadási folyamat automatizálása.
-   [Folyamatos integrációt](https://www.visualstudio.com/learn/what-is-continuous-integration/) meghajtók, a folyamatban lévő egyesítése és a kódot, ami hibák keresése korai vizsgálatára.  Egyéb előnyök rövidebb idő alatt az egyesítési problémákat és a fejlesztési csapat gyors visszajelzése elleni kihasználatlan tartalmazza.
-   [Folyamatos kézbesítési](https://www.visualstudio.com/learn/what-is-continuous-delivery/) szoftveres megoldások éles és tesztelési környezetek súgó a szervezetek gyorsan javítsa ki a hibákat, és válaszolhat azokra folyamatosan változó üzleti követelmények.
-   [Figyelési](https://www.visualstudio.com/learn/what-is-monitoring/) futó alkalmazások, beleértve az éles környezetben az alkalmazás állapotának, csakúgy, mint az ügyfél használati súgó szervezetek űrlap egy alapul, és gyorsan ellenőrzése vagy stratégiák disprove.  Részletes adatokat rögzíti, majd különböző naplózási formátumban tárolja.
-   [Szolgáltatott infrastruktúra (IaC) kód](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) eljárás, amely lehetővé teszi az automatizálás és létrehozásának érvényesítése és lebontás hálózatok és virtuális gépeket futtató rendszerek biztonságos, stabil alkalmazás továbbítása érdekében.
-   [Mikroszolgáltatások](https://www.visualstudio.com/learn/what-are-microservices/) architektúra elkülönítése üzleti alkalmazási esetek az kis újrafelhasználható szolgáltatásaiba elkészítéséhez használja.  Ez az architektúra lehetővé teszi a méretezhetőség és a hatékonyságot.

## <a name="next-steps"></a>További lépések
Többet OMS biztonsági, naplózási megoldást, olvassa el a következő cikkeket:

- [Az Operations Management Suite |} Biztonsági és megfelelőségi](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Figyelés és az Operations Management Suite biztonsági és naplózási megoldás a biztonsági riasztásokra való Reagálásról](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts).
- [Erőforrások figyelése az Operations Management Suite biztonsági és naplózási megoldás](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources).
