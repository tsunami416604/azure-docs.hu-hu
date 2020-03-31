---
title: A Service Fabric fürtkapacitásának megtervezése
description: Service Fabric fürt kapacitástervezési szempontok. Nodetypes, Operations, Tartósság és Megbízhatóság szintek
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: pepogors
ms.openlocfilehash: 6e60fc10dd7e0eec24de4a089d09d914624dcfbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258914"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>A Service Fabric fürtkapacitás-tervezési szempontjai
A kapacitástervezés az éles rendszerek üzembe helyezésének lényeges lépése. Íme néhány elem, amelyet figyelembe kell vennie a folyamat részeként.

* Azoknak a csomóponttípusoknak a száma, amelyekkel a fürtnek el kell indulnia
* Az egyes csomóponttípusok tulajdonságai (méret, elsődleges, internetezés, virtuális gépek száma stb.)
* A fürt megbízhatóságra és tartósságra vonatkozó jellemzői.

> [!NOTE]
> A tervezés során minimálisan tekintse át az összes **nem engedélyezett** frissítési házirend-értéket. Ez biztosítja, hogy megfelelően állítsa be az értékeket, és csökkentse a fürt későbbi leégetését a változatlan rendszerkonfigurációs beállítások miatt. 
> 

Nézzük röviden át tekintsük ezeket a tételeket.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Azoknak a csomóponttípusoknak a száma, amelyekkel a fürtnek el kell indulnia
Először ki kell találnia, hogy a létrehozott fürt mire lesz használva.  Milyen típusú alkalmazásokat tervez telepíteni a fürtbe? Ha nem egyértelmű a fürt célja, akkor valószínűleg még nem áll készen a kapacitástervezési folyamatba való belépésre.

Adja meg, hogy a fürtnek hány csomóponttípussal kell elindulnia.  Minden csomóponttípus egy virtuálisgép-méretezési csoporthoz van rendelve. Ezután mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy fel, eltérő nyitott portokkal rendelkezhet, és eltérő kapacitásmetrikái lehetnek. Tehát a csomóponttípusok számának döntése lényegében a következő megfontolásokon múlik:

* Az alkalmazás több szolgáltatással rendelkezik, és bármelyiknek nyilvánosnak vagy internettel szemben kell lennie? A tipikus alkalmazások egy előtér-átjárószolgáltatást tartalmaznak, amely bemenetet fogad egy ügyféltől és egy vagy több háttérszolgáltatástól, amelyek az előtér-szolgáltatásokkal kommunikálnak. Tehát ebben az esetben, ha a végén, hogy legalább két csomópont típusok.
* A szolgáltatások (amelyek az alkalmazást alkotják) különböző infrastruktúra-igényeket, például nagyobb RAM-mal vagy nagyobb CPU-ciklusokkal rendelkeznek? Tegyük fel például, hogy a telepíteni kívánt alkalmazás egy előtér-szolgáltatást és egy háttérszolgáltatást tartalmaz. Az előtér-szolgáltatás kisebb virtuális gépeken (például D2-hez hasonló virtuális gépeken) futtatható, amelyek portjai nyitva állnak az interneten.  A háttérszolgáltatás azonban számításigényes, és nagyobb virtuális gépeken kell futnia (olyan virtuális gépméretekkel, mint a D4, D6, D15), amelyek nem az internetfelé néznek.
  
  Ebben a példában, bár dönthet úgy, hogy az összes szolgáltatást egy csomóponttípusra helyezi, azt javasoljuk, hogy helyezze el őket egy fürtkét csomóponttípussal.  Ez lehetővé teszi, hogy minden csomóponttípus különböző tulajdonságokkal rendelkezik, például az internetkapcsolattal vagy a virtuális gép méretével. A virtuális gépek száma egymástól függetlenül is méretezhető.  
* Mivel nem tudja megjósolni a jövőt, menjen a tényeket, tudod, és válassza ki, hogy hány csomóponttípusok, hogy az alkalmazások kell kezdeni. Később bármikor hozzáadhat vagy eltávolíthat csomóponttípusokat. A Service Fabric-fürtnek legalább egy csomóponttípussal kell rendelkeznie.

## <a name="the-properties-of-each-node-type"></a>Az egyes csomóponttípusok tulajdonságai
A **csomóponttípus** a felhőszolgáltatások szerepköreivel egyenértékűnek tekinthető. A csomóponttípusok határozzák meg a virtuális gép méretét, a virtuális gépek számát és azok tulajdonságait. A Service Fabric-fürtben definiált minden csomóponttípus [egy virtuálisgép-méretezési csoportra van leképezve.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)  
Minden csomóponttípus egy különálló méretezési csoport, és egymástól függetlenül skálázható fel- vagy leskálázható, különböző portkészleteket nyithat meg, és különböző kapacitási metrikákkal rendelkezhet. A csomóponttípusok és a virtuálisgép-méretezési csoportok közötti kapcsolatokról, az RDP egyik példányba való bekapcsolásáról, az új portok megnyitásáról [stb.](service-fabric-cluster-nodetypes.md)

A Service Fabric-fürt egynél több csomóponttípusból állhat. Ebben az esetben a fürt egy elsődleges csomóponttípusból és egy vagy több nem elsődleges csomóponttípusból áll.

Egyetlen csomóponttípus nem tudja megbízhatóan átméretezni az SF-alkalmazások virtuálisgép-méretezési csoportjánként 100 csomópontot meghaladó méretet; a 100-nál megbízhatóan elérhető, további virtuálisgép-méretezési csoportok hozzáadásához szükséges.

### <a name="primary-node-type"></a>Elsődleges csomópont típusa

A Service Fabric rendszerszolgáltatások (például a Cluster Manager szolgáltatás vagy image store szolgáltatás) kerülnek az elsődleges csomópont típusa. 

![Képernyőkép egy két csomóponttípussal rendelkező fürtről][SystemServices]

* Az elsődleges csomóponttípus **virtuális gépeinek minimális méretét** a választott **tartóssági szint** határozza meg. Az alapértelmezett tartóssági szint a Bronz. További részletekért [lásd a fürt tartóssági jellemzőit.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)  
* Az elsődleges csomóponttípus **virtuális gépeinek minimális számát** a kiválasztott **megbízhatósági szint** határozza meg. Az alapértelmezett megbízhatósági szint a Silver. További részletek [a fürt megbízhatósági jellemzői.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster)  

Az Azure Resource Manager sablonból az elsődleges csomóponttípusa a `isPrimary` [csomóponttípus-definíció](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object)alatt található attribútummal van konfigurálva.

### <a name="non-primary-node-type"></a>Nem elsődleges csomóponttípus

A több csomóponttípussal rendelkező fürtben egy elsődleges csomóponttípus van, a többi pedig nem elsődleges.

* A virtuális **gépek minimális méretét** a nem elsődleges csomóponttípusok hoz a választott **tartóssági szint** határozza meg. Az alapértelmezett tartóssági szint a Bronz. További információ: [A fürt tartóssági jellemzői.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)  
* A nem elsődleges csomóponttípusok **virtuális gépeinek minimális száma** egy. Ezt a számot azonban az ebben a csomóponttípusban futtatni kívánt alkalmazás/szolgáltatások replikáinak száma alapján kell kiválasztania. A csomóponttípusban lévő virtuális gépek száma növelhető a fürt üzembe helyezése után.

## <a name="the-durability-characteristics-of-the-cluster"></a>A fürt tartósságára vonatkozó jellemzők
A tartóssági szint jelzi a rendszer számára a virtuális gépek az alapul szolgáló Azure-infrastruktúrával rendelkező jogosultságokat. Az elsődleges csomópont típusában ez a jogosultság lehetővé teszi, hogy a Service Fabric szüneteltesse a virtuális gép szintű infrastruktúra-kérelmek (például a virtuális gép újraindítása, virtuális gép újraképzése vagy virtuális gép áttelepítése), amelyek hatással vannak a rendszerszolgáltatások és az állapotalapú szolgáltatások kvórumkövetelményeinek. A nem elsődleges csomóponttípusok, ez a jogosultság lehetővé teszi, hogy a Service Fabric szüneteltesse a virtuálisgép-szintű infrastruktúra-kérelmek (például a virtuális gép újraindítása, virtuális gép újraképe és a virtuális gép áttelepítése), amelyek hatással vannak az állapotalapú szolgáltatások kvórumkövetelményeinek.

| Tartóssági szint  | A virtuális gépek minimális minimális száma | Támogatott VM SKUs                                                                  | A virtuálisgép-méretezési csoport frissítései                               | Az Azure által kezdeményezett frissítések és karbantartások                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Arany             | 5                              | Egyetlen ügyfélnek szentelt teljes csomópontos suk-ok (például L32s, GS5, G5, DS15_v2, D15_v2) | A Service Fabric-fürt által idáig elhalasztható | Ud-onként 2 órán keresztül szüneteltethető, így a kópiák nak további időt kell hagynia a korábbi hibák helyreállítására |
| Ezüst           | 5                              | Egymagos vagy annál magasabb magú virtuális gépek legalább 50 GB helyi SSD-vel                      | A Service Fabric-fürt által idáig elhalasztható | Nem késleltethető jelentős ideig                                                    |
| Bronz           | 1                              | Legalább 50 GB helyi SSD-vel rendelkező virtuális gépek                                              | A Service Fabric-fürt nem késlelteti           | Nem késleltethető jelentős ideig                                                    |

> [!WARNING]
> A Bronz tartóssággal futó csomóponttípusok _nem kapnak jogosultságokat._ Ez azt jelenti, hogy az állapotalapú számítási feladatokat befolyásoló infrastruktúra-feladatok nem lesznek leállítva vagy késve, ami hatással lehet a számítási feladatokra. Csak bronz ot használjon olyan csomóponttípusokhoz, amelyek csak állapot nélküli számítási feladatokat futtatnak. Éles számítási feladatok hoz, Silver vagy annál magasabb futtatása ajánlott. 
> 
> A tartóssági szinttől függetlenül a virtuálisgép-méretezési csoporton a [kiosztási](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) művelet megsemmisíti a fürtöt

**Az ezüst vagy arany tartóssági szintek használatának előnyei**
 
- Csökkenti a szükséges lépések számát egy horizontális felskálázási műveletben (azaz a csomópont inaktiválása és az Eltávolítás-ServiceFabricNodeState automatikusan van meghívva).
- Csökkenti az adatvesztés kockázatát az ügyfél által kezdeményezett virtuális gép termékváltozatának módosítása vagy az Azure-infrastruktúra-műveletek miatt.

**Az ezüst vagy arany tartóssági szintek használatának hátrányai**
 
- A virtuális gép méretezési csoportjába és más kapcsolódó Azure-erőforrásokra történő központi telepítések késleltethetők, időtúlodhatnak, vagy teljes mértékben blokkolhatók a fürtben vagy az infrastruktúra szintjén felmerülő problémák. 
- Növeli a [replika életciklus-események](service-fabric-reliable-services-lifecycle.md) (például elsődleges swap) az Azure-infrastruktúra-műveletek során az automatikus csomópont-inaktiválások miatt.
- A csomópontokat egy ideig üzemen kívüli reked, amíg az Azure platform szoftverfrissítései vagy hardverkarbantartási tevékenységei megtörténnek. Ezeka tevékenységek során előfordulhat, hogy a letiltó/letiltott állapotú csomópontok jelennek meg. Ez ideiglenesen csökkenti a fürt kapacitását, de nem befolyásolja a fürt vagy az alkalmazások rendelkezésre állását.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Ajánlások az ezüst vagy arany tartóssági szintek használatára vonatkozóan

Használja ezüst vagy arany tartósság minden csomópont-típusok, amelyek a fogadó állapotalapú szolgáltatások várhatóan a méretezési (csökkenti a virtuális gép példányok száma) gyakran, és szeretné, hogy a telepítési műveletek késik, és a kapacitás csökkenteni kell az egyszerűsítése érdekében ezek a méretezési Műveletek. A horizontális felskálázási forgatókönyvek (virtuális géppéldányok hozzáadása) nem játszanak a választott tartóssági réteg, csak a horizontális felskálázás nem.

### <a name="changing-durability-levels"></a>A tartóssági szintek változása
- Az ezüst vagy arany tartóssági szinttel rendelkező csomóponttípusok nem minősíthetők bronzra.
- A bronzról ezüstre vagy aranyra való frissítés néhány órát is igénybe vehet.
- Tartóssági szint módosításakor győződjön meg róla, hogy frissítse azt mind a Service Fabric-bővítmény konfigurációját a virtuális gép méretezési készlet erőforrásában, és a csomópont típusdefiníciójában a Service Fabric fürterőforrásban. Ezeknek az értékeknek egyezniük kell.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Az ezüst vagy arany tartóssági szintre beállított csomóponttípus működési javaslatai.

- A fürt és az alkalmazások mindig kifogástalan állapotban, és győződjön meg arról, hogy az alkalmazások reagálnak az összes [szolgáltatás replika életciklus-események](service-fabric-reliable-services-lifecycle.md) (például a replika buildbe van ragasztva) időben.
- A virtuális gép termékváltozatának módosításával biztonságosabb módszereket alkalmazhat (Fel-fel/le skálázás): A virtuálisgép-méretezési csoport virtuálisgép-méretezési készlet virtuálisgép-termékváltozatának módosítása számos lépést és szempontot igényel. Itt van a folyamat, amit követhet, hogy elkerülje a gyakori problémákat.
    - **Nem elsődleges csomóponttípusok esetén:** Javasoljuk, hogy hozzon létre új virtuálisgép-méretezési készletet, módosítsa a szolgáltatáselhelyezési megkötést, hogy tartalmazza az új virtuálisgép-méretezési készlet/csomópont típust, majd csökkentse a régi virtuálisgép-méretezési példány számát nullára, egyszerre egy csomópontra (ez annak biztosítása, hogy a csomópontok eltávolítása ne befolyásolja a fürt megbízhatóságát).
    - **Az elsődleges csomóponttípushoz:** Ha a kiválasztott virtuális gép termékváltozat kapacitása van, és nagyobb virtuálisgép-termékváltozatra szeretne váltani, kövesse az [elsődleges csomóponttípus vertikális skálázásra](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-node-type)vonatkozó útmutatásunkat. 

- Legalább öt csomópontot tart fenn minden olyan virtuálisgép-méretezési készlethez, amelynek tartóssági szintje engedélyezve van az Arany vagy ezüst értékhez.
- Minden virtuális gép méretezési szint beállítása tartóssági szint ezüst vagy arany kell leképezni a saját csomópont típusát a Service Fabric-fürtben. Több virtuálisgép-méretezési csoport egyetlen csomóponttípusra történő leképezése megakadályozza a Service Fabric-fürt és az Azure-infrastruktúra közötti koordináció megfelelő működését.
- Ne törölje a véletlenszerű virtuálisgép-példányokat, mindig használja a virtuális gép méretezési készletének leskálázási szolgáltatását. A véletlenszerű virtuálisgép-példányok törlése rendelkezik azzal a potenciállal, hogy a virtuálisgép-példányban az UD és az FD között elosztva egyensúlyhiányok létrehozása. Ez a kiegyensúlyozatlanság hátrányosan befolyásolhatja a rendszer azon képességét, hogy megfelelően terheléselosztás között a szolgáltatás példányok/szolgáltatás replikák.
- Automatikus skálázás használata esetén állítsa be a szabályokat úgy, hogy a skálázás (virtuálisgép-példányok eltávolítása) egyszerre csak egy csomóponttörténik. Egyszerre több példány leskálázása nem biztonságos.
- Ha az elsődleges csomóponttípuson lévő virtuális gépek törlése vagy felosztása, soha ne csökkentse a lefoglalt virtuális gépek számát a megbízhatósági szint által igényelt szint alá. Ezek a műveletek határozatlan ideig blokkolva lesznek egy ezüst vagy arany tartóssági szinttel rendelkező méretezési készletben.

## <a name="the-reliability-characteristics-of-the-cluster"></a>A fürt megbízhatósági jellemzői
A megbízhatósági szint a fürtben az elsődleges csomóponttípuson futtatni kívánt rendszerszolgáltatások replikáinak számát állítja be. Minél több a replikák száma, annál megbízhatóbbak a rendszerszolgáltatások a fürtben.  

A megbízhatósági szint a következő értékeket veheti fel:

* Platina – A rendszerszolgáltatások futtatása kilenc célreplikakészletszámmal
* Arany – A rendszerszolgáltatások futtatása hét célreplikakészletszámmal
* Ezüst – A rendszerszolgáltatások futtatása öt célreplikakészletszámmal 
* Bronz – A rendszerszolgáltatások futtatása három célreplika-készletszámmal

> [!NOTE]
> A kiválasztott megbízhatósági szint határozza meg az elsődleges csomóponttípusáltal rendelkeznie kívánt csomópontok minimális számát. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Ajánlások a megbízhatósági szintre vonatkozóan

Ha növeli vagy csökkenti a fürt méretét (a virtuálisgép-példányok összegét az összes csomóponttípusban), frissítenie kell a fürt megbízhatóságát az egyik rétegről a másikra. Ezzel elindítja a rendszerszolgáltatások replikakészletszámának módosításához szükséges fürtfrissítéseket. Várja meg, amíg a folyamatban lévő frissítés befejeződik, mielőtt bármilyen más módosítást végezne a fürtön, például csomópontok hozzáadása.  A frissítés előrehaladását a Service Fabric Explorer böngészőben vagy a [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) futtatásával figyelheti

Itt van a megbízhatósági szint kiválasztására vonatkozó javaslat.  A magcsomópontok száma is a megbízhatósági szint minimális csomópontjainak száma.  Például egy fürt Gold megbízhatósággal vannak 7 mag csomópontok.

| **Fürtcsomópontok száma** | **Megbízhatósági szint** |
| --- | --- |
| 1 |Ne adja meg a Megbízhatósági szint paramétert, a rendszer kiszámítja azt |
| 3 |Bronz |
| 5 vagy 6|Ezüst |
| 7 vagy 8 |Arany |
| 9 és fel |Platina |

## <a name="primary-node-type---capacity-guidance"></a>Elsődleges csomópont típusa - kapacitásútmutató

Az elsődleges csomóponttípus-kapacitás tervezéséhez az alábbiakat talál:

- **Az azure-beli éles számítási feladatok futtatásához a virtuálisgép-példányok száma:** Meg kell adnia egy minimális elsődleges csomóponttípus-méretet 5 és egy silver megbízhatósági szintet.  
- **A tesztszámítási feladatok futtatásához az Azure-ban futtatandó virtuálisgép-példányok száma** Megadhat egy minimális elsődleges csomóponttípus-méretet 1 vagy 3. Az egy csomópont-fürt, fut egy speciális konfigurációt, és így, horizontális felskálázása, hogy a fürt nem támogatott. Az egy csomópontos fürt, nincs megbízhatósága, és így az Erőforrás-kezelő sablonban el kell távolítania/nem kell megadnia, hogy a konfiguráció (nem a konfigurációs érték beállítása nem elegendő). Ha a portálon keresztül beállított egycsomópontos fürtöt állítja be, akkor a konfiguráció automatikusan gondoskodik. Egy és három csomópontfürtök nem támogatottak éles számítási feladatok futtatásához. 
- **VM Termékváltozat:** Elsődleges csomópont típusa, ahol a rendszerszolgáltatások futnak, így a virtuális gép Termékváltozat választja ki, figyelembe kell vennie a teljes csúcsterhelést, amelyet a fürtbe kíván helyezni. Itt van egy analógia, hogy bemutassa, mire gondolok itt - Gondoljon az elsődleges csomópont típus, mint a "Tüdő", ez az, ami oxigént biztosít az agyad, és így ha az agy nem kap elég oxigént, a szervezet szenved. 

Mivel a fürt kapacitásigényét a fürtben futtatni kívánt munkaterhelés határozza meg, nem tudunk minőségi útmutatást nyújtani az adott számítási feladathoz, azonban itt van egy átfogó útmutató az induláshoz

Éles számítási feladatok esetén: 

- Javasoljuk, hogy a fürtök elsődleges NodeType-ját a rendszerszolgáltatásoknak szentelje, és elhelyezési korlátozásokhasználatával telepítse az alkalmazást másodlagos nodetypes-re.
- Az ajánlott virtuális gép termékváltozat standard D2_V2 vagy egyenértékű, legalább 50 GB helyi SSD.The recommended VM SKU is Standard D2_V2 or equivalent with a minimum 50 GB local SSD.
- A minimálisan támogatott virtuálisgép-termékváltozat Standard_D2_V3 vagy standard D1_V2 vagy azzal egyenértékű, legalább 50 GB helyi SSD-vel. 
- Javaslatunk legalább 50 GB. A számítási feladatokhoz, különösen a Windows-tárolók futtatásakor, nagyobb lemezek szükségesek. 
- Részleges magvú skus, mint a standard A0 nem támogatott éles számítási feladatok.
- A sorozatos virtuálisgép-sorozat megfelelőségi készletei teljesítménybeli okokból nem támogatottak éles számítási feladatokhoz.
- Alacsony prioritású virtuális gépek nem támogatottak.

> [!WARNING]
> Az elsődleges csomópont virtuálisgép-termékváltozat méretének módosítása egy futó fürtön, egy méretezési művelet, és a [virtuálisgép-méretezési csoport horizontális felskálázási](virtual-machine-scale-set-scale-node-type-scale-out.md) dokumentációjában dokumentált.

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Nem elsődleges csomóponttípus – kapacitásútmutató az állapotalapú munkaterhelésekhez

Ez az útmutató a service fabric [megbízható gyűjtemények vagy megbízható szereplők,](service-fabric-choose-framework.md) amelyek nem elsődleges csomóponttípushasználatával állapotalapú számítási feladatok.

**Virtuálisgép-példányok száma:** Az állapotalapú éles számítási feladatok esetén ajánlott, hogy futtassa őket egy minimális és cél replika száma 5. Ez azt jelenti, hogy állandósult állapotban minden tartalék tartományban és frissítési tartományban egy replika (egy replikakészletből származó) replikát kap. Az elsődleges csomóponttípus teljes megbízhatósági szint-koncepciója a rendszerszolgáltatások ezen beállításának megadására szolgáló módszer. Tehát ugyanez a megfontolás vonatkozik az ön állami szolgáltatások is.

Így az éles számítási feladatok esetében a minimálisan ajánlott nem elsődleges csomóponttípus-méret 5, ha állapotalapú számítási feladatokat futtat benne.

**VM Termékváltozat:** Ez az a csomóponttípus, ahol az alkalmazásszolgáltatások futnak, így a virtuális gép termékváltozatának, amelyet kiválaszt, figyelembe kell vennie az egyes csomópontokba kívánt csúcsterhelést. A csomóponttípus kapacitásigényét a fürtben futtatni kívánt munkaterhelés határozza meg, így nem tudunk minőségi útmutatást nyújtani az adott munkaterheléshez, de itt van egy átfogó útmutató a kezdéshez

Éles számítási feladatokhoz 

- Az ajánlott virtuális gép termékváltozat standard D2_V2 vagy egyenértékű, legalább 50 GB helyi SSD.The recommended VM SKU is Standard D2_V2 or equivalent with a minimum 50 GB local SSD.
- A minimálisan támogatott virtuálisgép-termékváltozat Standard_D2_V3 vagy standard D1_V2 vagy azzal egyenértékű, legalább 50 GB helyi SSD-vel. 
- Részleges magvú skus, mint a standard A0 nem támogatott éles számítási feladatok.
- A sorozatos virtuálisgép-sorozat megfelelőségi készletei teljesítménybeli okokból nem támogatottak éles számítási feladatokhoz.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Nem elsődleges csomóponttípus – kapacitásútmutató állapot nélküli számítási feladatokhoz

Ez az útmutató a nem elsődleges csomóponttípuson futó állapot nélküli számítási feladatokról.

**Virtuálisgép-példányok száma:** Az állapot nélküli éles számítási feladatok esetében a minimálisan támogatott nem elsődleges csomóponttípus-méret 2. Ez lehetővé teszi, hogy futtassa az alkalmazás két állapotnélküli példányát, és lehetővé teszi a szolgáltatás számára, hogy túlélje a virtuálisgép-példány elvesztését. 

**VM Termékváltozat:** Ez az a csomóponttípus, ahol az alkalmazásszolgáltatások futnak, így a virtuális gép termékváltozatának, amelyet kiválaszt, figyelembe kell vennie az egyes csomópontokba kívánt csúcsterhelést. A csomóponttípus kapacitásigényét a fürtben futtatni kívánt munkaterhelés határozza meg. Nem tudunk minőségi útmutatást nyújtani az adott munkaterheléshez.  Azonban itt van a széles körű útmutatást, hogy segítsen az induláshoz.

Éles számítási feladatokhoz 

- Az ajánlott virtuális gép termékváltozat a standard D2_V2 vagy azzal egyenértékű. 
- A minimálisan támogatott virtuálisgép-termékváltozat a Standard D1 vagy standard D1_V2 vagy azzal egyenértékű. 
- Részleges magvú skus, mint a standard A0 nem támogatott éles számítási feladatok.
- A sorozatos virtuálisgép-sorozat megfelelőségi készletei teljesítménybeli okokból nem támogatottak éles számítási feladatokhoz.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>További lépések
Miután befejezte a kapacitástervezést és beállított egy fürtöt, olvassa el az alábbi szöveget:

* [A Service Fabric fürtjének biztonsága](service-fabric-cluster-security.md)
* [A Service Fabric fürtméretezése](service-fabric-cluster-scaling.md)
* [Katasztrófautáni helyreállítás tervezése](service-fabric-disaster-recovery.md)
* [Nodetypes kapcsolata a virtuálisgép-méretezési készlethez](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
