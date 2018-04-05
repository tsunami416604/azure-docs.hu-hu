---
title: A Microsoft Azure Service Fabric kapcsolatos gyakori kérdéseket |} Microsoft Docs
description: A Service Fabric és a válaszok kapcsolatos gyakori kérdések
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: ''
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: chackdan
ms.openlocfilehash: 38de0886de1d6068b2edad9aadc89d8048b48a55
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="commonly-asked-service-fabric-questions"></a>A Service Fabric gyakori kérdések

Nincsenek számos gyakran feltett kérdésekre a Service Fabric Teendők, és hogyan kell használni. Ez a dokumentum ismerteti a fenti gyakori kérdések és a válaszok.

## <a name="cluster-setup-and-management"></a>Fürt beállítása és kezelése

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Egy fürt, amely több Azure-régiók és a saját adatközpontját hozhat létre?

Igen. 

Az alapszintű Service Fabric fürtözési technológia segítségével futó bárhol a világon gépek kombinálhatja, feltéve, hogy egymáshoz hálózati kapcsolattal rendelkeznek. Azonban kialakításához és futtatásához az ilyen fürt bonyolult lehet.

Ha érdekli, ebben a forgatókönyvben, javasoljuk, hogy forduljon az beszerzése vagy keresztül a [Service Fabric Github problémák listájába](https://github.com/azure/service-fabric-issues) vagy a támogató szolgálat képviselője további útmutatás megkapásához. A Service Fabric csoport további egyértelműség, útmutatást és javaslatokat ehhez a forgatókönyvhöz dolgozik. 

Néhány mérlegelendő szempont ezzel kapcsolatban: 

1. A Service Fabric-fürt erőforrás az Azure-ban, regionális ma, vagy sem a virtuálisgép-méretezési beállítja, hogy a fürt épül. Ez azt jelenti, hogy a regionális hibája esetén elveszhetnek képes kezelni a fürt az Azure Resource Manager vagy az Azure-portálon keresztül. Ez akkor fordulhat elő, annak ellenére, hogy a fürt továbbra is futni fog, és közvetlenül használni tudná. Emellett Azure ma nem lehetővé teszik a rendelkezik egyetlen virtuális hálózaton használható régiók között. Ez azt jelenti, hogy az Azure-ban több területi fürt szükséges [az egyes virtuális gépek, a Virtuálisgép-méretezési készlet a nyilvános IP-címek](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) vagy [Azure VPN Gatewayek](../vpn-gateway/vpn-gateway-about-vpngateways.md). A fenti hálózatkezelési lehetőségek hatással különböző van a költségek, teljesítmény, és bizonyos mértékben alkalmazás tervét, hogy így körültekintően elemzés és tervezése előtt meg kell adni egy ilyen környezet állandó.
2. A karbantartási, kezeléséhez, és ezek a gépek figyelését válhat bonyolult, különösen akkor, ha átnyúlhatnak _típusok_ a környezetek, például különböző szolgáltatók közötti vagy a helyszíni erőforrások és az Azure közötti. Ügyelni kell arra, hogy frissítéseket, figyelés, felügyeleti és diagnosztikai értendők a fürt és az alkalmazások az ilyen környezetekben termelési számítási feladatokhoz futtatása előtt. Ha már rendelkezik tapasztalattal az Azure-ban vagy a saját adatközpontját problémák megoldásához, akkor valószínű, hogy ezek azonos megoldások is alkalmazható, ha épület kimenő, vagy a Service Fabric-fürt fut. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Hajtsa végre a Service Fabric-csomópontok automatikusan frissítését az operációs rendszer?

Nem ma de ez történik akkor is, amely Azure kívánja szállítani közös kérelmet.

Az idő alatt kell [alkalmazás megadott](service-fabric-patch-orchestration-application.md) , hogy az operációs rendszerek a Service Fabric-csomópont alatt maradnak javított, naprakész állapotban.

A kihívás az operációs rendszer frissítése érdekében, hogy ezek általában a számítógép újraindítása szükséges a gépet, amely ideiglenes rendelkezésre állást eredményez. Önmagában ez nem probléma, mivel a Service Fabric automatikusan átirányítja a forgalmat olyan szolgáltatási más csomópontokra. Azonban ha operációs rendszer frissítése érdekében van megfelelő koordináció hiányában a fürtön, nincs a lehetséges, hogy sok csomópont egyszerre leáll. Ilyen egyidejű újraindítások okozhat a teljes rendelkezésre állást egy szolgáltatás, vagy legalább egy adott partícióra (az állapotalapú szolgáltatás).

A jövőben tervezzük támogatja az operációs rendszer frissítési házirend teljesen automatizált és frissítési tartományokon, koordinált győződjön meg arról, hogy rendelkezésre állás annak ellenére, hogy az újraindítások és más váratlan meghibásodások esetén.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Használható a ú fürt nagy virtuálisgép-méretezési csoportok? 

**Válasz rövid** – nem 

**Hosszú válasz** – Bár a nagy virtuálisgép-méretezési csoportok lehetővé teszik a virtuális gépek méretezési készlet legfeljebb 1000 Virtuálisgép-példányok méretezési, akkor nem így elhelyezési csoportok (PGs) használatával. Tartalék tartományok (FDs) és a frissítési tartományok (UDs) belül egy elhelyezési csoport Service fabric használ FDs és UDs elhelyezési döntések a szolgáltatás replikák/szolgáltatás példányainak csak megegyeznek. Mivel a FDs és UDs elhelyezési csoporton belül csak összehasonlítható, ú nem tudja azt használni. Például, ha a PG1 VM1 a FD-topológia = 0, és a PG2 VM9 a FD-topológia = 4, ez nem jelenti azt, hogy VM1 és vm2 virtuális gépnek a két különböző hardver Rackszekrények, ezért ú nem használható a FD értékek ebben az esetben elhelyezési vonatkozó döntések meghozatalában.

Jelenleg más problémákat nagy virtuálisgép-méretezési csoportok, például a 4. szint hiánya betölteni a terheléselosztási támogatását. További [nagy részleteinek méretezése beállítása](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Mi az a Service Fabric-fürt a legkisebb méretét? Miért nem legyen kisebb?

A minimális támogatott termelési számítási feladatokhoz futó Service Fabric-fürt mérete az öt csomóponttal. Fejlesztési/Tesztelési forgatókönyvek esetén három csomópontos fürt támogatott.

Ezek minimumértékének létezik, mert a Service Fabric-fürt lefuttat egy állapotalapú rendszerszolgáltatások, beleértve a naming service és a Feladatátvevőfürt-kezelő. Ezek a szolgáltatások, nyomon követheti, milyen szolgáltatások telepítve vannak a fürthöz, és ha azok még jelenleg üzemel, az erős konzisztencia függ. Szerezzen be lehessen, függ, hogy az erős konzisztencia egy *kvórum* bármely adott frissítési állapotát a szolgáltatási, ahol a kvórum jelenti szigorú többsége a replikák (N/2 + 1) egy adott szolgáltatáshoz.

A háttérben történő vizsgáljuk meg néhány lehetséges fürtkonfigurációk:

**Egy csomópont**: ezt a beállítást nem biztosít magas rendelkezésre állás egyetlen csomópont elvesztését óta bármilyen okból azt jelenti, hogy a teljes fürt elvesztését.

**Két csomópont**: két csomópont között telepített szolgáltatáshoz a kvórum (N = 2) 2 (2/2 + 1 = 2). Ha egy replikát nem vesztek el, lehetetlen hozzon létre egy kvórum. Az szolgáltatás frissítés végrehajtása szükséges egy replika le ideiglenesen véve, ez a beállítás nem hasznos.

**Három csomópont**: a három csomópont (N = 3), az a követelmény, hozzon létre egy kvórum még két csomópont (3/2 + 1 = 2). Ez azt jelenti, hogy az egyes csomópontok elvesznek, és továbbra is a kvórum fenntartása.

A három csomópontos fürt konfigurációja támogatott fejlesztési és tesztelési célú biztonságosan frissítések végrehajtása és után is megmaradnak az egyes csomópontok hibáit, mert mindaddig, amíg azok nem fordulhat elő, egyidejűleg. A termelési számítási feladatokhoz kell lennie rugalmasak ilyen egyidejű hibája esetén, így öt csomópontra szükség.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Lehet kikapcsolni a fürt következő éjszakai/hétvégéket költségek csökkentése érdekében?

Általában nem. A Service Fabric tárolja állapot helyi, rövid élettartamú lemezeken, ami azt jelenti, hogy a virtuális gép átkerül egy másik gazdagépet, ha az adatok nem helyezi át a vele. A normál működés, amely nincs probléma, az új csomópont más csomópontok naprakész állapotba. Azonban ha állítson le minden csomópontot, és azokat később újraindíthatja a rendszert, nincs jelentős lehetséges, hogy a csomópontok többsége indítsa el az új gazdagépek számára, ellenőrizze a rendszer nem lehet helyreállítani.

Ha szeretné az alkalmazást tesztelni, mielőtt telepítené a fürtöket létrehozni, azt javasoljuk, hogy dinamikusan hozzon létre ezeket a fürt részeként a [folyamatos integráció/folyamatos központi telepítési folyamat](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Hogyan lehet frissíteni az operációs rendszer (például a Windows Server 2012, Windows Server 2016)?

Napjainkban fejlesztjük fejlett élményt, amíg való telepítésért felelős a frissítést. Frissítenie kell az operációsrendszer-lemezképek, a virtuális gépek a fürt egyik virtuális gép egyszerre. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Titkosíthatja a mellékelt adatok lemezt a fürt csomóponttípus (virtuálisgép-méretezési csoport)?
Igen.  További információkért lásd: [hozzon létre egy fürtöt csatolt adatlemezek](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks), [lemezek (PowerShell) titkosítása](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md), és [lemezek (CLI) titkosítása](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-cli.md).

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster-"></a>Mik azok a könyvtárak és folyamatok, amelyek a fürt egy víruskereső program futtatása során kell?

| **Víruskereső kizárt könyvtárak** |
| --- |
| Program Files\Microsoft a Service Fabric |
| A FabricDataRoot (a fürtkonfiguráció) |
| (A fürtkonfiguráció) fabriclogroot mappában |

| **Víruskereső kizárt folyamatok** |
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
| FileStoreService.exe |
 
## <a name="application-design"></a>Alkalmazás tervezése

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Adatait kérdezi le. a legjobb módszer mekkora megbízható gyűjtemény partíciók között?

Megbízható gyűjtemények jellemzően [particionált](service-fabric-concepts-partitioning.md) ahhoz, hogy a nagyobb teljesítmény és átviteli kibővítési. Ez azt jelenti, hogy az adott szolgáltatás állapotának terjedhetnek több tíz vagy több száz gépek között. A teljes adatkészlet keresztül műveletek végrehajtásához néhány lehetőség közül választhat:

- Hozzon létre egy szolgáltatás, amely egy másik szolgáltatást a szükséges adatok bekérésére partíciók lekérdezésével.
- Hozzon létre egy szolgáltatás, amely egy másik szolgáltatás minden partíciója fogadhat adatokat.
- Adatokat rendszeres időközönként minden szolgáltatásból a külső áruházban. Ez a módszer csak akkor megfelelő, ha a lekérdezések végrehajtásához nem szerepelnek a fő üzleti logika.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Adatait kérdezi le. a legjobb módszer mekkora a szereplője között?

Szereplője lettek kialakítva független egység állapot és a számítást, és nem célszerű a futási időben aktorállapot széleskörű lekérdezések végrehajtásához. Ha egy lekérdezést kell aktorállapot a teljes szétosztva, érdemes vagy:

- A aktorszolgáltatások cseréje az állapot-nyilvántartó megbízható szolgáltatásokat, úgy, hogy az összes adat gyűjtését szereplője száma a szolgáltatás a partíciók száma kérelmek hálózati száma.
- A rendszeres időközönként állapotukra könnyebb lekérdezése külső tárolóhoz szereplője megtervezése. Újabb verzióiban ez a megközelítés lesz csak kivitelezhető, ha a lekérdezések végrehajtásához esetén nincs szükség a működését.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Mennyi adatot tud tárolni egy megbízható gyűjtemény?

Megbízható szolgáltatások általában particionáltak, így a tárolhatja csak korlátozza a fürt rendelkezik gépek számát, és az elérhető memória mennyiségének ezeken a számítógépeken.

Tegyük fel tegyük fel, hogy egy megbízható gyűjtemény 100 partíciók 3 replikák egy szolgáltatást a 1 kb méretű átlagos objektumok tárolására. Most tegyük fel, hogy rendelkezik-e a 16gb memóriája gépenként 10 gép fürtben. Az egyszerűség és a óvatosan feltételeztük, hogy az operációs rendszerek és rendszerszolgáltatások, a Service Fabric-futtatókörnyezet és a szolgáltatások felhasználásához 6gb, így a 10 GB-os gépenként érhető el, vagy 100 gb a fürt.

Figyelembe vételével kell lennie a minden objektumon tárolt három időpontokban (egy elsődleges és két replika), akkor egy körülbelül 35 millió objektumok elegendő memória áll rendelkezésre a gyűjteményben teljes kapacitás üzemi. Azt javasoljuk azonban alatt rugalmas hiba tartományhoz és egy frissítési tartomány, amely körülbelül 1/3 kapacitás jelöli, és akkor adjon meg kevesebb nagyjából 23 millió egyidejű elvész.

Vegye figyelembe, hogy a számítási is feltételezi, hogy:

- Hogy az adatok így vannak elrendezve a partíciók terjesztését többé-kevésbé egységes, és, hogy Ön éppen reporting terhelési metrika a fürt erőforrás-kezelő. Alapértelmezés szerint a Service Fabric betölti egyenleg replikáinak száma alapján. A fenti példában, célszerű helyezni 10 elsődleges replika és 20 másodlagos replikák a fürt minden csomópontjának. Betöltési, amely egyenlően elosztva a partíciók esetén, amelyek működik. Betöltési nincs még akkor is, ha jelenteniük kell terhelés, hogy az erőforrás-kezelő együtt csomag kisebb replikákat, és lehetővé teszi a nagyobb replikák egy egyedi csomóponton több memóriát használ.

- A szóban forgó megbízható szolgáltatás fogja a fürt csak egy tárolni állapotát. Mivel több szolgáltatás telepítene egy fürtöt, kell lennie az erőforrások szem előtt tartva, hogy minden egyes kell futtatni, és az állapot kezelése.

- Hogy egyrészt a fürt nem növekvő vagy zsugorítását. További gépek hozzáadásakor a Service Fabric fog egyensúlyba kihasználhatják a további kapacitást, amíg gépek száma meghaladja a szolgáltatást, a partíciók száma, mivel az egyes replika gépek nem terjedhetnek ki replikát. Ezzel szemben gépek eltávolításával csökkentse a fürt méretét, ha a replikák szigorúbban vannak csomagolva és teljes kapacitásának rendelkezik.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Mennyi adatot is tárolnak a egy szereplő?

Csakúgy, mint a megbízható szolgáltatások szereplő szolgáltatásnak is tárolt adatok mennyisége csak korlátozza a teljes lemezterület és a rendelkezésre álló memória a fürt csomópontjai között. Azonban egyedi szereplője esetén leghatékonyabb foglalják magukban az állapot és a kapcsolódó üzleti logikát kis mennyiségű használhatók. Általános szabályként elmondható egy egyéni szereplő, amelyet a rendszer kilobájtban kell rendelkeznie.

## <a name="other-questions"></a>Egyéb kérdések

### <a name="how-does-service-fabric-relate-to-containers"></a>Hogyan kapcsolódnak a Service Fabric tárolók?

Tárolók csomag szolgáltatások és függőségi viszonyaikat egyszerű módszert kínál, úgy, hogy következetesen minden környezetben futtatni, és egy gépen csak elkülönített módon kapnak. A Service Fabric kínál telepíthetnek és kezelhetnek szolgáltatásokat, például a módon [, amely rendelkezik egy tárolóba van csomagolva szolgáltatások](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Tervezi nyílt forráskódú Service Fabric?

Service Fabric nyílt forrása részei tudunk ([megbízható szolgáltatások keretrendszer](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [megbízható szereplője keretrendszer](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [ASP.NET Core integrációs szalagtárak](https://github.com/Azure/service-fabric-aspnetcore), [ Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer), és [Service Fabric CLI](https://github.com/Azure/service-fabric-cli)) a Githubon, és fogadja el ezeket a projektek közösségi hozzájárulások. 

A Microsoft [nemrég jelentette be](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/) , hogy tervezzük nyílt forráskódú a Service Fabric-futtatókörnyezet. Ezen a ponton tudunk a [Service Fabric-tárház](https://github.com/Microsoft/service-fabric/) akár a Githubon Linux összeállításához és teszteléséhez eszközök, ami azt jelenti, hogy a tárház klónozása, létrehozhatja a Service Fabric Linux, egyszerű tesztek futtatása, nyissa meg a problémákat, és lekérési kérelmek elküldése. Keményen dolgozunk a Windows-összeállító környezetet áttelepítve, és teljes körű CI-környezetet.

Kövesse a [Service Fabric blog](https://blogs.msdn.microsoft.com/azureservicefabric/) azok még jelent további részletekért.

## <a name="next-steps"></a>További lépések

- [További tudnivalók a Service Fabric Alapfogalmak és ajánlott eljárások](https://mva.microsoft.com/en-us/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965)
