---
title: Hyperledger Fabric Consortium egyetlen tag
description: A Hyperledger Fabric Consortium megoldássablon segítségével telepítheti és konfigurálhatja az egyetlen tag hálózati
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/29/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: a6aa6d925d47ad9d24de68342f4a6e76a1d9d81f
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54828230"
---
# <a name="hyperledger-fabric-single-member-network"></a>Hyperledger Fabric egyetlen tag hálózati

A Hyperledger Fabric Consortium megoldássablon segítségével telepítheti és konfigurálhatja egy Hyperledger Fabric egyetlen tagot (több csomópontos) hálózatot.

A cikk elolvasása után:

- Blockchain, Hyperledger Fabric és a bonyolultabb consortium műholdhálózati architektúrákat beszerzése
- Ismerje meg, hogyan telepítheti és konfigurálhatja egy egyszemélyes Hyperledger Fabric consortium network, az Azure Portalon

## <a name="about-blockchain"></a>Információ a blockchainről

Ha most ismerkedik a blockchain-Közösséggel, ez a megoldássablon egy nagyszerű lehetőséget biztosít a technológia megismerheti az Azure-ban könnyen és konfigurálható módon. Blockchain az alapul szolgáló technológiát Bitcoin; mögött azonban sokkal több, mint egy virtuális pénznem programkódként. Olyan meglévő adatbázis, az elosztott rendszer és a titkosítási technológiák összetett, amely lehetővé teszi a biztonságos több résztvevős számítási garanciákkal módosíthatatlansági, verifiability, auditability és rugalmasság, a támadásokkal szemben. A különböző protokollok szoftverbiztonsági használható különböző mechanizmusokat biztosít, ezek az attribútumok. [Hyperledger Fabric](https://github.com/hyperledger/fabric) egyik protokoll.

## <a name="consortium-architecture-on-azure"></a>Az Azure Consortium architektúra

Ez a sablon üzembe helyez egy topológia segítségével tesztelheti, és szimulálhatja a felhasználók számára egy éles szervezet (egyetlen tag). A központi telepítés magában foglalja, egyetlen régióban, hamarosan több régióban ki kell bővíteni, több csomópontos hálózat.

A hálózati csomópont három típusú tevődik össze:

1. **Tag csomópont**: A Fabric tagsági szolgáltatást, amely regisztrálja és kezeli a hálózat tagjai futtató csomópont. Ez a csomópont lehet, hogy fürtözött, a méretezhetőség és a magas rendelkezésre állású; Azonban a laborban egyetlen tag csomópont fogja használni.
2. **Megrendelő csomópontok**: A végrehajtási egy kézbesítési garanciával, például a teljes rendelési szórástól vagy elemi tranzakciókat kommunikációs szolgáltatást futtató csomópont.
3. **Csomópontok társviszonyt**: Egy csomópont, amely véglegesíti a tranzakciókat, és fenntartja az állapot és a egy példányát az elosztott Főkönyv.

## <a name="getting-started"></a>Első lépések

Első lépésként szüksége lesz egy Azure-előfizetés által támogatott több virtuális gépek és a standard szintű tárfiókok telepítése. Ha nem rendelkezik Azure-előfizetéssel, akkor az [hozzon létre egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/).

Alapértelmezés szerint a legtöbb előfizetés-típus egy kis méretű üzembe helyezési topológia támogatja a anélkül, hogy a kvóta növeléséhez. A legkisebb lehetséges telepítési egy lesz szüksége:

- 5 virtuális gépek (5 mag)
- 1 virtuális hálózat
- 1 load balancerhez
- 1 public IP address

Ha már rendelkezik előfizetéssel, nyissa meg a [az Azure portal](https://portal.azure.com). Válassza ki **+** válassza **Blockchain**, és válassza ki **Hyperledger Fabric egyetlen tag Blockchain**.

![Hyperledger Fabric egyetlen tag Blockchain Marketplace-sablon](./media/hyperledger-fabric-single-member-blockchain/marketplace-template.png)

## <a name="deployment"></a>Környezet

Jelölje a **Hyperledger Fabric egyetlen tag Blockchain** kattintson **létrehozás** megnyitásához a **alapjai** panelen a varázslóban.

A sablon üzembe helyezéséhez végigvezeti a több csomópontos hálózati konfigurálása. Az üzembe helyezési folyamat három lépésben oszlik: Alapvető beállítások, a hálózati konfiguráció vagy a Fabric konfigurálása.

### <a name="basics"></a>Alapvető beállítások

Az a **alapjai** panelen adja meg minden olyan központi telepítés standard paraméterek értékeit. Például előfizetés, erőforráscsoport és az alapszintű virtuális gép tulajdonságait.

![Alapvető beállítások](./media/hyperledger-fabric-single-member-blockchain/basics.png)

Paraméter neve| Leírás| Megengedett értékek|Alapértelmezett érték
---|---|---|---
**Erőforrás-előtag**| A telepített erőforrások elnevezési alapként használt karakterlánc.|6 karakter vagy kevesebb|NA
**Virtuális gép felhasználóneve**| Azon felhasználó felhasználónevét, a rendszergazda minden ehhez a taghoz telepített virtuális gépekhez.|1 – 64 karakter|azureuser
**Hitelesítés típusa**| A módszert a virtuális géphez.|Jelszó vagy SSH nyilvános kulcs|Jelszó
**Jelszó (hitelesítési típus = jelszó)**|Az egyes üzembe helyezett virtuális gépek a rendszergazdai fiók jelszava. A jelszónak tartalmaznia kell hármat a következő karakterek közül: 1 nagybetűt, 1 kisbetűt, 1 szám és 1 különleges karakter.<br /><br />Minden virtuális gép kezdetben van ugyanazt a jelszót, üzembe helyezés után módosíthatja a jelszót.|12 – 72 karakter|NA
**SSH-kulcs (hitelesítési típus = a nyilvános kulcs)**|A secure shell-kulcsot a távoli bejelentkezéshez használt.||NA
**IP-cím alapján korlátozzák a hozzáférést**|A beállítás típusa határozza meg, hogy ügyfél végponti hozzáférés korlátozott-e vagy sem.|Igen/nem| Nem
**Engedélyezett IP-cím vagy alhálózat (IP-cím alapján korlátozzák a hozzáférést = Yes)**|Az IP-cím vagy IP-címek készletét, amely az ügyfél végponti hozzáférhet, ha engedélyezve van a hozzáférés-vezérlés.||NA
**Előfizetés** |Az előfizetés, amelyre telepíteni szeretné.
**Erőforráscsoport** |Az erőforráscsoport, melyben szeretné üzembe helyezni a consortium network.||NA
**Hely** |Az Azure-régió, melyben szeretné üzembe helyezni az első olyan tag ** s hálózat erőforrásigényét.

### <a name="network-size-and-performance"></a>A hálózat méretétől és a teljesítmény

Ezután **méretének és teljesítményének, hálózati** méretét, a consortium network bemenetek megadása. Például, a tagsági megrendelő és társ csomópontok száma. Válassza ki az infrastruktúra-beállításokat és a virtuális gép méretét.

![A hálózat méretétől és a teljesítmény](./media/hyperledger-fabric-single-member-blockchain/network-size-performance.png)

Paraméter neve| Leírás| Megengedett értékek|Alapértelmezett érték
---|---|---|---
**A tagság csomópontok száma**|A tagság szolgáltatást futtató csomópontok száma. A tagság szolgáltatás további részletekért tekintse meg a biztonság és a Hyperledger tagsági szolgáltatások [dokumentáció](https://media.readthedocs.org/pdf/hyperledger-fabric/latest/hyperledger-fabric.pdf).<br /><br />Ez az érték 1 csomópontos hozzáférés jelenleg korlátozott, de a következő felülvizsgálata fürtszolgáltatásokkal horizontális felskálázás támogatása.|1| 1
**Megrendelő csomópontok száma** |Rendelés csomópontok száma (rendszerezése) tranzakciókat blokk.--> Ez utasítás Terjengős és zavarosnak. A rendelési szolgáltatás további információért látogasson el a Hyperledger [dokumentáció](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html).<br /><br />Ez az érték 1 csomópontos jelenleg korlátozva. |1 |1
**A társ-csomópontok száma**| Tranzakciók végrehajtása, és az állapot és a egy példányát a Főkönyv karbantartása consortium tagokat tulajdonában lévő csomópontokat.<br /><br />A rendelési szolgáltatás további információért látogasson el a Hyperledger [dokumentáció](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html).|3| 3 - 9
**Tároló-teljesítményre**|A tárolás, a üzembe helyezett csomópontok biztonsági típusát. További storage-ról, látogasson el [a Microsoft Azure Storage bemutatása](https://docs.microsoft.com/azure/storage/common/storage-introduction) és [prémium szintű Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage).|Standard vagy prémium szintű|Standard
**Virtuális gép mérete** |A hálózaton lévő összes csomópont használt virtuálisgép-méret|Standard A,<br />Standard D<br />Standard D-v2,<br />Standard F sorozat<br />Standard DS-ben<br />and Standard FS|Standard D1_v2

### <a name="fabric-specific-settings"></a>Fabric-specifikus beállításokat

Végül **Hálóbeállítások**, adja meg a háló kapcsolatos konfigurációját.

![Fabric-beállítások](./media/hyperledger-fabric-single-member-blockchain/fabric-settings.png)

Paraméter neve| Leírás| Megengedett értékek|Alapértelmezett érték
---|---|---|---
**Rendszer-indításkori felhasználónév**| A kezdeti hitelesített felhasználó az üzembe helyezett hálózati tag szolgáltatásban regisztrált.|9-es vagy kevesebb karakter|admin
**Rendszer-indításkori jelszó a háló hitelesítésszolgáltató**|A rendszergazdai jelszót a importálja a tagság csomópont Fabric CA-fiókhoz használt.<br /><br />A jelszónak tartalmaznia kell egy számot, egy nagybetű és egy kisbetűs karaktert.|12 vagy több karaktert|NA

### <a name="deploy"></a>Üzembe helyezés

A **összefoglalás**, tekintse át a megadott bemeneti adatok és a központi telepítés előtti Alapszintű ellenőrzés futtatásához.

![Összegzés](./media/hyperledger-fabric-single-member-blockchain/summary.png)

Tekintse át a jogi és adatvédelmi szempontból, és kattintson a **beszerzési** üzembe helyezéséhez. Virtuális gépek kiépítése folyamatban számától függően üzembe helyezési idő perc alatt több tíz, változhat pár percet vagy.

### <a name="accessing-nodes"></a>Csomópontok elérése

Miután az üzembe helyezés befejeződött, egy **áttekintése** jelenik meg.

![Központi telepítés](./media/hyperledger-fabric-single-member-blockchain/deployments.png)

Ha a képernyő nem jelenik meg automatikusan (esetleg közben a felügyeleti portálon áthelyezhető, mert az üzembe helyezés futott), a bal oldali navigációs sávban az erőforráscsoportok lapon mindig megtalálják. Kattintson a nyissa meg az 1. lépésben megadott erőforráscsoport nevére a **áttekintése** lapot.

Áttekintés a megoldás sablon által üzembe helyezett erőforrásokat sorolja fel. Lesz, áttekintheti, de ezen a képernyőn is elérheti a _kimeneti paraméterek_ a sablon által létrehozott. Ezek a kimeneti paraméterek kap hasznos információkat a Hyperledger Fabric hálózathoz való csatlakozáskor.

A kimeneti paraméter elérésére, először kattintson az a **központi telepítések** fülre az erőforráscsoport panelen. Az üzembe helyezési előzmények jelenik meg.

![Üzembe helyezési előzmények](./media/hyperledger-fabric-single-member-blockchain/deployment-history.png)

Az üzembe helyezési előzmények kattintson a listában, és tekintse meg a részleteket az első üzembe helyezés.

![Telepítés részletei](./media/hyperledger-fabric-single-member-blockchain/deployment-details.png)

A részletek képernyőn megjelenik a központi telepítés három hasznos kimeneti paramétert kiegészítve összefoglalása:

- A _API-VÉGPONT_ is használható, ha telepít egy alkalmazást a hálózaton.
- A _ELŐTAG_ , más néven is néven _üzembe helyezési előtag_ , egyedi módon azonosítja az erőforrások és a központi telepítés. Ha a parancssori eszközök használatával lesz használható.
- A _SSH-FIRST--VM_ biztosít, előre összeállított ssh parancs a megfelelő paraméterekkel a hálózaton; az első virtuális Géphez való csatlakozáshoz szükséges Hyperledger fabric a Fabric-CA csomópontra lesz.

Távolról csatlakozhat a virtuális gépek SSH-n keresztül minden egyes csomópont esetében a megadott rendszergazdai felhasználónév és jelszó vagy SSH-kulccsal. Mivel a csomópont virtuális gépek nem rendelkeznek saját nyilvános IP-címek, szüksége lesz a terheléselosztón keresztül, és adja meg a portszámot. Az első tranzakció csomópontjának eléréséhez az SSH-parancs a harmadik a sablon kimeneti *SSH-FIRST--VM* (az üzembe helyezési minta: `sh -p 3000 azureuser@hlf2racpt.northeurope.cloudapp.azure.com`). További tranzakciós csomópontok lekéréséhez egyesével a portszám (például az első tranzakció csomópont van a porton 3000, a második 3001, a harmadik pedig a 3002, stb.).

## <a name="next-steps"></a>További lépések

Most már készen áll az alkalmazás és chaincode fejlesztési ellen a Hyperledger consortium blockchain-hálózat.
