---
title: A Azure Security Center újdonságai archívuma
description: A Azure Security Center újdonságait és változásait hat hónappal ezelőtt és korábban.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/30/2020
ms.author: memildin
ms.openlocfilehash: 854926c64b50cf4b8e7df9fa82da58b924ddbd83
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510420"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Archiválja a Azure Security Center újdonságait?

A [Azure Security Center legfontosabb újdonságai?](release-notes.md) a kiadási megjegyzések oldal az elmúlt hat hónapban tartalmaz frissítéseket, míg ez az oldal régebbi elemeket tartalmaz.

Ez az oldal a következő információkat tartalmazza:

- Új funkciók
- Hibajavítások
- Elavult funkciók


## <a name="june-2020"></a>2020. június

A júniusi frissítések a következők:
- [Secure score API (előzetes verzió)](#secure-score-api-preview)
- [Fejlett adatbiztonság SQL-gépekhez (Azure, egyéb felhők és helyszíni) (előzetes verzió)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [Két új javaslat a Log Analytics-ügynök üzembe helyezéséhez az Azure arc Machines szolgáltatásban (előzetes verzió)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Új szabályzatok folyamatos exportálási és munkafolyamat-automatizálási konfigurációk létrehozásához nagy méretekben](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Új javaslat a NSG használatára a nem internetre irányuló virtuális gépek elleni védelemhez](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Új szabályzatok a fenyegetések elleni védelem és a speciális adatbiztonság engedélyezéséhez](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Secure score API (előzetes verzió)

Mostantól elérheti a pontszámát a [Secure score API](/rest/api/securitycenter/securescores/) -n keresztül (jelenleg előzetes verzióban érhető el). Az API-módszerek lehetővé teszik az adatlekérdezés rugalmasságát és a biztonságos pontszámok saját jelentési mechanizmusának elkészítését az idő múlásával. Használhatja például a **Secure scores** API-t egy adott előfizetés pontszámának lekéréséhez. Emellett a **Secure score Controls** API használatával is listázhatja az előfizetések biztonsági vezérlőit és aktuális pontszámát.

A biztonságos pontszám API-val lehetséges külső eszközökre vonatkozó példákat a [GitHub-Közösség biztonságos pontszám területén](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)tekintheti meg.

További információ a [Azure Security Center biztonságos pontszámáról és biztonsági vezérlőinek](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a>Fejlett adatbiztonság SQL-gépekhez (Azure, egyéb felhők és helyszíni) (előzetes verzió)

Az SQL Machines szolgáltatáshoz készült fejlett adatbiztonság mostantól védi az Azure-ban üzemeltetett SQL-kiszolgálókat, más felhőalapú környezetekben, vagy akár helyszíni gépeken is. Azure Security Center Ez kibővíti az Azure-natív SQL-kiszolgálók védelmét, hogy teljes mértékben támogassa a hibrid környezeteket.

A speciális adatbiztonság biztonsági rések felmérését és komplex veszélyforrások elleni védelmet biztosít az SQL-gépek számára, bárhol is legyenek.

A beállítás két lépést tesz szükségessé:

1. Az Log Analytics-ügynök üzembe helyezése a SQL Server gazdagépén az Azure-fiókkal való kapcsolódás biztosításához.

1. A választható csomag engedélyezése Security Center díjszabási és beállítási lapján.

További információ [az SQL-gépek speciális adatbiztonságáról](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Két új javaslat a Log Analytics-ügynök üzembe helyezéséhez az Azure arc Machines szolgáltatásban (előzetes verzió)

Két új javaslat lett hozzáadva a [log Analytics-ügynök](../azure-monitor/platform/log-analytics-agent.md) üzembe helyezéséhez az Azure arc-gépeken, és gondoskodni kell arról, hogy a Azure Security Center védelemmel rendelkezzenek:

- **Log Analytics ügynöknek telepítve kell lennie a Windows-alapú Azure arc-gépeken (előzetes verzió)**
- **Log Analytics ügynöknek telepítve kell lennie a Linux-alapú Azure arc-gépeken (előzetes verzió)**

Ezek az új javaslatok ugyanabban a négy biztonsági vezérlőben jelennek meg, mint a meglévő (kapcsolódó) javaslat, a **figyelési ügynök telepítése a gépekre**: a biztonsági konfigurációk szervizelése, adaptív alkalmazás-vezérlés alkalmazása, rendszerfrissítések alkalmazása és az Endpoint Protection engedélyezése.

A javaslatok az üzembe helyezési folyamat felgyorsításához szükséges gyors javítási képességgel is rendelkeznek. 

Ebből a két új javaslatból megismerheti a [számítási és alkalmazási javaslatok](recommendations-reference.md#recs-computeapp) táblázatát.

További információ arról, hogy a Azure Security Center hogyan használja az ügynököt a [log Analytics Agent](faq-data-collection-agents.md#what-is-the-log-analytics-agent)ügynökben?.

További tudnivalók az [Azure arc-gépek bővítményeiről](../azure-arc/servers/manage-vm-extensions.md).


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Új szabályzatok folyamatos exportálási és munkafolyamat-automatizálási konfigurációk létrehozásához nagy méretekben

A szervezet figyelési és incidens-reagálási folyamatainak automatizálása nagy mértékben növelheti a biztonsági incidensek kivizsgálásához és enyhítéséhez szükséges időt.

Az Automation-konfigurációk szervezeten belüli üzembe helyezéséhez használja ezeket a beépített "DeployIfdNotExist" Azure-szabályzatokat a [folyamatos exportálási](continuous-export.md) és [munkafolyamat-automatizálási](workflow-automation.md) eljárások létrehozásához és konfigurálásához:

A szabályzatok az Azure Policy szolgáltatásban találhatók:


|Cél  |Szabályzat  |Házirend-azonosító  |
|---------|---------|---------|
|Folyamatos Exportálás az Event hub-ba|[Az Azure Security Center-riasztások és -javaslatok Event Hubsba való exportálásának üzembe helyezése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Folyamatos exportálás Log Analytics munkaterületre|[Az Azure Security Center-riasztások és -javaslatok Log Analytics-munkaterületre való exportálásának üzembe helyezése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Biztonsági riasztások munkafolyamat-automatizálása|[Azure Security Center-riasztásokat automatizáló munkafolyamat üzembe helyezése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Munkafolyamat-automatizálás biztonsági javaslatokhoz|[Azure Security Center-javaslatokat automatizáló munkafolyamat üzembe helyezése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Ismerkedés a [munkafolyamat-automatizálási sablonokkal](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

További információ a két exportálási házirend használatáról [: a Munkafolyamat-automatizálás konfigurálása a megadott szabályzatok segítségével](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) és [folyamatos exportálás beállítása](continuous-export.md#set-up-a-continuous-export).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Új javaslat a NSG használatára a nem internetre irányuló virtuális gépek elleni védelemhez

Az "ajánlott biztonsági eljárások implementálása" biztonsági vezérlő mostantól az alábbi új javaslatot tartalmazza:

- **A nem internetkapcsolattal rendelkező virtuális gépeket hálózati biztonsági csoportokkal kell védeni**

Egy meglévő, **internetre irányuló virtuális gépet védeni kell a hálózati biztonsági csoportokkal**, nem kell különbséget tenni az internetre irányuló és a nem internetkapcsolattal rendelkező virtuális gépek között. Mindkét esetben magas súlyosságú javaslat jön létre, ha egy virtuális gépet nem hálózati biztonsági csoporthoz rendeltek hozzá. Ez az új javaslat elkülöníti a nem internetkapcsolattal rendelkező gépeket a téves pozitív problémák csökkentése és a szükségtelen magas súlyosságú riasztások elkerülése érdekében.

További információ a [hálózati javaslatok](recommendations-reference.md#recs-network) táblázatban található.




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Új szabályzatok a fenyegetések elleni védelem és a speciális adatbiztonság engedélyezéséhez

Az alábbi új szabályzatok az ASC alapértelmezett kezdeményezéshez lettek hozzáadva, és úgy vannak kialakítva, hogy segítséget nyújtsanak a veszélyforrások elleni védelem vagy a speciális adatbiztonság biztosításához a megfelelő erőforrástípusok esetében.

A szabályzatok az Azure Policy szolgáltatásban találhatók:


| Szabályzat                                                                                                                                                                                                                                                                | Házirend-azonosító                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [A speciális adatbiztonságot engedélyezni kell Azure SQL Database-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [A speciális adatbiztonságot engedélyezni kell a gépeken futó SQL-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Az összetett veszélyforrások elleni védelemnek engedélyezve kell lennie az Azure Storage-fiókokban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [A komplex veszélyforrások elleni védelemnek engedélyezve kell lennie Azure Key Vault-tárolón](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [A komplex veszélyforrások elleni védelemnek engedélyezve kell lennie Azure App Service-csomagokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [A komplex veszélyforrások elleni védelemnek engedélyezve kell lennie Azure Container Registry-jegyzékeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Az összetett veszélyforrások elleni védelemnek engedélyezve kell lennie az Azure Kubernetes Service-fürtökön](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [A komplex veszélyforrások elleni védelemnek engedélyezve kell lennie Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

További információ a [veszélyforrások elleni védelemről Azure Security Center](azure-defender.md).


## <a name="may-2020"></a>2020. május

A frissítések a következők lehetnek:
- [Riasztás-elnyomási szabályok (előzetes verzió)](#alert-suppression-rules-preview)
- [A virtuális gépek sebezhetőségi felmérése mostantól általánosan elérhető](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Az igény szerinti (JIT) virtuális gépekhez való hozzáférés módosításai](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Egyéni javaslatok lettek áthelyezve egy külön biztonsági vezérlőbe](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [A bekapcsolva beállítással megtekintheti a vezérlőkre vagy a lapos listára vonatkozó javaslatokat](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Kibővített biztonsági szabályozás – az ajánlott biztonsági eljárások megvalósítása](#expanded-security-control-implement-security-best-practices)
- [Az egyéni metaadatokkal rendelkező egyéni szabályzatok már általánosan elérhetők](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Összeomlási memóriakép-elemzési képességek migrálása a fájlok közötti támadás észlelése érdekében](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Riasztás-elnyomási szabályok (előzetes verzió)

Ez az új funkció (jelenleg előzetes verzióban érhető el) segít csökkenteni a riasztások fáradtságát. Szabályok használatával automatikusan elrejtheti azokat a riasztásokat, amelyek ismerten ártalmatlanok vagy a szervezeten belüli szokásos tevékenységekkel kapcsolatosak. Ez lehetővé teszi, hogy a leginkább releváns fenyegetésekre koncentráljon. 

Az engedélyezett letiltási szabályoknak megfelelő riasztások továbbra is létrejönnek, de az állapotukat a rendszer elutasítja. Megtekintheti az állapotot a Azure Portalban, vagy hozzáférhet a Security Center biztonsági riasztásokhoz.

A mellőzési szabályok határozzák meg azokat a feltételeket, amelyeknek a riasztásait automatikusan el kell utasítani. Általában letiltási szabályt kell használnia a következőkhöz:

- hamis pozitívként azonosított riasztások letiltása

- a túl gyakran kiváltott riasztások mellőzése

További információ a [riasztások Azure Security Center fenyegetés elleni védelemből való letiltásáról](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>A virtuális gépek sebezhetőségi felmérése mostantól általánosan elérhető

A Security Center standard szintű csomagja mostantól egy integrált sebezhetőségi felmérést is tartalmaz a virtuális gépekhez, felár nélkül. Ezt a bővítményt a Qualys működteti, de az eredményeket közvetlenül visszaküldi Security Centerra. Nincs szüksége Qualys-licencre, vagy akár Qualys-fiókra is – minden a Security Centeron belül zökkenőmentesen kezelhető.

Az új megoldás segítségével folyamatosan ellenőrizheti a virtuális gépeket, és megtalálhatja a biztonsági réseket, és bemutathatja az eredményeket a Security Centerban. 

A megoldás üzembe helyezéséhez használja az új biztonsági javaslatot:

"A beépített sebezhetőségi felmérési megoldás engedélyezése a virtuális gépeken (Qualys-alapú)"

További információ a [Security Center a virtuális gépek integrált sebezhetőségi felméréséről](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Az igény szerinti (JIT) virtuális gépekhez való hozzáférés módosításai

Security Center tartalmaz egy opcionális szolgáltatást a virtuális gépek felügyeleti portjainak a megóvásához. Ez védelmet nyújt a találgatásos támadás leggyakoribb formája ellen.

Ez a frissítés a következő módosításokat hajtja végre a szolgáltatásban:

- A rendszer azt ajánlja, hogy a JIT engedélyezése egy virtuális gépen legyen átnevezve. Korábban "igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken." most már: "a virtuális gépek felügyeleti portjait az igény szerinti hálózati hozzáférés-vezérléssel kell védeni."

- A javaslat csak akkor aktiválódik, ha nyitott felügyeleti portok vannak.

További információ [az JIT-hozzáférési szolgáltatásról](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Egyéni javaslatok lettek áthelyezve egy külön biztonsági vezérlőbe

A fokozott biztonságú pontszámmal bevezetett egyik biztonsági vezérlő a következő: "az ajánlott biztonsági eljárások megvalósítása". Az előfizetésekhez létrehozott egyéni javaslatok automatikusan ebbe a vezérlőbe kerülnek. 

Ahhoz, hogy könnyebben megtalálja az egyéni javaslatokat, áthelyezte őket egy dedikált biztonsági vezérlőbe, "egyéni javaslatok". Ez a vezérlő nem befolyásolja a biztonságos pontszámot.

További információ a biztonsági ellenőrzésekről a [Azure Security Center továbbfejlesztett biztonságos pontszám (előzetes verzió)](secure-score-security-controls.md)című témakörben.


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>A bekapcsolva beállítással megtekintheti a vezérlőkre vagy a lapos listára vonatkozó javaslatokat

A biztonsági vezérlők a kapcsolódó biztonsági javaslatok logikai csoportjai. A sebezhető támadási felületeket tükrözik. A vezérlők a biztonsági javaslatok összessége, amelyek útmutatást nyújtanak a javaslatok megvalósításához.

Ha szeretné azonnal megtekinteni, hogy a szervezet milyen jól védi az egyes támadási felületet, tekintse át az egyes biztonsági vezérlők pontszámait.

Alapértelmezés szerint a javaslatok a biztonsági vezérlőkben jelennek meg. Ebből a frissítésből listát is megjeleníthet. Ha az érintett erőforrások állapotának megfelelően rendezi az egyszerű listát, használja az új "Group By Controls" (csoportosítás a vezérlők szerint) lehetőséget. A váltógomb a portálon megjelenő lista felett van.

A biztonsági vezérlők – és ez a váltás – az új biztonsági pontszám részét képezik. Ne feledje, hogy küldje el nekünk a visszajelzéseit a portálon belülről.

További információ a biztonsági ellenőrzésekről a [Azure Security Center továbbfejlesztett biztonságos pontszám (előzetes verzió)](secure-score-security-controls.md)című témakörben.

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="Javaslatok csoportosítása vezérlőkkel":::

### <a name="expanded-security-control-implement-security-best-practices"></a>Kibővített biztonsági szabályozás – az ajánlott biztonsági eljárások megvalósítása 

A fokozottan biztonságos pontszámsal bevezetett egyik biztonsági vezérlő "az ajánlott biztonsági eljárások megvalósítása". Ha egy javaslat ebben a vezérlőben van, az nem érinti a biztonságos pontszámot. 

Ezzel a frissítéssel három javaslatot helyeztek el azokról a vezérlőkről, amelyekben eredetileg elhelyezték őket, és az ajánlott eljárások szabályozásával. Azért tettük ezt a lépést, mert azt állapítottuk meg, hogy a három javaslat kockázata alacsonyabb, mint eredetileg gondolták.

Emellett két új javaslat is be lett vezetve, és hozzá lett adva a vezérlőhöz.

Az áthelyezett három javaslat a következőket tartalmazza:

- Az **MFA-t engedélyezni kell az előfizetés olvasási engedéllyel rendelkező fiókjain** (eredetileg az "MFA engedélyezése" vezérlőben)
- Az **olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből** (eredetileg a "hozzáférés és engedélyek kezelése" vezérlőben)
- Az **előfizetéshez legfeljebb 3 tulajdonost kell kijelölni** (eredetileg a "hozzáférés és engedélyek kezelése" vezérlőben)

A vezérlőhöz hozzáadott két új javaslat a következő:

- **A vendég konfigurációs bővítményt a Windows rendszerű virtuális gépekre (előzetes verzió) kell telepíteni** – a [Azure Policy vendég konfigurációjának](../governance/policy/concepts/guest-configuration.md) használatával a virtuális gépeken belül láthatók a kiszolgálók és az alkalmazások beállításai (csak Windows).

- A **Windows Defender Exploit Guard védelmet engedélyezni kell a gépeken (előzetes verzió)** – a Windows Defender Exploit Guard kihasználja a Azure Policy vendég konfigurációs ügynököt. A kiaknázási Gárda négy összetevőből áll, amelyek célja, hogy zárolják az eszközöket a különböző támadási vektorok és a kártevők elleni támadásokban leggyakrabban használt viselkedések blokkolásával, miközben lehetővé teszik a vállalatok számára a biztonsági kockázatok és a termelékenységi követelmények kiegyensúlyozását (csak Windows).

További információ a Windows Defender Exploit Guard-védelemről a biztonsági [rés kiaknázására szolgáló szabályzat létrehozása és üzembe helyezése című](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)témakörben.

További információ a biztonsági ellenőrzésekről: [bővített biztonságos pontszám (előzetes verzió)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Az egyéni metaadatokkal rendelkező egyéni szabályzatok már általánosan elérhetők

Az egyéni szabályzatok mostantól a Security Center javaslatok, a biztonságos pontszám és a szabályozási megfelelőségi szabványok irányítópultjának részét képezik. Ez a funkció mostantól általánosan elérhető, és lehetővé teszi a szervezet biztonsági értékelésének kiterjesztését Security Center. 

Hozzon létre egy egyéni kezdeményezést az Azure Policy-ben, adja hozzá a szabályzatokat, és helyezze be Azure Security Centerba, és jelenítse meg javaslatként.

Most hozzáadjuk az egyéni ajánlási metaadatok szerkesztésének lehetőségét is. A metaadat-beállítások közé tartozik a súlyosság, a Szervizelési lépések, a fenyegetésekkel kapcsolatos információk és egyebek.  

További információk az [Egyéni javaslatok részletes információkkal történő növeléséről](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Összeomlási memóriakép-elemzési képességek migrálása a fájlok közötti támadás észlelése érdekében 

Integráljuk a Windows Crash dump Analysis (CDA) észlelési funkcióit a [fájlok közötti támadás észlelésére](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers). A fájlok közötti támadás észlelésének elemzése a következő biztonsági riasztások továbbfejlesztett verzióit biztosítja a Windows rendszerű gépekhez: kód befecskendezése felderített, álcázott Windows-modul, észlelt Héjkód, és gyanús kódrészletet észlelt.

Az áttérés néhány előnye:

- **Proaktív és kellő időben kártevő szoftverek észlelése** – a CDA megközelítése arra vár, hogy összeomlik, majd futtatja az elemzést a kártékony összetevők kereséséhez. A fájlok közötti támadás észlelésével a memóriában tárolt fenyegetések proaktív módon azonosíthatók a futás közben. 

- Bővített **riasztások** – a fájlokkal nem rendelkező támadások észlelésével kapcsolatos biztonsági riasztások közé tartoznak a CDA-ból nem elérhető bővítések, például az aktív hálózati kapcsolatok adatai. 

- **Riasztások összesítése** – ha a CDA több támadási mintát észlelt egyetlen összeomlási memóriaképen belül, több biztonsági riasztást váltott ki. A fájl nélkül történő támadás észlelése az összes azonosított támadási mintát egyetlen riasztásba ötvözi, így nem kell több riasztást összekapcsolni.

- A **log Analytics munkaterületre vonatkozó kisebb követelmények** – a potenciálisan bizalmas adatokat tartalmazó összeomlási memóriaképek többé nem lesznek feltöltve a log Analytics-munkaterületre.






## <a name="april-2020"></a>2020. április

Az áprilisi frissítések a következők:
- [A dinamikus megfelelőségi csomagok már általánosan elérhetők](#dynamic-compliance-packages-are-now-generally-available)
- [A Azure Security Center ingyenes szinten már szerepelnek a személyazonossággal kapcsolatos javaslatok](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>A dinamikus megfelelőségi csomagok már általánosan elérhetők

A Azure Security Center szabályozási megfelelőségi irányítópult mostantól tartalmazza a **dinamikus megfelelőségi csomagokat** (mostantól általánosan elérhető) a további iparági és szabályozási szabványok nyomon követéséhez.

A dinamikus megfelelőségi csomagok hozzáadhatók az előfizetéshez vagy a felügyeleti csoportjához a Security Center biztonsági házirend lapról. Ha standard vagy teljesítménytesztet készített elő, a szabvány a szabályzatoknak megfelelő megfelelőségi irányítópulton jelenik meg, amely az értékelésként leképezett összes megfelelőségi adattal együtt szerepel. A rendszer letölthetővé teszi az előkészített szabványok bármelyikének összegző jelentését.

Most hozzáadhat olyan szabványokat, mint például a:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF – v2020**
- **Egyesült Királyság hivatalos és egyesült királysági NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (új)** (az Azure CIS 1.1.0 teljes körű képviselete)

Emellett a közelmúltban hozzáadta az **Azure biztonsági teljesítménytesztet**, a Microsoft által készített Azure-specifikus iránymutatásokat a biztonsági és megfelelőségi ajánlott eljárásokhoz a közös megfelelőségi keretrendszerek alapján. Az irányítópulton további szabványok is támogatottak lesznek, amint azok elérhetővé válnak.  
 
További információ [a szabványok készletének testreszabásáról a szabályozási megfelelőségi irányítópulton](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>A Azure Security Center ingyenes szinten már szerepelnek a személyazonossággal kapcsolatos javaslatok

A Azure Security Center ingyenes szinten már általánosan elérhetők az identitásra és a hozzáférésre vonatkozó biztonsági javaslatok. Ez annak a erőfeszítésnek a részét képezi, hogy a Cloud Security testtartás-felügyeleti (CSPM) funkciók ingyenesek legyenek. Eddig ezek a javaslatok csak a standard díjszabási szinten voltak elérhetők.

Az identitásra és a hozzáférésre vonatkozó javaslatok például a következők:

- "A többtényezős hitelesítést engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon."
- "Az előfizetéshez legfeljebb három tulajdonost kell kijelölni."
- "Az elavult fiókokat el kell távolítani az előfizetésből."

Ha előfizetése van az ingyenes díjszabási szinten, a biztonsági pontszámok hatással lesznek a változásokra, mivel soha nem értékelték a személyazonosságát és a hozzáférés biztonságát.

További információ az [identitással és a hozzáférési javaslatokkal](recommendations-reference.md#recs-identity)kapcsolatban.

További információ az [identitás és a hozzáférés figyeléséről](security-center-identity-access.md).



## <a name="march-2020"></a>2020. március

A márciusi frissítések a következők:

- [A Munkafolyamat-automatizálás mostantól általánosan elérhető](#workflow-automation-is-now-generally-available)
- [Azure Security Center integrációja a Windows felügyeleti központtal](#integration-of-azure-security-center-with-windows-admin-center)
- [Az Azure Kubernetes szolgáltatás védelme](#protection-for-azure-kubernetes-service)
- [Továbbfejlesztett, igény szerinti élmény](#improved-just-in-time-experience)
- [Két biztonsági javaslat elavult webalkalmazásokhoz](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>A Munkafolyamat-automatizálás mostantól általánosan elérhető

A Azure Security Center munkafolyamat-automatizálási funkciója már általánosan elérhető. Ezzel a megoldással automatikusan aktiválhatja Logic Apps biztonsági riasztásokra és javaslatokra. Emellett manuális eseményindítók is elérhetők a riasztásokhoz és az összes olyan javaslathoz, amelynél elérhető a gyors javítás lehetőség.

Minden biztonsági program több munkafolyamatot tartalmaz az incidensek megválaszolásához. Ezek a folyamatok magukban foglalhatják az érintett érintett felek értesítését, a módosítási felügyeleti folyamat indítását és az adott szervizelési lépések alkalmazását. A biztonsági szakértők azt ajánlják, hogy az eljárások több lépésének automatizálására legyen lehetőség. Az Automation csökkenti a terhelést, és javíthatja a biztonságot azáltal, hogy a folyamat lépéseinek gyors, konzisztens és az előre meghatározott követelmények szerint kell történnie.

A munkafolyamatok futtatásának automatikus és manuális Security Center funkcióival kapcsolatos további információkért lásd: [munkafolyamat-automatizálás](workflow-automation.md).

További információ a [Logic apps létrehozásáról](../logic-apps/logic-apps-overview.md).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Azure Security Center integrációja a Windows felügyeleti központtal

Mostantól közvetlenül a Azure Security Center helyezheti át a helyszíni Windows-kiszolgálókat a Windows felügyeleti központból. A Security Center ezután az egyetlen üvegtábla lesz a Windows felügyeleti központ összes erőforrásának (beleértve a helyszíni kiszolgálókat, a virtuális gépeket és a további Pásti munkaterheléseket) biztonsági információinak megtekintésére.

Miután áthelyezett egy kiszolgálót a Windows felügyeleti központból a Azure Security Centerba, a következőket teheti:

- Tekintse meg a biztonsági riasztásokat és javaslatokat a Windows felügyeleti központ Security Center bővítményében.
- Tekintse meg a biztonsági állapotot, és kérje le a Windows felügyeleti központ felügyelt kiszolgálóinak további részletes információit a Azure Portalon (vagy egy API-n keresztül) található Security Centerban.

További információ a [Azure Security Center integrálásáról a Windows felügyeleti központba](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Az Azure Kubernetes szolgáltatás védelme

Azure Security Center kibővíti a tárolók biztonsági funkcióit az Azure Kubernetes szolgáltatás (ak) védelmére.

A népszerű, nyílt forráskódú platform Kubernetes olyan széles körben vezették be, hogy most már iparági szabványnak számít a tárolók összehangolása. Ez a széleskörű megvalósítás ellenére még mindig hiányzik a Kubernetes-környezet biztonságossá tételével kapcsolatos megértés. Egy tároló alkalmazás támadási felületének védelme szaktudást igényel annak biztosításához, hogy az infrastruktúra biztonságosan legyen konfigurálva, és folyamatosan figyelje a potenciális fenyegetéseket.

A Security Center védelmi szolgáltatás a következőket tartalmazza:

- **Felderítés és láthatóság** – a felügyelt AK-példányok folyamatos felderítése a Security Center regisztrált előfizetéseken belül.
- **Biztonsági javaslatok** – gyakorlati ajánlások az AK-ra vonatkozó biztonsági eljárások betartása érdekében. Ezek a javaslatok a biztonságos pontszám részét képezik, így biztosítva, hogy a szervezete biztonsági helyzete része legyen. Egy példa arra, hogy a "szerepköralapú hozzáférés-vezérlést kell használni a Kubernetes Service-fürthöz való hozzáférés korlátozásához".
- **Veszélyforrások elleni védelem** – az AK üzembe helyezésének folyamatos elemzése révén Security Center riasztást küld, amely a gazdagép és az AK-fürt szintjén észlelt fenyegetésekkel és rosszindulatú tevékenységgel kapcsolatos.

További információ az [Azure Kubernetes Services és a Security Center integrálásáról](defender-for-kubernetes-introduction.md).

További információ [a Security Center tároló biztonsági funkcióival](container-security.md)kapcsolatban.


### <a name="improved-just-in-time-experience"></a>Továbbfejlesztett, igény szerinti élmény

A felügyeleti portok védelmét biztosító, a Azure Security Center funkcióit, műveleteit és felhasználói felületét a következőképpen fejlesztettük ki: 

- **Indoklási mező** – ha egy virtuális GÉPHEZ (VM) való hozzáférést kér a Azure Portal igény szerinti oldalán, egy új opcionális mező is elérhető, amely a kérelem indoklását adja meg. A mezőben megadott információ nyomon követhető a tevékenység naplójában. 
- A **redundáns igény szerinti (JIT) szabályok automatikus karbantartása** – amikor egy JIT-házirendet frissít, a rendszer automatikusan futtat egy karbantartó eszközt a teljes szabályrendszert érvényességének ellenőrzéséhez. Az eszköz eltéréseket keres a szabályzat szabályai és a NSG lévő szabályok között. Ha a tisztítási eszköz nem megfelelőnek találja, akkor meghatározza az okot, és ha ez biztonságos, eltávolítja a már nem szükséges beépített szabályokat. A tisztább soha nem törli a létrehozott szabályokat. 

További információ [az JIT-hozzáférési szolgáltatásról](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Két biztonsági javaslat elavult webalkalmazásokhoz

A webalkalmazásokkal kapcsolatos két biztonsági javaslat elavult: 

- A IaaS-NSG lévő webalkalmazások szabályait meg kell erősíteni.
    (Kapcsolódó szabályzat: a IaaS webalkalmazásaihoz tartozó NSG-szabályokat meg kell erősíteni)

- A App Serviceshoz való hozzáférést korlátozni kell.
    (Kapcsolódó házirend: a App Services elérését korlátozni kell [előzetes verzió])

Ezek a javaslatok többé nem jelennek meg a javaslatok Security Center listájában. A kapcsolódó házirendeket a rendszer nem fogja tartalmazni a "Security Center default" nevű kezdeményezésben.

További információ a [biztonsági javaslatokról](recommendations-reference.md).




## <a name="february-2020"></a>2020. február

### <a name="fileless-attack-detection-for-linux-preview"></a>Fájl alapú támadás észlelése Linux rendszerhez (előzetes verzió)

Mivel a támadók egyre nagyobb mértékben alkalmaznak legrejtettebb módszereket az észlelés elkerülésére, Azure Security Center a Windows rendszereken kívül kiterjeszti a fájl-alapú támadási észlelést Linux rendszeren. A szoftveres biztonsági réseket rosszindulatú támadások okozják, a kártékony hasznos adatokat a jóindulatú rendszerfolyamatok szúrják be, és elrejtik a memóriában. Ezek a technikák:

- a kártevők nyomkövetésének csökkentése vagy megszüntetése lemezen
- nagy mértékben csökkentheti a lemezes kártevő-ellenőrzési megoldások általi észlelés esélyét

Ennek a fenyegetésnek a ellensúlyozására a 2018-es, a Windows rendszerhez készült, a fájloktól mentesen észlelhető támadás észlelését Azure Security Center, és a Linuxon is kiterjesztette a fájlokkal való támadás észlelését. 



## <a name="january-2020"></a>2020. január

### <a name="enhanced-secure-score-preview"></a>Bővített biztonságos pontszám (előzetes verzió)

A Azure Security Center biztonságos pontszám funkciójának továbbfejlesztett verziója már előzetes verzióban érhető el. Ebben a verzióban a több javaslat olyan biztonsági vezérlőkbe van csoportosítva, amelyek jobban tükrözik a sebezhető támadási felületet (például a felügyeleti portokhoz való hozzáférés korlátozása).

Ismerkedjen meg a biztonsági pontszám változásaival az előzetes verzió fázisában, és határozza meg azokat a további szervizeléseket, amelyek segítséget nyújtanak a környezet további védelméhez.

További információ a [bővített biztonságos pontszámról (előzetes verzió)](secure-score-security-controls.md).



## <a name="november-2019"></a>2019. november

A novemberi frissítések a következők:
 - [Veszélyforrások elleni védelem Észak-Amerika régiók Azure Key Vault számára (előzetes verzió)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Az Azure Storage veszélyforrások elleni védelme magában foglalja a kártevők ismertségének szűrését](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Munkafolyamat-automatizálás Logic Appssal (előzetes verzió)](#workflow-automation-with-logic-apps-preview)
 - [Általánosan elérhető a tömeges erőforrások gyors javítása](#quick-fix-for-bulk-resources-generally-available)
 - [Biztonsági rések keresése a tárolók rendszerképein (előzetes verzió)](#scan-container-images-for-vulnerabilities-preview)
 - [További szabályozási megfelelőségi szabványok (előzetes verzió)](#additional-regulatory-compliance-standards-preview)
 - [Veszélyforrások elleni védelem az Azure Kubernetes szolgáltatásban (előzetes verzió)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Virtuális gépek sebezhetőségének felmérése (előzetes verzió)](#virtual-machine-vulnerability-assessment-preview)
 - [Speciális adatbiztonság az Azure-beli SQL-kiszolgálókon Virtual Machines (előzetes verzió)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Egyéni szabályzatok támogatása (előzetes verzió)](#support-for-custom-policies-preview)
 - [Azure Security Center lefedettség kiterjesztése közösségi és partneri platformmal](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Speciális integrációk javaslatok és riasztások exportálásával (előzetes verzió)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Helyszíni helyszíni kiszolgálók Security Center a Windows felügyeleti központból (előzetes verzió)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Veszélyforrások elleni védelem Észak-Amerika régiók Azure Key Vault számára (előzetes verzió)

Azure Key Vault egy alapvető szolgáltatás az adatok védelméhez és a felhőalapú alkalmazások teljesítményének javításához azáltal, hogy központilag kezeli a kulcsokat, a titkokat, a titkosítási kulcsokat és a szabályzatokat a felhőben. Mivel Azure Key Vault bizalmas és üzleti szempontból kritikus fontosságú adatokat tárol, a kulcstartók és a bennük tárolt adatok maximális biztonsága szükséges.

Azure Security Center a veszélyforrások elleni védelem támogatása Azure Key Vault a biztonsági intelligencia egy további rétegét nyújtja, amely szokatlan és potenciálisan ártalmas kísérleteket észlel a kulcstartók eléréséhez vagy kihasználásához. Ez az új védelmi réteg lehetővé teszi az ügyfelek számára, hogy biztonsági szakértő nélkül kezeljék a kulcstárolókkal szembeni fenyegetéseket, és nem rendelkeznek biztonsági szakértőkkel. A funkció nyilvános előzetes verzióban érhető el Észak-Amerika régióban.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Az Azure Storage veszélyforrások elleni védelme magában foglalja a kártevők ismertségének szűrését

Az Azure Storage veszélyforrások elleni védelme olyan új észleléseket kínál, amelyek a Microsoft fenyegetésekkel kapcsolatos intelligenciával észlelik a kártevők feltöltését az Azure Storage-ba a kivonatok hírnevének elemzése és az aktív Tor kilépési csomópontról (egy anonimizálásával-proxy) gyanús hozzáférés Mostantól a Azure Security Center használatával megtekintheti az észlelt kártevőket a Storage-fiókok között.


### <a name="workflow-automation-with-logic-apps-preview"></a>Munkafolyamat-automatizálás Logic Appssal (előzetes verzió)

A központilag felügyelt biztonságot és IT-műveleteket végző szervezetek belső munkafolyamat-folyamatokat vezetnek be a szükséges műveletek elvégzéséhez a szervezeten belül, ha eltérések észlelhetők a környezetében. Sok esetben ezek a munkafolyamatok ismételhető folyamatok, az automatizálás pedig nagy mértékben egyszerűsítheti a szervezeten belüli folyamatokat.

Ma egy új képességet vezetünk be Security Center, amely lehetővé teszi, hogy az ügyfelek automatizálási konfigurációkat hozzanak létre Azure Logic Apps és olyan házirendeket hozzanak létre, amelyek automatikusan elindítják őket az adott ASC-megállapítások, például javaslatok vagy riasztások alapján. Az Azure Logic app beállítható úgy, hogy a logikai alkalmazás-összekötők hatalmas közössége által támogatott egyéni műveleteket hajtsa végre, vagy használja a Security Center által biztosított sablonok valamelyikét, például e-mailek küldését vagy ServiceNow™ jegy megnyitását.

A munkafolyamatok futtatásának automatikus és manuális Security Center funkcióival kapcsolatos további információkért lásd: [munkafolyamat-automatizálás](workflow-automation.md).

Logic Apps létrehozásával kapcsolatos további tudnivalókért lásd: [Azure Logic apps](../logic-apps/logic-apps-overview.md).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Általánosan elérhető a tömeges erőforrások gyors javítása

Számos feladattal, amelyet a felhasználók a biztonságos pontszám részeként adnak meg, nagy kihívást jelenthet a jelentős flották problémáinak hatékony elhárítása.

A biztonsági hibák elhárításának leegyszerűsítése és az erőforrások nagy mennyiségű és biztonságos pontszámának javítása érdekében gyorsan javíthatja az ajánlásokat.

Ezzel a művelettel kiválaszthatja azokat az erőforrásokat, amelyekre alkalmazni kívánja a szervizelést, és elindít egy szervizelési műveletet, amely az Ön nevében konfigurálja a beállítást.

A gyors javítás általánosan elérhető a Security Center javaslatok oldalának részeként.

Tekintse meg, hogy mely javaslatok esetében van engedélyezve a gyors javítás a útmutatóban a [biztonsági javaslatok eléréséhez](recommendations-reference.md).


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Biztonsági rések keresése a tárolók rendszerképein (előzetes verzió)

A Azure Security Center mostantól megvizsgálhatja a tároló lemezképeit Azure Container Registry a biztonsági rések érdekében.

A képvizsgálat a tároló képfájljának elemzésével működik, majd ellenőrzi, hogy vannak-e ismert sebezhetőségek (Qualys-alapú).

A vizsgálat automatikusan elindul, amikor az új tároló lemezképeit Azure Container Registryba küldi. A biztonsági rések felszíne Security Center javaslatként, és az Azure-os biztonsági pontszámban is szerepel, valamint arról, hogy miként lehet javítani őket a támadási felület méretének csökkentése érdekében.


### <a name="additional-regulatory-compliance-standards-preview"></a>További szabályozási megfelelőségi szabványok (előzetes verzió)

A megfelelőségi irányítópult Security Center értékelések alapján betekintést nyújt a megfelelőségi helyzetbe. Az irányítópult azt mutatja be, hogy a környezet megfelel-e az egyes szabályozási szabványok és iparági referenciaértékek által meghatározott szabályozásoknak és követelményeknek, és előírásos ajánlásokat biztosít a követelmények megoldásához.

A szabályozási megfelelőségi irányítópult eddig négy beépített szabványt támogat: az Azure CIS 1.1.0, a PCI-DSS, az ISO 27001 és a SOC-TSP. Most bejelentjük a további támogatott szabványok nyilvános előzetes kiadását: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Kanada szövetségi PBMM és az Egyesült Királyság hivatalos tagja, valamint az Egyesült Királysági NHS. Emellett az Azure CIS 1.1.0 frissített verzióját is kiadjuk, amely a standard és a bővíthetőség növelését is magában foglalja.

[További információ a szabványok készletének testreszabásáról a szabályozási megfelelőségi irányítópulton](update-regulatory-compliance-packages.md).


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Veszélyforrások elleni védelem az Azure Kubernetes szolgáltatásban (előzetes verzió)

A Kubernetes gyorsan megkezdi a szoftverek Felhőbeli üzembe helyezésének és felügyeletének új szabványát. A Kubernetes széles körű tapasztalattal rendelkeznek, és számos az általános mérnöki és adminisztrációs funkciókra koncentrál, és a biztonsági szempontokat is figyelembe kell venni. A Kubernetes-környezetet körültekintően kell konfigurálni, így biztos lehet abban, hogy a tárolók célzott támadási felülete nem marad nyitva a támadók számára. Security Center kibővíti a tároló területén található támogatását az Azure-beli Kubernetes szolgáltatás (ak) egyik leggyorsabb növekedési szolgáltatásával.

A nyilvános előzetes kiadás új funkciói a következők:

- **Felderítés & láthatóság** – a felügyelt AK-példányok folyamatos észlelése Security Center regisztrált előfizetései között.
- **Biztonságos pontszámra vonatkozó javaslatok** – a gyakorlatban használható elemek segítenek az ÜGYFELEKNEK az AK-ra vonatkozó ajánlott biztonsági eljárások betartásában, valamint a biztonságos pontszám növelésében. A javaslatok közé tartoznak például a "szerepköralapú hozzáférés-vezérlés használata a Kubernetes-fürthöz való hozzáférés korlátozásához".
- **Fenyegetések észlelése** – gazdagép-és fürt-alapú elemzések, például "a rendszerjogosultságú tároló észlelve".


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Virtuális gépek sebezhetőségének felmérése (előzetes verzió)

A virtuális gépekre telepített alkalmazások gyakran olyan biztonsági réseket tartalmazhatnak, amelyek a virtuális gép megszegéséhez vezethetnek. Bejelentjük, hogy a Security Center standard szintű csomag beépített sebezhetőségi felmérést tartalmaz a virtuális gépekhez, felár nélkül. A nyilvános előzetes verzióban a Qualys által biztosított sebezhetőségi felmérés lehetővé teszi, hogy folyamatosan megvizsgálja a virtuális gépen telepített összes alkalmazást, hogy megkeresse a sebezhető alkalmazásokat, és megmutassa az eredményeket a Security Center portál felületén. Security Center gondoskodik az összes üzembe helyezési műveletről, így a felhasználó nem igényel további munkát. A jövőben a sebezhetőségi felmérési lehetőségek megadását tervezzük az ügyfeleink egyedi üzleti igényeinek kielégítése érdekében.

[További információ az Azure-Virtual Machines sebezhetőségi felméréséről](deploy-vulnerability-assessment-vm.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Speciális adatbiztonság az Azure-beli SQL-kiszolgálókon Virtual Machines (előzetes verzió)

A IaaS-alapú virtuális gépeken futó SQL-adatbázisok biztonsági rések elleni védelme és a sebezhetőségi felmérés Azure Security Center támogatása már előzetes verzióban érhető el.

A [sebezhetőségi felmérés](../azure-sql/database/sql-vulnerability-assessment.md) egy könnyen konfigurálható szolgáltatás, amely képes felderíteni és követni az adatbázisok lehetséges biztonsági réseit, és segít orvosolni azokat. Az Azure biztonságos pontszámának részeként biztosítja a biztonsági helyzet láthatóságát, és tartalmazza a biztonsági problémák megoldásához és az adatbázis-erődítmények fejlesztéséhez szükséges lépéseket.

A komplex [veszélyforrások elleni védelem](../azure-sql/database/threat-detection-overview.md) olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket mutatnak az SQL Server eléréséhez vagy kiaknázásához. A szolgáltatás folyamatosan figyeli az adatbázist a gyanús tevékenységekhez, és a rendellenes adatbázis-hozzáférési mintákon biztosít műveleti alapú biztonsági riasztásokat. Ezek a riasztások biztosítják a gyanús tevékenység részleteit, valamint az ajánlott műveleteket a fenyegetés kivizsgálásához és enyhítéséhez.


### <a name="support-for-custom-policies-preview"></a>Egyéni szabályzatok támogatása (előzetes verzió)

A Azure Security Center mostantól támogatja az egyéni házirendeket (előzetes verzió).

Ügyfeleinknek a Azure Policy-ban létrehozott szabályzatok alapján a saját biztonsági értékeléseit Szeretnék kiterjeszteni a Security Centerra. Az egyéni szabályzatok támogatása mostantól lehetséges.

Ezek az új szabályzatok a Security Center javaslatok, a biztonságos pontszám és a szabályozási megfelelőségi szabványok irányítópultjának részei lesznek. Az egyéni szabályzatok támogatásával mostantól létrehozhat egy egyéni kezdeményezést Azure Policyban, majd hozzáadhatja a szabályzatként Security Center és megjelenítheti azt javaslatként.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Azure Security Center lefedettség kiterjesztése közösségi és partneri platformmal

A Security Center használatával nem csupán a Microsofttól, hanem a partnerek meglévő megoldásaitól, például az ellenőrzési ponttól, a tartástól és a CyberArk származó megoldásoktól is kaphat javaslatokat.  Security Center egyszerű bevezetési folyamata összekapcsolhatja meglévő megoldásait Security Center, így egyetlen helyen tekintheti meg a biztonsági helyzetekre vonatkozó javaslatokat, egységes jelentéseket futtathat, és az összes Security Center funkcióját kihasználhatja a beépített és a partneri javaslatokkal szemben. A partnerek termékeire Security Center javaslatokat is exportálhat.

[További információ a Microsoft intelligens biztonsági társításáról](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Speciális integrációk javaslatok és riasztások exportálásával (előzetes verzió)

A nagyvállalati szintű forgatókönyvek Security Centeron való engedélyezéséhez mostantól lehetőség van Security Center riasztások és javaslatok használatára a Azure Portal vagy API kivételével további helyeken. Ezek közvetlenül exportálhatók egy Event hubhoz és Log Analytics munkaterületekre. Íme néhány munkafolyamat, amely az új funkciók köré hozható létre:

- Az Log Analytics munkaterületre való exportálással egyéni irányítópultokat hozhat létre Power BI használatával.
- Az Event hub-ba való exportálással Security Center riasztásokat és ajánlásokat exportálhat a harmadik féltől származó SIEM-re, egy harmadik féltől származó megoldásra valós időben vagy az Azure Adatkezelő.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Helyszíni helyszíni kiszolgálók Security Center a Windows felügyeleti központból (előzetes verzió)

A Windows felügyeleti központ egy olyan Windows-kiszolgálókhoz készült felügyeleti portál, amely nem az Azure-ban van üzembe helyezve, és több Azure-felügyeleti funkciót is kínál, például biztonsági mentési és rendszerfrissítéseket. Mostanában lehetőség van arra, hogy a nem Azure-beli kiszolgálókat közvetlenül a Windows felügyeleti központ felhasználói felületéről lehessen védeni.

Ezzel az új felhasználói felülettel a WAC-kiszolgáló bekerül a Azure Security Centerba, és lehetővé teszi a biztonsági riasztások és javaslatok közvetlen megtekintését a Windows felügyeleti központ felhasználói felületén.


## <a name="september-2019"></a>2019. szeptember

A szeptemberben elérhető frissítések a következők:

 - [Szabályok kezelése adaptív alkalmazás-vezérlőkkel – tökéletesítések](#managing-rules-with-adaptive-application-controls-improvements)
 - [A tárolók biztonsági javaslatának szabályozása Azure Policy használatával](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Szabályok kezelése adaptív alkalmazás-vezérlőkkel – tökéletesítések

Továbbfejlesztettük a virtuális gépekre vonatkozó szabályok kezelésének élményét az adaptív alkalmazások vezérlőinek használatával. Azure Security Center adaptív alkalmazás-vezérlői segítségével szabályozhatja, hogy mely alkalmazások futhatnak a virtuális gépeken. A szabályok kezelésének általános fejlesztésén kívül az új előny lehetővé teszi annak szabályozását, hogy az új szabályok hozzáadásakor milyen fájltípusok lesznek védve.

[További információ az adaptív alkalmazások vezérlőinek használatáról](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>A tárolók biztonsági javaslatának szabályozása Azure Policy használatával

Azure Security Center a biztonsági rések szervizelése a tárolók biztonsága szolgáltatásban mostantól engedélyezhető vagy letiltható a Azure Policy használatával.

Az engedélyezett biztonsági szabályzatok megtekintéséhez Security Center nyissa meg a biztonsági házirend lapot.


## <a name="august-2019"></a>2019. augusztus

Az augusztusi frissítések a következők:

 - [Igény szerinti (JIT) VM-hozzáférés Azure Firewall](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Egyszeri kattintás szervizelése a biztonsági helyzet fokozása érdekében (előzetes verzió)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Bérlők közötti felügyelet](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Igény szerinti (JIT) VM-hozzáférés Azure Firewall 

Az igény szerinti (JIT) virtuálisgép-hozzáférés Azure Firewall már általánosan elérhető. Ezzel biztosíthatja Azure Firewall védett környezetek védelmét a NSG által védett környezetek mellett.

A JIT VM-hozzáférés csökkenti a hálózati sávszélesség-alapú támadásoknak való kitettséget azáltal, hogy az NSG és a Azure Firewall szabályok segítségével csak szükség esetén felügyelt hozzáférést biztosít a virtuális gépekhez.

Ha engedélyezi a JIT-t a virtuális gépek számára, létre kell hoznia egy házirendet, amely meghatározza a védeni kívánt portokat, valamint azt, hogy mennyi ideig legyenek nyitva a portok, és hogy mely IP-címek érhetők el. Ez a szabályzat segít megtekinteni, hogy mit tehetnek a felhasználók, amikor hozzáférést igényelnek.

A rendszer naplózza a kérelmeket az Azure-tevékenység naplójában, így egyszerűen figyelheti és naplózhatja a hozzáférést. Az igény szerinti oldal segítségével gyorsan azonosíthatja azokat a meglévő virtuális gépeket, amelyeken a JIT engedélyezve van, és a virtuális gépek, amelyeken a JIT ajánlott.

[További információ a Azure Firewallról](../firewall/overview.md).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Egyszeri kattintás szervizelése a biztonsági helyzet fokozása érdekében (előzetes verzió)

A biztonságos pontszám egy olyan eszköz, amely segít felmérni a munkaterhelés biztonsági helyzetét. Megtekinti a biztonsági javaslatokat, és rangsorolja őket az Ön számára, így biztos lehet benne, hogy mely ajánlásokat kell elvégeznie. Ez segít megtalálni a legsúlyosabb biztonsági réseket a vizsgálat rangsorolása érdekében.

A biztonsági hibák elhárításának leegyszerűsítése és a biztonságos pontszám gyors javítása érdekében új képességgel bővült, amely lehetővé teszi, hogy egy kattintással több erőforrásra vonatkozó ajánlást javítsanak ki.

Ezzel a művelettel kiválaszthatja azokat az erőforrásokat, amelyekre alkalmazni kívánja a szervizelést, és elindít egy szervizelési műveletet, amely az Ön nevében konfigurálja a beállítást.

Tekintse meg, hogy mely javaslatok esetében van engedélyezve a gyors javítás a útmutatóban a [biztonsági javaslatok eléréséhez](recommendations-reference.md).


### <a name="cross-tenant-management"></a>Bérlők közötti felügyelet

A Security Center mostantól támogatja a több-bérlős felügyeleti forgatókönyveket az Azure Lighthouse részeként. Ez lehetővé teszi, hogy átláthatóbb és felügyelje Security Center több bérlő biztonsági állapotát. 

[További információ a több-bérlős felügyeleti](security-center-cross-tenant-management.md)környezetekről.


## <a name="july-2019"></a>2019. július

### <a name="updates-to-network-recommendations"></a>Hálózati javaslatok frissítései

A Azure Security Center (ASC) új hálózatkezelési ajánlásokat indított, és bizonyos meglévőket fejlesztett ki. Mostantól a Security Center használatával még nagyobb hálózati védelmet biztosíthat az erőforrásai számára. 

[További információ a hálózati javaslatokról](recommendations-reference.md#recs-network).


## <a name="june-2019"></a>2019. június

### <a name="adaptive-network-hardening---generally-available"></a>Adaptív hálózat megerősítése – általánosan elérhető

A nyilvános felhőben futó számítási feladatok egyik legnagyobb támadási felülete a nyilvános internetről érkező és a felé irányuló kapcsolat. Ügyfeleink nehezen tudják, hogy mely hálózati biztonsági csoportokra (NSG) vonatkozó szabályokra van szükség ahhoz, hogy az Azure-beli munkaterhelések csak a szükséges forrástartomány számára legyenek elérhetők. Ezzel a szolgáltatással a Security Center megismerheti az Azure-beli számítási feladatok hálózati forgalmát és kapcsolati mintáit, és NSG-szabályokra vonatkozó ajánlásokat biztosít az internet felé irányuló virtuális gépekhez. Így az ügyfél jobban konfigurálhatja a hálózati hozzáférési házirendeket, és korlátozhatja a támadásoknak való kitettséget. 

[További információ az adaptív hálózat megerősítéséről](security-center-adaptive-network-hardening.md).