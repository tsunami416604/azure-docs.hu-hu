---
title: Az Azure Security Center – gyakori kérdések (GYIK) |} A Microsoft Docs
description: Ez a GYIK az Azure Security Centerrel kapcsolatos kérdésekre ad választ.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2019
ms.author: monhaber
ms.openlocfilehash: ad676070bb684e459c0dae648443318199f77b6d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58091528"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Azure Security Center – gyakori kérdések
Ez a GYIK az Azure Security Center egy szolgáltatás, amellyel megelőzését, észlelését és háríthatja el a fenyegetéseket átláthatóbbá és a Microsoft Azure-erőforrások kézben kapcsolatos kérdésekre ad választ.

> [!NOTE]
> 2017 júniusának elejétől kezdve a Security Center a Microsoft Monitoring Agent használatával gyűjti össze és tárolja az adatokat. További tudnivalókért lásd: [Azure Security Center Platform Migrálása](security-center-platform-migration.md). A jelen cikkben található információk a Security Center a Microsoft Monitoring Agentre való váltás után elérhető funkcióit ismertetik.
>
>

## <a name="general-questions"></a>Általános kérdések
### <a name="what-is-azure-security-center"></a>Mi az az Azure Security Center?
Az Azure Security Center magasabb fokú betekintést és szélesebb körű vezérlést tesz lehetővé az Azure-erőforrások fölött, így segít a fenyegetések megelőzésében, észlelésében és kezelésében. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

### <a name="how-do-i-get-azure-security-center"></a>Hogyan szerezhetem be az Azure Security Center?
Az Azure Security Center a Microsoft Azure-előfizetéshez engedélyezve van, és elérhető a [az Azure portal](https://azure.microsoft.com/features/azure-portal/). ([Jelentkezzen be a portálra](https://portal.azure.com), jelölje be **Tallózás**, és görgessen a **a Security Center**).  

## <a name="billing"></a>Számlázás
### <a name="how-does-billing-work-for-azure-security-center"></a>Hogyan történik az elszámolás az Azure Security Center?
A Security Center két szinten érhető el:

A **ingyenes szint** biztonsági állapotát az Azure-erőforrások, az alapszintű biztonsági házirend, a biztonsági javaslatok és a integrációs rálátást biztosít a biztonsági termékeket és szolgáltatásokat partnereinktől.

A **Standard szintű** hozzáadja a komplex veszélyforrások elleni képességeket tartalmaz, beleértve a fenyegetésészlelési intelligencia, a viselkedés-elemzés, a rendellenességek észlelése, a biztonsági incidensek és tesznek elérhetővé; ilyenek jelentések fenyegetés. Standard szint ingyenes próba megkezdése. Szeretné frissíteni, válassza ki a [Tarifacsomag](https://docs.microsoft.com/azure/security-center/security-center-pricing) a biztonsági szabályzatban. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="permissions"></a>Engedélyek
Az Azure Security Center [szerepköralapú hozzáférés-vezérlést (RBAC)](../role-based-access-control/role-assignments-portal.md) használ, amelynek [beépített szerepköreit](../role-based-access-control/built-in-roles.md) az Azure különböző csoportjaihoz, felhasználóihoz és szolgáltatásaihoz rendelheti.

A Security Center a biztonsági problémák és biztonsági rések azonosításához erőforrásán konfiguráció értékeli. A Security Centerben csak láthatja az előfizetés vagy az erőforráscsoport, amely egy erőforrás tartozik, a tulajdonos, közreműködő vagy olvasó a szerepkör hozzárendelése esetén egy erőforráshoz kapcsolódó információkat.

Lásd: [engedélyek az Azure Security Centerben](security-center-permissions.md) tudhat meg többet a szerepkörök és a Security Centerben engedélyezett műveletek.

## <a name="data-collection-agents-and-workspaces"></a>Adatgyűjtés, az ügynökök és a-munkaterületek
A Security Center adatokat gyűjt az Azure-beli virtuális gépek (VM) és a nem Azure-beli számítógépekről a biztonsági rések és fenyegetések monitorozásához. Az adatgyűjtés a Microsoft Monitoring Agent segítségével történik, amely a biztonsághoz kapcsolódó különböző konfigurációkat és eseménynaplókat olvas be a gépről, és elemzés céljából átmásolja az adatokat az Ön munkaterületére.

### <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Kell fizetnem az Azure Monitor-naplók a Security Center által létrehozott munkaterületek a?
Nem. Security Center által létrehozott munkaterületek amíg konfigurálása az Azure Monitor-naplók csomópont számlázás, nem terheli Azure Monitor naplók. A Security Center minden esetben elszámolt díj attól a Security Center biztonsági házirend és a megoldások a munkaterülethez telepítve:

- **Ingyenes szint** – Security Center lehetővé teszi, hogy a "SecurityCenterFree" megoldást az alapértelmezett munkaterületre. Az ingyenes szint nem számlázzuk ki.
- **Standard szintű** – Security Center lehetővé teszi, hogy a "Security" megoldást az alapértelmezett munkaterületre.

További információ a díjszabásról lásd: [a Security Center díjszabási](https://azure.microsoft.com/pricing/details/security-center/). A díjszabási lapon biztonsági adattárolásban és az időarányos számlázásban 2017 júniusától módosításai megoldást.

> [!NOTE]
> A Security Center által létrehozott munkaterület tarifacsomagját a log analytics nincs hatással a Security Center a számlázás.
>
>

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>A Microsoft Monitoring Agent telepítése automatikus kiépítése milyen jogosult egy virtuális Gépet?
Windows vagy Linux rendszerű IaaS virtuális gépek jogosultak, ha:

- A Microsoft Monitoring Agent bővítményt jelenleg nem települ a virtuális gépen.
- A virtuális gép futó állapotban van.
- A Windows vagy Linux rendszerű Virtuálisgép-ügynök telepítve van.
- A virtuális gép nem használatos, például a webalkalmazási tűzfal vagy új generációs tűzfal telepíthetőek.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>A Security Center által létrehozott alapértelmezett munkaterületet is törli?
**Az alapértelmezett munkaterület törlése nem ajánlott.** A Security Center az alapértelmezett munkaterület használ a virtuális gépek biztonsági adatainak tárolásához.  Ha töröl egy munkaterületet, a Security Center nem tudja gyűjteni az adatokat, és egyes biztonsági javaslatok és riasztások nem érhetők el.

Szeretné használni, távolítsa el a Microsoft Monitoring Agentet a virtuális gépeken, a törölt munkaterülethez csatlakozik. A Security Center újra telepíti az ügynököt, és létrehozza az új alapértelmezett munkaterületek.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Hogyan használhatom a meglévő Log Analytics-munkaterületet?

Kiválaszthat egy meglévő Log Analytics-munkaterületet a Security Center által gyűjtött adatok tárolásához. A meglévő Log Analytics-munkaterület használata:

- A munkaterület a kiválasztott Azure-előfizetése társítva kell lennie.
- Minimális rendelkeznie kell olvasási engedéllyel a munkaterület eléréséhez.

Egy meglévő Log Analytics-munkaterület kiválasztása:

1. A **biztonsági szabályzat – adatgyűjtés**válassza **másik munkaterület használata**.

   ![Másik munkaterület használata][5]

2. A legördülő menüből válassza ki a munkaterületet a gyűjtött adatok tárolásához.

   > [!NOTE]
   > A lekéréses menüre, az csak azok a munkaterületek, amely hozzáféréssel rendelkezik, és az Azure-előfizetésében vannak jelennek meg.
   >
   >

3. Kattintson a **Mentés** gombra.
4. Miután **mentése**, meg kell adnia, ha szeretné újrakonfigurálja a figyelt virtuális gépek.

   - Válassza ki **nem** Ha azt szeretné, hogy az új munkaterület beállítások **csak új virtuális gépeket a alkalmazni**. Az új munkaterület csak vonatkoznak új ügynökök telepítése; az újonnan felderített virtuális gépek, amelyeken nincs telepítve a Microsoft Monitoring Agent telepítve van.
   - Válassza ki **Igen** Ha azt szeretné, hogy az új munkaterület beállítások **minden virtuális gép a alkalmazni**. Emellett egy munkaterület létrehozása a Security Center minden virtuális Gépről az új cél munkaterületet újra csatlakoztatni.

   > [!NOTE]
   > Ha az Igen lehetőséget választja, nem kell törölnie a mindaddig, amíg az összes virtuális gép újra lett csatlakozott az új cél munkaterületet, a Security Center által létrehozott munkaterület(ek). Ez a művelet sikertelen lesz, ha a munkaterület túl korán törlődik.
   >
   >

   - Válassza ki **Mégse** megszakítja a műveletet.

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>Mi történik, ha a Microsoft Monitoring Agent már telepítve van a virtuális gép bővítményeként?
A Security Center nem bírálja felül a meglévő kapcsolatok felhasználó munkaterületeket. A Security Center a munkaterülethez már csatlakoztatott tárolja a virtuális gép biztonsági adatait. A Security Center a bővítmény verziója tartalmazza a virtuális Gépet, a Security Center használatának támogatása az Azure erőforrás-Azonosítójára frissíti.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>Mi történik, ha korábban a Microsoft Monitoring Agent telepítve van a gépen, de nem bővítményeként?
A Microsoft Monitoring Agent közvetlenül a virtuális gép (nem pedig egy Azure-bővítmény) van telepítve, ha a Security Center nem telepíti a Microsoft Monitoring Agent, és a biztonság monitorozása korlátozva.

További információkért tekintse meg a következő szakaszban [mi történik, ha egy SCOM vagy OMS közvetlen ügynök már telepítve van a virtuális gépemen?](#scomomsinstalled)

### Mi történik, ha egy SCOM vagy OMS közvetlen ügynök már telepítve van a virtuális gépemen?<a name="scomomsinstalled"></a>
A Security Center nem tudja azonosítani az előzetesen, hogy az ügynök telepítve van-e.  A Security Center a Microsoft Monitoring Agent bővítményt telepíteni, és a meglévő telepített ügynök miatt meghiúsul.  Ez a hiba megakadályozza, hogy az ügynök kapcsolati beállításokat a munkaterület felülbírálása, és többkiszolgálós létrehozása.

> [!NOTE]
> Az ügynök verzióra frissült az OMS-ügynök legfrissebb verzióját.  Ez vonatkozik az SCOM-felhasználók is.
>
>

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Mit jelent az ilyen bővítmények eltávolításának következményei?
Ha eltávolítja a Microsoft Monitoring bővítményt, a Security Center nem tud a biztonsági adatok gyűjtésére a virtuális gép és az egyes biztonsági javaslatok és riasztások nem érhetők el. 24 órán belül a Security Center meghatározza, hogy a virtuális gép a bővítmény hiányzik, és újratelepíti a bővítményt.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Hogyan akadályozható meg az ügynök automatikus telepítés és a munkaterület létrehozását?
Automatikus üzembe helyezés a biztonsági szabályzat az előfizetések ki is kapcsolhatja, de ez nem ajánlott. Automatikus üzembe helyezési korlátozások Security Center javaslatait és riasztások kikapcsolásával. Az Automatikus kiépítés letiltása:

1. Ha a Standard szintű előfizetése van konfigurálva, nyissa meg a biztonsági szabályzatot az adott előfizetéshez tartozó, és válassza ki a **ingyenes** szint.

   ![Tarifacsomag][1]

2. Ezután kapcsolja ki az Automatikus kiépítés kiválasztásával **ki** a a **biztonsági szabályzat – adatgyűjtés** panelen.
   ![Adatgyűjtés][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Meg tudom tilthatják le az automatikus ügynöktelepítés és munkaterület létrehozása?

> [!NOTE]
> Feltétlenül olvassa el a szakaszok [engedélyezés következményei Mik?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) és [javasolt lépések, ha engedélyezés](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) Ha úgy dönt, hogy tilthatják le az automatikus kiépítést.
>
>

Előfordulhat, hogy szeretné tilthatják le az automatikus kiépítést, ha Ön számára az alábbiak érvényesek:

- A teljes előfizetés a Security Center által automatikus ügynöktelepítés vonatkozik.  Virtuális gépek egy része nem alkalmazhat automatikus telepítését. Ha nincsenek kritikus fontosságú virtuális gépek, amelyek a Microsoft Monitoring Agent nem telepíthető, majd kell kikapcsolja az Automatikus kiépítés.
- A Microsoft Monitoring Agent bővítmény telepítése az ügynök verzióját frissíti. Ez vonatkozik a közvetlen ügynök és a egy SCOM-ügynök. Ha a telepített SCOM-ügynökök 2012 verzió, és frissítve van, a kezelhetőségi képességeit elveszhet, esetén az SCOM-kiszolgálóval is 2012 verzióra. Fontolja meg az Automatikus kiépítés, ha a telepített SCOM-ügynökök 2012 verzió megtagadja.
- Ha egy egyéni munkaterület ahhoz az előfizetéshez (egy központosított munkaterületű) külső majd meg kell tilthatják le az automatikus kiépítést. Manuálisan telepítse a Microsoft Monitoring Agent bővítményt, és csatlakoztathatja azt a munkaterületet anélkül, hogy a Security Center a kapcsolat felülírása.
- Ha el szeretné kerülni előfizetésenként több munkaterülettel létrehozását, és az előfizetésen belül a saját egyéni munkaterületet, majd, két lehetősége van:

   1. Az Automatikus kiépítés kívül kérheti. Az áttelepítést követően állítsa be az alapértelmezett munkaterület beállításainak leírtak szerint [hogyan használható a meglévő Log Analytics-munkaterületet?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Vagy engedélyezheti, hogy az áttelepítés befejezéséhez, a Microsoft Monitoring Agentet a virtuális gépeket kell telepíteni, és a létrehozott munkaterülethez csatlakozik a virtuális gépeket. Ezután válassza ki a saját egyéni munkaterület, ha az alapértelmezett munkaterület beállítása a kiválasztott újrakonfigurálása a már telepített ügynökök. További információkért lásd: [hogyan használható a meglévő Log Analytics-munkaterületet?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Mik azok az Automatikus kiépítés megtagadja következményei?
Az áttelepítés után a Security Center nem tud a biztonsági adatok gyűjtésére a virtuális gép és az egyes biztonsági javaslatok és riasztások nem érhetők el. Ha kikapcsolja ezt a beállítást, a Microsoft Monitoring Agent manuálisan kell telepítenie. Lásd: [javasolt lépések, ha engedélyezés](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Mik azok a javasolt lépéseket, amikor az Automatikus kiépítés megtagadja?

A Microsoft Monitoring Agent bővítményt manuálisan kell telepítenie, így a Security Center biztonsági adatok gyűjtésére a virtuális gépeket, és adja meg a javaslatok és riasztások. Lásd: [Windows VM-ügynök telepítését](../virtual-machines/extensions/oms-windows.md) vagy [Linux rendszerű virtuális gép-ügynök telepítését](../virtual-machines/extensions/oms-linux.md) telepítési útmutatást.

Az ügynök csatlakozhat bármely meglévő egyéni munkaterületet, vagy a Security Center munkaterület létrehozása. Ha egy egyéni munkaterület nincs engedélyezve van, a "Security" vagy "SecurityCenterFree" megoldások, és a egy megoldást a alkalmazni kell. A alkalmazni, válassza ki az egyéni munkaterületet, vagy az előfizetés, és a tarifacsomag-n keresztül alkalmazza a **biztonsági szabályzat – tarifacsomag** panelen.

   ![Tarifacsomag][1]

A Security Center lehetővé teszi a munkaterületen a kijelölt tarifacsomag alapján a megfelelő megoldást.

### Hogyan távolíthatom el a Security Center által telepített OMS-bővítmények?<a name="remove-oms"></a>
Manuálisan távolítsa el a Microsoft Monitoring Agent. Ez nem ajánlott, mivel a Security Center javaslatait és riasztások korlátozza.

> [!NOTE]
> Ha az adatgyűjtés engedélyezve van, a Security Center újratelepíti az ügynök után távolítsa el azt.  Le kell tiltania az adatgyűjtés az ügynök manuális eltávolítása előtt. Tekintse meg, hogyan állítsa le az ügynök automatikus telepítés és a munkaterület létrehozását? útmutatás az adatok gyűjtésének letiltása.
>
>

Az ügynök manuális eltávolításához:

1. Nyissa meg a portálon **Log Analytics**.
2. A Log Analytics panelen válasszon ki egy munkaterületet:
3. Válasszon ki minden egyes virtuális Gépet, amelyet szeretne figyelni, és válassza ki **Disconnect**.

   ![Az ügynök eltávolítása][3]

> [!NOTE]
> Ha Linux rendszerű virtuális gép már nem bővítmény OMS-ügynök, a bővítmény eltávolítása, valamint az ügynök és az ügyfél rendelkezik, telepítse újra.
> 
> 
> ### <a name="how-do-i-disable-data-collection"></a>Hogyan tilthatom le az adatgyűjtést?
> Alapértelmezés szerint az Automatikus kiépítés le van. Letilthatja az Automatikus kiépítés erőforrásokból bármikor ezt a beállítást, a biztonsági szabályzatban kikapcsolásával. Az Automatikus kiépítés erősen ajánlott biztonsági riasztások és javaslatok Rendszerfrissítések, az operációs rendszer biztonsági rések és az endpoint protection információk lekérése érdekében.

Tiltsa le az adatgyűjtést, hogy [jelentkezzen be az Azure Portalon](https://portal.azure.com), jelölje be **Tallózás**, jelölje be **a Security Center**, válassza ki **válassza ki a szabályzat**. Válassza ki azt az előfizetést, amelynél le szeretné tiltani az automatikus kiépítést. Amikor kiválaszt egy előfizetés **biztonsági szabályzat – adatgyűjtés** nyílik meg. A **automatikus kiépítés**válassza **ki**.

### <a name="how-do-i-enable-data-collection"></a>Hogyan engedélyezhetem az adatgyűjtés?
Engedélyezheti az adatgyűjtést az Azure-előfizetés biztonsági szabályzatában. Az adatgyűjtés engedélyezéséhez. [Jelentkezzen be az Azure Portalon](https://portal.azure.com), jelölje be **Tallózás**, jelölje be **a Security Center**, és válassza ki **biztonsági házirend**. Automatikus kiépítés engedélyezése kívánt előfizetés kiválasztásához. Amikor kiválaszt egy előfizetés **biztonsági szabályzat – adatgyűjtés** nyílik meg. A **automatikus kiépítés**válassza **a**.

### <a name="what-happens-when-data-collection-is-enabled"></a>Mi történik, ha az adatgyűjtés engedélyezve van?
Ha az Automatikus kiépítés engedélyezve van, a Security Center létrehozza a Microsoft Monitoring Agentet az összes támogatott Azure-beli és újonnan létrehozott. Az Automatikus kiépítés használata erősen ajánlott, de a manuális ügynöktelepítések is rendelkezésre áll. [Ismerje meg, hogyan telepítse a Microsoft Monitoring Agent bővítményt](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Az ügynök lehetővé teszi, hogy a folyamat-létrehozási esemény 4688 és a *CommandLine* mezőt 4688-as belül. A virtuális gépen új folyamatok EventLog által rögzített és a Security Center észlelési szolgáltatások által figyelt. Információk a rögzített minden új folyamathoz részleteire: [4688 leírás mezőiben](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Az ügynök is a virtuális gépen 4688 eseményeit, és tárolja a keresés.

Az ügynök emellett lehetővé teszi adatok gyűjtésének [adaptív Alkalmazásvezérlők](security-center-adaptive-application.md), konfigurálja a helyi AppLocker-házirend rendszervizsgálati módban, hogy az összes alkalmazást. Ennek hatására az AppLocker események, amelyeket gyűjteni, és a Security Center által használható létrehozása. Fontos megjegyezni, hogy ez a szabályzat minden olyan gépeken, amelyeken már van egy konfigurált AppLocker-házirendek nem lesznek konfigurálva. 

Ha a Security Center a virtuális gép gyanús tevékenységet észlel, az ügyfél értesítést e-mailben, ha a [biztonsági kapcsolattartási adatok](security-center-provide-security-contact-details.md) lett megadva. Egy riasztás akkor is a Security Center biztonsági riasztások irányítópult látható.



### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>A Monitoring Agent befolyásolja a kiszolgáló teljesítményét?
Az ügynök egy névleges mennyiségű rendszer-erőforrásokat használ fel, és teljesítményére gyakorolt minimális hatás kell rendelkeznie. További információ a teljesítményt és az ügynök és a bővítmény: az [tervezéséhez és útmutató](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Hol tárolják az adataimat?
Ettől az ügynöktől gyűjtött adatok tárolva van vagy egy meglévő Log Analytics-munkaterületet az Ön előfizetéséhez rendelve, vagy egy új munkaterületet. További információkért lásd: [adatbiztonság](security-center-data-security.md).

## Meglévő Azure Monitor-naplók ügyfelek<a name="existingloganalyticscust"></a>

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>A Security Center nem bírálja felül bármely meglévő kapcsolatokat a virtuális gépek és a munkaterületek között?
Ha egy virtuális gép már a Microsoft Monitoring Agent telepítve van az Azure kiterjesztése, a Security Center nem bírálja felül a meglévő munkaterület-kapcsolatot. Ehelyett a Security Center a meglévő munkaterületet használja.

A Security Center megoldás telepítve van a munkaterülethez, ha nincs már, és a megoldás csak a megfelelő virtuális gépek lesz alkalmazva. A megoldás hozzáadásakor az automatikusan telepített összes Windows és Linux-ügynökök az a Log Analytics-munkaterülethez kapcsolódó alapértelmezés szerint a. [Megoldás célcsoportkezelés](../operations-management-suite/operations-management-suite-solution-targeting.md) lehetővé teszi, hogy a alkalmazni egy hatókört a megoldások.

A Microsoft Monitoring Agent közvetlenül a virtuális gép (nem pedig egy Azure-bővítmény) van telepítve, ha a Security Center nem telepíti a Microsoft Monitoring Agent, és a biztonság monitorozása korlátozva.

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>A Security Center telepítési megoldások a saját meglévő Log Analytics-munkaterület? Mik azok a számlázását?
A Security Center azonosítja, hogy egy virtuális Gépet már csatlakoztatva van egy munkaterületet hozott létre, ha a Security Center lehetővé teszi ezen a munkaterületen, a tarifacsomag szerint megoldásokat. A megoldás csak a megfelelő Azure virtuális gépek érvényesek keresztül [megoldás célcsoportjának](../operations-management-suite/operations-management-suite-solution-targeting.md), így a számlázás változatlan marad.

- **Ingyenes szint** – a Security Center telepíti a "SecurityCenterFree" megoldás a munkaterületen. Az ingyenes szint nem számlázzuk ki.
- **Standard szintű** – a Security Center telepíti a "Security" megoldás a munkaterületen.

   ![Alapértelmezett munkaterület-megoldások][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Már rendelkezem munkaterületek saját környezetben, használható őket használatával gyűjt biztonsági adatokat?
Ha egy virtuális gép már a Microsoft Monitoring Agent telepítve van az Azure kiterjesztése, a Security Center a meglévő csatlakoztatott munkaterület használ. A Security Center megoldás telepítve van a munkaterülethez, ha nincs már, és a megoldás csak a releváns virtuális gépekhez érvényes [megoldás célcsoportjának](../operations-management-suite/operations-management-suite-solution-targeting.md).

A Security Center telepíti a Microsoft Monitoring Agentet a virtuális gépekre, használja a Security Center által létrehozott alapértelmezett munkaterületeire.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Már van biztonsági megoldást a saját munkaterület. Mik azok a számlázását?
A biztonsági és naplózási megoldás a Security Center Standard szintű funkciók engedélyezéséhez az Azure virtuális gépek szolgál. Ha a biztonsági és auditálási megoldás már telepítve van a munkaterülethez, a Security Center a meglévő megoldást használ. Nem történik változás a számlázás.

## <a name="using-azure-security-center"></a>Az Azure Security Center használatával
### <a name="what-is-a-security-policy"></a>Mi az a biztonsági szabályzatot?
Biztonsági szabályzat határozza meg, az adott előfizetésen belüli erőforrások ajánlott szabályozza. Az Azure Security Centerben szabályzatokat az Azure-előfizetést a vállalat biztonsági elvárásainak és az alkalmazások típusához vagy az egyes előfizetések adatainak érzékenysége tartozó meghatározása.

Az Azure Security Center meghajtó biztonsági javaslatok és a figyelés az engedélyezett biztonsági szabályzatok. Biztonsági szabályzatok kapcsolatos további információkért lásd: [biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Ki módosíthatja a biztonsági szabályzatot?
Biztonsági szabályzat módosításához a rendszergazda vagy a tulajdonosának vagy Közreműködőjének előfizetésben kell lennie.

Biztonsági szabályzat konfigurálásával kapcsolatban lásd: [biztonsági szabályzatok beállítása az Azure Security Center](tutorial-security-policy.md).

### <a name="what-is-a-security-recommendation"></a>Mi az a biztonsági javaslatok?
Az Azure Security Center elemzi az Azure-erőforrások biztonsági állapotát. Ha az azonosított potenciális biztonsági réseket, javaslatok jönnek létre. A javaslatok végigvezetik a szükséges vezérlőt konfigurálásának folyamatán. Példák:

* Kiépítés kártevőirtó szoftverek azonosításához és kártevő szoftverek eltávolítása
* [Hálózati biztonsági csoportok](../virtual-network/security-overview.md) és a virtuális gépek forgalmának ellenőrzésére vonatkozó szabályok
* A webalkalmazások célzó támadások elleni segítségével webalkalmazási tűzfal kiépítése
* Hiányzó rendszerfrissítések telepítése
* Az operációs rendszer azon konfigurációinak kezelése, amelyek nem felelnek meg a javasolt alapkonfigurációknak

Csak azok a javaslatok, amelyek engedélyezve vannak a biztonsági szabályzatok Itt jelennek meg.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Hogyan tekinthetem meg a saját Azure-erőforrások aktuális biztonsági állapotát?
A **Security Center – áttekintés** panelen jelenik meg a számítási, hálózati, tárolási és adatok és alkalmazások szerinti bontásban környezet általános biztonsági állapotát. Egyes erőforrástípusok-kijelző ábrázoló van, ha a potenciális biztonsági hiányosságok azonosított. Minden egyes csempére kattintva azonosítja az erőforrást az előfizetésében leltára együtt a Security Center, biztonsági problémák listáját jeleníti meg.

### <a name="what-triggers-a-security-alert"></a>Mi a biztonsági riasztást aktivál?
Az Azure Security Center automatikusan gyűjti, elemzi és biztosítók az Azure-erőforrások, a hálózati és a partneri megoldások, például kártevőirtó- és tűzfalak naplóadatait. Fenyegetések észlelése esetén a központ biztonsági riasztást hoz létre. Példák fenyegetés észlelésére:

* Feltört virtuális gépek, amelyek kártékonyként azonosított IP-címekkel kommunikálnak
* Használatával a Windows hibajelentés észlelt speciális kártevő
* Virtuális gépek elleni, a teljes kipróbálás módszerén alapuló támadások
* Integrált partneri biztonsági megoldások, például a kártevő- vagy webalkalmazás-tűzfalak biztonsági riasztásai

### Miért volt biztonságos pontszámok módosítása? <a name="secure-score-faq"></a>
A Security Center kezdődően a 2019. február beállítani, néhány javaslatokat, annak érdekében, hogy jobban illeszkedjen a súlyosságuk a pontszámot. Ezt a beállítást, miatt előfordulhat, átfogó módosítások biztonságos pontszám értékeket.  Biztonságos pontszám kapcsolatos további információkért lásd: [biztonságos pontszám számítási](security-center-secure-score.md).

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Mi a különbség a fenyegetéseket észlelt, és kapni a Microsoft Security Response Center és az Azure Security Center által?
A Microsoft Security Response Center (MSRC) hajt végre, válassza ki a biztonsági figyelése az Azure-hálózat és az infrastruktúra, és harmadik felektől származó threat intelligence és visszaélések panaszok kap. MSRC tudomására jut, hogy vásárlói adatokat egy jogosulatlan vagy illetéktelen fél hozzáfért-e, illetve, hogy az Azure használatát az ügyfél nem felel meg a feltételeket az elfogadható használja, amikor egy biztonsági incidens manager értesíti a. Értesítés általában akkor fordul elő, a biztonsági felelősök kapcsolati adatait meg az Azure Security Center vagy az Azure-előfizetés tulajdonosa, ha nincs megadva a biztonsági kapcsolattartó e-mail elküldésével.

A Security Center egy Azure-szolgáltatás, amely folyamatosan figyeli az ügyfél Azure-környezetben, és automatikusan észlelni a potenciálisan kártékony tevékenységek számos elemzési vonatkozik. Ezek az észlelések illesztett biztonsági riasztásai a Security Center irányítópultján.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Melyik Azure-erőforrásokat az Azure Security Center által figyelt?
Az Azure Security Center figyeli a következő Azure-erőforrások:

* Virtuális gépek (VM) (beleértve a [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Azure virtuális hálózatok
* Azure SQL service
* Azure Storage-fiók
* Az Azure Web Apps (a [App Service Environment-környezet](../app-service/environment/intro.md))
* Például a webalkalmazási tűzfal a virtuális gépek és az App Service Environment az Azure-előfizetésében integrált partnermegoldások

## <a name="virtual-machines"></a>Virtuális gépek
### <a name="what-types-of-virtual-machines-are-supported"></a>Milyen típusú virtuális gépek támogatottak?
Figyelés és javaslatok érhetők el a virtuális gépek (VM) használatával is létrehozott a [klasszikus és Resource Manager üzembe helyezési modellek](../azure-classic-rm.md).

Lásd: [az Azure Security Center által támogatott platformok](security-center-os-coverage.md) által támogatott platformok listáját.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Miért nem az Azure Security Center ismeri fel a kártevőirtó megoldást, az Azure virtuális gépen?
Az Azure Security Center az Azure-bővítmények segítségével telepítve kártevőirtó láthassa. Például nem sikerül, amely előre telepítve van a megadott képet, vagy ha a virtuális gépeken a saját folyamatok (például konfigurációs rendszerek) segítségével telepítve kártevőirtó kártevőirtó észleli a Security Center.

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Miért jelenik meg az üzenet "Hiányzó vizsgálati adatok" virtuális gép?
Ez az üzenet jelenik meg, ha nem szerepel megjeleníthető vizsgálat adat egy virtuális gép. Ez eltarthat egy ideig (kevesebb mint egy óra) feltöltése után az adatgyűjtés engedélyezése az Azure Security Center vizsgálati adatok számára. Vizsgálati adatok kezdeti sokasága, miután ezt az üzenetet, mert egyáltalán nem tartoznak vizsgálat adatok, vagy nem szerepel megjeleníthető legutóbbi vizsgálat adat jelenhet meg. Vizsgálatok nem adja meg a virtuális gép leállított állapotban. Ez az üzenet volt is megjelenhet, ha vizsgálati adatok nem nemrég (a megtartási házirend a Windows-ügynök, amely rendelkezik az alapértelmezett érték 30 nap) megfelelően van feltöltve.

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>A Security Center milyen gyakran nem vizsgálata az operációs rendszer biztonsági réseivel, a rendszerfrissítésekkel és a végpontvédelem?
A késés, a Security Center átvizsgálja a biztonsági réseket, frissítések, és problémák:

- Operációs rendszer biztonsági konfigurációk – adatok 48 órán belül frissült
- Rendszerfrissítések – adatok 24 órán belül frissült
- Az Endpoint Protection kapcsolatos problémák – 8 órán belül adatainak frissítése

A Security Center általában óránként keres új adatokat, és ennek megfelelően frissíti a javaslatokat. 

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Miért jelenik meg az üzenet "Virtuálisgép-ügynök hiányzik?"
Virtuális gépek az adatgyűjtés engedélyezése a Virtuálisgép-ügynököt kell telepíteni. Az Azure Marketplace-ről üzembe helyezett virtuális gépek esetében a virtuálisgép-ügynök alapértelmezés szerint telepítve van. Más virtuális gépeken a Virtuálisgép-ügynök telepítésével kapcsolatos információkért lásd: a következő blogbejegyzésben: [Virtuálisgép-ügynök és -bővítmények](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
