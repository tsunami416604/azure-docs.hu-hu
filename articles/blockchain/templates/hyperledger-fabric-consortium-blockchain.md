---
title: Hyperledger Fabric Consortium-megoldási sablon üzembe helyezése az Azure-ban
description: A Hyperledger Fabric Consortium hálózati megoldási sablonjának üzembe helyezése és konfigurálása az Azure-ban
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: caleteet
ms.openlocfilehash: be35cfa26204b36ad65da91252144b9167cb9e54
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325136"
---
# <a name="hyperledger-fabric-consortium-network"></a>Hyperledger Fabric Consortium-hálózat

A Hyperledger Fabric Consortium megoldás sablonnal üzembe helyezhet és konfigurálhat egy Hyperledger Fabric Consortium-hálózatot az Azure-ban.

A cikk elolvasása után:

- A blockchain, a Hyperledger-háló és a bonyolultabb konzorcium-hálózati architektúrák munkaismeretének beszerzése
- Ismerje meg, hogyan helyezhet üzembe és konfigurálhat egy Hyperledger Fabric Consortium-hálózatot a Azure Portal

## <a name="about-blockchain"></a>A blockchain névjegye

Ha még nem ismeri a blockchain közösségét, ez a megoldási sablon nagyszerű lehetőség arra, hogy könnyen és konfigurálható módon tudjon megismerheti a technológiát az Azure-ban. A Blockchain az a mögöttes technológia, amely a Bitcoin mögött van; azonban sokkal több, mint egy virtuális valuta használata. Ez a meglévő adatbázis-, elosztott rendszer-és titkosítási technológiák összetett változata, amely lehetővé teszi a többtényezős adatszámítást a módosíthatatlansági, a hitelesítés, a auditálás és a rugalmasság érdekében. A különböző protokollok különböző mechanizmusokat alkalmaznak ezeknek az attribútumoknak a biztosításához. A [Hyperledger-háló](https://github.com/hyperledger/fabric) egy ilyen protokoll.

## <a name="consortium-architecture-on-azure"></a>Konzorciumi architektúra az Azure-ban

A Hyperledger-háló Azure-ban való engedélyezéséhez két elsődleges központi telepítési típus támogatott. Ezek az üzembe helyezések úgy vannak kialakítva, hogy a kívánt cél alapján különböző topológiákat fogadjon.

- **Egyetlen virtuális gép, fejlesztői kiszolgáló** – ez a központi telepítési típus a Hyperledger-hálóra épülő megoldások létrehozásához és teszteléséhez használt fejlesztői környezet.
- **Több virtuális gép, kibővíthető üzembe helyezés** – ez a központi telepítési típus olyan környezetekhez lett tervezve, amelyek egy megosztott környezetet hasznosító különböző résztvevők konzorciumát modellezik.

Az üzembe helyezés során a Hyperledger-háló magját alkotó építőelemek megegyeznek.  Az üzemelő példányok közötti különbségek az összetevők vertikális felskálázása.

- **Hitelesítésszolgáltatói csomópontok**: egy hitelesítésszolgáltatót futtató csomópont, amely a hálózatban található identitásokhoz használt tanúsítványok előállítására szolgál.
- **Rendezési csomópontok**: a kommunikációs szolgáltatást futtató csomópont, amely egy kézbesítési garanciát valósít meg, például a teljes megrendelés szórását vagy az atomi tranzakciókat.
- **Társ-csomópontok**: olyan csomópont, amely véglegesíti a tranzakciókat, és karbantartja az elosztott Főkönyv állapotát és másolatát.
- **CouchDB-csomópontok**: olyan csomópont, amely képes a CouchDB szolgáltatás futtatására, amely képes az állapot-adatbázis tárolására, valamint a chaincode-adatok széles körű lekérdezését, valamint az egyszerű kulcs/érték és a JSON típusú tároló közötti kibővítést.

### <a name="single-virtual-machine-architecture"></a>Egyetlen virtuális gép architektúrája

Ahogy azt korábban említettük, az egyetlen virtuálisgép-architektúra a fejlesztők számára készült, hogy az alkalmazások fejlesztéséhez használt kis helyigényű kiszolgálót használják. Az összes megjelenített tároló egyetlen virtuális gépen fut. A rendezési szolgáltatás a [Solot](https://github.com/hyperledger/fabric/tree/master/orderer) használja ehhez a konfigurációhoz. Ez a konfiguráció *nem* hibatűrő megrendelési szolgáltatás, azonban a célja, hogy a fejlesztési célokra könnyű legyen.

![Egyetlen virtuális gép architektúrája](./media/hyperledger-fabric-consortium-blockchain/hlf-single-arch.png)

### <a name="multiple-virtual-machine-architecture"></a>Több virtuális gép architektúrája

A több virtuális gép, a horizontális Felskálázási architektúra a magas rendelkezésre állással és az alapszintű összetevők skálázásával készült. Ez az architektúra sokkal megfelelőbb az éles környezetben üzemelő környezetekben.

![Több virtuális gép architektúrája](./media/hyperledger-fabric-consortium-blockchain/hlf-multi-arch.png)

## <a name="getting-started"></a>Első lépések

A kezdéshez olyan Azure-előfizetésre van szükség, amely támogatja több virtuális gép és standard Storage-fiók telepítését. Ha nem rendelkezik Azure-előfizetéssel, [létrehozhat egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/)is.

Ha már rendelkezik előfizetéssel, lépjen a [Azure Portal](https://portal.azure.com). Válassza **az erőforrás létrehozása > Blockchain > Hyperledger Fabric Consortium**elemet.

![Hyperledger-háló egyetlen tag Blockchain Marketplace-sablon](./media/hyperledger-fabric-consortium-blockchain/marketplace-template.png)

## <a name="deployment"></a>Környezet

A **Hyperledger Fabric Consortium** sablonban válassza a **Létrehozás**elemet.

A sablon központi telepítése végigvezeti a többcsomópontos [Hyperledger 1,3](https://hyperledger-fabric.readthedocs.io/en/release-1.3/) hálózat konfigurálásán. A központi telepítési folyamat négy lépésből áll: alapértékek, konzorcium hálózati beállításai, háló konfigurációja és opcionális összetevők.

### <a name="basics"></a>Alapvető beállítások

Az **alapvető beállítások**területen minden központi telepítéshez meg kell adnia a szabványos paraméterek értékeit. Ilyen például a, az előfizetés, az erőforráscsoport és az alapszintű virtuális gép tulajdonságai.

![Alapvető beállítások](./media/hyperledger-fabric-consortium-blockchain/basics.png)

| Paraméter neve | Leírás | Megengedett értékek |
|---|---|---|
**Erőforrás-előtag** | Az üzembe helyezés részeként kiépített erőforrások nevének előtagja |6 karakter vagy kevesebb |
**Felhasználónév** | Az adott tag számára központilag telepített virtuális gépek rendszergazdájának felhasználóneve |1-64 karakter |
**Hitelesítés típusa** | A virtuális géphez való hitelesítés módszere |Jelszó vagy nyilvános SSH-kulcs|
**Jelszó (hitelesítés típusa = jelszó)** |Az egyes telepített virtuális gépek rendszergazdai fiókjának jelszava. A jelszónak a következő karakterek közül hármat tartalmaznia kell: 1 nagybetűs karakter, 1 kisbetűs karakter, 1 szám és 1 speciális karakter.<br /><br />Habár a virtuális gépek kezdetben ugyanazzal a jelszóval rendelkeznek, a kiépítés után megváltoztathatja a jelszót|12-72 karakter|
**SSH-kulcs (hitelesítés típusa = SSH nyilvános kulcs)** |A távoli bejelentkezéshez használt Secure Shell-kulcs ||
**Előfizetés** |Az előfizetés, amelyre telepíteni kívánja ||
**Erőforráscsoport** |Az erőforráscsoport, amelyre a konzorcium-hálózatot telepíteni kell ||
**Hely** |Az Azure-régió, amelyre az első tagot telepíteni kell ||

Kattintson az **OK** gombra.

### <a name="consortium-network-settings"></a>Konzorcium hálózati beállításai

A **hálózati beállítások**területen adja meg a meglévő konzorcium-hálózat létrehozásához vagy csatlakoztatásához szükséges bemeneteket, és konfigurálja a szervezet beállításait.

![Konzorcium hálózati beállításai](./media/hyperledger-fabric-consortium-blockchain/network-settings.png)

| Paraméter neve | Leírás | Megengedett értékek |
|---|---|---|
**Hálózati konfiguráció** |Dönthet úgy is, hogy létrehoz egy új hálózatot, vagy egy meglévőt csatlakoztat. Ha a *Csatlakozás meglévőt*választja, további értékeket kell megadnia. |Új hálózat <br/> Csatlakozás meglévőhöz |
**HLF HITELESÍTÉSSZOLGÁLTATÓ jelszava** |Az üzembe helyezés részeként létrehozott hitelesítésszolgáltatók által létrehozott tanúsítványokhoz használt jelszó. A jelszónak a következő karakterek közül hármat tartalmaznia kell: 1 nagybetűs karakter, 1 kisbetűs karakter, 1 szám és 1 speciális karakter.<br /><br />Habár az összes virtuális gép kezdetben ugyanazzal a jelszóval rendelkezik, a kiépítés után megváltoztathatja a jelszót.|1-25 karakter |
**Szervezet beállítása** |Testreszabhatja a szervezet nevét és tanúsítványát, vagy megadhatja a használni kívánt alapértelmezett értékeket.|Alapértelmezett <br/> Speciális |
**VPN-hálózat beállításai** | VPN-alagút átjárójának kiépítése a virtuális gépek eléréséhez | Igen <br/> Nem |

Kattintson az **OK** gombra.

### <a name="fabric-specific-settings"></a>Háló-specifikus beállítások

A háló **konfigurációjában**konfigurálja a hálózati méretet és a teljesítményt, és adja meg a bemeneteket a hálózat rendelkezésre állásához. Például:, szám-sorrendi és társ-csomópontok, az egyes csomópontok által használt adatmegőrzési motor és a virtuális gép mérete.

![Háló beállításai](./media/hyperledger-fabric-consortium-blockchain/fabric-specific-settings.png)

| Paraméter neve | Leírás | Megengedett értékek |
|---|---|---|
**Méretezés típusa** |Egyetlen virtuális gép központi telepítési típusa több tárolóval vagy több virtuális géppel egy kibővíthető modellben.|Egyetlen virtuális gép vagy több virtuális gép |
**VM-lemez típusa** |Az egyes központilag telepített csomópontok biztonsági mentésének típusa. <br/> Ha többet szeretne megtudni a rendelkezésre álló lemezek típusairól, látogasson el [a lemez típusának kiválasztására](../../virtual-machines/windows/disks-types.md).|Standard SSD <br/> Prémium SSD |

### <a name="multiple-vm-deployment-additional-settings"></a>Több virtuális gép üzembe helyezése (további beállítások)

![Háló beállításai több virtuálisgép-telepítéshez](./media/hyperledger-fabric-consortium-blockchain/multiple-vm-deployment.png)

| Paraméter neve | Leírás | Megengedett értékek |
|---|---|---|
**A rendezési csomópontok száma** |Azon csomópontok száma, amelyek egy blokkba rendezik a tranzakciókat. <br />A rendezési szolgáltatással kapcsolatos további részletekért tekintse meg a Hyperledger [dokumentációját](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html) . |1 – 4 |
**A rendezési csomópont virtuális gép mérete** |A hálózatban található, a rendelést használó csomópontok számára használt virtuális gép mérete|Standard BS,<br />Standard DS,<br />Szabványos FS |
**Társ-csomópontok száma** | Azok a csomópontok, amelyek a tranzakciókat végrehajtó konzorcium tagjai, valamint az állapotot és a Főkönyv egy másolatát futtatják.<br />A rendezési szolgáltatással kapcsolatos további részletekért tekintse meg a Hyperledger [dokumentációját](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html).|1 – 4 |
**Csomópont állapotának megőrzése** |A társ-csomópontok által használt adatmegőrzési motor. Ezt a motort a társ-csomóponton konfigurálhatja. A részleteket lásd alább több társ-csomóponthoz.|CouchDB <br />LevelDB |
**Társ csomópont virtuális gép mérete** |A hálózatban lévő összes csomóponthoz használt virtuális gép mérete|Standard BS,<br />Standard DS,<br />Szabványos FS |

### <a name="multiple-peer-node-configuration"></a>Több társ-csomópontos konfiguráció

Ez a sablon lehetővé teszi, hogy kiválassza az adatmegőrzési motort egy társ-csomóponton. Ha például három társ-csomóponttal rendelkezik, a CouchDB a másik kettőben használhatja a LevelDB.

![Több társ-csomópontos konfiguráció](./media/hyperledger-fabric-consortium-blockchain/multiple-peer-nodes.png)

Kattintson az **OK** gombra.

### <a name="deploy"></a>Üzembe helyezés

Az **Összefoglalás**területen tekintse át a megadott bemeneteket és az alapszintű telepítés előtti ellenőrzés futtatását.

![Összefoglalás](./media/hyperledger-fabric-consortium-blockchain/summary.png)

Tekintse át a jogi és adatvédelmi feltételeket, és válassza a **vásárlás** lehetőséget a telepítéshez. A kiépített virtuális gépek számától függően a központi telepítési idő néhány perctől akár tíz percre is változhat.

## <a name="next-steps"></a>Következő lépések

Most már készen áll arra, hogy az alkalmazás-és chaincode-fejlesztésre koncentráljon a Hyperledger Consortium blockchain-hálózatán.
