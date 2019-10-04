---
title: Ethereum-szolgáltatói konzorcium – Azure
description: A többtagú konzorcium Ethereum üzembe helyezéséhez és konfigurálásához használja a Ethereum-szolgáltatói konzorciumi megoldást
services: azure-blockchain
keywords: ''
author: CodyBorn
ms.author: coborn
ms.date: 04/08/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: vamelech
ms.openlocfilehash: 01b9f7f74077737ea95a56bbe81f440db425bf0c
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698458"
---
# <a name="ethereum-proof-of-authority-consortium"></a>Ethereum-szolgáltatói konzorcium

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés
[Ez a megoldás](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) úgy lett kialakítva, hogy könnyebben lehessen üzembe helyezni, konfigurálni és szabályozni egy többtagú konzorcium Ethereum-hálózatát minimális Azure-és Ethereum-ismeretekkel.

Néhány felhasználói bemenet és egy kattintással elvégezhető központi telepítés a Azure Portalon keresztül minden egyes tag kiépítheti a hálózati lábnyomot, Microsoft Azure számítási, hálózatkezelési és tárolási szolgáltatásokat használva a világ különböző részein. Minden egyes tag hálózati lábnyoma olyan elosztott terhelésű érvényesítő csomópontokból áll, amelyekkel az alkalmazás vagy a felhasználó kapcsolatba tud lépni a Ethereum-tranzakciók elküldésével.

## <a name="concepts"></a>Alapelvek

### <a name="terminology"></a>Terminológia

-   **Konszenzus** – az adatszinkronizálási műveletek az elosztott hálózaton keresztül a blokkolás ellenőrzése és létrehozása révén.

-   **Konzorcium tagja** – olyan entitás, amely konszenzusban részt vesz a Blockchain-hálózaton.

-   **Rendszergazda** – egy adott konzorciumi tag részvételének kezeléséhez használt Ethereum-fiók.

-   **Validator** – egy olyan Ethereum-fiókhoz társított gép, amely egy rendszergazda nevében részt vesz a konszenzusban.

### <a name="proof-of-authority"></a>Hitelesítésszolgáltató ellenőrzése

Azok számára, akik a blockchain-Közösség újdonsága, a megoldás kiadása nagyszerű lehetőség a technológiára az Azure-ban könnyen és konfigurálható módon. A Sybil mechanizmusa lehetővé teszi a számítási költségek kiszámítását a hálózat önszabályozása és a méltányos részvétel engedélyezése érdekében. Ez remekül működik a névtelen, nyílt blockchain hálózatokban, ahol a cryptocurrency versenye elősegíti a hálózat biztonságát. A magán-és a Consortium-hálózatokban azonban az alapul szolgáló éternek nincs értéke. Egy alternatív protokoll, a hatóság, amely az engedélyezett hálózatok esetében megfelelőbb, és a konszenzus résztvevői ismertek és megbízhatók. Nincs szükség a bányászatra, a hitelesítő szervezet hatékonyabb, miközben továbbra is megőrizte a bizánci hibatűrést.

### <a name="consortium-governance"></a>Konzorciumi irányítás

Mivel a hitelesítő hatóság a hálózati hatóságok engedélyezett listájára támaszkodik, hogy a hálózat kifogástalan maradjon, fontos, hogy az engedélyek listájának módosítására igazságos mechanizmust biztosítson. Mindegyik üzembe helyezéshez intelligens szerződés és portál tartozik, amely az engedélyezett lista láncon belüli irányítására használható. Ha egy javasolt változás a konzorcium tagjainak többségi szavazatát éri el, a módosítást a rendszer hozza meg. Ez lehetővé teszi, hogy új konszenzust vegyenek fel, vagy feltörték a résztvevőket olyan transzparens módon, amely egy becsületes hálózatot bátorít.

### <a name="admin-account"></a>Rendszergazdai fiók

A hitelesítő szervezet csomópontjainak üzembe helyezése során meg kell adnia egy rendszergazdai Ethereum-címeket. A Ethereum-fiók létrehozásához és biztonságossá tételéhez több különböző mechanizmus is használható. Ha ezt a fiókot a hálózatban lévő szolgáltatóként adja hozzá, akkor a fiók használatával részt vehet a szabályozásban. Ez a rendszergazdai fiók arra is felhasználható, hogy delegálja a konszenzusos részvételt a központi telepítés részeként létrehozott érvényesítő csomópontoknak. Mivel a rendszer csak a nyilvános Ethereum-címeket használja, minden rendszergazda rugalmasan biztonságossá teheti a titkos kulcsait, így azok a kívánt biztonsági modellt követik.

### <a name="validator-node"></a>Érvényesítő csomópont

A hitelesítő hatóság protokolljában az ellenőrző csomópontok a hagyományos bányász-csomópontok helyét helyezik el. Minden érvényesítő egyedi Ethereum-identitással rendelkezik, amely egy intelligens szerződési engedélyek listájára kerül. Ha egy érvényesítő szerepel ezen a listán, akkor részt vehet a blokk létrehozási folyamatában. Ha többet szeretne megtudni erről a folyamatról, tekintse meg [](https://wiki.parity.io/Aura)a következő témakört: paritásos dokumentáció a hatóságon belüli konszenzusról. Mindegyik konzorciumi tag két vagy több, öt régióban lévő validator-csomópontot tud kiépíteni a Geo-redundancia érdekében. Az érvényesítő csomópontjai más érvényesítő csomópontokkal kommunikálnak, hogy konszenzust kapjanak az alapul szolgáló elosztott Főkönyv állapotán.
A hálózatban való méltányos részvétel biztosítása érdekében minden egyes konzorciumi tag számára tilos több validator használata, mint a hálózat első tagja (ha az első tag három érvényesítő telepít, akkor az egyes tagok legfeljebb három validatort tartalmazhatnak).

### <a name="identity-store"></a>Identity Store

Mivel minden tagnak több, egyszerre futó validator-csomóponttal kell rendelkeznie, és minden csomópontnak rendelkeznie kell egy engedélyezett identitással, fontos, hogy az érvényesítő biztonságosan szerezzenek be egyedi aktív identitást a hálózaton. Ennek egyszerűbbé tétele érdekében olyan identitás-tárolót hoztunk létre, amely minden olyan tag előfizetésében üzembe helyezhető, amely biztonságosan tárolja a generált Ethereum-identitásokat. Az üzembe helyezés után a Ethereum egy titkos kulcsot hoz majd az egyes validatorokhoz, és tárolja azt Azure Key Vault. A paritási csomópont elindítása előtt először egy nem használt identitáshoz tartozó bérletet hoz létre, hogy az identitás ne legyen egy másik csomópontban. Az identitás a-ügyfél számára van megadva, amely lehetővé teszi a számára, hogy megkezdje a blokkok létrehozását. Ha az üzemeltető virtuális gép kimaradást tapasztal, a rendszer felszabadítja az identitás bérletét, és lehetővé teszi, hogy a helyettesítő csomópont a jövőben is folytassa az identitását.

### <a name="bootnode-registrar"></a>Bootnode-regisztráló

A könnyű kapcsolat engedélyezéséhez minden egyes tag a kapcsolati adatokat az adatapi-végponton [](#data-api)fogja tárolni. Ezek az információk tartalmazzák azon bootnodes listáját, amelyek társítási csomópontként vannak megadva a csatlakozó tag számára. Az adat-API részeként a bootnode listát naprakészen tartjuk

### <a name="bring-your-own-operator"></a>Saját operátor használata

A konzorciumi tagok gyakran részt kívánnak venni a hálózati irányításban, de nem szeretnék működtetni és karbantartani az infrastruktúrát. A hagyományos rendszerektől eltérően a hálózaton belül egyetlen operátor működik a blockchain rendszerek decentralizált modelljével. Ahelyett, hogy egy központosított közvetítőt kellene felvennie egy hálózat üzemeltetésére, minden konzorcium-tag delegálhatja az infrastruktúra-kezelést a választott operátornak. Ez lehetővé teszi a hibrid modell használatát, amelyben az egyes tagok dönthetnek úgy, hogy saját infrastruktúrát működtetnek, vagy egy másik partnernek delegálják a műveletet. A delegált műveleti munkafolyamat a következőképpen működik:

1.  A **konzorcium tagja** létrehoz egy Ethereum-címeket (titkos kulccsal rendelkezik)

2.  A **konzorcium tagja** nyilvános Ethereum-címeket biztosít a kezelőnek

3.  Az **operátor** a Azure Resource Manager-megoldás használatával üzembe helyezi és konfigurálja a PoA validator-csomópontokat

4.  Az **operátor** biztosítja az RPC és a felügyeleti végpontot a **konzorcium tagjának** .

5.  A **konzorcium tagja** a saját titkos kulcsával aláír egy kérelmet, amely elfogadja az érvényesítő csomópontok kezelőjét, hogy a nevében részt vegyen

### <a name="azure-monitor"></a>Azure Monitor

Ez a megoldás a csomópont-és hálózati statisztikák nyomon követéséhez is Azure Monitor is tartalmaz. Az alkalmazások fejlesztői számára ez az alapul szolgáló blockchain ismerteti a blokk-generálási statisztikák nyomon követését. A hálózati kezelők a Azure Monitor használatával gyorsan észlelhetők és letilthatók a hálózati kimaradások az infrastruktúra-statisztikák és a lekérdezhető naplók segítségével. További információ: [szolgáltatás figyelése](#service-monitoring).

### <a name="deployment-architecture"></a>Üzembe helyezési architektúra

#### <a name="description"></a>Leírás

Ez a megoldás egyetlen vagy több régióból álló, többtagú Ethereum Consortium-hálózatot helyezhet üzembe. Alapértelmezés szerint az RPC-és a társítási végpontok elérhetők a nyilvános IP-címeken keresztül az előfizetések és a felhők közötti egyszerűsített kapcsolat engedélyezéséhez. Javasoljuk, hogy az alkalmazás szintű hozzáférés-vezérléshez a [paritás engedélyezési szerződéseit](https://wiki.parity.io/Permissioning) hasznosítsa. A VPN mögött üzembe helyezett hálózatokat is támogatjuk, amelyek az VNet-átjárókat használják az előfizetések közötti kapcsolathoz. Ezek az üzembe helyezések összetettebbek, ezért ajánlott először a nyilvános IP-modellel kezdeni.

#### <a name="consortium-member-overview"></a>A konzorcium tagjainak áttekintése

Mindegyik konzorciumi tag üzembe helyezése a következőket foglalja magában:

-   Virtual Machines a PoA validatorok futtatásához

-   Azure Load Balancer az RPC, a társítási és a irányítási DApp kérelmek terjesztéséhez

-   Azure Key Vault az érvényesítő identitások biztonságossá tételéhez

-   Az Azure Storage állandó hálózati információk üzemeltetéséhez és a lízing koordinálásához

-   Azure Monitor a naplók és a teljesítménnyel kapcsolatos statisztikák összesítéséhez

-   VNet-átjáró (nem kötelező) a VPN-kapcsolatok privát virtuális hálózatok való engedélyezéséhez

![üzembe helyezési architektúra](./media/ethereum-poa-deployment/deployment-architecture.png)

Kihasználjuk a Docker-tárolókat a megbízhatóság és a modularitás érdekében. Az egyes központi telepítések részeként a Azure Container Registry használatával üzemeltetheti és tálalhatja a verziószámmal ellátott lemezképeket. A tárolók képei a következőkből állnak:

-   Orchestrator

    -   Egyszer fut az üzembe helyezés során

    -   Identitások és irányítási szerződések generálása

    -   Identitások tárolása az Identity Store-ban

-   Paritásos ügyfél

    -   Az Identity Store-ból származó bérletek identitása

    -   Felfedi és összeköti a társait

-   EthStats-ügynök

    -   Helyi naplókat és statisztikákat gyűjt az RPC-n keresztül, és leküldi Azure Monitor

-   Irányítási DApp

    -   Az irányítási szerződésekkel való interakcióra szolgáló webes felület

## <a name="how-to-guides"></a>Útmutatók
### <a name="governance-dapp"></a>Irányítási DApp

Az igazoló hatóság középpontjában a decentralizált irányítás van. Az irányítási DApp az előre telepített [intelligens szerződések](https://github.com/Azure-Samples/blockchain/tree/master/ethereum-on-azure/) és a hálózatban lévő hatóságok irányítására szolgáló webalkalmazások összessége.
A hatóságok rendszergazdai identitásokra és érvényesítő csomópontokra vannak bontva.
A rendszergazdák jogosultak a konszenzusos részvétel delegálására az érvényesítő csomópontjain. A rendszergazdák más rendszergazdákat is szavazhatnak a hálózatról vagy onnan.

![irányítási Dapp](./media/ethereum-poa-deployment/governance-dapp.png)

-   **Decentralizált irányítás –** A hálózati kiszolgálókon végrehajtott módosításokat a rendszergazdák elemre kattintva felügyelheti a láncon belüli szavazással.

-   **Érvényesítő delegálása –** A hatóságok kezelhetik az egyes PoA-telepítésekben beállított érvényesítő csomópontjaikat.

-   **Naplózható változások előzményei –** A rendszer minden módosítást rögzít a blockchain, amely átláthatóságot és auditálást biztosít.

#### <a name="getting-started-with-governance"></a>Az irányítás első lépései
Ha bármilyen tranzakciót szeretne végrehajtani az irányítási DApp keresztül, ki kell használnia egy Ethereum-pénztárcát.  A legegyszerűbb megközelítés egy böngészőbeli mobiltárca használata, például a [MetaMask](https://metamask.io); mivel azonban ezek a hálózaton üzembe helyezett intelligens szerződések, automatizálhatja az irányítási Szerződéssel való interakciót is.

A MetaMask telepítése után navigáljon az irányítási DApp a böngészőben.  Az URL-címet a központi telepítés megerősítő e-mailben vagy az üzembe helyezési kimenet Azure Portal keresztül keresheti meg.  Ha nincs telepítve a böngészőbeli mobiltárca, nem fog tudni műveleteket végrehajtani; azonban továbbra is elolvashatja a rendszergazdai állapotot.  

#### <a name="becoming-an-admin"></a>Rendszergazdavé válás
Ha Ön a hálózaton üzembe helyezett első tag, akkor automatikusan rendszergazda lesz, és a paritásos csomópontok Érvényesítésként lesznek felsorolva.  Ha csatlakozik a hálózathoz, meg kell szavaznia a (50%-nál nagyobb) rendszergazdaként. a meglévő rendszergazda készletből.  Ha úgy dönt, hogy nem rendszergazda lesz, akkor a csomópontok továbbra is szinkronizálják és érvényesítik a blockchain; azonban nem fognak részt venni a blokk létrehozási folyamatában. A szavazási folyamat elindításához kattintson a __jelölés__ gombra, és adja meg a Ethereum címét és aliasát.

![Jelölés](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

#### <a name="candidates"></a>Jelöltek
A __jelöltek__ lapon megjelenik a jelölt rendszergazdák aktuális készlete.  Ha a jelölt a jelenlegi adminisztrátorok többsége eléri a szavazatot, a pályázó egy rendszergazdának lesz népszerűsítve.  A jelöltre való szavazáshoz válassza ki a sort, és kattintson a felül található "szavazás" gombra.  Ha meggondolja magát szavazásra, válassza ki a jelöltet, és kattintson a "szavazás törlése" gombra.

![Jelöltek](./media/ethereum-poa-deployment/governance-dapp-candidates.png)


#### <a name="admins"></a>Rendszergazdák
A __rendszergazdák__ lap megjeleníti a rendszergazdák aktuális készletét, és lehetővé teszi, hogy szavazzon.  Ha egy rendszergazda több mint 50%-os támogatást veszít, akkor a rendszer a hálózaton rendszergazdaként eltávolítja őket.  A rendszergazda tulajdonában lévő validator-csomópontok elveszítik az érvényesítő állapotát, és a hálózat tranzakciós csomópontjai lesznek.  A rendszergazdák tetszőleges számú okból eltávolíthatók. azonban a konzorciumnak meg kell egyeznie a szabályzattal.

![Rendszergazdák](./media/ethereum-poa-deployment/governance-dapp-admins.png)

#### <a name="validators"></a>Érvényesítőket
A bal oldali menüben a validatorok lap kiválasztásával megjelennek a példány aktuálisan telepített paritásos csomópontjai, valamint a jelenlegi állapotuk (csomópont típusa).  A konzorcium minden tagja más-más validatorokkal fog rendelkezni ebben a listában, mivel ez a nézet az aktuálisan telepített konzorciumi tagot jelképezi.  Ha ez egy újonnan telepített példány, és még nem adta hozzá a validatorokat, megjelenik a "validatorok hozzáadása" lehetőség.  Ezzel a beállítással automatikusan kiválaszthatja a paritásos csomópontok regionálisan elosztott készletét, és hozzárendelheti őket az érvényesítő készlethez.  Ha a rendelkezésre álló kapacitásnál több csomópontot telepített, a fennmaradó csomópontok a hálózat tranzakciós csomópontjai lesznek.

Az egyes validatorok címe automatikusan az Azure-beli [Identity Store](#identity-store) -on keresztül lesz hozzárendelve.  Ha egy csomópont leáll, leállítja az identitását, és lehetővé teszi, hogy a központi telepítés egy másik csomópontja tegye a helyét.  Ez biztosítja, hogy a konszenzus részvétele nagy rendelkezésre állású legyen.

![Érvényesítőket](./media/ethereum-poa-deployment/governance-dapp-validators.png)

#### <a name="consortium-name"></a>Konzorcium neve
Bármelyik rendszergazda frissítheti a konzorcium nevét, amely az oldal tetején jelenik meg.  A konzorcium nevének frissítéséhez kattintson a bal felső sarokban található fogaskerék ikonra.

#### <a name="account-menu"></a>Fiók menü
A jobb felső sarokban a Ethereum-fiók aliasa és a Identicon.  Ha Ön rendszergazda, lehetősége lesz frissíteni az aliast.

![Fiók](./media/ethereum-poa-deployment/governance-dapp-account.png)

### <a name="deploy-ethereum-proof-of-authority"></a>Ethereum-szolgáltató üzembe helyezése

Példa egy többrésztvevős üzembe helyezési folyamatra:

1.  Három tag mindegyike Ethereum-fiókot hoz MetaMask használatával

2.  *Az a tag* üzembe helyezi a Ethereum cselekvési tervét, és megadja a Ethereum nyilvános címeit

3.  Az *a tag* megadja a konzorcium URL-címét a *B* és *C* tag számára

4.  *B. tag* és *C. tag* üzembe helyezése, Ethereum PoA, a Ethereum nyilvános címe és *a tag*konzorciumi URL-címének megadása

5.  *Tag a* *B taghoz* tartozó szavazatot rendszergazdaként

6.  Az *A* és *B* tag, a *C* tagot pedig rendszergazdaként

Ehhez a folyamathoz olyan Azure-előfizetés szükséges, amely támogatja több virtuális gép és felügyelt lemez üzembe helyezését. Ha szükséges, [hozzon létre egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a kezdéshez.

Az előfizetés biztonságossá tétele után lépjen a Azure Portal. Válassza a "+", a piactér ("összes megjelenítése") lehetőséget, és keressen rá a Ethereum PoA Consortium kifejezésre.

A következő szakasz végigvezeti az első tag hálózati lábnyomának konfigurálásán. Az üzembe helyezési folyamat öt lépésből áll: Alapismeretek, központi telepítési régiók, hálózati méret és teljesítmény, Ethereum-beállítások, Azure Monitor.

#### <a name="basics"></a>Alapvető beállítások

Az alapértékek **területen minden**központi telepítéshez meg kell adnia a szabványos paraméterek értékét, például az előfizetést, az erőforráscsoportot és az alapszintű virtuális gép tulajdonságait.

Az egyes paraméterek részletes leírása a következő:

Paraméternév|Leírás|Megengedett értékek|Alapértelmezett értékek
---|---|---|---
Új hálózat létrehozása vagy meglévő hálózat csatlakoztatása|Új hálózat létrehozása vagy meglévő konzorciumi hálózat csatlakoztatása|Új JOIN meglévő létrehozása|Új létrehozása
E-mail-cím (nem kötelező)|E-mailben értesítést fog kapni, ha az üzemelő példány az üzembe helyezéssel kapcsolatos információkat tartalmaz.|Érvényes e-mail-cím|NA
Virtuális gép felhasználóneve|Az egyes telepített virtuális gépek rendszergazdai felhasználóneve (csak alfanumerikus karakterek esetén)|1-64 karakter|NA
Hitelesítési típus|A virtuális géphez való hitelesítés módszere.|Jelszó vagy nyilvános SSH-kulcs|Windows 10
Jelszó (hitelesítés típusa = jelszó)|Az egyes telepített virtuális gépek rendszergazdai fiókjának jelszava.  A jelszónak a következők közül hármat tartalmaznia kell: 1 nagybetűs karakter, 1 kisbetűs karakter, 1 szám és 1 speciális karakter. Habár a virtuális gépek kezdetben ugyanazzal a jelszóval rendelkeznek, a kiépítés után megváltoztathatja a jelszót.|12-72 karakter|NA
SSH-kulcs (hitelesítés típusa = nyilvános kulcs)|A távoli bejelentkezéshez használt Secure Shell-kulcs.||NA
Subscription|Az előfizetés, amelyre a konzorcium-hálózatot telepíteni kell||NA
Erőforráscsoport|Az az erőforráscsoport, amelyre a konzorcium-hálózatot telepíteni kell.||NA
Location|Az erőforráscsoport Azure-régiója.||NA

Alább látható egy példa a központi telepítésre: ![alapszintű panel](./media/ethereum-poa-deployment/basic-blade.png)

#### <a name="deployment-regions"></a>Központi telepítési régiók

Ezután az üzembe helyezési régiók területen adja meg a régió (k) számára a konzorcium-hálózat üzembe helyezéséhez és az Azure-régiók kiválasztásához szükséges bemeneti adatokat a megadott régiók száma alapján. A felhasználó legfeljebb 5 régióban helyezhető üzembe. Azt javasoljuk, hogy az első régió kiválasztásával egyezzen az erőforráscsoport helyével az alapok szakaszban. Fejlesztési vagy tesztelési hálózatok esetén a tagok egyetlen régiót ajánlanak. Éles környezetben ajánlott két vagy több régióban üzembe helyezni a magas rendelkezésre állást.

Az egyes paraméterek részletes leírása a következő:

  Paraméternév|Leírás|Megengedett értékek|Alapértelmezett értékek
  ---|---|---|---
  Régió (k) száma|A konzorcium-hálózat üzembe helyezéséhez szükséges régiók száma|1, 2, 3, 4, 5|1
  Első régió|A konzorcium-hálózat üzembe helyezésének első régiója|Az összes engedélyezett Azure-régió|NA
  Második régió|Második régió a konzorcium-hálózat üzembe helyezéséhez (csak akkor látható, ha a régiók száma kiválasztva 2)|Az összes engedélyezett Azure-régió|NA
  Harmadik régió|Harmadik régió a konzorcium-hálózat üzembe helyezéséhez (csak akkor látható, ha a régiók száma kiválasztva 3)|Az összes engedélyezett Azure-régió|NA
  Negyedik régió|A konzorcium-hálózat üzembe helyezésének negyedik régiója (csak akkor látható, ha a régiók száma kiválasztva 4)|Az összes engedélyezett Azure-régió|NA
  Ötödik régió|A Consortium Network üzembe helyezésének ötödik régiója (csak akkor látható, ha a régiók száma kiválasztva 5)|Az összes engedélyezett Azure-régió|NA

Alább látható egy példa a központi telepítésre: ![telepítési régiók](./media/ethereum-poa-deployment/deployment-regions.png)

#### <a name="network-size-and-performance"></a>Hálózati méret és teljesítmény

Ezután a "hálózati méret és teljesítmény" alatt a konzorciumi hálózat méretéhez tartozó bemeneteket kell megadni, például az érvényesítő csomópontok számát és méretét.
Az érvényesítő csomópont tárolási mérete a blockchain lehetséges méretét fogja megszabni. Ez az üzembe helyezés után módosítható.

Az egyes paraméterek részletes leírása a következő:

  Paraméternév|Leírás|Megengedett értékek|Alapértelmezett értékek
  ---|---|---|---
  Elosztott terhelésű érvényesítő csomópontok száma|A hálózat részeként kiépített érvényesítő csomópontok száma|2-15|2
  Érvényesítő csomópont tárolási teljesítménye|Az egyes telepített érvényesítő csomópontok által támogatott felügyelt lemez típusa.|standard SSD vagy prémium|Standard SSD
  Érvényesítő csomópont virtuális gép mérete|Az érvényesítő csomópontjaihoz használt virtuális gép mérete.|Standard A, standard D, standard D-v2, standard F sorozat, standard DS és standard FS|Standard D1 v2

[A Storage díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/)

[A virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)

A virtuális gép és a tárolási rétegek hatással lesznek a hálózati teljesítményre.  A következő SKU-ket ajánljuk a kívánt költséghatékonyság alapján:

  Virtuális gép SKU|Tárolási rétegek|Ár|Teljesítmény|Késés
  ---|---|---|---|---
  F1|Standard SSD|alacsony|alacsony|magas
  D2_v3|Standard SSD|közepes|közepes|közepes
  F16s|Prémium SSD|magas|magas|alacsony

Alább látható egy példa a központi telepítésre: ![hálózati méret és teljesítmény](./media/ethereum-poa-deployment/network-size-and-performance.png)

#### <a name="ethereum-settings"></a>Ethereum-beállítások

Ezután a Ethereum-beállítások területen válassza a Ethereum kapcsolatos konfigurációs beállításokat, például a hálózati azonosítót és a Ethereum-fiók jelszavát vagy a Genesis-blokkot.

Az egyes paraméterek részletes leírása a következő:

  Paraméternév|Leírás|Megengedett értékek|Alapértelmezett értékek
  ---|---|---|---
Konzorciumi tag azonosítója|Az ütközés elkerüléséhez az IP-címek konfigurálásához használt konzorciumi hálózaton résztvevő egyes tagokhoz tartozó azonosító. Magánhálózat esetén a tag AZONOSÍTÓjának egyedinek kell lennie az ugyanazon a hálózaton lévő különböző szervezetek között.  Egyedi tag AZONOSÍTÓra van szükség, még akkor is, ha ugyanaz a szervezet több régióban is üzembe helyezi. Jegyezze fel ennek a paraméternek az értékét, mert meg kell osztania a többi csatlakozó taggal, hogy ne legyen ütközés.|0-255|NA
Hálózatazonosító|Az üzembe helyezett konzorcium Ethereum hálózati azonosítója.  Mindegyik Ethereum-hálózat saját hálózati AZONOSÍTÓval rendelkezik, és 1 a nyilvános hálózat azonosítója.|5 – 999 999 999|10101010
Rendszergazdai Ethereum címe|A PoA-irányításban való részvételhez használt Ethereum-fiók címe.  Javasoljuk, hogy a MetaMask használatával Ethereum-címeket generáljon.|42 alfanumerikus karakter a 0x-től kezdődően|NA
Speciális beállítások|Speciális beállítások a Ethereum beállításaihoz|Engedélyezés vagy Letiltás|Letiltás
Nyilvános IP-cím (speciális beállítások = engedélyezés)|Központilag telepíti a hálózatot egy VNet-átjáró mögött, és eltávolítja a társ-hozzáférési hozzáférést. Ha ez a beállítás be van jelölve, az összes tagnak VNet átjárót kell használnia ahhoz, hogy a kapcsolatok kompatibilisek legyenek.|Nyilvános IP-cím magánhálózati VNet|Nyilvános IP-cím
A gáz korlátozásának letiltása (speciális beállítások = engedélyezés)|A hálózat indítási blokkjának gáz-korlátja|Bármilyen numerikus|50000000
Visszazárási időszak blokkolása (mp)|Az üres blokkok létrehozásának gyakorisága, ha nincsenek tranzakciók a hálózaton. A magasabb szintű gyakoriság gyorsabb lesz, de nagyobb a tárolási költségek.|Bármilyen numerikus|15
Tranzakciós engedély szerződése (speciális beállítások = engedélyezés)|A tranzakció bytecode vonatkozó szerződés. Az intelligens szerződések üzembe helyezésének és végrehajtásának korlátozása a Ethereum-fiókok engedélyezett listájára.|Szerződés bytecode|NA

Alább látható egy példa a központi telepítésre: ![ethereum beállítások](./media/ethereum-poa-deployment/ethereum-settings.png)

#### <a name="monitoring"></a>Figyelés

A figyelés panel lehetővé teszi egy Azure Monitor naplók erőforrásának konfigurálását a hálózat számára. A monitorozási ügynök a hálózatról gyűjti és felhasználja a hasznos mérőszámokat és naplókat, így gyorsan ellenőrizhetővé válik a hálózat állapota vagy hibakeresési problémái.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

  Paraméternév|Leírás|Megengedett értékek|Alapértelmezett értékek
  ---|---|---|---
Figyelés|A figyelés engedélyezésének lehetősége|Engedélyezés vagy Letiltás|Engedélyezés
Kapcsolódás meglévő Azure Monitor naplókhoz|Új Azure Monitor naplók példányának létrehozása vagy meglévő példány csatlakoztatása|Új létrehozása vagy meglévő csatlakoztatása|Új létrehozása
Hely figyelése (Kapcsolódás meglévő Azure Monitor naplókhoz = új létrehozása)|Az a régió, ahol az új Azure Monitor naplók példánya üzembe lesz helyezve|Minden Azure Monitor naplózási régió|NA
Meglévő log Analytics-munkaterület azonosítója (Csatlakozás meglévő Azure Monitor naplókhoz = csatlakozás meglévőhöz)|A meglévő Azure Monitor naplók példányának munkaterület-azonosítója||NA
Meglévő log Analytics elsődleges kulcs (Csatlakozás meglévő Azure Monitor naplókhoz = csatlakozás meglévőhöz)|A meglévő Azure Monitor naplók példányához való kapcsolódáshoz használt elsődleges kulcs||NA


Alább látható egy példa a központi telepítésre: ![Azure monitor](./media/ethereum-poa-deployment/azure-monitor.png)

#### <a name="summary"></a>Összegzés

Az összefoglalás panelen áttekintheti a megadott bemeneteket, és futtathatja az alapszintű telepítés előtti ellenőrzést. A telepítése előtt letöltheti a sablont és a paramétereket.

Tekintse át a jogi és adatvédelmi feltételeket, és kattintson a Vásárlás lehetőségre a telepítéshez. Ha az üzembe helyezés VNet-átjárókat tartalmaz, a központi telepítés 45 – 50 percet vesz igénybe.

#### <a name="post-deployment"></a>Üzembe helyezés utáni

##### <a name="deployment-output"></a>Központi telepítés kimenete

Miután az üzembe helyezés befejeződött, a megerősítő e-mailben vagy a Azure Portalon keresztül érheti el a szükséges paramétereket. A következő paraméterekben talál:

-   Ethereum RPC-végpont

-   Irányítási irányítópult URL-címe

-   Azure Monitor URL-cím

-   Adaturl-cím

-   VNet-átjáró erőforrás-azonosítója (nem kötelező)

##### <a name="confirmation-email"></a>Visszaigazoló e-mail

Ha e-mail-címet (alapszintű[szakaszt](#basics)) ad meg, a rendszer e-mailt küld az e-mail-címre az üzembe helyezési kimeneti információkkal.

![üzembe helyezési e-mail](./media/ethereum-poa-deployment/deployment-email.png)

##### <a name="portal"></a>Portál

Miután az üzembe helyezés sikeresen befejeződött, és az összes erőforrás ki lett töltve, megtekintheti az erőforráscsoport kimeneti paramétereit.

1.  Keresse meg az erőforráscsoportot a portálon

2.  Navigáljon az üzemelő példányokhoz

3.  Válassza ki a legfelső szintű telepítést ugyanazzal a névvel, mint az erőforráscsoport

4.  *Kimenetek* kiválasztása

### <a name="growing-the-consortium"></a>A konzorcium egyre nagyobb

A konzorcium kibontásához először össze kell kapcsolni a fizikai hálózatot.
A nyilvános IP-alapú telepítés használata esetén ez az első lépés zökkenőmentes. Ha VPN-t helyez üzembe, tekintse meg az [VNet-átjáró csatlakoztatása](#connecting-vnet-gateways) című szakaszt, amely a hálózati kapcsolatot az új tag központi telepítésének részeként hajtja végre.  Miután az üzembe helyezés befejeződött, használja az [irányítási DApp](#governance-dapp) , hogy hálózati rendszergazda legyen.

#### <a name="new-member-deployment"></a>Új tag üzembe helyezése

1.  Ossza meg a következő információkat a csatlakozó taggal. Ezek az információk az üzembe helyezés utáni e-mailben vagy a portál telepítési kimenetében találhatók.

    -  Konzorcium-adaturl-cím

    -  A telepített csomópontok száma

    -  VNet-átjáró erőforrás-azonosítója (VPN használata esetén)

2.  A központi telepítéshez használt tagnak [ugyanazt a megoldást](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) kell használnia, amikor a hálózati jelenlétét az alábbiak szem előtt tartásával telepíti:

    -  Válassza a *Csatlakozás meglévőt* lehetőséget

    -  Válassza ki ugyanazt a számú érvényesítő csomópontot, mint a hálózat többi tagját a tisztességes ábrázolás biztosításához.

    -  Használja ugyanazt a Ethereum-internetcímet, amelyet az előző lépésben adott meg

    -  Adja át a megadott *konzorcium-Adaturl-címet* a *Ethereum beállítások* lapon

    -  Ha a hálózat többi része VPN-kapcsolattal rendelkezik, a speciális szakaszban válassza a *privát VNet* elemet.

#### <a name="connecting-vnet-gateways"></a>VNet-átjárók csatlakoztatása

Ha már telepítette az alapértelmezett nyilvános IP-beállításokat, figyelmen kívül hagyhatja ezt a lépést. Magánhálózat esetén a különböző tagok a VNet-átjáró kapcsolatain keresztül kapcsolódnak egymáshoz. Ahhoz, hogy egy tag csatlakozzon a hálózathoz, és látni tudja a tranzakciós forgalmat, a meglévő tagnak a VPN-átjárón a kapcsolat elfogadásához végső konfigurációval kell rendelkeznie. Ez azt jelenti, hogy a csatlakozó tag Ethereum-csomópontjai nem futnak, amíg meg nem történik a kapcsolatok létrehozása. Azt javasoljuk, hogy hozzon létre redundáns hálózati kapcsolatokat (mesh) a konzorciumba, hogy csökkentse a meghibásodás esélyeit.

Az új tag üzembe helyezése után a meglévő tagnak be kell fejeznie a kétirányú kapcsolódást egy VNet-átjáró-kapcsolódás az új taggal való beállításával. Ennek eléréséhez a meglévő tagnak a következőkre lesz szüksége:

1.  A csatlakozó tag VNet-átjárójának ResourceID (lásd: telepítési kimenet)

2.  A megosztott kapcsolatok kulcsa

A meglévő tagnak a következő PowerShell-parancsfájlt kell futtatnia a kapcsolódás befejezéséhez. Javasoljuk, hogy a portál jobb felső navigációs sávján található Azure Cloud Shell használja.

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

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

### <a name="service-monitoring"></a>Szolgáltatásfigyelés

A Azure monitor-portált a központi telepítési e-mailben található hivatkozásra kattintva vagy a telepítési \[kimeneti OMS\_-portál\_URL-címében\]található paraméter megkeresésével érheti el.

A portál először a magas szintű hálózati statisztikákat és a csomópontok áttekintését jeleníti meg.

![Kategóriák figyelése](./media/ethereum-poa-deployment/monitor-categories.png)

A **csomópontok áttekintésének** kiválasztása a portálra irányítja a csomópontos infrastruktúra statisztikáit.

![csomópont statisztikái](./media/ethereum-poa-deployment/node-stats.png)

A **hálózati statisztika** lehetőség kiválasztásával megtekintheti a Ethereum hálózati statisztikáit.

![hálózati statisztika](./media/ethereum-poa-deployment/network-stats.png)

#### <a name="sample-kusto-queries"></a>Példa Kusto-lekérdezésekre

Ezen irányítópultok mögött a lekérdezhető nyers naplók halmaza látható. Ezekkel a nyers naplókkal testreszabhatja az irányítópultokat, megvizsgálhatja a hibákat, vagy beállíthatja a küszöbértékek riasztását. Az alábbiakban megtalálhatja azokat a lekérdezési példákat, amelyek a napló keresési eszközében futtathatók:

##### <a name="lists-blocks-that-have-been-reported-by-more-than-one-validator-useful-to-help-find-chain-forks"></a>Felsorolja a több validator által jelentett blokkokat. Hasznos segítséget nyújt a lánc villáinak megkereséséhez.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

##### <a name="get-average-peer-count-for-a-specified-validator-node-averaged-over-5-minute-buckets"></a>Egy adott validator csomópont átlagos számának beolvasása 5 perces gyűjtőn.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

### <a name="ssh-access"></a>SSH-hozzáférés

Biztonsági okokból az SSH-port elérését alapértelmezés szerint egy hálózati csoport biztonsági szabálya letiltja. A PoA hálózatban található virtuálisgép-példányok eléréséhez módosítania kell ezt a szabályt \"az engedélyezéshez.\"

1.  Kezdje a központilag telepített erőforráscsoport Áttekintés szakaszában Azure Portal.

    ![az SSH áttekintése](./media/ethereum-poa-deployment/ssh-overview.png)

2.  Válassza ki az elérni kívánt virtuális gép régiójának hálózati biztonsági csoportját

    ![SSH-NSG](./media/ethereum-poa-deployment/ssh-nsg.png)

3.  Az \"engedélyezés-SSH\" szabály kiválasztása

    ![SSH – engedélyezés](./media/ethereum-poa-deployment/ssh-allow.png)

4.  \"Módosítási\" művelet engedélyezése

    ![SSH engedélyezése engedélyezése](./media/ethereum-poa-deployment/ssh-enable-allow.png)

5.  Kattintson \"a\" Mentés gombra (a módosítások alkalmazása eltarthat néhány percig)

Mostantól távolról is csatlakozhat az érvényesítő csomópontok virtuális gépei számára az SSH-n keresztül a megadott rendszergazdai felhasználónévvel és jelszóval/SSH-kulccsal.
Az első érvényesítő csomópont eléréséhez futtatandó SSH-parancs a sablon központi telepítési kimenetének paraméterében szerepel, ahogy az "\_SSH\_–\_első\_VL\_Node terület1" (a minta üzembe helyezéséhez: SSH-p 4000 poaadmin\@leader4vb.eastus.cloudapp.Azure.com). További tranzakciós csomópontok megszerzéséhez növelje a portszámot (például az első tranzakciós csomópont a 4000-as porton).

Ha egynél több régiót telepít, módosítsa a fenti parancsot az adott régióban lévő terheléselosztó DNS-nevére vagy IP-címére. A többi régió DNS-nevének vagy IP-címének megkereséséhez keresse meg az erőforrást az \*elnevezési konvenció \* \* \* \*-\#lbpip-reg, és tekintse meg a DNS-név és az IP-cím tulajdonságait.

### <a name="azure-traffic-manager-load-balancing"></a>Az Azure Traffic Manager terheléselosztása

Az Azure Traffic Manager segítségével csökkentheti az állásidőt, és javíthatja a PoA hálózat rugalmasságát azáltal, hogy a bejövő forgalmat több különböző régióban üzemelő példányon irányítja át. A beépített állapot-ellenőrzéseket és az automatikus átirányítást az RPC-végpontok és az irányítási DApp magas rendelkezésre állásának biztosítása érdekében nyújtjuk. Ez a funkció akkor hasznos, ha több régióban van üzembe helyezve, és készen áll a gyártásra.

Traffic Manager használata a következőhöz:

-   A PoA hálózat rendelkezésre állásának javítása automatikus feladatátvételsel.

-   Növelje hálózata rugalmasságát azáltal, hogy a végfelhasználókat az Azure-helyre irányítja, és a legalacsonyabb hálózati késéssel rendelkezik.

Ha úgy dönt, hogy létrehoz egy Traffic Manager profilt, használhatja a profil DNS-nevét a hálózat eléréséhez. Ha más konzorcium-tagok lettek hozzáadva a hálózathoz, a Traffic Manager is felhasználhatja a terheléselosztást a telepített érvényesítő között.

#### <a name="creating-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Keresse meg és válassza \"ki Traffic Manager\" profilt, miután \"rákattintott\" az erőforrás létrehozása gombra a Azure Portal.

![Az Azure Traffic Manager keresése](./media/ethereum-poa-deployment/traffic-manager-search.png)

Adjon egyedi nevet a profilnak, és válassza ki azt az erőforráscsoportot, amelyet a PoA üzembe helyezése során hozott létre. Kattintson a "létrehozás" gombra a telepítéshez.

![Traffic Manager létrehozása](./media/ethereum-poa-deployment/traffic-manager-create.png)

A telepítés után válassza ki a példányt az erőforráscsoporthoz. A Traffic Manager eléréséhez használt DNS-név az Áttekintés lapon található.

![Traffic Manager DNS-helyének megkeresése](./media/ethereum-poa-deployment/traffic-manager-dns.png)

Válassza a végpontok fület, majd kattintson a Hozzáadás gombra. Adjon egyedi nevet a végpontnak. Módosítsa a cél erőforrás típusát a nyilvános IP-címekre. Ezután válassza ki az első régió\'s Load Balancer nyilvános IP-címét.

![Útválasztási Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Ismételje meg a műveletet a központilag telepített hálózat minden régiójában. Ha a végpontok \"engedélyezve\" vannak, az automatikusan betöltődik, és régiónként a Traffic Manager DNS-neve lesz. Ezt a DNS-nevet már \[használhatja a\_konzorciumi\_adaturl-cím\] paraméter helyett a dokumentum más lépéseiben.

### <a name="data-api"></a>Adatapi

Minden konzorcium tagja a szükséges információkat tárolja mások számára a hálózathoz való kapcsolódáshoz. A meglévő tag megadja a [CONSORTIUM_DATA_URL]-t a tag üzembe helyezése előtt. Az üzembe helyezés után a csatlakozó tag a JSON-felületről kéri le az adatokat a következő végponton:

`<CONSORTIUM_DATA_URL>/networkinfo`

A válasz olyan információkat tartalmaz, amelyek hasznosak lehetnek a tagokhoz való csatlakozáshoz (Genesis blokk, Validation set szerződéses ABI, bootnodes) és a meglévő tag (érvényesítő címei) számára hasznos információk. Javasoljuk, hogy használja ezt a szabványosítást, hogy kiterjessze a konzorciumot a felhőalapú szolgáltatók között. Ez az API egy JSON formátumú választ ad vissza a következő szerkezettel:
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
## <a name="tutorials"></a>Oktatóanyagok

### <a name="programmatically-interacting-with-a-smart-contract"></a>Intelligens Szerződéssel való programozott interakció

> [!WARNING]
> Soha ne küldje el a Ethereum titkos kulcsát a hálózaton keresztül! Győződjön meg arról, hogy minden tranzakció helyileg van aláírva, és az aláírt tranzakciót a hálózaton keresztül küldik.

A következő példában a *ethereumjs-Wallet* használatával Ethereum-címeket hozunk elő, a *ethereumjs-TX-* t pedig helyileg kell aláírni, és a *web3* a nyers tranzakciót a Ethereum RPC-végpontba kell küldenie.

Ebben a példában az egyszerű Hello-World intelligens szerződést fogjuk használni:

```javascript
pragma solidity ^0.4.11;
contract postBox {
    string message;
    function postMsg(string text) public {
        message = text;
    }
    function getMsg() public view returns (string) {
        return message;
    }
}
```

Ez a példa feltételezi, hogy a szerződés már telepítve van. A *solc* és a *web3* használatával programozott módon helyezhet üzembe egy szerződést. Először telepítse a következő Node-modulokat:
```
sudo npm install web3@0.20.2
sudo npm install ethereumjs-tx@1.3.6
sudo npm install ethereumjs-wallet@0.6.1
```
Ez a nodeJS-szkript a következőket fogja végrehajtani:

-   Nyers tranzakció kiépítése: postMsg

-   A tranzakció aláírása a generált titkos kulccsal

-   Az aláírt tranzakció elküldése a Ethereum-hálózatnak

```javascript
var ethereumjs = require('ethereumjs-tx')
var wallet = require('ethereumjs-wallet')
var Web3 = require('web3')

// TODO Replace with your contract address
var address = "0xfe53559f5f7a77125039a993e8d5d9c2901edc58";
var abi = [{"constant": false,"inputs": [{"name": "text","type": "string"}],"name": "postMsg","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": true,"inputs": [],"name": "getMsg","outputs": [{"name": "","type": "string"}],"payable": false,"stateMutability": "view","type": "function"}];

// Generate a new Ethereum account
var account = wallet.generate();
var accountAddress = account.getAddressString()
var privateKey = account.getPrivateKey();

// TODO Replace with your RPC endpoint
var web3 = new Web3(new Web3.providers.HttpProvider(
    "http://testzvdky-dns-reg1.eastus.cloudapp.azure.com:8545"));

// Get the current nonce of the account
web3.eth.getTransactionCount(accountAddress, function (err, nonce) {
   var data = web3.eth.contract(abi).at(address).postMsg.getData("Hello World");
   var rawTx = {
     nonce: nonce,
     gasPrice: '0x00',
     gasLimit: '0x2FAF080',
     to: address,
     value: '0x00',
     data: data
   }
   var tx = new ethereumjs(rawTx);

   tx.sign(privateKey);

   var raw = '0x' + tx.serialize().toString('hex');
   web3.eth.sendRawTransaction(raw, function (txErr, transactionHash) {
     console.log("TX Hash: " + transactionHash);
     console.log("Error: " + txErr);
   });
 });
```

### <a name="deploy-smart-contract-with-truffle"></a>Intelligens szerződés üzembe helyezése a szarvasgombával

-   A szükséges kódtárak telepítése

```javascript
npm init

npm install truffle-hdwallet-provider --save
```
-   A szarvasgomba. js-ben adja hozzá a következő kódot a MetaMask-fiók zárolásának feloldásához és a PoA-csomópont belépési pontként való konfigurálásához a hívóbetűje kifejezés (MetaMask/Settings/Reveal Seed Words) megadásával.

```javascript
var HDWalletProvider = require("truffle-hdwallet-provider");

var rpc_endpoint = "XXXXXX";
var mnemonic = "twelve words you can find in metamask/settings/reveal seed words";

module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    },
    poa: {
      provider: new HDWalletProvider(mnemonic, rpc_endpoint),
      network_id: 3,
      gasPrice : 0
    }
  }
};

```

-   Üzembe helyezés a PoA hálózaton

```javascript
$ truffle migrate --network poa
```

### <a name="debug-smart-contract-with-truffle"></a>Intelligens szerződés hibakeresése a szarvasgombával

A szarvasgomba olyan helyi fejlesztésű hálózatot tartalmaz, amely az intelligens szerződések hibakereséséhez használható. [Itt](https://truffleframework.com/tutorials/debugging-a-smart-contract)megtekintheti a teljes oktatóanyagot.

### <a name="webassembly-wasm-support"></a>Webszerelvény (WASM) támogatása

A webszerelvények támogatása már engedélyezve van az újonnan üzembe helyezett PoA-hálózatokon. Lehetővé teszi az intelligens szerződések fejlesztését bármilyen nyelven, amely transpiles (Rust, C C++). További információért tekintse meg az alábbi hivatkozásokat

-   A webszerelvény paritásos áttekintése –<https://wiki.parity.io/WebAssembly-Home>

-   Oktatóanyag a paritásos technológiáról –<https://github.com/paritytech/pwasm-tutorial>

## <a name="reference"></a>Hivatkozás

### <a name="faq"></a>GYIK

#### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Észrevettem, hogy a hálózaton sok tranzakció van, amit helytelen\'t küldek. Honnan jönnek ezek?

A [személyes API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html)zárolásának feloldása nem biztonságos. A botok figyelik a kinyitott Ethereum-fiókokat, és megpróbálják kiüríteni a forrásokat. A robot feltételezi, hogy ezek a fiókok valódi-étert tartalmaznak, és az egyenleg megérkezésére tett kísérletet. Ne engedélyezze a személyes API-t a hálózaton. Ehelyett manuálisan is aláírja a tranzakciókat, például MetaMask vagy programozott módon, az [intelligens Szerződéssel való programozott interakciót](#programmatically-interacting-with-a-smart-contract)ismertető szakaszban leírtak alapján.

#### <a name="how-to-ssh-onto-a-vm"></a>Hogyan lehet SSH-t egy virtuális gépre?

Az SSH-port biztonsági okokból nem érhető el. [Az SSH-port engedélyezéséhez kövesse ezt az útmutatót](#ssh-access).

#### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Hogyan beállítani egy naplózási tagot vagy tranzakciós csomópontot?

A tranzakciós csomópontok olyan paritásos ügyfelek, amelyek a hálózattal vannak összefoglalva, de nem vesznek részt a konszenzusban. Ezek a csomópontok továbbra is használhatók Ethereum-tranzakciók küldésére és az intelligens szerződés állapotának beolvasására.
Ez jól működik olyan mechanizmusként, amely a hálózatban nem a szervezeten kívüli konzorcium tagjainak naplózását biztosítja. Ennek eléréséhez egyszerűen kövesse a 2. lépést a konzorcium növekedésével.

#### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Miért tart hosszú időt a MetaMask-tranzakciók?

Ha biztosítani szeretné, hogy a tranzakciók a megfelelő sorrendben legyenek beérkeznek, minden Ethereum-tranzakció eggyel nő. Ha egy másik hálózatban használt egy fiókot a MetaMask-ben, alaphelyzetbe kell állítania az alkalom értékét. Kattintson a Beállítások ikonra (3 sáv), a beállítások, majd a fiók visszaállítása lehetőségre. A rendszer törli a tranzakció előzményeit, és most újra elküldheti a tranzakciót.

#### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Meg kell-e adni a MetaMask-beli gázárak díját?

Az éter nem szolgál az igazoló hatóság konzorciumának céljára. Ezért nem kell megadnia a MetaMask tranzakcióinak elküldésekor a gázellátási díjat.

#### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Mi a teendő, ha a telepítés meghiúsul, mert nem sikerült kiépíteni az Azure-OMS?

A figyelés egy választható szolgáltatás. Bizonyos ritka esetekben, amikor az üzembe helyezés sikertelen, mert nem sikerült kiépíteni Azure Monitor erőforrást, Azure Monitor nélkül újra üzembe helyezhető.

#### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>A nyilvános IP-telepítések kompatibilisek-e a magánhálózati központi telepítésekkel?

Nem, a társak kétirányú kommunikációt igényelnek, így a teljes hálózatnak nyilvánosnak vagy magánjellegűnek kell lennie.

#### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Mi a hitelesítő hatóság várható tranzakciós sebessége?

A tranzakció sebessége nagymértékben függ a tranzakciók típusaitól és a hálózati topológiától.  Egyszerű tranzakciók használatával a másodpercenként átlagosan 400 tranzakciót mértünk egy több régióban üzembe helyezett hálózattal.

#### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Hogyan előfizetést az intelligens szerződési eseményekre?

A Ethereum-szolgáltató mostantól támogatja a webes szoftvercsatornák használatát.  A webes szoftvercsatorna URL-címének és portjának megkereséséhez keresse meg a telepítési e-mail vagy a központi telepítés kimenetét.

## <a name="next-steps"></a>További lépések

Első lépések a Ethereum-szolgáltatói [konzorcium](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) megoldás használatával.
