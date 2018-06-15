---
title: Gyakori kérdések (GYIK) az Azure Security Center |} Microsoft Docs
description: Ez a GYIK az Azure Security Center kapcsolatos kérdésekre ad választ.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: terrylan
ms.openlocfilehash: d5a9f2ba68574ba8cb99b01ce426ec77a5eecd3d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363961"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Azure Security Center – gyakori kérdések
Ez a GYIK az Azure Security Center, egy szolgáltatás, amely segít a megakadályozása, észlelésében és kezelésében fenyegetések láthatóság növelésével és a Microsoft Azure-erőforrások védelmét kapcsolatos kérdésekre ad választ.

> [!NOTE]
> 2017 júniusának elejétől kezdve a Security Center a Microsoft Monitoring Agent használatával gyűjti össze és tárolja az adatokat. További tudnivalókért lásd: [Azure Security Center Platform áttelepítési](security-center-platform-migration.md). A jelen cikkben található információk a Security Center a Microsoft Monitoring Agentre való váltás után elérhető funkcióit ismertetik.
>
>

## <a name="general-questions"></a>Általános kérdések
### <a name="what-is-azure-security-center"></a>Mi az az Azure Security Center?
Az Azure Security Center magasabb fokú betekintést és szélesebb körű vezérlést tesz lehetővé az Azure-erőforrások fölött, így segít a fenyegetések megelőzésében, észlelésében és kezelésében. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

### <a name="how-do-i-get-azure-security-center"></a>Hogyan szerezhetek az Azure Security Center?
Az Azure Security Center a Microsoft Azure-előfizetés engedélyezve van, és elérhető a [Azure-portálon](https://azure.microsoft.com/features/azure-portal/). ([Jelentkezzen be a portálra](https://portal.azure.com), jelölje be **Tallózás**, és görgessen a **Security Center**).  

## <a name="billing"></a>Számlázás
### <a name="how-does-billing-work-for-azure-security-center"></a>Hogyan működik az Azure Security Center számlázási tevékenységeket?
A Security Center érhető el, a két réteg:

A **ingyenes szint** biztosít a biztonsági állapotát az Azure-erőforrások, az alapvető biztonsági házirend, a biztonsági javaslatok és a integrációs láthatósága biztonsági termékeinek és szolgáltatásainak partnertől.

A **Standard csomagra** hozzáadja az advanced threat az észlelési képességek, beleértve az eszközintelligencia, viselkedéssel összefüggő elemzésekkel, anomáliadetektálás, biztonsági incidensek fenyegetés, és a fenyegetés szolgáló jelentések. A Standard szint az első 60 napban ingyenesen használható. Választja 60 napon túl a szolgáltatás használatának folytatásához, automatikusan először a szolgáltatás igénybe.  Frissítéséhez válassza [Tarifacsomagot](https://docs.microsoft.com/azure/security-center/security-center-pricing) a biztonsági házirendben.

## <a name="permissions"></a>Engedélyek
Az Azure Security Center által használt [szerepköralapú hozzáférés-vezérlést (RBAC)](../role-based-access-control/role-assignments-portal.md), amely biztosítja [beépített szerepkörök](../role-based-access-control/built-in-roles.md) , hogy a felhasználók, csoportok és az Azure rendelhető.

A Security Center értékeli a konfigurációs az erőforrások biztonsági problémák és biztonsági rések azonosítása. A biztonsági központban csak akkor jelenik meg egy erőforrás tulajdonos, közreműködő vagy olvasó szerepkört az előfizetés vagy az erőforráscsoportot, amelyhez tartozik egy erőforrás hozzárendelése esetén kapcsolatos adatokat.

Lásd: [engedélyek az Azure Security Centerben](security-center-permissions.md) tudhat meg többet a szerepkörök és a Security Center engedélyezett műveletek.

## <a name="data-collection"></a>Adatgyűjtés
A Security Center az Azure virtuális gépek (VM) és a nem Azure számítógépek számára figyeli, hogy a biztonsági réseket és a fenyegetések adatait gyűjti. Az adatgyűjtés a Microsoft Monitoring Agent segítségével történik, amely a biztonsághoz kapcsolódó különböző konfigurációkat és eseménynaplókat olvas be a gépről, és elemzés céljából átmásolja az adatokat az Ön munkaterületére.

### <a name="how-do-i-disable-data-collection"></a>Hogyan tiltsa le az adatgyűjtő?
Automatikus kiépítés alapértelmezés szerint van kapcsolva. Automatikus kiépítés erőforrásokból bármikor ezt a beállítást, a biztonsági szabályzatban kikapcsolásával letilthatja. Automatikus kiépítés erősen ajánlott biztonsági riasztások és javaslatok Rendszerfrissítések, az operációs rendszer biztonsági réseket és az endpoint protection eléréséhez.

Adatok gyűjtésének a letiltása [jelentkezzen be az Azure-portálon](https://portal.azure.com), jelölje be **Tallózás**, jelölje be **Security Center**, és válassza ki **-szabályzat kiválasztásával**. Válassza ki azt az előfizetést, amelynél le szeretné tiltani az automatikus kiépítést. Ha bejelöli előfizetés **biztonsági házirend - adatok gyűjtése** megnyitása. A **automatikus kiépítés**, jelölje be **ki**.

### <a name="how-do-i-enable-data-collection"></a>Hogyan engedélyezhető az adatok gyűjtését?
Adatgyűjtés engedélyezheti a biztonsági szabályzatban Azure-előfizetése. Szeretné az adatgyűjtést. [Jelentkezzen be az Azure-portálon](https://portal.azure.com), jelölje be **Tallózás**, jelölje be **Security Center**, és válassza ki **biztonsági házirend**. Válassza ki az előfizetést, amely automatikus kiépítés engedélyezéséhez. Ha bejelöli előfizetés **biztonsági házirend - adatok gyűjtése** megnyitása. A **automatikus kiépítés**, jelölje be **a**.

### <a name="what-happens-when-data-collection-is-enabled"></a>Mi történik, ha engedélyezve van az adatok gyűjtését?
Ha automatikus kiépítés engedélyezve van, a Security Center kiosztja a Microsoft Monitoring Agent összes támogatott Azure virtuális gépek és bármely újakat létrehozott. Automatikus kiépítés erősen ajánlott, de a kézzel történő ügynöktelepítést is rendelkezésre áll. [Megtudhatja, hogyan telepítse a Microsoft Monitoring Agent bővítmény](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension).

Az ügynök lehetővé teszi, hogy a folyamat létrehozásának esemény 4688 és a *CommandLine* eseményből 4688 mezőjét. A virtuális Gépre létrehozott új folyamatok EventLog által rögzített, és a Security Center észlelési szolgáltatások által figyelendő. Információk a részletes adatait rögzíti az összes új folyamat: [4688 leírás mezőiben](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Az ügynök is létre a virtuális gép 4688 eseményeit, és tárolja őket a keresés.

Ha a Security Center a virtuális gép gyanús tevékenységet észlel, az ügyfél értesítést e-mailben, ha a [biztonsági kapcsolattartási adatokat](security-center-provide-security-contact-details.md) adtak meg. Riasztás egyben a Security Center biztonsági riasztások irányítópulton látható.

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Hatással lehet a kiszolgáló teljesítményét a Figyelőügynök?
Az ügynök névleges mennyisége rendszererőforrásokat fogyaszt, és teljesítményére gyakorolt minimális hatás kell rendelkeznie. A teljesítményre gyakorolt hatás és az ügynök és a bővítmény további információkért lásd: a [tervezési és műveletek útmutató](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Hol tárolják az adataimat?
Ez az ügynök gyűjtött adatokat vagy egy meglévő Naplóelemzési munkaterület az Ön előfizetéséhez rendelve, vagy új munkaterület tárolja. További információkért lásd: [adatbiztonság](security-center-data-security.md).

## <a name="using-azure-security-center"></a>Az Azure Security Centerben
### <a name="what-is-a-security-policy"></a>Mi az az olyan biztonsági házirenddel?
A biztonsági házirend határozza meg az adott előfizetésen belüli erőforrásokon ajánljuk, hogy meg. Az Azure Security Centerben, szabályzatok készítése az Azure-előfizetések a vállalat biztonsági követelményeinek, és az alkalmazások típusának vagy az egyes előfizetések adatainak érzékenysége alapján.

A biztonsági házirendek az Azure Security Center meghajtó biztonsági javaslatok és figyelésének engedélyezve van. Biztonsági házirendek kapcsolatos további információkért lásd: [biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Ki módosíthatja a biztonsági házirend?
Módosíthatja a biztonsági szabályzatot, a biztonsági rendszergazda vagy a tulajdonosának vagy Közreműködőjének előfizetésben kell lennie.

A biztonsági házirend konfigurálási módjával kapcsolatban a [biztonsági szabályzatok beállítása az Azure Security Center](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>Mi az a biztonsági ajánlás olyan környezetekben?
Az Azure Security Center elemzi az Azure-erőforrások biztonsági állapotát. Amikor a potenciális biztonsági hiányosságokat azonosít, javaslatok jönnek létre. A javaslatok végigvezeti Önt a szükséges ellenőrzési folyamatán. Például a következők:

* Kártevőirtó azonosításához, és távolítsa el a kártevő szoftverek telepítése
* [Hálózati biztonsági csoportok](../virtual-network/security-overview.md) és a virtuális gépek forgalmának ellenőrzésére vonatkozó szabályok
* A webes alkalmazások célzó támadások elleni védelemre a webalkalmazási tűzfal kiépítését
* Hiányzó rendszerfrissítések telepítése
* Az operációs rendszer azon konfigurációinak kezelése, amelyek nem felelnek meg a javasolt alapkonfigurációknak

Itt csak azok a javaslatok, amelyek engedélyezve vannak a biztonsági házirend látható.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Hogyan tekinthető meg a saját Azure-erőforrások aktuális biztonsági állapotát?
A **Security Center áttekintése** panel megjeleníti a számítási, hálózati, tárolási és adatok és alkalmazások szerinti bontásban környezet általános biztonsági állapotát. Minden erőforrás rendelkezik egy kijelző ábrázoló Ha azonosított potenciális biztonsági hiányosságok. Mindegyik mozaiknál kattintva megjelenik az erőforrást az előfizetésében leltárt együtt Security Center által azonosított biztonsági problémák listáját.

### <a name="what-triggers-a-security-alert"></a>Mi elindítja a biztonsági riasztások?
Az Azure Security Center automatikusan gyűjti, elemzi és biztosítók az Azure-erőforrások, a hálózati és a partneri megoldások, például kártevőirtó- és tűzfalak naplóadatait. Fenyegetések észlelése esetén a központ biztonsági riasztást hoz létre. Példák fenyegetés észlelésére:

* Feltört virtuális gépek, amelyek kártékonyként azonosított IP-címekkel kommunikálnak
* Windows hibajelentés észlelt speciális kártevő
* Virtuális gépek elleni, a teljes kipróbálás módszerén alapuló támadások
* Integrált biztonsági partnermegoldások például kártevőirtó vagy webalkalmazási tűzfalak biztonsági riasztásai

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Mi az a különbség a között fenyegetéseket észlelt, és riasztást kap a Microsoft Security Response Center és az Azure Security Center által?
A Microsoft biztonsági válasz Center (MSRC) hajtja végre, válassza ki a biztonság ellenőrzése az Azure-hálózatot és az infrastruktúra, és fenyegetést eszközintelligencia és visszaélés panaszokat kapott harmadik felek számára. Ha MSRC elérhetővé válik, hogy az ügyféladatok egy törvénybe ütköző vagy jogosulatlan félnek elérhető-e, vagy az, hogy az ügyfél általi használata Azure nem felel meg a feltételeket az elfogadható használ, egy biztonsági incidens manager értesíti az ügyfél. Értesítési rendszerint azért fordul elő, egy e-mailt küld a biztonsági kapcsolatba lép, ha nincs megadva a biztonsági lépjen kapcsolatba az Azure Security Center vagy az Azure-előfizetés tulajdonosának megadott.

A Security Center az Azure-szolgáltatások, amely folyamatosan figyeli az ügyfél Azure-környezetéhez, és alkalmazza a analytics automatikus észlelése az esetlegesen kártékony tevékenység számos. Ezek az észlelések a Security Center irányítópultjának biztonsági riasztásként illesztett.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Mely Azure-erőforrások az Azure Security Center által figyelt?
Az Azure Security Center figyeli a következő Azure-erőforrások:

* Virtuális gépek (VM) (beleértve a [Felhőszolgáltatások](../cloud-services/cloud-services-choose-me.md))
* Azure virtuális hálózatok
* Azure SQL service
* Azure Storage-fiók
* Az Azure Web Apps (a [App Service Environment-környezet](../app-service/environment/intro.md))
* Például a webalkalmazási tűzfal virtuális gépek és az App Service Environment-környezet az Azure-előfizetésében integrált partneri megoldások

## <a name="virtual-machines"></a>Virtuális gépek
### <a name="what-types-of-virtual-machines-are-supported"></a>Milyen típusú virtuális gépek támogatottak?
Figyelés és javaslatok érhetők el a virtuális gépek (VM) használatával is létre a [klasszikus és Resource Manager üzembe helyezési modell](../azure-classic-rm.md).

Lásd: [az Azure Security Center által támogatott platformok](security-center-os-coverage.md) a támogatott platformok listáját.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Miért nem az Azure Security Center ismeri fel a kártevőirtó megoldást az Azure virtuális gépen?
Az Azure Security Center rendelkezik Azure-bővítményeket keresztül telepített kártevőirtó láthatósága. Például az a Security Center nincs, de a megadott lemezkép, vagy ha a saját folyamatok (például a konfigurációs felügyeleti rendszerekhez) használata a virtuális gépek telepített kártevőirtó előre telepített kártevőirtó észleli.

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Miért jelenik meg az üzenet "Hiányzó vizsgálati adatok" a virtuális géphez?
Ez az üzenet akkor jelenik meg, amikor nincs a virtuális gépek vizsgálat adat. Ellenőrző adatok feltöltése után az Azure Security Centerben engedélyezett adatgyűjtés némi időbe (kevesebb mint egy óra) is igénybe vehet. Ellenőrző adatok kezdeti sokaságát után jelenhet meg ezt az üzenetet, mert nincs minden vizsgálat adat, vagy nincs legutóbbi vizsgálat adat. Vizsgálat nem sikerült adatokkal feltölteni a virtuális gép leállított állapotban. Ezt az üzenetet is jelennek meg, ha az ellenőrző adatok nem nemrég (a Windows-ügynök, amely alapértelmezett értéke 30 napos adatmegőrzési) megfelelően van feltöltve.

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Milyen gyakran nem Security Center operációs rendszer biztonsági rések, rendszerfrissítések és vizsgálatára végpontvédelmi problémákkal?
A Tiltás késése a Security Center ellenőrzi a biztonsági frissítések, és problémákat:

- Operációs rendszer biztonsági beállításokkal – adatok frissül 48 órán belül
- Rendszerfrissítések – adatok frissül 24 órában
- Az Endpoint Protection állít – 8 órán belül adatok frissítése

A Security Center általában megvizsgálja az új adatokat óránként. A késés fenti értékei a legrosszabb esetben ha nincs a legutóbbi vizsgálat vagy a vizsgálat nem sikerült.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Miért jelenik meg az üzenet "Virtuálisgép-ügynök hiányzó?"
A virtuális gép ügynököt telepíteni kell a virtuális gépeken szeretné az adatgyűjtést. Az Azure Marketplace-ről üzembe helyezett virtuális gépek esetében a virtuálisgép-ügynök alapértelmezés szerint telepítve van. A Virtuálisgép-ügynök telepítésével más virtuális gépeken információkért lásd: a következő blogbejegyzésben található [ügynök és Virtuálisgép-bővítmények](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
