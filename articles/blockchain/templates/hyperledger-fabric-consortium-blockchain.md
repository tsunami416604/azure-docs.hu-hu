---
title: Az Azure-ban Hyperledger Fabric Consortium Network
description: Sablon üzembe helyezéséhez és a egy Hyperledger Fabric consortium network konfigurálása
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/23/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: caleteet
manager: femila
ms.openlocfilehash: ce1afbd5499e798888e77f52d7b652e7e1f548fc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005817"
---
# <a name="hyperledger-fabric-consortium-network"></a>Hyperledger Fabric consortium network

Használhatja a Hyperledger Fabric consortium megoldássablon üzembe helyezéséhez és a egy Hyperledger Fabric consortium network konfigurálása az Azure-ban.

A cikk elolvasása után:

- Blockchain, Hyperledger Fabric és a bonyolultabb consortium műholdhálózati architektúrákat beszerzése
- Ismerje meg, hogyan telepítheti és konfigurálhatja egy Hyperledger Fabric consortium network, az az Azure Portalon

## <a name="about-blockchain"></a>Információ a blockchainről

Ha most ismerkedik a blockchain-Közösséggel, ez a megoldássablon egy nagyszerű lehetőséget biztosít a technológia megismerheti az Azure-ban könnyen és konfigurálható módon. Blockchain az alapul szolgáló technológiát Bitcoin; mögött azonban sokkal több, mint egy virtuális pénznem programkódként. Olyan meglévő adatbázis, az elosztott rendszer és a titkosítási technológiák összetett, amely lehetővé teszi a biztonságos több résztvevős számítási garanciákkal módosíthatatlansági, verifiability, auditability és rugalmasság, a támadásokkal szemben. A különböző protokollok szoftverbiztonsági használható különböző mechanizmusokat biztosít, ezek az attribútumok. [Hyperledger Fabric](https://github.com/hyperledger/fabric) egyik protokoll.

## <a name="consortium-architecture-on-azure"></a>Az Azure Consortium architektúra

Ahhoz, hogy az Azure-ban Hyperledger Fabric, nincsenek két elsődleges központi telepítési típusok támogatottak. Ezeket az üzemelő példányokat különböző topológiák kívánt cél alapján úgy lettek kialakítva.

- **Egyetlen virtuális gép, fejlesztői server** -célja, hogy a központi telepítési típus egy fejlesztési környezetként használt összeállításához és teszteléséhez Hyperledger Fabric épülő megoldásokkal.
- **Több virtuális gép üzembe helyezés horizontális** – a központi telepítési típus lett tervezve, amelyek a megosztott környezetben kihasználva különböző résztvevők konzorcium környezetekben.

Mindkét központi telepítés esetén, amelyek vannak Hyperledger fabric az alapvető építőelemeit azonosak.  Az üzemelő példányok közötti különbségek, hogyan vannak horizontálisan ezeket az összetevőket.

- **Hitelesítésszolgáltató csomópontok**: Egy hitelesítésszolgáltató identitások a hálózat által használt tanúsítványok előállításához használt futtató csomópont.
- **Megrendelő csomópontok**: A végrehajtási egy kézbesítési garanciával, például a teljes rendelési szórástól vagy elemi tranzakciókat kommunikációs szolgáltatást futtató csomópont.
- **Csomópontok társviszonyt**: Egy csomópont, amely véglegesíti a tranzakciókat, és fenntartja az állapot és a egy példányát az elosztott Főkönyv.
- **A CouchDB csomópontok**: A CouchDB-szolgáltatást, amelyek a adatbázis tárolásához, majd adja meg a chaincode adatok, JSON típusú Storage egyszerű kulcs-érték a kibontás lekérdezési futtatható csomópont.

### <a name="single-virtual-machine-architecture"></a>Egyetlen virtuális gép architektúrája

Amint azt korábban említettük a egyetlen virtuális gépének architektúrája: a fejlesztők egy kis erőforrás-igényű kiszolgálón az alkalmazások fejlesztéséhez használt épül fel. Látható az összes tárolót egyetlen virtuális gép fut. A rendelési szolgáltatás használ [egyedi](https://github.com/hyperledger/fabric/tree/master/orderer) ehhez a konfigurációhoz. Ez a konfiguráció *nem* tartalék hibatűrő rendezése szolgáltatást, de könnyű fejlesztési célokra tervezték.

![Egyetlen virtuális gép architektúrája](./media/hyperledger-fabric-consortium-blockchain/hlf-single-arch.png)

### <a name="multiple-virtual-machine-architecture"></a>Több virtuális gép architektúrája

A több virtuális gépet, a kibővített architektúrát, beépített magas rendelkezésre állással és a Méretezés minden egyes összetevő középpontjában. Ez az architektúra olyan sokkal jobban megfelel az éles környezetben szintű üzembe helyezéséhez.

![Több virtuális gép architektúrája](./media/hyperledger-fabric-consortium-blockchain/hlf-multi-arch.png)

## <a name="getting-started"></a>Első lépések

A kezdéshez Azure-előfizetés által támogatott több virtuális gépek és a standard szintű tárfiókok telepítése szükséges. Ha nem rendelkezik Azure-előfizetéssel, akkor az [hozzon létre egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/).

Ha már rendelkezik előfizetéssel, nyissa meg a [az Azure portal](https://portal.azure.com). Válassza ki **erőforrás létrehozása > Blockchain > Hyperledger Fabric Consortium**.

![Hyperledger Fabric egyetlen tag Blockchain Marketplace-sablon](./media/hyperledger-fabric-consortium-blockchain/marketplace-template.png)

## <a name="deployment"></a>Környezet

Az a **Hyperledger Fabric Consortium** sablont, válassza ki **létrehozás**.

A sablon üzembe helyezéséhez végigvezeti a több csomópontos konfigurálása [Hyperledger 1.3](https://hyperledger-fabric.readthedocs.io/en/release-1.3/) hálózati. Az üzembe helyezési folyamat négy lépést oszlik: Alapvető beállítások, Consortium hálózati beállítások, Fabric konfigurálása és a választható összetevők.

### <a name="basics"></a>Alapvető beállítások

A **alapjai**, adja meg minden olyan központi telepítés standard paraméterek értékeit. Például előfizetés, erőforráscsoport és az alapszintű virtuális gép tulajdonságait.

![Alapvető beállítások](./media/hyperledger-fabric-consortium-blockchain/basics.png)

| Paraméter neve | Leírás | Megengedett értékek |
|---|---|---|
**Erőforrás-előtag** | A telepítés részeként üzembe helyezett erőforrások-előtagja |6 karakter vagy kevesebb |
**Felhasználónév** | A felhasználónév, a rendszergazda minden ehhez a taghoz telepített virtuális gépekhez |1 – 64 karakter |
**Hitelesítés típusa** | A módszert a virtuális géphez |Jelszó vagy SSH nyilvános kulcs|
**Jelszó (hitelesítési típus = jelszó)** |Az egyes üzembe helyezett virtuális gépek a rendszergazdai fiók jelszava. A jelszónak tartalmaznia kell hármat a következő karakterek közül: 1 nagybetűt, 1 kisbetűt, 1 szám és 1 speciális karakter<br /><br />Minden virtuális gép kezdetben van ugyanazt a jelszót, üzembe helyezés után a jelszó megváltoztatásához|12 – 72 karakter|
**SSH-kulcs (hitelesítési típus = a nyilvános SSH-kulcs)** |A secure shell-kulcsot a távoli bejelentkezéshez használt ||
**Előfizetés** |Az előfizetés, melyben szeretné üzembe helyezni ||
**Erőforráscsoport** |Az erőforráscsoport, melyben szeretné üzembe helyezni a consortium network ||
**Hely** |Az Azure-régió, melyben szeretné üzembe helyezni az első tag ||

Kattintson az **OK** gombra.

### <a name="consortium-network-settings"></a>Consortium Network Settings

A **hálózati beállítások**, adja meg a bemenetek létrehozása vagy csatlakoztatása egy meglévő consortium hálózati és a szervezet beállításainak konfigurálása.

![Consortium Network Settings](./media/hyperledger-fabric-consortium-blockchain/network-settings.png)

| Paraméter neve | Leírás | Megengedett értékek |
|---|---|---|
**Hálózati konfiguráció** |Választhat, hozzon létre egy új hálózatot, vagy egy meglévő csatlakozhat. Ha úgy dönt, *csatlakozzon a meglévő*, meg kell adnia értékeket. |Új hálózati <br/> Csatlakozás a meglévő |
**HLF hitelesítésszolgáltató jelszó** |A hitelesítésszolgáltatók, a telepítés részeként létrehozott által létrehozott tanúsítványokat használt jelszó. A jelszónak tartalmaznia kell hármat a következő karakterek közül: 1 nagybetűt, 1 kisbetűt, 1 szám és 1 különleges karakter.<br /><br />Míg az összes virtuális gép kezdetben ugyanazt a jelszót, üzembe helyezés után módosíthatja a jelszót.|1 - 25 karakternél |
**A telepítő szervezet** |Testre szabhatja a szervezet és a tanúsítvány, vagy a használt alapértelmezett értékek találhatók.|Alapértelmezett <br/> Speciális |
**VPN-hálózati beállítások** | VPN-alagút átjáró fér hozzá a virtuális gépek kiépítése | Igen <br/> Nem |

Kattintson az **OK** gombra.

### <a name="fabric-specific-settings"></a>Fabric-specifikus beállításokat

A **Fabric konfigurációs**, hálózati méretének és teljesítményének megfelelő konfigurálásával, és adja meg a hálózat rendelkezésre állásának tartozó bemeneti. Például a number megrendelő és a társ-csomópontok, minden egyes csomópontot, és a virtuális gép mérete által használt adatmegőrzési-motor.

![Fabric-beállítások](./media/hyperledger-fabric-consortium-blockchain/fabric-specific-settings.png)

| Paraméter neve | Leírás | Megengedett értékek |
|---|---|---|
**Lépték típusa** |A központi telepítési típus több tárolót egyetlen virtuális gép vagy egy több virtuális gép egy horizontális felskálázási modellben.|Egyetlen virtuális Gépet vagy virtuális gépre kiterjedő |
**Virtuálisgép-lemez típusa** |A tárolás, a üzembe helyezett csomópontok biztonsági típusát. <br/> A rendelkezésre álló szabad típusaival kapcsolatos további tudnivalókért látogasson el a [válassza ki a lemez típusát](../../virtual-machines/windows/disks-types.md).|Standard SSD <br/> Prémium SSD |

### <a name="multiple-vm-deployment-additional-settings"></a>Több virtuális gép üzembe helyezése (további beállításokat)

![Több virtuálisgép-telepítéshez hálóbeállítások](./media/hyperledger-fabric-consortium-blockchain/multiple-vm-deployment.png)

| Paraméter neve | Leírás | Megengedett értékek |
|---|---|---|
**Megrendelő csomópontok száma** |A rendelés csomópontok száma (rendszerezése) egy tranzakciókat. <br />A rendelési szolgáltatás további információért látogasson el a Hyperledger [dokumentációja](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html) |1 – 4 |
**Megrendelő csomópont virtuális gépének mérete** |A használt megrendelő csomópontjai a hálózaton lévő virtuális gép mérete|Szabványos Bs<br />Standard DS-ben<br />Standard FS |
**A társ-csomópontok száma** | Tranzakciók végrehajtása, és az állapot és a egy példányát a Főkönyv karbantartása consortium tagokat tulajdonában lévő csomópontokat.<br />A rendelési szolgáltatás további információért látogasson el a Hyperledger [dokumentáció](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html).|1 – 4 |
**Csomópont-állapot megőrzését** |Az adatmegőrzés motor a társ csomópontja használ. Ez a motor társ csomópontonként konfigurálhatja. Tekintse meg több egyenrangú csomópontok részleteit alább olvashatja.|CouchDB <br />LevelDB |
**Társ csomópont virtuális gépének mérete** |A hálózaton lévő összes csomópont használt virtuálisgép-méret|Szabványos Bs<br />Standard DS-ben<br />Standard FS |

### <a name="multiple-peer-node-configuration"></a>Társ több csomópont-konfiguráció

Ez a sablon lehetővé teszi a társ csomópontonként adatmegőrzés motor válasszon. Például ha társ három csomóponttal rendelkezik is használhatja a CouchDB egy és a másik kettő a LevelDB.

![Társ több csomópont-konfiguráció](./media/hyperledger-fabric-consortium-blockchain/multiple-peer-nodes.png)

Kattintson az **OK** gombra.

### <a name="deploy"></a>Üzembe helyezés

A **összefoglalás**, tekintse át a megadott bemeneti adatok és a központi telepítés előtti Alapszintű ellenőrzés futtatásához.

![Összegzés](./media/hyperledger-fabric-consortium-blockchain/summary.png)

Jogi tudnivalók és adatvédelem feltételek áttekintése és kiválasztása **beszerzési** üzembe helyezéséhez. Virtuális gépek kiépítése folyamatban számától függően üzembe helyezési idő perc alatt több tíz, változhat pár percet vagy.

## <a name="next-steps"></a>További lépések

Most már készen áll az alkalmazás és chaincode fejlesztési ellen a Hyperledger consortium blockchain-hálózat.
