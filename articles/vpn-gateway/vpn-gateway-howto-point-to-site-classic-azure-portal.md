---
title: "Számítógép csatlakoztatása Azure-beli virtuális hálózathoz pont–hely kapcsolat használatával: klasszikus Azure Portal | Microsoft Docs"
description: "Az Azure Portal használatával létrehozott pont–hely VPN Gateway-kapcsolattal biztonságosan csatlakozhat a klasszikus Azure Virtual Networkhöz."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: c64e352a0a814b869703e1b00a6a0698f19b0ff9
ms.contentlocale: hu-hu
ms.lasthandoff: 05/10/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal-classic"></a>Pont–hely kapcsolat konfigurálása virtuális hálózat számára az Azure Portalon (klasszikus)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Ez a cikk bemutatja, hogyan hozhat létre pont–hely kapcsolattal rendelkező virtuális hálózatot a klasszikus üzemi modellben az Azure Portallal. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Klasszikus – Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

A pont–hely (P2S) konfiguráció lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat egy SSTP (Secure Socket Tunneling Protocol) használatával működő VPN-kapcsolat. A pont–hely kapcsolatok akkor hasznosak, ha egy távoli helyről szeretne csatlakozni a virtuális hálózathoz, például otthonról vagy egy konferenciáról, vagy akkor, ha csak néhány ügyfelet szeretne csatlakoztatni egy virtuális hálózathoz. A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet a működéshez. A VPN-kapcsolatot az ügyfélszámítógépről hozhatja létre. 


![Pont–hely diagram](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

A pont–hely kapcsolatokhoz a következőkre van szükség:

* Egy dinamikus VPN-átjáró.
* A nyilvános kulcs (.cer fájl) egy főtanúsítványhoz, az Azure-ba feltöltve. A rendszer ezt megbízható tanúsítványnak tekinti, és ezt használja hitelesítéshez.
* A főtanúsítványból létrejön egy ügyféltanúsítvány, majd települ az egyes csatlakozó ügyfélszámítógépekre. A rendszer ezt a tanúsítványt használja ügyfélhitelesítéshez.
* Minden csatlakozó ügyfélszámítógépen létre kell hozni és telepíteni kell egy VPN-ügyfélkonfigurációs csomagot. Az ügyfélkonfigurációs csomag konfigurálja a már az operációs rendszeren lévő natív VPN-ügyfelet a virtuális hálózathoz való csatlakozáshoz szükséges adatokkal.

A Pont–hely kapcsolatokról további információt a cikk végén, a [Pont–hely kapcsolatok – gyakori kérdések](#faq) című részben talál.

### <a name="example-settings"></a>Példabeállítások

Az alábbi értékek használatával létrehozhat egy tesztkörnyezetet, vagy segítségükkel értelmezheti a cikkben szereplő példákat:

* **Név: VNet1**
* **Címtér: 192.168.0.0/16**<br>Ebben a példában csak egy címteret használunk. Azonban a virtuális hálózatához több címteret is használhat.
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

## <a name="vnetvpn"></a>1. szakasz: Virtuális hálózat és VPN Gateway létrehozása

Mielőtt elkezdi végrehajtani a lépéseket, győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

### <a name="createvnet"></a>1. rész: Virtuális hálózat létrehozása

Ha még nem rendelkezik virtuális hálózattal, akkor hozzon létre egyet. A képernyőképek csak példaként szolgálnak. Ne felejtse el ezeket az értékeket a saját értékeire cserélni. Az alábbi lépésekkel hozhat létre virtuális hálózatokat az Azure portállal:

1. Egy böngészőből keresse fel az [Azure portált](http://portal.azure.com), majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson az **Új** lehetőségre. A **Piactér keresése** mezőbe írja be a „Virtuális hálózat” kifejezést. A visszaadott listában keresse meg a **Virtuális hálózat** elemet, és rákattintva nyissa meg a **Virtuális hálózat** panelt.

  ![Virtuális hálózat keresése panel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png)
3. A Virtuális hálózat panel alján, az **Üzemi modell kiválasztása** listában válassza ki a **Klasszikus** elemet, majd kattintson a **Létrehozás** elemre.

  ![Üzemi modell kiválasztása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png)
4. A **Virtuális hálózat létrehozása** panelen konfigurálja a virtuális hálózat beállításait. Ezen a panelen adhatja hozzá az első címterét és egy önálló alhálózati címtartományt. A virtuális hálózat létrehozása után visszaléphet, és további alhálózatokat és címtereket vehet fel.

  ![Virtuális hálózat létrehozása panel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. Ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg az **Előfizetés** résznél. Az előfizetéseket a legördülő menüben módosíthatja.
6. Kattintson az **Erőforráscsoport** elemre, és válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat az új erőforráscsoport nevének beírásával. Ha új erőforráscsoportot hoz létre, a tervezett konfigurációs értékeknek megfelelően nevezze el azt. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../azure-resource-manager/resource-group-overview.md#resource-groups) (Az Azure Resource Manager áttekintése).
7. Ezután válassza ki a virtuális hálózatához tartozó **Hely** beállításokat. Ez a hely határozza meg a VNeten üzembe helyezett erőforrások helyét.
8. Ha szeretné könnyen megtalálni a VNetet az irányítópulton, akkor válassza a **Pin to dashboard** (Rögzítés az irányítópulton) lehetőséget, majd kattintson a **Create** (Létrehozás) gombra.

  ![Rögzítés az irányítópulton](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. A Létrehozás gombra kattintva létrejön egy csempe az irányítópulton, amely a virtuális hálózat állapotát mutatja. A virtuális hálózat létrejöttével a csempe is módosul.

  ![Virtuális hálózat csempéjének létrehozása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. Miután létrejött a virtuális hálózata, a **Létrehozva** érték jelenik meg az **Állapot** tulajdonságban a klasszikus Azure-portál Networks (Hálózatok) lapján.
11. Adjon meg egy DNS-kiszolgálót (nem kötelező). Miután létrehozta a virtuális hálózatot, a névfeloldás érdekében hozzáadhatja egy DNS-kiszolgáló IP-címét. A megadott DNS-kiszolgálónak olyan kiszolgálónak kell lennie, amely fel tudja oldani a virtuális hálózatban található erőforrások nevét.<br>A DNS-kiszolgáló hozzáadásához nyissa meg a virtuális hálózat beállításait, kattintson a DNS-kiszolgálókra, majd adja hozzá a használni kívánt DNS-kiszolgáló IP-címét. Az ügyfél-konfigurációs csomag, amelyet egy későbbi lépésben fog létrehozni, tartalmazza az ebben a beállításban megadott DNS-kiszolgálók IP-címét. Ha a későbbiekben frissíteni szeretné a DNS-kiszolgálók listáját, létrehozhat és telepíthet egy új VPN-ügyfélkonfigurációs csomagot, amely a frissített listát tartalmazza.

### <a name="gateway"></a>2. rész: Átjáróalhálózat és dinamikus útválasztású átjáró létrehozása

Ebben a lépésben létrehoz egy átjáró-alhálózatot és egy dinamikus útválasztású átjárót. A klasszikus üzemi modell Azure Portalján az átjáró-alhálózat és a dinamikus útválasztású átjáró ugyanazokkal a konfigurációs panelekkel létrehozható.

1. Keresse meg a portálon azt a virtuális hálózatot, amelyhez létre kíván hozni egy átjárót.
2. A virtuális hálózat paneljén, az **Overview** (Áttekintés) panel VPN-kapcsolatok szakaszában kattintson a **Gateway** (Átjáró) elemre.

  ![Kattintson egy új átjáró létrehozásához](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. A **New VPN Connection** (Új VPN-kapcsolat) panelen válassza a **Point-to-site** (Pont–hely) beállítást.

  ![Pont–hely kapcsolat típusa](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. A **Client Address Space** (Ügyfélcímtér) területen adja hozzá az IP-címtartományt. Ez az a tartomány, amelyből a VPN-ügyfelek IP-címet kapnak csatlakozáskor. Olyan magánhálózati IP-címtartományt használjon, amely nincs átfedésben azzal a helyszíni hellyel, amelyről csatlakozik, vagy azzal a virtuális hálózattal, amelyhez csatlakozik. Az automatikusan kitöltött tartományt törölheti, majd hozzáadhatja a használni kívánt magánhálózati IP-címtartományt.

  ![Ügyfélcímtér](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Jelölje be a **Create gateway immediately** (Átjáró azonnali létrehozása) jelölőnégyzetet.

  ![Átjáró azonnali létrehozása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png)
6. Kattintson az **Optional gateway configuration** (Átjáró opcionális konfigurálása) lehetőségre a **Gateway configuration** (Átjáró konfigurációja) panel megnyitásához.

  ![Kattintson az Optional gateway configuration (Átjáró opcionális konfigurálása) lehetőségre](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
7. Kattintson a **Subnet Configure required settings** (Alhálózat konfigurálásához szükséges beállítások) elemre az **átjáró-alhálózat** hozzáadásához. Ugyan létrehozhat kicsi, akár /29-es méretű átjáró-alhálózatot is, a /28-as vagy /27-es lehetőség választásával ajánlott nagyobb alhálózatot létrehozni, amely több címet tartalmaz. Ez elegendő címet biztosít az esetleges későbbi konfigurációkhoz. Amikor átjáró-alhálózatokkal dolgozik, kerülje a hálózati biztonsági csoportok (NSG) társítását az átjáró-alhálózathoz. A hálózati biztonsági csoportok és az alhálózat társítása esetén előfordulhat, hogy a VPN Gateway nem fog a várt módon működni.

  ![Átjáró-alhálózat hozzáadása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
8. Válassza ki az átjáró **méretét**. A méret a virtuális hálózati átjáró termékváltozata. A portálon az alapértelmezett termékváltozat a **Basic** (Alapszintű). További információk a Gateway termékváltozatokról: [Tudnivalók a VPN Gateway beállításairól](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

  ![Az átjáró mérete](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. Válassza ki az átjáró **útválasztási típusát**. A pont–hely konfigurációhoz a **Dynamic** (Dinamikus) útválasztási típusra van szükség. Miután befejezte a panel konfigurálását, kattintson az **OK** gombra.

  ![Az útválasztási típus konfigurálása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
10. A **New VPN Connection** (Új VPN-kapcsolat) panel alján kattintson az **OK** gombra a virtuális hálózati átjáró létrehozásához. Egy VPN-átjáró létrehozása akár 45 percet is igénybe vehet a kiválasztott átjáró termékváltozatától függően.

## <a name="generatecerts"></a>2. szakasz – Tanúsítványok létrehozása

A tanúsítványokat az Azure használja a VPN-ügyfelek hitelesítésére a pont–hely VPN-kapcsolatokban. A főtanúsítvány nyilvánoskulcs-adatait feltölti az Azure-ba. A nyilvános kulcs ezután „megbízhatónak” tekinthető. Az ügyféltanúsítványokat a megbízható főtanúsítványból kell létrehozni, majd telepíteni kell az összes számítógépen a Certificates-Current User/Personal tanúsítványtárolóban. A tanúsítványt a rendszer az ügyfél hitelesítésére használja, amikor az a virtuális hálózathoz próbál csatlakozni. További információ a tanúsítványok létrehozásáról és telepítéséről: [Tanúsítványok pont–hely kapcsolatokhoz](vpn-gateway-certificates-point-to-site.md).

### <a name="cer"></a>1. rész: A nyilvános kulcs (.cer) beszerzése a főtanúsítványhoz

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="genclientcert"></a>2. rész: Ügyféltanúsítvány létrehozása

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload"></a>3. szakasz – A főtanúsítvány .cer fájljának feltöltése

Az átjáró létrehozása után töltse fel a megbízható főtanúsítványhoz tartozó .cer fájlt (amely a nyilvános kulcsot tartalmazza) az Azure-ba. A főtanúsítvány titkos kulcsát ne töltse fel az Azure-ba. Miután feltöltötte a .cer fájlt, az Azure felhasználhatja azt azon ügyfelek hitelesítéséhez, amelyeken telepítve lett egy, a megbízható főtanúsítványból létrehozott ügyféltanúsítvány. Szükség szerint később további megbízhatófőtanúsítvány-fájlokat is feltölthet (legfeljebb 20-at).  

1. A virtuális hálózat paneljének **VPN connections** (VPN-kapcsolatok) szakaszában kattintson a **clients** (ügyfelek) ábrára a **Point-to-site VPN connection** (Pont–hely típusú VPN-kapcsolat) panel megnyitásához.

  ![Ügyfelek](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. A **Point-to-site VPN connection** (Pont–hely típusú VPN-kapcsolat) panelen kattintson a **Manage certificates** (Tanúsítványok kezelése) lehetőségre a **Certificates** (Tanúsítványok) panel megnyitásához.<br>

  ![Tanúsítványok panel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. A **Certificates** (Tanúsítványok) panelen kattintson az **Upload** (Feltöltés) lehetőségre az **Upload certificate** (Tanúsítvány feltöltése) panel megnyitásához.<br>

    ![Upload certificates (Tanúsítványok feltöltése) panel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. Kattintson a mappa ábrára a .cer fájl kereséséhez. Válassza ki a fájlt majd kattintson az **OK** gombra. Frissítse az oldalt, hogy a feltöltött tanúsítvány megjelenjen a **Certificates** (Tanúsítványok) panelen.

  ![Tanúsítvány feltöltése](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>4. szakasz – Az ügyfél konfigurálása

Ha pont–hely típusú VPN-kapcsolattal szeretne kapcsolódni egy virtuális hálózathoz, minden ügyfélen telepíteni kell egy csomagot a natív Windows VPN-ügyfél konfigurálásához. A konfigurációs csomag konfigurálja a natív Windows VPN-ügyfelet a virtuális hálózathoz való csatlakozáshoz szükséges beállításokkal, továbbá, ha megadott egy DNS-kiszolgálót a virtuális hálózat számára, akkor tartalmazza a DNS-kiszolgáló IP-címét, amelyet az ügyfél a névfeloldáshoz fog használni. Ha később, az ügyfél-konfigurációs csomag létrehozása után módosítja a megadott DNS-kiszolgálót, ne felejtsen el új ügyfél-konfigurációs csomagot létrehozni az ügyfélszámítógépeken való telepítéshez.

Használhatja a VPN-ügyfél azonos konfigurációs csomagját minden ügyfélszámítógépen, feltéve, hogy a verzió megfelel az ügyfél architektúrájának. A támogatott ügyfél operációs rendszerek listáját a cikk végén, a [Pont–hely kapcsolatok – gyakori kérdések](#faq) című szakaszban tekintheti meg.

### <a name="part-1-generate-and-install-the-vpn-client-configuration-package"></a>1. rész – A VPN-ügyfél konfigurációs csomagjának létrehozása és telepítése

1. Az Azure Portalon a virtuális hálózat **Overview** (Áttekintés) paneljének **VPN connections** (VPN-kapcsolatok) részén kattintson az ügyfél ábrára a **Point-to-site VPN connection** (Pont–hely típusú VPN-kapcsolat) panel megnyitásához.
2. A **Point-to-site VPN connection** (Pont–hely típusú VPN-kapcsolat) panel felső részén kattintson arra a letöltési csomagra, amely ahhoz az ügyféloldali operációs rendszerhez tartozik, amelyen telepítve lesz:

  * 64 bites ügyfelek esetén válassza a **VPN Client (64-bit)** (VPN-ügyfél (64 bit)) lehetőséget.
  * 32 bites ügyfelek esetén válassza a **VPN Client (32-bit)** (VPN-ügyfél (32 bit)) lehetőséget.

  ![A VPN-ügyfél konfigurációs csomagjának letöltése](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. A csomag létrehozása után töltse le és telepítse azt az ügyfélszámítógépen. Ha megjelenik a SmartScreen egy előugró ablaka, kattintson a **További információ**, majd a **Futtatás mindenképpen** elemre. A csomagot mentheti is, így más ügyfélszámítógépekre is telepítheti.

### <a name="part-2-install-the-client-certificate"></a>2. rész – Az ügyféltanúsítvány telepítése

Ha a tanúsítvány létrehozásához használttól eltérő ügyfélszámítógépről szeretne pont–hely kapcsolatot létesíteni, akkor telepítenie kell egy ügyféltanúsítványt. Az ügyféltanúsítvány telepítésekor szükség lesz az ügyféltanúsítvány exportálásakor létrehozott jelszóra. Ehhez általában annyit kell tennie, hogy duplán rákattint a tanúsítványra, és telepíti azt. További információkért lásd az [exportált ügyféltanúsítványok telepítését](vpn-gateway-certificates-point-to-site.md#install) ismertető cikket.

## <a name="connect"></a>5. szakasz – Csatlakozás az Azure-hoz

### <a name="connect-to-your-vnet"></a>Csatlakozás a virtuális hálózathoz

1. Csatlakozzon a virtuális hálózathoz. Ehhez navigáljon az ügyfélszámítógépen a VPN-kapcsolatokhoz, és keresse meg a létrehozott VPN-kapcsolatot. Ugyanaz a neve, mint a virtuális hálózatnak. Kattintson a **Connect** (Csatlakozás) gombra. Megjelenhet egy előugró üzenet, amely a tanúsítvány használatára utal. Ilyen esetében kattintson a **Folytatás** gombra emelt szintű jogosultságok használatához.
2. A csatlakozás megkezdéséhez a **Kapcsolat** állapotlapon kattintson a **Csatlakozás** gombra. Ha megjelenik a **Tanúsítvány kiválasztása** képernyő, ellenőrizze, hogy az a csatlakozáshoz használni kívánt ügyféltanúsítványt mutatja-e. Ha nem, kattintson a legördülő nyílra, válassza ki a helyes tanúsítványt, majd kattintson az **OK** gombra.

  ![VPN-ügyfélkapcsolat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. A kapcsolat létrejött.

  ![Létrehozott kapcsolat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

Ha kapcsolódási problémákat észlel, ellenőrizze az alábbiakat:

- Nyissa meg a **Felhasználói tanúsítványok kezelése** ablakot, és navigáljon a **Megbízható legfelső szintű hitelesítésszolgáltatók\Tanúsítványok** részhez. Ellenőrizze, hogy a főtanúsítvány szerepel-e a listában. A főtanúsítvány a hitelesítés működéséhez szükséges. Ha .pfx formátumú ügyféltanúsítványt exportál az alapértelmezett „Minden tanúsítvány belefoglalása a tanúsítványláncba” érték használatával, a főtanúsítvány információit is exportálja a program. Amikor telepíti az ügyféltanúsítványt, az alkalmazás a főtanúsítványt is telepíti az ügyfélszámítógépre. 

- Ha vállalati hitelesítésszolgáltatói megoldás használatával kiadott tanúsítványt használ, és problémák merülnek fel a hitelesítés során, ellenőrizze a hitelesítési sorrendet az ügyféltanúsítványon. A hitelesítési lista sorrendjének ellenőrzéséhez kattintson duplán az ügyféltanúsítványra, és lépjen a **Részletek > Kibővített kulcshasználat** részre. Ellenőrizze, hogy a listán az „ügyfél-hitelesítés” jelenik-e meg első helyen. Ha nem, ki kell adnia egy ügyféltanúsítványt, amely az ügyfél-hitelesítést a lista első helyén tartalmazó felhasználói sablonon alapul. 

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

 
 Ha nem sikerült P2S-en keresztül csatlakozni egy virtuális géphez, az ipconfig használatával ellenőrizze annak a számítógépnek az Ethernet-adapteréhez hozzárendelt IPv4-címet, amelyről a kapcsolatot létesíti. Ha az IP-cím azon virtuális hálózat tartományában található, amelyhez csatlakozni kíván, vagy a VPN-ügyfél címkészletének címtartományában, akkor átfedő címtérről beszélünk. Ilyen átfedés esetén a hálózati forgalom nem éri el az Azure-t, és a helyi hálózaton marad. Ha a hálózati címtartományokban nincs átfedés, de mégsem tud kapcsolódni a virtuális géphez, olvassa el a [Troubleshoot Remote Desktop connections to a VM](../virtual-machines/windows/troubleshoot-rdp-connection.md) (A virtuális gépekhez való távoli asztali kapcsolatok hibáinak elhárítása) című témakört.

## <a name="connectVM"></a>Csatlakozás virtuális géphez

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add"></a>Megbízható főtanúsítványok hozzáadása vagy eltávolítása

A megbízható főtanúsítványokat felveheti vagy el is távolíthatja az Azure-ban. Főtanúsítvány eltávolításakor az abból a gyökérből létrehozott tanúsítvánnyal rendelkező ügyfelek nem fognak tudni hitelesítést végezni, így csatlakozni sem. Ha azt szeretné, hogy az ügyfelek hitelesítést végezhessenek és csatlakozni tudjanak, telepítenie kell egy olyan új ügyféltanúsítványt, amelyet az Azure által megbízhatónak tartott (feltöltött) főtanúsítványból hoztak létre.

### <a name="to-add-a-trusted-root-certificate"></a>Megbízható főtanúsítvány hozzáadása

Az Azure-ra legfeljebb 20 megbízható főtanúsítványt tölthet fel .cer fájl formájában. Útmutatásért lásd [a főtanúsítvány .cer fájljának feltöltését ismertető 3. szakaszt](#upload).

### <a name="to-remove-a-trusted-root-certificate"></a>Megbízható főtanúsítvány eltávolítása

1. A virtuális hálózat paneljének **VPN connections** (VPN-kapcsolatok) szakaszában kattintson a **clients** (ügyfelek) ábrára a **Point-to-site VPN connection** (Pont–hely típusú VPN-kapcsolat) panel megnyitásához.

  ![Ügyfelek](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. A **Point-to-site VPN connection** (Pont–hely típusú VPN-kapcsolat) panelen kattintson a **Manage certificates** (Tanúsítványok kezelése) lehetőségre a **Certificates** (Tanúsítványok) panel megnyitásához.<br>

  ![Tanúsítványok panel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. A **Tanúsítványok** panelen kattintson az eltávolítani kívánt tanúsítvány melletti, három pontot ábrázoló gombra, majd a **Törlés** parancsra.

  ![Főtanúsítvány törlése](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>

## <a name="revokeclient"></a>Ügyféltanúsítvány visszavonása

Az ügyféltanúsítványokat vissza lehet vonni. A visszavont tanúsítványok listájával az egyes ügyféltanúsítványok alapján, szelektíven tagadhatja meg a pont–hely kapcsolódás lehetőségét. Ez a folyamat eltér a megbízható főtanúsítvány eltávolításától. Ha töröl egy .cer formátumú megbízható főtanúsítványt az Azure-ból, azzal megvonja a hozzáférést minden olyan ügyféltanúsítványtól, amelyet a visszavont főtanúsítvánnyal hoztak létre/írtak alá. A főtanúsítvány helyett az ügyféltanúsítvány visszavonásával a főtanúsítványból létrehozott többi tanúsítvány továbbra is használható a pont–hely kapcsolat hitelesítésére.

A szokásos gyakorlat az, hogy a főtanúsítvánnyal kezelik a hozzáférést a munkacsoport vagy a szervezet szintjén, az egyes felhasználókra vonatkozó részletesebb szabályozást pedig visszavont ügyféltanúsítványokkal oldják meg.

### <a name="to-revoke-a-client-certificate"></a>Ügyféltanúsítvány visszavonása

Az ügyféltanúsítványok visszavonásához vegye fel az ujjlenyomatot a visszavont tanúsítványok listájára.

1. Kérje le az ügyféltanúsítvány ujjlenyomatát. További információkat [a tanúsítványok ujjlenyomatának lekérését ismertető útmutatóban](https://msdn.microsoft.com/library/ms734695.aspx) találhat.
2. Másolja át az adatokat egy szövegszerkesztőbe, és távolítsa el az összes szóközt, hogy egy folyamatos karakterláncot kapjon.
3. Lépjen a **„klasszikus virtuális hálózat neve” > Pont–hely kapcsolat > Tanúsítványok** panelre, majd kattintson a **Visszavont tanúsítványok listája** elemre a Visszavont tanúsítványok listája panel megnyitásához. 
4. A **Visszavont tanúsítványok listája** panelen kattintson a **+Tanúsítvány hozzáadása** parancsra a **Tanúsítvány felvétele a visszavont tanúsítványok listájára** panel megnyitásához.
5. A **Tanúsítvány felvétele a visszavont tanúsítványok listájára** panelen illessze be a tanúsítvány ujjlenyomatát összefüggő, szóközök nélküli szövegláncként. Kattintson az **OK** gombra a panel alján.
6. A frissítés befejezését követően a tanúsítvány már nem használható csatlakozáshoz. Azok az ügyfelek, akik ezzel a tanúsítvánnyal próbálnak csatlakozni, egy üzenetet kapnak majd arról, hogy a tanúsítvány már nem érvényes.

## <a name="faq"></a>Pont–hely kapcsolatok – gyakori kérdések

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Következő lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). A hálózatok és virtuális gépek ismertetését lásd az [Azure- és Linux-alapú virtuálisgép-hálózatok áttekintésében](../virtual-machines/linux/azure-vm-network-overview.md).

