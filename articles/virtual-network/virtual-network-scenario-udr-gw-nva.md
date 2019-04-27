---
title: Hibrid kapcsolat 2 szintű alkalmazással |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe virtuális készülékek és az udr-t, hozzon létre egy többrétegű alkalmazást környezetet az Azure-ban
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
ms.openlocfilehash: c959ee3bea24955e3281feb9db66e4e0cadc8bf9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034163"
---
# <a name="virtual-appliance-scenario"></a>Virtuális berendezés forgatókönyv
Nagyobb méretű Azure-ügyfelek körében gyakran előfordul, hogy meg kell adni egy kétszintű alkalmazás csatlakozik az internethez, miközben lehetővé teszi a hozzáférést a háttérrendszer szintre helyszíni adatközpontból elérhetővé tett. Ez a dokumentum végigvezeti egy forgatókönyvet, felhasználó által megadott útvonalak (UDR), a VPN-átjáró és a hálózati virtuális berendezések üzembe helyezése egy kétrétegű környezet, amely megfelel a következő használatával:

* Webes alkalmazás csak a nyilvános internetről elérhetőnek kell lennie.
* Az alkalmazást üzemeltető webkiszolgáló eléréséhez egy háttér-alkalmazáskiszolgáló képesnek kell lennie.
* A webalkalmazásnak az internetről származó minden forgalmat a virtuális készüléken keresztül kell lépnie. A virtuális készülék csak internetes forgalomhoz használható.
* Az Alkalmazáskiszolgáló teljes forgalmat a virtuális készüléken keresztül kell lépnie. A virtuális készülék és a háttérkiszolgáló teljes hozzáférés és a egy VPN-átjárót a helyszíni hálózatról érkező hozzáféréshez használható.
* A rendszergazdák a tűzfal virtuális készülékek felügyelete a helyi számítógépükön képesnek kell lennie, a harmadik tűzfal kizárólag felügyeleti célokra használt virtuális berendezés.

Ez a DMZ-t és a egy védett hálózati DMZ-t standard forgatókönyv. Ilyen forgatókönyv lehet létrehozni az Azure-ban az NSG-k, tűzfal virtuális berendezések vagy mindkettőt. Az alábbi táblázat néhány és a hátrányai NSG-k és a tűzfal virtuális berendezések között.

|  | Szakemberek számára | Hátrányok |
| --- | --- | --- |
| NSG |Költségek nélkül. <br/>Az Azure RBAC integrálva. <br/>Az ARM-sablonokat is létrehozhatók. |A nagyobb méretű környezetek eltérőek lehetnek a összetettségét. |
| Tűzfal |Teljes körűen felügyelve az adatsíkhoz. <br/>Központi felügyeleti tűzfal konzolon keresztül. |Tűzfal készülék díja. <br/>Nincs integrálva az Azure RBAC. |

A megoldás az alábbi tűzfal virtuális berendezések hálózati DMZ-t/védett Ez a forgatókönyv megvalósításához használja.

## <a name="considerations"></a>Megfontolandó szempontok
A környezet azt fent kifejtettük, az Azure-ban elérhető különböző funkcióit még ma, az alábbiak szerint telepítheti.

* **Virtuális hálózat (VNet)**. Egy Azure virtuális hálózat hasonlóan zajlik, egy helyszíni hálózat funkcionál, és a egy vagy több alhálózatot forgalom elkülönítése és a kockázatok elkülönítésének osztható.
* **Virtuális berendezés**. Számos partnerünk adja meg a virtuális készülékek az Azure Marketplace-en, amely a fent leírt három tűzfalak is használható. 
* **Felhasználó által megadott útvonalak (UDR)**. Útvonaltáblák udr-EK és a egy virtuális hálózaton belüli csomagok szabályozásához az Azure-hálózatok által használt is tartalmazhat. Ezek útvonaltáblák alhálózatokra alkalmazható. A legújabb funkciókat az Azure-ban egyik lehetővé teszi egy útválasztási táblázatot az átjáró-alhálózat biztonságosabbá teszi az összes forgalmat a az Azure virtuális hálózat a hibrid kapcsolat virtuális készülékre továbbítja a alkalmazni.
* **IP-továbbítás**. Alapértelmezés szerint az Azure hálózati motor továbbítsa a csomagokat a virtuális hálózati adapterek (NIC) csak akkor, ha a csomag cél IP-cím megegyezik a hálózati adapter IP-cím. Ezért ha egy udr-t, hogy egy csomagot kell küldeni egy adott virtuális berendezésre, az Azure hálózati motor adott csomag volna eldobni. Annak érdekében, hogy a csomag egy virtuális Géphez (az ebben az esetben egy virtuális készüléken), amely nem a tényleges cél a csomag számára kézbesíti a rendszer, kell engedélyeznie a virtuális készülék IP-továbbítást.
* **Hálózati biztonsági csoportok (NSG-k)**. Az alábbi példában nem derül NSG-k használatát, de használhat NSG-ket az alhálózatok és/vagy a hálózati adapterek ebben a megoldásban alkalmazott kívüli alhálózatok és a hálózati adapterek a forgalom további szűréséhez.

![IPv6-kapcsolatot](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

Ebben a példában van egy előfizetést, amely a következőket tartalmazza:

* 2 erőforráscsoportok, az ábrán nem látható. 
  * **ONPREMRG**. Egy helyszíni hálózat szimulálásához szükséges összes erőforrást tartalmaz.
  * **AZURERG**. Az Azure virtuális hálózati környezet számára szükséges összes erőforrást tartalmaz. 
* Egy VNet nevű **onpremvnet** egy helyszíni adatközpont az alább felsorolt szegmentált utánzására használja.
  * **onpremsn1**. Egy helyszíni kiszolgálón referenciaszámítógépnek Ubuntu rendszert futtató virtuális gép (VM) tartalmazó alhálózatról.
  * **onpremsn2**. Egy helyszíni számítógépen egy rendszergazda referenciaszámítógépnek Ubuntu rendszerű virtuális gép tartalmazó alhálózatról.
* Van egy tűzfal virtuális készülék nevű **OPFW** a **onpremvnet** vezető alagút karbantarthatja **azurevnet**.
* Egy VNet nevű **azurevnet** szegmentált az alábbiak szerint.
  * **azsn1**. Külső tűzfal alhálózat kizárólag a külső tűzfalat használ. Minden internetes forgalom ezen az alhálózaton haladnak származnak. Ez az alhálózat csak egy hálózati adapter kapcsolódik a külső tűzfalat tartalmaz.
  * **azsn2**. Az előtérbeli alhálózat egy webkiszolgálót, amely az interneten érhető el futtató virtuális gépek üzemeltetéséhez.
  * **azsn3**. Háttérbeli alhálózatot egy háttér-alkalmazáskiszolgáló webböngészőügyfelek által az előtér-webkiszolgálón futó virtuális gépek üzemeltetéséhez.
  * **azsn4**. Kizárólag a tűzfal az összes virtuális berendezések felügyeleti hozzáférés biztosítására használt felügyeleti alhálózaton. Ez az alhálózat csak egy hálózati Adaptert a megoldásban használt egyes tűzfal virtuális berendezés tartalmazza.
  * **Átjáró-alhálózat**. Az Azure hibrid kapcsolat alhálózat Azure virtuális hálózatok és egyéb hálózatok közötti kapcsolatot biztosít ExpressRoute- és VPN-átjáró szükséges. 
* Nincsenek 3 tűzfal virtuális készülékekre történő irányításához az **azurevnet** hálózati. 
  * **AZF1**. A nyilvános interneten egy nyilvános IP-cím erőforrás használatával az Azure-ban elérhetővé tett a külső tűzfalat. Ellenőrizze, hogy a sablon a piactérről, vagy közvetlenül a berendezés gyártójához, a rendelkezések a 3-NIC virtuális berendezés kell.
  * **AZF2**. Belső tűzfal közötti forgalom szabályozására szolgál **azsn2** és **azsn3**. Ez a 3-NIC virtuális berendezés is.
  * **AZF3**. A helyszíni adatközpontból a rendszergazdák számára elérhető, és minden tűzfalkészülékek kezelésére szolgáló felügyeleti alhálózathoz csatlakoztatott felügyeleti tűzfal. 2 hálózati adapterrel rendelkező virtuális készülék sablonok keresése a piactéren, vagy közvetlenül a készülék gyártójától kérhet.

## <a name="user-defined-routing-udr"></a>Felhasználó által definiált útválasztás (UDR)
Az Azure-ban minden alhálózat egy UDR táblázat segítségével határozhatók meg, hogyan kezdeményezett forgalmat, annak, hogy az alhálózat továbbítja a rendszer lehet kapcsolódni. Ha nincsenek udr-EK van definiálva, az Azure alapértelmezett útvonalak használatával forgalmat engedélyezi az egyik alhálózatból egy másikba. Jobb megértése érdekében udr-EK, látogasson el a [Mik a felhasználó által megadott útvonalak és IP-továbbítás](virtual-networks-udr-overview.md).

Ellenőrizze a kommunikációt a megfelelő tűzfal-készüléken, a fenti, utolsó követelmény alapján történik, a következő útvonaltáblát, mely az udr-EK létrehozása kell **azurevnet**.

### <a name="azgwudr"></a>azgwudr
Ebben a forgatókönyvben a csak az adatforgalom a helyszínről az Azure-ba való csatlakozással a tűzfalak kezeléséhez használható **AZF3**, és, hogy forgalmat a belső tűzfalon keresztül haladjon **AZF2**. Ezért csak egy útvonal erre szükség van a **GatewaySubnet** alább látható módon.

| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Lehetővé teszi, hogy a helyszíni forgalom elérhesse a felügyeleti tűzfal **AZF3** |

### <a name="azsn2udr"></a>azsn2udr
| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Engedélyezi a forgalmat a háttérbeli alhálózathoz, az alkalmazáskiszolgáló keresztül üzemeltető **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Lehetővé teszi az összes többi forgalom keresztül legyen irányítva **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Engedélyezi a forgalmat a **azsn2** a webkiszolgáló keresztül történő alkalmazás-kiszolgálóról flow **AZF2** |

Is szeretne létrehozni az útválasztási táblázatban az alhálózatok **onpremvnet** referenciaszámítógépnek a helyszíni adatközponthoz.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Engedélyezi a forgalmat a **onpremsn2** keresztül **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Cél | Következő ugrás | Magyarázat |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Az Azure-ban lehetővé teszi, hogy a biztonsági alhálózat forgalmát **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Engedélyezi a forgalmat a **onpremsn1** keresztül **OPFW** |

## <a name="ip-forwarding"></a>IP-továbbítás
Udr-t és az IP-továbbítást is használható funkciókat kombinálva, hogy a virtuális berendezésekkel irányíthatja a forgalmat egy Azure virtuális hálózaton használható.  A virtuális készülék nem más, mint egy virtuális gép, amelyen egy olyan alkalmazás fut, ami a hálózati forgalmat kezeli valamilyen módon, például tűzfallal vagy NAT-eszközzel.

A virtuális készüléknek képesnek kell lennie fogadni a nem neki címzett bejövő forgalmat. Ahhoz, hogy egy virtuális gép számára engedélyezze a más célhelyre irányított forgalom fogadását, először engedélyeznie kell a virtuális gép számára az IP-továbbítást. Ez egy Azure beállítás, nem a vendég operációs rendszer beállítása. A virtuális berendezés továbbra is kell futtatni, valamilyen típusú alkalmazás a bejövő forgalom kezelésére, és megfelelően irányítja.

Az IP-továbbítás kapcsolatos további információkért látogasson el [Mik a felhasználó által megadott útvonalak és IP-továbbítás](virtual-networks-udr-overview.md).

Például tegyük fel, a következő beállítás van egy Azure virtuális hálózat:

* Alhálózat **onpremsn1** nevű virtuális Gépet tartalmaz **onpremvm1**.
* Alhálózat **onpremsn2** nevű virtuális Gépet tartalmaz **onpremvm2**.
* Virtuális berendezés nevű **OPFW** csatlakozik **onpremsn1** és **onpremsn2**.
* Egy felhasználó által megadott útvonalat kapcsolódó **onpremsn1** Megadja, hogy minden forgalomnak a **onpremsn2** kell küldeni az **OPFW**.

At a pont, ha **onpremvm1** létre kapcsolatot próbál **onpremvm2**, az udr-t fogja használni, és a forgalmat küld **OPFW** következő ugrásként. Ne feledje, hogy a tényleges csomagban cél nem módosul, továbbra is ugyanakkor **onpremvm2** célhelye. 

IP-továbbítás engedélyezve nélkül **OPFW**, az Azure virtuális hálózati logika csökken, a csomagok, mivel csak lehetővé teszi a csomagok küldését egy virtuális géphez, ha a virtuális gép IP-cím a csomag számára.

Az IP-továbbítást az Azure virtuális hálózat logika továbbítja a csomagokat OPFW, eredeti cél-címének módosítása nélkül. **OPFW** kell kezelni a csomagokat, és határozza meg, mi a teendő velük.

A fenti forgatókönyv működjön, engedélyeznie kell a hálózati adaptert az IP-továbbítás **OPFW**, **AZF1**, **AZF2**, és **AZF3** szolgáló útválasztás (csak az általunk kapcsolódnak a felügyeleti alhálózaton minden NIC). 

## <a name="firewall-rules"></a>Tűzfalszabályok
A fentiekben ismertetettek szerint biztosítja az IP-továbbítás csak a virtuális készülékek küldött csomagokat. A berendezés továbbra is kell, hogy mit szeretne elvégezni ezeket a csomagokat. A fenti esetben szüksége lesz a következő szabályok létrehozása a berendezések:

### <a name="opfw"></a>OPFW
OPFW egy helyszíni eszköz a következő szabályokat tartalmazó jelöli:

* **útvonal**: A 10.0.0.0/16 minden forgalmat (**azurevnet**) alagúton keresztül kell küldeni **ONPREMAZURE**.
* **A házirend**: Az összes közötti kétirányú forgalom engedélyezéséhez **port2** és **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
AZF1 jelöli egy Azure virtuális berendezés, amely tartalmazza a következő szabályok:

* **A házirend**: Az összes közötti kétirányú forgalom engedélyezéséhez **port1** és **port2**.

### <a name="azf2"></a>AZF2
AZF2 jelöli egy Azure virtuális berendezés, amely tartalmazza a következő szabályok:

* **útvonal**: A 10.0.0.0/16 minden forgalmat (**onpremvnet**) el kell küldeni az Azure-átjáró IP-címet (például 10.0.0.1) keresztül **port1**.
* **A házirend**: Az összes közötti kétirányú forgalom engedélyezéséhez **port1** és **port2**.

## <a name="network-security-groups-nsgs"></a>Hálózati biztonsági csoportok (NSG-k)
Ebben a forgatókönyvben az NSG-k nem használják. Azonban alkalmazhat az NSG-ket az egyes alhálózatok bejövő és kimenő forgalom korlátozására. Például az alábbi NSG-szabályok sikerült alkalmazni a külső Keretrendszer-alhálózatot.

**bejövő**

* Engedélyezi az internetről érkező összes TCP-forgalom az alhálózaton lévő összes virtuális gép 80-as portjához.
* Az összes többi forgalom tiltása az internetről.

**Kimenő**

* Minden forgalom tiltása az internethez.

## <a name="high-level-steps"></a>Magas szintű lépései
Ez a forgatókönyv üzembe helyezéséhez kövesse az alábbi magas szintű lépéseket.

1. Jelentkezzen be az Azure-előfizetéshez.
2. Ha azt szeretné, a virtuális hálózat üzembe helyezése referenciaszámítógépnek a helyszíni hálózat részét képező-erőforrások kiépítése **ONPREMRG**.
3. Részét képező-erőforrások kiépítése **AZURERG**.
4. Az alagút a kiépítése **onpremvnet** való **azurevnet**.
5. Minden erőforrás kiosztása után jelentkezzen be **onpremvm2** és közötti kapcsolat teszteléséhez 10.0.3.101 pingelni **onpremsn2** és **azsn3**.

