---
title: Jelentések & a figyelési telepítés megtervezése - Azure AD
description: A jelentésés figyelés implmentációjának megtervezése és végrehajtása.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad84b8910e8d4f8af9845c33c22d128e317dedc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232107"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Azure Active Directory-jelentéskészítés és üzembe helyezés figyelésének megtervezése

Az Azure Active Directory (Azure AD) jelentéskészítési és figyelési megoldás a jogi, biztonsági és működési követelményektől, valamint a meglévő környezettől és folyamatoktól függ. Ez a cikk bemutatja a különböző tervezési lehetőségeket, és végigvezeti a megfelelő telepítési stratégia.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Az Azure AD jelentéskészítési és figyelési előnyei

Az Azure AD-jelentés átfogó áttekintést és naplókat biztosít az Azure AD-tevékenységekről a környezetében, beleértve a bejelentkezési eseményeket, a naplózási eseményeket és a címtár változásait.

A megadott adatokkal a következőket teheti:

* határozza meg, hogyan használják az alkalmazásokat és szolgáltatásokat.

* a környezet egészségét érintő lehetséges kockázatok észlelése.

* elháríthatja azon problémákat, amelyek megakadályozzák, hogy a felhasználók elvégezzék a munkájukat.

* az Azure AD-címtár változásainak naplózási eseményeinek megtekintésével betekintést nyerhet.

> [!IMPORTANT]
> Az Azure AD figyelése lehetővé teszi, hogy az Azure AD-jelentések által generált naplókat különböző célrendszerekbe irányítsa. Ezután megőrizheti őket hosszú távú használatra, vagy külső biztonságiinformáció- és eseménykezelési (SIEM-) eszközökkel integrálva elemezheti a környezetet.

Az Azure AD figyelésével a naplókat a következőkre irányíthatja:

* archiválási célokra egy Azure storage-fiók.
* Az Azure Monitor naplói, korábbi nevén Azure Log Analytics munkaterület, ahol elemezheti az adatokat, irányítópultokat hozhat létre, és riasztást adhat meg bizonyos eseményekről.
* egy Azure-eseményközpont, ahol integrálhatja a meglévő SIEM eszközök, például Splunk, Sumologic, vagy QRadar.

> [!NOTE]
Nemrég kezdtük el használni az Azure Monitor naplók kifejezést a Log Analytics helyett. A naplóadatok továbbra is egy Log Analytics-munkaterületen tárolódnak, és ugyanaz a Log Analytics-szolgáltatás továbbra is gyűjti és elemzi. Frissítjük a terminológiát, hogy jobban tükrözze a naplók szerepét az [Azure Monitorban.](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection) A részletekért tekintse meg az [Azure Monitor terminológiai változásait.](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-rebrand)

[További információ a jelentésmegőrzési házirendekről.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention)

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licencelés és az Azure AD-jelentéskészítés és -figyelés előfeltételei

Az Azure AD-bejelentkezési naplók eléréséhez szüksége lesz egy Azure AD prémium licencre.

Az Azure Active Directory [díjszabási útmutatójában](https://azure.microsoft.com/pricing/details/active-directory/)részletes funkció- és licencelési információkat talál.

Az Azure AD figyelésének és jelentésjelentésének üzembe helyezéséhez olyan felhasználóra van szüksége, aki az Azure AD-bérlő globális rendszergazdája vagy biztonsági rendszergazdája.

A naplóadatok végső céljától függően az alábbiak egyikére van szükség:

* Egy Azure Storage-fiók, amelyen ListKeys jogosultsággal rendelkezik. Azt javasoljuk, hogy általános tárfiókot használjon, ne Blob Storage-fiókot. A tárolás díjszabásával kapcsolatban lásd az [Azure Storage-díjkalkulátort](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Az Azure Event Hubs névtere, amely integrálható a külső SIEM-megoldásokkal.

* Az Azure Log Analytics-munkaterület a naplók küldéséhez az Azure Monitor naplók.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Azure-jelentéskészítési és figyelési telepítési projekt tervezése

Ebben a projektben meghatározza a jelentéseket használó és figyelő közönségeket, és meghatározza az Azure AD figyelési architektúráját.

### <a name="engage-the-right-stakeholders"></a>Vonja be a megfelelő érdekelt feleket

Ha a technológiai projektek kudarcot vallanak, általában a hatásra, az eredményekre és a felelősségre vonatkozó, nem megfelelő elvárások miatt teszik ezt. Hogy elkerülje ezeket a buktatókat, [győződjön meg arról, hogy a megfelelő érdekelt feleket vonja be.](https://aka.ms/deploymentplans) Az érdekelt felek, valamint projektbemenetük és elszámoltathatóságuk dokumentálásával biztosítják az érdekelt felek projektben betöltött szerepének megfelelő megismertetését is.

### <a name="plan-communications"></a>A kommunikáció tervezése

A kommunikáció minden új szolgáltatás sikeréhez elengedhetetlen. Proaktív módon kommunikáljon a felhasználókkal arról, hogy a felhasználói élményük hogyan változik, mikor változik meg, és hogyan szerezhet támogatást, ha problémákat tapasztalnak.

### <a name="document-your-current-infrastructure-and-policies"></a>Az aktuális infrastruktúra és házirendek dokumentálása

A jelenlegi infrastruktúra és szabályzatok fogja vezetni a jelentéskészítési és figyelési tervezés. Győződjön meg róla, hogy tudja

* Mi, ha van ilyen, SIEM eszközöket használ.

* Az Azure-infrastruktúra, beleértve a meglévő tárfiókok és a figyeltség használatban van.

* A naplózási rendszer szervezeti megőrzési szabályzatai, beleértve a szükséges megfelelőségi keretrendszereket is. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Azure AD-jelentéskészítési és figyelési telepítés megtervezése

A jelentések és a figyelés az üzleti igényeknek való megfeleltetésre, a használati minták megismerésére és a szervezet biztonsági állapotának növelésére szolgálnak.

### <a name="business-use-cases"></a>Üzleti használati esetek

* Az üzleti igényeknek megfelelő megoldáshoz szükséges
* Örülök, hogy meg kell felelnie az üzleti igényeknek
* Nem alkalmazható

|Terület |Leírás |
|-|-|
|Megőrzés| **30 napnál nagyobb log-megőrzés**. Jogi vagy üzleti követelmények miatt 30 napnál hosszabb ideig kell tárolnia a naplózási naplókat és az Azure AD-naplóit. |
|Elemzés| **A naplókat kell kereshető**. A tárolt naplóknak analitikus eszközökkel kereshetőnek kell lenniük. |
| Operational Insights| **Betekintést a különböző csapatok**. Annak szükségességét, hogy hozzáférést biztosítson a különböző felhasználók számára, hogy működési elemzéseket nyerjenek, például az alkalmazáshasználat, a bejelentkezési hibák, az önkiszolgáló használat, a trendek stb. |
| Biztonsági elemzések| **Betekintést a különböző csapatok**. Annak szükségességét, hogy hozzáférést biztosítson a különböző felhasználók számára, hogy működési elemzéseket nyerjenek, például az alkalmazáshasználat, a bejelentkezési hibák, az önkiszolgáló használat, a trendek stb. |
| Integráció a SIEM rendszerekbe      | **SIEM integráció**. Az Azure AD-bejelentkezési naplók és naplónaplók meglévő SIEM-rendszerekbe való integrálásának és streamelésének szükségessége. |

### <a name="choose-a-monitoring-solution-architecture"></a>Figyelési megoldásarchitektúra kiválasztása

Az Azure AD-figyeléssel az Azure AD-tevékenységnaplókat egy olyan rendszerbe irányíthatja, amely a legjobban megfelel az üzleti igényeinek. Ezután megtarthatja őket hosszú távú jelentéskészítéshez és elemzéshez, hogy betekintést nyerjen a környezetébe, és integrálja azt a SIEM eszközökkel.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>Döntési folyamatdiagram![A következő szakaszokban ismertetett képet mutatja](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Archiválási naplók tárfiókban

A naplók egy Azure-tárfiókba történő útválasztásával azokat az [adatmegőrzési szabályzatokban](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention)ismertetett alapértelmezett megőrzési időszaknál hosszabb ideig őrizheti meg. Akkor használja ezt a módszert, ha archiválnia kell a naplókat, de nem kell integrálnia őket egy SIEM-rendszerrel, és nincs szüksége folyamatos lekérdezésekre és elemzésekre. Továbbra is elvégezheti az igény szerinti kereséseket.

További információ [az adatok a tárfiókokba való irányításáról](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account).

#### <a name="send-logs-to-azure-monitor-logs"></a>Naplók küldése az Azure Monitor-naplókba

[Az Azure Monitor naplók](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) konszolidálja a különböző forrásokból származó figyelési adatokat. Emellett egy lekérdezési nyelvet és elemzési motort is biztosít, amely betekintést nyújt az alkalmazások működésébe és az erőforrások használatába. Az Azure AD-tevékenységnaplók nak az Azure Monitor-naplókba való elküldésével gyorsan lekérheti, figyelheti és riasztást adhat az összegyűjtött adatokról. Akkor használja ezt a módszert, ha nem rendelkezik olyan meglévő SIEM-megoldással, amelynek közvetlenül el szeretné küldeni az adatokat, de szeretne lekérdezéseket és elemzéseket. Miután az adatok az Azure Monitor naplók, majd küldje el az eseményközpontba, és onnan egy SIEM, ha szeretné.

Megtudhatja, hogyan [küldhet adatokat az Azure Monitor naplóiba.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

Az Azure AD-tevékenységnaplók előre elkészített nézeteket is telepítheti a bejelentkezési és naplózási eseményeket érintő gyakori forgatókönyvek figyeléséhez.

Megtudhatja, hogyan [telepítheti és használhatja az Azure AD-tevékenységnaplók naplóinak naplóelemzési nézeteit.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

#### <a name="stream-logs-to-your-azure-event-hub"></a>Naplók streamelése az Azure-eseményközpontba

Útválasztási naplók egy Azure-eseményközpont lehetővé teszi az integrációt a külső SIEM-eszközökkel. Ez az integráció lehetővé teszi az Azure AD-tevékenységnapló-adatok és a SIEM által kezelt egyéb adatok kombinálását, hogy gazdagabb betekintést nyújtson a környezetbe. 

További információ [a naplók eseményközpontokba való streameléséről](https://docs.microsoft.com//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Műveletek és biztonság tervezése az Azure AD jelentéskészítéséhez és figyeléséhez

Az érdekelt feleknek hozzá kell férniaz Azure AD-naplókhoz, hogy operatív elemzéseket nyerjenek. A felhasználók közé tartoznak a biztonsági csapat tagjai, a belső vagy külső auditorok, valamint az identitás- és hozzáférés-kezelési műveletek csapata.

Az Azure AD-szerepkörök lehetővé teszik az Azure AD-jelentések konfigurálásának és megtekintésének lehetőségét a szerepkör alapján. Azonosítsa, hogy a szervezetben kinek van szüksége az Azure AD-jelentések olvasásához szükséges engedélyre, és hogy milyen szerepkör lenne számukra megfelelő. 

A következő szerepkörök olvashatják az Azure AD-jelentéseket:

* Globális rendszergazda

* Biztonsági rendszergazda

* Biztonsági olvasó

* Jelentésolvasó

További információ az [Azure AD felügyeleti szerepköreiről.](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)

*Mindig alkalmazza a minimális jogosultságok fogalmát a fiókfeltörés kockázatának csökkentése érdekében.* Fontolja meg [a kiemelt identitáskezelés megvalósítását](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) a szervezet további védelme érdekében.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Az Azure AD-jelentéskészítés és figyelés telepítése

A fenti tervezési útmutató használatával korábban meghozott döntésektől függően ez a szakasz a különböző telepítési lehetőségek dokumentációjához vezet.

### <a name="consume-and-archive-azure-ad-logs"></a>Azure AD-naplók felhasználása és archiválása

[Tevékenységjelentések keresése az Azure Portalon](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-find-activity-reports)

[Az Azure AD-naplók archiválása Egy Azure Storage-fiókba](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)

### <a name="implement-monitoring-and-analytics"></a>Nyomon követés és elemzés megvalósítása

[Naplók küldése az Azure Monitornak](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

[Az Azure Active Directory naplóelemzési nézeteinek telepítése és használata](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

[Azure AD-tevékenységnaplók elemzése az Azure Monitor-naplókkal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

* [Naplónaplók sémájának értelmezése az Azure Monitorban](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

* [Bejelentkezési séma értelmezése az Azure Monitorban](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema)

 * [Az Azure AD-naplók streamelése egy Azure-eseményközpontba](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)

* [Azure AD-naplók integrálása a Splunkkal az Azure Monitor használatával](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-integrate-activity-logs-with-splunk)

* [Azure AD-naplók integrálása a SumoLogickal az Azure Monitor használatával](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)

 

 

## <a name="next-steps"></a>További lépések

Fontolja meg [a kiemelt identitáskezelés megvalósítását](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) 

[Szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) megvalósításának megfontolása

 
