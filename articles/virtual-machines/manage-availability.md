---
title: Virtuális gépek rendelkezésre állásának kezelése
description: Ismerje meg, hogyan használható több virtuális gép az alkalmazások magas rendelkezésre állásának biztosításához az Azure-ban
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cynthn
ms.openlocfilehash: fe89b58f71b14b211863fd46ba523e8c866764f1
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361789"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Linux rendszerű virtuális gépek rendelkezésre állásának kezelése

Ismerje meg, hogyan állíthat be és kezelhet több virtuális gépet az Azure-beli linuxos alkalmazások magas rendelkezésre állásának biztosítása érdekében. 


## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>A virtuális gépek újraindításának ismertetése – karbantartás és állásidő
Az Azure-beli virtuális gépeket három forgatókönyv befolyásolja: nem tervezett hardveres karbantartás, váratlan leállás és tervezett karbantartás.

* **Nem tervezett hardverkarbantartási esemény** akkor fordul elő, ha az Azure platform előre jelzi, hogy egy adott fizikai kiszolgálóhoz tartozó hardver vagy bármely platformösszetevő meg fog hibásodni. Amikor a platform előre jelez egy hibát, kiad egy nem tervezett hardverkarbantartási eseményt az adott hardveren futtatott virtuális gépekre gyakorolt hatás csökkentése érdekében. Az Azure [élő áttelepítés](./linux/maintenance-and-updates.md) technológiával telepíti át a Virtual Machinest a hibás hardverről egy kifogástalan fizikai gépre. Az Élő áttelepítés a virtuális gépet megőrző művelet, amely csak minimális időre állítja le a virtuális gépet. A memória tartalmát, a megnyitott fájlokat és a hálózati kapcsolatokat mind megőrzi, azonban a teljesítmény az esemény előtt és/vagy után csökkenhet. Ha az Élő áttelepítés nem használható, a virtuális gépen váratlan állásidő következik be az alábbiak szerint.


* **Váratlan leállás** esetén a virtuális gép hardvere vagy fizikai infrastruktúrája váratlanul leáll. Ebbe beletartozhatnak a helyi hálózati hibák, a helyi lemezek meghibásodása vagy más rack-szintű hibák. Ha a rendszer észleli, az Azure platform automatikusan áttelepíti (meggyógyítja) a virtuális gépet egy olyan kifogástalan fizikai gépre, amely ugyanabban az adatközpontban található. A javítási folyamat során a virtuális gép állásideje következik be (újraindítás), valamint bizonyos esetekben elveszhet az ideiglenes meghajtó. A csatlakoztatott rendszer- és adatmeghajtók minden esetben megmaradnak.

  A virtuális gépek a teljes adatközpontot vagy akár egy teljes régiót érintő, váratlan leállás vagy vészhelyzet esetén nem valószínű eseményen is előfordulhatnak állásidő. Ezekben az esetekben az Azure olyan védelmi lehetőségeket biztosít, mint a  [rendelkezésre állási zónák](../availability-zones/az-overview.md) és a [párosított régiók](regions.md#region-pairs).

* A **tervezett karbantartási események** a Microsoft által a mögöttes Azure platformon végzett időszakos frissítések, amelyek a virtuális gépek által futtatott platform-infrastruktúra általános megbízhatóságának, teljesítményének és biztonságának javításához szükségesek. A frissítések többsége a Virtual Machinesra vagy Cloud Services gyakorolt hatás nélkül történik (lásd: [karbantartás, amely nem igényel újraindítást](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)). Bár az Azure platform minden esetben VM-megőrző karbantartást igyekszik végrehajtani, egyes ritka esetekben a frissítések megkövetelik a virtuális gép újraindítását, hogy a szükséges frissítéseket telepíteni lehessen a mögöttes infrastruktúrára. Ilyen esetekben karbantartással és ismételt üzembe helyezéssel kiegészített Azure tervezett karbantartási eljárást hajthat végre, ha a megfelelő időtartományban kezdeményezi a virtuális gépek karbantartását. További információkért lásd: [Virtuális gépek tervezett karbantartása](maintenance-and-updates.md).


Az ilyen események okozta állásidő hatásainak csökkentése érdekében javasoljuk, hogy kövesse az alábbi bevált gyakorlatokat a virtuális gépek magas rendelkezésre állásának biztosításához:

* Availabiilty zónák használata az adatközpont hibái elleni védelemhez
* Több virtuális gép rendelkezésre állási csoportba konfigurálása a redundancia biztosítása érdekében
* Felügyelt lemezek használata rendelkezésre állási csoporthoz tartozó virtuális gépekkel
* Ütemezett események használatával proaktív módon válaszolhat a virtuális gépeket érintő eseményekre
* Az egyes alkalmazásrétegek külön rendelkezésre állási csoportokba konfigurálása
* Terheléselosztó kombinálása rendelkezésre állási zónákkal vagy készletekkel
* A rendelkezésre állási zónák használata az adatközponti szintű hibák elleni védelemhez

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>A rendelkezésre állási zónák használata az adatközponti szintű hibák elleni védelemhez

A [rendelkezésre állási zónák](../availability-zones/az-overview.md) kibővítik a vezérlés szintjét, hogy a virtuális gépeken elérhető alkalmazások és adatmennyiségek rendelkezésre álljanak. A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében az összes engedélyezett régióban legalább három különálló zónának kell lennie. Egy régión belüli Availability Zones fizikai elkülönítése megvédi az alkalmazásokat és az adatközpontok meghibásodását. Zóna – a redundáns szolgáltatások az alkalmazások és az adatok replikálását Availability Zones az egypontos meghibásodások elleni védelem érdekében.

Az Azure-régiók rendelkezésre állási zónái egy tartalék **tartomány** és egy **frissítési tartomány**kombinációja. Ha például három vagy több virtuális gépet hoz létre három zónában egy Azure-régióban, a virtuális gépeket a rendszer gyakorlatilag három tartalék tartományba és három frissítési tartományba terjeszti. Az Azure platform felismeri ezt az eloszlást a frissítési tartományok között, hogy megbizonyosodjon róla, hogy a különböző zónákban lévő virtuális gépek nem frissülnek egyidőben.

Az Azure Availability Zones az iparág legjobb 99,99%-os rendelkezésre állását kínálja a virtuális gép számára. Ha a megoldásait a zónákban lévő replikált virtuális gépek használatára kívánja használni, az alkalmazások és az adatok az adatközpont elvesztése miatt is védhetők. Ha egy zóna biztonsága sérül, a replikált alkalmazások és az adatszolgáltatások azonnal elérhetők lesznek egy másik zónában.

![Rendelkezésre állási zónák](./media/virtual-machines-common-manage-availability/three-zones-per-region.png)

További információ a Windows vagy [Linux](./linux/create-cli-availability-zone.md) [rendszerű](./windows/create-powershell-availability-zone.md) virtuális gépek rendelkezésre állási zónában való üzembe helyezéséről.

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Több virtuális gép rendelkezésre állási csoportba konfigurálása a redundancia biztosítása érdekében
A rendelkezésre állási csoportok egy másik adatközpont-konfiguráció, amely biztosítja a virtuális gépek redundanciát és rendelkezésre állását. Ez az adatközponton belüli konfiguráció biztosítja, hogy a tervezett vagy nem tervezett karbantartási események során legalább egy virtuális gép elérhető legyen, és megfelel a 99,95%-os Azure SLA-nak. További információkért lásd [a virtuális gépek esetében érvényes SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) részleteit.

> [!IMPORTANT]
> Egy, a rendelkezésre állási csoportba tartozó egypéldányos virtuális gépnek prémium SSD vagy ultravékony lemezt kell használnia az összes operációsrendszer-lemez és adatlemez számára, hogy az SLA-t legalább 99,9%-os virtuális géphez lehessen csatlakoztatni. 
> 
> Egy standard SSD virtuális gép legalább 99,5%-os SLA-val rendelkezik, míg a standard HDD egy példányos virtuális gép legalább 95%-os SLA-val fog rendelkezni.  Lásd: [SLA a Virtual Machines számára](https://azure.microsoft.com/support/legal/sla/virtual-machines/)

A rendelkezésre állási csoport minden virtuális gépe egy **frissítési tartományt** és egy tartalék **tartományt** rendel hozzá a mögöttes Azure platformhoz. Minden eges rendelkezésre állási csoportnak öt, felhasználó által nem konfigurálható frissítési tartományt oszt ki a rendszer alapértelmezés szerint (a Resource Manager-környezetek ezután akár 20 frissítési tartományig növelhetőek), és így kijelöli azokat a virtuális gépeket és mögöttes fizikai hardvereszközöket magukba foglaló csoportokat, amelyek egy időben újraindíthatóak. Ha egy rendelkezésre állási csoportba ötnél több virtuális gép van konfigurálva, a hatodik virtuális gép az elsővel azonos frissítési tartományba kerül, a hetedik a másodikkal és így tovább. A frissítési tartományok újraindítása nem haladhat szekvenciálisan a tervezett karbantartás során, hanem csak egyetlen frissítési tartományt lehet újraindítani egyszerre. Az újraindított frissítési tartománynak 30 perce van, hogy helyreálljon, mielőtt a karbantartás elkezdődik egy másik frissítési tartományon.

A tartalék tartományok azonos tápforrással és hálózati kapcsolóval rendelkező virtuális gépek csoportjai. Alapértelmezés szerint a rendelkezésre állási csoportba konfigurált virtuális gépek legfeljebb három tartalék tartományba vannak elkülönítve a Resource Manager-környezetben (két tartalék tartományba a klasszikus környezetben). Bár a virtuális gépek rendelkezésre állási csoportokba rendezése nem védi az alkalmazást az operációs rendszerre vezethető vagy az alkalmazásspecifikus hibáktól, korlátozza a potenciális hardvermeghibásodások, hálózatkimaradások vagy tápellátás-megszakadások hatását.

<!--Image reference-->
   ![A frissítési és tartalék tartományokat tartalmazó konfiguráció elméleti rajza](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Felügyelt lemezek használata rendelkezésre állási csoporthoz tartozó virtuális gépekkel
Ha jelenleg nem felügyelt lemezekkel rendelkező virtuális gépeket használ, javasoljuk, hogy a [Linux](./linux/convert-unmanaged-to-managed-disks.md) és a [Windows rendszerhez](./windows/convert-unmanaged-to-managed-disks.md)készült felügyelt lemezeket ne felügyelje.

A [felügyelt lemezek](./managed-disks-overview.md) jobb rendelkezésre állást nyújtanak a rendelkezésre állási csoportok számára, mivel biztosítják a rendelkezésre állási csoportban lévő virtuális gépek lemezeinek megfelelő elszigetelését a kritikus hibapontok elkerülése érdekében. Ezt úgy teszi meg, hogy a lemezeket a különböző tárolási tartalék tartományokban (Storage-fürtökben) helyezi el, és a virtuálisgép-tartalék tartományhoz igazítja őket. Ha a tárolási tartalék tartomány hardveres vagy szoftveres hiba miatt meghiúsul, akkor csak a tárolási tartalék tartomány lemezeit tartalmazó virtuálisgép-példány meghiúsul.
![Felügyelt lemezek tartalék](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> A felügyelt rendelkezésre állási csoportok tartalék tartományainak száma régiónként eltérő – régiónként kettő vagy három lehet. Az egyes régiók tartalék tartományát a következő parancsfájlok futtatásával tekintheti meg.

```azurepowershell-interactive
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli-interactive 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> [!NOTE]
> Bizonyos körülmények között az azonos rendelkezésre állási csoportba tartozó két virtuális gép megoszthatja a tartalék tartományt. A megosztott tartalék tartomány megerősítéséhez nyissa meg a rendelkezésre állási csoportját, és ellenőrizze a tartalék **tartomány** oszlopot. A virtuális gépek üzembe helyezésekor a következő szakasz befejezése okozhatja a megosztott tartalék tartományt:
> 1. Az első virtuális gép üzembe helyezése.
> 1. Az első virtuális gép leállítása/felszabadítása.
> 1. A második virtuális gép üzembe helyezése.
>
> Ilyen körülmények között előfordulhat, hogy a második virtuális gép operációsrendszer-lemeze ugyanazon a tartalék tartományon jön létre, mint az első virtuális gép, így a két virtuális gép ugyanazon a tartalék tartományban lesz. A probléma elkerülése érdekében javasoljuk, hogy ne állítson le/szabadítson fel virtuális gépeket az üzemelő példányok között.

Ha nem felügyelt lemezekkel rendelkező virtuális gépeket szeretne használni, kövesse az alábbi ajánlott eljárásokat azokhoz a tárolási fiókokhoz, amelyekben a virtuális merevlemezek (VHD-k) [blobként](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs)vannak tárolva.

1. **Tárolja az egyazon virtuális géppel társított összes lemezt (operációsrendszer- és adatlemezt) ugyanabban a tárfiókban.**
2. **Tekintse át az Azure Storage-fiókban nem felügyelt lemezek számának [korlátozásait](../storage/blobs/scalability-targets-premium-page-blobs.md) ** , mielőtt további virtuális merevlemezeket adna hozzá egy Storage-fiókhoz
3. **Használjon külön Storage-fiókot a rendelkezésre állási csoportba tartozó egyes virtuális gépekhez.** Ne tárolja az egyazon rendelkezésre állási csoportban lévő virtuális gépeket ugyanabban a Storage-fiókban. A különböző rendelkezésre állási csoportokban lévő virtuális gépek számára elfogadható a tárolási fiókok megosztása, ha a fenti ajánlott eljárások követik a nem ![ felügyelt lemezek tartalék](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Ütemezett események használatával proaktív módon válaszolhat a virtuális gépeket érintő eseményekre

Az [ütemezett eseményekre](./linux/scheduled-events.md)való előfizetéskor a virtuális gép értesítést kap arról, hogy a közelgő karbantartási események milyen hatással lehetnek a virtuális gépre. Ha az ütemezett események engedélyezve vannak, a virtuális gép a karbantartási tevékenység végrehajtása előtt legalább annyi időt kap. Például a gazdagép operációs rendszerének azon frissítései, amelyek hatással lehetnek a virtuális gépre, olyan események, amelyek meghatározzák a hatást, valamint azt, hogy a karbantartás mikor történjen, ha nincs művelet. Az ütemezett események akkor is várólistára kerülnek, amikor az Azure olyan közvetlen hardverhiba-meghibásodást észlel, amely hatással lehet a virtuális gépre, ami lehetővé teszi, hogy eldöntse, mikor kell elvégezni a gyógyulást. A-ügyfelek az eseményt a karbantartás előtt is elvégezhetik, például az állapot mentése, a másodlagos feladatátvétel és így tovább. Miután elvégezte a logikát a karbantartási esemény zökkenőmentes kezeléséhez, jóváhagyhatja a függőben lévő ütemezett eseményt, hogy a platform folytassa a karbantartással.


## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>Terheléselosztó kombinálása rendelkezésre állási zónákkal vagy készletekkel
Egyesítse a [Azure Load Balancer](../load-balancer/load-balancer-overview.md) egy rendelkezésre állási zónával, vagy állítsa be a legtöbb alkalmazás rugalmasságát. Az Azure Load Balancer több virtuális gép között osztja el a forgalmat. A Standard csomagban elérhető virtuális gépek esetében az Azure Load Balancer a csomag részét képezi. Nem mindegyik virtuális gép csomagja tartalmazza az Azure Load Balancert. További információ a virtuális gépek terheléselosztásáról: **virtuális gépek** terheléselosztása [Linux](linux/tutorial-load-balancer.md) vagy [Windows rendszerre](windows/tutorial-load-balancer.md).

Ha a terheléselosztó nem úgy van konfigurálva, hogy a terhelést elossza több virtuális gép között, a tervezett karbantartás hatással lesz az egyetlen forgalomkiszolgáló virtuális gépre, ami kimaradást okoz az alkalmazásrétegben. Ha több egy rétegbe tartozó virtuális gépet helyez egyazon terheléselosztó és rendelkezésre állási csoport alá, a forgalmat mindig legalább egy példány képes lesz kiszolgálni.

A rendelkezésre állási zónák közötti terheléselosztással kapcsolatos oktatóanyagért lásd [: oktatóanyag: virtuális gépek terheléselosztása rendelkezésre állási zónák között standard Load Balancer a Azure Portal használatával](../load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal.md).


## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a virtuális gépek terheléselosztásáról, tekintse meg a [virtuális gépek](../load-balancer/load-balancer-overview.md)terheléselosztását ismertető témakört.
