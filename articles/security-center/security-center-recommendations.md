---
title: Biztonsági javaslatok kezelése az Azure Security Centerben |} Microsoft Docs
description: A dokumentumból megtudhatja, hogyan javaslatok az Azure Security Centerben segítséget nyújtanak az Azure-erőforrások védelme és maradnak meg a biztonsági házirendeknek megfelelően.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 72070f46309adb526901192752fe421a3846398b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Biztonsági javaslatok kezelése az Azure Security Centerben
Ez a dokumentum végigvezeti javaslatok használata az Azure Security Centerben az Azure-erőforrások védelméhez.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  Ez a dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="what-are-security-recommendations"></a>Mik azok a biztonsági javaslatok?
A Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát. A Security Center javaslatokat hoz létre, amikor lehetséges biztonsági réseket észlel. A javaslatok végigvezetik Önt a szükséges vezérlők konfigurálásának folyamatán.

## <a name="implementing-security-recommendations"></a>Biztonsági javaslatok megvalósítása
### <a name="set-recommendations"></a>Set javaslatok
A [biztonsági szabályzatok beállítása az Azure Security Center](security-center-policies.md), ismerje meg, hogy:

* Biztonsági szabályzatainak konfigurálását.
* Bekapcsolni az adatgyűjtést.
* Válassza ki a mely ajánlások tekintse meg a biztonsági házirend részeként.

Aktuális javaslatok szabályzatközpontban körül Rendszerfrissítések, alapkonfigurációs szabályok, kártevőirtó programok [hálózati biztonsági csoportok](../virtual-network/security-overview.md) alhálózatok és a hálózati adapterek, SQL-adatbázis naplózásának, SQL adatbázis átlátható adattitkosítás webes alkalmazás tűzfalak és.  [Biztonsági szabályzatok beállítása](security-center-policies.md) minden javaslat beállítás leírását.

### <a name="monitor-recommendations"></a>A figyelő javaslatok
Miután beállított egy biztonsági házirendet, a Security Center elemzi az Ön erőforrásainak állapotát, hogy észlelhesse a potenciális biztonsági réseket. A **javaslatok** a csempén **áttekintése** jelzi, a Security Center által azonosított ajánlások teljes száma.

![Javaslatok csempe][1]

Minden ajánlást részleteinek megtekintéséhez válassza ki a **javaslatok csempe** alatt **áttekintése**. **Javaslatok** nyílik meg.

![Szűrő javaslatok][2]

A javaslatok egy tábla formájában jelennek meg, amelyben minden egyes sor egy adott javaslatot tartalmaz. Ez a tábla oszlopainak a következők:

* **Leírás**: a javaslat és mit kell annak érdekében, hogy kezelje azt be.
* **ERŐFORRÁS**: sorolja fel az erőforrások, amelyekre ez a javaslat vonatkozik.
* **ÁLLAPOT**: a javaslat aktuális állapotának leírása:
  * **Nyissa meg**: A javaslat még nem foglalkoztak.
  * **A folyamatban lévő**: A javaslat jelenleg vonatkozik az erőforrásokat, és nincs szükség felhasználói műveletek is.
  * **Megoldott**: A javaslat már befejeződött (ebben az esetben a sor szürkén jelenik meg).
* **SEVERITY** (Súlyosság): az adott javaslat súlyosságát határozza meg:
  * **Magas**: A biztonsági rés fontos erőforrásnál (például egy alkalmazást, a virtuális gép vagy a hálózati biztonsági csoport) létezik, ezért beavatkozást igényel.
  * **Közepes**: A biztonsági rés és a nem kritikus vagy kiegészítő lépések szükségesek, ezt meg kell szüntetni, vagy egy folyamat befejezéséhez.
  * **Alacsony**: A biztonsági rés kell tömni, de nem igényel azonnali beavatkozást. (Alapértelmezés szerint alacsony súlyosságú javaslatok nem jelenik meg, de ha meg szeretné tekinteni őket végezhet alacsony súlyosságú javaslatok.)

Az alábbi táblázatban használja hivatkozásként segít megérteni a rendelkezésre álló ajánlásokat, és mindegyik funkciója Ha alkalmazza azt.

> [!NOTE]
> Érdemes tudni a [klasszikus és Resource Manager üzembe helyezési modell](../azure-classic-rm.md) az Azure-erőforrások.
>
>

| Ajánlás | Leírás |
| --- | --- |
| [Adatgyűjtés engedélyezése az előfizetések számára](security-center-enable-data-collection.md) |Javasolja, hogy kapcsolja be a biztonsági házirend adatgyűjtés az egyes az előfizetések és az összes Azure virtuális gépek (VM) és az Azure-számítógépek. |
| [Biztonsági konfigurációk javítása](security-center-remediate-os-vulnerabilities.md) |Javasolja, hogy az operációs rendszer azon konfigurációit a javasolt biztonsági konfigurációs szabályokat, például igazítása, ne engedélyezze a jelszavak menteni. |
| [Rendszerfrissítések alkalmazása](security-center-apply-system-updates.md) |Javasolja, hogy telepít hiányzó rendszer biztonsági és kritikus frissítések a Windows és a Linux virtuális gépek és a számítógépek. |
| [A közvetlenül időponthoz kötött alkalmazni a hálózati hozzáférés-vezérlés](security-center-just-in-time.md) | Csak a virtuális gép elérhető telepítését javasolja. A szolgáltatás egyelőre időben csak és érhető el a Security Center Standard csomagra. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md). |
| [Rendszerfrissítések utáni újraindítás](security-center-apply-system-updates.md#reboot-after-system-updates) |Javasolja, hogy a rendszerfrissítések alkalmazási folyamatának befejezéséhez indítson újra egy virtuális gépet. |
| [Webalkalmazási tűzfal hozzáadása](security-center-add-web-application-firewall.md) |Javasolja, hogy telepít egy webalkalmazási tűzfal (WAF) webszolgáltatási végpontok. WAF ajánlás bármely nyilvános felé néző IP-cím (példány szint IP vagy terhelés eloszlik IP), amely rendelkezik egy társított hálózati biztonsági csoportot a nyissa meg a bejövő webes portokkal (80,443) jelenik meg. </br>A Security Center javasolja, hogy egy WAF a webes alkalmazások, virtuális gépek és az App Service Environment-környezet célzó támadások elleni védelemre való ellátásához. Az App Service környezetben (ASE) van egy [prémium](https://azure.microsoft.com/pricing/details/app-service/) terv lehetőséget az Azure App Service egy teljesen elkülönített és dedikált környezetben történő biztonságos futtatására az Azure App Service apps biztosító szolgáltatás. ASE kapcsolatos további tudnivalókért tekintse meg a [App Service környezetben dokumentáció](../app-service/environment/intro.md).</br>Adja hozzá ezeket az alkalmazásokat a meglévő WAF-központitelepítések védelmet biztosíthat a Security Center több webalkalmazás. |
| [Alkalmazásvédelem véglegesítése](security-center-add-web-application-firewall.md#finalize-application-protection) |Egy WAF konfigurációjának befejezéséhez forgalom WAF készülékre át irányítva. Ez a javaslat a következő befejezi a szükséges telepítőfájlokat módosításokat. |
| [Újgenerációs tűzfal hozzáadása](security-center-add-next-generation-firewall.md) |Javasolja, hogy növelje a biztonsági védelmet egy Microsoft-partner adja hozzá a következő generációs tűzfal (NGFW). |
| [Csak az újgenerációs tűzfalon keresztül haladjon a forgalom](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Javasolja, hogy konfigurálja a hálózati biztonsági csoport (NSG) szabályok kényszerítheti a bejövő forgalom a virtuális géphez a NGFW keresztül. |
| [Endpoint Protection telepítése](security-center-install-endpoint-protection.md) |Javasolja, hogy telepítsen kártevőirtó programokat a virtuális gépekre (csak Windows rendszerű virtuális gépek esetében). |
| [Hálózati biztonsági csoportok engedélyezi az alhálózatokat vagy a virtuális gépek](security-center-enable-network-security-groups.md) |Az alhálózatok és virtuális gépek NSG-k engedélyezését javasolja. |
| [Internetre irányuló végpont-en keresztüli hozzáférés korlátozása](security-center-restrict-access-through-internet-facing-endpoints.md) |Azt javasolja, hogy az NSG-ket konfigurálhat a bejövő forgalomra vonatkozó szabályokat. |
| [Naplózás és fenyegetésészlelés engedélyezése az SQL-kiszolgálókon](security-center-enable-auditing-on-sql-servers.md) |Javasolja, hogy kapcsolja be az Azure SQL-kiszolgálókra naplózás és a fenyegetések észlelésére. (Csak az azure SQL-szolgáltatás esetében. Nem tartalmazza a virtuális gépeken futó SQL.) |
| [Naplózás és fenyegetésészlelés engedélyezése az SQL-adatbázisokon](security-center-enable-auditing-on-sql-databases.md) |Javasolja, hogy kapcsolja be az Azure SQL-adatbázisok naplózásának és a fenyegetések észlelésére. (Csak az azure SQL-szolgáltatás esetében. Nem tartalmazza a virtuális gépeken futó SQL.) |
| [Az átlátható adattitkosítási engedélyezése az SQL-adatbázisok](security-center-enable-transparent-data-encryption.md) |Az SQL-adatbázisok titkosítási engedélyezését javasolja. (Az azure SQL-szolgáltatás csak.) |
| [Virtuálisgép-ügynök engedélyezése](security-center-enable-vm-agent.md) |Lehetővé teszi a virtuálisgép-ügynök alkalmazását igénylő virtuális gépek megtekintését. A virtuális gép ügynököt telepíteni kell a virtuális gépeken rendelkezés javítás vizsgálatát, Alapterv vizsgálatát, és a kártevőirtó-programok. Az Azure Marketplace-ről üzembe helyezett virtuális gépek esetében a virtuálisgép-ügynök alapértelmezés szerint telepítve van. A virtuálisgép-ügynök telepítéséről a [Virtuális gép-ügynök és -bővítmények – 2. rész](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) cikkben talál információkat. |
| [Lemeztitkosítás alkalmazása](security-center-apply-disk-encryption.md) |Javasolja, hogy végezze el a virtuális gép titkosítását az Azure Disk Encryption használatával (Windows és Linux rendszerű virtuális gépek esetében). A titkosítás elvégzése az operációs rendszer és az adatkötetek esetében egyaránt javasolt a virtuális gépen. |
| [Biztonsági kapcsolattartói adatok megadása](security-center-provide-security-contact-details.md) |Javasolja, hogy megadja a biztonsági információk kérjen minden egyes előfizetésnél. Kapcsolattartási adatok egy e-mail címét és telefonszámát számát. Az adatok kapcsolatba lépni Önnel, ha a biztonsági csoport megállapítja, hogy az erőforrások kerülnek veszélybe. |
| [Operációs rendszer verziójának frissítése](security-center-update-os-version.md) |Javasolja, hogy meg frissíteni az operációs rendszer verzió a felhőalapú szolgáltatáshoz az elérhető legújabb verzióra a operációsrendszer-család.  Cloud Services kapcsolatos további tudnivalókért tekintse meg a [felhőalapú szolgáltatások – áttekintés](../cloud-services/cloud-services-choose-me.md). |
| [A sebezhetőségi felmérés nincs telepítve](security-center-vulnerability-assessment-recommendations.md) |Javasolja, hogy telepítsen egy biztonsági rések felmérése szolgáló megoldást a virtuális gépére. |
| [Sebezhetőségek javítása](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Lehetővé teszi a virtuális gépre telepített sebezhetőségfelmérő megoldás által észlelt rendszer- és alkalmazássebezhetőségek megtekintését. |
| [Engedélyezze a titkosítást az Azure Storage-fiók](security-center-enable-encryption-for-storage-account.md) | Az inaktív adatok Azure Storage szolgáltatás titkosítási engedélyezését javasolja. Storage Service Encryption (SSE) beolvasása előtt visszafejti és az Azure storage beíródik esetén az adatok titkosításával működik. SSE jelenleg csak az Azure Blob szolgáltatáshoz érhető el, és nem használható blokkblobokat, lapblobokat, és hozzáfűző blobokat. További tudnivalókért lásd: [Storage szolgáltatás titkosítási az inaktív adatok](../storage/common/storage-service-encryption.md).</br>SSE csak erőforrás-kezelő tárfiókok esetén támogatott. |

Szűrés, és elvetésére vonatkozó javaslatok.

1. Válassza ki **szűrő** a a **javaslatok** panelen. A **szűrő** panel nyílik meg, és látni szeretné súlyossági és állapotának értékek választja.

2. Ha azt állapítja meg, hogy azt ajánljuk, nem alkalmazható, hagyja figyelmen kívül a javaslat, és szűréssel a nézetben. Két módon lehet elvetni ajánlást. Kattintson a jobb gombbal egy elemet, majd válassza ki az egyirányú **leállítási**. Vigye az egérmutatót egy elemre, kattintson a három pontra, amely a jobb oldalon megjelenő, és válassza ki a másik pedig **leállítási**. Kattintva megtekintheti a elvetettként javaslatok **szűrő**, és jelölje be **Dismissed**.

    ![Hagyja figyelmen kívül a javaslat][3]

### <a name="apply-recommendations"></a>Javaslatok alkalmazása
Miután megtekintette a ajánlások, döntse el, amely egy alkalmazza először. Azt javasoljuk, hogy használja a súlyosság, mely ajánlások kiértékelése a fő paraméter előbb alkalmazni kell.

A fenti ajánlásokat tábla válassza ki azt ajánljuk, és végezze el az példa bemutatja, hogyan alkalmaz egy javaslatot.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megismerhette a Security Center biztonsági javaslatok. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Center](security-center-policies.md) – útmutató az Azure-előfizetések és -erőforráscsoportok biztonsági szabályzatainak konfigurálásához.
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](security-center-managing-and-responding-alerts.md) – útmutató kezelése és válaszadás a biztonsági riasztásokra.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png
