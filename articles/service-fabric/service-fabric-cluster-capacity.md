---
title: A Service Fabric-fürt kapacitásának megtervezése |} Microsoft Docs
description: A Service Fabric fürt kapacitástervezésének szempontjai. A NodeType tulajdonságok értéke, a műveletek, a tartósság és a megbízhatóság rétegek
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: chackdan
ms.openlocfilehash: 848e27078d77a53a03d538b846eed0117d44dbe5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>A Service Fabric fürt kapacitástervezésének szempontjai
Minden éles telepítésében kapacitásának megtervezése fontos lépés. Az alábbiakban néhány kell figyelembe venni, hogy a folyamat részeként elemek.

* A fürt kell kezdődnie csomóponttípusok száma
* Az egyes (méret, elsődleges, internetre irányuló, virtuális gépek száma stb.) típusú csomópont tulajdonságait
* A fürt megbízhatóságra és tartósságra vonatkozó jellemzői.

Ossza meg velünk rövid időre tekintse át az összes ezeket az elemeket.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>A fürt kell kezdődnie csomóponttípusok száma
Először akkor ki kell deríteni a fürt létrehozásakor folyamatainak használandó.  Milyen típusú alkalmazások üzembe helyezés ehhez a fürthöz szeretne? Ha nem egyértelmű, a szándékosan a fürt, akkor valószínűleg még nem adja meg a kapacitást tervezési folyamat kész.

A fürt kell kezdődnie csomóponttípusok számának meghatározásához.  Az egyes csomóponttípusok egy virtuálisgép-méretezési csoport van leképezve. Ezután mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy fel, eltérő nyitott portokkal rendelkezhet, és eltérő kapacitásmetrikái lehetnek. Ezt a döntést a csomóponttípusok számának lényegében elérhető lesz az a következőket kell figyelembe venni:

* Az alkalmazás nem rendelkezik több szolgáltatásra, és bármelyiket kell lennie a public vagy az internetre? Tipikus alkalmazások tartalmazzák egy előtér-átjáró szolgáltatás, amely egy ügyfél és egy vagy több háttérszolgáltatások kommunikáló az előtér-szolgáltatások. Így ebben az esetben befejezi a legalább két csomópont típus rendelkezik.
* A szolgáltatások (az alkalmazást alkotó) rendelkeznek a különböző infrastruktúrához például nagyobb RAM vagy nagyobb CPU-ciklusok? Például tételezzük fel, hogy az alkalmazást, amely számára telepíteni kívánja az előtér-szolgáltatás és a háttér-szolgáltatás tartalmazza-e. Az előtér-szolgáltatás is futtathatók kisebb virtuális gépek (VM-méretek D2 beállításaihoz hasonlóan), amely a portokat nyissa meg az internethez.  A háttér-szolgáltatás azonban számítási intenzív és futtatni a nagyobb virtuális gépek (VM-méretek D4, D6, D15 hasonlóan), amelyek nincsenek internet felé néző.
  
  Ebben a példában Bár a szolgáltatások elhelyezése több csomóponttípus, dönthet úgy is javasoljuk, hogy Ön helyezze el őket egy fürtben két csomópont típussal.  Ez lehetővé teszi az egyes csomóponttípusok, hogy a különböző tulajdonságai például internetkapcsolat vagy a Virtuálisgép-méretet. Egymástól függetlenül, illetve a virtuális gépek számát is méretezhető.  
* A jövőben nem becsülhető, mivel tud tények-nal, és adja meg, hogy az alkalmazások kell kezdődnie csomóponttípusok száma. Mindig hozzáadása vagy eltávolítása a csomóponttípusok később. A Service Fabric-fürt legalább egy csomópont típusúnak kell lennie.

## <a name="the-properties-of-each-node-type"></a>Minden csomópont-típus tulajdonságait
A **csomóponttípus** a felhőalapú szolgáltatások szerepköröknek felelnek látható. Csomóponttípusok a Virtuálisgép-méretek, a virtuális gépek számát és azok tulajdonságait határozza meg. Minden csomópont-típus, a Service Fabric-fürt definiált egy külön virtuálisgép-méretezési csoport lett beállítva. Virtuálisgép-méretezési csoport egy központi telepítését és felügyeletét a virtuális gépek gyűjteménye készletként használt Azure számítási erőforrás. Az egyes csomóponttípusok, egy különálló méretezési beállítása és akár is méretezhető vagy le egymástól függetlenül, a portok megnyitása más-más részhalmazához, és különböző teljesítmény-mérőszámait rendelkezik.

Olvasási [Ez a dokumentum](service-fabric-cluster-nodetypes.md) a kapcsolatot a virtuálisgép-méretezési csoportok csomópont típusú olvashat, hogyan távoli asztali eléréséhez egy példányt, új portok megnyitása stb.

A fürt több csomóponttípus is rendelkezik, de az elsődleges csomóponttípusok (az első egy Ön által a portálon) kell rendelkeznie legalább öt virtuális gépeken használatos a termelési számítási feladatokhoz fürtök (vagy a tesztfürtökön legalább három virtuális gépek). Ha a fürt egy Resource Manager-sablon használatával hoz létre, majd keresse meg **elsődleges** attribútum alatt a csomópont-definícióban. Az elsődleges csomóponttípusok a csomópont típusa, ahol a Service Fabric rendszerszolgáltatások kerülnek.  

### <a name="primary-node-type"></a>Elsődleges csomóponttípusok
A több csomópont fürthöz válasszon egyet az elsődleges is kell. Az alábbiakban egy elsődleges csomóponttípusok jellemzői:

* A **virtuális gépek minimális mérete** az elsődleges csomópont típusa határozza meg a **tartóssági szint** választja. A tartóssági szint alapértelmezés szerint bronz. Görgessen le a tartóssági szint van, és az értékeket is igénybe vehet.  
* A **virtuális gépek minimális száma** az elsődleges csomópont típusa határozza meg a **megbízhatósági szint** választja. A megbízhatósági szint alapértelmezés szerint ezüst. Görgessen le a megbízhatósági szint van, és az értékeket is igénybe vehet. 


* A Service Fabric rendszer szolgáltatások (például a kezelő szolgáltatás vagy Image Store szolgáltatás) kerülnek, az elsődleges csomóponttípusok, és így a megbízhatóság és a fürt tartóssági határozza meg a megbízhatósági szint érték és a tartóssági szint értéket az elsődleges csomóponttípusok választja.

![Képernyőfelvétel egy fürt, amely két csomópont tartozik ][SystemServices]

### <a name="non-primary-node-type"></a>A csomóponttípus nem elsődleges
A több csomópont fürt esetén egy elsődleges csomóponttípusok van, és a többi nem elsődleges. Az alábbiakban egy nem elsődleges csomóponttípus jellemzői:

* A csomóponttípus virtuális gépek minimális méretét a tartóssági szint úgy dönt, határozza meg. A tartóssági szint alapértelmezés szerint bronz. Görgessen le a tartóssági szint van, és az értékeket is igénybe vehet.  
* A virtuális gépek minimális számát a csomópont típus egyike lehet. Azonban ez a szám, ez a csomóponttípus futtatni kívánt alkalmazás/szolgáltatás replikák száma alapján kell kiválasztani. A virtuális gépek számát csomóponttípus növelhető a fürt telepítése után.

## <a name="the-durability-characteristics-of-the-cluster"></a>A fürt a tartósság jellemzők
A tartóssági szint megjelenítésével jelzi a rendszer a virtuális gépeket, hogy az alapul szolgáló Azure-infrastruktúra jogosultságok szolgál. Az elsődleges csomóponttípusok Ez a jogosultság lehetővé teszi a Service Fabric VM szintű infrastruktúra kérelmet (például egy virtuális gép újraindítása, a VM-lemezkép-visszaállítási vagy a virtuális gép áttelepítése), amely hatással lehet a kvórum követelményei a rendszerszolgáltatások és az állapotalapú szolgáltatások felfüggesztését. Nem elsődleges csomóponttípusok Ez a jogosultság lehetővé teszi a virtuális gép szintű infrastruktúra kéréseit (például a virtuális gép újraindítása, a VM-lemezkép alaphelyzetbe és a virtuális gép áttelepítése), amely hatással lehet a kvórum követelményei az állapotalapú szolgáltatások felfüggesztését a Service Fabric.

Ez a jogosultság van kifejezve, a következő értékeket:

* Arany – az infrastruktúra feladatok szüneteltethetők UD két órát időtartamára. Arany tartóssági csak teljes csomópontos VM termékváltozatok pl. L32s GS5, G5, DS15_v2, D15_v2 engedélyezhető. Általában a Virtuálisgép-méretek megtalálható-e a http://aka.ms/vmspecs , amely "Példány egyetlen ügyfélhez dedikált hardverre elkülönített" megjegyzésben szereplő van megjelölve, teljes csomópontos virtuális gép.
* Az infrastruktúra-feladatok ezüst - UD 10 perces időtartamra szüneteltethető, és elérhető összes szabványos virtuális egymagos és annál.
* Bronz - jogosultság nélküli. Ez az alapértelmezett beállítás. Csak a tartóssági szint csomópont típusú használ, amelynek futtatása _csak_ állapot nélküli munkaterheléseket. 

> [!WARNING]
> A NodeType tulajdonságok értéke bronz tartós futtató beszerzése _jogosultság nélküli_. Ez azt jelenti, hogy az állapot nélküli munkaterheléseket befolyásoló feladatok infrastruktúra nem fog leállt vagy késleltetett. Akkor lehet, hogy ilyen feladatok továbbra is hatással lehet a munkaterhelés, leállás vagy más olyan problémák. Az éles munkaterhelés rendezést, fut legalább ezüst ajánlott. Minden csomópont-típus, amely rendelkezik egy arany vagy ezüst tartósságát öt csomópontok minimális száma kell fenntartani. 
> 

Az egyes a csomóponttípusok tartóssági szint kiválasztásához kap. Egy csomópont-típus rendelkezik egy arany tartóssági szint vagy ezüst és egy másik rendelkezik bronz ugyanabban a fürtben. **Minden csomópont-típus, amely rendelkezik egy arany vagy ezüst tartósságát öt csomópontok minimális száma kell fenntartani**. 

**Ezüst vagy arany tartóssági szint használatának előnyei**
 
- Csökkenti a skálázási művelet szükséges lépések számát (Ez azt jelenti, hogy csomópont inaktiválása és eltávolítása-ServiceFabricNodeState neve automatikusan)
- Csökkenti a felhasználói által kezdeményezett helyben VM SKU módosítási művelet vagy Azure-infrastruktúra műveletek miatti adatveszteséget.
     
**Hátrányait ezüst vagy arany tartóssági szint.**
 
- Központi telepítések a virtuálisgép-méretezési csoport és az egyéb kapcsolódó Azure-erőforrások) késleltethető, is időtúllépéssel fejeződött be, vagy teljesen problémák, a fürt vagy az infrastruktúra szintjén blokkolhatja. 
- Száma [replika életciklus-események](service-fabric-reliable-services-lifecycle.md) (például elsődleges swap) miatt automatikus csomópont deactivations Azure-infrastruktúra műveletek során.
- Csomópontok nem működik az Azure platformon és szoftverfrissítéseket hardver karbantartás tevékenységek során ideig tart. Ezek a tevékenységek során letiltása vagy letiltott állapotú csomópontokat jelenhet meg. A kapacitás, a fürt ideiglenesen csökkenti, de kell befolyásolja a fürt vagy az alkalmazások rendelkezésre állását.

### <a name="recommendations-on-when-to-use-silver-or-gold-durability-levels"></a>Ezüst vagy arany tartóssági szint használatával kapcsolatos javaslatok

Ezüst vagy arany tartóssági minden csomópont típusú használ, amelynek a skála a várt állapot-nyilvántartó szolgáltatásokat (csökkentheti a Virtuálisgép-példányok száma) gyakran, és szeretné, hogy a telepítési műveleteket kell késleltetett és kapacitása csökkenthető a skála a egyszerűsítése helyett műveletek. A kibővített forgatókönyvek (hozzáadása a virtuális gépek példányok) tölt be a választott a tartóssági szint, csak méretezési a does.

### <a name="changing-durability-levels"></a>Tartóssági szint módosítása
- Ezüst vagy arany szintű tartósságot csomóponttípusok a bronz nem léptethető vissza.
- Bronz rendszerről ezüst vagy arany néhány órát is igénybe vehet.
- Ha a tartóssági szint módosítását, ügyeljen arra, hogy mind a Service Fabric bővítménykonfiguráció a virtuálisgép-méretezési készlet erőforrást, és a Service Fabric-fürt erőforrás csomópont-típus definíciójában kell frissíteni. Ezeknek az értékeknek egyezniük kell.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>A csomópont működési javaslatok adja meg, hogy adott meg ezüst vagy arany tartóssági szint.

- Tartsa a fürt és az alkalmazások megfelelő időben a tulajdonos, és győződjön meg arról, hogy válaszol-e alkalmazások az összes [replika életciklus-események szolgáltatás](service-fabric-reliable-services-lifecycle.md) (ilyen például a build replika Beragadt) időben.
- Elfogadja a biztonságosabb módszer tesz virtuális gép Termékváltozat (méretezési fel/le): a virtuális gép Termékváltozat egy virtuálisgép-méretezési csoport módosítása során eredendően nem biztonságos, és ezért el kell kerülni Ha lehetséges. Itt az a folyamat követésével gyakori problémák elkerülése érdekében.
    - **A nem elsődleges csomóponttípusok:** javasoljuk, hogy hozzon létre új virtuálisgép-méretezési csoport, a szolgáltatás elhelyezési korlátozás tartalmaz a új virtuális gép méretezési készlet/csomópont-típust, és csökkentse a régi virtuálisgép-méretezési készlet példány módosítása száma 0-ra, (Ez a győződjön meg arról, hogy a csomópont eltávolítása nincs hatással a fürt megbízhatóságát) egyszerre csak egy csomópont.
    - **Az elsődleges csomóponttípus:** azt javasoljuk, nem módosíthatja, hogy az elsődleges csomóponttípusok VM Termékváltozata. Az elsődleges csomóponttípusok SKU nem támogatott módosítása. Ha az új SKU oka kapacitás, ajánlott több példány hozzáadása. Ha a nem lehetséges, hozzon létre egy új fürtöt és [alkalmazásállapot visszaállítása](service-fabric-reliable-services-backup-restore.md) (ha van ilyen) a régi fürtről. Nem kell minden rendszer szolgáltatás állapotának visszaállítására, akkor jönnek létre újból az új fürt számára az alkalmazások központi telepítésekor. Ha csak állapot nélküli alkalmazásokban a fürtben futó, akkor kell a alkalmazásokat az új fürtre, a rendszer nincs semmi visszaállítása. Ha úgy dönt, nyissa meg a nem támogatott útvonal és a virtuális gép SKU módosítani kívánja, majd végezni a módosításokat a virtuálisgép-méretezési Model definition tükrözzék az új SKU állítsa be. Ha a fürt csak egy csomópont fájltípusú, majd győződjön meg arról, hogy válaszol-e az állapotalapú alkalmazások az összes [replika életciklus-események szolgáltatás](service-fabric-reliable-services-lifecycle.md) (ilyen például a build replika Beragadt) egy időben, valamint a szolgáltatás a replika újbóli létrehozása időtartam értéke kisebb, mint öt percet (ezüst tartóssági szint). 

    > [!WARNING]
    > Virtuálisgép-méretezési készlet nem ajánlott legalább ezüst tartóssági nem fut a virtuális gép Termékváltozat-méretét módosítása. Virtuális gép SKU méretének módosítása egy olyan adatok felülíró helyszíni infrastruktúra művelet. Késleltetés vagy a figyelheti a módosítás legalább néhány képessége nélkül is lehetséges, hogy a művelet okozhat az állapotalapú szolgáltatások, vagy más váratlan működési problémákkal rendelkeznek, még a állapot nélküli munkaterheléseket vezethet. 
    > 
    
- Bármely arany szintű tartósságot virtuálisgép-méretezési csoport öt csomópontok minimális száma karbantartása vagy ezüst engedélyezve
- Nem véletlenszerű Virtuálisgép-példányok törléséhez mindig használja virtuálisgép-méretezési készlet méretezési szolgáltatás leállt. A törlés véletlenszerű Virtuálisgép-példányok magában hordozza a egyensúlyhiány UD és FD elosztva Virtuálisgép-példány létrehozása. Ez egyensúlyhiány ronthatja megfelelően terheléselosztásához többek között a szolgáltatás példányok/szolgáltatás replikák rendszerek lehetőséget.
- Ha használja az automatikus skálázás, majd a szabályok beállítása úgy, hogy a skála (Virtuálisgép-példányok eltávolítása) egyszerre csak egy csomópont végzett. Skálázás le több példány egyszerre is nem biztonságos.
- Ha skálázás le egy elsődleges csomóponttípushoz, meg kell soha nem csökkentheti azt több, mint a megbízhatósági szint lehetővé teszi, hogy.

## <a name="the-reliability-characteristics-of-the-cluster"></a>A fürt megbízhatóság jellemzői
A megbízhatósági szint beállítása a replikákat a rendszer szolgáltatások, az elsődleges csomóponttípusok kell ezen a fürtön futtatni kívánt szolgál. A további a replikák száma, a megbízhatóbb a rendszerszolgáltatások vannak, a fürtön.  

A megbízhatósági szint a következő értékeket veheti:

* Platinum - futtassa a rendszerszolgáltatások cél replika kilenc számának beállítása
* Arany - futtassa a rendszerszolgáltatások cél replika beállítva a hét száma
* Ezüst - futtassa a rendszerszolgáltatások cél replika öt számának beállítása 
* Bronz - futtassa a rendszerszolgáltatások cél replika három számának beállítása

> [!NOTE]
> A megbízhatósági szint úgy dönt, rendelkeznie kell az elsődleges csomóponttípushoz csomópontok minimális számát határozza meg. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>A megbízhatósági szint javaslatok

Vagy a fürt (a Virtuálisgép-példány összes csomóponttípusok összege) méretének csökkentése, frissítenie kell a fürt másik egyrétegű megbízhatóságát. Ezzel elindítja a fürt frissítési módosítása a rendszer szolgáltatások replika beállítása szükséges száma. Várjon, amíg a frissítés befejezéséhez, mielőtt bármilyen más jellegű módosítását a fürthöz, például a csomópontok hozzáadása folyamatban.  Ön nyomon követheti a frissítési Service Fabric Explorer vagy való futtatásával [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Ez a javaslat a megbízhatósági szint választásával.

| **Fürt mérete** | **Megbízhatósági szint** |
| --- | --- |
| 1 |Adja meg a megbízhatósági szint paramétert, a rendszer kiszámítja az |
| 3 |Bronz |
| 5 vagy 6|Silver |
| 7 vagy 8 |Arany |
| 9 vagy afölötti |Platinum |

## <a name="primary-node-type---capacity-guidance"></a>Típusú elsődleges csomópont - kapacitás útmutató

Ez az útmutató az elsődleges csomópont típus kapacitás:

- **Az Azure-ban bármelyik üzemi feladat futtatásához a Virtuálisgép-példányok száma:** meg kell adnia az elsődleges csomópont típus méretének legalább 5. 
- **A tesztelési feladatokat futtató Azure Virtuálisgép-példányok száma** megadhat egy minimális elsődleges csomópont méretének 1-es vagy 3. A több csomópontot tartalmazó fürtben, fut, különleges konfigurációs, ezért, méretezési kívül, hogy a fürt nem támogatott. A több csomópontot tartalmazó fürtben, vannak nincs megbízhatóság és így a Resource Manager sablon, el kell eltávolítás/nem adhatja meg, hogy a konfigurálás (nem a konfigurációs érték nincs elegendő). Ha beállította a portálon beállítani egy csomópontot tartalmazó fürtben, majd a konfigurációs rendszer automatikusan hozott kezeli. Egy és három csomópontos fürt nem támogatottak a termelési számítási feladatokhoz futtatásához. 
- **Virtuális gép Termékváltozat:** elsődleges csomópont típus a rendszerszolgáltatások hol fut, így a virtuális gép Termékváltozat választja, a teljes csúcs figyelembe veszik töltődhet, tervezze meg a fürt helyezésre. Bemutatják a I jelentését ide-gondolja, hogy az elsődleges csomópont típusú, a "tüdő", akkor a agy oxigén tartalma hasonló itt, és így a agy nem kap elég oxigén, ha a szervezet romlik. 

A kapacitási igényeihez a fürt szeretné futtatni a fürt munkaterhelés határozza meg, mert a Microsoft nem biztosítja az adott munkaterhelés, azonban ez a széles körű útmutatót segítséget nyújtanak a minőségi útmutatással megkezdése

A termelési számítási feladatokhoz: 

- Az ajánlott méretű termékváltozata Standard D3, a Standard D3_V2 vagy a megfelelő, egy legalább 14 GB helyi SSD-Meghajtóra.
- A minimális támogatott VM SKU használata Standard D1, a Standard D1_V2 vagy a megfelelő, egy legalább 14 GB helyi SSD-Meghajtóra. 
- Részleges core például normál A0 méretű SKU nem támogatottak a termelési számítási feladatokhoz.
- Standard A1 Termékváltozat nem támogatott a teljesítményre vonatkozó megfontolásból a termelési számítási feladatokhoz.

> [!WARNING]
> Az elsődleges csomóponton futó fürt VM SKU méretének módosítása jelenleg nem támogatott. Ezért körültekintően az elsődleges csomóponttípusok VM Termékváltozat, a kapacitás jövőbeli igények figyelembe véve. Jelenleg az egyetlen támogatott mód helyezze át az elsődleges csomóponttípushoz (kisebb vagy nagyobb) egy új virtuális gép másikra a megfelelő kapacitással rendelkező új fürt létrehozása az alkalmazások és az alkalmazás állapotát majd visszaállítja (ha van ilyen) telepítése a a [ biztonsági mentések legújabb szolgáltatás](service-fabric-reliable-services-backup-restore.md) elvégezte a régi fürtről. Nem kell minden rendszer szolgáltatás állapotának visszaállítására, akkor jönnek létre újból, amikor központilag alkalmazásokat az új fürtre. Ha csak állapot nélküli alkalmazásokban a fürtben futó, akkor kell a alkalmazásokat az új fürtre, a rendszer nincs semmi visszaállítása.
> 

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>A nem elsődleges csomóponttípus - kapacitás útmutató állapotalapú alkalmazások és szolgáltatások számára

Ez az útmutató az állapotalapú alkalmazások és szolgáltatások használatával a Service fabric rendszer [megbízható gyűjtemények vagy reliable Actors](service-fabric-choose-framework.md) , amely a következő típusban nem elsődleges csomópontot futtat.

**Virtuálisgép-példányok száma:** a termelési számítási feladatokhoz, amelyek állapotalapú, ajánlott a minimális és a cél replika számával, 5 futtassa. Ez azt jelenti, hogy stabil állapotban, végül replikát (a replikakészlethez) minden tartalék tartomány és a frissítési tartomány. A teljes megbízhatósági szint az elsődleges csomóponttípus fogalma annak meghatározására, hogy ezt a beállítást a rendszerszolgáltatások. Így az azonos szempont az állapotalapú szolgáltatások vonatkozik.

Ezért a termelési számítási feladatokhoz, minimális ajánlott nem elsődleges csomópontot típus mérete 5, ha állapotalapú alkalmazások és szolgáltatások futtatja azt.

**Virtuális gép Termékváltozat:** Ez a csomópont típusa ahol az alkalmazás szolgáltatások futnak, így a virtuális gép SKU úgy dönt, figyelembe kell vennie a csúcsterhelés azt tervezi, hogy minden csomópont helyezzen el. A nodetype kapacitás igényeinek szeretné futtatni a fürt, ezért jelenleg nem adhatók meg az adott munkaterhelés, azonban ez a széles körű útmutatót segítséget nyújtanak a minőségi útmutatással megkezdésében munkaterhelés határozza meg

A termelési számítási feladatokhoz 

- Az ajánlott méretű termékváltozata Standard D3, a Standard D3_V2 vagy a megfelelő, egy legalább 14 GB helyi SSD-Meghajtóra.
- A minimális támogatott VM SKU használata Standard D1, a Standard D1_V2 vagy a megfelelő, egy legalább 14 GB helyi SSD-Meghajtóra. 
- Részleges core például normál A0 méretű SKU nem támogatottak a termelési számítási feladatokhoz.
- Standard A1 Termékváltozat termelési számítási feladatokhoz a teljesítményre vonatkozó megfontolásból kifejezetten nem támogatott.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>A nem elsődleges csomóponttípus - állapot nélküli munkaterheléseket kapacitás útmutatást

Ez az útmutató az állapot nélküli Munkaterheléseket, amelyek a nem elsődleges nodetype futtatja.

**Virtuálisgép-példányok száma:** a termelési számítási feladatokhoz, amelyek állapot nélküli alkalmazások, a minimális támogatott nem elsődleges csomópontot típus mérete 2. Ez lehetővé teszi, hogy futtatni, akkor az alkalmazás- és a szolgáltatás engedélyezi a Virtuálisgép-példány megszűnését túlélést két állapotmentes példányait. 

**Virtuális gép Termékváltozat:** Ez a csomópont típusa ahol az alkalmazás szolgáltatások futnak, így a virtuális gép SKU úgy dönt, figyelembe kell vennie a csúcsterhelés azt tervezi, hogy minden csomópont helyezzen el. A nodetype kapacitás igényeinek szeretné futtatni a fürt, ezért jelenleg nem adhatók meg az adott munkaterhelés, azonban ez a széles körű útmutatót segítséget nyújtanak a minőségi útmutatással megkezdésében munkaterhelés határozza meg

A termelési számítási feladatokhoz 

- Az ajánlott méretű termékváltozata Standard D3 vagy Standard D3_V2 vagy ezzel egyenértékű csoport. 
- A minimális támogatott VM SKU használata Standard D1 vagy Standard D1_V2 vagy ezzel egyenértékű csoport. 
- Részleges core például normál A0 méretű SKU nem támogatottak a termelési számítási feladatokhoz.
- Standard A1 Termékváltozat nem támogatott a teljesítményre vonatkozó megfontolásból a termelési számítási feladatokhoz.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>További lépések
Amennyiben a kapacitástervezés Befejezés, és állítson be egy fürtbe, olvassa el az alábbiakat:

* [A Service Fabric-fürt biztonsági](service-fabric-cluster-security.md)
* [A Service Fabric-fürt méretezése](service-fabric-cluster-scaling.md)
* [Katasztrófa utáni helyreállítás tervezése](service-fabric-disaster-recovery.md)
* [A NodeType tulajdonságok értéke a virtuálisgép-méretezési a kapcsolat beállítása](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
