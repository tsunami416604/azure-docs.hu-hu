---
title: Hibrid kapcsolat kétszintű alkalmazással | Microsoft dokumentumok
description: Ismerje meg, hogyan telepíthet virtuális berendezéseket és UDR-t egy többrétegű alkalmazáskörnyezet létrehozásához az Azure-ban
services: virtual-network
documentationcenter: na
author: KumudD
manager: carmonm
editor: tysonn
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: kumud
ms.openlocfilehash: 1bdc485dfb352144e8a8d0fb75965cbb78288e2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "64575579"
---
# <a name="virtual-appliance-scenario"></a>Virtuális berendezés forgatókönyve
A nagyobb Azure-ügyfelek körében gyakori forgatókönyv, hogy egy kétrétegű alkalmazást kell biztosítaniaz internetnek, miközben lehetővé teszi a hozzáférést a hátsó réteghez egy helyszíni adatközpontból. Ez a dokumentum végigvezeti a felhasználó által definiált útvonalak (UDR), a VPN-átjáró és a hálózati virtuális berendezések használatával a következő követelményeknek megfelelő kétrétegű környezet üzembe helyezéséhez:

* A webalkalmazáscsak a nyilvános internetről érhető el.
* Az alkalmazást tároló webkiszolgálónak képesnek kell lennie a háttéralkalmazás-kiszolgáló elérésére.
* Az internetről a webalkalmazásba irányuló összes forgalomnak tűzfalvirtuális berendezésen keresztül kell mennie. Ez a virtuális készülék csak internetes forgalomra használható.
* Az alkalmazáskiszolgálóra irányuló összes forgalomnak egy tűzfal virtuális berendezésén keresztül kell mennie. Ez a virtuális berendezés a háttérrendszer-kiszolgáló elérésére, valamint a helyszíni hálózatról vpn-átjárón keresztül érkező hozzáférésre lesz használva.
* A rendszergazdáknak képesnek kell lenniük a tűzfal virtuális készülékeinek helyszíni számítógépükről történő kezelésére, egy harmadik, kizárólag felügyeleti célokra használt tűzfal-virtuális berendezés használatával.

Ez egy szabványos peremhálózati (más néven DMZ) forgatókönyv, amely dmz-t és védett hálózatot biztosít. Az ilyen forgatókönyv az Azure-ban nsg-k, tűzfal virtuális készülékek használatával, vagy a kettő kombinációjával hozhatlétre. Az alábbi táblázat az NSG-k és a tűzfal virtuális készülékei közötti előnyöket és hátrányokat mutatja be.

|  | Előnyök | Hátrányok |
| --- | --- | --- |
| NSG |Nincs költség. <br/>Integrálva az Azure RBAC-ba. <br/>Szabályok hozhatók létre az Azure Resource Manager-sablonokban. |A komplexitás nagyobb környezetekben változhat. |
| Tűzfal |Teljes hozzáférés az adatsíkhoz. <br/>Központi felügyelet tűzfalkonzolon keresztül. |Tűzfalkészülék költsége. <br/>Nincs integrálva az Azure RBAC-kal. |

Az alábbi megoldás a tűzfal virtuális berendezéseit használja a peremhálózat (DMZ)/védett hálózati forgatókönyv megvalósításához.

## <a name="considerations"></a>Megfontolandó szempontok
A fent ismertetett környezetet az Azure-ban a ma elérhető különböző funkciók használatával telepítheti, az alábbiak szerint.

* **Virtuális hálózat (VNet)**. Az Azure virtuális hálózat a helyszíni hálózathoz hasonlóan működik, és egy vagy több alhálózatra bontható a forgalom elkülönítése és az aggodalmak elkülönítése érdekében.
* **Virtuális készülék**. Számos partner biztosít virtuális berendezéseket az Azure Marketplace-en, amelyek a fent leírt három tűzfalhoz használhatók. 
* **Felhasználó által definiált útvonalak (UDR)**. Az útvonaltáblák tartalmazhatnak Az Azure-hálózat által használt UD-okat a virtuális hálózaton belüli csomagok áramlásának szabályozásához. Ezek az útvonaltáblák alhálózatokra alkalmazhatók. Az Azure egyik legújabb funkciója az a képesség, hogy egy útvonaltábla a GatewaySubnet, lehetővé teszi, hogy továbbítsa az Azure virtuális hálózatba érkező összes forgalmat egy hibrid kapcsolat egy virtuális berendezés.
* **IP-továbbítás**. Alapértelmezés szerint az Azure hálózati motor továbbítja a csomagokat a virtuális hálózati csatoló kártyák (NIC) csak akkor, ha a csomag cél IP-címe megegyezik a hálózati adapter IP-címét. Ezért ha egy UDR határozza meg, hogy egy csomagot kell küldeni egy adott virtuális berendezés, az Azure hálózati motor eldobja a csomagot. Annak érdekében, hogy a csomag egy virtuális gépre (ebben az esetben egy virtuális készülékre) kerüljön, amely nem a csomag tényleges célja, engedélyeznie kell az IP-továbbítást a virtuális készülék számára.
* **hálózati biztonsági csoportok (NSG-k)** számára. Az alábbi példa nem használja az NSG-ket, de ebben a megoldásban használhatja az alhálózatokra és/vagy hálózati adapterekre alkalmazott NSG-ket az alhálózatok és hálózati adapterek forgalom további szűréséhez.

![IPv6-kapcsolat](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

Ebben a példában van egy előfizetés, amely a következőket tartalmazza:

* 2 erőforráscsoport, nem jelenik meg a diagramon. 
  * **ONPREMRG**. A helyszíni hálózat szimulálásához szükséges összes erőforrást tartalmazza.
  * **AZURERG**. Az Azure virtuális hálózati környezetéhez szükséges összes erőforrást tartalmazza. 
* Az **onpremvnet** nevű virtuális hálózat az alább felsoroltak szerint szegmentált helyszíni adatközpontot utánoz.
  * **onpremsn1**. Az Ubuntut futtató virtuális gépet (VM) tartalmazó alhálózat, amely egy helyszíni kiszolgálót utánoz.
  * **onpremsn2**. Az Ubuntut futtató virtuális gép egy rendszergazda által használt helyszíni számítógépet utánzó alhálózatot tartalmaz.
* Van egy **opfw** nevű tűzfal virtuális készülék **onpremvnet** karbantartására használt alagút **azurevnet.**
* Az **azurevnet** nevű virtuális hálózat az alábbiak szerint szegmentált.
  * **azsn1**. Külső tűzfal alhálózat, amely kizárólag a külső tűzfalhoz használatos. Az összes internetes forgalom ezen az alhálózaton keresztül érkezik. Ez az alhálózat csak a külső tűzfalhoz kapcsolt hálózati adaptert tartalmaz.
  * **azsn2**. Az internetről elérhető webkiszolgálóként futó virtuális gép előtér-alhálózata.
  * **azsn3**. Háttér-alhálózat, amely egy háttéralkalmazás-kiszolgálót futtató virtuális rendszert üzemeltet, amelyet az előtér-webkiszolgáló fog elérni.
  * **azsn4**. A felügyeleti alhálózat kizárólag az összes tűzfal virtuális készülékéhez biztosít felügyeleti hozzáférést. Ez az alhálózat csak a megoldásban használt minden egyes tűzfal virtuális készülékhez tartalmaz hálózati adaptert.
  * **GatewaySubnet**. Az ExpressRoute és a VPN-átjáró számára szükséges Azure-hibrid kapcsolatalhálózat az Azure virtuális hálózatok és más hálózatok közötti kapcsolat biztosításához szükséges. 
* Az **azurevnet-hálózatban** 3 tűzfal virtuális készülék található. 
  * **AZF1**. Külső tűzfal a nyilvános interneten egy nyilvános IP-cím erőforrás használatával az Azure-ban. Győződjön meg arról, hogy rendelkezik egy sablont a Marketplace-en, vagy közvetlenül a készülék szállítójától, amely rendelkezik egy 3-NIC virtuális berendezés.
  * **AZF2**. Az **azsn2 és az azsn3** közötti forgalom szabályozására használt belső tűzfal . **azsn3** Ez is egy 3-NIC virtuális készülék.
  * **AZF3**. A helyszíni adatközpont rendszergazdái számára elérhető felügyeleti tűzfal, amely az összes tűzfalberendezés kezelésére használt felügyeleti alhálózathoz csatlakozik. A 2-NIC virtuális berendezés sablonokat a Piactéren talál, vagy közvetlenül a készülék gyártójától kérhet egyet.

## <a name="user-defined-routing-udr"></a>Felhasználó által definiált útválasztás (UDR)
Az Azure minden egyes alhálózata összekapcsolható egy UDR-táblával, amely meghatározza, hogy az adott alhálózatban kezdeményezett forgalom hogyan kerül átirányítva. Ha nincs megadva udrs, az Azure alapértelmezett útvonalakat használ, hogy a forgalom egyik alhálózatból a másikba folyjon. Az UDR-ek jobb megértéséhez látogasson el [a Mik azok a felhasználó által definiált útvonalak és az IP-továbbítás.](virtual-networks-udr-overview.md)

Annak érdekében, hogy a kommunikáció a megfelelő tűzfalberendezésen keresztül történik, a fenti utolsó követelmény alapján létre kell hoznia a következő útvonaltáblát, amely az **azurevnet-ben**tartalmazza az UD-okat.

### <a name="azgwudr"></a>azgwudr
Ebben a forgatókönyvben a helyszíni és az Azure-ba irányuló egyetlen forgalom az **AZF3-hoz**való csatlakozással kezeli a tűzfalakat, és hogy a forgalomnak a belső tűzfalon, az **AZF2-en**keresztül kell mennie. Ezért csak egy útvonalra van szükség a **GatewaySubnet-ben** az alábbiak szerint.

| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Lehetővé teszi a helyszíni forgalom számára az **AZF3** felügyeleti tűzfal elérését |

### <a name="azsn2udr"></a>azsn2udr
| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Lehetővé teszi az alkalmazáskiszolgálót az **AZF2-en** keresztül üzemeltető háttér-alhálózatba irányuló forgalmat |
| 0.0.0.0/0 |10.0.2.10 |Lehetővé teszi az összes többi forgalom átirányításának **az AZF1-en** keresztül |

### <a name="azsn3udr"></a>azsn3udr
| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Lehetővé teszi az **azsn2-be** irányuló forgalom áramlását az alkalmazáskiszolgálóról a webkiszolgálóra az **AZF2-n** keresztül |

Az **onpremvnet** alhálózataihoz is létre kell hoznia útvonaltáblákat a helyszíni adatközpont utánzatához.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Lehetővé teszi a forgalmat **onpremsn2** keresztül **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Lehetővé teszi a forgalmat a háttérben lévő alhálózataz Azure-ban **keresztül OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Lehetővé teszi a forgalmat **onpremsn1** keresztül **OPFW** |

## <a name="ip-forwarding"></a>IP-továbbítás
Az UDR és az IP-továbbítás olyan funkciók, amelyek együttesen lehetővé teszik a virtuális készülékek használatát az Azure virtuális hálózat forgalmának szabályozására.  A virtuális készülék nem más, mint egy virtuális gép, amelyen egy olyan alkalmazás fut, ami a hálózati forgalmat kezeli valamilyen módon, például tűzfallal vagy NAT-eszközzel.

A virtuális készüléknek képesnek kell lennie fogadni a nem neki címzett bejövő forgalmat. Ahhoz, hogy egy virtuális gép számára engedélyezze a más célhelyre irányított forgalom fogadását, először engedélyeznie kell a virtuális gép számára az IP-továbbítást. Ez egy Azure beállítás, nem a vendég operációs rendszer beállítása. A virtuális készülék továbbra is futtatnia kell valamilyen alkalmazást a bejövő forgalom kezeléséhez, és megfelelő en irányítani.

Ha többet szeretne megtudni az IP-továbbításról, látogasson el a [Mik azok a felhasználó által definiált útvonalak és ip-továbbítás.](virtual-networks-udr-overview.md)

Tegyük fel például, hogy a következő beállítással rendelkezik egy Azure-beli virtuális hálózatban:

* Az **alhálózat onpremsn1** egy **onpremvm1**nevű virtuális gépet tartalmaz.
* Az **onpremsn2** alhálózat egy **onpremvm2**nevű virtuális gépet tartalmaz.
* Az **OPFW** nevű virtuális készülék az **onpremsn1** és **az onpremsn2**készülékhez csatlakozik.
* Az **onpremsn1-hez** kapcsolódó, felhasználó által definiált útvonal azt adja meg, hogy **az onpremsn2-re** irányuló összes forgalmat el kell küldeni az **OPFW-nek.**

Ezen a ponton, ha **onpremvm1** megpróbál kapcsolatot **onpremvm2,** az UDR lesz használva, és a forgalom lesz elküldve **OPFW,** mint a következő ugrás. Ne feledje, hogy a tényleges csomag cél nem változik, még mindig azt mondja **onpremvm2** a cél. 

Az **OPFW**IP-továbbítás engedélyezésével az Azure virtuális hálózati logikája eldobja a csomagokat, mivel csak akkor engedélyezi a csomagok virtuális gépnek történő küldését, ha a virtuális gép IP-címe a csomag célja.

Az IP-továbbítás sal az Azure virtuális hálózati logikája továbbítja a csomagokat az OPFW-nek anélkül, hogy módosítaná az eredeti célcímét. **Az OPFW-nek** kezelnie kell a csomagokat, és meg kell határoznia, hogy mi legyen velük.

Ahhoz, hogy a fenti forgatókönyv működjön, engedélyeznie kell az IP-továbbítást az **OPFW,** **AZF1**, **AZF2**és **AZF3** hálózati adaptereken, amelyek et útválasztásra használnak (az összes hálózati adapter, kivéve a felügyeleti alhálózathoz kapcsolódóhálózati adaptereket). 

## <a name="firewall-rules"></a>Tűzfalszabályok
A fent leírtak szerint az IP-továbbítás csak azt biztosítja, hogy a csomagok a virtuális készülékekre kerülnek. A készüléknek még el kell döntenie, hogy mi hezióznia kell ezekkel a csomagokkal. A fenti forgatókönyvben a következő szabályokat kell létrehoznia a készülékekben:

### <a name="opfw"></a>OPFW között
Az OPFW a következő szabályokat tartalmazó helyszíni eszközt jelöli:

* **Útvonal**: A 10.0.0.0/16 **(azurevnet)** összes forgalmat **az ONPREMAZURE**alagúton keresztül kell elküldeni.
* **Házirend**: A **port2** és **az ONPREMAZURE**közötti kétirányú forgalom engedélyezése.

### <a name="azf1"></a>AZF1
Az AZF1 egy Azure virtuális berendezést jelöl, amely a következő szabályokat tartalmazza:

* **Házirend**: Az 1. és a **2.** **port** közötti kétirányú forgalom engedélyezése .

### <a name="azf2"></a>AZF2
Az AZF2 egy Azure virtuális berendezést jelöl, amely a következő szabályokat tartalmazza:

* **Útvonal**: A 10.0.0.0/16 **(onpremvnet)** összes forgalmat az Azure átjáró IP-címére (azaz 10.0.0.1) kell küldeni a **portonkeresztül1**.
* **Házirend**: Az 1. és a **2.** **port** közötti kétirányú forgalom engedélyezése .

## <a name="network-security-groups-nsgs"></a>Hálózati biztonsági csoportok (NSG-k)
Ebben a forgatókönyvben az NSG-k nincsenek használatban. Azonban az egyes alhálózatokra nsg-ket alkalmazhat a bejövő és kimenő forgalom korlátozásához. Például a következő NSG-szabályokat alkalmazhatja a külső FW alhálózatra.

**Bejövő**

* Az összes TCP-forgalom engedélyezése az internetről a 80-as portra az alhálózat bármely virtuális gépén.
* Minden más forgalom megtagadása az internetről.

**Kimenő**

* Az internetminden forgalmának megtagadása.

## <a name="high-level-steps"></a>Magas szintű lépések
A forgatókönyv üzembe helyezéséhez kövesse az alábbi magas szintű lépéseket.

1. Jelentkezzen be Azure-előfizetésbe.
2. Ha a helyszíni hálózatot utánzó virtuális hálózatot szeretne telepíteni, az **ONPREMRG**részét tartalmazó erőforrásokat létesítsen.
3. Az **AZURERG**részét használó erőforrások kiépítése .
4. Az alagút kiépítése **az onpremvnet-ről** az **azurevnet-re.**
5. Az összes erőforrás kiépítése után jelentkezzen be **az onpremvm2-be** és a ping 10.0.3.101-be az **onpremsn2** és **az azsn3**közötti kapcsolat teszteléséhez.

