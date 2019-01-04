---
title: Kapcsolatok konfigurálása a virtuális gépekről az Azure-ban (nagyméretű példányok) SAP Hana-hoz |} A Microsoft Docs
description: Kapcsolatok konfigurálása a virtuális gépeket, az SAP HANA használata az Azure-ban (nagyméretű példányok).
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
ms.openlocfilehash: 9116dd8a27b268b656f688083032a127177d2d51
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754560"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Az Azure virtuális gépek csatlakoztatásához a nagyméretű HANA-példányok

A cikk [Mi az SAP HANA az Azure-ban (nagyméretű példányok)?](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) említi, hogy a minimális telepítéssel HANA nagyméretű példányok az Azure-ban az SAP alkalmazásrétegre az alábbihoz hasonló:

![SAP HANA az Azure-ban (nagyméretű példányok) és a helyszínen csatlakoztatott Azure virtuális hálózat](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Keresése az Azure virtuális hálózat oldalán közelebb, tisztában vagyunk vele van szükség:

- A SAP alkalmazás réteg a virtuális gépek telepítéséhez fog, amelybe az Azure virtuális hálózat definíciója.
- Az Azure virtuális hálózatban, amely tényleg, amelybe a virtuális gépek üzembe egy alapértelmezett alhálózat definíciója.
- A létrehozott Azure virtuális hálózat rendelkeznie kell legalább egy Virtuálisgép-alhálózatot és egy Azure ExpressRoute virtuális hálózati átjáró-alhálózat. Ezen alhálózatok IP-címtartományok megadott és tárgyaltuk, a következő szakaszokban kell rendelni.

Lássunk erre egy kicsit közelebb HANA nagyméretű példányok az Azure virtuális hálózat létrehozásakor.

## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>HANA nagyméretű példányok az Azure virtuális hálózat létrehozása

>[!Note]
>Az Azure virtuális hálózat HANA nagyméretű példányok az Azure Resource Manager üzemi modellel kell létrehozni. A régi Azure üzembehelyezési modell, a klasszikus üzemi modellt gyakran nevezik a nagyméretű HANA-példány megoldás által nem támogatott.

Az Azure portal, PowerShell, Azure-sablon vagy az Azure CLI segítségével a virtuális hálózat létrehozásához. (További információkért lásd: [hozzon létre egy virtuális hálózatot az Azure portal használatával](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). Az alábbi példában áttekintjük az Azure portal használatával létrehozott virtuális hálózat.

Láthatjuk, hogy hogyan kapcsolódik egymáshoz a virtuális hálózatok azon mi látható, különböző IP-címtartományok. Amikor hogy beszélünk a **címtér**, az Azure virtuális hálózat számára engedélyezett a címtér alatt azt értjük. Ezt a címteret is a címtartományt, amely a virtuális hálózatot használ a BGP-útvonalpropagálás. Ez **címtér** Itt láthatók:

![Megjelenik az Azure Portalon egy Azure virtuális hálózat címtere](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

Az Azure virtuális hálózat megadott 10.16.0.0/16, az előző példában egy inkább nagy és széles IP-címtartomány használatára. További alhálózatokat a virtuális hálózaton belüli összes IP-címtartományok, így azok a tartományok,-címtéren belüli rendelkezhet. Általában nem ajánlott ilyen egy nagy-címtartományt az Azure-ban egyetlen virtuális hálózaton. Azonban az alhálózatok, az Azure virtuális hálózat meghatározott vizsgáljuk meg:

![Az Azure virtual network-alhálózatokat és az IP-címtartományok](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Áttekintjük a virtuális hálózat (Itt "alapértelmezett" is nevezik) első Virtuálisgép-alhálózat és a egy "GatewaySubnet" nevű alhálózat.

Az a két előző grafikus a **virtuális hálózat címtere** egyaránt vonatkozik a **az alhálózati IP-címtartományt az Azure virtuális gépek** , és hogy a virtuális hálózati átjárót.

Korlátozhatja a **virtuális hálózat címtere** az adott tartományokra, minden egyes alhálózatot használja. Azt is megadhatja a **virtuális hálózat címtere** egy virtuális hálózat több adott címtartományok beállítását is, itt látható módon:

![Az Azure virtuális hálózat címtere a két szóközt tartalmazó](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

Ebben az esetben a **virtuális hálózat címtere** definiált két szóközt tartalmaz. Ezek ugyanazok, mint az, hogy az alhálózati IP-címtartományt a virtuális hálózati átjáró és az Azure virtuális Géphez tartozó IP-címtartományok. 

Bármely elnevezési szabványnak, például a bérlői alhálózathoz (Virtuálisgép-alhálózatok) is használhatja. Azonban **mindig kell egyet, és csak egyet, az egyes virtuális hálózati átjáró-alhálózat** az SAP HANA az Azure-ban (nagyméretű példányok) ExpressRoute-kapcsolatcsoporthoz csatlakozik. És **az átjáró-alhálózat "GatewaySubnet" névvel rendelkezik** , győződjön meg arról, hogy az ExpressRoute-átjárót megfelelően kerül.

> [!WARNING] 
> Rendkívül fontos, hogy az átjáró-alhálózat mindig neve "GatewaySubnet".

Több Virtuálisgép-alhálózatok és a nem összefüggő címtartományok is használhatja. Ezek címtartományokat kell fedezni az **virtuális hálózat címtere** a virtuális hálózat. Ezek lehetnek az összesített formában. Ezek is lehetnek a Virtuálisgép-alhálózatok és az átjáró-alhálózat pontos tartományok listáján.

Következő található egy összefoglaló az a fontos alapvető tudnivalók az Azure virtuális hálózat, amely kapcsolódik a HANA nagyméretű példányok:

- Kell küldenie a **virtuális hálózat címtere** a Microsoftnak, amikor egy nagyméretű HANA-példányok kezdeti telepítése. 
- A **virtuális hálózat címtere** egy körére, amely lefedi a tartományokat mindkét az alhálózati IP-címtartományt a virtuális hálózati átjáró és az Azure virtuális gép is lehet.
- Vagy több tartomány, mind a különböző IP-címtartományok VM alhálózati IP-cím tranzakciónaplók és a virtuális hálózati átjáró IP-címtartomány küldhet.
- A definiált **virtuális hálózat címtere** útválasztási BGP-útvonalpropagálás szolgál.
- Az átjáró-alhálózat nevét kell lennie: **"GatewaySubnet"**.
- A címtér a nagyméretű HANA-példány oldalon szűrőként engedélyezése vagy letiltása a forgalom, a nagyméretű HANA-példány egység az Azure-ból szolgál. Az Azure virtuális hálózat és az IP-címtartományok, a nagyméretű HANA-példány oldalon szűréshez konfigurált BGP útválasztási adatokat meg kell egyeznie. Ellenkező esetben fordulhat elő kapcsolódási problémák.
- Az átjáró alhálózatának ismertetése egyes részletei később, a szakaszban tárgyalt **két virtuális hálózat HANA nagyméretű példány expressroute.**



## <a name="different-ip-address-ranges-to-be-defined"></a>Különböző IP-címtartományokat meghatározni 

Már bevezettük a nagyméretű HANA-példányok üzembe helyezéséhez szükséges IP-címtartományok egyes. Azonban további IP-címtartományokat, amelyek is fontos. Vegyünk át néhány további részleteket. A következő IP-címek nem az összes szükséges küldhető el a Microsoftnak. Azonban szüksége határozza meg azokat a kezdeti telepítés kérelem elküldése előtt:

- **Virtuális hálózat címtere**: A **virtuális hálózat címtere** van a cím terület paraméter az Azure virtuális hálózathoz rendelt IP-címtartományokat. Ezeket a hálózatokat a SAP HANA nagyméretű példányok környezet csatlakozni.

 Azt javasoljuk, hogy a cím terület paraméter értéke egy többsoros. Ez az alhálózati tartományhoz az Azure virtuális gépek és az Azure-átjáró az alhálózat tranzakciónaplók kell állnia. Az alhálózati címtartomány látható volt az előző képek. A helyszíni vagy a kiszolgáló IP-készlet vagy ER-P2P címtartományok nem lehetnek átfedésben. 
 
Hogyan be ezen IP-cím tranzakciónaplók? 

A vállalati hálózat csapata vagy a service provider biztosítania kell egy vagy több IP-cím átküldeni a hálózaton belüli nincsenek használva. Például tegyük az Azure virtuálisgép-alhálózat 10.0.1.0/24, és az alhálózat az Azure-átjáró-alhálózat 10.0.2.0/28.  Javasoljuk, hogy az Azure virtuális hálózat címterét-e a következő sort: 10.0.1.0/24 és 10.0.2.0/28. 

Bár a cím terület értékeik összesíthetők, javasoljuk, hogy az alhálózati címtartományokhoz megfelelő őket. Ezzel a módszerrel véletlenül elkerülheti a nem használt IP-címtartományok a hálózaton lévő máshol nagyobb címterek belül újbóli használata. **A virtuális hálózati címtér az IP-címtartományt. Hamarosan a Microsoftnak, amikor Ön feltesz egy kezdeti üzembe helyezéshez szükséges**.

- **Az Azure virtuális gép alhálózati IP-címtartomány:** Az IP-címtartományt rendel az Azure virtuális hálózat alhálózati paraméter lesz. Ez a paraméter az Azure virtuális hálózat és az SAP HANA nagyméretű példányok környezethez kapcsolódik. Az IP-címtartományt az IP-címek hozzárendelése az Azure-beli virtuális gépek szolgál. Az IP-címek a tartományon való csatlakozáshoz az SAP HANA nagyméretű példányok kiszolgáló(k) engedélyezettek. Ha szükséges, használhatja a több Azure-beli Virtuálisgép-alhálózatot. Azt javasoljuk, hogy egy/24 minden Azure-beli Virtuálisgép-alhálózat CIDR-blokkja. Ez a címtartomány egy Azure virtuális hálózat címterében használt értékeket részének kell lennie. 

Hogyan be az IP-címtartomány? 

A vállalati hálózat csapata vagy a service provider egy IP-címtartományt, amely nincs használatban a hálózaton belüli kell biztosítania.

- **Virtuális hálózati átjáró alhálózati IP-címtartomány:** Attól függően, a Funkciók, amelyet használni szeretne az ajánlott mérete a következő:
   - Ultranagy teljesítményű ExpressRoute-átjáró:, / 26 címterület – II. típusú osztályhoz termékváltozatok szükséges.
   - Együttműködés a VPN és ExpressRoute használatával egy nagy teljesítményű ExpressRoute virtuális hálózati átjáró (vagy kisebb): / 27-es címterület.
   - Minden más helyzetekben: / 28 címterület. Ez a címtartomány a "Virtuális hálózati címtér" értékeket használt értékek egy részének kell lennie. A címtartományt, amely elküldés a Microsoftnak az Azure virtual network terület értékeket használt értékeket egy részének kell lennie. Hogyan be az IP-címtartomány? A vállalati hálózat csapata vagy a service provider egy IP-címtartományt, amely a hálózaton belüli jelenleg nem használt kell biztosítania. 

- **ER-P2P kapcsolat címtartomány:** Ezt a tartományt az IP-címtartomány a SAP HANA nagyméretű példányok az ExpressRoute (ER) P2P kapcsolat. Ez az IP-címeket kell lennie egy/29 CIDR IP-címtartományt. Ez a tartomány nem lehet átfedésben a helyszíni vagy más Azure IP-címtartományok. Az IP-címtartományt az ER-kapcsolat beállítása az ExpressRoute virtuális átjáró, az SAP HANA nagyméretű példányok kiszolgálókra szolgál. Hogyan be az IP-címtartomány? A vállalati hálózat csapata vagy a service provider egy IP-címtartományt, amely a hálózaton belüli jelenleg nem használt kell biztosítania. **Ebben a tartományban egy IP-címtartományt. Hamarosan a Microsoftnak, amikor Ön feltesz egy kezdeti üzembe helyezéshez szükséges**.
  
- **Kiszolgáló IP-készlet címtartomány:** Az IP-címtartományt az egyes IP-cím hozzárendelése a HANA nagyméretű példányok kiszolgálók szolgál. Ajánlott alhálózat mérete egy/24 CIDR-blokkja. Ha szükséges, mindössze 64 IP-címek kisebb lehet. Ebből a tartományból az első 30 IP-címek vannak használatra fenntartva a Microsoft által. Győződjön meg arról, hogy Ön fiók számára Emiatt ha úgy dönt, hogy a tartomány méretétől. Ez a tartomány nem lehet átfedésben a helyszíni vagy más Azure IP-címeket. Hogyan be az IP-címtartomány? A vállalati hálózat csapata vagy a service provider egy IP-címtartományt, amely a hálózaton belüli jelenleg nem használt kell biztosítania. 

 **Ebben a tartományban egy IP-címtartományt, amelyet be kell nyújtani a Microsoft kérése az első üzembe helyezés során**.
 
Adja meg, és az előzőekben leírt IP-címtartományok megtervezése kell. Azonban nem kell továbbítja azokat a Microsoftnak. Nevezze el a Microsoftnak kell az IP-címtartományok a következők:

- Az Azure virtual network-cím (tárolóhelyeit)
- ER-P2P kapcsolat címtartománya
- Kiszolgáló IP-készlet címtartománya

Ha további virtuális hálózatok, amelyek nagyméretű HANA-példányokhoz való kapcsolódáshoz szükséges hozzá, akkor küldje el az új Azure virtuális hálózat címterét, amely a Microsoft adja hozzá. 

Következő egy példa a különböző tartományok és néhány példa címtartományok, konfigurálhatja, és végül adja meg a Microsoft szükség szerint. Az Azure virtuális hálózat címtere értéke az első példában nem összesíti. Azonban az első Azure virtuális gép alhálózati IP-címtartományt az tartományait és a virtuális hálózati átjáró alhálózati IP-címtartomány van definiálva. 

Használhatja az Azure virtuális hálózaton belül több Virtuálisgép-alhálózatok, konfigurálásakor, és küldje el a további IP-címtartományokat, a további Virtuálisgép-alhálózat az Azure virtuális hálózat címtere része.

![Az SAP HANA Azure-ban (nagyméretű példányok) minimális üzemelő példányon szükséges IP-címtartományok](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Az adatokat a Microsoftnak elküldött is lehet összesíteni. Ebben az esetben az Azure virtuális hálózat címteréhez csak egy tárolóhely tartalmazza. Az összesített virtuális hálózat címtere rákeresve használja a korábbi példából származó IP-címtartományokat, a következő képhez hasonlóan:

![Második lehetőségét, hogy az SAP HANA Azure-ban (nagyméretű példányok) minimális üzemelő példányon szükséges IP-címtartományok](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

A példában két kisebb tartományt, amely az Azure virtuális hálózat címterében meghatározott helyett, amely lefedi a 4096 IP-címek egy körére van. A címtér ilyen nagy definíciójának hagy egyes inkább nagy adattartományokat fel nem használt. Mivel a virtuális hálózati cím terület értékeket használja a BGP-útvonalpropagálás, használat, a fel nem használt tartományok a helyszínen vagy más helyen a hálózat útválasztási problémák léphetnek fel. 

Ezért azt javasoljuk, hogy őrizze meg a címtartományt, a tényleges alhálózat címtere használt szorosan igazítva. Ha szükséges, járó a virtuális hálózaton, leállás nélkül mindig később is hozzáadhat új cím terület értékeket.
 
> [!IMPORTANT] 
> Minden IP-címtartomány ER-P2P, a kiszolgáló IP-készlet és az Azure virtuális hálózat címtere kell **nem** átfedésben vannak egymással, vagy bármely más a hálózaton használt tartománnyal. Minden egyes diszkrétnek kell lennie. Látható a két előző grafikák, azok nem is egy alhálózat bármely más tartomány. Átfedésben van a tartományok között fordul elő, ha az az Azure virtuális hálózat nem lehet csatlakozni az ExpressRoute-kapcsolatcsoport.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Címtartomány definiálva követő lépések
Az IP-címtartományok meghatározása után a következőkre van szükség:

1. Küldje el az Azure virtuális hálózat címterét, a ER-P2P kapcsolat és a kiszolgáló IP-készlet címtartomány, és egyéb adatok, a dokumentum elején szereplő IP-címtartományokat. Ezen a ponton sikerült is elindítható a virtuális hálózat és a Virtuálisgép-alhálózatok létrehozása. 
2. ExpressRoute-kapcsolatcsoport hozza létre a Microsoft az Azure-előfizetés és a HANA nagyméretű szolgáltatáspéldányban között.
3. A bérlői hálózathoz a Microsoft által a nagyméretű szolgáltatáspéldányban jön létre.
4. A Microsoft hálózati konfigurálja az SAP HANA az Azure-ban (nagyméretű példányok) infrastruktúra, fogadja el az Azure virtuális hálózat címterét, amely kommunikál a HANA nagyméretű példányok IP-címeket.
5. Attól függően, az adott SAP HANA az Azure (nagyméretű példányok) Termékváltozat, amelytől vásárolta a Microsoft hozzárendel egy számítási egység a bérlői hálózathoz. Azt is foglalja le és csatlakoztatja a storage, és telepíti az operációs rendszert (SUSE vagy Red Hat Linux). Ezek az egységek IP-címek a kiszolgáló IP-készlet címtartománya elküldött kiveszik a Microsoftnak.

A telepítési folyamat végén a Microsoft a következő adatokat biztosít Önnek:
- Olyan információkat, amelyeket a nagyméretű HANA-példányok Azure virtuális hálózatokhoz csatlakozó ExpressRoute-kapcsolatcsoporthoz az Azure virtuális hálózat csatlakozni:
     - Engedélyezési kulcsával
     - Az ExpressRoute PeerID
- Adatok nagyméretű HANA-példányok eléréséhez ExpressRoute-kapcsolatcsoport és az Azure virtuális hálózat létrehozása után.

A HANA nagyméretű példányok összekapcsolása a dokumentum sorozatát is megtalálhatja [SAP HANA az Azure-ban (nagyméretű példányok) telepítő](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Számos, a következő lépéseket a dokumentum a központi telepítésre példát látható. 

## <a name="next-steps"></a>További lépések

- Tekintse meg [két virtuális hálózat HANA nagyméretű példány expressroute](hana-connect-vnet-express-route.md).
