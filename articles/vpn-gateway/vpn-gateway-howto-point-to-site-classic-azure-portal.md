---
title: 'Számítógép csatlakoztatása virtuális hálózathoz pont–hely kapcsolat és tanúsítványalapú hitelesítés használatával: klasszikus Azure Portal | Microsoft Docs'
description: Klasszikus pont–hely VPN-átjárókapcsolat létrehozása az Azure Portalon.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: 5d80cb2f2ed844126d1e9311151e6c53fcb11840
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244575"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Pont-hely kapcsolat konfigurálása tanúsítványhitelesítéssel (klasszikus)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Ez a cikk bemutatja, hogyan hozhat létre virtuális hálózatot pont-hely kapcsolattal. Ezt a virtuális hálózatot a klasszikus üzembe helyezési modell az Azure Portalhasználatával hozza létre. A konfiguráció önaláírt vagy hitelesítésszolgáltató által kibocsátott tanúsítványokat használ a kapcsolódó ügyfelek hitelesítéséhez. Ezt a konfigurációt egy másik központi telepítési eszközzel vagy modellel is létrehozhatja az alábbi cikkekben ismertetett beállításokkal:

> [!div class="op_single_selector"]
> * [Azure-portál](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Powershell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

A Point-to-Site (P2S) VPN-átjáró segítségével biztonságos kapcsolatot hozhat létre a virtuális hálózathoz egy adott ügyfélszámítógépről. A pont-hely VPN-kapcsolatok akkor hasznosak, ha távoli helyről szeretne csatlakozni a virtuális hálózathoz. Ha csak néhány ügyfél, amelynek csatlakoznia kell egy virtuális hálózathoz, a P2S VPN hasznos megoldás a helyek közötti VPN helyett. A pont–hely VPN-kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani.

> [!IMPORTANT]
> A klasszikus üzemi modell kizárólag a Windows VPN-ügyfeleket támogatja, és a Secure Socket Tunneling Protocol (SSTP) SSL-alapú VPN-protokollt használja. A nem Windows VPN-ügyfelek támogatásához létre kell hoznia a virtuális hálózatot az Erőforrás-kezelő telepítési modelljével. A Resource Manager-alapú üzemi modell az SSTP mellett az IKEv2 VPN protokollt is támogatja. További információk: [A pont–hely kapcsolatok](point-to-site-about.md).
>
>

![Pont–hely diagram](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="prerequisites"></a>Előfeltételek

A pont-hely tanúsítványhitelesítési kapcsolatokhoz a következő előfeltételek revansok szükségesek:

* Egy dinamikus VPN-átjáró.
* A nyilvános kulcs (.cer fájl) egy főtanúsítványhoz, amely az Azure-ba van feltöltve. Ez a kulcs megbízható tanúsítványnak minősül, és hitelesítésre szolgál.
* A főtanúsítványból létrejön egy ügyféltanúsítvány, majd települ az egyes csatlakozó ügyfélszámítógépekre. A rendszer ezt a tanúsítványt használja ügyfélhitelesítéshez.
* Minden csatlakozó ügyfélszámítógépen létre kell hozni és telepíteni kell egy VPN-ügyfélkonfigurációs csomagot. Az ügyfélkonfigurációs csomag konfigurálja a natív VPN-ügyfél, amely már az operációs rendszeren a szükséges információkat, hogy csatlakozzon a virtuális hálózathoz.

A pont-hely kapcsolatokhoz nincs szükség VPN-eszközre vagy helyszíni nyilvános IP-címre. A VPN-kapcsolat kiépítése SSTP (Secure Socket Tunneling Protocol) használatával történik. A kiszolgálói oldalon az SSTP 1.0, 1.1 és 1.2 verziója támogatott. Az ügyfél dönti el, hogy melyik verziót használja. Windows 8.1 és újabb kiadások esetén az SSTP alapértelmezés szerint az 1.2 verziót használja. 

A pont-hely közötti kapcsolatokról a [Pont–helyek – gyakori kérdések című témakörben](#point-to-site-faq)talál további információt.

### <a name="example-settings"></a>Példabeállítások

Az alábbi értékek segítségével hozzon létre egy tesztkörnyezetet, vagy tekintse meg ezeket az értékeket, hogy jobban megértse a jelen cikkben szereplő példákat:

- **Virtuális hálózati (klasszikus) beállítások létrehozása**
   - **Név**: Írja be *a vnet1 nevet.*
   - **Címterület**: Írja be a *192.168.0.0/16*értéket. Ebben a példában csak egy címteret használunk. A virtuális hálózatához több címteret is használhat, ahogy ez az alábbi diagramon is látható.
   - **Alhálózat neve**: Enter *FrontEnd*.
   - **Alhálózati címtartomány**: Írja be a *192.168.1.0/24*értéket.
   - **Előfizetés**: Válasszon egy előfizetést az elérhető előfizetések listájából.
   - **Erőforráscsoport**: Adja meg *a TestRG*értéket. Válassza **az Új létrehozása**lehetőséget, ha az erőforráscsoport nem létezik.
   - **Hely**: Válassza ki **az USA keleti részét** a listából.

  - **VPN-kapcsolat beállításai**
    - **Kapcsolat típusa**: Válassza **a Pont-hely lehetőséget**.
    - **Ügyfélcímtér**: Írja be *a 172.16.201.0/24*értéket. A virtuális hálózathoz ezzel a point-to-site kapcsolattal összekötő VPN-ügyfelek IP-címet kapnak a megadott készlettől.

- **Átjáró konfigurációs alhálózati beállításai**
   - **Név**: Automatikus kitöltés *a GatewaySubnet*segítségével.
   - **Címtartomány**: Adja meg *a 192.168.200.0/24*értéket. 

- **Átjáró konfigurációs beállításai**:
   - **Méret**: Válassza ki a használni kívánt átjáró termékváltozatot.
   - **Útválasztási típus**: Válassza a **Dinamikus**lehetőséget.

## <a name="create-a-virtual-network-and-a-vpn-gateway"></a>Virtuális hálózat és VPN-átjáró létrehozása

Mielőtt elkezdené, ellenőrizze, hogy rendelkezik-e Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>1. rész: Virtuális hálózat létrehozása

Ha még nem rendelkezik virtuális hálózattal (VNet), hozzon létre egyet. A képernyőképek csak példaként szolgálnak. Ne felejtse el ezeket az értékeket a saját értékeire cserélni. Az alábbi lépésekkel hozhat létre virtuális hálózatokat az Azure portállal:

1. Az [Azure Portal](https://portal.azure.com) menüben vagy a **kezdőlapon** válassza az **Erőforrás létrehozása**lehetőséget. Megnyílik **az Új** lap.

2. A **Keresés a piactéren** mezőbe írja be a *virtuális hálózatot,* és válassza a **Virtuális hálózat** lehetőséget a visszaadott listából. Megnyílik **a Virtuális hálózat** lap.

3. A **Telepítési modell kiválasztása** listában válassza a **Klasszikus**lehetőséget, majd a **Létrehozás lehetőséget.** Megnyílik **a Virtuális hálózat létrehozása** lap.

4. A **Virtuális hálózat létrehozása** lapon konfigurálja a VNet beállításait. Ezen a lapon adhatja hozzá az első címterét és egy önálló alhálózati címtartományt. A virtuális hálózat létrehozása után visszaléphet, és további alhálózatokat és címtereket vehet fel.

   ![Virtuális hálózat létrehozása lap](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)

5. Válassza ki a használni kívánt **előfizetést** a legördülő listából.

6. Jelöljön ki egy meglévő **erőforráscsoportot**. Vagy hozzon létre egy új erőforráscsoportot az **Új létrehozása és** egy név megadásával. Ha új erőforráscsoportot hoz létre, nevezze el az erőforráscsoportot a tervezett konfigurációs értékek nek megfelelően. Az erőforráscsoportokról az [Azure Resource Manager áttekintése című témakörben olvashat bővebben.](../azure-resource-manager/management/overview.md#resource-groups)

7. Válassza ki a **hely** a virtuális hálózathoz. Ez a beállítás határozza meg a virtuális hálózatra üzembe helyezett erőforrások földrajzi helyét.

8. Válassza a **Létrehozás** lehetőséget a virtuális hálózat létrehozásához. Az **Értesítések** lapon megjelenik egy **folyamatban lévő központi telepítés** üzenet.

8. A virtuális hálózat létrehozása után az **Értesítések** lapon a Telepítés szolgáltatásra vonatkozó üzenet **sikeresen sikerült.** Válassza **a Rögzítés az irányítópultra** lehetőséget, ha egyszerűen szeretné megtalálni a virtuális hálózatot az irányítópulton. 

10. Adjon meg egy DNS-kiszolgálót (nem kötelező). Miután létrehozta a virtuális hálózatot, a névfeloldás érdekében hozzáadhatja egy DNS-kiszolgáló IP-címét. A DNS-kiszolgáló megadott IP-címének egy olyan DNS-kiszolgáló címének kell lennie, amely fel tudja oldani a virtuális hálózatban található erőforrások nevét.

    DNS-kiszolgáló hozzáadásához válassza ki **a DNS-kiszolgálókat** a virtuális hálózat lapján. Ezután írja be a használni kívánt DNS-kiszolgáló IP-címét, és válassza a **Mentés gombot.**

### <a name="part-2-create-a-gateway-subnet-and-a-dynamic-routing-gateway"></a>2. rész: Átjáró-alhálózat és dinamikus útválasztási átjáró létrehozása

Ebben a lépésben hozzon létre egy átjáró alhálózatot és egy dinamikus útválasztási átjárót. A klasszikus üzembe helyezési modell Azure portalon hozza létre az átjáró alhálózatés az átjáró ugyanazon konfigurációs lapokon keresztül. Csak az átjáró-alhálózatot használja az átjárószolgáltatásokhoz. Semmit ne helyezzen üzembe közvetlenül az átjáróalhálózaton (például virtuális gépeket vagy más szolgáltatásokat).

1. Az Azure Portalon nyissa meg azt a virtuális hálózatot, amelyhez átjárót szeretne létrehozni.

2. A virtuális hálózat lapján válassza az **Áttekintés**lehetőséget, a **VPN-kapcsolatok** csoportban pedig az **Átjáró**lehetőséget.

   ![Jelölje be, ha átjárót szeretne létrehozni](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. A **Új VPN-kapcsolat** lapon válassza a **Pont–hely** beállítást.

   ![Pont–hely kapcsolat típusa](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Az **Ügyfélcímtér**hez adja hozzá azt az IP-címtartományt, amelyről a VPN-ügyfelek csatlakozáskor IP-címet kapnak. Használjon olyan privát IP-címtartományt, amely nem fedi át a helyszíni helyet, ahonnan csatlakozik, vagy a virtuális hálózattal, amelyhez csatlakozik. Az automatikus kitöltési tartomány felülírható a használni kívánt privát IP-címtartománysal. Ez a példa az automatikus kitöltési tartományt mutatja. 

   ![Ügyfélcímtér](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Válassza **az Átjáró létrehozása azonnal**lehetőséget, majd a Választható **átjárókonfiguráció** lehetőséget az **Átjáró konfigurációs** lapjának megnyitásához.

   ![Választható átjárókonfiguráció kiválasztása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)

6. Az **átjáró konfigurációs** lapján válassza **az Alhálózat** lehetőséget az átjáró alhálózatának hozzáadásához. A /29-es méretű átjáró-alhálózat is létrehozható. Azt javasoljuk azonban, hogy hozzon létre egy nagyobb alhálózatot, amely több címet tartalmaz legalább /28 vagy /27 kiválasztásával. Ha így tesz, elegendő cím lesz a lehetséges további konfigurációk elhelyezésére, amelyeket a jövőben esetleg szeretne. Amikor átjáró-alhálózatokkal dolgozik, kerülje a hálózati biztonsági csoportok (NSG) társítását az átjáró-alhálózathoz. Ha egy hálózati biztonsági csoportot társít ehhez az alhálózathoz, előfordulhat, hogy a VPN-átjáró nem a várt módon működik. A beállítás mentéséhez válassza az **OK gombot.**

   ![Átjáró-alhálózat hozzáadása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Válassza ki az átjáró **méretét**. A méret a virtuális hálózati átjáró termékváltozata. Az Azure Portalon az alapértelmezett termékváltozat **az alapértelmezett.** Az átjárótermékkel kapcsolatos további tudnivalókért olvassa el [a VPN-átjáró beállításainak beolvasása című témakört.](vpn-gateway-about-vpn-gateway-settings.md#gwsku)

   ![Az átjáró mérete](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Válassza ki az átjáró **útválasztási típusát**. A pont–hely konfigurációhoz a **Dynamic** (Dinamikus) útválasztási típusra van szükség. Ha befejezte a lap konfigurálását, válassza az **OK** gombot.

   ![Az útválasztási típus konfigurálása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)

9. Az **Új VPN-kapcsolat** lapon válassza az **OK** gombot a lap alján a virtuális hálózati átjáró létrehozásának megkezdéséhez. A VPN-átjáró befejezése akár 45 percet is igénybe vehet, a kiválasztott átjáró termékváltozattól függően.
 
## <a name="create-certificates"></a><a name="generatecerts"></a>Tanúsítványok létrehozása

Az Azure tanúsítványokat használ a VPN-ügyfelek hitelesítéséhez a pont-hely VPN-ek számára. A főtanúsítvány nyilvánoskulcs-adatait feltölti az Azure-ba. A nyilvános kulcs ezután *megbízhatónak*minősül. Az ügyféltanúsítványokat a megbízható főtanúsítványból kell létrehozni, majd telepíteni kell a Tanúsítványok aktuális felhasználó\Személyes\Tanúsítványok tanúsítványtároló minden ügyfélszámítógépére. A tanúsítvány az ügyfél hitelesítésére szolgál, amikor csatlakozik a virtuális hálózathoz. 

Ha önaláírt tanúsítványokat használ, azokat meghatározott paraméterek kel kell létrehozni. Önaláírt tanúsítványt a [PowerShell és](vpn-gateway-certificates-point-to-site.md)a Windows 10 vagy a [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)utasításainak megfelelően hozhat létre. Fontos, hogy kövesse az alábbi utasításokat, ha önaláírt főtanúsítványokat használ, és ügyféltanúsítványokat hoz létre az önaláírt főtanúsítványból. Ellenkező esetben a létrehozott tanúsítványok nem lesznek kompatibilisek a P2S-kapcsolatokkal, és csatlakozási hiba lép fel.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>A legfelső szintű tanúsítvány nyilvános kulcsának (.cer) beszerzése

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Ügyféltanúsítvány létrehozása

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>A főtanúsítvány .cer fájljának feltöltése

Az átjáró létrehozása után töltse fel a .cer fájlt (amely tartalmazza a nyilvános kulcs adatait) egy megbízható főtanúsítványhoz az Azure-kiszolgálóra. Ne töltse fel a főtanúsítvány személyes kulcsát. A tanúsítvány feltöltése után az Azure arra használja, hogy hitelesítse azokat az ügyfeleket, amelyek a megbízható főtanúsítványból létrehozott ügyféltanúsítványt telepítettek. Később további megbízható legfelső szintű tanúsítványfájlokat (legfeljebb 20) tölthet fel, ha szükséges.  

1. A virtuális hálózat **lapjának VPN-kapcsolatok** szakaszán jelölje ki az ügyfelek grafikáját a **pont-hely VPN-kapcsolat** lap megnyitásához.

   ![Ügyfelek](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. A **Pont-hely VPN-kapcsolat** lapon válassza a **Tanúsítvány kezelése lehetőséget** a **Tanúsítványok** lap megnyitásához.

   ![Tanúsítványok lap](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

1. A **Tanúsítványok** lapon válassza a **Feltöltés** lehetőséget a **Tanúsítvány feltöltése** lap megnyitásához.

    ![Tanúsítványok feltöltése lap](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)

4. Jelölje ki a .cer fájl tallózásához. Jelölje ki a fájlt, majd kattintson **az OK gombra.** A feltöltött tanúsítvány megjelenik a **Tanúsítványok** lapon.

   ![Tanúsítvány feltöltése](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)


## <a name="configure-the-client"></a>Az ügyfél konfigurálása

Ha egy virtuális hálózathoz szeretne csatlakozni egy pont-hely VPN használatával, minden ügyfélnek telepítenie kell egy csomagot a natív Windows VPN-ügyfél konfigurálásához. A konfigurációs csomag konfigurálja a natív Windows VPN-ügyfelet a virtuális hálózathoz való csatlakozáshoz szükséges beállításokkal.

Használhatja a VPN-ügyfél azonos konfigurációs csomagját minden ügyfélszámítógépen, feltéve, hogy a verzió megfelel az ügyfél architektúrájának. A támogatott ügyféloperációs rendszerek listáját a [Pont–helyek kapcsolatok – gyakori kérdések című témakörben talál.](#point-to-site-faq)

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>VPN-ügyfélkonfigurációs csomag létrehozása és telepítése

1. Az Azure Portalon a virtuális hálózat **áttekintése** lapon a **VPN-kapcsolatokban**válassza ki az ügyfélgrafikát a **pont-hely VPN-kapcsolat lap megnyitásához.**

2. A **pont-hely VPN-kapcsolat** lapon válassza ki azt a letöltési csomagot, amely megfelel annak az ügyféloperációs rendszernek, amelyen telepítve van:

   * 64 bites ügyfelek esetén válassza a **VPN Client (64-bit)** (VPN-ügyfél (64 bit)) lehetőséget.
   * 32 bites ügyfelek esetén válassza a **VPN Client (32-bit)** (VPN-ügyfél (32 bit)) lehetőséget.

   ![A VPN-ügyfél konfigurációs csomagjának letöltése](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)

3. A csomag létrehozása után töltse le, majd telepítse az ügyfélszámítógépre. Ha megjelenik egy SmartScreen előugró ablak, válassza a **További információ**lehetőséget, majd a **Futtatás lehetőséget.** A csomagot mentheti is, így más ügyfélszámítógépekre is telepítheti.

### <a name="install-a-client-certificate"></a>Ügyféltanúsítvány telepítése

Ha az ügyféltanúsítványok létrehozásához használtól eltérő ügyfélszámítógépről szeretne P2S-kapcsolatot létrehozni, telepítsen egy ügyféltanúsítványt. Az ügyféltanúsítvány telepítésekor szüksége van az ügyféltanúsítvány exportálásakor létrehozott jelszóra. A tanúsítványt általában csak duplán kattintva telepítheti. További információkért lásd az [exportált ügyféltanúsítványok telepítését](vpn-gateway-certificates-point-to-site.md#install) ismertető cikket.


## <a name="connect-to-your-vnet"></a>Csatlakozás a virtuális hálózathoz

>[!NOTE]
>Rendszergazdai jogosultsággal kell rendelkeznie azon az ügyfélszámítógépen, ahonnan csatlakozik.
>
>

1. A virtuális hálózathoz való csatlakozáshoz az ügyfélszámítógépen nyissa meg a **VPN-kapcsolatokat** az Azure Portalon, és keresse meg a létrehozott VPN-kapcsolatot. A VPN-kapcsolat neve megegyezik a virtuális hálózat nevével. Kattintson a **Csatlakozás** gombra. Ha megjelenik egy előugró üzenet a tanúsítvánnyal kapcsolatban, válassza a Továbbra emelt szintű jogosultságok használatának **folytatása** lehetőséget.

2. A **Kapcsolat** állapota lapon válassza a **Csatlakozás** lehetőséget a kapcsolat elindításához. Ha a **Tanúsítvány kiválasztása** képernyő látható, ellenőrizze, hogy a megjelenített ügyféltanúsítvány megfelelő-e. Ha nem, válassza ki a megfelelő tanúsítványt a legördülő listából, majd kattintson az **OK gombra.**

3. Ha a kapcsolat sikeres, megjelenik egy **Csatlakoztatott** értesítés.


### <a name="troubleshooting-p2s-connections"></a>Pont–hely kapcsolatok hibaelhárítása

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>A VPN-kapcsolat ellenőrzése

1. Ellenőrizze, hogy a VPN-kapcsolat aktív-e. Nyisson meg egy rendszergazda jogú parancssort az ügyfélszámítógépen, és futtassa **az ipconfig/all parancsot.**
2. Tekintse meg az eredményeket. A kapott IP-cím a virtuális hálózat létrehozásakor megadott pont–hely kapcsolati címtartományba tartozó valamelyik cím. Az eredmények a következő példához hasonlóak:

   ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
   ```

## <a name="connect-to-a-virtual-machine"></a>Csatlakozás virtuális géphez

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add-or-remove-trusted-root-certificates"></a>Megbízható főtanúsítványok hozzáadása vagy eltávolítása

A megbízható főtanúsítványokat felveheti vagy el is távolíthatja az Azure-ban. A főtanúsítvány eltávolításakor az adott gyökérből létrehozott tanúsítvánnyal rendelkező ügyfelek már nem hitelesíthetik és nem csatlakozhatnak. Ahhoz, hogy ezek az ügyfelek hitelesítsék magukat, és újra csatlakozzanak, telepítenie kell egy új ügyféltanúsítványt, amely et az Azure megbízható n.

### <a name="to-add-a-trusted-root-certificate"></a>Megbízható főtanúsítvány hozzáadása

Az Azure-ra legfeljebb 20 megbízható főtanúsítványt tölthet fel .cer fájl formájában. További információt a Főtanúsítvány .cer fájl feltöltése című témakörben talál.

### <a name="to-remove-a-trusted-root-certificate"></a>Megbízható főtanúsítvány eltávolítása

1. A virtuális hálózat **lapjának VPN-kapcsolatok** szakaszán jelölje ki az ügyfelek grafikáját a **pont-hely VPN-kapcsolat** lap megnyitásához.

   ![Ügyfelek](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. A **Pont-hely VPN-kapcsolat** lapon válassza a **Tanúsítvány kezelése lehetőséget** a **Tanúsítványok** lap megnyitásához.

   ![Tanúsítványok lap](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

3. A **Tanúsítványok** lapon jelölje ki az eltávolítani kívánt tanúsítvány melletti három pontot, majd kattintson a **Törlés gombra.**

   ![Főtanúsítvány törlése](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)

## <a name="revoke-a-client-certificate"></a>Ügyféltanúsítvány visszavonása

Szükség esetén visszavonhatja az ügyféltanúsítványt. A visszavont tanúsítványok listájával az egyes ügyféltanúsítványok alapján, szelektíven tagadhatja meg a pont–hely kapcsolódás lehetőségét. Ez a módszer eltér a megbízható főtanúsítvány eltávolításától. Ha töröl egy .cer formátumú megbízható főtanúsítványt az Azure-ból, azzal megvonja a hozzáférést minden olyan ügyféltanúsítványtól, amelyet a visszavont főtanúsítvánnyal hoztak létre/írtak alá. A főtanúsítvány helyett az ügyféltanúsítvány visszavonásával a főtanúsítványból létrehozott többi tanúsítvány továbbra is használható a pont–hely kapcsolat hitelesítésére.

A szokásos gyakorlat az, hogy a főtanúsítvánnyal kezelik a hozzáférést a munkacsoport vagy a szervezet szintjén, az egyes felhasználókra vonatkozó részletesebb szabályozást pedig visszavont ügyféltanúsítványokkal oldják meg.

### <a name="to-revoke-a-client-certificate"></a>Ügyféltanúsítvány visszavonása

Az ügyféltanúsítványok visszavonásához vegye fel az ujjlenyomatot a visszavont tanúsítványok listájára.

1. Kérje le az ügyféltanúsítvány ujjlenyomatát. További információ: [Hogyan: A tanúsítvány ujjlenyomatának beolvasása.](https://msdn.microsoft.com/library/ms734695.aspx)
2. Másolja az információt egy szövegszerkesztőbe, és távolítsa el a szóközöket, hogy az folytonos karakterlánc legyen.
3. Ugrás a klasszikus virtuális hálózatra. Válassza **a Pont-hely VPN-kapcsolat**lehetőséget, majd a **Tanúsítványok** lap megnyitásához válassza a Tanúsítvány **kezelés lehetőséget.**
4. A **Visszavonáslista** megnyitásához válassza a **Visszavonási lista** lehetőséget. 
5. A **Tanúsítvány hozzáadása** lehetőséget választva nyissa meg a Tanúsítvány hozzáadása **visszavonáshoz listalapot.**
6. Az **Ujjlenyomat mezőbe**illessze be a tanúsítvány ujjlenyomatát egy folytonos szövegsorba szóközök nélkül. A befejezéshez válassza az **OK gombot.**

A frissítés befejezését követően a tanúsítvány már nem használható csatlakozáshoz. Azok az ügyfelek, akik ezzel a tanúsítvánnyal próbálnak csatlakozni, üzenetet kapnak arról, hogy a tanúsítvány már nem érvényes.

## <a name="point-to-site-faq"></a>Pont–hely kapcsolatok – gyakori kérdések

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>További lépések

- A kapcsolat létrejötte után virtuális gépeket adhat hozzá a virtuális hálózatokhoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/). 

- Ha többet szeretne megtudni a hálózati és linuxos virtuális gépekről, olvassa el az [Azure és a Linux virtuális gépek hálózatának áttekintése című témakört.](../virtual-machines/linux/network-overview.md)

- A pont–hely hibaelhárítási információiért tekintse át az [Azure pont–hely kapcsolatok hibaelhárításával](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) foglalkozó cikket.
