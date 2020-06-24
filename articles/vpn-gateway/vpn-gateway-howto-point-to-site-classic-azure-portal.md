---
title: 'Számítógép csatlakoztatása virtuális hálózathoz pont–hely kapcsolat és tanúsítványalapú hitelesítés használatával: klasszikus Azure Portal | Microsoft Docs'
description: Klasszikus pont–hely VPN-átjárókapcsolat létrehozása az Azure Portalon.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: f68631771b8f86d995108112b1243ab38bf826bc
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2020
ms.locfileid: "84984797"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Pont – hely kapcsolat konfigurálása tanúsítványalapú hitelesítés használatával (klasszikus)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Ez a cikk bemutatja, hogyan hozhat létre pont – hely kapcsolattal rendelkező VNet. Ezt a vnet a klasszikus üzemi modellel hozza létre a Azure Portal használatával. A konfiguráció önaláírt vagy hitelesítésszolgáltató által kibocsátott tanúsítványokat használ a kapcsolódó ügyfelek hitelesítéséhez. Ezt a konfigurációt egy másik központi telepítési eszközzel vagy modellel is létrehozhatja az alábbi cikkekben ismertetett beállítások használatával:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

A pont – hely (P2S) VPN-átjáró használatával biztonságos kapcsolat hozható létre a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont – hely VPN-kapcsolat akkor hasznos, ha távoli helyről szeretne csatlakozni a VNet. Ha csak néhány ügyfelet szeretne egy VNet csatlakozni, a P2S VPN hasznos megoldás, ha helyek közötti VPN-t használ. A pont–hely VPN-kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani.

> [!IMPORTANT]
> A klasszikus üzemi modell kizárólag a Windows VPN-ügyfeleket támogatja, és a Secure Socket Tunneling Protocol (SSTP) SSL-alapú VPN-protokollt használja. A nem Windows rendszerű VPN-ügyfelek támogatásához létre kell hoznia a VNet a Resource Manager-alapú üzemi modellel. A Resource Manager-alapú üzemi modell az SSTP mellett az IKEv2 VPN protokollt is támogatja. További információk: [A pont–hely kapcsolatok](point-to-site-about.md).
>
>

![Pont–hely diagram](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="prerequisites"></a>Előfeltételek

A pont – hely tanúsítvány-hitelesítési kapcsolatokhoz a következő előfeltételek szükségesek:

* Egy dinamikus VPN-átjáró.
* A nyilvános kulcs (.cer fájl) egy főtanúsítványhoz, amely az Azure-ba van feltöltve. Ez a kulcs megbízható tanúsítványnak minősül, és a hitelesítéshez használatos.
* A főtanúsítványból létrejön egy ügyféltanúsítvány, majd települ az egyes csatlakozó ügyfélszámítógépekre. A rendszer ezt a tanúsítványt használja ügyfélhitelesítéshez.
* Minden csatlakozó ügyfélszámítógépen létre kell hozni és telepíteni kell egy VPN-ügyfélkonfigurációs csomagot. Az ügyfél-konfigurációs csomag konfigurálja az operációs rendszeren már meglévő natív VPN-ügyfelet a VNet való csatlakozáshoz szükséges adatokkal.

A pont – hely kapcsolatok nem igényelnek VPN-eszközt vagy helyszíni nyilvános IP-címet. A VPN-kapcsolat kiépítése SSTP (Secure Socket Tunneling Protocol) használatával történik. A kiszolgálói oldalon az SSTP 1.0, 1.1 és 1.2 verziója támogatott. Az ügyfél dönti el, hogy melyik verziót használja. Windows 8.1 és újabb kiadások esetén az SSTP alapértelmezés szerint az 1.2 verziót használja. 

További információk a pont – hely kapcsolatokról: [pont – hely kapcsolatok – gyakori kérdések](#point-to-site-faq).

### <a name="example-settings"></a>Példabeállítások

A következő értékek használatával hozzon létre egy tesztkörnyezetben, vagy tekintse át ezeket az értékeket a cikkben szereplő példák jobb megismeréséhez:

- **Virtuális hálózat (klasszikus) beállításainak létrehozása**
   - **Név**: adja meg a *VNet1*.
   - **Címterület**: írja be a *192.168.0.0/16*értéket. Ebben a példában csak egy címteret használunk. A virtuális hálózatához több címteret is használhat, ahogy ez az alábbi diagramon is látható.
   - **Alhálózat neve**: írja be a *FrontEnd*nevet.
   - **Alhálózat címtartomány**: írja be a *192.168.1.0/24*értéket.
   - **Előfizetés**: válasszon ki egy előfizetést az elérhető előfizetések listájából.
   - **Erőforráscsoport**: adja meg a *TestRG*. Válassza az **új létrehozása**elemet, ha az erőforráscsoport nem létezik.
   - Hely: válassza ki az **USA keleti** **régióját**a listából.

  - **VPN-kapcsolat beállításai**
    - **Kapcsolat típusa**: válassza **a pont – hely**lehetőséget.
    - **Ügyfél címterület**: írja be a *172.16.201.0/24*értéket. Azok a VPN-ügyfelek, amelyek ezzel a pont – hely kapcsolattal csatlakoznak a VNet, a megadott készletből kapnak IP-címet.

- **Átjáró-konfigurációs alhálózat beállításai**
   - **Név**: az *GatewaySubnet*-mel rendelkező autofilled.
   - **Címtartomány**: írja be a *192.168.200.0/24*értéket. 

- **Átjáró konfigurációs beállításai**:
   - **Méret**: válassza ki a használni kívánt átjáró SKU-t.
   - **Útválasztás típusa**: válassza a **dinamikus**lehetőséget.

## <a name="create-a-virtual-network-and-a-vpn-gateway"></a>Virtuális hálózat és VPN-átjáró létrehozása

Mielőtt elkezdené, ellenőrizze, hogy rendelkezik-e Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>1. rész: Virtuális hálózat létrehozása

Ha még nem rendelkezik virtuális hálózattal (VNet), hozzon létre egyet. A képernyőképek csak példaként szolgálnak. Ne felejtse el ezeket az értékeket a saját értékeire cserélni. Az alábbi lépésekkel hozhat létre virtuális hálózatokat az Azure portállal:

1. A [Azure Portal](https://portal.azure.com) menüben vagy a **Kezdőlap** lapon válassza az **erőforrás létrehozása**lehetőséget. Megnyílik az **új** oldal.

2. A **Keresés a piactéren** mezőbe írja be a *virtuális hálózat* kifejezést, és válassza a **virtuális hálózat** elemet a visszaadott listából. Megnyílik a **virtuális hálózat** lap.

3. A **telepítési modell kiválasztása** listában válassza a **klasszikus**lehetőséget, majd válassza a **Létrehozás**lehetőséget. Megnyílik a **virtuális hálózat létrehozása** lap.

4. A **Virtuális hálózat létrehozása** lapon konfigurálja a VNet beállításait. Ezen a lapon adhatja hozzá az első címterét és egy önálló alhálózati címtartományt. A virtuális hálózat létrehozása után visszaléphet, és további alhálózatokat és címtereket vehet fel.

   ![Virtuális hálózat létrehozása lap](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)

5. Válassza ki a használni kívánt **előfizetést** a legördülő listából.

6. Válasszon ki egy meglévő **erőforráscsoportot**. Vagy hozzon létre egy új erőforráscsoportot az **új létrehozása** elem kiválasztásával, és adjon meg egy nevet. Ha új erőforráscsoportot hoz létre, a tervezett konfigurációs értékeknek megfelelően nevezze el az erőforráscsoportot. További információ az erőforráscsoportok használatáról: [Azure Resource Manager Overview (áttekintés](../azure-resource-manager/management/overview.md#resource-groups)).

7. Válassza ki a VNet **helyét** . Ez a beállítás határozza meg a VNet üzembe helyezett erőforrások földrajzi helyét.

8. Válassza a **Létrehozás** lehetőséget a VNet létrehozásához. Az **értesítések** lapon megjelenik egy **központi telepítés folyamatban lévő** üzenetben.

8. A virtuális hálózat létrehozása után az **értesítések** lapon lévő üzenet az **üzembe helyezés sikeres**állapotba kerül. Válassza a **rögzítés az irányítópulton** lehetőséget, ha szeretné könnyen megkeresni a VNet az irányítópulton. 

10. Adjon meg egy DNS-kiszolgálót (nem kötelező). Miután létrehozta a virtuális hálózatot, a névfeloldás érdekében hozzáadhatja egy DNS-kiszolgáló IP-címét. A DNS-kiszolgáló megadott IP-címének egy olyan DNS-kiszolgáló címének kell lennie, amely fel tudja oldani a virtuális hálózatban található erőforrások nevét.

    DNS-kiszolgáló hozzáadásához válassza ki a **DNS-kiszolgálók** elemet a VNet oldaláról. Ezután adja meg a használni kívánt DNS-kiszolgáló IP-címét, majd válassza a **Mentés**lehetőséget.

### <a name="part-2-create-a-gateway-subnet-and-a-dynamic-routing-gateway"></a>2. rész: átjáró-alhálózat és dinamikus útválasztási átjáró létrehozása

Ebben a lépésben létrehoz egy átjáró-alhálózatot és egy dinamikus útválasztási átjárót. A klasszikus üzemi modell Azure Portal az átjáró alhálózatát és az átjárót ugyanazokkal a konfigurációs lapokkal hozza létre. Az átjáró-alhálózatot csak az átjáró-szolgáltatásokhoz használja. Semmit ne helyezzen üzembe közvetlenül az átjáróalhálózaton (például virtuális gépeket vagy más szolgáltatásokat).

1. A Azure Portal nyissa meg azt a virtuális hálózatot, amelyhez átjárót kíván létrehozni.

2. A virtuális hálózat lapján válassza az **Áttekintés**lehetőséget, majd a **VPN-kapcsolatok** szakaszban válassza az **átjáró**elemet.

   ![Átjáró létrehozásához válassza a lehetőséget.](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. A **Új VPN-kapcsolat** lapon válassza a **Pont–hely** beállítást.

   ![Pont–hely kapcsolat típusa](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Az **ügyfél címtartomány**területen adja meg azt az IP-címtartományt, amelyből a VPN-ügyfelek IP-címet kapnak a csatlakozáskor. Olyan magánhálózati IP-címtartományt használjon, amely nincs átfedésben azzal a helyszíni hellyel, amelyről csatlakozik, vagy azzal a VNet, amelyhez csatlakozik. A használni kívánt magánhálózati IP-címtartomány segítségével felülírhatja az autofilled Range tartományt. Ez a példa a kitöltött tartományt mutatja. 

   ![Ügyfélcímtér](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Válassza az **átjáró létrehozása azonnal**lehetőséget, majd válassza az átjáró **opcionális konfigurálása** lehetőséget az **átjáró konfigurációs** oldalának megnyitásához.

   ![Az átjáró választható konfigurációjának kiválasztása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)

6. Az átjáró **konfigurációja** lapon válassza az **alhálózat** lehetőséget az átjáró alhálózatának hozzáadásához. Az átjáró alhálózata is létrehozható kisebb as/29-ként. Azt javasoljuk azonban, hogy hozzon létre egy nagyobb alhálózatot, amely több címet is tartalmaz a legalább/28 vagy/27 érték kiválasztásával. Ez lehetővé teszi, hogy a jövőben elegendő címet biztosítson a lehetséges további konfigurációkhoz. Amikor átjáró-alhálózatokkal dolgozik, kerülje a hálózati biztonsági csoportok (NSG) társítását az átjáró-alhálózathoz. Ha hálózati biztonsági csoportot társít ehhez az alhálózathoz, előfordulhat, hogy a VPN-átjáró nem a várt módon fog működni. A beállítás mentéséhez kattintson **az OK gombra** .

   ![Átjáró-alhálózat hozzáadása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Válassza ki az átjáró **méretét**. A méret a virtuális hálózati átjáró termékváltozata. A Azure Portal az alapértelmezett SKU **alapértelmezett érték**. További információ az átjárók SKU-ról: [Tudnivalók a VPN Gateway beállításairól](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

   ![Az átjáró mérete](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Válassza ki az átjáró **útválasztási típusát**. A pont–hely konfigurációhoz a **Dynamic** (Dinamikus) útválasztási típusra van szükség. Ha befejezte a lap konfigurálását, kattintson **az OK gombra** .

   ![Az útválasztási típus konfigurálása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)

9. Az **új VPN-kapcsolat** lapon kattintson az **OK gombra** a lap alján a virtuális hálózati átjáró létrehozásának megkezdéséhez. A VPN-átjáró akár 45 percet is igénybe vehet, attól függően, hogy melyik átjárói SKU-t választotta.
 
## <a name="create-certificates"></a><a name="generatecerts"></a>Tanúsítványok létrehozása

Az Azure tanúsítványokat használ a VPN-ügyfelek hitelesítéséhez pont – hely VPN-kapcsolatokhoz. A főtanúsítvány nyilvánoskulcs-adatait feltölti az Azure-ba. A nyilvános kulcsot Ezután *megbízhatónak*tekinti a rendszer. Az ügyféltanúsítványt a megbízható főtanúsítványból kell létrehozni, majd telepíteni kell az egyes ügyfélszámítógépekre a tanúsítványok – aktuális User\Personal\Certificates tanúsítványtárolóban. A tanúsítvány az ügyfél hitelesítésére szolgál a VNet való csatlakozáskor. 

Ha önaláírt tanúsítványokat használ, azokat meghatározott paraméterek használatával kell létrehoznia. Létrehozhat egy önaláírt tanúsítványt a [PowerShell és a Windows 10](vpn-gateway-certificates-point-to-site.md), vagy a [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)utasítások használatával. Fontos, hogy kövesse ezeket az utasításokat, amikor önaláírt főtanúsítványokat használ, és az önaláírt főtanúsítványból állít elő ügyféltanúsítványt. Ellenkező esetben a létrehozott tanúsítványok nem lesznek kompatibilisek a P2S-kapcsolatokkal, és hibaüzenetet kap.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>A főtanúsítványhoz tartozó nyilvános kulcs (. cer) megvásárlása

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Ügyféltanúsítvány létrehozása

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>A főtanúsítvány .cer fájljának feltöltése

Az átjáró létrehozása után töltse fel a megbízható főtanúsítványhoz tartozó. cer fájlt (amely a nyilvános kulcs adatait tartalmazza) az Azure-kiszolgálóra. Ne töltse fel a főtanúsítvány titkos kulcsát. A tanúsítvány feltöltése után az Azure arra használja, hogy hitelesítse a megbízható főtanúsítványból generált ügyféltanúsítványt telepítő ügyfeleket. Később további megbízható főtanúsítvány-fájlokat (akár 20) is feltölthet, ha szükséges.  

1. A VNet lapjának **VPN-kapcsolatok** szakaszában válassza az ügyfelek grafikát a **pont – hely VPN-kapcsolat** lap megnyitásához.

   ![Ügyfelek](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. A **pont – hely VPN-kapcsolat** lapon válassza a **tanúsítvány kezelése** elemet a **tanúsítványok** lap megnyitásához.

   ![Tanúsítványok lap](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

1. A **tanúsítványok** lapon válassza a **feltöltés** lehetőséget a **tanúsítvány feltöltése** lap megnyitásához.

    ![Tanúsítványok feltöltése lap](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)

4. Válassza ki a mappa grafikáját a. cer fájl tallózásához. Válassza ki a fájlt, majd kattintson **az OK gombra**. A feltöltött tanúsítvány megjelenik a **tanúsítványok** lapon.

   ![Tanúsítvány feltöltése](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)


## <a name="configure-the-client"></a>Az ügyfél konfigurálása

Ha pont – hely típusú VPN-kapcsolattal szeretne kapcsolódni egy VNet, minden ügyfélnek telepítenie kell egy csomagot a natív Windows VPN-ügyfél konfigurálásához. A konfigurációs csomag konfigurálja a natív Windows VPN-ügyfelet a virtuális hálózathoz való csatlakozáshoz szükséges beállításokkal.

Használhatja a VPN-ügyfél azonos konfigurációs csomagját minden ügyfélszámítógépen, feltéve, hogy a verzió megfelel az ügyfél architektúrájának. A támogatott ügyféloldali operációs rendszerek listáját lásd: [pont – hely kapcsolatok – gyakori kérdések](#point-to-site-faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>VPN-ügyfél konfigurációs csomagjának létrehozása és telepítése

1. A Azure Portal a VNet **Áttekintés** lapjának **VPN-kapcsolatok**területén válassza ki az ügyfél grafikáját a **pont – hely VPN-kapcsolat** lap megnyitásához.

2. A **pont – hely VPN-kapcsolat** lapon válassza ki azt a letöltési csomagot, amely a telepített ügyfél operációs rendszernek felel meg:

   * 64 bites ügyfelek esetén válassza a **VPN Client (64-bit)** (VPN-ügyfél (64 bit)) lehetőséget.
   * 32 bites ügyfelek esetén válassza a **VPN Client (32-bit)** (VPN-ügyfél (32 bit)) lehetőséget.

   ![A VPN-ügyfél konfigurációs csomagjának letöltése](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)

3. A csomag létrehozása után töltse le, majd telepítse az ügyfélszámítógépre. Ha egy SmartScreen előugró ablak jelenik meg, válassza a **További információ**, majd a **Futtatás egyébként**lehetőséget. A csomagot mentheti is, így más ügyfélszámítógépekre is telepítheti.

### <a name="install-a-client-certificate"></a>Ügyféltanúsítvány telepítése

Ha az Ügyféltanúsítványok létrehozásához használt P2S szeretne létrehozni egy másik ügyfélszámítógépről, telepítsen egy ügyféltanúsítványt. Ügyféltanúsítvány telepítésekor szükség lesz az ügyféltanúsítvány exportálásakor létrehozott jelszóra. A tanúsítványt általában úgy is telepítheti, ha duplán kattint rá. További információkért lásd az [exportált ügyféltanúsítványok telepítését](vpn-gateway-certificates-point-to-site.md#install) ismertető cikket.


## <a name="connect-to-your-vnet"></a>Csatlakozás a virtuális hálózathoz

>[!NOTE]
>Rendszergazdai jogosultsággal kell rendelkeznie azon az ügyfélszámítógépen, ahonnan csatlakozik.
>
>

1. Ha csatlakozni szeretne a VNet, nyissa meg az ügyfélszámítógépen a **VPN-kapcsolatokat** a Azure Portal, és keresse meg a létrehozott VPN-kapcsolatot. A VPN-kapcsolat neve megegyezik a virtuális hálózattal. Kattintson a **Csatlakozás** gombra. Ha megjelenik egy előugró üzenet a tanúsítványról, kattintson a **tovább** gombra emelt szintű jogosultságok használatához.

2. **A kapcsolat állapota lapon** válassza a **Kapcsolódás** lehetőséget a kapcsolat indításához. Ha a **tanúsítvány kiválasztása** képernyő jelenik meg, ellenőrizze, hogy a megjelenő ügyféltanúsítvány megfelelő-e. Ha nem, válassza ki a megfelelő tanúsítványt a legördülő listából, majd kattintson az **OK gombra**.

3. Ha a kapcsolat sikeres, egy **csatlakoztatott** értesítés jelenik meg.


### <a name="troubleshooting-p2s-connections"></a>Pont–hely kapcsolatok hibaelhárítása

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>A VPN-kapcsolat ellenőrzése

1. Ellenőrizze, hogy a VPN-kapcsolat aktív-e. Nyisson meg egy rendszergazda jogú parancssort az ügyfélszámítógépen, és futtassa az **ipconfig/all**parancsot.
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

A megbízható főtanúsítványokat felveheti vagy el is távolíthatja az Azure-ban. Ha eltávolít egy főtanúsítványt, akkor az adott gyökérből létrehozott tanúsítvánnyal rendelkező ügyfelek már nem hitelesíthetők és nem csatlakozhatnak. Ahhoz, hogy az ügyfelek hitelesíteni és újra csatlakozzanak, telepítenie kell egy új ügyféltanúsítványt, amely az Azure által megbízhatónak tartott főtanúsítványból lett létrehozva.

### <a name="to-add-a-trusted-root-certificate"></a>Megbízható főtanúsítvány hozzáadása

Az Azure-ra legfeljebb 20 megbízható főtanúsítványt tölthet fel .cer fájl formájában. Útmutatásért lásd: a főtanúsítvány. cer fájljának feltöltése.

### <a name="to-remove-a-trusted-root-certificate"></a>Megbízható főtanúsítvány eltávolítása

1. A VNet lapjának **VPN-kapcsolatok** szakaszában válassza az ügyfelek grafikát a **pont – hely VPN-kapcsolat** lap megnyitásához.

   ![Ügyfelek](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. A **pont – hely VPN-kapcsolat** lapon válassza a **tanúsítvány kezelése** elemet a **tanúsítványok** lap megnyitásához.

   ![Tanúsítványok lap](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

3. A **tanúsítványok** lapon válassza ki az eltávolítani kívánt tanúsítvány melletti három pontot, majd válassza a **Törlés**lehetőséget.

   ![Főtanúsítvány törlése](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)

## <a name="revoke-a-client-certificate"></a>Ügyféltanúsítvány visszavonása

Ha szükséges, visszavonhatja az ügyféltanúsítványt. A visszavont tanúsítványok listájával az egyes ügyféltanúsítványok alapján, szelektíven tagadhatja meg a pont–hely kapcsolódás lehetőségét. Ez a módszer különbözik a megbízható főtanúsítványok eltávolításának. Ha töröl egy .cer formátumú megbízható főtanúsítványt az Azure-ból, azzal megvonja a hozzáférést minden olyan ügyféltanúsítványtól, amelyet a visszavont főtanúsítvánnyal hoztak létre/írtak alá. A főtanúsítvány helyett az ügyféltanúsítvány visszavonásával a főtanúsítványból létrehozott többi tanúsítvány továbbra is használható a pont–hely kapcsolat hitelesítésére.

A szokásos gyakorlat az, hogy a főtanúsítvánnyal kezelik a hozzáférést a munkacsoport vagy a szervezet szintjén, az egyes felhasználókra vonatkozó részletesebb szabályozást pedig visszavont ügyféltanúsítványokkal oldják meg.

### <a name="to-revoke-a-client-certificate"></a>Ügyféltanúsítvány visszavonása

Az ügyféltanúsítványok visszavonásához vegye fel az ujjlenyomatot a visszavont tanúsítványok listájára.

1. Kérje le az ügyféltanúsítvány ujjlenyomatát. További információ [: How to: lekérdezheti a tanúsítvány ujjlenyomatát](https://msdn.microsoft.com/library/ms734695.aspx).
2. Másolja az adatokat egy szövegszerkesztőbe, és távolítsa el a szóközöket úgy, hogy az folytonos sztring legyen.
3. Nyissa meg a klasszikus virtuális hálózatot. Válassza a **pont – hely VPN-kapcsolat**lehetőséget, majd válassza a **tanúsítvány kezelése** elemet a **tanúsítványok** lap megnyitásához.
4. Válassza a **visszavont tanúsítványok listája** elemet a **visszavont tanúsítványok listája** lap megnyitásához. 
5. Válassza a **tanúsítvány hozzáadása** lehetőséget a **tanúsítvány hozzáadása** a visszavont tanúsítványok listájához lap megnyitásához.
6. Az **ujjlenyomatban**illessze be a tanúsítvány ujjlenyomatát egyetlen folytonos szövegként, szóközök nélkül. A befejezéshez kattintson **az OK gombra** .

A frissítés befejezését követően a tanúsítvány már nem használható csatlakozáshoz. Azok az ügyfelek, akik ezzel a tanúsítvánnyal próbálnak csatlakozni, egy üzenetet kapnak, amely azt jelzi, hogy a tanúsítvány már nem érvényes.

## <a name="point-to-site-faq"></a>Pont–hely kapcsolatok – gyakori kérdések

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>További lépések

- A kapcsolódás befejezése után virtuális gépeket adhat hozzá a virtuális hálózatokhoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/). 

- A hálózatkezelési és a Linux-alapú virtuális gépekkel kapcsolatos további információkért lásd: az [Azure és a Linux](../virtual-machines/linux/network-overview.md)rendszerű virtuálisgép-hálózatok áttekintése.

- A pont–hely hibaelhárítási információiért tekintse át az [Azure pont–hely kapcsolatok hibaelhárításával](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) foglalkozó cikket.
