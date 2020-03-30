---
title: A virtuális gépek és az SAP HANA közötti kapcsolat beállítása az Azure-ban (nagy példányok) | Microsoft dokumentumok
description: Az SAP HANA Azure-beli (nagy példányok) virtuális gépekről történő csatlakoztatási beállítása.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fb6f88fbfcbd539603e435b11661c428d54f3c34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74224729"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Azure-beli virtuális gépek csatlakozása nagy méretű HANA-példányokhoz

A cikk [mi az SAP HANA az Azure-ban (nagy példányok)?](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) megemlíti, hogy a HANA nagy példányok minimális üzembe helyezése az SAP-alkalmazásréteggel az Azure-ban a következőképpen néz ki:

![Az Azure virtuális hálózat csatlakozik az SAP HANA-hoz az Azure-ban (nagy példányok) és a helyszínen](./media/hana-overview-architecture/image1-architecture.png)

Közelebbről megvizsgálva az Azure virtuális hálózati oldalát, a következőkre van szükség:

- Egy Azure virtuális hálózat definíciója, amelybe az SAP-alkalmazásréteg virtuális gépeinek üzembe helyezését fogja telepíteni.
- Az Azure virtuális hálózat alapértelmezett alhálózatának definíciója, amely valóban az, amelybe a virtuális gépek üzembe helyezése van.
- A létrehozott Azure virtuális hálózatnak legalább egy virtuális gép alhálózattal és egy Azure ExpressRoute virtuális hálózati átjáró alhálózattal kell rendelkeznie. Ezeket az alhálózatokat a megadott és a következő szakaszokban tárgyalt IP-címtartományokhoz kell rendelni.


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Az Azure virtuális hálózat létrehozása hana nagy példányokhoz

>[!Note]
>A HANA nagy példányok Azure virtuális hálózatát az Azure Resource Manager telepítési modelljével kell létrehozni. A régebbi Azure-telepítési modell, közismert nevén a klasszikus üzembe helyezési modell, nem támogatja a HANA nagy példány megoldás.

Használhatja az Azure Portalon, a PowerShell, egy Azure-sablon, vagy az Azure CLI a virtuális hálózat létrehozásához. (További információt a [Virtuális hálózat létrehozása az Azure Portal használatával című témakörben](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)talál. A következő példában egy virtuális hálózatot tekintünk meg, amely az Azure Portal használatával jön létre.

Ha a jelen dokumentációban szereplő **címterületre** hivatkozik, az Azure virtuális hálózat által használható címterületre. Ez a címterület egyben az a címtartomány is, amelyet a virtuális hálózat a BGP-útvonal propagálásához használ. Ez **a címtér** itt látható:

![Az Azure portalon megjelenített Azure-virtuális hálózat címtere](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

Az előző példában a 10.16.0.0/16, az Azure virtuális hálózat kapott egy meglehetősen nagy és széles IP-címtartományhasználata. Ezért a virtuális hálózaton belüli további alhálózatok összes IP-címtartománya az adott címterületen belül lehet. Általában nem javasoljuk, hogy egy ilyen nagy címtartomány egyetlen virtuális hálózat az Azure-ban. De nézzük meg az Azure virtuális hálózatában definiált alhálózatokat:

![Az Azure virtuális hálózati alhálózatai és IP-címtartományai](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Megtekintjük a virtuális hálózat egy első virtuális gép alhálózat (itt az úgynevezett "alapértelmezett") és egy alhálózat neve "GatewaySubnet".

Az előző két ábrán a **virtuális hálózati címterület** az **Azure virtuális gép alhálózati IP-címtartományát** és a virtuális hálózati átjáró alhálózati CÍMtartományát is lefedi.

A virtuális **hálózati címteret** az egyes alhálózatok által használt tartományokra korlátozhatja. A virtuális hálózat **virtuális hálózati címterét** több konkrét tartományként is megadhatja, ahogy az itt látható:

![Az Azure virtuális hálózati címtere két szóközzel](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

Ebben az esetben a **virtuális hálózati címtér** két tér van definiálva. Ezek megegyeznek az Azure virtuális gép és a virtuális átjáró alhálózati IP-címtartományához definiált IP-címtartományokkal. 

Ezekhez a bérlői alhálózatokhoz (virtuálisgép-alhálózatokhoz) bármilyen elnevezési szabványt használhat. Azonban **mindig kell lennie egy, és csak egy átjáró alhálózat minden virtuális hálózat,** amely csatlakozik az SAP HANA az Azure (nagy példányok) ExpressRoute-kapcsolat. **Ezt az átjáró-alhálózatot "GatewaySubnet" névnek kell nevezni** az ExpressRoute-átjáró megfelelő elhelyezésének biztosítása érdekében.

> [!WARNING] 
> Fontos, hogy az átjáró alhálózata mindig "GatewaySubnet" névvel legyen elnevezve.

Több virtuálisgép-alhálózatot és nem összefüggő címtartományokat is használhat. Ezeket a címtartományokat a virtuális **hálózat virtuális hálózati címterének** kell lefednie. Ezek lehetnek összesített formában. A virtuális gép alhálózatainak és az átjáró alhálózatainak pontos tartományainak listájában is szerepelhetnek.

Az alábbiakban összefoglaljuk a HANA nagy példányokhoz csatlakozó Azure virtuális hálózat fontos tényeit:

- A hana nagy példányok kezdeti üzembe helyezésekor el kell küldenie a **virtuális hálózati címterületet** a Microsoftnak. 
- A **virtuális hálózati címtér** lehet egy nagyobb tartomány, amely lefedi az Azure Virtuális gép alhálózati IP-címtartományának tartományait.
- Vagy beküldhet több tartományt, amelyek lefedik a virtuális hálózat alhálózati IP-címtartományainak és a virtuális hálózati átjáró IP-címtartományának különböző IP-címtartományait.
- A definiált **virtuális hálózati címtér** a BGP útválasztási propagáláshoz használatos.
- Az átjáró alhálózatának neve: **"GatewaySubnet"**.
- A címterület szűrőként a HANA nagy példány oldalán, hogy engedélyezze vagy letiltotta a forgalmat a HANA nagy példány egységek az Azure-ból. Az Azure virtuális hálózat BGP-útválasztási információinak és a HANA nagy példány oldalán szűrésre konfigurált IP-címtartományoknak egyezniük kell. Ellenkező esetben kapcsolódási problémák léphetnek fel.
- Vannak néhány részletet az átjáró alhálózat, amelyek később tárgyalt, a **szakaszban a virtuális hálózat csatlakoztatása hana nagy példány ExpressRoute című szakaszban.**



## <a name="different-ip-address-ranges-to-be-defined"></a>Különböző MEGHATÁROZandó IP-címtartományok 

A HANA nagy példányok üzembe helyezéséhez szükséges IP-címtartományok némelyike már bevezetésre került. De vannak több IP cím sor amit van is fontos. Nem kell az alábbi IP-címtartományok mindegyikét elküldeni a Microsoftnak. A kezdeti üzembe helyezési kérelem küldése előtt azonban meg kell határoznia őket:

- **Virtuális hálózati címterület**: A **virtuális hálózati címtér** az Azure virtuális hálózatokcímterület-paraméteréhez hozzárendelt IP-címtartományok. Ezek a hálózatok csatlakoznak az SAP HANA nagy példány környezethez. Azt javasoljuk, hogy ez a címterület paraméter többsoros érték. Az Azure virtuális gép alhálózati tartományából és az Azure-átjáró alhálózati tartományából kell állnia. Ez az alhálózati tartomány az előző ábrákon is látható volt. NEM lehet átfedésben a helyszíni vagy kiszolgálói IP-készlettel vagy az ER-P2P címtartományokkal. Hogyan szerezheti be ezeket az IP-címtartomány(oka)t? A vállalati hálózati csapatnak vagy szolgáltatónak egy vagy több olyan IP-címtartományt kell megadnia, amelyet nem használnak a hálózaton belül. Az Azure-beli virtuális gép alhálózata például 10.0.1.0/24, az Azure-átjáró alhálózatának alhálózata pedig 10.0.2.0/28.  Azt javasoljuk, hogy az Azure virtuális hálózati címterület meghatározása: 10.0.1.0/24 és 10.0.2.0/28. Bár a címtér értékei összesíthetők, javasoljuk, hogy párosítsa őket az alhálózati tartományokhoz. Így véletlenül elkerülheti a nem használt IP-címtartományok újbóli felhasználását a hálózat más részein található nagyobb címterekben. **A virtuális hálózati címtér egy IP-címtartomány. Be kell nyújtani a Microsoftnak, amikor kezdeti telepítést kér.**
- **Azure Virtuálishálózat alhálózati IP-címtartománya:** Ez az IP-címtartomány az Azure virtuális hálózati alhálózati paraméterhez rendelt. Ez a paraméter az Azure virtuális hálózatában található, és csatlakozik az SAP HANA nagy példány környezetéhez. Ez az IP-címtartomány az Azure-beli virtuális gépekhez ip-címek hozzárendelésére szolgál. Az IP-címek ebből a tartományból az SAP HANA nagy példánykiszolgáló(k) csatlakozhatnak. Szükség esetén több Azure virtuálisgép-alhálózatot is használhat. Javasoljuk, hogy minden Azure virtuális gép alhálózata /24 CIDR-blokkot javasoljon. Ennek a címtartománynak az Azure virtuális hálózati címterületén használt értékek részét kell, hogy legyen. Hogyan juthat el ehhez az IP-címtartományhoz? A vállalati hálózati csapatnak vagy szolgáltatónak olyan IP-címtartományt kell megadnia, amelyet nem használ a hálózaton belül.
- **Virtuális hálózati átjáró alhálózati IP-címtartománya:** A használni kívánt szolgáltatásoktól függően az ajánlott méret a következő:
   - Ultra-teljesítményű ExpressRoute-átjáró: /26 címblokk – szükséges a Nagykedési készlet II.
   - Együttműködés a VPN-nel és az ExpressRoute-tal egy nagy teljesítményű ExpressRoute virtuális hálózati átjáró használatával (vagy kisebb): /27 címblokk.
   - Minden más helyzet: /28 címblokk. Ennek a címtartománynak a "Virtuálishálózat-címtér" értékekben használt értékek egy részét kell lennie. Ennek a címtartománynak az Azure virtuális hálózati címtérértékeiben használt értékek egy részét kell, hogy legyen. Hogyan juthat el ehhez az IP-címtartományhoz? A vállalati hálózati csapatnak vagy szolgáltatónak olyan IP-címtartományt kell megadnia, amelyet jelenleg nem használ a hálózaton belül. 
- **Az ER-P2P-kapcsolat címtartománya:** Ez a tartomány az SAP HANA Large Instance ExpressRoute (ER) P2P-kapcsolat IP-tartománya. Ennek az IP-címtartománynak /29 CIDR IP-címtartománynak kell lennie. Ez a tartomány nem lehet átfedésben a helyszíni vagy más Azure IP-címtartományokkal. Ez az IP-címtartomány az EXPRESSRoute virtuális átjáróról az SAP HANA nagy példánykiszolgálóira való ER-kapcsolat beállítására szolgál. Hogyan juthat el ehhez az IP-címtartományhoz? A vállalati hálózati csapatnak vagy szolgáltatónak olyan IP-címtartományt kell megadnia, amelyet jelenleg nem használ a hálózaton belül. **Ez a tartomány egy IP-címtartomány. Be kell nyújtani a Microsoftnak, amikor kezdeti telepítést kér.**  
- **Kiszolgáló IP-készletének címtartománya:** Ez az IP-címtartomány az egyes IP-cím hana nagypéldány-kiszolgálókhoz rendelésére szolgál. Az ajánlott alhálózat mérete /24 CIDR blokk. Szükség esetén kisebb is lehet, mindössze 64 IP-címmel. Ebből a tartományból az első 30 IP-cím a Microsoft számára van fenntartva. Győződjön meg arról, hogy figyelembe ezt a tényt, amikor kiválasztja a méret a tartomány. Ez a tartomány nem lehet átfedésben a helyszíni vagy más Azure-IP-címekkel. Hogyan juthat el ehhez az IP-címtartományhoz? A vállalati hálózati csapatnak vagy szolgáltatónak olyan IP-címtartományt kell megadnia, amelyet jelenleg nem használ a hálózaton belül.  **Ez a tartomány egy IP-címtartomány, amelyet el kell küldeni a Microsoftnak, amikor kezdeti központi telepítést kér.**

Választható IP-címtartományok, amelyeket végül be kell küldeni a Microsoftnak:

- Ha úgy dönt, hogy [az ExpressRoute globális elérés](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) használatával engedélyezi a közvetlen útválasztást a helyszíni HANA nagypéldány-egységekközött, le kell foglalnia egy másik /29 IP-címtartományt. Előfordulhat, hogy ez a tartomány nem fedi át a korábban megadott többi IP-címtartományt.
- Ha úgy dönt, hogy [az ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) használatával engedélyezi a közvetlen útválasztást egy HANA nagypéldány-bérlőtől egy Azure-régióban egy másik Azure-régióban lévő hana nagy példány bérlőjébe, le kell foglalnia egy másik /29 IP-címtartományt. Előfordulhat, hogy ez a tartomány nem fedi át a korábban megadott többi IP-címtartományt.

Az ExpressRoute globális elérési útjáról és a HANA nagypéldányai körüli használatról a dokumentumokban talál további információt:

- [SAP HANA (nagy példányok) hálózati architektúrája](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)
- [Virtuális hálózat csatlakoztatása hana nagy példányokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)
 
Meg kell határoznia és meg kell terveznie a korábban ismertetett IP-címtartományokat. Azonban nem kell mindet továbbítania a Microsoftnak. A Microsoft nak elnevezendő IP-címtartományok a következők:

- Az Azure virtuális hálózati címterülete(i)
- Az ER-P2P-kapcsolat címtartománya
- Kiszolgáló IP-készletének címtartománya

Ha további virtuális hálózatokat ad hozzá, amelyeknek csatlakozniuk kell a HANA nagy példányaihoz, el kell küldenie az új Azure virtuális hálózati címteret, amelyet hozzá ad a Microsofthoz. 

Az alábbiakban egy példa a különböző tartományok és néhány példa tartományok, ahogy kell konfigurálni, és végül a Microsoft számára. Az Azure virtuális hálózati címtér értéke nem összesített az első példában. Azonban az első Azure VM alhálózati IP-címtartomány tartományaiból és a virtuális hálózati átjáró alhálózati IP-címtartományaiból van meghatározva. 

Az Azure virtuális hálózaton belül több virtuális virtuális hálózatot is használhat, amikor konfigurálja és elküldi a további virtuális gép alhálózatainak további IP-címtartományait az Azure virtuális hálózati címtér részeként.

![Az SAP HANA-ban az Azure-ban (nagy példányok) minimális üzembe helyezéshez szükséges IP-címtartományok](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

A grafika nem jeleníti meg az ExpressRoute globális elérési út opcionális használatához szükséges további IP-címtartomány(oka)t.

A Microsoftnak küldött adatokat is összesítheti. Ebben az esetben az Azure virtuális hálózat címterülete csak egy helyet foglal magában. A korábbi példából származó IP-címtartományok használatával az összesített virtuális hálózati címterület a következő képhez hasonlóan nézhet ki:

![Az SAP HANA-ban az Azure-on (nagy példányok) minimális üzembe helyezéshez szükséges IP-címtartományok második lehetősége](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

A példában az Azure virtuális hálózat címterét meghatározó két kisebb tartomány helyett egy nagyobb, 4096 IP-címre lefedő tartományáll. A címtér ilyen nagy meghatározása néhány meglehetősen nagy tartományt nem használ. Mivel a virtuális hálózati címterület értéke(i) a BGP-útvonal propagálásához használatos, a nem használt tartományok használata a helyszínen vagy a hálózat más részein útválasztási problémákat okozhat. A grafika nem jeleníti meg az ExpressRoute globális elérési út opcionális használatához szükséges további IP-címtartomány(oka)t.

Azt javasoljuk, hogy a címterületet szorosan igazítsa a ténylegesen használt alhálózati címterülethez. Szükség esetén a virtuális hálózaton állásidő nélkül később bármikor hozzáadhat új címtérértékeket.
 
> [!IMPORTANT] 
> Az ER-P2P, a kiszolgáló IP-készletében és az Azure virtuális hálózati címterében lévő minden IP-címtartomány **nem** fedheti át egymást vagy a hálózatban használt bármely más tartományt. Mindegyiknek diszkrétnek kell lennie. Ahogy a két korábbi grafika mutatja, nem lehetnek más tartomány alhálózatai sem. Ha átfedések fordulnak elő a tartományok között, előfordulhat, hogy az Azure virtuális hálózat nem csatlakozik az ExpressRoute-kapcsolaton.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Következő lépések a címtartományok definiálása után
Az IP-címtartományok definiálása után a következő dolgoknak kell történniük:

1. Küldje el az IP-címtartományokat az Azure virtuális hálózati címterületéhez, az ER-P2P-kapcsolathoz és a kiszolgáló IP-készletcímtartományához, a dokumentum elején felsorolt egyéb adatokkal együtt. Ezen a ponton is elkezdheti a virtuális hálózat és a virtuális gép alhálózatok létrehozását. 
2. Az ExpressRoute-áramkört a Microsoft az Azure-előfizetés és a HANA nagypéldány bélyegzője között hoz létre.
3. A Microsoft a nagy példány bélyegzőjével jön létre egy bérlői hálózat.
4. A Microsoft konfigurálja a hálózatépítést az SAP HANA azure-beli (nagy példányok) infrastruktúrájában, hogy fogadja az IP-címeket az Azure virtuális hálózati címteréből, amely a HANA nagy példányokkal kommunikál.
5. Attól függően, hogy az adott SAP HANA az Azure -on (nagy példányok) Termékváltozat, amely megvásárolta, a Microsoft hozzárendel egy számítási egységet a bérlői hálózatban. Azt is osztja és csatolja a tároló, és telepíti az operációs rendszer (SUSE vagy Red Hat Linux). Az ilyen egységek IP-címei kikerülnek a Microsoftnak megadott kiszolgálóIP-készlet címtartományából.

A telepítési folyamat végén a Microsoft a következő adatokat kézbesíti Önnek:
- Az Azure virtuális hálózat(ok) és az Azure virtuális hálózatokHANA nagy példányokhoz való csatlakoztatásához szükséges információk:
     - Engedélyezési kulcs(ok)
     - Expressroute-társazonosító
- Az ExpressRoute-kapcsolat és az Azure virtuális hálózat létrehozása után a HANA nagy példányok eléréséhez.

A HANA nagy példányok csatlakoztatásának sorrendjét is megtalálhatja az [SAP HANA azure-beli (nagy példányok) telepítődokumentumában.](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/) Az alábbi lépések közül sok az adott dokumentumban egy példatelepítésben jelenik meg. 

## <a name="next-steps"></a>További lépések

- Lásd: [Virtuális hálózat csatlakoztatása a HANA Large Instance ExpressRoute-hoz.](hana-connect-vnet-express-route.md)
