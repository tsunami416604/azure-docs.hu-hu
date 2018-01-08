---
title: "Számítógép csatlakoztatása Azure-beli virtuális hálózathoz pont–hely kapcsolat és az Azure natív tanúsítványalapú hitelesítésének használatával: Azure Portal | Microsoft Docs"
description: "A tanúsítványalapú hitelesítés használatával biztonságosan csatlakoztathat egy számítógépet az Azure Virtual Networkhöz pont–hely VPN Gateway-kapcsolat létrehozásával. Ez a cikk a Resource Manager-alapú üzemi modellre vonatkozik, és az Azure Portalt használja."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cherylmc
ms.openlocfilehash: fc3ba82003d7714ee26ffcfb32f096f0374d2800
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Pont–hely kapcsolat konfigurálása virtuális hálózathoz az Azure natív tanúsítványalapú hitelesítésének használatával: Azure Portal

Ez a cikk bemutatja, hogyan hozhat létre pont–hely kapcsolattal rendelkező virtuális hálózatot a Resource Manager-alapú üzemi modellben az Azure Portalon. Ez a konfiguráció tanúsítványokat használ a hitelesítéséhez. Ebben a konfigurációban a RADIUS-kiszolgáló helyett az Azure VPN-átjáró végzi a tanúsítvány ellenőrzését. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

A pont–hely (P2S) VPN-átjáró lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely VPN-kapcsolat akkor hasznos, ha távoli helyről szeretne csatlakozni a virtuális hálózathoz, például otthonról vagy egy konferenciáról. A pont–hely VPN emellett akkor is hasznos megoldás lehet a helyek közötti VPN helyett, ha csak néhány ügyfelet szeretne egy VNetre csatlakoztatni. A pont–hely VPN-kapcsolat indítása a Windows- és Mac-eszközökről történik.

A kapcsolódó ügyfelek az alábbi hitelesítési módszereket használhatják:

* RADIUS-kiszolgáló – jelenleg előzetes verzióban
* VPN-átjáró – hitelesítés natív Azure-tanúsítvánnyal

Ez a cikk bemutatja, hogyan állíthat be hitelesítést pont–hely konfigurációkhoz az Azure natív tanúsítványalapú hitelesítésével. Ha a RADIUS használatával szeretné hitelesíteni a csatlakozó ügyfeleket, tekintse meg a [Pont–hely kapcsolat RADIUS-hitelesítéssel](point-to-site-how-to-radius-ps.md) című részt.

![Számítógép csatlakoztatása Azure-beli virtuális hálózathoz – pont-hely kapcsolati diagram](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeps.png)

A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet. Pont–hely kapcsolat esetén SSTP (Secure Socket Tunneling Protocol) vagy IKEv2-protokoll használatával jön létre a VPN-kapcsolat.

* Az SSTP egy SSL-alapú VPN-alagút, amely kizárólag Windows-ügyfélplatformokon támogatott. Képes áthatolni a tűzfalakon, ezért ideális megoldás az Azure-hoz való csatlakozáshoz bármilyen tetszőleges. A kiszolgálói oldalon az SSTP 1.0, 1.1 és 1.2 verziója támogatott. Az ügyfél dönti el, hogy melyik verziót használja. Windows 8.1 és újabb kiadások esetén az SSTP alapértelmezés szerint az 1.2 verziót használja.

* IKEv2 VPN, egy szabványalapú IPsec VPN-megoldás. Az IKEv2 VPN segítségével Macről is lehetségessé válik a csatlakozás (OSX 10.11-es vagy újabb verziók használata esetén). Az IKEv2 jelenleg előzetes verzióban érhető el.

A natív Azure-tanúsítvánnyal hitelesített pont–hely kapcsolatokhoz a következőkre van szükség:

* Útvonalalapú VPN-átjáró.
* A nyilvános kulcs (.cer fájl) egy főtanúsítványhoz, amely az Azure-ba van feltöltve. A tanúsítványt a feltöltését követően megbízható tanúsítványnak tekinti a rendszer, és ezt használja hitelesítéshez.
* Egy ügyféltanúsítvány, amely a főtanúsítványból jött létre, és települt a virtuális hálózathoz csatlakozó egyes ügyfélszámítógépekre. A rendszer ezt a tanúsítványt használja ügyfélhitelesítéshez.
* A VPN-ügyfél konfigurációja. A VPN-ügyfélkonfigurációs fájlok azokat az adatokat tartalmazzák, amelyekre az ügyfélnek szüksége van a virtuális hálózathoz való csatlakozáshoz. A csomag konfigurálja az operációs rendszer meglévő, natív VPN-ügyfelét. Minden csatlakozó ügyfelet a konfigurációs fájlokban szereplő beállításokkal kell konfigurálni.

További információk a pont–hely kapcsolatokról: [Információk a pont–hely kapcsolatokról](point-to-site-about.md).

#### <a name="example"></a>Példaértékek

Az alábbi értékek használatával létrehozhat egy tesztkörnyezetet, vagy segítségükkel értelmezheti a cikkben szereplő példákat:

* **Virtuális hálózat neve:** VNet1
* **Címtér:** 192.168.0.0/16<br>Ebben a példában csak egy címteret használunk. Azonban a virtuális hálózatához több címteret is használhat.
* **Alhálózat neve:** FrontEnd
* **Alhálózati címtartomány:** 192.168.1.0/24
* **Előfizetés:** Ha több előfizetése is van, ellenőrizze, hogy a megfelelőt használja-e.
* **Erőforráscsoport:** TestRG
* **Hely:** az USA keleti régiója
* **Átjáró-alhálózat:** 192.168.200.0/24<br>
* **DNS-kiszolgáló:** (nem kötelező) Annak a DNS-kiszolgálónak az IP-címe, amelyet névfeloldásra kíván használni.
* **Virtuális hálózati átjáró neve:** VNet1GW
* **Átjáró típusa:** VPN
* **VPN típusa:** útvonalalapú
* **Nyilvános IP-cím neve:** VNet1GWpip
* **Kapcsolat típusa:** pont–hely
* **Ügyfélcímkészlet:** 172.16.201.0/24<br>Azok a VPN-ügyfelek, amelyek ezzel a pont–hely kapcsolattal csatlakoznak a virtuális hálózathoz, az ügyfélcímkészletből kapnak IP-címet.

## <a name="createvnet"></a>1. Virtuális hálózat létrehozása

Mielőtt elkezdi végrehajtani a lépéseket, győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).
[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-p2s-vnet-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>2. Átjáróalhálózat hozzáadása

Mielőtt csatlakoztatja virtuális hálózatát egy átjáróhoz, létre kell hoznia az átjáróalhálózatot ahhoz a virtuális hálózathoz, amelyhez csatlakozni szeretne. Az átjárószolgáltatások az átjáróalhálózatban megadott IP-címeket használják. Ha lehetséges, hozzon létre egy átjáróalhálózatot /28 vagy /27 CIDR-blokk használatával annak érdekében, hogy a jövőbeli további konfigurációs követelmények számára elegendő IP-címet biztosíthasson.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-p2s-rm-portal-include.md)]

## <a name="dns"></a>3. DNS-kiszolgáló megadása (nem kötelező)

Miután létrehozta a virtuális hálózatot, hozzáadhatja a DNS-kiszolgáló IP-címét a névfeloldás kezelésének érdekében. A DNS-kiszolgálót ebben a konfigurációban nem kötelező megadni, de a névfeloldáshoz szükséges. Az érték megadásával nem jön létre új DNS-kiszolgáló. A megadott DNS-kiszolgáló IP-címének olyan DNS-kiszolgálónak kell lennie, amely fel tudja oldani azoknak az erőforrásoknak a nevét, amelyekkel Ön kapcsolatot fog létesíteni. Ebben a példában egy magánhálózati IP-címet használtunk, de ez valószínűleg nem az Ön DNS-kiszolgálójának IP-címe. Ügyeljen arra, hogy a saját értékeit használja. A megadott értéket a virtuális hálózaton üzembe helyezett erőforrások használják, nem a pont–hely kapcsolat vagy a VPN-ügyfél.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>4. Virtuális hálózati átjáró létrehozása

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

## <a name="generatecert"></a>5. Tanúsítványok előállítása

A tanúsítványokat az Azure a virtuális hálózathoz pont–hely VPN-kapcsolaton keresztül csatlakozó ügyfelek hitelesítésére használja. Amint beszerzett egy főtanúsítványt, a nyilvánoskulcs-adatait [feltölti](#uploadfile) az Azure-ba. Az Azure a főtanúsítványt ettől kezdve „megbízhatónak” tekinti a virtuális hálózathoz pont–hely kapcsolaton keresztüli csatlakozás esetén. Létrehoz ügyféltanúsítványokat is a megbízható főtanúsítványból, majd telepíti őket az összes ügyfélszámítógépeken. Az ügyféltanúsítványt a rendszer az ügyfél hitelesítésére használja, amikor az a VNethez próbál csatlakozni. 

### <a name="getcer"></a>1. A .cer fájl beszerzése a főtanúsítványhoz

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generateclientcert"></a>2. Ügyféltanúsítvány létrehozása

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="addresspool"></a>6. Az ügyfélcímkészlet hozzáadása

Az ügyfélcímkészlet megadott magánhálózati IP-címek tartománya. A pont–hely VPN-kapcsolattal csatlakozó ügyfelek ebből a tartományból kapnak IP-címet. Olyan magánhálózati IP-címtartományt használjon, amely nincs átfedésben azzal a helyszíni hellyel, amelyről csatlakozik, vagy azzal a virtuális hálózattal, amelyhez csatlakozik.

1. Miután létrehozta a virtuális hálózati átjárót, navigáljon a virtuális hálózati átjáró lapjának **Beállítások** részéhez. A **Beállítások** részben kattintson a **Pont–hely konfiguráció** elemre a **Pont–hely konfiguráció** lap megnyitásához.

  ![Pont–hely lap](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/gatewayblade.png)
2. A **Pont-hely konfiguráció** lapon törölheti az automatikusan kitöltött tartományt, majd hozzáadhatja a használni kívánt magánhálózati IP-címtartományt. Kattintson a **Mentés** gombra a beállítás érvényesítéséhez és mentéséhez.

  ![Ügyfélcímkészlet](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/ipaddresspool.png)

## <a name="uploadfile"></a>7. A főtanúsítvány nyilvános tanúsítványadatainak feltöltése

Az átjáró létrehozása után feltöltheti a főtanúsítványhoz tartozó nyilvános kulcsadatokat az Azure-ba. Miután feltöltötte a nyilvános tanúsítványadatokat, az Azure felhasználhatja azon ügyfelek hitelesítéséhez, amelyeken telepítve lett egy, a megbízható főtanúsítványból létrehozott ügyféltanúsítvány. További megbízható főtanúsítványokat is feltölthet (legfeljebb 20-at).

1. A tanúsítványokat a rendszer hozzáadja a **Főtanúsítvány** szakasz **Pont–hely konfiguráció** lapjához.  
2. Győződjön meg arról, hogy Base-64 kódolású X.509 (.cer) fájlként exportálta a főtanúsítványt. Ebben a formátumban kell exportálnia a tanúsítványt, hogy szövegszerkesztővel meg tudja azt nyitni.
3. Nyissa megy a tanúsítványt egy szövegszerkesztővel, például a Jegyzettömbbel. A tanúsítványadatok másolásakor a szöveget egy folyamatos sorként másolja kocsivissza vagy új sor nélkül. A kocsivisszák és az új sorok megjelenítéséhez lehet, hogy módosítania kell a nézetet a szövegszerkesztőben a „Szimbólum megjelenítése/Minden karakter megjelenítése” beállításra. Csak a következő szakaszt másolja egy folyamatos sorként:

  ![Tanúsítványadatok](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png)
4. Illessze be a tanúsítványadatokat a **Nyilvános tanúsítványadatok** mezőbe. **Nevezze el** a tanúsítványt, majd a kattintson a **Mentés** gombra. Legfeljebb 20 megbízható főtanúsítványt adhat hozzá.

  ![Tanúsítvány feltöltése](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcertupload.png)

## <a name="installclientcert"></a>8. Exportált ügyféltanúsítvány telepítése

Ha a tanúsítvány létrehozásához használttól eltérő ügyfélszámítógépről szeretne pont–hely kapcsolatot létesíteni, akkor telepítenie kell egy ügyféltanúsítványt. Az ügyféltanúsítvány telepítésekor szükség lesz az ügyféltanúsítvány exportálásakor létrehozott jelszóra.

Győződjön meg arról, hogy az ügyféltanúsítványt .pfx fájlként exportálta a teljes tanúsítványlánccal együtt (ez az alapértelmezett beállítás). Egyéb esetben a főtanúsítvány adatai nem lesznek jelen az ügyfélszámítógépen, és az ügyfél nem fogja tudni megfelelően elvégezni a hitelesítést. 

A telepítés lépései az [ügyféltanúsítvány telepítésével](point-to-site-how-to-vpn-client-install-azure-cert.md) foglalkozó részben találhatók.

## <a name="clientconfig"></a>9. A VPN-ügyfél konfigurációs csomagjának létrehozása és telepítése

A VPN-ügyfél konfigurációs fájljai tartalmazzák az eszközök azon beállításait, amelyekkel pont–hely kapcsolaton keresztül kapcsolódhatnak egy virtuális hálózathoz. A VPN-ügyfél konfigurációs fájljainak létrehozásához és telepítéséhez szükséges utasításokért lásd:[A VPN-ügyfél konfigurációs fájljainak létrehozása és telepítése az Azure natív tanúsítványalapú hitelesítést használó pont–hely kapcsolatokhoz](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="connect"></a>10. Csatlakozás az Azure szolgáltatáshoz

### <a name="to-connect-from-a-windows-vpn-client"></a>Csatlakozás Windows VPN-ügyfélről

1. Csatlakozzon a virtuális hálózathoz. Ehhez navigáljon az ügyfélszámítógépen a VPN-kapcsolatokhoz, és keresse meg a létrehozott VPN-kapcsolatot. Ugyanaz a neve, mint a virtuális hálózatnak. Kattintson a **Connect** (Csatlakozás) gombra. Megjelenhet egy előugró üzenet, amely a tanúsítvány használatára utal. Kattintson a **Folytatás** gombra emelt szintű jogosultságok használatához.

2. A csatlakozás megkezdéséhez a **Kapcsolat** állapotlapon kattintson a **Csatlakozás** gombra. Ha megjelenik a **Tanúsítvány kiválasztása** képernyő, ellenőrizze, hogy az a csatlakozáshoz használni kívánt ügyféltanúsítványt mutatja-e. Ha nem, kattintson a legördülő nyílra, válassza ki a helyes tanúsítványt, majd kattintson az **OK** gombra.

  ![A VPN-ügyfél az Azure-hoz csatlakozik](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)
3. A kapcsolat létrejött.

  ![A kapcsolat létrejött](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)

#### <a name="troubleshoot-windows-p2s-connections"></a>Windows pont–hely kapcsolatok hibaelhárítása

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Csatlakozás Mac VPN-ügyfélről

A Hálózat párbeszédpanelen keresse meg a használni kívánt ügyfél profilját, majd kattintson a **Csatlakozás** gombra.

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

## <a name="add"></a>Megbízható főtanúsítványok hozzáadása vagy eltávolítása

A megbízható főtanúsítványokat felveheti vagy el is távolíthatja az Azure-ban. Főtanúsítvány eltávolításakor az abból a gyökérből létrehozott tanúsítvánnyal rendelkező ügyfelek nem fognak tudni hitelesítést végezni, így csatlakozni sem. Ha azt szeretné, hogy az ügyfelek hitelesítést végezhessenek és csatlakozni tudjanak, telepítenie kell egy olyan új ügyféltanúsítványt, amelyet az Azure által megbízhatónak tartott (feltöltött) főtanúsítványból hoztak létre.

### <a name="to-add-a-trusted-root-certificate"></a>Megbízható főtanúsítvány hozzáadása

Az Azure-ra legfeljebb 20 megbízható főtanúsítványt tölthet fel .cer fájl formájában. Útmutatásért lásd a jelen cikk [Megbízható főtanúsítvány feltöltése](#uploadfile) című szakaszát.

### <a name="to-remove-a-trusted-root-certificate"></a>Megbízható főtanúsítvány eltávolítása

1. A megbízható főtanúsítvány eltávolításához lépjen a virtuális hálózati átjáróhoz tartozó **Pont–hely konfiguráció** lapra.
2. Keresse meg az eltávolítani kívánt tanúsítványt a lap **Főtanúsítvány** szakaszában.
3. Kattintson a tanúsítvány melletti, három pontot ábrázoló gombra, majd az „Eltávolítás” parancsra.

## <a name="revokeclient"></a>Ügyféltanúsítvány visszavonása

Az ügyféltanúsítványokat vissza lehet vonni. A visszavont tanúsítványok listájával az egyes ügyféltanúsítványok alapján, szelektíven tagadhatja meg a pont–hely kapcsolódás lehetőségét. Ez a folyamat eltér a megbízható főtanúsítvány eltávolításától. Ha töröl egy .cer formátumú megbízható főtanúsítványt az Azure-ból, azzal megvonja a hozzáférést minden olyan ügyféltanúsítványtól, amelyet a visszavont főtanúsítvánnyal hoztak létre/írtak alá. A főtanúsítvány helyett az ügyféltanúsítvány visszavonása esetén a főtanúsítványból létrehozott többi tanúsítvány továbbra is használható hitelesítésre.

A szokásos gyakorlat az, hogy a főtanúsítvánnyal kezelik a hozzáférést a munkacsoport vagy a szervezet szintjén, az egyes felhasználókra vonatkozó részletesebb szabályozást pedig visszavont ügyféltanúsítványokkal oldják meg.

### <a name="revoke-a-client-certificate"></a>Ügyféltanúsítvány visszavonása

Az ügyféltanúsítványok visszavonásához vegye fel az ujjlenyomatot a visszavont tanúsítványok listájára.

1. Kérje le az ügyféltanúsítvány ujjlenyomatát. További információkat [a tanúsítványok ujjlenyomatának lekérését ismertető útmutatóban](https://msdn.microsoft.com/library/ms734695.aspx) találhat.
2. Másolja át az adatokat egy szövegszerkesztőbe, és távolítsa el az összes szóközt, hogy egy folyamatos karakterláncot kapjon.
3. Lépjen a virtuális hálózati átjáró **Pont–hely konfiguráció** lapjára. Ez ugyanaz a lap, amelyet a [megbízható főtanúsítvány feltöltéséhez](#uploadfile) használt.
4. A **Visszavont tanúsítványok** szakaszban adjon egy rövid nevet a tanúsítványnak (ennek nem kell megegyeznie a tanúsítvány köznapi nevével).
5. Másolja ki és illessze be az ujjlenyomat karakterláncát az **Ujjlenyomat** mezőbe.
6. A rendszer ellenőrzi az ujjlenyomatot, és automatikusan hozzáadja a visszavont tanúsítványok listájához. A képernyőn megjelenik egy üzenet, amely szerint a lista frissítése folyamatban van. 
7. A frissítés befejezését követően a tanúsítvány már nem használható csatlakozáshoz. Azok az ügyfelek, akik ezzel a tanúsítvánnyal próbálnak csatlakozni, egy üzenetet kapnak majd arról, hogy a tanúsítvány már nem érvényes.

## <a name="faq"></a>Pont–hely kapcsolatok – gyakori kérdések

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>További lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). A hálózatok és virtuális gépek ismertetését lásd az [Azure- és Linux-alapú virtuálisgép-hálózatok áttekintésében](../virtual-machines/linux/azure-vm-network-overview.md).
