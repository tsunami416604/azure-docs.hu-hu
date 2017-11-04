---
title: "Kapcsolat létrehozása a Vnetek között: klasszikus: Azure-portál |} Microsoft Docs"
description: "Hogyan csatlakozzon az Azure virtuális hálózatok együtt a PowerShell és a klasszikus Azure portál használatával."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/02/2017
ms.author: cherylmc
ms.openlocfilehash: 77097d59077cd8e199acdb5dc0d8427369565eea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Konfigurálja a VNet – VNet-kapcsolatot (klasszikus)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Ebből a cikkből megtudhatja, hogyan hozható létre VPN Gateway-kapcsolat virtuális hálózatok között. A virtuális hálózatok lehetnek azonos vagy eltérő régiókban, illetve azonos vagy eltérő előfizetésekben. A cikkben leírt lépéseket a klasszikus üzembe helyezési modellel és az Azure-portálon érvényesek. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Különböző üzemi modellek összekapcsolása – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Különböző üzemi modellek összekapcsolása – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![VNet hálózatok kapcsolati diagramja](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)

## <a name="about-vnet-to-vnet-connections"></a>Tudnivalók a virtuális hálózatok közötti kapcsolatokról

Csatlakozás virtuális hálózat egy másik virtuális hálózathoz (VNet – VNet) a klasszikus üzembe helyezési modellel, VPN-átjáró használata hasonlít egy virtuális hálózathoz csatlakozik egy helyszíni hely. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához.

A csatlakozás Vnetekhez különböző előfizetések és a különböző régiókban is szerepelhet. Virtuális hálózat kombinálhatja a virtuális hálózat kommunikáció a többhelyes konfigurációk. Így létrehozhat olyan hálózati topológiákat, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal kombinálják.

![VNet VNet kapcsolatokhoz](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why"></a>Miért érdemes összekapcsolni a virtuális hálózatokat?

A virtuális hálózatokat a következő okokból érdemes összekapcsolni:

* **Georedundancia és földrajzi jelenlét több régióban**

  * Beállíthatja a saját georeplikációját vagy szinkronizálását biztonságos kapcsolaton át, internetes végpontok használata nélkül.
  * Azure Load Balancer és a Microsoft vagy harmadik fél fürtözési technológiával állíthat be magas rendelkezésre állású munkaterhelés georedundancia több Azure-régiók között. Például beállíthat folyamatosan működő SQL-t több Azure-régióban található rendelkezésre állási csoportokkal.
* **Az erős elkülönítési határt alkotnak regionális Többrétegű alkalmazások**

  * A régión belül állíthat be több rétegből álló alkalmazások az erős elkülönítést és a réteg közötti kommunikációt a csatlakoztatott több Vnetek.
* **Kereszt-előfizetés, a szervezet közötti kommunikáció az Azure-ban**

  * Ha több Azure-előfizetéssel rendelkezik, összekapcsolhatja a munkaterhelések különböző előfizetések a együtt biztonságosan virtuális hálózatok között.
  * Vállalatok és szolgáltatók engedélyezheti a biztonságos VPN-technológia az Azure közötti-szervezet kommunikációt.

A virtuális hálózatok közötti kapcsolatokról további információt a cikk végén, [A virtuális hálózatok közötti kapcsolatokra vonatkozó szempontok](#faq) című részben talál.

### <a name="before-you-begin"></a>Előkészületek

Ebben a gyakorlatban megkezdése előtt töltse le és telepítse a legújabb verziót az Azure Service Management (SM) PowerShell-parancsmagokat. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál. A legtöbb lépések használjuk a portálon, de a PowerShell segítségével a Vnetek közötti kapcsolatok létrehozása. Nem hozható létre a kapcsolatokat, az Azure portál használatával.

## <a name="plan"></a>1. lépés – Az IP-címtartományok megtervezése

Fontos, döntse el, hogy a tartományt, amelyet a virtuális hálózatok konfigurálásához fog használni. Ehhez a konfigurációhoz meg kell győződnie arról, hogy a virtuális hálózat tartományok egyike átfedésben vannak egymással, vagy egy helyi hálózaton, amelyhez csatlakoznak.

A következő táblázat a Vnetek definiálása példáját mutatja be. Használja a tartományokat csak iránymutatásként. Jegyezze fel a virtuális hálózatok a tartományokat. Ezt az információt a későbbi lépésekben szükség van.

**Példa**

| Virtual Network | Címterület | Régió | Csatlakozik a helyi hálózati telephely |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA keleti régiója |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA nyugati régiója |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="vnetvalues"></a>2. lépés – a virtuális hálózatok létrehozása

A két virtuális hálózat létrehozása a [Azure-portálon](https://portal.azure.com). A klasszikus virtuális hálózatok létrehozásához lépéseiért lásd: [klasszikus virtuális hálózat létrehozása](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

Amikor a portál segítségével hozzon létre egy klasszikus virtuális hálózatot, akkor kell megkeresnie és kijelölnie a virtuális hálózat panel az alábbi lépéseket követve, ellenkező esetben a klasszikus virtuális hálózat létrehozása a beállítás nem jelenik meg:

1. Kattintson a "+" a "New" panel megnyitásához.
2. A "Keresése a piactéren" mezőben írja be a "Virtuális hálózat". Ha ehelyett hálózatkezelés virtuális hálózat ->, létrehozhat egy klasszikus virtuális hálózat nem fog.
3. Keresse meg a "Virtuális hálózat" a visszaadott listából, és kattintson arra a virtuális hálózat panel megnyitásához. 
4. A virtuális hálózati panelen válassza ki a "Klasszikus" klasszikus VNet létrehozása. 

Ha ez a cikk egy gyakorlatot használ, használhatja a következő példában szereplő értékeket:

**TestVNet1 értékei**

Name: TestVNet1<br>
Címtér: 10.11.0.0/16, 10.12.0.0/16 (nem kötelező)<br>
Alhálózati név: alapértelmezett<br>
Alhálózati címtartományt: 10.11.0.1/24<br>
Erőforráscsoport: ClassicRG<br>
Hely: East US<br>
A GatewaySubnet: 10.11.1.0/27

**TestVNet4 értékei**

Name: TestVNet4<br>
Címtér: 10.41.0.0/16, 10.42.0.0/16 (nem kötelező)<br>
Alhálózati név: alapértelmezett<br>
Alhálózati címtartományt: 10.41.0.1/24<br>
Erőforráscsoport: ClassicRG<br>
Hely: West US<br>
A GatewaySubnet: 10.41.1.0/27

**A Vnetek létrehozásakor vegye figyelembe a következő beállításokat:**

* **Virtuális hálózati címtereket** – a virtuális hálózati címtereket lapon adja meg a címtartományt, amelyet a virtuális hálózat használatával. Ezek azok a dinamikus IP-címek rendeli hozzá a virtuális gépek és a többi szerepkörpéldányon központilag telepíteni a virtuális hálózathoz.<br>Nem lehet átfedésben a címterek választja a más Vnetekről vagy a helyszíni helyeken, amelyhez ez a virtuális hálózat csatlakozni fognak a-címterét.

* **Hely** – egy virtuális hálózatban, létrehozásakor hozzá kell rendelni egy Azure-beli hely (régió). Például ha azt szeretné, hogy a virtuális gépek, a virtuális hálózaton kell lenniük fizikailag USA nyugati régiója telepített, jelölje ki erre a helyre. A létrehozás után a virtuális hálózathoz tartozó helye nem módosítható.

**Miután létrehozta a Vneteket, adja hozzá a következő beállításokat:**

* **Címtér** – további címtartományok nincs szükség ehhez a konfigurációhoz, de a virtuális hálózat létrehozása után is hozzáadhat további címtartományt.

* **Alhálózatok** – további alhálózatokat nem szükségesek ehhez a konfigurációhoz, de előfordulhat, hogy szeretné a virtuális gépeket a többi szerepkörpéldányon külön alhálózatokon.

* **DNS-kiszolgálók** – adja meg a DNS-kiszolgáló neve és IP-címet. A beállítás nem hoz létre DNS-kiszolgálót. Lehetővé teszi, hogy megadja azokat a DNS-kiszolgálókat, amelyeket névfeloldásra kíván használni ennél a virtuális hálózatnál.

Ebben a szakaszban konfigurálhatja a kapcsolat típusát, a helyi webhelyhez, és létrehozhatja az átjárót.

## <a name="localsite"></a>3. lépés - a helyi webhely konfigurálása

Azure egyes helyi hálózati telephely megadott beállításokat használja, a Vnetek közötti forgalom irányítására, hogyan lehet. A megfelelő helyi hálózati forgalmat a kívánt minden egyes virtuális hálózaton kell mutatnia. Meghatározhatja, hogy az egyes helyi hálózati helyek hivatkozni kívánt nevet. A legcélszerűbb leíró nevet.

TestVNet1 például egy helyi hálózati telephely az Ön által létrehozott "VNet4Local" nevű csatlakozik. VNet4Local beállításait a címelőtagokat TestVNet4 tartalmaz.

A hely minden egyes vnet a virtuális hálózat. A konfiguráció a következő példában szereplő értékeket használják:

| Virtual Network | Címterület | Régió | Csatlakozik a helyi hálózati telephely |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA keleti régiója |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA nyugati régiója |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Keresse meg a TestVNet1 az Azure portálon. Az a **VPN-kapcsolatok** szakaszban kattintson a panel **átjáró**.

    ![Nincs átjáró](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Az a **új VPN-kapcsolat** lapon jelölje be **pont-pont**.
3. Kattintson a **helyi** nyissa meg a helyi webhelyhez lapját, és adja meg a beállításokat.
4. Az a **helyi** lapon, a helyi webhely neve. Ebben a példában azt a helyi webhelyhez "VNet4Local" nevet.
5. A **VPN-átjáró IP-címet**, használhatja a használni kívánt IP-címeket, amíg a egy érvényes formátumban. A tényleges külső IP-cím használna általában VPN-eszközhöz. De klasszikus VNet – VNet-konfigurációt a Vnethez tartozó van rendelve az átjáró nyilvános IP-cím használatára. Fényében, hogy létrehozta a virtuális hálózati átjáró még nincs, adjon meg érvényes nyilvános IP-cím helyőrzőként.<br>Ne hagyja üresen a mezőt,-nem választható, ehhez a konfigurációhoz. Egy későbbi lépésben térjen vissza ezeket a beállításokat, és konfigurálja őket a megfelelő virtuális hálózati átjáró IP-címekkel rendelkező, amennyiben az azt létrehozó Azure.
6. A **ügyfél Címterület**, használja a virtuális hálózat címtartománya. Tekintse át a tervezési példa. Kattintson a **OK** mentse a beállításokat, és térjen vissza a **új VPN-kapcsolat** panelen.

    ![helyi webhelyhez](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="gw"></a>4. lépés - a virtuális hálózati átjáró létrehozása

Minden virtuális hálózathoz rendelkeznie kell a virtuális hálózati átjáró. A virtuális hálózati átjáró irányítja a, és titkosítja a forgalmat.

1. Az **Új VPN-kapcsolat** panelen jelölje be az **Átjáró azonnali létrehozása** jelölőnégyzetet.
2. Kattintson a **alhálózat, méret és útválasztási típus**. Az a **átjáró konfigurációs** panelen kattintson a **alhálózati**.
3. Az átjáró-alhálózat neve automatikusan kitölti a szükséges "GatewaySubnet" nevű. A **-címtartományt** a számára a VPN-átjáró szolgáltatásokat kiosztott IP-cím szerepel. Bizonyos konfigurációk lehetővé teszik egy átjáró alhálózatának /29, de a legjobb, ha egy /28 vagy /27 használja, amelyre szüksége lehet több IP-címet az átjáró Services jövőbeli konfigurációk befogadásához. A példa beállítások 10.11.1.0/27 használjuk. A címterület módosítsa, majd kattintson az **OK**.
4. Konfigurálja a **átjáró mérete**. Ez a beállítás hivatkozik a [átjáró-Termékváltozat](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Konfigurálja a **útválasztási típus**. Ebben a konfigurációban kell lennie az Útválasztás típusa **dinamikus**. Az útválasztási típusa nem módosítható később, kivéve, ha az átjáró le szakadjon el, és hozzon létre egy újat.
6. Kattintson az **OK** gombra.
7. Az a **új VPN-kapcsolat** panelen kattintson a **OK** a virtuális hálózati átjáró létrehozásának megkezdéséhez. Az átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró kiválasztott termékváltozatától függően.

## <a name="vnet4settings"></a>5. lépés - TestVNet4 beállításainak konfigurálása

Ismételje meg a [helyi webhely létrehozása](#localsite) és [a virtuális hálózati átjáró létrehozása](#gw) konfigurálása TestVNet4, és az értékeket, ha szükséges. Ha egy gyakorlat szerint végzi, a [példaértékeket](#vnetvalues).

## <a name="updatelocal"></a>6. lépés - a helyi helyek frissítése

Miután a virtuális hálózati átjárók mindkét Vnetek létrejött, módosítania kell a helyi telephely **VPN-átjáró IP-címet** értékeket.

|VNet neve|Az összekapcsolt hely|Átjáró IP-címe|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|VPN-átjáró IP-címet a TestVNet4|
|TestVNet4|VNet1Local|VPN-átjáró IP-címet a TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>1. rész – a virtuális hálózati átjáró nyilvános IP-cím beszerzése

1. Keresse meg a virtuális hálózat az Azure portálon.
2. Ide kattintva nyissa meg a virtuális hálózat **áttekintése** panelen. A panelen a **VPN-kapcsolatok**, tekintse meg az IP-címet a virtuális hálózati átjáró.

  ![Nyilvános IP-cím](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Másolja az IP-címet. A következő szakaszban még szüksége lesz rájuk.
4. Ismételje meg ezeket a lépéseket TestVNet4

### <a name="part-2---modify-the-local-sites"></a>2. rész – a helyi telephely módosítása

1. Keresse meg a virtuális hálózat az Azure portálon.
2. A virtuális hálózaton **áttekintése** panelen kattintson a helyi webhelyhez.

  ![Helyi webhely létrehozása](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Az a **pont-pont VPN-kapcsolatok** panelen kattintson a módosítandó hely nevére.

  ![Nyissa meg a helyi webhelyhez](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Kattintson a **helyi** , amely a módosítani kívánt.

  ![webhely](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Frissítés a **VPN-átjáró IP-címet** kattintson **OK** menti a beállításokat.

  ![átjáró IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Zárja be a más paneleket.
7. Ismételje meg ezeket a lépéseket TestVNet4.

## <a name="getvalues"></a>A hálózati konfigurációs fájlból olvasható be értékek, 7 -. lépés

Amikor a hagyományos Vneteket hoz létre az Azure portálon, megjelenített neve nincs a teljes nevet, a PowerShell használt. Például egy virtuális hálózat neve lehet megjelenő **TestVNet1** a portálon, előfordulhat, hogy mekkora hosszabb nevet adni a hálózati konfigurációs fájlban. A név lehet, hogy alábbihoz hasonló: **csoport ClassicRG TestVNet1**. A kapcsolatok létrehozásakor fontos az értékeket szeretné használni, hogy a hálózati konfigurációs fájlban.

A következő lépésekben meg csatlakozni az Azure-fiókjával, és letölti és tekintse meg a hálózati konfigurációs fájlt az értékek, amelyek szükségesek a kapcsolatokat. az beszerzése.

1. Töltse le és telepítse a legújabb verziót az Azure Service Management (SM) PowerShell-parancsmagokat. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.

2. Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

  ```powershell
  Login-AzureRmAccount
  ```

  Keresse meg a fiókot az előfizetésekben.

  ```powershell
  Get-AzureRmSubscription
  ```

  Ha egynél több előfizetéssel rendelkezik, akkor válassza ki azt, amelyiket használni szeretné.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

  Ezután az alábbi parancsmaggal adja hozzá az Azure-előfizetéshez a PowerShell a a klasszikus üzembe helyezési modellben.

  ```powershell
  Add-AzureAccount
  ```
3. Exportálja, és tekintse meg a hálózati konfigurációs fájlt. Hozzon létre egy könyvtárat a számítógépén, majd exportálja a hálózati konfigurációs fájlt a könyvtárba. Ebben a példában a hálózati konfigurációs fájlt exportálják **C:\AzureNet**.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
4. Nyissa meg a fájlt egy szövegszerkesztőben, és a Vnetek és a webhelyek nevének megtekintése. Ezek a kapcsolatok létrehozásakor használt név lesz.<br>Virtuális hálózat nevének felsorolt **VirtualNetworkSite neve =**<br>Helyneveket felsorolt **LocalNetworkSiteRef neve =**

## <a name="createconnections"></a>8. lépés - a VPN-átjáró kapcsolatok létrehozása

Az előző lépések befejezésekor, beállíthatja az IPsec/IKE előmegosztott kulcs és a kapcsolat létrehozásához. E lépések csoportja PowerShell használja. VNet – VNet kapcsolatokhoz a klasszikus üzembe helyezési modell nem állítható be az Azure-portálon.

A példákban láthatja, hogy a megosztott kulcs pontosan megegyezik. A megosztott kulcsot mindig egyeznie kell. Feltétlenül cserélje le a virtuális hálózatokat és a helyi hálózati helyek pontos nevek ezekben a példákban szereplő értékeket.

1. Hozza létre a TestVNet1–TestVNet4 kapcsolatot.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
  -LocalNetworkSiteName '17BE5E2C_VNet4Local' -SharedKey A1b2C3D4
  ```
2. Hozza létre a TestVNet4–TestVNet1 kapcsolatot.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
  -LocalNetworkSiteName 'F7F7BFC7_VNet1Local' -SharedKey A1b2C3D4
  ```
3. Várjon, amíg a kapcsolat inicializálása. Ha az átjáró inicializálása, az állapota "Sikeres".

  ```
  Error          :
  HttpStatusCode : OK
  Id             :
  Status         : Successful
  RequestId      :
  StatusCode     : OK
  ```

## <a name="faq"></a>A hagyományos Vneteket VNet – VNet szempontjai
* A virtuális hálózatok az ugyanazon vagy másik előfizetést is lehet.
* A virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban (helyeken).
* A felhőszolgáltatás és a terheléselosztási végpont nem ívelhet át több virtuális hálózaton, akkor sem, ha ezek össze vannak kapcsolva.
* Több virtuális hálózat összekapcsolása a VPN-eszközök nem igényel.
* VNet – VNet támogatja a kapcsolódó Azure virtuális hálózatok. Csatlakozó virtuális gépek támogatására, vagy nem a felhőalapú szolgáltatások a virtuális hálózat nem telepített.
* VNet – VNet dinamikus útválasztó átjáró szükséges. Az Azure statikus útválasztó átjáró használata nem támogatott.
* A virtuális hálózati kapcsolat használható többhelyes virtuális VPN-ekkel együtt. Nincs legfeljebb 10 VPN-alagutat a virtuális hálózat VPN-átjáró vagy más virtuális hálózatok, vagy a helyszíni helyek csatlakozik.
* A virtuális hálózatok címterei és a helyszíni helyi hálózati helyek nem lehetnek egymással átfedésben. Átfedő címterek miatt sikertelen a feltöltést netcfg konfigurációs fájlok és virtuális hálózatok létrehozása.
* A virtuális hálózatok párjai közötti redundáns alagutak nem támogatottak.
* A vnet P2S VPN, beleértve az összes VPN-alagutat ossza meg a VPN-átjáró és az azonos VPN-átjáró hasznos üzemidő SLA-t az Azure-ban rendelkezésre álló sávszélességet.
* VNet – VNet forgalmát áthaladó Azure gerincét.

## <a name="next-steps"></a>Következő lépések
Ellenőrizze a kapcsolatokat. Lásd: [ellenőrizni a VPN-átjáró kapcsolatot](vpn-gateway-verify-connection-resource-manager.md).
