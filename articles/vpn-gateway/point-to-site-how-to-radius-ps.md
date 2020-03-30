---
title: 'Számítógép csatlakoztatása virtuális hálózathoz pont–hely és RADIUS-hitelesítés használatával: PowerShell | Azure'
description: P2S- és RADIUS-hitelesítéssel biztonságosan csatlakoztathatja a Windows és A Mac OS X ügyfeleket egy virtuális hálózathoz.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: 25bc25d9ec12804cc20baa558dce67fb3f8269a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77149154"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Pont-hely kapcsolat konfigurálása virtuális hálózattal RADIUS-hitelesítés használatával: PowerShell

Ez a cikk bemutatja, hogyan hozhat létre radius-hitelesítést használó point-to-site kapcsolattal rendelkező virtuális hálózatot. Ez a konfiguráció csak az Erőforrás-kezelő telepítési modelljéhez érhető el.

A pont–hely (P2S) VPN-átjáró lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont-hely VPN-kapcsolatok akkor hasznosak, ha távoli helyről szeretne csatlakozni a virtuális hálózathoz, például amikor otthonról vagy konferenciáról távmunkát kezdeményez. A pont–hely VPN emellett akkor is hasznos megoldás lehet a helyek közötti VPN helyett, ha csak néhány ügyfelet szeretne egy VNetre csatlakoztatni.

A pont–hely VPN-kapcsolat indítása a Windows- és Mac-eszközökről történik. A kapcsolódó ügyfelek az alábbi hitelesítési módszereket használhatják: 

* RADIUS-kiszolgáló
* VPN-átjáró natív tanúsítványának hitelesítése

Ez a cikk segít a P2S-konfiguráció konfigurálása radius-kiszolgálón történő hitelesítéssel. Ha ehelyett létrehozott tanúsítványok és VPN-átjárónatív tanúsítványhitelesítéssel szeretné hitelesíteni magát, olvassa el a [Pont-hely kapcsolat konfigurálása a virtuális hálózattal vpn-átjáró natív tanúsítványhitelesítésével](vpn-gateway-howto-point-to-site-rm-ps.md)című témakört.

![Kapcsolatdiagram - RADIUS](./media/point-to-site-how-to-radius-ps/p2sradius.png)

A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet. A P2S a VPN-kapcsolatot SSTP (Secure Socket Tunneling Protocol), OpenVPN vagy IKEv2 protokollal hozza létre.

* Az SSTP egy SSL-alapú VPN-alagút, amely kizárólag Windows-ügyfélplatformokon támogatott. Képes áthatolni a tűzfalakon, ezért ideális megoldás az Azure-hoz való csatlakozáshoz bármilyen tetszőleges. A kiszolgálói oldalon az SSTP 1.0, 1.1 és 1.2 verziója támogatott. Az ügyfél dönti el, hogy melyik verziót használja. Windows 8.1 és újabb kiadások esetén az SSTP alapértelmezés szerint az 1.2 verziót használja.

* OpenVPN® Protokoll, egy SSL/ TLS alapú VPN protokoll. Az SSL VPN-megoldás áthatolhat a tűzfalakon, mivel a legtöbb tűzfal megnyitja a 443-as TCP-portot, amelyet az SSL használ. OpenVPN lehet használni, hogy csatlakozzon az Android, iOS (11.0-s és újabb verziók), Windows, Linux és Mac eszközök (OSX verzió10.13 és újabb).

* IKEv2 VPN, egy szabványalapú IPsec VPN-megoldás. Az IKEv2 VPN segítségével Macről is lehetségessé válik a csatlakozás (OSX 10.11-es vagy újabb verziók használata esetén).

A pont–hely kapcsolatokhoz a következőkre van szükség:

* Útvonalalapú VPN-átjáró. 
* A felhasználói hitelesítést kezelő RADIUS-kiszolgáló. A RADIUS-kiszolgáló a helyszínen vagy az Azure virtuális hálózatban is telepíthető.
* Vpn-ügyfél konfigurációs csomag a virtuális hálózathoz csatlakozó Windows-eszközökhöz. A VPN-ügyfél konfigurációs csomag biztosítja a VPN-ügyfél P2S-en keresztül ihez való csatlakozásához szükséges beállításokat.

## <a name="about-active-directory-ad-domain-authentication-for-p2s-vpns"></a><a name="aboutad"></a>Az Active Directory (AD) tartományi hitelesítése p2s VPN-hez

Az AD-tartomány hitelesítése lehetővé teszi a felhasználók számára, hogy a szervezeti tartomány hitelesítő adataival jelentkezzenek be az Azure-ba. Olyan RADIUS-kiszolgálóra van szükség, amely integrálható az AD-kiszolgálóval. A szervezetek a meglévő RADIUS-telepítésüket is kihasználhatják.
 
A RADIUS-kiszolgáló a helyszínen vagy az Azure virtuális hálózatban is eltalálható. A hitelesítés során a VPN-átjáró átmenő ként működik, és továbbítja a hitelesítési üzeneteket oda-vissza a RADIUS-kiszolgáló és a csatlakozó eszköz között. Fontos, hogy a VPN-átjáró elérje a RADIUS-kiszolgálót. Ha a RADIUS-kiszolgáló a helyszínen található, akkor az Azure és a helyszíni hely közötti VPN-kapcsolat szükséges.

Az Active Directory n kívül a RADIUS-kiszolgáló más külső identitáskezelő rendszerekkel is integrálható. Ez számos hitelesítési lehetőséget nyit meg a pont-hely VPN-ek számára, beleértve az MFA-beállításokat is. Ellenőrizze a RADIUS-kiszolgáló gyártójának dokumentációját, hogy leolvassa az identitásrendszerek listáját, amelyekkel integrálható.

![Kapcsolatdiagram - RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>A helyszíni RADIUS-kiszolgálóhoz való csatlakozáshoz csak VPN-helyek közötti kapcsolat használható. ExpressRoute-kapcsolat nem használható.
>
>

## <a name="before-beginning"></a><a name="before"></a>Mielőtt hozzálát

Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell együttműködése

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>Példaértékek

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
* **Helyszín: USA keleti része**
* **DNS-kiszolgáló:** Annak a DNS-kiszolgálónak az IP-címe, amelyet a virtuális hálózat névfeloldásához kíván használni. (nem kötelező)
* **Átjáró neve: Vnet1GW**
* **Nyilvános IP-név: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="1-set-the-variables"></a><a name="signin"></a>1. Állítsa be a változókat

Deklarálja a használni kívánt változókat. Használja a következő példát, és szükség szerint cserélje le az értékeket a sajátjaira. Ha bezárja a PowerShell/Cloud Shell munkamenet bármely pontján az edzés során, csak másolja és illessze be az értékeket újra deklarálni a változók.

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

## <a name="2-create-the-resource-group-vnet-and-public-ip-address"></a>2. <a name="vnet"> </a>Az erőforráscsoport, a virtuális hálózat és a nyilvános IP-cím létrehozása

A következő lépések hozzon létre egy erőforráscsoportot és egy virtuális hálózatot az erőforráscsoportban három alhálózattal. Értékek helyettesítésekénél fontos, hogy mindig az átjáró alhálózatát nevezze el kifejezetten "GatewaySubnet".In subtuting values, it's important that you always name your gateway subnet specifically 'GatewaySubnet'. Ha valami másnak nevezi el, az átjáró létrehozása sikertelen;

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

   Ebben a példában a -DnsServer paramétert nem kötelező megadni. Az érték megadásával nem jön létre új DNS-kiszolgáló. A megadott DNS-kiszolgáló IP-címének olyan DNS-kiszolgálónak kell lennie, amely fel tudja oldani azoknak az erőforrásoknak a nevét, amelyekkel Ön kapcsolatot fog létesíteni a virtuális hálózatról. Ebben a példában egy magánhálózati IP-címet használtunk, de ez valószínűleg nem az Ön DNS-kiszolgálójának IP-címe. Ügyeljen arra, hogy a saját értékeit használja. A megadott értéket a virtuális hálózatra üzembe helyezett erőforrások használják, nem pedig a P2S-kapcsolat.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. Egy VPN Gateway-nek rendelkeznie kell nyilvános IP-címmel. Először az IP-cím típusú erőforrást kell kérnie, majd hivatkoznia kell arra, amikor létrehozza a virtuális hálózati átjárót. Az IP-címet a rendszer dinamikusan rendeli hozzá az erőforráshoz a VPN Gateway létrehozásakor. A VPN Gateway jelenleg csak a *Dinamikus* nyilvános IP-cím lefoglalását támogatja. Nem kérheti statikus IP-cím hozzárendelését. Ez azonban nem jelenti azt, hogy az IP-cím módosul a VPN Gateway-hez való hozzárendelése után. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

   Adja meg azokat a változókat, amelyek dinamikusan hozzárendelt nyilvános IP-címet kérnek.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
   $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="3-set-up-your-radius-server"></a>3. <a name="radius"> </a>A RADIUS-kiszolgáló beállítása

A virtuális hálózati átjáró létrehozása és konfigurálása előtt a RADIUS-kiszolgálót megfelelően kell konfigurálni a hitelesítéshez.

1. Ha nincs telepítve RADIUS-kiszolgáló, telepítsen egyet. A központi telepítési lépésekről a RADIUS-szállító által biztosított telepítési útmutatóban tájékyak.  
2. Konfigurálja a VPN-átjárót RADIUS-ügyfélként a RADIUS-on. A RADIUS-ügyfél hozzáadásakor adja meg a létrehozott virtuális hálózati GatewaySubnet-et. 
3. A RADIUS-kiszolgáló beállítása után szerezd meg a RADIUS-kiszolgáló IP-címét és azt a megosztott titkot, amelyet a RADIUS-ügyfeleknek a RADIUS-kiszolgálóval való beszélgetéshez kell használniuk. Ha a RADIUS-kiszolgáló az Azure virtuális hálózatban található, használja a RADIUS-kiszolgáló virtuális gépének hitelesítési és hitelesítéshálózati IP-címét.

A [Hálózati házirend-kiszolgáló (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) cikk útmutatást nyújt a Windows RADIUS-kiszolgáló (NPS) AD tartományhitelesítéshez történő konfigurálásához.

## <a name="4-create-the-vpn-gateway"></a>4. <a name="creategw"> </a>A VPN-átjáró létrehozása

Konfigurálja és hozza létre a VPN-átjárót a virtuális hálózathoz.

* A -GatewayType típusnak "Vpn" típusúnak kell lennie, a -VpnType típusnak pedig "RouteBased" típusúnak.
* A VPN-átjáró befejezése akár 45 percet is igénybe vehet, a kiválasztott [átjáró termékváltozattól](vpn-gateway-about-vpn-gateway-settings.md#gwsku) függően.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## <a name="5-add-the-radius-server-and-client-address-pool"></a>5. <a name="addradius"> </a>Adja hozzá a RADIUS-kiszolgálót és az ügyfélcímkészletet
 
* A -RadiusServer megadható név vagy IP-cím alapján. Ha megadja a nevet, és a kiszolgáló a helyszínen található, akkor előfordulhat, hogy a VPN-átjáró nem tudja feloldani a nevet. Ha ez a helyzet, akkor jobb, ha megadja a kiszolgáló IP-címét. 
* A -RadiusSecret értéknek meg kell egyeznie a RADIUS-kiszolgálón konfigurált értékekkel.
* A -VpnClientAddressPool az a tartomány, amelyből a csatlakozó VPN-ügyfelek IP-címet kapnak.Olyan magánhálózati IP-címtartományt használjon, amely nincs átfedésben azzal a helyszíni hellyel, amelyről csatlakozik, vagy azzal a virtuális hálózattal, amelyhez csatlakozik. Győződjön meg arról, hogy elég nagy címkészlet van konfigurálva.  

1. Hozzon létre egy biztonságos karakterláncot a RADIUS-titkosítmányhoz.

   ```azurepowershell-interactive
   $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
   ```

2. A rendszer kéri, hogy adja meg a RADIUS-titkot. A beírt karakterek nem jelennek meg, hanem a "*" karakter váltja fel őket.

   ```azurepowershell-interactive
   RadiusSecret:***
   ```
3. Adja hozzá a VPN-ügyfélcímkészletet és a RADIUS-kiszolgáló adatait.

   SSTP-konfigurációk esetén:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   OpenVPN® konfigurációk esetén:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "OpenVPN" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```


   IKEv2 konfigurációk esetén:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   SSTP + IKEv2 esetén

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

## <a name="6-download-the-vpn-client-configuration-package-and-set-up-the-vpn-client"></a>6. <a name="vpnclient"> </a>Töltse le a VPN-ügyfél konfigurációs csomagját, és állítsa be a VPN-ügyfelet

A VPN-ügyfél konfigurációlehetővé teszi, hogy az eszközök P2S-kapcsolaton keresztül csatlakozzanak a virtuális hálózathoz.Vpn-ügyfélkonfigurációs csomag létrehozásához és a VPN-ügyfél beállításához olvassa el a [VPN-ügyfélkonfiguráció létrehozása radius-hitelesítéshez című témakört.](point-to-site-vpn-client-configuration-radius.md)

## <a name="7-connect-to-azure"></a><a name="connect"></a>7. Csatlakozás az Azure-hoz

### <a name="to-connect-from-a-windows-vpn-client"></a>Csatlakozás Windows VPN-ügyfélről

1. Csatlakozzon a virtuális hálózathoz. Ehhez navigáljon az ügyfélszámítógépen a VPN-kapcsolatokhoz, és keresse meg a létrehozott VPN-kapcsolatot. Ugyanaz a neve, mint a virtuális hálózatnak. Adja meg a tartományi hitelesítő adatokat, és kattintson a "Csatlakozás" gombra. Megjelenik egy előugró üzenet, amely emelt szintű jogokat kér. Fogadja el, és adja meg a hitelesítő adatokat.

   ![A VPN-ügyfél az Azure-hoz csatlakozik](./media/point-to-site-how-to-radius-ps/client.png)
2. A kapcsolat létrejött.

   ![A kapcsolat létrejött](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Csatlakozás Mac VPN-ügyfélről

A Hálózat párbeszédpanelen keresse meg a használni kívánt ügyfél profilját, majd kattintson a **Csatlakozás** gombra.

  ![Mac-kapcsolat](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-your-connection"></a><a name="verify"></a>A kapcsolat ellenőrzése

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

A P2S-kapcsolatok hibaelhárításához olvassa el az [Azure pont-hely közötti kapcsolatok hibaelhárítása című témakört.](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Csatlakozás virtuális géphez

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a><a name="faq"></a>GYIK

Ez a gyakori kérdések a RADIUS-hitelesítést használó P2S-re vonatkoznak

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>További lépések

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/). A hálózatok és virtuális gépek ismertetését lásd az [Azure- és Linux-alapú virtuálisgép-hálózatok áttekintésében](../virtual-machines/linux/azure-vm-network-overview.md).
