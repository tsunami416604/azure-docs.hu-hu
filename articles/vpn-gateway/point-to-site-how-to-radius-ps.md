---
title: 'Számítógép csatlakoztatása virtuális hálózathoz pont – hely és a RADIUS-hitelesítés használatával: PowerShell | Azure'
description: Windows és Mac OS X-ügyfelek biztonságos kapcsolatot P2S és a RADIUS-hitelesítést használó virtuális hálózatokhoz.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: cherylmc
ms.openlocfilehash: 86eb47f3ee1fa16d786a09bcafc103c70535a28f
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56983931"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>RADIUS-hitelesítés használatával virtuális hálózathoz pont – hely kapcsolat konfigurálása: PowerShell

Ez a cikk bemutatja, hogyan hozhat létre egy Vnetet a RADIUS-hitelesítést használó pont – hely kapcsolattal. Ez a konfiguráció csak a Resource Manager üzemi modell érhető el.

A pont–hely (P2S) VPN-átjáró lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. Pont – hely VPN-kapcsolatok akkor hasznos, ha szeretne csatlakozni egy távoli helyről, például amikor dolgozzon, az otthonról vagy konferenciáról, a virtuális hálózathoz. A pont–hely VPN emellett akkor is hasznos megoldás lehet a helyek közötti VPN helyett, ha csak néhány ügyfelet szeretne egy VNetre csatlakoztatni.

A pont–hely VPN-kapcsolat indítása a Windows- és Mac-eszközökről történik. A kapcsolódó ügyfelek az alábbi hitelesítési módszereket használhatják:

* RADIUS-kiszolgáló
* VPN-átjáró natív Tanúsítványalapú hitelesítés

Ez a cikk segít P2S konfiguráció konfigurálása a RADIUS-kiszolgálót használó hitelesítéssel. Ha szeretné hitelesíteni a létrehozott tanúsítványok és a VPN gateway natív Tanúsítványalapú hitelesítés használata esetén inkább, lásd: [VPN gateway natív tanúsítványalapú hitelesítésének használatával virtuális hálózathoz pont – hely kapcsolat konfigurálása](vpn-gateway-howto-point-to-site-rm-ps.md).

![Kapcsolati diagram – RADIUS](./media/point-to-site-how-to-radius-ps/p2sradius.png)

A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet. Pont–hely kapcsolat esetén SSTP (Secure Socket Tunneling Protocol) vagy IKEv2-protokoll használatával jön létre a VPN-kapcsolat.

* Az SSTP egy SSL-alapú VPN-alagút, amely kizárólag Windows-ügyfélplatformokon támogatott. Képes áthatolni a tűzfalakon, ezért ideális megoldás az Azure-hoz való csatlakozáshoz bármilyen tetszőleges. A kiszolgálói oldalon az SSTP 1.0, 1.1 és 1.2 verziója támogatott. Az ügyfél dönti el, hogy melyik verziót használja. Windows 8.1 és újabb kiadások esetén az SSTP alapértelmezés szerint az 1.2 verziót használja.

* IKEv2 VPN, egy szabványalapú IPsec VPN-megoldás. Az IKEv2 VPN segítségével Macről is lehetségessé válik a csatlakozás (OSX 10.11-es vagy újabb verziók használata esetén).

A pont–hely kapcsolatokhoz a következőkre van szükség:

* Útvonalalapú VPN-átjáró. 
* Egy RADIUS-kiszolgáló, a felhasználói hitelesítés kezeléséhez. A RADIUS-kiszolgáló lehet üzembe helyezhető a helyszínen, vagy az Azure virtuális hálózat.
* Egy VPN-ügyfélkonfigurációs csomagot a virtuális hálózathoz csatlakozó Windows-eszközökhöz. Egy VPN-ügyfélkonfigurációs csomagot biztosít egy VPN-ügyfél P2S keresztül csatlakozni a szükséges beállításokat.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="aboutad"></a>Tudnivalók az Active Directory (AD) tartományhitelesítés P2S VPN-EK

AD-tartományhitelesítés lehetővé teszi a felhasználóknak az Azure-bA bejelentkezni szervezeti tartományi hitelesítő adataik használatával. Egy RADIUS-kiszolgáló, amely integrálható az AD-kiszolgáló szükséges. Szervezetek is használhatják a meglévő RADIUS üzembe helyezésükben.
 
A RADIUS-kiszolgáló is található a helyszínen, vagy az Azure virtuális hálózat. A hitelesítés során a VPN-átjáró egy csatlakoztatott és a továbbított hitelesítési üzenetek oda-vissza a RADIUS-kiszolgáló és a csatlakozó eszköz közötti funkcionál. Fontos tudni elérni a RADIUS-kiszolgálót a VPN-átjáró számára. Ha a RADIUS-kiszolgálót a helyszínen található, a VPN--helyek közötti kapcsolat az Azure-ból a helyszíni helyre szükség.

Az Active Directory szereplőkkel RADIUS-kiszolgáló is integrálhatók a más külső identitáskezelő rendszerekkel. Ekkor megnyílik a bőségesen több hitelesítési lehetőség a pont – hely VPN-eket, beleértve az MFA-beállítások mentése. Ellenőrizze a RADIUS kiszolgáló gyártójának dokumentációjából integrálható az identitáskezelési rendszerek listájának beolvasása.

![Kapcsolati diagram – RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Csak egy VPN hely – hely kapcsolat RADIUS-kiszolgálón való kapcsolódáshoz használható a helyszínen. Az ExpressRoute-kapcsolatok nem használható.
>
>

## <a name="before"></a>Mielőtt hozzálát

Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example"></a>Példaértékek

A példaértékek használatával létrehozhat egy tesztkörnyezetet, vagy a segítségükkel értelmezheti a cikkben szereplő példákat. Megteheti, hogy lépésről lépésre végighalad az eljáráson, és módosítás nélkül ezeket az értékeket használja, de módosíthatja is őket, hogy megfeleljenek a saját környezetének.

* **név: VNet1**
* **Címtér: 192.168.0.0/16** és **10.254.0.0/16**<br>Ez a példa egynél több címtartományt használ annak szemléltetésére, hogy ez a konfiguráció több címtartománnyal is működik. Azonban nem kötelező több címtartományt megadni ehhez a konfigurációhoz.
* **Alhálózat neve: Előtér**
  * **Alhálózati címtartomány: 192.168.1.0/24**
* **Alhálózat neve: Háttér**
  * **Alhálózati címtartomány: 10.254.1.0/24**
* **Alhálózat neve: GatewaySubnet**<br>Ennek az alhálózatnak kötelező a *GatewaySubnet* nevet adni, ellenkező esetben nem működik a VPN-átjáró.
  * **Átjáróalhálózat címtartománya: 192.168.200.0/24** 
* **VPN-ügyfélcímkészlet: 172.16.201.0/24**<br>Azok a VPN-ügyfelek, amelyek ezzel a pont–hely kapcsolattal csatlakoznak a virtuális hálózathoz, a VPN-ügyfél címkészletből kapnak IP-címet.
* **Előfizetés:** Ha több előfizetéssel rendelkezik, győződjön meg arról, hogy a megfelelőt használja-e.
* **Erőforráscsoport: TestRG**
* **Hely: USA keleti RÉGIÓJA**
* **DNS-kiszolgáló: IP-cím** a névfeloldáshoz virtuális hálózatához használni kívánt DNS-kiszolgáló. (nem kötelező)
* **Átjáró neve: Vnet1GW**
* **Nyilvános IP-név: VNet1GWPIP**
* **VpnType: Útvonalalapú**


## <a name="signin"></a>Jelentkezzen be, és a változók beállítása

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

### <a name="declare-variables"></a>Változók deklarálása

Deklarálja a használni kívánt változókat. Használja a következő példát, és szükség szerint cserélje le az értékeket a sajátjaira. A gyakorlat során bezárta a PowerShell vagy a Cloud Shell munkamenet bármikor leállítható, ha egyszerűen másolja be az értékeket ismét a újra deklarálja a változókat.

  ```azurepowershell-interactive
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## 1. <a name="vnet"></a>Az erőforráscsoport, a virtuális hálózat és a nyilvános IP-cím létrehozása címe

Az alábbi lépéseket hozzon létre egy erőforráscsoportot és a egy virtuális hálózatot három alhálózattal az erőforráscsoportot. Értékek behelyettesítésekor fontos, hogy Ön mindig az átjáróalhálózat neve "GatewaySubnet". Ha Ön nevezi el, az átjáró létrehozása sikertelen lesz;

1. Hozzon létre egy erőforráscsoportot.

  ```azurepowershell-interactive
  New-AzResourceGroup -Name "TestRG" -Location "East US"
  ```
2. Hozza létre a virtuális hálózat alhálózatainak konfigurációit, névként a következő értékeket adja meg: *FrontEnd*, *BackEnd*, illetve *GatewaySubnet*. Ezek az előtagok a deklarált virtuális hálózati címtér részei kell, hogy legyenek.

  ```azurepowershell-interactive
  $fesub = New-AzVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
  $besub = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
  $gwsub = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
  ```
3. Hozza létre a virtuális hálózatot.

  Ebben a példában a -DnsServer paramétert nem kötelező megadni. Az érték megadásával nem jön létre új DNS-kiszolgáló. A megadott DNS-kiszolgáló IP-címének olyan DNS-kiszolgálónak kell lennie, amely fel tudja oldani azoknak az erőforrásoknak a nevét, amelyekkel Ön kapcsolatot fog létesíteni a virtuális hálózatról. Ebben a példában egy magánhálózati IP-címet használtunk, de ez valószínűleg nem az Ön DNS-kiszolgálójának IP-címe. Ügyeljen arra, hogy a saját értékeit használja. A megadott érték nem a P2S-kapcsolat által a virtuális hálózaton üzembe helyezett erőforrások használják.

  ```azurepowershell-interactive
  New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
  ```
4. Egy VPN Gateway-nek rendelkeznie kell nyilvános IP-címmel. Először az IP-cím típusú erőforrást kell kérnie, majd hivatkoznia kell arra, amikor létrehozza a virtuális hálózati átjárót. Az IP-címet a rendszer dinamikusan rendeli hozzá az erőforráshoz a VPN Gateway létrehozásakor. A VPN Gateway jelenleg csak a *Dinamikus* nyilvános IP-cím lefoglalását támogatja. Nem kérheti statikus IP-cím hozzárendelését. Ez azonban nem jelenti azt, hogy az IP-cím módosul a VPN Gateway-hez való hozzárendelése után. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

  Adja meg a változók egy dinamikusan hozzárendelt nyilvános IP-cím kéréséhez.

  ```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
  $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
  $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
  ```

## 2. <a name="radius"></a>A RADIUS-kiszolgáló beállítása

Létrehozásához, és a virtuális hálózati átjáró konfigurálásához, mielőtt a RADIUS-kiszolgáló kell megfelelően beállítva a hitelesítés.

1. Ha nem rendelkezik telepített RADIUS-kiszolgáló, üzembe helyezéséhez. A telepítési lépéseket tekintse meg a RADIUS gyártója által biztosított beállítási útmutatóhoz.  
2. A VPN-átjáró konfigurálása a RADIUS a RADIUS-ügyfélként. A RADIUS-ügyfél hozzáadásakor adja meg a virtuális hálózati átjáró-alhálózat, amelyet Ön hozott létre. 
3. A RADIUS-kiszolgáló be van állítva, ha a RADIUS-kiszolgáló IP-cím és a közös titkos kulcsot, amelyet használnia kell a RADIUS-ügyfelek való kommunikációhoz a RADIUS-kiszolgáló kaphat. Ha a RADIUS-kiszolgáló az Azure virtuális hálózat, használja a CA IP-címét a RADIUS-kiszolgáló virtuális Géphez.

A [hálózati házirend-kiszolgáló (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) a cikk egy Windows RADIUS-kiszolgáló (NPS) az AD-tartomány hitelesítés konfigurálásával kapcsolatos útmutatást nyújt.

## 3. <a name="creategw"></a>A VPN-átjáró létrehozása

Állítsa be, és a VPN-átjáró létrehozása a virtuális hálózat számára.

* A - GatewayType "Vpn" kell lennie, és a - vpntype pedig a "RouteBased" kell lennie.
* VPN-átjáró is igénybe vehet legfeljebb 45 percig, attól függően, a [átjáró-Termékváltozatot](vpn-gateway-about-vpn-gateway-settings.md#gwsku) választja.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## 4. <a name="addradius"></a>A RADIUS-kiszolgáló és ügyfél-címkészlet hozzáadása
 
* A - RadiusServer nevével vagy IP-cím adható meg. Ha a kiszolgáló található, a helyszíni nevét adja meg, majd a VPN-átjáró nem lehet képes feloldani a nevet. Ha ez a helyzet, majd akkor célszerűbb adja meg a kiszolgáló IP-címét. 
* A - RadiusSecret egyeznie kell a RADIUS-kiszolgálón konfigurált.
* A - VpnClientAddressPool a tartomány, amelyből a csatlakozó VPN-ügyfelek IP-címet kapnak. Olyan magánhálózati IP-címtartományt használjon, amely nincs átfedésben azzal a helyszíni hellyel, amelyről csatlakozik, vagy azzal a virtuális hálózattal, amelyhez csatlakozik. Győződjön meg arról, hogy rendelkezik-e elég nagy címkészlet megadására.  

1. Hozzon létre egy biztonságos karakterláncot a RADIUS titkos.

  ```azurepowershell-interactive
  $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
  ```

2. Adja meg a RADIUS-titok kéri. Azt adja meg a következő karakterek nem jelennek meg, és ehelyett lesz lecserélve az "*" karaktert.

  ```azurepowershell-interactive
  RadiusSecret:***
  ```
3. Adja hozzá a VPN-ügyfélcímkészlet és a RADIUS-kiszolgáló adatait.

  Az SSTP-konfiguráció:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

  Az IKEv2-konfiguráció:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

  For SSTP + IKEv2

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

## 5. <a name="vpnclient"></a>A VPN-ügyfél konfigurációs csomagjának letöltése és a VPN-ügyfél beállítása

A VPN-ügyfél konfigurációja lehetővé teszi, hogy az eszközök csatlakoztatása virtuális hálózathoz P2S-kapcsolaton keresztül. Egy VPN-ügyfél konfigurációs csomagjának létrehozása és a VPN-ügyfél beállítása: [hozzon létre egy VPN-ügyfél konfigurációja a RADIUS-hitelesítés](point-to-site-vpn-client-configuration-radius.md).

## <a name="connect"></a>6. Csatlakozás az Azure szolgáltatáshoz

### <a name="to-connect-from-a-windows-vpn-client"></a>Csatlakozás Windows VPN-ügyfélről

1. Csatlakozzon a virtuális hálózathoz. Ehhez navigáljon az ügyfélszámítógépen a VPN-kapcsolatokhoz, és keresse meg a létrehozott VPN-kapcsolatot. Ugyanaz a neve, mint a virtuális hálózatnak. Adja meg a tartományi hitelesítő adatait, és kattintson a "Kapcsolódás" elemre. Emelt szintű jogosultságokkal rendelkeznek kér egy előugró üzenet jelenik meg. Fogadja el, és adja meg a hitelesítő adatait.

  ![A VPN-ügyfél az Azure-hoz csatlakozik](./media/point-to-site-how-to-radius-ps/client.png)
2. A kapcsolat létrejött.

  ![A kapcsolat létrejött](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Csatlakozás Mac VPN-ügyfélről

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

A P2S-kapcsolat hibaelhárítása: [pont – hely kapcsolatok hibaelhárítása Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

## <a name="connectVM"></a>Csatlakozás virtuális géphez

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a>GYIK

Ez a GYIK vonatkozik a P2S RADIUS-hitelesítés használatával

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>További lépések

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/). A hálózatok és virtuális gépek ismertetését lásd az [Azure- és Linux-alapú virtuálisgép-hálózatok áttekintésében](../virtual-machines/linux/azure-vm-network-overview.md).
