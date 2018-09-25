---
title: Az Azure Security Center biztonsági szabályzatainak külön-külön vagy Azure házirendek részeként állítható |} A Microsoft Docs
description: Ebből a dokumentumból megismerheti, hogy a szabályzatok beállítását, vagy az Azure Policy az Azure Security Centerben.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/5/2018
ms.author: terrylan
ms.openlocfilehash: 3c198ea44953c0b2e72a544cd0e83b6592d9a81f
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032076"
---
# <a name="setting-security-policies-in-security-center-or-in-azure-policy"></a>Biztonsági szabályzatok beállítása a Security Center vagy az Azure Policy

Ez a cikk segít az Azure Security Center biztonsági szabályzatainak konfigurálásához szükséges. Azure Ügyfélkörnyezetre Center szabályzatainak integrálása az Azure-szabályzatok, tehát beállíthatja azokat a Security Center vagy egy meghatározott előfizetésre, vagy a [Azure Policy](../azure-policy/azure-policy-introduction.md), amely lehetővé teszi szabályzatok beállítását a felügyeleti csoportokhoz, és több előfizetések...

## <a name="what-are-security-policies"></a>Mik azok a biztonsági szabályzatok?
A biztonsági szabályzat határozza meg a számítási feladatokhoz tartozó kívánt konfigurációkat, és segít biztosítani a vállalati vagy hatósági követelményeknek való megfelelést. Az Azure Security Centerben az Azure-előfizetésekre vonatkozó szabályzatokat határozhat meg, és testre szabni azokat a számítási feladatok típusának vagy az adatok érzékenysége. A szabályozott adatokat, például személyazonosításra alkalmas adatokat használó alkalmazások például szükség lehet egy magasabb biztonsági szintet, mint a többi munkaterhelését. Házirend beállítása a felügyeleti csoportok vagy előfizetések között, állítsa őket a [Azure Policy](../azure-policy/azure-policy-introduction.md).

> [!NOTE]
> Ha korábban konfigurálta a biztonsági házirendek egy adott előfizetés, amely a felügyeleti csoport része, vagy még több szabályzat-hozzárendelést a(z), ezek a házirendek szürke színnel jelennek meg a Security Centerben, hogy a szabályzat az Azure-on keresztül a felügyeleti csoport szintjén kezelheti Házirend lap. 

## <a name="how-security-policies-work"></a>A biztonsági szabályzatok működése
A Security Center automatikusan létrehoz egy alapértelmezett biztonsági szabályzatot minden egyes Azure-előfizetéséhez. A szabályzatokat szerkesztheti a Security Centerben, vagy az Azure Policy segítségével elvégezheti a következőket:
- Új szabályzatdefiníciók létrehozása.
- Szabályzatok hozzárendelése felügyeleti csoportokhoz és előfizetésekhez, amelyek jelölhetnek egy teljes vállalatot, vagy egy adott üzleti egységet a vállalaton belül.
- Szabályzatoknak való megfelelés figyelése.

Az Azure Policyvel kapcsolatos további információkért olvassa el [a megfelelőség szabályzatok létrehozásával és kezelésével történő kikényszerítésével](../azure-policy/create-manage-policy.md) foglalkozó témakört.

Egy Azure-szabályzat az alábbi összetevőkből áll:

- A **szabályzat** egy szabály
- A **kezdeményezés** egy szabályzatgyűjtemény
- A **hozzárendelés** egy kezdeményezés vagy szabályzat alkalmazása egy megadott hatókörre (felügyeleti csoport, előfizetés vagy erőforráscsoport)

Egy erőforrást a rendszer azon szabályzatok szerint értékel ki, amelyek hozzá vannak rendelve, majd az erőforrás kap egy megfelelőségi arányt azon szabályzatok alapján, amelyeknek megfelel.

## <a name="who-can-edit-security-policies"></a>Ki szerkeszthet biztonsági szabályzatok?
A Security Center szerepköralapú hozzáférés-vezérlés (RBAC), amely biztosít beépített szerepkörök, felhasználók, csoportok és Azure-szolgáltatások rendelhető használ. A Security Center megnyitásakor látnak csak ők is hozzáférhetnek az erőforrásokhoz kapcsolódó információkat. Ami azt jelenti, hogy felhasználók tulajdonosi, közreműködői vagy olvasói szerepköre sincs hozzárendelve a erőforrás tartozik előfizetés vagy az erőforrás-csoport. Ezen szerepkörök mellett két speciális Security Center-szerepkör van:

- Biztonsági olvasó: rendelkezik megtekintése a Security Centernek, amely tartalmazza a javaslatok, riasztások, a házirend és egészségügyi, rights, de azok nem végezhet módosításokat.
- Biztonsági rendszergazda: biztonsági olvasó nézetben megegyező jogokkal rendelkezik, és azok is a biztonsági házirend módosítása és javaslatok és riasztások bezárása.

## <a name="edit-security-policies"></a>Biztonsági szabályzatok szerkesztése
Az egyes Azure-előfizetések és felügyeleti csoportok alapértelmezett biztonsági szabályzatait a Security Centerben szerkesztheti. Biztonsági szabályzat módosításához az előfizetésben vagy az azt tartalmazó felügyeleti csoportban tulajdonos, közreműködő vagy biztonsági rendszergazda szerepkörrel kell rendelkeznie. Biztonsági szabályzatok megtekintése a Security Centerben:

> [!NOTE]
> Házirendeknek, amelyek a felügyeleti csoport része, vagy több szabályzat-hozzárendelések, rendelkezik egy adott előfizetés a Security Center kiszürkítve fog megjelenni. Ezeket a szabályzatokat szerkesztheti [Azure Policy](../azure-policy/azure-policy-introduction.md). 

1. A **Security Center** irányítópultjának **SZABÁLYZAT ÉS MEGFELELŐSÉG** területén válassza a **Biztonsági szabályzat** elemet. Megnyílik a **Szabályzatkezelés** panel.

    ![A Szabályzatkezelés panel](./media/security-center-azure-policy/security-center-policies-fig10.png)

  A Szabályzatkezelés panel megjeleníti a felügyeleti csoportok, előfizetések és munkaterületek számát, valamint a felügyeleticsoport-struktúrát.

  > [!NOTE]
  > A Security Center irányítópultján magasabb szám jelenhet meg az **Előfizetési lefedettség** alatt az előfizetéseknél, mint a **Szabályzatkezelés** menüben. Az Előfizetési lefedettség alatt a Standard, Ingyenes és „nem lefedett” előfizetések is megjelennek. A „nem lefedett” előfizetésekhez nincs engedélyezve a Security Center, ezért nem jelennek meg a **Szabályzatkezelés** alatt.
  >
  >

  A táblázatban lévő oszlopok a következők:

 - Szabályzatkezdeményezés-hozzárendelés – a Security Center egy előfizetéshez vagy felügyeleti csoporthoz hozzárendelt beépített szabályzatai és kezdeményezései.
 - Megfelelőség – Összesített megfelelőségi érték egy felügyeleti csoporthoz, előfizetéshez vagy munkaterülethez. Az érték a hozzárendelések súlyozott átlaga. A súlyozott átlag figyelembe veszi egy hozzárendelés szabályzatainak számát és azon erőforrások számát, amelyre a hozzárendelés érvényes.

 Ha például az előfizetéshez két VM tartozik, és hozzá van rendelve egy kezdeményezés öt szabályzattal, akkor 10 értékelése lesz az előfizetésében. Ha az egyik VM két szabályzatnak nem felel meg, akkor az előfizetés teljes megfelelőségi értéke 80% lesz.

 - Lefedettség – Azt a tarifacsomagot (Ingyenes vagy Standard) azonosítja, amelyen a felügyeleti csoport, előfizetés vagy munkaterület fut.  A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).
 - Beállítások – Az előfizetéseknél megtalálható a **Beállítások szerkesztése** hivatkozás. Ha a **Beállítások szerkesztése** lehetőséget választja, frissítheti az előfizetés beállításait, például az adatgyűjtést, a tarifacsomagot és az e-mailes értesítéseket.

2. Válassza ki azt az előfizetést vagy felügyeleti csoportot, amelyhez biztonsági szabályzatot szeretne engedélyezni. Megnyílik a **Biztonsági szabályzat** képernyő.

3.  A **Biztonsági szabályzat** területen a **Be** kiválasztásával választhatja ki azokat a vezérlőket, amelyeket a Security Center monitorozzon, és amelyekhez javaslatokat adjon.  Ha azt szeretné, hogy a Security Center ne monitorozza ezt a vezérlőt, válassza a **Ki** lehetőséget.

    ![Szabályzat összetevői](./media/security-center-azure-policy/security-policy.png)

4. Kattintson a **Mentés** gombra.

## <a name="available-security-policy-definitions"></a>Elérhető biztonságiszabályzat-definíciók

Az alapértelmezett biztonsági szabályzatban elérhető szabályzatdefiníciók megértéséhez tekintse át az alábbi táblázatot:

| Szabályzat | Az engedélyezett szabályzat működése |
| --- | --- |
| System updates (Rendszerfrissítések) |Lekéri az elérhető biztonsági és kritikus frissítések napi listáját a Windows Update vagy a Windows Server Update Services szolgáltatástól. A lekért lista a virtuális gépek számára beállított szolgáltatástól függ, és javasolja a hiányzó frissítések alkalmazását. Linux rendszerek esetében a szabályzat a disztribúció által biztosított csomagkezelő rendszert használja az elérhető frissítésekkel rendelkező csomagok meghatározásához. Az [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md) virtuális gépeitől származó biztonsági és kritikus frissítéseket is keres. |
| Biztonsági konfigurációk |Naponta elemzi az operációs rendszer beállításait olyan problémák meghatározása érdekében, amelyek a virtuális gépet sebezhetővé tehetik a támadásokkal szemben. A szabályzat a biztonsági rések megszüntetését elősegítő konfigurációmódosításokat is javasol. A szabályzat által figyelt konfigurációkkal kapcsolatban lásd [a javasolt alapkonfigurációk listáját](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (A Windows Server 2016 jelenleg nem részesül teljes mértékű támogatásban.) |
| Endpoint protection (Végpontok védelme) |A szabályzat az összes windowsos virtuális gép (VM) esetében végpontvédelem beállítását javasolja a vírusok, kémprogramok és más kártevőszoftverek hatékonyabb azonosításához és eltávolításához. |
| Disk encryption (Lemeztitkosítás) |Nyugalmi állapotban az adatvédelmi teljesítmény javításához ajánlja a lemeztitkosítás engedélyezését az összes virtuális gépen. |
| Network security groups (Hálózati biztonsági csoportok) |[Hálózati biztonsági csoportok](../virtual-network/security-overview.md) konfigurálását ajánlja a be- és kimenő adatforgalom vezérlésére a nyilvános végpontokkal rendelkező virtuális gépek esetében. Az alhálózatra beállított hálózati biztonsági csoportokat az összes virtuális géphez tartozó hálózati adapter örökli, kivéve, ha Ön más beállítást ad meg. Annak figyelése mellett, hogy a hálózati biztonsági csoportok konfigurálása megtörtént-e, ez a szabályzat a bejövő biztonsági szabályokat is ellenőrzi, és azonosítja azokat a szabályokat, amelyek engedélyezik a bejövő forgalmat. |
| Web application firewall (Webalkalmazási tűzfal) |Webalkalmazási tűzfal beállítását javasolja a virtuális gépeken, ha az alábbiak valamelyike teljesül: <ul><li>A rendszer egy [példányszintű nyilvános IP-címet](../virtual-network/virtual-networks-instance-level-public-ip.md) használ, és a kapcsolódó hálózati biztonsági csoportra vonatkozó bejövő biztonsági szabályok a 80/443-as port elérésének engedélyezésére vannak konfigurálva.</li><li>A rendszer elosztott terhelésű IP-címet használ, és a kapcsolódó terheléselosztás és a hálózati címfordításra (NAT) vonatkozó bejövő szabályok a 80/443-as port elérésének engedélyezésére vannak konfigurálva. A további információkat az [Azure Resource Manager support for Load Balancer](../load-balancer/load-balancer-arm.md) (Azure Resource Manager-támogatás a terheléselosztóhoz) című rész tartalmazza.</li> |
| Next generation firewall (Új generációs tűzfal) |Az Azure-ba épített hálózati biztonsági csoportokon túlra is kiterjesztheti a hálózati védelmet. A Security Center felderíti az üzemelő példányokat, amelyekhez új generációs tűzfal használata javasolt, és engedélyezi a virtuális berendezés beállítását. |
| SQL-naplózás és fenyegetésészlelés |Javasolja, hogy a megfelelőség, a jobb fenyegetésészlelés és a támadások hatékonyabb kivizsgálásához engedélyezze az Azure Database-hozzáférések naplózását. |
| SQL-titkosítás |Javasolja, hogy engedélyezze az inaktív adatok titkosítását az Azure SQL Database számára, valamint az azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra vonatkozóan. Így hiába jutnak be illetéktelen személyek a rendszerbe, az adatokat nem tudják olvasni. |
| Sebezhetőségi felmérés |Javasolja, hogy telepítsen egy biztonsági rések felmérése szolgáló megoldást a virtuális gépére. |
| Storage-titkosítás |Ez a szolgáltatás jelenleg az Azure Blob Storage-hoz és az Azure Fileshoz érhető el. A Storage szolgáltatás titkosításának engedélyezése után csak az új adatok lesznek titkosítva, és a tárfiók meglévő fájljai titkosítatlanok maradnak. |
| JIT hálózati hozzáférés |Ha az igény szerinti hálózati hozzáférés engedélyezve van, a Security Center minden, az Azure-beli virtuális gépekre érkező forgalmat zárol egy hálózati biztonsági csoport típusú szabály létrehozásával. Ön választja ki a virtuális gép azon portjait, amelyeken a beérkező forgalmat a rendszer zárolja. További információk: [Manage virtual machine access using just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) (A virtuális gépekhez való hozzáférés kezelése igény szerinti hozzáférés használata esetén). |

## <a name="management-groups"></a>Felügyeleti csoportok
Ha a vállalatnak sok előfizetése van, jól jöhet egy módszer, hogy hatékonyan kezelje az előfizetésekhez való hozzáférést, a szabályzatokat és a megfelelőséget. Az Azure Management Groups előfizetések fölötti hatókörszintet biztosít. Az előfizetéseket „felügyeleti csoportok” nevű tárolókba rendezheti, és az irányítási szabályzatokat alkalmazhatja a felügyeleti csoportokra. A felügyeleti csoporton belüli összes előfizetés automatikusan örökli a felügyeleti csoportra alkalmazott szabályzatokat. Minden címtárhoz tartozik egy legfelső szintű, „gyökér” felügyeleti csoportnak nevezett felügyeleti csoport. Ez a gyökérszintű felügyeleti csoport úgy épül be a hierarchiába, hogy minden felügyeleti csoport és előfizetés fölött legyen. Ez a gyökérszintű felügyeleti csoport lehetővé teszi globális szabályzatok és RBAC-hozzárendelések címtárszintű alkalmazását. Ha a felügyeleti csoportokat be szeretné állítani az Azure Security Centerrel való használatra, kövesse az [Azure Security Centerbeli bérlőszintű láthatóságot](security-center-management-groups.md) ismertető cikket. 

> [!NOTE]
> Fontos, hogy átlássa a felügyeleti csoportok és előfizetések hierarchiáját. További információt a felügyeleti csoportokról, a gyökérszintű felügyeletről és a felügyeleti csoportok hozzáféréséről [az erőforrások az Azure Felügyeleti csoportok segítségével való rendszerezését](../governance/management-groups/index.md#root-management-group-for-each-directory) ismertető részben talál.
>
>


## <a name="next-steps"></a>További lépések
Ebből a cikkből megismerhette a Security Center biztonsági szabályzatainak konfigurálását. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md): Az Azure Security Centerhez tartozó tervezési szempontokat ismertető és az azokat figyelembe vevő tervezési folyamatokban segítő útmutató, amely megkönnyíti az Azure Security Center használatát.
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md): Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md): Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Bérlőszintű láthatóság az Azure Security Centerben](security-center-management-groups.md): Megismerheti a felügyeleti csoportok beállításának módját az Azure Security Centerben. 
* [Azure Security Center – gyakori kérdések](security-center-faq.md): Választ találhat a szolgáltatás használatával kapcsolatos gyakori kérdésekre.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

További információk az Azure Policyról: [Mi az az Azure Policy?](../azure-policy/azure-policy-introduction.md)
