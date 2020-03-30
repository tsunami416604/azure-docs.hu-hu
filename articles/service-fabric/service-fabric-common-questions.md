---
title: Gyakori kérdések a Microsoft Azure Service Fabric programról
description: Gyakori kérdések a Service Fabric, beleértve a képességek, használati esetek és gyakori forgatókönyvek.
ms.topic: troubleshooting
ms.date: 08/18/2017
ms.author: pepogors
ms.openlocfilehash: bf61858b446c1ac6d4a0210571fffaa721ad0166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254885"
---
# <a name="commonly-asked-service-fabric-questions"></a>Service Fabric – GYIK

Számos gyakran feltett kérdések, hogy mit tehet a Service Fabric, és hogyan kell használni. Ez a dokumentum számos ilyen gyakori kérdést és választ foglal magában.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Fürt beállítása és kezelése

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Hogyan állíthatja vissza a Service Fabric-fürttanúsítványomat?

Az alkalmazás bármely frissítésének visszaállítása állapothiba-észlelést igényel, mielőtt a Service Fabric-fürt fürtkvóruma véglegesíti a módosítást; a véglegesített módosítások at csak előre lehet görgetni. Eszkalációs mérnök az ügyfélszolgálaton keresztül, szükség lehet a fürt helyreállításához, ha nem figyelt törés tanúsítvány változás került bevezetésre.  [A Service Fabric alkalmazásfrissítése](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade?branch=master) [alkalmazásfrissítési paramétereket](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master)alkalmaz, és nulla állásidő-frissítési ígéretet tesz.  Az ajánlott alkalmazásfrissítési figyelt módot követően a frissítési tartományokon keresztüli automatikus előrehaladás az állapotellenőrzések átadásán alapul, és automatikusan visszaáll, ha egy alapértelmezett szolgáltatás frissítése sikertelen.
 
Ha a fürt továbbra is kihasználja a klasszikus tanúsítvány ujjlenyomat tulajdonság a Resource Manager sablonban, javasoljuk, hogy [módosítsa a fürt tanúsítvány ujjlenyomata a közös név,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn)a modern titkos kulcsok kezelési funkciók.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Létrehozhatok egy fürtöt, amely több Azure-régióra vagy saját adatközpontokra terjed ki?

Igen. 

A Service Fabric alapvető fürtözési technológiája a világ bármely pontján futó gépek kombinálására használható, feltéve, hogy rendelkeznek egymással a hálózati kapcsolattal. Az ilyen fürt létrehozása és futtatása azonban bonyolult lehet.

Ha érdekli ez a forgatókönyv, azt javasoljuk, hogy lépjen kapcsolatba akár a [Service Fabric GitHub problémák listája,](https://github.com/azure/service-fabric-issues) vagy a támogatási képviselő, hogy további útmutatást. A Service Fabric csapata azon dolgozik, hogy további egyértelműséget, útmutatást és javaslatokat ebben a forgatókönyvben. 

Néhány dolog, hogy fontolja meg: 

1. A Service Fabric fürterőforrás az Azure-ban regionális ma, csakúgy, mint a virtuális gép méretezési készletek, amelyek a fürt épül. Ez azt jelenti, hogy regionális hiba esetén elveszítheti a fürt kezelésére az Azure Resource Manager vagy az Azure Portal on keresztül. Ez akkor is előfordulhat, ha a fürt továbbra is fut, és ön képes lenne közvetlenül kommunikálni vele. Emellett az Azure ma már nem rendelkezik azzal a képességgel, hogy egyetlen virtuális hálózat, amely használható régiók között. Ez azt jelenti, hogy az Azure-ban egy több régióra kiterjedő fürt höz [nyilvános IP-címek szükségesek a virtuális gép méretezési csoportjaiban lévő minden virtuális géphez,](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) vagy az Azure [VPN-átjárókhoz.](../vpn-gateway/vpn-gateway-about-vpngateways.md) Ezek a hálózati lehetőségek különböző hatással vannak a költségekre, a teljesítményre és bizonyos mértékig az alkalmazások tervezésére, ezért gondos elemzésre és tervezésre van szükség, mielőtt egy ilyen környezetet felállna.
2. Ezeknek a gépeknek a karbantartása, kezelése és figyelése bonyolulttá válhat, különösen akkor, ha a különböző felhőszolgáltatók, illetve a helyszíni erőforrások és az Azure között _többféle_ környezetben is használható. Ügyelni kell arra, hogy a frissítések, a figyelés, a felügyelet és a diagnosztika a fürt és az alkalmazások szempontjából is érthető legyen, mielőtt éles számítási feladatokat futtatna ilyen környezetben. Ha már van tapasztalata a problémák megoldásában az Azure-ban vagy a saját adatközpontok, majd valószínű, hogy ugyanezek a megoldások is alkalmazhatók a Service Fabric-fürt létrehozásakor vagy futtatásakor. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>A Service Fabric-csomópontok automatikusan megkapják az operációs rendszer frissítéseit?

Használhatja [a virtuális gép méretezése set automatikus operációsrendszer-lemezkép frissítése](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) általánosan elérhető funkció ma.

Az Azure-ban nem futtatott fürtök esetében [biztosítottunk egy alkalmazást](service-fabric-patch-orchestration-application.md) a Service Fabric-csomópontok alatti operációs rendszerek javításához.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Használhatok nagy virtuálisgép-méretezési csoportokat az SF-fürtben? 

**Rövid válasz** : Nem. 

**Hosszú válasz** – Bár a nagy virtuálisgép-méretezési csoportok lehetővé teszik az 1000 virtuálisgép-példányra beállított virtuálisgép-méretezés méretezését, ezt elhelyezési csoportok (PGs) használatával teszi. A tartalék tartományok (FDs) és a frissítési tartományok (UD-k) csak egy elhelyezési csoporton belül konzisztensek, a Service fabric FD-ket és UD-ket használ a szolgáltatás replikák/szolgáltatáspéldányok elhelyezési döntéseihez. Mivel az FD-k és az UD-k csak egy elhelyezési csoporton belül hasonlíthatók össze, az SF nem tudja használni. Ha például a PG1-ben a VM1 topológiája FD=0, a PG2-ben pedig VM9 topológiája FD=4 topológiával rendelkezik, az nem jelenti azt, hogy a VM1 és a VM2 két különböző hardverállványon van, ezért az SF ebben az esetben nem használhatja az FD értékeket elhelyezési döntések meghozatalához.

Vannak más problémák a nagy virtuálisgép-méretezési készletek jelenleg, mint a 4-es szintű terheléselosztás támogatás hiánya. A [nagyméretű készletekrészleteiről itt tájékán tájékformát talál.](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Mi a Service Fabric-fürt minimális mérete? Miért nem lehet kisebb?

Az éles számítási feladatokat futtató Service Fabric-fürt minimális támogatott mérete öt csomópont. Fejlesztési forgatókönyvek esetén támogatjuk az egyik csomópontot (a Visual Studio gyors fejlesztési élményére optimalizálva) és öt csomópont-fürtöt.

A következő három ok miatt egy éles fürtnek legalább 5 csomóponton kell rendelkeznie:
1. Még akkor is, ha nincs felhasználói szolgáltatás fut, a Service Fabric-fürt állapotalapú rendszerszolgáltatások készletét futtatja, beleértve az elnevezési szolgáltatást és a feladatátvételi kezelő szolgáltatást. Ezek a rendszerszolgáltatások elengedhetetlenek ahhoz, hogy a fürt működőképes maradjon.
2. Egy szolgáltatás replikáját mindig csomópontonként helyezzük el, így a fürtmérete a szolgáltatás (valójában egy partíció) replikák számára vonatkozó felső korlát.
3. Mivel a fürtfrissítés legalább egy csomópontot lefog állítani, legalább egy csomópontpuffert szeretnénk, ezért azt szeretnénk, hogy egy termelési fürt a minimális minimumon kívül legalább két csomót *hozzon létre.* A minimális minimum a rendszerszolgáltatás kvórummérete az alábbiakszerint.  

Azt szeretnénk, hogy a fürt két csomópont egyidejű meghibásodása esetén is elérhető legyen. Ahhoz, hogy egy Service Fabric-fürt elérhető legyen, a rendszerszolgáltatásoknak elérhetőnek kell lenniük. Állapotalapú rendszerszolgáltatások, például a névadási szolgáltatás és a feladatátvételi kezelő szolgáltatás, amely nyomon követi, hogy milyen szolgáltatások vannak telepítve a fürtre, és ahol jelenleg üzemeltetik, erős konzisztencia függ. Ez az erős konzisztencia viszont attól függ, hogy képes-e *a kvórum* a szolgáltatások állapotának adott frissítéséhez, ahol a kvórum egy adott szolgáltatás replikáinak (N/2 +1) szigorú többségét képviseli. Így ha azt akarjuk, hogy rugalmas egyidejű elvesztése két csomópont (így egyidejű elvesztése két replikák egy rendszerszolgáltatás), meg kell ClusterSize - QuorumSize >= 2, amely arra kényszeríti a minimális méretet, hogy öt. Ennek megtekintéséhez vegye figyelembe, hogy a fürt N-csomópontokkal rendelkezik, és a rendszerszolgáltatás N replikái vannak - minden csomóponton egy. A rendszerszolgáltatás kvórummérete (N/2 + 1). A fenti egyenlőtlenség úgy néz ki, mint N - (N/2 + 1) >= 2. Két esetet kell figyelembe venni: amikor N egyenlít, és amikor N páratlan. Ha N páros, mondjuk\*N = 2 m, ahol m\*>=\*1, az egyenlőtlenség 2 m - (2 m/2 + 1) >= 2 vagy m >= 3. Az N minimum 6, és ez akkor érhető el, ha m = 3. Másrészről, ha N páratlan, mondjuk\*N = 2 m+1, ahol m\*>= 1,\*az egyenlőtlenség úgy néz ki, mint 2\*m+1 - ( (2 m+1)/2 + 1 ) >= 2 vagy 2 m+1 - (m+1) >= 2 vagy m >= 2. Az N minimum 5, és ez akkor érhető el, ha m = 2. Ezért az N összes értéke közül, amely megfelel a ClusterSize - QuorumSize >= 2 egyenlőtlenségnek, a minimum 5.

Ne feledje, hogy a fenti argumentumban azt feltételeztük, hogy minden csomópont rendelkezik egy rendszerszolgáltatás replikájával, így a kvórum mérete a fürtben lévő csomópontok száma alapján kerül kiszámításra. A *TargetReplicaSetSize* módosításával azonban a kvórumméretet (N/2+1) kisebbre tehetjük, ami azt a benyomást keltheti, hogy 5 csomópontnál kisebb fürttel rendelkezhetünk, és még mindig 2 további csomóponttal rendelkezünk a kvórumméret felett. Például egy 4 csomópontos fürtben, ha a TargetReplicaSetSize-ot 3-ra állítjuk, a TargetReplicaSetSize-on alapuló kvórumméret (3/2 + 1) vagy 2, így clustersize - QuorumSize = 4-2 >= 2. Azonban nem tudjuk garantálni, hogy a rendszerszolgáltatás lesz, vagy a fenti kvórum, ha elveszítjük a pár csomópont egyszerre, lehet, hogy a két csomópont elvesztettük volt otthont két replikák, így a rendszerszolgáltatás megy kvórum elvesztése (miután csak egy replika maradt) és elérhetetlenné válik.

Ezzel a háttérrel vizsgáljunk meg néhány lehetséges fürtkonfigurációt:

**Egy csomópont:** ez a beállítás nem biztosít magas rendelkezésre állást, mivel az egyetlen csomópont elvesztése bármilyen okból a teljes fürt elvesztését jelenti.

**Két csomópont:** a két csomóponton (N = 2) üzembe helyezett szolgáltatás kvóruma 2 (2/2 + 1 = 2). Ha egyetlen replika elvész, nem lehet kvórumot létrehozni. Mivel a szolgáltatásfrissítés végrehajtása ideiglenesen le kell venni egy replika, ez nem hasznos konfiguráció.

**Három csomópont:** három csomó (N=3) esetén a kvórum létrehozásának követelménye még mindig két csomópont (3/2 + 1 = 2). Ez azt jelenti, hogy elveszthet egy egyes csomópontot, és továbbra is fenntarthatja a kvórumot, de két csomópont egyidejű meghibásodása a rendszerszolgáltatásokat a kvórum elvesztésébe fogja vezetni, és a fürt elérhetetlenné válik.

**Négy csomópont:** négy csomó (N=4) esetén a kvórum létrehozásának követelménye három csomópont (4/2 + 1 = 3). Ez azt jelenti, hogy elveszthet egy egyes csomópontot, és továbbra is fenntarthatja a kvórumot, de két csomópont egyidejű meghibásodása a rendszerszolgáltatásokat a kvórum elvesztésébe fogja vezetni, és a fürt elérhetetlenné válik.

**Öt csomópont:** öt csomóponttal (N=5) a kvórum létrehozásának követelménye még mindig három csomópont (5/2 + 1 = 3). Ez azt jelenti, hogy egyszerre két csomópontot is elveszthet, és továbbra is fenntarthatja a rendszerszolgáltatások kvórumát.

Éles számítási feladatok esetén legalább két csomópont egyidejű meghibásodásával szemben kell lennie (például egy fürtfrissítés miatt, egy más okok miatt), ezért öt csomópontra van szükség.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Kikapcsolhatom a fürtöt éjszaka/hétvégén a költségek megtakarítása érdekében?

Általában nem. A Service Fabric tárolja az állapotot a helyi, ideiglenes lemezeken, ami azt jelenti, hogy ha a virtuális gép egy másik gazdagépre kerül, az adatok nem mozog vele. Normál működés közben ez nem jelent problémát, mivel az új csomópontot más csomópontok naprakészen tartják. Ha azonban leállítja az összes csomópontot, és később újraindítja őket, jelentős a valószínűsége annak, hogy a csomópontok többsége új gazdagépeken indul, és a rendszer nem képes helyreállítani.

Ha fürtöt szeretne létrehozni az alkalmazás üzembe helyezése előtt történő teszteléséhez, javasoljuk, hogy dinamikusan hozza létre ezeket a fürtöket a [folyamatos integrációs/folyamatos üzembe helyezési folyamat](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)részeként.


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Hogyan frissíthetem az operációs rendszert (például Windows Server 2012-ről Windows Server 2016-ra)?

Miközben egy továbbfejlesztett élményen dolgozunk, ma Ön felelős a frissítésért. Frissítenie kell az operációs rendszer képe a virtuális gépek a fürt egy virtuális gép egy időben. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Titkosíthatók a fürtcsomópont-típushoz csatolt adatlemezek (virtuálisgép-méretezési csoport)?
Igen.  További információt a [Fürt létrehozása csatlakoztatott adatlemezekkel](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks) és az Azure Disk Encryption a [virtuálisgép-méretezési készletekhez című témakörben talál.](../virtual-machine-scale-sets/disk-encryption-overview.md)

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Használhatok alacsony prioritású virtuális gépeket fürtcsomópont-típusban (virtuálisgép-méretezési csoportban)?
Nem. Alacsony prioritású virtuális gépek nem támogatottak. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Melyek azok a könyvtárak és folyamatok, amelyeket ki kell zárnom, amikor víruskereső programot futtatok a fürtömben?

| **Víruskereső kizárt könyvtárak** |
| --- |
| Programfájlok\Microsoft Service Fabric |
| FabricDataRoot (fürtkonfigurációból) |
| FabricLogRoot (fürtkonfigurációból) |

| **Vírusvédelem kizárt folyamatai** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe fájl |
 
### <a name="how-can-my-application-authenticate-to-keyvault-to-get-secrets"></a>Hogyan hitelesítheti az alkalmazásomat a KeyVault-on, hogy titkokat kapjon?
Az alábbi eszközök keltik az alkalmazást a KeyVault-hitelesítéshez szükséges hitelesítő adatok beszerzéséhez:

A. Az alkalmazások buildelési/csomagolási feladata során lehívhat egy tanúsítványt az SF-alkalmazás adatcsomagjába, és ezzel hitelesítheti magát a KeyVault-ban.
B. Az MSI-kompatibilis virtuálisgép-méretezési készlethez egy egyszerű PowerShell SetupEntryPoint-ot fejleszthet az SF-alkalmazáshoz, hogy hozzáférési jogkivonatot kapjon [az MSI-végpontról,](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)majd [lekérje a titkos kulcsokat a KeyVault ból.](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret)

## <a name="application-design"></a>Alkalmazás tervezése

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Mi a legjobb módja az adatok lekérdezése a megbízható gyűjtemény partíciói között?

Megbízható gyűjtemények általában [particionált](service-fabric-concepts-partitioning.md) horizontális felskálázás a nagyobb teljesítmény és az átviteli teljesítmény érdekében. Ez azt jelenti, hogy egy adott szolgáltatás állapota több tíz vagy több száz gépközött elosztható. A teljes adatkészleten keresztüli műveletek végrehajtásához néhány lehetőség közül választhat:

- Hozzon létre egy szolgáltatást, amely lekérdezi egy másik szolgáltatás összes partícióját a szükséges adatok lekérése érdekében.
- Hozzon létre egy szolgáltatást, amely képes fogadni az adatokat egy másik szolgáltatás összes partíciójáról.
- Rendszeres időközönként leküldéses adatokat az egyes szolgáltatások egy külső tárolóba. Ez a megközelítés csak akkor megfelelő, ha a lekérdezések ön által végzett nem része az alapvető üzleti logika, mivel a külső tároló adatai elavultlesz.
- Másik lehetőségként tárolja azokat az adatokat, amelyeknek támogatniuk kell az összes rekord lekérdezését közvetlenül az adattárban, nem pedig egy megbízható gyűjteményben. Ez kiküszöböli az elavult adatokkal kapcsolatos problémát, de nem teszi lehetővé a megbízható gyűjtemények előnyeit.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Mi a legjobb módja az adatok lekérdezésének a szereplőim között?

A szereplők úgy vannak kialakítva, hogy független állapot- és számítási egységek legyenek, ezért nem ajánlott az aktorállapot széles körű lekérdezései futásidőben. Ha az aktorállapot teljes készletében kell lekérdeznie, fontolja meg a következőket:

- Az aktor szolgáltatások állapotalapú megbízható szolgáltatásokkal való lecserélése, hogy a hálózati kérelmek száma, hogy összegyűjtse az összes adatot a szereplők száma a partíciók száma a szolgáltatásban.
- A szereplők tervezése, hogy rendszeresen leküldéses állapotukat egy külső tárolóba a könnyebb lekérdezés. Mint fent, ez a megközelítés csak akkor életképes, ha a lekérdezések ön által végzett nem szükséges a futásidejű viselkedés.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Mennyi adatot tárolhatok egy megbízható gyűjteményben?

A megbízható szolgáltatások általában particionáltak, így a tárolható mennyiséget csak a fürtben lévő gépek száma és az ezeken a gépeken rendelkezésre álló memória korlátozza.

Tegyük fel például, hogy egy 100 partícióval és 3 replikával rendelkező szolgáltatásmegbízható gyűjteményt tartalmaz, amely átlagosan 1 kb méretű objektumokat tárol. Most tegyük fel, hogy van egy 10 gépfürt 16GB memória gépenként. Az egyszerűség kedvéért, és hogy konzervatív, tegyük fel, hogy az operációs rendszer és a rendszer szolgáltatások, a Service Fabric futásidejű, és a szolgáltatások fogyasztanak 6gb, hogy így 10GB elérhető gépenként, vagy 100 gb a fürt számára.

Szem előtt tartva, hogy minden objektumot háromszor kell tárolni (egy elsődleges és két replikát), akkor elegendő memóriával rendelkezik körülbelül 35 millió objektum számára a gyűjteményben, ha teljes kapacitással működik. Azonban azt javasoljuk, hogy rugalmas az egyidejű veszteség egy hiba tartomány és egy frissítési tartomány, amely körülbelül 1/3 kapacitás, és csökkentené a számot, hogy durván 23 millió.

Ne feledje, hogy ez a számítás a következőket is feltételezi:

- Az adatok partíciók közötti elosztása nagyjából egységes, vagy hogy a terhelési metrikák at a fürt erőforrás-kezelő. Alapértelmezés szerint a Service Fabric terhelések egyenleg e replika száma alapján. Az előző példában, amely 10 elsődleges replikák és 20 másodlagos replikák a fürt minden csomópontján. Ez jól működik a terhelés, amely egyenletesen oszlik el a partíciókközött. Ha a betöltés nem egyenletes, jelentenie kell a betöltést, hogy az Erőforrás-kezelő kisebb replikákat csomagoljon együtt, és lehetővé tegye a nagyobb replikák számára, hogy több memóriát használjanak fel egy adott csomóponton.

- Hogy a kérdéses megbízható szolgáltatás az egyetlen tároló állapot a fürtben. Mivel több szolgáltatást is üzembe helyezhet egy fürtre, figyelembe kell vennie az erőforrásokat, amelyeket az egyes szükséges az állapot futtatásához és kezeléséhez.

- Hogy maga a fürt nem növekszik vagy zsugorodik. Ha további gépeket ad hozzá, a Service Fabric újraegyensúlyozza a replikákat, hogy kihasználja a további kapacitást, amíg a gépek száma meghaladja a partíciók számát a szolgáltatásban, mivel egy adott replika nem képes a gépekre. Ezzel szemben, ha csökkenti a fürt méretét a gépek eltávolításával, a replikák vannak csomagolva szorosabban, és kevesebb teljes kapacitással.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Mennyi adatot tárolhatok egy szereplőben?

A megbízható szolgáltatásokhoz hasonlóan az aktorszolgáltatásban tárolható adatok mennyiségét csak a fürt csomópontjaiközött rendelkezésre álló teljes lemezterület és memória korlátozza. Az egyes szereplők azonban akkor a leghatékonyabbak, ha kis mennyiségű állami és kapcsolódó üzleti logikát foglalnak magukba. Általános szabályként az egyes aktornak kilobájtban mért állapottal kell rendelkeznie.

## <a name="other-questions"></a>Egyéb kérdések

### <a name="how-does-service-fabric-relate-to-containers"></a>Hogyan kapcsolódik a Service Fabric a tárolókhoz?

A tárolók egyszerű módot kínálnak a szolgáltatások és függőségeik csomagolására, hogy azok minden környezetben következetesen fussanak, és egyetlen gépen elszigetelt módon működhessenek. A Service Fabric lekínál egy lehetőséget a szolgáltatások üzembe helyezésére és kezelésére, beleértve [a tárolóba csomagolt szolgáltatásokat](service-fabric-containers-overview.md)is.

### <a name="are-you-planning-to-open-source-service-fabric"></a>Azt tervezi, hogy a nyílt forráskódú Service Fabric?

A Service Fabric nyílt forráskódú részei[(megbízható szolgáltatási keretrendszer,](https://github.com/Azure/service-fabric-services-and-actors-dotnet) [megbízható szereplők keretrendszere](https://github.com/Azure/service-fabric-services-and-actors-dotnet), ASP.NET Core [integrációs könyvtárak](https://github.com/Azure/service-fabric-aspnetcore), Service Fabric [Explorer](https://github.com/Azure/service-fabric-explorer)és Service [Fabric CLI)](https://github.com/Azure/service-fabric-cli)találhatók a GitHubon, és elfogadják a projektekhez való közösségi hozzájárulásokat. 

[Nemrég bejelentette,](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/) hogy azt tervezi, hogy a nyílt forráskódú a Service Fabric futásidejű. Ezen a ponton már a [Service Fabric repo](https://github.com/Microsoft/service-fabric/) fel A GitHub Linux build és teszt eszközök, ami azt jelenti, hogy klónozhatja a táregyesítés, build Service Fabric linuxos, alapvető tesztek futtatása, nyitott problémák, és küldjön lekéréses kérelmeket. Keményen dolgozunk, hogy a Windows build környezet átvándorolt is, valamint a teljes CI környezetben.

Kövesse a [Service Fabric blog](https://blogs.msdn.microsoft.com/azureservicefabric/) további részletekért, ahogy ők jelentették be.

## <a name="next-steps"></a>További lépések

Ismerje meg a [Service Fabric alapvető fogalmait](service-fabric-technical-overview.md) és [ajánlott eljárásokat](service-fabric-best-practices-overview.md)
