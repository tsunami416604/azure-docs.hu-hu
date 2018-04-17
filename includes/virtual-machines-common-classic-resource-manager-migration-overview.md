# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>IaaS-erőforrásokra a klasszikus Azure Resource Manager platform által támogatott áttelepítése
Ez a cikk azt ismerteti hogyan azt még engedélyezése erőforrásként egy szolgáltatási (IaaS) klasszikus Resource Manager üzembe helyezési modelljeire infrastruktúra áttelepítését. További tudnivalók [Azure erőforrás-kezelő szolgáltatásait és előnyeit](../articles/azure-resource-manager/resource-group-overview.md). A két üzembe helyezési modellel, az előfizetés virtuális hálózati helyek átjáró használatával elért párhuzamosan az erőforrások összekapcsolása részletességi azt.

## <a name="goal-for-migration"></a>Áttelepítés célja
Erőforrás-kezelő lehetővé teszi, hogy a sablonok összetett alkalmazások telepítését, konfigurálja a virtuális gépek Virtuálisgép-bővítmények használatával, és magában foglalja a hozzáférés-kezelés és a címkézést. Az Azure Resource Manager méretezhető, párhuzamos szolgáltatássablonjaikat a virtuális gépek rendelkezésre állási készletek tartalmazza. Az új központi telepítési modell is biztosít a számítási, hálózati és tárolási életciklus-felügyeletének egymástól függetlenül. Végül pedig a fókusz a biztonsági alapértelmezés szerint a virtuális hálózatban lévő virtuális gépek végrehajtásának engedélyezése.

A klasszikus telepítési modellből szinte minden szolgáltatás a számítási, hálózati és tárolási az Azure Resource Manager használata támogatott. Kihasználják az új képességek az Azure Resource Manager, a klasszikus telepítési modell telepítéseit telepíthet át.

## <a name="supported-resources-for-migration"></a>Az áttelepítéshez támogatott erőforrások
A klasszikus IaaS-erőforrásokra támogatott az áttelepítés során

* Virtuális gépek
* Rendelkezésre állási csoportok
* Cloud Services
* Tárfiókok
* Virtuális hálózatok
* VPN Gateway átjárók
* Express Route átjárók _(a tárolóként ugyanazt az előfizetést virtuális hálózat csak)_
* Network Security Groups (Hálózati biztonsági csoportok)
* Útvonaltáblák
* Fenntartott IP-címek

## <a name="supported-scopes-of-migration"></a>Az áttelepítés támogatott hatókörök
Többféleképpen 4 számítási, hálózati és tárolási erőforrásokat áttelepítésének befejezéséhez. Ezek a

* (Nem része virtuális hálózatnak) virtuális gépek áttelepítése
* Virtuális gépek (a virtuális hálózat) áttelepítése
* Fiókok tárolóáttelepítés
* Leválasztott erőforrások (hálózati biztonsági csoportok, Útvonaltábláit & fenntartott IP-címek)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>(Nem része virtuális hálózatnak) virtuális gépek áttelepítése
A Resource Manager üzembe helyezési modellel biztonsági kikényszeríti az alkalmazások alapértelmezett. Minden virtuális gép kell lennie a Resource Manager modellt a virtuális hálózatban. Az Azure platformon újraindul (`Stop`, `Deallocate`, és `Start`) a virtuális gépek, az áttelepítés részeként. A virtuális hálózatok, a rendszer áttelepíti a virtuális gépek két lehetőség közül választhat:

* Hozzon létre egy új virtuális hálózatot és az új virtuális hálózat a virtuális gép áttelepítéséhez a platform kérhet.
* Áttelepítheti a virtuális gépet létrehozni meglévő virtuális hálózatban az erőforrás-kezelőben.

> [!NOTE]
> Az áttelepítés hatókör a felügyeleti-vezérlősík műveletek mind az adatok-vezérlősík műveletek nem lehet engedélyezni, egy adott időn belül az áttelepítés során.
>
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Virtuális gépek (a virtuális hálózat) áttelepítése
A legtöbb Virtuálisgép-konfigurációk esetén csak a metaadatok között a klasszikus és Resource Manager üzembe helyezési modellel telepítünk át. Az alapul szolgáló virtuális gépek futnak a ugyanazon a hardveren, ugyanazon a hálózaton, és ugyanazt a tárhelyet. A felügyeleti-vezérlősík műveletek nem engedélyezhető egy bizonyos időn az áttelepítés során. Azonban az adatok vezérlősík továbbra is működik. Ez azt jelenti, hogy a virtuális gépek (klasszikus) felett futó alkalmazások nem számítunk leállás az áttelepítés során.

A következő konfigurációk jelenleg nem támogatottak. Támogatás bevezetése esetén a jövőben az egyes virtuális gépek ebben a konfigurációban előfordulhat, hogy fel Önnek állásidő (Nyissa meg felszabadítani a stop, keresztül, majd indítsa újra a virtuális gép műveletek).

* Egynél több rendelkezésre állási egyetlen felhőszolgáltatás csoport rendelkezik.
* Rendelkezik egy vagy több rendelkezésre állási készletek és a virtuális gépek, amelyek nem tagjai rendelkezésre állási készlet egyetlen felhőszolgáltatásban.

> [!NOTE]
> Az áttelepítés hatókör a felügyeleti vezérlősík nem lehet engedélyezni, egy adott időn belül az áttelepítés során. Bizonyos konfigurációk ismertetett módon, adat-vezérlősík leállás.
>
>

### <a name="storage-accounts-migration"></a>Fiókok tárolóáttelepítés
Zökkenőmentes áttelepítés engedélyezéséhez klasszikus tárfiókokban erőforrás-kezelő virtuális gépeket telepíthet. Ezzel a lehetőséggel a számítási és hálózati erőforrásokat is, és a storage-fiókok függetlenül át kell telepíteni. Miután áttelepítette a virtuális gépek és a virtuális hálózat felett, szeretné áttelepíteni a storage-fiókok az áttelepítési folyamat befejezéséig keresztül.

> [!NOTE]
> A Resource Manager üzembe helyezési modellben nem rendelkezik a klasszikus lemezképet és lemezt fogalmát. Ha a tárfiók az áttelepített, klasszikus képek és lemezek nem láthatók a Resource Manager-készletben a, de a VHD-k biztonsági megmaradnak a tárfiókban lévő.
>
>

### <a name="unattached-resources-network-security-groups-route-tables--reserved-ips"></a>Leválasztott erőforrások (hálózati biztonsági csoportok, Útvonaltábláit & fenntartott IP-címek)
Hálózati biztonsági csoportok, Útvonaltábláit & fenntartott IP-címek, amelyek nem kapcsolódnak a virtuális gépek és virtuális hálózatok egymástól függetlenül telepíthető át.

<br>

## <a name="unsupported-features-and-configurations"></a>Nem támogatott funkciókat és konfigurációk
A Microsoft jelenleg nem támogatja bizonyos szolgáltatásokat és konfigurációkat. A következő szakaszok ismertetik a javaslatok felhasználókat ezekbe a csoportokba.

### <a name="unsupported-features"></a>Nem támogatott funkciók
A következő szolgáltatások jelenleg nem támogatottak. Opcionálisan eltávolítsa ezeket a beállításokat, a virtuális gépek és majd engedélyezze újra a beállításokat a Resource Manager üzembe helyezési modellben.

| Erőforrás-szolgáltató | Szolgáltatás | Ajánlás |
| --- | --- | --- |
| Számítás | Társítatlan virtuálisgép-lemezeket. | A VHD-blobok ezeknek a lemezeknek mögött fog települnek, ha a Tárfiók telepít át |
| Számítás | Virtuálisgép-lemezképeket. | A VHD-blobok ezeknek a lemezeknek mögött fog települnek, ha a Tárfiók telepít át |
| Network (Hálózat) | Végponti ACL-eket. | Távolítsa el a végponti ACL-eket, és ismételje meg az áttelepítés. |
| Network (Hálózat) | Application Gateway | Távolítsa el az Alkalmazásátjáró áttelepítésének megkezdése előtt, és ezután hozza létre újból az Application Gateway áttelepítés befejezése után. |
| Network (Hálózat) | Virtuális hálózatok használatával a Vnetben társviszony-létesítés. | Telepítse át a virtuális hálózati erőforrás-kezelő, majd a partnert. További információ [Vnetben társviszony-létesítés](../articles/virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Nem támogatott konfigurációk
A következő konfigurációk jelenleg nem támogatottak.

| Szolgáltatás | Konfiguráció | Ajánlás |
| --- | --- | --- |
| Resource Manager |Szerepköralapú hozzáférés vezérlés (RBAC) hagyományos erőforrások |Az erőforrás URI az áttelepítés után módosult, mert ajánlott, hogy megtörténjen-e az áttelepítés után kell RBAC házirend-frissítési tervezi. |
| Számítás |A virtuális gépek társított több alhálózattal |Frissítse a alhálózati konfigurációt való hivatkozáshoz csak egy alhálózattal rendelkezik. Ez előfordulhat, hogy egy másodlagos hálózati adapter (hivatkozik egy másik alhálózat) eltávolítása a virtuális Gépet, majd csatlakoztassa újra áttelepítés befejeződése után. |
| Számítás |Virtuális gépek, virtuális hálózathoz tartozó, de nincs hozzárendelve egy explicit alhálózatot |Törölheti a virtuális gép nem kötelező. |
| Számítás |Riasztások, automatikus skálázás házirendek rendelkező virtuális gépek |Az áttelepítés végighalad, és ezeket a beállításokat a rendszer eldobja. Erősen ajánlott a környezet értékeléséhez, az áttelepítés előtt. Újrakonfigurálhatja azt is megteheti, az értesítés beállításait az áttelepítés befejezése után. |
| Számítás |XML-Virtuálisgép-bővítmények (BGInfo 1.*, a Visual Studio hibakereső funkcióját, a Web Deploy és távoli hibakeresés) |Ez nem támogatott. Javasoljuk, hogy ezek a bővítmények eltávolítása a virtuális gép áttelepítése a folytatáshoz, vagy azok el lesz dobva, automatikusan az áttelepítési folyamat során. |
| Számítás |Prémium szintű Storage a rendszerindítási diagnosztika |Áttelepítés folytatása előtt tiltsa le a virtuális gépek a rendszerindítási diagnosztikai funkciót. Az áttelepítés befejezése után újra engedélyezheti a Resource Manager-készletben a rendszerindítási diagnosztikát. Emellett képernyőkép és soros naplók használt blobot törölni kell, már nem az adott blobok van szó. |
| Számítás | Webes vagy feldolgozói szerepköröket tartalmazó felhőszolgáltatások | Ez jelenleg nem támogatott. |
| Számítás | Egynél több rendelkezésre állási tartalmazó felhőszolgáltatások be, vagy több rendelkezésre állási készletek. |Ez jelenleg nem támogatott. Helyezze át a virtuális gépek ugyanabban a rendelkezésre állási áttelepítése előtt állítsa be. |
| Számítás | Az Azure Security Center kiterjesztésű VM | Az Azure Security Center bővítményeket automatikusan telepíti a virtuális gépeken, a biztonsági figyelésére, és riasztást. Ezek a bővítmények általában telepíteni automatikusan Ha az Azure Security Center házirend engedélyezve van az előfizetésben. A virtuális gépek áttelepítéséhez, tiltsa le az előfizetéshez, amely eltávolítja a bővítményt a virtuális gépekről származó figyelési Security Center biztonsági center-házirendben. |
| Számítás | Virtuális gép biztonsági mentését, illetve a pillanatkép-bővítménnyel | Ezek a bővítmények települnek az Azure Backup szolgáltatással konfigurált virtuális gépen. Ezek a virtuális gépek áttelepítéséhez kövesse az útmutatást [Itt](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#vault).  |
| Network (Hálózat) |Virtuális hálózatok, virtuális gépek és a webes vagy feldolgozói szerepköröket tartalmazó |Ez jelenleg nem támogatott. Helyezze át a webes vagy feldolgozói szerepköröket a saját virtuális hálózat áttelepítése előtt. Miután a klasszikus virtuális hálózaton át az áttelepített az Azure Resource Manager Virtual Network is társítottak, hasonló konfigurációt azt korábban eléréséhez a klasszikus virtuális hálózattal.|
| Network (Hálózat) | Klasszikus Expressroute-Kapcsolatcsoportok |Ez jelenleg nem támogatott. Ezek a kapcsolatok kell áttelepíteni az Azure Resource Manager infrastruktúra-szolgáltatási áttelepítésének megkezdése előtt. További információt a további részletekért lásd [a Resource Manager üzembe helyezési modellben a klasszikus áthelyezése ExpressRoute-Kapcsolatcsoportok](../articles/expressroute/expressroute-move.md).|
| Azure App Service |Virtuális hálózatok, amelyek tartalmazzák az App Service-környezetek |Ez jelenleg nem támogatott. |
| Azure HDInsight |Virtuális hálózatok, amelyek tartalmazzák a HDInsight-szolgáltatások |Ez jelenleg nem támogatott. |
| A Microsoft Dynamics életciklus szolgáltatások |Dynamics életciklus szolgáltatások által kezelt virtuális gépeket tartalmazó virtuális hálózatok |Ez jelenleg nem támogatott. |
| Azure AD Domain Services |Virtuális hálózatok, amelyek tartalmazzák az Azure AD tartományi szolgáltatások |Ez jelenleg nem támogatott. |
| Azure RemoteApp |Virtuális hálózatok, amelyek tartalmazzák az Azure RemoteApp központi telepítések |Ez jelenleg nem támogatott. |
| Azure API Management |Virtuális hálózatok, amelyek tartalmazzák az Azure API Management központi telepítések |Ez jelenleg nem támogatott. Az infrastruktúra-szolgáltatási virtuális hálózaton át, módosítsa a virtuális hálózat, az API Management környezetben, amely nincs állásidő művelet. |
