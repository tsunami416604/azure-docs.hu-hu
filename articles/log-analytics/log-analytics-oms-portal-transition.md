---
title: Az Azure-bA áthelyezése OMS-portálon |} Microsoft Docs
description: Az OMS-portálon az összes funkciót az Azure portálra sunsetted folyamatban van. Ez a cikk részletesen Ez a változás.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: bwren
ms.openlocfilehash: 5719dc3719739fb561626e307ee295729752c1fa
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297753"
---
# <a name="oms-portal-moving-to-azure"></a>Az Azure-bA áthelyezése OMS-portálon
Köszönjük az OMS-portálon. Azt a támogatási javasolt, és továbbra is a figyelés és a felügyeleti szolgáltatások erősen fektetnek. Egy visszajelzésekben ismételten kérték egyes ügyfelek felügyeletét és kezelését a helyszíni, mind az Azure munkaterhelések egységes felhasználói élmény szükség. Valószínűleg már tudja az Azure-portálon az összes Azure-szolgáltatásokhoz központi és gazdag felügyeleti élmény képességeit irányítópultok a rögzítéshez megállapítás erőforrás intelligens keresése, és az erőforrás-kezelés címkézés erőforrások, például a kínál. Összevonása akkor történjen meg, és egyszerűsíthető a kiszolgálófigyelési és -kezelési munkafolyamat, azt hozzáadásának indítása az OMS portál képességeket be az Azure portálon. Örömmel jelenthetjük be a most már az Azure-portálon részét képezik az OMS-portálon a szolgáltatások többsége már dolgozunk. Valójában a új szolgáltatások, például a Traffic Manager némelyike csak az Azure portálon elérhető. Nincsenek csak néhány hézagok fennmaradó, a legtöbb impactful öt megoldások, amelyek továbbra is a kell helyezni az Azure-portál folyamat alatt. Ha a rendszer nem használja ezeket a funkciókat, lesz mindent korábban végzett, az az OMS-portálon az Azure-portálon, és több megvalósításához. Ha még nem tette meg, javasoljuk, indítsa el az Azure portál használatával még ma! 

A két portál által augusztus 2018 közötti fennmaradó hézagok le várhatóan. Az ügyfelek visszajelzései alapján, igazolnia kell tájékoztatnia az ütemterv sunsetting az OMS-portálon. Dolgozunk a Izgatottan várja, hogy helyezze át az Azure-portálon, és elvárják, könnyen átmenet. De tudjuk módosítások nehéz és zavart okozhatnak. Olyan kérdéseket, a visszajelzést vagy a problémákat a küldése LAUpgradeFeedback@microsoft.com. Ez a cikk többi a legfontosabb forgatókönyvek, az aktuális hézagok és ütemterv Ez a változás a kerül. 


## <a name="what-will-change"></a>Változások? 
A következő módosításokat az OMS-portálon elavulása jelenteni van folyamatban. Ezek a változások leírását az alábbi szakaszokban részletesebben.

- Az új riasztások kezelése teljes felül fogja írni a riasztás-kezelési megoldásban.
- Felhasználói hozzáférés-kezelés az Azure portálon az Azure szerepköralapú hozzáférés-vezérlés használatával történik.
- Az Application Insights-összekötő nem szükség, mert ugyanezt a funkcionalitást kereszt-munkaterület lekérdezések keresztül engedélyezhető.
- Az OMS mobilalkalmazás elavulttá válik. 
- Az NSG-megoldás a továbbfejlesztett funkciókért forgalom elemzési megoldások keresztül érhető el lecserélni.



## <a name="current-known-gaps"></a>Jelenlegi ismert hézagok 
Jelenleg néhány funkció hézagok meghatározása érdekében, hogy továbbra is használja az OMS-portálon igénylő. Ezek hézagok éppen be van zárva, és ez a dokumentum megfelelően frissülnek. Emellett tekintse át [Azure frissítések](https://azure.microsoft.com/updates/?product=log-analytics) bővítmények és a változtatások folyamatos hirdetmények.

- A következő megoldásokat még nem teljesen működőképes, az Azure portálon. Ön továbbra is használhatja ezeknek a megoldásoknak a klasszikus portálon mezők frissítése.

    - Windows elemzési megoldásokat ([frissítésre való alkalmasság](https://technet.microsoft.com/itpro/windows/deploy/upgrade-readiness-get-started), [Eszközállapot](https://docs.microsoft.com/windows/deployment/update/device-health-monitor), és [frissítési megfelelőségét](https://technet.microsoft.com/itpro/windows/manage/update-compliance-get-started))
    - [DNS-elemzés](log-analytics-dns.md) 
    - [Surface Hub](log-analytics-surface-hubs.md)

-  Az Azure Log Analytics-erőforrás eléréséhez a felhasználói hozzáférést kell rendelni a keresztül [Azure szerepköralapú hozzáférés-](#user-access-and-role-migration).
- Az OMS-portálon létrehozott frissítési ütemezés nem tükröződnek az ütemezett frissítés központi telepítéseket, vagy frissítse a feladatelőzményekben a frissítés kezelési irányítópult az Azure portálon. Ez a szünet is meg kell oldani. június 2018 végére várt.
- Egyéni naplókat előzetes funkció csak akkor engedélyezhető, OMS-portálon keresztül. 2018. június végén ez automatikusan engedélyezve lesz a munkahelyi csak szóközökből.
 
## <a name="what-should-i-do-now"></a>Mit tegyek most?  
Tekintse át [közös kérdések az Azure-portál a Log Analyticshez felhasználók OMS-portálon áttérést](../log-analytics/log-analytics-oms-portal-faq.md) való az Azure-portálon való áttéréssel kapcsolatos információkat. Ha a [hézagok a fent leírt](#current-known-gaps) nem alkalmazhatók a környezetben, akkor érdemes lehet az Azure portál használata az elsődleges élmény indítása. Küldjön visszajelzést, maillel kérdéseivel a LAUpgradeFeedback@microsoft.com.

## <a name="changes-to-alerts"></a>Riasztások módosításait 

### <a name="alert-extension"></a>Riasztási bővítmény  
Riasztások az éppen érvényesítését [kiterjeszthetőek az Azure-portálon](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Ha ez befejeződött, riasztások felügyeleti műveletek csak Azure-portálon érhetők el. Meglévő riasztásokat továbbra is megtalálható az OMS-portálon. Ha érhetők el riasztások programozott módon a napló Analytics riasztási REST API vagy a napló Analytics riasztási erőforrás sablon, szüksége lesz a műveletek az API-hívások, az Azure Resource Manager-sablonok és a PowerShell-parancsok helyett művelet csoportok használata.

### <a name="alert-management-solution"></a>Riasztási felügyeleti megoldás
Ahelyett, hogy a [megoldást kezelési riasztási](log-analytics-solution-alert-management.md), használhat [Azure figyelő riasztási felület egyesített](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) jelenítheti meg és kezelheti a riasztásokat. Az új felhasználói élmény összesíti a riasztások többek között a következőket napló riasztások Azure Naplóelemzés belül több forrásból. Tekintse meg azokat a terjesztéseket, a riasztások, intelligens csoportok keresztül kapcsolódó riasztások automatikus csoportosítása előnyeit, és megtekintheti a riasztásokat több előfizetésekhez gazdag szűrők alkalmazása közben. Ezek a szolgáltatások indítása 2018. június 4 Preview érhetők el. A riasztási felügyeleti megoldás nem lesz elérhető az Azure portálon. 

(Riasztás típusú rekordok) riasztási MDM-megoldás által gyűjtött adatok továbbra is Naplóelemzési mindaddig, amíg a megoldás telepítve van a munkaterületen. Augusztus 2018-től kezdődően adatfolyamként való küldése a riasztásokat a munkaterületek egyesített figyelmeztetéseit engedélyezve lesz, ezt a képességet cseréje. Néhány változás történt várható, és egy későbbi időpontban bejelentések.

## <a name="user-access-and-role-migration"></a>Felhasználói hozzáférés és a szerepkör áttelepítése
Az Azure portál kezelési részletesebb és hatékonyabb, mint a hozzáférés-kezelés az OMS-portálon, de néhány átalakítások legyen szükség. Lásd: [munkaterületek kezelése](log-analytics-manage-access.md#manage-accounts-and-users) a Naplóelemzési kezelési részleteit.

Kezdési. június 25, és folytatás. július keresztül, automatikus átalakítás a hozzáférés szabályozza, engedélyeket az OMS-portálon az Azure-portálhoz engedélyek indul el. Az átalakítást hajt végre, az OMS-portálon felhasználói felügyeleti szakasz fogja átirányítani a felhasználók a hozzáférés-vezérlést (IAM) az Azure-ban. 

Az átalakítás során a rendszer ellenőrizze minden felhasználót vagy biztonsági csoportot, amely jogosult az OMS-portálon, és határozza meg, ha azonos szintű vagy rendelkezik engedéllyel az Azure-ban. Engedélyek hiányoznak, ha azt a következő szerepkörök a megfelelő munkaterületekkel és a megoldások rendeli.

| OMS-portálon engedély | Az Azure szerepkör |
|:---|:---|
| ReadOnly | Log Analytics olvasó |
| Közreműködő | Log Analytics közreműködő |
| Rendszergazda | Tulajdonos |

Győződjön meg arról, hogy nincs túl sok engedélyeket állít be a felhasználók számára, hogy a rendszer nem automatikusan hozzárendeli ezeket az engedélyeket az erőforráscsoport szintjén. Ennek eredményeképpen munkaterület a rendszergazdák kell manuálisan hozzá nem rendeli maguk _tulajdonos_ vagy _közreműködő_ szerepkörök szintjén erőforrás csoport vagy az előfizetés a következő műveletek végrehajtásához.

- Hozzáadni vagy eltávolítani a megoldások
- Új egyéni nézeteinek definiálásához
- Riasztások kezelése 

Néhány esetben az Automatikus átalakítás nem vihetők át engedéllyel, és felkéri a rendszergazdát arra, hogy a manuálisan az engedélyeket.

## <a name="oms-mobile-app"></a>OMS mobilalkalmazás
Az OMS mobilalkalmazás lesz sunsetted az OMS-portállal együtt. Az OMS mobilalkalmazás helyett az informatikai infrastruktúrát, irányítópultok és a lekérdezések, információ eléréséhez érheti el az Azure-portálon közvetlenül a böngészőből a mobileszközről. Ahhoz, hogy a riasztásokat, konfigurálnia kell [Azure művelet csoportok](../monitoring-and-diagnostics/monitoring-action-groups.md) értesítéseket SMS vagy telefonhívás formájában

## <a name="application-insights-connector-and-solution"></a>Application Insights-összekötő és megoldás
[Application Insights-összekötő](log-analytics-app-insights-connector.md) Application Insights-adatok beolvasása a Naplóelemzési munkaterület lehetőséget kínál. Az adatok párhuzamos volt szükség között infrastruktúra-és alkalmazásadatok látható engedélyezéséhez.

Támogatása [cross-erőforrás lekérdezések](log-analytics-cross-workspace-search.md), már nincs a szükséges adatokat. Így a meglévő Application Insights-megoldást elavulttá válik. Július-től kezdődően nem lesz új Application Insights-erőforrások csatolása a Naplóelemzési munkaterület. Meglévő hivatkozások és irányítópultok tovább fog működni, amíg November 2018.


## <a name="azure-network-security-group-analytics"></a>Azure Network Security Group Analytics
A [Azure hálózati biztonsági csoport elemzési megoldások](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) fogja írni a a legutóbb indított [forgalom Analytics](../network-watcher/traffic-analytics.md) amelyhez ez a felhasználó és az alkalmazás tevékenységek láthatósága felhőalapú hálózatokhoz. Forgalom Analytics segítségével a szervezet hálózati tevékenységet, a biztonságos alkalmazások és adatok naplózása, munkaterhelés teljesítményének optimalizálásához, és megfelelő maradnak. 

Ez a megoldás NSG Flow logs elemzi, és a következő betekintést nyújt.

- A hálózatok közötti Azure és az Internet, nyilvános felhő régiók, Vnetek és alhálózatok közötti forgalmat.
- Alkalmazások és protokollok a hálózaton, szimatolók vagy dedikált folyamata gyűjtemény készülékek szükségessége nélkül.
- Felső talkers, chatty alkalmazások, virtuális gép beszélgetések a felhőben, a forgalom csatlakozási pontokhoz.
- Források és célok forgalom Vnetek, közötti kritikus fontosságú üzleti szolgáltatások és alkalmazások közötti kapcsolatok között.
- A biztonsági rosszindulatú forgalmat, nyissa meg az internethez, az alkalmazások és a virtuális gépek Internet-hozzáférés megkísérlése portok.
- Kapacitáskihasználás, amely segít a problémák kiküszöbölése kiépítés és alacsony kihasználtságot eredményez.

Továbbra is NSG logs szolgáltatáshoz, a meglévő mentett keresések, riasztások, irányítópultok továbbra is működni fog küldeni a diagnosztikai beállítások támaszkodnak. Az ügyfelek, akik már telepítették a megoldás is használható, további értesítésig. Kezdési. június 20 az NSG-megoldás a rendszer eltávolítja a piactérről és a Közösség keresztül elérhetővé tett egy [Azure gyors üzembe helyezés sablon](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="next-steps"></a>További lépések
- Lásd: [közös kérdések az Azure-portál a Log Analyticshez felhasználók OMS-portálon áttérést](log-analytics-oms-portal-faq.md) útmutatót az OMS-portálon áthelyezése az Azure-portálon.