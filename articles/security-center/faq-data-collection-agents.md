---
title: Az Azure Security Center – gyakori kérdések – adatgyűjtés és ügynökök
description: Gyakori kérdések az Azure Security Center adatgyűjtésével, ügynökeivel és munkaterületeivel kapcsolatban, amely egy olyan termék, amely segít a fenyegetések megelőzésében, észlelésében és az azokra való reagálásban
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
ms.openlocfilehash: 0dbad1a94479430426dae47df7ca3a3ecd9dc980
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436198"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>GYAKRAN FELTETT KÉRDÉSEK – Az adatgyűjtésre, az ügynökökre és a munkaterületekre vonatkozó kérdések

A Security Center adatokat gyűjt az Azure virtuális gépekről (VM-ek), a virtuálisgép-méretezési csoportokból, az IaaS-tárolókból és a nem Azure-beli számítógépekről (beleértve a helyszíni gépeket is) a biztonsági rések és fenyegetések figyeléséhez. Az adatok gyűjtése a Log Analytics-ügynök használatával történik, amely beolvassa a különböző biztonsággal kapcsolatos konfigurációkat és eseménynaplókat a gépről, és átmásolja az adatokat a munkaterületre elemzésre.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Kiszámláztam az Azure Monitor-naplókat a Security Center által létrehozott munkaterületeken?

Nem. A Security Center által létrehozott munkaterületek, miközben konfigurálva vannak az Azure Monitor-naplók csomópontonkénti számlázáshoz, nem merülnek fel az Azure Monitor naplóinak díjai. A Security Center számlázása mindig a Security Center biztonsági házirendje és a munkaterületre telepített megoldásokon alapul:

- **Ingyenes szint** – A Security Center engedélyezi a "SecurityCenterFree" megoldást az alapértelmezett munkaterületen. Az ingyenes szintért nem kell fizetnie.

- **Standard szint** – A Security Center engedélyezi a "Biztonság" megoldást az alapértelmezett munkaterületen.

Az árképzésről a [Security Center díjszabása](https://azure.microsoft.com/pricing/details/security-center/)című témakörben talál további információt.

> [!NOTE]
> A Security Center által létrehozott munkaterületek naplóelemzési tarifaszintje nincs hatással a Security Center számlázására.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>Mi minősíti a virtuális gép a Log Analytics-ügynök telepítésének automatikus kiépítését?

A Windows vagy Linux iaaS virtuális gépek akkor jogosultak, ha:

- A Log Analytics-ügynök bővítmény jelenleg nincs telepítve a virtuális gépre.
- A virtuális gép futó állapotban van.
- A Windows vagy Linux [Azure virtuálisgép-ügynök](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) telepítve van.
- A virtuális gép nem használható olyan eszközként, mint például a webalkalmazás tűzfala vagy a következő generációs tűzfal.


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Törölhetem a Security Center által létrehozott alapértelmezett munkaterületeket?

**Az alapértelmezett munkaterület törlése nem ajánlott.** A Security Center az alapértelmezett munkaterületeket használja a virtuális gépek biztonsági adatainak tárolására. Ha töröl egy munkaterületet, a Security Center nem tudja összegyűjteni ezeket az adatokat, és néhány biztonsági javaslat és riasztás nem érhető el.

A helyreállításhoz távolítsa el a Log Analytics-ügynök a törölt munkaterülethez csatlakoztatott virtuális gépeken. A Security Center újratelepíti az ügynököt, és új alapértelmezett munkaterületeket hoz létre.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Hogyan használhatom a meglévő Log Analytics-munkaterületet?

Kiválaszthat egy meglévő Log Analytics-munkaterületet a Security Center által gyűjtött adatok tárolására. A meglévő Log Analytics-munkaterület használata:

- A munkaterületet a kiválasztott Azure-előfizetéshez kell társtársosan kezelni.
- Legalább olvasási engedéllyel kell rendelkeznie a munkaterület eléréséhez.

Meglévő Log Analytics-munkaterület kiválasztása:

1. A **Biztonsági házirend – Adatgyűjtés**csoportban válassza a Másik munkaterület **használata**lehetőséget.

    ![Másik munkaterület használata][4]

1. A legördülő menüben válassza ki az összegyűjtött adatok tárolására kívánt munkaterületet.

    > [!NOTE]
    > A legördülő menüben csak azok a munkaterületek jelennek meg, amelyekhez hozzáférése van, és amelyek az Azure-előfizetésben találhatók.

1. Kattintson a **Mentés** gombra. A rendszer megkérdezi, hogy szeretné-e újrakonfigurálni a figyelt virtuális gépeket.

    - Válassza a **Nem** lehetőséget, ha azt szeretné, hogy az új munkaterületi beállítások **csak az új virtuális gépekre vonatkozzanak.** Az új munkaterület-beállítások csak az új ügynöktelepítésekre vonatkoznak; újonnan felderített virtuális gépek, amelyek nem rendelkeznek a Log Analytics-ügynök telepítve.
    - Válassza az **Igen** lehetőséget, ha azt szeretné, hogy az új munkaterületi beállítások **az összes virtuális gépre érvényesek legyenek.** Emellett minden virtuális gép csatlakozik a Security Center létrehozott munkaterület újracsatlakozik az új cél munkaterületre.

    > [!NOTE]
    > Ha az **Igen**lehetőséget választja, ne törölje a Security Center által létrehozott munkaterületeket, amíg az összes virtuális gépet újra nem csatlakoztatta az új célmunkaterülethez. Ez a művelet sikertelen, ha egy munkaterületet túl korán törölnek.

    - A művelet megszakításához válassza a **Mégse gombot.**

## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>Mi a teendő, ha a Log Analytics-ügynök már telepítve van bővítményként a virtuális gépen?<a name="mmaextensioninstalled"></a>

Ha a figyelőügynök bővítményként van telepítve, a bővítmény konfigurációja lehetővé teszi, hogy csak egyetlen munkaterületre jelentsen. A Security Center nem bírálja felül a felhasználói munkaterületekkel létesített meglévő kapcsolatokat. A Security Center a virtuális gép biztonsági adatait egy már csatlakoztatott munkaterületen tárolja, feltéve, hogy a "Security" vagy a "SecurityCenterFree" megoldás telepítve van. A Security Center a bővítményverziót a folyamat legújabb verziójára frissítheti.

További információ: [Automatikus kiépítés meglévő ügynöktelepítés esetén.](security-center-enable-data-collection.md#preexisting)



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>Mi a teendő, ha egy Log Analytics-ügynök közvetlenül telepítve van a számítógépen, de nem bővítményként (közvetlen ügynökként)?<a name="directagentinstalled"></a>

Ha a Log Analytics-ügynök közvetlenül a virtuális gépre van telepítve (nem Azure-bővítményként), a Security Center telepíti a Log Analytics-ügynök bővítményt, és frissítheti a Log Analytics-ügynököt a legújabb verzióra.

A telepített ügynök továbbra is jelentést tesz a már konfigurált munkaterület(ek)nek, továbbá a Security Centerben konfigurált munkaterületnek is jelentést tesz (a Több-homing szolgáltatás windowsos gépeken támogatott).

Ha a konfigurált munkaterület egy felhasználói munkaterület (nem a Security Center alapértelmezett munkaterülete), telepítenie kell a "Security/"SecurityCenterFree" megoldást a Security Center számára, hogy megkezdhesse az események feldolgozását a virtuális gépekről és az adott munkaterületre jelentést tevő számítógépekről.

Linux-gépek esetében az ügynök több-homing még nem támogatott - ezért ha egy meglévő ügynök telepítése észlelhető, automatikus kiépítés nem történik meg, és a gép konfigurációja nem módosul.

2019. március 17-e előtt a Security Centerbe beszállt meglévő gépek esetében, amikor egy meglévő ügynököt észlel, a Log Analytics-ügynökbővítmény nem lesz telepítve, és a gép nem lesz hatással. Ezekről a gépekről tekintse meg a "Monitoring agent health issues on your machines" című javaslat az ügynök telepítési problémáinak megoldásához ezeken a gépeken

További információ: A következő [szakasz: Mi történik, ha a System Center Operations Manager vagy az OMS közvetlen ügynök már telepítve van a virtuális gépen?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>Mi a teendő, ha egy System Center Operations Manager-ügynök már telepítve van a virtuális gépre?<a name="scomomsinstalled"></a>

A Security Center a Log Analytics-ügynök bővítményt a meglévő System Center Operations Manager-ügynök mellé telepíti. A meglévő ügynök továbbra is a rendszerközpont Operations Manager kiszolgálójának a szokásos módon jelent. Vegye figyelembe, hogy az Operations Manager-ügynök és a Log Analytics-ügynök közös futásidejű kódtárak, amelyek a folyamat során a legújabb verzióra frissülnek. Megjegyzés – Ha az Operations Manager-ügynök 2012-es verziója telepítve van, ne kapcsolja be az automatikus kiépítést (a felügyeleti képességek elveszhetnek, ha az Operations Manager-kiszolgáló is 2012-es verziójú).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Milyen hatással van a kiterjesztések eltávolítása?

Ha eltávolítja a Microsoft Figyelési bővítményt, a Security Center nem tud biztonsági adatokat gyűjteni a virtuális gépről, és néhány biztonsági javaslat és riasztás nem érhető el. 24 órán belül a Security Center megállapítja, hogy a virtuális gép hiányzik a bővítmény, és újratelepíti a bővítményt.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Hogyan állíthatom le az automatikus ügynöktelepítést és a munkaterület létrehozását?

Kikapcsolhatja az előfizetések automatikus kiépítését a biztonsági házirendben, de ez nem ajánlott. Az automatikus kiépítés kikapcsolása korlátozza a Biztonsági központ ajánlásait és riasztásait. Az automatikus kiépítés letiltása:

1. Ha az előfizetés a standard szinthez van konfigurálva, nyissa meg az adott előfizetés biztonsági szabályzatát, és válassza ki az **ingyenes** szintet.

   ![Tarifacsomag][1]

1. Ezután kapcsolja ki az automatikus kiépítést a **Biztonsági házirend – Adatgyűjtés** lapon a **Ki** lehetőség kiválasztásával.
   ![Adatgyűjtés][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Lekell iratkoznom az automatikus ügynöktelepítésről és a munkaterület létrehozásáról?

> [!NOTE]
> Győződjön meg arról, hogy tekintse át a szakaszok [Milyen következményekkel jár a leiratkozás?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) és [az ajánlott lépéseket, ha leiratkozik,](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) ha úgy dönt, hogy leiratkozik az automatikus kiépítés.

Ha az alábbiak vonatkoznak Önre, érdemes leiratkoznia az automatikus kiépítésről:

- A Security Center által imitátus automatikus telepítése a teljes előfizetésre vonatkozik. Az automatikus telepítés nem alkalmazható a virtuális gépek egy részhalmazára. Ha vannak olyan kritikus virtuális gépek, amelyek nem telepíthetők a Log Analytics-ügynökkel, akkor le kell tiltania az automatikus kiépítést.
- A Log Analytics-ügynök bővítmény telepítése frissíti az ügynök verzióját. Ez egy közvetlen ügynökre és egy System Center Operations Manager-ügynökre vonatkozik (az utóbbiban az Operations Manager és a Log Analytics ügynök közös futásidejű kódtárakon osztozik , amelyek a folyamat során frissülnek). Ha a telepített Operations Manager-ügynök 2012-es verziójú és frissített, a kezelhetőségi képességek elveszhetnek, ha az Operations Manager-kiszolgáló is 2012-es verziójú. Fontolja meg az automatikus kiépítés letiltását, ha a telepített Operations Manager-ügynök 2012-es verziójú.
- Ha az előfizetésen kívüli egyéni munkaterülettel (egy központi munkaterülettel) rendelkezik, akkor le kell tiltania az automatikus kiépítést. Manuálisan telepítheti a Log Analytics ügynök bővítményt, és csatlakoztathatja a munkaterületet anélkül, hogy a Security Center felülbírálta volna a kapcsolatot.
- Ha el szeretné kerülni több munkaterület létrehozását előfizetésenként, és saját egyéni munkaterülettel rendelkezik az előfizetésen belül, akkor két lehetősége van:

   1. Leiratkozhat az automatikus kiépítésről. Az áttelepítés után állítsa be az alapértelmezett munkaterületi beállításokat a [Hogyan használhatom a meglévő Log Analytics-munkaterületet?](#how-can-i-use-my-existing-log-analytics-workspace)

   1. Vagy engedélyezheti az áttelepítés befejezését, a Log Analytics-ügynök a virtuális gépekre való telepítését, valamint a létrehozott munkaterülethez csatlakoztatott virtuális gépeket. Ezután válassza ki a saját egyéni munkaterületet az alapértelmezett munkaterületi beállítás beállításával, és engedélyezve a már telepített ügynökök újrakonfigurálása. További információ: [Hogyan használhatom a meglévő Log Analytics-munkaterületet?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Milyen következményekkel jár az automatikus kiépítésből való leiratkozás?

Az áttelepítés befejeződése után a Security Center nem tud biztonsági adatokat gyűjteni a virtuális gépről, és néhány biztonsági javaslat és riasztás nem érhető el. Ha leiratkozik, telepítse manuálisan a Log Analytics-ügynököt. A [leiratkozáskor](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)tekintse meg az ajánlott lépéseket.


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Melyek az ajánlott lépések az automatikus kiépítés letiltásakor?

Manuálisan telepítse a Log Analytics ügynök bővítményt, hogy a Security Center biztonsági adatokat gyűjthessen a virtuális gépekről, és javaslatokat és riasztásokat adjon meg. A telepítéssel kapcsolatos útmutatásért tekintse meg [a Windows virtuális gép ügynöktelepítését](../virtual-machines/extensions/oms-windows.md) vagy [linuxos virtuális gép ügynöktelepítését.](../virtual-machines/extensions/oms-linux.md)

Az ügynököt bármely meglévő egyéni munkaterülethez vagy a Security Center által létrehozott munkaterülethez csatlakoztathatja. Ha egy egyéni munkaterületen nincs engedélyezve a "Security" vagy a "SecurityCenterFree" megoldás, akkor megoldást kell alkalmaznia. Az alkalmazáshoz válassza ki az egyéni munkaterületet vagy előfizetést, és alkalmazzon egy tarifacsomagot a **Biztonsági házirend – Tarifacsomag** lapon keresztül.

   ![Tarifacsomag][1]

A Security Center a kiválasztott tarifacsomag alapján engedélyezi a megfelelő megoldást a munkaterületen.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Hogyan távolíthatom el a Security Center által telepített OMS-bővítményeket?<a name="remove-oms"></a>

Manuálisan eltávolíthatja a Log Analytics-ügynököt. Ez nem ajánlott, mivel korlátozza a Security Center ajánlásait és riasztásait.

> [!NOTE]
> Ha az adatgyűjtés engedélyezve van, a Security Center az eltávolítás után újratelepíti az ügynököt.  Az ügynök manuális eltávolítása előtt le kell tiltania az adatgyűjtést. Lásd: Hogyan állíthatom le az automatikus ügynöktelepítést és a munkaterület létrehozását? az adatgyűjtés letiltására vonatkozó utasításokért.

Az ügynök manuális eltávolítása:

1.    A portálon nyissa meg a **Log Analytics**szolgáltatást.

1.    A Log Analytics lapon válasszon ki egy munkaterületet:

1.    Jelölje ki azokat a virtuális gépeket, amelyeket nem szeretne figyelni, és válassza a **Kapcsolat bontása**lehetőséget.

   ![Az ügynök eltávolítása][3]

> [!NOTE]
> Ha egy Linux virtuális gép már rendelkezik egy nem bővítmény OMS-ügynök, eltávolítja a bővítmény eltávolítja az ügynök is, és akkor újra kell telepítenie.


## <a name="how-do-i-disable-data-collection"></a>Hogyan tilthatom le az adatgyűjtést?

Az automatikus kiépítés alapértelmezés szerint ki van kapcsolva. Az erőforrásokból való automatikus kiépítést bármikor letilthatja, ha a biztonsági házirendben kikapcsolja ezt a beállítást. Automatikus kiépítése erősen ajánlott annak érdekében, hogy a biztonsági riasztások és javaslatok a rendszerfrissítések, operációs rendszer biztonsági rések, és a végpont védelem.

Az adatgyűjtés letiltásához [jelentkezzen be az Azure Portalra,](https://portal.azure.com)válassza a **Tallózás**gombot, a **Security Center**lehetőséget, és válassza a **Házirend kiválasztása**lehetőséget. Válassza ki azt az előfizetést, amelynél le szeretné tiltani az automatikus kiépítést. Amikor kiválaszt egy előfizetést **Biztonsági házirend – Megnyílik az adatgyűjtés.** Az **Automatikus kiépítés**csoportban válassza **a Ki**lehetőséget.


## <a name="how-do-i-enable-data-collection"></a>Hogyan engedélyezhetem az adatgyűjtést?

Engedélyezheti az Azure-előfizetés adatgyűjtését a Biztonsági szabályzatban. Az adatgyűjtés engedélyezése. [Jelentkezzen be az Azure Portalra,](https://portal.azure.com)válassza a **Tallózás**gombot, válassza a **Biztonsági központ**lehetőséget, és válassza a **Biztonsági házirend**lehetőséget. Válassza ki az automatikus kiépítést engedélyezni kívánt előfizetést. Amikor kiválaszt egy előfizetést **Biztonsági házirend – Megnyílik az adatgyűjtés.** Az **Automatikus kiépítés**csoportban válassza **a Be**lehetőséget.


## <a name="what-happens-when-data-collection-is-enabled"></a>Mi történik, ha engedélyezve van az adatgyűjtés?

Ha az automatikus kiépítés engedélyezve van, a Security Center a Log Analytics-ügynököt az összes támogatott Azure-virtuális gépre és a létrehozott újgépekre vonatkozóan lehetővé teszi. Automatikus kiépítés ajánlott, de kézi ügynök telepítése is rendelkezésre áll. [További információ a Log Analytics-ügynökbővítmény telepítéséről.](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) 

Az ügynök engedélyezi a folyamat létrehozási esemény 4688 és a *CommandLine* mező belül esemény 4688. A virtuális gépen létrehozott új folyamatokat az EventLog rögzíti, és a Security Center észlelési szolgáltatásai figyelik. Az egyes új folyamatokhoz rögzített részletekről a [4688 leírásámezőben](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields)talál további információt. Az ügynök is gyűjti a virtuális gépen létrehozott 4688-as eseményeket, és tárolja őket a keresésben.

Az ügynök lehetővé teszi az [adaptív alkalmazásvezérlők](security-center-adaptive-application.md)adatgyűjtését is, a Security Center egy helyi AppLocker-házirendet konfigurál naplózási módban, hogy minden alkalmazás lehetővé tegye. Ez a szabályzat hatására az AppLocker eseményeket hoz létre, amelyeket a Security Center gyűjt és használ fel. Fontos megjegyezni, hogy ez a házirend nem lesz konfigurálva olyan gépeken, amelyeken már van konfigurált AppLocker-házirend. 

Ha a Security Center gyanús tevékenységet észlel a virtuális gépen, az ügyfél e-mailben értesítést kap, ha [biztonsági kapcsolattartási adatokat](security-center-provide-security-contact-details.md) adtak meg. A biztonsági központ biztonsági riasztások irányítópultján is látható egy riasztás.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Működik a Security Center oms átjáró használatával?

Igen. Az Azure Security Center az Azure Monitor segítségével gyűjt adatokat az Azure virtuális gépekről és kiszolgálókról a Log Analytics-ügynök használatával.
Az adatok gyűjtéséhez minden virtuális gépnek és kiszolgálónak https használatával kell csatlakoznia az internethez. A kapcsolat lehet közvetlen, proxy használatával vagy az [OMS átjárón](../azure-monitor/platform/gateway.md)keresztül.


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>A figyelőügynök hatással van a kiszolgálóim teljesítményére?

Az ügynök névleges mennyiségű rendszererőforrást használ fel, és kevés hatással lehet a teljesítményre. A teljesítményre gyakorolt hatásról, valamint az ügynökről és a bővítményről a [tervezési és üzemeltetési útmutatóban](security-center-planning-and-operations-guide.md#data-collection-and-storage)talál további információt.


## <a name="where-is-my-data-stored"></a>Hol vannak tárolva az adataim?

Az ügynöktől gyűjtött adatokat az előfizetéshez társított meglévő Log Analytics-munkaterület vagy egy új munkaterület tárolja. További információ: [Data Security](security-center-data-security.md).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png
