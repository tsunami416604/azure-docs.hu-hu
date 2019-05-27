---
title: A Security Center nyugdíjazási szolgáltatásokat (július 2019) |} A Microsoft Docs
description: Ez a cikk ismerteti a szolgáltatások a Security Centerben, amelyek 2019. július 31-jén kivonjuk a forgalomból.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 069345f9c2d0fff0b580365153d8be13bb4ba204
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952139"
---
# <a name="retirement-of-security-center-features-july-2019"></a>A Security Center-szolgáltatásokat (július 2019) kivonása

Végeztünk néhány [fejlesztései](https://azure.microsoft.com/updates/?product=security-center) az Azure Security Center az elmúlt hat hónapban.
Ezekkel a továbbfejlesztett lehetőségekkel azt szeretne eltávolítani egyes redundáns szolgáltatások és a kapcsolódó API-k a Security Centerből a 2019. július 31-ig.  

A legtöbb leköszönő szolgáltatás lecserélhető az Azure Security Center vagy az Azure Log Analytics új funkciókat. Egyéb szolgáltatások segítségével valósítható meg [Azure Sentinel-(előzetes verzió)](https://azure.microsoft.com/services/azure-sentinel/).

A Security Center-szolgáltatások kivezetés előtt a következőket:

- [Események irányítópultja](#menu_events)
- [Keresés menüpont](#menu_search)
- [Klasszikus identitás és hozzáférés nézethivatkozás az identitás- és hozzáférés (előzetes verzió)](#menu_classicidentity)
- [Biztonsági események leképezése gombra a biztonsági riasztások térkép (előzetes verzió)](#menu_securityeventsmap)
- [Egyéni riasztási szabályok (előzetes verzió)](#menu_customalerts)
- [Gombra a veszélyforrások elleni védelem a biztonsági riasztások vizsgálata](#menu_investigate)
- [Biztonsági megoldások egy részhalmazát](#menu_solutions)
- [Biztonsági szabályzatok biztonsági beállításokkal szerkesztése](#menu_securityconfigurations)
- [Biztonsági és auditálási irányítópultja (az OMS-portált eredetileg használt) a Log Analytics-munkaterületek](#menu_securityomsdashboard)

Ez a cikk részletes információkat szolgáltat minden eltávolított funkciót és a lépések helyettesítő funkciók implementálását is igénybe vehet.

## Események irányítópultja<a name="menu_events"></a>

A Security Center a Microsoft Monitoring Agent használ a különböző biztonsági konfigurációkat és események gyűjtésére a gépek. A munkaterületeken tárolja ezeket az eseményeket. A [események irányítópultja](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) lehetővé teszi az adatok megtekintését és a egy belépési pont a Log Analytics szolgáltatásba.

Hogy mégy az események irányítópultja jelenik meg, amikor a munkaterület kiválasztása:

![Események irányítópultja][2]

### <a name="events-dashboard---the-new-experience"></a>Események irányítópultja – az új felhasználói felület

Javasolt azt, hogy az Azure Log Analytics natív képességeit jelentős események a nézeten a munkaterületeket.

Ha a Security Center létrehozott egyéni jelentős esemény, legyenek elérhetők. A Log Analyticsben, Ugrás **válassza ki a munkaterület** > **mentett keresések**. Az adatok nem elvész, vagy módosítani. Natív jelentős események ugyanazon a képernyőn a Log Analytics is elérhetőek.

![Mentett keresések munkaterület][3]

## Keresés menüpont<a name="menu_search"></a>

Az Azure Security Center jelenleg használja a naplók keresése az Azure Monitor és a biztonsági adatok elemzése. Ezen a képernyőn egy Log Analytics keresési ablak szolgál, és lehetővé teszi a felhasználók a kijelölt munkaterület keresési lekérdezések futtatására. További információkért lásd: [az Azure Security Center keresési](https://docs.microsoft.com/azure/security-center/security-center-search). A Keresés ablak mégy azt:

![Keresés lap][4]

### <a name="search-menu-entry---the-new-experience"></a>Keresés menüpont – az új felhasználói felület

Javasoljuk, hogy az Azure Log Analytics natív képességeket használja a-munkaterületek a keresési lekérdezések végrehajtásához. Nyissa meg az Azure Log Analyticshez, és válassza ki **naplók**.

![Log Analytics naplók lap][5]

## Klasszikus identitás és hozzáférés (előzetes verzió)<a name="menu_classicidentity"></a>

A klasszikus identitás és hozzáférés a Security Center jelenleg a tapasztalat az identitás és hozzáférés irányítópult a Log Analyticsben. Az irányítópult megtekintéséhez:

1. Válassza ki **klasszikus identitás és hozzáférés megtekintése**.

   ![Alkalmazásidentitás lap][6]

1. Nézet a **identitás és hozzáférés irányítópult**.

    ![Alkalmazásidentitás lap - munkaterület kiválasztása][7]

1. Válasszon ki egy munkaterületet, nyissa meg a **identitás és hozzáférés** irányítópult a Log Analyticsben identitás megtekinthessék és elérhessék az adatokat a munkaterületen.

   ![Alkalmazásidentitás lap - irányítópult][8]

Az előző lépésekben látható összes három képernyővel mégy azt. Az adatok továbbra is elérhető a Log Analytics biztonsági megoldás, és nem módosítható vagy eltávolítva.

### <a name="classic-identity--access-preview---the-new-experience"></a>Klasszikus identitás és hozzáférés (előzetes verzió) – az új felhasználói felület

A Log Analytics-irányítópult insights jelenik meg egyetlen munkaterületen. Azonban a natív a Security Center képességei az összes előfizetés és a hozzájuk társított összes munkaterületet biztosít. Egy könnyen hozzáférhet-nézet, amely lehetővé teszi használata fontos javaslat a biztonságos pontszám alapján összpontosíthat.

Összes funkcióját a **identitás és hozzáférés** kiválasztásával elérhető a Log Analytics irányítópult **identitás és hozzáférés (előzetes verzió)** biztonsági központban.

![Alkalmazásidentitás lap – klasszikus élmény kivonása][9]

## Biztonsági események leképezése<a name="menu_securityeventsmap"></a>

A Security Center a következőket biztosítja egy [biztonsági riasztások térkép](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) segítségével azonosíthatja a biztonsági fenyegetéseket. A **Ugrás a biztonsági események térkép** a térképen a gombra kattintva megnyithat egy irányítópultot, amely lehetővé teszi, hogy a nyers biztonsági események megtekintése a kiválasztott munkaterületen.

Hogy szeretne eltávolítani a **Ugrás a biztonsági események térkép** gombra, és a munkaterület irányítópulton.

![Biztonsági riasztások térkép - gomb][10]

Amikor kiválasztja a **Ugrás a biztonsági események térkép** gombra, a fenyegetésészlelési irányítópult megnyitásakor. Mi a fenyegetésfelderítési irányítópult mégy.  

![Fenyegetésészlelési irányítópult][11]

Ha úgy dönt, hogy a munkaterületet a megfelelő fenyegetésészlelési irányítópult megtekintéséhez, nyissa meg a biztonsági riasztások térkép (előzetes verzió) képernyőn a Log Analyticsben. Ez a képernyő mégy azt.

![Biztonsági riasztások térkép a Log Analyticsben][12]

A meglévő adatok továbbra is elérhető a Log Analytics biztonsági megoldás, és nem módosított sem eltávolítva.

### <a name="security-events-map---the-new-experience"></a>Biztonsági események térkép - az új felhasználói felület

Javasoljuk, hogy a riasztások térkép a Security Center beépített funkcióinak használata: **Biztonsági riasztások térkép (előzetes verzió)**. Ez a funkció egy optimalizált felhasználói élményt biztosít, és minden előfizetések és munkaterületek társított. Azt, magas szintű áttekintést nyújt a környezete, és nem egyetlen munkaterület összpontosítanak.

## Egyéni riasztási szabályok (előzetes verzió)<a name="menu_customalerts"></a>

Sajnáljuk, [kivonása az egyéni riasztások élmény](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) a 2019. június 30., mert az alapul szolgáló infrastruktúra kivonás alatt áll. Addig szerkesztheti a meglévő egyéni riasztási szabályok, de nem újakat adhat hozzá. Javasoljuk, hogy engedélyezze a [Azure Sentinel-](https://azure.microsoft.com/services/azure-sentinel/) automatikus áttelepítése a meglévő riasztásokat, és hozzon létre újakat. Másik lehetőségként a riasztásokat hozhat létre az Azure Monitor riasztások.

Tartani a meglévő riasztásokat, és Azure Sentinel-telepítheti át őket:

1. Nyissa meg az Azure-Sentinel, és válassza ki a munkaterületet, amely tárolja az egyéni riasztások.
1. Válassza ki **Analytics** automatikusan áttelepíteni a riasztások menüből.

![Egyéni riasztások][13]

Ha Ön nem érdeklik az Azure-Sentinel transitioning, javasoljuk, hogy a riasztások létrehozása az Azure Monitor riasztások. Útmutatásért lásd: [létrehozása, megtekintése, és a riasztások kezelése az Azure Monitor használatával](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) és [Naplóriasztások az Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

Egyéni riasztások használatból való kivonást egyaránt további információkért lásd: [egyéni riasztási szabályok az Azure Security Centerben (előzetes verzió)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Biztonsági riasztások vizsgálat<a name="menu_investigate"></a>

[A vizsgálati funkciójáról](https://docs.microsoft.com/azure/security-center/security-center-investigation) a Security Center segít a osztályozhatja a potenciális biztonsági incidensek. A funkció lehetővé teszi, hogy az incidens körének ismerete és nyomon követheti az alapvető ok. Azt szeretne eltávolítani ezt a szolgáltatást a Security Centerből, mert a rendszer helyébe-továbbfejlesztett felületet [Azure Sentinel-](https://azure.microsoft.com/services/azure-sentinel/).

![Biztonsági incidens][14]

Amikor kiválasztja a **vizsgálat** gombra egy **biztonsági incidens** képernyő, a vizsgálati irányítópult (előzetes verzió) nyissa meg a Log Analyticsben. A vizsgálati irányítópult mégy azt.  

A meglévő adatok továbbra is elérhető a Log Analytics biztonsági megoldás, és nem módosított sem eltávolítva.

![A Log Analytics vizsgálati irányítópult][15]

### <a name="investigation---the-new-experience"></a>Vizsgálat – az új felhasználói felület

Azt javasoljuk, való áttérés [Azure Sentinel-](https://azure.microsoft.com/services/azure-sentinel/) részletes vizsgálat biztosítása érdekében. Az Azure Sentinel egyszerre a szervezet biztonsági fenyegetések hunt való hatékony keresést és lekérdezéseket eszközöket biztosít.  

## Biztonsági megoldások részhalmazát<a name="menu_solutions"></a>

Engedélyezheti a Security Center [integrált Azure biztonsági megoldásait](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). A következő partnermegoldások a Security Centerből mégy azt. Ezek a megoldások vannak engedélyezve a [Azure Sentinel-](https://azure.microsoft.com/services/azure-sentinel/) együtt egy további adatforrások számát.

- [Következő generációs tűzfalak és a webes alkalmazás tűzfal megoldások](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [A Common Event Format (CEF) támogató biztonsági megoldások integrálása](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Kivonás, után azonosra felvenni vagy módosítani valamelyik már említettük, az előző listában, vagy a felhasználói felületen vagy az API-t a megoldás típusát.

Ha rendelkezik meglévő csatlakoztatott megoldások, javasoljuk, hogy Azure Sentinel-re.

![Biztonsági megoldások központok][16]

## Biztonsági szabályzatok biztonsági beállításokkal szerkesztése<a name="menu_securityconfigurations"></a>

Az Azure Security Center biztonsági konfigurációk figyeli egy alkalmazásával [több mint 150 ajánlott szabályok](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). az operációs rendszer korlátozására. Ezek a szabályok vonatkoznak a tűzfal, naplózás, jelszóházirendek és egyéb. Ha egy gép konfigurációját sebezhetőnek találja, a Security Center egy biztonsági javaslatot állít elő. A [szerkesztési biztonsági konfigurációs képernyőjén](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) szabhatja testre az alapértelmezett operációs rendszer biztonsági konfigurációs a Security Center lehetővé teszi.

Az előzetes verziójú funkció mégy azt. Ha a kivezetési dátum után szeretné visszaállítani a biztonsági konfigurációk az alapértelmezett értékeket, megteheti keresztül API-t vagy a Powershell használatával a [utasítások](https://aka.ms/ascresetsecurityconfigurations)

![Biztonsági beállítások szerkesztése][17]

### <a name="edit-security-configurations---the-new-experience"></a>Biztonsági konfigurációk – az új felület szerkesztése

Ahhoz, hogy a Security Center támogatásához tervezzük az [konfigurációs vendégügynök](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration). Az ilyen frissítés lehetővé teszi egy sokkal gazdagabb szolgáltatáskészlet, beleértve a több operációs rendszerek és az Azure a Vendég házirendek Vendég konfigurációk támogatását. Miután ezek a változások engedélyezve vannak, lehetővé teszi nagy mennyiségű konfigurációk vezérlőelemet, és automatikusan alkalmazza őket új erőforrásokat is rendelkezni fog.

## A Log Analytics-munkaterületek biztonsági és auditálási irányítópultja<a name="menu_securityomsdashboard"></a>

A biztonsági és auditálási irányítópultja az OMS-portált eredetileg használták. A Log Analytics, az irányítópult egy munkaterület áttekintést nyújt az jelentős biztonsági események és fenyegetések, egy fenyegetésfelderítési térkép és biztonsági eseményeket a munkaterületen menti az identitás és hozzáférés értékelését. Hogy még az irányítópult eltávolítása. Ahogy már javasoljuk az irányítópult felhasználói felület, azt javasoljuk, hogy, hogy, hogy az Azure Security Center való áttérés.

![Log Analytics-biztonság irányítópult][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Biztonsági és auditálási irányítópultja – az új felhasználói felület

Azt javasoljuk, hogy váltson át az Azure Security Center. Több előfizetésre kiterjedő biztosít az ugyanolyan biztonsági szolgáltatásainak áttekintése és a munkaterületekhez társítva, valamint egy gazdagabb szolgáltatáskészleteket.

Az eredeti Log Analytics-lekérdezések, amely feltölti a biztonsági és auditálási irányítópultján megtekintheti a [GitHub-adattár](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) a Security Center.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [az Azure Security Center](https://docs.microsoft.com/azure/security-center/).
- Tudjon meg többet [Azure Sentinel-](https://docs.microsoft.com/azure/sentinel).

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
