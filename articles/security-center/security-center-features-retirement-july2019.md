---
title: A Biztonsági Központ szolgáltatásainak kivonása (2019. július) | Microsoft dokumentumok
description: Ez a cikk a Security Center 2019.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 456f379e2b3c2ff411b196d45aef4663fddb0fe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245407"
---
# <a name="retirement-of-security-center-features-july-2019"></a>A Biztonsági központ szolgáltatásainak kivonása (2019. július)

> [!NOTE]
> Ez a dokumentum az Azure Security Centerből 2019.
>
>

A 2019 júliusát megelőző hat hónapban számos [fejlesztést](https://azure.microsoft.com/updates/?product=security-center) hajtottunk végre az Azure Security Centerben.
2019. július 31-én eltávolítottunk néhány redundáns funkciót és kapcsolódó API-t a Security Centerből.

A legtöbb ilyen kivisszavonult funkciók at lehet helyettesíteni más funkciók az Azure Security Center vagy az Azure Log Analytics. Más funkciók az [Azure Sentinel (előzetes verzió)](https://azure.microsoft.com/services/azure-sentinel/)használatával valósíthatók meg.

A Biztonsági központ kivisszavonult szolgáltatásai a következők:

- [Események irányítópultja](#menu_events)
- [Keresés menübejegyzésben](#menu_search)
- [Klasszikus identitás & Access hivatkozás megtekintése identitáson és hozzáférésen (előzetes verzió)](#menu_classicidentity)
- [Biztonsági események térkép gomb ja: Biztonsági riasztások térkép (előnézet)](#menu_securityeventsmap)
- [Egyéni riasztási szabályok (előzetes verzió)](#menu_customalerts)
- [A Veszélyforrások elleni védelem biztonsági riasztásaiban található Vizsgálat gomb](#menu_investigate)
- [A biztonsági megoldások egy részhalmaza](#menu_solutions)
- [Biztonsági házirendek biztonsági beállításainak szerkesztése](#menu_securityconfigurations)
- [Biztonsági és naplózási irányítópult (eredetileg az OMS-portálon használatos) a Log Analytics-munkaterületekhez](#menu_securityomsdashboard)

Ez a cikk részletes információkat tartalmaz az egyes kivisszavonult szolgáltatásokról és a cserefunkciók megvalósításának lépéseiről.

## <a name="events-dashboard"></a>Események irányítópultja<a name="menu_events"></a>

A Security Center a Microsoft Monitoring Agent segítségével különböző, biztonsággal kapcsolatos konfigurációkat és eseményeket gyűjt a gépekről. Ezeket az eseményeket a munkaterületeken tárolja. Az [események irányítópultja](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) lehetővé teszi az adatok megtekintését, és egy belépési pontot biztosít a Log Analytics számára.

Megszüntettük a munkaterület kiválasztásakor megjelenő események irányítópultját:

![Események irányítópultja][2]

### <a name="events-dashboard---the-new-experience"></a>Események irányítópult - az új élmény

Azt javasoljuk, hogy használja az Azure Log Analytics natív képességeit a munkaterületeken található jelentős események megtekintéséhez.

Ha egyéni említésre méltó eseményeket hozott létre a Security Centerben, azok elérhetők lesznek. A Log Analytics csoportban válassza **ki a** > **munkaterület mentett keresések lehetőséget.** Az adatok nem vesznek el és nem módosulnak. Natív említésre méltó események is elérhetők ugyanazon a képernyőn a Log Analytics.

![Munkaterületáltal mentett keresések][3]

## <a name="search-menu-entry"></a>Keresés menübejegyzésben<a name="menu_search"></a>

Az Azure Security Center jelenleg az Azure Monitor naplóinak keresését használja a biztonsági adatok lekéréséhez és elemzéséhez. Ez a képernyő a Log Analytics keresési oldalának ablakaként szolgál, és lehetővé teszi a felhasználók számára, hogy keresési lekérdezéseket futtassanak a kiválasztott munkaterületen. További információt az [Azure Security Center keresés című témakörben talál.](https://docs.microsoft.com/azure/security-center/security-center-search) Kivontuk ezt a keresési ablakot:

![Keresési lap][4]

### <a name="search-menu-entry---the-new-experience"></a>Keresés menübejegyzés - az új élmény

Azt javasoljuk, hogy használja az Azure Log Analytics natív képességek keresési lekérdezések a munkaterületeken. Nyissa meg az Azure Log Analytics elemet, és válassza **a Naplók**lehetőséget.

![A Log Analytics naplóinak lapja][5]

## <a name="classic-identity--access-preview"></a>Klasszikus identitás & Access (előzetes verzió)<a name="menu_classicidentity"></a>

A Security Center klasszikus identitás- & access-élménye jelenleg az identitás- és hozzáférési adatok irányítópultját jeleníti meg a Log Analytics szolgáltatásban. Az irányítópult megtekintése:

1. Válassza **a Klasszikus identitás & hozzáférés megtekintése lehetőséget.**

   ![Identitás lap][6]

1. Tekintse meg az **Identitás & Access irányítópultját.**

    ![Identitáslap – munkaterület kiválasztása][7]

1. Válasszon ki egy munkaterületet az **Identitás & Access** irányítópult megnyitásához a Log Analytics szolgáltatásban az identitás és a munkaterületi adatok eléréséhez.

   ![Identitás lap - irányítópult][8]

Az előző lépésekben látható mindhárom képernyőt megszüntettük. Az adatok továbbra is elérhetők maradnak a Log Analytics biztonsági megoldásában, és nem lettmódosítva vagy eltávolítva.

### <a name="classic-identity--access-preview---the-new-experience"></a>Classic Identity & Access (Preview) - az új élmény

A Log Analytics irányítópult egyetlen munkaterületen jelenít meg elemzési adatokat. A natív Security Center-képességek azonban betekintést nyújtanak az összes előfizetésbe és a hozzájuk társított összes munkaterületbe. Egy könnyen használható nézethez férhet hozzá, amely lehetővé teszi, hogy a fontos dolgokra összpontosítson a biztonságos pontszámuk szerint rangsorolt ajánlásokkal.

A Log Analytics **identitás-& access** irányítópultjának összes szolgáltatása elérhető a Security Center **identitás-hozzáférés& hozzáférési (előzetes)** kiválasztásával.

![Identity oldal - klasszikus tapasztalat nyugdíj][9]

## <a name="security-events-map"></a>Biztonsági események térképe<a name="menu_securityeventsmap"></a>

A Security Center [biztonsági riasztási térképet](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) biztosít a biztonsági fenyegetések azonosításához. A **térkép Ugrás a biztonsági eseményekhez** gomb megnyit egy irányítópultot, amely lehetővé teszi a nyers biztonsági események megtekintését a kiválasztott munkaterületen.

Eltávolítottuk az Ugrás a **biztonsági eseményekre térképgombot** és a munkaterületenkénti irányítópultot.

![Biztonsági riasztások térképe - gomb][10]

Ha kiválasztja az Ugrás a **biztonsági eseményekre térkép** gombot, megnyitotta a (már visszavonult) fenyegetésfelderítési irányítópultot.

![Fenyegetésfelderítési irányítópult][11]

Ha úgy dönt, hogy egy munkaterületet a fenyegetésfelderítési irányítópult megtekintéséhez, megnyitotta a (már visszavonult) biztonsági riasztások térkép (előzetes) képernyő log analytics.

![Biztonsági riasztások térképe a Log Analytics szolgáltatásban][12]

A meglévő adatok továbbra is elérhetők maradnak a Log Analytics biztonsági megoldásban, és nem módosították vagy eltávolították.

### <a name="security-events-map---the-new-experience"></a>Biztonsági események térképe - az új élmény

Javasoljuk, hogy használja a Security Center: Biztonsági **riasztások térkép (Előzetes verzió)** beépített riasztási leképezési funkcióját. Ez a funkció optimalizált élményt nyújt, és minden előfizetésben és a kapcsolódó munkaterületeken működik. Magas szintű képet ad a környezetben, és nem egyetlen munkaterületre összpontosít.

## <a name="custom-alert-rules-preview"></a>Egyéni riasztási szabályok (előzetes verzió)<a name="menu_customalerts"></a>

2019. június 30-án [megszüntettük az egyéni riasztások élményét,](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) mert az alapul szolgáló infrastruktúrát megszüntettük. A kivonási dátum után az egyéni biztonsági riasztások már nem jönnek létre.
Azt javasoljuk, hogy engedélyezze [az Azure Sentinel,](https://azure.microsoft.com/services/azure-sentinel/) és újra hozza létre az egyéni riasztások ott. Azt is megteheti, hogy az Azure Monitor naplóriasztásaival hozza létre a riasztásokat.

Egyéni riasztások létrehozása az Azure Sentinel használatával:

1. [Nyissa meg](https://portal.azure.com/#create/Microsoft.ASI/preview) az Azure Sentinelt, és válassza ki azt a munkaterületet, ahol az egyéni riasztások tárolódnak
1. Válassza az **Analytics** elemet a menüből
1. Kövesse az alábbi [oktatóanyag](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) utasításait az egyéni riasztások létrehozásáról az Azure Sentinelben

Ha nem szeretné használni az Azure Sentinel, létrehozhatja a riasztásokat az Azure Monitor naplóriasztásaival. További információt a [Naplóriasztások létrehozása, megtekintése és kezelése az Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) és a Log riasztások használatával az Azure Monitorban című [témakörben talál.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)

![Egyéni riasztások][13]

Az egyéni riasztások megszüntetésével kapcsolatos további információkért lásd: [Egyéni riasztási szabályok az Azure Security Centerben (előzetes verzió).](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)

## <a name="security-alerts-investigation"></a>Biztonsági riasztások vizsgálata<a name="menu_investigate"></a>

A Biztonsági központ [Vizsgálati funkciója](https://docs.microsoft.com/azure/security-center/security-center-investigation) segít egy esetleges biztonsági incidens osztályozásában. A funkció lehetővé teszi az incidens hatókörének megértését és a kiváltó okának nyomon követését. Eltávolítottuk ezt a funkciót a Security Centerből, mert az [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)ben javított felhasználói élményváltotta fel.

![Biztonsági incidens][14]

Ha egy **biztonsági incidens** képernyőről **kiválasztja** a Vizsgálat gombot, megnyithatja a Vizsgálati irányítópultot (előzetes verziót) a Log Analytics alkalmazásban. Megszüntettük a nyomozási irányítópultot.

A meglévő adatok továbbra is elérhetők maradnak a Log Analytics biztonsági megoldásban, és nem módosították vagy eltávolították.

![Vizsgálati irányítópult a Log Analytics szolgáltatásban][15]

### <a name="investigation---the-new-experience"></a>Vizsgálat - az új tapasztalat

Javasoljuk, hogy váltson át az [Azure Sentinelre](https://azure.microsoft.com/services/azure-sentinel/) a gazdag vizsgálati élmény érdekében. Az Azure Sentinel hatékony keresési és lekérdezési eszközöket biztosít a szervezet adatforrásaiban a biztonsági fenyegetések keresésére.

## <a name="subset-of-security-solutions"></a>A biztonsági megoldások részhalmaza<a name="menu_solutions"></a>

A Security Center integrált biztonsági megoldásokat tud engedélyezni [az Azure-ban.](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) A következő partnermegoldásokat vontuk ki a Security Centerből. Ezek a megoldások engedélyezve vannak az [Azure Sentinelben](https://azure.microsoft.com/services/azure-sentinel/) számos további adatforrással együtt.

- [Új generációs tűzfal- és webalkalmazás-tűzfalmegoldások](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [A közös eseményformátumot (CEF) támogató biztonsági megoldások integrálása](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

A megszüntetés után az előző listában említett megoldástípusok egyikét sem adhathozzá és nem módosíthatja, sem a felhasználói felületről, sem az API-ból. Az Azure Security Center a továbbiakban nem fedez fel új példányokat ezekből a partnermegoldásokból.

Ha már rendelkezik csatlakoztatott megoldásokkal, javasoljuk, hogy költözzön az Azure Sentinelbe.

![Biztonsági központok megoldásai][16]

## <a name="edit-security-configurations-for-security-policies"></a>Biztonsági házirendek biztonsági beállításainak szerkesztése<a name="menu_securityconfigurations"></a>

Az Azure Security Center a biztonsági konfigurációk monitorozásához egy [több mint 150 ajánlott szabályt](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) tartalmazó készletet alkalmaz az operációs rendszer védelmének fokozására. Ezek a szabályok tűzfalakra, naplózásra, jelszóházirendre és egyebekre vonatkoznak. Ha egy gép konfigurációját sebezhetőnek találja, a Security Center egy biztonsági javaslatot állít elő. A [Biztonsági konfiguráció szerkesztése képernyő](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) lehetővé teszi az ügyfelek számára az operációs rendszer alapértelmezett biztonsági konfigurációjának testreszabását a Security Center ben.

Megszüntettük ezt az előnézeti funkciót. Ha vissza szeretné állítani a biztonsági konfigurációkat az alapértelmezett értékekre a kivonási dátum után, ezt az API-n vagy a Powershellen keresztül tegye meg az [alábbi utasításokat](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20samples/Reset%20security%20configurations%20customization)használva.

![Biztonsági konfigurációk szerkesztése][17]

### <a name="edit-security-configurations---the-new-experience"></a>Biztonsági konfigurációk szerkesztése - az új élmény

Szándékunkban áll engedélyezni, hogy a Security Center támogassa a [Vendég konfigurációs ügynököt.](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) Egy ilyen frissítés lehetővé teszi egy sokkal gazdagabb szolgáltatáskészlet, beleértve a több operációs rendszer támogatását és az Azure vendégkonfigurációkhoz való beépítését. Miután ezek a módosítások engedélyezve vannak, a konfigurációkat is szabályozhatja, és automatikusan alkalmazhatja az új erőforrásokra.

## <a name="security-and-audit-dashboard-for-log-analytics-workspaces"></a>Biztonsági és naplózási irányítópult a Log Analytics-munkaterületekhez<a name="menu_securityomsdashboard"></a>

A biztonsági és naplózási irányítópultot eredetileg az OMS-portálon használták. A Log Analytics irányítópultján a munkaterületenként áttekintést nyújt a jelentős biztonsági eseményekről és fenyegetésekről, egy fenyegetésfelderítési térképet, valamint a munkaterületre mentett biztonsági események identitás- és hozzáférés-felmérését. Eltávolítottuk a műszerfalat. Ahogy azt már ajánlotta az irányítópult felhasználói felületén, azt javasoljuk, hogy az Azure Security Center.

![A Log Analytics biztonsági irányítópultja][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Biztonsági és ellenőrzési irányítópult - az új élmény

Javasoljuk, hogy váltson az Azure Security Centerre. Ugyanazt a biztonsági áttekintést nyújt több előfizetés és a hozzájuk társított munkaterületek között, valamint egy gazdagabb szolgáltatáskészletet.

Beszerezheti az eredeti Log Analytics-lekérdezéseket, amelyek feltöltik a biztonsági és naplózási irányítópultot a [GitHub biztonsági központ tárházában.](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards)

## <a name="next-steps"></a>További lépések

- További információ az [Azure Security Centerről.](https://docs.microsoft.com/azure/security-center/)
- További információ az [Azure Sentinelről.](https://docs.microsoft.com/azure/sentinel)

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
