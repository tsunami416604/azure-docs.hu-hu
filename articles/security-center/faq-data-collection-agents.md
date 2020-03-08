---
title: Azure Security Center gyakori kérdések – adatgyűjtés és ügynökök
description: Az adatgyűjtéssel,-ügynökökkel és-munkaterületekkel kapcsolatos gyakori kérdések Azure Security Center, egy olyan termék, amely segít a fenyegetések megelőzésében, észlelésében és megválaszolásában.
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
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 8317a13b9ef87679836f55627268deefa4500dce
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372928"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Gyakori kérdések – az adatgyűjtés, az ügynökök és a munkaterületek kérdései

A Security Center adatokat gyűjt az Azure-beli virtuális gépekről (VM), a virtuálisgép-méretezési csoportokról, a IaaS-tárolók és a nem Azure-beli számítógépekről (beleértve a helyszíni gépeket is) a biztonsági rések és fenyegetések figyelésére. Az adatgyűjtés a Microsoft Monitoring Agent segítségével történik, amely a biztonsághoz kapcsolódó különböző konfigurációkat és eseménynaplókat olvas be a gépről, és elemzés céljából átmásolja az adatokat az Ön munkaterületére.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Fizetnem kell Azure Monitor naplókat a Security Center által létrehozott munkaterületeken?

Nem. A Security Center által létrehozott munkaterületek, miközben a csomópontok számlázása Azure Monitor naplókhoz van konfigurálva, nem számítunk fel Azure Monitor naplók díját. A Security Center minden esetben elszámolt díj attól a Security Center biztonsági házirend és a megoldások a munkaterülethez telepítve:

- **Ingyenes szintű** – Security Center engedélyezi az "SecurityCenterFree" megoldást az alapértelmezett munkaterületen. Az ingyenes szintet nem számoljuk fel.

- **Standard szintű** – a Security Center engedélyezi a "biztonsági" megoldást az alapértelmezett munkaterületen.

További információ a díjszabásról: [Security Center díjszabása](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> A Security Center által létrehozott munkaterületek log Analytics árképzési szintje nem befolyásolja Security Center számlázást.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>A Microsoft Monitoring Agent telepítése automatikus kiépítése milyen jogosult egy virtuális Gépet?

Windows vagy Linux rendszerű IaaS virtuális gépek jogosultak, ha:

- A Microsoft Monitoring Agent bővítményt jelenleg nem települ a virtuális gépen.
- A virtuális gép futó állapotban van.
- A Windows vagy Linux rendszerű Azure-beli [virtuális gép ügynöke](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) telepítve van.
- A virtuális gép nem használatos, például a webalkalmazási tűzfal vagy új generációs tűzfal telepíthetőek.


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>A Security Center által létrehozott alapértelmezett munkaterületet is törli?

**Az alapértelmezett munkaterület törlése nem ajánlott.** A Security Center az alapértelmezett munkaterület használ a virtuális gépek biztonsági adatainak tárolásához. Ha töröl egy munkaterületet, a Security Center nem tudja gyűjteni az adatokat, és egyes biztonsági javaslatok és riasztások nem érhetők el.

Szeretné használni, távolítsa el a Microsoft Monitoring Agentet a virtuális gépeken, a törölt munkaterülethez csatlakozik. A Security Center újra telepíti az ügynököt, és létrehozza az új alapértelmezett munkaterületek.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Hogyan használhatom a meglévő Log Analytics-munkaterületet?

Kiválaszthat egy meglévő Log Analytics-munkaterületet a Security Center által gyűjtött adatok tárolásához. A meglévő Log Analytics-munkaterület használata:

- A munkaterület a kiválasztott Azure-előfizetése társítva kell lennie.
- Minimális rendelkeznie kell olvasási engedéllyel a munkaterület eléréséhez.

Egy meglévő Log Analytics-munkaterület kiválasztása:

1. A **biztonsági házirend – adatgyűjtés**területen válassza a **másik munkaterület használata**lehetőséget.

    ![Másik munkaterület használata][4]

1. A legördülő menüből válassza ki a munkaterületet a gyűjtött adatok tárolásához.

    > [!NOTE]
    > A lekéréses menüre, az csak azok a munkaterületek, amely hozzáféréssel rendelkezik, és az Azure-előfizetésében vannak jelennek meg.

1. Kattintson a **Mentés** gombra. A rendszer megkérdezi, hogy szeretné-e újrakonfigurálni a figyelt virtuális gépeket.

    - Válassza a **nem** lehetőséget, ha azt szeretné, hogy az új munkaterület-beállítások **csak az új virtuális gépeken legyenek érvényesek**. Az új munkaterület csak vonatkoznak új ügynökök telepítése; az újonnan felderített virtuális gépek, amelyeken nincs telepítve a Microsoft Monitoring Agent telepítve van.
    - Válassza az **Igen** lehetőséget, ha azt szeretné, hogy az új munkaterület-beállítások **minden virtuális gépen érvényesek**legyenek. Emellett egy munkaterület létrehozása a Security Center minden virtuális Gépről az új cél munkaterületet újra csatlakoztatni.

    > [!NOTE]
    > Ha az **Igen**lehetőséget választja, ne törölje a Security Center által létrehozott munkaterületeket, amíg az összes virtuális gép újra nem csatlakozik az új cél munkaterülethez. Ez a művelet sikertelen lesz, ha a munkaterület túl korán törlődik.

    - A művelet megszakításához válassza a **Mégse**lehetőséget.

## Mi a teendő, ha a Microsoft monitoring Agent már telepítve van a virtuális gépen?<a name="mmaextensioninstalled"></a>

Ha a figyelési ügynök bővítményként van telepítve, a bővítmény konfigurációja lehetővé teszi, hogy a jelentéskészítés csak egyetlen munkaterületre legyen elérhető. A Security Center nem bírálja felül a meglévő kapcsolatok felhasználó munkaterületeket. Security Center a biztonsági adatok egy már csatlakoztatott munkaterületen lévő virtuális gépről lesznek tárolva, ha a "biztonság" vagy a "SecurityCenterFree" megoldás telepítve van rajta. Security Center a folyamat legújabb verziójára frissítheti a bővítmény verzióját.

További információ: [az automatikus kiépítés egy korábban létező ügynök telepítése esetén](security-center-enable-data-collection.md#preexisting).



## Mi a teendő, ha a Microsoft monitoring Agent közvetlenül telepítve van a gépen, de nem bővítményként (közvetlen ügynökként)?<a name="directagentinstalled"></a>

Ha a Microsoft monitoring Agent közvetlenül a virtuális gépre van telepítve (nem Azure-bővítményként), Security Center telepíti a Microsoft monitoring Agent bővítményt, és a legújabb verzióra frissítheti a Microsoft monitoring agentet.

A telepített ügynök továbbra is a már konfigurált munkaterülete (ke) t jelenti, és a jelentés a Security Center konfigurált munkaterületre fog jelentést adni (a többsoros vezérlés támogatott a Windows rendszerű gépeken).

Ha a konfigurált munkaterület egy felhasználói munkaterület (nem Security Center alapértelmezett munkaterülete), telepítenie kell a "Security/" SecurityCenterFree "megoldást arra, hogy Security Center a munkaterületre irányuló virtuális gépekről és számítógépekről származó események feldolgozásának megkezdéséhez.

Linux rendszerű gépek esetén az ügynök többsoros vezérlése még nem támogatott – ezért ha egy meglévő ügynök telepítését észleli, az automatikus kiépítés nem történik meg, és a gép konfigurációja nem módosul.

A Security Center március 17 2019 előtt előfizetett előfizetések meglévő gépei esetében, ha a rendszer egy meglévő ügynököt észlel, a Microsoft monitoring Agent bővítmény nem lesz telepítve, és a gép nem lesz hatással. Ezen gépek esetében tekintse meg a "figyelési ügynök állapotával kapcsolatos problémák megoldása a gépeken" című javaslatot az ügynök telepítési problémáinak megoldásához ezeken a gépeken

További információ: [Mi történik, ha egy System Center Operations Manager vagy OMS közvetlen ügynök már telepítve van a virtuális gépre?](#scomomsinstalled)

## Mi a teendő, ha egy System Center Operations Manager ügynök már telepítve van a virtuális gépre?<a name="scomomsinstalled"></a>

A Security Center a Microsoft monitoring Agent bővítményt a meglévő System Center Operations Manager ügynöknek kell telepítenie. A meglévő ügynök a szokásos módon továbbra is jelentést küld a System Center Operations Manager-kiszolgálónak. Vegye figyelembe, hogy a Operations Manager ügynök és a Microsoft monitoring Agent közös futásidejű kódtárakat használ, amelyeket a rendszer a folyamat során a legújabb verzióra frissít. Megjegyzés: Ha a Operations Manager ügynök 2012-es verziója van telepítve, ne kapcsolja be az automatikus kiépítés funkciót (a kezelhetőségi képességek elvesznek, ha a Operations Manager-kiszolgáló a 2012-es verzióban is megtalálható).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Mit jelent az ilyen bővítmények eltávolításának következményei?

Ha eltávolítja a Microsoft Monitoring bővítményt, a Security Center nem tud a biztonsági adatok gyűjtésére a virtuális gép és az egyes biztonsági javaslatok és riasztások nem érhetők el. 24 órán belül a Security Center meghatározza, hogy a virtuális gép a bővítmény hiányzik, és újratelepíti a bővítményt.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Hogyan akadályozható meg az ügynök automatikus telepítés és a munkaterület létrehozását?

Automatikus üzembe helyezés a biztonsági szabályzat az előfizetések ki is kapcsolhatja, de ez nem ajánlott. Automatikus üzembe helyezési korlátozások Security Center javaslatait és riasztások kikapcsolásával. Az Automatikus kiépítés letiltása:

1. Ha az előfizetése konfigurálva van a standard csomagra, nyissa meg az előfizetéshez tartozó biztonsági házirendet, és válassza az **ingyenes** szintet.

   ![Tarifacsomag][1]

1. Ezt követően kapcsolja ki az automatikus kiépítés szolgáltatást a **biztonsági házirend – adatgyűjtési** lapon a **kikapcsolás** lehetőség kiválasztásával.
   ![Adatgyűjtés][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Meg tudom tilthatják le az automatikus ügynöktelepítés és munkaterület létrehozása?

> [!NOTE]
> Ügyeljen arra, hogy tekintse át a [kilépések](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) [következményeit?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) és az ajánlott lépések szakaszt, ha az automatikus kiépítés lehetőséget választja.

Előfordulhat, hogy szeretné tilthatják le az automatikus kiépítést, ha Ön számára az alábbiak érvényesek:

- A teljes előfizetés a Security Center által automatikus ügynöktelepítés vonatkozik. Virtuális gépek egy része nem alkalmazhat automatikus telepítését. Ha nincsenek kritikus fontosságú virtuális gépek, amelyek a Microsoft Monitoring Agent nem telepíthető, majd kell kikapcsolja az Automatikus kiépítés.
- A Microsoft monitoring Agent (MMA) bővítmény telepítése frissíti az ügynök verzióját. Ez egy közvetlen ügynökre és egy System Center Operations Manager ügynökre vonatkozik (az utóbbi esetben a Operations Manager és az MMA közös futásidejű kódtárakat használ – ez a folyamat frissülni fog). Ha a telepített Operations Manager ügynök az 2012-es verzió, és frissül, akkor a kezelhetőségi képességek elvesznek, ha a Operations Manager-kiszolgáló a 2012-es verzióval is rendelkezik. Ha a telepített Operations Manager ügynök 2012-es verzióval rendelkezik, érdemes megfontolni az automatikus kiépítés kiválasztását.
- Ha az előfizetéshez (egy központi munkaterülethez) kívüli egyéni munkaterülettel rendelkezik, akkor az automatikus kiépítés után le kell választania. Manuálisan telepítse a Microsoft Monitoring Agent bővítményt, és csatlakoztathatja azt a munkaterületet anélkül, hogy a Security Center a kapcsolat felülírása.
- Ha el szeretné kerülni előfizetésenként több munkaterülettel létrehozását, és az előfizetésen belül a saját egyéni munkaterületet, majd, két lehetősége van:

   1. Az Automatikus kiépítés kívül kérheti. Az áttelepítés után állítsa be a munkaterület alapértelmezett beállításait a [Hogyan használhatom a meglévő log Analytics munkaterületet?](#how-can-i-use-my-existing-log-analytics-workspace) című témakörben leírtak szerint?

   1. Vagy engedélyezheti, hogy az áttelepítés befejezéséhez, a Microsoft Monitoring Agentet a virtuális gépeket kell telepíteni, és a létrehozott munkaterülethez csatlakozik a virtuális gépeket. Ezután válassza ki a saját egyéni munkaterület, ha az alapértelmezett munkaterület beállítása a kiválasztott újrakonfigurálása a már telepített ügynökök. További információ: [Hogyan használhatom a meglévő log Analytics munkaterületet?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Mik azok az Automatikus kiépítés megtagadja következményei?

Az áttelepítés befejezésekor Security Center nem tud biztonsági adatokat gyűjteni a virtuális gépről, és bizonyos biztonsági javaslatok és riasztások nem érhetők el. Ha letiltják, manuálisan telepítse a Microsoft monitoring agentet. A [kilépéskor tekintse meg a javasolt lépéseket](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Mik azok a javasolt lépéseket, amikor az Automatikus kiépítés megtagadja?

Manuálisan telepítse a Microsoft monitoring Agent bővítményt, hogy Security Center biztonsági adatokat gyűjtsön a virtuális gépekről, és javaslatokat és riasztásokat nyújtson. A telepítéssel kapcsolatos útmutatásért tekintse meg a [Windows rendszerű virtuális gép](../virtual-machines/extensions/oms-windows.md) vagy az ügynök telepítése a [linuxos virtuális géphez](../virtual-machines/extensions/oms-linux.md) című témakört.

Az ügynök csatlakozhat bármely meglévő egyéni munkaterületet, vagy a Security Center munkaterület létrehozása. Ha egy egyéni munkaterület nincs engedélyezve van, a "Security" vagy "SecurityCenterFree" megoldások, és a egy megoldást a alkalmazni kell. Az alkalmazáshoz válassza ki az egyéni munkaterületet vagy előfizetést, és alkalmazza a díjszabási szintet a **biztonsági házirend – díjszabási** csomag lapon.

   ![Tarifacsomag][1]

A Security Center lehetővé teszi a munkaterületen a kijelölt tarifacsomag alapján a megfelelő megoldást.


## Hogyan eltávolítja a Security Center által telepített OMS-bővítményeket?<a name="remove-oms"></a>

Manuálisan távolítsa el a Microsoft Monitoring Agent. Ez nem ajánlott, mivel a Security Center javaslatait és riasztások korlátozza.

> [!NOTE]
> Ha az adatgyűjtés engedélyezve van, a Security Center újratelepíti az ügynök után távolítsa el azt.  Le kell tiltania az adatgyűjtés az ügynök manuális eltávolítása előtt. Lásd: Hogyan az ügynök automatikus telepítésének leállítása és a munkaterület létrehozása? az adatgyűjtés letiltására vonatkozó utasítások.

Az ügynök manuális eltávolításához:

1.  A portálon nyissa meg **log Analytics**.

1.  A Log Analytics lapon válasszon ki egy munkaterületet:

1.  Válassza ki azokat a virtuális gépeket, amelyeket nem szeretne figyelni, és válassza a **Leválasztás**lehetőséget.

   ![Az ügynök eltávolítása][3]

> [!NOTE]
> Ha egy Linux rendszerű virtuális gép már rendelkezik egy nem kiterjesztésű OMS-ügynökkel, a bővítmény eltávolítása eltávolítja az ügynököt is, és újra kell telepítenie.


## <a name="how-do-i-disable-data-collection"></a>Hogyan tilthatom le az adatgyűjtést?

Alapértelmezés szerint az Automatikus kiépítés le van. Letilthatja az Automatikus kiépítés erőforrásokból bármikor ezt a beállítást, a biztonsági szabályzatban kikapcsolásával. Az automatikus kiépítés kifejezetten ajánlott ahhoz, hogy biztonsági riasztásokat és javaslatokat kapjon a rendszerfrissítésekkel, az operációs rendszer sebezhetőségével és az Endpoint Protection szolgáltatással kapcsolatban.

Az adatgyűjtés letiltásához [Jelentkezzen be a Azure Portalba](https://portal.azure.com), válassza a **Tallózás**lehetőséget, válassza a **Security Center**lehetőséget, majd válassza a **házirend kiválasztása**lehetőséget. Válassza ki azt az előfizetést, amelynél le szeretné tiltani az automatikus kiépítést. Amikor kiválaszt egy előfizetési **biztonsági házirendet** , megnyílik az adatgyűjtés. Az **automatikus kiépítés**alatt válassza ki a **ki**lehetőséget.


## <a name="how-do-i-enable-data-collection"></a>Hogyan engedélyezhetem az adatgyűjtés?

Engedélyezheti az adatgyűjtést az Azure-előfizetés biztonsági szabályzatában. Az adatgyűjtés engedélyezéséhez. [Jelentkezzen be a Azure Portalba](https://portal.azure.com), válassza a **Tallózás**lehetőséget, válassza a **Security Center**lehetőséget, és válassza a **biztonsági szabályzat**lehetőséget. Automatikus kiépítés engedélyezése kívánt előfizetés kiválasztásához. Amikor kiválaszt egy előfizetési **biztonsági házirendet** , megnyílik az adatgyűjtés. Az **automatikus kiépítés**területen válassza **a**be lehetőséget.


## <a name="what-happens-when-data-collection-is-enabled"></a>Mi történik, ha az adatgyűjtés engedélyezve van?

Ha az Automatikus kiépítés engedélyezve van, a Security Center létrehozza a Microsoft Monitoring Agentet az összes támogatott Azure-beli és újonnan létrehozott. Az automatikus kiépítés ajánlott, de a manuális ügynök telepítése is elérhető. [Tudnivalók a Microsoft Monitoring Agent bővítmény telepítéséről](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Az ügynök engedélyezi a 4688-es folyamat-létrehozási eseményt és a 4688-es eseményen belüli *parancssori* mezőt. A virtuális gépen új folyamatok EventLog által rögzített és a Security Center észlelési szolgáltatások által figyelt. Az egyes új folyamatokra vonatkozóan rögzített részletekkel kapcsolatos további információkért lásd: [a Leírás mezői a 4688-ben](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Az ügynök is a virtuális gépen 4688 eseményeit, és tárolja a keresés.

Az ügynök az [adaptív alkalmazások vezérlőinek](security-center-adaptive-application.md)adatgyűjtését is lehetővé teszi, Security Center a helyi AppLocker-házirendet naplózási módban konfigurálja az összes alkalmazás engedélyezéséhez. Ez a szabályzat az AppLockert az események előállítására fogja használni, amelyeket a Security Center gyűjt és használ. Fontos megjegyezni, hogy ez a szabályzat minden olyan gépeken, amelyeken már van egy konfigurált AppLocker-házirendek nem lesznek konfigurálva. 

Ha a Security Center gyanús tevékenységet észlel a virtuális gépen, a rendszer e-mailben értesíti az ügyfelet, ha a [biztonsági kapcsolattartási adatokat](security-center-provide-security-contact-details.md) megadták. Egy riasztás akkor is a Security Center biztonsági riasztások irányítópult látható.


## <a name="will-security-center-work-using-an-oms-gateway"></a>A Security Center OMS-átjáró használatával fog működni?

Igen. Azure Security Center kihasználja a Azure Monitor az Azure-beli virtuális gépekről és kiszolgálókról származó adatok gyűjtésére a Microsoft monitoring Agent használatával.
Az adatok gyűjtéséhez az egyes virtuális gépeknek és kiszolgálóknak HTTPS használatával kell csatlakozniuk az internethez. A csatlakozás lehet közvetlen, proxy használatával vagy a [OMS-átjárón](../azure-monitor/platform/gateway.md)keresztül.


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>A Monitoring Agent befolyásolja a kiszolgáló teljesítményét?

Az ügynök egy névleges mennyiségű rendszer-erőforrásokat használ fel, és teljesítményére gyakorolt minimális hatás kell rendelkeznie. A teljesítményre gyakorolt hatásra, valamint az ügynökre és a bővítményre vonatkozó további információkért tekintse meg a [tervezési és üzemeltetési útmutatót](security-center-planning-and-operations-guide.md#data-collection-and-storage).


## <a name="where-is-my-data-stored"></a>Hol tárolják az adataimat?

Ettől az ügynöktől gyűjtött adatok tárolva van vagy egy meglévő Log Analytics-munkaterületet az Ön előfizetéséhez rendelve, vagy egy új munkaterületet. További információkért lásd az [adatbiztonságot](security-center-data-security.md)ismertető témakört.


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png