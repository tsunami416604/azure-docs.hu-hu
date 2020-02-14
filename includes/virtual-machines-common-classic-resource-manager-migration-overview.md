---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: tanmaygore
ms.service: virtual-machines
ms.topic: include
ms.date: 02/06/2020
ms.author: tagore
ms.custom: include file
ms.openlocfilehash: 4e07334e859f2c1401547cc3f88988830b71c5e1
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192865"
---
Ez a cikk bemutatja, hogyan telepíthet át szolgáltatásként (IaaS) elérhető erőforrásokat a Klasszikusról Resource Manager-alapú üzemi modellekre, és hogyan kapcsolódhat az előfizetésben található két üzembe helyezési modellből a virtuális hálózat használatával helyek közötti átjárók. További információ: [Azure Resource Manager funkciók és előnyök](../articles/azure-resource-manager/management/overview.md). 

## <a name="goal-for-migration"></a>Cél az áttelepítéshez
A Resource Manager lehetővé teszi összetett alkalmazások sablonokon keresztüli üzembe helyezését, a virtuális gépek virtuálisgép-bővítmények használatával történő konfigurálását, valamint a hozzáférés-kezelést és a címkézést. A Azure Resource Manager méretezhető, párhuzamos üzembe helyezést biztosít a virtuális gépek számára a rendelkezésre állási csoportokban. Az új üzembe helyezési modell a számítás, a hálózat és a tárolás életciklus-kezelését is biztosítja egymástól függetlenül. Végezetül a virtuális gépek virtuális gépei általi kényszerítésével a biztonsági beállítások alapértelmezés szerint is elérhetővé válik.

A klasszikus üzemi modellből majdnem minden funkció támogatott a számítási, hálózati és tárolási Azure Resource Manager alatt. A Azure Resource Manager új képességeinek kihasználásához áttelepítheti a meglévő központi telepítéseket a klasszikus üzemi modellből.

## <a name="supported-resources-for-migration"></a>Az áttelepítéshez támogatott források
A klasszikus IaaS-erőforrások az áttelepítés során támogatottak

* Virtuális gépek
* Rendelkezésre állási csoportok
* Tárfiókok
* Virtuális hálózatok
* VPN-átjárók
* Express Route Gateways _(ugyanabban az előfizetésben, mint Virtual Network csak)_
* Network Security Groups (Hálózati biztonsági csoportok)
* Útvonaltáblák
* Fenntartott IP-címek

## <a name="supported-scopes-of-migration"></a>Áttelepítési támogatott hatókörök
A számítási, hálózati és tárolási erőforrások áttelepítésének négy különböző módja van:

* [Virtuális gépek áttelepítése (nem virtuális hálózatban)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Virtuális gépek áttelepítése (virtuális hálózatban)](#migration-of-virtual-machines-in-a-virtual-network)
* [Storage-fiókok áttelepítése](#migration-of-storage-accounts)
* [A nem csatolt erőforrások áttelepítése](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Virtuális gépek áttelepítése (nem virtuális hálózatban)
A Resource Manager-alapú üzemi modellben alapértelmezés szerint az alkalmazások biztonsága érvénybe lép. Minden virtuális gépnek a Resource Manager-modellben lévő virtuális hálózatban kell lennie. Az Azure platform a Migrálás részeként újraindítja a virtuális gépeket (`Stop`, `Deallocate`és `Start`). Két lehetőség közül választhat a virtuális hálózatok számára, amelyeket a Virtual Machines áttelepíteni fog a következőre:

* Kérheti a platformot, hogy hozzon létre egy új virtuális hálózatot, és telepítse át a virtuális gépet az új virtuális hálózatba.
* A virtuális gépet áttelepítheti egy meglévő virtuális hálózatba a Resource Managerben.

> [!NOTE]
> Ebben az áttelepítési hatókörben a felügyeleti sík és az adatsík műveletek nem engedélyezettek az áttelepítés ideje alatt.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Virtuális gépek áttelepítése (virtuális hálózatban)
A legtöbb virtuálisgép-konfiguráció esetében a rendszer csak a metaadatokat telepíti át a klasszikus és a Resource Manager-alapú üzemi modellek között. A mögöttes virtuális gépek ugyanazon a hardveren, ugyanabban a hálózatban és ugyanazzal a tárolóval futnak. Előfordulhat, hogy a felügyeleti sík műveletei bizonyos ideig nem engedélyezettek az áttelepítés során. Az adatközpont azonban továbbra is működik. Vagyis a virtuális gépeken futó alkalmazások (klasszikus) nem járnak állásidővel az áttelepítés során.

A következő konfigurációk jelenleg nem támogatottak. Ha a jövőben is támogatást adnak hozzá, a konfiguráció egyes virtuális gépei állásidőt okozhatnak (leállíthatja, felszabadíthatja és újraindíthatja a virtuális gépek műveleteit).

* Egynél több rendelkezésre állási csoporttal rendelkezik egyetlen felhőalapú szolgáltatásban.
* Egy vagy több rendelkezésre állási csoporttal és virtuális géppel rendelkezik, amelyek egyetlen felhőalapú szolgáltatásban nem rendelkezésre állási csoportba tartoznak.

> [!NOTE]
> Ebben az áttelepítési hatókörben előfordulhat, hogy a felügyeleti sík az áttelepítés során nem engedélyezett ideig. A korábban leírtaknak megfelelően a rendszer az adatsík állásidőt alkalmazza.
>

### <a name="migration-of-storage-accounts"></a>Storage-fiókok áttelepítése
A zökkenőmentes áttelepítés lehetővé tételéhez a Resource Manager-alapú virtuális gépeket egy klasszikus Storage-fiókban is üzembe helyezheti. Ezzel a képességgel a számítási és hálózati erőforrások a Storage-fiókoktól függetlenül telepíthetők és áttelepíthetők. Miután áttelepítette a Virtual Machines és Virtual Network, át kell telepítenie a Storage-fiókjait az áttelepítési folyamat befejezéséhez.

Ha a Storage-fiók nem rendelkezik társított lemezzel vagy Virtual Machines adattal, és csak Blobokkal, fájlokkal, táblázatokkal és várólistákkal rendelkezik, akkor a rendszerbe Azure Resource Manager történő áttelepítés a függőségek nélküli önálló áttelepítésként is elvégezhető.

> [!NOTE]
> A Resource Manager-alapú üzemi modell nem rendelkezik a klasszikus rendszerképek és lemezek fogalmával. A Storage-fiók áttelepítésekor a klasszikus rendszerképek és lemezek nem láthatók a Resource Manager-veremben, de a háttérben működő virtuális merevlemezek megmaradnak a Storage-fiókban.

A következő képernyőképek bemutatják, hogyan frissíthet egy klasszikus Storage-fiókot egy Azure Resource Manager Storage-fiókra Azure Portal használatával:
1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
2. Nyissa meg a tárfiókot.
3. A **Beállítások** szakaszban kattintson az **áttelepítés az ARM**-be elemre.
4. Az áttelepítés megvalósíthatóságának meghatározásához kattintson az **Érvényesítés** elemre.
5. Ha érvényesítést végez, kattintson az **előkészítés** elemre az áttelepített Storage-fiók létrehozásához.
6. Írja be az **Igen** értéket az áttelepítés megerősítéséhez, majd kattintson a **véglegesítés** gombra az áttelepítés befejezéséhez.

    ![Storage-fiók ellenőrzése](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Storage-fiók előkészítése](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![A Storage-fiók áttelepítésének véglegesítése](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>A nem csatolt erőforrások áttelepítése
A társított lemezzel nem rendelkező és Virtual Machines adatmennyiségű tárolási fiókok egymástól függetlenül telepíthetők át.

A hálózati biztonsági csoportok, az útválasztási táblák & a fenntartott IP-címek, amelyek nincsenek csatolva a Virtual Machineshoz és a virtuális hálózatokhoz, egymástól függetlenül is áttelepíthetők.

<br>

## <a name="unsupported-features-and-configurations"></a>Nem támogatott funkciók és konfigurációk
Bizonyos funkciók és konfigurációk jelenleg nem támogatottak; a következő szakaszok ismertetik a rájuk vonatkozó javaslatokat.

### <a name="unsupported-features"></a>Nem támogatott funkciók
A következő funkciók jelenleg nem támogatottak. Szükség esetén eltávolíthatja ezeket a beállításokat, áttelepítheti a virtuális gépeket, majd újból engedélyezheti a beállításokat a Resource Manager-alapú üzemi modellben.

| Erőforrás-szolgáltató | Funkció | Ajánlás |
| --- | --- | --- |
| Számítás | Nem társított virtuálisgép-lemezek. | A rendszer áttelepíti a virtuális merevlemezek mögötti blobokat, amikor a rendszer áttelepíti a Storage-fiókot |
| Számítás | Virtuálisgép-lemezképek. | A rendszer áttelepíti a virtuális merevlemezek mögötti blobokat, amikor a rendszer áttelepíti a Storage-fiókot |
| Hálózat | Végponti ACL-ek. | Távolítsa el a végponti ACL-eket, és próbálkozzon újra |
| Hálózat | Application Gateway | A Migrálás megkezdése előtt távolítsa el a Application Gateway, majd hozza létre újra a Application Gateway az áttelepítés befejeződése után. |
| Hálózat | VNet-társítást használó virtuális hálózatok. | Virtual Network migrálása a Resource Managerbe, majd a társ. További információ a [VNet](../articles/virtual-network/virtual-network-peering-overview.md)-kezelésről. |

### <a name="unsupported-configurations"></a>Nem támogatott konfigurációk
A következő konfigurációk jelenleg nem támogatottak.

| Szolgáltatás | Konfiguráció | Ajánlás |
| --- | --- | --- |
| Resource Manager |Szerepköralapú Access Control (RBAC) klasszikus erőforrásokhoz |Mivel az erőforrások URI-ja módosul az áttelepítés után, azt javasoljuk, hogy tervezze meg azokat a RBAC-házirend-frissítéseket, amelyekre az áttelepítés után szükség van. |
| Számítás |Egy virtuális géppel társított több alhálózat |Az alhálózat konfigurációjának frissítése, hogy csak egy alhálózatra hivatkozzon. Ehhez a virtuális gépről el kell távolítania egy másodlagos hálózati ADAPTERt (amely egy másik alhálózatra hivatkozik), és az áttelepítés befejeződése után újra csatlakoztatni kell. |
| Számítás |Virtuális hálózathoz tartozó virtuális gépek, de nincs hozzárendelve explicit alhálózat |Lehetősége van törölni a virtuális gépet. |
| Számítás |Riasztásokkal rendelkező virtuális gépek, autoskálázási szabályzatok |Az áttelepítés áthalad, és ezek a beállítások el lesznek dobva. Javasoljuk, hogy az áttelepítés megkezdése előtt értékelje ki a környezetét. Azt is megteheti, hogy újrakonfigurálja a riasztási beállításokat az áttelepítés befejeződése után. |
| Számítás |XML virtuálisgép-bővítmények (BGInfo 1. *, Visual Studio Debugger, web Deploy és távoli hibakeresés) |Ez nem támogatott. Javasoljuk, hogy távolítsa el ezeket a bővítményeket a virtuális gépről a Migrálás folytatásához, vagy az áttelepítési folyamat során a rendszer automatikusan eldobja azokat. |
| Számítás |Rendszerindítási diagnosztika Premium Storage szolgáltatással |A Migrálás folytatása előtt tiltsa le a virtuális gépek rendszerindítási diagnosztikai funkcióját. Az áttelepítés befejezése után újra engedélyezheti a rendszerindítási diagnosztikát a Resource Manager-veremben. Emellett a képernyőfelvételekhez és a soros naplókhoz használt blobokat is törölni kell, így a Blobok után már nem számítunk fel díjat. |
| Számítás | Webes/feldolgozói szerepköröket tartalmazó felhőalapú szolgáltatások | Ez jelenleg nem támogatott. |
| Számítás | Több rendelkezésre állási csoportot vagy több rendelkezésre állási készletet tartalmazó Cloud Services. |Ez jelenleg nem támogatott. A Migrálás előtt helyezze át a Virtual Machinest ugyanarra a rendelkezésre állási csoportba. |
| Számítás | Virtuális gép Azure Security Center bővítménnyel | A Azure Security Center automatikusan telepíti a bővítményeket a Virtual Machines a biztonsági és riasztási riasztások figyelésére. Ezek a bővítmények általában automatikusan települnek, ha a Azure Security Center szabályzat engedélyezve van az előfizetésben. A Virtual Machines áttelepítése érdekében tiltsa le az előfizetéshez tartozó Security Center-szabályzatot, amely eltávolítja a Virtual Machines Security Center figyelési bővítményét. |
| Számítás | Virtuális gép biztonsági mentési vagy pillanatkép-bővítménnyel | Ezek a bővítmények a Azure Backup szolgáltatással konfigurált virtuális gépre vannak telepítve. A virtuális gépek áttelepítése nem támogatott, az [itt](/azure/virtual-machines/windows/migration-classic-resource-manager-faq#i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault) található útmutatást követve megőrizheti a Migrálás előtt végrehajtott biztonsági mentéseket.  |
| Számítás | Virtuális gép Azure Site Recovery bővítménnyel | Ezek a bővítmények a Azure Site Recovery szolgáltatással konfigurált virtuális gépre vannak telepítve. Amíg a Site Recovery által használt tárterület áttelepítése működni fog, a rendszer hatással lesz a jelenlegi replikálásra. A tároló áttelepítése után le kell tiltania és engedélyeznie kell a virtuális gépek replikálását. |
| Hálózat |Virtuális gépeket és webes/feldolgozói szerepköröket tartalmazó virtuális hálózatok |Ez jelenleg nem támogatott. Migrálás előtt helyezze át a webes/feldolgozói szerepköröket a saját Virtual Networkba. A klasszikus Virtual Network áttelepítését követően az áttelepített Azure Resource Manager Virtual Network a klasszikus Virtual Networkkal is összehívhatók, hogy hasonló konfigurációt érjenek el, mint korábban.|
| Hálózat | Klasszikus expressz Route-áramkörök |Ez jelenleg nem támogatott. A IaaS áttelepítés megkezdése előtt át kell telepíteni ezeket az áramköröket Azure Resource Manager. További információ: [ExpressRoute-áramkörök áthelyezése a Klasszikusból a Resource Manager](../articles/expressroute/expressroute-move.md)-alapú üzemi modellbe.|
| Azure App Service |App Service környezeteket tartalmazó virtuális hálózatok |Ez jelenleg nem támogatott. |
| Azure HDInsight |HDInsight szolgáltatásokat tartalmazó virtuális hálózatok |Ez jelenleg nem támogatott. |
| Microsoft Dynamics Lifecycle Services |A Dynamics Lifecycle Services által felügyelt virtuális gépeket tartalmazó virtuális hálózatok |Ez jelenleg nem támogatott. |
| Azure AD Domain Services |Azure AD tartományi szolgáltatásokat tartalmazó virtuális hálózatok |Ez jelenleg nem támogatott. |
| Azure API Management |Azure API Management üzemelő példányokat tartalmazó virtuális hálózatok |Ez jelenleg nem támogatott. A IaaS-VNET áttelepítéséhez módosítsa az API Management üzemelő példány VNET, amely nem leállási művelet. |
