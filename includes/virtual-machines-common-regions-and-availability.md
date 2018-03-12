# <a name="regions-and-availability-for-virtual-machines-in-azure"></a>Az Azure-beli virtuális gépek régiók szerinti csoportosítása és rendelkezésre állása
Az Azure világszerte számos adatközpontban működik. Ezek az adatközpontok földrajzi régiók szerint vannak csoportosítva, ami kellő mozgásteret biztosít az alkalmazások létrehozási helyének megválasztásához. Fontos átlátni, hogy a virtuális gépek hol és hogyan működnek az Azure-ban, illetve hogy a teljesítmény, a rendelkezésre állás és a redundancia maximalizálása terén milyen lehetőségek vannak. Ez a cikk az Azure rendelkezésre állással és redundanciával kapcsolatos szolgáltatásairól nyújt áttekintést.

## <a name="what-are-azure-regions"></a>Mik azok az Azure-régiók?
A megadott földrajzi régióban "USA nyugati régiója", "Észak-Európa" vagy "Délkelet-Ázsia" Azure-erőforrások hoz létre. [A régiók és a kapcsolódó helyek listáját itt](https://azure.microsoft.com/regions/) tekintheti meg. A redundancia és a rendelkezésre állás biztosítása érdekében minden egyes régióban egyszerre több adatközpont működik. Ez a módszer rugalmasságot biztosít legközelebbi a felhasználók számára létrehozott egy virtuális gépet, és a felel meg a jogi, megfelelőségi vagy adó szempontjából alkalmazásokhoz tervezéséhez.

## <a name="special-azure-regions"></a>Különleges Azure-régiók
Azure rendelkezik néhány speciális régióban, előfordulhat, hogy szeretné használni az alkalmazások megfelelőségi vagy jogi céllal kimenő felépítése közben. Ezek a különleges régiók a következők:

* **USA-beli államigazgatás – Virginia** és **USA-beli államigazgatás – Iowa**
  * Az Azure fizikailag és logikailag elszigetelt példánya az USA-beli államigazgatási szervek és partnereik számára, amelyet biztonsági szempontból átvilágított, USA-beli személyek kezelnek. Olyan további megfelelőségi tanúsítványokat is tartalmaz, mint a [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) vagy a [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Tudjon meg többet az [Azure Governmentről](https://azure.microsoft.com/features/gov/).
* **Kelet-Kína** és **Észak-Kína**
  * Ezek a régiók a Microsoft és a 21Vianet közötti különleges partnerség révén érhetők el, amelyben az adatközpontok fenntartója nem közvetlenül a Microsoft. Tudjon meg többet a [Microsoft Azure kínai működéséről](http://www.windowsazure.cn/).
* **Közép-Németország** és **Északkelet-Németország**
  * Ezek a területek amellyel ügyféladatok Németországban ellenőrzés alá eső T-rendszerek, a német Telekom vállalati, a német adatok adatkezelő működött adatkezelő adatmodellt keresztül érhetők el.

## <a name="region-pairs"></a>Régiópárok
Minden egyes Azure-régió párban áll egy másik, azonos földrajzi helyhez tartozó régióval (amilyen például az USA, Európa vagy Ázsia). Ez lehetővé teszi az erőforrások, így például a virtuálisgép-tárolók földrajzi hely szerinti replikálását abból a megfontolásból, hogy természeti katasztrófák, zavargások, áramkimaradások vagy a fizikai hálózat fennakadásai kisebb eséllyel jelentkeznek egyszerre mindkét régióban. A régiópárok további előnyei még a következők:

* Az Azure szélesebb körű leállása esetén minden párból az egyik régió előnyt élvez, ami segít csökkenteni az alkalmazások helyreállításához szükséges időt. 
* A tervezett Azure-frissítések egyszerre csak a régiópár egyik tagján jelennek meg, ami csökkenti az állásidőt és az alkalmazáskimaradás kockázatát.
* Az adatok a pár mindkét tagja esetében ugyanazon a földrajzi helyen maradnak (Dél-Brazília kivételével), ami adóügyi és törvényi szempontból fontos.

Néhány példa a régiópárokra:

| Elsődleges | Másodlagos |
|:--- |:--- |
| USA nyugati régiója |USA keleti régiója |
| Észak-Európa |Nyugat-Európa |
| Délkelet-Ázsia |Kelet-Ázsia |

[A régiópárok teljes listája](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions) itt érhető el.

## <a name="feature-availability"></a>Szolgáltatások rendelkezésre állása
Néhány szolgáltatás és virtuálisgép-funkció, például meghatározott méretű virtuális gépek vagy adott tárolótípusok csak bizonyos régiókban érhetők el. Emellett létezik pár olyan globális Azure-szolgáltatás is, amelyhez nem kell régiót választani. Ilyen például az [Azure Active Directory](../articles/active-directory/active-directory-whatis.md), a [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md) vagy az [Azure DNS](../articles/dns/dns-overview.md). Az alkalmazáskörnyezet megtervezéséhez javasoljuk, hogy tekintse meg [az Azure-szolgáltatások régiók szerinti rendelkezésre állását](https://azure.microsoft.com/regions/#services). Emellett [programozott módon lekérdezni a támogatott Virtuálisgép-méretek és minden régióban korlátozások](../articles/azure-resource-manager/resource-manager-sku-not-available-errors.md).

## <a name="storage-availability"></a>Tárterület rendelkezésre állása
A rendelkezésre álló tárreplikációs lehetőségek mérlegeléséhez fontos átlátni az Azure-régiók és földrajzi helyek működését. A tárterület típusától függően eltérő replikálási lehetőségek állnak rendelkezésre.

**Azure Managed Disks**
* Helyileg redundáns tárolás (LRS)
  * A rendszer háromszor replikálja az adatokat abban a régióban, amelyben a tárfiókot létrehozták.

**Tárfiókalapú lemezek**
* Helyileg redundáns tárolás (LRS)
  * A rendszer háromszor replikálja az adatokat abban a régióban, amelyben a tárfiókot létrehozták.
* Zónaredundáns tárolás (ZRS)
  * A rendszer két vagy három intézményben háromszor replikálja az adatokat, amelyek egy vagy két régióban is lehetnek.
* Georedundáns tárolás (GRS)
  * A rendszer egy másodlagos régióba replikálja az adatokat, amely a forrásadatok elsődleges helyétől több száz kilométerre található.
* Írásvédett georedundáns tárolás (RA-GRS)
  * A rendszer egy másodlagos régióba replikálja az adatokat (csakúgy, mint a GRS esetében), és az adatokhoz a másodlagos helyszínen is írásvédett hozzáférést biztosít.

A következő táblázat a tárreplikáció típusai közötti különbségek rövid összefoglalását tartalmazza:

| Replikációs stratégia | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| A rendszer több intézményben replikálja az adatokat. |Nem |Igen |Igen |Igen |
| Az adatok a másodlagos és az elsődleges helyről is olvashatók. |Nem |Nem |Nem |Igen |
| A külön csomópontokon fenntartott adatmásolatok száma. |3 |3 |6 |6 |

[Az Azure tárreplikációs lehetőségeiről itt](../articles/storage/common/storage-redundancy.md) olvashat bővebben. További információ a felügyelt lemezekről: [Azure Managed Disks – áttekintés](../articles/virtual-machines/windows/managed-disks-overview.md).

### <a name="storage-costs"></a>Tárolási költségek
Az árak a választott tárolótípus és rendelkezésre állás függvényében változnak.

**Azure Managed Disks**
* Prémium szintű felügyelt lemez üzemelnek Solid-State meghajtók (SSD), és a standard szintű felügyelt lemez rendszeres forgó lemezek üzemelnek. Mind a Premium, mind a Standard Managed Disks szolgáltatás díjszabása a kiépített lemezkapacitás szerint alakul.

**Nem felügyelt lemezek**
* Prémium szintű storage Solid-State meghajtók (SSD-k) által támogatott, és a lemez kapacitását alapján terheli.
* A Standard szintű tárterület általános merevlemezekkel működik, a díjszabás pedig a kihasznált kapacitástól és a rendelkezésre álló, választott tárterülettől függ.
  * Az RA-GRS esetében egy további georeplikációs adatátviteli díj is érvényben van, az adatok másik Azure-régióba történő replikálásához használt sávszélességre vonatkozóan.

A különböző tártípusokhoz és rendelkezésre állási lehetőségekhez tartozó díjszabásról lásd: [Az Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).

## <a name="availability-sets"></a>Rendelkezésre állási csoportok
Egy rendelkezésre állási csoportok pedig egy adatközpontban, amely lehetővé teszi a megértése, hogyan épül fel az alkalmazás redundancia és rendelkezésre állás biztosításához az Azure virtuális gépek logikai csoportosítása. Azt javasoljuk, hogy két vagy több virtuális gépek rendelkezésre állási készlet megfelelő és adjon meg egy magas rendelkezésre állású alkalmazáshoz belül jöttek létre a [99,95 % Azure garantált szolgáltatási szintje](https://azure.microsoft.com/support/legal/sla/virtual-machines/). A rendelkezésre állási csoport költség nélkül magát, a csak kell fizetnie az Ön által létrehozott minden egyes Virtuálisgép-példány. Ha az önálló virtuális gép [Azure Premium Storage](../articles/virtual-machines/windows/premium-storage.md) csomagot használ, az Azure SLA a nem tervezett karbantartási események során is érvényben van. 

Egy rendelkezésre állási csoportot, amely a hardver meghibásodása elleni védelem érdekében, és beállítható a frissítések biztonságosan alkalmazható - tartományok (FDs) és a frissítési tartományok (UDs) fault két további csoporthoz tevődik össze. Tudjon meg többet a [Linux-alapú virtuális gépek](../articles/virtual-machines/linux/manage-availability.md), illetve a [Windows-alapú virtuális gépek](../articles/virtual-machines/windows/manage-availability.md) rendelkezésre állásának kezeléséről.

### <a name="fault-domains"></a>Tartalék tartományok
A tartalék tartomány a mögöttes hardverelemek logikus csoportja, ahol az áramforrás és a hálózati kapcsoló közös, a helyszíni adatközpontok állványaihoz hasonlóan. Amikor rendelkezésre állási csoporton belül hoz létre virtuális gépeket, az Azure platform automatikusan elosztja ezeket a tartalék tartományok között. Ez a módszer korlátozza a potenciális hardvermeghibásodások, hálózatkimaradások vagy a tápellátás megszakadásának hatását.

### <a name="update-domains"></a>Frissítési tartományok
A frissítési tartomány a mögöttes hardverelemek logikus csoportja, amelyen egyszerre végezhető karbantartás vagy újraindítás. Amikor rendelkezésre állási csoporton belül hoz létre virtuális gépeket, az Azure platform automatikusan elosztja ezeket a frissítési tartományok között. Ez lehetővé teszi, hogy az alkalmazás legalább egy példánya mindig fusson akkor is, ha az Azure platformon épp rendszeres karbantartás folyik. A frissítési tartományok újraindítása nem haladhat szekvenciálisan a tervezett karbantartás során, hanem csak egyetlen frissítési tartományt lehet újraindítani egyszerre.

### <a name="managed-disk-fault-domains"></a>Lemez tartalék tartományok kezelése
Az [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md) használatával létrehozott virtuális gépek felügyelt rendelkezésre állási csoportok használata esetén felügyelt lemezes tartalék tartományokra vannak elosztva. Ez a kiosztás biztosítja, hogy a virtuális géphez csatolt mindegyik felügyelt lemez ugyanabban a felügyelt lemezes tartalék tartományban legyen. Kizárólag felügyelt lemezeken futó virtuális gépek hozhatók létre felügyelt rendelkezésre állási csoportokban. A felügyelt lemezes tartalék tartományok száma régiónként eltérő – régiónként kettő vagy három felügyelt lemezes tartalék tartomány lehet. További ezekről a lemez tartalék tartományainak felügyelt [Linux virtuális gépek](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) vagy [Windows virtuális gépek](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

## <a name="availability-zones"></a>Rendelkezésre állási zónák

[Rendelkezésre állási zónák](../articles/availability-zones/az-overview.md) (előzetes verzió), ahelyett, hogy a rendelkezésre állási állítja be, bontsa ki a felügyeleti, hogy az alkalmazások és a virtuális gépeken adatok rendelkezésre állását fenntartásához. A rendelkezésre állási zónák egy Azure-régió fizikailag elkülönített zónáit jelentik. Három rendelkezésre állási zónák / támogatott Azure-régióban van. Egyes rendelkezésre állási zóna rendelkezik különálló kapcsolja a forrás-, hálózati és hűtési, és logikailag elkülönül a többi rendelkezésre állási zóna belül az Azure-régió. Újratervezni a megoldások replikált virtuális gépek használata a zónák, megvédheti az alkalmazások és adatok származó az adatközpontban. Ha egy zóna biztonsága sérül, majd replikált alkalmazások és adatok érhetők el azonnal egy másik zónában. 

![Rendelkezésre állási zónák](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

[!INCLUDE [availability-zones-preview-statement.md](availability-zones-preview-statement.md)]

Telepítésével kapcsolatban a [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) vagy [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) VM egy rendelkezésre állási zónában.

## <a name="next-steps"></a>További lépések
Mostantól a saját Azure-környezetében is használhatja ezeket a rendelkezésre állási és redundanciával kapcsolatos szolgáltatásokat. Javasoljuk, hogy tájékozódjon [az Azure rendelkezésre állásával kapcsolatos ajánlott eljárásokról](../articles/best-practices-availability-checklist.md).

