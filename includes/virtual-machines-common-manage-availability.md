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
ms.openlocfilehash: a4140ffc0d4e97afabb1c3080951eeb75c792a8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76961376"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>A virtuális gépek újraindításának ismertetése – karbantartás és állásidő
Három forgatókönyv, amely az Azure-ban a virtuális gép érintett: nem tervezett hardverkarbantartás, váratlan állásidő és a tervezett karbantartás.

* **Nem tervezett hardverkarbantartási esemény** akkor fordul elő, ha az Azure platform előre jelzi, hogy egy adott fizikai kiszolgálóhoz tartozó hardver vagy bármely platformösszetevő meg fog hibásodni. Amikor a platform előre jelez egy hibát, kiad egy nem tervezett hardverkarbantartási eseményt az adott hardveren futtatott virtuális gépekre gyakorolt hatás csökkentése érdekében. Az Azure [élő áttelepítési](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) technológiát használ a virtuális gépek áttelepítéséhez a hibás hardverről egy kifogástalan fizikai gépre. Az Élő áttelepítés a virtuális gépet megőrző művelet, amely csak minimális időre állítja le a virtuális gépet. A memória tartalmát, a megnyitott fájlokat és a hálózati kapcsolatokat mind megőrzi, azonban a teljesítmény az esemény előtt és/vagy után csökkenhet. Ha az Élő áttelepítés nem használható, a virtuális gépen váratlan állásidő következik be az alábbiak szerint.


* **A váratlan állásidő** az, amikor a virtuális gép hardvere vagy fizikai infrastruktúrája váratlanul meghibásodik. Ez magában foglalhatja a helyi hálózati hibákat, a helyi lemezhibákat vagy más rackszintű hibákat. Észlelése esetén az Azure platform automatikusan áttelepíti (gyógyítja) a virtuális gépet egy kifogástalan állapotú fizikai gép ugyanabban az adatközpontban. A javítási folyamat során a virtuális gép állásideje következik be (újraindítás), valamint bizonyos esetekben elveszhet az ideiglenes meghajtó. A csatlakoztatott rendszer- és adatmeghajtók minden esetben megmaradnak.

  A virtuális gépek leállást is tapasztalhatnak abban a valószínűtlen esetben, ha egy kimaradás vagy katasztrófa, amely hatással van egy teljes adatközpont, vagy akár egy teljes régióban. Ezeknél a forgatókönyveknél az Azure védelmi lehetőségeket biztosít, beleértve [a rendelkezésre állási zónákat](../articles/availability-zones/az-overview.md) és [a párosított régiókat.](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions)

* **A tervezett karbantartási események** a Microsoft által az alapul szolgáló Azure platformon végzett rendszeres frissítések, amelyek javítják a virtuális gépek által futtatott platforminfrastruktúra általános megbízhatóságát, teljesítményét és biztonságát. A frissítések a legtöbb esetben semmilyen hatással nincsenek a virtuális gépek vagy a felhőszolgáltatások működésére (lásd: [VM-megőrző karbantartás](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance)). Bár az Azure platform minden esetben VM-megőrző karbantartást igyekszik végrehajtani, egyes ritka esetekben a frissítések megkövetelik a virtuális gép újraindítását, hogy a szükséges frissítéseket telepíteni lehessen a mögöttes infrastruktúrára. Ilyen esetekben karbantartással és ismételt üzembe helyezéssel kiegészített Azure tervezett karbantartási eljárást hajthat végre, ha a megfelelő időtartományban kezdeményezi a virtuális gépek karbantartását. További információkért lásd: [Virtuális gépek tervezett karbantartása](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/).


Az ilyen események okozta állásidő hatásainak csökkentése érdekében javasoljuk, hogy kövesse az alábbi bevált gyakorlatokat a virtuális gépek magas rendelkezésre állásának biztosításához:

* [Több virtuális gép rendelkezésre állási csoportba konfigurálása a redundancia biztosítása érdekében]
* [Felügyelt lemezek használata rendelkezésre állási csoporthoz tartozó virtuális gépekkel]
* [Ütemezett események használata proaktív válaszként a virtuális gépre, amely hatással van az eseményekre](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Az egyes alkalmazásrétegek külön rendelkezésre állási csoportokba konfigurálása]
* [Terheléselosztó és rendelkezésre állási készletek kombinálása]
* [Az adatközpontszintű hibák elleni védelem a rendelkezésre állási zónák használatával]

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Az adatközpontszintű hibák elleni védelem a rendelkezésre állási zónák használatával

[A rendelkezésre állási zónák kibővítik](../articles/availability-zones/az-overview.md) a vezérlőszintet, amelyet a virtuális gépeken lévő alkalmazások és adatok rendelkezésre állásának fenntartásához kell megtartania. A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében minden engedélyezett régióban legalább három külön zóna van. A rendelkezésre állási zónák fizikai elkülönítése egy régión belül védi az alkalmazásokat és az adatokat az adatközpont-hibáktól. A zónaredundáns szolgáltatások replikálják az alkalmazásokat és az adatokat a rendelkezésre állási zónák között, hogy megvédjék az egypontosokat.

Az Azure-régióban rendelkezésre állási zóna egy **tartalék tartomány** és egy **frissítési tartomány**kombinációja. Ha például három vagy több virtuális gépet hoz létre egy Azure-régió három zónája között, a virtuális gépek hatékonyan oszlanak el három tartalék tartomány és három frissítési tartomány között. Az Azure platform felismeri ezt a terjesztési frissítési tartományok között, hogy győződjön meg arról, hogy a különböző zónákban lévő virtuális gépek nem frissülnek egyszerre.

A rendelkezésre állási zónákkal az Azure az iparág legjobb 99,99%-os virtuálisgép-rendelkezésre állási SLA-ját kínálja. A megoldások at a zónákban replikált virtuális gépek használatára való megoldásként megvédheti az alkalmazásokat és az adatokat az adatközpont elvesztésétől. Ha az egyik zóna sérül, majd replikált alkalmazások és adatok azonnal elérhetők egy másik zónában.

![Rendelkezésre állási zónák](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

További információ a [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) vagy [Linux virtuális](../articles/virtual-machines/linux/create-cli-availability-zone.md) gép rendelkezésre állási zónában történő központi telepítéséről.

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Több virtuális gép rendelkezésre állási csoportba konfigurálása a redundancia biztosítása érdekében
A rendelkezésre állási készletek egy másik adatközpont-konfiguráció, amely biztosítja a virtuális gép redundancia és a rendelkezésre állás. Ez a konfiguráció egy adatközponton belül biztosítja, hogy egy tervezett vagy nem tervezett karbantartási esemény során legalább egy virtuális gép elérhető, és megfelel a 99,95%-os Azure SLA. További információkért lásd [a virtuális gépek esetében érvényes SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) részleteit.

> [!IMPORTANT]
> A rendelkezésre állási készletben lévő egyetlen példányvirtuális gépnek prémium szintű SSD-t vagy Ultra Lemezt kell használnia az összes operációs rendszer lemezéhez és adatlemezéhez, hogy jogosult legyen a legalább 99,9%-os virtuálisgép-kapcsolatra vonatkozó SLA-ra.

A mögöttes Azure platform a rendelkezésre állási csoportban lévő mindegyik virtuális gépnek kioszt egy **frissítési tartományt** és egy **tartalék tartományt**. Minden eges rendelkezésre állási csoportnak öt, felhasználó által nem konfigurálható frissítési tartományt oszt ki a rendszer alapértelmezés szerint (a Resource Manager-környezetek ezután akár 20 frissítési tartományig növelhetőek), és így kijelöli azokat a virtuális gépeket és mögöttes fizikai hardvereszközöket magukba foglaló csoportokat, amelyek egy időben újraindíthatóak. Ha egy rendelkezésre állási csoportba ötnél több virtuális gép van konfigurálva, a hatodik virtuális gép az elsővel azonos frissítési tartományba kerül, a hetedik a másodikkal és így tovább. A frissítési tartományok újraindítása nem haladhat szekvenciálisan a tervezett karbantartás során, hanem csak egyetlen frissítési tartományt lehet újraindítani egyszerre. Az újraindított frissítési tartománynak 30 perce van, hogy helyreálljon, mielőtt a karbantartás elkezdődik egy másik frissítési tartományon.

A tartalék tartományok azonos tápforrással és hálózati kapcsolóval rendelkező virtuális gépek csoportjai. Alapértelmezés szerint a rendelkezésre állási csoportba konfigurált virtuális gépek legfeljebb három tartalék tartományba vannak elkülönítve a Resource Manager-környezetben (két tartalék tartományba a klasszikus környezetben). Bár a virtuális gépek rendelkezésre állási csoportokba rendezése nem védi az alkalmazást az operációs rendszerre vezethető vagy az alkalmazásspecifikus hibáktól, korlátozza a potenciális hardvermeghibásodások, hálózatkimaradások vagy tápellátás-megszakadások hatását.

<!--Image reference-->
   ![A frissítési és tartalék tartományokat tartalmazó konfiguráció elméleti rajza](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Felügyelt lemezek használata rendelkezésre állási csoporthoz tartozó virtuális gépekkel
Ha jelenleg a virtuális gépeket nem felügyelt lemezeken használja, határozottan ajánlott [a rendelkezésre állási csoportban lévő virtuális gépeket átalakítása, hogy felügyelt lemezeken fussanak](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

A [felügyelt lemezek](../articles/virtual-machines/windows/managed-disks-overview.md) jobb rendelkezésre állást nyújtanak a rendelkezésre állási csoportok számára, mivel biztosítják a rendelkezésre állási csoportban lévő virtuális gépek lemezeinek megfelelő elszigetelését a kritikus hibapontok elkerülése érdekében. Ezt úgy éri el, hogy a lemezeket automatikusan a különböző tárolótartalék tartományokba (tárolófürtökbe) helyezi, és a virtuális gép tartalék tartományához igazítja őket. Ha egy tárolótartalék-tartomány hardver- vagy szoftverhiba miatt meghibásodik, csak a tárolóhiba-tartományban lévő lemezekkel rendelkező virtuálisgép-példány sikertelen lesz.
![Felügyelt lemezek FD-k](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> A felügyelt rendelkezésre állási csoportok tartalék tartományainak száma régiónként eltérő – régiónként kettő vagy három lehet. Az egyes régiók tartalék tartományát a következő parancsfájlok futtatásával láthatja.

```azurepowershell-interactive
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli-interactive 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> [!NOTE]
> Bizonyos körülmények között az azonos AvailabilitySet-ben lévő 2 virtuális gép ugyanazt a faultdomaint oszthatja meg. Ezt a rendelkezésre állási készletbe való bevetéssel és a **Tartalék tartomány** oszlop ellenőrzésével lehet megerősíteni.
> Ennek oka a következő sorozat lehet a virtuális gépek telepítése közben:
> - Az első virtuális gép üzembe helyezése
> - Az első virtuális gép leállítása/felszabadítása
> - A 2. 
> A probléma elkerülése érdekében javasoljuk, hogy ne állítsa le/szabadítsa fel a virtuális gépeket a központi telepítések között.

Ha nem felügyelt lemezekkel rendelkező virtuális gépeket kíván használni, kövesse az alábbi gyakorlati tanácsokat a Storage-fiókokban, ahol a virtuális gépek virtuális merevlemezeit (VHD-k) [lapblobként](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs)tárolják.

1. **Tárolja az egyazon virtuális géppel társított összes lemezt (operációsrendszer- és adatlemezt) ugyanabban a tárfiókban.**
2. **Tekintse át az Azure Storage-fiók nem felügyelt lemezeinek számának [korlátait,](../articles/storage/blobs/scalability-targets-premium-page-blobs.md) ** mielőtt további virtuális merevlemezeket ad hozzá egy tárfiókhoz
3. **Egy rendelkezésre állási csoport minden egyes virtuális gép hez külön tárfiókot használjon.** Ne tárolja az egyazon rendelkezésre állási csoportban lévő virtuális gépeket ugyanabban a Storage-fiókban. A különböző rendelkezésre állási csoportokban lévő virtuális gépek számára elfogadható ![a tárfiókok megosztása, ha a fenti ajánlott eljárásokat követik a nem felügyelt lemezek FD-k](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Ütemezett események használata proaktív válaszként a virtuális gépre hatást befolyásoló eseményekre

Amikor előfizet [az ütemezett eseményekre,](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)a virtuális gép értesítést kap a közelgő karbantartási eseményekről, amelyek hatással lehetnek a virtuális gépre. Ha az ütemezett események engedélyezve vannak, a virtuális gép a karbantartási tevékenység végrehajtása előtt legalább annyi időt kap. Például a gazdaoperációs rendszer frissítései, amelyek hatással lehetnek a virtuális gép várólistára, mint események, amelyek meghatározzák a hatást, valamint egy időpontban, amikor a karbantartás lesz végrehajtva, ha nem történik művelet. Ütemezési események is várólistára kerülnek, ha az Azure észleli a közelgő hardverhiba, amely hatással lehet a virtuális gép, amely lehetővé teszi, hogy eldöntse, mikor kell elvégezni a gyógyulást. Az ügyfelek használhatják az eseményt a karbantartás előtti feladatok végrehajtására, például az állapot mentésére, a másodlagos feladatátadásra és így tovább. Miután befejezte a karbantartási esemény szabályos kezeléséhez szükséges logikát, jóváhagyhatja a függőben lévő ütemezett eseményt, hogy a platform folytathassa a karbantartást.

## <a name="configure-each-application-tier-into-separate-availability-zones-or-availability-sets"></a>Az egyes alkalmazásszintek konfigurálása külön rendelkezésre állási zónákba vagy rendelkezésre állási csoportokba
Ha a virtuális gépek mind közel azonosak, és ugyanazt a célt szolgálják az alkalmazás számára, azt javasoljuk, hogy konfigurálja a rendelkezésre állási zóna vagy rendelkezésre állási készlet az alkalmazás minden rétegéhez.  Ha két különböző réteget helyez el ugyanabban a rendelkezésre állási zónában vagy készletben, az ugyanabban az alkalmazásszinten lévő összes virtuális gép egyszerre újraindítható. Konfigurálásával legalább két virtuális gép egy rendelkezésre állási zónában vagy az egyes rétegek beállítása, garantálja, hogy legalább egy virtuális gép minden rétegben elérhető.

Például az összes virtuális gépet az IIS, Apache és Nginx alkalmazást futtató alkalmazás elő- és csoportonként egyetlen rendelkezésre állási zónába vagy készletbe helyezheti. Győződjön meg arról, hogy csak az előtér-virtuális gépek vannak ugyanabban a rendelkezésre állási zónában vagy készletben. Hasonlóképpen győződjön meg arról, hogy csak az adatszintű virtuális gépek vannak elhelyezve a saját rendelkezésre állási zónájukban vagy beállítva, például a replikált SQL Server virtuális gépek, vagy a MySQL virtuális gépek.

<!--Image reference-->
   ![Alkalmazásrétegek](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>Terheléselosztó kombinálása rendelkezésre állási zónákkal vagy készletekkel
Kombinálja az [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) egy rendelkezésre állási zóna, vagy állítsa be, hogy a legtöbb alkalmazás rugalmasságát. Az Azure Load Balancer több virtuális gép között osztja el a forgalmat. A Standard csomagban elérhető virtuális gépek esetében az Azure Load Balancer a csomag részét képezi. Nem mindegyik virtuális gép csomagja tartalmazza az Azure Load Balancert. A virtuális gépek terheléselosztásáról további információkért lásd a [virtuális gépek terheléselosztását](../articles/virtual-machines/virtual-machines-linux-load-balance.md) ismertető témakört.

Ha a terheléselosztó nem úgy van konfigurálva, hogy a terhelést elossza több virtuális gép között, a tervezett karbantartás hatással lesz az egyetlen forgalomkiszolgáló virtuális gépre, ami kimaradást okoz az alkalmazásrétegben. Ha több egy rétegbe tartozó virtuális gépet helyez egyazon terheléselosztó és rendelkezésre állási csoport alá, a forgalmat mindig legalább egy példány képes lesz kiszolgálni.

A rendelkezésre állási zónák közötti terheléselosztásról az [Azure CLI használatával az összes rendelkezésre állási zónában](../articles/load-balancer/load-balancer-standard-public-zone-redundant-cli.md)található virtuális gépek terhelése című témakörben található.


<!-- Link references -->
[Több virtuális gép rendelkezésre állási csoportba konfigurálása a redundancia biztosítása érdekében]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Az egyes alkalmazásrétegek külön rendelkezésre állási csoportokba konfigurálása]: #configure-each-application-tier-into-separate-availability-zones-or-availability-sets
[Terheléselosztó és rendelkezésre állási készletek kombinálása]: #combine-a-load-balancer-with-availability-zones-or-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Felügyelt lemezek használata rendelkezésre állási csoporthoz tartozó virtuális gépekkel]: #use-managed-disks-for-vms-in-an-availability-set
[Az adatközpontszintű hibák elleni védelem a rendelkezésre állási zónák használatával]: #use-availability-zones-to-protect-from-datacenter-level-failures
