---
title: "Számítógép csatlakoztatása egy virtuális hálózatot, pont-pont és a RADIUS-hitelesítés használatával: PowerShell |} Azure"
description: "Biztonságos számítógép csatlakoztatása az Azure-beli virtuális hálózathoz hozzon létre egy pont – hely típusú VPN gateway-kapcsolatot, amely a RADIUS-hitelesítést használ."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2017
ms.author: anzaman
ms.openlocfilehash: 13ae129eefb717f22db25ab29232fe1efe69a8ce
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Egy RADIUS-hitelesítés használatával VNet pont – hely kapcsolat konfigurálva: PowerShell

Ez a cikk bemutatja, hogyan hozzon létre egy Vnetet egy pont – hely kapcsolat, amely a RADIUS-hitelesítést használ. Ez a konfiguráció esetén csak a Resource Manager üzembe helyezési modellben érhető el.

A pont–hely (P2S) VPN-átjáró lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. Pont-pont VPN-kapcsolatok hasznosak, ha szeretne csatlakozni egy távoli tartalomkiszolgálóról például ha vannak, a home vagy konferencia dolgozzon a virtuális hálózat. A pont–hely VPN emellett akkor is hasznos megoldás lehet a helyek közötti VPN helyett, ha csak néhány ügyfelet szeretne egy VNetre csatlakoztatni.

A pont–hely VPN-kapcsolat indítása a Windows- és Mac-eszközökről történik. A kapcsolódó ügyfelek az alábbi hitelesítési módszereket használhatják:

* RADIUS-kiszolgáló
* VPN-átjáró natív Tanúsítványalapú hitelesítés

Ez a cikk segítséget nyújt a P2S-konfiguráció, hitelesítéssel RADIUS-kiszolgáló konfigurálásához. Ha szeretné hitelesíteni a létrehozott tanúsítványok és a VPN-átjáró natív Tanúsítványalapú hitelesítés használatát, lásd: [egy virtuális hálózat VPN gateway natív tanúsítvány hitelesítése a pont-pont kapcsolat](vpn-gateway-howto-point-to-site-rm-ps.md).

![Kapcsolat diagram – RADIUS](./media/point-to-site-how-to-radius-ps/p2sradius.png)

A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet. Pont–hely kapcsolat esetén SSTP (Secure Socket Tunneling Protocol) vagy IKEv2-protokoll használatával jön létre a VPN-kapcsolat.

* Az SSTP egy SSL-alapú VPN-alagút, amely kizárólag Windows-ügyfélplatformokon támogatott. Képes áthatolni a tűzfalakon, ezért ideális megoldás az Azure-hoz való csatlakozáshoz bármilyen tetszőleges. A kiszolgálói oldalon az SSTP 1.0, 1.1 és 1.2 verziója támogatott. Az ügyfél dönti el, hogy melyik verziót használja. Windows 8.1 és újabb kiadások esetén az SSTP alapértelmezés szerint az 1.2 verziót használja.

* IKEv2 VPN, egy szabványalapú IPsec VPN-megoldás. Az IKEv2 VPN segítségével Macről is lehetségessé válik a csatlakozás (OSX 10.11-es vagy újabb verziók használata esetén).

A pont–hely kapcsolatokhoz a következőkre van szükség:

* Útvonalalapú VPN-átjáró. 
* Egy RADIUS-kiszolgáló felhasználói hitelesítés kezelésére. A RADIUS-kiszolgáló lehet a telepített helyszíni, vagy az Azure virtuális hálózatot.
* A VPN-konfigurációs ügyfélcsomag a Windows-eszközök, amelyek a virtuális hálózat csatlakozni fognak. A VPN-ügyfélcsomag konfigurációs biztosít a P2S keresztül csatlakozni egy VPN-ügyfél szükséges beállításokat.

## <a name="aboutad"></a>Az Active Directory (AD) tartományi hitelesítéshez P2S VPN kapcsolatos

Active Directory-tartománynak hitelesítés lehetővé teszi a felhasználóknak jelentkezzen be az Azure a szervezet tartományi hitelesítő adataik használatával. Egy RADIUS-kiszolgáló, amely integrálható az Active server igényel. A szervezetek is kihasználhatja a meglévő RADIUS-telepítésekhez.
 
A RADIUS-kiszolgáló lehetnek a helyszíni, vagy az Azure virtuális hálózat. A hitelesítés során a VPN-átjárót úgy működik, mint a csatlakoztatott és a továbbítást hitelesítési üzeneteket a RADIUS-kiszolgáló és a csatlakozó eszköz között. Fontos, a VPN-átjárót kell tudni érnie a RADIUS-kiszolgáló számára. Ha a RADIUS-kiszolgáló a helyszínen található, a VPN-helyek csatlakozni az Azure-ból a helyszíni hely szükség.

Active Directory, leszámítva a RADIUS-kiszolgáló integrálhatja más külső identitáskezelő rendszerekkel. Ekkor megnyílik a bőven hitelesítési beállítások a pont-pont VPN, beleértve az MFA-beállítások mentése. Ellenőrizze a RADIUS kiszolgáló gyártójának dokumentációjából identitáskezelési rendszereket, hogy integrálható a listájának lekérdezése.

![Kapcsolat diagram – RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Csak a VPN-helyek kapcsolat használható egy RADIUS-kiszolgálóval történő kapcsolódáshoz a helyszíni. Egy ExpressRoute-kapcsolat nem használható.
>
>

## <a name="before"></a>Megkezdése előtt

* Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

* Telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. A PowerShell-parancsmagok telepítéséről további információt a [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása) című témakörben talál.

### <a name="log-in"></a>Bejelentkezés

[!INCLUDE [Log in](../../includes/vpn-gateway-ps-login-include.md)]

### <a name="example"></a>Példaértékek

A példaértékek használatával létrehozhat egy tesztkörnyezetet, vagy a segítségükkel értelmezheti a cikkben szereplő példákat. Megteheti, hogy lépésről lépésre végighalad az eljáráson, és módosítás nélkül ezeket az értékeket használja, de módosíthatja is őket, hogy megfeleljenek a saját környezetének.

* **Név: VNet1**
* **Címtartomány: 192.168.0.0/16** és **10.254.0.0/16**<br>Ez a példa egynél több címtartományt használ annak szemléltetésére, hogy ez a konfiguráció több címtartománnyal is működik. Azonban nem kötelező több címtartományt megadni ehhez a konfigurációhoz.
* **Alhálózat neve: FrontEnd**
  * **Alhálózati címtartomány: 192.168.1.0/24**
* **Alhálózat neve: BackEnd**
  * **Alhálózati címtartomány: 10.254.1.0/24**
* **Alhálózat neve: GatewaySubnet**<br>Ennek az alhálózatnak kötelező a *GatewaySubnet* nevet adni, ellenkező esetben nem működik a VPN-átjáró.
  * **Átjáró-alhálózat címtartománya: 192.168.200.0/24** 
* **VPN-ügyfelek címkészlete: 172.16.201.0/24**<br>Azok a VPN-ügyfelek, amelyek ezzel a pont–hely kapcsolattal csatlakoznak a virtuális hálózathoz, a VPN-ügyfél címkészletből kapnak IP-címet.
* **Előfizetés:** Ha több előfizetése is van, ellenőrizze, hogy a megfelelőt használja-e.
* **Erőforráscsoport: TestRG**
* **Hely: East US**
* **DNS-kiszolgáló: IP-cím** , amelyet a virtuális hálózat a névfeloldáshoz használt DNS-kiszolgáló. (nem kötelező)
* **Átjáró neve: Vnet1GW**
* **Nyilvános IP-név: VNet1GWPIP**
* **VPN típusa: RouteBased** 

## 1. <a name="vnet"></a>Az erőforráscsoport, a virtuális hálózat és a nyilvános IP-cím létrehozása cím

Az alábbi lépéseket az erőforráscsoportot és egy virtuális hálózatot hozzon létre három alhálózattal rendelkező erőforráscsoportban. És az értékeket, fontos, hogy mindig nevezze el az átjáró alhálózatának kifejezetten a "GatewaySubnet". Ha Ön adjon neki nevet valami mást, az átjáró létrehozása sikertelen;

1. Hozzon létre egy erőforráscsoportot.

  ```powershell
  New-AzureRmResourceGroup -Name "TestRG" -Location "East US"
  ```
2. Hozza létre a virtuális hálózat alhálózatainak konfigurációit, névként a következő értékeket adja meg: *FrontEnd*, *BackEnd*, illetve *GatewaySubnet*. Ezek az előtagok a deklarált virtuális hálózati címtér részei kell, hogy legyenek.

  ```powershell
  $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
  $besub = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
  $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
  ```
3. Hozza létre a virtuális hálózatot.

  Ebben a példában a -DnsServer paramétert nem kötelező megadni. Az érték megadásával nem jön létre új DNS-kiszolgáló. A megadott DNS-kiszolgáló IP-címének olyan DNS-kiszolgálónak kell lennie, amely fel tudja oldani azoknak az erőforrásoknak a nevét, amelyekkel Ön kapcsolatot fog létesíteni a virtuális hálózatról. Ebben a példában egy magánhálózati IP-címet használtunk, de ez valószínűleg nem az Ön DNS-kiszolgálójának IP-címe. Ügyeljen arra, hogy a saját értékeit használja. A megadott érték nem a P2S-kapcsolat által a virtuális hálózat számára központilag telepített erőforrások használják.

  ```powershell
  New-AzureRmVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
  ```
4. Egy VPN Gateway-nek rendelkeznie kell nyilvános IP-címmel. Először az IP-cím típusú erőforrást kell kérnie, majd hivatkoznia kell arra, amikor létrehozza a virtuális hálózati átjárót. Az IP-címet a rendszer dinamikusan rendeli hozzá az erőforráshoz a VPN Gateway létrehozásakor. A VPN Gateway jelenleg csak a *Dinamikus* nyilvános IP-cím lefoglalását támogatja. Nem kérheti statikus IP-cím hozzárendelését. Ez azonban nem jelenti azt, hogy az IP-cím módosul a VPN Gateway-hez való hozzárendelése után. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

  Adja meg a változók egy dinamikusan hozzárendelt nyilvános IP-cím kéréséhez.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
  $pip = New-AzureRmPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
  ```

## 2. <a name="radius"></a>A RADIUS-kiszolgáló beállítása

Mielőtt létrehozna, és konfigurálja a virtuális hálózati átjárót, a RADIUS-kiszolgáló kell megfelelően beállítva a hitelesítés.

1. Ha nem rendelkezik telepített RADIUS-kiszolgáló, központi telepítése. A központi telepítés lépéseit tekintse meg a telepítés útmutatóját a RADIUS gyártója által biztosított.  
2. A VPN-átjáró konfigurálása a RADIUS a RADIUS-ügyfélként. A RADIUS-ügyfél hozzáadásakor adja meg a virtuális hálózatot GatewaySubnet létrehozott. 
3. Ha a RADIUS való működésre van beállítva, a RADIUS-kiszolgáló IP-cím és a közös titkos kulcsot, amely a RADIUS-ügyfelek által használandó kérdezze meg a RADIUS-kiszolgáló beolvasása. Ha a RADIUS-kiszolgáló az Azure virtuális hálózat, a CA IP-címét a RADIUS-kiszolgáló virtuális gép.

A [hálózati házirend-kiszolgáló (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) a cikk útmutatást nyújt a viturális AD tartományi hitelesítéshez egy Windows RADIUS-kiszolgáló (NPS) konfigurálása.

## 3. <a name="creategw"></a>A VPN-átjáró létrehozása

Konfigurálja, és hozzon létre a VPN-átjáró a virtuális hálózat.

* -GatewayType "Vpn" kell lennie, és – VpnType "RouteBased" kell lennie.
* VPN-átjáró is igénybe vehet akár 45 percet, attól függően, a [gateway SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku) választja.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## 4. <a name="addradius"></a>A RADIUS-kiszolgáló és az ügyfél címkészlet hozzáadása
 
* A - RadiusServer nevét vagy IP-cím adható meg. Adja meg a nevét, és a kiszolgáló helyszíni helyezkedik el, ha a VPN-átjáró előfordulhat, hogy nem tudják feloldani a nevet. Ha ez a helyzet, majd célszerűbb adhatja meg a kiszolgáló IP-címét. 
* A - RadiusSecret egyeznie kell a RADIUS-kiszolgálón konfigurált.
* A - VpnCientAddressPool az a tartomány, amelyből a csatlakozás VPN-ügyfelek IP-címet kap. Olyan magánhálózati IP-címtartományt használjon, amely nincs átfedésben azzal a helyszíni hellyel, amelyről csatlakozik, vagy azzal a virtuális hálózattal, amelyhez csatlakozik. Győződjön meg arról, hogy rendelkezik-e a megfelelő méretű-címkészlet megadására.  

1. Hozzon létre egy biztonságos karakterláncot kell megadnia a RADIUS-titkos.

  ```powershell
  $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
  ```

2. A RADIUS titkos kulcs megadását kéri. A beírt karakterek nem jelenik meg, és ehelyett váltja fel a "*" karaktert.

  ```powershell
  RadiusSecret:***
  ```
3. Adja hozzá a Magánhálózati ügyfélcímkészlete és a RADIUS-kiszolgáló adatai.

  Az SSTP-konfiguráció:

    ```powershell
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

  Az IKEv2-konfiguráció:

    ```powershell
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

  Az SSTP + IKEv2

    ```powershell
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

## 5. <a name="vpnclient"></a>Töltse le a VPN-ügyfélcsomag konfigurációs és a VPN-ügyfél beállítása

A VPN-ügyfél konfigurációja lehetővé teszi, hogy az eszközök egy P2S-kapcsolaton keresztül kapcsolódik a virtuális hálózat. Konfigurációs egy VPN-ügyfélcsomag létrehozása, és állítsa be a VPN-ügyfél, [hozzon létre egy VPN-ügyfél beállításainak konfigurálása a RADIUS-hitelesítés](point-to-site-vpn-client-configuration-radius.md).

## <a name="connect"></a>6. Csatlakozás az Azure szolgáltatáshoz

### <a name="to-connect-from-a-windows-vpn-client"></a>Csatlakozás Windows VPN-ügyfélről

1. Csatlakozzon a virtuális hálózathoz. Ehhez navigáljon az ügyfélszámítógépen a VPN-kapcsolatokhoz, és keresse meg a létrehozott VPN-kapcsolatot. Ugyanaz a neve, mint a virtuális hálózatnak. Adja meg a tartományi hitelesítő adatokat, és kattintson a "Csatlakozás". A kért emelt szintű jogosultságokkal előugró üzenet jelenik meg. Fogadja el, és adja meg a hitelesítő adatokat.

  ![A VPN-ügyfél az Azure-hoz csatlakozik](./media/point-to-site-how-to-radius-ps/client.png)
2. A kapcsolat létrejött.

  ![A kapcsolat létrejött](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Csatlakoztassa a Mac ügyfelek

A Hálózat párbeszédpanelen keresse meg a használni kívánt ügyfél profilját, majd kattintson a **Csatlakozás** gombra.

  ![Mac-kapcsolat](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>A kapcsolat ellenőrzése

1. Annak ellenőrzéséhez, hogy a VPN-kapcsolat aktív-e, nyisson meg egy rendszergazda jogú parancssort, és futtassa az *ipconfig/all* parancsot.
2. Tekintse meg az eredményeket. Figyelje meg, hogy a kapott IP-cím azok közül a címek közül való, amelyeket a pont–hely VPN-ügyfél konfigurációjának címkészletében megadott. Az eredmények az alábbi példában szereplőkhöz hasonlóak:

  ```
  PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
  ```

## <a name="connectVM"></a>Csatlakozás virtuális géphez

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a>GYIK

Ez a GYIK vonatkozik P2S RADIUS-hitelesítés használatával

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>További lépések

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). A hálózatok és virtuális gépek ismertetését lásd az [Azure- és Linux-alapú virtuálisgép-hálózatok áttekintésében](../virtual-machines/linux/azure-vm-network-overview.md).
