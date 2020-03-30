---
title: Az Azure Service Fabric vész-helyreállítási
description: Az Azure Service Fabric katasztrófák kezelésére is kínál lehetőségeket. Ez a cikk ismerteti a katasztrófák típusait, és hogyan kell kezelni őket.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b29985d40ae3a1bf582099e998e000fed83460f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371647"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Vészhelyreállítás az Azure Service Fabricben
A magas rendelkezésre állás biztosításának kritikus része annak biztosítása, hogy a szolgáltatások túléljék a különböző típusú hibákat. Ez különösen fontos a nem tervezett és az ön által nem szabályos hibák esetén. 

Ez a cikk néhány gyakori hibamódot ismertet, amelyek katasztrófák lehetnek, ha nem megfelelően modellezve és megfelelően nem megfelelően kezelik. Azt is tárgyalja mérséklések és intézkedéseket kell tenni, ha a katasztrófa történik egyébként. A cél az állásidő vagy adatvesztés kockázatának korlátozása vagy kiküszöbölése, ha a tervezett vagy más típusú hibák bekövetkeznek.

## <a name="avoiding-disaster"></a>A katasztrófa elkerülése
Az Azure Service Fabric fő célja, hogy segítsen a környezet és a szolgáltatások modellezésében oly módon, hogy a gyakori hibatípusok nem katasztrófák. 

Általában kétféle katasztrófa/hiba forgatókönyv létezik:
- Hardver- és szoftverhibák
- Működési hibák

### <a name="hardware-and-software-faults"></a>Hardver- és szoftverhibák
A hardver- és szoftverhibák kiszámíthatatlanok. A hibák túlélésének legegyszerűbb módja a szolgáltatás több példányának futtatása a hardver- vagy szoftverhiba-határokon keresztül. 

Ha például a szolgáltatás csak egy gépen fut, az adott gép hibája katasztrófa az adott szolgáltatás számára. A katasztrófa elkerülésének egyszerű módja annak biztosítása, hogy a szolgáltatás több gépen fusson. Tesztelésre is szükség van annak érdekében, hogy egy gép meghibásodása ne zavarja meg a futó szolgáltatást. A kapacitástervezés biztosítja, hogy egy helyettesítő példány máshol is létrehozható, és hogy a kapacitáscsökkentés ne terhelje túl a fennmaradó szolgáltatásokat. 

Ugyanez a minta működik, függetlenül attól, hogy mit próbál elkerülni a hiba. Ha például aggódik egy san-i meghibásodás a hiba miatt, több san-on keresztül fut. Ha aggódik a kiszolgálók elvesztése miatt, több állványon fut. Ha aggódik az adatközpontok elvesztése miatt, a szolgáltatásnak több Azure-régióban, több Azure-rendelkezésre állási zónában vagy a saját adatközpontokban kell futnia. 

Ha egy szolgáltatás több fizikai példányon (gépeken, állványokon, adatközpontokban, régiókban) átnyúló, bizonyos típusú egyidejű hibák továbbra is fennállnak. De egy adott típusú egyszeri és akár több hiba (például egyetlen virtuális gép vagy hálózati kapcsolat sikertelen) automatikusan kezeli, és így már nem "katasztrófa". 

A Service Fabric mechanizmusokat biztosít a fürt bővítéséhez, és kezeli a sikertelen csomópontok és szolgáltatások visszahozása. A Service Fabric lehetővé teszi a szolgáltatások számos példányának futtatását, hogy megakadályozza a nem tervezett hibák valódi katasztrófákká való befordulását.

Lehet, hogy miért nem valósítható meg a hibák nak ahhoz elegendő méretű központi telepítés futtatása. Előfordulhat például, hogy több hardvererőforrást igényel, mint amennyit a hiba esélyéhez képest hajlandó fizetni. Elosztott alkalmazásokkal kapcsolatban további kommunikációs ugrások vagy állami replikációs költségek földrajzi távolságok között elfogadhatatlan késést okozhatnak. Ha ezt a vonalat meghúzzák, az minden alkalmazás esetében eltérő. 

Szoftverhibák esetén a hiba lehet a kívánt szolgáltatás méretezése. Ebben az esetben több példány nem akadályozza meg a katasztrófát, mert a hiba feltételkorfüggő az összes példányban.

### <a name="operational-faults"></a>Működési hibák
Még akkor is, ha a szolgáltatás spaned szerte a világon sok redundancia, akkor is megtapasztalhatja katasztrofális eseményeket. Előfordulhat például, hogy valaki véletlenül újrakonfigurálja a szolgáltatás DNS-nevét, vagy véglegesen törli azt. 

Tegyük fel például, hogy volt egy állapotalapú Service Fabric-szolgáltatása, és valaki véletlenül törölte azt a szolgáltatást. Hacsak nincs valami más mérséklés, a szolgálat és az összes állam, hogy már elment. Az ilyen típusú működési katasztrófák ("hoppá") különböző megoldásokat és lépéseket igényelnek a helyreállításhoz, mint a rendszeres nem tervezett hibák. 

Az ilyen típusú működési hibák elkerülésének legjobb módjai a következők:
- Korlátozza a környezethez való operatív hozzáférést.
- Szigorúan ellenőrizze a veszélyes műveleteket.
- Automatizálás kényszerítése, manuális vagy sávon kívüli módosítások megelőzése, valamint adott módosítások ellenőrzése a környezettel szemben, mielőtt életbe léptetné őket.
- Győződjön meg arról, hogy a destruktív műveletek "puha". A lágy műveletek nem lépnek életbe azonnal, vagy egy időablakon belül visszaállíthatók.

A Service Fabric mechanizmusokat biztosít a működési hibák megelőzésére, például [szerepköralapú hozzáférés-vezérlést](service-fabric-cluster-security-roles.md) biztosít a fürtműveletekhez. Azonban a legtöbb ilyen működési hibák igényel szervezeti erőfeszítéseket és más rendszerek. A Service Fabric biztosítja a működési hibák túlélésének mechanizmusait, különösen [az állapotalapú szolgáltatások biztonsági mentését és visszaállítását.](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

## <a name="managing-failures"></a>Hibák kezelése
A Service Fabric célja a hibák automatikus kezelése. Bizonyos típusú hibák kezeléséhez azonban a szolgáltatásoknak további kóddal kell rendelkezniük. A más típusú hibákat biztonsági és üzletmenet-folytonossági okokból _nem_ szabad automatikusan kezelni. 

### <a name="handling-single-failures"></a>Egyszeri hibák kezelése
Az egyes gépek sokféle okból meghibásodhatnak. Néha hardveres okok, például tápegységek és hálózati hardverhibák. Egyéb hibák vannak a szoftver. Ezek közé tartoznak az operációs rendszer és maga a szolgáltatás hibái. A Service Fabric automatikusan észleli az ilyen típusú hibákat, beleértve azokat az eseteket is, amikor a gép hálózati problémák miatt más gépektől ellesz szigetelve.

A szolgáltatás típusától függetlenül egyetlen példány futtatása leállást eredményez az adott szolgáltatás számára, ha a kód egyetlen példánya bármilyen okból meghibásodik. 

Egyetlen hiba kezeléséhez a legegyszerűbb dolog, amit tehetünk, hogy a szolgáltatások futnak több csomópont alapértelmezés szerint. Állapot nélküli szolgáltatások esetén `InstanceCount` győződjön meg arról, hogy ez nagyobb, mint 1. Az állapotalapú szolgáltatások esetében a `TargetReplicaSetSize` `MinReplicaSetSize` minimális ajánlás az, hogy mindkettő 3-ra van állítva. A szolgáltatáskód több példányának futtatása biztosítja, hogy a szolgáltatás automatikusan kezelni tudja az egyes hibákat. 

### <a name="handling-coordinated-failures"></a>Koordinált hibák kezelése
A fürt koordinált hibái lehetnek tervezett vagy nem tervezett infrastruktúra-hibák és -módosítások, illetve tervezett szoftvermódosítások. A Service Fabric olyan infrastruktúrazónákat modellez, amelyek koordinált hibákat *tapasztalnak tartalék tartományként.* Az összehangolt szoftvermódosításokat tapasztaló területek *frissítési tartományként*vannak modellezve. A tartalék tartományokról, a frissítési tartományokról és a fürttopológiaszolgáltatásról a [Service Fabric-fürt leírása a Fürterőforrás-kezelő használatával című](service-fabric-cluster-resource-manager-cluster-description.md)témakörben talál további információt.

Alapértelmezés szerint a Service Fabric figyelembe veszi a hiba és a frissítési tartományok tervezésekor, ahol a szolgáltatások futtatásához. Alapértelmezés szerint a Service Fabric megpróbálja biztosítani, hogy a szolgáltatások több tartalék és frissítési tartományokon keresztül futnak, így ha tervezett vagy nem tervezett módosítások történnek, a szolgáltatások továbbra is elérhetők maradnak. 

Tegyük fel például, hogy egy áramforrás meghibásodása miatt az állványon lévő összes gép egyszerre meghibásodik. A szolgáltatás több példányban futó, a veszteség sok gép a tartalék tartomány meghibásodása válik csak egy újabb példa egy szolgáltatás egyetlen hiba. Ez az oka annak, hogy a hiba- és frissítési tartományok kezelése kritikus fontosságú a szolgáltatások magas rendelkezésre állásának biztosításához. 

Amikor a Service Fabric az Azure-ban fut, a tartalék tartományok és a frissítési tartományok kezelése automatikusan történik. Más környezetekben előfordulhat, hogy nem. Ha saját fürtöket hoz fel a helyszínen, győződjön meg arról, hogy a tartalék tartomány elrendezése megfelelően van leképezve és tervezve.

A frissítési tartományok olyan modellezési területek esetében hasznosak, ahol a szoftver frissítése egyszerre lesz. Emiatt a frissítési tartományok gyakran meghatározzák azokat a határokat is, ahol a szoftvereket a tervezett frissítések során leveszik. A Service Fabric és a szolgáltatások frissítései ugyanazt a modellt követik. A működés közbeni frissítésekkel, a frissítési tartományokkal és a Service Fabric állapotmodelljével kapcsolatos további információkért lásd:

 - [Alkalmazás frissítése](service-fabric-application-upgrade.md)
 - [Alkalmazásfrissítési oktatóanyag](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric egészségügyi modell](service-fabric-health-introduction.md)

A fürt elrendezését a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)ben biztosított fürttérkép segítségével jelenítheti meg:

<center>

![A Service Fabric-kezelőben a tartalék tartományok között elosztott csomópontok][sfx-cluster-map]
</center>

> [!NOTE]
> A hiba, a működés közbeni frissítés, a szolgáltatáskód és az állapot számos példányának futtatása, az elhelyezési szabályok, amelyek biztosítják, hogy a szolgáltatások hiba- és frissítési tartományokon keresztül fussanak, és a beépített állapotfigyelés csak *néhány* a Service Fabric által biztosított funkciók közül, amelyek megakadályozzák a normál működési problémák és hibák katasztrófákká való befordulását. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Egyidejű hardver- vagy szoftverhibák kezelése
Szingli kudarcokról beszéltünk. Mint látható, ők könnyen kezelhető mind állapotnélküli és állapotalapú szolgáltatások csak azáltal, hogy több példányban a kód (és állapot) futó hiba és a frissítési tartományok. 

Több egyidejű véletlenszerű hibák is előfordulhatnak. Ezek nagyobb valószínűséggel vezetnek leálláshoz vagy tényleges katasztrófához.


#### <a name="stateless-services"></a>Állapotnélküli szolgáltatások
Egy állapotmentes szolgáltatás példányszáma azt jelzi, hogy hány példánynak kell futnia. Ha a példányok bármelyike (vagy az összes) sikertelen, a Service Fabric válaszol a cserepéldányok automatikus létrehozásával más csomópontokon. A Service Fabric továbbra is hozza létre a csere, amíg a szolgáltatás visszatér a kívánt példányszám.

Tegyük fel például, hogy `InstanceCount` az állapotmentes szolgáltatás értéke -1. Ez az érték azt jelenti, hogy egy példánynak kell futnia a fürt minden csomópontján. Ha néhány ilyen példány sikertelen, a Service Fabric észleli, hogy a szolgáltatás nem a kívánt állapotban van, és megpróbálja létrehozni a példányokat a csomópontokon, ahol hiányoznak.

#### <a name="stateful-services"></a>Államos szolgáltatások
Az állapotalapú szolgáltatásoknak két típusa van:
- Állapottalan a kitartó állapottal.
- Államos, nem tartós állapotban. (Az állapot a memóriában van tárolva.)

Az állapotalapú szolgáltatás meghibásodása utáni helyreállítás az állapotalapú szolgáltatás típusától, a szolgáltatás replikáinak és a sikertelen replikák tól függ.

Egy állapotalapú szolgáltatás ban a bejövő adatok replikálódnak a replikák (az elsődleges és az aktív másodlagos replikák között). Ha a kópiák többsége megkapja az adatokat, az adatok *kvórum véglegesítése* minősül. (Öt másolat esetén három határozatképesség lesz.) Ez azt jelenti, hogy bármikor, nem lesz legalább egy kvóruma replikák a legújabb adatokat. Ha a replikák sikertelenek (mondjuk ötből kettő), a kvórumérték segítségével kiszámíthatjuk, hogy helyre tudjuk-e állítani. (Mivel az öt replikából a fennmaradó három még mindig működik, garantált, hogy legalább egy replika teljes adatokkal fog rendelkezni.)

Ha a kópiák kvóruma meghibásodik, a partíció *kvórumveszteség-állapotban* van. Tegyük fel, hogy egy partíció öt replikával rendelkezik, ami azt jelenti, hogy legalább három garantáltan teljes adatokkal rendelkezik. Ha a kópiák kvóruma (három az ötből) sikertelen, a Service Fabric nem tudja megállapítani, hogy a fennmaradó replikák (kettő ötből) elegendő adattal rendelkezik-e a partíció visszaállításához. Azokban az esetekben, ahol a Service Fabric észleli a kvórum veszteség, az alapértelmezett viselkedés megakadályozza a partícióra további írások, kvórum elvesztése, és várja meg a kvóruma replikák visszaállítása.

Annak meghatározása, hogy egy állapotalapú szolgáltatás esetében katasztrófa történt-e, majd annak kezelése három szakaszból áll:

1. Annak megállapítása, hogy történt-e kvórumveszteség vagy sem.
   
   A kvórumveszteség akkor deklarálva van, ha egy állapotalapú szolgáltatás replikáinak többsége egyszerre nem működik.
2. Annak megállapítása, hogy a kvórum vesztesége állandó-e vagy sem.
   
   Az idő nagy részében a kudarcok átmenetiek. A folyamatok újraindulnak, a csomópontok újraindulnak, a virtuális gépek újraindulnak, és a hálózati partíciók gyógyulnak. Néha azonban a kudarcok állandóak. Az, hogy a hibák állandóak-e vagy sem, attól függ, hogy az állapotalapú szolgáltatás megmarad-e az állapot, vagy csak a memóriában tartja: 
   
   - A megőrzött állapot nélküli szolgáltatások esetén a kvórum vagy a replikák meghibásodása _azonnal_ végleges kvórumvesztést eredményez. Ha a Service Fabric észleli a kvórum elvesztését egy állapotalapú, nem állandó szolgáltatásban, azonnal folytatja a 3. Az adatvesztés folytatása van értelme, mert a Service Fabric tudja, hogy nincs értelme a replikák, hogy jöjjön vissza. Még akkor is, ha helyreállnak, az adatok elvesznek a szolgáltatás nem tartós jellege miatt.
   - Állapotalapú állandó szolgáltatások esetén a kvórum vagy a replikák meghibásodása miatt a Service Fabric megvárja, amíg a replikák visszatérnek, és visszaállítják a kvórumot. Ez szolgáltatáskimaradást eredményez a szolgáltatás érintett partíciójára (vagy "replikakészletére") írt _írások_ esetén. Az olvasások azonban csökkentett konzisztenciagaranciákkal továbbra is lehetségesek lehetnek. Az alapértelmezett idő, amíg a Service Fabric vár a kvórum visszaállítása *végtelen,* mert a folytatás egy (potenciális) adatvesztési esemény, és egyéb kockázatokat hordoz. Ez azt jelenti, hogy a Service Fabric nem folytatja a következő lépést, kivéve, ha a rendszergazda lépéseket tesz az adatvesztés deklarációjára.
3. Az adatok elveszésének megállapítása, valamint visszaállítás a biztonsági mentésekből.

   Ha kvórumveszteség deklarálva lett deklarálva (automatikusan vagy felügyeleti művelet révén), a Service Fabric és a szolgáltatások továbblépnek annak meghatározására, hogy az adatok ténylegesen elvesztek-e. Ezen a ponton a Service Fabric is tudja, hogy a többi replikák nem jön vissza. Ez volt az a döntés, amikor már nem vártuk, hogy a kvórum elvesztése magától megoldódjon. A szolgáltatás számára a legjobb megoldás általában a befagyasztás és a konkrét adminisztratív beavatkozás rakoncátora.
   
   Amikor a Service `OnDataLossAsync` Fabric meghívja a _metódust,_ mindig a feltételezett adatvesztés miatt. A Service Fabric biztosítja, hogy ez a hívás a _legjobb_ fennmaradó replika leszállítható. Ez az a kópia, amely a legnagyobb előrehaladást ért el. 
   
   Azért mondjuk mindig _a feltételezett adatvesztést,_ mert lehetséges, hogy a fennmaradó replika állapota megegyezik az elsődleges állapottal, amikor a kvórum elveszett. Azonban anélkül, hogy az állam összehasonlítani, nincs jó módja a Service Fabric vagy az üzemeltetők, hogy biztosan tudja.     
   
   Tehát mit csinál a `OnDataLossAsync` módszer tipikus megvalósítása?
   1. A megvalósítási `OnDataLossAsync` naplók, amelyek aktiválták, és elindítja a szükséges felügyeleti riasztásokat.
   1. A megvalósítás általában szünetel, és megvárja a további döntéseket és a manuális intézkedéseket. Ennek az az oka, hogy még akkor is, ha a biztonsági mentések rendelkezésre állnak, előfordulhat, hogy elő kell készíteni őket. 
   
      Ha például két különböző szolgáltatás koordinálja az adatokat, előfordulhat, hogy ezeket a biztonsági másolatokat módosítani kell annak érdekében, hogy a visszaállítás után a két szolgáltatás által fontos információk konzisztensek legyenek. 
   1. Gyakran van néhány más telemetriai adatok vagy kipufogó a szolgáltatásból. Ezek a metaadatok más szolgáltatásokban vagy naplókban is szerepelhetnek. Ez az információ szükség szerint használható annak megállapítására, hogy voltak-e olyan hívások, amelyek et az elsődleges helyen fogadtak és dolgoztak fel, amelyek nem voltak jelen a biztonsági mentésben, vagy replikáltak-e erre a replikára. Előfordulhat, hogy ezeket a hívásokat újra le kell játszani, vagy hozzá kell adni a biztonsági mentéshez, mielőtt a visszaállítás megvalósítható lenne.  
   1. A megvalósítás összehasonlítja a fennmaradó replika állapotát a rendelkezésre álló biztonsági mentések a tárolt állapottal. Ha a Service Fabric megbízható gyűjtemények, vannak ehhez elérhető [eszközök és folyamatok.](service-fabric-reliable-services-backup-restore.md) A cél az, hogy lássa, hogy a replika állapota elegendő-e, és hogy lássa, mi lehet a biztonsági mentés.
   1. Az összehasonlítás befejezése után, és a visszaállítás befejezése után (ha szükséges), a szolgáltatáskód vissza kell **adnia igaz,** ha bármilyen állapotmódosítások történtek. Ha a replika megállapította, hogy ez az állapot elérhető legjobb példánya, és nem módosította, a kód **hamis**értéket ad vissza. 
   
      A **true** értéke azt jelzi, hogy a _többi_ fennmaradó replikák most lehet, hogy nem egyeztethető össze ezzel. A lesznek eldobva és újraépítve ebből a replikából. A **hamis** érték azt jelzi, hogy nem történt állapotmódosítás, így a többi replikák megtarthassák, amije van. 

Rendkívül fontos, hogy a szolgáltatás szerzői gyakorolják a lehetséges adatvesztési és hibaforgatókönyveket, mielőtt a szolgáltatások éles környezetben üzembe helyezése. Az adatvesztés lehetősége elleni védelem érdekében fontos, hogy rendszeresen [biztonsági másolatot kell tartania az](service-fabric-reliable-services-backup-restore.md) állapotalapú szolgáltatások állapotáról egy georedundáns tárolóba. 

Azt is meg kell győződnie arról, hogy képes-e az állapot visszaállítására. Mivel számos különböző szolgáltatás biztonsági mentésekülönböző időpontokban készült, biztosítania kell, hogy a visszaállítás után a szolgáltatások egységes képet kapnak egymásról. 

Vegyünk például egy olyan helyzetet, amikor az egyik szolgáltatás létrehoz egy számot, és tárolja azt, majd elküldi azt egy másik szolgáltatásnak, amely szintén tárolja. A visszaállítás után előfordulhat, hogy a második szolgáltatás rendelkezik a számmal, de az első nem, mert a biztonsági mentés nem tartalmazza a műveletet.

Ha úgy találja, hogy a fennmaradó replikák nem elegendőek az adatvesztési forgatókönyv folytatásához, és nem rekonstruálhatja a szolgáltatás állapotát telemetriai adatokból vagy kipufogóból, a biztonsági mentések gyakorisága határozza meg a lehető legjobb helyreállításipont-célkitűzést (RPO). A Service Fabric számos eszközt biztosít a különböző hibaforgatókönyvek teszteléséhez, beleértve az állandó kvórumot és az adatvesztést, amely biztonsági másolatból történő helyreállítást igényel. Ezek a forgatókönyvek a Service Fabric tesztképességi eszközeinek részeként szerepelnek, a Hibaelemzési szolgáltatás által. Az eszközökkel és mintákkal kapcsolatos további információkért [lásd: Bevezetés a Hibaelemzési szolgáltatásba](service-fabric-testability-overview.md). 

> [!NOTE]
> A rendszerszolgáltatások kvórumveszteséget is szenvedhetnek. A hatás a szóban forgó szolgáltatásra jellemző. Például a névadó szolgáltatás kvórumának elvesztése hatással van a névfeloldásra, míg a Feladatátvevő kezelő szolgáltatás kvórumvesztesége blokkolja az új szolgáltatás létrehozását és feladatátvételeket. 
> 
> A Service Fabric rendszerszolgáltatások ugyanazt a mintát követik, mint a szolgáltatások állapotkezelés, de nem javasoljuk, hogy megpróbálja áthelyezni őket a kvórum elvesztése és a potenciális adatvesztés. Ehelyett azt javasoljuk, hogy [kérjen támogatást,](service-fabric-support.md) hogy megtalálja a megoldást, amely célzott a helyzet. Általában célszerű egyszerűen megvárni, amíg a lefelé replikák visszatérnek.
>

#### <a name="troubleshooting-quorum-loss"></a>Kvórumveszteség elhárítása

A replikák átmeneti hiba miatt időnként leállhatnak. Várjon egy ideig, ahogy a Service Fabric megpróbálja őket felállítani. Ha a replikák a vártnál hosszabb ideig nem voltak lecsökkentve, kövesse az alábbi hibaelhárítási műveleteket:
- Lehet, hogy a replikák összeomlanak. Ellenőrizze a replikaszintű állapotjelentéseket és az alkalmazásnaplókat. Gyűjtse összeomlás guba, és megteszi a szükséges lépéseket, hogy visszaszerezze.
- Lehet, hogy a replikafolyamat nem válaszol. Ellenőrizze az alkalmazásnaplókat, és ellenőrizze ezt. Gyűjtse össze a folyamatmemóriaképeket, majd állítsa le a nem válaszoló folyamatot. A Service Fabric létrehoz egy cserefolyamatot, és megpróbálja visszahozni a replika.
- Előfordulhat, hogy a replikákat tartalmazó csomópontok nem lesznek. Indítsa újra az alapul szolgáló virtuális gépet a csomópontok felhozásához.

Előfordulhat, hogy nem lehet helyreállítani a replikákat. Például a meghajtók meghibásodtak, vagy a gépek fizikailag nem válaszol. Ezekben az esetekben a Service Fabric kell mondani, hogy ne várjon replika helyreállítása.

*Ne* alkalmazza ezeket a módszereket, ha a szolgáltatás online állapotba hozása elfogadhatatlan. Ebben az esetben minden erőfeszítést meg kell tenni a fizikai gépek helyreállítása érdekében.

A következő műveletek adatvesztést okozhatnak. Ellenőrizd, mielőtt követed őket.
   
> [!NOTE]
> _Ez soha nem_ biztonságos használni ezeket a módszereket más, mint célzott módon ellen adott partíciókat. 
>

- Használja `Repair-ServiceFabricPartition -PartitionId` a `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` vagy API-t. Ez az API lehetővé teszi a partíció azonosítójának megadását a kvórumveszteségből való kiköltözéshez és a potenciális adatvesztésbe való áthelyezéshez.
- Ha a fürt gyakori hibákat tapasztal, amelyek miatt a szolgáltatások kvórumveszteség-állapotba kerülnek, és a lehetséges _adatvesztés elfogadható,_ a megfelelő [QuorumLossWaitDuration](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) érték megadása segíthet a szolgáltatás automatikus helyreállításában. A Service Fabric megvárja a megadott `QuorumLossWaitDuration` értéket (az alapértelmezett érték végtelen) a helyreállítás végrehajtása előtt. *Nem* javasoljuk ezt a módszert, mert váratlan adatvesztést okozhat.

## <a name="availability-of-the-service-fabric-cluster"></a>A Service Fabric-fürt elérhetősége
Általában a Service Fabric-fürt egy erősen elosztott környezetben nincs egyetlen meghibásodási pont. Egy csomópont meghibásodása nem okoz rendelkezésre állási vagy megbízhatósági problémákat a fürt számára, elsősorban azért, mert a Service Fabric rendszerszolgáltatások ugyanazokat az irányelveket követik korábban. Ez azt, hogy alapértelmezés szerint mindig három vagy több replikával futnak, és az állapot nélküli rendszerszolgáltatások minden csomóponton futnak. 

Az alapul szolgáló Service Fabric hálózati és hibaészlelési rétegek teljes mértékben elosztott. A legtöbb rendszerszolgáltatás újraépíthető a fürt metaadataiból, vagy tudja, hogyan szinkronizálhatja újra az állapotát más helyekről. A fürt rendelkezésre állása veszélybe kerülhet, ha a rendszerszolgáltatások kvórumveszteség-helyzetekbe kerülnek, mint amilyeneket korábban leírt. Ezekben az esetekben előfordulhat, hogy nem tud bizonyos műveleteket végrehajtani a fürtön (például egy frissítést vagy új szolgáltatásokat üzembe helyezni), de maga a fürt még mindig működik. 

A futó fürtön futó szolgáltatások továbbra is futnak ilyen körülmények között, kivéve, ha a rendszerszolgáltatások működésének folytatásához írásra van szükség. Ha például a Feladatátvétel-kezelő kvórumveszteségben van, az összes szolgáltatás továbbra is futni fog. De minden olyan szolgáltatás, amely nem lesz képes automatikusan újraindítani, mert ehhez a feladatátvételi kezelő bevonását igényli. 

### <a name="failures-of-a-datacenter-or-an-azure-region"></a>Adatközpont vagy Azure-régió hibái
Ritka esetekben a fizikai adatközpont átmenetileg elérhetetlenné válhat az áramellátás vagy a hálózati kapcsolat elvesztése miatt. Ezekben az esetekben a Service Fabric-fürtök és -szolgáltatások az adott adatközpontban vagy az Azure-régióban nem lesz elérhető. Az adatok azonban _megőrződnek._ 

Az Azure-ban futó fürtök esetében megtekintheti a kimaradások frissítéseit az [Azure állapotlapján.][azure-status-dashboard] Abban a nagyon valószínűtlen esetben, ha egy fizikai adatközpont részben vagy teljesen megsemmisült, az ott üzemeltetett Service Fabric-fürtök, vagy a bennük lévő szolgáltatások elveszhetnek. Ez a veszteség magában foglalja az adatközponton vagy régión kívül nem biztonsági másolatot nem tartalmazó bármely állapotot.

Két különböző stratégiák a túlélésre az állandó vagy tartós hiba egyetlen adatközpont vagy régió: 

- Futtasson külön Service Fabric-fürtöket több ilyen régióban, és használjon valamilyen mechanizmust a feladatátvételhez és a feladat-feladat-visszavételhez ezen környezetek között. Ez a fajta többfürtös aktív/aktív vagy aktív/passzív modell további felügyeleti és műveleti kódot igényel. Ez a modell is szükség van a biztonsági mentések egy adatközpontban vagy régióban a szolgáltatások biztonsági mentéseit, hogy azok más adatközpontokban vagy régiókban érhetők el, ha az egyik meghibásodik. 
- Egyetlen Service Fabric-fürt, amely több adatközpontra vagy régióra terjed ki. A stratégia minimálisan támogatott konfigurációja három adatközpont vagy régió. A régiók vagy adatközpontok ajánlott száma öt. 
  
  Ehhez a modellhez összetettebb fürttopológia szükséges. A haszon azonban az, hogy egy adatközpont vagy régió meghibásodása egy katasztrófa egy normál hiba. Ezeket a hibákat az egy régión belüli fürtökszámára dolgozó mechanizmusok oldhatják meg. A tartalék tartományok, a frissítési tartományok és a Service Fabric elhelyezési szabályai biztosítják a számítási feladatok elosztását, hogy azok tolerálják a normál hibákat. 
  
  Az ilyen típusú fürtszolgáltatások üzemeltetését segítő házirendekkel kapcsolatos további információkért olvassa el [a Service Fabric-szolgáltatások elhelyezési szabályzatait.](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-that-lead-to-cluster-failures"></a>Fürthibákhoz vezető véletlenszerű hibák
A Service Fabric a *magcsomópontok*fogalma. Ezek olyan csomópontok, amelyek fenntartják az alapul szolgáló fürt rendelkezésre állását. 

A magcsomópontok segítenek annak biztosításában, hogy a fürt más csomópontokkal való bérletek létrehozásával és bizonyos típusú hibák esetén tiebreakerként szolgáljon. Ha a véletlenszerű hibák eltávolítják a fürt ben lévő magcsomópontok többségét, és nem hozzák vissza őket gyorsan, a fürt automatikusan leáll. A fürt ezután meghibásodik. 

Az Azure-ban a Service Fabric erőforrás-szolgáltató kezeli a Service Fabric-fürtkonfigurációk. Alapértelmezés szerint az Erőforrás-szolgáltató elosztja a magcsomópontokat a hiba- és frissítési tartományok között az *elsődleges csomóponttípushoz.* Ha az elsődleges csomóponttípus ezüst vagy arany tartósságként van megjelölve, amikor eltávolít egy magcsomópontot (vagy az elsődleges csomópont típusának méretezésével, vagy manuálisan eltávolítja), a fürt megpróbál előléptetni egy másik nem magozott csomópontot az elsődleges csomóponttípus rendelkezésre álló kapacitásából. Ez a kísérlet sikertelen lesz, ha kevesebb rendelkezésre álló kapacitással rendelkezik, mint amit a fürt megbízhatósági szintje az elsődleges csomóponttípushoz igényel.

Az önálló Service Fabric-fürtök és az Azure-ban az elsődleges csomópont típusa az, amely futtatja a magok. Elsődleges csomóponttípus definiálásakor a Service Fabric automatikusan kihasználja a megadott csomópontok számát, ha az egyes rendszerszolgáltatások legfeljebb kilenc magcsomópontok és hét replikák létrehozásával. Ha egy sor véletlenszerű hibák veszi ki a legtöbb ilyen replikák egyidejűleg, a rendszerszolgáltatások lép kvórum elvesztése. Ha a magcsomópontok többsége elvész, a fürt nem sokkal később leáll.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan szimulálhatja a különböző hibák segítségével [a testability keretrendszer](service-fabric-testability-overview.md).
- Olvassa el az egyéb vész-helyreállítási és magas rendelkezésre állású erőforrásokat. A Microsoft nagy mennyiségű útmutatást tett közzé ezekről a témakörökről. Bár ezen erőforrások némelyike más termékekben való használatra vonatkozó speciális technikákra vonatkozik, számos általános ajánlott eljárás, amelyet a Service Fabric környezetben alkalmazhat:
  - [Rendelkezésre állási ellenőrzőlista](/azure/architecture/checklist/resiliency-per-service)
  - [Vész-helyreállítási gyakorlat végrehajtása](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Vészhelyreállítás és magas szintű rendelkezésre állás az Azure-alkalmazásokhoz][dr-ha-guide]
- További információ a [Service Fabric támogatási lehetőségeiről.](service-fabric-support.md)


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
