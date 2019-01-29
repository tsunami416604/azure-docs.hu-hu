---
title: A Microsoft Azure Service Fabric kapcsolatos gyakori kérdéseket |} A Microsoft Docs
description: A Service Fabric és a válaszok kapcsolatos gyakori kérdések
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: ''
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: chackdan
ms.openlocfilehash: ce88c8c4850e5226ddda12ce5ee0e1d18b51ea5c
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104082"
---
# <a name="commonly-asked-service-fabric-questions"></a>Gyakori kérdések a Service Fabric

Nincsenek számos összetevővel kapcsolatos gyakori kérdésekre mi a Service Fabric képességeit és hogyan kell használni. Ez a dokumentum ismerteti ezeket a gyakori kérdéseket és a válaszok.

## <a name="cluster-setup-and-management"></a>Fürt beállítása és kezelése

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Hogyan do I állítja vissza a Service Fabric-fürt tanúsítványt?

Az alkalmazásnak minden olyan frissítés szükséges visszagörgetése hibaészlelés állapotát a Service Fabric fürtök kvóruma számára a módosítás; véglegesítése előtt véglegesített módosítások csak előre lesz állítva. Eszkalációs mérnök a keresztül ügyfél-támogatási szolgálathoz, helyreállítani, szükség lehet, ha egy nem figyelt használhatatlanná tévő tanúsítvány változás fejlődéséből.  [Service Fabric-alkalmazás frissítése](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade?branch=master) vonatkozik [alkalmazásfrissítési paraméterek](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master), és nyújt nulla állásidő frissítési megtartva.  Az ajánlott alkalmazás a következő frissítési figyelt módot, állapot-ellenőrzések passzok, működés közbeni vissza automatikusan, ha egy alapértelmezett szolgáltatás frissítése nem sikerül típusán alapuló automatikus folyamat révén a frissítési tartományok.
 
Ha a fürt továbbra is kihasználva a klasszikus tanúsítvány-ujjlenyomat tulajdonság a Resource Manager-sablonban, azt javasoljuk, Ön [tanúsítvány ujjlenyomatát a fürt módosítása köznapi név](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn), használhatja a modern titkos kulcsok felügyeleti funkciókat.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Több Azure-régióban, vagy saját adatközpontok kiterjedő álló fürtöket tud létrehozni?

Igen. 

A core a Service Fabric-fürtözési technológia használható úgy, hogy bárhol a világon futtató gépek mindaddig, amíg egymáshoz hálózati kapcsolattal rendelkeznek. Ugyanakkor ilyen fürt készítése és bonyolult feladatnak bizonyulhat.

Ha érdekli, ebben a forgatókönyvben, azt javasoljuk, hogy az ügyfél beolvasása vagy keresztül a [Service Fabric GitHub Hibalistájában](https://github.com/azure/service-fabric-issues) vagy a támogatási képviselő további útmutatást a beszerzéséhez. A Service Fabric csapata dolgozik azon, hogy az egyértelműség érdekében további, útmutatást és javaslatokat ehhez a forgatókönyvhöz. 

Néhány mérlegelendő szempont ezzel kapcsolatban: 

1. Az Azure-ban a Service Fabric fürterőforrás regionális még ma, mivel a virtuálisgép-méretezési csoport beállítja, hogy a fürt épül. Ez azt jelenti, hogy egy regionális hiba esetén előfordulhat, hogy elveszíti képes kezelni a fürtöt az Azure Resource Manager vagy az Azure Portalon keresztül. Ez akkor fordulhat elő, annak ellenére, hogy a fürt továbbra is futni fog, és közvetlenül használni tudná. Emellett az Azure jelenleg nem használt összegeket egyetlen virtuális hálózattal, amely használható régióban vannak. Ez azt jelenti, hogy az Azure-ban egy többrégiós fürt igényel vagy [minden egyes virtuális gépéhez a Virtuálisgép-méretezési csoportok nyilvános IP-címek](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) vagy [Azure VPN Gateway átjárók](../vpn-gateway/vpn-gateway-about-vpngateways.md). A fenti hálózatkezelési lehetőségek költségek, teljesítmény, különböző hatásokkal rendelkezik, és bizonyos mértékben az alkalmazások kialakítását, hogy így alapos elemzése és tervezése előtt meg kell adni az ilyen környezet felállítását illeti.
2. A karbantartás, kezeléséhez, és ezek a gépek figyelését is bonyolultakká válnak, különösen akkor, ha átnyúlhatnak _típusok_ környezetek, például közötti érvénnyel a különböző felhőszolgáltatók, vagy a helyszíni erőforrások és az Azure között. Ügyelni kell arra, hogy frissítéseket, figyelés, felügyeleti és diagnosztikai értelmezni tud a fürt és az alkalmazások az ilyen környezetekben éles számítási feladatok futtatása előtt. Ha már rendelkezik tapasztalattal az Azure-ban vagy a saját az adatközpontokon belül problémák megoldásához, majd valószínű, hogy ezek azonos megoldásokat létrehozni, vagy a Service Fabric-fürtön futó is alkalmazható. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Hajtsa végre a Service Fabric-csomópont automatikusan jogosultak operációs rendszer frissítéseit?

Használhat [virtuális gép méretezési beállítása automatikus operációs rendszer lemezkép frissítési](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) általánosan elérhető a szolgáltatásban még ma.

Az Azure-ban nem futtató fürtök esetén van [alkalmazás megadott](service-fabric-patch-orchestration-application.md) javítása az operációs rendszerek, a Service Fabric-csomópont alá.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Használható a saját SF cluster nagyméretű virtuálisgép-méretezési csoportok? 

**Válasz rövid** – nem 

**Mennyi ideig választ** – Bár a nagyméretű virtuálisgép-méretezési csoportok lehetővé teszik a virtuális gép méretezése méretezési csoport legfeljebb 1000 Virtuálisgép-példányok, ezt úgy valósítja elhelyezési csoportra (PGs) használatát. Tartalék tartományok és frissítési tartományok (frissítési) is csak tartalék és frissítési tartománnyal elhelyezési döntéseket a szolgáltatáspéldányok replikák/szolgáltatás az elhelyezési csoport Service fabric használja belül konzisztensek. Mivel a tartalék és frissítési tartománnyal összehasonlítható csak az elhelyezési csoporton belül, az SF használható. Például, ha a PG1 VM1 FD-topológiája = 0, és a PG2 VM9 FD-topológiája = 4, ez nem jelenti azt, hogy a VM1, VM2 és a két különböző hardver állványokon, ezért SF nem használható az FD-értékek ebben az esetben elhelyezési döntéseket.

Jelenleg más nagyméretű virtuálisgép-méretezési csoportokkal kapcsolatos problémák, például a 4. szint hiánya betölteni a terheléselosztási támogatását. További [részletei a nagy méretezési csoportok](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Mi a Service Fabric-fürt minimális mérete? Miért nem lenne, kisebb?

A minimális támogatott számítási feladatok futtatása a Service Fabric-fürt mérete öt csomópontot. Fejlesztési forgatókönyvek esetén támogatott (optimalizált, gyors fejlesztési élményt biztosít a Visual Studióban) egy csomópontot, és 5 csomópontos fürt.

Szükség van egy éles fürtöt legalább 5 csomóponttal rendelkeznek, a következő három okok miatt:
1. Akkor is, ha nincs olyan szolgáltatást futtatja, a Service Fabric-fürt állapot-nyilvántartó rendszer szolgáltatások, többek között az elnevezési szolgáltatás és a Feladatátvevőfürt-kezelő szolgáltatás futtatja. E rendszerszolgáltatások nélkülözhetetlenek a fürt működőképes maradjon.
2. Egy replika csomópontonként, a szolgáltatás azt mindig helyezze el, így foglalásiegység-méret (valójában egy partíció) szolgáltatás rendelkezhet replikák számának felső határát.
3. Legalább egy csomópont le a fürtfrissítések tartalomtérkép érhető el, mivel azt szeretné, hogy legalább egy csomópont puffer, ezért szeretnénk egy fürt legalább két csomóponttal rendelkezik, *emellett* operációs minimális. Az operációs rendszer nélküli minimális érték kövesse az alábbi utasításokat rendszerszolgáltatás kvórum méretét.  

Szeretnénk, hogy a fürt két csomópont egyidejű meghibásodása esetén érhető el. A Service Fabric-fürtön elérhető legyen a rendszer szolgáltatások elérhetőnek kell lennie. Állapotalapú szolgáltatások, például elnevezési szolgáltatás és feladatátvételi kezelő szolgáltatás, nyomon követheti, mely szolgáltatások a fürtben lett telepítve, és ha azok még jelenleg üzemel, erős konzisztencia függenek. A konzisztenciát, függ beszerezni lehetővé teszi egy *kvórum* bármely azok állapotát az adott frissítés szolgáltatási, ahol a kvórum jelenti a szigorú többsége a replikák (N/2 + 1) egy adott szolgáltatáshoz. Így két csomópontot (így a rendszer szolgáltatás két replika egyidejű elvesztésével) egyidejű adatvesztés elleni rugalmas szeretnénk, ha azt kell rendelkeznie a fürt méretének nullánál - QuorumSize > = 2, amely arra kényszeríti a minimális méretét öt. Megtekintheti, fontolja meg a fürt rendelkezik N csomópontokat, és N replika, a rendszer szolgáltatás – egy minden egyes csomóponton. A kvórum rendszerszolgáltatás mérete (N/2 + 1). A fenti egyenlótlenség néz N - (N/2 + 1) > = 2. Két esetekben érdemes figyelembe venni: Ha N még akkor is, és ha N páratlan. Ha N még akkor is, például: N = 2\*m ahol m > = 1, 2 egyenlótlenség tűnik\*m - (2\*m/2 + 1) > 2-es vagy m = > = 3. Az n-re legalább 6, és ez mikor elért m = 3. Másrészről, ha N páratlan, mondjuk N = 2\*m + 1 where m > = 1, 2 egyenlótlenség tűnik\*m + 1 – ((2\*m + 1) / 2 + 1) > = 2-es vagy 2\*m + 1 – (m + 1) > 2-es vagy m = > = 2. Az n-re legalább 5, és ez mikor elért m = 2. Ezért között szereplő összes N, amelyek megfelelnek a fürt méretének nullánál - QuorumSize egyenlótlenség > = 2, a minimális érték 5.

Vegye figyelembe, a fenti argumentum azt rendelkezik feltételezi, hogy minden csomópont egy replikát a rendszer szolgáltatás, így a kvórum mérete számított a fürtben található csomópontok száma alapján. Azonban módosításával *TargetReplicaSetSize* azt teheti, hogy a kvórum mérete kisebb, mint (N / 2 + 1) amely előfordulhat, hogy adjon a benyomást, hogy mi sikerült fürt kisebb, mint 5 csomópontot, és továbbra is rendelkeznek 2 extra csomópont feletti a kvórum mérete. Például egy 4 csomópont fürtben elkészülünk a TargetReplicaSetSize 3, ha a kvórum TargetReplicaSetSize alapján mérete (3/2 + 1) vagy 2, így van a fürt méretének nullánál - QuorumSize = 4-2 > = 2. Azonban nem tudjuk garantálni, hogy a rendszer szolgáltatás lesz vagy újabb kvórum azt elvesztése minden virtuálisgép-pár csomópontok egyidejűleg, annak oka az lehet, hogy a két csomópont elveszítjük két replika is üzemeltető, így a rendszer szolgáltatás (csak egyetlen replika kellene balra) kvórum elvesztése lépnek egy ND nem lesz elérhető.

Végzett most vizsgálja meg néhány lehetséges fürtkonfigurációk:

**Egy csomópont**: Ez a beállítás nem biztosít magas rendelkezésre állású óta az egycsomópontos elvesztését bármilyen okból azt jelenti, hogy az egész fürt elvesztését.

**Két csomópont**: a két csomópontra telepített szolgáltatáshoz a kvórum (N = 2) a következő 2 (2/2 + 1 = 2). Egyetlen replika nem vesztek el, ha nem lehet létrehozni egy kvórum. Egy szolgáltatás frissítése szükséges ideiglenesen le egy replikát tart, ez a beállítás nem lehet hasznos.

**Három csomóponttal**: három csomóponttal (N = 3) a követelmény létrehozása egy kvórum, még két csomópont (3/2 + 1 = 2). Ez azt jelenti, hogy az egyes csomópontok elvesznek, és továbbra is a kvórum fenntartásához, de két csomópont egyidejű meghibásodása be kvórum elvesztése a helyrendszeri szolgáltatások hatékony felhasználhatóságot, és a fürt elérhetetlenné okoz.

**Négy csomópont**: négy csomópont (N = 4), hogy hozzon létre egy kvórum három csomóponttal (4/2 + 1 = 3). Ez azt jelenti, hogy az egyes csomópontok elvesznek, és továbbra is a kvórum fenntartásához, de két csomópont egyidejű meghibásodása be kvórum elvesztése a helyrendszeri szolgáltatások hatékony felhasználhatóságot, és a fürt elérhetetlenné okoz.

**Öt csomópont**: öt csomóponttal (N = 5), a követelmény létrehozása egy kvórum, továbbra is három csomóponttal (5/2 + 1 = 3). Ez azt jelenti, hogy egy időben két csomópont elvesznek, és továbbra is a rendszerszolgáltatások a kvórum fenntartásához.

Az éles számítási feladatokhoz, rugalmasnak kell lennie (például egy fürt frissítése, egy más okok miatt), legalább két csomópont egyidejű meghibásodása, így öt csomópont szükség.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Ki lehet kapcsolni a fürt, éjszaka és Hétvége költségeit?

Általában nem. A Service Fabric tárolja állapot helyi, rövid élettartamú lemezeken, ami azt jelenti, hogy a virtuális gép áthelyezését egy másik gazdagépre, ha az adatok nem helyezi át a vele. A normál működés ez nem probléma, az új csomópont más csomópontok naprakész állapotba. Azonban ha állítsa le az összes csomóponton, és később újraindíthatja, nincs jelentős lehetséges, hogy a csomópontok a legtöbb indítsa el az új gazdagépek számára, ellenőrizze a rendszer nem lehet helyreállítani.

Ha szeretné tesztelni az alkalmazását, mielőtt telepítené a fürtök létrehozása, javasoljuk, hogy dinamikusan hozzon létre ezen a fürtök részeként a [folyamatos integráció/folyamatos üzembe helyezési folyamat](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Hogyan frissíthetem az operációs rendszer (például a Windows Server 2012, Windows Server 2016-ra)?

Még dolgozunk egy továbbfejlesztett még ma, miközben Ön felelős a frissítést. Frissítenie kell az operációsrendszer-képet a virtuális gépek a fürt egy virtuális gép egyszerre. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Egy fürtcsomóponttípus (virtuálisgép-méretezési) a csatlakoztatott adatlemezekkel is titkosítani?
Igen.  További információkért lásd: [-fürt létrehozása csatlakoztatott adatlemezekkel rendelkező](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks), [(PowerShell) lemezek titkosítása](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md), és [(CLI) lemezek titkosítása](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-cli.md).

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Használható egy fürtcsomóponttípus (virtuálisgép-méretezési) az alacsony prioritású virtuális gépek?
Nem. Alacsony prioritású virtuális gépek nem támogatottak. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Mik azok a könyvtárak és folyamatokat kizárhat, amikor egy víruskereső program futtatása a fürtben kell?

| **A víruskereső kizárt könyvtárak** |
| --- |
| Program Files\Microsoft a Service Fabric |
| FabricDataRoot (a fürt konfiguráció) |
| FabricLogRoot (a fürt konfiguráció) |

| **A víruskereső kizárt folyamatok** |
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
 
### <a name="how-can-my-application-authenticate-to-keyvault-to-get-secrets"></a>Hogyan hitelesítheti az alkalmazást a KeyVault titkos kódok lekéréséhez?
Azt jelenti, hogy az alkalmazás hitelesítéséhez a KeyVault hitelesítő adatok beszerzése a következők:

A. Az alkalmazások létrehozási/csomagolási feladat során kérje le a tanúsítványt az SF alkalmazás adatok csomagba, és használja ezt a KeyVault hitelesítésre.
B. Virtuálisgép-méretezési csoport beállítása engedélyezve van az MSI-gazdagépek, egy egyszerű PowerShell SetupEntryPoint SF alkalmazásához be is fejleszthet [egy hozzáférési jogkivonatot az MSI-végpontról](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token), majd [beolvasni a titkos kulcsokat a KeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Get-AzureKeyVaultSecret?view=azurermps-6.5.0)

## <a name="application-design"></a>Alkalmazás-tervezés

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Mi az a legjobb módszer használatával adatokat lekérdezni, egy megbízható gyűjteményben partíciók között?

A Reliable collections jellemzően [particionált](service-fabric-concepts-partitioning.md) horizontális felskálázás nagyobb teljesítményt és az átviteli sebesség engedélyezéséhez. Ez azt jelenti, hogy az adott szolgáltatás állapota tíz vagy több száz gépek között előfordulhat, hogy lehetnek elosztva. A teljes adatkészletet keresztül műveletek végrehajtásához néhány lehetőségek állnak rendelkezésére:

- Létrehoz egy szolgáltatást, amely használatával a szükséges adatok kérhetők le egy másik szolgáltatás összes partíció.
- Hozzon létre egy szolgáltatás, amely egy másik szolgáltatás összes partíció fogadhat adatokat.
- Rendszeres időközönként adatok leküldése az egyes szolgáltatások egy külső tároló. Ez a módszer csak akkor megfelelő, ha a lekérdezéseket hajt végre nem részei az alapvető üzleti logikát.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Mi az a legjobb módszer használatával adatokat lekérdezni az actors között?

Actors tervezték független egység állapota és a számítási erőforrásokat, ezért nem javasoljuk, hogy az aktorok állapotának futásidőben széles körű lekérdezéseket. Ha nincs szüksége a lekérdezés aktorállapot teljes szétosztva, érdemes vagy:

- Az aktor szolgáltatások cserélje le a stateful reliable services úgy, hogy a több hálózati kérések actors száma a szolgáltatás a partíciók száma az összes adatainak gyűjtésére.
- Az aktorok rendszeres időközönként le egy külső tároló egyszerűbb az állapotuk tervez. A fenti Ez a módszer csak akkor megvalósítható, ha a lekérdezéseket hajt végre nem szükségesek a működését.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Mennyi adatot tárolhatok a Reliable Collection?

A Reliable services általában particionáltak, így a tárolhatja csak korlátozza a fürtben található gépek száma, és ezeken a gépeken rendelkezésre álló memória mennyisége.

Például tegyük fel, hogy egy megbízható gyűjteményben 100 partíció és 3 replika egy szolgáltatásban átlagos 1 kb méretű objektumok tárolására. Most tegyük fel, hogy egy 16gb memóriája gépenként 10 gép fürtöt. Az egyszerűség és a óvatosan, feltételeztük, hogy az operációs rendszer és a helyrendszeri szolgáltatások, a Service Fabric-futtatókörnyezet és a szolgáltatások felhasználásához, 6gb, 10 GB-os gépenként érhető el, vagy 100 GB-os és a fürt.

Vegye figyelembe, hogy minden objektumnak kell lennie tartja tárolt három időpontokban (egy elsődleges és két replika), hogy körülbelül 35 millió objektumok számára elegendő memória a gyűjtemények teljes kapacitás üzemi. Azt javasoljuk azonban, egy hiba tartomány és a egy frissítési tartományt, amely körülbelül 1/3 kapacitás képvisel, és hány csökkentené nagyjából 23 millió egyidejű elvesztése folyamatban.

Vegye figyelembe, hogy a számítás is feltételezi, hogy:

- Hogy az adatok a partíciók közt a terjesztési nagyjából egységes vagy, hogy Ön már reporting terhelési mérőszámok a fürt Resource Manager. Alapértelmezés szerint a Service Fabric betölti egyenleg replika száma alapján. Az előző példában, amely célszerű 10 elsődleges replika és másodlagos replikák 20 helyezni a fürt minden csomópontján. Amely jól terhelés egyenletesen oszlik el a partíciók között működik. Ha terhelés nem még akkor is, jelentenie kell terhelés, hogy az erőforrás-kezelő is együtt csomag kisebb replikákat, és nagyobb replikák több memóriát az egyes csomóponton engedélyezi.

- Hogy a szóban forgó reliable Services a fürtben csak egy tárolását állapota. Mivel több szolgáltatást is üzembe helyezhet egy fürthöz, fordítson erőforrást kell, hogy minden egyes kell futtatni és felügyelni az állapotában.

- Hogy a fürt, másrészt nem növekvő vagy zsugorítását. Ha további gépeket ad hozzá, a Service Fabric fog újraegyensúlyozására kihasználhatja a megnövelt kapacitás, amíg azon gépek száma, mivel az egyes replika nem terjedhetnek ki gépeket teret hagy a szolgáltatáshoz, a partíciók száma a replikákat. Ezzel szemben az gépek törlésével csökkentse a fürt méretét, ha a replikák nagyobb mértékben vannak-e csomagolt és kevésbé kihasznált teljes kapacitás rendelkezik.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Mennyi adatot tárolhatok a egy szereplő?

Akárcsak a reliable services actor-szolgáltatások is tárolt adatok mennyisége csak korlátozza a teljes lemezterület és a rendelkezésre álló memória a fürtben található csomópontok között. Azonban az egyes actors tárolják az állapot és a kapcsolódó üzleti logika kisebb mennyiségű használatakor is leghatékonyabb. Általános szabály egy egyéni szereplő kilobájtban mért állapotban kell rendelkeznie.

## <a name="other-questions"></a>Egyéb kérdések

### <a name="how-does-service-fabric-relate-to-containers"></a>Hogyan kapcsolódnak a Service Fabric tárolók?

Tárolók csomag szolgáltatások és azok függőségeit egyszerű módszert kínálnak, úgy, hogy az összes környezetben konzisztens módon futtassa, és a egy egyetlen gépen elkülönített módon működhet. A Service Fabric egy telepíthetnek vagy kezelhetnek szolgáltatásokat, beleértve a megoldást kínál a [szolgáltatásokat, amelyek rendelkeznek egy tárolóban van csomagolva](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Tervezi a nyílt forráskódú Service Fabric?

A Service Fabric nyílt forráskódú részeit van ([a reliable services-keretrendszer](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [a reliable actors-keretrendszer](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [ASP.NET Core-integráció kódtárak](https://github.com/Azure/service-fabric-aspnetcore), [ A Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer), és [Service Fabric parancssori felület](https://github.com/Azure/service-fabric-cli)) a Githubon, és fogadja el azokat a projekteket, a közösségi közreműködést. 

Hogy [megtudhat](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/) , hogy tervezzük nyílt forráskódú Service Fabric-futtatókörnyezet. Rendelkezünk ezen a ponton a [Service Fabric-adattárat](https://github.com/Microsoft/service-fabric/) akár a Linux és a Githubon összeállításához és teszteléséhez eszközökkel, ami azt jelenti, hogy akkor is klónozza az adattárat, hozhat létre a Service Fabric Linux-, egyszerű tesztek futtatása, nyissa meg a problémákat és pull-kérelmek elküldéséhez. Dolgozunk a Windows-összeállító környezetet át, és teljes CI-környezet beolvasásához.

Kövesse a [Service Fabric blog](https://blogs.msdn.microsoft.com/azureservicefabric/) vagyunk bejelentésnek további részletekért.

## <a name="next-steps"></a>További lépések

- [A Service Fabric alapfogalmait és ajánlott eljárások ismertetése](https://mva.microsoft.com/en-us/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965)
