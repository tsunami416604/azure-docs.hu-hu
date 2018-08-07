---
title: Ethereum koncepció jogosultság Consortium
description: A Etherereum koncepció jogosultság Consortium megoldás használatával telepítheti és konfigurálhatja egy többtagú consortium Ethereum hálózati
services: azure-blockchain
keywords: ''
author: CodyBorn
ms.author: coborn
ms.date: 8/2/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: vamelech
ms.openlocfilehash: fcb35f23be384b3625e4e17e5dc2285b7eeb341a
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39530944"
---
# <a name="ethereum-proof-of-authority-consortium"></a>A koncepció jogosultság consortium Ethereum

[Ez a megoldás](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) célja, hogy könnyebben telepítése, konfigurálása és egy többtagú koncepció jogosultság Ethereum konzorciumhálózat minimális Azure és az Ethereum kapcsolatos szabályozzák.

Felhasználói adatok és a egy kattintással üzembe helyezés az Azure Portalon néhány minden tagjának helyezhet üzembe egy hálózati jelenlét, a Microsoft Azure Compute-tal, hálózati és tárolási szolgáltatások szerte a világon. Minden tag hálózati erőforrás-igényű egy elosztott terhelésű érvényesítő csomópontok készlete áll és amely egy alkalmazás vagy a felhasználó használhatja Ethereum-tranzakciók elküldeni.

## <a name="concepts"></a>Alapelvek

### <a name="terminology"></a>Terminológia

-   **Konszenzus** – a művelet, a blokk hitelesítése és létrehozása elosztott hálózati adatok szinkronizálása.

-   **Consortium tag** – egy entitás, amely részt vesz a Blockchain-hálózat konszenzus.

-   **Rendszergazdai** –, amellyel egy adott consortium tag jóváhagyásigénylő az Ethereum-fiók.

-   **Érvényesítési** – egy gép egy alkalmazásakor a consensus rendszergazda nevében Ethereum-fiókjához társított

### <a name="proof-of-authority"></a>A koncepció jogosultság

Azoknak, akik nem ismeri a blockchain-Közösséggel Ez a megoldás megjelenésével nagyszerű lehetőséget biztosít a technológia megismerheti az Azure-ban könnyen és konfigurálható módon. A koncepció-az-munka egy Sybil-ellenállás mechanizmust, amely önálló szabályozzák a hálózaton, és engedélyezheti a valós részvételi számítási költségeket is. Ez kiválóan ahol kriptovaluták versenyek elősegíti a hálózati biztonsági névtelen, nyissa meg a blockchain-hálózatokon. Azonban a privát/consortium hálózatok az alapul szolgáló távolítsuk nem rendelkezik értékkel. Egy másik protokoll, koncepció jogosultság, ahol konszenzus résztvevők ismertek permissioned hálózatok számára megfelelő, és megbízható. Adatbányászati nélkül koncepció jogosultság a hatékonyabb ugyanakkor megmaradnak helyreállításra a Byzantine hibatűrést.

### <a name="consortium-governance"></a>Consortium cégirányítási

Jogosultság igazolja, hogy a hálózat megfelelő állapotú hálózati hatóságok permissioned listáját megbízhatóak, mivel fontos az engedélylistában módosításokat valós mechanizmus biztosítására. Minden egyes üzembe helyezési intelligenskártya-szerződések és-portál a láncban lévő cégirányítási permissioned lista tartalmaz. Ha az ajánlott változás elérte a legtöbb vote consortium tagjai által, a módosítás végrehajtása. Ez lehetővé teszi, hogy új caiq résztvevők kell hozzáadni, vagy sérült a résztvevők, amely arra ösztönzi a egy hangon hálózati átlátható módon el kell távolítani.

### <a name="admin-account"></a>Rendszergazdai fiók

A koncepció jogosultság csomópontok központi telepítése során kell adnia egy rendszergazdai Ethereum-cím. Számos különféle mechanizmus használatával hozzon létre, és az Ethereum-fiókhoz. Miután ezt a címet hozzáadta-szolgáltatóként, a hálózaton, ezt a fiókot használhatja a cégirányítási részt. A rendszergazdai fiók is használható delegálása caiq részvételét a központi telepítés részeként létrehozott érvényesítő csomópontjain. Csak a nyilvános Ethereum-címet használja, mivel minden egyes rendszergazda úgy, hogy megfelel-e a kívánt biztonsági modellt az titkos kulcsaikat biztonságos rugalmasságot.

### <a name="validator-node"></a>Érvényesítési csomópont

A koncepció jogosultság protokoll érvényesítő csomópontok történnie a hagyományos miner csomópontok. Minden egyes érvényesítő rendelkezik egy egyedi Ethereum-identitás, amely egy intelligenskártya-szerződés engedélylistájához hozzáadása. Ha egy érvényesítő ezen a listán, a blokk létrehozását, vehet részt. Ez a folyamat kapcsolatos további információkért lásd: paritásos a dokumentáció [hatóság kerek konszenzus](https://wiki.parity.io/Aura). Minden egyes consortium tag helyezhet üzembe két vagy több érvényesítési csomópont öt régióban, a geo-redundancia biztosítása érdekében. Érvényesítő csomópontok kommunikálni más érvényesítő csomópontok hozására való konszenzus az alapul szolgáló elosztott Főkönyv állapota.
Ahhoz, hogy a valós részvételét a hálózaton, minden consortium tag van nem használhatják fel, mint az első olyan tag további érvényesítőket a hálózaton (ha az első olyan tag három érvényesítők telepíti, minden tagjának legfeljebb három érvényesítők).

### <a name="identity-store"></a>Ügyfélidentitás-tárolóval

Mivel minden tagjának fut egyszerre több érvényesítő csomópont fog rendelkezni, és minden egyes csomópont rendelkeznie kell egy permissioned identitás, fontos, hogy a érvényesítők biztonságosan szerezhetnek be egy egyedi aktív identitást, a hálózaton. Ennek megkönnyítéséhez hoztunk létre egy identitás-Store, amely az egyes tagok előfizetés, amely biztonságosan tárolja a létrehozott Ethereum-identitások megtörténik. Üzembe helyezés után a vezénylési tároló az egyes érvényesítő Ethereum titkos kulcs létrehozásához, és az Azure Key Vaultban tárolja. A paritásos csomópont indul, mielőtt először megszerzi érvényesül a Bérlés a fel nem használt identitás annak biztosítása érdekében az identitás nem dolgozza fel egy másik csomópontra. Az identitás biztosítja az ügyfélnek, amely lehetővé teszi a szolgáltató blokkok létrehozásának megkezdéséhez. Ha a üzemeltető virtuális gép szolgáltatáskimaradás következik be, az identitás bérleti kiadjuk, így a jövőben folytatásához az identitása helyettesítő csomópont.

### <a name="bootnode-registrar"></a>Bootnode regisztráló

Ahhoz, hogy a kapcsolat a könnyű, minden tagja a kapcsolati adatok halmaza fogja futtatni a [adatok API-végpont](#data-api). Ezek az adatok a csatlakozó tag számára biztosított társviszony-létesítési csomópontként bootnodes listáját tartalmazza. Ezek az adatok API részeként azt tartsa naprakészen a bootnode lista

### <a name="bring-your-own-operator"></a>A saját operátor használata

Gyakran a consortium tagja lesz szeretne részt venni a hálózat cégirányítási, de nem szeretné, működtetéséhez és karbantartásához, hogy az infrastruktúra. Ellentétben a hagyományos-rendszerek egyetlen operátor a hálózat működését a decentralizált modellen blockchain rendszerek között. Ahelyett, hogy megfelelően működjenek a hálózaton a központi közvetítő mindegyik, minden consortium tagja is engedélyezheti – delegálja hozhatna az operátor az infrastruktúra-felügyelettel. Ez lehetővé teszi a hibrid modell, ahol minden tagja lehet váltani, saját infrastruktúra működtetése és a egy eltérő partnert művelet. A delegált művelet munkafolyamat a következőképpen történik:

1.  **Consortium tag** állít elő, az Ethereum-cím (tartalmaz titkos kulcsot)

2.  **Consortium tag** nyilvános Ethereum-címet biztosít **operátor**

3.  **Operátor** telepíti és konfigurálja az Azure Resource Manager-megoldás használatának PoA érvényesítő csomópontok

4.  **Operátor** az RPC- és a felügyeleti végpontot biztosít **Consortium tag**

5.  **Consortium tag** fogadja a érvényesítő csomópontok kérés aláírásához használt titkos kulcsát **operátor** helyezte üzembe a nevükben részt venni

![A saját operátor használata](./media/ethereum-poa-deployment-guide/bring-you-own-operator.png)

### <a name="azure-monitor"></a>Azure Monitor

Ez a megoldás az Azure monitorral nyomon követéséhez a csomópont- és hálózati statisztika is tartalmaz. Az alkalmazásfejlesztők Ez az alapul szolgáló blockchain blokk. generációs statisztikák nyomon betekintést biztosít. Hálózati szolgáltatók az Azure Monitor segítségével gyorsan észlelheti és infrastruktúra statisztikák és lekérdezhető naplók hálózati kimaradások elkerülése. Lásd: [szolgáltatás megfigyelése](#service-monitoring) további részletekért.

### <a name="deployment-architecture"></a>Üzembe helyezési architektúrája

#### <a name="description"></a>Leírás

Ez a megoldás üzembe helyezhet egy- vagy többrégiós alapú többtagú Ethereum consortium network. Alapértelmezés szerint a távoli Eljáráshívás és társviszony-létesítési végpontok érhetők el nyilvános IP-cím egyszerűsített kapcsolódásának engedélyezése az előfizetések és a felhők felett. Javasoljuk, hogy kihasználva [paritásos a csatornákban szerződések](https://wiki.parity.io/Permissioning) alkalmazás szintű hozzáférés-vezérlők. VPN-eket, amelynek kihasználhatja a virtuális hálózati átjárók az előfizetések közötti kapcsolathoz mögött üzembe helyezett hálózatok is támogatja. Ezeket az üzemelő példányokat összetettebbek, ezért javasoljuk, hogy először indítsa el a nyilvános IP-modell.

#### <a name="consortium-member-overview"></a>Consortium tag áttekintése

Minden egyes consortium tag központi telepítés tartalmazza:

-   Virtuális M méretezési csoportok (VMSS) a PoA érvényesítők futtatásához

-   Az Azure Load Balancer az RPC történő társviszony-létesítés és Cégirányítási DApp kérelmek

-   Az érvényesítő identitások számára az Azure Key Vault

-   Azure Storage, a üzemeltetési állandó hálózati információkat, és összehangolása a naplóbérbeadási

-   Az Azure Monitor összesítésekor naplók és teljesítményének statisztikája

-   Virtuális hálózati átjáró (nem kötelező) a személyes virtuális hálózatok közötti VPN-kapcsolatok engedélyezése

![Üzembe helyezési architektúrája](./media/ethereum-poa-deployment-guide/deployment-architecture.png)

Használhatja azt a Docker-tárolók, a megbízhatóság és modularitást biztosítson. Azure Container Registry használatával üzemeltetése és szolgálja ki a rendszerverzióval ellátott képek egyes központi telepítésének részeként. A tárolórendszerképek áll:

-   Orchestrator

    -   Lefut egyszer az üzembe helyezés során

    -   Állít elő, az identitások és cégirányítási szerződések

    -   Az identitás Store tárolók identitások

-   Paritásos ügyfél

    -   Identitás az Identity Store bérletei

    -   Felderíti és csatlakozik a társaknak

-   EthStats ügynök

    -   Gyűjti a helyi naplók és -statisztikák RPC-n keresztül, majd leküldi az Azure Monitor

-   Cégirányítási DApp

    -   Cégirányítási szerződések folytatott interakcióra szolgáló webes felület

## <a name="governance-dapp"></a>Cégirányítási DApp

A koncepció jogosultság kulcsfontosságú decentralizált cégirányítási van. A cégirányítási DApp olyan előre telepített intelligens szerződések és a egy webalkalmazást, amely segítségével szabályozhatja a hitelesítésszolgáltatók, a hálózaton.
Hitelesítésszolgáltatók vannak felosztani rendszergazdai az identitások és érvényesítő csomópontok.
Rendszergazdák rendelkeznek delegálása caiq részvételi érvényesítő csomópontok készletét a teljesítmény. A rendszergazdák is szavazhat más rendszergazdák vagy onnan máshová a hálózaton.

![cégirányítási dapp](./media/ethereum-poa-deployment-guide/governance-dapp.png)

-   **Cégirányítási – decentralizált** hálózati tárolójában módosításokat a láncban lévő szavazási válassza ki a rendszergazdák által segítségével felügyelhetők.

-   **Delegálás érvényesítési -** hatóságok kezelheti az egyes PoA központi telepítési beállítása érvényesítő csomópontjai.

-   **Naplózható változások nyomon követése –** minden módosítása a blockchain biztosítása átlátszóság és auditability rögzítve van.

## <a name="how-to-guides"></a>Útmutatók

### <a name="deploy-ethereum-proof-of-authority"></a>Ethereum Proof-az-szolgáltató üzembe helyezése

Íme egy példa több résztvevős üzembe helyezési folyamat:

1.  Három tagokat hozzon létre egy MetaMask Ethereum-fiókot

2.  *A tag* üzembe helyezi az Ethereum PoA, azok nyilvános Ethereum-cím megadása

3.  *A tag* consortium URL-címe Itt *tag B* és *tag C*

4.  *Tag B* és *tag C* üzembe helyezése, Ethereum PoA, azok nyilvános Ethereum-cím megadása és *tag A*a consortium URL-címe

5.  *A tag* szavazat *tag B* rendszergazdaként

6.  *A tag* és *tag B* mindkét vote *tag C* rendszergazdaként

Ez a folyamat az Azure-előfizetés, amely támogathatja több virtuálisgép-méretezési csoportok üzembe helyezése és a managed disks szükséges. Ha szükséges, [hozzon létre egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/) megkezdéséhez.

Miután egy előfizetést védett, nyissa meg az Azure Portalon. Jelölje be "+", a piactér ("összes megjelenítése"), és keresse meg a Ethereum PoA Consortium.

Az alábbi szakasz végigvezeti az első tagtól erőforrás-igényű konfigurálása a hálózatban. Az üzembe helyezési folyamat öt lépést oszlik: alapjait, üzembe helyezési régiók, a hálózat mérete és a teljesítmény, Ethereum-beállítások, az Azure Monitor.

#### <a name="basics"></a>Alapvető beállítások

A **alapjai**, megadhatja a környezethez, például az előfizetés, erőforráscsoport és alapszintű virtuális gép tulajdonságainak standard paramétereinek értékét.

Egyes paraméterek részletes leírását a következő:

Paraméter neve|Leírás|Megengedett értékek|Alapértelmezett értékek    
---|---|---|---
Hozzon létre egy új vagy meglévő hálózaton való csatlakozás?|Hozzon létre egy új hálózatot, vagy egy már létező consortium network csatlakoztatása|Hozzon létre új való csatlakozás meglévő|Új létrehozása
E-mail cím (nem kötelező)|Az üzembe helyezés befejezése után az üzembe helyezéssel kapcsolatos adatokat az e-mailben értesítést kap.|Érvényes e-mail címet|NA
Virtuális gép felhasználóneve|Az egyes üzembe helyezett virtuális gépek (csak alfanumerikus karaktereket) a rendszergazdai jogosultságú felhasználónevet|1 – 64 karakter|NA
Hitelesítés típusa|A módszert a virtuális géphez.|Jelszó vagy SSH nyilvános kulcs|Jelszó
Jelszó (hitelesítési típus = jelszó)|Az egyes üzembe helyezett virtuális gépek a rendszergazdai fiók jelszava.  A jelszónak tartalmaznia kell a következő 3: 1 nagybetűt, 1 kisbetűt, 1 szám és 1 különleges karakter. Minden virtuális gép kezdetben van ugyanazt a jelszót, üzembe helyezés után módosíthatja a jelszót.|12 – 72 karakter|NA
SSH-kulcs (hitelesítési típus = a nyilvános kulcs)|A secure shell-kulcsot a távoli bejelentkezéshez használt.||NA
Előfizetés|Az előfizetés, melyben szeretné üzembe helyezni a consortium network||NA
Erőforráscsoport|Az erőforráscsoport, melyben szeretné üzembe helyezni a consortium network.||NA
Hely|Az Azure-régió erőforráscsoport.||NA

Üzembe helyezési minta az alább látható: ![alapszintű panel](./media/ethereum-poa-deployment-guide/basic-blade.png)

#### <a name="deployment-regions"></a>Üzembe helyezési régiók

Ezután üzembe helyezési régiók szerint adja meg a bemenetek száma régió(k) üzembe helyezéséhez a consortium network és Azure-régió alapján adott régiók számának kiválasztása. Felhasználó legfeljebb 5 régióban is üzembe. Azt javasoljuk, hogy az erőforráscsoport helyét alapjai szakaszban megfelelő első régió kiválasztásával. Fejlesztési-tesztelési hálózatok egy tagnak egy régió használata javasolt. Éles környezetben javasoljuk, hogy két vagy több régióban a magas rendelkezésre állású.

Egyes paraméterek részletes leírását a következő:

  Paraméter neve|Leírás|Megengedett értékek|Alapértelmezett értékek
  ---|---|---|---
  Régió(k) száma|Több régióban való üzembe helyezése a consortium network|1, 2, 3, 4, 5|1
  Első régióban|Első régióban való üzembe helyezése a consortium network|Az összes engedélyezett az Azure-régiók|NA
  Második régióban|Második régióban való üzembe helyezése a consortium network (csak akkor, ha több régióban beállítás van megadva 2 látható)|Az összes engedélyezett az Azure-régiók|NA
  Harmadik régiót|Harmadik régiót üzembe helyezéséhez a consortium network (Visible csak akkor, ha több régióban 3 van kiválasztva)|Az összes engedélyezett az Azure-régiók|NA
  Negyedik régió|Negyedik régióban való üzembe helyezése a consortium network (Visible csak akkor, ha több régióban 4 van kiválasztva)|Az összes engedélyezett az Azure-régiók|NA
  Ötödik régió|Ötödik régióban való üzembe helyezése a consortium network (Visible csak akkor, ha több régióban van jelölve, mint 5)|Az összes engedélyezett az Azure-régiók|NA

Üzembe helyezési minta az alább látható: ![üzembe helyezési régiók](./media/ethereum-poa-deployment-guide/deployment-regions.png)

#### <a name="network-size-and-performance"></a>A hálózat méretétől és a teljesítmény 

Ezután "hálózati méretének és teljesítményének" területen adja meg a bemenetek consortium a hálózaton, például számát és méretét, érvényesítő csomópontok méretét.
Az érvényesítő csomópont tárméret szabja meg a blockchain lehetséges méretét. Ez a telepítés után módosítható.

Egyes paraméterek részletes leírását a következő:

  Paraméter neve|Leírás|Megengedett értékek|Alapértelmezett értékek
  ---|---|---|---
  Elosztott terhelésű érvényesítő csomópontok száma|Az üzembe helyezni a hálózat részét képező érvényesítő csomópontok számát|2. 15.|2
  Érvényesítési csomópont tároló-teljesítményre|A telepített érvényesítő csomópontok biztonsági felügyelt lemez típusa.|Standard vagy prémium szintű|Standard
  Érvényesítési csomópont virtuális gépének mérete|Az érvényesítő csomópontok használt virtuálisgép-méretet.|Standard A, Standard D, Standard D-v2, Standard F sorozat, Standard DS és a Standard FS|Standard D1 v2

Üzembe helyezési minta az alább látható: ![hálózati mérete és a teljesítmény](./media/ethereum-poa-deployment-guide/network-size-and-performance.png)

#### <a name="ethereum-settings"></a>Ethereum-beállítások

Ezután Ethereum-beállítások területén adja meg a Ethereum-konfigurációs beállítások, például a hálózati azonosító és az Ethereum fiók jelszavát vagy képződés letiltása.

Egyes paraméterek részletes leírását a következő:

  Paraméter neve|Leírás|Megengedett értékek|Alapértelmezett értékek
  ---|---|---|---
Consortium tag azonosítója|Minden tagja részt vesz a consortium network konfigurálhatók az IP-címterek ütközések elkerülése érdekében társított azonosítója. A privát hálózat esetén Tagazonosító egyedinek kell lennie különböző szervezetekhez ugyanazon a hálózaton keresztül.  Egy egyedi Tagazonosító van szükség, akkor is, ha több régióban telepít ugyanazon a szervezeten belül. Jegyezze meg a paraméter értékét, mivel annak biztosítására ne legyen semmilyen ütközési csatlakozó tagjaival ossza meg kell.|0-255|NA
Hálózati azonosító|A hálózati Azonosítót a consortium Ethereum hálózati üzembe helyezve.  Minden egyes Ethereum hálózati rendelkezik a saját hálózati azonosító, az 1 folyamatban van a nyilvános hálózat azonosítója.|5 – 999,999,999|10101010
Rendszergazdai Ethereum-cím|Ethereum fiók címét, amely részt vesz a PoA cégirányítási szolgál.  Az Ethereum-cím generálásához MetaMask használatát javasoljuk.|0 x 42 alfanumerikus karakterek|NA
Speciális beállítások|Speciális beállítások Ethereum-beállítások|Engedélyezése vagy letiltása|Letiltás
Nyilvános IP-cím (Speciális beállítások = engedélyezés)|A hálózati virtuális hálózati átjáró mögött helyezi üzembe, és eltávolítja a társviszony-létesítési hozzáférés. Ha ezt a lehetőséget választja, minden tag kell használnia a kapcsolatot a virtuális hálózati átjáró kompatibilis.|Nyilvános IP-Címek privát virtuális hálózaton|Nyilvános IP-cím
Tranzakció engedély szerződés (Speciális beállítások = engedélyezés)|A tranzakció csatornákban szerződés ByteCode. Korlátozza az intelligens szerződés üzembe helyezésének és végrehajtásának permissioned listához Ethereum-fiókok.|Szerződés bytecode|NA

Üzembe helyezési minta az alább látható: ![ethereum-beállítások](./media/ethereum-poa-deployment-guide/ethereum-settings.png)

#### <a name="azure-monitor"></a>Azure Monitor

Az Azure Monitor panel lehetővé teszi, hogy az Azure Monitor erőforrás a hálózat konfigurálását. Az Azure Monitor gyűjt, és a surface hasznos metrikákat és naplókat a hálózatról, így gyorsan ellenőrizheti, hogy a hálózati állapotfigyelő vagy hibakeresési lehetővé teszi problémák.

  Paraméter neve|Leírás|Megengedett értékek|Alapértelmezett értékek
  ---|---|---|---
Figyelés|Az Azure Monitor lehetővé teszi|Engedélyezése vagy letiltása|Bekapcsolás
Csatlakozás a meglévő Log Analytics szolgáltatásba|Hozzon létre egy új Log Analytics-példányt az Azure Monitor részeként, vagy egy meglévő példányát csatlakoztatása|Új létrehozása vagy csatlakozás meglévő|Új létrehozása
Az Azure Monitor Location(Connect to Existing Azure Monitor = Create new)|A régió, ahol szeretné telepíteni az új Azure Monitor|Az Azure Monitor az összes régióban|NA
Meglévő Log Analytics-munkaterület azonosítójára (Kapcsolódás a meglévő Azure Monitor = való csatlakozás meglévő)|Munkaterület-Azonosítót a meglévő Log Analytics-példány||NA
Elsődleges kulcs meglévő Log Analytics (Kapcsolódás a meglévő Azure Monitor = való csatlakozás meglévő)|A meglévő Azure Monitor-példányhoz való csatlakozáshoz használt elsődleges kulcs||NA


Üzembe helyezési minta az alább látható: ![az azure monitor](./media/ethereum-poa-deployment-guide/azure-monitor.png)

#### <a name="summary"></a>Összegzés

Kattintson az összefoglalás panel a megadott bemeneti adatok és a központi telepítés előtti Alapszintű ellenőrzés futtatásához. Üzembe helyezés előtt, előfordulhat, hogy a sablon és paraméterek letöltése.

Tekintse át a jogi tudnivalók és adatvédelem a feltételeket, majd kattintson a "Vásárlás" üzembe helyezéséhez. Ha az üzemelő példány tartalmazza a virtuális hálózati átjárók, a központi telepítés tarthat, amíg 45 50 perc.

#### <a name="post-deployment"></a>Üzembe helyezés után

##### <a name="deployment-output"></a>Üzembe helyezés kimenet 

Az üzembe helyezés befejezése után fogja tudni elérni a szükséges paramétereket a megerősítő e-mailen keresztül, vagy az Azure Portalon keresztül. Ezeket a paramétereket lévő találja:

-   RPC Ethereum-végpont

-   Cégirányítási irányítópult URL-címe

-   Az Azure Monitor URL-címe

-   Adatok URL-címe

-   Virtuális hálózati átjáró erőforrás-azonosító (nem kötelező)

##### <a name="confirmation-email"></a>Visszaigazoló e-mailben 

Ha egy e-mail-címet ad meg ([alapjai szakasz](#basics)), egy e-mailben küldendő az e-mail-címre, amely a központi telepítés a kimeneti információt.

![üzembe helyezési e-mail](./media/ethereum-poa-deployment-guide/deployment-email.png)

##### <a name="portal"></a>Portál

A telepítés sikeresen befejeződött, és a kiépített erőforrások fogja tudni megtekinteni a kimeneti paraméterek az erőforráscsoportban.

1.  Keresse meg az erőforráscsoportot a portálon

2.  Navigáljon a *központi telepítések*

3.  Válassza ki az első üzembe helyezés ugyanazzal a névvel, az erőforráscsoport

4.  Válassza ki *kimenetek*

### <a name="growing-the-consortium"></a>A consortium növekvő

Bontsa ki a consortium, először csatlakoznia kell a fizikai hálózaton.
A nyilvános IP-alapú üzemelő példány használatával az első lépéseként zökkenőmentes. VPN mögött üzembe, tekintse meg a szakasz [csatlakozó virtuális hálózati átjáró](#connecting-vnet-gateways) részeként a hálózati kapcsolat végrehajtásához [2. lépés](#step-2-new-admin-deployment).

#### <a name="step-1-add-the-new-admin"></a>1. lépés: Az új rendszergazda hozzáadása

1.  Az új felügyeleti nyilvános Ethereum-cím összegyűjtése.

2.  Keresse meg a Cégirányítási DApp, és hozzon létre egy új felügyeleti az Ethereum-cím és a megfelelő alias

3.  Értesítés az új kérés meglévő más tagjaival, hogy azok szavazhat az új rendszergazda hozzáadásához

4.  Ha a szavazás elérte a 51 %-ot, a tag lesz felvéve rendszergazdaként

![rendszergazda hozzáadása](./media/ethereum-poa-deployment-guide/adding-admin.png)

#### <a name="step-2-new-admin-deployment"></a>2. lépés: Új felügyeleti telepítés

1.  Az alábbi adatokat megoszthatja a csatlakozó tag. Ez az információ az üzembe helyezés utáni e-mailben vagy a portálon keresztüli üzembe helyezési kimenetében található.

    -  Consortium adatok URL-címe

    -  Már telepítette a csomópontok száma

    -  Virtuális hálózati átjáró erőforrás-azonosítója (Ha a VPN segítségével)

2.  A központi telepítés tagja kell használnia a [megoldásban](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) a szem előtt tartja a következő hálózati jelenlétük telepítésekor:

    -  Válassza ki *csatlakozzon a meglévő*

    -  Válassza ki a többi taghoz valós ábrázolás biztosításához a hálózaton azonos számú érvényesítő csomópontok

    -  A megadott ugyanazt Ethereum-címet használja a [előző lépésben](#step-1-add-the-new-admin)

    -  Adja át a megadott *Consortium adatok URL-címének* a a *Ethereum-beállítások* lap

    -  Ha a hálózat többi részétől VPN mögött található, válassza ki a *magánhálózati VNet* a speciális szakasz alatt

#### <a name="step-3-delegate-validators"></a>3. lépés: A delegált érvényesítők

Az új felügyeleti kell engedélyezni a érvényesítők a nevében részt venni. Ez nem lehetséges automatikusan, mivel csak a tag szabályozza azok kulcsait.\*

_\*Az első tagtól csomópontjai a hálózaton automatikusan vehetők fel, mert azt is üzembehelyezésnél fordítson elő érvényesítő csomópontjai a hálózaton a képződés blokk be._

Az új felügyeleti végezze el az alábbi lépéseket:

1.  Nyissa meg a Cégirányítási DApp részeként üzembe helyezett *azok* üzembe helyezés

2.  Lépjen a érvényesítők lapra

3.  Válassza ki, amelyek telepítették, és kattintson a érvényesítők mindegyike **hozzáadása**

![érvényesítők hozzáadása](./media/ethereum-poa-deployment-guide/adding-validators.png)

#### <a name="connecting-vnet-gateways"></a>Virtuális hálózati átjárók csatlakoztatása

Tartozik egy magánhálózaton, akkor a tagokat a VNet-gateway-kapcsolatok keresztül vannak csatlakoztatva. Mielőtt tag csatlakozzon a hálózathoz és a tranzakciós forgalom jelenik meg, meglévő tagjára végső konfiguráció kell elvégeznie a VPN-átjáró fogadja el a kapcsolatot. Ez azt jelenti, hogy a csatlakozó tag Ethereum-csomópontok nem fog futni, amíg létrejön a kapcsolat. Javasoljuk, hogy redundáns hálózati kapcsolatokat (háló), a szoftver-és a egy meghibásodási pont csökkentése érdekében consortium létrehozása.

Miután üzembe helyezte az új tag, a meglévő tag a kétirányú kapcsolatot kell végrehajtania az új tag egy virtuális hálózati átjárókapcsolat beállításával. A meglévő tag elérésére lesz szüksége:

1.  A virtuális hálózati átjáró erőforrás-azonosító, a kapcsolódó tag (lásd az üzembe helyezés kimeneti)

2.  A megosztott kulcs

A meglévő tag a következő PowerShell-parancsfájlt a kapcsolatot kell futtatnia. Azt javasoljuk, hogy a jobb oldali navigációs sáv tetején a portálon található Azure Cloud Shell használatával.

![a cloud shell](./media/ethereum-poa-deployment-guide/cloud-shell.png)

```bash
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
$Subscription=Select-AzureRmSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzureRmVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

### <a name="service-monitoring"></a>Szolgáltatásfigyelés

Az Azure Monitor portál az üzembe helyezés e-mailben szereplő hivatkozást követve vagy megkeresése a paraméter üzembe helyezési kimenet keresheti \[OMS\_PORTÁL\_URL-cím\].

A portál első jelenik meg a magas szintű statisztikák és csomópont áttekintése.

![a figyelő kategória](./media/ethereum-poa-deployment-guide/monitor-categories.png)

Kiválasztásával **csomópont áttekintése** fogja irányítani, csomópontonkénti infrastruktúra statisztika megtekintése a portálon.

![csomópont-stats](./media/ethereum-poa-deployment-guide/node-stats.png)

Kiválasztásával **hálózati Stats** közvetlen, Ethereum hálózati statisztika megtekintése.

![hálózati statisztika](./media/ethereum-poa-deployment-guide/network-stats.png)

#### <a name="sample-log-analytics-queries"></a>Mintául szolgáló Log Analytics-lekérdezések

Ezek az irányítópultok mögött egy olyan lekérdezhető nyers naplókat. A nyers naplók segítségével az irányítópultokat személyre szabott, hibavizsgálat vagy riasztási küszöbérték beállítása. Az alábbiakban találja, amely lehet például lekérdezések futtatásakor a naplók keresése eszközt:

##### <a name="lists-blocks-that-have-been-reported-by-more-than-one-validator-useful-to-help-find-chain-forks"></a>Több mint egy érvényesítő által jelentett blokkok listája. Megkeresheti a lánc elágazásoknak hasznos.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

##### <a name="get-average-peer-count-for-a-specified-validator-node-averaged-over-5-minute-buckets"></a>A megadott érvényesítési csomópont átlagolt több mint 5 perces gyűjtők beolvasni társ átlagos száma.

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

Biztonsági okokból az SSH-port hozzáférés hálózati biztonsági csoportszabály által alapértelmezés szerint nem. A virtuálisgép-méretezési csoport eléréséhez állítsa példányok PoA hálózatban, ez a szabály módosításához kell \"engedélyezése\"

1.  Indítsa el az Azure Portal használatával üzembe helyezett erőforráscsoport-áttekintés szakaszban.

    ![ssh – áttekintés](./media/ethereum-poa-deployment-guide/ssh-overview.png)

2.  Válassza ki a hálózati biztonsági csoport

    ![ssh NSG-t](./media/ethereum-poa-deployment-guide/ssh-nsg.png)

3.  Válassza ki a \"engedélyezése – ssh\" szabály

    ![ssh-engedélyezése](./media/ethereum-poa-deployment-guide/ssh-allow.png)

4.  Változás \"művelet\" engedélyezéséhez

    ![ssh engedélyezése engedélyezése](./media/ethereum-poa-deployment-guide/ssh-enable-allow.png)

5.  Kattintson a \"mentése\" (a módosítások alkalmazása néhány percig eltarthat)

Most már távolról csatlakozhat a virtuális gépek a érvényesítő csomópontok SSH-n keresztül a megadott rendszergazdai felhasználónév és jelszó vagy SSH-kulccsal.
Az SSH-parancs futtatása az első érvényesítési csomópontjának eléréséhez, mint a sablon üzembe helyezési kimeneti paraméter szerepel "SSH\_TO\_első\_VL\_csomópont\_Tartomány1" (a minta üzembe helyezéshez: ssh - p 4000-es poaadmin\@leader4vb.eastus.cloudapp.azure.com). További tranzakciós csomópontok lekéréséhez egyesével a portszám (például tranzakciós első csomópontjára a 4000-es porton).

Ha egynél több régióban üzembe helyezett, módosítsa a fenti parancs a DNS-nevét vagy IP-címet a terheléselosztó az adott régióban. A DNS-nevét vagy IP-cím más régióban található, keresse meg az erőforrás-elnevezési \* \* \* \* \*- lbpip-reg\#, és a DNS-nevet és a IP-cím tulajdonságainak megtekintése.

### <a name="azure-traffic-manager-load-balancing"></a>Az Azure Traffic Manager-terheléselosztás

Az Azure Traffic Manager segítségével csökkenthető a leállások, és növelni a válaszkészséget a PoA hálózat által a bejövő forgalom útválasztását a több, különböző régióban üzemelő alkalmazáspéldányok között. Beépített állapot-ellenőrzési és automatikus azonnal biztosítanak az RPC-végpontok és a Cégirányítási DApp magas rendelkezésre állásának. Ez a funkció akkor hasznos, ha több régióban üzembe, és nem éles üzemre.

Traffic Manager használata segítségével:

-   Automatikus feladatátvétellel PoA hálózat rendelkezésre állásának javítása.

-   A hálózatok válaszkészségének útválasztási végfelhasználói számára az Azure-beli helyre növelheti a legalacsonyabb hálózati késéssel rendelkező.

Ha úgy dönt, hogy a Traffic Manager-profil létrehozása, a DNS-név, a profil használatával férhet hozzá a hálózatához. Egyszer másik consortium tagok lettek hozzáadva a hálózathoz, a Traffic Manager is használható a telepített érvényesítők terheléselosztásához.

#### <a name="creating-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Keresse meg és válassza \"Traffic Manager-profil\" kattintás után a \"erőforrás létrehozása\" gomb az Azure Portalon.

![Keresse meg az azure traffic Managerrel](./media/ethereum-poa-deployment-guide/traffic-manager-search.png)

Adjon meg egy egyedi nevet a profil, és válassza ki a PoA üzembe helyezése során létrehozott erőforráscsoportot.

![Hozzon létre traffic manager](./media/ethereum-poa-deployment-guide/traffic-manager-create.png)

Miután telepítették, majd válassza ki a példány az erőforráscsoportot. Az Áttekintés lap található eléréséhez a traffic manager DNS-név

![Keresse meg a traffic manager DNS](./media/ethereum-poa-deployment-guide/traffic-manager-dns.png)

Válassza ki a végpontok lapot, és kattintson a Hozzáadás gombra. Ezután módosítsa a célerőforrás típusa nyilvános IP-címet. Válassza ki a nyilvános IP-cím első terület\'s load balancert.

![Útválasztási a traffic manager](./media/ethereum-poa-deployment-guide/traffic-manager-routing.png)

Ismételje meg minden régióban üzembe helyezett a hálózatban. Miután a végpont a \"engedélyezve\" állapotát, akkor lehet automatikusan betölti és régió elosztott terhelésű, a traffic manager DNS-nevét. Most már használhatja a DNS-név helyére a \[CONSORTIUM\_adatok\_URL-cím\] a dokumentum többi paramétert.

## <a name="data-api"></a>Adatok API

Minden consortium tag üzemelteti a szükséges információkat, mások számára, hogy csatlakozhassanak a hálózathoz. A meglévő tag a [CONSORTIUM_DATA_URL] adja meg a tag üzembe helyezés előtt. Üzembe helyezés után csatlakozó tagja lesz lévő információk lekéréséhez a JSON-felület, a következő végpont:

`<CONSORTIUM_DATA_URL>/networkinfo`

A válasz való csatlakozáshoz tagok (képződés blokk, ABI, bootnodes szerződés érvényesítő beállítva), valamint adatait, javasoljuk, hogy a meglévő tag (érvényesítő címek) hasznos információkat fog tartalmazni. A szabványosítás a consortium kiterjeszteni a különböző felhőszolgáltatók használatát javasoljuk. Az API-t az alábbi struktúra használatával formázott JSON-választ adja vissza:
```json
{
  "$id": "", 
  "type": "object", 
  "definitions": {}, 
  "$schema": "http://json-schema.org/draft-07/schema#", 
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
## <a name="development"></a>Fejlesztés

### <a name="programmatically-interacting-with-a-smart-contract"></a>Egy intelligens szerződés programozott módon használata

> [!WARNING]
> Soha ne küldjön a Ethereum titkos kulcsot a hálózaton keresztül! Győződjön meg arról, hogy minden tranzakció van bejelentkezve helyileg először, és az aláírt tranzakció küldi el a hálózaton keresztül.

A következő példában használunk *ethereumjs-wallet* létrehozni egy Ethereum-címet, *ethereumjs-tx* helyileg, aláírásához és *weben 3* a nyers tranzakció történő küldéséhez a Ethereum RPC-végpont.

Ebben a példában ez egyszerű Hello-World intelligens szerződés fogjuk használni:

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

Ez a példa feltételezi, hogy a szerződés már üzembe van helyezve. Használhat *solc* és *weben 3* programozott módon üzembe helyezéséhez egy szerződést. Először telepítse a következő csomópontmodulok:
```
sudo npm install web3@0.20.2
sudo npm install ethereumjs-tx@1.3.6
sudo npm install ethereumjs-wallet@0.6.1
```
A nodeJS parancsprogrammal végezheti el a következőket:

-   Hozhatnak létre nyers tranzakció: postMsg

-   Jelentkezzen a tranzakció a létrehozott titkos kulccsal

-   Az aláírt tranzakció Ethereum hálózati beküldése

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

### <a name="debug-a-smart-contract-truffle-develop"></a>Egy intelligens szerződés (Truffle fejlesztése) hibakeresése

Truffle intelligens szerződés hibakeresési elérhető helyi fejlesztés hálózattal rendelkezik. A teljes oktatóanyagban talál [Itt](http://truffleframework.com/tutorials/debugging-a-smart-contract).

### <a name="webassembly-wasm-support"></a>WebAssembly (WASM) támogatása

WebAssembly támogatási már engedélyezve van az Ön számára újonnan üzembe helyezett PoA hálózatokon. Lehetővé teszi a intelligenskártya-szerződés fejlesztés bármilyen nyelven, hogy transpiles Web-szerelvény (Rust, C, C++). További információt az alábbi forrásokban talál

-   WebAssembly - paritásos áttekintése <https://wiki.parity.io/WebAssembly-Home>

-   A paritásos Tech - oktatóanyaga <https://github.com/paritytech/pwasm-tutorial>

## <a name="faq"></a>GYIK

### <a name="i-notice-a-bunch-of-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>E figyelje meg, hogy a hálózaton lévő tranzakciók többféle, amely helytelen\'t küldése. Ha ezeknek érkeznek?

Zárolásának feloldása nem biztonságos legyen a [személyes API](https://web3js.readthedocs.io/en/1.0/web3-eth-personal.html). Bot fiókok zárolásának Ethereum-fiókok figyelik, és próbálja meg a alapok kiürítési. A robot feltételezi, hogy ezeket a fiókokat valós-távolítsuk tartalmazhat, és megkísérli az egyenleg siphon elsőként. Ne engedélyezze a személyes API a hálózaton. Ehelyett előre jelentkezzen a tranzakciókat, vagy manuálisan segítségével egy wallet hasonlóan MetaMask vagy programozott módon, az intelligens szerződés programozott módon kommunikáló szakaszban foglaltak szerint.

### <a name="how-to-ssh-onto-a-vm"></a>Hogyan be egy virtuális Gépet SSH?

Az SSH-port, biztonsági okokból azt zárolását. Hajtsa végre a [Ez az útmutató az SSH-port engedélyezéséhez](#_Accessing_Nodes_via).

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Hogyan állíthatok be egy naplózási tag vagy a tranzakció csomópontot?

Tranzakció csomópontok olyan paritásos ügyfelek és a hálózat társviszonyban állnak, de nem részei konszenzus. Ezek a csomópontok továbbra is használható Ethereum-tranzakciók és az intelligens szerződés állapotadatokat olvasni.
Ez a módszer jól, amelyek biztosítják a jogszabályi a hálózat auditability mechanizmusként. Eléréséhez Ez egyszerűen hajtsa végre a 2. lépés a Consortium nőjön.

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Miért MetaMask tranzakciók hosszú ideig tart?

Annak érdekében, tranzakciók a megfelelő sorrendben érkeznek, minden egyes Ethereum tranzakció egy növekvő egyszeri tartalmaz. Ha korábban már használta az fiók MetaMask, egy másik hálózaton, állítsa vissza a nonce szüksége. Kattintson a beállítások ikonra (3-sávok) beállítások alaphelyzetbe állítása fiók. A tranzakciós előzményekben törölve lesz, és most már lehetősége van újraküldeni a tranzakciót.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Nyilvános IP-cím központi telepítések kompatibilisek-e privát hálózaton üzemelő példányok?

Nem, kell lennie kell a kétirányú kommunikáció, a teljes hálózat nyilvános vagy privát kell lennie.

## <a name="next-steps"></a>További lépések

Használatának első lépései a [Ethereum koncepció jogosultság Consortium](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) megoldás.
