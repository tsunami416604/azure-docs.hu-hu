---
title: "Számítógép csatlakoztatása Azure-beli virtuális hálózathoz pont–hely kapcsolat használatával: PowerShell | Microsoft Docs"
description: "Biztonságosan csatlakoztathat egy számítógépet az Azure Virtual Networkhöz pont–hely VPN Gateway-kapcsolat létrehozásával."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: d340210d799f995cb10a20cf48a9245bbd3bc8d3
ms.lasthandoff: 04/27/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>Pont–hely kapcsolat konfigurálása virtuális hálózathoz a PowerShell segítségével
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Klasszikus – Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

A pont–hely (P2S) konfiguráció lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat egy SSTP (Secure Socket Tunneling Protocol) használatával működő VPN-kapcsolat. A pont–hely kapcsolatok akkor hasznosak, ha egy távoli helyről szeretne csatlakozni a virtuális hálózathoz, például otthonról vagy egy konferenciáról, vagy akkor, ha csak néhány ügyfelet szeretne csatlakoztatni egy virtuális hálózathoz. A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet a működéshez. A VPN-kapcsolatot az ügyfélszámítógépről hozhatja létre.

Ez a cikk végigkalauzolja egy pont–hely típusú kapcsolattal rendelkező virtuális hálózat a PowerShell használatával történő létrehozásának lépésein a Resource Manager üzemi modellben. A Pont–hely kapcsolatokról további információt a cikk végén, a [Pont–hely kapcsolatok – gyakori kérdések](#faq) című részben talál.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Üzemi modellek és módszerek a pont–hely (P2S) kapcsolatokhoz
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Az alábbi táblázatban látható a két üzemi modell és a pont–hely konfigurációkhoz elérhető üzembe helyezési módszerek. Amint elérhetővé válik a konfigurációs lépéseket ismertető cikk, egy arra mutató közvetlen hivatkozás szerepel majd a táblázatban.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Alapvető munkafolyamat
![Számítógép csatlakoztatása Azure-beli virtuális hálózathoz – pont-hely kapcsolati diagram](./media/vpn-gateway-howto-point-to-site-rm-ps/point-to-site-diagram.png)

Ez az útmutató azt ismerteti, hogyan hozhat létre pont–hely kapcsolattal rendelkező virtuális hálózatot. Az útmutatás segít az ehhez a konfigurációhoz szükséges tanúsítványok előállításában is. A pont–hely kapcsolatot a következő elemek alkotják: egy VPN-átjárót tartalmazó virtuális hálózat, egy főtanúsítvány (.cer fájl formájában, ez a nyilvános kulcs), egy ügyféltanúsítvány és a VPN-konfigurációja az ügyfélen. 

Ehhez a konfigurációhoz a következő értékeket használjuk: A változókat a cikk [1](#declare). szakaszában állítjuk be. Megteheti, hogy lépésről lépésre végighalad az eljáráson, és módosítás nélkül ezeket az értékeket használja, de módosíthatja is őket, hogy megfeleljenek a saját környezetének. 

### <a name="example"></a>Példaértékek
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
* DNS-kiszolgáló: Annak a DNS-kiszolgálónak az **IP-címe**, amelyet névfeloldásra kíván használni.
* **Átjáró neve: Vnet1GW**
* **Nyilvános IP-név: VNet1GWPIP**
* **VPN típusa: RouteBased**

## <a name="before-beginning"></a>Mielőtt hozzálát
* Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).
* Telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. A PowerShell-parancsmagok telepítéséről további információt a [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása) című témakörben talál. 

## <a name="declare"></a>1. rész – Bejelentkezés és a változók beállítása
Ez a szakasz a bejelentkezést és a konfigurációban használt értékek deklarálását ismerteti. A minta parancsprogramok a deklarált értékeket használják. Módosítsa az értékeket úgy, hogy megfeleljenek a saját környezetének. Azt is megteheti, hogy a deklarált értékeket használja, és gyakorlásként halad végig a lépéseken.

1. Nyissa meg emelt a PowerShell-konzolt szintű jogosultságokkal, és jelentkezzen be az Azure-fiókjába. Ez a parancsmag bejelentkezési hitelesítő adatokat kér be. A bejelentkezés után letölti a fiók beállításait, hogy elérhetők legyenek az Azure PowerShell számára.

  ```powershell
  Login-AzureRmAccount
  ```
2. Olvassa be az Azure-előfizetések listáját.

  ```powershell  
  Get-AzureRmSubscription
  ```
3. Válassza ki a használni kívánt előfizetést.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
4. Deklarálja a használni kívánt változókat. Használja a következő példát, és szükség szerint cserélje le az értékeket a sajátjaira.

  ```powershell
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
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="ConfigureVNet"></a>2. rész – Virtuális hálózat konfigurálása
1. Hozzon létre egy erőforráscsoportot.

  ```powershell
  New-AzureRmResourceGroup -Name $RG -Location $Location
  ```
2. Hozza létre a virtuális hálózat alhálózatainak konfigurációit, névként a következő értékeket adja meg: *FrontEnd*, *BackEnd*, illetve *GatewaySubnet*. Ezek az előtagok a deklarált virtuális hálózati címtér részei kell, hogy legyenek.

  ```powershell
  $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
  $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
  $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
  ```
3. Hozza létre a virtuális hálózatot. A megadott DNS-kiszolgálónak olyan DNS-kiszolgálónak kell lennie, amely fel tudja oldani azoknak az erőforrásoknak a nevét, amellyel Ön kapcsolatot fog létesíteni. Ebben a példában egy nyilvános IP-címet használtunk. Ügyeljen arra, hogy a saját értékeit használja.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS
  ```
4. Adja meg a most létrehozott virtuális hálózat változóit.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  ```
5. Kérjen egy dinamikusan hozzárendelt nyilvános IP-címet. Ez az IP-cím szükséges ahhoz, hogy a hálózat megfelelően működjön. Később fogja összekapcsolni az átjárót az átjáró IP-címkonfigurációjával.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```


## <a name="Certificates"></a>3. rész – Tanúsítványok

A tanúsítványokat az Azure használja a VPN-ügyfelek hitelesítésére a pont–hely VPN-kapcsolatokban. A főtanúsítvány létrehozása után exportálja a nyilvános tanúsítványadatokat (ne a titkos kulcsot) egy Base-64 kódolású X.509 .cer fájlba. Ezután töltse fel a nyilvános tanúsítványadatokat a főtanúsítványból az Azure-ba.

Minden, a virtuális hálózathoz pont–hely kapcsolattal csatlakozó ügyfélszámítógépnek rendelkeznie kell telepített ügyféltanúsítvánnyal. Az ügyféltanúsítványokat a főtanúsítványból hozzák létre, majd telepítik az egyes ügyfélszámítógépekre. Ha nincs telepítve érvényes ügyféltanúsítvány, és az ügyfél megpróbál csatlakozni a virtuális hálózathoz, akkor a hitelesítés meghiúsul.

### <a name="cer"></a>1. lépés – A .cer fájl beszerzése a főtanúsítványhoz

#### <a name="enterprise-certificate"></a>Vállalati tanúsítvány
 
Nagyvállalati megoldás esetén használhatja meglévő tanúsítványláncát. Szerezze be a használni kívánt főtanúsítványhoz tartozó .cer fájlt.

#### <a name="self-signed-root-certificate"></a>Önaláírt főtanúsítvány

Ha nem vállalati tanúsítványmegoldást használ, létre kell hoznia egy önaláírt főtanúsítványt. A pont–hely típusú hitelesítéshez szükséges mezőket tartalmazó önaláírt főtanúsítvány létrehozásához a PowerShellt használhatja. Az [önaláírt főtanúsítványok PowerShell használatával, pont–hely kapcsolatokhoz történő létrehozását ismertető](vpn-gateway-certificates-point-to-site.md) rész végigvezeti az önaláírt főtanúsítványok létrehozásához szükséges lépéseken.

> [!NOTE]
> Korábban a makecert volt az ajánlott módszer az önaláírt főtanúsítványok és a pont–hely kapcsolatokhoz szükséges ügyféltanúsítványok létrehozására. Most már a PowerShell használatával hozhatja létre ezeket a tanúsítványokat. A PowerShell egyik előnye az SHA-2-tanúsítványok létrehozásának lehetősége. Lásd az [önaláírt főtanúsítványok PowerShell használatával, pont–hely kapcsolatokhoz történő létrehozását ismertető](vpn-gateway-certificates-point-to-site.md) részt a szükséges értékekért.
>
>

#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>Az önaláírt főtanúsítványhoz tartozó nyilvános kulcs exportálása

A pont–hely kapcsolatokhoz a nyilvános kulcsot (.cer) fel kell tölteni az Azure-ba. A következő lépések segítségével tudja feltölteni az önaláírt főtanúsítvány .cer fájlját.

1. A .cer fájl tanúsítványból történő beszerzéséhez nyissa meg a **Felhasználói tanúsítványok kezelése** elemet.
2. Keresse meg a „P2SRootCert” önaláírt főtanúsítványt a Tanúsítványok – aktuális felhasználó\Személyes\Tanúsítványok útvonalon, és kattintson rá a jobb gombbal. Kattintson a **Minden feladat**, majd az **Exportálás** elemre a **Tanúsítványexportáló varázsló** elindításához.
3. A varázslóban kattintson a **Tovább** gombra. Válassza a **Nem, nem akarom exportálni a titkos kulcsomat** lehetőséget, majd kattintson a **Tovább** gombra.
4. Az **Exportfájlformátum** lapon válassza a **Base-64 kódolású X.509 (.CER)** lehetőséget, majd kattintson a **Tovább** gombra. 
5. Az **Exportálandó fájl** lapon a Tallózás gombra kattintva keresse meg a „C:” meghajtót, hozzon létre egy „cert” nevű alkönyvtárat, és válassza ki. Adja a tanúsítványfájlnak a „P2SRootCert.cer” nevet, majd kattintson a **Mentés** gombra. 
6. Kattintson a **Tovább** gombra, majd a **Befejezés** gombra a tanúsítvány exportálásához. Megjelenik **Az exportálás sikeres volt** üzenet. A varázsló bezárásához kattintson az **OK** gombra.

### <a name="generate"></a>2. lépés – Ügyféltanúsítvány létrehozása
Létrehozhat egy egyedi tanúsítványt minden ügyfél számára, vagy használhatja ugyanazt a tanúsítványt több ügyfél esetén. Az egyedi ügyféltanúsítványok előállításának előnye az, hogy visszavonhat egyetlen tanúsítványt is. Ha azonban mindenki ugyanazt az ügyféltanúsítványt használja, és Önnek vissza kell vonnia a tanúsítványt, az összes olyan ügyfél számára elő kell állítania és telepítenie kell új tanúsítványokat, amelyek az adott tanúsítványt használják a hitelesítéshez.

#### <a name="enterprise-certificate"></a>Vállalati tanúsítvány
- Ha vállalati tanúsítványmegoldást használ, az általános „name@yourdomain.com” formátumban hozza létre az ügyféltanúsítványokat a „tartománynév\felhasználónév” formátum helyett.
- Ellenőrizze, hogy az ügyféltanúsítvány azon a „felhasználói” tanúsítványsablonon alapul-e, amely használati listájának első helyén az „ügyfél-hitelesítés” áll, nem pedig az intelligens kártyás bejelentkezés vagy egyebek. A tanúsítvány ellenőrzéséhez kattintson duplán az ügyféltanúsítványra, és tekintse meg a **Részletek > Kibővített kulcshasználat** részt.

#### <a name="self-signed-root-certificate"></a>Önaláírt főtanúsítvány 
Ha önaláírt főtanúsítványt használ, tekintse meg a [tanúsítvány PowerShell használatával történő létrehozását](vpn-gateway-certificates-point-to-site.md#clientcert) ismertető szakaszt a pont–hely kapcsolatokkal kompatibilis ügyféltanúsítványok létrehozásának lépéseiért.


### <a name="exportclientcert"></a>3. lépés – Az ügyféltanúsítvány exportálása

Ha egy önaláírt főtanúsítványból a [PowerShellre](vpn-gateway-certificates-point-to-site.md#clientcert) vonatkozó utasítások segítségével hoz létre ügyféltanúsítványt, akkor az automatikusan a létrehozásához használt számítógépre lesz telepítve. Ha egy ügyféltanúsítványt egy másik ügyfélszámítógépre szeretne telepíteni, akkor exportálnia kell azt.
 
1. Ügyféltanúsítvány exportálásához nyissa meg a **Felhasználói tanúsítványok kezelése** elemet. Kattintson a jobb gombbal az exportálni kívánt ügyféltanúsítványra, majd a **Minden feladat** és az **Exportálás** elemre a **Tanúsítványexportáló varázsló** elindításához.
2. A varázslóban kattintson a **Tovább** gombra, válassza az **Igen, a titkos kulcs exportálását választom** lehetőséget, majd kattintson a **Tovább** gombra.
3. Az **Exportfájlformátum** lapon hagyja bejelölve az alapértelmezett elemeket. Győződjön meg róla, hogy a **Minden tanúsítvány belefoglalása a tanúsítványláncba** jelölőnégyzet be van jelölve, hogy a szükséges főtanúsítvány-információkat is exportálja. Ezután kattintson a **Tovább** gombra.
4. A **Biztonság** lapon be kell állítania a titkos kulcs védelmét. Ha jelszó használata mellett dönt, jegyezze fel vagy jegyezze meg a tanúsítványhoz beállított jelszót. Ezután kattintson a **Tovább** gombra.
5. Az **Exportálandó fájl** lapon a **Tallózás** gombra kattintva keresse meg azt a helyet, ahová exportálni szeretné a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **Tovább** gombra.
6. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához.

### <a name="upload"></a>4. lépés – A főtanúsítvány .cer fájljának feltöltése

Miután létrehozta az átjárót, feltöltheti a megbízható főtanúsítványhoz tartozó .cer fájlt az Azure-ba. Legfeljebb 20 főtanúsítványhoz tölthet fel fájlokat. A főtanúsítvány titkos kulcsát ne töltse fel az Azure-ba. Miután feltöltötte a .cer fájlt, az Azure használhatja azt azon ügyfelek hitelesítéséhez, amelyek a virtuális hálózathoz csatlakoznak.

1. A saját értékeivel való helyettesítéssel deklarálja a tanúsítványnév változóját:

  ```powershell
  $P2SRootCertName = "Mycertificatename.cer"
  ```
2. Helyettesítse a fájl elérési útját a sajátjával, majd futtassa a parancsmagokat.

  ```powershell
  $filePathForCert = "C:\cert\Mycertificatename.cer"
  $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
  $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
  $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
  ```


## <a name="creategateway"></a>4. lépés – A VPN-átjáró létrehozása
Konfigurálja és hozza létre a virtuális hálózati átjárót a virtuális hálózat számára. A *-GatewayType* csak **Vpn** lehet, a *-VpnType* pedig csak **RouteBased** lehet. Egy VPN-átjáró létrehozása akár 45 percet is igénybe vehet.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
-VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert
```


## <a name="clientconfig"></a>5. rész: A VPN-ügyfél konfigurációs csomagjának letöltése
Ha pont–hely típusú VPN-kapcsolattal szeretne kapcsolódni egy virtuális hálózathoz, minden ügyfélen telepíteni kell egy VPN-ügyfélkonfigurációs csomagot. A csomag nem telepít VPN-ügyfelet. Az a natív windowsos VPN-ügyfelet konfigurálja a virtuális hálózathoz való csatlakozáshoz szükséges beállításokkal. A támogatott ügyfél operációs rendszerek listáját a cikk végén, a [Pont–hely kapcsolatok – gyakori kérdések](#faq) című szakaszban tekintheti meg.

1. Az átjáró létrehozása után előállíthatja és letöltheti az ügyfél-konfigurációs csomagot. Ebben a példában a 64 bites ügyfél csomag kerül letöltésre. Ha a 32 bites ügyfelet szeretné letölteni, akkor az ’AMD64’ sort cserélje le ’x86’-ra. A VPN-ügyfelet az Azure Portal használatával is letöltheti.

  ```powershell
  Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
  -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
  ```
2. A csomag letöltéséhez másolja és illessze be a visszaadott hivatkozást a böngészőbe, ügyelve arra, hogy eltávolítsa a hivatkozást körülvevő idézőjeleket. 
3. Töltse le és telepítse a csomagot az ügyfélszámítógépen. Ha megjelenik a SmartScreen egy előugró ablaka, kattintson a **További információ**, majd a **Futtatás mindenképpen** elemre. A csomagot mentheti is, így más ügyfélszámítógépekre is telepítheti.
4. Nyissa meg az ügyfélszámítógépen a **Hálózati beállítások** eszközt, és kattintson a **VPN** elemre. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott.



## <a name="clientcertificate"></a>6. rész – Exportált ügyféltanúsítvány telepítése

Ha a tanúsítvány létrehozásához használttól eltérő ügyfélszámítógépről szeretne pont–hely kapcsolatot létesíteni, akkor telepítenie kell egy ügyféltanúsítványt. Az ügyféltanúsítvány telepítésekor szükség lesz az ügyféltanúsítvány exportálásakor létrehozott jelszóra.

1. Keresse meg, és másolja a *.pfx* fájlt az ügyfélszámítógépre. Az ügyfélszámítógépen kattintson duplán a *.pfx* fájlra annak telepítéséhez. Hagyja meg a **Tárolás helye** esetében az **Aktuális felhasználó** értéket, és kattintson a **Tovább** gombra.
2. A **Fájl** importálása lapon nem kell semmit módosítania. Kattintson a **Tovább** gombra.
3. A **Titkos kulcs védelme** lapon adja meg a tanúsítvány jelszavát, vagy ellenőrizze, hogy a rendszerbiztonsági tag megfelelő-e, majd kattintson a **Tovább** gombra.
4. A **Tanúsítványtároló** lapon ne módosítsa az alapértelmezett helyet, majd kattintson a **Tovább** gombra.
5. Kattintson a **Finish** (Befejezés) gombra. A tanúsítványtelepítés **Biztonsági figyelmeztetés** párbeszédpanelén kattintson az **Igen** gombra. Nyugodtan rákattinthat az Igenre, mivel már létrehozta a tanúsítványt. A rendszer ezután sikeresen importálja a tanúsítványt.

## <a name="connect"></a>7. rész – Csatlakozás az Azure-hoz
1. Csatlakozzon a virtuális hálózathoz. Ehhez navigáljon az ügyfélszámítógépen a VPN-kapcsolatokhoz, és keresse meg a létrehozott VPN-kapcsolatot. Ugyanaz a neve, mint a virtuális hálózatnak. Kattintson a **Connect** (Csatlakozás) gombra. Megjelenhet egy előugró üzenet, amely a tanúsítvány használatára utal. Kattintson a **Folytatás** gombra emelt szintű jogosultságok használatához. 
2. A csatlakozás megkezdéséhez a **Kapcsolat** állapotlapon kattintson a **Csatlakozás** gombra. Ha megjelenik a **Tanúsítvány kiválasztása** képernyő, ellenőrizze, hogy az a csatlakozáshoz használni kívánt ügyféltanúsítványt mutatja-e. Ha nem, kattintson a legördülő nyílra, válassza ki a helyes tanúsítványt, majd kattintson az **OK** gombra.
   
    ![A VPN-ügyfél az Azure-hoz csatlakozik](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. A kapcsolat létrejött.
   
    ![A kapcsolat létrejött](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

Ha kapcsolódási problémákat észlel, ellenőrizze az alábbiakat:

- Nyissa meg a **Felhasználói tanúsítványok kezelése** ablakot, és navigáljon a **Megbízható legfelső szintű hitelesítésszolgáltatók\Tanúsítványok** részhez. Ellenőrizze, hogy a főtanúsítvány szerepel-e a listában. A főtanúsítvány a hitelesítés működéséhez szükséges. Ha .pfx formátumú ügyféltanúsítványt exportál az alapértelmezett „Minden tanúsítvány belefoglalása a tanúsítványláncba” érték használatával, a főtanúsítvány információit is exportálja a program. Amikor telepíti az ügyféltanúsítványt, az alkalmazás a főtanúsítványt is telepíti az ügyfélszámítógépre. 

- Ha vállalati hitelesítésszolgáltatói megoldás használatával kiadott tanúsítványt használ, és problémák merülnek fel a hitelesítés során, ellenőrizze a hitelesítési sorrendet az ügyféltanúsítványon. A hitelesítési lista sorrendjének ellenőrzéséhez kattintson duplán az ügyféltanúsítványra, és lépjen a **Részletek > Kibővített kulcshasználat** részre. Ellenőrizze, hogy a listán az „ügyfél-hitelesítés” jelenik-e meg első helyen. Ha nem, ki kell adnia egy ügyféltanúsítványt, amely az ügyfél-hitelesítést a lista első helyén tartalmazó felhasználói sablonon alapul.  


## <a name="verify"></a>8. lépés – A kapcsolat ellenőrzése
1. Annak ellenőrzéséhez, hogy a VPN-kapcsolat aktív-e, nyisson meg egy rendszergazda jogú parancssort, és futtassa az *ipconfig/all* parancsot.
2. Tekintse meg az eredményeket. Figyelje meg, hogy a kapott IP-cím azok közül a címek közül való, amelyeket a pont–hely VPN-ügyfél konfigurációjának címkészletében megadott. Az eredmények az alábbi példában szereplőkhöz hasonlóak:
   
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


## <a name="connectVM"></a>Csatlakozás virtuális géphez

1. Miután csatlakozott a virtuális hálózathoz, kapcsolódhat a virtuális géphez P2S-kapcsolattal. A virtuális géphez való csatlakozáshoz szükség lesz a virtuális gép privát IP-címére. Az alábbi példa segít a privát IP-cím beolvasásában a [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) használatával. Eredményül egy virtuális gépeket és a hozzájuk tartozó privát IP-címeket tartalmazó listát kap minden erőforráscsoportban. 

  ```powershell   
  $vms = get-azurermvm
  $nics = get-azurermnetworkinterface | where VirtualMachine -NE $null #skip Nics with no VM
  
  foreach($nic in $nics)
  {
    $vm = $vms | where-object -Property Id -EQ $nic.VirtualMachine.id
    $prv =  $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAddress
    $alloc =  $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAllocationMethod
    Write-Output "$($vm.Name) : $prv , $alloc"
  }
  ```
2. Használja az alábbi parancsot egy távoli asztali kapcsolat létrehozásához a virtuális géppel. Cserélje le az IP-címet annak a virtuális gépnek a privát IP-címére, amelyhez kapcsolódni szeretne. Ha a rendszer erre kéri, adja meg a virtuális gép létrehozásakor használt hitelesítő adatokat. 

  ```powershell   
  mstsc /v:192.168.1.4
  ```

Ha nem sikerült P2S-en keresztül csatlakozni egy virtuális géphez, az ipconfig használatával ellenőrizze annak a számítógépnek az Ethernet-adapteréhez hozzárendelt IPv4-címet, amelyről a kapcsolatot létesíti. Ha az IP-cím azon virtuális hálózat tartományában található, amelyhez csatlakozni kíván, vagy a VPN-ügyfél címkészletének címtartományában, akkor átfedő címtérről beszélünk. Ilyen átfedés esetén a hálózati forgalom nem éri el az Azure-t, és a helyi hálózaton marad. Ha a hálózati címtartományokban nincs átfedés, de mégsem tud kapcsolódni a virtuális géphez, olvassa el a [Troubleshoot Remote Desktop connections to a VM](../virtual-machines/windows/troubleshoot-rdp-connection.md) (A virtuális gépekhez való távoli asztali kapcsolatok hibáinak elhárítása) című témakört.

## <a name="addremovecert"></a>Megbízható főtanúsítvány hozzáadása vagy eltávolítása

A megbízható főtanúsítványokat felveheti vagy el is távolíthatja az Azure-ban. Ha eltávolít egy megbízható tanúsítványt, akkor a főtanúsítványból generált ügyféltanúsítványok nem tudnak pont–hely kapcsolattal csatlakozni az Azure-hoz. Ha azt szeretné, hogy az ügyfelek csatlakozni tudjanak, egy, az Azure által megbízhatónak tartott tanúsítványból generált új ügyféltanúsítványt kell telepítenie.

### <a name="to-add-a-trusted-root-certificate"></a>Megbízható főtanúsítvány hozzáadása
Az Azure-ra legfeljebb 20 megbízható főtanúsítványt tölthet fel .cer fájl formájában. A következő lépések segítségével adhat hozzá főtanúsítványt:

1. Hozza létre és készítse elő az Azure-ba felvenni kívánt új főtanúsítványt. Exportálja a nyilvános kulcsot Base-64-kódolású X.509 (.CER) formátumban, és nyissa meg egy egyszerű szövegszerkesztőben. Másolja a példányokat az alábbi példában látható módon.
   
  ![tanúsítvány](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

    > [!NOTE]
    > A tanúsítványadatok másolásakor a szöveget egy folyamatos sorként másolja kocsivissza vagy új sor nélkül. A kocsivisszák és az új sorok megjelenítéséhez lehet, hogy módosítania kell a nézetet a szövegszerkesztőben a „Szimbólum megjelenítése/Minden karakter megjelenítése” beállításra.
  >
  >

2. Adja meg a tanúsítvány nevét és a kulcs adatait egy változóként. Helyettesítse az adatokat a saját adataival az alábbi példában látható módon:

  ```powershell
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
3. Adja hozzá a megbízható főtanúsítványt. Egyszerre csak egy tanúsítványt adhat hozzá.

  ```powershell
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
4. A következő példa használatával ellenőrizheti, hogy helyesen ment-e végbe a tanúsítvány hozzáadása:

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```


### <a name="to-remove-a-trusted-root-certificate"></a>Megbízható főtanúsítvány eltávolítása


1. Deklarálja a változókat.

  ```powershell
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
2. Távolítsa el a tanúsítványt.

  ```powershell
  Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
3. A következő példával meggyőződhet arról, hogy sikeresen megtörtént a tanúsítvány eltávolítása.

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

## <a name="revoke"></a>Ügyféltanúsítvány visszavonása
Az ügyféltanúsítványokat vissza lehet vonni. A visszavont tanúsítványok listájával az egyes ügyféltanúsítványok alapján, szelektíven tagadhatja meg a pont–hely kapcsolódás lehetőségét. Ez a folyamat eltér a megbízható főtanúsítvány eltávolításától. Ha töröl egy .cer formátumú megbízható főtanúsítványt az Azure-ból, azzal megvonja a hozzáférést minden olyan ügyféltanúsítványtól, amelyet a visszavont főtanúsítvánnyal hoztak létre/írtak alá. A főtanúsítvány helyett az ügyféltanúsítvány visszavonása esetén a főtanúsítványból létrehozott többi tanúsítvány továbbra is használható hitelesítésre.

A szokásos gyakorlat az, hogy a főtanúsítvánnyal kezelik a hozzáférést a munkacsoport vagy a szervezet szintjén, az egyes felhasználókra vonatkozó részletesebb szabályozást pedig visszavont ügyféltanúsítványokkal oldják meg.

### <a name="to-revoke-a-client-certificate"></a>Ügyféltanúsítvány visszavonása

1. Kérje le az ügyféltanúsítvány ujjlenyomatát. További információkat [a tanúsítványok ujjlenyomatának lekérését ismertető útmutatóban](https://msdn.microsoft.com/library/ms734695.aspx) találhat.
2. Másolja át az adatokat egy szövegszerkesztőbe, és távolítsa el az összes szóközt, hogy egy folyamatos karakterláncot kapjon. A következő lépésben ez változóként van deklarálva.
3. Deklarálja a változókat. Győződjön meg róla, hogy az előző lépésben lekért ujjlenyomatot deklarálja.

  ```powershell
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
4. Vegye fel az ujjlenyomatot a visszavont tanúsítványok listájára. Az ujjlenyomat hozzáadása után a „Sikeres” üzenet jelenik meg.

  ```powershell
  Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
  -Thumbprint $RevokedThumbprint1
  ```
5. Ellenőrizze, hogy az ujjlenyomat bekerült-e a visszavont tanúsítványok listájába.

  ```powershell
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```
6. Az ujjlenyomat hozzáadását követően a tanúsítvány már nem használható csatlakozáshoz. Azok az ügyfelek, akik ezzel a tanúsítvánnyal próbálnak csatlakozni, egy üzenetet kapnak majd arról, hogy a tanúsítvány már nem érvényes.

### <a name="to-reinstate-a-client-certificate"></a>Ügyféltanúsítvány érvényességének visszaállítása
Vissza is állíthatja az ügyféltanúsítványok érvényességét. Ehhez törölni kell az ujjlenyomatukat a visszavont ügyféltanúsítványok listájából.

1. Deklarálja a változókat. Győződjön meg arról, hogy a megfelelő, a visszaállítani kívánt tanúsítványhoz tartozó ujjlenyomatot deklarálta.

  ```powershell
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
2. Törölje a tanúsítvány ujjlenyomatát a visszavont tanúsítványok listájából.

  ```powershell
  Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
  ```
3. Ellenőrizze, hogy megtörtént-e az ujjlenyomat eltávolítása a listából.

  ```powershell
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```

## <a name="faq"></a>Pont–hely kapcsolatok – gyakori kérdések

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Következő lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). A hálózatok és virtuális gépek ismertetését lásd az [Azure- és Linux-alapú virtuálisgép-hálózatok áttekintésében](../virtual-machines/linux/azure-vm-network-overview.md).

