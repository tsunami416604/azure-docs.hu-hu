---
title: 'Számítógép csatlakoztatása virtuális hálózathoz pont – hely és a Tanúsítványalapú hitelesítés használatával: A klasszikus Azure portálon |} A Microsoft Docs'
description: Klasszikus pont–hely VPN-átjárókapcsolat létrehozása az Azure Portalon.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: e71999e9c5b118fbf31d0d735d03cddb321b0065
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773418"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Pont – hely kapcsolat konfigurálása a Tanúsítványalapú hitelesítés (klasszikus) használatával

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Ez a cikk bemutatja, hogyan hozhat létre virtuális hálózat pont – hely kapcsolattal rendelkező. A klasszikus üzemi modellben a virtuális hálózat létrehozása az Azure portal használatával. A konfiguráció önaláírt vagy hitelesítésszolgáltató által kibocsátott tanúsítványokat használ a kapcsolódó ügyfelek hitelesítéséhez. Ezt a konfigurációt más üzembehelyezési eszközzel vagy a modell az alábbi cikkekben leírt beállítások használatával is létrehozhat:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Pont – hely (P2S) VPN-átjáró használatával egy biztonságos kapcsolat létesítését a virtuális hálózat egy különálló ügyfélszámítógépről. Pont – hely VPN-kapcsolatok akkor hasznos, ha szeretne csatlakozni egy távoli helyről, a virtuális hálózathoz. Ha csak néhány ügyfelet kíván csatlakoztatni egy virtuális hálózathoz, a P2S VPN-kapcsolattal hasznos megoldás lehet a helyek közötti VPN helyett használja. A pont–hely VPN-kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani.

> [!IMPORTANT]
> A klasszikus üzemi modell kizárólag a Windows VPN-ügyfeleket támogatja, és a Secure Socket Tunneling Protocol (SSTP) SSL-alapú VPN-protokollt használja. Támogatja a Windows VPN-ügyfelek számára, létre kell hoznia a virtuális hálózathoz a Resource Manager üzemi modellel. A Resource Manager-alapú üzemi modell az SSTP mellett az IKEv2 VPN protokollt is támogatja. További információk: [A pont–hely kapcsolatok](point-to-site-about.md).
>
>

![Pont–hely diagram](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="prerequisites"></a>Előfeltételek

Pont – hely tanúsítvánnyal hitelesített kapcsolatokhoz szükséges a következő előfeltételek vonatkoznak:

* Egy dinamikus VPN-átjáró.
* A nyilvános kulcs (.cer fájl) egy főtanúsítványhoz, amely az Azure-ba van feltöltve. Ezt a kulcsot a megbízható tanúsítványnak tekinti, és ezt használja hitelesítéshez.
* A főtanúsítványból létrejön egy ügyféltanúsítvány, majd települ az egyes csatlakozó ügyfélszámítógépekre. A rendszer ezt a tanúsítványt használja ügyfélhitelesítéshez.
* Minden csatlakozó ügyfélszámítógépen létre kell hozni és telepíteni kell egy VPN-ügyfélkonfigurációs csomagot. Az ügyfél-konfigurációs csomag konfigurálja a natív VPN-ügyfél, amely már be van kapcsolva az operációs rendszer a virtuális hálózathoz való csatlakozáshoz szükséges adatokkal.

Pont – hely kapcsolatok nem igényelnek VPN-eszközt vagy helyszíni nyilvános IP-címet. A VPN-kapcsolat kiépítése SSTP (Secure Socket Tunneling Protocol) használatával történik. A kiszolgálói oldalon az SSTP 1.0, 1.1 és 1.2 verziója támogatott. Az ügyfél dönti el, hogy melyik verziót használja. Windows 8.1 és újabb kiadások esetén az SSTP alapértelmezés szerint az 1.2 verziót használja. 

Pont – hely kapcsolatok kapcsolatos további információkért lásd: [pont – hely – gyakori kérdések](#point-to-site-faq).

### <a name="example-settings"></a>Példabeállítások

A következő értékek használatával létrehozhat egy tesztkörnyezetet, vagy tekintse meg ezeket az értékeket értelmezheti a cikkben szereplő példákat:

- **Virtuális hálózat létrehozása (klasszikus) beállításai**
   - **Név**: Adja meg *VNet1*.
   - **Címtér**: Adja meg *192.168.0.0/16*. Ebben a példában csak egy címteret használunk. A virtuális hálózatához több címteret is használhat, ahogy ez az alábbi diagramon is látható.
   - **Alhálózat neve**: Adja meg *előtérbeli*.
   - **Alhálózati címtartomány**: Adja meg *192.168.1.0/24*.
   - **Előfizetés**: Válasszon ki egy előfizetést az elérhető előfizetések listájából.
   - **Erőforráscsoport**: Adja meg *TestRG*. Válassza ki **új létrehozása**, ha az erőforráscsoport nem létezik.
   - **Hely**: Válassza ki **USA keleti Régiójában** a listából.

 - **VPN-beállításokat**
   - **Kapcsolat típusa**: Válassza ki **pont – hely**.
   - **Ügyfélcímtér**: Adja meg *172.16.201.0/24*. A pont – hely kapcsolat használatával csatlakoznak a virtuális hálózathoz a VPN-ügyfelek a megadott címkészletből kapnak IP-címet.

- **Átjáró alhálózati beállítások**
   - **Név**: A Autofilled *GatewaySubnet*.
   - **Címtartomány**: Adja meg *192.168.200.0/24*. 

- **Gateway konfigurációs beállításairól**:
   - **Méret**: Válassza ki az átjáró használni kívánt Termékváltozatot.
   - **Útválasztási típus**: Válassza ki **dinamikus**.

## <a name="create-a-virtual-network-and-a-vpn-gateway"></a>Virtuális hálózat és VPN-átjáró létrehozása

Mielőtt elkezdené, győződjön meg arról, hogy egy Azure-előfizetést. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>1. rész: Virtuális hálózat létrehozása

Ha már nincs virtuális hálózat (VNet), hozzon létre egyet. A képernyőképek csak példaként szolgálnak. Ne felejtse el ezeket az értékeket a saját értékeire cserélni. Az alábbi lépésekkel hozhat létre virtuális hálózatokat az Azure portállal:

1. Jelentkezzen be a [az Azure portal](http://portal.azure.com) válassza **erőforrás létrehozása**. A **új** lap megnyitásakor. 

2. Az a **keresés a piactéren** írja be a következőt *virtuális hálózat* válassza **virtuális hálózati** a visszaadott listában. A **virtuális hálózati** lap megnyitásakor.

3. Az a **telepítési modell kiválasztása** listájáról válassza ki a **klasszikus**, majd válassza ki **létrehozás**. A **virtuális hálózat létrehozása** lap megnyitásakor.

4. A **Virtuális hálózat létrehozása** lapon konfigurálja a VNet beállításait. Ezen a lapon adhatja hozzá az első címterét és egy önálló alhálózati címtartományt. A virtuális hálózat létrehozása után visszaléphet, és további alhálózatokat és címtereket vehet fel.

   ![Virtuális hálózat létrehozása lap](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)

5. Válassza ki a **előfizetés** a legördülő listából válassza ki a használni kívánt.

6. Válasszon egy meglévő **erőforráscsoport**. Vagy hozzon létre egy új erőforráscsoportot kiválasztásával **új létrehozása** , és adjon meg egy nevet. Ha egy új erőforráscsoportot hoz létre, adjon nevet az erőforráscsoport, a tervezett konfigurációs értékeknek megfelelően. További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md#resource-groups).

7. Válassza ki a **hely** a virtuális hálózat számára. Ez a beállítás meghatározza, hogy a virtuális hálózaton üzembe helyezett erőforrások földrajzi helyét.

8. Válassza ki **létrehozás** a virtuális hálózat létrehozásához. Az a **értesítések** lapon látni fog egy **üzembe helyezés folyamatban** üzenet.

8. A virtuális hálózat létrehozása után, az üzenet a a **értesítések** lapra vált **üzembe helyezés sikeres**. Válassza ki **rögzítés az irányítópulton** Ha szeretné könnyen megtalálni a Vnetet az irányítópulton. 

10. Adjon meg egy DNS-kiszolgálót (nem kötelező). Miután létrehozta a virtuális hálózatot, a névfeloldás érdekében hozzáadhatja egy DNS-kiszolgáló IP-címét. A DNS-kiszolgáló megadott IP-címének egy olyan DNS-kiszolgáló címének kell lennie, amely fel tudja oldani a virtuális hálózatban található erőforrások nevét.

    DNS-kiszolgáló hozzáadásához válassza **DNS-kiszolgálók** a virtuális hálózat lapján. Ezután írja be a DNS-kiszolgáló, amelyet szeretne használni, és válassza ki az IP-cím **mentése**.

### <a name="part-2-create-a-gateway-subnet-and-a-dynamic-routing-gateway"></a>2. rész: Egy átjáró-alhálózatot és egy dinamikus útválasztású átjáró létrehozása

Ebben a lépésben egy átjáró-alhálózatot és egy dinamikus útválasztású átjárót hoz létre. Az Azure Portalon a klasszikus üzemi modellhez az átjáró-alhálózat és az átjáró ugyanazokkal a konfigurációs lapokkal keresztül hoz létre. Az átjáró-alhálózat használata kizárólag az átjárószolgáltatásokhoz. Semmit ne helyezzen üzembe közvetlenül az átjáróalhálózaton (például virtuális gépeket vagy más szolgáltatásokat).

1. Az Azure Portalon lépjen a virtuális hálózathoz, amelyekhez az átjáró létrehozásához.

2. Válassza ki a lapon a virtuális hálózat **áttekintése**, majd a a **VPN-kapcsolatok** szakaszban jelölje be **átjáró**.

  ![Válassza ki az átjáró létrehozása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. A **Új VPN-kapcsolat** lapon válassza a **Pont–hely** beállítást.

  ![Pont–hely kapcsolat típusa](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. A **Ügyfélcímtér**, adja hozzá az IP-címtartományt, amelyből a VPN-ügyfelek IP-címet kapnak, kapcsolódáskor. A helyszíni hellyel, amelyről csatlakozik, vagy a virtuális hálózattal, hogy csatlakozni, használja a magánhálózati IP-címtartományt, amely nincs átfedésben. A magánhálózati IP-címtartományt, amelyet használni szeretne a autofilled tartomány is felülírása. Ez a példa bemutatja a autofilled tartományt. 

  ![Ügyfélcímtér](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Válassza ki **átjáró azonnali létrehozása**, majd válassza ki **átjáró opcionális konfigurálása** megnyitásához a **átjárókonfiguráció** lapot.

  ![Válassza ki az átjáró opcionális konfigurálása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)

6. Az a **átjárókonfiguráció** lapon jelölje be **alhálózati** az átjáró-alhálózat hozzáadása. Létrehozható átjáróalhálózat kisebb, akár/29 méretű legyen. Azt javasoljuk azonban, hogy a, több címet tartalmazó legalább/28-as vagy/27-eset kiválasztásával nagyobb alhálózat létrehozását. Így lehetővé teheti elegendő címet, amelyet érdemes a jövőben lehetséges további konfigurációk megvalósításához. Amikor átjáró-alhálózatokkal dolgozik, kerülje a hálózati biztonsági csoportok (NSG) társítását az átjáró-alhálózathoz. Egy hálózati biztonsági csoportot az alhálózathoz társítja, előfordulhat, hogy a VPN gateway nem az elvárt módon működnek. Válassza ki **OK** Ez a beállítás mentéséhez.

  ![Átjáró-alhálózat hozzáadása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Válassza ki az átjáró **méretét**. A méret a virtuális hálózati átjáró termékváltozata. Az Azure Portalon, az az alapértelmezett Termékváltozat érték **alapértelmezett**. Átjáró-Termékváltozatokkal kapcsolatos további információkért lásd: [tudnivalók a VPN gateway beállításairól](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

  ![Az átjáró mérete](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Válassza ki az átjáró **útválasztási típusát**. A pont–hely konfigurációhoz a **Dynamic** (Dinamikus) útválasztási típusra van szükség. Válassza ki **OK** amikor befejezte a lap konfigurálását.

  ![Az útválasztási típus konfigurálása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)

9. Az a **új VPN-kapcsolat** lapon jelölje be **OK** a virtuális hálózati átjáró létrehozásához a lap alján. Egy VPN-átjárót az átjáró Termékváltozatától függően akár 45 percet is igénybe vehet.
 
## <a name="generatecerts"></a>Tanúsítványok létrehozása

Azure tanúsítványok segítségével hitelesíti a VPN-ügyfelek a pont – hely VPN-kapcsolatokban. A főtanúsítvány nyilvánoskulcs-adatait feltölti az Azure-ba. A nyilvános kulcs ezután minősül *megbízható*. Ügyféltanúsítványok kell lennie a megbízható főtanúsítványból létrehozott ügyféltanúsítvány, és majd telepítve a tanúsítványok – aktuális felhasználó\személyes\tanúsítványok útvonalon tanúsítványtárolóban minden egyes ügyfélszámítógépen. A tanúsítvány a virtuális hálózathoz való csatlakozáskor az ügyfél hitelesítésére szolgál. 

Ha önaláírt tanúsítványokat használ, azokat megadott paraméterekkel kell létrehozni. Létrehozhat egy önaláírt tanúsítvány használatával a következő útmutatót: [PowerShell és Windows 10-es](vpn-gateway-certificates-point-to-site.md), vagy [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Fontos kövesse ezeket az utasításokat az önaláírt főtanúsítványok használata és önaláírt főtanúsítványból az ügyféltanúsítványokat. Ellenkező esetben a létrehozott tanúsítványok nem lesznek kompatibilisek a P2S-kapcsolatokkal, és a kapcsolódási hiba kap.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>A nyilvános kulcs (.cer) beszerzése a főtanúsítványhoz

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Ügyféltanúsítvány létrehozása

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>A főtanúsítvány .cer fájljának feltöltése

Az átjáró létrehozása után töltse fel a .cer fájlt (amely a nyilvános kulcsot tartalmazza) az Azure-kiszolgáló megbízható legfelső szintű tanúsítvány. A főtanúsítvány titkos kulcsát ne töltse fel. Miután feltölti a tanúsítványt, az Azure használja, amelyen telepítve van a megbízható főtanúsítványból létrehozott ügyféltanúsítvány-ügyfelek hitelesítésére. Szükség esetén további megbízható legfelső szintű tanúsítvány fájlokat (legfeljebb 20), később is feltölthet.  

1. Az a **VPN-kapcsolatok** szakaszban a virtuális hálózat lapjának, válassza ki az ügyfél ábrára a **pont – hely VPN kapcsolat** lapot.

  ![Ügyfelek](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Az a **pont – hely VPN kapcsolat** lapon jelölje be **szükséges tanúsítvány kezelése** megnyitásához a **tanúsítványok** lap.

  ![Tanúsítványok lap](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

1. Az a **tanúsítványok** lapon jelölje be **feltöltése** megnyitásához a **tanúsítvány feltöltése** lap.

    ![Tanúsítványok feltöltése lap](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)

4. Válassza ki a mappa ábrára a .cer fájl. Válassza ki a fájlt, majd válassza ki **OK**. A feltöltött tanúsítvány megjelenik-e a **tanúsítványok** lapot.

  ![Tanúsítvány feltöltése](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)


## <a name="configure-the-client"></a>Az ügyfél konfigurálása

Egy virtuális hálózathoz való csatlakozáshoz egy pont – hely VPN-en keresztül, minden ügyfélen telepíteni kell egy csomagot, amely a natív Windows VPN-ügyfél konfigurálásához. A konfigurációs csomag konfigurálja a natív Windows VPN-ügyfelet a virtuális hálózathoz való csatlakozáshoz szükséges beállításokkal.

Használhatja a VPN-ügyfél azonos konfigurációs csomagját minden ügyfélszámítógépen, feltéve, hogy a verzió megfelel az ügyfél architektúrájának. A támogatott ügyfél operációs rendszerek listáját lásd: a [pont – hely kapcsolatok – gyakori kérdések](#point-to-site-faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Hozzon létre, és a egy VPN-ügyfél konfigurációs csomagjának telepítése

1. Az Azure Portalon az a **áttekintése** a virtuális hálózat lap **VPN-kapcsolatok**, válassza ki az ügyfél ábrára a **pont – hely VPN kapcsolat** lap.

2. Az a **pont – hely VPN kapcsolat** lapon, válassza ki a letöltési csomagra, amely megfelel az ügyfél operációs rendszerét, amelyre telepítve van:

  * 64 bites ügyfelek esetén válassza a **VPN Client (64-bit)** (VPN-ügyfél (64 bit)) lehetőséget.
  * 32 bites ügyfelek esetén válassza a **VPN Client (32-bit)** (VPN-ügyfél (32 bit)) lehetőséget.

  ![A VPN-ügyfél konfigurációs csomagjának letöltése](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)

3. Miután a csomag állít elő, töltse le, és telepítse az ügyfélszámítógépen. Ha egy SmartScreen előugró ablak jelenik meg, válassza ki a **szűrőfüggvénnyel**, majd **Futtatás mindenképpen**. A csomagot mentheti is, így más ügyfélszámítógépekre is telepítheti.

### <a name="install-a-client-certificate"></a>Ügyféltanúsítvány telepítése

A P2S-kapcsolat létrehozása a különböző ügyfél-számítógépen fut, a használandó ügyféltanúsítványok előállításához, telepíthet ügyféltanúsítványt. Amikor telepít egy ügyféltanúsítványt, az ügyféltanúsítvány exportálásakor létrehozott jelszóra van szükség. A tanúsítvány általában telepíthető csak kattintani. További információkért lásd az [exportált ügyféltanúsítványok telepítését](vpn-gateway-certificates-point-to-site.md#install) ismertető cikket.


## <a name="connect-to-your-vnet"></a>Csatlakozás a virtuális hálózathoz

>[!NOTE]
>Rendszergazdai jogosultsággal kell rendelkeznie azon az ügyfélszámítógépen, ahonnan csatlakozik.
>
>

1. Csatlakozás a virtuális hálózathoz, az ügyfélszámítógépen navigáljon a **VPN-kapcsolatok** az Azure Portalon, és keresse meg a létrehozott VPN-kapcsolatot. A VPN-kapcsolat neve megegyezik a virtuális hálózat rendelkezik. Kattintson a **Csatlakozás** gombra. Ha megjelenik egy előugró üzenet tanúsítvánnyal kapcsolatban, válassza ki a **Folytatás** emelt szintű jogosultságok használatához.

2. Az a **kapcsolat** állapota lapon jelölje be **Connect** a kapcsolat elindításához. Ha megjelenik a **tanúsítvány kiválasztása** képernyőn, győződjön meg arról, hogy a megjelenített ügyféltanúsítvány a megfelelőt. Ha nem, válassza ki a megfelelő tanúsítványt a legördülő listából, és válassza **OK**.

3. Ha a kapcsolódás sikeres, megjelenik egy **csatlakoztatva** értesítést.


### <a name="troubleshooting-p2s-connections"></a>Pont–hely kapcsolatok hibaelhárítása

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>A VPN-kapcsolat ellenőrzése

1. Ellenőrizze, hogy a VPN-kapcsolat aktív-e. Nyisson meg egy rendszergazda jogú parancssort az ügyfélszámítógépen, és futtassa **ipconfig/all**.
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

## <a name="add-or-remove-trusted-root-certificates"></a>Adja hozzá, vagy távolítsa el a megbízható legfelső szintű tanúsítványok

A megbízható főtanúsítványokat felveheti vagy el is távolíthatja az Azure-ban. Főtanúsítvány eltávolításakor abból a gyökérből létrehozott tanúsítvánnyal rendelkező ügyfelek már nem hitelesítheti és csatlakozáshoz. Ezek az ügyfelek hitelesítéséhez, és csatlakozzon újra, amely az Azure megbízható főtanúsítványból generált új ügyféltanúsítványt kell telepítenie.

### <a name="to-add-a-trusted-root-certificate"></a>Megbízható főtanúsítvány hozzáadása

Az Azure-ra legfeljebb 20 megbízható főtanúsítványt tölthet fel .cer fájl formájában. Útmutatásért lásd: a főtanúsítvány .cer fájljának feltöltése.

### <a name="to-remove-a-trusted-root-certificate"></a>Megbízható főtanúsítvány eltávolítása

1. Az a **VPN-kapcsolatok** szakaszban a virtuális hálózat lapjának, válassza ki az ügyfél ábrára a **pont – hely VPN kapcsolat** lapot.

   ![Ügyfelek](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Az a **pont – hely VPN kapcsolat** lapon jelölje be **szükséges tanúsítvány kezelése** megnyitásához a **tanúsítványok** lap.

   ![Tanúsítványok lap](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

3. Az a **tanúsítványok** lapra, jelölje be a távolítsa el, majd válassza ki a kívánt tanúsítvány melletti három pontra **törlése**.

   ![Főtanúsítvány törlése](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)

## <a name="revoke-a-client-certificate"></a>Ügyféltanúsítvány visszavonása

Ha szükséges, visszavonhatja a ügyféltanúsítvány. A visszavont tanúsítványok listájával az egyes ügyféltanúsítványok alapján, szelektíven tagadhatja meg a pont–hely kapcsolódás lehetőségét. Ez a módszer eltér a megbízható főtanúsítvány eltávolításától. Ha töröl egy .cer formátumú megbízható főtanúsítványt az Azure-ból, azzal megvonja a hozzáférést minden olyan ügyféltanúsítványtól, amelyet a visszavont főtanúsítvánnyal hoztak létre/írtak alá. A főtanúsítvány helyett az ügyféltanúsítvány visszavonásával a főtanúsítványból létrehozott többi tanúsítvány továbbra is használható a pont–hely kapcsolat hitelesítésére.

A szokásos gyakorlat az, hogy a főtanúsítvánnyal kezelik a hozzáférést a munkacsoport vagy a szervezet szintjén, az egyes felhasználókra vonatkozó részletesebb szabályozást pedig visszavont ügyféltanúsítványokkal oldják meg.

### <a name="to-revoke-a-client-certificate"></a>Ügyféltanúsítvány visszavonása

Az ügyféltanúsítványok visszavonásához vegye fel az ujjlenyomatot a visszavont tanúsítványok listájára.

1. Kérje le az ügyféltanúsítvány ujjlenyomatát. További információkért lásd: [hogyan: A tanúsítvány ujjlenyomatának lekérését](https://msdn.microsoft.com/library/ms734695.aspx).
2. Másolja át az adatokat egy szövegszerkesztőbe, és távolítsa el a szóközöket, úgy, hogy egy folyamatos karakterláncot kapjon.
3. Lépjen a klasszikus virtuális hálózatot. Válassza ki **pont – hely VPN kapcsolat**, majd **szükséges tanúsítvány kezelése** megnyitásához a **tanúsítványok** lap.
4. Válassza ki **visszavont tanúsítványok listájának** megnyitásához a **visszavont tanúsítványok listájának** lapot. 
5. Válassza ki **tanúsítvány hozzáadása** megnyitásához a **tanúsítvány felvétele a visszavont tanúsítványok listájának** lapot.
6. A **ujjlenyomat**, illessze be a tanúsítvány ujjlenyomatát egy folyamatos sorként szöveg, szóközök nélküli szövegláncként. Válassza ki **OK** befejezéséhez.

A frissítés befejezését követően a tanúsítvány már nem használható csatlakozáshoz. Azok az ügyfelek ezt a tanúsítványt használatával csatlakozik, hibaüzenet arról tájékoztatja, hogy a tanúsítvány már nem érvényes.

## <a name="point-to-site-faq"></a>Pont – hely – gyakori kérdések

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>További lépések

- Miután a kapcsolat elkészült, a virtuális hálózatokhoz hozzáadhat virtuális gépeket. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/). 

- Hálózatok és Linux rendszerű virtuális gépek ismertetése: [Azure- és Linux rendszerű virtuális gép hálózati áttekintés](../virtual-machines/linux/network-overview.md).

- A pont–hely hibaelhárítási információiért tekintse át az [Azure pont–hely kapcsolatok hibaelhárításával](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) foglalkozó cikket.
