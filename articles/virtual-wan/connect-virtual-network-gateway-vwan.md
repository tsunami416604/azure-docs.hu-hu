---
title: Virtuális hálózati átjáró csatlakoztatása Azure Virtual WAN-hoz
description: Ez a cikk segít az Azure virtuális hálózati átjárójának egy Azure Virtual WAN VPN-átjáróhoz való csatlakoztatásában
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: 688183bc07aa14d5e5df182d7de0897cec93f0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066239"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>VPN-átjáró (virtuális hálózati átjáró) csatlakoztatása a Virtual WAN-hoz

Ez a cikk segít az Azure VPN-átjáróról (virtuális hálózati átjáró) egy Azure Virtual WAN (VPN-átjáró) való kapcsolat beállításával. A VPN-átjáróból (virtuális hálózati átjáróból) egy virtuális WAN-hoz (VPN-átjáró) való kapcsolat létrehozása hasonló a virtuális WAN-hoz való csatlakozás beállításához a vpn-helyekről.

Annak érdekében, hogy minimálisra csökkentsük a két funkció közötti esetleges zavart, az átjárót az általunk utalt funkció nevével fogjuk előzni. Például a VPN Gateway virtuális hálózati átjáró és a Virtual WAN VPN átjáró.

## <a name="before-you-begin"></a>Előkészületek

Mielőtt elkezdené, hozza létre a következő erőforrásokat:

Azure Virtual WAN

* [Hozzon létre egy virtuális WAN](virtual-wan-site-to-site-portal.md#openvwan).
* [Hozzon létre egy központot](virtual-wan-site-to-site-portal.md#hub). A virtuális hub tartalmazza a Virtuális WAN VPN átjárót.

Azure Virtual Network

* Virtuális hálózat létrehozása virtuális hálózati átjárók nélkül. Ellenőrizze, hogy a helyszíni hálózatok egyik alhálózata sem fedi-e át azokat a virtuális hálózatokat, amelyekhez csatlakozni szeretne. Virtuális hálózat létrehozásához az Azure Portalon tekintse meg a [gyorsútmutatót.](../virtual-network/quick-create-portal.md)

## <a name="1-create-an-azure-virtual-network-gateway"></a><a name="vnetgw"></a>1. Hozzon létre egy Azure virtuális hálózati átjáró

Hozzon létre egy VPN Átjáró virtuális hálózati átjárót a virtuális hálózat aktív-aktív módban a virtuális hálózat hoz létre. Az átjáró létrehozásakor használhatja a meglévő nyilvános IP-címeket az átjáró két példányához, vagy létrehozhat új nyilvános IP-címeket. Ezeket a nyilvános IP-ket a virtuális WAN-webhelyek beállításakor használhatja. Az aktív-aktív módról az [Aktív-aktív kapcsolatok konfigurálása](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway)című témakörben talál további információt.

### <a name="active-active-mode-setting"></a><a name="active-active"></a>Aktív-aktív mód beállítása

![aktív-aktív](./media/connect-virtual-network-gateway-vwan/active.png "aktív-aktív")

### <a name="bgp-setting"></a><a name="BGP"></a>BGP-beállítás

A BGP ASN nem lehet 65515. A 66515-öt az Azure Virtual WAN fogja használni.

![BGP](./media/connect-virtual-network-gateway-vwan/bgp.png "Bgp")

### <a name="public-ip-addresses"></a><a name="pip"></a>Nyilvános IP-címek

Az átjáró létrehozásakor keresse meg a **Tulajdonságok** lapot. A tulajdonságok és a konfigurációs beállítások hasonlóak lesznek a következő példához. Figyelje meg az átjáróhoz használt két nyilvános IP-címet.

![Tulajdonságok](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. Hozzon létre virtuális WAN VPN-helyeket

Virtuális WAN VPN-helyek létrehozásához keresse meg a virtuális WAN-t, és a **Kapcsolat**csoportban válassza a **VPN-helyek lehetőséget.** Ebben a szakaszban két virtuális WAN VPN-helyet hoz létre, amelyek megfelelnek az előző szakaszban létrehozott virtuális hálózati átjáróknak.

1. Válassza **a +Create site**lehetőséget.
2. A **VPN-helyek létrehozása** lapon írja be a következő értékeket:

   * **Régió** - (ugyanaz a régió, mint az Azure VPN Gateway virtuális hálózati átjáró)
   * **Eszközszállító** – Adja meg az eszköz gyártóját (bármilyen név)
   * **Privát címtér** - (Adjon meg egy értéket, vagy hagyja üresen, ha a BGP engedélyezve van)
   * **Border Gateway Protocol** - **(Enable** beállítás, ha az Azure VPN Gateway virtuális hálózati átjárója bgp-vel van engedélyezve)
   * **Csatlakozás hubokhoz** (Az előfeltételekben létrehozott hub kiválasztása a legördülő menüből)
3. A **Hivatkozások**csoportban adja meg a következő értékeket:

   * **Szolgáltató neve** – Hivatkozás név és szolgáltatónév megadása (bármilyen név)
   * **Sebesség** - Sebesség (bármilyen szám)
   * **IP-cím** – Adja meg az IP-címet (megegyezik a (VPN-átjáró) virtuális hálózati átjáró tulajdonságai alatt látható első nyilvános IP-címmel)
   * **BGP-cím** és **ASN** - BGP-cím és ASN. Ezeknek meg kell egyeznie a BGP-társ IP-címének és az [1.](#vnetgw)
4. Tekintse át, és válassza **a Megerősítés** lehetőséget a webhely létrehozásához.
5. Ismételje meg az előző lépéseket a második hely létrehozásához, amely megfelel a VPN-átjáró virtuális hálózati átjáró jának második példányának. Meg fogja tartani ugyanazokat a beállításokat, kivéve a második nyilvános IP-címet és a második BGP-társ IP-címét a VPN Gateway konfigurációból.
6. Most már két hely sikeresen kiépített, és folytassa a következő szakaszban a konfigurációs fájlok letöltéséhez.

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. Töltse le a VPN konfigurációs fájlokat

Ebben a szakaszban az előző szakaszban létrehozott webhelyek VPN-konfigurációs fájlját tölti le.

1. A Virtual WAN **VPN-helyek** lap tetején válassza a **Webhely**lehetőséget, majd válassza **a Helyek közötti VPN-konfiguráció letöltése lehetőséget.** Az Azure létrehoz egy konfigurációs fájlt a beállításokkal.

   ![konfigurációs fájl letöltése](./media/connect-virtual-network-gateway-vwan/download.png "letöltés")
2. Töltse le és nyissa meg a konfigurációs fájlt.
3. Ismételje meg ezeket a lépéseket a második helyen. Miután mindkét konfigurációs fájlt megnyitotta, folytathatja a következő szakaszra.

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. A helyi hálózati átjárók létrehozása

Ebben a szakaszban két Azure VPN-átjáró helyi hálózati átjárók. Az előző lépés konfigurációs fájljai tartalmazzák az átjáró konfigurációs beállításait. Ezekkel a beállításokkal hozhatja létre és konfigurálhatja az Azure VPN Gateway helyi hálózati átjárók.

1. Ezekkel a beállításokkal hozza létre a helyi hálózati átjárót. A VPN-átjáró helyi hálózati átjárójának létrehozásáról a VPN-átjáró létrehozása című, [Helyi hálózati átjáró létrehozása](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway)című cikkben olvashat.

   * **IP-cím** – Használja a konfigurációs fájlból származó *átjárókonfigurációhoz* megjelenített Instance0 IP-címet.
   * **BGP** – Ha a kapcsolat bgp-n túl van, válassza **a BGP-beállítások konfigurálása** lehetőséget, és adja meg a '65515' ASN-t. Adja meg a BGP-társ IP-címét. Használja a "Instance0 BgpPeeringAddresses" az *átjárókonfiguráció* a konfigurációs fájlból.
   * **Az előfizetés, az erőforráscsoport és** a hely megegyezik a Virtuális WAN-központ éval.
2. Tekintse át és hozza létre a helyi hálózati átjárót. A helyi hálózati átjáró nak ehhez a példához hasonlóan kell kinéznie.

   ![konfigurációs fájl letöltése](./media/connect-virtual-network-gateway-vwan/lng1.png "instance0")
3. Ismételje meg ezeket a lépéseket egy másik helyi hálózati átjáró létrehozásához, de ezúttal használja a "Instance1" értékeket a konfigurációs fájl "Instance0" értékei helyett.

   ![konfigurációs fájl letöltése](./media/connect-virtual-network-gateway-vwan/lng2.png "instance1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. Kapcsolatok létrehozása

Ebben a szakaszban kapcsolatot hoz létre a VPN-átjáró helyi hálózati átjárói és a virtuális hálózati átjáró között. A VPN-átjárókapcsolat létrehozásának lépéseit a [Kapcsolat konfigurálása](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection)című témakörben tismerteti.

1. A portálon keresse meg a virtuális hálózati átjárót, és kattintson **a Kapcsolatok gombra.** A Kapcsolatok lapon kattintson a **+Hozzáadás** elemre a **Kapcsolat hozzáadása** lap megnyitásához.
2. A **Kapcsolat hozzáadása** lapon adja meg a következő értékeket a kapcsolathoz:

   * **Név:** Nevezze el a kapcsolatot.
   * **Kapcsolat típusa:** **Helyről webhelyre (IPSec)** kiválasztása
   * **Virtuális hálózati átjáró:** Az értéke rögzített, mivel erről az átjáróról csatlakozik.
   * **Helyi hálózati átjáró:** Ez a kapcsolat összeköti a virtuális hálózati átjárót a helyi hálózati átjáróval. Válassza ki a korábban létrehozott helyi hálózati átjárók egyikét.
   * **Megosztott kulcs:** Adja meg a megosztott kulcsot.
   * **IKE protokoll:** Válassza ki az IKE protokollt.
   * **BGP:** Válassza **a BGP engedélyezése lehetőséget,** ha a kapcsolat bgp-n túl van.
3. A kapcsolat létrehozásához kattintson az **OK** gombra.
4. A kapcsolatot a virtuális hálózat átjárójának **Kapcsolatok** lapján tekintheti meg.

   ![Kapcsolat](./media/connect-virtual-network-gateway-vwan/connect.png "kapcsolat")
5. Ismételje meg az előző lépéseket a második kapcsolat létrehozásához. A második kapcsolathoz jelölje ki a létrehozott másik helyi hálózati átjárót.

## <a name="6-test-connections"></a><a name="test"></a>6. Vizsgálati csatlakozások

A kapcsolatot két virtuális gép létrehozásával tesztelheti, amelyek közül az egyik a VPN-átjáró virtuális hálózati átjárója, a másik a virtuális wan virtuális hálózatában található, majd pingelheti a két virtuális gépet.

1. Hozzon létre egy virtuális gépet a virtuális hálózatban (Test1-VNet) az Azure VPN Gateway (Test1-VNG) számára. Ne hozza létre a virtuális gépet a GatewaySubnet.
2. Hozzon létre egy másik virtuális hálózatot a virtuális WAN-hoz való csatlakozáshoz. Hozzon létre egy virtuális gépet a virtuális hálózat alhálózatában. Ez a virtuális hálózat nem tartalmazhat virtuális hálózati átjárókat. A helyek közötti kapcsolatról szóló cikk Ben található [PowerShell-lépések](virtual-wan-site-to-site-portal.md#vnet) használatával gyorsan létrehozhat virtuális hálózatot. Ügyeljen arra, hogy módosítsa az értékeket, mielőtt futtatja a parancsmagokat.
3. Csatlakoztassa a virtuális hálózatot a virtuális WAN-elosztóhoz. A virtuális WAN lapján válassza a **Virtuális hálózati kapcsolatok**lehetőséget, majd a **+Kapcsolat hozzáadása**lehetőséget. A **Kapcsolat hozzáadása** lapon töltse ki a következő mezőket:

    * **Kapcsolat neve** – Nevezze el a kapcsolatot.
    * **Elosztók** – Válassza ki azt az elosztót, amelyet a kapcsolattal társítani kíván.
    * **Előfizetés** – Ellenőrizze az előfizetést.
    * **Virtuális hálózat** – Válassza ki azt a virtuális hálózatot, amelyet az elosztóhoz csatlakoztatni kíván. A virtuális hálózat nem rendelkezhet már meglévő virtuális hálózati átjáróval.
4. A virtuális hálózati kapcsolat létrehozásához kattintson az **OK** gombra.
5. A virtuális gépek között most már be van állítva a kapcsolat. Képesnek kell lennie arra, hogy pingelje az egyik virtuális gép a másik, kivéve, ha vannak olyan tűzfalak vagy más házirendek blokkolja a kommunikációt.

## <a name="next-steps"></a>További lépések

Az egyéni IPsec-házirend konfigurálásának lépéseit a [Virtual WAN egyéni IPsec-házirendének konfigurálása című témakörben található.](virtual-wan-custom-ipsec-portal.md)
A Virtual WAN szolgáltatásról további információt az [Azure Virtual WAN és](virtual-wan-about.md) az Azure Virtual WAN – gyakori kérdések című [témakörben talál.](virtual-wan-faq.md)