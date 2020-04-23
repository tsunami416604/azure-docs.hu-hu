---
title: A Service Fabric-fürt kapacitásának megtervezése
description: Service Fabric a fürt kapacitásának tervezési szempontjait. Nodetypes, üzemeltetés, tartósság és megbízhatósági szintek
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
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Service Fabric a fürt kapacitásának tervezési szempontjait
A kapacitástervezés az éles rendszerek üzembe helyezésének lényeges lépése. Íme néhány olyan elem, amelyet a folyamat részeként kell figyelembe vennie.

* Azon csomópont-típusok száma, amelyeknek a fürtnek el kell indulnia
* Az egyes csomópontok típusának tulajdonságai (méret, elsődleges, internet felé irányuló, virtuális gépek száma stb.)
* A fürt megbízhatóságra és tartósságra vonatkozó jellemzői.

> [!NOTE]
> A tervezés során minimálisan ellenőrizze az összes **nem engedélyezett** frissítési szabályzat értékét. Ezzel a beállítással biztosíthatja, hogy a rendszer megfelelően állítsa be a megfelelő értékeket, és csökkentse a fürt elégetését később, a nem módosítható rendszerkonfigurációs beállítások miatt. 
> 

Röviden tekintsük át ezeket az elemeket.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Azon csomópont-típusok száma, amelyeknek a fürtnek el kell indulnia
Először is meg kell állapítania, hogy a létrehozandó fürt milyen módon lesz használatban.  Milyen típusú alkalmazásokat kíván telepíteni a fürtbe? Ha nem törli a fürt célját, valószínűleg még nem áll készen a kapacitás-tervezési folyamat megadására.

Határozza meg, hogy a fürt hány csomópont-típust kell elindítania.  Mindegyik csomópont-típus egy virtuálisgép-méretezési csoportra van leképezve. Ezután mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy fel, eltérő nyitott portokkal rendelkezhet, és eltérő kapacitásmetrikái lehetnek. Ezért a csomópont-típusok számának döntése alapvetően a következő szempontokat követi:

* Az alkalmazása több szolgáltatással rendelkezik, és ezek közül bármelyiknek nyilvánosnak vagy internetkapcsolatnak kell lennie? A tipikus alkalmazások olyan előtér-átjáró szolgáltatást tartalmaznak, amely egy ügyféltől érkező adatokat fogad, valamint egy vagy több háttér-szolgáltatást, amely az előtér-szolgáltatásokkal kommunikál. Így ebben az esetben a befejezéshez legalább két csomópont-típust kell beállítania.
* Különböző infrastrukturális igényeket (például nagyobb RAM-ot vagy magasabb CPU-ciklusokat) igényelnek a szolgáltatásai (az alkalmazást alkotó alkalmazások)? Tegyük fel például, hogy a telepíteni kívánt alkalmazás előtér-szolgáltatást és háttér-szolgáltatást tartalmaz. Az előtér-szolgáltatás a kisebb méretű virtuális gépeken (például a D2-ben) is futtatható, amelyeken a portok nyitva vannak az interneten.  A háttérben futó szolgáltatás azonban nagy számítási igényű, és nagyobb méretű virtuális gépeken (például D4, D6, D15) kell futnia, amelyek nem az internetkapcsolattal rendelkeznek.
  
  Ebben a példában úgy is dönthet, hogy az összes szolgáltatást egy csomópont-típuson helyezi el, ezért azt javasoljuk, hogy egy két csomóponttal rendelkező fürtbe helyezze őket.  Ez lehetővé teszi, hogy az egyes csomópont-típusok különböző tulajdonságokkal rendelkezzenek, például az internetkapcsolat vagy a virtuális gép méretével. A virtuális gépek száma egymástól függetlenül méretezhető.  
* Mivel nem tudja megjósolni a jövőt, tekintse meg a tényeket, és válassza ki, hogy hány csomópont-típust kell kezdenie az alkalmazásokhoz. Később bármikor hozzáadhat vagy eltávolíthat csomópont-típusokat. Egy Service Fabric fürtnek legalább egy csomópont-típussal kell rendelkeznie.

## <a name="the-properties-of-each-node-type"></a>Az egyes csomópontok típusának tulajdonságai
A **csomópont típusa** a Cloud Services szerepköreinek megfelelőként tekinthető meg. A csomópontok típusai határozzák meg a virtuálisgép-méreteket, a virtuális gépek számát és a tulajdonságaikat. Minden Service Fabric fürtben definiált csomópont-típus egy [virtuálisgép-méretezési csoportra](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)mutat.  
Az egyes csomópont-típusok külön méretezési csoportok, amelyek egymástól függetlenül méretezhetők, különböző portokat nyitnak meg, és különböző kapacitási metrikákkal rendelkeznek. További információ a csomópontok típusai és a virtuálisgép-méretezési csoportok közötti kapcsolatokról, az RDP-nek az egyik példányhoz való használatáról, az új portok megnyitásáról és így tovább: [Service Fabric fürtcsomópontok típusai](service-fabric-cluster-nodetypes.md).

Egy Service Fabric-fürt több csomópont-típusból állhat. Ebben az esetben a fürt egy elsődleges csomópont-típusból és egy vagy több nem elsődleges csomópontból áll.

Az egycsomópontos típusok nem tudják megbízhatóan méretezni a virtuálisgép-méretezési csoporton túli 100-csomópontok számát az SF-alkalmazásokhoz. Ha a 100-nél több csomópontot szeretne megbízhatóan elérni, további virtuálisgép-méretezési csoportokat kell hozzáadnia.

### <a name="primary-node-type"></a>Elsődleges csomópont típusa

A Service Fabric rendszerszolgáltatások (például a Fürtfelügyelő szolgáltatás vagy a lemezképtároló szolgáltatás) az elsődleges csomópont típusára kerülnek. 

![Képernyőfelvétel két csomóponttal rendelkező fürtről][SystemServices]

* Az elsődleges csomópont típusához tartozó **virtuális gépek minimális méretét** a választott **tartóssági szint** határozza meg. Az alapértelmezett tartóssági szintje bronz. További részletekért tekintse meg a [fürt tartóssági jellemzőit](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) .  
* Az elsődleges csomópont típusához tartozó **virtuális gépek minimális számát** a választott **megbízhatósági szint** határozza meg. Az alapértelmezett megbízhatósági szintek az ezüst. További részletekért tekintse meg a [fürt megbízhatósági jellemzőit](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) .  

A Azure Resource Manager sablonban az elsődleges csomópont típusa a `isPrimary` [csomópont típusú definícióban](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object)található attribútummal van konfigurálva.

### <a name="non-primary-node-type"></a>Nem elsődleges csomópont típusa

Egy több csomópontot tartalmazó fürtben van egy elsődleges csomópont típusa, a többi pedig nem elsődleges.

* A nem elsődleges csomópont típusú **virtuális gépek minimális méretét** a választott **tartóssági szint** határozza meg. Az alapértelmezett tartóssági szintje bronz. További információ: a [fürt tartóssági jellemzői](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* A nem elsődleges csomópont típusú **virtuális gépek minimális száma** egy. Ezt a számot azonban az ebben a csomópont-típusban futtatni kívánt alkalmazás/szolgáltatások replikáinak száma alapján kell kiválasztani. A csomópont-típusban lévő virtuális gépek száma a fürt üzembe helyezése után növelhető.

## <a name="the-durability-characteristics-of-the-cluster"></a>A fürt tartósságára vonatkozó jellemzők
A tartóssági szint azt jelzi, hogy a rendszer a virtuális gépek által a mögöttes Azure-infrastruktúrához tartozó jogosultságokat használja. Az elsődleges csomópont típusában ez a jogosultság lehetővé teszi, hogy a Service Fabric szüneteltetni lehessen a rendszerszolgáltatások és az állapot-nyilvántartó szolgáltatások Kvórumának követelményeit érintő virtuálisgép-szintű infrastruktúra-kérelmeket (például a virtuális gépek újraindítását, a virtuális gépek rendszerképét vagy a virtuális gépek áttelepítését). A nem elsődleges csomópontok típusainál ez a jogosultság lehetővé teszi, hogy a Service Fabric szüneteltetni lehessen a virtuálisgép-szintű infrastruktúra-kérelmeket (például a virtuális gépek újraindítását, a virtuális gép rendszerképét és a virtuális gépek áttelepítését), amelyek hatással vannak az állapot-nyilvántartó szolgáltatásokra

| Tartóssági szintek  | A virtuális gépek minimálisan szükséges száma | Támogatott VM-beli SKU-ket                                                                  | A virtuálisgép-méretezési csoporton elérhető frissítések                               | Az Azure által kezdeményezett frissítések és karbantartás                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Arany             | 5                              | Egyetlen ügyfél számára dedikált teljes csomópontos SKU-EK (például L32s, GS5, G5, DS15_v2, D15_v2) | A Service Fabric-fürt általi jóváhagyásig késleltethető | Akár 2 óráig is szüneteltethető, így a replikák a korábbi hibák miatt további időt is igénybe vehetnek. |
| Ezüst           | 5                              | Egymagos vagy annál újabb virtuális gépek legalább 50 GB helyi SSD-vel                      | A Service Fabric-fürt általi jóváhagyásig késleltethető | Nem késleltethető jelentős időszakra                                                    |
| Bronz           | 1                              | Legalább 50 GB helyi SSD-vel rendelkező virtuális gépek                                              | Nem késlelteti a Service Fabric-fürt           | Nem késleltethető jelentős időszakra                                                    |

> [!WARNING]
> A bronz tartóssággal futó csomópont-típusok _nem kapnak jogosultságot_. Ez azt jelenti, hogy az állapot-nyilvántartó számítási feladatokat befolyásoló infrastrukturális feladatok nem lesznek leállítva vagy késleltetve, ami hatással lehet a számítási feladatokra. Csak az állapot nélküli munkaterheléseket futtató csomópont-típusok esetében használjon Bronzat. Az éles számítási feladatokhoz a Silver vagy a Above futtatása ajánlott. 
> 
> Bármilyen tartóssági szinttől függetlenül a virtuálisgép-méretezési csoport [felszabadítási](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) művelete el fogja pusztítani a fürtöt.

**Az ezüst vagy arany tartóssági szintek használatának előnyei**
 
- Csökkenti a szükséges lépések számát a méretezési műveletekben (azaz a csomópont inaktiválása és a Remove-ServiceFabricNodeState automatikusan történik).
- Csökkenti az adatvesztés kockázatát az ügyfél által kezdeményezett, helyi virtuálisgép-módosítási művelet vagy az Azure-infrastruktúra műveletei miatt.

**Az ezüst vagy arany tartóssági szintek használatának hátránya**
 
- A virtuálisgép-méretezési csoportra és más kapcsolódó Azure-erőforrásokra való üzembe helyezések késleltethető, időtúllépést okozhatnak, vagy a fürtben vagy az infrastruktúra szintjén is teljes mértékben letiltható. 
- Növeli a replika- [életciklus eseményeinek](service-fabric-reliable-services-lifecycle.md) (például az elsődleges swapok) számát az Azure-infrastruktúra műveletei során felmerülő automatizált csomópont-inaktiválások miatt.
- Az Azure platform szoftverfrissítések vagy a hardveres karbantartási tevékenységek ideje alatt a csomópontokon kívüli szolgáltatások is igénybe vesznek. Ezen tevékenységek során előfordulhat, hogy a letiltási/letiltási állapotú csomópontok láthatók. Ez csökkenti a fürt kapacitását átmenetileg, de nem befolyásolja a fürt vagy az alkalmazások rendelkezésre állását.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Javaslatok az ezüst vagy arany tartóssági szintek használatára

Használja az ezüst vagy a arany tartósságot az összes olyan csomópont-típushoz, amely a méretezéshez várhatóan (a virtuálisgép-példányok számának csökkentése) elvárt állapot-nyilvántartó szolgáltatásokat üzemeltet. A kibővíthető forgatókönyvek (virtuális gépek példányainak hozzáadásával) nem játszhatók le a tartóssági szinten, csak a méretezési lehetőségekkel.

### <a name="changing-durability-levels"></a>Tartóssági szintek módosítása
- Az ezüst vagy arany tartóssági szinttel rendelkező csomópont-típusok nem állíthatók vissza a Bronzra.
- A bronzról ezüstre vagy arannyal való frissítés néhány órát is igénybe vehet.
- A tartóssági szint módosításakor ügyeljen arra, hogy a virtuálisgép-méretezési csoport erőforrásában lévő Service Fabric-bővítmény konfigurációjában, valamint a Service Fabric-fürterőforrás csomópont-típus definíciójában is frissítse azt. Ezeknek az értékeknek egyezniük kell.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Működési javaslatok az ezüst vagy arany tartóssági szintre beállított csomópont-típushoz.

- Mindig kifogástalan állapotban tarthatja a fürtjét és az alkalmazásait, és gondoskodhat arról, hogy az alkalmazások az összes [szolgáltatás-replika életciklus-eseményre](service-fabric-reliable-services-lifecycle.md) válaszoljanak (például a buildben található replika beragadva).
- A virtuális gép SKU-jának módosításának biztonságosabbá tétele (vertikális felskálázás): a virtuálisgép-méretezési csoport VM SKU-jának módosítása számos lépést és szempontot igényel. Itt látható a gyakori problémák elkerülésére szolgáló folyamat.
    - **Nem elsődleges csomópontok típusai esetén:** Javasoljuk, hogy hozzon létre új virtuálisgép-méretezési készletet, módosítsa a szolgáltatás elhelyezésére vonatkozó korlátozást, hogy tartalmazza az új virtuálisgép-méretezési csoport/csomópont-típust, majd csökkentse a régi virtuálisgép-méretezési csoport példányainak számát nulla értékre, egyszerre egy csomópontra (ez biztosítja, hogy a csomópontok eltávolítása ne befolyásolja a fürt megbízhatóságát).
    - **Az elsődleges csomópont típusa:** Ha a kiválasztott virtuálisgép-SKU kapacitással rendelkezik, és egy nagyobb virtuálisgép-SKU-ra szeretne váltani, kövesse az [elsődleges csomópont típusának vertikális skálázásra vonatkozó](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-node-type)útmutatását. 

- Legalább öt csomópontot kell fenntartania minden olyan virtuálisgép-méretezési csoportnál, amelynél a tartóssági szint (arany vagy ezüst) engedélyezve van.
- Az ezüst vagy arany tartóssági szinttel rendelkező virtuálisgép-méretezési csoportoknak a Service Fabric fürtben lévő saját csomópont-típusra kell leképezniük. Több virtuálisgép-méretezési csoport egyetlen csomópontos típusra való leképezése megakadályozza a Service Fabric-fürt és az Azure-infrastruktúra megfelelő működésének összehangolását.
- Ne törölje a véletlenszerű virtuálisgép-példányokat, mindig használja a virtuálisgép-méretezési csoport méretezési funkcióját. A véletlenszerű virtuálisgép-példányok törlésének lehetősége van a virtuálisgép-példányok által az UD és az FD-ban elterjedt egyenlőtlenségek létrehozására. Ez az egyensúlyhiány hátrányosan befolyásolhatja, hogy a rendszer képes legyen a szolgáltatás példányainak vagy szolgáltatás-replikái közötti megfelelő terheléselosztásra.
- Ha az autoscale-t használja, akkor állítsa be azokat a szabályokat, amelyek (a virtuálisgép-példányok eltávolításával) a skálázást végzik, egyszerre csak egy csomópontot végeznek. Egyszerre legfeljebb egy példány skálázása nem biztonságos.
- Ha a virtuális gépeket az elsődleges csomópont típusa szerint törli vagy felszabadítja, soha ne csökkentse a kiosztott virtuális gépek számát, amely alatt a megbízhatósági réteg szükséges. Ezeket a műveleteket a rendszer határozatlan ideig blokkolja egy olyan méretezési csoportnál, amelynek tartóssági szintje ezüst vagy arany.

## <a name="the-reliability-characteristics-of-the-cluster"></a>A fürt megbízhatósági jellemzői
A megbízhatósági szint azon rendszerszolgáltatások replikáinak a megadására szolgál, amelyeket ebben a fürtben szeretne futtatni az elsődleges csomópont típusán. Minél több replikát, annál megbízhatóbb a rendszerszolgáltatások a fürtben.  

A megbízhatósági szintek a következő értékeket vehetik igénybe:

* Platinum – a rendszerszolgáltatások futtatása a célként megadott replikakészlet kilencnél több számával
* Gold – a rendszerszolgáltatások futtatása egy célként megadott replikakészlet-készlettel hét
* Silver – a rendszerszolgáltatások futtatása a célként megadott replikakészlet öt értékének számával 
* Bronz – a rendszerszolgáltatások futtatása a célként megadott replikakészlet három számával

> [!NOTE]
> A kiválasztott megbízhatósági szint határozza meg, hogy az elsődleges csomópont típusának milyen csomópontok minimális száma szükséges. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>A megbízhatósági szintű javaslatok

A fürt méretének növelése vagy csökkentése (a virtuálisgép-példányok száma az összes csomópontban) a fürt megbízhatóságát az egyik rétegről egy másikra kell frissítenie. Ennek hatására a rendszer elindítja a fürt frissítéseit a rendszerszolgáltatások replikáinak számának módosításához. Várjon, amíg a frissítés befejeződik, mielőtt bármilyen más módosítást hajt végre a fürtön, például csomópontok hozzáadását.  A frissítés előrehaladását Service Fabric Explorer vagy a [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) futtatásával figyelheti.

A megbízhatósági szintet a következő javaslat alapján választhatja ki.  A magok csomópontjainak számát a megbízhatósági szint csomópontjainak minimális száma is beállítja.  Az arany megbízhatóságú fürtök esetében például 7 mag csomópont található.

| **Fürtcsomópontok száma** | **Megbízhatósági szintek** |
| --- | --- |
| 1 |Ne határozza meg a megbízhatósági szint paraméterét, a rendszeren kiszámítva |
| 3 |Bronz |
| 5 vagy 6|Ezüst |
| 7 vagy 8 |Arany |
| 9 és fel |Platina |

## <a name="primary-node-type---capacity-guidance"></a>Elsődleges csomópont típusa – kapacitási útmutató

Az alábbi útmutató az elsődleges csomópont típusú kapacitás megtervezéséhez nyújt útmutatást:

- Virtuálisgép **-példányok száma az Azure-ban az éles számítási feladatok futtatásához:** Meg kell adnia egy minimális elsődleges csomópont-típust, amely 5, a megbízhatósági szint pedig ezüst.  
- A **tesztelési feladatokat az Azure-ban futtató virtuálisgép-példányok száma** Megadhatja a minimálisan 1 vagy 3 típusú elsődleges csomópont-méretet. Az egy csomópontos fürt speciális konfigurációval fut, így a fürt kiskálázása nem támogatott. Az egy csomópontos fürtnek nincs megbízhatósága, így a Resource Manager-sablonban el kell távolítania/nem kell megadnia a konfigurációt (nem kell beállítania a konfigurációs értéket). Ha az egy csomópontos fürtöt a portálon keresztül állítja be, akkor a rendszer automatikusan gondoskodik a konfigurációról. Az egyik és a három csomópontos fürt nem támogatott éles számítási feladatok futtatásához. 
- **VM SKU:** Az elsődleges csomópont típusa a rendszerszolgáltatások futtatásának helye, így a hozzá tartozó virtuálisgép-SKU-nak figyelembe kell vennie a fürtbe helyezendő teljes terhelést. Itt egy analógia szemlélteti, hogy mit gondolok itt – gondoljunk az elsődleges csomópont típusára, mint a "tüdő", ami az oxigént biztosítja az agy számára, így ha az agy nem kap elég oxigént, a törzs szenved. 

Mivel a fürt kapacitási igényeit a fürtben futtatni kívánt számítási feladatok határozzák meg, nem biztosítunk Önnek minőségi útmutatást az adott számítási feladathoz, azonban itt találja az első lépésekhez segítséget nyújtó átfogó útmutatást.

Éles munkaterhelések esetén: 

- Javasoljuk, hogy a fürtök elsődleges NodeType a rendszerszolgáltatásokra fordítsa, és használja az elhelyezési korlátozásokat az alkalmazás másodlagos NodeTypes való üzembe helyezéséhez.
- Az ajánlott VM-SKU standard D2_V2 vagy azzal egyenértékű, legalább 50 GB helyi SSD-vel.
- A minimálisan támogatott virtuális gép SKU Standard_D2_V3 vagy standard D1_V2, vagy azzal egyenértékű, legalább 50 GB helyi SSD-vel. 
- A Javaslatunk legalább 50 GB. A számítási feladatokhoz – különösen a Windows-tárolók futtatásakor – nagyobb lemezek szükségesek. 
- A részleges Core virtuális gépek, például a standard a0 nem támogatottak az éles számítási feladatokhoz.
- Az adatsorozatos virtuális gépek nem támogatottak a teljesítmény szempontjából a termelési számítási feladatok esetében.
- Az alacsony prioritású virtuális gépek nem támogatottak.

> [!WARNING]
> Az elsődleges csomópont virtuális gép SKU-méretének módosítása egy futó fürtön, egy méretezési művelet, és dokumentálva van a [virtuálisgép-méretezési csoport horizontális Felskálázási](virtual-machine-scale-set-scale-node-type-scale-out.md) dokumentációjában.

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Nem elsődleges csomópont típusa – kapacitási útmutató állapot-nyilvántartó számítási feladatokhoz

Ez az útmutató olyan állapot-nyilvántartó számítási feladatokhoz használható, amelyek a Service Fabric [megbízható gyűjteményeit vagy](service-fabric-choose-framework.md) a nem elsődleges csomópont-típusban futtatott megbízható szereplőket használják.

Virtuálisgép- **példányok száma:** Az állapot-nyilvántartó számítási feladatokhoz ajánlott a minimális és a célként megadott replika 5-öt kell futtatnia. Ez azt jelenti, hogy az állandó állapotban az egyes tartalék tartományokban és a frissítési tartományokban egy replikával (egy replikakészlet-készletből) fejeződik be. Az elsődleges csomópont típusának teljes megbízhatósági szint koncepciója a rendszerszolgáltatások beállításainak megadása. Ezért ugyanez a megfontolás vonatkozik az állapot-nyilvántartó szolgáltatásokra is.

Az éles munkaterhelések esetében tehát a minimálisan ajánlott nem elsődleges csomópont típusa 5, ha állapot-nyilvántartó számítási feladatokat futtat.

**VM SKU:** Ez az a csomópont-típus, ahol az alkalmazás-szolgáltatások futnak, ezért az Ön által választott virtuálisgép-SKU-nak figyelembe kell vennie az egyes csomópontokon elhelyezni kívánt maximális terhelést. A csomópont típusú kapacitás szükségleteit a fürtben futtatni kívánt számítási feladatok határozzák meg, ezért nem biztosítunk Önnek minőségi útmutatást az adott számítási feladatokhoz, de itt találja az első lépésekhez segítséget nyújtó átfogó útmutatót.

Éles számítási feladatokhoz 

- Az ajánlott VM-SKU standard D2_V2 vagy azzal egyenértékű, legalább 50 GB helyi SSD-vel.
- A minimálisan támogatott virtuális gép SKU Standard_D2_V3 vagy standard D1_V2, vagy azzal egyenértékű, legalább 50 GB helyi SSD-vel. 
- A részleges Core virtuális gépek, például a standard a0 nem támogatottak az éles számítási feladatokhoz.
- Az adatsorozatos virtuális gépek nem támogatottak a teljesítmény szempontjából a termelési számítási feladatok esetében.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Nem elsődleges csomópont típusa – kapacitási útmutató állapot nélküli munkaterhelésekhez

A nem elsődleges csomópont-típuson futó állapot nélküli munkaterhelések útmutatója.

Virtuálisgép- **példányok száma:** Az állapot nélküli éles számítási feladatokhoz a minimálisan támogatott, nem elsődleges csomópont típusának mérete 2. Ez lehetővé teszi az alkalmazás két állapot nélküli példányának futtatását, és lehetővé teszi, hogy a szolgáltatás megmaradjon a virtuálisgép-példányok elvesztése miatt. 

**VM SKU:** Ez az a csomópont-típus, ahol az alkalmazás-szolgáltatások futnak, ezért az Ön által választott virtuálisgép-SKU-nak figyelembe kell vennie az egyes csomópontokon elhelyezni kívánt maximális terhelést. A csomópont típusú kapacitás szükségleteit a fürtben futtatni kívánt munkaterhelés határozza meg. Az adott számítási feladathoz nem biztosítunk minőségi útmutatást.  Itt azonban az első lépésekhez segítséget nyújtó átfogó útmutató.

Éles számítási feladatokhoz 

- Az ajánlott VM-SKU standard D2_V2 vagy azzal egyenértékű. 
- A minimálisan támogatott virtuálisgép-SKU standard D1 vagy standard D1_V2 vagy azzal egyenértékű. 
- A részleges Core virtuális gépek, például a standard a0 nem támogatottak az éles számítási feladatokhoz.
- Az adatsorozatos virtuális gépek nem támogatottak a teljesítmény szempontjából a termelési számítási feladatok esetében.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>További lépések
Miután befejezte a kapacitás megtervezését és a fürt beállítását, olvassa el a következőket:

* [Service Fabric-fürt biztonsága](service-fabric-cluster-security.md)
* [Fürt méretezése Service Fabric](service-fabric-cluster-scaling.md)
* [Vész-helyreállítási tervezés](service-fabric-disaster-recovery.md)
* [A Nodetypes kapcsolata a virtuálisgép-méretezési csoporttal](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
