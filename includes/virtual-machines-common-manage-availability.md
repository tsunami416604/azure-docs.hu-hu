---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 5350ecdd3b73894e43db3b9f342fc657cf73f224
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76268331"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>A virtuális gépek újraindításának ismertetése – karbantartás és állásidő
Az Azure-beli virtuális gépeket három forgatókönyv befolyásolja: nem tervezett hardveres karbantartás, váratlan leállás és tervezett karbantartás.

* **Nem tervezett hardverkarbantartási esemény** akkor fordul elő, ha az Azure platform előre jelzi, hogy egy adott fizikai kiszolgálóhoz tartozó hardver vagy bármely platformösszetevő meg fog hibásodni. Amikor a platform előre jelez egy hibát, kiad egy nem tervezett hardverkarbantartási eseményt az adott hardveren futtatott virtuális gépekre gyakorolt hatás csökkentése érdekében. Az Azure [élő áttelepítés](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) technológiával telepíti át a Virtual Machinest a hibás hardverről egy kifogástalan fizikai gépre. Az Élő áttelepítés a virtuális gépet megőrző művelet, amely csak minimális időre állítja le a virtuális gépet. A memória tartalmát, a megnyitott fájlokat és a hálózati kapcsolatokat mind megőrzi, azonban a teljesítmény az esemény előtt és/vagy után csökkenhet. Ha az Élő áttelepítés nem használható, a virtuális gépen váratlan állásidő következik be az alábbiak szerint.


* **Váratlan leállás** esetén a virtuális gép hardvere vagy fizikai infrastruktúrája váratlanul leáll. Ebbe beletartozhatnak a helyi hálózati hibák, a helyi lemezek meghibásodása vagy más rack-szintű hibák. Ha a rendszer észleli, az Azure platform automatikusan áttelepíti (meggyógyítja) a virtuális gépet egy olyan kifogástalan fizikai gépre, amely ugyanabban az adatközpontban található. A javítási folyamat során a virtuális gép állásideje következik be (újraindítás), valamint bizonyos esetekben elveszhet az ideiglenes meghajtó. A csatlakoztatott rendszer- és adatmeghajtók minden esetben megmaradnak.

  A virtuális gépek a teljes adatközpontot vagy akár egy teljes régiót érintő, váratlan leállás vagy vészhelyzet esetén nem valószínű eseményen is előfordulhatnak állásidő. Ezekben az esetekben az Azure olyan védelmi lehetőségeket biztosít, mint a [rendelkezésre állási zónák](../articles/availability-zones/az-overview.md) és a [párosított régiók](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

* A **tervezett karbantartási események** a Microsoft által a mögöttes Azure platformon végzett időszakos frissítések, amelyek célja a virtuális gépeket futtató platforminfrastruktúra általános megbízhatóságának, teljesítményének és biztonságának növelése. A frissítések a legtöbb esetben semmilyen hatással nincsenek a virtuális gépek vagy a felhőszolgáltatások működésére (lásd: [VM-megőrző karbantartás](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance)). Bár az Azure platform minden esetben VM-megőrző karbantartást igyekszik végrehajtani, egyes ritka esetekben a frissítések megkövetelik a virtuális gép újraindítását, hogy a szükséges frissítéseket telepíteni lehessen a mögöttes infrastruktúrára. Ilyen esetekben karbantartással és ismételt üzembe helyezéssel kiegészített Azure tervezett karbantartási eljárást hajthat végre, ha a megfelelő időtartományban kezdeményezi a virtuális gépek karbantartását. További információkért lásd: [Virtuális gépek tervezett karbantartása](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/).


Az ilyen események okozta állásidő hatásainak csökkentése érdekében javasoljuk, hogy kövesse az alábbi bevált gyakorlatokat a virtuális gépek magas rendelkezésre állásának biztosításához:

* [Több virtuális gép rendelkezésre állási csoportba konfigurálása a redundancia biztosítása érdekében]
* [Felügyelt lemezek használata rendelkezésre állási csoporthoz tartozó virtuális gépekkel]
* [Ütemezett események használatával proaktív módon válaszolhat a virtuális gépeket érintő eseményekre](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Az egyes alkalmazásrétegek külön rendelkezésre állási csoportokba konfigurálása]
* [Terheléselosztók és rendelkezésre állási csoportok együttes alkalmazása]
* [A rendelkezésre állási zónák használata az adatközponti szintű hibák elleni védelemhez]

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>A rendelkezésre állási zónák használata az adatközponti szintű hibák elleni védelemhez

A [rendelkezésre állási zónák](../articles/availability-zones/az-overview.md) kibővítik a vezérlés szintjét, hogy a virtuális gépeken elérhető alkalmazások és adatmennyiségek rendelkezésre álljanak. A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében az összes engedélyezett régióban legalább három különálló zónának kell lennie. Egy régión belüli Availability Zones fizikai elkülönítése megvédi az alkalmazásokat és az adatközpontok meghibásodását. Zóna – a redundáns szolgáltatások az alkalmazások és az adatok replikálását Availability Zones az egypontos meghibásodások elleni védelem érdekében.

Az Azure-régiók rendelkezésre állási zónái egy tartalék **tartomány** és egy **frissítési tartomány**kombinációja. Ha például három vagy több virtuális gépet hoz létre három zónában egy Azure-régióban, a virtuális gépeket a rendszer gyakorlatilag három tartalék tartományba és három frissítési tartományba terjeszti. Az Azure platform felismeri ezt az eloszlást a frissítési tartományok között, hogy megbizonyosodjon róla, hogy a különböző zónákban lévő virtuális gépek nem frissülnek egyidőben.

Az Azure Availability Zones az iparág legjobb 99,99%-os rendelkezésre állását kínálja a virtuális gép számára. Ha a megoldásait a zónákban lévő replikált virtuális gépek használatára kívánja használni, az alkalmazások és az adatok az adatközpont elvesztése miatt is védhetők. Ha egy zóna biztonsága sérül, a replikált alkalmazások és az adatszolgáltatások azonnal elérhetők lesznek egy másik zónában.

![Rendelkezésre állási zónák](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

További információ a Windows vagy [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) [rendszerű](../articles/virtual-machines/windows/create-powershell-availability-zone.md) virtuális gépek rendelkezésre állási zónában való üzembe helyezéséről.

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Több virtuális gép rendelkezésre állási csoportba konfigurálása a redundancia biztosítása érdekében
A rendelkezésre állási csoportok egy másik adatközpont-konfiguráció, amely biztosítja a virtuális gépek redundanciát és rendelkezésre állását. Ez az adatközponton belüli konfiguráció biztosítja, hogy a tervezett vagy nem tervezett karbantartási események során legalább egy virtuális gép elérhető legyen, és megfelel a 99,95%-os Azure SLA-nak. További információkért lásd [a virtuális gépek esetében érvényes SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) részleteit.

> [!IMPORTANT]
> Ha lehet, önmagában ne helyezzen egyetlen különálló virtuális gépet egy rendelkezésre állási csoportba. Az ebben a konfigurációban lévő virtuális gépek nem felelnek meg SLA-garanciának és az Azure tervezett karbantartási események során felmerülő állásidőnek, kivéve, ha egyetlen virtuális gép használja az [Azure Premium SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd)-ket. A prémium SSD-ket használó önálló virtuális gépek esetében az Azure SLA érvényes.

A mögöttes Azure platform a rendelkezésre állási csoportban lévő mindegyik virtuális gépnek kioszt egy **frissítési tartományt** és egy **tartalék tartományt**. Minden eges rendelkezésre állási csoportnak öt, felhasználó által nem konfigurálható frissítési tartományt oszt ki a rendszer alapértelmezés szerint (a Resource Manager-környezetek ezután akár 20 frissítési tartományig növelhetőek), és így kijelöli azokat a virtuális gépeket és mögöttes fizikai hardvereszközöket magukba foglaló csoportokat, amelyek egy időben újraindíthatóak. Ha egy rendelkezésre állási csoportba ötnél több virtuális gép van konfigurálva, a hatodik virtuális gép az elsővel azonos frissítési tartományba kerül, a hetedik a másodikkal és így tovább. A frissítési tartományok újraindítása nem haladhat szekvenciálisan a tervezett karbantartás során, hanem csak egyetlen frissítési tartományt lehet újraindítani egyszerre. Az újraindított frissítési tartománynak 30 perce van, hogy helyreálljon, mielőtt a karbantartás elkezdődik egy másik frissítési tartományon.

A tartalék tartományok azonos tápforrással és hálózati kapcsolóval rendelkező virtuális gépek csoportjai. Alapértelmezés szerint a rendelkezésre állási csoportba konfigurált virtuális gépek legfeljebb három tartalék tartományba vannak elkülönítve a Resource Manager-környezetben (két tartalék tartományba a klasszikus környezetben). Bár a virtuális gépek rendelkezésre állási csoportokba rendezése nem védi az alkalmazást az operációs rendszerre vezethető vagy az alkalmazásspecifikus hibáktól, korlátozza a potenciális hardvermeghibásodások, hálózatkimaradások vagy tápellátás-megszakadások hatását.

<!--Image reference-->
   a frissítési tartomány és a tartalék tartomány konfigurációjának ![fogalmi rajza](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Felügyelt lemezek használata rendelkezésre állási csoporthoz tartozó virtuális gépekkel
Ha jelenleg a virtuális gépeket nem felügyelt lemezeken használja, határozottan ajánlott [a rendelkezésre állási csoportban lévő virtuális gépeket átalakítása, hogy felügyelt lemezeken fussanak](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

A [felügyelt lemezek](../articles/virtual-machines/windows/managed-disks-overview.md) jobb rendelkezésre állást nyújtanak a rendelkezésre állási csoportok számára, mivel biztosítják a rendelkezésre állási csoportban lévő virtuális gépek lemezeinek megfelelő elszigetelését a kritikus hibapontok elkerülése érdekében. Ezt úgy teszi meg, hogy a lemezeket a különböző tárolási tartalék tartományokban (Storage-fürtökben) helyezi el, és a virtuálisgép-tartalék tartományhoz igazítja őket. Ha a tárolási tartalék tartomány hardveres vagy szoftveres hiba miatt meghiúsul, akkor csak a tárolási tartalék tartomány lemezeit tartalmazó virtuálisgép-példány meghiúsul.
![felügyelt lemezek tartalék](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> A felügyelt rendelkezésre állási csoportok tartalék tartományainak száma régiónként eltérő – régiónként kettő vagy három lehet. Az egyes régiók tartalék tartományát a következő parancsfájlok futtatásával tekintheti meg.

```azurepowershell-interactive
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli-interactive 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> Megjegyzés: bizonyos esetekben előfordulhat, hogy az azonos üzemmódú két virtuális gép ugyanazon a FaultDomain van megosztva. Ezt megerősítheti a üzemmódú, és a "tartalék tartomány" oszlopot is megtekintheti.
> Ez a viselkedés akkor figyelhető meg, ha a virtuális gépek telepítése során a következő folyamat történt:
> - Az első virtuális gép üzembe helyezése
> - Az első virtuális gép leállítása/felszabadítása
> - A 2. virtuális gép üzembe helyezése ilyen körülmények között előfordulhat, hogy a második virtuális gép operációsrendszer-lemeze ugyanazon a tartalék tartományon jön létre, mint az első virtuális gép, így a második virtuális gép is ugyanazon a FaultDomain fog futni. 
> A probléma elkerülése érdekében javasoljuk, hogy ne állítsa le/szabadítsa fel a virtuális gépet az üzemelő példányok között.

Ha nem felügyelt lemezekkel rendelkező virtuális gépeket szeretne használni, kövesse az alábbi ajánlott eljárásokat azokhoz a tárolási fiókokhoz, amelyekben a virtuális merevlemezek (VHD-k) [blobként](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs)vannak tárolva.

1. **Tárolja az egyazon virtuális géppel társított összes lemezt (operációsrendszer- és adatlemezt) ugyanabban a tárfiókban.**
2. **Tekintse át az Azure Storage-fiókban nem felügyelt lemezek számának [korlátozásait](../articles/storage/blobs/scalability-targets-premium-page-blobs.md)**  , mielőtt további virtuális merevlemezeket adna hozzá egy Storage-fiókhoz
3. **Használjon külön Storage-fiókot a rendelkezésre állási csoportba tartozó egyes virtuális gépekhez.** Ne tárolja az egyazon rendelkezésre állási csoportban lévő virtuális gépeket ugyanabban a Storage-fiókban. A különböző rendelkezésre állási csoportokban lévő virtuális gépek számára elfogadható a Storage-fiókok megosztása, ha az ajánlott eljárások követése ![nem felügyelt lemezek tartalék](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Ütemezett események használatával proaktív módon válaszolhat a virtuális gépeket érintő eseményekre

Az [ütemezett eseményekre](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)való előfizetéskor a virtuális gép értesítést kap arról, hogy a közelgő karbantartási események milyen hatással lehetnek a virtuális gépre. Ha az ütemezett események engedélyezve vannak, a virtuális gép a karbantartási tevékenység végrehajtása előtt legalább annyi időt kap. Például a gazdagép operációs rendszerének azon frissítései, amelyek hatással lehetnek a virtuális gépre, olyan események, amelyek meghatározzák a hatást, valamint azt, hogy a karbantartás mikor történjen, ha nincs művelet. Az ütemezett események akkor is várólistára kerülnek, amikor az Azure olyan közvetlen hardverhiba-meghibásodást észlel, amely hatással lehet a virtuális gépre, ami lehetővé teszi, hogy eldöntse, mikor kell elvégezni a gyógyulást. A-ügyfelek az eseményt a karbantartás előtt is elvégezhetik, például az állapot mentése, a másodlagos feladatátvétel és így tovább. Miután elvégezte a logikát a karbantartási esemény zökkenőmentes kezeléséhez, jóváhagyhatja a függőben lévő ütemezett eseményt, hogy a platform folytassa a karbantartással.

## <a name="configure-each-application-tier-into-separate-availability-zones-or-availability-sets"></a>Az egyes alkalmazások szintjeinek beállítása különálló rendelkezésre állási zónákra vagy rendelkezésre állási csoportokra
Ha a virtuális gépek csaknem azonosak, és ugyanazt a célt szolgálják az alkalmazáshoz, javasoljuk, hogy az alkalmazás minden egyes szintjéhez konfiguráljon egy rendelkezésre állási zónát vagy rendelkezésre állási készletet.  Ha két különböző szintet helyez el ugyanabban a rendelkezésre állási zónában vagy készletben, akkor az azonos alkalmazási szinten lévő összes virtuális gép újraindítható egyszerre. Ha legalább két virtuális gépet konfigurál egy rendelkezésre állási zónában, vagy beállítja az egyes rétegeket, akkor garantálhatja, hogy az egyes szinteken legalább egy virtuális gép elérhető.

Például az IIS, az Apache és az Nginx egyetlen rendelkezésre állási zónában vagy a set-ben való futtatásával az alkalmazás kezelőfelületének összes virtuális gépet üzembe helyezheti. Győződjön meg arról, hogy csak az előtér-virtuális gépek vannak elhelyezve ugyanahhoz a rendelkezésre állási zónához vagy a készlethez. Hasonlóképpen ügyeljen arra, hogy csak az adatrétegbeli virtuális gépek legyenek elhelyezve a saját rendelkezésre állási zónájában vagy készletében, például a replikált SQL Server virtuális gépekhez vagy a MySQL virtuális gépekhez.

<!--Image reference-->
   ![alkalmazási rétegek](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>Terheléselosztó kombinálása rendelkezésre állási zónákkal vagy készletekkel
Egyesítse a [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) egy rendelkezésre állási zónával, vagy állítsa be a legtöbb alkalmazás rugalmasságát. Az Azure Load Balancer több virtuális gép között osztja el a forgalmat. A Standard csomagban elérhető virtuális gépek esetében az Azure Load Balancer a csomag részét képezi. Nem mindegyik virtuális gép csomagja tartalmazza az Azure Load Balancert. A virtuális gépek terheléselosztásáról további információkért lásd a [virtuális gépek terheléselosztását](../articles/virtual-machines/virtual-machines-linux-load-balance.md) ismertető témakört.

Ha a terheléselosztó nem úgy van konfigurálva, hogy a terhelést elossza több virtuális gép között, a tervezett karbantartás hatással lesz az egyetlen forgalomkiszolgáló virtuális gépre, ami kimaradást okoz az alkalmazásrétegben. Ha több egy rétegbe tartozó virtuális gépet helyez egyazon terheléselosztó és rendelkezésre állási csoport alá, a forgalmat mindig legalább egy példány képes lesz kiszolgálni.

A rendelkezésre állási zónák közötti terheléselosztással kapcsolatos oktatóanyagért lásd: [virtuális gépek terheléselosztása az összes rendelkezésre állási zónában az Azure CLI használatával](../articles/load-balancer/load-balancer-standard-public-zone-redundant-cli.md).


<!-- Link references -->
[Több virtuális gép rendelkezésre állási csoportba konfigurálása a redundancia biztosítása érdekében]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Az egyes alkalmazásrétegek külön rendelkezésre állási csoportokba konfigurálása]: #configure-each-application-tier-into-separate-availability-zones-or-availability-sets
[Terheléselosztók és rendelkezésre állási csoportok együttes alkalmazása]: #combine-a-load-balancer-with-availability-zones-or-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Felügyelt lemezek használata rendelkezésre állási csoporthoz tartozó virtuális gépekkel]: #use-managed-disks-for-vms-in-an-availability-set
[A rendelkezésre állási zónák használata az adatközponti szintű hibák elleni védelemhez]: #use-availability-zones-to-protect-from-datacenter-level-failures
