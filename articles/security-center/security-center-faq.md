---
title: Az Azure Security Center – gyakori kérdések (GYIK) |} A Microsoft Docs
description: Ez a GYIK az Azure Security Centerrel kapcsolatos kérdésekre ad választ.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2018
ms.author: rkarlin
ms.openlocfilehash: 320c7c483e865c85948d32ee2b5b70a92181920f
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160069"
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

A **Standard szintű** hozzáadja a komplex veszélyforrások elleni képességeket tartalmaz, beleértve a fenyegetésészlelési intelligencia, a viselkedés-elemzés, a rendellenességek észlelése, a biztonsági incidensek és tesznek elérhetővé; ilyenek jelentések fenyegetés. A Standard szint az első 60 napban ingyenesen használható. Válasszák 60 napos időszak letelte után a szolgáltatás használatát, automatikusan megkezdjük a megfelelő díj felszámítását.  Szeretné frissíteni, válassza ki a [Tarifacsomag](https://docs.microsoft.com/azure/security-center/security-center-pricing) a biztonsági szabályzatban.

## <a name="permissions"></a>Engedélyek
Használja az Azure Security Center [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/role-assignments-portal.md), amely biztosítja [beépített szerepkörök](../role-based-access-control/built-in-roles.md) , hogy a felhasználók, csoportok és Azure-szolgáltatások rendelhetők.

A Security Center a biztonsági problémák és biztonsági rések azonosításához erőforrásán konfiguráció értékeli. A Security Centerben csak láthatja az előfizetés vagy az erőforráscsoport, amely egy erőforrás tartozik, a tulajdonos, közreműködő vagy olvasó a szerepkör hozzárendelése esetén egy erőforráshoz kapcsolódó információkat.

Lásd: [engedélyek az Azure Security Centerben](security-center-permissions.md) tudhat meg többet a szerepkörök és a Security Centerben engedélyezett műveletek.

## <a name="data-collection"></a>Adatgyűjtés
A Security Center adatokat gyűjt az Azure-beli virtuális gépek (VM) és a nem Azure-beli számítógépekről a biztonsági rések és fenyegetések monitorozásához. Az adatgyűjtés a Microsoft Monitoring Agent segítségével történik, amely a biztonsághoz kapcsolódó különböző konfigurációkat és eseménynaplókat olvas be a gépről, és elemzés céljából átmásolja az adatokat az Ön munkaterületére.

### <a name="how-do-i-disable-data-collection"></a>Hogyan tilthatom le az adatgyűjtést?
Alapértelmezés szerint az Automatikus kiépítés le van. Letilthatja az Automatikus kiépítés erőforrásokból bármikor ezt a beállítást, a biztonsági szabályzatban kikapcsolásával. Az Automatikus kiépítés erősen ajánlott biztonsági riasztások és javaslatok Rendszerfrissítések, az operációs rendszer biztonsági rések és az endpoint protection információk lekérése érdekében.

Tiltsa le az adatgyűjtést, hogy [jelentkezzen be az Azure Portalon](https://portal.azure.com), jelölje be **Tallózás**, jelölje be **a Security Center**, válassza ki **válassza ki a szabályzat**. Válassza ki azt az előfizetést, amelynél le szeretné tiltani az automatikus kiépítést. Amikor kiválaszt egy előfizetés **biztonsági szabályzat – adatgyűjtés** nyílik meg. A **automatikus kiépítés**válassza **ki**.

### <a name="how-do-i-enable-data-collection"></a>Hogyan engedélyezhetem az adatgyűjtés?
Engedélyezheti az adatgyűjtést az Azure-előfizetés biztonsági szabályzatában. Az adatgyűjtés engedélyezéséhez. [Jelentkezzen be az Azure Portalon](https://portal.azure.com), jelölje be **Tallózás**, jelölje be **a Security Center**, és válassza ki **biztonsági házirend**. Automatikus kiépítés engedélyezése kívánt előfizetés kiválasztásához. Amikor kiválaszt egy előfizetés **biztonsági szabályzat – adatgyűjtés** nyílik meg. A **automatikus kiépítés**válassza **a**.

### <a name="what-happens-when-data-collection-is-enabled"></a>Mi történik, ha az adatgyűjtés engedélyezve van?
Ha az Automatikus kiépítés engedélyezve van, a Security Center létrehozza a Microsoft Monitoring Agentet az összes támogatott Azure-beli és újonnan létrehozott. Az Automatikus kiépítés használata erősen ajánlott, de a manuális ügynöktelepítések is rendelkezésre áll. [Ismerje meg, hogyan telepítse a Microsoft Monitoring Agent bővítményt](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Az ügynök lehetővé teszi, hogy a folyamat-létrehozási esemény 4688 és a *CommandLine* mezőt 4688-as belül. A virtuális gépen új folyamatok EventLog által rögzített és a Security Center észlelési szolgáltatások által figyelt. Információk a rögzített minden új folyamathoz részleteire: [4688 leírás mezőiben](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Az ügynök is a virtuális gépen 4688 eseményeit, és tárolja a keresés.

Ha a Security Center a virtuális gép gyanús tevékenységet észlel, az ügyfél értesítést e-mailben, ha a [biztonsági kapcsolattartási adatok](security-center-provide-security-contact-details.md) lett megadva. Egy riasztás akkor is a Security Center biztonsági riasztások irányítópult látható.

> [!NOTE]
> - Adatgyűjtés engedélyezése [adaptív Alkalmazásvezérlők](security-center-adaptive-application.md), konfigurálja a helyi AppLocker-házirend rendszervizsgálati módban, hogy az összes alkalmazást. Ennek hatására az AppLocker események, amelyeket gyűjteni, és a Security Center által használható létrehozása. Fontos megjegyezni, hogy ez a szabályzat minden olyan gépeken, amelyeken már van egy konfigurált AppLocker-házirendek nem lesznek konfigurálva. 

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>A Monitoring Agent befolyásolja a kiszolgáló teljesítményét?
Az ügynök egy névleges mennyiségű rendszer-erőforrásokat használ fel, és teljesítményére gyakorolt minimális hatás kell rendelkeznie. További információ a teljesítményt és az ügynök és a bővítmény: az [tervezéséhez és útmutató](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Hol tárolják az adataimat?
Ettől az ügynöktől gyűjtött adatok tárolva van vagy egy meglévő Log Analytics-munkaterületet az Ön előfizetéséhez rendelve, vagy egy új munkaterületet. További információkért lásd: [adatbiztonság](security-center-data-security.md).

## <a name="using-azure-security-center"></a>Az Azure Security Center használatával
### <a name="what-is-a-security-policy"></a>Mi az a biztonsági szabályzatot?
Biztonsági szabályzat határozza meg, az adott előfizetésen belüli erőforrások ajánlott szabályozza. Az Azure Security Centerben szabályzatokat az Azure-előfizetést a vállalat biztonsági elvárásainak és az alkalmazások típusához vagy az egyes előfizetések adatainak érzékenysége tartozó meghatározása.

Az Azure Security Center meghajtó biztonsági javaslatok és a figyelés az engedélyezett biztonsági szabályzatok. Biztonsági szabályzatok kapcsolatos további információkért lásd: [biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Ki módosíthatja a biztonsági szabályzatot?
Biztonsági szabályzat módosításához a rendszergazda vagy a tulajdonosának vagy Közreműködőjének előfizetésben kell lennie.

Biztonsági szabályzat konfigurálásával kapcsolatban lásd: [biztonsági szabályzatok beállítása az Azure Security Center](security-center-policies.md).

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

A Security Center általában figyeli az új adatokat óránként. A késés a fenti értékek a legrosszabb esetben ahol nem áll a legutóbbi vizsgálat vagy a vizsgálat nem sikerült.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Miért jelenik meg az üzenet "Virtuálisgép-ügynök hiányzik?"
Virtuális gépek az adatgyűjtés engedélyezése a Virtuálisgép-ügynököt kell telepíteni. Az Azure Marketplace-ről üzembe helyezett virtuális gépek esetében a virtuálisgép-ügynök alapértelmezés szerint telepítve van. Más virtuális gépeken a Virtuálisgép-ügynök telepítésével kapcsolatos információkért lásd: a következő blogbejegyzésben: [Virtuálisgép-ügynök és -bővítmények](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
