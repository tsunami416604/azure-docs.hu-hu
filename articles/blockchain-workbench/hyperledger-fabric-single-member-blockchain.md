---
title: Hyperledger háló konzorcium
description: A háló konzorcium Hyperledger megoldás sablon használatával telepíthet és konfigurálhat egy egyetlen tagot hálózati
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 7b60c086896506e5883607db48a64d2a2efbd967
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655115"
---
# <a name="hyperledger-fabric-single-member-network"></a>Egy tag hálózati Hyperledger háló

A Hyperledger háló konzorcium megoldás sablon használatával telepíthet és konfigurálhat egy Hyperledger háló egyetlen tagot (több csomópontos) hálózatot.

A cikk elolvasása után:

- Szerezze be a blockchain Hyperledger háló és bonyolultabb konzorcium ismeretét hálózati architektúra
- Megtudhatja, hogyan telepítheti és konfigurálhatja a single-tag Hyperledger háló konzorcium hálózaton belül az Azure-portálon

## <a name="about-blockchain"></a>Blockchain kapcsolatos

Ha most ismerkedik a blockchain Közösség, ez az Azure könnyen és konfigurálható módon a technológiával kapcsolatos további nagyszerű lehetőséget. Blockchain az alapul szolgáló technológiát mögött Bitcoin; azonban sokkal nem csak egy virtuális pénznem tekintenek. Meglévő adatbázis, a rendszer elosztott és a titkosítási technológiák összetett, amely lehetővé teszi a biztonságos több résztvevős számítási garanciák immutability, verifiability, auditability és rugalmasság támadható meg a legyen. Különböző protokollok arra, hogy ezek az attribútumok másik mechanizmust alkalmaz. [Hyperledger háló](https://github.com/hyperledger/fabric) egyik ilyen protokoll.

## <a name="consortium-architecture-on-azure"></a>Az Azure-on konzorcium architektúrája

Ez a sablon telepíti a topológia tesztelése, és a felhasználók számára egy éles szimulálása szervezet (egyetlen tagot). A központi telepítés egy régió, sokkal több régióba bővíthető a többcsomópontos hálózat foglalja magában.

A hálózati csomópont három típusú áll:

1. **Tag csomópont**: regisztrálja és kezeli a hálózat tagjai háló tagsági szolgáltatást futtató csomópont. Ez a csomópont végül csoportosíthatók a méretezhetőség és a magas rendelkezésre állás érdekében, azonban az ebben a tesztkörnyezetben egyetlen tagot csomópontja használható.
2. **Megrendelő csomópontok**: a végrehajtási kézbesítési növekvő, például az összes kommunikáció szolgáltatást futtató csomópont sorrendben szórás vagy atomi tranzakciók.
3. **Csomópontok partnert**: egy csomópont, amelyek megmutatják a véglegesítések számát tranzakciók és állapotát és az elosztott főkönyvi másolatot tart fenn.

## <a name="getting-started"></a>Első lépések

Megkezdéséhez szüksége lesz egy Azure-előfizetés, amely képes támogatni a több virtuális gépek és standard szintű storage-fiókok telepítése. Ha még nem rendelkezik Azure-előfizetéssel, akkor [egy ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/).

Alapértelmezés szerint a legtöbb előfizetéstípusok kis telepítési topológia támogatja a anélkül, hogy a kvóta növeléséhez. Egy tag a legkisebb lehetséges telepítési lesz szüksége:

- 5 virtuális gépek (5 mag)
- 1 virtuális hálózat
- 1 terheléselosztó
- 1 nyilvános IP-cím

Ha rendelkezik előfizetéssel, navigáljon a [Azure-portálon](https://portal.azure.com). Válassza ki **+**, jelölje be **Blockchain**, és válassza ki **Hyperledger háló egyetlen tag Blockchain**.

![Hyperledger háló egyetlen tag Blockchain piactér sablon](./media/hyperledger-fabric-single-member-blockchain/marketplace-template.png)

## <a name="deployment"></a>Környezet

Elindításához válassza ki a **Hyperledger háló egyetlen tag Blockchain** kattintson **létrehozása**. Ekkor megnyílik a **alapjai** panel a varázslóban.

A sablon-üzembehelyezés végigvezeti a többcsomópontos hálózat konfigurálása. Az üzembe helyezési folyamat három lépést oszlik: alapjai, hálózati konfigurációt, és a háló konfigurációja.

### <a name="basics"></a>Alapvető beállítások

A a **alapjai** panelen adja meg a telepítést, például az előfizetés, erőforráscsoport és alapszintű virtuálisgép-tulajdonságokat a szabványos paraméter értékét.

![Alapvető beállítások](./media/hyperledger-fabric-single-member-blockchain/basics.png)

Paraméter neve| Leírás| Megengedett értékek|Alapértelmezett érték
---|---|---|---
**Erőforrás-előtag**| A telepített erőforrások elnevezési alapjaként használt karakterlánc.|6 karakter vagy kevesebb|NA
**Virtuális gép felhasználónév**| A felhasználónév a rendszergazda minden, az ehhez a taghoz telepített virtuális gépek.|1 – 64 karakter hosszúságú lehet|azureuser
**Hitelesítés típusa**| A módszer a virtuális gép felé történő hitelesítésre.|Jelszó vagy SSH nyilvános kulcs|Jelszó
**Jelszó (hitelesítési típus = jelszó)**|A telepített virtuális gépek mindegyikének a rendszergazdai fiók jelszavát. A jelszónak tartalmaznia kell a 3. a következőket: 1 nagybetűt, 1 kisbetűt, 1 számot vagy 1 különleges karaktert.<br /><br />Bár minden virtuális gép kezdetben rendelkezik ugyanazzal a jelszóval, módosíthatja a jelszó kiépítése után.|12 - 72 karakter|NA
**Az SSH-kulcsot (hitelesítési típus = nyilvános kulcs)**|A biztonságos rendszerhéj kulcs távoli bejelentkezés.||NA
**Hozzáférés korlátozása IP-cím szerint**|A beállítás típusának meghatározására, hogy végpont ügyfélhozzáférés korlátozott-e vagy sem.|Igen/nem| Nem
**Engedélyezett IP-cím vagy alhálózat (hozzáférés korlátozása IP-cím szerint = Yes)**|Az IP-cím vagy IP-címek készlete, amely az ügyfél végpont elérheti, ha a hozzáférés-vezérlés engedélyezve van.||NA
**Előfizetés** |Az előfizetés, amelyre telepíteni szeretné.
**Erőforráscsoport** |Az erőforráscsoport, amelyre a konzorcium hálózati telepíteni szeretné.||NA
**Hely** |Az Azure-régió, amelyre az első tagtól telepíteni szeretné ** s hálózat erőforrásigényét.

### <a name="network-size-and-performance"></a>A hálózat méretétől és teljesítmény

A következő **hálózati mérete és a teljesítmény,** adja meg a bemeneti adatokat a konzorcium hálózatokon, például a tagság megrendelő és társ csomópontok száma méretének. Válassza ki az infrastruktúra-beállításokat és a virtuális gép méretét.

![A hálózat méretétől és teljesítmény](./media/hyperledger-fabric-single-member-blockchain/network-size-performance.png)

Paraméter neve| Leírás| Megengedett értékek|Alapértelmezett érték
---|---|---|---
**Tagság csomópontok száma**|A tagsági szolgáltatást futtató csomópontok száma. A tagság szolgáltatás további részletekért tekintse meg biztonsági & Tagságszolgáltatás alatt a Hyperledger [dokumentáció](https://media.readthedocs.org/pdf/hyperledger-fabric/latest/hyperledger-fabric.pdf).<br /><br />Ez az érték 1 csomópontra jelenleg korlátozott, de a következő verzióban fürtszolgáltatásokkal kibővítési támogatása.|1| 1
**Megrendelő csomópontok száma** |A rendezés csomópontok száma (rendszerezése) tranzakciók blokk. Ez--> Terjengős és zavaró utasítás. A rendezési szolgáltatás további részletekért látogasson el a Hyperledger [dokumentáció](http://hyperledger-fabric.readthedocs.io/en/latest/orderingservice.html).<br /><br />Az értéke 1 csomópontra jelenleg korlátozott. |1 |1
**Társ csomópontok száma**| Tranzakciók végrehajtása, és az állapot és a főkönyvi másolatát karbantartása konzorcium tagokat tulajdonában lévő csomópontok.<br /><br />A rendezési szolgáltatás további részletekért látogasson el a Hyperledger [dokumentáció](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html).|3| 3 – 9
**Tárolási teljesítmény**|A telepített csomópontok biztonsági tárolási típusát. Tárolási kapcsolatos további információkért látogasson el a [Microsoft Azure Storage bemutatása](https://docs.microsoft.com/azure/storage/common/storage-introduction) és [prémium szintű Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage).|Standard vagy prémium|Standard
**Virtuális gép mérete** |A virtuális gép méretét, a hálózaton levő összes csomóponthoz használt|Standard A<br />Standard D<br />Standard D-2-es<br />Standard F sorozat<br />Standard DS-ben<br />és a szabványos FS|Standard D1_v2

### <a name="fabric-specific-settings"></a>Háló vonatkozó beállításokat

Végezetül a **Hálóbeállításokat**, adja meg a háló konfigurációs beállításokat.

![Fabric-beállítások](./media/hyperledger-fabric-single-member-blockchain/fabric-settings.png)

Paraméter neve| Leírás| Megengedett értékek|Alapértelmezett érték
---|---|---|---
**A rendszerindítási felhasználónév**| A kezdeti hitelesített felhasználó, amely a tag szolgáltatáshoz a telepített regisztrálva lesz.|9-es vagy kevesebb karakter|Rendszergazda
**A rendszerindítási jelszó a háló hitelesítésszolgáltató**|A rendszergazdai jelszó a háló hitelesítésszolgáltató fiók a tagság csomópont importálni.<br /><br />A jelszónak tartalmaznia kell egy számot, egy nagybetű és egy kisbetűt.|legalább 12 karaktert|NA

### <a name="deploy"></a>Üzembe helyezés

A **összegzés**, tekintse át a megadott bemeneti adatok és a központi telepítés előtti alapvető érvényesítési futtatásához.

![Összegzés](./media/hyperledger-fabric-single-member-blockchain/summary.png)

Olvassa el a jogi tudnivalók és adatvédelem feltételeket és a **beszerzési** központi telepítéséhez. Attól függően, hogy a virtuális gépek kiépítése folyamatban számát központi telepítés alkalmával változhat néhány percig, amíg a több tíz perc.

### <a name="accessing-nodes"></a>Csomópontok elérése

A telepítés befejeződése után egy **áttekintése** jelenik meg.

![Központi telepítés](./media/hyperledger-fabric-single-member-blockchain/deployments.png)

Ha a képernyő nem jelenik meg automatikusan (lehet, hogy mivel körül során a kezelési portál helyezte a központi telepítés futott), a bal oldali navigációs sávban a erőforráscsoportok lapján mindig talál. Kattintson az Ugrás az 1. lépésben megadott erőforráscsoport nevére a **áttekintése** lap.

Áttekintés megjeleníti az erőforrásokat, amelyek a megoldás-sablon üzembe helyezése. Ismerje meg azokat a rendszer, de ezen a képernyőn is elérheti a _a kimeneti paraméterek_ a sablon által létrehozott. A kimeneti paraméterek fogja diktálni hasznos információkat a Hyperledger háló rendszerű hálózathoz való csatlakozáskor.

A kimeneti paraméterek szeretne használni, először kattintson az a **központi telepítések** az erőforráscsoport panelről lapján. Az üzembe helyezési előzményeket jelenik meg.

![Üzembe helyezési előzmények](./media/hyperledger-fabric-single-member-blockchain/deployment-history.png)

Az üzembe helyezési előzményeket kattintson az első központi telepítése a részletekért tekintse meg a listában.

![Központi telepítésének részletei](./media/hyperledger-fabric-single-member-blockchain/deployment-details.png)

Az részletei képernyőn láthatja, a központi telepítés három hasznos kimeneti paramétert kiegészítve összefoglalása:

- A _API-VÉGPONT_ is használható, ha az alkalmazás a hálózaton.
- A _ELŐTAG_ is néven _telepítési előtag_ , egyedi módon azonosítja az erőforrások és a központi telepítés. A parancssori eszközök használata esetén használandó.
- A _SSH-FIRST-VM_ , előre összeállított ssh parancs a megfelelő paraméterekkel ad az első virtuális gép a hálózat eléréséhez szükséges; Hyperledger háló esetében a Fabric-hitelesítésszolgáltató csomópont lesz.

Távolról csatlakozhat a virtuális gépek minden egyes csomópontja SSH-kapcsolaton keresztül a megadott rendszergazdai felhasználónév és jelszó vagy SSH-kulccsal. Mivel a virtuális gépek csomópont nem rendelkezik a saját nyilvános IP-címek, szüksége lesz a terheléselosztó keresztül halad, és adja meg a portszámot. Az SSH-parancstól elérni az első tranzakció csomópont a harmadik sablon kimeneti ** SSH-FIRST--VM (az üzembe helyezési minta: `sh -p 3000 azureuser@hlf2racpt.northeurope.cloudapp.azure.com`). Ahhoz, hogy további tranzakciós csomópontokra, egyesével a portszám (például az első tranzakció csomópont van-e a porton 3000, a második pedig a 3001, a harmadik pedig a 3002, stb.).

## <a name="next-steps"></a>További lépések

Most már készen áll alkalmazás és a Hyperledger konzorcium blockchain hálózati elleni chaincode fejlesztési összpontosíthat.
