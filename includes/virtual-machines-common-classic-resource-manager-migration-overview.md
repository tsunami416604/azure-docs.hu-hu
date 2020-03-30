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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77192865"
---
Ez a cikk bemutatja, hogyan lehet áttelepíteni az infrastruktúrát szolgáltatásként (IaaS) a klasszikus erőforrás-kezelő telepítési modellekből, és részletesen ismerteti, hogyan lehet erőforrásokat csatlakoztatni az előfizetésben a virtuális hálózat használatával. helyek közötti átjárók. Az [Azure Resource Manager funkcióiról és előnyeiről olvashat bővebben.](../articles/azure-resource-manager/management/overview.md) 

## <a name="goal-for-migration"></a>Az áttelepítés célja
Az Erőforrás-kezelő lehetővé teszi az összetett alkalmazások sablonokon keresztüli telepítését, a virtuális gépek virtuálisgép-bővítmények használatával történő konfigurálását, valamint a hozzáférés-kezelés és a címkézés használatát. Az Azure Resource Manager skálázható, párhuzamos üzembe helyezést biztosít a virtuális gépek számára rendelkezésre állási csoportokba. Az új központi telepítési modell a számítási, hálózati és tárolási életciklus-kezelést is biztosítja egymástól függetlenül. Végül van egy hangsúly a biztonság engedélyezése alapértelmezés szerint a virtuális gépek kényszerítése a virtuális hálózatban.

A klasszikus üzembe helyezési modell szinte minden szolgáltatása támogatott az Azure Resource Manager számítási, hálózati és tárolási funkcióihoz. Az Azure Resource Manager új képességeinek kihasználása érdekében áttelepítheti a meglévő központi telepítéseket a klasszikus központi telepítési modellből.

## <a name="supported-resources-for-migration"></a>Támogatott áttelepítési erőforrások
Ezek a klasszikus IaaS-erőforrások támogatottak az áttelepítés során

* Virtuális gépek
* Rendelkezésre állási csoportok
* Tárfiókok
* Virtuális hálózatok
* VPN-átjárók
* Expressz útvonalátjárók _(ugyanabban az előfizetésben, mint csak a virtuális hálózat)_
* Network Security Groups (Hálózati biztonsági csoportok)
* Útvonaltáblák
* Fenntartott IP-címek

## <a name="supported-scopes-of-migration"></a>Támogatott áttelepítési hatókörök
A számítási, hálózati és tárolási erőforrások áttelepítésének négy különböző módja van:

* [Virtuális gépek áttelepítése (NEM virtuális hálózatban)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Virtuális gépek áttelepítése (virtuális hálózatban)](#migration-of-virtual-machines-in-a-virtual-network)
* [A tárfiókok áttelepítése](#migration-of-storage-accounts)
* [A független erőforrások áttelepítése](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Virtuális gépek áttelepítése (NEM virtuális hálózatban)
Az Erőforrás-kezelő telepítési modelljében alapértelmezés szerint az alkalmazások biztonsága lép érvénybe. Minden virtuális gépnek virtuális hálózatban kell lennie az Erőforrás-kezelő modellben. Az Azure platform`Stop`újraindítja a ( , `Deallocate`és `Start`) a virtuális gépeket az áttelepítés részeként. Két lehetősége van azon virtuális hálózatokra, amelyekbe a virtuális gépek et áttelepíti a következő:

* Kérheti a platformot, hogy hozzon létre egy új virtuális hálózatot, és telepítse át a virtuális gépet az új virtuális hálózatba.
* A virtuális gépet áttelepítheti egy meglévő virtuális hálózatba az Erőforrás-kezelőben.

> [!NOTE]
> Ebben az áttelepítési hatókörben a felügyeleti sík műveletek és az adatsík-műveletek az áttelepítés során egy ideig nem engedélyezhetők.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Virtuális gépek áttelepítése (virtuális hálózatban)
A legtöbb virtuálisgép-konfigurációk csak a metaadatok a klasszikus és az Erőforrás-kezelő telepítési modellek között. Az alapul szolgáló virtuális gépek ugyanazon a hardveren, ugyanazon a hálózaton és ugyanazzal a tárolóval futnak. Előfordulhat, hogy a felügyeleti sík műveletei az áttelepítés során egy bizonyos ideig nem engedélyezettek. Az adatsík azonban továbbra is működik. Ez azt jelenti, hogy a virtuális gépek (klasszikus) futó alkalmazások nem merülnek fel állásidő az áttelepítés során.

A következő konfigurációk jelenleg nem támogatottak. Ha a jövőben támogatást ad hozzá, ebben a konfigurációban egyes virtuális gépek leállást (stop, deallocate és indítsa újra a virtuális gép műveleteit).

* Egy felhőszolgáltatásban több rendelkezésre állási készlet is rendelkezésre áll.
* Egy vagy több rendelkezésre állási készletek és virtuális gépek, amelyek nem egy rendelkezésre állási készlet egyetlen felhőalapú szolgáltatásban.

> [!NOTE]
> Ebben az áttelepítési hatókörben előfordulhat, hogy a felügyeleti sík az áttelepítés során egy ideig nem engedélyezett. Bizonyos konfigurációk, korábban leírt, adatsík állásidő történik.
>

### <a name="migration-of-storage-accounts"></a>A tárfiókok áttelepítése
A zökkenőmentes áttelepítés lehetővé teszi, hogy a Resource Manager virtuális gépek egy klasszikus tárfiókban. Ezzel a funkcióval a számítási és hálózati erőforrásokat a tárfiókoktól függetlenül át lehet és kell áttelepíteni. Miután áttelepítette a virtuális gépek és a virtuális hálózat, át kell telepítenie a tárfiókok at az áttelepítési folyamat befejezéséhez.

Ha a tárfiók nem rendelkezik társított lemezekkel vagy virtuális gépekkel, és csak blobokat, fájlokat, táblákat és várólistákat tartalmaz, akkor az Azure Resource Managerbe való migrálás önálló áttelepítésként, függőségek nélkül végezhető el.

> [!NOTE]
> Az Erőforrás-kezelő központi telepítési modellje nem rendelkezik a klasszikus lemezképek és lemezek koncepciójával. A tárfiók áttelepítésekor a klasszikus lemezképek és lemezek nem láthatók az Erőforrás-kezelő veremben, de a biztonsági virtuális merevlemezek a tárfiókban maradnak.

Az alábbi képernyőképek bemutatják, hogyan frissíthet klasszikus tárfiókot egy Azure Resource Manager-tárfiókra az Azure Portal használatával:
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Nyissa meg a tárfiókot.
3. A **Beállítások csoportban** kattintson az **Áttelepítés ARM-ba**gombra.
4. Kattintson az **Ellenőrzés gombra** az áttelepítés megvalósíthatóságának meghatározásához.
5. Ha az érvényesítés sikeres, kattintson a **Felkészülés** az áttelepített tárfiók létrehozásához elemre.
6. Az áttelepítés megerősítéséhez írja be az **igen** t, majd kattintson a **Véglegesítés gombra** az áttelepítés befejezéséhez.

    ![Tárfiók ellenőrzése](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Tárfiók előkészítése](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Tárfiók áttelepítésének véglegesítése](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>A független erőforrások áttelepítése
A társított lemezekkel vagy virtuális gépekkel rendelkező tárfiókok egymástól függetlenül telepíthetők át.

A virtuális gépekhez és virtuális hálózatokhoz nem csatolt hálózati biztonsági csoportok, útvonaltáblák & fenntartott IP-k egymástól függetlenül is áttelepíthetők.

<br>

## <a name="unsupported-features-and-configurations"></a>Nem támogatott szolgáltatások és konfigurációk
Egyes szolgáltatások és konfigurációk jelenleg nem támogatottak; a következő szakaszok ismertetik a körülöttük lévő ajánlásokat.

### <a name="unsupported-features"></a>Nem támogatott szolgáltatások
A következő szolgáltatások jelenleg nem támogatottak. Ezeket a beállításokat tetszés szerint eltávolíthatja, áttelepítheti a virtuális gépeket, majd újra engedélyezheti a beállításokat az Erőforrás-kezelő telepítési modelljében.

| Erőforrás-szolgáltató | Szolgáltatás | Ajánlás |
| --- | --- | --- |
| Compute | Nem társított virtuálisgép-lemezek. | A lemezek mögötti virtuális merevlemez-blobok áttelepítésre kerülnek a tárfiók áttelepítésekekén |
| Compute | Virtuális gép képei. | A lemezek mögötti virtuális merevlemez-blobok áttelepítésre kerülnek a tárfiók áttelepítésekekén |
| Network (Hálózat) | Végpontok hozzáférés-vezérlési listái. | Távolítsa el az Endpoint ACL-eket, és próbálja meg újra az áttelepítést. |
| Network (Hálózat) | Application Gateway | Távolítsa el az Alkalmazásátjárót az áttelepítés megkezdése előtt, majd hozza létre újra az alkalmazásátjárót, amint az áttelepítés befejeződött. |
| Network (Hálózat) | Virtuális hálózatok virtuális társviszony-létesítést használó. | Virtuális hálózat áttelepítése az Erőforrás-kezelőbe, majd a társba. További információ a [virtuális társviszony-létesítésről.](../articles/virtual-network/virtual-network-peering-overview.md) |

### <a name="unsupported-configurations"></a>Nem támogatott konfigurációk
A következő konfigurációk jelenleg nem támogatottak.

| Szolgáltatás | Konfiguráció | Ajánlás |
| --- | --- | --- |
| Resource Manager |Szerepköralapú hozzáférés-vezérlés (RBAC) klasszikus erőforrásokhoz |Mivel az erőforrások URI-ja az áttelepítés után módosul, ajánlott megtervezni az RBAC-házirend frissítéseit, amelyeknek az áttelepítés után kell megtörténnie. |
| Compute |Virtuális géphez társított több alhálózat |Frissítse az alhálózati konfigurációt úgy, hogy csak egy alhálózatra hivatkozzon. Ez szükségessé teheti, hogy távolítsa el a másodlagos hálózati adapter (amely hivatkozik egy másik alhálózat) a virtuális gép, és az áttelepítés befejezése után újra csatolja azt. |
| Compute |Virtuális hálózathoz tartozó, de explicit alhálózattal nem rendelkező virtuális gépek |A virtuális gép szükség esetén törölheti. |
| Compute |Riasztást tartalmazó virtuális gépek, automatikus skálázási házirendek |Az áttelepítés megy keresztül, és ezeket a beállításokat elkell dobni. Erősen ajánlott, hogy értékelje ki a környezetet, mielőtt az áttelepítés. Azt is megteheti, hogy az áttelepítés befejezése után újrakonfigurálja a riasztási beállításokat. |
| Compute |XML virtuálisgép-bővítmények (BGInfo 1.*, Visual Studio hibakereső, webes telepítés és távoli hibakeresés) |Ez nem támogatott. Javasoljuk, hogy távolítsa el ezeket a bővítményeket a virtuális gépről az áttelepítés folytatásához, vagy az áttelepítési folyamat során automatikusan ellesznek dobva. |
| Compute |Rendszerindítási diagnosztika prémium szintű tárhellyel |Tiltsa le a rendszerindítási diagnosztika szolgáltatást a virtuális gépekhez az áttelepítés folytatása előtt. Az áttelepítés befejezése után újra engedélyezheti a rendszerindítási diagnosztikát az Erőforrás-kezelő veremben. Emellett a képernyőképhez és a soros naplókhoz használt blobokat törölni kell, hogy a blobokért már nem kell fizetnie. |
| Compute | Webes/feldolgozói szerepköröket tartalmazó felhőszolgáltatások | Ez jelenleg nem támogatott. |
| Compute | Felhőszolgáltatások, amelyek egynél több rendelkezésre állási csoport vagy több rendelkezésre állási csoport. |Ez jelenleg nem támogatott. Az áttelepítés előtt helyezze át a virtuális gépeket ugyanarra a rendelkezésre állási csoportra. |
| Compute | Virtuális gép az Azure Security Center-bővítménylel | Az Azure Security Center automatikusan telepíti a bővítményeket a virtuális gépekre a biztonságuk figyeléséhez és a riasztások riasztásaihoz. Ezek a bővítmények általában automatikusan települnek, ha az Azure Security Center-szabályzat engedélyezve van az előfizetésben. A virtuális gépek áttelepítéséhez tiltsa le az előfizetés biztonsági központ házirendje, amely eltávolítja a Security Center figyelési bővítményt a virtuális gépekről. |
| Compute | Virtuális gép biztonsági mentési vagy pillanatkép-bővítménylel | Ezek a bővítmények az Azure Backup szolgáltatással konfigurált virtuális gépre vannak telepítve. Bár ezek a virtuális gépek áttelepítése nem támogatott, kövesse az [itt](/azure/virtual-machines/windows/migration-classic-resource-manager-faq#i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault) található útmutatást az áttelepítés előtt készített biztonsági mentések megőrzéséhez.  |
| Compute | Virtuális gép az Azure Site Recovery bővítménylel | Ezek a bővítmények az Azure Site Recovery szolgáltatással konfigurált virtuális gépre vannak telepítve. Amíg a Site Recovery szolgáltatással használt tárhely áttelepítése működni fog, az aktuális replikációra lesz hatással. Le kell tiltania, és engedélyeznie kell a virtuális gép replikációját a tároló áttelepítése után. |
| Network (Hálózat) |Virtuális gépeket és webes/feldolgozói szerepköröket tartalmazó virtuális hálózatok |Ez jelenleg nem támogatott. Az áttelepítés előtt helyezze át a web-/feldolgozói szerepköröket a saját virtuális hálózatukba. A klasszikus virtuális hálózat áttelepítése után az áttelepített Azure Resource Manager virtuális hálózat társviszonyt létesíthető a klasszikus virtuális hálózattal a korábhoz hasonló konfiguráció elérése érdekében.|
| Network (Hálózat) | Klasszikus expressz útvonal-áramkörök |Ez jelenleg nem támogatott. Ezeket az áramköröket át kell telepíteni az Azure Resource Managerbe az IaaS-áttelepítés megkezdése előtt. További információ: [ExpressRoute-áramkörök áthelyezése a klasszikusról az Erőforrás-kezelő telepítési modelljére](../articles/expressroute/expressroute-move.md)című témakörben olvashat.|
| Azure App Service |App Service-környezeteket tartalmazó virtuális hálózatok |Ez jelenleg nem támogatott. |
| Azure HDInsight |HDInsight-szolgáltatásokat tartalmazó virtuális hálózatok |Ez jelenleg nem támogatott. |
| Microsoft Dynamics életciklus-szolgáltatások |A Dynamics Életciklus-szolgáltatások által kezelt virtuális gépeket tartalmazó virtuális hálózatok |Ez jelenleg nem támogatott. |
| Azure AD Domain Services |Azure AD tartományi szolgáltatásokat tartalmazó virtuális hálózatok |Ez jelenleg nem támogatott. |
| Azure API Management |Az Azure API Management központi telepítését tartalmazó virtuális hálózatok |Ez jelenleg nem támogatott. Az IaaS-virtuális hálózat áttelepítéséhez módosítsa az API Management központi telepítésének virtuális hálózatát, amely egy állásidő-művelet nélküli. |
