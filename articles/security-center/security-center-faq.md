---
title: Azure Security Center gyakori kérdések (GYIK) | Microsoft Docs
description: Ez a gyakori kérdések a Azure Security Centerával kapcsolatos kérdésekre adnak választ, amely segít a fenyegetések megelőzésében, észlelésében és megválaszolásában.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: memildin
ms.openlocfilehash: 896db06204188c4347fbdced0b1bb3f216f56ef9
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558669"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Azure Security Center – gyakori kérdések
Ez a gyakori kérdések a Azure Security Centerával kapcsolatos kérdésekre adnak választ, amely segít a fenyegetések megelőzésében, észlelésében és elhárításában azáltal, hogy a Microsoft Azure erőforrásainak jobb láthatóságát és felügyeletét teszi lehetővé.

> [!NOTE]
> Security Center a Microsoft monitoring Agent használatával gyűjti és tárolja az adatokat. További információ: [Azure Security Center platform migrálása](security-center-platform-migration.md).
>
>

## <a name="general-questions"></a>Általános kérdések
### <a name="what-is-azure-security-center"></a>Mi az Azure Security Center?
Az Azure Security Center magasabb fokú betekintést és szélesebb körű vezérlést tesz lehetővé az Azure-erőforrások fölött, így segít a fenyegetések megelőzésében, észlelésében és kezelésében. A biztonsági megoldások átfogó ökoszisztémájának alkalmazásával az összes előfizetésére vonatkozóan integrált biztonságfelügyeletet és szabályzatkezelést biztosít, és segít felderíteni az egyébként nehezen észlelhető fenyegetéseket.

### <a name="how-do-i-get-azure-security-center"></a>Hogyan lekérni Azure Security Center?
A Azure Security Center engedélyezve van a Microsoft Azure-előfizetéssel, és a [Azure Portal](https://azure.microsoft.com/features/azure-portal/)érhető el. ([Jelentkezzen be a portálra](https://portal.azure.com), válassza a **Tallózás**lehetőséget, majd görgessen a **Security Center**).  

## <a name="billing"></a>Számlázás
### <a name="how-does-billing-work-for-azure-security-center"></a>Hogyan működik a számlázás a Azure Security Center?
A Security Center szolgáltatást két szinten biztosítjuk:

Az **ingyenes szint** betekintést nyújt az Azure-erőforrások biztonsági állapotára, az alapszintű biztonsági házirendre, a biztonsági javaslatokra, valamint a partnerektől származó biztonsági termékekkel és szolgáltatásokkal való integrációra.

A **standard szint** fejlett veszélyforrás-észlelési képességeket biztosít, többek között a fenyegetések felderítését, a viselkedés elemzését, a rendellenességek észlelését, a biztonsági incidenseket és a veszélyforrások azonosítására vonatkozó jelentéseket. Elindíthat egy standard szintű ingyenes próbaverziót is. A frissítéshez válassza az [árképzési szintet](https://docs.microsoft.com/azure/security-center/security-center-pricing) a biztonsági házirendben. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

### <a name="how-can-i-track-who-in-my-organization-performed-pricing-tier-changes-in-azure-security-center"></a>Hogyan követhetem nyomon, hogy kik a szervezetem díjszabási szintjein végrehajtott változások Azure Security Center
Az Azure-előfizetések több rendszergazdai jogosultsággal rendelkezhetnek az árképzési szint módosításához. Annak megállapításához, hogy melyik felhasználó hajtott végre egy díjszabási szintet, használja az Azure-tevékenység naplóját. További információ: [itt](https://techcommunity.microsoft.com/t5/Security-Identity/Tracking-Changes-in-the-Pricing-Tier-for-Azure-Security-Center/td-p/390832).

## <a name="permissions"></a>Engedélyek
Az Azure Security Center [szerepköralapú hozzáférés-vezérlést (RBAC)](../role-based-access-control/role-assignments-portal.md) használ, amelynek [beépített szerepköreit](../role-based-access-control/built-in-roles.md) az Azure különböző csoportjaihoz, felhasználóihoz és szolgáltatásaihoz rendelheti.

Security Center megvizsgálja az erőforrások konfigurációját a biztonsági problémák és a biztonsági rések azonosítása érdekében. A Security Centerban csak akkor jelenik meg az erőforrásokhoz kapcsolódó információ, ha a tulajdonos, közreműködő vagy olvasó szerepkörét rendelte hozzá ahhoz az előfizetéshez vagy erőforráscsoporthoz, amelyhez az erőforrás tartozik.

A Security Center szerepkörökkel és engedélyezett műveletekkel kapcsolatos további tudnivalókért tekintse meg [a Azure Security Center engedélyeit](security-center-permissions.md) .

## <a name="data-collection-agents-and-workspaces"></a>Adatgyűjtés, ügynökök és munkaterületek
A Security Center adatokat gyűjt az Azure-beli virtuális gépekről (VM), a virtuálisgép-méretezési csoportokról, a IaaS-tárolókra és a nem Azure-beli számítógépekről (beleértve a helyszíni rendszert is) a biztonsági rések és fenyegetések figyelése érdekében. Az adatgyűjtés a Microsoft Monitoring Agent segítségével történik, amely a biztonsághoz kapcsolódó különböző konfigurációkat és eseménynaplókat olvas be a gépről, és elemzés céljából átmásolja az adatokat az Ön munkaterületére.

### <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Fizetnem kell Azure Monitor naplókat a Security Center által létrehozott munkaterületeken?
Nem. A Security Center által létrehozott munkaterületek, miközben a csomópontok számlázása Azure Monitor naplókhoz van konfigurálva, nem számítunk fel Azure Monitor naplók díját. Security Center számlázás mindig a Security Center biztonsági házirendje és a munkaterületre telepített megoldások alapján történik:

- **Ingyenes szintű** – Security Center engedélyezi az "SecurityCenterFree" megoldást az alapértelmezett munkaterületen. Az ingyenes szintet nem számoljuk fel.
- **Standard szintű** – a Security Center engedélyezi a "biztonsági" megoldást az alapértelmezett munkaterületen.

További információ a díjszabásról: [Security Center díjszabása](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> A Security Center által létrehozott munkaterületek log Analytics árképzési szintje nem befolyásolja Security Center számlázást.
>
>

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Mi jogosult a virtuális gépekre a Microsoft monitoring Agent telepítésének automatikus kiépítéséhez?
A Windows-vagy Linux-IaaS virtuális gépek az alábbiak szerint minősülnek:

- A Microsoft monitoring Agent bővítmény jelenleg nincs telepítve a virtuális gépen.
- A virtuális gép fut állapotban van.
- A Windows vagy Linux rendszerű Azure-beli [virtuális gép ügynöke](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) telepítve van.
- A virtuális gép nem használatos készülékként, például webalkalmazási tűzfalként vagy a következő generációs tűzfalon.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Törölhetem a Security Center által létrehozott alapértelmezett munkaterületeket?
**Az alapértelmezett munkaterület törlése nem ajánlott.** Security Center az alapértelmezett munkaterületeket használja a virtuális gépek biztonsági adatainak tárolására.  Ha töröl egy munkaterületet, Security Center nem tudja összegyűjteni ezeket az adatokat, és bizonyos biztonsági javaslatok és riasztások nem érhetők el.

A helyreállításhoz távolítsa el a Microsoft monitoring agentet a törölt munkaterülethez csatlakoztatott virtuális gépeken. Security Center újratelepíti az ügynököt, és új alapértelmezett munkaterületeket hoz létre.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Hogyan használhatom a meglévő Log Analytics munkaterületet?

Kiválaszthat egy meglévő Log Analytics munkaterületet a Security Center által gyűjtött adatok tárolásához. Meglévő Log Analytics munkaterület használata:

- A munkaterületet társítani kell a kiválasztott Azure-előfizetéshez.
- Legalább olvasási jogosultsággal kell rendelkeznie a munkaterület eléréséhez.

Meglévő Log Analytics munkaterület kiválasztása:

1. A **biztonsági házirend – adatgyűjtés**területen válassza a **másik munkaterület használata**lehetőséget.

   ![Másik munkaterület használata][5]

2. A legördülő menüben válasszon ki egy munkaterületet az összegyűjtött adatok tárolásához.

   > [!NOTE]
   > A legördülő menüben csak azok a munkaterületek jelennek meg, amelyekhez hozzáférése van az Azure-előfizetéséhez.
   >
   >

3. Kattintson a **Mentés** gombra.
4. A **Mentés**gombra kattintva a rendszer megkérdezi, hogy szeretné-e újrakonfigurálni a figyelt virtuális gépeket.

   - Válassza a **nem** lehetőséget, ha azt szeretné, hogy az új munkaterület-beállítások **csak az új virtuális gépeken legyenek érvényesek**. Az új munkaterület-beállítások csak az új ügynök telepítésére érvényesek; újonnan felderített virtuális gépek, amelyeken nincs telepítve a Microsoft monitoring Agent.
   - Válassza az **Igen** lehetőséget, ha azt szeretné, hogy az új munkaterület-beállítások **minden virtuális gépen érvényesek**legyenek. Továbbá a Security Center létrehozott munkaterülethez csatlakozó összes virtuális gép újra csatlakozik az új cél munkaterülethez.

   > [!NOTE]
   > Ha az Igen lehetőséget választja, nem szabad törölni a Security Center által létrehozott munkaterületeket, amíg az összes virtuális gép újra nem csatlakozik az új cél munkaterülethez. A művelet meghiúsul, ha a munkaterület túl korán van törölve.
   >
   >

   - A művelet megszakításához kattintson a **Mégse** gombra.

### Mi a teendő, ha a Microsoft monitoring Agent már telepítve van a virtuális gépen?<a name="mmaextensioninstalled"></a>
Ha a figyelési ügynök bővítményként van telepítve, a bővítmény konfigurációja lehetővé teszi, hogy a jelentéskészítés csak egyetlen munkaterületre legyen elérhető. A Security Center nem bírálja felül a felhasználói munkaterületek meglévő kapcsolatait. Security Center a biztonsági adatok egy már csatlakoztatott munkaterületen lévő virtuális gépről lesznek tárolva, ha a "biztonság" vagy a "SecurityCenterFree" megoldás telepítve van rajta. Security Center a folyamat legújabb verziójára frissítheti a bővítmény verzióját.

További információ: [az automatikus kiépítés egy korábban létező ügynök telepítése esetén](security-center-enable-data-collection.md#preexisting).


### Mi a teendő, ha a Microsoft monitoring Agent közvetlenül telepítve van a gépen, de nem bővítményként (közvetlen ügynök)?<a name="directagentinstalled"></a>
Ha a Microsoft monitoring Agent közvetlenül a virtuális gépre van telepítve (nem Azure-bővítményként), Security Center telepíti a Microsoft monitoring Agent bővítményt, és a legújabb verzióra frissítheti a Microsoft monitoring agentet.
A telepített ügynök továbbra is a már konfigurált munkaterülete (ke) t jelenti, és a jelentés a Security Center konfigurált munkaterületre fog jelentést adni (a többsoros vezérlés támogatott a Windows rendszerű gépeken).
Ha a konfigurált munkaterület egy felhasználói munkaterület (nem Security Center alapértelmezett munkaterülete), telepítenie kell a "Security/" SecurityCenterFree "megoldást arra, hogy Security Center a munkaterületre irányuló virtuális gépekről és számítógépekről származó események feldolgozásának megkezdéséhez.

Linux rendszerű gépek esetén az ügynök többsoros vezérlése még nem támogatott – ezért ha egy meglévő ügynök telepítését észleli, az automatikus kiépítés nem történik meg, és a gép konfigurációja nem módosul.

A Security Center március 17 2019 előtt előfizetett előfizetések meglévő gépei esetében, ha a rendszer egy meglévő ügynököt észlel, a Microsoft monitoring Agent bővítmény nem lesz telepítve, és a gép nem lesz hatással. Ezen gépek esetében tekintse meg a "figyelési ügynök állapotával kapcsolatos problémák megoldása a gépeken" című javaslatot az ügynök telepítési problémáinak megoldásához ezeken a gépeken

 További információ: [Mi történik, ha egy System Center Operations Manager vagy OMS közvetlen ügynök már telepítve van a virtuális gépre?](#scomomsinstalled)

### Mi történik, ha egy System Center Operations Manager ügynök már telepítve van a virtuális gépre?<a name="scomomsinstalled"></a>
A Security Center a Microsoft monitoring Agent bővítményt a meglévő System Center Operations Manager ügynöknek kell telepítenie. A meglévő ügynök a szokásos módon továbbra is jelentést küld a System Center Operations Manager-kiszolgálónak. Vegye figyelembe, hogy a Operations Manager ügynök és a Microsoft monitoring Agent közös futásidejű kódtárakat használ, amelyeket a rendszer a folyamat során a legújabb verzióra frissít. Megjegyzés: Ha a Operations Manager ügynök 2012-es verziója van telepítve, ne kapcsolja be az automatikus kiépítés funkciót (a kezelhetőségi képességek elvesznek, ha a Operations Manager-kiszolgáló a 2012-es verzióban is megtalálható).

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Mik a bővítmények eltávolításának következményei?
Ha eltávolítja a Microsoft figyelési bővítményt, Security Center nem tud biztonsági adatokat gyűjteni a virtuális gépről, és bizonyos biztonsági javaslatok és riasztások nem érhetők el. A Security Center 24 órán belül megállapítja, hogy a virtuális gép hiányzik a bővítményből, és újratelepíti a bővítményt.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Hogyan leállítani az ügynök automatikus telepítését és a munkaterület létrehozását?
Az előfizetésekhez tartozó automatikus kiépítés kikapcsolható a biztonsági házirendben, de ez nem ajánlott. Az automatikus kiépítési korlátok kikapcsolásával Security Center javaslatok és riasztások. Az automatikus kiépítés letiltása:

1. Ha az előfizetése konfigurálva van a standard csomagra, nyissa meg az előfizetéshez tartozó biztonsági házirendet, és válassza az **ingyenes** szintet.

   ![Díjcsomag][1]

2. Ezt követően kapcsolja ki az automatikus kiépítés szolgáltatást a **biztonsági házirend – adatgyűjtési** lapon a **kikapcsolás** lehetőség kiválasztásával.
   ![Adatgyűjtés][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Letiltom az ügynök automatikus telepítését és a munkaterület létrehozását?

> [!NOTE]
> Ügyeljen arra, hogy tekintse át a [kilépések](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) [következményeit?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) és az ajánlott lépések szakaszt, ha az automatikus kiépítés lehetőséget választja.
>
>

Előfordulhat, hogy ki kell kapcsolni az automatikus kiépítés lehetőségeit, ha az alábbiak vonatkoznak Önre:

- A Security Center automatikus ügynök telepítése a teljes előfizetésre vonatkozik. A virtuális gépek egy részhalmazára nem alkalmazhat automatikus telepítést. Ha vannak olyan kritikus virtuális gépek, amelyek nem telepíthetők a Microsoft monitoring Agent szolgáltatással, akkor le kell választania az automatikus üzembe helyezést.
- A Microsoft monitoring Agent (MMA) bővítmény telepítése frissíti az ügynök verzióját. Ez egy közvetlen ügynökre és egy System Center Operations Manager ügynökre vonatkozik (az utóbbi esetben a Operations Manager és az MMA közös futásidejű kódtárakat használ – ez a folyamat frissülni fog). Ha a telepített Operations Manager ügynök az 2012-es verzió, és frissül, akkor a kezelhetőségi képességek elvesznek, ha a Operations Manager-kiszolgáló a 2012-es verzióval is rendelkezik. Ha a telepített Operations Manager ügynök 2012-es verzióval rendelkezik, érdemes megfontolni az automatikus kiépítés kiválasztását.
- Ha az előfizetéshez (egy központi munkaterülethez) kívüli egyéni munkaterülettel rendelkezik, akkor az automatikus kiépítés után le kell választania. Manuálisan is telepítheti a Microsoft monitoring Agent bővítményt, és anélkül csatlakoztathatja a munkaterületet, hogy Security Center felülbírálja a kapcsolatot.
- Ha el szeretné kerülni az előfizetések több munkaterületének létrehozását, és saját egyéni munkaterülettel rendelkezik az előfizetésben, akkor két lehetőség közül választhat:

   1. Kiválaszthatja az automatikus kiépítés lehetőségeit. Az áttelepítés után állítsa be a munkaterület alapértelmezett beállításait a [Hogyan használhatom a meglévő log Analytics munkaterületet?](#how-can-i-use-my-existing-log-analytics-workspace) című témakörben leírtak szerint?
   2. Azt is megteheti, hogy az áttelepítés befejeződik, a Microsoft monitoring Agent telepítése a virtuális gépekre és a létrehozott munkaterülethez csatlakozó virtuális gépekre. Ezután válassza ki a saját egyéni munkaterületét úgy, hogy a már telepített ügynökök újrakonfigurálásához megnyomja az alapértelmezett munkaterület-beállítást. További információ: [Hogyan használhatom a meglévő log Analytics munkaterületet?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Milyen hatással van az automatikus kiépítés kivonására?
Az áttelepítés befejezésekor Security Center nem tud biztonsági adatokat gyűjteni a virtuális gépről, és bizonyos biztonsági javaslatok és riasztások nem érhetők el. Ha letiltják, manuálisan telepítse a Microsoft monitoring agentet. A [kilépéskor tekintse meg a javasolt lépéseket](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Mi a javasolt lépés az automatikus kiépítés kiválasztásakor?

Manuálisan telepítse a Microsoft monitoring Agent bővítményt, hogy Security Center biztonsági adatokat gyűjtsön a virtuális gépekről, és javaslatokat és riasztásokat nyújtson. A telepítéssel kapcsolatos útmutatásért tekintse meg a [Windows rendszerű virtuális gép](../virtual-machines/extensions/oms-windows.md) vagy az ügynök telepítése a [linuxos virtuális géphez](../virtual-machines/extensions/oms-linux.md) című témakört.

Az ügynököt bármely meglévő egyéni munkaterülethez vagy Security Center létrehozott munkaterülethez is összekapcsolhatjuk. Ha egy egyéni munkaterületen nincs engedélyezve a "biztonság" vagy a "SecurityCenterFree" megoldás, akkor egy megoldást kell alkalmaznia. Az alkalmazáshoz válassza ki az egyéni munkaterületet vagy előfizetést, és alkalmazza a díjszabási szintet a **biztonsági házirend – díjszabási** csomag lapon.

   ![Díjcsomag][1]

Security Center a kiválasztott díjszabási csomag alapján engedélyezi a megfelelő megoldást a munkaterületen.

### Hogyan eltávolítja a Security Center által telepített OMS-bővítményeket?<a name="remove-oms"></a>
A Microsoft monitoring agentet manuálisan is eltávolíthatja. Ez nem ajánlott, mert korlátozza Security Center javaslatokat és riasztásokat.

> [!NOTE]
> Ha engedélyezve van az adatgyűjtés, Security Center újratelepíti az ügynököt az eltávolítása után.  Az ügynök manuális eltávolítása előtt le kell tiltania az adatgyűjtés letiltását. Lásd: Hogyan az ügynök automatikus telepítésének leállítása és a munkaterület létrehozása? az adatgyűjtés letiltására vonatkozó utasítások.
>
>

Az ügynök manuális eltávolítása:

1.  A portálon nyissa meg **log Analytics**.
2.  A Log Analytics lapon válasszon ki egy munkaterületet:
3.  Válassza ki azokat a virtuális gépeket, amelyeket nem szeretne figyelni, és válassza a **Leválasztás**lehetőséget.

   ![Az ügynök eltávolítása][3]

> [!NOTE]
> Ha egy Linux rendszerű virtuális gép már rendelkezik egy nem kiterjesztésű OMS-ügynökkel, a bővítmény eltávolítása eltávolítja az ügynököt is, és az ügyfélnek újra kell telepítenie.
>
>
### <a name="how-do-i-disable-data-collection"></a>Hogyan az adatgyűjtés letiltása?
Az automatikus kiépítés alapértelmezés szerint ki van kapcsolva. A biztonsági házirendben a beállítás kikapcsolásával bármikor letilthatja az erőforrások automatikus kiépítési lehetőségeit. Az automatikus kiépítés kifejezetten ajánlott ahhoz, hogy biztonsági riasztásokat és javaslatokat kapjon a rendszerfrissítésekkel, az operációs rendszer sebezhetőségével és az Endpoint Protection szolgáltatással kapcsolatban.

Az adatgyűjtés letiltásához [Jelentkezzen be a Azure Portalba](https://portal.azure.com), válassza a **Tallózás**lehetőséget, válassza a **Security Center**lehetőséget, majd válassza a **házirend kiválasztása**lehetőséget. Válassza ki azt az előfizetést, amelynél le szeretné tiltani az automatikus kiépítést. Amikor kiválaszt egy előfizetési **biztonsági házirendet** , megnyílik az adatgyűjtés. Az **automatikus kiépítés**alatt válassza ki a **ki**lehetőséget.

### <a name="how-do-i-enable-data-collection"></a>Hogyan engedélyezi az adatgyűjtés?
Az Azure-előfizetéshez tartozó adatgyűjtést a biztonsági szabályzatban engedélyezheti. Az adatgyűjtés engedélyezéséhez. [Jelentkezzen be a Azure Portalba](https://portal.azure.com), válassza a **Tallózás**lehetőséget, válassza a **Security Center**lehetőséget, és válassza a **biztonsági szabályzat**lehetőséget. Válassza ki azt az előfizetést, amelyhez engedélyezni kívánja az automatikus kiépítés engedélyezését. Amikor kiválaszt egy előfizetési **biztonsági házirendet** , megnyílik az adatgyűjtés. Az **automatikus kiépítés**területen válassza **a**be lehetőséget.

### <a name="what-happens-when-data-collection-is-enabled"></a>Mi történik, ha az adatgyűjtés engedélyezve van?
Ha engedélyezve van az automatikus kiépítés, Security Center a Microsoft monitoring agentet az összes támogatott Azure-beli virtuális gépen és a létrehozott újakon. Az automatikus kiépítés ajánlott, de a manuális ügynök telepítése is elérhető. [Tudnivalók a Microsoft Monitoring Agent bővítmény telepítéséről](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Az ügynök engedélyezi a 4688-es folyamat-létrehozási eseményt és a 4688-es eseményen belüli *parancssori* mezőt. A virtuális gépen létrehozott új folyamatokat az eseménynapló rögzíti, és a Security Center észlelési szolgáltatásai figyelik. Az egyes új folyamatokra vonatkozóan rögzített részletekkel kapcsolatos további információkért lásd: [a Leírás mezői a 4688-ben](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Az ügynök a virtuális gépen létrehozott 4688 eseményt is gyűjti, és azokat a keresés során tárolja.

Az ügynök az [adaptív alkalmazások vezérlőinek](security-center-adaptive-application.md)adatgyűjtését is lehetővé teszi, Security Center a helyi AppLocker-házirendet naplózási módban konfigurálja az összes alkalmazás engedélyezéséhez. Ez a szabályzat az AppLockert az események előállítására fogja használni, amelyeket a Security Center gyűjt és használ. Fontos megjegyezni, hogy ez a szabályzat nem lesz konfigurálva olyan gépeken, amelyeken már van konfigurált AppLocker-házirend. 

Ha a Security Center gyanús tevékenységet észlel a virtuális gépen, a rendszer e-mailben értesíti az ügyfelet, ha a [biztonsági kapcsolattartási adatokat](security-center-provide-security-contact-details.md) megadták. A Security Center biztonsági riasztások irányítópultján is látható egy riasztás.

### <a name="will-security-center-work-using-an-oms-gateway"></a>A Security Center OMS-átjáró használatával fog működni?
Igen. Azure Security Center kihasználja a Azure Monitor az Azure-beli virtuális gépekről és kiszolgálókról származó adatok gyűjtésére a Microsoft monitoring Agent használatával.
Az adatok gyűjtéséhez az egyes virtuális gépeknek és kiszolgálóknak HTTPS használatával kell csatlakozniuk az internethez. A csatlakozás lehet közvetlen, proxy használatával vagy a [OMS-átjárón](../azure-monitor/platform/gateway.md)keresztül.

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Befolyásolja a figyelési ügynök a saját kiszolgálók teljesítményét?
Az ügynök a rendszererőforrások névleges mennyiségét használja fel, és kevés hatással van a teljesítményre. A teljesítményre gyakorolt hatásra, valamint az ügynökre és a bővítményre vonatkozó további információkért tekintse meg a [tervezési és üzemeltetési útmutatót](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Hol tárolják az adataimat?
Az ettől az ügynöktől gyűjtött adatokat az előfizetéséhez vagy egy új munkaterülethez társított meglévő Log Analytics munkaterületen tároljuk. További információkért lásd az [adatbiztonságot](security-center-data-security.md)ismertető témakört.

## Meglévő Azure Monitor naplók ügyfeleinek<a name="existingloganalyticscust"></a>

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>A Security Center felülbírálja a virtuális gépek és a munkaterületek közötti meglévő kapcsolatokat?
Ha egy virtuális gépen már telepítve van a Microsoft monitoring Agent Azure-bővítményként, Security Center nem bírálja felül a meglévő munkaterület-kapcsolatokat. Ehelyett Security Center a meglévő munkaterületet használja. A virtuális gép védett lesz, ha a "biztonság" vagy a "SecurityCenterFree" megoldás telepítve van azon a munkaterületen, amelyre a jelentést küldi. 

Egy Security Center megoldás van telepítve az adatgyűjtési képernyőn kiválasztott munkaterületre, ha még nincs jelen, és a megoldás csak a megfelelő virtuális gépekre lesz alkalmazva. Megoldás hozzáadásakor a rendszer alapértelmezés szerint automatikusan telepíti a Log Analytics munkaterülethez csatlakoztatott összes Windows-és Linux-ügynököt. A [megoldás célcsoportjának](../operations-management-suite/operations-management-suite-solution-targeting.md) használatával hatókört alkalmazhat a megoldásokra.

Ha a Microsoft monitoring Agent közvetlenül a virtuális gépre van telepítve (nem Azure-bővítményként), Security Center nem telepíti a Microsoft monitoring agentet, és a biztonság figyelése korlátozott.

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Security Center telepítési megoldásokat a meglévő Log Analytics-munkaterületekre? Mik a számlázási következményei?
Ha Security Center azt észleli, hogy egy virtuális gép már csatlakoztatva van egy létrehozott munkaterülethez, Security Center engedélyezi a megoldásokat a munkaterületen az Ön díjszabási szintjétől függően. A megoldásokat csak a megfelelő Azure-beli virtuális gépekre alkalmazza a [megoldás célcsoportján](../operations-management-suite/operations-management-suite-solution-targeting.md)keresztül, így a számlázás változatlan marad.

- **Ingyenes szintű** – Security Center a "SecurityCenterFree" megoldást telepíti a munkaterületre. Az ingyenes szintet nem számoljuk fel.
- **Standard szint** – Security Center a "biztonsági" megoldást telepíti a munkaterületre.

   ![Megoldások az alapértelmezett munkaterületen][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Már van munkaterületem a saját környezetben, használhatom a biztonsági adatok gyűjtésére?
Ha egy virtuális gépen már telepítve van a Microsoft monitoring Agent Azure-bővítményként, Security Center a meglévő csatlakoztatott munkaterületet használja. A munkaterületre egy Security Center megoldás van telepítve, ha még nincs jelen, és a megoldás csak a megfelelő virtuális gépekre vonatkozik, a [megoldás célcsoportján](../operations-management-suite/operations-management-suite-solution-targeting.md)keresztül.

Amikor Security Center telepíti a Microsoft monitoring agentet a virtuális gépekre, a Security Center által létrehozott alapértelmezett munkaterületet (ka) t használja.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Már van biztonsági megoldásom a saját munkaterületeken. Mik a számlázási következményei?
A biztonsági & auditálási megoldással engedélyezhető az Azure-beli virtuális gépek Security Center standard szintű szolgáltatásainak használata. Ha a biztonsági & auditálási megoldás már telepítve van egy munkaterületen, Security Center a meglévő megoldást használja. A számlázás nem változik.

## <a name="using-azure-security-center"></a>Azure Security Center használata
### <a name="what-is-a-security-policy"></a>Mi az a biztonsági szabályzat?
A biztonsági szabályzat határozza meg a megadott előfizetésben lévő erőforrásokhoz ajánlott vezérlők készletét. Azure Security Center az Azure-előfizetésekre vonatkozó szabályzatokat a vállalat biztonsági követelményeinek, valamint az egyes előfizetésekben szereplő alkalmazások típusának vagy az adatérzékenység alapján határozhatja meg.

A biztonsági szabályzatok Azure Security Center meghajtó biztonsági javaslatai és figyelése. A biztonsági házirendekkel kapcsolatos további információkért lásd: [biztonsági állapot figyelése Azure Security Center](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Kik módosíthatják a biztonsági házirendeket?
A biztonsági szabályzatok módosításához biztonsági rendszergazdának vagy az előfizetés tulajdonosának vagy közreműködőnek kell lennie.

A biztonsági szabályzatok konfigurálásával kapcsolatos további információkért lásd: [biztonsági házirendek beállítása Azure Security Centerban](tutorial-security-policy.md).

### <a name="what-is-a-security-recommendation"></a>Mi az a biztonsági javaslat?
Az Azure Security Center elemzi az Azure-erőforrások biztonsági állapotát. A potenciális biztonsági rések észlelése esetén javaslatok jönnek létre. A javaslatok végigvezetik a szükséges vezérlő konfigurálásának lépésein. Példák:

* Kártevő szoftver kiépítés a kártékony szoftverek azonosításához és eltávolításához
* [Hálózati biztonsági csoportok](../virtual-network/security-overview.md) és szabályok a virtuális gépek forgalmának vezérléséhez
* Webalkalmazási tűzfal üzembe helyezése a webalkalmazásokra irányuló támadások elleni védelem érdekében
* Hiányzó rendszerfrissítések telepítése
* Az operációs rendszer azon konfigurációinak kezelése, amelyek nem felelnek meg a javasolt alapkonfigurációknak

Itt csak a biztonsági házirendekben engedélyezett javaslatok jelennek meg.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Hogyan tekinthetem meg az Azure-erőforrások aktuális biztonsági állapotát?
A **Security Center áttekintő** oldal a környezet általános biztonsági állapotát mutatja számítás, hálózatkezelés, tárterület & adatok és alkalmazások szerint lebontva. Minden erőforrástípus rendelkezik egy kijelzővel, amely az esetleges biztonsági rések azonosítását mutatja be. A csempére kattintva megjelenik a Security Center által azonosított biztonsági problémák listája, valamint az előfizetésében lévő erőforrások leltára.

### <a name="what-triggers-a-security-alert"></a>Mi váltja fel a biztonsági riasztást?
Azure Security Center automatikusan gyűjti, elemzi és megtagadja az Azure-erőforrások, a hálózat és a partneri megoldások, például az antimalware és a tűzfalak naplózási adatait. Fenyegetések észlelése esetén a központ biztonsági riasztást hoz létre. Példák fenyegetés észlelésére:

* Feltört virtuális gépek, amelyek kártékonyként azonosított IP-címekkel kommunikálnak
* A Windows hibajelentés használatával észlelt speciális kártevők
* Virtuális gépek elleni, a teljes kipróbálás módszerén alapuló támadások
* Biztonsági riasztások olyan integrált partneri biztonsági megoldásokból, mint például a kártevők elleni védelem vagy a webalkalmazási tűzfalak

### Miért változnak a biztonsági pontszámok értékei? <a name="secure-score-faq"></a>
Február 2019-én a súlyosságuk jobb illeszkedése érdekében Security Center néhány javaslat pontszámát. Ennek a beállításnak az eredményeképpen előfordulhat, hogy a biztonsági pontszámok teljes értékei módosulnak.  A biztonságos pontszámról további információt a [biztonságos pontszám kiszámítása](security-center-secure-score.md)című témakörben talál.

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Mi a különbség az észlelt fenyegetések és a riasztások között a Microsoft Security Response Center és a Azure Security Center között?
A Microsoft Security Response Center (MSRC) az Azure-hálózat és-infrastruktúra biztonsági figyelését végzi, és a fenyegetésekkel kapcsolatos intelligenciát és a harmadik felektől érkező visszaéléseket fogad. Ha a MSRC tisztában van azzal, hogy az ügyféladatokat egy törvénytelen vagy jogosulatlan fél használja, vagy az Azure nem felel meg az elfogadható használat feltételeinek, akkor a biztonsági incidens kezelője értesíti az ügyfelet. Az értesítés általában akkor fordul elő, ha e-mailt küld a Azure Security Center vagy az Azure-előfizetés tulajdonosa által megadott biztonsági partnereknek, ha nincs biztonsági kapcsolat megadva.

Security Center egy Azure-szolgáltatás, amely folyamatosan figyeli az ügyfél Azure-környezetét, és az elemzést alkalmazza a potenciálisan kártékony tevékenységek széles körének automatikus észlelésére. Ezek az észlelések biztonsági riasztásként jelennek meg a Security Center irányítópulton.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Mely Azure-erőforrásokat figyeli a Azure Security Center?
Azure Security Center a következő Azure-erőforrásokat figyeli:

* Virtuális gépek (VM-EK) (beleértve a [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Virtual Machine Scale Sets
* Azure virtuális hálózatok
* Azure SQL-szolgáltatás
* Azure Storage-fiók
* Azure Web Apps ( [app Service Environment](../app-service/environment/intro.md))
* Azure-előfizetéssel integrált partneri megoldások, például webalkalmazási tűzfal virtuális gépeken és App Service Environment

Emellett a nem Azure (beleértve a helyszíni) számítógépeket is figyelheti Azure Security Center (a [Windows rendszerű számítógépek](./quick-onboard-windows-computer.md) és a Linux rendszerű [számítógépek](./quick-onboard-linux-computer.md) is támogatottak)

## <a name="virtual-machines"></a>Virtual Machines
### <a name="what-types-of-virtual-machines-are-supported"></a>Milyen típusú virtuális gépek támogatottak?
A [klasszikus és a Resource Manager-alapú üzemi modellekkel](../azure-classic-rm.md)létrehozott virtuális gépek (VM-EK) figyelése és javaslatai elérhetők.

A támogatott platformok listáját a [Azure Security Center támogatott platformok](security-center-os-coverage.md) részben tekintheti meg.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Miért nem Azure Security Center ismeri fel az Azure-beli virtuális gépen futó antimalware-megoldást?
A Azure Security Center az Azure-bővítményeken keresztül telepített antimalware-t is betekintést biztosít. A Security Center például nem képes észlelni a megadott rendszerképre előre telepített kártevő szoftvereket, vagy ha a saját folyamatai (például a konfigurációs felügyeleti rendszerek) használatával telepítette az antimalware-t a virtuális gépekre.

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Miért kapok "hiányzó vizsgálati adataim" üzenetet a virtuális géphez?
Ez az üzenet jelenik meg, ha egy virtuális gép vizsgálati adatai hiányoznak. Miután az Azure Security Centerben engedélyezte az adatgyűjtést, némi időt igénybe vehet (de kevesebb mint egy órát), amíg az adatok érkezni kezdenek. A vizsgálati adatok kezdeti feltöltését követően akkor jelenhet meg ez az üzenetet, ha egyáltalán nincsenek vizsgálati adatok, vagy ha az utóbbi időben nem érkeztek. A leállított állapotú virtuális gépekről nem érkeznek vizsgálati adatok. Ez az üzenet akkor is megjelenhet, ha a vizsgálati adatok nem lettek a közelmúltban kitöltve (a Windows-ügynök adatmegőrzési szabályzatának megfelelően, amelynek alapértelmezett értéke 30 nap).

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Milyen gyakran Security Center az operációs rendszer biztonsági réseit, a rendszerfrissítéseket és az Endpoint Protection szolgáltatással kapcsolatos problémákat?
Az alábbiakban láthatók a biztonsági rések, frissítések és problémák Security Center vizsgálatainak késési ideje:

- Operációs rendszer biztonsági beállításai – az Adatfrissítés 48 órán belül megtörténik
- Rendszerfrissítések – a rendszer 24 órán belül frissíti az adatfrissítést
- Endpoint Protection problémák – az Adatfrissítés 8 órán belül frissül

Security Center általában óránként vizsgálja az új adatforrásokat, és ennek megfelelően frissíti az ajánlásokat. 

> [!NOTE]
> Security Center a Microsoft monitoring Agent használatával gyűjti és tárolja az adatokat. További információ: [Azure Security Center platform migrálása](security-center-platform-migration.md).
>
>

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Miért jelenik meg a "VM-ügynök hiányzik?" üzenet?
A virtuálisgép-ügynöknek telepítve kell lennie a virtuális gépeken az adatgyűjtés engedélyezéséhez. Az Azure Marketplace-ről üzembe helyezett virtuális gépek esetében a virtuálisgép-ügynök alapértelmezés szerint telepítve van. A virtuálisgép-ügynök más virtuális gépekre való telepítésével kapcsolatos információkért tekintse meg a [VM-ügynök és-bővítmények](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)című blogbejegyzést.


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
