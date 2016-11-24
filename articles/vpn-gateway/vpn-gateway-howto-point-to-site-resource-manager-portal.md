---
title: "Pont–hely VPN Gateway-kapcsolat konfigurálása virtuális hálózathoz a Resource Manager-alapú üzemi modellel és az Azure Portallal | Microsoft Docs"
description: "A Resource Managerrel és az Azure Portallal létrehozott pont–hely VPN Gateway-kapcsolattal biztonságosan csatlakozhat az Azure Virtual Networkhöz."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: f6fa6511c8d54e191de27fda73aad9feb734191f
ms.openlocfilehash: 0250f58f00f13483a629ca35e6a911484ba52a66


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Pont–hely kapcsolat konfigurálása virtuális hálózat számára az Azure Portalon
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Klasszikus – Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

A pont–hely (P2S) konfiguráció lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat akkor hasznos, ha távoli helyről szeretne csatlakozni a virtuális hálózathoz, például otthonról vagy konferenciáról, vagy akkor, ha csak néhány ügyfelet kíván csatlakoztatni a virtuális hálózathoz. 

A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet a működéshez. VPN-kapcsolat létesítéséhez manuálisan kell kezdeményezni a kapcsolatot az ügyfélszámítógépről. További információk a pont–hely kapcsolatokról: [VPN Gateway – gyakori kérdések](vpn-gateway-vpn-faq.md#point-to-site-connections) és [Tudnivalók a létesítmények közötti kapcsolatokról](vpn-gateway-plan-design.md).

Ez a cikk végigvezeti azokon a lépéseken, amelyekkel pont–hely kapcsolattal rendelkező virtuális hálózatot hozhat létre a Resource Manager-alapú üzemi modellben az Azure Portallal.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Üzemi modellek és módszerek a pont–hely (P2S) kapcsolatokhoz
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Az alábbi táblázatban látható a két üzemi modell és a pont–hely konfigurációkhoz elérhető üzembe helyezési módszerek. Amint elérhetővé válik a konfigurációs lépéseket ismertető cikk, egy arra mutató közvetlen hivatkozás szerepel majd a táblázatban.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Alapvető munkafolyamat
![Pont–hely diagram](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-to-site")

### <a name="a-nameexampleaexample-values"></a><a name="example"></a>Példaértékek
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

## <a name="before-beginning"></a>Mielőtt hozzálát
* Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

## <a name="a-namecreatevnetapart-1---create-a-virtual-network"></a><a name="createvnet"></a>1. rész – Virtuális hálózat létrehozása
Ha gyakorlatként hozza létre ezt a konfigurációt, használja a [példaértékeket](#example).

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

### <a name="2-add-additional-address-space-and-subnets"></a>2. További címterek és alhálózatok felvétele
Miután létrehozta a virtuális hálózatot, további címtereket és alhálózatokat adhat hozzá.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

### <a name="3-create-a-gateway-subnet"></a>3. Átjáróalhálózat létrehozása
Mielőtt csatlakoztatja virtuális hálózatát egy átjáróhoz, létre kell hoznia az átjáróalhálózatot ahhoz a virtuális hálózathoz, amelyhez csatlakozni szeretne. Ha lehetséges, a legjobb megoldás egy átjáróalhálózat létrehozása /28 vagy /27 CIDR-blokk használatával annak érdekében, hogy a jövőbeli további konfigurációs követelmények számára elegendő IP-címet biztosíthasson.

Az ebben a részben szereplő képernyőképek példa referenciaként szolgálnak. Ügyeljen arra, hogy azt az átjáróalhálózati címtartományt használja, amelyik a konfigurációhoz szükséges értékeknek megfelelő.

**Átjáróalhálózat létrehozása**

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="a-namednsa4-specify-a-dns-server-optional"></a><a name="dns"></a>4. DNS-kiszolgáló megadása (nem kötelező)
[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="a-namecreategwapart-2---create-a-virtual-network-gateway"></a><a name="creategw"></a>2. rész – Virtuális hálózati átjáró létrehozása
A pont–hely kapcsolatokhoz a következő beállításokra van szükség:

* Átjáró típusa: VPN
* VPN típusa: Útvonalalapú

### <a name="to-create-a-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="a-namegeneratecertapart-3---generate-certificates"></a><a name="generatecert"></a>3. rész – Tanúsítványok előállítása
A tanúsítványokat az Azure használja a VPN-ügyfelek hitelesítésére a pont–hely VPN-kapcsolatokban. A nyilvános tanúsítványadatokat (nem a titkos kulcsot) egy Base-64 kódolású X.509 .cer fájlként exportálja egy vállalati tanúsítványmegoldás által létrehozott főtanúsítványból vagy egy önaláírt főtanúsítványból. Ezután importálja a nyilvános tanúsítványadatokat a főtanúsítványból az Azure-ba. Ezenfelül létre kell hoznia egy ügyféltanúsítványt az ügyfelek főtanúsítványából. Minden egyes ügyfélen, amelynek pont–hely kapcsolattal kell csatlakoznia a virtuális hálózathoz, telepíteni kell egy, a főtanúsítványból létrehozott ügyféltanúsítványt.

### <a name="a-namegetcera1-obtain-the-cer-file-for-the-root-certificate"></a><a name="getcer"></a>1. A .cer fájl beszerzése a főtanúsítványhoz
Nagyvállalati megoldás esetén használhatja meglévő tanúsítványláncát. Ha nem használ vállalati hitelesítésszolgáltatói (CA) megoldást, létrehozhat egy önaláírt főtanúsítványt. Az önaláírt tanúsítvány létrehozásának egyik módja a makecert.

* Ha vállalati tanúsítványrendszert használ, szerezze be a használni kívánt főtanúsítványhoz tartozó .cer fájlt. 
* Ha nem vállalati tanúsítványmegoldást használ, létre kell hoznia egy önaláírt főtanúsítványt. A Windows 10 esetén használható lépéseket lásd: [Working with self-signed root certificates for Point-to-Site configurations](vpn-gateway-certificates-point-to-site.md) (Önaláírt főtanúsítványok használata pont–hely konfigurációk esetében).

1. A .cer fájl tanúsítványból való beszerzéséhez nyissa meg a **certmgr.msc** fájlt, és keresse meg a főtanúsítványt. Kattintson a jobb gombbal az önaláírt főtanúsítványra, és kattintson a **minden tevékenység**, majd az **exportálás** elemre. Megnyílik a **Tanúsítványexportáló varázsló**.
2. A varázslóban kattintson a **Tovább** gombra, válassza a **Nem, nem akarom exportálni a titkos kulcsomat** lehetőségre, majd kattintson a **Tovább** gombra.
3. Az **Exportfájlformátum** lapon válassza a **Base-64 kódolású X.509 (.CER)** lehetőséget. Ezután kattintson a **Tovább** gombra. 
4. Az **Exportálandó fájl** lapon a **Tallózás** gombra kattintva keresse meg azt a helyet, ahová exportálni szeretné a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **Next** (Tovább) gombra.
5. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához.

### <a name="a-namegenerateclientcerta2-generate-a-client-certificate"></a><a name="generateclientcert"></a>2. Ügyféltanúsítvány létrehozása
Létrehozhat egy egyedi tanúsítványt minden csatlakozó ügyfél számára, vagy használhatja ugyanazt a tanúsítványt több ügyfél esetén. Az egyedi ügyféltanúsítványok előállításának előnye az, hogy szükség esetén visszavonhat egyetlen tanúsítványt. Ha azonban mindenki ugyanazt az ügyféltanúsítványt használja, és úgy találja, hogy egyetlen ügyféltől vissza kell vonnia a tanúsítványt, az összes olyan ügyfél számára elő kell állítania és telepítenie kell új tanúsítványokat, amelyek az adott tanúsítványt használják a hitelesítéshez.

* Ha vállalati tanúsítványmegoldást használ, az általános 'name@yourdomain.com', formátumban hozza létre az ügyféltanúsítványokat a „tartománynév\felhasználónév” formátuma helyett. 
* Ha önaláírt tanúsítványt használ, az ügyféltanúsítvány létrehozásával kapcsolatban tekintse meg a [Working with self-signed root certificates for Point-to-Site configurations](vpn-gateway-certificates-point-to-site.md) (Önaláírt főtanúsítványok használata pont–hely konfigurációk esetében).

### <a name="a-nameexportclientcerta3-export-the-client-certificate"></a><a name="exportclientcert"></a>3. Az ügyféltanúsítvány exportálása
A hitelesítéshez ügyféltanúsítványra van szükség. Az ügyféltanúsítvány létrehozása után exportálja azt. Az exportált ügyféltanúsítvány később telepítve lesz mindegyik ügyfélszámítógépen.

1. Az ügyféltanúsítványok exportálásához a *certmgr.msc* fájlt használhatja. Kattintson a jobb gombbal az exportálni kívánt ügyféltanúsítványra, majd a **minden feladat** és az **exportálás** elemre.
2. Exportálja az ügyféltanúsítványt a titkos kulccsal. Ez egy *.pfx* fájl. Jegyezze fel vagy jegyezze meg a jelszót (kulcsot), amelyet beállított a tanúsítványhoz.

## <a name="a-nameaddresspoolapart-4---add-the-client-address-pool"></a><a name="addresspool"></a>4. rész – Az ügyfélcímkészlet hozzáadása
1. Miután létrehozta a virtuális hálózati átjárót, navigáljon a virtuális hálózati átjáró paneljének **Beállítások** részéhez. A **Beállítások** részben kattintson a **Pont–hely konfiguráció** elemre a **Konfiguráció** panel megnyitásához.
   
    ![pont–hely panel](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png "point to site blade")
2. A **Címkészlet** azon IP-címek készletét jelenti, amelyek közül a csatlakozó ügyfelek IP-címet kapnak. Adja hozzá a címkészletet, majd kattintson a **Mentés** gombra.
   
    ![ügyfélcímkészlet](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/addresspool.png "client address pool")

## <a name="a-nameuploadfileapart-5---upload-the-root-certificate-cer-file"></a><a name="uploadfile"></a>5. rész – A főtanúsítvány .cer fájljának feltöltése
Miután létrehozta az átjárót, feltöltheti a megbízható főtanúsítványhoz tartozó .cer fájlt az Azure-ba. Legfeljebb 20 főtanúsítványhoz tölthet fel fájlokat. A főtanúsítvány titkos kulcsát ne töltse fel az Azure-ba. Miután feltöltötte a .cer fájlt, az Azure használhatja azt azon ügyfelek hitelesítéséhez, amelyek a virtuális hálózathoz csatlakoznak.

1. Navigáljon a **Pont–hely konfiguráció** panelhez. A panel **Főtanúsítvány**szakaszában fogja hozzáadni a .cer fájlokat.
   
    ![pont–hely panel](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcert.png "point to site blade")
2. Győződjön meg arról, hogy Base-64 kódolású X.509 (.cer) fájlként exportálta a főtanúsítványt. Ebben a formátumban kell exportálnia, hogy szövegszerkesztővel meg tudja nyitni a tanúsítványt.
3. Nyissa megy a tanúsítványt egy szövegszerkesztővel, például a Jegyzettömbbel. Csak a következő szakaszt másolja:
   
    ![tanúsítványadatok](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png "certificate data")
4. Illessze be a tanúsítványadatot a portál **Nyilvános tanúsítványadatok** szakaszába. Írja be a tanúsítvány nevét a **Név** mezőbe, majd kattintson a **Mentés** gombra. Legfeljebb 20 megbízható főtanúsítványt adhat hozzá.
   
    ![tanúsítvány feltöltése](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploadcert.png "certificate upload")

## <a name="a-nameclientconfigapart-6---download-and-install-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>6. rész – A VPN-ügyfél konfigurációs csomagjának letöltése és telepítése
Az Azure-hoz pont–hely kapcsolattal csatlakozó ügyfeleken az ügyféltanúsítványon kívül telepíteni kell egy VPN-ügyfélkonfigurációs csomagot is. A windowsos ügyfelekhez érhetők el VPN-ügyfélkonfigurációs csomagok. 

A VPN-ügyfélcsomag tartalmazza a Windows beépített VPN-ügyfélszoftverének konfigurálásához szükséges információkat. A konfiguráció arra a VPN-re jellemző, amelyhez csatlakozni szeretne. A csomag nem telepít további szoftvert. További információk: [VPN Gateway – gyakori kérdések](vpn-gateway-vpn-faq.md#point-to-site-connections).

1. A **Pont–hely konfiguráció** panelen kattintson a **VPN-ügyfél letöltése** elemre a **VPN-ügyfél letöltése** panel megnyitásához.
   
    ![VPN-ügyfél letöltése](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadclient.png "VPN client download")
2. Válassza ki az ügyfélnek megfelelő csomagot, majd kattintson a **Letöltés** gombra. 64 bites ügyfelek esetén válassza az **AMD64** lehetőséget. 32 bites ügyfelek esetén válassza az **x86** lehetőséget.
3. Telepítse a csomagot az ügyfélszámítógépen. Ha egy SmartScreen előugró ablak jelenik meg, kattintson a **További információ**, majd a **Futtatás mindenképpen** lehetőségre a csomag telepítéséhez.
4. Nyissa meg az ügyfélszámítógépen a **Hálózati beállítások** eszközt, és kattintson a **VPN** elemre. A listán láthatja a kapcsolatot. Annak a virtuális hálózatnak a nevét mutatja, amellyel kapcsolatot létesít, és következő példára hasonlít: 
   
    ![VPN-ügyfél](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpn.png "VPN client")

## <a name="a-nameinstallclientcertapart-7---install-the-client-certificate"></a><a name="installclientcert"></a>7. rész – Az ügyféltanúsítvány telepítése
Minden ügyfélszámítógépnek rendelkeznie kell egy ügyféltanúsítvánnyal a hitelesítéshez. Az ügyféltanúsítvány telepítésekor szükség lesz az ügyféltanúsítvány exportálásakor létrehozott jelszóra.

1. Másolja a .pfx fájlt az ügyfélszámítógépre.
2. Kattintson duplán a .pfx fájlra a telepítéshez. Ne módosítsa a telepítés helyét.

## <a name="a-nameconnectapart-8---connect-to-azure"></a><a name="connect"></a>8. rész – Csatlakozás az Azure-hoz
1. Csatlakozzon a virtuális hálózathoz. Ehhez navigáljon az ügyfélszámítógépen a VPN-kapcsolatokhoz, és keresse meg a létrehozott VPN-kapcsolatot. Ugyanaz a neve, mint a virtuális hálózatnak. Kattintson a **Connect** (Csatlakozás) gombra. Megjelenhet egy előugró üzenet, amely a tanúsítvány használatára utal. Ilyen esetében kattintson a **Folytatás** gombra emelt szintű jogosultságok használatához. 
2. A csatlakozás megkezdéséhez a **Kapcsolat** állapotlapon kattintson a **Csatlakozás** gombra. Ha megjelenik a **Tanúsítvány kiválasztása** képernyő, ellenőrizze, hogy az a csatlakozáshoz használni kívánt ügyféltanúsítványt mutatja-e. Ha nem, kattintson a legördülő nyílra, válassza ki a helyes tanúsítványt, majd kattintson az **OK** gombra.
   
    ![2. VPN-ügyfél](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "VPN client connection")
3. Ekkor létre kell jönnie a kapcsolatnak.
   
    ![3. VPN-ügyfél](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "VPN client connection 2")

## <a name="a-nameverifyapart-9---verify-your-connection"></a><a name="verify"></a>9. rész – A kapcsolat ellenőrzése
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

## <a name="a-nameaddato-add-or-remove-trusted-root-certificates"></a><a name="add"></a>Megbízható főtanúsítványok hozzáadása vagy eltávolítása
A megbízható főtanúsítványt eltávolíthatja az Azure-ból. Ha eltávolít egy megbízható tanúsítványt, akkor az abból a főtanúsítványból generált ügyféltanúsítványok nem tudnak többé pont–hely kapcsolattal csatlakozni az Azure-hoz. Ha azt szeretné, hogy az ügyfelek csatlakozni tudjanak, telepíteniük kell egy olyan új ügyféltanúsítvány, amelyet az Azure által megbízhatónak tartott tanúsítványból generáltak.

A visszavont ügyféltanúsítványok listáját a**Pont–hely konfiguráció** panelen kezelheti. Ez az a panel, amelyet a [megbízható főtanúsítvány feltöltéséhez](#uploadfile) használt.

## <a name="a-namerevokeclientato-manage-the-list-of-revoked-client-certificates"></a><a name="revokeclient"></a>A visszavont ügyféltanúsítványok listájának kezelése
Az ügyféltanúsítványokat vissza lehet vonni. A visszavont tanúsítványok listájával az egyes ügyféltanúsítványok alapján, szelektíven tagadhatja meg a pont–hely kapcsolódás lehetőségét. Ha töröl egy .cer formátumú főtanúsítványt az Azure-ból, azzal megvonja a hozzáférést minden olyan ügyféltanúsítványtól, amelyet a visszavont főtanúsítvánnyal generáltak/írtak alá. Ha egy konkrét ügyféltanúsítványt szeretne visszavonni, és nem a főtanúsítványt, erre is lehetősége nyílik. Ily módon továbbra is érvényesek marad a főtanúsítványból generált többi tanúsítvány. 

A szokásos gyakorlat az, hogy a főtanúsítvánnyal kezelik a hozzáférést a munkacsoport vagy a szervezet szintjén, az egyes felhasználókra vonatkozó részletesebb szabályozást pedig visszavont ügyféltanúsítványokkal oldják meg.

A visszavont ügyféltanúsítványok listáját a**Pont–hely konfiguráció** panelen kezelheti. Ez az a panel, amelyet a [megbízható főtanúsítvány feltöltéséhez](#uploadfile) használt.

## <a name="next-steps"></a>Következő lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).




<!--HONumber=Nov16_HO3-->


