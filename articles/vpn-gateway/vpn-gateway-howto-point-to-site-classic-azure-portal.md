---
title: "Pont–hely VPN Gateway-kapcsolat konfigurálása Azure Virtual Networkhöz az Azure Portal használatával | Microsoft Docs"
description: "Az Azure Portal használatával létrehozott pont–hely VPN Gateway-kapcsolattal biztonságosan csatlakozhat az Azure Virtual Networkhöz."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 87d52de2d6ccb80390f8680371527a23904c5bb0


---
# <a name="configure-a-pointtosite-connection-to-a-vnet-using-the-azure-portal"></a>Pont–hely kapcsolat konfigurálása virtuális hálózat számára az Azure Portalon
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Klasszikus – Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Ez a cikk lépésről lépésre bemutatja, hogyan hozható létre virtuális hálózat pont–hely kapcsolattal a klasszikus üzemi modellben az Azure Portal használatával. A pont–hely (P2S) konfiguráció lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat akkor hasznos, ha távoli helyről szeretne csatlakozni a virtuális hálózathoz, például otthonról vagy konferenciáról, vagy akkor, ha csak néhány ügyfelet kíván csatlakoztatni a virtuális hálózathoz.

A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet a működéshez. VPN-kapcsolat létesítéséhez manuálisan kell kezdeményezni a kapcsolatot az ügyfélszámítógépről. További információk a pont–hely kapcsolatokról: [VPN Gateway – gyakori kérdések](vpn-gateway-vpn-faq.md#point-to-site-connections) és [Információk a VPN Gateway-ről](vpn-gateway-about-vpngateways.md#point-to-site).

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Üzemi modellek és módszerek a pont–hely (P2S) kapcsolatokhoz
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Az alábbi táblázatban látható a két üzemi modell és a pont–hely konfigurációkhoz elérhető üzembe helyezési módszerek. Amint elérhetővé válik a konfigurációs lépéseket ismertető cikk, egy arra mutató közvetlen hivatkozás szerepel majd a táblázatban.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Alapvető munkafolyamat
![Pont–hely diagram](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-to-site")

A következő szakaszok végigvezetik virtuális hálózat felé irányuló pont–hely kapcsolat kialakításán. 

1. Virtuális hálózat és VPN Gateway létrehozása
2. Tanúsítványok előállítása
3. A .cer fájl feltöltése
4. A VPN-ügyfél konfigurációs csomagjának létrehozása
5. Az ügyfélszámítógép konfigurálása
6. Csatlakozás az Azure szolgáltatáshoz

### <a name="example-settings"></a>Példabeállítások
Használhatja a következő példabeállításokat:

* **Név: VNet1**
* **Címtér: 192.168.0.0/16**
* **Alhálózat neve: FrontEnd**
* **Alhálózati címtartomány: 192.168.1.0/24**
* **Előfizetés:**Ha több előfizetése is van, ellenőrizze, hogy a megfelelőt használja-e.
* **Erőforráscsoport: TestRG**
* **Hely: East US**
* **Kapcsolat típusa: pont–hely**
* **Ügyfélcímtér: 172.16.201.0/24**. Azok a VPN-ügyfelek, amelyek ezzel a pont–hely kapcsolattal csatlakoznak a virtuális hálózathoz, a megadott címkészletből kapnak IP-címet.
* **Átjáró-alhálózat: 192.168.200.0/24**. Az átjáró-alhálózat neve „GatewaySubnet” kell legyen.
* **Méret:** Válassza ki az átjáró használni kívánt termékváltozatát.
* **Útválasztási típus: dinamikus**

## <a name="a-namevnetvpnasection-1-create-a-virtual-network-and-a-vpn-gateway"></a><a name="vnetvpn"></a>1. szakasz: Virtuális hálózat és VPN Gateway létrehozása
### <a name="a-namecreatevnetapart-1-create-a-virtual-network"></a><a name="createvnet"></a>1. rész: Virtuális hálózat létrehozása
Ha még nem rendelkezik virtuális hálózattal, akkor hozzon létre egyet. A képernyőképek csak példaként szolgálnak. Ne felejtse el ezeket az értékeket a saját értékeire cserélni. Az alábbi lépésekkel hozhat létre virtuális hálózatokat az Azure portállal: 

1. Egy böngészőből keresse fel az [Azure portált](http://portal.azure.com), majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson az **Új** lehetőségre. A **Piactér keresése** mezőbe írja be a „Virtuális hálózat” kifejezést. A visszaadott listában keresse meg a **Virtuális hálózat** elemet, és rákattintva nyissa meg a **Virtuális hálózat** panelt.
   
    ![Virtuális hálózat keresése panel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png "Search for virtual network blade")
3. A Virtuális hálózat panel alján, az **Üzemi modell kiválasztása** listában válassza ki a **Klasszikus** elemet, majd kattintson a **Létrehozás** elemre.
   
    ![Üzemi modell kiválasztása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png "Select deployment model")
4. A **Virtuális hálózat létrehozása** panelen konfigurálja a virtuális hálózat beállításait. Ezen a panelen adhatja majd hozzá az első címterét és egy önálló alhálózati címtartományt. A virtuális hálózat létrehozása után visszaléphet, és további alhálózatokat és címtereket vehet fel.
   
    ![Virtuális hálózat létrehozása panel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png "Create virtual network blade")
5. Ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg az **Előfizetés** résznél. Az előfizetéseket a legördülő menüben módosíthatja.
6. Kattintson az **Erőforráscsoport** elemre, és válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat az új erőforráscsoport nevének beírásával. Ha új csoportot hoz létre, a tervezett konfigurációs értékeknek megfelelően nevezze el az erőforráscsoportot. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../azure-resource-manager/resource-group-overview.md#resource-groups) (Az Azure Resource Manager áttekintése).
7. Ezután válassza ki a virtuális hálózatához tartozó **Hely** beállításokat. Ez a hely határozza meg a virtuális hálózaton üzembe helyezett erőforrások helyét.
8. Ha szeretné könnyen megtalálni virtuális hálózatát az irányítópulton, akkor válassza a **Rögzítés az irányítópulton** lehetőséget, majd kattintson a **Létrehozás** gombra.
   
    ![Rögzítés az irányítópulton](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png "Pin to dashboard")
9. A Létrehozás gombra kattintva létrejön egy csempe az irányítópulton, amely a virtuális hálózat állapotát mutatja. A virtuális hálózat létrejöttével a csempe is módosul.
   
    ![Virtuális hálózat csempéjének létrehozása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Creating virtual network tile")
10. Miután létrehozta a virtuális hálózatot, hozzáadhatja a DNS-kiszolgáló IP-címét a névfeloldás kezelésének érdekében. Nyissa meg a virtuális hálózat beállításait, kattintson a DNS-kiszolgálókra, majd adja hozzá a használni kívánt DNS-kiszolgáló IP-címét. A beállítás nem hoz létre új DNS-kiszolgálót. Ügyeljen arra, hogy olyan DNS-kiszolgálókat adjon hozzá, amelyekkel az erőforrások kommunikálni tudnak.

Miután létrejött a virtuális hálózata, megjelenik a **Létrehozva** elem a klasszikus Azure-portál Hálózatok lapján az **Állapot** listában.

### <a name="a-namegatewayapart-2-create-gateway-subnet-and-a-dynamic-routing-gateway"></a><a name="gateway"></a>2. rész: Átjáróalhálózat és dinamikus útválasztású átjáró létrehozása
Ebben a lépésben létrehoz egy átjáró-alhálózatot és egy dinamikus útválasztású átjárót. A klasszikus üzemi modell Azure Portalján az átjáró-alhálózat és a dinamikus útválasztású átjáró ugyanazokkal a konfigurációs panelekkel létrehozható.

1. Keresse meg a portálon azt a virtuális hálózatot, amelyhez létre kíván hozni egy átjárót.
2. A virtuális hálózat paneljén, az **Overview** (Áttekintés) panel VPN-kapcsolatok szakaszában kattintson a **Gateway** (Átjáró) elemre.
   
    ![Kattintson ide az átjáró létrehozásához](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png "Click here to create a gateway")
3. A **New VPN Connection** (Új VPN-kapcsolat) panelen válassza a **Point-to-site** (Pont–hely) beállítást.
   
    ![Pont–hely kapcsolati típus](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png "P2S connection type")
4. A **Client Address Space** (Ügyfélcímtér) területen adja hozzá az IP-címtartományt. Ez a tartomány, ahonnan a VPN-ügyfelek csatlakozáskor IP-címet kapnak. Törölje az automatikusan kitöltött tartományt, és adja meg a sajátját.
   
    ![Ügyfélcímtér](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png "Client address space")
5. Jelölje be a **Create gateway immediately** (Átjáró azonnali létrehozása) jelölőnégyzetet.
   
    ![Átjáró azonnali létrehozása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png "Create gateway immediately")
6. Kattintson az **Optional gateway configuration** (Átjáró opcionális konfigurálása) lehetőségre a **Gateway configuration** (Átjáró konfigurációja) panel megnyitásához.
   
    ![Kattintson az Optional gateway configuration (Átjáró opcionális konfigurálása) lehetőségre](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png "Click optional gateway configuration")
7. Kattintson a **Subnet Configure required settings** (Alhálózat konfigurálásához szükséges beállítások) elemre az **átjáró-alhálózat** hozzáadásához. Ugyan létrehozhat kicsi, akár /29-es méretű átjáró-alhálózatot is, a /28-as vagy /27-es lehetőség választásával ajánlott nagyobb alhálózatot létrehozni, amely több címet tartalmaz. Ez elegendő címet biztosít az esetleges későbbi konfigurációkhoz.
   
   > [!IMPORTANT]
   > Amikor átjáró-alhálózatokkal dolgozik, kerülje a hálózati biztonsági csoportok (NSG) társítását az átjáró-alhálózathoz. A hálózati biztonsági csoportok és az alhálózat társítása esetén előfordulhat, hogy a VPN Gateway nem fog a várt módon működni. A hálózati biztonsági csoportokkal kapcsolatos további információért tekintse meg a [Mi az a hálózati biztonsági csoport?](../virtual-network/virtual-networks-nsg.md) című cikket.
   > 
   > 
   
    ![Átjáró-alhálózat hozzáadása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png "Add GatewaySubnet")
8. Válassza ki az átjáró **méretét**. Ez a virtuális hálózati átjáró létrehozásához használt Gateway termékváltozat. A portálon az alapértelmezett termékváltozat a **Basic** (Alapszintű). További információk a Gateway termékváltozatokról: [Tudnivalók a VPN Gateway beállításairól](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   
    ![az átjáró mérete](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. Válassza ki az átjáró **útválasztási típusát**. A pont–hely konfigurációhoz a **Dynamic** (Dinamikus) útválasztási típusra van szükség. Miután befejezte a panel konfigurálását, kattintson az **OK** gombra.
   
    ![Az útválasztási típus konfigurálása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png "Configure routing type")
10. A **New VPN Connection** (Új VPN-kapcsolat) panel alján kattintson az **OK** gombra a virtuális hálózati átjáró létrehozásához. Ennek végrehajtása 45 percig is eltarthat. 

## <a name="a-namegeneratecertsasection-2-generate-certificates"></a><a name="generatecerts"></a>2. szakasz – Tanúsítványok előállítása
A tanúsítványokat az Azure használja a VPN-ügyfelek hitelesítésére a pont–hely VPN-kapcsolatokban. A nyilvános tanúsítványadatokat (nem a titkos kulcsot) egy Base-64 kódolású X.509 .cer fájlként exportálja egy vállalati tanúsítványmegoldás által létrehozott főtanúsítványból vagy egy önaláírt főtanúsítványból. Ezután importálja a nyilvános tanúsítványadatokat a főtanúsítványból az Azure-ba. Ezenfelül létre kell hoznia egy ügyféltanúsítványt az ügyfelek főtanúsítványából. Minden ügyfélnek, aki a virtuális hálózathoz szeretne csatlakozni P2S kapcsolat használatával, rendelkeznie kell egy főtanúsítványból létrehozott telepített ügyféltanúsítvánnyal.

### <a name="a-namecerapart-1-obtain-the-cer-file-for-the-root-certificate"></a><a name="cer"></a>1. rész: A .cer fájl beszerzése a főtanúsítványhoz
Nagyvállalati megoldás esetén használhatja meglévő tanúsítványláncát. Ha nem használ vállalati hitelesítésszolgáltatói (CA) megoldást, létrehozhat egy önaláírt főtanúsítványt. Az önaláírt tanúsítvány létrehozásának egyik módja a makecert.

* Ha vállalati tanúsítványrendszert használ, szerezze be a használni kívánt főtanúsítványhoz tartozó .cer fájlt. 
* Ha nem vállalati tanúsítványmegoldást használ, létre kell hoznia egy önaláírt főtanúsítványt. A Windows 10 esetén használható lépéseket lásd: [Working with self-signed root certificates for Point-to-Site configurations](vpn-gateway-certificates-point-to-site.md) (Önaláírt főtanúsítványok használata pont–hely konfigurációk esetében).

1. A .cer fájl tanúsítványból való beszerzéséhez nyissa meg a **certmgr.msc** fájlt, és keresse meg a főtanúsítványt. Kattintson a jobb gombbal az önaláírt főtanúsítványra, és kattintson a **minden tevékenység**, majd az **exportálás** elemre. Megnyílik a **Tanúsítványexportáló varázsló**.
2. A varázslóban kattintson a **Tovább** gombra, válassza a **Nem, nem akarom exportálni a titkos kulcsomat** lehetőségre, majd kattintson a **Tovább** gombra.
3. Az **Exportfájlformátum** lapon válassza a **Base-64 kódolású X.509 (.CER)** lehetőséget. Ezután kattintson a **Tovább** gombra. 
4. Az **Exportálandó fájl** lapon a **Tallózás** gombra kattintva keresse meg azt a helyet, ahová exportálni szeretné a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **Next** (Tovább) gombra.
5. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához.

### <a name="a-namegenclientcertapart-2-generate-a-client-certificate"></a><a name="genclientcert"></a>2. rész: Ügyféltanúsítvány létrehozása
Létrehozhat egy egyedi tanúsítványt minden csatlakozó ügyfél számára, vagy használhatja ugyanazt a tanúsítványt több ügyfél esetén. Az egyedi ügyféltanúsítványok előállításának előnye az, hogy szükség esetén visszavonhat egyetlen tanúsítványt. Ha azonban mindenki ugyanazt az ügyféltanúsítványt használja, és úgy találja, hogy egyetlen ügyféltől vissza kell vonnia a tanúsítványt, az összes olyan ügyfél számára elő kell állítania és telepítenie kell új tanúsítványokat, amelyek az adott tanúsítványt használják a hitelesítéshez.

* Ha vállalati tanúsítványmegoldást használ, az általános 'name@yourdomain.com', formátumban hozza létre az ügyféltanúsítványokat a „tartománynév\felhasználónév” formátuma helyett. 
* Ha önaláírt tanúsítványt használ, az ügyféltanúsítvány létrehozásával kapcsolatban tekintse meg a [Working with self-signed root certificates for Point-to-Site configurations](vpn-gateway-certificates-point-to-site.md) (Önaláírt főtanúsítványok használata pont–hely konfigurációk esetében).

### <a name="a-nameexportclientcertapart-3-export-the-client-certificate"></a><a name="exportclientcert"></a>3. rész: Az ügyféltanúsítvány exportálása
Telepítsen egy ügyféltanúsítványt minden olyan számítógépen, amelyet csatlakoztatni szeretne a virtuális hálózathoz. A hitelesítéshez ügyféltanúsítványra van szükség. Az ügyféltanúsítványt telepítheti automatikusan vagy manuálisan. A következő lépések végigvezetik az ügyféltanúsítvány manuális exportálásán és telepítésén.

1. Az ügyféltanúsítványok exportálásához a *certmgr.msc* fájlt használhatja. Kattintson a jobb gombbal az exportálni kívánt ügyféltanúsítványra, majd a **minden feladat** és az **exportálás** elemre.
2. Exportálja az ügyféltanúsítványt a titkos kulccsal. Ez egy *.pfx* fájl. Jegyezze fel vagy jegyezze meg a jelszót (kulcsot), amelyet beállított a tanúsítványhoz.

## <a name="a-nameuploadasection-3-upload-the-root-certificate-cer-file"></a><a name="upload"></a>3. szakasz – A főtanúsítvány .cer fájljának feltöltése
Miután létrehozta az átjárót, feltöltheti a megbízható főtanúsítványhoz tartozó .cer fájlt az Azure-ba. Legfeljebb 20 főtanúsítványhoz tölthet fel fájlokat. A főtanúsítvány titkos kulcsát ne töltse fel az Azure-ba. Miután feltöltötte a .cer fájlt, az Azure használhatja azt azon ügyfelek hitelesítéséhez, amelyek a virtuális hálózathoz csatlakoznak.

1. A virtuális hálózat paneljének **VPN connections** (VPN-kapcsolatok) szakaszában kattintson a **clients** (ügyfelek) ábrára a **Point-to-site VPN connection** (Pont–hely típusú VPN-kapcsolat) panel megnyitásához.
   
    ![Ügyfelek](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png "Clients")
2. A **Point-to-site VPN connection** (Pont–hely típusú VPN-kapcsolat) panelen kattintson a **Manage certificates** (Tanúsítványok kezelése) lehetőségre a **Certificates** (Tanúsítványok) panel megnyitásához.<br>
   
    ![Tanúsítványok panel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png "Certificates blade")<br><br>
3. A **Certificates** (Tanúsítványok) panelen kattintson az **Upload** (Feltöltés) lehetőségre az **Upload certificate** (Tanúsítvány feltöltése) panel megnyitásához.<br>
   
    ![Upload certificates (Tanúsítványok feltöltése) panel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png "Upload certificates blade")<br>
4. Kattintson a mappa ábrára a .cer fájl kereséséhez. Válassza ki a fájlt majd kattintson az **OK** gombra. Frissítse az oldalt, hogy a feltöltött tanúsítvány megjelenjen a **Certificates** (Tanúsítványok) panelen.
   
    ![Tanúsítvány feltöltése](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png "Upload certificate")<br>

## <a name="a-namevpnclientconfigasection-4-generate-the-vpn-client-configuration-package"></a><a name="vpnclientconfig"></a>4. szakasz – A VPN-ügyfél konfigurációs csomagjának létrehozása
A virtuális hálózathoz való csatlakozáshoz emellett konfigurálnia kell a VPN-ügyfelet is. Az ügyfélszámítógép csatlakozásához szükséges egy ügyféltanúsítvány és a VPN-ügyfél megfelelő konfigurációs csomagja.

A VPN-ügyfélcsomag tartalmazza a Windows beépített VPN-ügyfélszoftverének konfigurálásához szükséges konfigurációs adatokat. A csomag nem telepít további szoftvert. A beállítások arra a virtuális hálózatra vonatkoznak, amelyhez csatlakozni szeretne. A támogatott ügyfél operációs rendszerek listáját a VPN Gateway – gyakori kérdések [Pont–hely kapcsolatok](vpn-gateway-vpn-faq.md#point-to-site-connections) című szakaszában tekintheti meg. 

### <a name="to-generate-the-vpn-client-configuration-package"></a>A VPN-ügyfél konfigurációs csomagjának létrehozása
1. Az Azure Portalon a virtuális hálózat **Overview** (Áttekintés) paneljének **VPN connections** (VPN-kapcsolatok) részén kattintson az ügyfél ábrára a **Point-to-site VPN connection** (Pont–hely típusú VPN-kapcsolat) panel megnyitásához.
2. A **Point-to-site VPN connection** (Pont–hely típusú VPN-kapcsolat) panel felső részén kattintson arra a letöltési csomagra, amely ahhoz az ügyféloldali operációs rendszerhez tartozik, amelyen telepítve lesz:
   
   * 64 bites ügyfelek esetén válassza a **VPN Client (64-bit)** (VPN-ügyfél (64 bit)) lehetőséget.
   * 32 bites ügyfelek esetén válassza a **VPN Client (32-bit)** (VPN-ügyfél (32 bit)) lehetőséget.
     
     ![A VPN-ügyfél konfigurációs csomagjának letöltése](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png "Download VPN client configuration package")<br>
3. Megjelenik egy üzenet, amely arról tájékoztatja, hogy az Azure létrehozza a VPN-ügyfél konfigurációs csomagját a virtuális hálózathoz. Néhány perc elteltével a csomag létrehozása befejeződik, és megjelenik egy üzenet a helyi számítógépen, amely arról tájékoztatja, hogy a csomag letöltődött. Mentse a konfigurációscsomag-fájlt. Telepítse minden olyan ügyfélszámítógépen, amely a pont–hely kapcsolattal csatlakozni fog a virtuális hálózathoz.

## <a name="a-nameclientconfigurationasection-5-configure-the-client-computer"></a><a name="clientconfiguration"></a>5. szakasz – Az ügyfélszámítógép konfigurálása
### <a name="part-1-install-the-client-certificate"></a>1. rész: Az ügyféltanúsítvány telepítése
Minden ügyfélszámítógépnek rendelkeznie kell egy ügyféltanúsítvánnyal a hitelesítéshez. Az ügyféltanúsítvány telepítésekor szükség lesz az ügyféltanúsítvány exportálásakor létrehozott jelszóra.

1. Másolja a .pfx fájlt az ügyfélszámítógépre.
2. Kattintson duplán a .pfx fájlra a telepítéshez. Ne módosítsa a telepítés helyét.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>2. rész: A VPN-ügyfél konfigurációs csomagjának telepítése
Használhatja a VPN-ügyfél ugyanazon konfigurációs csomagját minden ügyfélszámítógépen, feltéve, hogy a verzió megegyezik az ügyfél architektúrájával.

1. Másolja a konfigurációs fájlt helyileg a virtuális hálózathoz csatlakoztatni kívánt számítógépre, és kattintson az .exe fájlra. 
2. Miután megtörtént a csomag telepítése, elindíthatja a VPN-kapcsolatot. A konfigurációs csomagot nem írta alá a Microsoft. Aláírhatja a csomagot a szervezete aláírószolgáltatásával, vagy saját kezűleg a [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327) használatával, de használhatja aláírás nélkül is. Ha azonban a csomag nincs aláírva, a telepítésekor megjelenik egy figyelmeztetés.
3. Nyissa meg az ügyfélszámítógépen a **Hálózati beállítások** eszközt, és kattintson a **VPN** elemre. A listán láthatja a kapcsolatot. Annak a virtuális hálózatnak a nevét mutatja, amellyel kapcsolatot létesít, és következőre hasonlít: 
   
    ![VPN-ügyfél](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png "VNet VPN client")

## <a name="a-nameconnectasection-6-connect-to-azure"></a><a name="connect"></a>6. szakasz – Csatlakozás az Azure-hoz
### <a name="connect-to-your-vnet"></a>Csatlakozás a virtuális hálózathoz
1. Csatlakozzon a virtuális hálózathoz. Ehhez navigáljon az ügyfélszámítógépen a VPN-kapcsolatokhoz, és keresse meg a létrehozott VPN-kapcsolatot. Ugyanaz a neve, mint a virtuális hálózatnak. Kattintson a **Connect** (Csatlakozás) gombra. Megjelenhet egy előugró üzenet, amely a tanúsítvány használatára utal. Ilyen esetében kattintson a **Folytatás** gombra emelt szintű jogosultságok használatához. 
2. A csatlakozás megkezdéséhez a **Kapcsolat** állapotlapon kattintson a **Csatlakozás** gombra. Ha megjelenik a **Tanúsítvány kiválasztása** képernyő, ellenőrizze, hogy az a csatlakozáshoz használni kívánt ügyféltanúsítványt mutatja-e. Ha nem, kattintson a legördülő nyílra, válassza ki a helyes tanúsítványt, majd kattintson az **OK** gombra.
   
    ![Kapcsolódás](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png "VPN client connection")
3. Ekkor létre kell jönnie a kapcsolatnak.
   
    ![Létrehozott kapcsolat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png "Connection established")

### <a name="verify-the-vpn-connection"></a>A VPN-kapcsolat ellenőrzése
1. Annak ellenőrzéséhez, hogy a VPN-kapcsolat aktív-e, nyisson meg egy rendszergazda jogú parancssort, és futtassa az *ipconfig/all* parancsot.
2. Tekintse meg az eredményeket. A kapott IP-cím a virtuális hálózat létrehozásakor megadott pont–hely kapcsolati címtartományba tartozó valamelyik cím. Az eredmények a következőhöz hasonlóak:

Példa:

    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## <a name="next-steps"></a>Következő lépések
A virtuális hálózatához hozzáadhat virtuális gépeket. Lásd: [How to create a custom virtual machine](../virtual-machines/virtual-machines-windows-classic-createportal.md) (Egyéni virtuális gép létrehozása).




<!--HONumber=Nov16_HO2-->


