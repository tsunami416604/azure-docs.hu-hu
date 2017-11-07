---
title: "Gyakori kérdések (GYIK) az Azure Security Center |} Microsoft Docs"
description: "Ez a GYIK az Azure Security Center kapcsolatos kérdésekre ad választ."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: terrylan
ms.openlocfilehash: e71d407050f210c770bcac30259b9c2f2fb27aa3
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
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

A **Standard csomagra** hozzáadja az advanced threat az észlelési képességek, beleértve az eszközintelligencia, viselkedéssel összefüggő elemzésekkel, anomáliadetektálás, biztonsági incidensek fenyegetés, és a fenyegetés szolgáló jelentések. A Standard csomag szabad az első 60 nap. Választja 60 napon túl a szolgáltatás használatának folytatásához, automatikusan először a szolgáltatás igénybe.  Frissítéséhez válassza [Tarifacsomagot](https://docs.microsoft.com/azure/security-center/security-center-pricing) a biztonsági házirendben.

## <a name="permissions"></a>Engedélyek
Az Azure Security Center [szerepköralapú hozzáférés-vezérlést (RBAC)](../active-directory/role-based-access-control-configure.md) használ, amelynek [beépített szerepköreit](../active-directory/role-based-access-built-in-roles.md) az Azure különböző csoportjaihoz, felhasználóihoz és szolgáltatásaihoz rendelheti.

A Security Center értékeli a konfigurációs az erőforrások biztonsági problémák és biztonsági rések azonosítása. A biztonsági központban csak akkor jelenik meg egy erőforrás tulajdonos, közreműködő vagy olvasó szerepkört az előfizetés vagy az erőforráscsoportot, amelyhez tartozik egy erőforrás hozzárendelése esetén kapcsolatos adatokat.

Lásd: [engedélyek az Azure Security Centerben](security-center-permissions.md) tudhat meg többet a szerepkörök és a Security Center engedélyezett műveletek.

## <a name="data-collection"></a>Adatgyűjtés
A Security Center adatokat gyűjti össze a virtuális gépek felmérheti a biztonsági állapotát, adja meg a biztonsági javaslatok és riasztásokat fenyegetéseket. A Security Center első megnyitásakor adatgyűjtés engedélyezett az előfizetésében szereplő összes virtuális gépet. A Security Center házirend adatgyűjtés is engedélyezheti.

### <a name="how-do-i-disable-data-collection"></a>Hogyan tiltsa le az adatgyűjtő?
Ha az Azure Security Center ingyenes csomagot használ, letilthatja a virtuális gépek bármikor adatok gyűjtése. Adatgyűjtés a Standard csomagot előfizetések szükség. A biztonsági házirend előfizetésre vonatkozó adatok gyűjtésének letiltása ([Jelentkezzen be az Azure-portálon](https://portal.azure.com), jelölje be **Tallózás**, jelölje be **Security Center**, és válassza ki **házirend**.)  Előfizetés kiválasztásakor egy új panelen nyitja meg, és kikapcsolása lehetőséget biztosít **adatgyűjtés**.

### <a name="how-do-i-enable-data-collection"></a>Hogyan engedélyezhető az adatok gyűjtését?
Adatgyűjtés engedélyezheti a biztonsági szabályzatban Azure-előfizetése. Szeretné az adatgyűjtést. [Jelentkezzen be az Azure-portálon](https://portal.azure.com), jelölje be **Tallózás**, jelölje be **Security Center**, és válassza ki **házirend**. Állítsa be **adatgyűjtés** való **a**.

### <a name="what-happens-when-data-collection-is-enabled"></a>Mi történik, ha engedélyezve van az adatok gyűjtését?
Adatgyűjtés engedélyezésekor a rendszer a Microsoft Monitoring Agent automatikusan kiépített összes meglévő, és az előfizetés új támogatott virtuális gépek vannak telepítve.

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
* Konfigurálás [hálózati biztonsági csoportok](../virtual-network/virtual-networks-nsg.md) és a virtuális gépek forgalmának ellenőrzésére vonatkozó szabályok
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
* Az Azure SQL-szolgáltatás
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

- Operációs rendszer biztonsági rés (amelyet a Microsoft) – adatok 48 órán belül frissítése
- Rendszerfrissítések – adatok frissül 24 órában
- Az Endpoint Protection állít – 8 órán belül adatok frissítése

A Security Center általában megvizsgálja az új adatokat óránként. A késés fenti értékei a legrosszabb esetben ha nincs a legutóbbi vizsgálat vagy a vizsgálat nem sikerült.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Miért jelenik meg az üzenet "Virtuálisgép-ügynök hiányzó?"
A virtuális gép ügynököt telepíteni kell a virtuális gépeken szeretné az adatgyűjtést. Az Azure Marketplace-ről üzembe helyezett virtuális gépek esetében a virtuálisgép-ügynök alapértelmezés szerint telepítve van. A Virtuálisgép-ügynök telepítésével más virtuális gépeken információkért lásd: a következő blogbejegyzésben található [ügynök és Virtuálisgép-bővítmények](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
