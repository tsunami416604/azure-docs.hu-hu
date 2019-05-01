---
title: A Security Center nyugdíjazási szolgáltatásokat (július 2019) |} A Microsoft Docs
description: Ez a cikk részletesen szolgáltatások a Security Centerben, amelyek a 2019. július 31-én megszűnik.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 392782310d8bc3b38a3dd1349cb1760ca287acd1
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875589"
---
# <a name="retirement-of-security-center-features-july-2019"></a>A Security Center-szolgáltatásokat (július 2019) kivonása

Hajtottunk végre több [fejlesztései](https://azure.microsoft.com/updates/?product=security-center) az Azure Security Center az elmúlt 6 hónapban.  
A továbbfejlesztett funkciók megszüntetjük 2019. július 31-ig. a Security centerből egy redundáns funkciókat, valamint a kapcsolódó API-k számát.  

A megszűnő funkciók a legtöbb lecserélhető az Azure Security Center és a Log Analytics; új funkciói bizonyos funkciók segítségével valósítható meg, és [Azure Sentinel-(előzetes verzió)](https://azure.microsoft.com/services/azure-sentinel/), nemrég megjelent.

A Security centerből kivezetjük funkciók listáját tartalmazza:

- [Események irányítópultja](#menu_events)
- [Keresés menüpont](#menu_search)
- [Klasszikus identitás és hozzáférés nézethivatkozás az identitás- és hozzáférés (előzetes verzió)](#menu_classicidentity)
- [Biztonsági események leképezése gombra a biztonsági riasztások térkép (előzetes verzió)](#menu_securityeventsmap)
- [Egyéni riasztási szabályok (előzetes verzió)](#menu_customalerts)
- [Gombra a veszélyforrások elleni védelem a biztonsági riasztások vizsgálata](#menu_investigate)
- [Biztonsági megoldások egy részhalmazát](#menu_solutions)
- [Biztonsági szabályzatok biztonsági beállításokkal szerkesztése](#menu_securityconfigurations)
- [Biztonsági és auditálási irányítópultján (az OMS-portált eredetileg használt) Log Analytics-munkaterületeket.](#menu_securityomsdashboard)

A részletes információkat alább tartalmaz minden eltávolított funkciót és a lépéseket, amelyeket a csere funkciók használatával.

## Események irányítópultja<a name="menu_events"></a>
A Security Center használja a Microsoft Monitoring Agent a gyűjtése a különböző biztonsági konfigurációkat, és eseményként kapcsolódó gépekről származó, és ezeket az eseményeket az Ön munkaterületeire tárolja. A [események irányítópultja](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) lehetővé teszi, hogy ezek az adatok megtekintése és lényegében biztosít egy másik belépési pont a Log Analytics szolgáltatásba.

Az események irányítópultja módosítástól kivonjuk a forgalomból:

![Események munkaterület kiválasztására szolgáló képernyő megjelenítéséhez][1]

Az események irányítópultja, amely akkor jelenik meg, miután a felhasználó rákattint a munkaterülethez, el lesz távolítva:

![Események irányítópultja][2]

### <a name="events-dashboard---new-experience"></a>Események irányítópultja – új felülete

Ügyfelek biztatjuk a Log Analytics natív képességek jelentős események megtekintése a saját munkaterületen.
Már létrehozott egyéni jelentős esemény a Security Centerből, ha ezek elérhetők lesznek Log analytics-kiválasztása > Munkaterület -> mentett keresések. Az adatok nem lehet elvesztése sem módosította. Natív jelentős események is elérhetők az ugyanazon a képernyőn.

![Mentett keresések munkaterület][3]

## Keresés menüpont<a name="menu_search"></a>
Az Azure Security Center jelenleg használja a naplók keresése az Azure Monitor és a biztonsági adatok elemzése. Ez a képernyő lényegében szolgál a Log Analytics az előtérrendszer "[keresési](https://docs.microsoft.com/azure/security-center/security-center-search)" lap – így a felhasználók a kijelölt munkaterület keresési lekérdezések futtatására. A Keresés ablak módosítástól kivonjuk a forgalomból:

![Keresés lap][4]

### <a name="search-menu-entry---new-experience"></a>Menüpont – új keresés

Hogy használni **Log Analytics** natív képességek a munkaterület keresési lekérdezések végrehajtásához. Ehhez, ugorjon az Azure Log analytics, és válassza ki: "Logs":

![Log Analytics naplók lap][5]

## Klasszikus identitás és hozzáférés (előzetes verzió)<a name="menu_classicidentity"></a>
"Klasszikus" identitás és hozzáférés felhasználói élménye a Security Center olyan módon, az ügyfelek megtekintéséhez identitás- és hozzáférés-kapcsolódó információkat a log analytics volt megadva. Ez történt, az alábbi kattintással követve:

Kattintson a "Nézet klasszikus identitás és hozzáférés"

![Alkalmazásidentitás lap][6]

A képernyő ezen a lapon szeretné megnyitni, valamint "identitás és hozzáférés irányítópult":

![Alkalmazásidentitás lap - munkaterület kiválasztása][7]

Elemre kattint, a munkaterületen megnyílik a "Identitás- és hozzáférés" log analytics-irányítópult ahol ügyfelek identitás megtekinthet és érheti el az adatokat a saját munkaterület:

![Alkalmazásidentitás lap - irányítópult][8]

Az összes fenti három képernyővel módosítástól kivezetjük. Az adatok továbbra is elérhető a log analytics biztonsági megoldás, és nem módosulnak vagy eltávolítva.

### <a name="classic-identity--access-preview---new-experience"></a>Klasszikus identitás és hozzáférés (előzetes verzió) – új felülete
A Log analytics-irányítópult csak egy adott munkaterület a biztosított elemzések, míg a natív a Security Center képességei az összes előfizetés és az összes munkaterülethez társítva, egy könnyen betekintést biztosítanak-nézet, amely lehetővé teszi a mi összpontosíthat a Fontos, az az identitás és hozzáférés javaslat a biztonságos pontszám alapján.
Az identitás- és hozzáférés a Log analytics-irányítópult összes funkcióját a Security Center belül elérhető "Identitás és hozzáférés (előzetes verzió)" kiválasztásával:

![Alkalmazásidentitás lap – klasszikus élmény kivonása][9]

## Biztonsági események leképezése<a name="menu_securityeventsmap"></a>
A Security Center a következőket biztosítja egy [térkép](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) , amelyek segítségével azonosíthatja a környezetre leselkedő biztonsági fenyegetéseket. A "Ugrás a biztonsági események térkép" gombra, hogy a leképezés egy irányítópultot, amely lehetővé teszi a nyers biztonsági események megtekintése a kiválasztott munkaterületen vezet.
A gomb mellett a munkaterület irányítópult elavulásának után törlődni fog.

![Biztonsági riasztások térkép - gomb][10]

Még ma az "Ugrás a biztonsági események térkép" kattintva a Fenyegetésészlelési irányítópult nyílik meg. A fenyegetésészlelési irányítópult kivonjuk a forgalomból.  

![Fenyegetésfelderítési irányítópult][11]

Ha úgy dönt, hogy a munkaterületet a megfelelő fenyegetésészlelési irányítópult megtekintéséhez, a biztonsági riasztások map(Preview) képernyő *a Log Analytics* nyitja meg. Ez a képernyő kivonjuk a forgalomból.

![Biztonsági riasztások térkép a Log Analyticsben][12]

A meglévő adatok továbbra is elérhető a log analytics biztonsági megoldás és fog nem lehet módosítani, és nem eltávolítva.

### <a name="security-events-map---new-experience"></a>Biztonsági események térkép - új felülete
Azt javasoljuk, hogy ügyfeleink számára, hogy a riasztások térkép a Security Center beépített funkcióinak használata – "a biztonsági riasztások térkép (előzetes verzió)". Ez egy optimalizált felhasználói élményt biztosít, és együttműködés az összes előfizetés és társított munkaterületek, lehetővé téve a makró a környezete megtekintheti, és nem egyetlen munkaterület összpontosíthat.

## Egyéni riasztási szabályok (előzetes verzió)<a name="menu_customalerts"></a>
Egyéni riasztások lesz az [elavult](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) 2019. június 30., használatból való kivonást egyaránt az alapul szolgáló infrastruktúra miatt alapján készült. Amíg elavult időkeretét, a felhasználók tudni meglévő egyéni riasztási szabályok szerkesztése, de nem fogja tudni újakat vehet fel. Ahhoz, hogy a felhasználók végigvitelével [Azure Sentinel-](https://azure.microsoft.com/services/azure-sentinel/) automatikus áttelepítése a meglévő figyelmeztetéseket és újakat hoz létre, vagy másik lehetőségként létre újból a figyelmeztetéseket, és az Azure Monitor egy kattintással üzembe helyezésében naplóriasztások.

Tartani a meglévő riasztásokat, és Azure Sentinel-telepítheti át őket, indítsa el az Azure-Sentinel. Első lépésként válassza ki a munkaterületet, az egyéni értesítések tárolására, és válassza a "Analytics" menüpontot a riasztások automatikusan át.

![Egyéni riasztások][13]

Újra létre kell hoznia a riasztásokat az Azure Monitor riasztások biztatjuk a nem üzembe helyezése az Azure-Sentinel iránt. Útmutatásért tekintse meg: [létrehozása, megtekintése és kezelése az Azure Monitor használatával naplóriasztások](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log). Riasztások létrehozásával kapcsolatos útmutatásért tekintse meg: [Naplóriasztások az Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

Egyéni riasztások használatból való kivonást egyaránt további információkért tekintse meg a [a Security Center egyéni riasztások dokumentáció](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Biztonsági riasztások vizsgálat<a name="menu_investigate"></a>
[A vizsgálati funkciójáról](https://docs.microsoft.com/azure/security-center/security-center-investigation) a Security Center lehetővé teszi, hogy osztályozhatja, feltérképezheti a hatókörüket, és a potenciális biztonsági incidens kiváltó okának. Ez a szolgáltatás eltávolítja a Security Centerből, helyette a továbbfejlesztett élményt [Azure Sentinel-](https://azure.microsoft.com/services/azure-sentinel/).

![Biztonsági incidens][14]

Ha a fenti "Vizsgálat" gombra kattint, megnyílik a "vizsgálati irányítópult (előzetes verzió)" a Log Analyticsben. A vizsgálati irányítópult kivonjuk a forgalomból.  
A meglévő adatok továbbra is elérhető a Log Analytics biztonsági megoldás és fog nem lehet módosítani, és nem eltávolítva.

![A Log Analytics vizsgálati irányítópult][15]

### <a name="investigation---new-experience"></a>Vizsgálat – új felülete

Azt javasoljuk, hogy ügyfeleink számára, hogy előkészítése [Azure Sentinel-](https://azure.microsoft.com/services/azure-sentinel/) részletes vizsgálat biztosítása érdekében, a riasztások hunting szolgáltatás működteti. Az Azure Sentinel hatékony vadászat keresést és lekérdezéseket eszközöket egyszerre a szervezet biztonsági fenyegetések hunt biztosít.  

## Biztonsági megoldások részhalmazát<a name="menu_solutions"></a>

A Security Center képes engedélyezése [integrált Azure biztonsági megoldásait](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). A következő partneri megoldások lenne távolítva, és a támogatott [Azure Sentinel-](https://azure.microsoft.com/services/azure-sentinel/), valamint további adatforrásokhoz is.

- Új generációs tűzfal- és a Web application Firewall megoldások (az Azure-Sentinel [dokumentáció](https://docs.microsoft.com/azure/sentinel/connect-data-sources))
- A közös esemény (CEF) formátumot támogató biztonsági megoldások integrálása (Azure Sentinel- [dokumentáció](https://docs.microsoft.com/azure/sentinel/connect-common-event-format))
- Microsoft Advanced Threat Analytics (az Azure Sentinel [dokumentáció](https://docs.microsoft.com/azure/sentinel/connect-azure-atp))
- Az Azure AD Identity Protection (Azure Sentinel [dokumentáció](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection))

Az elavulással kapcsolatos, a következő felhasználók nem fognak tudni hozzáadhat új vagy meglévő csatlakoztatott megoldások a típusú említettük, a felhasználói felület és az API-t is módosíthatja.
A meglévő csatlakoztatott megoldások javasolt, hogy ez az időszak végén az Azure-Sentinel áthelyezése.

![Biztonsági megoldások központok][16]

## Biztonsági szabályzatok biztonsági beállításokkal szerkesztése<a name="menu_securityconfigurations"></a>
Az Azure Security Center biztonsági konfigurációk figyeli egy alkalmazásával [több mint 150 ajánlott szabályok](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) az operációs rendszer korlátozására, például a szabályok kapcsolódó tűzfalak, naplózás, jelszóházirendek és egyéb. Ha egy gép sebezhető konfigurációval rendelkezik, a Security Center biztonsági javaslatot hoz létre. A [szerkesztési biztonsági konfigurációs képernyőjén](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) szabhatja testre az alapértelmezett operációs rendszer biztonsági konfigurációs a Security Center lehetővé teszi.

Ez a funkció előzetes verzióban volt, és megszűnik.

![Biztonsági beállítások szerkesztése][17]

### <a name="edit-security-configurations---new-experience"></a>Biztonsági konfigurációk – új felület szerkesztése

A Security Center támogatja a [vendégen belüli ügynök](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) a közeljövőben, így sokkal gazdagabb szolgáltatáskészlet – például további operációs rendszerekhez és Vendég configuration (a Vendég szabályzatokkal az Azure-integráció szabályzatok). Ez lehetővé teszi nagy mennyiségű vezérlőelemet, és automatikusan alkalmazza az új erőforrásokat is biztosít.

## Biztonsági és auditálási irányítópultján (az OMS-portált eredetileg használt) Log Analytics-munkaterületek<a name="menu_securityomsdashboard"></a>

A biztonsági irányítópult a Log analytics jelentős biztonsági események és fenyegetések, fenyegetésészlelési térkép és identitás és hozzáférés értékelése a munkaterületen mentett biztonsági eseményeket a munkaterület áttekintést nyújt. Az irányítópult eltűnik a jövőben. Ahogy már javasoljuk az irányítópult felhasználói felület, hogy a felhasználók javasolja, hogy az Azure Security Center a jövőben.

![Log analytics-biztonság irányítópult][18]

### <a name="security--audit-dashboard---new-experience"></a>Biztonság és naplózás irányítópult – új felülete
Ügyfeleink javasolja, hogy az Azure Security Center, így az azonos biztonsági áttekintése több előfizetések és munkaterületek társítva, a szolgáltatás a levélforgalmi együtt.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [Azure Security Centerben](https://docs.microsoft.com/azure/security-center/)
- Tudjon meg többet [Azure Sentinel-](https://docs.microsoft.com/azure/sentinel)

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
