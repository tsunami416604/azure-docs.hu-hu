---
title: Infrastruktúra és az SAP HANA Azure (nagy példányok) kapcsolat |} Microsoft Docs
description: Az Azure (nagy példányok) SAP HANA használandó kapcsolat szükséges infrastruktúra konfigurálása.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 43debeb710e5ab5112f9f0a85a76761cde3051a7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>SAP HANA (nagy példányok) infrastruktúra és az Azure-kapcsolat 

Néhány definíciók előzetes megfizetése esetén ez az útmutató olvasása előtt. A [SAP HANA (nagy példányok) – áttekintés és Azure architektúra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) HANA nagy példány egység, amelyek két különböző osztályú bevezetett azt:

- S72, S72m, S144, S144m, S192 és S192m, amely a "Type i. osztály" lesz az SKU.
- S384, S384m, S384xm, S576, S768 és S960, amely a "típusú class" SKU lesz az.

Az osztály kulcsszavak használatával a HANA nagy példány dokumentációban végül tekintse meg a különböző képességeket és a követelmények alapján HANA nagy példány termékváltozatok fog.

Más definíciók gyakran használjuk a következők:
- **Nagy példány stamp:** hardver infrastruktúra verem egy SAP HANA TDI van hitelesített, és az Azure SAP HANA-példányok futtatásához dedikált.
- **SAP HANA Azure (nagy példány):** az SAP HANA TDI hardver, amely telepítve van a különböző Azure-régiók nagy példány bélyegzők hitelesített példánya HANA futtassa az Azure-ajánlatot hivatalos nevét. A kapcsolódó kifejezés **HANA nagy példány** rövid a SAP HANA Azure (nagy példányokat), és széles körben használt műszaki telepítési útmutatóban.
 

SAP HANA (nagy példányok) Azure megvásárlása le van zárva, és a vállalati fiók csoportja között, miután a következő információkat a Microsoft által telepítéséhez szükséges HANA nagy példány egységek:

- Ügyfél neve
- Céges kapcsolattartási adatait (beleértve az e-mail címét és telefonszámát)
- Műszaki kapcsolattartási adatait (beleértve az e-mail címét és telefonszámát száma)
- Műszaki kapcsolattartási adatait (beleértve az e-mail címét és telefonszámát száma)
- Az Azure-telepítés régióban (USA nyugati régiója, USA keleti régiója, Kelet-Ausztrália, Ausztrália délkeleti, Nyugat-Európában, frissítésétől július 2017 Észak-Európa)
- Erősítse meg az Azure (nagy példányok) Termékváltozat (konfiguráció) SAP HANA
- A már részletes a áttekintése és architektúra dokumentum HANA nagy példányok, minden Azure-régió telepített:
    - Egy /29 IP-címtartomány ER-P2P kapcsolatok HANA nagy példányok Azure Vnet-hez
    - Egy/24 a HANA nagy példányok kiszolgáló IP-készlet használt CIDR-blokkja
- Az IP cím tartományértékeknek minden Azure vnet, amely kapcsolódik a HANA nagy példányok VNet címterület attribútumában
- Az egyes HANA nagy példányok rendszer adatokat:
  - Kívánt gazdagépnévvel - ideális esetben a teljesen minősített tartománynevét.
  - A kiszolgáló IP-készlet címtartománya - kívül HANA nagy példány egységéhez kívánt IP-címet vegye figyelembe, hogy a kiszolgáló IP-készlet címtartománya első 30 IP-címek fenntartott HANA nagy példányok belüli belső használatra
  - SAP HANA SID (a szükséges SAP HANA-kapcsolódó kötetek létrehozásához szükséges) SAP HANA-példány nevét. A HANA SID sidadm engedélyeit a HANA nagy példány egysége csatolt első NFS köteteknél létrehozásához szükség. Azt is szolgál az beszerzése a csatlakoztatott kötetek neve összetevői. Ha azt szeretné, egynél több HANA példány egységben futtatásához, szüksége több HANA SID listában. Mindegyik lekérdezi a hozzárendelt kötetek külön készletét.
  - A csoportazonosító a sidadm felhasználó rendelkezik-e a Linux operációs rendszer a szükséges SAP HANA-kapcsolódó kötetek létrehozásához szükséges. A SAP HANA-telepítés egy csoport azonosítójú 1001, általában a sapsys csoportot hoz létre. A sidadm felhasználói csoport tagja
  - A felhasználói azonosítóját a sidadm felhasználó rendelkezik-e a Linux operációs rendszer a szükséges SAP HANA-kapcsolódó kötetek létrehozásához szükséges. Ha több HANA példány egységben futtat, akkor listázza az összes a <sid>adm-felhasználók 
- Az Azure-előfizetés számára melyik SAP HANA Azure HANA a nagy példányok tervezi, hogy közvetlenül az Azure-előfizetése Azonosítóját. Az előfizetés-azonosító az Azure-előfizetésre, és a HANA nagy példány egység(ek) engedélyezéséhez elemleírásként hivatkozik.

Miután megadta az adatokat, a Microsoft Azure (nagy példányok) SAP HANA kiépítését, és ad meg az információkat az Azure Vnetekhez csatolása HANA nagy példányok és a HANA nagy példány egységek eléréséhez szükséges.

## <a name="connecting-azure-vms-to-hana-large-instances"></a>Csatlakozás Azure virtuális gépek nagy HANA-példányok

Említettek szerint a [SAP HANA (nagy példányok) – áttekintés és Azure architektúra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) Azure keres SAP alkalmazás réteggel HANA nagy példányok minimális telepítését, például:

![Az Azure virtuális hálózat csatlakozik az Azure (nagy példányok) és a helyszíni SAP HANA](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Keresése szorosabb Azure VNet oldalán, azt megvalósításához van szükség:

- Egy Azure virtuális hálózatot, amelyet szeretne az SAP alkalmazási réteg a virtuális gépek telepítéséhez használandó definíciója.
- Hogy automatikusan azt jelenti, hogy az Azure vnet alapértelmezett alhálózat meghatározott, valójában a virtuális gépek történő központi telepítéséhez használt.
- A létrehozott Azure virtuális hálózatot kell legalább egy Virtuálisgép-alhálózatot és egy ExpressRoute-átjáró-alhálózatot. Ezek alhálózatok hozzá kell rendelni az IP-címtartományok, megadott és tárgyaltuk, a következő szakaszokban.

Igen most megismerhetők kicsit szorosabb HANA nagy példányok Azure virtuális hálózat létrehozása

### <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Az Azure virtuális hálózat létrehozása nagy HANA-példányok

>[!Note]
>Az Azure VNet HANA nagy példány az Azure Resource Manager telepítési modell segítségével kell létrehozni. A régebbi Azure telepítési modell, a klasszikus üzembe helyezési modellel, gyakran nevezik HANA nagy példány megoldás nem támogatott.

A virtuális hálózat az Azure-portálon, a PowerShell, a Azure-sablon alapján vagy az Azure CLI segítségével hozhatók létre (lásd: [hozzon létre egy virtuális hálózatot az Azure portál használatával](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). A következő példában azt megismerhetők a VNet létrehozása az Azure portálon keresztül.

Ha megnézzük be egy Azure virtuális hálózatot az Azure portálon keresztül meghatározását, nézzük egyes definíciókat, és hogyan azok kapcsolódnak-e azt a listából a különböző IP-címtartományok. Azt is van szó, a **Címterület**, azt jelenti, hogy a címtér, hogy az Azure virtuális hálózatot használja. Ez a címtartomány egyben a tartománya, a virtuális hálózatot használ a BGP útválasztási propagálása. Ez **Címterület** itt találja:

![Cím terület az Azure virtuális hálózatot az Azure-portálon jelenik meg](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

Abban az esetben a 10.16.0.0/16, megelőző az Azure VNet egy ahelyett, hogy nagy és széles IP-címtartomány használatára lett megadva. Azt jelenti, hogy további alhálózatokat a Vneten belül az összes IP-címtartományok lehet a tartományokat a "címtartomány" belül. Általában nem javasoljuk ilyen egy nagy-címtartományt az egyetlen virtuális hálózat az Azure-ban. De a lépés további kap, az Azure VNet meghatározott alhálózatokra vizsgáljuk meg:

![Az Azure VNet-alhálózatok és az IP-címtartományok](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Ahogy látja, úgy tekintünk, a virtuális hálózat (Itt "alapértelmezett" is nevezik) első Virtuálisgép-alhálózatot és a "GatewaySubnet" nevű alhálózat.
A következő szakaszban azt tekintse meg az IP-címtartományt az alhálózat, amely a grafikus, mint a "default" hívták **Azure virtuális alhálózat IP-címtartomány**. A következő szakaszokban azt tekintse meg az IP-címtartományt, mint az átjáró alhálózatának **hálózatok átjáró alhálózati IP-címtartomány**. 

A fenti két grafikus által bemutatott esetben megjelenik a **VNet Címterület** foglalja, a **Azure virtuális alhálózat IP-címtartomány** és a **hálózatok átjáró alhálózati IP-címtartomány**. 

Más esetekben, ahol kell takarítson meg és nem kell az IP-címtartományok az adott korlátozhatja a **VNet Címterület** egy vnet az adott tartomány minden egyes alhálózatot használja. Ebben az esetben megadhat a **VNet Címterület** egy vnet több adott címtartományok itt látható módon:

![Az Azure VNet-címtér és két szóköz](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

Ebben az esetben a **VNet Címterület** definiált két szóközt tartalmaz. E két szóközt tartalmaz, megegyeznek a megadott IP-címtartományok **Azure virtuális alhálózat IP-címtartomány** és a **hálózatok átjáró alhálózati IP-címtartományt.**

A bérlői alhálózat (Virtuálisgép-alhálózatok) bármilyen tetszés elnevezési szabványnak is használhatja. Azonban **mindig kell egy és csak egy átjáró alhálózatának minden vnet** , amely csatlakozik a SAP HANA (nagy példányok) Azure ExpressRoute körön. És **az átjáró-alhálózat neve kötelezően "GatewaySubnet"** az ExpressRoute-átjáró megfelelő elhelyezési biztosításához.

> [!WARNING] 
> Alapvető fontosságú, hogy az átjáró alhálózatának mindig neve "GatewaySubnet".

Több Virtuálisgép-alhálózatok is használható, még a nem összefüggő címtartományai használatával. Ahogy korábban említettük, a cím tartományok át kell gondolni által, de a **VNet Címterület** a vnet összesített formában, vagy a Virtuálisgép-alhálózatokat és az átjáró-alhálózat pontos tartományok listájának.

Egy Azure virtuális hálózatot, amely kapcsolódik a HANA nagy példányok kapcsolatos fontos tény összefoglalása:

- A Microsoftnak elküldeni kívánt a **VNet Címterület** HANA nagy példányok egy kezdeti telepítés végrehajtása során. 
- A **VNet Címterület** lehet egy nagyobb tartományt, amely lefedi az Azure virtuális gép alhálózat IP-cím tranzakciónaplók és a virtuális hálózat átjáró alhálózati IP-címtartomány a tartományon.
- Vagy elküldheti a **VNet Címterület** több tartományt fedik le a különböző IP-címtartományok VM alhálózat IP-cím tranzakciónaplók és a virtuális hálózat átjáró alhálózati IP-címtartományt.
- A definiált **VNet Címterület** használt BGP útválasztási propagálás van.
- Az átjáró alhálózatának nevét kell lennie: **"GatewaySubnet".**
- A **VNet Címterület** engedélyezheti vagy letilthatja a forgalmat a HANA nagy példány egységnél az Azure-ból a HANA nagy példány oldalon szűrőjeként szolgál. Ha a BGP útválasztási információ az Azure virtuális hálózat és az IP-címtartományok HANA nagy példány oldalán szűrésének beállítása nem egyezik, a kapcsolat problémák merülhetnek fel.
- Az átjáró alhálózatának bizonyos adatai, amelyek lejjebb "Csatlakozás a virtuális hálózat HANA nagy példány ExpressRoute" szakaszban tárgyalt van



### <a name="different-ip-address-ranges-to-be-defined"></a>Különböző IP-címtartományok kell megadni 

A Microsoft már vezette be néhány, az IP-címtartományok HANA nagy példányok korábbi szakaszokban központi telepítéséhez szükséges. Azonban néhány további IP-címtartományokat, amelyek fontosak. Ugorjunk keresztül néhány további részleteket. A következő IP-címek, amelyek nem mindegyik kell elküldeni a Microsoftnak kell meghatározni, a kezdeti telepítés kérelem elküldése előtt:

- **Virtuális hálózat címtartomány:** , korábban már bevezetett, vagy az IP-cím range(s) rendelt (vagy a csomag hozzárendelése) számára az Azure virtuális hálózatok felé (VNet) környezethez való csatlakozását a SAP HANA nagy példány a címe terület paramétere. Javasoljuk, hogy a címtartomány paraméter értéke egy többsoros magában foglalja az Azure Virtuálisgép-alhálózat tranzakciónaplók és az Azure-átjáró-alhálózat tartományának a grafikus korábban ismertetett módon. Ezt a tartományt a helyi vagy a kiszolgáló IP-készletet vagy ER-P2P címtartományai nem lehetnek átfedők. Hogyan kérhet ez vagy ezek IP-cím tranzakciónaplók? A vállalati hálózat team vagy szolgáltatás szolgáltató egy vagy több IP cím tranzakciónaplók, vagy nem használják a hálózaton belüli kell biztosítania. Példa: Ha az Azure Virtuálisgép-alhálózatot (lásd fentebb) 10.0.1.0/24, és az Azure-átjáró alhálózatának (lásd alább) 10.0.2.0/28, majd az Azure VNet címteret javasoljuk, hogy lehet két sort; 10.0.1.0/24 és 10.0.2.0/28. Bár a címterület értékek összesíthetők, ajánlott a egyeztetése alhálózati tartományt a hálózaton belül nagyobb címterek a jövőben máshol nem használt IP-címtartományok véletlen használatának elkerülése érdekében. **A virtuális hálózat címtér az IP-címtartományt, amely Microsoft elküldésre kérése az első központi telepítése során szükséges**

- **Az Azure virtuális gép alhálózat IP-címtartomány:** az IP-címtartományt, korábban már bemutatott-hoz hozzárendelt egy (vagy terv hozzárendelni) az Azure virtuális hálózat, a SAP HANA nagy példány környezethez való csatlakozását az Azure virtuális hálózat alhálózati paramétere. Az IP-címtartomány IP-címek hozzárendelése az Azure virtuális gépeken használatos. Az IP-címek a tartományon kívül csatlakozhatnak a SAP HANA nagy példány kiszolgálókkal. Ha szükséges, a több Azure Virtuálisgép-alhálózatok is használható. A/24 CIDR-blokkja Microsoft által ajánlott minden Azure Virtuálisgép-alhálózathoz. Ez a címtartomány a Azure VNet címterületen belülre használt értékek egy részének kell lennie. Hogyan kérhet az IP-címtartomány? A vállalati hálózat team vagy szolgáltatás szolgáltató IP-címtartományt, amely jelenleg nincs használatban a hálózaton belüli kell biztosítania.

- **Virtuális hálózat átjáró alhálózati IP-címtartomány:** attól függően, hogy a használni kívánt szolgáltatásokat, az ajánlott mérete lenne:
   - Ultranagy teljesítményű ExpressRoute-átjáró: / 26 címterület - típus II osztály SKU szükséges
   - A VPN- és egy nagy teljesítményű ExpressRoute-átjáró használatával ExpressRoute létezzenek (vagy kisebb): / 27 címterülete
   - Más helyzetekben: / 28 címterület. Ez a címtartomány a "VNet címterület" értékek használt értékek egy részének kell lennie. Ez a címtartomány a Azure VNet címterület értékeket kell küldeni a Microsoftnak használt értékek egy részének kell lennie. Hogyan kérhet az IP-címtartomány? A vállalati hálózat team vagy szolgáltatás szolgáltató IP-címtartományt, amely jelenleg nincs használatban a hálózaton belüli kell biztosítania. 

- **ER-P2P csatlakozási címtartomány:** ebben a tartományban, az IP-címtartományt az SAP HANA nagy példány ExpressRoute (ER) P2P csatlakozási. Ez az IP-címeknek kell lenniük egy /29 CIDR IP-címtartományt. Ebben a tartományban kell nem lehetnek a helyszíni vagy más Azure IP-címtartományokat. Az IP-címtartomány a ER kapcsolat beállítása az Azure VNet ExpressRoute-átjárót az SAP HANA nagy példány kiszolgálókra a szolgál. Hogyan kérhet az IP-címtartomány? A vállalati hálózat team vagy szolgáltatás szolgáltató IP-címtartományt, amely jelenleg nincs használatban a hálózaton belüli kell biztosítania. **A tartomány a következő IP-címtartományt, amely Microsoft elküldésre kérése az első központi telepítése során szükséges:**
  
- **Kiszolgáló IP-készlet címtartománya:** az egyes IP-cím hozzárendelése HANA nagy példány kiszolgálók az IP-címtartomány szolgál. Az ajánlott alhálózat mérete egy/24 CIDR blokkolása - Ha azonban szükség lehet kisebb legalább 64 IP-címek megadása. Az ezt a tartományt az első 30 IP-címek nem választhatók ki használja a Microsoft. Győződjön meg arról, ez a tény biztonságos kimenekítésére is hangsúlyt a tartomány méretétől kiválasztásakor. Ebben a tartományban kell nem lehetnek a helyszíni vagy más Azure IP-címeket. Hogyan kérhet az IP-címtartomány? A vállalati hálózat team vagy szolgáltatás szolgáltató biztosítania kell az IP-címtartományok, amely jelenleg nincs használatban a hálózaton belül. Egy/24 (ajánlott) egyedi CIDR megakadályozza az SAP HANA Azure (nagy példányok) a szükséges IP-címek kiosztására használható. **A tartomány a következő IP-címtartományt, amely Microsoft elküldésre kérése az első központi telepítése során szükséges:**
 
Határozza meg, és tervezze meg a fenti IP-címtartományok van szüksége, bár nem minden őket kell továbbítani a Microsoftnak. A fenti, az IP-címtartományok Microsoft nevet kell az alábbiak:

- Az Azure VNet cím tárolóhelyeit
- ER-P2P kapcsolat címtartománya
- Kiszolgáló IP-készlet címtartománya

További Vnetek HANA nagy példányokhoz való csatlakozáshoz szükséges hozzá, meg kell az új Azure VNet címtartományt adja hozzá a Microsoftnak küldje el. 

Az alábbiakban olvashatja a különböző tartományok és néhány példa tartományok példát, meg kell konfigurálnia, és végül adhat a Microsoftnak. Ahogy látja, az Azure VNet-címterület érték nem összesített értéket az első, de az első Azure virtuális alhálózat IP-címtartományt és a virtuális hálózat átjáró alhálózati IP-címtartományban a tartomány alapján van definiálva. Az Azure virtuális hálózaton belül több Virtuálisgép-alhálózatok használatával akkor működik, ennek megfelelően az konfigurálása és elküldése a további IP-címtartományok, a további Virtuálisgép-alhálózat az Azure VNet címterület részeként.

![Az IP-címtartományok SAP HANA szükséges minimális üzemelő Azure (nagy példány)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Az adatok elküldését a Microsoftnak összesítése lehetősége is van. Ebben az esetben az Azure virtuális hálózat címtartománya csak tartalmazhat egy szóközzel. A korábbi példában használt IP-címtartományok használatával. A virtuális hálózat összesített címtartomány nézhet:

![Második lehetőségét, amely az IP-címtartományok SAP HANA szükséges minimális üzemelő Azure (nagy példány)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Két kisebb tartományt, az Azure virtuális hálózat címtartománya a megadott helyett a fent látható, amely lefedi a 4096 IP-címek egy nagyobb tartományon van. A címterület ilyen nagy definíciójának elhagyja néhány ahelyett, hogy nagy adattartományokat vizsgálnak nem használt. Mivel a virtuális hálózat címterület érték(ek) végzi, a BGP útválasztási propagálás, a nem használt tartományok a helyszíni vagy más helyen a hálózat használati útválasztási hibákat is okozhat. Ezért javasoljuk szorosan összhangban a tényleges alhálózati cím használt lemezterület címterület tartani. Ha szükséges, a virtuális hálózaton, leállás nélkül mindig értékeket is hozzáadhat új címterület később.
 
> [!IMPORTANT] 
> Minden IP-cím tartomány a ER-P2P, kiszolgáló IP-készlet, Azure virtuális hálózat címterület kell **nem** átfedésben vannak egymással vagy bármely más tartomány használt valahol máshol a hálózat; egyes kell lennie, és a két grafikus korábbi megjelenítése nem lehet bármely más tartomány alhálózat. Ha a rendszer átfedéseket tartományok között történik, akkor az Azure virtuális hálózat nem az ExpressRoute-kapcsolatcsoport csatlakozhat.

### <a name="next-steps-after-address-ranges-have-been-defined"></a>Címtartomány definiált követő lépések
Az IP-címtartományok meghatározása után a következő tevékenységek van szükség:

1. Küldje el az IP-címtartományok Azure VNet címterület, a ER-P2P kapcsolat és a kiszolgáló IP-készlet címtartománya, és más adatról, a dokumentum elején szerepel. Ezen a ponton az időben akkor is tudta elindítani a virtuális hálózat és a Virtuálisgép-alhálózatok létrehozása. 
2. Az Express Route-körhöz hozta létre a Microsoft Azure-előfizetése és a HANA nagy példány stamp között.
3. A bérlői hálózat Microsoft által készített a nagy példány stamp.
4. Microsoft hálózati IP-címek az Azure VNet címteret kommunikáló HANA nagy osztályt fogadásához (nagy példányok) Azure infrastruktúra SAP HANA konfigurálja.
5. Attól függően, hogy az adott SAP HANA a vásárolt Azure (nagy példányok) SKU Microsoft hozzárendel egy számítási egység a bérlői hálózat, lefoglalni és tároló csatlakoztatása és az operációs rendszer (SUSE vagy Red Hat Linux) telepítése. Ezeket az egységeket IP-címet a kiszolgáló IP-készlet címtartomány Ön által küldött kiveszik a Microsoftnak.

A telepítési folyamat végén Microsoft továbbítja a következő adatokat meg:
- Az Azure VNet(s) az ExpressRoute-kapcsolatcsoport, amely összeköti az Azure Vnetekhez HANA nagy példányokhoz való csatlakozáshoz szükséges információt:
     - Hitelesítési kulcs vagy kulcsok
     - ExpressRoute PeerID
- Adatok HANA nagy példányok eléréséhez, ExpressRoute-kapcsolatcsoportot és Azure VNet létrehozása után.

Is megkeresheti a dokumentum nagy példányok HANA kapcsolódás sorozatát [végpontok közötti telepítés SAP HANA-nagy példányok](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). A dokumentum a központi telepítésre példát a következő lépések láthatók. 


## <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Csatlakozás a virtuális hálózat HANA nagy példány ExpressRoute

Meghatározott összes IP-címtartományok, és most az adatok biztonsági kapott azonosítóértékeket Microsoft, indítsa el a virtuális hálózat előtt létrehozott HANA nagy példányok való kapcsolódás. Az Azure virtuális hálózat létrehozása után a virtuális hálózaton, a virtuális hálózat csatolása az ExpressRoute-kapcsolatcsoport, amely kapcsolódik a felhasználói bérlő a nagy példány stamp ExpressRoute-átjárót kell létrehozni.

> [!NOTE] 
> Ez a lépés el, mivel az új átjáró létrehozása a kijelölt Azure-előfizetés és majd kapcsolódik a megadott Azure VNet akár 30 percet is igénybe vehet.

Ha már létezik egy átjáró, ellenőrizze, hogy azt egy ExpressRoute-átjárót, vagy nem. Ha nem, akkor az átjáró kell törölni, majd újból ExpressRoute átjáróként. Egy ExpressRoute-átjáró már létrejött, mivel az Azure virtuális hálózat már csatlakoztatva van a helyi kapcsolat ExpressRoute-kapcsolatcsoport, folytassa a műveletet a Vnetek Linking című részhez.

- Használata vagy a (új) [Azure-portálon](https://portal.azure.com/), vagy a PowerShell segítségével hozzon létre egy ExpressRoute VPN-átjáró csatlakoztatva a virtuális hálózat.
  - Ha használja az Azure-portálon, adjon hozzá egy új **virtuális hálózati átjáró** majd **ExpressRoute** az átjáró típusa.
  - Ha ehelyett PowerShell, először töltse le és használja a legújabb [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) az optimális élmény biztosításához. A következő parancsokat egy ExpressRoute-átjáró létrehozásához. A szövegek utasításnak egy _$_ frissítenie kell a konkrét információkat a felhasználó által definiált változót.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Ebben a példában a HighPerformance gateway SKU lett megadva. Az alábbi lehetőségek állnak HighPerformance vagy UltraPerformance csak átjáróként termékváltozatok SAP Hana Azure (nagy példányok) támogatott.

> [!IMPORTANT]
> Az SKU nagy példányai HANA meg kell adnia S384, S384m, S384xm, S576, S768 és S960 (típusú osztály termékváltozatok), az átjáró-Termékváltozat UltraPerformance használatát megadása kötelező.

### <a name="linking-vnets"></a>Hivatkozási Vnetek

Most, hogy az Azure virtuális hálózat rendelkezik egy ExpressRoute-átjárót, a Microsoft által biztosított engedélyezési információ segítségével csatlakozzon az ExpressRoute-átjáró (nagy példány) az Azure ExpressRoute körön, ez az összekapcsolhatóság létre az SAP HANA. Ebben a lépésben az Azure-portálon vagy a PowerShell használatával hajtható végre. A portál használata ajánlott, azonban PowerShell-utasításokat a következők. 

- Lefuttatja a következő parancsokat minden egyes virtuális hálózat átjárót egy másik AuthGUID minden egyes kapcsolathoz. A parancsfájl következő megjelennek az első két bejegyzés határozza meg a Microsoft által biztosított információkat. A AuthGUID is minden virtuális hálózat és az átjáróhoz. Azt jelenti, ha hozzá szeretne adni egy másik Azure VNet, kell beszereznie egy másik AuthID a HANA nagy példányok csatlakozik az Azure ExpressRoute-kapcsolatcsoport esetében. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Ha szeretné csatlakoztatni az átjárót az előfizetéshez társított több ExpressRoute-Kapcsolatcsoportok, esetleg csak egyszer hajtható végre ezt a lépést. Például valószínűleg fog csatlakozni az azonos VNet-átjáró, amely a virtuális hálózat csatlakozik a helyi hálózat ExpressRoute-kapcsolatcsoportot.

## <a name="adding-more-ip-addresses-or-subnets"></a>Több IP-cím vagy alhálózat hozzáadása

Használja az Azure-portálon, PowerShell, vagy amikor hozzáadása több IP-címek CLI vagy alhálózatok.

Ebben az esetben az új IP-címtartományt, az új tartomány hozzáadása egy új összevont tartományt generálása helyett a VNet-terület a javaslat. Mindkét esetben meg kell küldenie ez a változás, amely engedélyezi a csatlakozást kívül, hogy új IP-címtartomány a HANA nagy példány egységek az ügyfél a Microsoft. Az új VNet címtartományt hozzáadott beolvasni az Azure támogatási kérelmet nyithatja meg. Megerősítés megjelenése után hajtsa végre a következő lépéseket.

Azure-portálról egy további alhálózati létrehozásához lásd: a cikk [hozzon létre egy virtuális hálózatot az Azure portál használatával](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network), és a PowerShell létrehozásához lásd: [PowerShell virtuális hálózat létrehozása](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="adding-vnets"></a>Vnetek hozzáadása

Egy vagy több Azure Vnetekhez kezdetben csatlakozás után érdemes az Azure (nagy példányok) SAP HANA-et elérő újak hozzáadásához. Először az Azure támogatási kérelmet, a kérelem olyan is az adott Azure-telepítés és az Azure VNet címterületek IP cím terület tranzakciónaplók azonosító információkat. Az Azure szolgáltatásfelügyelet SAP HANA majd a további virtuális hálózatokat és ExpressRoute csatlakoztatni kell a szükséges információkat biztosít. Minden virtuális hálózaton kell engedélyezési egyedi kulcs az ExpressRoute-kapcsolatcsoport HANA nagy példányokhoz való csatlakozáshoz.

Adja hozzá egy új Azure VNet lépéseket:

1. Befejeződött a bevezetési folyamat első lépéseként tekintse meg a _Azure VNet létrehozása_ szakasz.
2. Befejeződött a második lépést a bevezetési folyamat során lásd: a _átjáróalhálózat létrehozásához_ szakasz.
3. A további Vnetek csatlakozhat a HANA nagy példány ExpressRoute-kapcsolatcsoportot, nyissa meg az Azure támogatási kérelmet a az új VNet vonatkozó, és igényeljen új hitelesítési kulcsot.
4. Ha értesítést kap, hogy helyesek-e az engedélyezési, tekintse meg a használja a Microsoft által biztosított engedélyezési információt, a harmadik befejezéséhez. lépés: a bevezetési folyamat során a _Linking Vnetek_ szakasz.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Az ExpressRoute-kapcsolatcsoport sávszélessége növelése

Vegye fel a kapcsolatot az Azure szolgáltatásfelügyelet SAP HANA. Ha azt javasolja, hogy növelje a SAP HANA (nagy példányok) Azure ExpressRoute körön sávszélességét, hozzon létre egy Azure támogatási kérelmet. (Kérhet egy 10 GB/s legfeljebb egy egyetlen kapcsolatcsoport sávszélessége növelését.) A művelet végeztével befejeződik; majd kap értesítést az Azure-ban ez nagyobb sebesség engedélyezéséhez szükséges további művelet.

## <a name="adding-an-additional-expressroute-circuit"></a>További ExpressRoute-kapcsolatcsoportot hozzáadása

Vegye fel a kapcsolatot SAP HANA az Azure szolgáltatásfelügyelet, ha azt javasolja, hogy további ExpressRoute-kapcsolatcsoportot van szükség, ellenőrizze az Azure kérelmet hozzon létre egy új ExpressRoute-kapcsolatcsoportot (és engedélyezési információ a csatlakozáshoz) támogatja. A címtér, amelyet használni, a Vnetek ahhoz, hogy az Azure szolgáltatásfelügyelet SAP HANA engedély biztosításához a kérelem végrehajtása előtt meg kell határozni.

Miután az új kapcsolat jön létre, és az SAP HANA Azure szolgáltatásfelügyelet konfiguráció befejeződött, fog kell folytatnia az adatokat az értesítések fogadásához. Kövesse a fenti lépéseket követve létrehozásához és az új VNet csatlakozik a további kapcsolat. Nem tudunk Azure Vnetekhez kapcsolódni a további kapcsolat, ha azok már kapcsolódik egy másik SAP HANA Azure ugyanabban a régióban (nagy példány) Azure ExpressRoute körön.

## <a name="deleting-a-subnet"></a>Egy alhálózat törlése

Távolítsa el a virtuális hálózat alhálózatot, az Azure portálon, a PowerShell vagy a CLI használható. Abban az esetben, ha az Azure VNet IP cím tartomány vagy az Azure VNet címterület volt egy összevont tartományt, nincs nem a Microsoft az Ön szolgáltatáshoz hajtsa végre. Azzal a különbséggel, hogy a virtuális hálózat továbbra is propagálása a BGP-útvonal címterület, amely tartalmazza a törölt alhálózat. Ha több IP-címtartományokat, amelyek a törölt alhálózat egy lett hozzárendelve a Azure VNet IP cím tartomány vagy az Azure VNet címterület meghatározott, törölje, amely a VNet cím elfogyott, és ezt követően tájékoztatja az Azure Service Management azokat a tartományokat, hogy az Azure (nagy példányok) SAP HANA kommunikálni eltávolítani az SAP HANA.

Egy alhálózat törléséhez, lásd: [alhálózat törlése](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet) alhálózatok létrehozásáról további információt.

## <a name="deleting-a-vnet"></a>A virtuális hálózat törlése

A virtuális hálózat törlése, lásd: [egy virtuális hálózat törlése](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network). Az Azure szolgáltatásfelügyelet SAP HANA eltávolítja a meglévő engedélyek meg az SAP HANA (nagy példány) az Azure ExpressRoute körön, és távolítsa el az Azure VNet IP cím tartomány vagy az Azure VNet címterület a kommunikáció HANA nagy osztályt.

A virtuális hálózat az Eltávolítás után, nyissa meg az Azure támogatási kérelmet arra, hogy az IP cím terület tranzakciónaplók távolíthatók el.

Annak érdekében, hogy minden eltávolítják, törli a következő elemeket:

- Az ExpressRoute-kapcsolatot, a virtuális hálózat átjáró hálózatok átjáró nyilvános IP-címet, és virtuális hálózat

## <a name="deleting-an-expressroute-circuit"></a>Egy ExpressRoute-kapcsolatcsoport törlése

Egy további SAP HANA (nagy példányok) Azure ExpressRoute körön eltávolításához nyissa meg az Azure támogatási kérelmet az Azure szolgáltatásfelügyelet SAP HANA, és kérjen, hogy a kapcsolatcsoport törölni kell. Az Azure-előfizetés belül törölheti vagy tartani a virtuális hálózat szükséges. Azonban törölnie kell a HANA nagy példányok ExpressRoute-kapcsolatcsoportot és a csatolt VNet-átjáró közötti kapcsolat.

Ha el szeretné távolítani egy Vnetet, kövesse az útmutatást a fenti részben egy virtuális hálózat törlése.


