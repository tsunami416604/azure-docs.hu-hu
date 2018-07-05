---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jpconnock
ms.service: virtual-machines
ms.topic: include
ms.date: 05/18/2018
ms.author: jeconnoc
ms.custom: include file
ms.openlocfilehash: d1a6ff8dbd17d2792709a1ce065bcf793154e585
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37780672"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>A platform által támogatott áttelepítés IaaS-erőforrások klasszikusból Azure Resource Manager
Ez a cikk bemutatja az infrastruktúra áttelepítése egy szolgáltatás (IaaS) erőforrásokat a klasszikusból Resource Manager üzembe helyezési modellek és részleteivel kapcsolatban, a virtual network használatával az előfizetésében fennálló két üzemi modellekből származó erőforrások összekapcsolása helyek közötti átjárók. További információ [Azure Resource Manager-funkciók és előnyök](../articles/azure-resource-manager/resource-group-overview.md). 

## <a name="goal-for-migration"></a>Áttelepítés célja
Resource Manager lehetővé teszi az összetett alkalmazások sablonok üzembe helyezése, konfigurálja a virtuális gépek Virtuálisgép-bővítmények használatával, és magában foglalja a hozzáférés-kezelés és a címkézést. Az Azure Resource Manager tartalmazza a méretezhető, párhuzamos üzembe helyezés virtuális gépek rendelkezésre állási csoportokba. Az új üzembe helyezési modellre is biztosít számítási, hálózati és storage, életciklus-felügyelet egymástól függetlenül. Végül pedig a fókusz a biztonság engedélyezése annak érdekében, a virtuális hálózatban lévő virtuális gépek alapértelmezés szerint.

A klasszikus üzemi modellből szinte minden szolgáltatás számítási, hálózati és tárolási Azure Resource Manager alatt támogatottak. Kihasználhatja az Azure Resource Manager új funkcióit, migrálja meglévő megoldásait a klasszikus üzemi modellből.

## <a name="supported-resources-for-migration"></a>Az áttelepítéshez támogatott erőforrások
A klasszikus IaaS-erőforrások támogatottak az áttelepítés során

* Virtuális gépek
* Rendelkezésre állási csoportok
* Cloud Services és a virtuális gépek
* Tárfiókok
* Virtuális hálózatok
* VPN Gateway átjárók
* Express Route-átjárók _(azonos előfizetésben található virtuális hálózat csak)_
* Network Security Groups (Hálózati biztonsági csoportok)
* Útvonaltáblák
* Fenntartott IP-címek

## <a name="supported-scopes-of-migration"></a>Az áttelepítés támogatott hatókörök
Számítási, hálózati és tárolási erőforrások áttelepítése a négy különböző módja van:

* [(Nem a virtuális hálózat) virtuális gépek migrálása](#migration-of-virtual-machines-not-in-a-virtual-network)
* [(A virtuális hálózat) virtuális gépek migrálása](#migration-of-virtual-machines-in-a-virtual-network)
* [A tárfiókok áttelepítése](#migration-of-storage-accounts)
* [Nem csatolt erőforrások migrálása](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>(Nem a virtuális hálózat) virtuális gépek migrálása
A Resource Manager-alapú üzemi modellben biztonsági alapértelmezés szerint kényszerítve alkalmazásai számára. Minden virtuális gépnek kell lennie egy virtuális hálózatot a Resource Manager-modellben. Az Azure-platform újraindul (`Stop`, `Deallocate`, és `Start`) a virtuális gépek a migrálás részeként. A virtuális hálózatok, amelyek a virtuális gépek migrálása két lehetősége van:

* Hozzon létre egy új virtuális hálózatot, és migrálni a virtuális gépet az új virtuális hálózatot a platform kérhetnek.
* Áttelepítheti a virtuális gép meglévő virtuális hálózatban a Resource Managerben.

> [!NOTE]
> A migrálás hatókörben felügyeletisík műveletei, mind az adatsík műveletek nem lehet engedélyezni, adott időszakban az áttelepítés során.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>(A virtuális hálózat) virtuális gépek migrálása
A legtöbb Virtuálisgép-konfigurációk esetén csak a metaadatokat a klasszikus és Resource Manager üzemi modellek között végez áttelepítést. Az alapul szolgáló virtuális gépek ugyanazt a hardvert, ugyanazon a hálózaton, és ugyanazon a tárterületen futnak. Felügyeletisík műveletei nem lehet engedélyezni, egy bizonyos ideig az áttelepítés során. Azonban az adatsík továbbra is működik. Vagyis a virtuális gépek (klasszikus) felett futó alkalmazások nem jár többletköltséggel leállás az áttelepítés során.

A következő konfigurációk jelenleg nem támogatottak. Támogatás bevezetése esetén a későbbiekben, az egyes virtuális gépeken Ez a konfiguráció vonatkozhatnak állásidő (keresse a stop, keresztül szabadítsa fel, és indítsa újra a virtuális gép műveletek).

* Az egyetlen felhőalapú szolgáltatás beállítása egynél több rendelkezésre állási rendelkezik.
* Rendelkezik egy vagy több rendelkezésre állási csoportok és a virtuális gépek, amelyek nem rendelkezésre állási csoportba egy felhőszolgáltatásban.

> [!NOTE]
> A migrálás hatókörben a felügyeleti sík nem lehet engedélyezni, adott időszakban az áttelepítés során. Az egyes konfigurációk adatsík állásidő következik be a korábban ismertetett módon.
>

### <a name="migration-of-storage-accounts"></a>A tárfiókok áttelepítése
Ahhoz, hogy problémamentes, üzembe helyezése Resource Manager virtuális gépek a klasszikus tárfiók. Ez a képesség számítási és hálózati erőforrásokat is, és a storage-fiókok függetlenül kell áttelepíteni. Miután az áttelepítés során a virtuális gépek és a virtuális hálózat szeretné áttelepíteni keresztül a storage-fiókok a migrálás befejezéséhez.

A storage-fiók nem rendelkezik társított lemezek vagy a virtuális gépek adatai, és csak a blobok, fájlok, táblák és üzenetsorok rendelkezik majd a migrálás az Azure Resource Manager hajtható végre, egy önálló áttelepítés függőségek nélkül.

> [!NOTE]
> A Resource Manager üzemi modell nem rendelkezik a klasszikus lemezképek és lemezek fogalmát. Ha a tárfiók áttelepített, klasszikus lemezképek és lemezek nem láthatók a Resource Manager-készletben, de a mögöttes VHD-k a tárfiókban maradnak.
>

### <a name="migration-of-unattached-resources"></a>Nem csatolt erőforrások migrálása
Nincs társított lemezek vagy a virtuális gépek adatainak a Storage-fiókok egymástól függetlenül is telepíthető át.

Hálózati biztonsági csoportok, útválasztási táblázatokat és a fenntartott IP-címek a virtuális gépek és virtuális hálózatok nem csatolt is áttelepíthetők egymástól függetlenül.

<br>

## <a name="unsupported-features-and-configurations"></a>Nem támogatott funkciók és konfigurációk
Egyes szolgáltatások és a konfiguráció jelenleg nem támogatott; a következő szakaszok ismertetik az ajánlásokat közöttük.

### <a name="unsupported-features"></a>Nem támogatott szolgáltatások
A következő funkciók jelenleg nem támogatottak. Szükség esetén távolítsa el ezeket a beállításokat, a virtuális gépek migrálása, és majd engedélyezze újra a beállításokat a Resource Manager-alapú üzemi modellben.

| Erőforrás-szolgáltató | Szolgáltatás | Ajánlás |
| --- | --- | --- |
| Compute | Társítatlan virtuálisgép-lemezeket. | Mögött ezek a lemezek Virtuálismerevlemez-blobokat fog települnek, ha telepít át a Storage-fiók |
| Compute | Virtuálisgép-lemezképeket. | Mögött ezek a lemezek Virtuálismerevlemez-blobokat fog települnek, ha telepít át a Storage-fiók |
| Network (Hálózat) | Végponti ACL-ek. | Végponti ACL-ek távolítsa el, és próbálkozzon újra a migrálással. |
| Network (Hálózat) | Application Gateway | Távolítsa el az Application Gateway áttelepítés megkezdése előtt, és ezután hozza létre újból az Application Gateway migrálás befejeződése után. |
| Network (Hálózat) | Virtuális hálózatok közötti Társviszony útján. | Virtuális hálózat migrálása a Resource Manager, majd társviszonyt. Tudjon meg többet [virtuális hálózatok közötti Társviszony](../articles/virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Nem támogatott konfigurációk
A következő konfigurációk jelenleg nem támogatottak.

| Szolgáltatás | Konfiguráció | Ajánlás |
| --- | --- | --- |
| Resource Manager |Szerepkör alapú hozzáférés-vezérlés (RBAC) a klasszikus erőforrások |Mivel URI-ját az erőforrásokat az áttelepítés után módosult, javasoljuk, hogy az RBAC szabályzatok történjen a migrálás után szükséges frissítését tervezi. |
| Compute |Több alhálózattal társított virtuális gép |Frissítse az alhálózati konfigurációt való hivatkozáshoz csak egy alhálózat. Ez előfordulhat, hogy távolítsa el a másodlagos hálózati adapter (amely egy másik alhálózatra hivatkozik) a virtuális gépről, és csatlakoztassa újra a migrálás befejezése után. |
| Compute |Virtuális gépek, virtuális hálózathoz tartozó, de még nincs hozzárendelve egy explicit alhálózat |Szükség esetén törölheti a virtuális Gépet. |
| Compute |Riasztások, szabályzatok automatikus skálázási rendelkező virtuális gépek |Az áttelepítés halad végig, és ezeket a beállításokat a rendszer elveti. Azt javasoljuk, hogy a környezet kiértékelése, az áttelepítés előtt. Újrakonfigurálhatja azt is megteheti, a riasztási beállítások az áttelepítés befejezése után. |
| Compute |XML-Virtuálisgép-bővítmények (BGInfo 1.*, Visual Studio hibakereső funkcióját, a Web Deploy és távoli hibakeresés) |Ez nem támogatott. Javasoljuk, hogy ezek a bővítmények a virtuális gépről a migrálás folytatásához távolítsa el, vagy azok dobja el a rendszer automatikusan az áttelepítési folyamat során. |
| Compute |A Premium storage a rendszerindítási diagnosztika |Migrálás folytatása előtt tiltsa le a virtuális gépek rendszerindítási diagnosztikai funkciót. A migrálás befejezése után újra engedélyezheti a rendszerindítási diagnosztika a Resource Manager veremben. Ezenkívül képernyőkép és a soros naplók használt blobot törölni kell a így már nem kell azokat a blobokat. |
| Compute | A cloud services webes és feldolgozói szerepköröket tartalmazó | Ez jelenleg nem támogatott. |
| Compute | Egynél több rendelkezésre állási tartalmazó felhőszolgáltatások állítsa be, vagy több rendelkezésre állási csoportokat. |Ez jelenleg nem támogatott. Helyezze át a virtuális gépek ugyanazon rendelkezésre állási csoportjának való migrálás előtt. |
| Compute | Virtuális gép az Azure Security Center-bővítménnyel | Az Azure Security Center automatikusan telepíti a bővítményeket, a virtuális gépeken a biztonsági állapotuk figyelését, és riasztást. Ezekről a kiterjesztésekről általában települjenek automatikusan, ha az Azure Security Center házirend engedélyezve van az előfizetésben. A virtuális gépek áttelepítését, tiltsa le a security center szabályzatával, ezzel a művelettel eltávolítja a bővítményt a virtuális gépek figyelése a Security Center az előfizetésben. |
| Compute | Virtuális gép biztonsági mentési vagy a pillanatkép-bővítménnyel | Ezek a bővítmények telepítve az Azure Backup szolgáltatással konfigurált virtuális gépen. Ezek a virtuális gépek áttelepítése nem támogatott, az útmutatót követve [Itt](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#vault) való migrálás előtt végrehajtott biztonsági mentések megtartása.  |
| Network (Hálózat) |Virtuális hálózatok, amelyek tartalmazzák a virtuális gépek és a webes/feldolgozói szerepkörök |Ez jelenleg nem támogatott. Helyezze át a webes/feldolgozói szerepkörök a saját virtuális hálózaton való migrálás előtt. A klasszikus virtuális hálózat telepít át, miután a migrált Azure Resource Managerbeli virtuális hálózat társviszonyba állíthatók elérése érdekében a hasonló konfigurációjú mint korábban a klasszikus virtuális hálózattal.|
| Network (Hálózat) | Klasszikus Expressroute-Kapcsolatcsoportok |Ez jelenleg nem támogatott. A Kapcsolatcsoportok kell áttelepíteni az Azure Resource Manager IaaS-migrálás megkezdése előtt. További tudnivalókért lásd: [áthelyezése ExpressRoute-Kapcsolatcsoportok a klasszikusból a Resource Manager-alapú üzemi modellbe](../articles/expressroute/expressroute-move.md).|
| Azure App Service |Virtuális hálózatok, amelyek tartalmazzák az App Service Environment-környezetek |Ez jelenleg nem támogatott. |
| Azure HDInsight |Virtuális hálózatok, amelyek tartalmazzák a HDInsight-szolgáltatások |Ez jelenleg nem támogatott. |
| A Microsoft Dynamics Lifecycle Services |Dynamics Lifecycle Services által felügyelt virtuális gépeket tartalmazó virtuális hálózatok |Ez jelenleg nem támogatott. |
| Azure AD Domain Services |Virtuális hálózatok, amelyek tartalmazzák az Azure AD tartományi szolgáltatások |Ez jelenleg nem támogatott. |
| Azure RemoteApp |Virtuális hálózatok, amelyek tartalmazzák az Azure RemoteApp központi telepítések |Ez jelenleg nem támogatott. |
| Azure API Management |Virtuális hálózatok, amelyek tartalmazzák az Azure API Management-telepítések |Ez jelenleg nem támogatott. Az IaaS virtuális hálózat migrálása, módosítsa a virtuális hálózat, az API Management központi telepítését, amely nincs állásidő művelet. |
