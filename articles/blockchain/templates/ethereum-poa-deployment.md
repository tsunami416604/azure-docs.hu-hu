---
title: Az Ethereum proof-of-Authority konzorciumi megoldássablonjának üzembe helyezése az Azure-ban
description: Az Ethereum Proof-of-Authority konzorciumi megoldás sal telepíthet és konfigurálhat egy többtagú konzorciumi Ethereum-hálózatot az Azure-ban
ms.date: 12/18/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: 7e9af5c501b58f6828360ee280440ea85698bf16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75387501"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Az Ethereum proof-of-authority konzorciumi megoldássablontelepítése az Azure-ban

Az [Ethereum Proof-of-Authority Consortium előzetes Azure-megoldássablonnal](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) üzembe helyezheti, konfigurálhatja és szabályozhatja a többtagú konzorciumi ethereum-i hálózatot minimális Azure- és Ethereum-ismeretekkel.

A megoldássablont minden konzorciumtag használhatja egy blokklánc hálózati lábnyom kiépítéséhez a Microsoft Azure számítási, hálózati és tárolási szolgáltatásainak használatával. Minden konzorciumtag hálózati lábnyoma egy terheléselosztásos érvényesítő csomópontkészletből áll, amelyekkel egy alkalmazás vagy felhasználó kapcsolatba léphet az Ethereum-tranzakciók elküldéséhez.

## <a name="choose-an-azure-blockchain-solution"></a>Válasszon azure blockchain megoldást

Mielőtt az Ethereum proof-of-authority konzorciumi megoldássablon t, összehasonlíthatja a forgatókönyvet a rendelkezésre álló Azure Blockchain-beállítások gyakori használati eseteivel.

Beállítás | Szolgáltatásmodell | Általános használati eset
-------|---------------|-----------------
Megoldássablonok | IaaS | A megoldássablonok olyan Azure Resource Manager-sablonok, amelyek segítségével egy teljesen konfigurált blockchain hálózati topológiát hozhat létre. A sablonok telepítik és konfigurálják a Microsoft Azure számítási, hálózati és tárolási szolgáltatásait egy adott blokklánc-hálózati típushoz.
[Azure Blockchain Service](../service/overview.md) | PaaS | Az Azure Blockchain Service Preview leegyszerűsíti a konzorciumi blokklánc-hálózatok kialakítását, kezelését és irányítását. Használja az Azure Blockchain szolgáltatást a PaaS-t, konzorciumkezelést vagy szerződés- és tranzakciós adatvédelmet igénylő megoldásokhoz.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS és PaaS | Az Azure Blockchain Workbench Preview az Azure-szolgáltatások és képességek gyűjteménye, amelyek segítségével hozhat létre és helyezhet üzembe blokklánc-alkalmazásokat üzleti folyamatok és adatok más szervezetekkel való megosztásához. Az Azure Blockchain Workbench használatával blockchain-megoldás vagy blockchain alkalmazáskoncepció-igazolást hoz.

## <a name="solution-architecture"></a>Megoldásarchitektúra

Az Ethereum megoldássablon használatával egyetlen vagy többrégiós többrégiós Ethereum proof-of-authority konzorciumi hálózatot telepíthet.

![telepítési architektúra](./media/ethereum-poa-deployment/deployment-architecture.png)

Minden konzorciumi tag telepítése a következőket tartalmazza:

* Virtuális gépek a PoA-érvényesítőket futtató gépek számára
* Azure Load Balancer RPC, társviszony-létesítési és cégirányítási DApp-kérelmek terjesztéséhez
* Azure Key Vault a validator identitások védelméhez
* Azure Storage állandó hálózati információk tárolásához és a lízing koordinálásához
* Azure Monitor a naplók és a teljesítménystatisztikák összesítéséhez
* Virtuálishálózat-átjáró (nem kötelező) a VPN-kapcsolatok privát virtuális hálózatok közötti engedélyezéséhez

Alapértelmezés szerint az RPC és a társviszony-létesítési végpontok érhetők el a nyilvános IP-cím, amely lehetővé teszi az egyszerűsített kapcsolat az előfizetések és a felhők között. Alkalmazásszintű hozzáférés-vezérlésesetén [használhatja a Paritás engedélyezési szerződéseit.](https://wiki.parity.io/Permissioning) A VPN-ek mögött telepített hálózatok, amelyek a virtuális hálózatok átjáróit használják az előfizetések közötti kapcsolathoz, támogatottak. Mivel a VPN- és virtuálishálózat-telepítések összetettebbek, érdemes lehet egy nyilvános IP-modellel kezdeni egy megoldás prototípusakor.

A Docker-tárolók megbízhatóságra és modularitusra szolgálnak. Az Azure Container Registry az egyes központi telepítések részeként verziózott lemezképek üzemeltetésére és kiszolgálására szolgál. A tárolóképek a következőkből állnak:

* Orchestrator – identitások és cégirányítási szerződések létrehozása. Identitások tárolása egy identitástárolóban.
* Paritásügyfél – identitás tanév bérletek az identitástárolóból. Felfedezi és összeköti a társakat.
* EthStats Agent – helyi naplók és statisztikák gyűjtése RPC-n keresztül, és leküldéses információkat az Azure Monitor.
* Cégirányítási DApp – Webes felület a cégirányítási szerződésekkel való interakcióhoz.

### <a name="validator-nodes"></a>Validator csomópontok

A proof-of-authority protokollban a validator csomópontok a hagyományos bányászcsomópontok helyét veszik át. Minden validator egyedi Ethereum identitással rendelkezik, amely lehetővé teszi, hogy részt vegyen a blokk létrehozási folyamatban. Minden konzorciumtag két vagy több érvényesítő csomópontot hozhat létre öt régióban, a földrajzi redundancia érdekében. A validátorcsomópontok más validátor-csomópontokkal kommunikálnak, hogy konszenzusra jussanak az alapul szolgáló elosztott főkönyv állapotáról. A hálózaton való tisztességes részvétel biztosítása érdekében minden konzorciumi tag számára tilos a hálózat első tagjánál érvényesebb érvényesítőket használni. Ha például az első tag három validátort telepít, minden tagnak legfeljebb három validátora lehet.

### <a name="identity-store"></a>Identitástároló

Az identitástároló minden tag előfizetésében üzembe helyezésre kerül, amely biztonságosan tárolja a létrehozott Ethereum-identitásokat. Minden egyes validator a vezénylési tároló létrehoz egy Ethereum személyes kulcsot, és tárolja az Azure Key Vaultban.

## <a name="deploy-ethereum-consortium-network"></a>Az Ethereum konzorciumi hálózat telepítése

Ebben a séta, tegyük fel, hogy létrehoz egy többpárti Ethereum konzorcium hálózat. A következő folyamat egy példa egy többrésztvevős telepítésre:

1. Három tag mindegyike létrehoz egy Ethereum fiókot a MetaMask használatával
1. *"A" tag* beveti az Ethereum PoA-t, megadva ethereumi nyilvános beszédét
1. *"A" tag* adja meg a konzorcium URL-jét *"B"* és *"C" tag*
1. *"B" és* *"C" tag* telepítése, Ethereum PoA, amely megadja az Ethereum nyilvános címét és *az A tag*konzorciumának URL-címét
1. *"A" tag* a *B tagban* rendszergazdaként szavaz
1. *"A" és* *"B" képviselő* egyaránt c *képviselőre* szavaz, mint

A következő szakaszok bemutatják, hogyan konfigurálhatja az első tag lábnyomát a hálózatban.

### <a name="create-resource"></a>Erőforrás létrehozása

Az [Azure Portalon](https://portal.azure.com)válassza **az erőforrás létrehozása** a bal felső sarokban.

Válassza a **Blockchain** > **Ethereum Proof-of-Authority Consortium (előzetes verzió) lehetőséget.**

### <a name="basics"></a>Alapvető beállítások

Az **Alapjai csoportban**adja meg a szabványos paraméterek értékeit bármely központi telepítéshez.

![Alapvető beállítások](./media/ethereum-poa-deployment/basic-blade.png)

Paraméter | Leírás | Példaérték
----------|-------------|--------------
Új hálózat létrehozása vagy meglévő hálózathoz való csatlakozás | Létrehozhat egy új konzorciumi hálózatot, vagy csatlakozhat egy már meglévő konzorciumi hálózathoz. A meglévő hálózathoz való csatlakozás további paramétereket igényel. | Új létrehozása
E-mail-cím | E-mailben értesítést kap, amikor a központi telepítés befejeződik a központi telepítéssel kapcsolatos információkkal. | Érvényes e-mail cím
Virtuális gép felhasználóneve | Az egyes üzembe helyezett virtuális gépek rendszergazdai felhasználóneve | 1-64 alfanumerikus karakter
Hitelesítés típusa | A virtuális gépen hitelesíthető metódus. | Jelszó
Jelszó | A rendszergazdai fiók jelszava az egyes telepített virtuális gépekhez. Kezdetben minden virtuális gép ugyanazt a jelszót. A jelszó a kiépítés után módosítható. | 12-72 karakter 
Előfizetés | A konzorciumi hálózat üzembe helyezéséhez szükséges előfizetés |
Erőforráscsoport| Az az erőforráscsoport, amelyre a konzorciumi hálózatot telepíteni szeretné. | myResourceGroup
Hely | Az Azure-régió erőforráscsoporthoz. | USA nyugati régiója, 2.

Válassza **az OK gombot.**

### <a name="deployment-regions"></a>Telepítési régiók

A *Telepítési régiók csoportban*adja meg az egyes régiók és helyek számát. Legfeljebb öt régióban telepíthető. Az első régiónak meg kell egyeznie az erőforráscsoport helyével *az Alapjak* szakaszból. A fejlesztési vagy tesztelési hálózatok, tagonként egy régiót használhat. Éles környezetben két vagy több régióban is üzembe helyezheti a magas rendelkezésre állás érdekében.

![telepítési régiók](./media/ethereum-poa-deployment/deployment-regions.png)

Paraméter | Leírás | Példaérték
----------|-------------|--------------
Régió(k) száma|A konzorciumi hálózat ot üzembe helyező régiók száma| 2
Első régió | A konzorciumi hálózat első üzembe helyezésére irányuló régió | USA nyugati régiója, 2.
Második régió | A konzorciumi hálózat üzembe helyezésének második régiója. További régiók akkor láthatók, ha a régiók száma kettő vagy nagyobb. | USA 2. keleti régiója

Válassza **az OK gombot.**

### <a name="network-size-and-performance"></a>A hálózat mérete és teljesítménye

A *Hálózat mérete és teljesítménye*csoportban adja meg a konzorciumi hálózat méretének bemeneteit. A validator csomópont tárolási mérete határozza meg a blokklánc lehetséges méretét. A méret az üzembe helyezés után módosítható.

![A hálózat mérete és teljesítménye](./media/ethereum-poa-deployment/network-size-and-performance.png)

Paraméter | Leírás | Példaérték
----------|-------------|--------------
A terheléselosztásos érvényesítő csomópontok száma | A hálózat részeként üzembe helyezhető érvényesítő csomópontok száma. | 2
Validator csomópont tárolási teljesítmény | Az egyes üzembe helyezett validátor-csomópontok felügyelt lemezének típusa. Az árképzésről a [tárolási díjszabásban](https://azure.microsoft.com/pricing/details/managed-disks/) talál további részleteket. | Standard SSD
Validator csomópont virtuális gép mérete | A validator csomópontokhoz használt virtuális gép mérete. Az árképzésről a [virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | Szabványos D2 v3

A virtuális gép és a tárolási szint befolyásolja a hálózati teljesítményt.  Az alábbi táblázat segítségével választhatja ki a költséghatékonyságot:

Virtuális gép termékváltozatának|Tárolási szint|Price|Teljesítmény|Késés
---|---|---|---|---
F1|Standard SSD|Alacsony|Alacsony|magas
D2_v3|Standard SSD|közepes|közepes|közepes
F16-osok|Prémium SSD|magas|magas|Alacsony

Válassza **az OK gombot.**

### <a name="ethereum-settings"></a>Ethereum beállításai

Az *Ethereum-beállítások csoportban*adja meg az Ethereumhoz kapcsolódó konfigurációs beállításokat.

![Ethereum beállításai](./media/ethereum-poa-deployment/ethereum-settings.png)

Paraméter | Leírás | Példaérték
----------|-------------|--------------
Konzorciumi tag azonosítója | A konzorciumi hálózatban részt vevő minden egyes taghoz társított azonosító. Az IP-címterek konfigurálására szolgál az ütközés elkerülése érdekében. Magánhálózat esetén a tagazonosítónak egyedinek kell lennie az ugyanazon hálózat különböző szervezetei között.  Egy egyedi tagazonosítóra akkor is szükség van, ha ugyanaz a szervezet több régióban is üzembe helyezi. Jegyezze fel ennek a paraméternek az értékét, mivel meg kell osztania más csatlakozó tagokkal, hogy megbizonyosodjon arról, hogy nincs ütközés. Az érvényes tartomány 0 és 255 között van. | 0
Hálózati azonosító | A központi telepítés alatt álló konzorcium Ethereum hálózathálózati azonosítója. Minden Ethereum-hálózat saját hálózati azonosítóval rendelkezik, és az 1 a nyilvános hálózat azonosítója. Az érvényes tartomány 5 és 999 999 999 999 | 10101010
Admin Ethereum cím | A PoA-irányításban való részvételhez használt Ethereum-fiókcím. A MetaMask segítségével Ethereum-címet hozhat létre. |
Speciális beállítások | Az Ethereum beállításainak speciális beállításai | Bekapcsolás
Telepítés nyilvános IP-cím használatával | Ha a privát virtuális hálózat van kiválasztva, a hálózat egy Virtuálishálózati átjáró mögött van telepítve, és eltávolítja a társviszony-létesítési hozzáférést. A privát virtuális hálózat esetén minden tagnak virtuális hálózati átjárót kell használnia ahhoz, hogy a kapcsolat kompatibilis legyen. | Nyilvános IP-cím
Blokk gáz határérték | A hálózat kezdőblokkgáz-határértéke. | 50000000
Blokk visszazárási időszak (mp) | Az üres blokkok létrehozásának gyakorisága, ha nincsenek tranzakciók a hálózaton. A magasabb frekvenciának gyorsabb a véglegessége, de megnő a tárolási költség. | 15
Tranzakcióengedély-egyezmény | Bytecode a tranzakcióengedélyezési szerződéshez. Az intelligens szerződések telepítését és végrehajtását az Ethereum-fiókok engedélyezett listájára korlátozza. |

Válassza **az OK gombot.**

### <a name="monitoring"></a>Figyelés

A figyelés lehetővé teszi a hálózat naplóerőforrásának konfigurálását. A figyelőügynök hasznos metrikákat és naplókat gyűjt és surfaceez a hálózatról, így gyorsan ellenőrizheti a hálózat állapotát vagy hibakeresési problémákat.

![Azure-figyelő](./media/ethereum-poa-deployment/azure-monitor.png)

Paraméter | Leírás | Példaérték
----------|-------------|--------------
Figyelés | Lehetőség a figyelés engedélyezésére | Bekapcsolás
Csatlakozás meglévő Azure-figyelőnaplókhoz | Új Azure Monitor-naplópéldány létrehozásának vagy meglévő példányhoz való csatlakozásának lehetősége | Új létrehozása
Hely | Az a régió, ahol az új példány telepítve van | USA keleti régiója
Meglévő naplóelemzési munkaterület-azonosító (Csatlakozás meglévő Azure-figyelőnaplókhoz = Csatlakozás meglévőhez)|A meglévő Azure Monitor-naplók példányának munkaterület-azonosítója||NA
Meglévő loganalytics elsődleges kulcs (Csatlakozás meglévő Azure Monitor naplókhoz = Csatlakozás meglévőhez)|A meglévő Azure Monitor-naplók hoz való csatlakozáshoz használt elsődleges kulcs||NA

Válassza **az OK gombot.**

### <a name="summary"></a>Összefoglalás

Kattintson az összegzésre a megadott bemenetek áttekintéséhez, és futtassa az alapvető üzembe helyezés előtti érvényesítést. A telepítés előtt letöltheti a sablont és a paramétereket.

A telepítéshez válassza a **Létrehozás** gombot.

Ha a központi telepítés virtuális hálózati átjárókat tartalmaz, a központi telepítés 45–50 percet is igénybe vehet.

## <a name="deployment-output"></a>Központi telepítés kimenete

Miután a központi telepítés befejeződött, az Azure Portal használatával elérheti a szükséges paramétereket.

### <a name="confirmation-email"></a>Visszaigazoló e-mail

Ha megad egy e-mail címet ([Basics section](#basics)), egy e-mailt küld, amely tartalmazza a telepítési információkat és a dokumentációra mutató hivatkozásokat.

![központi e-mail](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portál

Miután a központi telepítés sikeresen befejeződött, és az összes erőforrás kivan építve, megtekintheti a kimeneti paramétereket az erőforráscsoportban.

1. Nyissa meg az erőforráscsoportot a portálon.
1. Válassza **az Áttekintés > a központi telepítések lehetőséget.**

    ![Erőforráscsoport – áttekintés](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Válassza ki a **microsoft-azure-blockchain.azure-blockchain-ether-...** üzembe helyezést.
1. Jelölje ki a **Kimenetek szakaszt.**

    ![Központi telepítési kimenetek](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>A konzorcium növekedése

A konzorcium bővítéséhez először csatlakoztatnia kell a fizikai hálózatot. Ha vpn mögött telepíti a rendszert, olvassa el a [Virtuálishálózati átjáró csatlakoztatása](#connecting-vnet-gateways) a hálózati kapcsolat konfigurálása az új tag központi telepítésének részeként című szakaszt. Miután a központi telepítés befejeződött, használja a [cégirányítási dapp](#governance-dapp) hálózati rendszergazdaként.

### <a name="new-member-deployment"></a>Új tag telepítése

Ossza meg a következő információkat a csatlakozó taggal. Az információ a telepítés utáni e-mailben vagy a portál központi telepítési kimenetében található.

* Konzorciumi adatok URL-címe
* A telepített csomópontok száma
* VNet átjáróerőforrás-azonosító (VPN használata esetén)

A üzembe helyező tagnak ugyanazt az Ethereum Proof-of-Authority konzorciumi megoldássablont kell használnia a hálózati jelenlét ük üzembe helyezésekor az alábbi útmutatás alkalmazásával:

* Válassza **az Összekapcsolódás meglévő lehetőséget**
* A tisztességes képviselet biztosítása érdekében ugyanannyi validator csomópontot válasszon, mint a hálózat többi tagja.
* Ugyanazt az Admin Ethereum-címet használja
* A megadott *konzorciumi adatURL használata* az *Ethereum-beállításokban*
* Ha a hálózat többi része VPN mögött van, válassza a **Privát virtuális hálózat** lehetőséget a speciális szakaszban.

### <a name="connecting-vnet-gateways"></a>Virtuálishálózati átjárók csatlakoztatása

Ez a szakasz csak akkor szükséges, ha egy privát virtuális hálózat használatával telepített. Ezt a szakaszt kihagyhatja, ha nyilvános IP-címeket használ.

Magánhálózat esetén a különböző tagok virtuális hálózati átjárókapcsolatokon keresztül csatlakoznak. Ahhoz, hogy egy tag csatlakozna a hálózathoz, és láthassa a tranzakciós forgalmat, a meglévő tagnak végleges konfigurációt kell végeznie a VPN-átjárón a kapcsolat elfogadásához. A csatlakozó tag Ethereum-csomópontjai nem futnak, amíg létre nem jön a kapcsolat. Egyetlen meghibásodási pont esélyének csökkentése érdekében hozzon létre redundáns hálózati kapcsolatokat a konzorciumban.

Az új tag telepítése után a meglévő tagnak létre kell adnia a kétirányú kapcsolatot az új taggal létesített virtuális hálózati átjárókapcsolattal. A meglévő tagnak szüksége van:

* A csatlakozó tag virtuális hálózati átjáró erőforrás-azonosítója. Lásd [a központi telepítés kimenetét](#deployment-output).
* A megosztott kapcsolat kulcsa.

A meglévő tagnak a következő PowerShell-parancsfájlt kell futtatnia a kapcsolat befejezéséhez. Használhatja az Azure Cloud Shell található a jobb felső navigációs sáv a portálon.

![felhő héj](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="service-monitoring"></a>Szolgáltatásfigyelés

Az Azure Monitor-portált a központi telepítési e-mailben található hivatkozás tésével vagy a központi telepítési kimenetben a paraméter megkeresésével [OMS_PORTAL_URL] keresheti meg.

A portál először magas szintű hálózati statisztikákat és csomópontáttekintést jelenít meg.

![Monitor kategóriák](./media/ethereum-poa-deployment/monitor-categories.png)

A **Csomópont áttekintése** lehetőséget választva a csomópontonkénti infrastruktúra statisztikái jelennek meg.

![Csomópontstatisztika](./media/ethereum-poa-deployment/node-stats.png)

A **Hálózati statisztika** kiválasztása az Ethereum hálózati statisztikáit jeleníti meg.

![Hálózati statisztikák](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>Minta Kusto-lekérdezések

Lekérdezheti a figyelési naplókhibák kivizsgálásához vagy a beállítási küszöbérték riasztási. A következő lekérdezések a *Naplókereső* eszközben futtathatók példák:

Az egynél több validátor-lekérdezés által jelentett listablokkok hasznosak lehetnek a láncvillák megkereséséhez.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

Egy adott validator csomópont átlagosan 5 perces gyűjtők átlagos társszáma.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

## <a name="ssh-access"></a>SSH-hozzáférés

Biztonsági okokból az SSH-porthoz való hozzáférést alapértelmezés szerint egy hálózati csoport biztonsági szabálya tagadja meg. A virtuálisgép-példányok eléréséhez a PoA-hálózatban, meg kell változtatni a következő biztonsági szabály *engedélyezése*.

1. Nyissa meg az Azure Portalon üzembe helyezett erőforráscsoport **áttekintése** szakaszát.

    ![ssh áttekintés](./media/ethereum-poa-deployment/ssh-overview.png)

1. Válassza ki a **hálózati biztonsági csoportot** a virtuális gép azon régiójához, amelyet el szeretne érni.

    ![ssh nsg](./media/ethereum-poa-deployment/ssh-nsg.png)

1. Jelölje ki az **allow-ssh** szabályt.

    ![ssh-allow](./media/ethereum-poa-deployment/ssh-allow.png)

1. **A művelet** módosítása **az Engedélyezéshez**

    ![ssh engedélyezése](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. Kattintson a **Mentés** gombra. A módosítások alkalmazása eltarthat néhány percig.

A validator-csomópontok virtuális gépeihez az SSH-n keresztül távolról csatlakozhat a megadott rendszergazdai felhasználónévvel és jelszóval/SSH-kulccsal. Az első validator-csomópont elérésére vonatkozó SSH parancs megjelenik a sablon központi telepítési kimenetében. Példa:

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

További tranzakciócsomópontok eléréséhez a portszámát egyenként kell megszámozni.

Ha egynél több régióban telepített, módosítsa a parancsot az adott régió terheléselosztójának DNS-nevére vagy IP-címére. A többi régió DNS-nevének vagy IP-címének megkereséséhez keresse meg az ** \* \* \* \* \*-lbpip-reg\# ** elnevezési konvencióval rendelkező erőforrást, és tekintse meg a DNS-nevét és IP-címtulajdonságait.

## <a name="azure-traffic-manager-load-balancing"></a>Az Azure Traffic Manager terheléselosztása

Az Azure Traffic Manager segítségével csökkentheti az állásidőt, és javíthatja a PoA-hálózat válaszképességét azáltal, hogy a bejövő forgalmat több központi telepítés között irányítja a különböző régiókban. A beépített állapotellenőrzések és az automatikus átirányítás segít biztosítani az RPC-végpontok és a cégirányítási dapp magas rendelkezésre állását. Ez a funkció akkor hasznos, ha több régióban telepített, és éles használatra kész.

A Traffic Manager segítségével javíthatja a PoA-hálózat rendelkezésre állását az automatikus feladatátvételsel. A Traffic Manager segítségével is növelheti a hálózatok válaszképességét azáltal, hogy a végfelhasználókat az Azure-helyre a legalacsonyabb hálózati késéssel irányítja.

Ha úgy dönt, hogy létrehoz egy Traffic Manager-profilt, a profil DNS-nevét használhatja a hálózat eléréséhez. Miután más konzorciumtagok at adtak hozzá a hálózathoz, a Traffic Manager is használható a terheléselosztás az üzembe helyezett validátorok között.

### <a name="creating-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

1. Az [Azure Portalon](https://portal.azure.com)válassza **az erőforrás létrehozása** a bal felső sarokban.
1. A **Traffic Manager-profil**keresése.

    ![Az Azure Traffic Manager keresése](./media/ethereum-poa-deployment/traffic-manager-search.png)

    Adjon a profilnak egyedi nevet, és válassza ki a felhasználói erőforrás telepítéséhez használt erőforráscsoportot.

1. A telepítéshez válassza a **Létrehozás** gombot.

    ![Traffic Manager létrehozása](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. Üzembe helyezés után válassza ki a példányt az erőforráscsoportban. A forgalomkezelő eléréséhez vezető DNS-név az Áttekintés lapon található.

    ![A forgalomkezelő DNS megkeresése](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. Válassza a **Végpontok** lapot, és válassza a **Hozzáadás** gombot.
1. Adjon a végpontnak egyedi nevet.
1. A **Cél erőforrástípus mezőben**válassza **a Nyilvános IP-cím**lehetőséget.
1. Válassza ki az első régió terheléselosztójának nyilvános IP-címét.

    ![Forgalomkezelő](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Ismételje meg a műveletet az üzembe helyezett hálózat minden régiójában. Ha a végpontok az **engedélyezett** állapotban vannak, azok automatikusan betöltődnek, és a régió egyensúlyban lesz a forgalomkezelő DNS-nevével. Most már használhatja ezt a DNS-nevet a [CONSORTIUM_DATA_URL] paraméter helyett a cikk más lépéseiben.

## <a name="data-api"></a>Adat API

Minden konzorciumtag tartalmazza a szükséges információkat ahhoz, hogy mások is csatlakozhassanak a hálózathoz. A könnyű kapcsolat érdekében minden tag az adatAPI-végponton kapcsolati adatokat tartalmaz.

A meglévő tag biztosítja a [CONSORTIUM_DATA_URL] a tag telepítése előtt. A telepítés során egy illesztési tag a következő végponton olvassa le az információkat a JSON-kapcsolatról:

`<CONSORTIUM_DATA_URL>/networkinfo`

A válasz a tagok hozadékához hasznos információkat (Genesis blokk, Validator Set contract ABI, bootnodes) és a meglévő tag számára hasznos információkat (validátorcímek) tartalmaz. Ezzel a szabványosítási segítségével kiterjesztheti a konzorciumot a felhőszolgáltatók között. Ez az API JSON-formátumú választ ad vissza a következő struktúrával:

```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```

## <a name="governance-dapp"></a>Irányítás DApp

A hatalomigazolás középpontjában a decentralizált kormányzás áll. Mivel a hitelesítésigazolás a hálózati hatóságok engedélyezett listájára támaszkodik a hálózat kifogástalan állapotának megőrzése érdekében, fontos, hogy tisztességes mechanizmust biztosítson az engedélyezési lista módosításához. Minden üzembe helyezés intelligens szerződések és portál a láncon keresztül az engedélyezett lista láncon alapuló irányításához. Amint a javasolt változtatás eléri a konzorciumi tagok többségi szavazatát, a módosítás hatályba lép. A szavazás lehetővé teszi az új konszenzusos résztvevők hozzáadását vagy a résztvevők átlátható módon történő eltávolítását, ami a tisztességes hálózatra ösztönöz.

A vállalatirányítási DApp egy előre telepített [intelligens szerződések](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) és egy webalkalmazás, amely szabályozza a hatóságok a hálózaton. A hatóságok rendszergazdai identitásokra és validator csomópontokra vannak felosztva.
Az adminisztrátorok felhatalmazással rendelkeznek arra, hogy konszenzusos részvételt delegáljanak egy validator csomópontkészletre. A rendszergazdák más rendszergazdákat is szavazhatnak a hálózatba vagy a hálózatból.

![Irányítás DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **Decentralizált kormányzás:** A hálózati hatóságok változásait a láncon alapuló szavazással kezelik a kiválasztott rendszergazdák.
* **Validator delegálás:** A hatóságok kezelhetik az egyes poa-telepítésekben beállított érvényesítő csomópontjaikat.
* **Naplózható módosítási előzmények:** Minden változást rögzítenek a blokkláncon, amely átláthatóságot és auditálhatóságot biztosít.

### <a name="getting-started-with-governance"></a>Első lépések a kormányzással

Bármilyen tranzakció végrehajtásához a cégirányítási DApp-on keresztül, ethereum pénztárcát kell használnia. A legegyszerűbb megközelítés az, hogy egy in-böngésző pénztárca, mint a [MetaMask](https://metamask.io); azonban, mivel ezek az intelligens szerződések vannak telepítve a hálózaton is automatizálhatja a műveleteket a cégirányítási szerződés.

A MetaMask telepítése után keresse meg a cégirányítási dappot a böngészőben.  Az URL-címet az Azure Portalon keresztül a központi telepítési kimeneten keresse meg.  Ha nincs telepítve a böngészőben lévő pénztárca, akkor nem fog tudni műveleteket végrehajtani; azonban megtekintheti a rendszergazda állapotát.  

### <a name="becoming-an-admin"></a>Egyre rendszergazda

Ha Ön az első tag, aki a hálózaton üzembe helyezett, akkor automatikusan rendszergazda lesz, és a paritáscsomópontok érvényesítőként jelennek meg. Ha csatlakozik a hálózathoz, meg kell, hogy szavazott, mint egy admin a többség (nagyobb, mint 50%) a meglévő rendszergazdai készlet. Ha úgy dönt, hogy nem lesz rendszergazda, a csomópontok továbbra is szinkronizálni, és érvényesíti a blockchain; azonban nem vesznek részt a blokk létrehozásának folyamatában. A szavazási folyamat adminisztrátorvá válásához válassza **a Jelölés** lehetőséget, és adja meg Ethereum címét és aliasát.

![Nevezés](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Jelöltek

A **Jelöltek** lap kiválasztása a jelölt rendszergazdák aktuális készletét jeleníti meg.  Amint egy jelölt eléri a többségi szavazást a jelenlegi adminok, a jelölt lesz elő egy admin.  Ha egy jelöltre szeretne szavazni, válassza ki a sort, és válassza a Szavazás lehetőséget **a alkalmazásban.** Ha meggondolja magát a szavazáskor, válassza ki a jelöltet, és válassza **a Rescind szavazás**lehetőséget .

![Jelöltek](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Rendszergazdák

A **Rendszergazdák** lapon látható az adminisztrátorok aktuális készlete, és lehetővé teszi, hogy ellene szavazzon.  Ha egy rendszergazda elveszíti több mint 50%-os támogatást, akkor eltávolítja, mint egy admin a hálózaton. A rendszergazda tulajdonában lévő érvényesítő csomópontok elveszítik a validator státuszát, és tranzakciócsomópontokká válnak a hálózaton. A rendszergazda számos okból eltávolítható; azonban a konzorciumon múlik, hogy előre megállapodjon-e egy politikáról.

![Rendszergazdák](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Validátorok

Az **Érvényesítők** lap kiválasztásával megjelennek a példány aktuálisüzembe helyezett paritáscsomópontjai és aktuális állapotuk (csomóponttípus). Minden konzorciumtag nak más-más érvényesítőkészlete van a listában, mivel ez a nézet a jelenlegi üzembe helyezett konzorciumi tagot képviseli. Ha a példány újonnan telepített, és még nem adta hozzá a validátorokat, akkor lehetősége van **az Érvényesítők hozzáadása lehetőségre.** A validátorok hozzáadása automatikusan kiválasztja a paritáscsomópontok regionálisan kiegyensúlyozott készletét, és hozzárendeli őket a validator készlethez. Ha az engedélyezett kapacitásnál több csomópontot telepített, a fennmaradó csomópontok tranzakciócsomópontokká válnak a hálózaton.

Az egyes validator oka automatikusan az Azure-beli [identitástárolón](#identity-store) keresztül kerül hozzárendelésre.  Ha egy csomópont leáll, lemond a személyazonosságáról, így egy másik csomópont a központi telepítés, hogy a helyét. Ez a folyamat biztosítja, hogy az Ön konszenzusos részvétele magas rendelkezésre állású legyen.

![Validátorok](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Konzorcium neve

Bármely rendszergazda frissítheti a konzorcium nevét.  A konzorcium nevének frissítéséhez válassza a bal felső sarokban lévő fogaskerék ikont.

### <a name="account-menu"></a>Fiók menü

A jobb felső sarokban található az Ethereum-fiók aliasa és az identicon.  Ha Ön rendszergazda, frissítheti az aliasát.

![Fiók](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>Ethereum fejlesztés<a id="tutorials"></a>

Az intelligens szerződések összeállításához, üzembe helyezéséhez és teszteléséhez az alábbiakat érdemes figyelembe venni az Ethereum fejlesztéséhez:
* [Szarvasgomba Lakosztály](https://www.trufflesuite.com/docs/truffle/overview) - Ügyfélalapú Ethereum fejlesztői környezet
* [Ethereum Remix](https://remix-ide.readthedocs.io/en/latest/index.html ) - Böngésző alapú és helyi Ethereum fejlesztési környezet

### <a name="compile-deploy-and-execute-smart-contract"></a>Intelligens szerződés fordítása, üzembe helyezése és végrehajtása

A következő példában egy egyszerű intelligens szerződést hoz létre. A Szarvasgomba segítségével lefordíthatja és üzembe helyezheti az intelligens szerződést a blokklánc-hálózatára. Üzembe helyezés után egy intelligens szerződésfüggvényt egy tranzakción keresztül hívhat meg.

#### <a name="prerequisites"></a>Előfeltételek

* Telepítse [a Python 2.7.15 -ot.](https://www.python.org/downloads/release/python-2715/) Python szükséges szarvasgomba és web3. Válassza ki a telepítési lehetőséget, hogy a Python az útvonalon.
* Telepítse szarvasgomba v5.0.5 `npm install -g truffle@v5.0.5`. Szarvasgomba igényel több eszközt kell telepíteni, beleértve [Node.js](https://nodejs.org), [Git](https://git-scm.com/). További információ: [Szarvasgomba dokumentáció](https://github.com/trufflesuite/truffle).

### <a name="create-truffle-project"></a>Szarvasgomba-projekt létrehozása

Az intelligens szerződés összeállítása és üzembe helyezése előtt létre kell hoznia egy szarvasgomba-projektet.

1. Nyisson meg egy parancssort vagy rendszerhéjat.
1. Hozzon létre egy `HelloWorld` nevű mappát.
1. Módosítsa a könyvtárat az új `HelloWorld` mappára.
1. Új szarvasgomba-projekt inicializálása a paranccsal. `truffle init`

    ![Új szarvasgomba-projekt létrehozása](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>Intelligens egyezmény hozzáadása

Hozza létre intelligens szerződéseit a Szarvasgomba-projekt **szerződések** alkönyvtárában.

1. Hozzon létre egy `postBox.sol` fájlt a szarvasgomba-projekt **szerződések** alkönyvtárában.
1. Adja hozzá a következő Szilárdságkódot a **postBox.sol**mappához.

    ```javascript
    pragma solidity ^0.5.0;
    
    contract postBox {
        string message;
        function postMsg(string memory text) public {
            message = text;
        }
        function getMsg() public view returns (string memory) {
            return message;
        }
    }
    ```

### <a name="deploy-smart-contract-using-truffle"></a>Intelligens szerződés telepítése szarvasgombával

A szarvasgomba-projektek konfigurációs fájlt tartalmaznak a blockchain hálózati kapcsolat részleteihez. Módosítsa a konfigurációs fájlt úgy, hogy az tartalmazza a hálózat csatlakozási adatait.

> [!WARNING]
> Soha ne küldje el az Ethereum személyes kulcsát a hálózaton keresztül. Győződjön meg arról, hogy minden tranzakció taszerint először helyileg van aláírva, és az aláírt tranzakció a hálózaton keresztül kerül elküldésre.

1. Szüksége van a mnemonic kifejezés az [Ethereum admin fiók használt telepítésekor a blockchain hálózat](#ethereum-settings). Ha a MetaMask segítségével hozta létre a fiókot, akkor a mnemonikus a MetaMask. Válassza a Rendszergazdai fiók ikonját a MetaMask kiterjesztés jobb felső részén, majd a **Beállítások > biztonság & adatvédelmi > a kezdőszavak felfedése lehetőséget**.
1. Cserélje le `truffle-config.js` a tartalmát a szarvasgomba projekt a következő tartalmat. Cserélje ki a helyőrző végpontot és a mnemonikus értékeket.

    ```javascript
    const HDWalletProvider = require("truffle-hdwallet-provider");
    const rpc_endpoint = "<Ethereum RPC endpoint>";
    const mnemonic = "Twelve words you can find in MetaMask > Security & Privacy > Reveal Seed Words";

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*" // Match any network id
        },
        poa: {
          provider: new HDWalletProvider(mnemonic, rpc_endpoint),
          network_id: 10101010,
          gasPrice : 0
        }
      }
    };
    ```

1. Mivel a Szarvasgomba HD Wallet szolgáltatót használjuk, telepítse a `npm install truffle-hdwallet-provider --save`modult a projektbe a parancs segítségével.

A Truffle áttelepítési parancsfájlok segítségével intelligens szerződéseket telepít egy blokklánc-hálózatra. Az új intelligens szerződés üzembe helyezéséhez áttelepítési parancsfájlra van szükség.

1. Új áttelepítés hozzáadása az új szerződés üzembe helyezéséhez. Hozzon `2_deploy_contracts.js` létre fájlt a Szarvasgomba-projekt **áttelepítési** alkönyvtárában.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. Telepítse a PoA-hálózatra a Szarvasgomba áttelepítése paranccsal. A Szarvasgomba projekt könyvtárának parancssorában futtassa a következőparancsot:

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>Intelligens szerződésfüggvény felhívása

Most, hogy az intelligens szerződés telepítve van, elküldheti a tranzakciót egy függvény hívásához.

1. A Szarvasgomba projekt könyvtárában hozzon `sendtransaction.js`létre egy új fájlt, amelynek neve .
1. Adja hozzá a következő tartalmat a **sendtransaction.js fájlhoz.**

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the postBox smart contract")
      postBox.deployed().then(function(instance) {
        console.log("Calling postMsg function for contract ", instance.address);
        return instance.postMsg("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. A parancsfájl végrehajtása a Szarvasgomba-végrehajtás paranccsal.

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![Parancsfájl végrehajtása a függvény tranzakción keresztüli hívásához](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>A WebAssembly (WASM) támogatása

A WebAssembly támogatása már engedélyezve van az újonnan telepített PoA-hálózatokon. Ez lehetővé teszi az intelligens szerződés fejlesztése minden nyelven, hogy transpiles a Web-Assembly (Rust, C, C ++). További információ: [Paritás a WebAssembly](https://wiki.parity.io/WebAssembly-Home) és az Oktatóanyag áttekintése [a Parity Tech-ből](https://github.com/paritytech/pwasm-tutorial)

## <a name="faq"></a>GYIK

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Észrevettem, hogy sok olyan tranzakció van a hálózaton, amit nem küldtem el. Honnan jönnek ezek?

Ez nem biztonságos, hogy feltárja a [személyes API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html). Botok hallgatni kinyitotta Ethereum számlák és megpróbálja lecsapolni a forrásokat. A bot feltételezi, hogy ezek a fiókok valós étert tartalmaznak, és megpróbálnak elsőként elszívni az egyensúlyt. Ne engedélyezze a személyes API-t a hálózaton. Ehelyett előírja a tranzakciókat manuálisan egy pénztárca használatával, például metamaszk használatával, vagy programozott módon.

### <a name="how-to-ssh-onto-a-vm"></a>Hogyan ssh-ra egy virtuális gép?

Az SSH-port biztonsági okokból nincs kitéve. Az [SSH-port engedélyezéséhez kövesse ezt az útmutatót.](#ssh-access)

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Hogyan állíthatok be naplózási tagot vagy tranzakciócsomópontokat?

A tranzakciócsomópontok olyan paritásügyfelek, amelyek a hálózattal társviszonyban vannak, de nem vesznek részt konszenzusban. Ezek a csomópontok továbbra is használhatók az Ethereum-tranzakciók elküldéséhez és az intelligens szerződés állapotának olvasásához. Ez a mechanizmus azon dolgozik, hogy a hálózaton a nem hatósági konzorciumi tagok számára ellenőrizhetőséget biztosítson. Ennek elérése érdekében kövesse [a Konzorcium termesztése](#growing-the-consortium)című részt.

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Miért tartanak sokáig a MetaMask-tranzakciók?

Annak érdekében, hogy a tranzakciók a megfelelő sorrendben érkezhessenek, minden Ethereum tranzakció nkkalinkulé nonce-vel érkezik. Ha egy másik hálózaton használt egy fiókot a MetaMask alkalmazásban, alaphelyzetbe kell állítania a nonce értéket. Kattintson a beállítások ikonra (három sávos), Beállítások, Fiók visszaállítása. A tranzakcióelőzmények törlődnek, és most újra elküldheti a tranzakciót.

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Meg kell adnom a gázdíjat a MetaMask ban?

Az Éternek nincs célja a proof-of-authority konzorciumban. Ezért nincs szükség gázdíj megadására a MetaMask tranzakcióinak benyújtásakor.

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Mit tegyek, ha a központi telepítés sikertelen az Azure OMS kiépítésének sikertelené miatt?

A figyelés nem kötelező funkció. Néhány ritka esetben, amikor a központi telepítés sikertelen, mert nem képes sikeresen kiépíteni az Azure Monitor erőforrást, újratelepítheti az Azure Monitor nélkül.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>A nyilvános IP-telepítések kompatibilisek a magánhálózati telepítésekkel?

Nem. A társviszony-létesítés kétirányú kommunikációt igényel, így a teljes hálózatnak nyilvánosnak vagy magánjellegűnek kell lennie.

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Mi a proof-of-Authority várható tranzakciós átviteli-áteresztőjele?

A tranzakció átviteli sebessége nagymértékben függ a tranzakciók típusaés a hálózati topoológia. Egyszerű tranzakciók használatával másodpercenként átlagosan 400 tranzakciót használtunk fel egy több régióban telepített hálózattal.

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Hogyan tudok feliratkozni az intelligens szerződéseseményekre?

Az Ethereum Proof-of-Authority mostantól támogatja a web-szoftvercsatornákat.  Ellenőrizze a központi telepítési kimenetet a websocket URL-címének és portjának megkereséséhez.

## <a name="next-steps"></a>További lépések

További Azure Blockchain-megoldásokért tekintse meg az [Azure Blockchain dokumentációját.](https://docs.microsoft.com/azure/blockchain/)
