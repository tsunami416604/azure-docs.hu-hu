<properties
   pageTitle="Pont–hely VPN-átjárókapcsolat konfigurálása Azure Virtual Networkhöz a klasszikus portál használatával | Microsoft Azure"
   description="Biztonságosan csatlakozhat az Azure Virtual Networkhöz pont–hely VPN Gateway-kapcsolat létrehozásával."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/06/2016"
   ms.author="cherylmc"/>


# Pont–hely kapcsolat konfigurálása virtuális hálózat számára a klasszikus portálon

> [AZURE.SELECTOR]
- [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Klasszikus – Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
- [Klasszikus – Klasszikus portál](vpn-gateway-point-to-site-create.md)

A pont–hely (P2S) konfiguráció lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat akkor hasznos, ha távoli helyről szeretne csatlakozni a virtuális hálózathoz, például otthonról vagy konferenciáról, vagy akkor, ha csak néhány ügyfelet kíván csatlakoztatni a virtuális hálózathoz.

Ez a cikk lépésről lépésre bemutatja, hogyan hozható létre virtuális hálózat pont–hely kapcsolattal a **klasszikus üzemi modellben** a **klasszikus portál** használatával.

A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet a működéshez. VPN-kapcsolat létesítéséhez manuálisan kell kezdeményezni a kapcsolatot az ügyfélszámítógépről. További információk a pont–hely kapcsolatokról: [VPN Gateway – gyakori kérdések](vpn-gateway-vpn-faq.md#point-to-site-connections) és [Tudnivalók a létesítmények közötti kapcsolatokról](vpn-gateway-plan-design.md).


### Üzemi modellek és módszerek a pont–hely (P2S) kapcsolatokhoz

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 

**Tudnivalók az Azure üzembehelyezési modellekről**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

![Pont–hely diagram](./media/vpn-gateway-point-to-site-create/p2sclassic.png "point-to-site")

## Tudnivalók Pont–hely kapcsolat létrehozásáról
 
A következő lépések végigvezetik virtuális hálózat felé irányuló pont–hely kapcsolat kialakításán. 

A pont–hely kapcsolatok konfigurálása négy szakaszból áll. Fontos, hogy a következők szakaszokat a megadott sorrendben konfigurálja. Ne hagyjon ki egyetlen lépést sem, és ne ugorjon előre.

- **1. szakasz** – Virtuális hálózat és VPN Gateway létrehozása.
- **2. szakasz** – A hitelesítéshez használt tanúsítványok létrehozása és feltöltése.
- **3. szakasz** – Az ügyféltanúsítványok exportálása és telepítése.
- **4. szakasz** – A VPN-ügyfél konfigurálása.

## <a name="vnetvpn"></a>1. szakasz: Virtuális hálózat és VPN Gateway létrehozása

### 1. rész: Virtuális hálózat létrehozása

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com/). Ezek a lépések a klasszikus portált használják, nem az Azure Portalt. Jelenleg nem hozható létre pont–hely kapcsolat az Azure Portal használatával.

2. Kattintson a képernyő bal alsó sarkában található **Új** gombra. A navigációs ablakban kattintson a **Hálózati szolgáltatások**, majd a **Virtuális hálózat** lehetőségre. Kattintson az **Egyéni létrehozás** lehetőségre a konfigurációs varázsló elindításához.

3. A **Virtuális hálózat részletei** lapon adja meg az alábbi információkat, majd a továbblépéshez kattintson a jobb alsó Tovább nyílra.
    - **Név**: Nevezze el a virtuális hálózatát. Például: „VNet1”. Erre a névre fog hivatkozni, amikor a virtuális hálózaton üzembe helyezi a virtuális gépeket.
    - **Hely**: A hely közvetlenül kapcsolódik ahhoz a fizikai helyhez (régiót), ahol az erőforrásokat (virtuális gépeket) el szeretné helyezni. Ha például azt szeretné, hogy a virtuális hálózaton üzembe helyezni kívánt virtuális gépek az USA keleti régiójában helyezkedjenek el, válassza ki azt helyként. A virtuális hálózat létrehozása után az ahhoz tartozó régiót nem lehet módosítani.

4. A **DNS-kiszolgálók és VPN-kapcsolat** lapon adja meg az alábbi információkat, majd a továbblépéshez kattintson a jobb alsó Tovább nyílra.
    - **DNS-kiszolgálók**: Adja meg a DNS-kiszolgáló nevét és IP-címét, vagy válasszon egy korábban regisztrált DNS-kiszolgálót a helyi menüből. A beállítás nem hoz létre DNS-kiszolgálót. Lehetővé teszi, hogy megadja azokat a DNS-kiszolgálókat, amelyeket névfeloldásra kíván használni ennél a virtuális hálózatnál. Ha az Azure alapértelmezett névfeloldási szolgáltatását kívánja használni, hagyja üresen ezt a mezőt.
    - **Pont–hely VPN konfigurálása**: Jelölje be a jelölőnégyzetet.

5. A **Pont–hely kapcsolat** lapon adja meg azt az IP-címtartományt, ahonnan a VPN-ügyfelei csatlakozáskor IP-címet kapnak. A megadható címtartományokra vonatkozik néhány szabály. Fontos ellenőrizni, hogy a megadott tartomány nincs-e átfedésben a helyszíni hálózata más tartományaival.

6. Adja meg az alábbi információkat, és kattintson a Tovább nyílra.
 - **Címtér**: Adja meg a kezdő IP-címet és a CIDR-t (Címek száma).
 - **Címtér felvétele**: Csak akkor vegyen fel címteret, ha ez szükséges a hálózat kialakításához.

7. A **Virtuális hálózat címterei** lapon adja meg a virtuális hálózatához használni kívánt címtartományt. Ezek a dinamikus IP-címek (DIP-k) lesznek a virtuális gépekhez és az egyéb ezen a virtuális hálózaton üzembe helyezett szerepkörpéldányokhoz rendelve.<br><br>Rendkívül fontos, hogy olyan tartományt válasszon, amely nincs átfedésben a helyszíni hálózatához használt egyéb tartományokkal. Működjön együtt hálózati rendszergazdájával, akinek esetlegesen ki kell jelölnie egy IP-címtartományt a helyszíni hálózati címtérből a virtuális hálózaton való használatra.

8. Adja meg az alábbi információkat, majd kattintson a pipára a virtuális hálózat létrehozásának megkezdéséhez.
 - **Címtér**: Adja meg a virtuális hálózatához használni kívánt belső IP-címtartományt, beleértve a kezdő IP-címet és a címek számát. Fontos, hogy olyan tartományt válasszon, amely nincs átfedésben a helyszíni hálózatához használt egyéb tartományokkal. 
 - **Alhálózat felvétele**: További alhálózatok nem szükségesek, de célszerű lehet egy külön alhálózat létrehozása a statikus DIP-kkel rendelkező virtuális gépekhez. Esetleg dönthet úgy is, hogy a virtuális gépeit egy, a többi szerepkörpéldánytól különálló alhálózaton helyezi el.
 - **Átjáróalhálózat felvétele**: A pont–hely VPN-kapcsolathoz szükséges az átjáróalhálózat. Kattintson ide az átjáróalhálózat felvételéhez. Az átjáróalhálózat kizárólag a virtuális hálózati átjáróhoz használatos.

9. Miután létrejött a virtuális hálózata, megjelenik a **Létrehozva** elem a klasszikus Azure Portal Hálózatok lapján az **Állapot** listában. Miután létrejött a virtuális hálózat, létrehozhatja a dinamikus útválasztási átjárót.

### 2. rész: Dinamikus útválasztású átjáró létrehozása

Az átjáró típusát dinamikusként kell beállítani. A statikus útválasztású átjárók ezzel a funkcióval nem működnek.

1. A klasszikus Azure Portal **Hálózatok** lapján kattintson a létrehozott virtuális hálózatra, és navigáljon az **Irányítópult** lapra.

2. Kattintson az **Irányítópult** lap alján lévő **Átjáró létrehozása** elemre. Ekkor megjelenik egy üzenet, amely rákérdez, hogy **létrehoz-e átjárót a „VNet1” virtuális hálózathoz**. Az **Igen** gombra kattintva hozzákezdhet az átjáró létrehozásához. Az átjáró létrehozása hozzávetőlegesen 15 percet vesz igénybe.

## <a name="generate"></a>2. szakasz – Tanúsítványok létrehozása és feltöltése

A tanúsítványok a VPN-ügyfelek hitelesítésére használatosak a pont–hely VPN-kapcsolatokban. Használhat vállalati tanúsítványmegoldás által létrehozott főtanúsítványt, vagy pedig egy önaláírt tanúsítványt. Az Azure-ra legfeljebb 20 főtanúsítványt tölthet fel. Miután feltöltötte a .cer fájlt, az Azure felhasználhatja az abban tárolt információkat azon ügyfelek hitelesítéséhez, amelyeken telepítve lett egy ügyféltanúsítvány. Az ügyféltanúsítványt ugyanabból a tanúsítványból kell létrehozni, mint amelyiket a .cer fájl képvisel.

Ebben a szakaszban az alábbi lépéseket fogja végrehajtani:

- A .cer fájl beszerzése a főtanúsítványhoz. Ez lehet egy önaláírt tanúsítvány, vagy használhatja a vállalati tanúsítványrendszert.
- A .cer fájl feltöltése az Azure-ba.
- Ügyféltanúsítványok előállítása.

### <a name="root"></a>1. rész: A .cer fájl beszerzése a főtanúsítványhoz

Ha vállalati tanúsítványrendszert használ, szerezze be a használni kívánt főtanúsítványhoz tartozó .cer fájlt. A [3. részben](#createclientcert) a főtanúsítványból fog létrehozni ügyféltanúsítványokat.

Ha nem vállalati tanúsítványmegoldást használ, létre kell hoznia egy önaláírt főtanúsítványt. A Windows 10 esetén használható lépéseket lásd: [Working with self-signed root certificates for Point-to-Site configurations](vpn-gateway-certificates-point-to-site.md) (Önaláírt főtanúsítványok használata pont–hely konfigurációk esetében). A cikk lépésről lépésre bemutatja, hogy hogyan használható a makecert önaláírt tanúsítvány létrehozására, majd ezt követően a .cer fájl exportálására.

### <a name="upload"></a>2. rész: A főtanúsítvány .cer fájljának feltöltése a klasszikus Azure portálra

Adjon hozzá egy megbízható tanúsítványt az Azure-hoz. Amikor egy Base64-kódolású X.509-fájlt (.cer) ad hozzá az Azure-hoz, utasíthatja az Azure-t arra, hogy bízzon meg a fájl által képviselt főtanúsítványban.

1. A klasszikus Azure portálon a virtuális hálózata **Tanúsítványok** lapján kattintson a **Főtanúsítvány feltöltése** lehetőségre.

2. A**Tanúsítvány feltöltése** lapon keresse meg a .cer főtanúsítványt, majd kattintson a pipára.

### <a name="createclientcert"></a>3. rész: Ügyféltanúsítvány létrehozása

Ezután állítsa elő az ügyféltanúsítványokat. Létrehozhat egy egyedi tanúsítványt minden csatlakozó ügyfél számára, vagy használhatja ugyanazt a tanúsítványt több ügyfél esetén. Az egyedi ügyféltanúsítványok előállításának előnye az, hogy szükség esetén visszavonhat egyetlen tanúsítványt. Ha azonban mindenki ugyanazt az ügyféltanúsítványt használja, és úgy találja, hogy egyetlen ügyféltől vissza kell vonnia a tanúsítványt, az összes olyan ügyfél számára elő kell állítania és telepítenie kell új tanúsítványokat, amelyek az adott tanúsítványt használják a hitelesítéshez.

- Ha vállalati tanúsítványmegoldást használ, az általános „név@vállalat.com” formátumban hozza létre az ügyféltanúsítványokat a NetBIOS „TARTOMÁNY\felhasználónév” formátuma helyett. 

- Ha önaláírt tanúsítványt használ, az ügyféltanúsítvány létrehozásával kapcsolatban tekintse meg a [Working with self-signed root certificates for Point-to-Site configurations](vpn-gateway-certificates-point-to-site.md) (Önaláírt főtanúsítványok használata pont–hely konfigurációk esetében).

## <a name="installclientcert"></a>3. szakasz – Az ügyféltanúsítvány exportálása és telepítése

Telepítsen egy ügyféltanúsítványt minden olyan számítógépen, amelyet csatlakoztatni szeretne a virtuális hálózathoz. A hitelesítéshez ügyféltanúsítványra van szükség. Az ügyféltanúsítványt telepítheti automatikusan vagy manuálisan. A következő lépések végigvezetik az ügyféltanúsítvány manuális exportálásán és telepítésén.

1. Az ügyféltanúsítványok exportálásához a *certmgr.msc* fájlt használhatja. Kattintson a jobb gombbal az exportálni kívánt ügyféltanúsítványra, majd a **minden feladat** és az **exportálás** elemre.
2. Exportálja az ügyféltanúsítványt a titkos kulccsal. Ez egy *.pfx* fájl. Jegyezze fel vagy jegyezze meg a jelszót (kulcsot), amelyet beállított a tanúsítványhoz.
3. Másolja a *.pfx* fájlt az ügyfélszámítógépre. Az ügyfélszámítógépen kattintson duplán a *.pfx* fájlra annak telepítéséhez. Amikor a rendszer kéri, adja meg a jelszót. Ne módosítsa a telepítés helyét.

## <a name="vpnclientconfig"></a>4. szakasz – A VPN-ügyfél konfigurálása

A virtuális hálózathoz való csatlakozáshoz emellett konfigurálnia kell a VPN-ügyfelet is. Az ügyfél csatlakozásához szükséges egy ügyféltanúsítvány és a megfelelő VPN-ügyfélkonfiguráció. A VPN-ügyfél konfigurálásához végezze el a következő lépéseket az itt leírt sorrendben.

### 1. rész: A VPN-ügyfél konfigurációs csomagjának létrehozása

1. A klasszikus Azure portálon, a virtuális hálózathoz tartozó **Irányítópult** lapon navigáljon a jobb sarokban található gyors áttekintési menüre. A támogatott ügyfél operációs rendszerek listáját a VPN Gateway – gyakori kérdések [Pont–hely kapcsolatok](vpn-gateway-vpn-faq.md#point-to-site-connections) című szakaszában tekintheti meg. A VPN-ügyfélcsomag tartalmazza a Windows beépített VPN-ügyfélszoftverének konfigurálásához szükséges konfigurációs adatokat. A csomag nem telepít további szoftvert. A beállítások arra a virtuális hálózatra vonatkoznak, amelyhez csatlakozni szeretne.<br><br>Válassza ki azt a letöltési csomagot, amely ahhoz az ügyféloldali operációs rendszerhez tartozik, amelyen telepítve lesz:
 - 32 bites ügyfelek esetén válassza a **32 bites VPN-ügyfélcsomag letöltése** lehetőséget.
 - 64 bites ügyfelek esetén válassza a **64 bites VPN-ügyfélcsomag letöltése** lehetőséget.

2. Az ügyfélcsomag létrehozása néhány percet vesz igénybe. Miután a csomag létrejött, letöltheti a fájlt. A letöltött *.exe* fájl a helyi számítógépén lesz biztonságosan tárolva.

3. Miután létrehozta és letöltötte a VPN-ügyfélcsomagot a klasszikus Azure portálról, telepítheti az ügyfélcsomagot azon az ügyfélszámítógépen, amelyet csatlakoztatni kíván a virtuális hálózathoz. Ha több ügyfélszámítógépen kívánja telepíteni a VPN-ügyfélcsomagot, ügyeljen arra, hogy mindegyiken telepítve legyen ügyféltanúsítvány.

### 2. rész: A VPN-konfigurációs csomag telepítése az ügyfélen

1. Másolja a konfigurációs fájlt helyileg a virtuális hálózathoz csatlakoztatni kívánt számítógépre, és kattintson az .exe fájlra. 

2. Miután megtörtént a csomag telepítése, elindíthatja a VPN-kapcsolatot. A konfigurációs csomagot nem írta alá a Microsoft. Aláírhatja a csomagot a szervezete aláírószolgáltatásával, vagy saját kezűleg a [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327) használatával, de használhatja aláírás nélkül is. Ha azonban a csomag nincs aláírva, a telepítésekor megjelenik egy figyelmeztetés.

3. Nyissa meg az ügyfélszámítógépen a **Hálózati beállítások** eszközt, és kattintson a **VPN** elemre. A listán láthatja a kapcsolatot. Annak a virtuális hálózatnak a nevét mutatja, amellyel kapcsolatot létesít, és következőre hasonlít: 

    ![VPN-ügyfél](./media/vpn-gateway-point-to-site-create/vpn.png "VPN client")


### 3. rész – Csatlakozás az Azure-hoz

1. Csatlakozzon a virtuális hálózathoz. Ehhez navigáljon az ügyfélszámítógépen a VPN-kapcsolatokhoz, és keresse meg a létrehozott VPN-kapcsolatot. Ugyanaz a neve, mint a virtuális hálózatnak. Kattintson a **Connect** (Csatlakozás) gombra. Megjelenhet egy előugró üzenet, amely a tanúsítvány használatára utal. Ilyen esetében kattintson a **Folytatás** gombra emelt szintű jogosultságok használatához. 

2. A csatlakozás megkezdéséhez a **Kapcsolat** állapotlapon kattintson a **Csatlakozás** gombra. Ha megjelenik a **Tanúsítvány kiválasztása** képernyő, ellenőrizze, hogy az a csatlakozáshoz használni kívánt ügyféltanúsítványt mutatja-e. Ha nem, kattintson a legördülő nyílra, válassza ki a helyes tanúsítványt, majd kattintson az **OK** gombra.

    ![2. VPN-ügyfél](./media/vpn-gateway-point-to-site-create/clientconnect.png "VPN client connection")

3. Ekkor létre kell jönnie a kapcsolatnak.

    ![3. VPN-ügyfél](./media/vpn-gateway-point-to-site-create/connected.png "VPN client connection 2")

### 4. rész: A VPN-kapcsolat ellenőrzése

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

## Következő lépések

A virtuális hálózatához hozzáadhat virtuális gépeket. Lásd: [How to create a custom virtual machine](../virtual-machines/virtual-machines-windows-classic-createportal.md) (Egyéni virtuális gép létrehozása).

További információ a virtuális hálózatokról a [Virtual Network-dokumentáció](https://azure.microsoft.com/documentation/services/virtual-network/) lapon található.



<!--HONumber=Oct16_HO3-->


