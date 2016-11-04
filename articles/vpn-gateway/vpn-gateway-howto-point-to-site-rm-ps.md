---
title: Pont–hely VPN-kapcsolat konfigurálása virtuális hálózathoz a Resource Manager üzemi modell használatával | Microsoft Docs
description: Biztonságosan csatlakozhat az Azure Virtual Networkhöz pont–hely VPN Gateway-kapcsolat létrehozásával.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2016
ms.author: cherylmc

---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>Pont–hely kapcsolat konfigurálása virtuális hálózathoz a PowerShell segítségével
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Klasszikus – Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> * [Klasszikus – Klasszikus portál](vpn-gateway-point-to-site-create.md)
> 
> 

A pont–hely (P2S) konfiguráció lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat akkor hasznos, ha távoli helyről szeretne csatlakozni a virtuális hálózathoz, például otthonról vagy konferenciáról, vagy akkor, ha csak néhány ügyfelet kíván csatlakoztatni a virtuális hálózathoz. 

Ez a cikk végigkalauzolja azokon a lépéseken, amelyekkel pont–hely kapcsolatot rendelkező virtuális hálózatot hozhat létre a **Resource Manager üzemi modellen belül**. A lépésekhez a PowerShell szükséges. Ez a megoldás egyelőre nem konfigurálható teljes egészében az Azure Portal segítségével.

A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet a működéshez. VPN-kapcsolat létesítéséhez manuálisan kell kezdeményezni a kapcsolatot az ügyfélszámítógépről. További információk a pont–hely kapcsolatokról: [VPN Gateway – gyakori kérdések](vpn-gateway-vpn-faq.md#point-to-site-connections) és [Tudnivalók a létesítmények közötti kapcsolatokról](vpn-gateway-plan-design.md).

**Tudnivalók az Azure üzembehelyezési modellekről**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**Üzembe helyezési modellek és eszközök a pont–hely kapcsolatokhoz**

[!INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

![Pont–hely diagram](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-to-site")

## <a name="about-this-configuration"></a>A konfiguráció bemutatása
Ez az útmutató azt ismerteti, hogyan hozhat létre pont–hely kapcsolattal rendelkező virtuális hálózatot. Az útmutatás segít az ehhez a konfigurációhoz szükséges tanúsítványok előállításában is. A pont–hely kapcsolatot a következő elemek alkotják: egy VPN-átjárót tartalmazó virtuális hálózat, egy főtanúsítvány (.cer fájl formájában, ez a nyilvános kulcs), egy ügyféltanúsítvány és a VPN-konfigurációja az ügyfélen. 

Ehhez a konfigurációhoz a következő értékeket használjuk: A változókat a cikk [1](#declare). szakaszában állítjuk be. Megteheti, hogy lépésről lépésre végighalad az eljáráson, és módosítás nélkül ezeket az értékeket használja, de módosíthatja is őket, hogy megfeleljenek a saját környezetének. 

* Név: **VNet1**, amely a **192.168.0.0/16** és a **10.254.0.0/16** címteret használja. Felhívjuk figyelmét, hogy egy virtuális hálózathoz több címteret is használhat.
* Alhálózat neve: **FrontEnd** (ez a **192.168.1.0/24** címteret használja)
* Alhálózat neve: **BackEnd** (ez a **10.254.1.0/24** címteret használja)
* Alhálózat neve: **GatewaySubnet** (ez a **192.168.200.0/24** címteret használja) Ennek az alhálózatnak kötelező a *GatewaySubnet* nevet adni, ellenkező esetben nem működik az átjáró. 
* VPN-ügyfelek címkészlete: **172.16.201.0/24**. Azok a VPN-ügyfelek, amelyek ezzel a pont–hely kapcsolattal csatlakoznak a virtuális hálózathoz, ebből a címkészletből kapnak IP-címet.
* Előfizetés: Ha több előfizetése is van, ellenőrizze, hogy a megfelelőt használja-e.
* Erőforráscsoport: **TestRG**
* Hely: **East US**
* DNS-kiszolgáló: Annak a DNS-kiszolgálónak az **IP-címe**, amelyet névfeloldásra kíván használni.
* Átjáró neve: **GW**
* Nyilvános IP-név: **GWIP**
* VPN típusa: **RouteBased**

## <a name="before-beginning"></a>Mielőtt hozzálát
* Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Telepítse az Azure Resource Manager PowerShell-parancsmagjait (az 1.0.2-es vagy újabb verziót). A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](../powershell-install-configure.md). Ahhoz, hogy ezt a konfigurációt elvégezhesse, a PowerShellt rendszergazdaként kell futtatnia. 

## <a name="declare"></a>1. rész – Bejelentkezés és a változók beállítása
Ez a szakasz a bejelentkezést és a konfigurációban használt értékek deklarálását ismerteti. A minta parancsprogramok a deklarált értékeket használják. Módosítsa az értékeket úgy, hogy megfeleljenek a saját környezetének. Azt is megteheti, hogy a deklarált értékeket használja, és gyakorlásként halad végig a lépéseken.

1. Nyissa meg a PowerShell konzolt, és jelentkezzen be az Azure-fiókjába. Ez a parancsmag kéri az Azure-fiók bejelentkezési hitelesítő adatait. A bejelentkezés után letölti a fiók beállításait, hogy elérhetők legyenek az Azure PowerShell számára.
   
        Login-AzureRmAccount 
2. Olvassa be az Azure-előfizetések listáját.
   
        Get-AzureRmSubscription
3. Válassza ki a használni kívánt előfizetést. 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"
4. Deklarálja a használni kívánt változókat. Használja a következő példát, és szükség szerint cserélje le az értékeket a sajátjaira.
   
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
        $DNS = "8.8.8.8"
        $GWName = "GW"
        $GWIPName = "GWIP"
        $GWIPconfName = "gwipconf"
        $P2SRootCertName = "ARMP2SRootCert.cer"

## <a name="part-2---configure-a-vnet"></a>2. rész – Virtuális hálózat konfigurálása
1. Hozzon létre egy erőforráscsoportot.
   
        New-AzureRmResourceGroup -Name $RG -Location $Location
2. Hozza létre a virtuális hálózat alhálózatainak konfigurációit, névként a következő értékeket adja meg: *FrontEnd*, *BackEnd*, illetve *GatewaySubnet*. Ezek az előtagok a deklarált virtuális hálózati címtér részei kell, hogy legyenek.
   
        $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
        $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
        $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
3. Hozza létre a virtuális hálózatot. A megadott DNS-kiszolgálónak olyan DNS-kiszolgálónak kell lennie, amely fel tudja oldani azoknak az erőforrásoknak a nevét, amellyel Ön kapcsolatot fog létesíteni. Ebben a példában egy nyilvános IP-címet használtunk. Ügyeljen arra, hogy a saját értékeit használja.
   
        New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS
4. Adja meg a most létrehozott virtuális hálózat változóit.
   
        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
5. Kérjen egy dinamikusan hozzárendelt nyilvános IP-címet. Ez az IP-cím szükséges ahhoz, hogy a hálózat megfelelően működjön. Később fogja összekapcsolni az átjárót az átjáró IP-címkonfigurációjával.
   
        $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## <a name="part-3---add-trusted-certificates"></a>3. rész – Megbízható tanúsítványok hozzáadása
Az Azure tanúsítvánnyal hitelesíti azokat az ügyfeleket, amelyek pont–hely konfigurációt használnak. Importálnia kell a .cer fájlt (a nyilvános kulcsot) főtanúsítványként az Azure-ba. Amikor hozzáad egy Base64-kódolású X.509 (.cer) fájlt az Azure-hoz, arra utasítja az Azure-t, hogy bízzon meg a fájl által képviselt főtanúsítványban.

Nagyvállalati megoldás esetén használhatja meglévő tanúsítványláncát. Ha nem használ vállalati hitelesítésszolgáltatói (CA) megoldást, létrehozhat egy önaláírt főtanúsítványt. Az önaláírt tanúsítvány létrehozásának egyik módja a makecert. Az önaláírt főtanúsítványnak a *makecert* segítségével történő létrehozásáról a [Working with self-signed root certificates for Point-to-Site configurations](vpn-gateway-certificates-point-to-site.md) (Önaláírt főtanúsítványok használata pont–hely konfigurációk esetében) című cikk nyújt tájékoztatást.

Bárhogyan jut is a tanúsítványhoz, fel kell töltenie a tanúsítvány .cer fájlját az Azure-ba, és elő kell állítania az ügyféltanúsítványokat, majd telepítenie kell őket azokra az ügyfelekre, amelyek csatlakozni fognak. Nem magát az önaláírt tanúsítványt kell telepíteni az ügyfelekre. Az ügyféltanúsítványokat később, a cikk [Ügyfél-konfiguráció](#cc) szakaszában is telepítheti.

Az Azure-ra legfeljebb 20 megbízható tanúsítványt tölthet fel. A nyilvános kulcsot úgy állíthatja elő, hogy Base64-kódolású X.509 (.cer) fájlként exportálja tanúsítványt. A .cer fájlként való exportálás egyik módja a **certmgr.msc** konzol használata. Nyissa meg, és keresse meg a tanúsítványt a Személyes/Tanúsítványok tárolóban. Kattintson rá a jobb gombbal, és exportálja a titkos kulcs nélkül, „Base64 kódolású X.509 (.CER)” formátumban. Jegyezze fel azt az elérési utat, ahová exportálta a .cer fájlt. Ez a minta azt mutatja be, hogyan lehet beszerezni a tanúsítvány Base64-kódolású karakterláncos leképezését. 

Vegye fel a megbízható tanúsítványt az Azure-ba. Ehhez a lépéshez azt az elérési utat kell használnia, amelyre exportálta saját .cer fájlját. Különösen ügyeljen a cikk 1. részében beállított $P2SRootCertName = "ARMP2SRootCert.cer" változóra. Ha nem ez a tanúsítvány neve, annak megfelelően módosítsa a változót.

        $filePathForCert = "pasteYourCerFilePathHere"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="part-4---create-the-vpn-gateway"></a>4. lépés – A VPN-átjáró létrehozása
Konfigurálja és hozza létre a virtuális hálózati átjárót a virtuális hálózat számára. A *-GatewayType* csak **Vpn** lehet, a *-VpnType* pedig csak **RouteBased** lehet. Ennek végrehajtása 45 percig is eltarthat.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="part-5---download-the-vpn-client-configuration-package"></a>5. rész: A VPN-ügyfél konfigurációs csomagjának letöltése
Az Azure-hoz pont–hely kapcsolattal csatlakozó ügyfelekre az ügyféltanúsítványon kívül telepíteni kell a VPN-ügyfélkonfigurációs csomagot is. A windowsos ügyfelekhez érhetők el VPN-ügyfélkonfigurációs csomagok. A VPN-ügyfélcsomag arra vonatkozó információkat tartalmaz, hogy miképpen kell konfigurálnia Windows beépített VPN-ügyfélszoftverét, és konkrétan arra a VPN-re vonatkozik, amellyel kapcsolatot létesít. A csomag nem telepít további szoftvert. További információk: [VPN Gateway – gyakori kérdések](vpn-gateway-vpn-faq.md#point-to-site-connections).

1. Az átjáró létrehozása után a következő példa alapján töltheti le az ügyfél-konfigurációs csomagot:
   
        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
2. A PowerShell-parancsmag egy URL-hivatkozást ad vissza. Íme egy példa a visszaadott URL-címre:
   
        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
3. A csomag letöltéséhez másolja a visszaadott hivatkozást, és illessze be egy böngészőbe. A csomag telepítése az ügyfélszámítógépre.
4. Nyissa meg az ügyfélszámítógépen a **Hálózati beállítások** eszközt, és kattintson a **VPN** elemre. A listán láthatja a kapcsolatot. Annak a virtuális hálózatnak a nevét mutatja, amellyel kapcsolatot létesít, és következő példára hasonlít: 
   
    ![VPN-ügyfél](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "VPN client")

## <a name="cc"></a>6. rész – Az ügyféltanúsítvány létrehozása
Ezután állítsa elő az ügyféltanúsítványokat. Létrehozhat egy egyedi tanúsítványt minden csatlakozó ügyfél számára, vagy használhatja ugyanazt a tanúsítványt több ügyfél esetén. Az egyedi ügyféltanúsítványok előállításának előnye az, hogy szükség esetén visszavonhat egyetlen tanúsítványt. Ha azonban mindenki ugyanazt az ügyféltanúsítványt használja, és úgy találja, hogy egyetlen ügyféltől vissza kell vonnia a tanúsítványt, az összes olyan ügyfél számára elő kell állítania és telepítenie kell új tanúsítványokat, amelyek az adott tanúsítványt használják a hitelesítéshez.

* Ha vállalati tanúsítványmegoldást használ, az általános 'name@yourdomain.com', formátumban hozza létre az ügyféltanúsítványokat a NetBIOS „TARTOMÁNY\felhasználónév” formátuma helyett. 
* Ha önaláírt tanúsítványt használ, az ügyféltanúsítvány létrehozásával kapcsolatban tekintse meg a [Working with self-signed root certificates for Point-to-Site configurations](vpn-gateway-certificates-point-to-site.md) (Önaláírt főtanúsítványok használata pont–hely konfigurációk esetében).

## <a name="part-7---install-the-client-certificate"></a>7. rész – Az ügyféltanúsítvány telepítése
Telepítsen egy ügyféltanúsítványt minden olyan számítógépen, amelyet csatlakoztatni szeretne a virtuális hálózathoz. A hitelesítéshez ügyféltanúsítványra van szükség. Az ügyféltanúsítványt telepítheti automatikusan vagy manuálisan. A következő lépések végigvezetik az ügyféltanúsítvány manuális exportálásán és telepítésén.

1. Az ügyféltanúsítványok exportálásához a *certmgr.msc* fájlt használhatja. Kattintson a jobb gombbal az exportálni kívánt ügyféltanúsítványra, majd a **minden feladat** és az **exportálás** elemre.
2. Exportálja az ügyféltanúsítványt a titkos kulccsal. Ez egy *.pfx* fájl. Jegyezze fel vagy jegyezze meg a jelszót (kulcsot), amelyet beállított a tanúsítványhoz.
3. Másolja a *.pfx* fájlt az ügyfélszámítógépre. Az ügyfélszámítógépen kattintson duplán a *.pfx* fájlra annak telepítéséhez. Amikor a rendszer kéri, adja meg a jelszót. Ne módosítsa a telepítés helyét.

## <a name="part-8---connect-to-azure"></a>8. rész – Csatlakozás az Azure-hoz
1. Csatlakozzon a virtuális hálózathoz. Ehhez navigáljon az ügyfélszámítógépen a VPN-kapcsolatokhoz, és keresse meg a létrehozott VPN-kapcsolatot. Ugyanaz a neve, mint a virtuális hálózatnak. Kattintson a **Connect** (Csatlakozás) gombra. Megjelenhet egy előugró üzenet, amely a tanúsítvány használatára utal. Ilyen esetében kattintson a **Folytatás** gombra emelt szintű jogosultságok használatához. 
2. A csatlakozás megkezdéséhez a **Kapcsolat** állapotlapon kattintson a **Csatlakozás** gombra. Ha megjelenik a **Tanúsítvány kiválasztása** képernyő, ellenőrizze, hogy az a csatlakozáshoz használni kívánt ügyféltanúsítványt mutatja-e. Ha nem, kattintson a legördülő nyílra, válassza ki a helyes tanúsítványt, majd kattintson az **OK** gombra.
   
    ![2. VPN-ügyfél](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "VPN client connection")
3. Ekkor létre kell jönnie a kapcsolatnak.
   
    ![3. VPN-ügyfél](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "VPN client connection 2")

## <a name="part-9---verify-your-connection"></a>9. lépés – A kapcsolat ellenőrzése
1. Annak ellenőrzéséhez, hogy a VPN-kapcsolat aktív-e, nyisson meg egy rendszergazda jogú parancssort, és futtassa az *ipconfig/all* parancsot.
2. Tekintse meg az eredményeket. Figyelje meg, hogy a kapott IP-cím azok közül a címek közül való, amelyeket a pont–hely VPN-ügyfél konfigurációjának címkészletében megadott. Az eredmények a következőhöz hasonlóak:
   
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

## <a name="to-add-or-remove-a-trusted-root-certificate"></a>Megbízható főtanúsítvány hozzáadása vagy eltávolítása
A tanúsítványok a VPN-ügyfelek hitelesítésére használatosak a pont–hely VPN-kapcsolatokban. A következő lépések végigvezetik a főtanúsítványok hozzáadásának és eltávolításának folyamatán. Amikor hozzáad egy Base64-kódolású X.509 (.cer) fájlt az Azure-hoz, arra utasítja az Azure-t, hogy bízzon meg a fájl által képviselt főtanúsítványban. 

A megbízható főtanúsítvány hozzáadására és eltávolítására használhatja a PowerShellt, illetve az Azure Portal webhelyet. Ha az Azure Portal segítségével szeretné elvégezni, nyissa meg a **virtuális hálózati átjáró > beállítások > Pont-hely konfiguráció > Főtanúsítványok** területet. A következő lépések végigvezetik ezen feladatok PowerShell használatával való elvégzésén. 

### <a name="add-a-trusted-root-certificate"></a>Megbízható főtanúsítvány hozzáadása
Az Azure-ra legfeljebb 20 megbízható főtanúsítványt tölthet fel .cer fájl formájában. A következő lépésekkel tölthet fel főtanúsítványt.

1. Hozza létre és készítse elő az új főtanúsítványt, amelyet fel fog tölteni az Azure-ba. Exportálja a nyilvános kulcsot Base-64-kódolású X.509 (.CER) formátumban, és nyissa meg egy egyszerű szövegszerkesztőben. Másolja belőle az alább látható szakaszt. 
   
    Másolja a példányokat az alábbi példában látható módon.
   
    ![tanúsítvány](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "certificate")
2. Adja meg a tanúsítvány nevét és a kulcs adatait egy változóként. Helyettesítse az adatokat a saját adataival az alábbi példában látható módon:
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
3. Adja hozzá a megbízható főtanúsítványt. Egyszerre csak egy tanúsítványt adhat hozzá.
   
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
4. A következő parancsmaggal ellenőrizheti, hogy helyesen ment-e végbe a tanúsítvány hozzáadása.
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "GW"

### <a name="to-remove-a-trusted-root-certificate"></a>Megbízható főtanúsítvány eltávolítása
A megbízható főtanúsítványt eltávolíthatja az Azure-ból. Ha eltávolít egy megbízható tanúsítványt, akkor az abból a főtanúsítványból generált ügyféltanúsítványok nem tudnak többé pont–hely kapcsolattal csatlakozni az Azure-hoz. Ha azt szeretné, hogy az ügyfelek csatlakozni tudjanak, telepíteniük kell egy olyan új ügyféltanúsítvány, amelyet az Azure által megbízhatónak tartott tanúsítványból generáltak.

1. Megbízható főtanúsítvány eltávolításához módosítsa a következő mintát:
   
    Deklarálja a változókat.
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
2. Távolítsa el a tanúsítványt.
   
        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
3. A következő parancsmaggal meggyőződhet róla, hogy sikeresen megtörtént a tanúsítvány eltávolítása. 
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "GW"

## <a name="to-manage-the-list-of-revoked-client-certificates"></a>A visszavont ügyféltanúsítványok listájának kezelése
Az ügyféltanúsítványokat vissza lehet vonni. A visszavont tanúsítványok listájával az egyes ügyféltanúsítványok alapján, szelektíven tagadhatja meg a pont–hely kapcsolódás lehetőségét. Ha töröl egy .cer formátumú főtanúsítványt az Azure-ból, azzal megvonja a hozzáférést minden olyan ügyféltanúsítványtól, amelyet a visszavont főtanúsítvánnyal generáltak/írtak alá. Ha egy konkrét ügyféltanúsítványt szeretne visszavonni, és nem a főtanúsítványt, erre is lehetősége nyílik. Ily módon továbbra is érvényesek marad a főtanúsítványból generált többi tanúsítvány. A szokásos gyakorlat az, hogy a főtanúsítvánnyal kezelik a hozzáférést a munkacsoport vagy a szervezet szintjén, az egyes felhasználókra vonatkozó részletesebb szabályozást pedig visszavont ügyféltanúsítványokkal oldják meg.

### <a name="revoke-a-client-certificate"></a>Ügyféltanúsítvány visszavonása
1. Keresse meg a visszavonni kívánt tanúsítvány ujjlenyomatát.
   
        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"
2. Vegye fel az ujjlenyomatot a visszavont ujjlenyomatok listájára.
   
        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
3. Ellenőrizze, hogy az ujjlenyomat bekerült-e a visszavont tanúsítványok listájába. Egyszerre egy ujjlenyomatot vehet fel a listába.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### <a name="reinstate-a-client-certificate"></a>Ügyféltanúsítvány érvényességének visszaállítása
Vissza is állíthatja az ügyféltanúsítványok érvényességét. Ehhez törölni kell az ujjlenyomatukat a visszavont ügyféltanúsítványok listájából.

1. Törölje az ujjlenyomatot a visszavont ügyféltanúsítványok ujjlenyomatát tartalmazó listából.
   
       Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
       -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
2. Ellenőrizze, hogy megtörtént-e az ujjlenyomat eltávolítása a listából.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## <a name="next-steps"></a>Következő lépések
Felvehet virtuális gépet a virtuális hálózatba. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

<!--HONumber=Oct16_HO3-->


