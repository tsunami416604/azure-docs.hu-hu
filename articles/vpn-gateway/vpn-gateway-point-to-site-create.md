<properties
   pageTitle="Pont–hely VPN-kapcsolat konfigurálása Azure Virtual Networkhöz a klasszikus portál használatával | Microsoft Azure"
   description="Biztonságosan csatlakozhat az Azure Virtual Networkhöz pont–hely VPN-kapcsolat létrehozásával. Útmutató azokhoz a virtuális hálózatokhoz, amelyek a szolgáltatásfelügyeleti (klasszikus) üzemi modellel készültek."
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
   ms.date="03/30/2016"
   ms.author="cherylmc"/>

# Pont–hely VPN-kapcsolat konfigurálása egy VNethez a klasszikus portálon

> [AZURE.SELECTOR]
- [PowerShell – Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Portál – Klasszikus](vpn-gateway-point-to-site-create.md)

A pont–hely konfiguráció lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal külön-külön az egyes ügyfélszámítógépekről. VPN-kapcsolat létesítéséhez manuálisan kell kezdeményezni a kapcsolatot az ügyfélszámítógépről. A pont–hely kapcsolat tökéletes megoldás, ha távoli helyről szeretne csatlakozni a virtuális hálózathoz, például otthonról vagy egy konferenciáról vagy akkor, ha csak néhány ügyfelet kíván csatlakoztatni a virtuális hálózathoz. 

A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet a működéshez. További információk a pont–hely kapcsolatokról: [VPN Gateway – gyakori kérdések](vpn-gateway-vpn-faq.md#point-to-site-connections) és [Tudnivalók a létesítmények közötti kapcsolatokról](vpn-gateway-cross-premises-options.md).

Ez a cikk az olyan, virtuális hálózatok felé irányuló pont–hely VPN Gateway-kapcsolatokra vonatkozik, amelyek a **klasszikus üzemi modell** (szolgáltatásfelügyelet) és a klasszikus portál használatával lettek létrehozva. Amikor az Azure Portalra vonatkozó lépések is rendelkezésre állnak, a megfelelő cikkre mutató hivatkozás szerepel ezen a lapon.

**Üzemi modellek és eszközök a pont–hely kapcsolatokhoz**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 


**Tudnivalók az Azure üzembehelyezési modellekről**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

![Pont–hely diagram](./media/vpn-gateway-point-to-site-create/point2site.png "point-to-site")



## Tudnivalók Pont–hely kapcsolat létrehozásáról
 
A következő lépések végigvezetik virtuális hálózat felé irányuló pont–hely kapcsolat kialakításán. A pont–hely kapcsolat konfigurálása több lépésből áll, ideális megoldás, ha VPN-eszköz beszerzése és konfigurálása nélkül szeretne biztonságos kapcsolatot létesíteni számítógépe és virtuális hálózata között. 

A pont–hely kapcsolatok konfigurálása 4 szakaszból áll. Fontos, hogy a következőket a megadott sorrendben konfigurálja, ezért ne hagyjon ki egyetlen lépést sem, és ne ugorjon előre.


- Az **1. szakasz** végigvezeti a virtuális hálózat és a VPN Gateway létrehozásán.
- A **2. szakasz** segít a hitelesítéshez használt tanúsítványok létrehozásában és feltöltésében.
- A **3. szakasz** végigvezeti az ügyféltanúsítványok exportálásának és telepítésének lépésein.
- A **4. szakasz** a VPN-ügyfél konfigurálásának lépésein vezeti végig.

## 1. szakasz: Virtuális hálózat és VPN Gateway létrehozása


### 1. rész: Virtuális hálózat létrehozása

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com/). Vegye figyelembe, hogy ezek a lépések a klasszikus portált használják, nem az Azure Portalt.

2. Kattintson a képernyő bal alsó sarkában található **Új** gombra. A navigációs ablakban kattintson a **Hálózati szolgáltatások**, majd a **Virtuális hálózat** lehetőségre. Kattintson az **Egyéni létrehozás** lehetőségre a konfigurációs varázsló elindításához.

3. A **Virtuális hálózat részletei** lapon adja meg az alábbi információkat, majd a továbblépéshez kattintson a jobb alsó Tovább nyílra.
    - **Név**: Nevezze el a virtuális hálózatát. Például: „VNetEast”. Erre a névre fog hivatkozni, amikor a virtuális hálózaton üzembe helyezi a virtuális gépeket és a PaaS-példányokat.
    - **Hely**: A hely közvetlenül kapcsolódik ahhoz a fizikai helyhez (régiót), ahol az erőforrásokat (virtuális gépeket) el szeretné helyezni. Ha például azt szeretné, hogy a virtuális hálózaton üzembe helyezni kívánt virtuális gépek az USA keleti régiójában helyezkedjenek el, válassza ki azt helyként. A virtuális hálózat létrehozása után az ahhoz tartozó régiót nem lehet módosítani.

4. A **DNS-kiszolgálók és VPN-kapcsolat** lapon adja meg az alábbi információkat, majd a továbblépéshez kattintson a jobb alsó Tovább nyílra.
    - **DNS-kiszolgálók**: Adja meg a DNS-kiszolgáló nevét és IP-címét, vagy válasszon egy korábban regisztrált DNS-kiszolgálót a helyi menüből. Ez a beállítás nem hoz létre DNS-kiszolgálót, hanem lehetővé teszi azoknak a DNS-kiszolgálóknak a megadását, amelyeket névfeloldásra kíván használni ennél a virtuális hálózatnál. Ha az Azure alapértelmezett névfeloldási szolgáltatását kívánja használni, hagyja üresen ezt a mezőt.
    - **Pont–hely VPN konfigurálása**: Jelölje be a jelölőnégyzetet.

5. A **Pont–hely kapcsolat** lapon adja meg azt az IP-címtartományt, ahonnan a VPN-ügyfelei csatlakozáskor IP-címet kapnak. A megadható címtartományokra vonatkozik néhány szabály. Fontos ellenőrizni, hogy a megadott tartomány nincs-e átfedésben a helyszíni hálózata más tartományaival.

6. Adja meg az alábbi információkat, és kattintson a Tovább nyílra.
 - **Címtér**: Adja meg a kezdő IP-címet és a CIDR-t (Címek száma).
 - **Címtér felvétele**: Csak akkor vegyen fel címteret, ha ez szükséges a hálózat kialakításához.

7. A **Virtuális hálózat címterei** lapon adja meg a virtuális hálózatához használni kívánt címtartományt. Ezek a dinamikus IP-címek (DIP-k) lesznek a virtuális gépekhez és az egyéb ezen a virtuális hálózaton üzembe helyezett szerepkörpéldányokhoz rendelve. Rendkívül fontos, hogy olyan tartományt válasszon, amely nincs átfedésben a helyszíni hálózatához használt egyéb tartományokkal. Működjön együtt hálózati rendszergazdájával, akinek esetlegesen ki kell jelölnie egy IP-címtartományt a helyszíni hálózati címtérből a virtuális hálózaton való használatra.

8. Adja meg az alábbi információkat, majd kattintson a pipára a virtuális hálózat létrehozásának megkezdéséhez.
 - **Címtér**: Adja meg a virtuális hálózatához használni kívánt belső IP-címtartományt, beleértve a kezdő IP-címet és a címek számát. Fontos, hogy olyan tartományt válasszon, amely nincs átfedésben a helyszíni hálózatához használt egyéb tartományokkal. Működjön együtt hálózati rendszergazdájával, akinek esetlegesen ki kell jelölnie egy IP-címtartományt a helyszíni hálózati címtérből a virtuális hálózaton való használatra.
 - **Alhálózat felvétele**: További alhálózatok nem szükségesek, de célszerű lehet egy külön alhálózat létrehozása a statikus DIP-kkel rendelkező virtuális gépekhez. Esetleg dönthet úgy is, hogy a virtuális gépeit egy, a többi szerepkörpéldánytól különálló alhálózaton helyezi el.
 - **Átjáróalhálózat felvétele**: A pont–hely VPN-kapcsolathoz szükséges az átjáróalhálózat. Kattintson ide az átjáróalhálózat felvételéhez. Az átjáróalhálózat kizárólag a virtuális hálózati átjáróhoz használatos.

9. Miután létrejött a virtuális hálózata, megjelenik a **Létrehozva** elem a klasszikus Azure portál Hálózatok lapján az **Állapot** listában. Miután létrejött a virtuális hálózat, létrehozhatja a dinamikus útválasztási átjárót.

### 2. rész: Dinamikus útválasztású átjáró létrehozása

Az átjáró típusát dinamikusként kell beállítani. A statikus útválasztású átjárók ezzel a funkcióval nem működnek.

1. A klasszikus Azure portál **Hálózatok** lapján kattintson az imént létrehozott virtuális hálózatra, és navigáljon az **Irányítópult** lapra.

2. Kattintson az **Irányítópult** lap alján lévő **Átjáró létrehozása** elemre. Ekkor megjelenik egy üzenet, amely rákérdez, hogy **létrehoz-e átjárót a „yournetwork” virtuális hálózathoz**. Az **Igen** gombra kattintva hozzákezdhet az átjáró létrehozásához. Az átjáró létrehozása hozzávetőlegesen 15 percet vesz igénybe.

## 2. szakasz – Tanúsítványok létrehozása és feltöltése

A tanúsítványok a VPN-ügyfelek hitelesítésére használatosak a pont–hely VPN-kapcsolatokban. Használhat vállalati tanúsítványmegoldás által létrehozott tanúsítványokat, vagy önaláírt tanúsítványokat. Az Azure-ra legfeljebb 20 főtanúsítványt tölthet fel.

Ha önaláírt tanúsítványt szeretne használni, az alábbi lépések végigvezetik a folyamaton. Ha vállalati tanúsítványmegoldást szeretne használni, az egyes szakaszok lépései eltérőek lesznek, de a következő lépéseket ebben az esetben is el kell végeznie:

### 1. rész: Főtanúsítvány azonosítása vagy létrehozása

Ha nem vállalati tanúsítványmegoldást használ, létre kell hoznia egy önaláírt főtanúsítványt. Az ebben a szakaszban szereplő lépések Windows 8 esetén használhatóak. A Windows 10 esetén használható lépéseket lásd: [Working with self-signed root certificates for Point-to-Site configurations](vpn-gateway-certificates-point-to-site.md) (Önaláírt főtanúsítványok használata pont–hely konfigurációk esetében).

Az X.509 tanúsítványok létrehozásának egyik módja a tanúsítvány-létrehozási eszköz (makecert.exe) használata. A makecert használatához töltse le és telepítse az ingyenes [Microsoft Visual Studio Expresst](https://www.visualstudio.com/products/visual-studio-express-vs.aspx).

1. Lépjen a Visual Studio Tools mappához, és indítsa el a parancssort rendszergazdaként.

2. A következő példában szereplő parancs létrehoz és telepít egy főtanúsítványt a számítógép személyes tanúsítványtárolójába, és létrehozza a hozzá tartozó *.cer* fájlt, amelyet később fel fog tölteni a klasszikus Azure portálra.

3. Lépjen abba a könyvtárba, ahová a .cer fájlt helyezni szeretné, majd futtassa a következő parancsot, ahol a *RootCertificateName* a tanúsítványhoz használni kívánt név. Ha a következő példát módosítások nélkül futtatja, létrejön egy főtanúsítvány és a hozzá tartozó *RootCertificateName.cer* fájl.

>[AZURE.NOTE] Mivel létrehozta a főtanúsítványt, amelyből létrejönnek az ügyféltanúsítványok, érdemes exportálni ezt a tanúsítványt a titkos kulcsával együtt és menteni egy olyan biztonságos helyen, ahonnan később helyreállítható.

    makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

### 2. rész: A főtanúsítvány .cer fájljának feltöltése a klasszikus Azure portálra

Az összes főtanúsítványhoz tartozó .cer fájlt fel kell töltenie az Azure-ra. Legfeljebb 20 tanúsítvány tölthető fel.

1. Amikor a korábbi eljárással létrehozta a főtanúsítványt, létrejött egy *.cer* fájl is. Most ezt fogja feltölteni a klasszikus Azure portálra. Vegye figyelembe, hogy a .cer fájl nem tartalmazza a főtanúsítvány titkos kulcsát. Legfeljebb 20 főtanúsítvány tölthető fel.

2. A klasszikus Azure portálon a virtuális hálózata **Tanúsítványok** lapján kattintson a **Főtanúsítvány feltöltése** lehetőségre.

3. A**Tanúsítvány feltöltése** lapon keresse meg a .cer főtanúsítványt, majd kattintson a pipára.

### 3. rész: Ügyféltanúsítvány létrehozása

Az alábbi lépésekkel ügyféltanúsítvány hozható létre az önaláírt főtanúsítványból. Az ebben a szakaszban szereplő lépések Windows 8 esetén használhatóak. A Windows 10 esetén használható lépéseket lásd: [Working with self-signed root certificates for Point-to-Site configurations](vpn-gateway-certificates-point-to-site.md) (Önaláírt főtanúsítványok használata pont–hely konfigurációk esetében). Ha vállalati tanúsítványmegoldást használ, kövesse a használt megoldásra vonatkozó útmutatást. 

1. Nyisson meg egy Visual Studio parancssori ablakot rendszergazdaként azon a számítógépen, amelyiken létrehozta az önaláírt főtanúsítványt.

2. Lépjen abba a könyvtárba, ahová az ügyféltanúsítvány-fájlt menteni kívánja. A *RootCertificateName* a létrehozott önaláírt főtanúsítvány. A következő példa futtatásával (a RootCertificateName név helyett a főtanúsítvány nevét használva) létrehoz egy „ClientCertificateName” nevű ügyféltanúsítványt a személyes tanúsítványtárolójában.

3. Írja be a következő parancsot:

        makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Az összes tanúsítvány a számítógépe személyes tanúsítványtárolójában található. Ezt a *certmgr* segítségével ellenőrizheti. Az eljárással annyi ügyféltanúsítványt hozhat létre, amennyire szüksége van. Ajánlott egyedi ügyféltanúsítványok létrehozása minden olyan számítógéphez, amelyet csatlakoztatni szeretne a virtuális hálózathoz.

## 3. szakasz – Az ügyféltanúsítvány exportálása és telepítése

Az ügyféltanúsítványok telepítése kötelező lépés minden olyan számítógéphez, amelyet csatlakoztatni szeretne a virtuális hálózathoz. Az alábbi lépések végigvezetik az ügyféltanúsítvány manuális telepítésén.

1. Az összes, a virtuális hálózathoz csatlakoztatni kívánt számítógépen telepíteni kell egy ügyféltanúsítványt. Emiatt valószínűleg több ügyféltanúsítványt hoz majd létre, amelyeket azután exportál. Az ügyféltanúsítványok exportálására használja a *certmgr.msc*programot. Kattintson a jobb gombbal az exportálni kívánt ügyféltanúsítványra, majd a **minden feladat** és az **exportálás** elemre.
2. Exportálja az *ügyféltanúsítványt* a titkos kulccsal. Ez egy *.pfx* fájl lesz. Jegyezze fel vagy jegyezze meg a jelszót (kulcsot), amelyet beállított a tanúsítványhoz.
3. Másolja a *.pfx* fájlt az ügyfélszámítógépre. Az ügyfélszámítógépen kattintson duplán a *.pfx* fájlra annak telepítéséhez. Amikor a rendszer kéri, adja meg a jelszót. Ne módosítsa a telepítés helyét.

## 4. szakasz – A VPN-ügyfél konfigurálása

A virtuális hálózathoz való csatlakozáshoz emellett konfigurálnia kell a VPN-ügyfelet is. Az ügyfél csatlakozásához szükséges egy ügyféltanúsítvány és a megfelelő VPN-ügyfélkonfiguráció. A VPN-ügyfél konfigurálásához végezze el a következő lépéseket az itt leírt sorrendben.

### 1. rész: A VPN-ügyfél konfigurációs csomagjának létrehozása

1. A klasszikus Azure portálon, a virtuális hálózatához tartozó **Irányítópult** lapon navigáljon a jobb sarokban található gyors áttekintési menüre, és kattintson arra a VPN-csomagra, amely a virtuális hálózathoz csatlakoztatni kívánt ügyfélhez tartozik.

2. 
A következő ügyféloldali operációs rendszerek támogatottak:
 - Windows 7 (32 bites és 64 bites)
 - Windows Server 2008 R2 (csak 64 bites)
 - Windows 8 (32 bites és 64 bites)
 - Windows 8.1 (32 bites és 64 bites)
 - Windows Server 2012 (csak 64 bites)
 - Windows Server 2012 R2 (csak 64 bites)
 - Windows 10

3. Válassza ki azt a letöltési csomagot, amely ahhoz az ügyféloldali operációs rendszerhez tartozik, amelyen telepítve lesz:
 - 32 bites ügyfelek esetén válassza a **32 bites VPN-ügyfélcsomag letöltése** lehetőséget.
 - 64 bites ügyfelek esetén válassza a **64 bites VPN-ügyfélcsomag letöltése** lehetőséget.

4. Az ügyfélcsomag létrehozása néhány percet vesz igénybe. Miután a csomag létrejött, letöltheti a fájlt. A letöltött *.exe* fájl a helyi számítógépén lesz biztonságosan tárolva.

5. Miután létrehozta és letöltötte a VPN-ügyfélcsomagot a klasszikus Azure portálról, telepítheti az ügyfélcsomagot azon az ügyfélszámítógépen, amelyet csatlakoztatni kíván a virtuális hálózathoz. Ha több ügyfélszámítógépen kívánja telepíteni a VPN-ügyfélcsomagot, ügyeljen arra, hogy mindegyiken telepítve legyen ügyféltanúsítvány. A VPN-ügyfélcsomag tartalmazza a Windows beépített VPN-ügyfélszoftverének konfigurálásához szükséges konfigurációs adatokat. A csomag nem telepít további szoftvert.

### 2. rész: A VPN-konfigurációs csomag telepítése az ügyfélen, és a kapcsolat elindítása

1. Másolja a konfigurációs fájlt helyileg a virtuális hálózathoz csatlakoztatni kívánt számítógépre, és kattintson az .exe fájlra. Miután megtörtént a csomag telepítése, elindíthatja a VPN-kapcsolatot.
Vegye figyelembe, hogy a konfigurációs csomagot nem írta alá a Microsoft. Aláírhatja a csomagot a szervezete aláírószolgáltatásával, vagy saját kezűleg a [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327) használatával, de használhatja aláírás nélkül is. Ha a csomag nincs aláírva, a telepítésekor megjelenik egy figyelmeztetés.
2. Az ügyfélszámítógépen navigáljon a VPN-kapcsolatokhoz és keresse meg az imént létrehozott VPN-kapcsolatot. Ennek a neve megegyezik a virtuális hálózata nevével. Kattintson a **Connect** (Csatlakozás) gombra.
3. Ekkor megjelenik egy előugró üzenet, amelyben létrehozható egy önaláírt tanúsítvány az átjáró végpontjához. Kattintson a **Folytatás** gombra emelt szintű jogosultságok használatához.
4. A csatlakozás megkezdéséhez a **Kapcsolat** állapotlapon kattintson a **Csatlakozás** gombra.
5. Ha megjelenik a **Tanúsítvány kiválasztása** képernyő, ellenőrizze, hogy az a csatlakozáshoz használni kívánt ügyféltanúsítványt mutatja-e. Ha nem, kattintson a legördülő nyílra, válassza ki a helyes tanúsítványt, majd kattintson az **OK** gombra.
6. Ekkor csatlakozik a virtuális hálózathoz, és hozzáférhet a hálózaton elérhető összes szolgáltatáshoz és virtuális géphez.

### 3. rész: A VPN-kapcsolat ellenőrzése

1. Annak ellenőrzéséhez, hogy a VPN-kapcsolat aktív-e, nyisson meg egy rendszergazda jogú parancssort, és futtassa az *ipconfig/all* parancsot.
2. Tekintse meg az eredményeket. A kapott IP-cím a virtuális hálózat létrehozásakor megadott pont–hely kapcsolati címtartományba tartozó valamelyik cím. Az eredmények a következőhöz hasonlóak:

Példa:



    PPP adapter VNetEast:
        Connection-specific DNS Suffix .:
        Description.....................: VNetEast
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



<!--HONumber=jun16_HO2-->


