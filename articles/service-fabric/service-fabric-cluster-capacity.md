---
title: A Service Fabric fürtkapacitás tervezése |} A Microsoft Docs
description: A Service Fabric-fürt kapacitása tervezési szempontok. NodeType, Operations, tartósság és megbízhatósági szint
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: chackdan
ms.openlocfilehash: 86099c5385c14ce7cf10bbc84ebe1b5e70b2b3e6
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094207"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Tervezési megfontolások a Service Fabric-fürt kapacitása
Éles rendszerek üzembe a kapacitástervezés egy fontos lépés. Íme néhány, az elem, meg kell figyelembe venni, hogy a folyamat részeként.

* A fürt kell kezdődnie csomóponttípusok száma
* Az egyes csomóponttípusok (méret, elsődleges, internetkapcsolattal rendelkező, a virtuális gépek száma stb.) tulajdonságai
* A fürt megbízhatóságra és tartósságra vonatkozó jellemzői.

> [!NOTE]
> Minimálisan tekintse át az összes **nem engedélyezett** frissítési házirend értékei tervezése során. Ez a annak érdekében, hogy megfelelően állítsa be az értékeket, és enyhítheti a ehhez írja a fürt később miatt nem módosítható rendszer konfigurációs beállításait. 
> 

Mondja el röviden tekintse át ezeket az elemeket.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>A fürt kell kezdődnie csomóponttípusok száma
Először döntse el, mi a fürt létrehozásakor fog használni a.  Milyen típusú alkalmazásokat kíván a fürt üzembe helyezése a? Ha nem egyértelmű az a célja a fürt, akkor nagy valószínűséggel még nem adja meg a kapacitást, tervezési folyamat kész.

Létrehozza a fürt kell kezdődnie csomóponttípusok száma.  Mindegyik csomóponttípus van rendelve egy virtuálisgép-méretezési csoportot. Ezután mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy fel, eltérő nyitott portokkal rendelkezhet, és eltérő kapacitásmetrikái lehetnek. Ezért csomóponttípusok száma döntése lényegében tartalmaz a következő szempontokat:

* Az alkalmazás nem rendelkezik több szolgáltatást, és ezek közül bármelyik szükséges nyilvános vagy az internetre? Tipikus alkalmazások tartalmazzák egy előtér-átjáró szolgáltatás, amely fogad az ügyféltől érkező bemeneti és a egy vagy több háttér-szolgáltatás, amely kommunikálni a előtér-szolgáltatásokat. Tehát ebben az esetben, végül kellene legalább két csomópont típusa.
* Rendelkezik a szolgáltatások (alkotó az alkalmazás) infrastruktúra különböző igényeinek megfelelően, például a nagyobb RAM vagy nagyobb CPU-ciklusok? Például tegyük fel, hogy az alkalmazás, amely számára telepíteni kívánja előtér-szolgáltatás és a egy háttérszolgáltatás tartalmazza-e. Az előtér-szolgáltatás futtatható kisebb rendelkező virtuális gépeken (VM-méretek D2 hasonlóan) a portokat nyissa meg az internethez.  A háttérszolgáltatás, azonban nagy számítási igényű és kell futtatni használó virtuális gépeken nagyobb (például: D4, D6, D15 Virtuálisgép-méretek), amelyek nem az internet felé néző.
  
  Ebben a példában eldöntheti, hogy minden szolgáltatás elhelyezése több csomóponttípus, bár ajánlott elhelyezni azokat egy fürtben két csomópont adattípusukkal együtt.  Ez lehetővé teszi, hogy mindegyik csomóponttípus, hogy a különböző tulajdonságai például internetkapcsolat vagy Virtuálisgép-méretet. A virtuális gépek száma skálázhatók egymástól függetlenül, illetve.  
* A jövőben nem előre jelezni, mert tények ismeri a go, és válassza ki, hogy az alkalmazások a kezdéshez szükséges csomóponttípusok száma. Mindig hozzáadhat vagy későbbi távolítsa el a csomóponttípusok. Service Fabric-fürt legalább egy csomópont típusúnak kell lennie.

## <a name="the-properties-of-each-node-type"></a>Az egyes csomóponttípusok tulajdonságai
A **csomóponttípus** Cloud Services-szerepkörök ekvivalensként láthatók. Csomóponttípusok határozzák meg, a Virtuálisgép-méretek, a virtuális gépek számát és azok tulajdonságait. Minden csomóponttípusa egy Service Fabric-fürt definiált képez le egy [virtuálisgép-méretezési csoport](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  
Mindegyik csomóponttípus különböző méretezési csoport beállítása és vertikálisan fel vagy le egymástól függetlenül, amelyekre eltérő nyitott portokkal, és eltérő kapacitásmetrikái. Csomóponttípusok és virtuálisgép-méretezési csoportok közötti kapcsolatok kapcsolatos további információkért hogyan egy példányt, távoli asztali eléréséhez nyissa meg az új portok, és így tovább, lásd: [Service Fabric-fürt csomóponttípusok](service-fabric-cluster-nodetypes.md).

Service Fabric-fürtben több csomóponttípus állhat. Ebben az esetben a fürt tartalmaz egy elsődleges csomóponttípusok és a egy vagy több nem elsődleges csomóponttípusok.

Egyetlen csomóponttípus; nem lehet megbízhatóan méretezheti meghaladja a 100-csomópontok maximális száma virtuálisgép-méretezési SF az alkalmazások számára. elérése nagyobb, mint 100 csomópont megbízhatóan, arra kéri, adjon hozzá további virtuálisgép-méretezési csoportok.

### <a name="primary-node-type"></a>Elsődleges csomóponttípus

A Service Fabric-rendszerszolgáltatások (például a kezelő szolgáltatás vagy kép Store szolgáltatás) az elsődleges csomóponttípushoz kerülnek. 

![Képernyőfelvétel egy fürtöt, amely két csomópontot típusai][SystemServices]

* A **virtuális gép minimális mérete** az elsődleges csomópont típusa határozza meg a **tartóssági szint** választja. Az alapértelmezett tartóssági szint bronz. Lásd: [a fürt tartóssági jellemzőit](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) további részletekért.  
* A **virtuális gépek minimális száma** az elsődleges csomópont típusa határozza meg a **megbízhatósági szint** választja. Az alapértelmezett megbízhatóság szintje Silver. Lásd: [a fürt megbízhatósági jellemzőit](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) további részletekért.  

Az Azure Resource Manager-sablonból az elsődleges csomóponttípushoz van konfigurálva a `isPrimary` attribútum alapján az [csomópont típusdefiníció](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object).

### <a name="non-primary-node-type"></a>A nem elsődleges csomóponttípus

Egy fürtben több csomóponttípus az van egy elsődleges csomóponttípus és a többi nem elsődleges.

* A **virtuális gép minimális mérete** nem elsődleges csomópont típusa határozza meg a **tartóssági szint** választja. Az alapértelmezett tartóssági szint bronz. Lásd: [a fürt tartóssági jellemzőit](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) további részletekért.  
* A **virtuális gépek minimális száma** nem elsődleges csomópontot típusok egyike. Azonban ez a szám, hogy ennek a csomóponttípusnak futtatni kívánt alkalmazás/szolgáltatás replikák száma alapján kell kiválasztani. A csomópont típusa a virtuális gépek száma növekedhet, a fürt üzembe helyezését követően.

## <a name="the-durability-characteristics-of-the-cluster"></a>A fürt tartóssági jellemzői
A tartóssági szint, amelyek jelzik, hogy a rendszer a jogosultságokat, amely a virtuális gépek rendelkeznek az alapul szolgáló Azure-infrastruktúra-szolgál. Az elsődleges csomóponttípushoz Ez a jogosultság lehetővé teszi a Service Fabric bármely virtuális gép szintű infrastruktúra kérelem (például egy virtuális gép újraindítása, a virtuális gép rendszerképét alaphelyzetbe állítani vagy a virtuális gépek migrálása), amely hatással van a kvórum követelményei a helyrendszeri szolgáltatások és az állapotalapú szolgáltatások felfüggesztését. Ez a jogosultság nem elsődleges csomóponttípusok megállítja a virtuális gép szintű infrastruktúra kérések eredményéről (például a virtuális gép újraindítása, a virtuális gép rendszerképét alaphelyzetbe állítani és a virtuális gépek migrálása), amely hatással van a kvórum követelményei az állapotalapú szolgáltatások Service Fabric lehetővé teszi.

| Tartóssági szint  | Virtuális gépek szükséges minimális száma | Támogatott VM-termékváltozatok                                                                  | Frissítések választja ki a vmss-hez                               | Frissítések és az Azure által kezdeményezett karbantartás                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Arany             | 5                              | (Például L32s, GS5, G5, DS15_v2, D15_v2) egyetlen ügyfél számára dedikált csomópontot teljes termékváltozatok | Mindaddig, amíg a Service Fabric-fürt által jóváhagyott késhet | 2 óra / UD replikák helyreállításához a korábbi hibák további időt szüneteltethetők |
| Silver           | 5                              | Egymagos, vagy az újabb virtuális gépek                                                        | Mindaddig, amíg a Service Fabric-fürt által jóváhagyott késhet | Nem minden jelentős ideig késleltethetők                                                    |
| Bronz           | 1                              | Összes                                                                                | Nem időszakú késleltetéssel kerül a Service Fabric-fürt           | Nem minden jelentős ideig késleltethetők                                                    |

> [!WARNING]
> A csomóponttípusok a bronz tartóssági futó beszerzése _jogosultság nélküli_. Ez azt jelenti, hogy infrastruktúra feladatok, amelyek hatással az állapot nélküli munkaterhelés nem kell leállt vagy késleltetett, ami hatással lehet a számítási feladatokat. Csak az állapot nélküli számítási feladatokat futtató csomóponttípusok csak bronz használja. Az éles számítási feladatokhoz Silver vagy javasolt felett. 

> Függetlenül bármely tartóssági szint [felszabadítási](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/deallocate) Virtuálisgép-méretezési művelet szüntesse meg a fürt

**Silver vagy Gold tartóssági szint használatának előnyei**
 
- Szükséges lépéseket és a horizontális leskálázási művelet csökkenti (azt jelenti, csomópont inaktiválásra és a Remove-ServiceFabricNodeState neve automatikusan).
- Csökkenti a egy ügyfél által kezdeményezett helyben a VM-Termékváltozatok módosítási művelet vagy az Azure-infrastruktúra működését okozta adatvesztés kockázatát.

**Silver vagy Gold tartóssági szint használatának hátrányai**
 
- Központi telepítések a virtuálisgép-méretezési, állítsa be és más kapcsolódó Azure-erőforrások késhet, is időtúllépéssel fejeződött be, vagy teljes egészében a fürtben, vagy az infrastruktúra szintjén kapcsolatos problémák blokkolhatja. 
- Növeli a számát [replika életciklusesemények](service-fabric-reliable-services-lifecycle.md) (például elsődleges cseréje) oka az, hogy automatikus csomópont deactivations Azure-infrastruktúra műveletek során.
- Vesz igénybe a csomópontok időszakokra, miközben az Azure platform szoftverfrissítéseket és a hardver karbantartási tevékenységek nem működik. Ezek a tevékenységek során letiltása/letiltott állapotú csomópontok jelenhet meg. Ez a fürt kapacitásának ideiglenesen csökkenti, de nem érinti a fürt vagy az alkalmazások rendelkezésre állását.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Silver vagy Gold tartóssági szint használatára vonatkozó ajánlások

Silver vagy Gold tartóssági használja minden csomópont esetében, amelyek várhatóan horizontális leskálázási állapotalapú szolgáltatások (csökkentése érdekében a Virtuálisgép-példányok száma) gyakran, és szeretne használni, hogy később, üzembe helyezési műveletek és a kapacitás, hogy a beállításukat megkönnyíti a horizontális leskálázási értéke műveletek. A horizontális felskálázási forgatókönyveket (virtuális gépek példányok hozzáadása) játssza le a tartóssági szint a választott be, csak horizontális leskálázási does.

### <a name="changing-durability-levels"></a>Tartóssági szint módosítása
- Silver vagy Gold szintű tartósságot csomóponttípusok a bronz értéket nem lehet visszaminősíteni.
- Silver vagy Gold bronz verzióról néhány órát is igénybe vehet.
- Tartóssági szint módosításakor ügyeljen arra, mind a Service Fabric bővítmény konfigurációja a virtuálisgép-méretezési készlet erőforrás, és a Service Fabric-fürt erőforrás csomópont típusdefinícióban kell frissíteni. Ezeknek az értékeknek egyezniük kell.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>A csomópont működési javaslatok írja be a beállított a silver vagy gold tartóssági szint.

- Hagyja meg a fürt és az alkalmazások megfelelő mindenkor, és győződjön meg arról, hogy válaszol-e alkalmazásokat az összes [replika életciklusesemények szolgáltatás](service-fabric-reliable-services-lifecycle.md) (ilyen például a replika a build beragad) időben elkészüljön.
- Elfogadja a biztonságosabb módon, hogy egy virtuális Gépet a Termékváltozat módosítása (horizontális felskálázás/leskálázás): egy virtuálisgép-méretezési csoportot a virtuális gép Termékváltozata módosítása természetüknél fogva egy nem biztonságos művelet, ezért el kell kerülni, ha lehetséges. Itt az a folyamat, követheti a gyakori problémák elkerülése érdekében.
    - **A nem elsődleges csomóponttípusok:** javasoljuk, hogy létrehozott új virtuálisgép-méretezési csoportot, a szolgáltatás-elhelyezési korlátozás közé tartozik az új virtuális gép méretezési készlet vagy csomópont típusa, és csökkentse a régi virtuálisgép-méretezési készlet példány módosítása száma 0-ra, egy adott csomópont egy időpontot (Ez a győződjön meg arról, hogy a csomópontok eltávolítása nincs hatással a fürt megbízhatóságát).
    - **Az elsődleges csomóponttípus:** azt javasoljuk, nem módosíthatja, hogy az elsődleges csomóponttípushoz VM Termékváltozata. -Termékváltozat nem támogatott az elsődleges csomóponttípushoz módosítását. Ha az új Termékváltozat oka kapacitás, javasoljuk, további példányok hozzáadása. Ha nem lehetséges, hozzon létre egy új fürtöt, és [alkalmazásállapot visszaállítása](service-fabric-reliable-services-backup-restore.md) (ha van ilyen) a régi fürtről. Nem kell minden olyan szolgáltatás rendszerállapot visszaállítása, akkor létrejönnek az új fürthöz az alkalmazások központi telepítésekor. Ugyanúgy, mintha állapot nélküli alkalmazások a fürtben futó, majd kell az új fürtre alkalmazások üzembe helyezése, akkor semmit nem kell visszaállítani. Ha úgy dönt, nyissa meg a nem támogatott útvonal és a VM-Termékváltozat módosítani kívánja, majd végezni a módosításokat a virtuálisgép-méretezési csoport beállítása modell definícióját, hogy tükrözzék az új Termékváltozat. Ha a fürt egyetlen csomópont típusa, majd ellenőrizze, hogy, hogy válaszol-e az állapotalapú alkalmazások az összes [replika életciklusesemények szolgáltatás](service-fabric-reliable-services-lifecycle.md) (ilyen például a replika a build beragad) időben feldolgozza, és hogy a szolgáltatás replika újraépítése időtartam (a Silver szintű tartóssági szint) kisebb, mint öt perc alatt. 
    
- Karbantartása öt csomópont minden olyan virtuálisgép-méretezési tartóssági szintű arany és ezüst engedélyezve van a minimális számát.
- Minden egyes Virtuálisgép-méretezési csoport Silver vagy Gold tartóssági szint beállítása a Service Fabric-fürtöt a saját csomóponttípusa kell rendelni. Több virtuális gép leképezés egyetlen csomóponttípus; a méretezési csoportok megakadályozza, hogy a Service Fabric-fürt és az Azure-infrastruktúra közötti koordinációt megfelelően működik.
- Nem véletlen Virtuálisgép-példányok törlése, mindig a virtuális gép méretezési készlet vertikális leskálázási funkciót használja. A törlés véletlenszerű Virtuálisgép-példányok potenciálisan egyensúlyhiány létrehozhat UD és FD elosztva a Virtuálisgép-példány. Ez egyenetlenségének negatívan megfelelően igazítva a szolgáltatás példányai vagy szolgáltatás replikák terheléselosztása rendszerek lehetővé teszi.
- Ha használja az automatikus méretezés, állítsa a szabályokat úgy, hogy a skála (Virtuálisgép-példányok eltávolítása) egyszerre csak egy csomópont végzett. Skálázás lefelé több példány egyszerre jelenleg nem biztonságos.
- Ha törlése, illetve az elsődleges csomóponttípushoz a virtuális gép felszabadítása, akkor soha nem csökkentse a megbízhatósági szint szükséges alábbi lefoglalt virtuális gépek száma. Ezek a műveletek határozatlan időre blokkolja Silver vagy Gold szintű tartósságot méretezési csoportban lévő.

## <a name="the-reliability-characteristics-of-the-cluster"></a>A fürt megbízhatósági jellemzői
A megbízhatósági szint segítségével állítsa be a replikákat a rendszer szolgáltatások a fürtben futó az elsődleges csomóponttípushoz kívánt számát. A további a replikák száma, a megbízhatóbb a helyrendszeri szolgáltatások vannak, a fürtben.  

A megbízhatósági szint hajthatja végre a következő értékeket:

* Platinum – a rendszer szolgáltatások futtatása cél replikakészlet nine száma
* Gold – a rendszer szolgáltatások futtatása cél replika beállítva a hét száma
* Ezüst - futtassa a Helyrendszeri szolgáltatások cél replikakészlet öt száma 
* Bronz – a rendszer szolgáltatások futtatása cél replikakészlet három száma

> [!NOTE]
> A megbízhatósági szint úgy dönt, rendelkeznie kell az elsődleges csomóponttípushoz csomópontok minimális számát határozza meg. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>A megbízhatósági szint javaslatok

Ha növeli vagy csökkenti a fürt (az összes csomóponttípusok lévő Virtuálisgép-példányok összege) méretét, frissítenie kell a fürt az egyik rétegről a másikra megbízhatóságát. Ezzel elindítja a fürtfrissítések módosításához a rendszer szolgáltatások replika beállítása szükséges száma. Várjon, amíg a frissítés befejezését, mielőtt megváltoztatna más a fürt, például a csomópontok hozzáadása folyamatban van.  A frissítés a Service Fabric Explorerben vagy futtatja az előrehaladását nyomon követheti [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Ez a javaslat a megbízhatósági szint választásával.

| **Fürt mérete** | **Megbízhatósági szint** |
| --- | --- |
| 1 |Adja meg a megbízhatósági szint paramétert, a rendszer kiszámítja, |
| 3 |Bronz |
| 5 vagy 6|Silver |
| 7 vagy 8 |Arany |
| 9 és újabb |Platinum |

## <a name="primary-node-type---capacity-guidance"></a>Elsődleges csomóponttípus - kapacitás útmutató

Itt látható az elsődleges csomópont-példánytípusok kapacitásának tervezési útmutatást:

- **Bármely az éles számítási feladatok futtatása az Azure-beli Virtuálisgép-példányok száma:** meg kell adnia az elsődleges csomópont típusa méretének legalább 5 és a egy ezüst megbízhatósági szint.  
- **Tesztelési számítási feladatok futtatása az Azure-beli Virtuálisgép-példányok száma** is megadhat egy elsődleges csomópontok minimális méretének 1 vagy 3. Az egyik fürtcsomópont fut, egy speciális konfigurációja, és így, méretezési ki, hogy a fürt nem támogatott. Az egyik fürtcsomópont nincs megbízhatóság rendelkezik, és ezért a Resource Manager-sablonban kell eltávolítása/nem adja meg, hogy a configuration (nem a konfigurációs érték, nem elegendő). Ha beállította a egy csomópontot a fürthöz, állítsa be a portálon keresztül, majd a konfiguráció automatikusan elvégzi. Egy és három csomópontot tartalmazó fürt nem támogatottak az éles számítási feladatok futtatásához. 
- **A VM-Termékváltozatok:** elsődleges csomóponttípus az, ahol a rendszer szolgáltatások futnak, így a virtuális gép Termékváltozat úgy dönt, kell általános csúcsértéke figyelembe veszik a betöltés, tervezi, hogy a fürt helyezi. Íme egy hasonlattal bemutatják, milyen szeretnél itt – az elsődleges csomóponttípushoz, mint a "tüdőt", úgy érzi mit biztosít oxygent megvalósíthatja a, és így az agy nem elég oxygent kap, ha a szervezet romlik a. 

Számítási feladatot tervez futtatni a fürt a fürt szükséges kapacitást határozza meg, mivel nem biztosítunk az adott számítási feladathoz, azonban itt van a széles körű útmutatás nyújtása a minőségi útmutatása alapján használatának megkezdése

A termelési számítási feladatokhoz: 

- Azt javasoljuk, hogy a fürtök dedikált elsődleges NodeType csomóponttípus rendszerszolgáltatások és elhelyezési korlátozások használata a másodlagos NodeType azokat üzembe helyezni.
- Az ajánlott Virtuálisgép-Termékváltozat a Standard D3, a standard szintű D3_V2 vagy a megfelelő legalább 14 GB helyi SSD-.
- A minimális támogatott VM-Termékváltozatok használata Standard D1 vagy Standard D1_V2 vagy ezzel egyenértékű legalább 14 GB helyi SSD. 
- 14 GB helyi SSD a minimális követelmények része. Azt javasoljuk, legalább 50 GB. A számítási feladatok, különösen ha fut a Windows-tárolók nagyobb méretű lemezeket szükség. 
- Részleges core például Standard A0 VM Termékváltozatokban nem támogatottak a termelési számítási feladatokhoz.
- Standard A1-Termékváltozat nem támogatott a termelési számítási feladatokhoz megfelelő teljesítmény biztosítása érdekében.
- Alacsony prioritású virtuális gépek nem támogatottak.

> [!WARNING]
> Az elsődleges csomópont egy fürtön futó virtuális gép termékváltozat módosítása egy skálázási művelet, és részletes ismertetését lásd: [virtuálisgép-méretezési csoport horizontális felskálázási](virtual-machine-scale-set-scale-node-type-scale-out.md) dokumentációját.

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>A nem elsődleges csomóponttípus - kapacitás útmutatást az állapotalapú alkalmazások és szolgáltatások

Ez az útmutató az állapotalapú alkalmazások és szolgáltatások, a Service fabricet használja a [a reliable collections vagy a reliable Actors](service-fabric-choose-framework.md) , írja be a nem elsődleges csomópont futtatja.

**Virtuálisgép-példányok száma:** az éles számítási feladatokhoz, amelyek állapot-nyilvántartó ajánlott minimális és a cél replika-számot 5 futtassa. Ez azt jelenti, hogy a stabil állapotot, a kialakított egy replikát mindegyik tartalék tartomány és a frissítési tartomány (egy készletből replika). A teljes megbízhatósági szint esetében az elsődleges csomóponttípushoz koncepciójuk annak meghatározására, hogy ezt a beállítást a helyrendszeri szolgáltatások. Így az azonos figyelembe veszi az állapotalapú szolgáltatások vonatkozik.

Ezért az éles számítási feladatokhoz a minimális ajánlott nem elsődleges csomópontot típusa mérete 5, futtatásakor állapotalapú alkalmazások és szolgáltatások.

**A VM-Termékváltozatok:** Ez a csomópont típusa, az alkalmazás szolgáltatások futnak, így a virtuális gép Termékváltozata úgy dönt, figyelembe kell vennie a csúcsterhelés között azt tervezi, hogy egyes csomópontok helyezze. A kapacitásigények uzlu (nodetype), határozza meg a számítási feladatot tervez futtatni a fürtben, hogy nem kínálunk, hogy az adott számítási feladathoz, azonban itt van a széles körű útmutatás nyújtása a minőségi útmutatása alapján használatának megkezdése

A termelési számítási feladatokhoz 

- Az ajánlott Virtuálisgép-Termékváltozat a Standard D3, a standard szintű D3_V2 vagy a megfelelő legalább 14 GB helyi SSD-.
- A minimális támogatott VM-Termékváltozatok használata Standard D1 vagy Standard D1_V2 vagy ezzel egyenértékű legalább 14 GB helyi SSD. 
- Részleges core például Standard A0 VM Termékváltozatokban nem támogatottak a termelési számítási feladatokhoz.
- Standard A1 Termékváltozat kifejezetten nem támogatott a termelési számítási feladatokhoz megfelelő teljesítmény biztosítása érdekében.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>A nem elsődleges csomóponttípus - kapacitás útmutatást állapot nélküli számítási feladatokhoz

Ez az útmutató az állapot nélküli számítási feladatokat, amelyek a nem elsődleges NodeType csomóponttípus futtat.

**Virtuálisgép-példányok száma:** éles számítási feladatokra, amelyek az állapot nélküli elemek, a minimális támogatott nem elsődleges csomópontot típus mérete 2. Ez lehetővé teszi, hogy az alkalmazás- és a szolgáltatás lehetővé teszi egy Virtuálisgép-példány elvesztését stabilitást biztosít két állapotmentes példányának futtatása. 

**A VM-Termékváltozatok:** Ez a csomópont típusa, az alkalmazás szolgáltatások futnak, így a virtuális gép Termékváltozata úgy dönt, figyelembe kell vennie a csúcsterhelés között azt tervezi, hogy egyes csomópontok helyezze. A kapacitásigények, a csomópont típusa határozza meg a számítási feladatot tervez futtatni a fürtben, hogy nem kínálunk, hogy az adott számítási feladathoz, azonban itt van a széles körű útmutatás nyújtása a minőségi útmutatása alapján használatának megkezdése

A termelési számítási feladatokhoz 

- Az ajánlott Virtuálisgép-Termékváltozat a Standard D3 vagy a standard szintű D3_V2 vagy ezzel egyenértékű. 
- A minimális támogatott VM-Termékváltozatok használata Standard D1 vagy standard szintű D1_V2 vagy ezzel egyenértékű. 
- Részleges core például Standard A0 VM Termékváltozatokban nem támogatottak a termelési számítási feladatokhoz.
- Standard A1-Termékváltozat nem támogatott a termelési számítási feladatokhoz megfelelő teljesítmény biztosítása érdekében.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>További lépések
Fejezze be a kapacitástervezés és fürt létrehozása után olvassa el a következőket:

* [A Service Fabric-Fürtbiztonság](service-fabric-cluster-security.md)
* [A Service Fabric-fürt méretezése](service-fabric-cluster-scaling.md)
* [Vész-helyreállítás tervezése](service-fabric-disaster-recovery.md)
* [A virtuálisgép-méretezési csoport a NodeType kapcsolat beállítása](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
