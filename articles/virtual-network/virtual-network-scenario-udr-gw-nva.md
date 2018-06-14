---
title: A hibrid kapcsolat 2 szintű alkalmazással |} Microsoft Docs
description: Megtudhatja, hogyan telepítheti a virtuális készülékek és UDR környezetet hozhat létre, egy többrétegű alkalmazást az Azure-ban
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: jdial
ms.openlocfilehash: 544ba6484b23da425d53594622122b1e18b92359
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
ms.locfileid: "23943223"
---
# <a name="virtual-appliance-scenario"></a>Virtuális készülék forgatókönyv
Egy általános forgatókönyv nagyobb Azure felhasználói között szükség egy kétszintű alkalmazás kommunikál az internettel, miközben egy helyszíni adatközpontját a hátsó réteg hozzáférést biztosítanak. Ez a dokumentum végigvezeti felhasználó definiált útvonalakat (UDR), a VPN-átjáró és a virtuális hálózati berendezések használatát központi telepítése egy kétrétegű környezet, amely megfelel az alábbi követelményeknek:

* Webes alkalmazás csak a nyilvános internetről elérhetőnek kell lennie.
* Az alkalmazás-webkiszolgálón kell tudni hozzáférni egy háttér-alkalmazáskiszolgáló.
* A webalkalmazás az internetről származó összes forgalmat virtuális készülékként egy tűzfalat kell végrehajtania. A virtuális készüléknek csak az internetes forgalmat fog történni.
* Az Alkalmazáskiszolgáló teljes forgalmat a virtuális készülékként egy tűzfalat kell végrehajtania. A virtuális készüléknek, és a hozzáférés a helyszíni hálózathoz egy VPN-átjárón keresztül várható a háttér-kiszolgálófiók hozzáférés fog történni.
* Lehet, hogy a rendszergazdák kezelhetik a tűzfal virtuális készülékek a helyi számítógépükön, a harmadik tűzfal kizárólag felügyeleti szempontból használt virtuális készülék.

Ez egy webfarmos szabványos DMZ DMZ és a védett hálózati. Ilyen eset lehet létrehozni az Azure-ban a NSG-ket, virtuális tűzfalkészülékek, vagy mindkettőt. Néhány és NSG-k és a tűzfalon virtuális készülékek között az alábbi táblázat mutatja be.

|  | Informatikai szakemberek | Hátrányok |
| --- | --- | --- |
| NSG |Költség nélkül. <br/>Az Azure RBAC integrálva. <br/>Szabályok ARM-sablonokkal hozhatók létre. |Nagyobb környezetekben eltérőek lehetnek a összetettségét. |
| Tűzfal |Adatok vezérlősík teljes ellenőrzést. <br/>Központi felügyeleti tűzfal konzolon keresztül. |Tűzfal készülék költségét. <br/>Nincs integrálva az Azure RBAC. |

A megoldás az alábbi tűzfal virtuális készülékek használja egy Szegélyhálózaton/védett hálózati forgatókönyv megvalósításához.

## <a name="considerations"></a>Megfontolandó szempontok
A környezet az Azure-ban elérhető különböző szolgáltatások ma, az alábbiak szerint fenti telepítése.

* **Virtuális hálózathoz (VNet)**. Egy Azure virtuális hálózatot a helyszíni hálózathoz hasonló módon működik, és az adatforgalom-elkülönítést és aggályokat elkülönítése egy vagy több alhálózatból is lehet szegmentált.
* **Virtuális készülék**. Több partnerek adja meg a virtuális készülékek az Azure piactéren, amely a fent leírt három tűzfalak nem használható. 
* **Felhasználó által megadott útvonalak (UDR)**. Az útvonaltáblák udr-EK egy Vneten belül csomagok üzenetáramlásának szabályozására Azure hálózat által használt tartalmazhat. Ezek az útvonaltáblák alhálózatok alkalmazhatja. A legújabb funkciókat az Azure-ban egyike egy útválasztási táblázatot alkalmazása a GatewaySubnet, ezáltal képesek egy hibrid kapcsolat hamarosan be az Azure virtuális hálózat virtuális készülékre összes forgalom továbbítására képes.
* **IP-továbbítás**. Alapértelmezés szerint az Azure hálózati motor továbbítsa a csomagokat a virtuális hálózati adapterek (NIC) csak akkor, ha a csomag cél IP-címe megegyezik a hálózati adapter IP-címet. Ezért egy UDR határozza meg, hogy egy csomagot kell küldeni egy adott virtuális készüléket, az Azure hálózati motor csomagban csökkenne. Győződjön meg arról, a csomagot a rendszer a virtuális gépek (ebben az esetben a virtuális készülék), amely nem a csomag tényleges célját, IP-továbbítást a virtuális készülék engedélyezni kell.
* **Hálózati biztonsági csoportokkal (NSG-k)**. Az alábbi példa tegye az NSG-k használatát, de az alhálózatok és/vagy a hálózati adapterek ebben a megoldásban alkalmazott NSG-k segítségével mindkét ezek alhálózatok és a hálózati adapterek forgalom további szűréséhez.

![IPv6-kapcsolatot](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

Ebben a példában nincs olyan előfizetést, amelyet a következőket tartalmazza:

* 2 erőforráscsoportok, az ábrán nem látható. 
  * **ONPREMRG**. Az összes szükséges egy helyszíni hálózat szimulálása erőforrásokat tartalmaz.
  * **AZURERG**. Tartalmazza az összes erőforrást az Azure virtuális hálózati környezet szükséges. 
* Egy VNet nevű **onpremvnet** segítségével egy helyszíni adatközpontot az alább felsorolt szegmentált utánozzák.
  * **onpremsn1**. Ubuntu, hogy egy helyszíni kiszolgáló utánozzák futtató virtuális gép (VM) tartalmazó alhálózat.
  * **onpremsn2**. A virtuális gép fut, hogy egy a helyszíni számítógépek, a rendszergazda által használt utánozzák Ubuntu tartalmazó alhálózat.
* Van egy tűzfal virtuális készülék nevű **OPFW** a **onpremvnet** használandó megőrzése alagutat **azurevnet**.
* Egy VNet nevű **azurevnet** szegmentált az alábbiak szerint.
  * **azsn1**. Külső tűzfal alhálózati kizárólag a külső tűzfal használja. Minden internetes forgalomhoz határozza meg az alhálózat keresztül. Ez az alhálózat csak egy külső tűzfaltól kapcsolódó hálózati Adaptert tartalmaz.
  * **azsn2**. Előtérben lévő alhálózat alhálózat üzemeltető virtuális gép fut, és a webkiszolgáló kell elérnie az interneten.
  * **azsn3**. Backend alhálózathoz üzemeltető virtuális gép fut, egy háttér-alkalmazáskiszolgáló az előtér-webkiszolgálón kell elérnie.
  * **azsn4**. Felügyeleti alhálózati használt kizárólag az összes virtuális tűzfalkészülékek felügyeleti hozzáférést biztosítanak. Ez az alhálózat csak egy hálózati Adapterhez a megoldásban használt egyes tűzfal virtuális készülék tartalmazza.
  * **A GatewaySubnet**. Azure hibrid kapcsolat alhálózati Azure Vnet és egyéb hálózatok közötti kapcsolatot biztosít az ExpressRoute- és VPN-átjáró szükséges. 
* Nincsenek 3 tűzfal virtuális készülékek a **azurevnet** hálózati. 
  * **AZF1**. A nyilvános internethez egy nyilvános IP-cím erőforrás használatával az Azure-ban elérhetővé tett külső tűzfaltól. Ellenőrizze, hogy a sablon a piactérről, vagy közvetlenül a készülék forgalmazójával, hogy a rendelkezések a 3-NIC virtuális készülék kell.
  * **AZF2**. Közötti forgalom vezérlő belső tűzfal **azsn2** és **azsn3**. Ez egyben a 3-NIC virtuális készülék.
  * **AZF3**. A helyszíni adatközpontját a rendszergazdák számára, és minden tűzfalkészülékek kezelésére szolgáló felügyeleti alhálózathoz csatlakoztatott felügyeleti tűzfal. Virtuális készülék sablonok 2-NIC keresése a piactéren, vagy a készülék szállítótól közvetlenül kérhet.

## <a name="user-defined-routing-udr"></a>Felhasználó által definiált útválasztási (UDR)
Az Azure-ban minden alhálózat egy UDR táblázat segítségével határozza meg, hogyan forgalom kezdeményezett abban, hogy az alhálózati történik lehet társítani. Ha nincs udr-EK vannak meghatározva, a Azure alapértelmezett útvonalakat használ a forgalmat engedélyezi egy alhálózatról a másikra. Jobb megértése érdekében udr-EK, látogasson el a [Mik azok a felhasználó által megadott útvonalak és IP-továbbítás](virtual-networks-udr-overview.md).

A következő útválasztási táblázatot tartalmazó udr-EK a létrehozásához szükséges kommunikációs a megfelelő tűzfal, rendszerre épülő készüléknek meg a fenti utolsó követelmény segítségével történik biztosításához **azurevnet**.

### <a name="azgwudr"></a>azgwudr
Ebben a forgatókönyvben az Azure-bA helyszíni áramlanak csak felé irányuló forgalmat fog felügyeletére lehet használni a tűzfalak csatlakozva **AZF3**, és, hogy a forgalmat a belső tűzfalon keresztül kell lépnie **AZF2**. Ezért csak egy útvonal erre szükség van a **GatewaySubnet** alább látható módon.

| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Lehetővé teszi a helyszíni forgalom felügyeleti tűzfal eléréséhez **AZF3** |

### <a name="azsn2udr"></a>azsn2udr
| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Lehetővé teszi a backend alhálózathoz az alkalmazáskiszolgáló keresztül üzemeltető forgalom **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Lehetővé teszi az összes többi forgalom keresztül irányíthatja át **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Lehetővé teszi, hogy a forgalom **azsn2** a webkiszolgálón keresztül történő alkalmazás-kiszolgálóról folyamokhoz **AZF2** |

Is szeretné létrehozni az útvonaltáblák lévő alhálózatok **onpremvnet** hogy a helyszíni adatközpontját utánozzák.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Lehetővé teszi, hogy a forgalom **onpremsn2** keresztül **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |A biztonsági mentés alhálózat-forgalmát engedélyezi az Azure-keresztül **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Lehetővé teszi, hogy a forgalom **onpremsn1** keresztül **OPFW** |

## <a name="ip-forwarding"></a>IP-továbbítás
UDR és IP-továbbítást is használó szolgáltatások kombinálva egy Azure virtuális hálózatot az adatforgalom szabályozásához használandó virtuális készülékek engedélyezéséhez.  A virtuális készülék nem más, mint egy virtuális gép, amelyen egy olyan alkalmazás fut, ami a hálózati forgalmat kezeli valamilyen módon, például tűzfallal vagy NAT-eszközzel.

A virtuális készüléknek képesnek kell lennie fogadni a nem neki címzett bejövő forgalmat. Ahhoz, hogy egy virtuális gép számára engedélyezze a más célhelyre irányított forgalom fogadását, először engedélyeznie kell a virtuális gép számára az IP-továbbítást. Ez egy Azure beállítás, nem a vendég operációs rendszer beállítása. A virtuális készülékre továbbra is kell futtatni a valamilyen alkalmazáshoz, és megfelelően irányítja a bejövő forgalom kezelésére.

Az IP-továbbítás kapcsolatos további információkért látogasson el a [Mik azok a felhasználó által megadott útvonalak és IP-továbbítás](virtual-networks-udr-overview.md).

Tegyük fel képzelhető el, a következő telepítési rendelkezik egy Azure virtuális hálózatot:

* Alhálózati **onpremsn1** tartalmaz egy nevű virtuális gép **onpremvm1**.
* Alhálózati **onpremsn2** tartalmaz egy nevű virtuális gép **onpremvm2**.
* A virtuális készülék nevű **OPFW** csatlakozik-e **onpremsn1** és **onpremsn2**.
* A felhasználó által megadott útvonalat kapcsolódó **onpremsn1** határozza meg, hogy minden forgalomnak a **onpremsn2** kell küldeni a **OPFW**.

At a pont, ha **onpremvm1** kapcsolatot próbál **onpremvm2**, a UDR fogja használni, és a forgalom kapnak **OPFW** következő ugrásként. Ne feledje, hogy a tényleges csomagban cél nem módosul, továbbra is felirat látható **onpremvm2** a cél. 

Az IP-továbbítás engedélyezve nélkül **OPFW**, az Azure virtuális hálózat logikai eldobja a csomagokat, mivel csak lehetővé teszi a csomagok küldését a virtuális géphez, ha a virtuális gép IP-cím a cél a csomag számára.

Az IP-továbbítást az Azure-beli virtuális hálózat logikai továbbítja a csomagokat OPFW, eredeti cél címének módosítása nélkül. **OPFW** kell kezelni a csomagokat, és határozza meg, mi a teendő velük.

A fenti forgatókönyv működjön, engedélyeznie kell az IP-továbbítás a hálózati adaptert a **OPFW**, **AZF1**, **AZF2**, és **AZF3** irányításához használt (hálózati adapterek összes felügyeleti alhálózathoz megfelelően kivételével). 

## <a name="firewall-rules"></a>Tűzfalszabályok
A fent leírtaknak megfelelően biztosítja az IP-továbbítás csak a virtuális készülékek küldött csomagok. A készülék továbbra is hozzá kell döntenie, hogy mi a teendő, ha ezeket a csomagokat. A fenti esetben szüksége lesz a készülékek a következő szabályok létrehozására:

### <a name="opfw"></a>OPFW
OPFW jelöli egy a helyszíni eszközöket, amelyek a következő szabályokat:

* **Útvonal**: 10.0.0.0/16 minden forgalmat (**azurevnet**) alagúton keresztül kell küldeni **ONPREMAZURE**.
* **Házirend**: közötti összes kétirányú forgalmat engedélyezi **port2** és **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
AZF1 egy Azure virtuális készüléknek, a következő szabályokat tartalmazó jelöli:

* **Házirend**: közötti összes kétirányú forgalmat engedélyezi **port1** és **port2**.

### <a name="azf2"></a>AZF2
AZF2 egy Azure virtuális készüléknek, a következő szabályokat tartalmazó jelöli:

* **Útvonal**: 10.0.0.0/16 minden forgalmat (**onpremvnet**) el kell küldeni az Azure-átjáró IP-cím (például 10.0.0.1) keresztül **port1**.
* **Házirend**: közötti összes kétirányú forgalmat engedélyezi **port1** és **port2**.

## <a name="network-security-groups-nsgs"></a>Hálózati biztonsági csoportokkal (NSG-k)
Ebben a forgatókönyvben az NSG-k nincsenek használatban. Azonban alkalmazhat az NSG-k minden egyes alhálózatnak korlátozzák a bejövő és kimenő forgalmat. Például a következő NSG-szabályok tudta alkalmazni a külső FW alhálózathoz.

**Bejövő**

* Engedélyezi az internetről érkező összes TCP-forgalom 80-as port az alhálózat minden virtuális gépen.
* Megtagadása az összes többi forgalom az internetről.

**Kimenő**

* Megtagadása az összes forgalom az internethez.

## <a name="high-level-steps"></a>Magas szintű lépései
Ebben a forgatókönyvben telepítéséhez kövesse az alábbi magas szintű lépéseket.

1. Bejelentkezés az Azure-előfizetéshez.
2. Ha szeretne telepíteni egy VNet és a helyszíni hálózat utánoznia, kiépíteni az erőforrásokat, amelyek részei **ONPREMRG**.
3. Kiépíteni az erőforrásokat, amelyek részei **AZURERG**.
4. Az alagutat a kiépítése **onpremvnet** való **azurevnet**.
5. Amennyiben az összes erőforrás törlődnek, jelentkezzen be **onpremvm2** és pingelje közötti kapcsolat tesztelése 10.0.3.101 **onpremsn2** és **azsn3**.

