---
title: Security Center szolgáltatások kivonása (július 2019) | Microsoft Docs
description: Ez a cikk a Security Center azon funkcióit ismerteti, amelyek a 2019. július 31-én lesznek kivonva.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 8e7456eff3062ef6667e7b0022ea9740c397a493
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679252"
---
> [!NOTE]
> Ez a dokumentum részletesen ismerteti a Azure Security Center által kivont szolgáltatások listáját a 2019. július 31-ig.
>
>


# <a name="retirement-of-security-center-features-july-2019"></a>Security Center szolgáltatások kivonása (július 2019)

Az elmúlt hat hónapban számos [javítást](https://azure.microsoft.com/updates/?product=security-center) hajtottunk Azure Security Center.
Ezekkel a továbbfejlesztett képességekkel néhány redundáns szolgáltatást és kapcsolódó API-t törölünk a Security Centerról a 2019. július 31-én.  

A kivonási funkciók nagy része a Azure Security Center vagy az Azure Log Analytics új funkcióival is lecserélhető. Más funkciók az [Azure Sentinel (előzetes verzió)](https://azure.microsoft.com/services/azure-sentinel/)használatával valósíthatók meg.

Security Center kivonni kívánt funkciók közé tartoznak a következők:

- [Események irányítópult](#menu_events)
- [Keresési menü bejegyzése](#menu_search)
- [A klasszikus identitás & hozzáférési hivatkozás megtekintése az identitáson és a hozzáférésen (előzetes verzió)](#menu_classicidentity)
- [Biztonsági események leképezése gomb a biztonsági riasztások térképén (előzetes verzió)](#menu_securityeventsmap)
- [Egyéni riasztási szabályok (előzetes verzió)](#menu_customalerts)
- [A fenyegetések elleni védelem biztonsági riasztásai gombjának vizsgálata](#menu_investigate)
- [A biztonsági megoldások egy részhalmaza](#menu_solutions)
- [Biztonsági házirendek biztonsági konfigurációinak szerkesztése](#menu_securityconfigurations)
- [Biztonsági és auditálási irányítópult (eredetileg a OMS-portálon) Log Analytics munkaterületekhez](#menu_securityomsdashboard)

Ez a cikk részletes információkat tartalmaz a kivont szolgáltatásokról, valamint a helyettesítő funkciók megvalósításához szükséges lépésekről.

## Események irányítópult<a name="menu_events"></a>

A Security Center a Microsoft monitoring Agent használatával gyűjt különféle, biztonsággal kapcsolatos konfigurációkat és eseményeket a gépekről. Ezeket az eseményeket a munkaterületeken tárolja. Az [események irányítópulton](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) megtekintheti ezeket az adatnézeteket, és belépési pontot biztosít a log Analyticshoz.

Kihasználjuk az események irányítópultot, amely akkor jelenik meg, amikor kiválasztja a munkaterületet:

![Események irányítópultja][2]

### <a name="events-dashboard---the-new-experience"></a>Események irányítópult – az új felület

Javasoljuk, hogy az Azure Log Analytics natív funkcióinak használatával megtekinthesse a jelentős eseményeket a munkaterületeken.

Ha Security Centerban egyéni jelentős eseményeket hozott létre, azok elérhetővé válnak. A log Analytics területen lépjen a **munkaterület** > **mentett keresések**kiválasztása elemre. Az adatai nem vesznek el vagy nem módosulnak. A natív jelentős események a Log Analytics azonos képernyőjén is elérhetők.

![Munkaterület által mentett keresések][3]

## Keresési menü bejegyzése<a name="menu_search"></a>

A Azure Security Center jelenleg Azure Monitor naplók keresésével kéri le és elemzi a biztonsági adatait. Ez a képernyő ablakként szolgál a keresési oldal Log Analyticséhez, és lehetővé teszi a felhasználók számára, hogy keresési lekérdezéseket futtassanak a kiválasztott munkaterületen. További információ: [Azure Security Center keresés](https://docs.microsoft.com/azure/security-center/security-center-search). Kihasználjuk ezt a keresési ablakot:

![Keresési oldal][4]

### <a name="search-menu-entry---the-new-experience"></a>Keresési menü bejegyzése – az új felület

Javasoljuk, hogy az Azure Log Analytics natív képességeit használva végezzen keresési lekérdezéseket a munkaterületeken. Lépjen az Azure Log Analytics elemre, és válassza a **naplók**lehetőséget.

![Naplók Log Analytics lapja][5]

## Klasszikus identitás &-hozzáférés (előzetes verzió)<a name="menu_classicidentity"></a>

Security Center jelenleg a klasszikus identitás & a hozzáférési élmény az identitás-és hozzáférési információk irányítópultját mutatja Log Analytics. Az irányítópult megtekintése:

1. Válassza a **klasszikus identitás & hozzáférés megtekintése**lehetőséget.

   ![Identitás lap][6]

1. Tekintse meg az **Identity & Access irányítópultot**.

    ![Identitás lap – munkaterület kiválasztása][7]

1. Válassza ki a munkaterületet, és nyissa meg az **identity & Access** irányítópultot log Analytics a munkaterületen található identitás-és hozzáférési információk megtekintéséhez.

   ![Identitás lap – irányítópult][8]

Az előző lépésekben látható mindhárom képernyőt kihasználjuk. Az adatai elérhetők maradnak a Log Analytics biztonsági megoldásban, és nem lesznek módosítva vagy eltávolítva.

### <a name="classic-identity--access-preview---the-new-experience"></a>Klasszikus identitás &-hozzáférés (előzetes verzió) – az új felület

Az Log Analytics irányítópulton egyetlen munkaterületen jelennek meg az eredmények. A natív Security Center képességek azonban megtekinthetik az összes előfizetést és a hozzájuk társított munkaterületeket. Egy könnyen használható nézethez férhet hozzá, amely lehetővé teszi, hogy a biztonságos pontszám alapján rangsorolja a javaslatokkal kapcsolatos tudnivalókat.

Az **identitás &** log Analytics elérési irányítópultjának összes funkcióját elérhetővé teheti, ha az **identitás & hozzáférés (előzetes verzió)** lehetőséget választja Security Centeron belül.

![Identitás lap – klasszikus élmény][9]

## Biztonsági események térképe<a name="menu_securityeventsmap"></a>

A Security Center [biztonsági riasztási térképet](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) biztosít a biztonsági fenyegetések azonosításához. A Térkép **biztonsági események megjelenítése** gombjára kattintva megnyílik egy irányítópult, amely lehetővé teszi a nyers biztonsági események megtekintését a kiválasztott munkaterületen.

A **Go to Security Events Map (ugrás a biztonsági események leképezése** ) gombra és a munkaterület-irányítópultra kerül.

![Biztonsági riasztások térképe – gomb][10]

Amikor bejelöli a **Ugrás a biztonsági események leképezése** gombra, megnyithatja a fenyegetés intelligencia irányítópultot. Kihasználjuk a fenyegetés intelligencia irányítópultját.  

![Fenyegetésészlelési irányítópult][11]

Amikor kiválaszt egy munkaterületet a fenyegetés intelligencia irányítópultjának megtekintéséhez, nyissa meg a biztonsági riasztások leképezése (előzetes verzió) képernyőt a Log Analytics. Kihasználjuk ezt a képernyőt.

![Biztonsági riasztások leképezése Log Analytics][12]

A meglévő adatai továbbra is elérhetők lesznek a Log Analytics biztonsági megoldásban, és nem lesznek módosítva és nem távolíthatók el.

### <a name="security-events-map---the-new-experience"></a>Biztonsági események térképe – az új felület

Javasoljuk, hogy a Security Center beépített riasztások leképezési funkcióját használja: **Biztonsági riasztások térképe (előzetes verzió)** . Ez a funkció optimalizált élményt nyújt, és az összes előfizetésben és a társított munkaterületeken is működik. Magas szintű áttekintést nyújt a környezetről, és nem egyetlen munkaterületre koncentrál.

## Egyéni riasztási szabályok (előzetes verzió)<a name="menu_customalerts"></a>

2019. június 30-án kivonásra kerül [az egyéni riasztások felülete](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) , mert a mögöttes infrastruktúra kivonásra kerül. Addig is szerkesztheti a meglévő egyéni riasztási szabályokat, de nem adhat hozzá újakat. A lejárati dátum után a definiált egyéni riasztások nem lépnek érvénybe, és a szabályok alapján nem jönnek létre biztonsági riasztások.
Javasoljuk, hogy engedélyezze az [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) használatát, és hozza létre újra az egyéni riasztásokat. Azt is megteheti, hogy riasztásokat hoz létre Azure Monitor log-riasztásokkal.

A meglévő riasztások megőrzése és az Azure Sentinel használatával történő létrehozása:

1. [Nyissa meg az Azure sentinelt](https://portal.azure.com/#create/Microsoft.ASI/preview) , és válassza ki azt a munkaterületet, ahol az egyéni riasztások tárolódnak
1. Válassza az **elemzés** lehetőséget a menüből
1. Kövesse az alábbi [oktatóanyag](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) utasításait, hogy miként hozhat létre egyéni riasztásokat az Azure sentinelben

Ha nem érdekli az Azure Sentinel használata, a riasztásokat Azure Monitor log-riasztásokkal is létrehozhatja. Útmutatásért tekintse meg a [naplók létrehozásával, megtekintésével és kezelésével](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) kapcsolatos riasztásokat a Azure monitor és [a naplózási riasztások](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)segítségével Azure monitorban.

![Egyéni riasztások][13]

További információ az egyéni riasztások kivonásáról: [Azure Security Center (előzetes verzió) egyéni riasztási szabályai](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Biztonsági riasztások vizsgálata<a name="menu_investigate"></a>

[A Security Center vizsgálati funkciója](https://docs.microsoft.com/azure/security-center/security-center-investigation) segíti a potenciális biztonsági incidensek osztályozását. A szolgáltatás segítségével megismerheti az incidensek hatókörét, és nyomon követheti a kiváltó okot. Ezt a funkciót a Security Center távolítjuk el, mivel az az [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)továbbfejlesztett felületét váltotta fel.

![Biztonsági incidens][14]

Amikor bejelöli a **vizsgálat gombot** egy **biztonsági incidens** képernyőjén, megnyithatja a vizsgálati irányítópultot (előzetes verzió) a log Analytics. Kihasználjuk a vizsgálati irányítópultot.  

A meglévő adatai továbbra is elérhetők lesznek a Log Analytics biztonsági megoldásban, és nem lesznek módosítva és nem távolíthatók el.

![Vizsgálati irányítópult a Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Vizsgálat – az új felület

Javasoljuk, hogy a részletes nyomozási élmény érdekében váltson át az [Azure sentinelre](https://azure.microsoft.com/services/azure-sentinel/) . Az Azure Sentinel hatékony keresési és lekérdezési eszközöket kínál a szervezet adatforrásaiban a biztonsági fenyegetések elleni vadászathoz.  

## A biztonsági megoldások részhalmaza<a name="menu_solutions"></a>

[A Security Center az Azure-ban is engedélyezheti az integrált biztonsági megoldásokat](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). A következő partneri megoldásokat kihasználjuk Security Centerról. Ezek a megoldások számos további adatforrással együtt engedélyezettek az [Azure sentinelben](https://azure.microsoft.com/services/azure-sentinel/) .

- [A következő generációs tűzfal-és webalkalmazási tűzfal megoldásai](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [A Common Event Format (CEF) formátumot támogató biztonsági megoldások integrálása](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

A kivonulás után nem fogja tudni felvenni vagy módosítani az előző listában említett megoldások egyikét sem a felhasználói felületen, sem az API-ban.

Ha már rendelkezik csatlakoztatott megoldásokkal, javasoljuk, hogy váltson az Azure Sentinelre.

![A Security Center megoldásai][16]

## Biztonsági házirendek biztonsági konfigurációinak szerkesztése<a name="menu_securityconfigurations"></a>

Azure Security Center a biztonsági konfigurációkat [több mint 150 javasolt szabály](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)alkalmazásával figyeli. az operációs rendszer megerősítéséhez. Ezek a szabályok a tűzfalakra, a naplózásra, a jelszó-házirendekre és egyebekre vonatkoznak. Ha egy gép konfigurációját sebezhetőnek találja, a Security Center egy biztonsági javaslatot állít elő. A [biztonsági beállítások szerkesztése képernyő](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) lehetővé teszi, hogy az ügyfelek testre szabják az operációs rendszer alapértelmezett biztonsági konfigurációját Security Centerban.

Kihasználjuk ezt az előzetes funkciót. Ha a lejárati dátum után vissza szeretné állítani a biztonsági konfigurációkat az alapértelmezett értékekre, ezt az [alábbi utasításokat](https://aka.ms/ascresetsecurityconfigurations) követve teheti meg az API-n vagy a powershellen keresztül.

![Biztonsági beállítások szerkesztése][17]

### <a name="edit-security-configurations---the-new-experience"></a>Biztonsági konfigurációk szerkesztése – az új felület

Javasoljuk, hogy Security Center támogassa a [vendég konfigurációs ügynököt](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration). Egy ilyen frissítés lehetővé teszi a sokkal gazdagabb szolgáltatáskészlet használatát, többek között több operációs rendszer támogatását és az Azure in-Guest-szabályzatok a vendég konfigurációkhoz való integrálását. Miután a módosítások engedélyezve lettek, a konfigurációkat a méretezési lehetőséggel is vezérelheti, és automatikusan alkalmazhatja őket az új erőforrásokra.

## Biztonsági és naplózási irányítópult a Log Analytics-munkaterületekhez<a name="menu_securityomsdashboard"></a>

A biztonsági és auditálási irányítópultot eredetileg a OMS-portálon használták. Log Analytics az irányítópult a jelentős biztonsági események és fenyegetések, a veszélyforrások felderítése és a munkaterületen mentett biztonsági események identitás-és hozzáférés-elemzését is tartalmazza. Eltávolítjuk az irányítópultot. Ahogy az irányítópult felhasználói felületén már ajánlott, javasoljuk, hogy váltson Azure Security Centerra.

![Log Analytics biztonsági irányítópult][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Biztonsági és auditálási irányítópult – az új felület

Javasoljuk, hogy váltson Azure Security Centerra. Ugyanazt a biztonsági áttekintést nyújtja több előfizetésre és a hozzájuk társított munkaterületekre, valamint egy gazdagabb szolgáltatáskészlet-készletre.

A biztonsági és auditálási irányítópultot a Security Centerhoz tartozó [GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) -tárházban töltheti le az eredeti log Analytics lekérdezéseket.

## <a name="next-steps"></a>További lépések

- További információ a [Azure Security Centerról](https://docs.microsoft.com/azure/security-center/).
- További információ az [Azure sentinelről](https://docs.microsoft.com/azure/sentinel).

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
