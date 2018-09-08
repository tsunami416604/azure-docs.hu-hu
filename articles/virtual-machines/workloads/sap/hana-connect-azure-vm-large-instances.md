---
title: Kapcsolatok konfigurálása a virtuális gépekről az Azure-ban (nagyméretű példányok) SAP Hana-hoz |} A Microsoft Docs
description: Kapcsolatok konfigurálása a virtuális gépek használatához az SAP HANA az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0e09e6dfce5d0ede525f461193866219b7f9429
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164755"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Az Azure virtuális gépek csatlakoztatásához a nagyméretű HANA-példányok

Említettek szerint a [SAP HANA (nagyméretű példányok) áttekintése és architektúrája az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) HANA nagyméretű példányok minimális telepítése a SAP alkalmazás réteggel Azure alábbihoz hasonló:

![SAP HANA az Azure-ban (nagyméretű példányok) és a helyszínen csatlakoztatott Azure virtuális hálózat](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Keresése az Azure virtuális hálózat oldalán közelebb, tisztában vagyunk vele van szükség:

- Egy Azure virtuális hálózatot, amelyet a SAP alkalmazás réteg be a virtuális gépek telepítéséhez használandó definíciója.
- Hogy automatikusan azt jelenti, hogy egy alapértelmezett alhálózat az Azure virtuális hálózat van meghatározva, amely valójában a virtuális gépeken való üzembe helyezéséhez használt.
- A létrehozott Azure virtuális hálózathoz rendelkeznie kell legalább egy Virtuálisgép-alhálózat és a egy ExpressRoute-átjáró-alhálózatot. Ezen alhálózatok IP-címtartományok megadott és tárgyaltuk, a következő szakaszokban kell rendelni.

Tehát vizsgáljuk meg közelebbről egy kicsit HANA nagyméretű példányok az Azure virtuális hálózat létrehozása

## <a name="creating-the-azure-vnet-for-hana-large-instances"></a>HANA nagyméretű példányok az Azure virtuális hálózat létrehozása

>[!Note]
>Az Azure virtuális hálózat, a nagyméretű HANA-példányt az Azure Resource Manager-alapú üzemi modellel kell létrehozni. A régi Azure üzembehelyezési modell, gyakran nevezik a klasszikus üzemi modellben nem támogatott a nagyméretű HANA-példány megoldással.

A virtuális hálózat az Azure portal, PowerShell, az Azure-sablon vagy az Azure CLI segítségével hozható létre (lásd: [hozzon létre egy virtuális hálózatot az Azure portal használatával](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). Az alábbi példában áttekintjük az Azure Portalon létrehozott egy vnetbe.

Mi az Azure Portalon egy Azure virtuális hálózat definíciói megvizsgáljuk, ha vizsgáljuk meg, néhány definíciókat és azok összefüggéseit, hogy a listában az eltérő IP-címtartományok. Ahogy arról beszélünk a **címtér**, az Azure virtuális hálózat számára engedélyezett a címtér alatt azt értjük. Ezt a címteret is a címtartományt, amely a virtuális hálózatot használ a BGP-útvonalpropagálás. Ez **címtér** Itt láthatók:

![Cím terület az Azure virtuális hálózat jelenik meg az Azure Portalon](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

Abban az esetben 10.16.0.0/16, az előző az Azure virtuális hálózat megadott egy inkább nagy és széles IP-címtartomány használatára. Azt jelenti, hogy további alhálózatokat a virtuális hálózaton belüli összes IP-címtartományok,-Címtéren belüli is rendelkezik saját tartományok. Általában nem útválasztókra sincs ilyen nagy címtartomány egy virtuális hálózathoz az Azure-ban. De kap egy lépéssel tovább, az alhálózatok, az Azure virtuális hálózat meghatározott vizsgáljuk meg:

![Az Azure VNet-alhálózatok és az IP-címtartományok](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Ahogy láthatjuk, áttekintjük a virtuális hálózat (Itt "default" is nevezik) első Virtuálisgép-alhálózat és a egy "GatewaySubnet" nevű alhálózat.
A következő szakaszban nevezzük az volt a neve "default", a grafikus elemek az alhálózat IP-címtartomány **Azure-beli Virtuálisgép-alhálózat IP-címtartomány**. A következő szakaszokban azt tekintse meg az átjáró-alhálózat, IP-címtartományú **virtuális hálózati átjáró alhálózati IP-címtartomány**. 

A fenti két grafikus által bemutatott esetben látni, hogy a **virtuális hálózati címtér** foglalja, a **Azure-beli Virtuálisgép-alhálózat IP-címtartomány** és a **virtuális hálózati átjáró alhálózati IP-címtartomány**. 

Más esetekben, ahol kell takarítson meg, vagy pedig az adott, az az IP-címtartományok, korlátozhatja a **virtuális hálózati címtér** egy virtuális hálózat, az adott tartomány minden egyes alhálózatot használja. Ebben az esetben megadhat a **virtuális hálózati címtér** egy vnet több adott címtartományok itt látható módon:

![Két szóközt tartalmazó Azure virtuális hálózati címtér](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

Ebben az esetben a **virtuális hálózati címtér** definiált két szóközt tartalmaz. E két szóköz megegyeznek a megadott IP-címtartományok **Azure-beli Virtuálisgép-alhálózat IP-címtartomány** és a **virtuális hálózati átjáró alhálózati IP-címtartományt.**

Bármely elnevezési szabványnak, például a bérlői alhálózathoz (Virtuálisgép-alhálózatok) is használhatja. Azonban **mindig kell egy, és csak egy, az egyes virtuális hálózatok átjáró-alhálózat** az SAP HANA az Azure-ban (nagyméretű példányok) ExpressRoute-kapcsolatcsoporthoz csatlakozik. És **az átjáró-alhálózatot kell mindig neve "GatewaySubnet"** annak biztosítása érdekében a megfelelő elhelyezését az ExpressRoute-átjárót.

> [!WARNING] 
> Rendkívül fontos, hogy az átjáró-alhálózat mindig neve "GatewaySubnet".

Használható több Virtuálisgép-alhálózatok, még a nem összefüggő címtartományok felhasználásával. Amint azt korábban említettük, ezek címtartományok által kell szerepelnie, de a **virtuális hálózati címtér** a virtuális hálózat vagy összesített formában, vagy a Virtuálisgép-alhálózatok és az átjáró-alhálózat pontos tartományok listáján.

Egy Azure virtuális hálózat, amely kapcsolódik a HANA nagyméretű példányok kapcsolatos fontos tény összegzése:

- Elküldés a Microsoftnak kell a **virtuális hálózati címtér** végrehajtása egy nagyméretű HANA-példányok kezdeti telepítése során. 
- A **virtuális hálózati címtér** , amely lefedi a tartományt az Azure virtuális gép alhálózati IP cím tranzakciónaplók és a virtuális hálózati átjáró alhálózati IP-címtartomány egy körére is lehet.
- Vagy elküldheti **virtuális hálózati címtér** több tartomány, mind a különböző IP-címtartományok VM alhálózati IP-cím tranzakciónaplók és a virtuális hálózati átjáró alhálózati IP-címtartományt.
- A definiált **virtuális hálózati címtér** használt útválasztási BGP-útvonalpropagálás van.
- Az átjáró-alhálózat nevét kell lennie: **"GatewaySubnet".**
- A **virtuális hálózati címtér** engedélyezheti vagy letilthatja a forgalmat a nagyméretű HANA-példány egységre az Azure-ból a nagyméretű HANA-példány oldalon szűrőként szolgál. Ha az Azure virtuális hálózat és a nagyméretű HANA-példány oldalon szűréshez konfigurált IP-címtartományok a BGP útválasztási információk nem egyeznek, a kapcsolódási problémák merülhetnek fel.
- Nincsenek az átjáró-alhálózat bizonyos adatai, amelyek lejjebb "A virtuális hálózathoz csatlakozik a HANA nagyméretű példány ExpressRoute" szakaszban tárgyalt



## <a name="different-ip-address-ranges-to-be-defined"></a>Különböző IP-címtartományokat meghatározni 

Már bevezettük némelyike a korábbi szakaszokban nagyméretű HANA-példányok üzembe helyezéséhez szükséges IP-címtartományokat. De van néhány további IP-címtartományokat, amelyek fontosak. Vegyük át néhány további részleteket. A következő IP-címek, amelyek nem az összes szükséges elküldését a Microsoftnak kell definiálni, a kezdeti telepítés kérelem elküldése előtt:

- **VNet-címtér:** ahogy korábban már jelent meg, vagy az IP-cím átküldeni rendelt (vagy terv hozzárendelése), a cím terület paraméterben az Azure virtuális hálózat (VNet) összekötő az SAP HANA nagyméretű példányok környezetbe. Javasoljuk, hogy a címtartomány paraméter értéke egy többsoros, ahogyan korábban a grafikus elemek az Azure Virtuálisgép-alhálózat tranzakciónaplók és az Azure-átjáró alhálózati címtartomány áll. Ez a tartomány nem lehetnek átfedésben a helyszíni vagy a kiszolgáló IP-készlet vagy ER-P2P-címtartományt. Ez vagy ezen IP-cím tranzakciónaplók hogyan? A vállalati hálózat csapata vagy a service provider egy vagy több IP cím tranzakciónaplók, amelyek nem használják a hálózaton belül, vagy kell biztosítania. Példa: Ha az Azure Virtuálisgép-alhálózat 10.0.1.0/24 (lásd fentebb), és az Azure-átjáró-alhálózat (lásd alább) 10.0.2.0/28, majd az Azure virtuális hálózat Címterét javasoljuk, hogy két sort; lehet 10.0.1.0/24 és 10.0.2.0/28. Bár a címtér értékeik összesíthetők, ajánlott a megfelelő őket az alhálózati címtartományokhoz a hálózaton belül a jövőben máshol nagyobb címterei nem használt IP-címtartományok véletlen használatának elkerülése érdekében. **A virtuális hálózat Címtere egy IP-címtartományt, amelyet be kell nyújtani a Microsoft kérése az első üzembe helyezés során**

- **Az Azure virtuális gép alhálózati IP-címtartomány:** az IP-címtartomány, mivel korábban már tárgyalt hozzárendelte egy (vagy tervezett hozzárendelése) az Azure virtuális hálózat, a SAP HANA nagyméretű példányok környezet csatlakozik az Azure virtuális hálózat alhálózati paraméteréhez. Az IP-címtartományt az IP-címek hozzárendelése az Azure-beli virtuális gépek szolgál. Az IP-címek a tartományon való csatlakozáshoz az SAP HANA nagyméretű példányok kiszolgáló(k) engedélyezettek. Ha szükséges, több Azure-beli Virtuálisgép-alhálózat használható. Egy/24 CIDR-blokk a Microsoft által ajánlott minden egyes Azure Virtuálisgép-alhálózatot. Ez a címtartomány egy Azure virtuális hálózat címterében használt értékek részének kell lennie. Hogyan kérhet az IP-címtartomány? A vállalati hálózat csapata vagy a service provider egy IP-címtartományt, amely jelenleg nem használja a hálózaton belüli kell biztosítania.

- **Virtuális hálózati átjáró alhálózati IP-címtartomány:** függően a funkciókat tervez használni, az ajánlott mérete lenne:
   - Ultranagy teljesítményű ExpressRoute-átjáró:, / 26 címterület – II. típusú osztályhoz termékváltozatok szükséges
   - A VPN és ExpressRoute használatával egy nagy teljesítményű ExpressRoute-átjáró együttes jelenléte (vagy kisebb): / 27-es címterülete
   - Minden más helyzetekben: / 28 címterület. Ez a címtartomány a "Virtuális hálózati címtér" értékeket használt értékek egy részének kell lennie. Ez a címtartomány küldheti be a Microsoft Azure virtuális hálózat Címtere értékek az értékeket egy részének kell lennie. Hogyan kérhet az IP-címtartomány? A vállalati hálózat csapata vagy a service provider egy IP-címtartományt, amely jelenleg nem használja a hálózaton belüli kell biztosítania. 

- **ER-P2P kapcsolathoz címtartomány:** ezt a tartományt az IP-címtartomány a SAP HANA nagyméretű példányok az ExpressRoute (ER) P2P kapcsolat. Ez az IP-címeket kell lennie egy/29 CIDR IP-címtartományt. Ez a tartomány nem lehet átfedésben a helyszíni vagy más Azure IP-címtartományok. Az IP-címtartományt az ER-kapcsolat beállítása az Azure virtuális hálózat ExpressRoute-átjárót az SAP HANA nagyméretű példányok kiszolgálókra a szolgál. Hogyan kérhet az IP-címtartomány? A vállalati hálózat csapata vagy a service provider egy IP-címtartományt, amely jelenleg nem használja a hálózaton belüli kell biztosítania. **Ebben a tartományban egy IP-címtartományt, amelyet be kell nyújtani a Microsoft kérése az első üzembe helyezés során**
  
- **Kiszolgáló IP-készlet címtartomány:** az IP-címtartományt az egyes IP-cím hozzárendelése a HANA nagyméretű példányok kiszolgálók szolgál. Ajánlott alhálózat mérete egy/24 CIDR letiltása – Ha azonban szükség lehet kisebb a minimális biztosító 64 IP-címek. Ebből a tartományból az első 30 IP-címek vannak használatra fenntartva a Microsoft által. Győződjön meg arról, emiatt van-e sorolva a tartomány méretétől kiválasztásakor. Ez a tartomány nem lehet átfedésben a helyszíni vagy más Azure IP-címeket. Hogyan kérhet az IP-címtartomány? A vállalati hálózat csapata vagy a service provider egy IP-címtartományt, amely jelenleg nem használja a hálózaton belüli kell biztosítania. Egy/24 az SAP Hana az Azure-ban (nagyméretű példányok) szükséges, konkrét IP-címek hozzárendelése használandó egyedi CIDR (ajánlott) letiltása. **Ebben a tartományban egy IP-címtartományt, amelyet be kell nyújtani a Microsoft kérése az első üzembe helyezés során**
 
Határozza meg, és tervezze meg a fenti IP-címtartományokat kell, bár nem minden őket kell továbbítani a Microsoftnak. A fenti összegzéséhez nevezze el a Microsoftnak kell IP-címtartományok a következők:

- Az Azure VNet-cím (tárolóhelyeit)
- ER-P2P kapcsolat címtartománya
- Kiszolgáló IP-készlet címtartománya

További virtuális hálózatok, amelyek nagyméretű HANA-példányokhoz való kapcsolódáshoz szükséges hozzá, akkor küldje be az új Azure virtuális hálózati címtér adja hozzá a Microsoft van szükség. 

Következő egy példa a különböző tartományok és néhány példa címtartományok, konfigurálhatja, és végül adja meg a Microsoft szükség szerint. Ahogy látható, az Azure virtuális hálózati címtér értéke nem összesített értéket jelenít meg az első példában, de az első Azure virtuális gép alhálózati IP-címtartományt és a virtuális hálózati átjáró alhálózati IP-címtartomány a tartomány alapján van definiálva. Az Azure virtuális hálózaton belül több Virtuálisgép-alhálózatok használata akkor működik megfelelően, konfigurálása és elküldése a további IP-címtartományok a további Virtuálisgép-alhálózat, az Azure virtuális hálózati címtér részeként.

![Az SAP HANA Azure-ban (nagyméretű példányok) minimális üzemelő példányon szükséges IP-címtartományok](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Elküldés a Microsoftnak az adatok összesítése lehetősége is van. Ebben az esetben az Azure virtuális hálózat Címteréhez csak tartalmazhat egy szóközzel. A példa a korábban használt IP-címtartományokat használja. A virtuális hálózatok közötti összesített címtartomány nézhet:

![Második lehetőségét, hogy az SAP HANA Azure-ban (nagyméretű példányok) minimális üzemelő példányon szükséges IP-címtartományok](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Amint láthatja a fenti két kisebb tartományt, amely az Azure virtuális hálózat címterében meghatározott helyett, amely lefedi a 4096 IP-címek egy körére rendelkezünk. A címtér ilyen nagy definíciójának hagy egyes inkább nagy adattartományokat fel nem használt. Mivel a virtuális hálózati címtér érték(ek) BGP-útvonalpropagálás használnak, a fel nem használt tartományok a helyszínen vagy más helyen a hálózat használati útválasztási problémák léphetnek fel. Ezért azt javasoljuk, hogy tartsa meg a címtartományt, a tényleges alhálózat címtere használt szorosan igazítva. Ha szükséges, járó a virtuális hálózaton, leállás nélkül mindig később is hozzáadhat új címteret értékeket.
 
> [!IMPORTANT] 
> Minden IP-cím-tartományt az ER-P2P, kiszolgáló IP-készlet Azure virtuális hálózati címtér kell **nem** egymással átfedésben vagy bármilyen más tartományt használják valahol máshol a hálózaton; egyes diszkrétnek kell lennie, és a két grafikus korábbi megjelenítése nem lehet egy az alhálózat bármely más tartomány. Átfedésben van a tartományok között fordul elő, ha az Azure virtuális hálózat nem csatlakozni lehet, hogy az ExpressRoute-kapcsolatcsoport.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Címtartomány definiálva követő lépések
Az IP-címtartományok meghatározása után a következő tevékenységeket kell fordulhat elő:

1. Az IP-címtartományok nyújt Azure virtuális hálózat Címtere, az ER-P2P kapcsolat és a kiszolgáló IP-készlet címtartomány más, a dokumentum elején szereplő adatokkal együtt. Ezen a ponton az időben akkor is sikerült elindítani a virtuális hálózat és a Virtuálisgép-alhálózatok létrehozása. 
2. Egy Expressroute-kapcsolatcsoportot hozza létre a Microsoft az Azure-előfizetés és a HANA nagyméretű szolgáltatáspéldányban között.
3. A bérlői hálózathoz a Microsoft által a nagyméretű szolgáltatáspéldányban jön létre.
4. A Microsoft hálózati konfigurálja az SAP HANA az Azure-ban (nagyméretű példányok) infrastruktúra, fogadja el az Azure virtuális hálózat Címterét, amely kommunikál a HANA nagyméretű példányok IP-címeket.
5. Attól függően, az adott SAP HANA az Azure (nagyméretű példányok) Termékváltozat vásárolt Microsoft hozzárendel egy számítási egység a bérlői hálózathoz, lefoglalni és csatlakoztassa a storage és (SUSE vagy Red Hat Linux) az operációs rendszer telepítése. Ezek az egységek IP-címek kiveszik a kiszolgáló IP-címkészlet címtartomány küldte el a Microsoftnak.

A telepítési folyamat végén a Microsoft a következő adatokat biztosít Önnek:
- Az Azure-hoz a HANA nagyméretű példányok az Azure virtuális hálózatokhoz csatlakozó ExpressRoute-kapcsolatcsoporthoz való csatlakozáshoz szükséges információkat:
     - Engedélyezési kulcsával
     - Az ExpressRoute PeerID
- Adatok eléréséhez a HANA nagyméretű példányok, az ExpressRoute-kapcsolatcsoport és Azure virtuális hálózat létrehozása után.

A HANA nagyméretű példányok összekapcsolása a dokumentum sorozatát is megtalálhatja [végpontok közötti telepítés az SAP HANA nagyméretű példányok](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Számos, a következő lépéseket a dokumentum a központi telepítésre példát látható. 

**Következő lépések**

- Tekintse meg [egy virtuális hálózathoz csatlakozik a HANA nagyméretű példányok ExpressRoute](hana-connect-vnet-express-route.md).