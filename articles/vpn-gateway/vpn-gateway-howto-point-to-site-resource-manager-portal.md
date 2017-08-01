---
title: "Számítógép csatlakoztatása Azure-beli virtuális hálózathoz pont–hely kapcsolat használatával: Portal | Microsoft Docs"
description: "A Resource Managerrel és az Azure Portallal létrehozott pont–hely VPN Gateway-kapcsolattal biztonságosan csatlakozhat az Azure Virtual Networkhöz."
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
ms.date: 06/27/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 4045dd501ba70b04cb8ea06901c76072fc009018
ms.contentlocale: hu-hu
ms.lasthandoff: 06/28/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Pont–hely kapcsolat konfigurálása virtuális hálózat számára az Azure Portalon

Ez a cikk bemutatja, hogyan hozhat létre pont–hely kapcsolattal rendelkező virtuális hálózatot a Resource Manager-alapú üzemi modellben az Azure Portallal. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

A pont–hely (P2S) konfiguráció lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolatok akkor hasznosak, ha egy távoli helyről szeretne csatlakozni a virtuális hálózathoz, például otthonról vagy egy konferenciáról, vagy akkor, ha csak néhány ügyfelet szeretne csatlakoztatni egy virtuális hálózathoz. A P2S VPN kapcsolatot az ügyfélszámítógépről kezdeményezik a natív Windows VPN-ügyfél használatával. Az ügyfelek hitelesítése a kapcsolódáskor tanúsítványokkal történik. 


![Pont–hely diagram](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-connection-diagram.png)

A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet. A P2S-kapcsolat SSTP (Secure Socket Tunneling Protocol) használatával hozza létre a VPN-kapcsolatot. A kiszolgálói oldalon az SSTP 1.0, 1.1 és 1.2 verziója támogatott. Az ügyfél dönti el, hogy melyik verziót használja. Windows 8.1 és újabb kiadások esetén az SSTP alapértelmezés szerint az 1.2 verziót használja. A Pont–hely kapcsolatokról további információt a cikk végén, a [Pont–hely kapcsolatok – gyakori kérdések](#faq) című részben talál.

A pont–hely kapcsolatokhoz a következőkre van szükség:

* Útvonalalapú VPN-átjáró.
* A nyilvános kulcs (.cer fájl) egy főtanúsítványhoz, az Azure-ba feltöltve. A rendszer ezt megbízható tanúsítványnak tekinti, és ezt használja hitelesítéshez.
* A főtanúsítványból létrejön egy ügyféltanúsítvány, majd települ az egyes csatlakozó ügyfélszámítógépekre. A rendszer ezt a tanúsítványt használja ügyfélhitelesítéshez.
* Minden csatlakozó ügyfélszámítógépen létre kell hozni és telepíteni kell egy VPN-ügyfélkonfigurációs csomagot. Az ügyfélkonfigurációs csomag konfigurálja a már az operációs rendszeren lévő natív VPN-ügyfelet a virtuális hálózathoz való csatlakozáshoz szükséges adatokkal.

### <a name="example"></a>Példaértékek

Az alábbi értékek használatával létrehozhat egy tesztkörnyezetet, vagy segítségükkel értelmezheti a cikkben szereplő példákat:

* **Név: VNet1**
* **Címtér: 192.168.0.0/16**<br>Ebben a példában csak egy címteret használunk. Azonban a virtuális hálózatához több címteret is használhat.
* **Alhálózat neve: FrontEnd**
* **Alhálózati címtartomány: 192.168.1.0/24**
* **Előfizetés:**Ha több előfizetése is van, ellenőrizze, hogy a megfelelőt használja-e.
* **Erőforráscsoport: TestRG**
* **Hely: East US**
* **Átjáró-alhálózat: 192.168.200.0/24**
* **Virtuális hálózati átjáró neve: VNet1GW**
* **Átjáró típusa: VPN**
* **VPN típusa: Útvonalalapú**
* **Nyilvános IP-cím: VNet1GWpip**
* **Kapcsolat típusa: pont–hely**
* **Ügyfélcímkészlet: 172.16.201.0/24**<br>Azok a VPN-ügyfelek, amelyek ezzel a pont–hely kapcsolattal csatlakoznak a virtuális hálózathoz, az ügyfélcímkészletből kapnak IP-címet.

## <a name="createvnet"></a>1 – Virtuális hálózat létrehozása

Mielőtt elkezdi végrehajtani a lépéseket, győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial). Ha gyakorlatként hozza létre ezt a konfigurációt, használja a [példaértékeket](#example).

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="address"></a>2 – Címterek és alhálózatok megadása

Miután létrehozta a virtuális hálózatot, további címtereket és alhálózatokat adhat hozzá.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3 – Átjáróalhálózat hozzáadása

Mielőtt csatlakoztatja virtuális hálózatát egy átjáróhoz, létre kell hoznia az átjáróalhálózatot ahhoz a virtuális hálózathoz, amelyhez csatlakozni szeretne. Az átjárószolgáltatások az átjáróalhálózatban megadott IP-címeket használják. Ha lehetséges, hozzon létre egy átjáróalhálózatot /28 vagy /27 CIDR-blokk használatával annak érdekében, hogy a jövőbeli további konfigurációs követelmények számára elegendő IP-címet biztosíthasson.

Az ebben a részben szereplő képernyőképek példa referenciaként szolgálnak. Ügyeljen arra, hogy azt az átjáróalhálózati címtartományt használja, amelyik a konfigurációhoz szükséges értékeknek megfelelő.

### <a name="to-create-a-gateway-subnet"></a>Átjáróalhálózat létrehozása

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>4 – DNS-kiszolgáló megadása (nem kötelező)

Miután létrehozta a virtuális hálózatot, hozzáadhatja a DNS-kiszolgáló IP-címét a névfeloldás kezelésének érdekében. A megadott DNS-kiszolgálónak olyan DNS-kiszolgálónak kell lennie, amely fel tudja oldani azoknak az erőforrásoknak a nevét, amellyel Ön kapcsolatot fog létesíteni. A VPN-ügyfél konfigurációs csomagja, amelyet egy későbbi lépésben fog létrehozni, tartalmazza az ebben a beállításban megadott DNS-kiszolgálók IP-címeit. Ha a későbbiekben frissíteni szeretné a DNS-kiszolgálók listáját, létrehozhatja és telepítheti az új VPN-ügyfélkonfigurációs csomagot, amely az új listát tartalmazza.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>5 – Virtuális hálózati átjáró létrehozása

A pont–hely kapcsolatokhoz a következő beállításokra van szükség:

* Átjáró típusa: VPN
* VPN típusa: Útvonalalapú

### <a name="to-create-a-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

[!INCLUDE [create a vnet gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generatecert"></a>6 – Tanúsítványok előállítása

A tanúsítványokat az Azure használja a VPN-ügyfelek hitelesítésére a pont–hely VPN-kapcsolatokban. A főtanúsítvány nyilvánoskulcs-adatait feltölti az Azure-ba. A nyilvános kulcs ezután „megbízhatónak” tekinthető. Az ügyféltanúsítványokat a megbízható főtanúsítványból kell létrehozni, majd telepíteni kell az összes számítógépen a Certificates-Current User/Personal tanúsítványtárolóban. A tanúsítványt a rendszer az ügyfél hitelesítésére használja, amikor az a virtuális hálózathoz próbál csatlakozni. 

Önaláírt tanúsítványok használata esetén azokat megadott paraméterekkel kell létrehozni. Önaláírt tanúsítványt a [PowerShell és Windows 10](vpn-gateway-certificates-point-to-site.md), vagy [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) című cikkekben leírt utasítások alapján hozhat létre. Fontos, hogy az önaláírt legfelső szintű tanúsítványokkal végzett munka és az ügyféltanúsítványoknak a legfelső szintű tanúsítványból történő generálása során lépésről lépésre betartsa ezeket az utasításokat. Ellenkező esetben a létrehozott tanúsítványok nem lesznek kompatibilisek a P2S-kapcsolatokkal, és hibaüzenetet eredményeznek kapcsolódáskor.

### <a name="getcer"></a>1. lépés – A .cer fájl beszerzése a főtanúsítványhoz

[!INCLUDE [obtain root certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generateclientcert"></a>2. lépés – Ügyféltanúsítvány létrehozása

[!INCLUDE [generate client certificate](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="addresspool"></a>7 – Az ügyfélcímkészlet hozzáadása

Az ügyfélcímkészlet megadott magánhálózati IP-címek tartománya. A pont–hely kapcsolattal csatlakozó ügyfelek ebből a tartományból kapnak IP-címet. Olyan magánhálózati IP-címtartományt használjon, amely nincs átfedésben azzal a helyszíni hellyel, amelyről csatlakozik, vagy azzal a virtuális hálózattal, amelyhez csatlakozik.

1. Miután létrehozta a virtuális hálózati átjárót, navigáljon a virtuális hálózati átjáró paneljének **Beállítások** részéhez. A **Beállítások** részben kattintson a **Pont–hely konfiguráció** elemre a **Konfiguráció** panel megnyitásához.

  ![Pont–hely panel](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png)
2. Az automatikusan kitöltött tartományt törölheti, majd hozzáadhatja a használni kívánt magánhálózati IP-címtartományt. Kattintson a **Mentés** gombra a beállítás érvényesítéséhez és mentéséhez.

  ![Ügyfélcímkészlet](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/ipaddresspool.png)

## <a name="uploadfile"></a>8 – A főtanúsítvány .cer fájljának feltöltése

Az átjáró létrehozása után töltse fel a megbízható főtanúsítványhoz tartozó .cer fájlt (amely a nyilvános kulcsot tartalmazza) az Azure-ba. Miután feltöltötte a .cer fájlt, az Azure felhasználhatja azt azon ügyfelek hitelesítéséhez, amelyeken telepítve lett egy, a megbízható főtanúsítványból létrehozott ügyféltanúsítvány. Szükség szerint később további megbízhatófőtanúsítvány-fájlokat is feltölthet (legfeljebb 20-at). 

1. A tanúsítványokat a rendszer hozzáadja a **Főtanúsítvány** szakasz **Pont–hely konfiguráció** paneléhez.  
2. Győződjön meg arról, hogy Base-64 kódolású X.509 (.cer) fájlként exportálta a főtanúsítványt. Ebben a formátumban kell exportálnia a tanúsítványt, hogy szövegszerkesztővel meg tudja azt nyitni.
3. Nyissa megy a tanúsítványt egy szövegszerkesztővel, például a Jegyzettömbbel. A tanúsítványadatok másolásakor a szöveget egy folyamatos sorként másolja kocsivissza vagy új sor nélkül. A kocsivisszák és az új sorok megjelenítéséhez lehet, hogy módosítania kell a nézetet a szövegszerkesztőben a „Szimbólum megjelenítése/Minden karakter megjelenítése” beállításra. Csak a következő szakaszt másolja egy folyamatos sorként:

  ![Tanúsítványadatok](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png)
4. Illessze be a tanúsítványadatokat a **Nyilvános tanúsítványadatok** mezőbe. **Nevezze el** a tanúsítványt, majd a kattintson a **Mentés** gombra. Legfeljebb 20 megbízható főtanúsítványt adhat hozzá.

  ![Tanúsítvány feltöltése](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcertupload.png)

## <a name="clientconfig"></a>9 – A VPN-ügyfél konfigurációs csomagjának telepítése

Ha pont–hely típusú VPN-kapcsolattal szeretne kapcsolódni egy virtuális hálózathoz, minden ügyfélen telepíteni kell egy csomagot a natív Windows VPN-ügyfél konfigurálásához. A konfigurációs csomag konfigurálja a natív Windows VPN-ügyfelet a virtuális hálózathoz való csatlakozáshoz szükséges beállításokkal, továbbá, ha megadott egy DNS-kiszolgálót a virtuális hálózat számára, akkor tartalmazza a DNS-kiszolgáló IP-címét, amelyet az ügyfél a névfeloldáshoz fog használni. Ha később, az ügyfél-konfigurációs csomag létrehozása után módosítja a megadott DNS-kiszolgálót, ne felejtsen el új ügyfél-konfigurációs csomagot létrehozni az ügyfélszámítógépeken való telepítéshez.

Használhatja a VPN-ügyfél azonos konfigurációs csomagját minden ügyfélszámítógépen, feltéve, hogy a verzió megfelel az ügyfél architektúrájának. A támogatott ügyfél operációs rendszerek listáját a cikk végén, a [Pont–hely kapcsolatok – gyakori kérdések](#faq) című szakaszban tekintheti meg.

### <a name="step-1---download-the-client-configuration-package"></a>1. rész: Az ügyfél-konfigurációs csomag letöltése

1. A **Pont–hely konfiguráció** panelen kattintson a **VPN-ügyfél letöltése** elemre a **VPN-ügyfél letöltése** panel megnyitásához. A csomag létrehozása egy-két percet vesz igénybe.

  ![VPN-ügyfél letöltése, 1](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadvpnclient1.png)
2. Válassza ki az ügyfélnek megfelelő csomagot, majd kattintson a **Letöltés** gombra. Mentse a konfigurációscsomag-fájlt. Telepítse a VPN-ügyfélkonfigurációs csomagot minden olyan ügyfélszámítógépen, amely csatlakozik a virtuális hálózathoz.

  ![VPN-ügyfél letöltése, 2](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpnclient.png)

### <a name="step-2---install-the-client-configuration-package"></a>2. rész: Az ügyfél-konfigurációs csomag telepítése

1. Másolja a konfigurációs fájlt a virtuális hálózathoz csatlakoztatni kívánt számítógépre. 
2. Kattintson duplán az .exe fájlra, hogy a csomagot az ügyfélszámítógépre telepítse. Mivel a konfigurációs csomagot Ön hozta létre, nincs aláírva, ezért lehet, hogy figyelmeztetés jelenik meg. Ha egy Windows SmartScreen előugró ablak jelenik meg, kattintson a **További információ** (a bal oldalon), majd a **Futtatás mindenképpen** elemre a csomag telepítéséhez.
3. Telepítse a csomagot az ügyfélszámítógépen. Ha egy Windows SmartScreen előugró ablak jelenik meg, kattintson a **További információ** (a bal oldalon), majd a **Futtatás mindenképpen** elemre a csomag telepítéséhez.
4. Nyissa meg az ügyfélszámítógépen a **Hálózati beállítások** eszközt, és kattintson a **VPN** elemre. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott.

## <a name="installclientcert"></a>10 – Az ügyféltanúsítvány telepítése

Ha a tanúsítvány létrehozásához használttól eltérő ügyfélszámítógépről szeretne pont–hely kapcsolatot létesíteni, akkor telepítenie kell egy ügyféltanúsítványt. Az ügyféltanúsítvány telepítésekor szükség lesz az ügyféltanúsítvány exportálásakor létrehozott jelszóra. Ehhez általában annyit kell tennie, hogy duplán rákattint a tanúsítványra, és telepíti azt. További információkért lásd az [exportált ügyféltanúsítványok telepítését](vpn-gateway-certificates-point-to-site.md#install) ismertető cikket.

## <a name="connect"></a>11 – Csatlakozás az Azure-hoz

1. Csatlakozzon a virtuális hálózathoz. Ehhez navigáljon az ügyfélszámítógépen a VPN-kapcsolatokhoz, és keresse meg a létrehozott VPN-kapcsolatot. Ugyanaz a neve, mint a virtuális hálózatnak. Kattintson a **Connect** (Csatlakozás) gombra. Megjelenhet egy előugró üzenet, amely a tanúsítvány használatára utal. Kattintson a **Folytatás** gombra emelt szintű jogosultságok használatához.

2. A csatlakozás megkezdéséhez a **Kapcsolat** állapotlapon kattintson a **Csatlakozás** gombra. Ha megjelenik a **Tanúsítvány kiválasztása** képernyő, ellenőrizze, hogy az a csatlakozáshoz használni kívánt ügyféltanúsítványt mutatja-e. Ha nem, kattintson a legördülő nyílra, válassza ki a helyes tanúsítványt, majd kattintson az **OK** gombra.

  ![A VPN-ügyfél az Azure-hoz csatlakozik](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)
3. A kapcsolat létrejött.

  ![A kapcsolat létrejött](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)

Ha problémákat tapasztal a csatlakozás során, ellenőrizze a következőket:

- Nyissa meg a **Felhasználói tanúsítványok kezelése** elemet, és keresse meg a **Megbízható legfelső szintű hitelesítésszolgáltatók\Tanúsítványok** részt. Ellenőrizze, hogy a főtanúsítvány szerepel-e a listában. A főtanúsítvány a hitelesítés működéséhez szükséges. Ha .pfx formátumú ügyféltanúsítványt exportál az alapértelmezett „Minden tanúsítvány belefoglalása a tanúsítványláncba” érték használatával, a főtanúsítvány információit is exportálja a program. Amikor telepíti az ügyféltanúsítványt, az alkalmazás a főtanúsítványt is telepíti az ügyfélszámítógépre. 

- Ha vállalati hitelesítésszolgáltatói megoldás használatával kiadott tanúsítványt használ, és problémák merülnek fel a hitelesítés során, ellenőrizze a hitelesítési sorrendet az ügyféltanúsítványon. A hitelesítési lista sorrendjének ellenőrzéséhez kattintson duplán az ügyféltanúsítványra, és lépjen a **Részletek > Kibővített kulcshasználat** részre. Ellenőrizze, hogy a listán az „ügyfél-hitelesítés” jelenik-e meg első helyen. Ha nem, ki kell adnia egy ügyféltanúsítványt, amely az ügyfél-hitelesítést a lista első helyén tartalmazó felhasználói sablonon alapul.


## <a name="verify"></a>12 – A kapcsolat ellenőrzése

1. Annak ellenőrzéséhez, hogy a VPN-kapcsolat aktív-e, nyisson meg egy rendszergazda jogú parancssort, és futtassa az *ipconfig/all* parancsot.
2. Tekintse meg az eredményeket. Figyelje meg, hogy a kapott IP-cím azok közül a címek közül való, amelyeket a pont–hely VPN-ügyfél konfigurációjának címkészletében megadott. Az eredmények az alábbi példához hasonlóak:
   
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


Ha problémákat tapasztal, amikor P2S használatával szeretne kapcsolódni egy virtuális géphez, az „ipconfig” használatával ellenőrizze annak a számítógépnek az Ethernet-adapteréhez rendelt IPv4-címet, amelyről csatlakozik. Ha az IP-cím azon virtuális hálózat tartományában található, amelyhez csatlakozni kíván, vagy a VPN-ügyfél címkészletének címtartományában, akkor átfedő címtérről beszélünk. Ilyen átfedés esetén a hálózati forgalom nem éri el az Azure-t, és a helyi hálózaton marad. Ha a hálózati címtartományokban nincs átfedés, de mégsem tud kapcsolódni a virtuális géphez, olvassa el a [Troubleshoot Remote Desktop connections to a VM](../virtual-machines/windows/troubleshoot-rdp-connection.md) (A virtuális gépekhez való távoli asztali kapcsolatok hibáinak elhárítása) című témakört.

## <a name="connectVM"></a>Csatlakozás virtuális géphez

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="add"></a>Megbízható főtanúsítványok hozzáadása vagy eltávolítása

A megbízható főtanúsítványokat felveheti vagy el is távolíthatja az Azure-ban. Főtanúsítvány eltávolításakor az abból a gyökérből létrehozott tanúsítvánnyal rendelkező ügyfelek nem fognak tudni hitelesítést végezni, így csatlakozni sem. Ha azt szeretné, hogy az ügyfelek hitelesítést végezhessenek és csatlakozni tudjanak, telepítenie kell egy olyan új ügyféltanúsítványt, amelyet az Azure által megbízhatónak tartott (feltöltött) főtanúsítványból hoztak létre.

### <a name="to-add-a-trusted-root-certificate"></a>Megbízható főtanúsítvány hozzáadása

Az Azure-ra legfeljebb 20 megbízható főtanúsítványt tölthet fel .cer fájl formájában. Útmutatásért lásd a jelen cikk [Megbízható főtanúsítvány feltöltése](#uploadfile) című szakaszát.

### <a name="to-remove-a-trusted-root-certificate"></a>Megbízható főtanúsítvány eltávolítása

1. A megbízható főtanúsítvány eltávolításához lépjen a virtuális hálózati átjáróhoz tartozó **Pont–hely konfiguráció** panelre.
2. Keresse meg az eltávolítani kívánt tanúsítványt a panel **Főtanúsítvány** szakaszában.
3. Kattintson a tanúsítvány melletti, három pontot ábrázoló gombra, majd az „Eltávolítás” parancsra.

## <a name="revokeclient"></a>Ügyféltanúsítvány visszavonása

Az ügyféltanúsítványokat vissza lehet vonni. A visszavont tanúsítványok listájával az egyes ügyféltanúsítványok alapján, szelektíven tagadhatja meg a pont–hely kapcsolódás lehetőségét. Ez a folyamat eltér a megbízható főtanúsítvány eltávolításától. Ha töröl egy .cer formátumú megbízható főtanúsítványt az Azure-ból, azzal megvonja a hozzáférést minden olyan ügyféltanúsítványtól, amelyet a visszavont főtanúsítvánnyal hoztak létre/írtak alá. A főtanúsítvány helyett az ügyféltanúsítvány visszavonása esetén a főtanúsítványból létrehozott többi tanúsítvány továbbra is használható hitelesítésre.

A szokásos gyakorlat az, hogy a főtanúsítvánnyal kezelik a hozzáférést a munkacsoport vagy a szervezet szintjén, az egyes felhasználókra vonatkozó részletesebb szabályozást pedig visszavont ügyféltanúsítványokkal oldják meg.

### <a name="to-revoke-a-client-certificate"></a>Ügyféltanúsítvány visszavonása

Az ügyféltanúsítványok visszavonásához vegye fel az ujjlenyomatot a visszavont tanúsítványok listájára.

1. Kérje le az ügyféltanúsítvány ujjlenyomatát. További információkat [a tanúsítványok ujjlenyomatának lekérését ismertető útmutatóban](https://msdn.microsoft.com/library/ms734695.aspx) találhat.
2. Másolja át az adatokat egy szövegszerkesztőbe, és távolítsa el az összes szóközt, hogy egy folyamatos karakterláncot kapjon.
3. Lépjen a virtuális hálózati átjáró **Pont–hely konfiguráció** paneljére. Ez ugyanaz a panel, amelyet a [megbízható főtanúsítvány feltöltéséhez](#uploadfile) használt.
4. A **Visszavont tanúsítványok** szakaszban adjon egy rövid nevet a tanúsítványnak (ennek nem kell megegyeznie a tanúsítvány köznapi nevével).
5. Másolja ki és illessze be az ujjlenyomat karakterláncát az **Ujjlenyomat** mezőbe.
6. A rendszer ellenőrzi az ujjlenyomatot, és automatikusan hozzáadja a visszavont tanúsítványok listájához. A képernyőn megjelenik egy üzenet, amely szerint a lista frissítése folyamatban van. 
7. A frissítés befejezését követően a tanúsítvány már nem használható csatlakozáshoz. Azok az ügyfelek, akik ezzel a tanúsítvánnyal próbálnak csatlakozni, egy üzenetet kapnak majd arról, hogy a tanúsítvány már nem érvényes.

## <a name="faq"></a>Pont–hely kapcsolatok – gyakori kérdések

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Következő lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). A hálózatok és virtuális gépek ismertetését lásd az [Azure- és Linux-alapú virtuálisgép-hálózatok áttekintésében](../virtual-machines/linux/azure-vm-network-overview.md).

