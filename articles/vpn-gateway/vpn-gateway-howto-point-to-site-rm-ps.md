---
title: 'Számítógép csatlakoztatása egy Azure virtuális hálózat pont – hely és az Azure natív tanúsítványalapú hitelesítésének használatával: PowerShell |} A Microsoft Docs'
description: Windows- vagy Mac OS X-ügyfelek biztonságos csatlakoztatása Azure-beli virtuális hálózathoz, a pont–hely kapcsolat és önaláírt vagy hitelesítésszolgáltató által kibocsátott tanúsítványok használatával. Ebben a cikkben a PowerShellt fogjuk használni.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: cherylmc
ms.openlocfilehash: deba3962adb6d1fed8c52376c9c953a96bd6ce4e
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699091"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>Azure natív tanúsítványalapú hitelesítésének használatával virtuális hálózathoz pont – hely kapcsolat konfigurálása: PowerShell

Ez a cikk ahhoz nyújt segítséget, hogyan lehet biztonságosan csatlakoztatni Windowst vagy Mac OS X-et futtató egyedi ügyfeleket egy Azure-beli virtuális hálózathoz. A pont–hely VPN-kapcsolat akkor hasznos, ha távoli helyről szeretne csatlakozni a virtuális hálózathoz, például otthonról vagy egy konferenciáról. Pont–hely kapcsolatot is használhat helyek közötti VPN helyett, ha csak néhány ügyfelet szeretne egy virtuális hálózathoz csatlakoztatni. A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet. Pont–hely kapcsolat esetén SSTP (Secure Socket Tunneling Protocol) vagy IKEv2-protokoll használatával jön létre a VPN-kapcsolat. További információkat a pont–hely VPN-ről a [pont–hely VPN-t ismertető](point-to-site-about.md) témakör tartalmaz.

![Számítógép csatlakoztatása Azure-beli virtuális hálózathoz – pont-hely kapcsolati diagram](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)

## <a name="architecture"></a>Architektúra

A natív Azure-tanúsítvánnyal hitelesített pont–hely kapcsolatok a következő elemeket használják, amelyeket külön konfigurálni kell:

* Útvonalalapú VPN-átjáró.
* A nyilvános kulcs (.cer fájl) egy főtanúsítványhoz, amely az Azure-ba van feltöltve. A tanúsítványt a feltöltését követően megbízható tanúsítványnak tekinti a rendszer, és ezt használja hitelesítéshez.
* A főtanúsítványból létrejött ügyféltanúsítvány, amely a virtuális hálózathoz csatlakozó egyes ügyfélszámítógépekre telepített ügyféltanúsítvány. A rendszer ezt a tanúsítványt használja ügyfélhitelesítéshez.
* A VPN-ügyfél konfigurációja. A VPN-ügyfélkonfigurációs fájlok azokat az adatokat tartalmazzák, amelyekre az ügyfélnek szüksége van a virtuális hálózathoz való csatlakozáshoz. A csomag konfigurálja az operációs rendszer meglévő, natív VPN-ügyfelét. Minden csatlakozó ügyfelet a konfigurációs fájlokban szereplő beállításokkal kell konfigurálni.

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example"></a>Példaértékek

A példaértékek használatával létrehozhat egy tesztkörnyezetet, vagy a segítségükkel értelmezheti a cikkben szereplő példákat. A változókat a cikk [1](#declare). szakaszában állítjuk be. Megteheti, hogy lépésről lépésre végighalad az eljáráson, és módosítás nélkül ezeket az értékeket használja, de módosíthatja is őket, hogy megfeleljenek a saját környezetének.

* **név: VNet1**
* **Címtér: 192.168.0.0/16** és **10.254.0.0/16**<br>Ez a példa egynél több címteret használ annak szemléltetésére, hogy ez a konfiguráció több címtérrel is működik. Azonban nem kötelező több címtartományt megadni ehhez a konfigurációhoz.
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
* **DNS-kiszolgáló: IP-cím** a névfeloldáshoz használni kívánt DNS-kiszolgáló. (nem kötelező)
* **Átjáró neve: Vnet1GW**
* **Nyilvános IP-név: VNet1GWPIP**
* **VpnType: Útvonalalapú** 

## <a name="declare"></a>1. Jelentkezzen be, és a változók beállítása

Ebben a szakaszban jelentkezzen be, és ehhez a konfigurációhoz használt értékek deklarálását ismerteti. A minta parancsprogramok a deklarált értékeket használják. Módosítsa az értékeket úgy, hogy megfeleljenek a saját környezetének. Azt is megteheti, hogy a deklarált értékeket használja, és gyakorlásként halad végig a lépéseken.

### <a name="sign-in"></a>Bejelentkezés

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

### <a name="declare-variables"></a>Változók deklarálása

Deklarálja a használni kívánt változókat. Használja a következő példát, és szükség szerint cserélje le az értékeket a sajátjaira.

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

## <a name="ConfigureVNet"></a>2. Virtuális hálózat konfigurálása

1. Hozzon létre egy erőforráscsoportot.

  ```azurepowershell-interactive
  New-AzureRmResourceGroup -Name $RG -Location $Location
  ```
2. Hozza létre a virtuális hálózat alhálózatainak konfigurációit, névként a következő értékeket adja meg: *FrontEnd*, *BackEnd*, illetve *GatewaySubnet*. Ezek az előtagok a deklarált virtuális hálózati címtér részei kell, hogy legyenek.

  ```azurepowershell-interactive
  $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
  $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
  $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
  ```
3. Hozza létre a virtuális hálózatot.

  Ebben a példában a -DnsServer paramétert nem kötelező megadni. Az érték megadásával nem jön létre új DNS-kiszolgáló. A megadott DNS-kiszolgáló IP-címének olyan DNS-kiszolgálónak kell lennie, amely fel tudja oldani azoknak az erőforrásoknak a nevét, amelyekkel Ön kapcsolatot fog létesíteni a virtuális hálózatról. Ez a példa egy magánhálózati IP-címet használ, de ez valószínűleg nem az Ön DNS-kiszolgálójának IP-címe. Ügyeljen arra, hogy a saját értékeit használja. A megadott értéket a virtuális hálózaton üzembe helyezett erőforrások használják, nem a pont–hely kapcsolat vagy a VPN-ügyfél.

  ```azurepowershell-interactive
  New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
  ```
4. Adja meg a most létrehozott virtuális hálózat változóit.

  ```azurepowershell-interactive
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  ```
5. Egy VPN Gateway-nek rendelkeznie kell nyilvános IP-címmel. Először az IP-cím típusú erőforrást kell kérnie, majd hivatkoznia kell arra, amikor létrehozza a virtuális hálózati átjárót. Az IP-címet a rendszer dinamikusan rendeli hozzá az erőforráshoz a VPN Gateway létrehozásakor. A VPN Gateway jelenleg csak a *Dinamikus* nyilvános IP-cím lefoglalását támogatja. Nem kérheti statikus IP-cím hozzárendelését. Ez azonban nem jelenti azt, hogy az IP-cím módosul a VPN Gatewayhez való hozzárendelése után. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

  Kérjen egy dinamikusan hozzárendelt nyilvános IP-címet.

  ```azurepowershell-interactive
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```

## <a name="creategateway"></a>3. A VPN-átjáró létrehozása

Konfigurálja és hozza létre a virtuális hálózati átjárót a virtuális hálózat számára.

* A -GatewayType csak **Vpn** lehet, a -VpnType pedig csak **RouteBased** lehet.
* A -VpnClientProtocol paraméterrel adhatja meg az engedélyezni kívánt alagutak típusát. Alagutak esetén **SSTP** és **IKEv2** közül választhat. Engedélyezheti csak az egyiket, vagy egyszerre mindkettőt. Ha mindkettőt engedélyezni szeretné, akkor adja meg mindkét nevet, vesszővel elválasztva. Az Android- és Linux-alapú strongSwan-ügyfél, valamint az iOS- és OS X-alapú natív IKEv2 VPN-ügyfél csak IKEv2-alagutat használ a kapcsolódáshoz. A Windows-ügyfél először az IKEv2-vel próbálkozik, majd ha azzal nem sikerült, visszavált SSTP-re.
* A virtuális hálózati átjáró "Alapszintű" Termékváltozat nem támogatja az IKEv2- vagy RADIUS-hitelesítés. Ha azt tervezi, hogy a Mac ügyfelek csatlakoznak a virtuális hálózat, ne használja az alapszintű Termékváltozat.
* Egy VPN-átjáró létrehozása akár 45 percet is igénybe vehet a kiválasztott [átjáró termékváltozatától](vpn-gateway-about-vpn-gateway-settings.md) függően. Ez a példa az IKEv2-t használja.

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
```

## <a name="addresspool"></a>4. A VPN-ügyfélcímkészlet hozzáadása

Miután befejeződött a VPN-átjáró létrehozása, hozzáadhatja a VPN-ügyfélcímkészletet. A VPN-ügyfélcímkészlet az a tartomány, amelyből a VPN-ügyfelek IP-címet kapnak csatlakozáskor. Olyan magánhálózati IP-címtartományt használjon, amely nincs átfedésben azzal a helyszíni hellyel, amelyről csatlakozik, vagy azzal a virtuális hálózattal, amelyhez csatlakozik. Ebben a példában a VPN-ügyfélcímkészlet [változóként](#declare) lett deklarálva az 1. lépésben.

```azurepowershell-interactive
$Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="Certificates"></a>5. Tanúsítványok előállítása

A tanúsítványokat az Azure használja a VPN-ügyfelek hitelesítésére a pont–hely VPN-kapcsolatokban. A főtanúsítvány nyilvánoskulcs-adatait feltölti az Azure-ba. A nyilvános kulcs ezután „megbízhatónak” tekinthető. Az ügyféltanúsítványokat a megbízható főtanúsítványból kell létrehozni, majd telepíteni kell az összes számítógépen a Certificates-Current User/Personal tanúsítványtárolóban. A tanúsítványt a rendszer az ügyfél hitelesítésére használja, amikor az a virtuális hálózathoz próbál csatlakozni. 

Önaláírt tanúsítványok használata esetén azokat megadott paraméterekkel kell létrehozni. Önaláírt tanúsítványt a [PowerShell és Windows 10](vpn-gateway-certificates-point-to-site.md), vagy ha nem rendelkezik Windows 10 rendszerrel, a [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) című cikkekben leírt utasítások alapján hozhat létre. Fontos, hogy az önaláírt legfelső szintű tanúsítványok és az ügyféltanúsítványok generálása során lépésről lépésre betartsa ezeket az utasításokat. Ellenkező esetben a létrehozott tanúsítványok nem lesznek kompatibilisek a P2S-kapcsolatokkal, és hibaüzenetet eredményeznek kapcsolódáskor.

### <a name="cer"></a>1. A .cer fájl beszerzése a főtanúsítványhoz

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]


### <a name="generate"></a>2. Ügyféltanúsítvány létrehozása

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload"></a>6. A főtanúsítvány nyilvánoskulcs-adatainak feltöltése

Ellenőrizze, hogy a VPN-átjáró létrehozása befejeződött-e. Ha befejeződött, töltse fel a megbízható főtanúsítványhoz tartozó .cer fájlt (amely a nyilvános kulcsot tartalmazza) az Azure-ba. Miután feltöltötte a .cer fájlt, az Azure felhasználhatja azt azon ügyfelek hitelesítéséhez, amelyeken telepítve lett egy, a megbízható főtanúsítványból létrehozott ügyféltanúsítvány. Szükség szerint később további megbízhatófőtanúsítvány-fájlokat is feltölthet (legfeljebb 20-at).

1. Deklarálja a tanúsítványnév változóját, és cserélje le az értékeket a saját értékeire.

  ```azurepowershell-interactive
  $P2SRootCertName = "P2SRootCert.cer"
  ```
2. Helyettesítse a fájl elérési útját a sajátjával, majd futtassa a parancsmagokat.

  ```azurepowershell-interactive
  $filePathForCert = "C:\cert\P2SRootCert.cer"
  $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
  $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
  $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
  ```
3. Töltse fel a nyilvánoskulcs-adatokat az Azure-ba. A tanúsítványadatok feltöltését követően az Azure megbízható főtanúsítványnak tekinti ezt.

  ```azurepowershell-interactive
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $CertBase64
  ```

## <a name="clientcertificate"></a>7. Exportált ügyféltanúsítvány telepítése

Ha a tanúsítvány létrehozásához használttól eltérő ügyfélszámítógépről szeretne pont–hely kapcsolatot létesíteni, akkor telepítenie kell egy ügyféltanúsítványt. Az ügyféltanúsítvány telepítésekor szükség lesz az ügyféltanúsítvány exportálásakor létrehozott jelszóra.

Győződjön meg arról, hogy az ügyféltanúsítványt .pfx fájlként exportálta a teljes tanúsítványlánccal együtt (ez az alapértelmezett beállítás). Egyéb esetben a főtanúsítvány adatai nem lesznek jelen az ügyfélszámítógépen, és az ügyfél nem fogja tudni megfelelően elvégezni a hitelesítést. 

A telepítés lépései az [ügyféltanúsítvány telepítésével](point-to-site-how-to-vpn-client-install-azure-cert.md) foglalkozó részben találhatók.

## <a name="clientconfig"></a>8. A natív VPN-ügyfél konfigurálása

A VPN-ügyfél konfigurációs fájljai tartalmazzák az eszközök azon beállításait, amelyekkel pont–hely kapcsolaton keresztül kapcsolódhatnak egy virtuális hálózathoz. A VPN-ügyfél konfigurációs fájljainak létrehozásához és telepítéséhez szükséges utasításokért lásd:[A VPN-ügyfél konfigurációs fájljainak létrehozása és telepítése az Azure natív tanúsítványalapú hitelesítést használó pont–hely kapcsolatokhoz](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="connect"></a>9. Csatlakozás az Azure szolgáltatáshoz

### <a name="to-connect-from-a-windows-vpn-client"></a>Csatlakozás Windows VPN-ügyfélről

>[!NOTE]
>Rendszergazdai jogosultsággal kell rendelkeznie azon a Windows ügyfélszámítógépen, ahonnan csatlakozik.
>
>

1. Csatlakozzon a virtuális hálózathoz. Ehhez navigáljon az ügyfélszámítógépen a VPN-kapcsolatokhoz, és keresse meg a létrehozott VPN-kapcsolatot. Ugyanaz a neve, mint a virtuális hálózatnak. Kattintson a **Connect** (Csatlakozás) gombra. Megjelenhet egy előugró üzenet, amely a tanúsítvány használatára utal. Kattintson a **Folytatás** gombra emelt szintű jogosultságok használatához. 
2. A csatlakozás megkezdéséhez a **Kapcsolat** állapotlapon kattintson a **Csatlakozás** gombra. Ha megjelenik a **Tanúsítvány kiválasztása** képernyő, ellenőrizze, hogy az a csatlakozáshoz használni kívánt ügyféltanúsítványt mutatja-e. Ha nem, kattintson a legördülő nyílra, válassza ki a helyes tanúsítványt, majd kattintson az **OK** gombra.

  ![A VPN-ügyfél az Azure-hoz csatlakozik](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. A kapcsolat létrejött.

  ![A kapcsolat létrejött](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

#### <a name="troubleshooting-windows-client-p2s-connections"></a>Windows-ügyél pont–hely kapcsolatainak hibaelhárítása

[!INCLUDE [client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Csatlakozás Mac VPN-ügyfélről

A Hálózat párbeszédpanelen keresse meg a használni kívánt ügyfél profilját, majd kattintson a **Csatlakozás** gombra.
Ellenőrizze [telepítő - (OS X) Mac](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac) részletes útmutatást. Ha problémába ütközik a csatlakozás, ellenőrizze, hogy a virtuális hálózati átjáró nem használ egy alapszintű Termékváltozat. Alapszintű Termékváltozat nem támogatott Mac számítógépekhez.

  ![Mac-kapcsolat](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>A kapcsolat ellenőrzése

Ezek az utasítások Windows-ügyfelekre érvényesek.

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

Ezek az utasítások Windows-ügyfelekre érvényesek.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="addremovecert"></a>Főtanúsítvány hozzáadása vagy eltávolítása

A megbízható főtanúsítványokat felveheti vagy el is távolíthatja az Azure-ban. Főtanúsítvány eltávolításakor a főtanúsítványból létrehozott tanúsítvánnyal rendelkező ügyfelek nem fognak tudni hitelesítést végezni, így csatlakozni sem. Ha azt szeretné, hogy az ügyfelek hitelesítést végezhessenek és csatlakozni tudjanak, telepítenie kell egy olyan új ügyféltanúsítványt, amelyet az Azure által megbízhatónak tartott (feltöltött) főtanúsítványból hoztak létre.

### <a name="addtrustedroot"></a>Megbízható főtanúsítvány hozzáadása

Az Azure-ba legfeljebb 20 főtanúsítványt tölthet fel .cer fájl formájában. A következő lépések segítségével adhat hozzá főtanúsítványt:

#### <a name="certmethod1"></a>1. módszer:

Ez a főtanúsítvány feltöltésének leghatékonyabb módszere.

1. Készítse elő a .cer fájlt feltöltésre:

  ```azurepowershell-interactive
  $filePathForCert = "C:\cert\P2SRootCert3.cer"
  $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
  $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
  $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64_3
  ```
2. Töltse fel a fájlt. Egyszerre csak egy fájlt tölthet fel.

  ```azurepowershell-interactive
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $CertBase64_3
  ```

3. A tanúsítványfájl sikeres feltöltésének ellenőrzése:

  ```azurepowershell-interactive
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

#### <a name="certmethod2"></a>2. módszer:

Ez a módszer több lépésből áll, mint az 1. módszer, de az eredménye ugyanaz. Arra az esetre szerepel itt, ha meg kellene tekintenie a tanúsítványadatokat.

1. Hozza létre és készítse elő az Azure-ba felvenni kívánt új főtanúsítványt. Exportálja a nyilvános kulcsot Base-64-kódolású X.509 (.CER) formátumban, és nyissa meg egy egyszerű szövegszerkesztőben. Másolja a példányokat az alábbi példában látható módon.

  ![tanúsítvány](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

  > [!NOTE]
  > A tanúsítványadatok másolásakor a szöveget egy folyamatos sorként másolja kocsivissza vagy új sor nélkül. A kocsivisszák és az új sorok megjelenítéséhez lehet, hogy módosítania kell a nézetet a szövegszerkesztőben a „Szimbólum megjelenítése/Minden karakter megjelenítése” beállításra.
  >
  >

2. Adja meg a tanúsítvány nevét és a kulcs adatait egy változóként. Helyettesítse az adatokat a saját adataival az alábbi példában látható módon:

  ```azurepowershell-interactive
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
3. Adja hozzá a megbízható főtanúsítványt. Egyszerre csak egy tanúsítványt adhat hozzá.

  ```azurepowershell-interactive
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
4. A következő példa használatával ellenőrizheti, hogy helyesen ment-e végbe a tanúsítvány hozzáadása:

  ```azurepowershell-interactive
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

### <a name="removerootcert"></a>Főtanúsítvány eltávolítása

1. Deklarálja a változókat.

  ```azurepowershell-interactive
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
2. Távolítsa el a tanúsítványt.

  ```azurepowershell-interactive
  Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
3. A következő példával meggyőződhet arról, hogy sikeresen megtörtént a tanúsítvány eltávolítása.

  ```azurepowershell-interactive
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

## <a name="revoke"></a>Ügyféltanúsítvány visszavonása

Az ügyféltanúsítványokat vissza lehet vonni. A visszavont tanúsítványok listájával az egyes ügyféltanúsítványok alapján, szelektíven tagadhatja meg a pont–hely kapcsolódás lehetőségét. Ez a folyamat eltér a megbízható főtanúsítvány eltávolításától. Ha töröl egy .cer formátumú megbízható főtanúsítványt az Azure-ból, azzal megvonja a hozzáférést minden olyan ügyféltanúsítványtól, amelyet a visszavont főtanúsítvánnyal hoztak létre/írtak alá. A főtanúsítvány helyett az ügyféltanúsítvány visszavonása esetén a főtanúsítványból létrehozott többi tanúsítvány továbbra is használható hitelesítésre.

A szokásos gyakorlat az, hogy a főtanúsítvánnyal kezelik a hozzáférést a munkacsoport vagy a szervezet szintjén, az egyes felhasználókra vonatkozó részletesebb szabályozást pedig visszavont ügyféltanúsítványokkal oldják meg.

### <a name="revokeclientcert"></a>Ügyféltanúsítvány visszavonása

1. Kérje le az ügyféltanúsítvány ujjlenyomatát. További információkat [a tanúsítványok ujjlenyomatának lekérését ismertető útmutatóban](https://msdn.microsoft.com/library/ms734695.aspx) találhat.
2. Másolja át az adatokat egy szövegszerkesztőbe, és távolítsa el az összes szóközt, hogy egy folyamatos sztringet kapjon. Ez a sztring a következő lépésben változóként van deklarálva.
3. Deklarálja a változókat. Győződjön meg róla, hogy az előző lépésben lekért ujjlenyomatot deklarálja.

  ```azurepowershell-interactive
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
4. Vegye fel az ujjlenyomatot a visszavont tanúsítványok listájára. Az ujjlenyomat hozzáadása után a „Sikeres” üzenet jelenik meg.

  ```azurepowershell-interactive
  Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
  -Thumbprint $RevokedThumbprint1
  ```
5. Ellenőrizze, hogy az ujjlenyomat bekerült-e a visszavont tanúsítványok listájába.

  ```azurepowershell-interactive
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```
6. Az ujjlenyomat hozzáadását követően a tanúsítvány már nem használható csatlakozáshoz. Azok az ügyfelek, akik ezzel a tanúsítvánnyal próbálnak csatlakozni, egy üzenetet kapnak majd arról, hogy a tanúsítvány már nem érvényes.

### <a name="reinstateclientcert"></a>Ügyféltanúsítvány érvényességének visszaállítása

Vissza is állíthatja az ügyféltanúsítványok érvényességét. Ehhez törölni kell az ujjlenyomatukat a visszavont ügyféltanúsítványok listájából.

1. Deklarálja a változókat. Győződjön meg arról, hogy a megfelelő, a visszaállítani kívánt tanúsítványhoz tartozó ujjlenyomatot deklarálta.

  ```azurepowershell-interactive
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
2. Törölje a tanúsítvány ujjlenyomatát a visszavont tanúsítványok listájából.

  ```azurepowershell-interactive
  Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
  ```
3. Ellenőrizze, hogy megtörtént-e az ujjlenyomat eltávolítása a listából.

  ```azurepowershell-interactive
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```

## <a name="faq"></a>Pont–hely kapcsolatok – gyakori kérdések

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>További lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/). A hálózatok és virtuális gépek ismertetését lásd az [Azure- és Linux-alapú virtuálisgép-hálózatok áttekintésében](../virtual-machines/linux/azure-vm-network-overview.md).

P2S hibaelhárítási információkért [hibaelhárítás: Az Azure pont – hely kapcsolati problémák](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
