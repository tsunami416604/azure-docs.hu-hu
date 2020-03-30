---
title: 'Csatlakozás virtuális hálózathoz P2S VPN-& tanúsítványhitelesítés használatával: portál'
titleSuffix: Azure VPN Gateway
description: Windows, Mac OS X és Linux-ügyfelek biztonságos csatlakoztatása egy Azure virtuális hálózathoz a P2S és az önaláírt vagy hitelesítésszolgáltató által kiállított tanúsítványok használatával. Ebben a cikkben az Azure Portalon fogunk dolgozni.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: cherylmc
ms.openlocfilehash: 013ebc2a1343c8eab3d477023e36660c93fa6da5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244484"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Pont-hely vpn-kapcsolat konfigurálása virtuális hálózattal natív Azure-tanúsítványhitelesítés használatával: Azure Portal

Ez a cikk segít biztonságosan összekapcsolni a Windows, Linux vagy Mac OS X rendszerű egyes ügyfeleket egy Azure virtuális hálózathoz. A pont–hely VPN-kapcsolat akkor hasznos, ha távoli helyről szeretne csatlakozni a virtuális hálózathoz, például otthonról vagy egy konferenciáról. Pont–hely kapcsolatot is használhat helyek közötti VPN helyett, ha csak néhány ügyfelet szeretne egy virtuális hálózathoz csatlakoztatni. A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet. Pont–hely kapcsolat esetén SSTP (Secure Socket Tunneling Protocol) vagy IKEv2-protokoll használatával jön létre a VPN-kapcsolat. További információkat a pont–hely VPN-ről a [pont–hely VPN-t ismertető](point-to-site-about.md) témakör tartalmaz.

![Számítógép csatlakoztatása Azure-beli virtuális hálózathoz – pont-hely kapcsolati diagram](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)

## <a name="architecture"></a>Architektúra

A natív Azure-tanúsítvánnyal hitelesített pont–hely kapcsolatok a következő elemeket használják, amelyeket külön konfigurálni kell:

* Útvonalalapú VPN-átjáró.
* A nyilvános kulcs (.cer fájl) egy főtanúsítványhoz, amely az Azure-ba van feltöltve. A tanúsítványt a feltöltését követően megbízható tanúsítványnak tekinti a rendszer, és ezt használja hitelesítéshez.
* A főtanúsítványból létrejött ügyféltanúsítvány, amely a virtuális hálózathoz csatlakozó egyes ügyfélszámítógépekre telepített ügyféltanúsítvány. A rendszer ezt a tanúsítványt használja ügyfélhitelesítéshez.
* A VPN-ügyfél konfigurációja. A VPN-ügyfélkonfigurációs fájlok azokat az adatokat tartalmazzák, amelyekre az ügyfélnek szüksége van a virtuális hálózathoz való csatlakozáshoz. A csomag konfigurálja az operációs rendszer meglévő, natív VPN-ügyfelét. Minden csatlakozó ügyfelet a konfigurációs fájlokban szereplő beállításokkal kell konfigurálni.

#### <a name="example-values"></a><a name="example"></a>Példaértékek

Az alábbi értékek használatával létrehozhat egy tesztkörnyezetet, vagy segítségükkel értelmezheti a cikkben szereplő példákat:

* **Virtuális hálózat neve:** VNet1
* **Címterület:** 10.1.0.0/16<br>Ebben a példában csak egy címteret használunk. Azonban a virtuális hálózatához több címteret is használhat.
* **Alhálózat neve:** Frontend
* **Alhálózati címtartomány:** 10.1.0.0/24
* **Előfizetés:** Ha több előfizetése is van, ellenőrizze, hogy a megfelelőt használja-e.
* **Erőforráscsoport:** TestRG1
* **Helyszín:** USA keleti része
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Virtuális hálózati átjáró neve:** VNet1GW
* **Átjáró típusa:** VPN
* **VPN típusa:** útvonalalapú
* **Nyilvános IP-cím neve:** VNet1GWpip
* **Kapcsolat típusa:** pont–hely
* **Ügyfél címkészlete:** 172.16.201.0/24<br>Azok a VPN-ügyfelek, amelyek ezzel a pont–hely kapcsolattal csatlakoznak a virtuális hálózathoz, az ügyfélcímkészletből kapnak IP-címet.

## <a name="1-create-a-virtual-network"></a><a name="createvnet"></a>1. Hozzon létre egy virtuális hálózat

Mielőtt elkezdi végrehajtani a lépéseket, győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).
[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. Hozzon létre egy virtuális hálózati átjáró

Ebben a lépésben a virtuális hálózat virtuális hálózati átjáróját fogja létrehozni. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet a választott átjáró-termékváltozattól függően.

>[!NOTE]
>Az alapszintű átjáró termékváltozat nem támogatja az IKEv2 vagy RADIUS-hitelesítést. Ha azt tervezi, hogy a Mac-ügyfelek csatlakoznak a virtuális hálózathoz, ne használja az alaptermékváltozatot.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="3-generate-certificates"></a><a name="generatecert"></a>3. Tanúsítványok létrehozása

A tanúsítványokat az Azure a virtuális hálózathoz pont–hely VPN-kapcsolaton keresztül csatlakozó ügyfelek hitelesítésére használja. Amint beszerzett egy főtanúsítványt, a nyilvánoskulcs-adatait [feltölti](#uploadfile) az Azure-ba. Az Azure a főtanúsítványt ettől kezdve „megbízhatónak” tekinti a virtuális hálózathoz pont–hely kapcsolaton keresztüli csatlakozás esetén. Létrehoz ügyféltanúsítványokat is a megbízható főtanúsítványból, majd telepíti őket az összes ügyfélszámítógépeken. Az ügyféltanúsítványt a rendszer az ügyfél hitelesítésére használja, amikor az a VNethez próbál csatlakozni. 

### <a name="1-obtain-the-cer-file-for-the-root-certificate"></a><a name="getcer"></a>1. A főtanúsítvány .cer fájljának beszerzése

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="2-generate-a-client-certificate"></a><a name="generateclientcert"></a>2. Ügyféltanúsítvány létrehozása

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="4-add-the-client-address-pool"></a><a name="addresspool"></a>4. Adja hozzá az ügyfél címkészletét

Az ügyfélcímkészlet megadott magánhálózati IP-címek tartománya. A pont–hely VPN-kapcsolattal csatlakozó ügyfelek ebből a tartományból kapnak dinamikusan IP-címet. Olyan magánhálózati IP-címtartományt használjon, amely nincs átfedésben azzal a helyszíni hellyel, amelyről csatlakozik, vagy azzal a virtuális hálózattal, amelyhez csatlakozik. Ha több protokollt konfigurál, és az SSTP az egyik protokoll, akkor a konfigurált címkészlet egyenlően oszlik meg a konfigurált protokollok között.

1. Miután létrehozta a virtuális hálózati átjárót, navigáljon a virtuális hálózati átjáró lapjának **Beállítások** részéhez. A **Beállítások** csoportban válassza a **Pont-hely konfiguráció lehetőséget.** A konfigurációs lap megnyitásához válassza a **Konfigurálás most** lehetőséget.

   ![Pont–hely lap](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-configure.png "Pont-hely beállítás most")
2. A **pont-hely konfigurációs** lapon számos beállítást konfigurálhat. Ha ezen a lapon nem látható a Bújtatás típusa vagy a Hitelesítés típusa, az átjáró az alapszintű termékváltozatot használja. Az alapszintű termékváltozat nem támogatja az IKEv2- vagy RADIUS-hitelesítést. Ha ezeket a beállításokat szeretné használni, törölnie kell, majd újra létre kell hoznia az átjárót egy másik átjáró termékváltozat használatával.

   [![Pont–hely konfigurációs lap](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/certificate-settings-address.png "címkészlet megadása")](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/certificate-settings-expanded.png#lightbox)
3. A **Címkészlet** mezőben adja hozzá a használni kívánt privát IP-címtartományt. A VPN-ügyfelek dinamikusan kapnak egy IP-címet a megadott tartományból. A minimális alhálózati maszk 29 bit aktív/passzív és 28 bit az aktív/aktív konfigurációhoz.
4. Ugrás a következő szakaszra a bújtatástípus konfigurálásához.

## <a name="5-configure-tunnel-type"></a><a name="tunneltype"></a>5. Bújtatástípus konfigurálása

Kiválaszthatja az alagút típusát. Az alagút beállításai: OpenVPN, SSTP és IKEv2.

* Az Android- és Linux-alapú strongSwan-ügyfél, valamint az iOS- és OS X-alapú natív IKEv2 VPN-ügyfél csak IKEv2-alagutat használ a kapcsolódáshoz.
* A Windows-ügyfelek először az IKEv2-vel próbálkoznak, és ha ez nem csatlakozik, akkor visszaállnak az SSTP-re.
* Az OpenVPN-ügyféllel csatlakozhat az OpenVPN-alagút típusához.

![Alagúttípus](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/tunnel.png "bújtatástípus megadása")

## <a name="6-configure-authentication-type"></a><a name="authenticationtype"></a>6. Hitelesítéstípus konfigurálása

A **Hitelesítés típusa mezőben**válassza az **Azure-tanúsítvány lehetőséget.**

  ![Hitelesítés típusa](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/authentication-type.png "hitelesítéstípus megadása")

## <a name="7-upload-the-root-certificate-public-certificate-data"></a><a name="uploadfile"></a>7. Töltse fel a főtanúsítvány nyilvános tanúsítványadatait

További megbízható főtanúsítványokat is feltölthet (legfeljebb 20-at). Miután feltöltötte a nyilvános tanúsítványadatokat, az Azure felhasználhatja azon ügyfelek hitelesítéséhez, amelyeken telepítve lett egy, a megbízható főtanúsítványból létrehozott ügyféltanúsítvány. Töltse fel a főtanúsítvány nyilvánoskulcs-adatait az Azure-ba.

1. A tanúsítványokat a rendszer hozzáadja a **Főtanúsítvány** szakasz **Pont–hely konfiguráció** lapjához.
2. Győződjön meg arról, hogy Base-64 kódolású X.509 (.cer) fájlként exportálta a főtanúsítványt. Ebben a formátumban kell exportálnia a tanúsítványt, hogy szövegszerkesztővel meg tudja azt nyitni.
3. Nyissa megy a tanúsítványt egy szövegszerkesztővel, például a Jegyzettömbbel. A tanúsítványadatok másolásakor a szöveget egy folyamatos sorként másolja kocsivissza vagy új sor nélkül. A kocsivisszák és az új sorok megjelenítéséhez lehet, hogy módosítania kell a nézetet a szövegszerkesztőben a „Szimbólum megjelenítése/Minden karakter megjelenítése” beállításra. Csak a következő szakaszt másolja egy folyamatos sorként:

   ![Tanúsítvány adatai](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png "gyökértanúsítvány adatainak másolása")
4. Illessze be a tanúsítványadatokat a **Nyilvános tanúsítványadatok** mezőbe. **Nevezze el** a tanúsítványt, majd válassza a **Mentés gombot.** Legfeljebb 20 megbízható főtanúsítványt adhat hozzá.

   ![Tanúsítványadatok beillesztése](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png "tanúsítványadatok beillesztése")
5. Az összes konfigurációs beállítás mentéséhez válassza a lap tetején található **Mentés** lehetőséget.

   ![Konfiguráció mentése](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png "konfiguráció mentése")

## <a name="8-install-an-exported-client-certificate"></a><a name="installclientcert"></a>8. Exportált ügyféltanúsítvány telepítése

Ha a tanúsítvány létrehozásához használttól eltérő ügyfélszámítógépről szeretne pont–hely kapcsolatot létesíteni, akkor telepítenie kell egy ügyféltanúsítványt. Az ügyféltanúsítvány telepítésekor szükség lesz az ügyféltanúsítvány exportálásakor létrehozott jelszóra.

Győződjön meg arról, hogy az ügyféltanúsítványt .pfx fájlként exportálta a teljes tanúsítványlánccal együtt (ez az alapértelmezett beállítás). Egyéb esetben a főtanúsítvány adatai nem lesznek jelen az ügyfélszámítógépen, és az ügyfél nem fogja tudni megfelelően elvégezni a hitelesítést.

A telepítés lépései az [ügyféltanúsítvány telepítésével](point-to-site-how-to-vpn-client-install-azure-cert.md) foglalkozó részben találhatók.

## <a name="9-generate-and-install-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>9. A VPN-ügyfél konfigurációs csomagjának létrehozása és telepítése

A VPN-ügyfél konfigurációs fájljai tartalmazzák az eszközök azon beállításait, amelyekkel pont–hely kapcsolaton keresztül kapcsolódhatnak egy virtuális hálózathoz. A VPN-ügyfél konfigurációs fájljainak létrehozásához és telepítéséhez szükséges utasításokért lásd:[A VPN-ügyfél konfigurációs fájljainak létrehozása és telepítése az Azure natív tanúsítványalapú hitelesítést használó pont–hely kapcsolatokhoz](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. Csatlakozás az Azure-hoz

### <a name="to-connect-from-a-windows-vpn-client"></a>Csatlakozás Windows VPN-ügyfélről

>[!NOTE]
>Rendszergazdai jogosultsággal kell rendelkeznie azon a Windows ügyfélszámítógépen, ahonnan csatlakozik.
>
>

1. Csatlakozzon a virtuális hálózathoz. Ehhez navigáljon az ügyfélszámítógépen a VPN-kapcsolatokhoz, és keresse meg a létrehozott VPN-kapcsolatot. Ugyanaz a neve, mint a virtuális hálózatnak. Kattintson a **Csatlakozás** gombra. Megjelenhet egy előugró üzenet, amely a tanúsítvány használatára utal. Válassza **a Továbbra** emelt szintű jogosultságok használatához lehetőséget.

2. A **Kapcsolat** állapota lapon válassza a **Csatlakozás** lehetőséget a kapcsolat elindításához. Ha megjelenik a **Tanúsítvány kiválasztása** képernyő, ellenőrizze, hogy az a csatlakozáshoz használni kívánt ügyféltanúsítványt mutatja-e. Ha nem, a legördülő nyíllal jelölje ki a megfelelő tanúsítványt, majd kattintson az **OK gombra.**

   ![A VPN-ügyfél az Azure-hoz csatlakozik](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png "csatlakozásra")
3. A kapcsolat létrejött.

   ![A kapcsolat létrejött](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png "kapcsolat létrejött")

#### <a name="troubleshoot-windows-p2s-connections"></a>Windows pont–hely kapcsolatok hibaelhárítása

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Csatlakozás Mac VPN-ügyfélről

A Hálózat párbeszédpanelen keresse meg a használni kívánt ügyfélprofilt, adja meg a [vpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac)fájl beállításait, majd válassza a **Csatlakozás lehetőséget.**

A [telepítés - Mac (OS X)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac) című témakörben talál részletes útmutatást. Ha problémái vannak a csatlakozással, ellenőrizze, hogy a virtuális hálózati átjáró nem használ-e alapszintű termékváltozatot. Az alapszintű termékváltozat nem támogatott a Mac-ügyfelek számára.

  ![Mac-kapcsolat](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png "Kapcsolódás")

## <a name="to-verify-your-connection"></a><a name="verify"></a>A kapcsolat ellenőrzése

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

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Csatlakozás virtuális géphez

Ezek az utasítások Windows-ügyfelekre érvényesek.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>Megbízható főtanúsítványok hozzáadása vagy eltávolítása

A megbízható főtanúsítványokat felveheti vagy el is távolíthatja az Azure-ban. Főtanúsítvány eltávolításakor az abból a gyökérből létrehozott tanúsítvánnyal rendelkező ügyfelek nem fognak tudni hitelesítést végezni, így csatlakozni sem. Ha azt szeretné, hogy az ügyfelek hitelesítést végezhessenek és csatlakozni tudjanak, telepítenie kell egy olyan új ügyféltanúsítványt, amelyet az Azure által megbízhatónak tartott (feltöltött) főtanúsítványból hoztak létre.

### <a name="to-add-a-trusted-root-certificate"></a>Megbízható főtanúsítvány hozzáadása

Az Azure-ra legfeljebb 20 megbízható főtanúsítványt tölthet fel .cer fájl formájában. Útmutatásért lásd a jelen cikk [Megbízható főtanúsítvány feltöltése](#uploadfile) című szakaszát.

### <a name="to-remove-a-trusted-root-certificate"></a>Megbízható főtanúsítvány eltávolítása

1. A megbízható főtanúsítvány eltávolításához lépjen a virtuális hálózati átjáróhoz tartozó **Pont–hely konfiguráció** lapra.
2. Keresse meg az eltávolítani kívánt tanúsítványt a lap **Főtanúsítvány** szakaszában.
3. Jelölje ki a tanúsítvány melletti három pontot, majd válassza az "Eltávolítás" lehetőséget.

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>Ügyféltanúsítvány visszavonása

Az ügyféltanúsítványokat vissza lehet vonni. A visszavont tanúsítványok listájával az egyes ügyféltanúsítványok alapján, szelektíven tagadhatja meg a pont–hely kapcsolódás lehetőségét. Ez a folyamat eltér a megbízható főtanúsítvány eltávolításától. Ha töröl egy .cer formátumú megbízható főtanúsítványt az Azure-ból, azzal megvonja a hozzáférést minden olyan ügyféltanúsítványtól, amelyet a visszavont főtanúsítvánnyal hoztak létre/írtak alá. A főtanúsítvány helyett az ügyféltanúsítvány visszavonása esetén a főtanúsítványból létrehozott többi tanúsítvány továbbra is használható hitelesítésre.

A szokásos gyakorlat az, hogy a főtanúsítvánnyal kezelik a hozzáférést a munkacsoport vagy a szervezet szintjén, az egyes felhasználókra vonatkozó részletesebb szabályozást pedig visszavont ügyféltanúsítványokkal oldják meg.

### <a name="revoke-a-client-certificate"></a>Ügyféltanúsítvány visszavonása

Az ügyféltanúsítványok visszavonásához vegye fel az ujjlenyomatot a visszavont tanúsítványok listájára.

1. Kérje le az ügyféltanúsítvány ujjlenyomatát. További információkat [a tanúsítványok ujjlenyomatának lekérését ismertető útmutatóban](https://msdn.microsoft.com/library/ms734695.aspx) találhat.
2. Másolja át az adatokat egy szövegszerkesztőbe, és távolítsa el az összes szóközt, hogy egy folyamatos sztringet kapjon.
3. Lépjen a virtuális hálózati átjáró **Pont–hely konfiguráció** lapjára. Ez ugyanaz a lap, amelyet a [megbízható főtanúsítvány feltöltéséhez](#uploadfile) használt.
4. A **Visszavont tanúsítványok** szakaszban adjon egy rövid nevet a tanúsítványnak (ennek nem kell megegyeznie a tanúsítvány köznapi nevével).
5. Másolja ki és illessze be az ujjlenyomat sztringjét az **Ujjlenyomat** mezőbe.
6. A rendszer ellenőrzi az ujjlenyomatot, és automatikusan hozzáadja a visszavont tanúsítványok listájához. A képernyőn megjelenik egy üzenet, amely szerint a lista frissítése folyamatban van. 
7. A frissítés befejezését követően a tanúsítvány már nem használható csatlakozáshoz. Azok az ügyfelek, akik ezzel a tanúsítvánnyal próbálnak csatlakozni, egy üzenetet kapnak majd arról, hogy a tanúsítvány már nem érvényes.

## <a name="point-to-site-faq"></a><a name="faq"></a>Pont–hely kapcsolatok – gyakori kérdések

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>További lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/). A hálózatok és virtuális gépek ismertetését lásd az [Azure- és Linux-alapú virtuálisgép-hálózatok áttekintésében](../virtual-machines/linux/azure-vm-network-overview.md).

A pont–hely hibaelhárítási információiért tekintse át az [Azure pont–hely kapcsolatok hibaelhárításával](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) foglalkozó cikket.
